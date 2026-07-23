\- \[ [&larr; Previous Page _Projects_](/PROJECTS.md) \] &middot; \[ **OPEN SOURCE** \] &middot; \[ [Next Page _AI_ &rarr;](/AI.md) \] -

# Open Source

I formalize problems I encounter into reusable packages rather than one-off scripts. Most of what's here started as a real operational need — something I hit at a client or in my own infrastructure — and became a proper package so others can use it too.

### Infrastructure tooling

My professional career began in the data centers of Cisco's Boxborough Campus - infrastructure is built into my career since the first day I started professionally.

- [reconcile-tfstate](https://github.com/andreimerlescu/reconcile-tfstate) — recover corrupted Terraform state files; battle-tested at Beamable
- [configure-ebs](https://github.com/andreimerlescu/configure-ebs) — mount/unmount AWS EBS volumes on EC2 with filesystem and fstrim options
- [encrypted-luks-workspace](https://github.com/andreimerlescu/encrypted-luks-workspace) — LUKS encrypted volumes for sensitive application configs
- [extra-ssh-bash](https://github.com/andreimerlescu/extra-ssh-bash) — run the same command across N Linux hosts simultaneously
- [disk-speed](https://github.com/andreimerlescu/disk-speed) — per-volume disk speed benchmarking from the command line

### AI + developer workflow

This workstation exists so I can run inference locally and privately — no API calls, no data leaving the machine. This is to provide services under regulatory clarity.

- [summarize](https://github.com/andreimerlescu/summarize) — aggregate a codebase into a single markdown file; chat with it via local Ollama LLM
- [aigcm](https://github.com/andreimerlescu/aigcm) — AI-generated git commit messages from `git diff`, powered by Ollama `qwen3:8b`

### Go packages + CLI utilities

I learned Go and built a handful of commercial and enterprise scale applications in Go (3 completed each over 10K lines). In addition to that, while learning Go, I built a handful of packages to help me master the patterns of Go. I use the packages I write. Making them open source, allows you to run them as well and work off from them as well. 

- [lemmings](https://github.com/andreimerlescu/lemmings) - simulate real world load testing with lemmings
- [sovereign](https://github.com/andreimerlescu/sovereign) - deploys `lemmings` in multiple regions for large scale load testing
- [room](https://github.com/andreimerlescu/room) - drop in waiting room for go web apps that use gin for routing
- [figtree](https://github.com/andreimerlescu/figtree) — unified CLI flag / env / config file management
- [sema](https://github.com/andreimerlescu/sema) — semaphore primitives for Go concurrency control <sup><span style="color:red;">Updated Spring 2026</span></sup>
- [checkfs](https://github.com/andreimerlescu/checkfs) — cross-platform filesystem path abstractions (Linux, macOS, Windows)
- [bump](https://github.com/andreimerlescu/bump) — VERSION file management for CI pipelines
- [verbose](https://github.com/andreimerlescu/verbose) — logging package that censors secrets in stdout while writing them to disk
- [igo](https://github.com/andreimerlescu/igo) — install and manage multiple Go versions without sudo

## My Open Source Story

After the pandemic, I decided to change fundamentally what was more than a decade of professional 
experience that was contributed behind closed doors in a way that proved my value year over year 
making what I was making. In 2020, I was asked to learn Go, didn't comprehend the fundamentals yet, 
and by 2021 I was formally learning Go and wanted to master the language. By 2025, I would say that 
I have accomplished many great things in the language. Since 2021, I have committed my Go learnings 
to be framed in a pro-open source manner. 

I'm used to building apps from start to finish. Different kind of apps. Some web apps. Some CLI apps. 
Some data pipeline apps. Some apps in AppleScript. Some apps in Swift. Some apps in C++. Some apps in 
Objective-C. Some apps for macOS. Some apps for Windows. Some apps for Linux. Some apps for Windows. 
No apps for Android. In the age of AI, this can be accomplished by anybody who has the plain English
to ask simply for what they want. What I demonstrated over 30 years since my first computer, was a 
curiosity that was satisfied when the answer was presented to me. I didn't just stop with, well the 
training data has been exhausted and we have no idea how to fix it; rather I am the relentless type 
of engineer that stays on a problem, sometimes for weeks, in order to come to a solution that not 
only works, but can be tested and proven to work. Thus, in 2026 I have driven myself to build more 
and more open source software. At the end of the day, with AI capable now, building these programs 
that I have needed for many years, all within a matter of months, unlocks things for my consulting 
opportunities for _your benefit_. You see, with [figtree](https://github.com/andreimerlescu/figtree) 
the flagship CLI configuration utility that I have built and used in dozens of projects both in a 
personal project capacity and in production code that runs in Fortune&reg; 100&trade; companies. 

## `summarize`

This is one of my **go to programs** that I wrote. It doesn't _summarize_ something _per-se_ in any capacity, 
but rather what it does is _summarize a directory_ by capturing the status of every _file_ in the directory, 
recrusively, looking for plain text files (and omitting secrets), to render a summary file of a given directory.

    go install github.com/andreimerlescu/summarize@latest

## `figtree`

This package is called [figtree](https://github.com/andreimerlescu/figtree) and its an attempt at me understanding 
the programming of life itself by simulating the life of a _fig tree_ in the lifecycle of a **configuration utility** 
in the language of Go, by offering callbacks, validations and type safety. I have other features planned for it, but 
it serves most purposes that I have needed in basic CLI utilities that are designed with DevOps in mind. 

    go get -u github.com/andreimerlescu/figtree/v2

## `counter`

The universal [counter](https://github.com/andreimerlescu/counter) is a basic Go application designed for storing 
a _counter_ in a text file and then safely increase or decease that value. In DevSecOps, the `counter` package has
been used to keep track of events happening on a system, in a conspicious way that is very difficult to tamper with
unless you explicitly know what to look for - and then you can run checks against those counters and perform actual
intelligence operations on the host itself. 

    go install github.com/andreimerlescu/counter@latest

## `room`

In 2015 when I launched Trakify and was in Yokohama Japan, I needed the [room](https://github.com/andreimerlescu/room) 
package for my Ruby on Rails web application. When I got featured in [Barrons Magazine](https://www.barrons.com/articles/trakify-keeps-you-current-on-your-securities-portfolio-1444457064?gaa_at=eafs&gaa_n=AWEtsqdExXelmOtklurLNX3j9Fqwakc8j8R3xsFFD-qZ7xGd_lXqYro00iG6FoeIXFE%3D&gaa_ts=69d1a438&gaa_sig=TkgzA7hWBhVwj6ggpKRLL8t0tG0Tj6gZBUAlLDqGe6VzU7h4y1Ozis7EkkzIycTrlmSihmhvJ9dgWoTw-ARKQw%3D%3D) 
I got immediately slammed with a bunch of connections that reached my software. Trakify was my real world experience, and
it got the attention of **Mike Hogan**. Had I built and used the  [room](https://github.com/andreimerlescu/room) I wouldn't 
have had the launch experience that I had. When my cluster was scaling up and things were collapsing under pressure, the 
[room](https://github.com/andreimerlescu/room) offers a better experience that allows for appliance based apps, like the 
[reader](https://github.com/ProjectApario/reader) has. Effectively, that middleware was extracted into its own self 
contained packaged that is extensively tested with a powerful [test.sh](https://github.com/andreimerlescu/room/blob/main/sample/basic-web-app/test.sh) 
script. 

    go get -u github.com/andreimerlescu/room@latest

## `lemmings`

Given that the Project Apario [reader](https://github.com/ProjectApario/reader) application has experienced, on popular 
collection sets, over 500 concurrent readers at a time being served. Over 5 years of collecting data in this manner, I 
have been able to build out a concept of lemmings that replicates the _StumbleInto&trade;_ feature. 

[lemmings](https://github.com/andreimerlescu/lemmings) load tests web application endpoints with reports and observability
while it is running. Lemmings are broken into terrains that have their own pack that are basically using _StumbleInto&trade;_ 
against their own _sitemap_, _robots.txt_, or _crawled_ paths. So, I put it in code. This way, in order to say with confidence 
it will _take this_ to _run this_, the [lemmings](https://github.com/andreimerlescu/lemmings) package allows for that. 

    go install github.com/andreimerlescu/lemmings@latest
    
## `sovereign`

The code is free, but the implementation is where my services are needed. [sovereign](https://github.com/andreimerlescu/sovereign)
was designed to deply [lemmings](https://github.com/andreimerlescu/lemmings) across multiple regions in either **AWS**, **OVH** 
or **DigitalOcean**. Since you know what [lemmings](https://github.com/andreimerlescu/lemmings) does, [sovereign](https://github.com/andreimerlescu/sovereign)
takes that binary and runs it in n-<em>Regions</em> of your choice. This package is a 2026 upgrade and port of [topobuilder](https://dev.to/andreimerlescu/topobuilder-brings-a-cpanel-website-into-an-aws-multi-region-containerized-application-with-an-1fpa) designed specifically for [lemmings](https://github.com/andreimerlescu/lemmings). 

    go install github.com/andreimerlescu/sovereign@latest

## `goini`

When managing multiple sandbox AWS accounts, I ended up needing to manage `.ini` files quite often. In CI pipelines 
interacting with these `.ini` files was a pain, so I built [goini](https://github.com/andreimerlescu/goini) so that 
programatic interaction with `.ini` files is capable. 

    go install github.com/andreimerlescu/goini@latest

## `goenv`

After I got a taste of interacting with [goini](https://github.com/andreimerlescu/goini) in CI pipelines, I wanted 
the same with `.env` called [goenv](https://github.com/andreimerlescu/goenv).

    go install github.com/andreimerlescu/goend@latest

## `genwordpass`

This package is a **multilingal** _random password generator_ that uses a combination of uppercase, lowercase and
symbols in order to randomly generate strong and memorable passwords. There are **five (5)** languages included in
the package including **Romanian**, **German**, **Russian**, **Spanish**, and **English**. 

    go install github.com/andreimerlescu/genwordpass@latest

## `entgenpass`

This package is a different attempt at random password generation. Instead of focusing on memorable passwords that are
big in memory ([genwordpass](https://github.com/andreimerlescu/genwordpass) takes a few seconds to load but its strong).
This package is [entgenpass](https://github.com/andreimerlescu/entgenpass) and its responsible for random strings that 
are calculated with entropy strength. It's a similar generation time for a password, but its a different approach. Both 
packages in my judgement are safe to use. 

    go install github.com/andreimerlescu/entgenpass@latest

### When you want to learn more information

To get a better sense of me and my capabilities outside of my repositories, feel free to continue reading below.

| Sub-Page | Description | 
|------|-------|
| [README.md](/README.md) | My Professional GitHub Profile |
| [CONSULTING.md](/CONSULTING.md) | I offer **consulting services** to companies that need _custom services_. |
| [PROJECTS.md](/PROJECTS.md) | I offer **free applications** to anybody who have _similar needs_. |
| **THIS PAGE** [OPENSOURCE.md](/OPENSOURCE.md) | I offer **free and open source** software on my GitHub profile. |
| [AI.md](/AI.md) | I have an advanced AI capable workstation for development. |
| [IS-BE.md](/IS-BE.md) | I break down my pronouns for the unicorn sluether. |
| [FULLTIME.md](/FULLTIME.md) | I can be hired **full time** for the right match. |
| [PERSONAL.md](/PERSONAL.md) | I am a real human with a real story. |

\- \[ [&larr; Previous Page _Projects_](/PROJECTS.md) \] &middot; \[ **OPEN SOURCE** \] &middot; \[ [Next Page _AI_ &rarr;](/AI.md) \] -
