### Hi there 👋, 

[![Typing SVG](https://readme-typing-svg.herokuapp.com?font=Fira+Code&size=17&pause=1000&width=465&lines=Thank+you+for+visiting+my+profile!;Wentworth+Institute+Graduate+2011;Cisco+Systems+-+2009-2016;Oracle+Cloud+(OCI)++-+2016-2020;WB+Games+(WBNY)+-+2021-2023;Invented+PhoenixVault+in+2020;Launched+Project+Apario+in+2021;Rewrote+PhoenixVault+in+2023;%24IAM+RESEARCH+%26+DEVELOPMENT!;%24IAM+building+XRP+games+and+apps!;Cisco+Called+Me.+I+Answered.;Oracle+Called+Me.+I+Answered.;WB+Games+Called+Me.+I+Answered.;Beamable+Called+Me.+I+Answered.;Beamable+Broke+US+Employment+Law!;Say+Good-Bye+To+Beamable!;I+built+Topobuilder+for+Enterprise!)](https://git.io/typing-svg)

![I Look Like Commander Riker](banner_riker1.jpg)

My name is Andrei. I enjoy writing Go applications and doing DevOps engineering. My collection of open source projects are contributions that I give to the community to help give people more learning opportunities with Go, usefulness in the utilities that I provide and the breadth of them offered as well. Tools ranging from genwordpass, summarize, counter, cli-gematria, goini, bump, and now prettyboy, offer you and me a unique toolset of powerful applications that I built. 

## Projects

I am a freelance software engineer architecting platforms written in Go that run on Rocky 9 Linux with SELinux enforcing. I am a professional DevOps Architect who uses Docker, Terraform, Ansible, Bash, GitHub Actions, GitLab CI/CD YAML, and builds for cloud providers like AWS, OCI and OpenStack. I avoid using Azure. I prefer cloud platforms that are built without windows in mind, the universe is much bigger than a window and limiting yourself to such, to me, doesn't make too much sense. Doing what I do on Windows was painful and I experienced it at WB Games. I ended up using a Rocky 9 Linux VM instead full time. 

### Designed To Serve Selflessly

My projects are designed to serve you and your needs without involving me in the process. Sure there is a learning curve involved, but with AI these days, it should be a walk in the park for anyone. The `igo` package, https://github.com/ProjectApario/igo -> https://github.com/andreimerlescu/igo, package allows you to run multiple versions of Go on the same user $HOME/go workspace and conviently use `igo -cmd list` and `igo -cmd install -gover 1.24.3` and `igo -cmd uninstall -gover 1.18.0` to manage their versioning. Additionally, after one version of Go has been installed, the shim at `$HOME/go/shims/go` will use the `igo -cmd install -gover <from your go.mod file>` without showing you any errors related to "You don't have go 1.17.6 installed!` errors, but rather you see `Go 1.17.6 is missing... installing now! .... go version go1.17.6 linux/amd64` instead. 

### My Projects

Here's a breakdown of the project that I've created on GitHub and how they impact my "profile". 

### GO ENV 

I built both a package and a cli utility called `goenv` that allows me to interact with environment variables in a typed manner. For instance, `if !env.Bool("NEVER_PANIC", false) { panic("something happened") }` and not have anything else to do. If I am working with a new project that needs an `.env` file, I can run `goenv -init -write` and voila. Sure, its easier to write `touch .env` its less characters, but the idea is that `goenv` can be a medium to interact with `.env` files through. For instance, verifying whether NEVER_PANIC is defined in `.env` can be done using `goenv -has -env NEVER_PANIC`. The exit code will indicate for you. You can do things like `goenv -is -env NEVER_PANIC -value true` and rely on the exit code. Adding `HOSTNAME` to the `.env` is now super easy! `goenv -add -write -env NEVER_PANIC -value true` and voila... the `.env` file now contains `NEVER_PANIC=true` because of the changes made to it. 

The long and tall of it, as you can see, is to build a better developer experience around tooling and pipelining around interacting with common configuration file types. In addition to `goenv`, which is designed to work with `.env` files, I also have [goini](https://github.com/andreimerlescu/goini) that does the same thing this does, but for `.ini` files. However, speaking of `.ini` files, `goenv` has the ability to convert your `.env` into `.env.ini` if you want to use `.ini` processing for your configurations of any kind. The output formatting options of the `goenv` package are the widest yet that I have offered. The `-print` is STDOUT text but the `-json` renders `.json` files, `-ini` renders a `.ini` file, `-yaml` renfers a `.yaml` file. I also have `toml` and `xml` in the output formats supported. The XML for has an array of elements called `<envs>` that contain the actual environments themselves as `<NEVER_PANIC>true</NEVER_PANIC>`. 

```sh
go install github.com:andreimerlescu/goenv@latest

mkdir test-env
cd test-env

goenv -init -write
goenv -write -add -env HOSTNAME -value "$(hostname)"
goenv -write -add -env USER -value "$(whoami)"
goenv -write -add -env HOME -value "${HOME}"
goenv -write -add -env PWD -value "$(pwd)"
goenv -write -add -env DISK_FREE -value "$(df -h / | grep disk | awk '{ print $4 }' | tr '\n' ' ')"
goenv -write -rm -env PWD
```

Result will have `HOSTNAME`, `USER`, `HOME`, and `DISK_FREE` defined inside of it. 

```sh
goenv -mkall -write # creates .env.toml, .env.yaml, .env.ini, .env.xml, .env.json
goenv -cleanall -write # deletes .env.toml, .env.yaml, .env.ini, .env.xml, .env.json
```

Aside from the Command Line Utility, the package itself can be used in Go projects too. 

```bash
go get -u github.com/andreimerlescu/goenv/env
```

You'll need to make sure that you're using `import "github.com/andreimerlescu/goenv"` while using it. 

```go
// configure accepts a figtree.Plant and returns a modified plant
func configure(figs figtree.Plant) figtree.Plant {
	if figs == nil {
		figs = figtree.Grow()
	}

	figs = figs.NewString("hostname", env.String("HOSTNAME", "localhost"), "Hostname")
	figs = figs.NewInt("port", env.Int("PORT", 8080), "Port")
	figs = figs.NewUnitDuration("timeout", env.UnitDuration("TIMEOUT", time.Duration(3), time.Second), time.Second, "Timeout")

	if env.Exists("USE_VALIDATORS") && env.IsTrue("FIGTREE_VALIDATORS") {
		figs = figs.WithValidator("port", figtree.AssureIntInRange(1,65535))
	}

	if env.AreTrue("THIS_MUST_BE_TRUE", "AND_THIS_MUST_BE_TRUE") {
		panic("yeah these things are true!")
	}

	env.Set("TAGS", "fun,expensive,far-away,german")
	env.Set("ABOUT", "name=Andrei&career=Engineer&company=Beamable")

	if env.ListContains("TAGS", env.ZeroList, "far-away") {
		log.Println("This is far away!")
	}

	if env.MapHasKeys("ABOUT", env.ZeroMap, "name", "career", "company") {
		log.Println("property has all attributes")
	}

	//                         ↓ fallback if "PORT" is undefined
	//						   ↓ 
    //                         ↓     ↓ max
	if !env.IntInRange("PORT", 0, 1, 65535) < 1 {
	// 						      ↑ min

		panic("-port is required to be between 1-65535")

	}

	if err := figs.Load(); err != nil {
		if !env.Bool("NEVER_PANIC", true) {
			panic(err)
		} else {
			log.Fatal(err)
		}
	}
}
```

So, aside from the CLI utility having value, the package itself can be used and reduce the complexity of your environment specific Go code. 

### Bump

I built Bump in a single day. It's designed to work with a VERSION file and work with the `v1.0.0` string inside of it that is handled by a `go:embed` on the `VERSION` file in the `Version()`
func. It's a convention that I use for my Go binaries, and the `bump` binary allows me to programatically interact with the `VERSION` file across projects using `bump`. 

```bash
[ ! -d ~/work ] && mkdir -p ~/work
go install github.com:andreimerlescu/bump@latest
git clone git@github.com:andreimerlescu/prettyboy.git
cd prettyboy

bump -check
v1.0.1

bump -patch
Bumped v1.0.1 -> v1.0.2

bump -patch -write
Bumped v1.0.1 -> v1.0.2 (Saved to VERSION)

bump -check
v1.0.2
```

### Pretty Boy

This utility is designed to offer a BubbleTea interactive menu to browse the `history` contents. Plans include to add the `.hist` directory and parse through `.gz` files to index them and make them part of the application, then provide an easy interface around history browsing. 

```bash
go install github.con:andreimerlescu/prettyboy@latest

prettyboy history
```

### Certificate Authority 

This utility is responsible for rendering your own Root Certificate Authority where you can issue X509 certificates. It's been minimally developed and has a fork in it that is looking to make it Go managed instead. It's a low priority item for me, but this package has given me the ability to establish zero trust networks that are TLS clusters of hosts that talk to each other over TLS controlled by my Root CA. This means no interception. 

https://github.com/andreiwashere/certificate-authority

### `entpassgen` Entropy Password Generator

This utility is an **entropy** focused password generator with a basic word dictionary built into it. The `genwordpass` omits the entropy generation of the `entgenpass` utility. 

https://github.com/andreimerlescu/entgenpass

### `counter` A tool for counting things

This utility was inspired by `p4 counter` from Perforce. It's such a great and simple tool, that something like it shouldn't be coupled to the `p4` suite itself. It's useful in DevOps, and as a DevOps Architect, I have found use in the `counter` package. It's very powerful and offers you a clean interface that is manipulated by your environmental preferences, whomever you may be. It's built to work around you. 

https://github.com/andreimerlescu/counter

### `cli-gematria` A tool for calculating 6 types of Gematria

This is for my frens and my fans. I know you boys and girls love Gematria because lets face it, its everywhere. Whether you understand it or not, its a pattern of numbers that you begin to associate words to values. For instance...

```bash
go install github.com/andreimerlescu/cli-gematria@latest
cli-gematria
Welcome to Gematria Calculator! 🎉
Type '?' for menu, 'exit' or 'quit' to exit.
> i love you
🔢 Gematria result for 'iloveyou'
+----------+--------+
|   KIND   | VALUE  |
+----------+--------+
|  Simple  | ★ 124  |
|  Jewish  | ✡ 1434 |
| English  | ♚ 744  |
| Majestic | ♛ 372  |
| Mystery  | ♜ 2539 |
|  Eights  | ∞ 879  |
+----------+--------+

```

https://github.com/andreimerlescu/cli-gematria is powered by https://github.com/andreimerlescu/gematria

### `genwordpass` A tool for generating multi-lingual word-based passwords with millions of built-in-dictionary words for complex and strong rememberable passwords

This utility generates language specific words. 5 languages are provided. English, Spanish, French, German, Romanian and Russian. By default it'll use 2+ special characters to split words and it defaults to 3 words. 

```bash
$ go install github.com/ProjectApario/genwordpass@latest
$ genwordpass -version
1.0.0

$ genwordpass
minimum7moralizing-bibliotic%

$ genwordpass -languages fr
convoierez$absolu3malins!
```

https://github.com/ProjectApario/genwordpass

### `topobuilder` A tool for deploying a cPanel website to multiple AWS Regions with a Cloudflare Load Balancer from a single YAML file using Go Docker and Terraform

This package is designed to take a cPanel website, dockerize it, build a Terraform set of templates based on a single YAML file that deploys said website to multiple AWS regions with a common configuration. It creates an HAProxy load balancer, uses TLS for internal connections, and can deploy a 1GB PHP MySQL website that runs in 4 AWS regions in the US (`us-east-1`, `us-east-2`, `us-west-2`, and `us-west-1`) in under 10 minutes. Wuzah can be connected and `counter` is installed on each host and the counters are used! Deployments have environments like `staging` or `production` and are stored in `/topobuilder/deployments/<name>/<environment>` where a host of directories are created for the `container.docker` and `container.terraform` including `keys` and `logs`. These directories store the `.tbstate` file that `topobuilder` has `topocli`, a second Go utility that re-uses the `internal` of `topobuilder` that parses the `.tbstate` that gets compiled on a successful deployment and provide access points into the hosts to perform actions on the hosts; including making cluster size changes and live-updating the `.tbstate` and the connected `terraform` state files. 

These `.tfstate` files are stored in the `/topobuilder/deployments/<name>/<env>/container.terraform/<region>/<name>.tfstate` path. These are not stored on AWS S3 as configured in `tf` files; but rather I use `encrypted-luks-workspace` in order to mount the `/topobuilder/deployments/<name>` for any client that uses the software for their needs. This software is designed to allow you to deploy a fleet of spot instances in multiple regions and allow the `topoadmin` program to watch the cluster and keep the desired count healthy. That runs from the `topobuilder.app` host that is accessibly via 127.0.0.1 via `/etc/hosts`. 

The `topodash` application provides a CLI interface to edit and manage YAML files that are stored in the `/topobuilder/configurations/<name>.<env>.yaml`. This file keeps versioned history in a MongoDB Atlas (Free Tier) database. Access is controlled by SAML via Microsoft 365 Corporate Domains. From `topodash`, you can run `topobuilder` and various commands of the `topocli` through the Vue 3 web application.

This application is designed to scale a landing page to dozens of hosts across the globe using AWS infrastructure. ECS is not used in `topobuilder`'s design. 

https://github.com/andreimerlescu/topobuilder

### `figtree` A command line utility interface builder that powers all of my open source software

The `figtree` package is a command line configuration interface builder that lets you define `Fig` Mutagenesis like String, List, Int64, etc.. This package supports using an ENV `CONFIG_FILE` that will load `.yml|.yaml|.json|.ini` config files into a `Plant` that has `Callbacks`, `Validators`, and `Rules` with the ability to load/save a Plant from a file at will. Figtree can be used in many application types, and is being explored to provide secret sourcing such that you can pass in `op://` or `aws::arn` path to import the secret with the ability to define and build n-implementations of the Secret interface. 

```bash
go get github.com/andreimerlescu/figtree/v2
```

https://github.com/andreimerlescu/figtree

### `apario-writer-html2csv` A tool for converting NARA HTML into `apario-writer` input CSV as `source` paths to `.gov|.mil` authentic sources

This utility was designed for the 2025 JFK files released by the National Archives and Records Administration. Hosted at https://jfkfiles.info, this collection of data was compiled using the `apario-writer` application, but the initial CSV to render that collection was missing. This utility takes the HTML table of columns, parses it, and renders a CSV import that `apario-writer` can use. 

https://github.com/andreimerlescu/apario-writer-html2csv

### `summarize` A tool for summarizing content in your current workspace for AI ingestion

This utility is installed via `go install github.com/andreimerlescu/summarize@latest` and its super useful if you use xAI's SuperGrok like I do! The context is super large and the DeepResearch / Think capabilities of the AI surpass that of even Claude 4 and anything that OpenAI can come up with; in my humble opinion based on a 4 year research analysis of the various AI tools and capabilities. When I am working on open source projects, I run `summarize` in my workspace, a new folder called `summaries` gets added, and the timestamp copy of the summary. 

This package has **AI Enhancements** in the `develop` branch that leverages Ollama. 

```bash
go install github.com/andreimerlescu/summarize@latest

summarize
find ./summaries -type f -name 'summary.*.md' -exec cat {} \;
```

https://github.com/andreimerlescu/summarize

### `xlm-vanity-address-finder` A tool for finding an XLM cryptocurrency wallet address and seed to use for brand purposes

I don't use Stellar Lumens for anything, so I made the utility for finding these vanity addressess free. XRP is the premium business service whereas XLM is the freelance small-adoption of crypto. Think of XLM as digital silver and XRP as digital gold. Their utilities are as such too. If you're crafting with digital silver, then here are my digital Shekels to you. Brand your business and your XLM digital silver experience with as many vanity addresses as you desire! If you're into regulated business work, and XRP is your preferred cryptocurrency, then the `xrp-vanity-address-finder` is the tool for you!

https://github.com/andreimerlescu/xlm-vanity-address-finder

### `xrp-vanity-address-finder` A tool for finding an XRP cryptocurrency wallet address and seed to use for brand purposes

Unreleased code, but this will find an XRP wallet address that has a vanity name to it. Available upon request and personal discretionary approval of sharing of the code. It can be used for bad purposes and in the wrong hands, its not good for anyone. However, I am happy to work with any business or organization who needs to get a vanity address for, say minting their own tokens, using their brand. This premium service costs 10 XRP or 10M $APARIO tokens. Then I'll sign a contract with you protecting your secret key and deliver to you the results of your XRP Wallet Address + Family Seed in a GPG Encrypted Message sent via ProtonMail. After, I destroy my records and certify under penalty of contract breach that you are the only holder of the decryptable value of the transaction. 

### `verbose` A tool for logging secrets to log files

This utility is designed to let you render to your log file, using `verbose.Printf("this contains a secret: %v", myVariable)` where the connected log file to the `verbose` package stores a register of SHA512 hashes secrets. You use `verbose.AddSecret("secret-value", "replace-with")` to your project and then your `verbose.log` will use `***` masks for your secrets or your `replace-with` value. Preloading secrets can be done using JSON or YAML as a map of `SHA512([32]byte): length(int64)`. 

https://github.com/andreimerlescu/verbose

### `ips.shabbat` A tool for enforcing Shabbat on Invision Power Community software

I felt that my Jewish brothers and sisters would like it if I helped them honor Shabbat by shutting down their IPS forums on Shabbat. This software is designed to lock Invision Powered Community Software self-hosted solutions on Saturdays for Shabbat. It's something that all should be doing, since its for Jew and Gentile alike. The body has to recharge and Saturdays is when our own I AM does His work in each of us. It's what I've experienced, and offering this freely for anyone who wishes to leverage software to remind them the power of the Sabbath and what true Shabbat is all about, then you may consider using this! To each their own way. I choose Yah's way! 

https://github.com/andreimerlescu/ips.shabbat

### `report-tfvars` A tool for giving you an interface to Terraform variables based on current working directory

This utility will read through a directory and find terraform variables wherever they may be defined. No pattern enforcement. STDOUT is a rendered output of the result of what `-var` options you'll need to run Terraform with values applied, say in Github actions. 

https://github.com/andreimerlescu/report-tfvars

### `go-animate-coin` A tool for rotating a transparent PNG image 

This utility will take a transparent circular image and rotate it in a manner that looks like a coin moving from left to right in a circular manner. This was used for the `rAparioji3FxAtD7UufS8Hh9XmFn7h6AX` minted coin from the `rU16Gt85z6ZM84vTgb7D82QueJ26HvhTz2` [TrustLine](https://xpmarket.com/token/APARIO-rU16Gt85z6ZM84vTgb7D82QueJ26HvhTz2) token called [$APARIO](https://bithomp.com/explorer/rU16Gt85z6ZM84vTgb7D82QueJ26HvhTz2).

https://github.com/andreimerlescu/go-animate-coin

### `disk-speed` A tool for detecting disk read/write speed

This utility is written in Bash and will tell you, based on a sample, what your current directory/disk read/write speed is. This utility is particularly useful when you have I/O demanding applications that need SSD/NVMe speeds, you can run tests on your IOPS/Throughput on your EBS settings for a given path with this utility until you find the right setting you need for the given appliances' needs. 

https://github.com/andreimerlescu/disk-speed

### `extra-ssh-bash` A tool for running bash commands on multiple hosts at the same time using Go concurrency

This utiltiy is designed to run with Terraform and GitLab Managed State files where you can connect to your dynamic infrastructure results and execute commands on the hosts built. It doesn't work with all Terraform modules, it was designed in conjunction with `topobuilder` before `topobuilder` existed.

```bash
go build -o extra-bash-ssh .
./extra-ssh-bash . \
    --api "https://gitlab.com/api/v4" \
    --id 3 \
    --token "$(cat "${HOME}/.secrets/docker-cluster-access-token")" \
    --tfdir ~/work/terraform/docker-cluster \
    --user ubuntu \
    --key ~/.ssh/docker-cluster-master.pem \
    --bash "docker ps" \
    --json
```

This would run `docker ps` on each of your Terraform inventory items using your Managed State in GitLab using an SSH key. 

https://github.com/andreimerlescu/extra-ssh-bash

### `encrypted-luks-workspace` A tool for creating LUKS workspaces as mounted directories for encrypted filesystem creation

This utility is written in Bash and it is designed to run on a linux host, but it will create a LUKS workspace using a pattern in your `$HOME` directory. Your workspaces can be mounted when needed, encrypted at rest, and moved between hosts and universally accessed via this package. 

A strategy before `stronghold` project began, is to use this utility to create a directory to store secrets, such as SSH keys, GPG keys, etc. in an S3 bucket as your encrypted filesystem. Then pull the files down inheriting your IAM permissions, and then voila `$HOME/.secrets` gets mounted and your `domain_com.crt`, `domain_com.key`, and `domain_com.ca-bundle.crt` and `id_ed25519.pub` and `id_ed25519` and `.passwd.id_ed25519` and `.my-secret-name` as files that I could use in my application configuration paths. When the host reboots, the directory would need to get re-mounted. Crontab provides a solution to that. Then your app can have an encrypted workspace, versioned in S3, and mounted read-only; and voila, your app can have secure secrets in one pattern of security. Stronghold is going to be combining multiple strategies.

https://github.com/andreimerlescu/encrypted-luks-workspace 

### `configure-ebs` A tool for configuring AWS EBS instances attached to running Linux hosts

This utility is written in Bash and is designed to run on an EC2 instance in AWS. When a new EBS is attached to the EC2, `configure-ebs.sh` can let you mount/configure your `fstab` automatically based on the provided `vol-<id>` from AWS's console interface. This script was built so I could create an EC2 instance for GitLab Self-Hosted where I wanted to assign an EBS device for each of the various sub-components of GitLab. I ran: 

```bash
./configure-ebs.sh --device "/dev/nvme1n1" \
                   --label "gitlab_registry" \
                   --mount "/var/opt/gitlab/gitlab-rails/shared/registry" \
                   --fs "xfs" \
                   --fstrim "on"
```

This permits me to have `backups`, `ci_secure_files`, `terraform_state`, `public`, `artifacts`, `lfs-objects`, and `registry` as separate EBS volumes. Things like `lfs-objects`; why would I want `registry` to take away from the limit of 16TB per EBS? With this configuration, including the `root` drive at `/`, I have a maximum capacity of 16TB x 8 = 128TB of available storage where I'd be able to pay the minimal costs at `gp3` baseline performance for that capacity. For things like `terraform_state`, this can be an IO1 EBS and the [disk-speed](https://github.com/andreimerlescu/disk-speed) project can give you MB/s read/write speeds for the mounted directory inside `/var/opt/gitlab/gitlab-rails/shared/terraform_state` where you can run:

```bash
sh <(curl -sL https://raw.githubusercontent.com/andreimerlescu/disk-speed/main/disk-speed.sh) --path . --size 100M --no-sudo
```

yields:

```log
Path: .
Device: /dev/nvme0n1p1

Write Speed: 340.110 MB/s (took .294022381s to write 100M)
Read Speed: 325.024 MB/s (took .307669007s to read 100M)
```

That is sufficient for the `terraform-state` for GitLab. The registry can have its own speed and costs, and you can tag your volumes accordingly and keep tracking of billing that way. 

https://github.com/andreimerlescu/configure-ebs

### `go-common` A utility that powers Go applications

This utility provides packages called `basic`, `fibonacci`, `filesystem`, `hashing`, `spacetime`, `temere`, `version`. 

[GoPlay.tools](https://goplay.tools/snippet/Mx36bFCuMKp)

https://pkg.go.dev/github.com/andreimerlescu/go-common

### `textee` A utility for calculating substrings

Textee is the idea that a long string, like a paragraph or output of `tesseract` can be reduced into substrings of up to 1-3 words and then ranked by quantity of substring found within the result. Combine this with with the `gematria` tool, you'll understand why `apario-search` has 13 types of searches that it conducts.

[GoPlay.tools](https://goplay.tools/snippet/l9zpFIwqa67)

https://github.com/andreimerlescu/textee

### `gematria` A utility for calculating 6 types of Gematria

Gematria is the idea that each letter corresponds to a number, and the sum of the digits represents the Gematria value of a given string. The Gematria package offers this to programmers using Go who wish to manipulate their data using Go and Gematria. 

```go
package main

import (
	"fmt"
	"github.com/andreimerlescu/gematria"
)

func main(){
	name := "yah i love yahuah and yah i am saint andrei"
	gematria := gematria.FromString(name)
	fmt.Printf("name = %v ; gematria = %v", name, gematria)
}
```

[GoPlay.tools](https://goplay.tools/snippet/9YVcsz7C2UI)

https://github.com/andreimerlescu/gematria

### IP IsHere Dev

I can't count how many times I've asked, "what's my IP address?" and then immediately after trying to find a service that provides easy API access to such information, I am confronted 
with the harsh truth of advertising and tracking. Therefore, I created [IP IsHere Dev](https://github.com/andreimerlescu/ip.ishere.dev) that provides you with a clean API that provides
you with your WAN IP address, upon request - without tracking. You run it. That's how there's no tracking! The source code is open and available for all, but its also hosted at the 
URL [ip.ishere.dev](https://ip.ishere.dev). Interfacing with it is easy: 

```bash
curl -sL ip.ishere.dev | grep IPv4 | awk '{print $2}'
```

### Go INI

I created [Go INI](https://github.com/andreimerlescu/goini) that provides quick and easy access to manipulate and validate `.ini` files that you come across. For instance, the file
`~/.aws/credentials` and `~/.aws/config` are both **INI** files, and as such, they can be used against this binary for easy testing/verification purposes. 

There are two ways to install `goini`, the first is without using `Go` directly: 

```bash
curl -sL https://github.com/andreimerlescu/goini/releases/download/v1.0.0/goini-linux-amd64 \
         --ouput /tmp/goini
chmod +x /tmp/goini
sudo mv /tmp/goini /usr/local/bin/goini
which goini
```

And the second way is using `go install`...

```bash
go install github.com/andreimerlescu/goini@latest
```

You can use it against your system like so: 

```bash
goini -ini ~/.aws/config --section "profile sandbox" --json --list-keys
[
  "region",
  "output"
]
```

Other systems that actively use `.ini` files that I regularly use include Ansible, AWS, and some other projects. Overall, I created this tool so I could more programatically interact with `.ini` files as I need. Feel free to leverage this tool in your workflow as well!

### PhoenixVault / Project Apario 

**Reader** is the front-end application that uses a directory as a database source. [ProjectApario/reader](https://github.com/ProjectApario/reader) or [@andreimerlescu/apario-reader](https://github.com/andreimerlescu/apario-reader)

**Writer** is what renders the database directory source for the _reader_. [ProjectApario/reader](https://github.com/ProjectApario/writer) or [@andreimerlescu/apario-reader](https://github.com/andreimerlescu/apario-writer)

**Search** is a separate web socket interfacing app that lets you search through a compiled database from the _writer_. [ProjectApario/reader](https://github.com/ProjectApario/search) or [@andreimerlescu/apario-reader](https://github.com/andreimerlescu/apario-search)

## Beamable

I will publicly speak about what happened to me at Beamable. 

[![Typing SVG](https://readme-typing-svg.herokuapp.com?font=Fira+Code&weight=900&size=36&duration=6369&pause=1776&color=660099&vCenter=true&width=639&height=55&lines=3%2F17%2F25+I+CONTACTED+BEAMABLE;3%2F31%2F25+BEAMABLE+HIRED+ME;6%2F2%2F25+MY+1ST+HEART+ATTACK+%F0%9F%92%94;6%2F3%2F25+MY+9+YEAR+ANNIVERSARY!+%F0%9F%91%A9%E2%80%8D%E2%9D%A4%EF%B8%8F%E2%80%8D%F0%9F%91%A8;6%2F3%2F25+ADA+CLAIM+REPORTED+%F0%9F%8F%A9;6%2F17%2F25+%F0%9F%9B%AB+FLEW+TO+%F0%9F%9B%AC+VENICE+%F0%9F%87%AE%F0%9F%87%B9;6%2F21%2F25+MET+BIRTHMOTHER!!!+%F0%9F%92%9C;7%2F1%2F25+%F0%9F%9B%AB+FLEW+BACK+%F0%9F%9B%AC+HOME+%F0%9F%87%BA%F0%9F%87%B8;7%2F7%2F25+FORCED+BACK+ON-CALL+%F0%9F%93%B2;9%2F14%2F25+MY+2ND+HEART+ATTACK+%F0%9F%92%94;9%2F15%2F25+I+RESIGN+AT+BEAMABLE+%E2%9D%A4%EF%B8%8F%E2%80%8D%F0%9F%94%A5;9%2F16%2F25+I+SPEAK+WITH+ALI%2C+CTO!+%F0%9F%AB%B0%F0%9F%8F%BD;9%2F17%2F25+ON-CALL+RESUMES+%F0%9F%93%B2;9%2F22%2F25+EEOC+CLAIM+FILED+%F0%9F%8F%9B%EF%B8%8F;9%2F23%2F25+1ST+ADA+OFFICIAL+MEETING;9%2F24%2F25+FIRED+FOR+EEOC+FILING;9%2F25%2F25+PUBLIC+DISCLOSURE)](https://git.io/typing-svg)

The tried to character assassinate me by filing a frivolous law-fare lawsuit against me that the Kangaroo Court Liberal judge just quoted Ali's words, said incorrectly that they were my words, and then ruled against me.

Not only did the staff at Beamable use expensive attorneys to go after me, but they used their connections locally in order to throw their weight in the courts against me. 

I was not given due process. It was explicitly denied to me, where the Judge said, "you are not entitled to due process."

A pure accusation is a guilty sentence. 

Literally folks, this is Beamable. 

It's a radioactive, toxic company that needs to be shut down by the US Federal Government to protect US Citizens from further harm. 


