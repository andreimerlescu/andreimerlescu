An interviewer recently spent five hours with me without asking a single question about the job, pausing along the way to wonder aloud whether I know how to write code — what a `defer` statement does. This is the answer I wanted a link for.

In 2024, I built a piece of software called `topobuilder` in Go. Below is what it is and how I built it, told through fragments of a closed-source codebase.

**What Is Topobuilder?**

`topobuilder` is a **go binary** that runs off a single **YAML** configuration file that defines an existing PHP website hosted on a cPanel server. The defaults assume that any database connectivity is going to be performed over **RDS** or _another remote connection_. What you're doing is going from a _single region_ into a **multi-region load balanced** containerized application. It takes about 12-36 minutes to deploy a 3-6-9 region cluster with an HAProxy load balancer. 3 regions is 12 minutes; 9 regions defaults to 36 minutes but can also be 12 minutes by adjusting the configuration — though this increases concurrency load and may result in rate limiting from the API utility.

One clarification worth making up front, because it's the kind of thing that gets misread: the binary is portable and I've run it from DigitalOcean droplets and OVH boxes, but the resources it _provisions_ are AWS. Where `topobuilder` runs and what `topobuilder` builds are two different questions, and only the first one is cloud-agnostic.

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
	runMode     string
	statePath   string
	configFile  string
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

	log.Printf("Topobuilder running with: %s %s", runMode, configFile)

	vLogErr := verbose.InitVerboseLogger()
	if vLogErr != nil {
		log.Fatal(vLogErr)
	}

	ctx := context.Background()
	app := models.NewApplication(ctx, runMode, statePath)
	if err := app.LoadConfigs(configFile); err != nil {
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

That's `topobuilder`! 😏🤫 Well, let's actually digest it. Given that I've built [configurable](https://github.com/andreimerlescu/configurable) that became [figtree](https://github.com/andreimerlescu/figtree), why would I use the stdlib `flag` package for `topobuilder`? Because this application doesn't need to dynamically interact with the configuration runtime the way a web server needs to reload its SSL certificates across rotations without forcing the binary to be restarted. In `topobuilder`, we read our configs and then begin processing the application.

We'll break each of these down further, but I use the `models` package to return a new `app` that I can use. This is the primary entrypoint for `topobuilder`.

```go
app := models.NewApplication(ctx, runMode, statePath)
```

Once we have an `app`, we need to load the massive YAML config file into the runtime. Instead of getting into _what_ the configurations are for `topobuilder`, I am going to focus on how the `.LoadConfigs(path string)` operates in the `(app *Application)` itself.

```go
if err := app.LoadConfigs(configFile); err != nil {
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

Extracted into its own package, [verbose](https://github.com/andreimerlescu/verbose) provides a logger capable of scrubbing secrets before they touch STDOUT while preserving the written values to disk in your actual log file.

```bash
go get -u github.com/andreimerlescu/verbose
```

With the **verbose** package, you interact with it using `.AddSecret()`, `.RemoveSecret()`, `.ImportSecrets()`, and `.AddKeyType()` when handling **sensitive data** that you want to _suppress_ from STDOUT, but not from the written log file.

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
verbose.TracefReturn("invalid -name provided: %v", *name)

// bypass sanitization — prints the raw value to the verbose log
verbose.Plain("The raw value is:", *secret)

// this will redact the secret automatically
verbose.Printf("Hello %s, your secret is safe: %s", *name, *secret)
```

With the [verbose](https://github.com/andreimerlescu/verbose) package, you're able to safely register new secrets at runtime and then rely on `verbose.Printf(string, any)` and `verbose.Plain(any)`, plus many other `fmt`-style methods including `verbose.TracefReturn(any)`, `verbose.Sanitize(any)`, and `verbose.Scrub(any)`. Throughout the application, `topobuilder` writes things that could contain secrets to STDOUT, but uses the `verbose` package to ensure that `***` and `[REDACTED]` are what actually appear. This made running a demo in front of stakeholders not a zero-day incident waiting to happen. When I needed the actual values, I could open the text file written to disk and retrieve the raw value that way, or rely on the `verbose.Plain(any)` method.

```go
flag.StringVar(&verbose.LogDir, "logs", filepath.Join(".", "logs"), "Path to store verbose logs")
```

In the `main()` func in the `main.go` file, you can see that we are directly assigning via the `flag.StringVar` method a value directly into the `&verbose.LogDir` string with a default of `./logs` (OS safe). This means that `-logs /var/log/myApp` sets `verbose.LogDir = /var/log/myApp` and it's scoped package-wide regardless of where or what calls `verbose.*`.

## The `models` package

This is where the magic happens and where we'll spend the most time digging in for your learning curiosity.

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

	if err = app.config.Validate(app); err != nil {
		log.Fatal(err)
	}

	verbose.Asf("APPLICATION RUNTIME VERIFIED (took %d seconds)!", int64(time.Since(startedAt).Seconds()))
	return
}
```

Worth naming before someone else does: this method has a named `error` return that it never actually gets to use, because every failure path calls `log.Fatal`. For a one-shot CLI that has provisioned nothing yet, failing loudly at the config gate is the behavior I wanted — there's nothing to unwind at that stage and a half-validated config should never reach `Run()`. But the signature promises a choice the body doesn't offer, and that's a fair thing to call me on.

This method is small despite doing so much. First the `-config` path is read into `topobuilder`. This points to a **YAML** file that contains your runtime configurations.

You'll also see another method from the `verbose` package here — `.Asis(any)` does _as it says_, printing the value _as it is_, meaning no filtering takes place. Static strings like _VERIFYING APPLICATION RUNTIME_ are informational notices that contain _no secrets_ and therefore shouldn't use `verbose.Printf` — otherwise you're spending cycles filtering nothing.

The true magic is happening here:

```go
app.config.Validate(app)
```

In order to understand what this line is doing — and why it can take up to 6 minutes to complete depending on topology size — let's look under the hood.

Let's begin with what `Application` is, so we can see what `app.config` is talking about.

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

We have a `*user.User` stored as `app.user` that can be `nil`, and a `state` + `stateFile` used for a **topobuilder state file**. The `mode` was defined from `main()` above and the `ctx` is passed in from `context.Background()` in `main()` as well.

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

The `Config` struct is split into two groups of properties, arbitrarily separated by a comment only, for required and optional. The required properties are flattened types. The optional properties are additional `struct {}` definitions with their own rules. Ultimately, what this flattened `Config struct {}` offers is a **YAML Configuration** that natively looks something like this:

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
OnePassword: # properties defined in the OPConfigs struct
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

The pattern is that a `<Property>:` at the _root level_ of the **YAML Configuration File** is defined as `type <Property>Configs struct {}` in the Go program. So adding `Something:` means writing a `type SomethingConfigs struct {}` attached to it, defined as YAML using camel-case and JSON using underline_connected_words.

Lines 48-219 of the `config.go` file are dedicated just to the implementation of `app.config.Validate(app)`, expecting back an `error` or `nil`.

First the function handles — without revealing the whole thing — the required properties in series.

```go
errs = data.AppendError(errs, cfg.Name.Validate(app))
```

`data.AppendError` is a helper from the `data` package (similar in spirit to `verbose`) that performs data manipulation tasks like appending an error type, unlike the bare `append(slice, value)` pattern.

`cfg.Name` points to the `Name` property in the `Config` struct. That is a type `ProjNameKey` which has defined:

```go
func (n ProjNameKey) Validate(app *Application) error {}
```

This pattern is used on every property defined in the `Config` struct — both _optional_ and _required_. What `Validate(*Application)` is doing behind the scenes is calling `.Validate(*Application)` on each element in the `Config{}` itself. That means `WazuhConfigs.Validate(*Application)` is defined and returns an error type, and so does everything else.

The next part of the configuration loading is done concurrently where possible, on the assumption that some tasks take longer than others. This is why I use conditional mutexes as coordinators:

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

What these coordinators encode is a dependency graph, not just parallelism. Every validation goroutine waits on the coordinator that gates it and broadcasts the next one on its way out via `defer`. An earlier goroutine broadcasts `awsCoordinator` when its own work completes, which releases this one; when AWS validation finishes, its `defer` broadcasts `dockerCoordinator`, which releases Docker validation, which eventually reaches `deploymentCoordinator`. Notice that I pass a pointer to `*Application` into the `Validate(*Application)` method attached to the `AWSConfigs` struct, and that the response from that validation goes straight into `errCh`, a buffered channel of errors.

Here's the sharp edge, and I'd rather name it than have it named for me in the comments: `Wait()` is not wrapped in a predicate loop. `sync.Cond` has no memory — if a broadcast lands before this goroutine has reached `Wait()`, that wakeup is gone and the goroutine blocks forever. In `topobuilder` the ordering holds in practice, but it holds because of timing, not because the code enforces it. If you're copying this pattern, wrap it — `for !ready { c.Wait() }` with a mutex-guarded flag — or reach for `errgroup` with channels, which gives you ordering and cancellation for free. I'd write it the second way today.

For each of the optional configurations, the coordinators balance the order of validation operations needed to properly validate the runtime of `topobuilder` before it deploys anything into the wild.

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
   Zones: # the AWSZone struct
```

The `Zones` property in the `AWSConfigs` struct also follows the `.Validate(*Application)` pattern in the `AWSZone` struct itself.

Lines 54-63 of the `config_aws.go` file contain:

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

This allows the **YAML** to reference **1Password** secrets via the `op://<Vault>/<Item>/<Field>` lookup syntax. If you've defined the `OnePassword:` properties in the config file, you can use `op://` values and `topobuilder` will look them up and automatically register the retrieved secrets with the [verbose](https://github.com/andreimerlescu/verbose) package — so a secret becomes redactable the instant it exists in memory. Throughout many parts of the application we pass the `*Application` pointer directly into struct methods, including `NewOPParse`, which returns a 1Password structure with methods attached including `.GetValue()`.

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

First we set up the concurrency channels, slices, and wait group; then we spawn two **goroutines**. The first handles _zone processing_ and the second handles _error collecting_. Each zone defined is spawned into its own goroutine.

Without the `<-doneCh` at the end of the block, the zones would never validate. If the configuration calls for three zones, there will be three spawned goroutines — one per zone. If a zone yields an error, that value is passed into `errCh` and picked up by the error collector via `range errCh`.

After each of these extensive checks is performed on each of the components used throughout `topobuilder`, `app.config.Validate(app)` finally completes:

```go
verbose.Asis("SUCCESSFULLY VALIDATED APPLICATION RUNTIME ENVIRONMENT!")
```

That brings us back to `main()`, where we hand off to `app.Run()`. Lines 131-194 in `application.go`:

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

This is where the meat and potatoes are. First, I establish a rollback plan. Currently unimplemented, the rollback plan is threaded through each step, so that when a rollback is triggered it invokes the block of code in the comment.

When the load balancer is created, a set of `.tf` files are rendered by `topobuilder` in the `DirectoriesConfigs.TerraformDir` property. Then `terraform apply` is executed in that path until the **HAProxy** instance is up. `topobuilder` reads the output of `terraform`, but it requires further capability verification before it continues — Terraform reporting success and the host being able to do its job are not the same claim.

Next, `app.BuildImage(rollback)` connects to **cPanel** and compiles a **Docker Image** from the PHP website. The binary uses the `DirectoriesConfigs` struct to define where the `Dockerfile` is written and where various `docker` commands execute. This can take a long time depending on the size of the application and network conditions. What comes out is a `.tar.gz` inside the docker workspace directory — the container image that gets deployed to every endpoint.

During deployment, `Provisioner` is a struct used throughout `BuildTopology(rollback)`. The pattern is `<Utility>Provisioner` for the interface. If the utility is `DockerImage`, the interface is `DockerImageProvisioner`.

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

I define provisioner capabilities using interfaces:

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

This means I can have n-provisioners with capabilities like `Stateful`, `Runnable`, `Recoverable`, `Pingable`, `Populatable`, and `Installable`, with predictable behavior across packages like [igo](https://github.com/andreimerlescu/igo) and [counter](https://github.com/andreimerlescu/counter), including `docker` and the **Wazuh Agent** — DevSecOps monitoring supported by `topobuilder`. As each provisioner is consumed, output is written via the [verbose](https://github.com/andreimerlescu/verbose) package, with raw values landing in the log file itself. The operator gets to watch what each instance is doing as it happens.

The `<Utility>Provisioner` interface composes those capabilities:

```go
type DockerContainerProvisioner interface {
	Recoverable
	Installable
	Runnable
	Pingable
}
```

And the implementation methods are defined against the mirrored struct name:

```go
type ProvisionerDockerContainer struct {
	provisioner *Provisioner
}
```

The pattern is `<Utility>Provisioner` for the contract, `Provisioner<Utility>` for the implementation. Once you've read one, you can predict all of them — which is the entire point.

Next, `app.BuildTopology(rollback)` executes the AWS calls needed to create EC2 instances and consumes the `EBSConfigs` struct from the main config. Disk options are cluster-wide and defined in one location.

As the topology is created, each zone runs concurrently (3 at a time by default, configurable) creating EC2 instances, uploading the `.tar.gz` image, and installing the provisioned software each host needs. Once the load balancer can listen on the configured `Ports:` settings, the AWS networking is secured.

Then `app.ConfigureNetwork(rollback, clusters)` configures security groups, the firewalld process, and connectivity checks. This governs port access to and from the instances and the load balancer, and ultimately secures the network. A properly configured VPC is required before `topobuilder` runs.

Depending on whether **Cloudflare** or **HAProxy** is the load balancer of choice, `app.ConfigureLoadBalancer(rollback, clusters)` either runs the necessary Cloudflare API calls or compiles an `haproxy.cfg` that gets loaded into a dedicated EC2 instance designated as the load balancer entrypoint. `topobuilder` establishes secure connectivity between each endpoint and uses encrypted traffic for all data transmissions.

Once the **HAProxy** load balancer is **healthy**, `app.PublishLoadBalancer(rollback)` issues a DNS record update to the **Cloudflare API**, setting the `Domain: <string>` value from the **YAML Configuration** file. That A record now points at the HAProxy instance.

Once the cluster exists, the image is deployed and running healthy, and the load balancer is serving verified traffic across all endpoints, `NewTBState(app, rollback, clusters)` is invoked. This writes a `.tbstate` JSON file recording the state of the entire deployment step by step, including timestamps. This _log of record_ is maintained during the runtime of `topobuilder` and is part of the `rollback` functionality. The state is then saved via `state.SaveTo(app.stateFile)` before `app.DeploymentSummary(startedAt, clusters)` renders a summary to STDOUT.

## Why I Built This

A front-end landing page — a basic PHP website with an **RDS**-connected database — is the perfect contender for `topobuilder`. It allows **spot instances** to be selected in the configuration so a campaign can run for a fraction of the cost. If you're serving thousands of clients across a large cluster, you can fly first class for a premium; you might call that Kubernetes or ECS. I built the budget Spirit Airlines alternative. Yes, `topobuilder` truly is _Spirit Airlines_ 🤫. No seat recline, gets you there, costs a quarter as much.

That's also the honest answer when someone asks _what do you know about Kubernetes_. I know enough to understand the _tax_ associated with its operations, and enough to explain to EVPs and CTOs what each option actually costs them. `topobuilder` was built in a single month and delivered for a major launch, and OPEX dropped dramatically because of it. When I interview with your company, I am interviewing _you_ just as much as _you are interviewing me_ — and this is the kind of tradeoff I want to be talking about in that room.

## What I Skipped

This piece is deliberately partial. Here's the map of what's missing, so the gaps are visible rather than accidental:

- **How `BuildImage` actually pulls a live cPanel site into a reproducible image.** The hardest part of the whole binary, and the most site-specific.
- **The rollback design.** The scaffolding is threaded through every step in `Run()`; the execution is the `TODO` you can see in the code. I'd rather discuss why it's unimplemented than pretend it isn't.
- **The Wazuh provisioner** and what DevSecOps monitoring costs you at provisioning time.
- **How 9 regions collapses from 36 minutes to 12**, and where the API rate limits bite when you turn that dial.
- **The full `Validate` chain** — roughly 170 lines of `config.go` coordinating a dependency graph across a dozen config structs.
- **The rough edges I already know about**: `ConfigureNetwork` is the only step in `Run()` that warns instead of fatals, `LoadConfigs` promises an error return it never delivers, and the `sync.Cond` waits want predicate loops.

Those are the conversations I'd rather have in person than pad this article with.

## Conclusion

I've been writing software for 17 years, since graduating as a **Computer Engineer** and running the IEEE Student Branch I joined as a widdle freshman. At Cisco and Oracle, much of my career was closed source and off limits. After the pandemic I wanted to give selflessly without expecting anything back — that's why I wrote [Project Apario](https://github.com/ProjectApario) as a three-binary trinity: [reader](https://github.com/ProjectApario/reader), [writer](https://github.com/ProjectApario/writer), and [search](https://github.com/ProjectApario/search).

When the client brought me the problem statement that became `topobuilder`, I told them I had built complex software in Go from start to finish that shipped into production and stayed stable for months with minimal maintenance. They extended trust, and I spent a month building it. It combined years of deploying multi-region architectures by hand — all the manual work that automation has to reproduce before anyone will believe it.

One number surprised me. Text analysis of the repository turned up roughly 72,000 words of source resolving to about 4,800 unique ones — an average of 15 repetitions each. That isn't mysticism, it's what a small pattern language looks like from the outside: `Validate(*Application)` on every config type, `<Utility>Provisioner` for every contract, `Provisioner<Utility>` for every implementation, the same capability verbs on every host. It's also exactly why AI documentation tooling digests this codebase so easily — once you've seen a pattern three times, you've seen all fifteen instances of it. A single config file runs a few hundred lines, and its syntax is legible directly from the structs.

The source stays closed. The ideas don't have to be. It was built two years ago and hasn't needed an update since, which is its own kind of argument — and the parts worth stealing are the config validation pattern and the provisioner capability interfaces, both of which are above in full.

If you've read this far and want to argue about the `sync.Cond` block, or ask what a `defer` statement does, I'm around.
