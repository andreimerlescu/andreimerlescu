### Hi there 👋, 

I enjoy writing Go. Here's a fun micro-project that I did to explore what happens when you try to close a closed channel... what if I didn't want a panic to get thrown? Is it possible? Why yes it is!

**safe_close.go**
```go
package main

func safeClose[T any](ch chan T) (closed bool) {
	// allow panic
	defer func() {
		if r := recover(); r != nil {
			closed = true
			return
		}
	}()
	_, chOpen := <-ch
	if chOpen {
		closed = true
		close(ch)
	} else {
		closed = false
	}
	return
}
````

**safe_close_test.go**
```go
package main

import (
	"github.com/stretchr/testify/assert"
	"testing"
)

func Test_safeClose(t *testing.T) {
	type args[T any] struct {
		ch chan T
	}
	type testCase[T any] struct {
		name string
		args args[T]
		want bool
	}

	// open channel with bool in buffer
	ch1 := make(chan bool, 1)
	ch1 <- true

	// closed channel with bool in buffer
	ch2 := make(chan bool, 1)
	ch2 <- false
	close(ch2)

	// closed channel with empty buffer
	ch3 := make(chan bool, 1)
	close(ch3)

	tests := []testCase[bool]{
		{
			name: "open channel with bool in buffer",
			args: args[bool]{ch1},
			want: true,
		},
		{
			name: "closed channel with bool in buffer",
			args: args[bool]{ch2},
			want: true,
		},
		{
			name: "closed channel with empty buffer",
			args: args[bool]{ch3},
			want: false,
		},
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			assert.Equal(t, tt.want, safeClose(tt.args.ch))
		})
	}
}

```

Then **go** run it....


```sh
go test .
```

Which returns:

```txt
=== RUN   Test_safeClose
--- PASS: Test_safeClose (0.00s)
=== RUN   Test_safeClose/open_channel_with_bool_in_buffer
    --- PASS: Test_safeClose/open_channel_with_bool_in_buffer (0.00s)
=== RUN   Test_safeClose/closed_channel_with_bool_in_buffer
    --- PASS: Test_safeClose/closed_channel_with_bool_in_buffer (0.00s)
=== RUN   Test_safeClose/closed_channel_with_empty_buffer
    --- PASS: Test_safeClose/closed_channel_with_empty_buffer (0.00s)
PASS

Process finished with the exit code 0
```

> [Check it out in the Go Playground...](https://go.dev/play/p/Z3HoVxJysLv)

<a href='https://ko-fi.com/P5P36GT3H' target='_blank'><img height='36' style='border:0px;height:36px;' src='https://storage.ko-fi.com/cdn/kofi2.png?v=3' border='0' alt='Buy Me a Coffee at ko-fi.com' /></a>

## Projects

I am a freelance software engineer architecting platforms written in Go that run on Rocky 9 Linux with SELinux enforcing. I am a professional DevOps Architect who uses Docker, Terraform, Ansible, Bash, GitHub Actions, GitLab CI/CD YAML, and builds for cloud providers like AWS, OCI and OpenStack. I avoid using Azure. I prefer cloud platforms that are built without windows in mind, the universe is much bigger than a window and limiting yourself to such, to me, doesn't make too much sense. Doing what I do on Windows was painful and I experienced it at WB Games. I ended up using a Rocky 9 Linux VM instead full time. 

### Designed To Serve Selflessly

My projects are designed to serve you and your needs without involving me in the process. Sure there is a learning curve involved, but with AI these days, it should be a walk in the park for anyone. The `igo` package, https://github.com/ProjectApario/igo -> https://github.com/andreimerlescu/igo, package allows you to run multiple versions of Go on the same user $HOME/go workspace and conviently use `igo -cmd list` and `igo -cmd install -gover 1.24.3` and `igo -cmd uninstall -gover 1.18.0` to manage their versioning. Additionally, after one version of Go has been installed, the shim at `$HOME/go/shims/go` will use the `igo -cmd install -gover <from your go.mod file>` without showing you any errors related to "You don't have go 1.17.6 installed!` errors, but rather you see `Go 1.17.6 is missing... installing now! .... go version go1.17.6 linux/amd64` instead. 

### My Projects

I spent the last few hours thinking and praying on the words to write in this post. I am who I am by the gace of my creator and I am happy to build fun software! This is my collection of contributions on GitHub that are open source that have been developed by me over the last 17 months. These contributions are designed to make programming in Go fun and easy for you to begin the learning process. I have starred repos if you want to learn, and there is a large open source project on GitHub at https://github.com/ProjectApario that hosts the `apario-reader`, `apario-writer`, and `apario-search` software that is powered by things like `figtree`, `checkfs`, `stronghold` and deployed with utilities like `topobuilder` and uses `igo` to manage multiple versions of Go. 

I love writing software. I write software for free for all to use it. I use it. I eat my own dog food. LOL I am the type of person that is, you wanna use my code, lets talk if you want to talk. I am all ears. I will not speak in the hearing of a fool, but I am not a fool to turn down listening when there is an opportunity to help you advance if it costs me just replying. The JFK files are hosted at https://jfkfiles.info and the DIA STAR GATE files are hosted at https://idoread.com and the Project Minnesota Election Integrity efforts of Erik are at https://electionselections.com. I have collections in the pipeline that will include the https://teslafiles.info and the https://stargatefiles.info. You can make your own instance! The software is free. It's battle tested. Highly configurable, and my more recent work is very well tested. The `igo` package has a testing suite to it that is both unit level testing and integration testing through a `run-on-local.sh` and the `tester.sh` underlying test script that dockerizes the test environment and battle tests the `igo` utility. Perhaps I need to consider a fuzz test on the unit test to find corner cases of bad input. 

In my `figtree` package, I reflected upon a near death experience that I had last year where I believe I met my creator. He introduced Himself to me as Yeshua the KING OF THE JEWS and that I am a programmer because He is a programmer. And I thought, let me intentionally see if I can unpack the story of the figtree through code. The terms used in the package are unique and faith inspired. I don't believe in dogma. If you asked me what I believe, be prepared to hear an 8 hour response. I understand much since stepping foot out of the orphanage. 

### Certificate Authority 

This utility is responsible for rendering your own Root Certificate Authority where you can issue X509 certificates. It's been minimally developed and has a fork in it that is looking to make it Go managed instead. It's a low priority item for me, but this package has given me the ability to establish zero trust networks that are TLS clusters of hosts that talk to each other over TLS controlled by my Root CA. This means no interception. 

https://github.com/andreiwashere/certificate-authority

##### Installing `igo` on MacOS (Apple Silicon)

```bash
mkdir ~/bin

# USING BASH
[ -f ~/.bashrc ] && echo 'export PATH=~/bin:$PATH' >> ~/.bashrc

# USING ZSH
[ -f ~/.zshrc ] && echo 'export PATH=~/bin:$PATH' >> ~/.zshrc

curl -L https://github.com/andreimerlescu/igo/releases/download/v1.0.3/igo-darwin-arm64 ~/bin/igo

cd ~/bin
xattr -d com.apple.quarantine igo
chmod +x igo
cd ~
which igo
igo -version

igo -cmd install -gover 1.24.3 --debug
```

##### Installing `igo` on MacOS (Intel)

```bash
mkdir ~/bin

# USING BASH
[ -f ~/.bashrc ] && echo 'export PATH=~/bin:$PATH' >> ~/.bashrc

# USING ZSH
[ -f ~/.zshrc ] && echo 'export PATH=~/bin:$PATH' >> ~/.zshrc

curl -L https://github.com/andreimerlescu/igo/releases/download/v1.0.3/igo-darwin-amd64 ~/bin/igo

cd ~/bin
xattr -d com.apple.quarantine igo
chmod +x igo
cd ~
which igo
igo -version

igo -cmd install -gover 1.24.3 --debug
```


##### Installing `igo` on Linux (amd64)

```bash
mkdir ~/bin

# USING BASH
[ -f ~/.bashrc ] && echo 'export PATH=~/bin:$PATH' >> ~/.bashrc

# USING ZSH
[ -f ~/.zshrc ] && echo 'export PATH=~/bin:$PATH' >> ~/.zshrc

curl -L https://github.com/andreimerlescu/igo/releases/download/v1.0.3/igo-linux-amd64 ~/bin/igo

cd ~/bin
chmod +x igo
cd ~
which igo
igo -version

igo -cmd install -gover 1.24.3 --debug
```

### `entpassgen` Entropy Password Generator

This utility is an **entropy** focused password generator with a basic word dictionary built into it. The `genwordpass` omits the entropy generation of the `entgenpass` utility. 

```bash
# Clone the repository, build then install
git clone git@github.com/andreimerlescu/entpassgen.git
cd entpassgen
make install # OR RUN THESE 4 ⬇︎⬇︎⬇︎⬇︎
go build -o entpassgen entpassgen.go
sudo mv entpassgen /usr/bin/entpassgen
chmod +x /usr/bin/entpassgen
entpassgen -h

# Install using Go
go install github.com/andreimerlescu/entpassgen@latest
entpassgen -h

# Download the binary and move
curl -o ./entpassgen -s https://github.com/andreimerlescu/entpassgen/releases/download/v1.0.0/entpassgen.linux-amd64
chmod +x entpassgen
sudo mv entpassgen /usr/bin/entpassgen
entpassgen -h
```

```bash
$ which entpassgen
/usr/bin/entpassgen

$ entpassgen -h
Usage of entpassgen:
  -E string
        Define exclude symbols in new password
  -L    Do not use lowercase characters in new password
  -N    Do not use numbers in new password
  -S    Do not use symbols in new password
  -U    Do not use uppercase characters in new password
  -W string
        Separate words with these possible characters (default "!@#$%^&*()_+1234567890-=,.></?;:[]|")
  -a    Generate new passwords to get average entropy, min entropy and max entropy calculated for options
  -e string
        Minimum entropy value to accept in new password (default "avg")
  -j    JSON formatted output
  -k int
        Quantity of passwords to generate when calculating average entropy (default 100000)
  -l int
        Character length in new password (default -1)
  -q int
        Quantity of passwords to generate (default = 1) (default 1)
  -s string
        Define acceptable symbols in new password (default "!@#$%^&*()_+=-[]\\{}|;':,./<>?")
  -t    TEXT formatted output (default) (default true)
  -w    Use words (ignores -U -L -S -E -N -s)

```

```bash
$ # Generate 1 New Password (Defaults)
$ entpassgen
uLbirj64,oPDaO5^&uLbirj64,oPDaO5^&

$ # Generate 1 New Password (JSON Output)
$ entpassgen -j | jq '.'
{
  "length": 17,
  "uppercase": true,
  "lowercase": true,
  "digits": true,
  "symbols": true,
  "value": "v>s81|/7:UmrzKXE{",
  "sample": {
    "limit": 100000,
    "average": 66.54055868500511,
    "recommended": 68.01371349313044,
    "min": 49.486868301255775,
    "max": 69.48686830125578
  },
  "entropy": {
    "score": 69.48686830125578
  }
}

$ # Generate Bogus Hebrew Words
$ entpassgen -L -U -N -S -s "אבגדהוזחטיכלמנסעפצקרשת" -l 12 -q 5
גקגתתעשנפכט
שבגדעהתלנמ
כדעפמכלכחת
כגטששתחטצת
טכלננמנסא

$ # Generate Random Number (9 digits long)
$ entpassgen -L -U -S -l 9 
804799183

$ # Generate Random String (A-Za-z0-9 only)
$ entpassgen -L -U -S -l 9 
804799183

$ # Generate 10 Random Passwords (Defaults)
$ entpassgen -q 10
r<GcVng4W$iA@JQ#\
x3Q^MT4q2Xmp,ly>G
t!LH7uvnNT9aiQDD>
ywEaYRpl\CWv;Jh52
Ebfh1sHNao2HwM8>u
,XTG@vUx2V{y0:7TC
;4W^w]E:t|ZqJ*WnU
X(og^y^*3nhRfq=7j
OcZSm[#>I.WjU,o>5
w:ng1H+jNtrL!2ETr

$ # Generate Random Password (memorable words)
$ entpassgen -w -l 3
misserve(kinematographic]daunii4

$ # Analyze Strong Passwords
$ entpassgen -w -a
Entropy Report: 
  Samples: 100000
  Length: 5
  Uppercase: true
  Lowercase: true
  Digits: true
  Symbols: true
  Use Words: true
  Average: 221.935
  Minimum: 141.402
  Maximum: 328.852
  Recommended: 275.393

$ entpassgen -a   
Entropy Report: 
  Samples: 100000
  Length: 17
  Uppercase: true
  Lowercase: true
  Digits: true
  Symbols: true
  Use Words: false
  Average: 66.534
  Minimum: 53.487
  Maximum: 69.487
  Recommended: 68.010
```

This was a fun project. 

https://github.com/andreimerlescu/entgenpass

### `counter` A tool for counting things

This utility was inspired by `p4 counter` from Perforce. It's such a great and simple tool, that something like it shouldn't be coupled to the `p4` suite itself. It's useful in DevOps, and as a DevOps Architect, I have found use in the `counter` package. It's very powerful and offers you a clean interface that is manipulated by your environmental preferences, whomever you may be. It's built to work around you. 

```bash
go install github.com/andreimerlescu/counter@latest
[q@localhost]~% counter -v
1.0.1
[q@localhost]~% counter -env
COUNTER_USE_FORCE=false
COUNTER_NEVER_ADD=false
COUNTER_NEVER_RESET=false
COUNTER_NEVER_DELETE=false
COUNTER_NEVER_SET_TO=false
COUNTER_NEVER_SUBTRACT=false
COUNTER_DIR=/tmp/.counters
COUNTER_QUANTITY=1
[q@localhost]~% counter -name subscribers 
Error: directory /tmp/.counters does not exist
[q@localhost]~% counter -name subscribers -F
0
[q@localhost]~% counter -name subscribers   
0
[q@localhost]~% counter -name subscribers -add
1
[q@localhost]~% counter -name subscribers -sub
0
[q@localhost]~% counter -name subscribers -set 20
20
[q@localhost]~% counter -name subscribers -reset 
will reset counter subscribers to 0 after you re-run with -yes
[q@localhost]~% counter -name subscribers -reset -yes
0
[q@localhost]~% counter -name subscribers            
0
[q@localhost]~% counter -name subscribers -delete
deleting counter subscribers (0) when you re-run with -yes
[q@localhost]~% counter -name subscribers -delete -yes
counter subscribers deleted
```

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

### `apario-reader` A tool for hosting `apario-writer` rendered PDF archives with full text search and dark mode

This utility takes a rendered directory, like `/apario/app/jfk-files` that is 385GB that contains 83K pages of declassified JFK Files on https://jfkfiles.info allows you to host declassified documents, like the STARGATE files from the Defense Intelligence Agency (https://idoread.com). Yes, this app is open-source and can be used for ANY collection. You use the `apario-writer` to generate it. If you need to take HTML and compile your CSV from importing some website of PDFs, you can use the `apario-writer-html2csv` utility. Feel free to fork it and use AI to modify it. Any collection can be used! Raven Squad Army is where instance details are held. This is a public service for God and Country. I believe that President Kennedy would want his files to be searchable. I was disappointed when the National Archives released them unsearchable. As somebody who could do something about it, I did something about it. Yeshua helped me. Thank Yeshua. Now its truly decentralized and truly a PhoenixVault that is powered by the $APARIO token. You can learn more about that later if you're reading through this post. May Yeshua bless you. The I AM is inside of you too! I told you that you were an IS-BE. I wasn't lying when I said it. It's literal. IS-BE === IAM-ANDREI. I AM AN IS-BE === I AM ANDREI === I AM + ANDREI === YESHUA + ANDREI === WE. This project also hosts [Election Selections](https://electionselections.com/) from the Project Minnesota efforts of Mr Erik van Mechelen, former MN Secretary of State candidate. 36.9% of the vote with staying true to the core message. The evidence of his efforts, inspired by my suggestion, manifested in real change in a real community. Erik knows Yeshua.

https://github.com/ProjectApario/reader or https://github.com/andreimerlescu/apario-reader

### `apario-writer` A tool for generating PDF archives with full text search and dark mode

This utility takes a CSV file of URLs or Paths to PDFs that has metadata like total pages, source URL, name of document, (optional are description and keywords). Then it'll render a directory that contains light/dark rendered small, medium and original progressive JPEG files with split `<name>.pdf` into `<name>_<pg-no>.pdf` with a `page.<6-0-padded-pg-no>.json` and `ocr.<6-0-padded-pg-no>.txt` for the `tesseract` output. The `JSON` file contains the `textee`, `gematria`, `cryptonym`, `date`, and `Locations` matches for the document to be later indexed. 

This output rendered the 2025 JFK files into 385GB of data for 83K pages. The pages load in under a quarter of a second. Search is less than second. Since launching, it's been hit over 800K times! 

### `apario-search` A tool for performing 13 types of complex search on `apario-writer` rendered results to web socket and REST requests with caching

This utility will take an `apario-writer` output directory, such as `/apario/app/jfk-files` for https://jfkfiles.info/ and will expose an `https://` and `wss://` endpoint on port `8888` where you can hit `/search?q=<uery>`. This utility will perform 13 unique types of search on the archive. The results for `top secret` took 12 minutes. It analyzed like-gematria and used the power of 369 to find a collection that you could potentially stumbleinto. The `/stumbleinto?q=<uery>` allows you to hit results one at a time, and the endpoint doesn't kick off a new search. It can be hit as many times as the rate limiter permits. Your `figtree` `config.yaml` will define those values. 

This utility will be merged into the `apario-reader` in the form that search will be replaced internally to the `apario-reader` and swapped out with this new search. It'll also add StumbleInto search results and let the instance keep track of search results and hits/views on the results. 

https://github.com/ProjectApario/search or https://github.com/andreimerlescu/apario-search

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

### `stronghold` A tool for securely exposing path keyed key/value secrets to a host using S3 GPG Encryption and `universal-iam` permissions of securing read/write permissions on the Stronghold

This package is designed to allow me to create a GPG encrypted `.asc` file, store it in S3, with versioning and object locking enabled upon bucket creation such that the stronghold vault storage will be an S3 target. When `stronghold` boots, it relies on its IAM access and an environment variable that contains its access key into the stronghold, the key unlocks the stronghold for that user. Once the stronghold is loaded in the system, decrypted by the AES encryption key, the remaining `.asc` is GPG armored and requires unsealing keys in order to access the secrets contained. These secrets then get exposed on `GET https://127.0.0.1:1776/stronghold/get?path=my/secret/path&field=keyName` or `POST https://127.0.0.1:1776/stronghold/set?path=my/secret/path  {"keyName": "valueOne", "keyTwo": "valueTwo"}`. When new secerts are created, the secrets get locked in S3, a new record is committed, and then all hosts receive the new `stronghold` file through a sync mechanism that runs every minute. Stronghold is currently in development and is closed source at the moment. Because of its security implications in running it in production, it's designed to solve a very business specific need. It's not needed for open-source development projects or anything that isn't explicitly sensitive. Therefore, to use this software, reach out to me, and show me that you hold over 33M $APARIO or over 33M $THOTH, or 33M $IAM, or 369,369,369,369+ $YAHUAH, 33B $RAHKI, 1M $PLAYANDPROSPER then you'll automatically qualify for access to the stronghold project and a non-disclosure agreement will be presented to you where the source of the project remains the intellectual property of Andrei Merlescu and use of the software comes with no assurances, guarantees or refunds of any kind. Use of the software is considered volunteer good-faith services to improve your brand without assuming any liability. It's to help not to harm and I do not want to be harmed when I help, so those are the terms. The crypto token projects are not financial advice and I am the developer of those tokens. I have projects planned for all of them. I believe in my rock, Yeshua, and I know that it will happen at the right time. Each project has its own purpose and will NOT be built by me, but by my team instead!

https://github.com/andreimerlescu/stronghold

### `figtree` A command line utility interface builder that powers all of my open source software

The `figtree` package is a command line configuration interface builder that lets you define `Fig` Mutagenesis like String, List, Int64, etc.. The package is biblically inspired by Yeshua Ben Joseph. If life was programmed to be a simulation, how can the figtree reveal inner truths of how we handle command line interfaces? This package supports using an ENV `CONFIG_FILE` that will load `.yml|.yaml|.json|.ini` config files into a `Plant` that has `Callbacks`, `Validators`, and `Rules` with the ability to load/save a Plant from a file at will. Figtree can be used in many application types, and is being explored to provide secret sourcing such that you can pass in `op://` or `aws::arn` path to import the secret with the ability to define and build n-implementations of the Secret interface. 

```bash
go get github.com/andreimerlescu/figtree/v2
```

```go
const kWorkers string = "workers"
const kSeconds string = "seconds"
figs := figtree.With(figtree.Options{Tracking: true})
figs.NewInt(kWorkers, 17, "number of workers")
figs.NewUnitDuration(kSeconds, 76, time.Second, "number of seconds")
go func(){
  for mutation := range figs.Mutations() {
    log.Printf("Fig Changed! %s went from '%v' to '%v'", mutation.Property, mutation.Old, mutation.New)
  }
}()
err := figs.Load()
if err != nil {
   log.Fatal(err)
}
workers := *figs.Int(kWorkers)
seconds := *figs.UnitDuration(kSeconds, time.Second)
minutes := *figs.UnitDuration(kSeconds, time.Minute) // use time.Minute instead as the unit
fmt.Printf("There are %d workers and %v minutes %v seconds", workers, minutes, seconds)
```

https://github.com/andreimerlescu/figtree

### `apario-writer-html2csv` A tool for converting NARA HTML into `apario-writer` input CSV as `source` paths to `.gov|.mil` authentic sources

This utility was designed for the 2025 JFK files released by the National Archives and Records Administration. Hosted at https://jfkfiles.info, this collection of data was compiled using the `apario-writer` application, but the initial CSV to render that collection was missing. This utility takes the HTML table of columns, parses it, and renders a CSV import that `apario-writer` can use. 

https://github.com/andreimerlescu/apario-writer-html2csv

### `summarize` A tool for summarizing content in your current workspace for AI ingestion

This utility is installed via `go install github.com/andreimerlescu/summarize@latest` and its super useful if you use xAI's SuperGrok like I do! The context is super large and the DeepResearch / Think capabilities of the AI surpass that of even Claude 4 and anything that OpenAI can come up with; in my humble opinion based on a 4 year research analysis of the various AI tools and capabilities. When I am working on open source projects, I run `summarize` in my workspace, a new folder called `summaries` gets added, and the timestamp copy of the summary. 

```bash
go install github.com/andreimerlescu/summarize@latest
cd ~/GolandProjects/myproject
summarize
find ./summaries -type f -name 'summary.*.md'
cat summaries/summary.2025.05.25.01.04.37.UTC.md
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
# GitLab Registry
./configure-ebs.sh --device "/dev/nvme1n1" \
                   --label "gitlab_registry" \
                   --mount "/var/opt/gitlab/gitlab-rails/shared/registry" \
                   --fs "xfs" \
                   --fstrim "on"

# Git Large File Storage
./configure-ebs.sh --device "/dev/nvme2n1" \
                   --label "gitlab_lfs_objects" \
                   --mount "/var/opt/gitlab/gitlab-rails/shared/lfs-objects" \
                   --fs "xfs" \
                   --fstrim "on"

# Artifacts
./configure-ebs.sh --device "/dev/nvme3n1" \
                   --label "gitlab_artifacts" \
                   --mount "/var/opt/gitlab/gitlab-rails/shared/artifacts" \
                   --fs "xfs" \
                   --fstrim "on"

# Public Uploads
./configure-ebs.sh --device "/dev/nvme4n1" \
                   --label "gitlab_uploads" \
                   --mount "/opt/gitlab/embedded/service/gitlab-rails/public" \
                   --fs "xfs" \
                   --fstrim "on"

# Terraform State
./configure-ebs.sh --device "/dev/nvme5n1" \
                   --label "gitlab_terraform_state" \
                   --mount "/var/opt/gitlab/gitlab-rails/shared/terraform_state" \
                   --fs "xfs" \
                   --fstrim "on"

# CI Secure Files
./configure-ebs.sh --device "/dev/nvme6n1" \
                   --label "gitlab_ci_secure_files" \
                   --mount "/var/opt/gitlab/gitlab-rails/shared/ci_secure_files" \
                   --fs "xfs" \
                   --fstrim "on"

# Backup Directory
./configure-ebs.sh --device "/dev/nvme7n1" \
                   --label "gitlab_backups" \
                   --mount "/var/opt/gitlab/backups" \
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

If you want me to set this up for your GitLab Self-Managed instance, it can be provided to you by showing me that you own at least 50M $APARIO tokens or 170B+ $YAHUAH or 17+M $XPQ or 1+M $PLAYANDPROSPER or 369M+ $RAHKI tokens. Your preference on which to choose, but its not financial advice and these XRP tokens can be used for whatever purpose you choose. I plan on building with them, but I am not giving you financial advice. I am saying that if you want me to run for you `configure-ebs.sh` on your Self-Hosted GitLab server, I would be more than happy to do it for you, but to show me that you sow seeds where you wish to reap, I ask that you do it this way and then I'll help you run this script. I don't get paid when those tokens are purchased/held as they are managed by the blockchain's smart contracts and XPMarket's issued AMM liquidity pools that support the token and its holders. When the token value surges because of these kind of transactions, other wallet holders will buy/sell as they please. I am holding onto my tokens and am not spending them on things. I need them. 

https://github.com/andreimerlescu/configure-ebs

### `go-common` A utility that powers Go applications

This utility provides packages called `basic`, `fibonacci`, `filesystem`, `hashing`, `spacetime`, `temere`, `version`. 

```go
package main

import (
   "fmt"

   "github.com/andreimerlescu/go-common/temere/v2"
)

func main(){
   mathRandString := temere.String(32) // random 32 character string using math.Rand
   cryptoRandInt := temere.Integer(8) // random integer up to limit (8) using crypto.Rand
   numberInRange := temere.RangeInteger(1,999) // random integer using crypto.Rand between 1,999

   fmt.Println(mathRandString, cryptoRandInt, numberInRange)
}

```

[GoPlay.tools](https://goplay.tools/snippet/Mx36bFCuMKp)

https://pkg.go.dev/github.com/andreimerlescu/go-common

### `textee` A utility for calculating substrings

Textee is the idea that a long string, like a paragraph or output of `tesseract` can be reduced into substrings of up to 1-3 words and then ranked by quantity of substring found within the result. Combine this with with the `gematria` tool, you'll understand why `apario-search` has 13 types of searches that it conducts.

```go
package main

import (
    "fmt"
    "os"
    "errors"

    "github.com/andreimerlescu/textee"
)

const inputString = `All right let's move from this point on 16 March 84, let's move in time to our second location which is a specific building near where you are now. Are you ready? Just a minute. All. right, I will wait. All right, move now from this area to the front ground level of the building known as the Menara Building, to the front of, on the ground, the Menara Building.`

func main() {
    // just calculate the substrings (no Gematria)
    tt1, err := textee.NewTextee(inputString)
    if err != nil {
        _,_ = fmt.Fprintf(os.Stderr, "%v", errors.Join(textee.ErrBadParsing, err))
    }

    // calculate a new substring with Gematria 
    var err2 error
    tt1, err2 = tt1.CalculateGematria()
    if err2 != nil {
        _,_ = fmt.Fprintf(os.Stderr, "%v", errors.Join(textee.ErrBadParsing, err2))
    }
    for substring, quantity := range tt1.Substrings {
        fmt.Printf("substring '%v' has %d occurrences\n", substring, quantity.Load())
    }

    // combine them together
    tt2, err3 := textee.NewTextee(inputString)
    if err3 != nil {
        _,_ = fmt.Fprintf(os.Stderr, "%v", errors.Join(textee.ErrBadParsing, err3))
    }
    var err4 error
    tt2, err4 = tt2.CalculateGematria()
    if err4 != nil {
        _,_ = fmt.Fprintf(os.Stderr, "%v", errors.Join(textee.ErrBadParsing, err4))
    }
    fmt.Println(tt2)

    // sort the substrings by the quantity of occurrences in the original string, most common are first
    sortedSubstrings := tt2.SortedSubstrings()
    for idx, substring := range sortedSubstrings {
        fmt.Printf("%d: substring '%v' has %d occurrences\n", idx, substring.Substring, substring.Quantity)
    }
}

```

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

Of course numerology reveals to you that in Simple Gematria that phrase is 306 which if 0 is 9 according Nikola Tesla, then 396 can be arranged as 369 which would make Nikola Tesla very happy. Seems like the numbers have meaning, I wonder what the original string was that yielded I AM from Iodine (53 w/ 126.904) and Americium (95 w/ 243.061) gives you with the two larger numbers 369.965, so in essence "I AM = 369" and so if "yah i love yahuah and yah i am saint andrei" = 369 = "I AM". Hrm. Was Nikola Tesla onto something here?

https://github.com/andreimerlescu/gematria

## Purpose of this page 

Well, I have had my GitHub profile for a very long time. I express who I am online through my contributions and while I stopped contributing to normal social media, my contributions are in the form of building open source software. If you want to support my efforts, you know what to do. I have opened the windows of opportunity for those who have faith to sow seeds where they wish to reap. The major engineering effort of the Apario Reader / Writer / Search trio powered by another trio of software like textee, gematria, and figtree. The summarize package levels me up with AI prompts on Grok and Co-Pilot. If you commit your summaries, then "versions" of your code will be preserved "all-in-one" that you can ask Co-Pilot to compare explicit verion implementations without looking at actual git-tree history. For real programmers, that matters. Sometimes, you just want to know whats going on between two revisions only; and this gives you that ability. It gives me the ability too. Additionally, when I am deploying apps with Topobuilder, I am using the `encrypted-luks-workspace` to securely keep copies of the secrets protected in archive form and mounted while the host is running with an automatic unsealing mechanism built into the systemd on boot enabled. These projects are intended to help you too. If you find any use in them, make sure to add a STAR ⭐! 
