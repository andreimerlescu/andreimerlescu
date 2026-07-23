In 2024, I built a piece of software called `topobuilder` in Go. I'd like to break it down and explain what it is, and how I built it - looking at some fragments of the closed-source source code.

**What Is Topobuilder?**

`topobuilder` is a **go binary** that runs off from a single **YAML** configuration file that defines an existing PHP website hosted on a cPanel server. The defaults assumes that any database connectivity is going to be performed over **RDS** or _another remote connection_. What you're doing is going from a _single region_ into a **multi-region load balanced** containerized application. It takes about 12-36 minutes to deploy a 3-6-9 region cluster with an HAProxy load balancer. 3 regions is 12 minutes ; 9 regions defaults to 36 minutes but can also be 12 minutes by adjusting the configuration - though this increases concurrency load and may result in rate limiting from API utility.

## Go Application Design  

What I am going to do is explain this program, how it was built, and give you some insights into Go application design. To begin, we have the `main.go` file that is the primary entry point for the `main()` func. 

```go
package main

import (
	"context"
	"flag"
	"fmt"
	"gopkg.in/yaml.v3"
	"log"
	"os"
	"path/filepath"
	"strings"
	"topobuilder/cmd/models"
	"topobuilder/cmd/verbose"
)

const VERSION = "1.0.0"

var (
	runMode string
	statePath string
	configFile string
	showVersion bool
)

func main() {
	flag.BoolVar(&showVersion, "v", false, "show version")
	flag.BoolVar(&showVersion, "version", showVersion, "show version")
	flag.StringVar(&configFile, "config", filepath.Join(".", "cfg.default.yaml"), "Path to cfg.slug-name.yaml")
	flag.StringVar(&verbose.LogDir, "logs", filepath.Join(".", "logs"), "Path to store verbose logs")
	flag.StringVar(&runMode, "mode", ModeDevelopment, "Runtime mode - choices are: "+strings.Join(RunModes(), ", "))
	flag.StringVar(&statePath, "state", "", "Path to state file")
	flag.Parse()

	if showVersion {
		fmt.Println(VERSION)
		os.Exit(0)
	}

	log.Printf("Topbuilder running with: %s %s", runMode, configFile)

	vLogErr := verbose.InitVerboseLogger()
	if vLogErr != nil {
		log.Fatal(vLogErr)
	}

	ctx := context.Background()
	app := models.NewApplication(ctx, runMode, statePath)
	if err := app.LoadConfigs(configFile);  err != nil {
		log.Fatal(err)
	}

	app.Run()

	if cleanErr := app.Cleanup(); cleanErr != nil {
		_, _ = fmt.Fprintln(os.Stderr, cleanErr)
		os.Exit(1)
	}

	// exit
	os.Exit(0)
}
``` 

That's `topobuilder`! 😏🤫 Well, let's actually digest it. Given that I've built [configurable](https://github.com/andreimerlescu/configurable) that became [figtree](https://github.com/andreimerlescu/figtree) why would I use the stdlib `flag` package for `topobuilder`? Well, because this application doesn't need to dynamically interact with the configuration runtime like a web server would need to reload its SSL certificates across rotations without forcing the binary to be restarted. In `topobuilder`, we read our configs and then begin processing the application. 

We'll break each of these down further, but I use the `models` package to return a new `app` that I can use. This is the primary entrypoint for `topobuilder`.

```go
app := models.NewApplication(ctx, runMode, statePath)
```

Once we have an `app`, we need to load the massive YAML config file into the runtime. Instead of getting into _what_ the configurations are for `topobuilder`, I am going to focus on how the `.LoadConfigs(path string)` operates in the `(app *Application)` itself. 

```go
if err != nil; err := app.LoadConfigs(configFile) {
	log.Fatal(err)
}
```

Once the configurations are loaded, the bulk of the application's processing is actually conducted in this one line: 

```go
app.Run()
```

If the `.Run()` has a problem, it'll be captured in the `.Cleanup()` method in the `(app *Application)` definition.

```go
if cleanErr := app.Cleanup(); cleanErr != nil {
	_, _ = fmt.Fprintln(os.Stderr, cleanErr)
	os.Exit(1)
}
```

Now, before we get into the configuration of `topobuilder`, we need to discuss the `verbose` package first.

## The `verbose` package

Extracted into its own package, [verbose](https://github.com/andreimerlescu/verbose) provides a logger capable of scrubbing secrets before they touch STDOUT but preserving the written values to disk in your actual log file. 

```bash
go get -u github.com/andreimerlescu/verbose
```

With the **verbose** package, you interact with it using `.AddSecret()`, `.RemoveSecret()`, `.ImportSecrets()`, `.AddKeyType()` when interacting with **sensitive data** that you want to _suppress_ from STDOUT, but not the written log file.

```go
// Existing SHA512 HEX strings + length can be registered below
verbose.ImportSecrets(map[string]int{
    "abc123...sha512hex...": 32,
})

// New header+trailer pattern to filter between values out of logs
verbose.AddKeyType(verbose.KeyType{
    Opening: "BEGIN_MY_SECRET",
    Closing: "END_MY_SECRET",
})

// Add a secret to `verbose` filtering
verbose.AddSecret(verbose.SecretBytes("my-api-token"), "[REDACTED]")

// Remove a secret from `verbose` filtering
verbose.RemoveSecret(verbose.SecretBytes("my-api-token"))

// render a stack trace to return with
verbose.TracefReturn("invalid -name provided: %v", *name))

// bypass sanitization — prints the raw value to the verbose log
verbose.Plain("The raw value is:", *secret)

// this will redact the secret automatically
verbose.Printf("Hello %s, your secret is safe: %s", *name, *secret)
```

With the [verbose](https://github.com/andreimerlescu/verbose) package, you're able to safely register new secrets in runtime and then rely on the `verbose.Printf(string, any)` and `verbose.Plain(any)` (plus many other `fmt` style methods including `verbose.TracefReturn(any)` and others like `verbose.Sanitize(any)` and `verbose.Scrub(any)`. Throughout the application, `topobuilder` depends on writing what could contain secrets to the STDOUT, but uses the `verbose` package to ensure that `***` and `[REDACTED]` are used instead. This made running a demo in front of stakeholders not a zero-day incident waiting to happen. When I needed the actual values, I can open the text file written to disk and retrieve the raw value that way; or rely on the `verbose.Plain(any)` method. 

```go
flag.StringVar(&verbose.LogDir, "logs", filepath.Join(".", "logs"), "Path to store verbose logs")
```

In the `main()` func in the `main.go` file, you can see that we are directly assigning via the `flag.StringVar` method a value directly into the `&verbose.LogDir` string with a default of `./logs` (OS safe). This means that when `-logs /var/log/myApp` indicates that `verbose.LogDir = /var/log/myApp` and its scoped package wide regardless of where/what calls `verbose.*`. 

## The `models` package

This is where the magic happens and we'll spend the most amount of time digging into for your learning curiosity.

```go
func (app *Application) LoadConfigs(configFile string) (err error) {
	// validate config file
	if len(configFile) == 0 {
		log.Fatal("invalid -config value provided")
	}
	configFileBytes, readErr := os.ReadFile(configFile)
	if readErr != nil {
		log.Fatal(readErr)
	}
	if err = yaml.Unmarshal(configFileBytes, app.config); err != nil {
		log.Fatal(err)
	}
	verbose.Asis("VERIFYING APPLICATION RUNTIME")
	startedAt := time.Now().Local()

	if err != nil; err = app.config.Validate(app) {
		log.Fatal(err)
	}

	verbose.Asf("APPLICATION RUNTIME VERIFIED (took %d seconds)!", int64(time.Since(startedAt).Seconds()))
	return
}
```

This method is pretty small, despite doing so much. We'll traverse this and unpack it, but first the `-config` path is read into `topobuilder`. This points to a **YAML** file that contains your runtime configurations.

As you can see, we're using another method from the `verbose` package called `.Asis(any)` does _as it says_ - it prints the value _as it is_ - meaning no filtering will take place. For static strings, like _VERIFY APPLICATION RUNTIME_ are information notices that contain _no secrets_ and therefore shouldn't use the `verbose.Printf` method - otherwise you're using costly cycles filtering nothing.

The true magic is happening here: 

```go
app.config.Validate(app)
```

In order to understand what this line is doing - and why this one line can take up to 6 minutes to complete - depending on topology size - under the hood. Let's take a look, shall we?

Let's begin by looking at what `Application` is first, so we can see what `app.config` is talking about.

```go
type Application struct {
	ctx       context.Context
	mode      ModeType
	config    *Config
	state     *TBState
	stateFile string
	user      *user.User
}
```

As you can see, we have a `*user.User` stored as `app.user` that can be `nil` and we have a `state` + `stateFile` that is used for a **topobuilder state file**. The `mode` was defined from the `main()` above and the `ctx` is passed in from the `context.Background()` in `main()` also. 

The `Config` struct is where the rules of engagement live for the **YAML Configuration File** that `topobuilder` accepts as the `-config` value. 

```go
type Config struct {

	// Required Properties

	Name            ProjNameKey   `json:"name" yaml:"Name"`
	Slug            ProjSlugKey   `json:"slug" yaml:"Slug"`
	Domain          ProjDomainKey `json:"domain" yaml:"Domain"`
	Mode            ModeType      `json:"mode" yaml:"Mode"`
	Email           string        `json:"email" yaml:"Email"`
	RollbackEnabled bool          `json:"rollback_enabled" yaml:"RollbackEnabled"`

	// Optional Properties

	Firewall     FirewallConfigs     `json:"firewall" yaml:"Firewall"`
	Directories  DirectoriesConfigs  `json:"directories,omitempty" yaml:"Directories,omitempty"`
	OnePassword  OPConfigs           `json:"one_password,omitempty" yaml:"OnePassword,omitempty"`
	AWS          AWSConfigs          `json:"aws,omitempty" yaml:"AWS,omitempty"`
	GitLab       GitLabConfigs       `json:"gitlab,omitempty" yaml:"GitLab,omitempty"`
	EBS          EBSConfigs          `json:"ebs,omitempty" yaml:"EBS,omitempty"`
	Ports        PortsConfigs        `json:"ports,omitempty" yaml:"Ports,omitempty"`
	LoadBalancer LoadBalancerConfigs `json:"load_balancer,omitempty" yaml:"LoadBalancer,omitempty"`
	SSL          SSLConfigs          `json:"ssl,omitempty" yaml:"SSL,omitempty"`
	Docker       DockerConfigs       `json:"docker,omitempty" yaml:"Docker,omitempty"`
	SSHConfigs   SSHConfigs          `json:"ssh,omitempty" yaml:"SSH,omitempty"`
	Wazuh        WazuhConfigs        `json:"wazuh,omitempty" yaml:"Wazuh,omitempty"`
	Secrets      SecretsConfigs      `json:"secrets,omitempty" yaml:"Secrets,omitempty"`
	Times        TimesConfig         `json:"times,omitempty" yaml:"Times,omitempty"`
	ShellKeys    ShellKeys
}
```

The `Config` struct is split into two groups of properties - arbitrarily separated by a comment only for required and optional. The required properties are flattened types. The optional properties are additional `struct {}` definitions with their own rules. Ultimately, what this flattened `Config struct {}` offers a **YAML Configuration** that natively looks something like this: 

```yaml
---
Name: My Landing Site
Slug: landing-1
Domain: mylandingsite.com
Mode: Development
Email: info@mylandingsite.com 
RollbackEnabled: true

Firewall: # properties defined in the FirewallConfigs struct
Directories: # properties defined in the DirectoriesConfigs struct
OnePassword: # properties defined in the OnePasswordConfigs struct
AWS: # properties defined in the AWSConfigs struct
GitLab: # properties defined in the GitLabConfigs struct
EBS: # properties defined in the EBSConfigs struct
Ports: # properties defined in the PortsConfigs struct
LoadBalancer: # properties defined in the LoadBalancerConfigs struct
SSL: # properties defined in the SSLConfigs struct
Docker: # properties defined in the DockerConfigs struct
SSH: # properties defined in the SSHConfigs struct
Wazuh: # properties defined in the WazuhConfigs struct
Secrets: # properties defined in the SecretsConfigs struct
Times: # properties defined in the TimesConfigs struct
```

The pattern is that the `<Property>:` at the _root level_ of the **YAML Configuration File** is defined as the `type <Property>Configs struct {}` in the Go program - as you see above. So when adding `Something: ` it would have a `type SomethingConfigs struct {}` attached to it and defined as YAML using camel-case and JSON using underline_connected_words.

Lines 48-219 of the `config.go` file are dedicated just to the implementation of the `app.config.Validate(app)` and expecting back an `error` or `nil`.

First the function conducts - without revealing the whole thing - the required properties in series.

```go
errs = data.AppendError(errs, cfg.Name.Validate(app))
```

The `data.AppendError` is a helper function from the `data` package (similar to the `verbose` package) but just performing data manipulation tasks like `AppendError` that accepts an error type unlike the `append(slice, value)` pattern. 

The `cfg.Name` points to the `Name` property in the `Config` struct. That is a type `ProjNameKey` which has defined: 

```go
func (n ProjectNameKey) Validate(app *Application) error {}
```

In fact - this pattern is used on every property defined in the `Config` struct - both _optional_ and _required_ properties. What the `Validate(*Application)` is doing behind the scenes is calling `.Validate(*Application)` on each element in the `Config{}` itself. That means that `WazuhConfigs.Validate(*Application)` is defined that returns an error type.

The next part of the configuration loading is done in a concurrent where possible manner that assumes and understands that some tasks may take longer than others - in order to validate the configurations of the `*Application` runtime. This is why I use conditional mutex as coordinators like so.

```go
awsCoordinator := sync.NewCond(&sync.Mutex{})
dockerCoordinator := sync.NewCond(&sync.Mutex{})
deploymentCoordinator := sync.NewCond(&sync.Mutex{})
```

Lines 144-159 show us: 

```go
// Validate AWS Configs
// awsCoordinator issues Wait signal (blocks this goroutine)
// dockerCoordinator issues Broadcast signal
go func(wg *sync.WaitGroup, errCh chan<- error) {
	defer func() {
		dockerCoordinator.L.Lock()
		dockerCoordinator.Broadcast() // notify all waiting
		dockerCoordinator.L.Unlock()
		wg.Done()
	}()
	awsCoordinator.L.Lock()
	awsCoordinator.Wait()
	awsCoordinator.L.Unlock()
	errCh <- cfg.AWS.Validate(app)
}(&wg, errCh)
```

What you can see here is I am using the `defer func(){}` in order to issue a `.Broadcast()` on the coordinator that I need to signal. Before the func returns and that `defer` is executed, the first coordinator blocks the **goroutine** using the `.Wait()` method. Once the `awsCoordinator.Broadcast()` is called, then `cfg.AWS.Validate(app)` is executed. Notice that I am passing a pointer to `*Application` into the `Validate(*Application)` method attached to the `AWSConfigs` struct. Also, notice how I am just passing the response from that validation directly into `errCh` which is a buffered channel of errors.

For each of the optional configurations, the coordinators balance between the order of validation operations needed in order to properly validate the runtime of the `topobuilder` before it deploys the binary into the wild.

The `AWSConfigs` struct looks like: 

```go
type AWSConfigs struct {
	AccessKey       string     `json:"access_key,omitempty" yaml:"ACCESS_KEY,omitempty"`
	AccessSecretKey string     `json:"access_secret_key,omitempty" yaml:"ACCESS_SECRET_KEY,omitempty"`
	Zones           []*AWSZone `json:"zones,omitempty" yaml:"Zones,omitempty"`
}
```

Your **YAML Configuration** will look like: 

```yaml
AWS:
   AccessKey: ""
   AccessSecretKey: ""
   Zones: // the AWSZone struct
```

The `Zones` property in the `AWSConfigs` struct also follows the `.Validate(*Application)` pattern in the `AWSZone` struct itself.

Lines 54-63 of the `config_aws.go` file contains: 

```go
if strings.HasPrefix(aws.AccessSecretKey, `op://`) {
	op, pErr := NewOPParse(app, app.config.AWS.AccessSecretKey)
	if pErr != nil {
		return pErr
	}
	value, err := op.GetValue()
	if err != nil {
		return err
	}
	app.config.AWS.AccessSecretKey = strings.Clone(value)
}
```

This magical line allows for the **YAML** to reference **1Password** secrets via the `op://<Vault>/<Item>/<Field>` lookup syntax. This means that if you've also defined the `OnePassword:` properties in the config file, then you can use the `op://` values and `topobuilder` will look them up and automatically register their secrets with the [verbose](https://github.com/andreimerlescu/verbose) package. Throughout many parts of the application, we pass the `*Application` pointer directly into many struct methods, including the `NewOPParse` method that returns a 1Password structure that has a bunch of methods attached to it including `.GetValue()`. 

Lines 69-93 in the same file look like: 

```go
// THE SETUP

errCh := make(chan error, len(aws.Zones))
doneCh := make(chan struct{})
wg := new(sync.WaitGroup)
errs := make([]error, 0)

// ZONES PROCESSED IN GOROUTINE -> EACH ZONE = GOROUTINE

go func() {
	for i, zone := range aws.Zones {
		i := i
		wg.Add(1)
		go func(wg *sync.WaitGroup, errCh chan<- error, zone *AWSZone, i int) {
			defer wg.Done()
			errCh <- zone.Validate(app, i)
		}(wg, errCh, zone, i)
	}
	wg.Wait()
	close(errCh)
}()

// COLLECT ERRORS

go func() {
	for err := range errCh {
		errs = data.AppendError(errs, err)
	}
	doneCh <- struct{}{}
	close(doneCh)
}()

// WAIT FOR EVERYTHING

<-doneCh
```

First we set up the concurrency channels, slices and wait group; then we proceed over to the spawning 2 **goroutines**. The first is for the _zones processing_ and the second is for the _error collecting_. Each zone defined is spawned into its own goroutine.

Without the `<-doneCh` at the end of the block, the zones would never validate. If the configuration calls for three zones, then there will be three spawned goroutines - one for each zone. If the zone yields an error, that value is passed into the `errCh` and then picked up by the error collector via the `range errCh`. 

After each of these extensive checks are performed on each of the components used throughout `topobuilder`, the `app.config.Validate(app)` finally completes! 

```go
verbose.Asis("SUCCESSFULLY VALIDATED APPLICATION RUNTIME ENVIRONMENT!")
```

That brings us back up to `main()` where we hand off to the `app.Run()` next. Lines 131-194 in `application.go`, the `Run()` method is defined as: 

```go

// Run iterates over all AWS Zones and deploys the cluster topology to that endpoint
func (app *Application) Run() {
	fmt.Println("Deploying topology...")
	startedAt := time.Now().Local()
	var err error

	// Start a rollback
	rollback := app.NewRollback()

	// create the load balancer
	rollback, err = app.CreateLoadBalancer(rollback)
	if err != nil {
		// TODO #feature-rollback implement rollback here
		log.Fatal(err)
	}

	// build the image
	rollback, err = app.BuildImage(rollback)
	if err != nil {
		log.Fatalf("BuildDockerImage() failed with err: %v", err)
	}

	// create the topology
	var clusters []*Cluster
	rollback, clusters, err = app.BuildTopology(rollback)
	if err != nil {
		// TODO #feature-rollback implement rollback here
		log.Fatal(err)
	}

	// configure the network
	rollback, err = app.ConfigureNetwork(rollback, clusters)
	if err != nil {
		_, _ = fmt.Fprintf(os.Stderr, "NetworkClusters(app, rollback, clusters[%d]) resulted in networkingErr: %v\n",
			len(clusters), err)
	}
	fmt.Printf("Finished establishing multi-cluster network.")

	// configure HAProxy from the clusters
	rollback, err = app.ConfigureLoadBalancer(rollback, clusters)
	if err != nil {
		log.Fatal(err)
	}

	// update the domain's dns to point to the load balancer
	rollback, err = app.PublishLoadBalancer(rollback)
	if err != nil {
		log.Fatal(err)
	}

	state, stateErr := NewTBState(app, rollback, clusters)
	if stateErr != nil {
		log.Fatal(stateErr)
	}

	saveStateErr := state.SaveTo(app.stateFile)
	if saveStateErr != nil {
		log.Fatal(saveStateErr)
	}

	fmt.Printf("Wrote Topobuilder State to %s\n", app.stateFile)

	app.DeploymentSummary(startedAt, clusters)
}
```

This is where the actual meat and potatoes, so to speak, are contained for the binary. First, I establish a rollback plan. Currently unimplemented, the rollback plan is passed through each of the steps since in the event of a rollback being triggered, that would invoke the block of code in the comment.

When the load balancer is created, a set of `.tf` files are rendered by `topobuilder` in the `DirectoriesConfigs.TerraformDir` property. Then `terraform apply` is executed in that path until the **HAProxy** instance is up and running. `topobuilder` looks at the output of `terraform` but it requires further capability verification before it continues.

Next we see that the `app.BuildImage(rollback)` is called. This connects to **cPanel** and compiles a **Docker Image** based on the PHP website. The `topobuilder` binary will use the `DirectoriesConfigs` struct to define where the `Dockerfile` is going to be written to, and the path where various `docker` commands will be executed. This method can take a long time depending on the size of the application and the network conditions. The file that gets produced here is a `.tar.gz` that is inside of the docker workspace directory. It's the image of the container that is going to be deployed on the various endpoints.

During the deployment of the application, `Provisioner` is a struct that is used throughout the `BuildTopology(rollback)` function. The pattern was `<Utility>Provisioner` as the struct. If the utility was `DockerImage` then the provisioner was `DockerImageProvisioner`. 

```go
type DockerProvisioner interface {
	Installable
	Pingable
	Runnable
	Recoverable
	Prune() error
	Reset() error
}

type ProvisionerDocker struct {
	provisioner *Provisioner
}

// NewDockerProvisioner attaches DockerProvisioner to Provisioner
func NewDockerProvisioner(p *Provisioner) DockerProvisioner {
	return &ProvisionerDocker{provisioner: p}
}

// Status returns a DockerStatus populated with data
func (docker *ProvisionerDocker) Status() *DockerStatus {}

// Recover will attempt to get the docker process started on the Server
func (docker *ProvisionerDocker) Recover() error {}

// Start initiates the docker process on the Server
func (docker *ProvisionerDocker) Start() error {}

// Stop will terminate the docker process
func (docker *ProvisionerDocker) Stop() error {}

// Online checks ps aux for docker on each Instances' Server
func (docker *ProvisionerDocker) Online() bool {}

func (docker *ProvisionerDocker) Reset() error {}

// Uninstall removes docker from the Server
func (docker *ProvisionerDocker) Uninstall() error {}

// Installed checks ps aux for docker on each Instances' Server
func (docker *ProvisionerDocker) Installed() bool {}

// Install puts the docker engine on the Instances Server
func (docker *ProvisionerDocker) Install() error {}

// Prune runs docker prune on each Instances Server
func (docker *ProvisionerDocker) Prune() error {}
``` 

I define provisioner capabilities using the interfaces as: 

```go
type Installable interface {
	Install() error
	Uninstall() error
	Installed() bool
}

type Populatable interface {
	Populatable() PopulatableData
}

type Pingable interface {
	Online() bool
}

type Recoverable interface {
	Runnable
	Pingable
}

type Runnable interface {
	Start() error
	Stop() error
}

type Stateful interface {
	SetState(enabled bool) error
}
```

This means that I can have n-Provisioners that can have the capabilities like being `Stateful`, `Runnable`, `Recoverable`, `Pingable`, `Populatable`, `Installable` and have predictable behavior across packages like [igo](https://github.com/andreimerlescu/igo), [counter](https://github.com/andreimerlescu/counter) including `docker` and the **Wazuh Agent** - a DevSecOps monitoring supported by `topobuilder`. As each of these provisioners are consumed and used, the output is written via the [verbose](https://github.com/andreimerlescu/verbose) package where the raw values are written to the log file itself. This provides the operator access to see what each instance is doing as its happening.

The `<Utility>Provisioner` struct will be defined to consume the capabilities above like: 

```go
type DockerContainerProvisioner interface {
	Recoverable
	Installable
	Runnable
	Pingable
}
``` 

So you can understand that the `<Utility>Provisioner` structs will contain `interface{}` contracts for various capabilities like being `Recoverable`, `Installable`, `Runnable` and/or `Pingable` as seen above.

Then the various Provisioner implementation methods are defined against the following struct: 

```go
type ProvisionerDockerContainer struct {
	provisioner *Provisioner
}
```

This pattern is similar, it is `Provisioner<Utility>` in its struct name, and this will have its own methods as required by the `<Utility>Provisioner` interface requires. 

Next, we see `app.BuildTopology(rollback)` - which will execute many of the AWS commands needed to create an EC2 instance and consume the `EBSConfigs` struct out of the main config. The disk options on the cluster are cluster-wide and defined in one location.

As the topology is created, each zone runs concurrently (though 3 at a time is a configurable limit) as it creates the EC2 instances, uploads the `.tar.gz` image file and installs the necessary provisioned software for the hosts. Once the load balance can listen on the configured `Ports:` settings, the AWS networking is secured. 

Then we see `app.ConfigureNetwork(rollback, clusters)`. This is where the security groups are configured, the firewalld process, and the connectivity checks take place. This configures port access to and from the instance and the load balancer. This stage ultimately secures the network. A properly configured VPC is required before `topobuilder` can run.

 Then, depending on whether **Cloudflare** is being used or if **HAPRoxy** is being used as the load balancer of choice, the `app.ConfigureLoadBalancer(rollback, clusters)` block is responsible for either running the necessary Cloudflare API calls, or compiling an HAPRoxy.conf file that gets loaded into a new EC2 instance created that is designated as the load balancer entrypoint. `topobuilder` will establish security connectivity between each endpoint and use encrypted traffic for all data transmissions.

Once the **HAProxy** load balancer is **healthy**, then we can run the `app.PublishLoadBalancer(rollback)` function, which issues a DNS record update to the **Cloudflare API** to set the `Domain: <string>` value from the **YAML Configuration** file defining the website that you're deploying the cluster out to. This A record gets updated to the **HAProxy** instance. 

Once the cluster has been created, the docker image deployed and launched, running and healthy, and the load balancer up and running serving traffic on all endpoints in a healthy status check verified manner, the `NewTBState(app, rollback, clusters)` is invoked next. This writes a new `.tbstate` JSON structured text file that records the state of the output of the entire deployment step by step including timestamps of when things were executed. This _log of record_ is maintained during the runtime of `topobuilder` and is part of the `rollback` functionality. Once the state of the cluster is compiled, its then saved via `state.SaveTo(app.stateFile)` before `app.DeploymentSummary(startedAt, clusters)` is called to render to STDOUT a summary of the deployment.

## Why I Built This

Front end landing pages like a basic PHP website that uses an **RDS** connected database is a perfect contender for `topobuilder`. It allows for **spot instances** to be selected in the configuration, and a campaign be executed for a FRACTION OF THE COST. Ultimately - if you have a large cluster of instances that you're serving thousands of clients into, you can do it in the first class cabin for a premium - you might call that Kubernetes or Elastic Container Service (ECS) - but I built a budget Spirit Airlines alternative. Yes, `topobuilder` truly is like _Spirit Airlines_ 🤫. I am talking about it because it was super cool to build, and I haven't released its source code because of its use case being limited and unnecessary to do so. But, how I built the configuration and the provisioners themselves are the meat and potatoes of this piece that I am offering for your reading time this Wednesday in July.

[The Library](https://dev.to/andreimerlescu/when-ai-creates-a-closed-source-world-2oin) still needs to be contributed to despite being [Locked In With AI](https://dev.to/andreimerlescu/locked-in-with-ai-5g60) and the problems that Applicant Tracking Systems (ATS) have caused the [interviewing market](https://dev.to/andreimerlescu/five-hours-of-interviewing-zero-questions-about-the-job-378k). When I interview with your company, I am interviewing _you_ just as much _you are interviewing me_. It goes both ways. So, when I am asked, _what do you know about kubernetes_ - I know enough to understand the _tax_ associated with its operations. I also know enough to explain to EVPs and CTOs the options that each solution give and what I can offer. `topobuilder` was built in a single month and delivered for a major launch. OPEX is dramatically reduced by leveraging `topobuilder`. It was **not made open source** because frankly speaking - it was built over 2 years ago and _has not been updated since._ It hasn't needed updating. ❤️‍🔥 For now 😏🤫. A single config file can be a few hundred lines long, and the syntax of it is very much seen in the structures themselves. AI powered documentation generation systems effectively can understand the `topobuilder` application and provide effective documentation for the package.

## Conclusion

I've been writing software for 17 years since graduating college as a **Computer Engineer** and running the IEEE Student Branch since joining as a widdle freshman. At Cisco and Oracle, much of my career was closed source and off limits. After the pandemic, I wanted to give selflessly without expecting in return. That's why I wrote [Project Apario](https://github.com/ProjectApario) into a three binary trinity called [reader](https://github.com/ProjectApario/reader), [writer](https://github.com/ProjectApario/writer) and [search](https://github.com/ProjectApario/search). When the problem statement was made by the client that requested `topobuilder`, I assured them that **I have built complex software in Go from start to finish that is stable and shipped into production for months with minimal updates needed for maintenance** and then with their _trust_, I built over a single month to produce `topobuilder`. It combined many years of experience in deploying multi-region architectures and the manual work involved in any of those automated systems in order to get them to work. I wanted to build something that could go from start to finish and do it in a way that I understood top to bottom. `topobuilder` was the result. I will never release the source code of `topobuilder`. It's mine to give away, but its 4,800 words repeated 15 times each in a majestic unique manner that deploys a single cPanel website that is built with PHP and uses RDS into a multi-region load balanced cluster behind **HAProxy** in 12-36 minutes depending on configuration choices and site size. When I ran text analysis against the `topobuilder` repository, I was shocked to discover out of the 72,000 words in the source code, there were only 4800 unique words - which meant that each unique word was duplicated _in a mystery majestic order_ that counted to an average of 15 times per word. Talk about your needle in a haystack problem. The human understand when you're at the 12th or 13th time the word has shown up that you are about to expire your average usage of that word so finish up with what you're doing and _move on_ 🤣.

But to be interviewed for [five hours](https://dev.to/andreimerlescu/five-hours-of-interviewing-zero-questions-about-the-job-378k) by an _internet intelligence_ company and being one of the pre-public BITNET University of Maine internet users of the 90s questioned _does this guy even know how to write code? what's a defer statement do?_ The hostility in how that particular interview was conducted triggered my ego and prompted me to **own without ego** in a way that didn't name names or act petty in any manner. But, when I spoke about `topobuilder`, I wanted to have something to point to - I could say - check this out and direct them here where the project is publicly discussed.






