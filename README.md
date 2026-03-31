### Hi there 👋, 

## Andrei Merlescu (formerly Michael Trimm)

My name is Andrei. I used to be called Michael. Some people in my family still call me that. My name is Andrei. What do you need?

| Qualification | Experience |
|--------|--------|
| Years of Experience | Since 2009 (17y) |
| Top Languages | `Go`, `Ruby`, `PHP`, `Bash` + AI (MCP + RAG + API) |
| Primary Workstation | M3 Mac Studio w/ 256GB RAM |
| Secondary Workstation | MacBook Pro w/ 48GB RAM |
| Tertiary Workstation | Cellular iPad Pro |
| Full Time Rate | starting at `$170K/yr` w/ Benefits |
| Maximum Weekly Hours | 63 Hours (Across no more than 2 clients) |

I have a 256GB RAM M3 Mac Studio capable of running 80B+ parameter models locally yielding over 80 tokens per second with an initial response of under 2 seconds for most requests that include context windows up to 2M tokens. I have licenses to software already and do not require equipment to begin working. I have a strong portfolio of open source contributions and I have a long history of stable employment.

### Rates & Availability

The $2,000/mo retainer covers up to 10 hours of consulting — nothing is billed on top of the retainer. Time is billed in 20-minute units<sup>[1]</sup>, rounded up to the next unit. Additional hours beyond the retainer are billed at $200/hr.

<sup>[1]</sup> 1 unit = 20 min = $40

## Professional Career

| Website | Years | Title | Technologies |
|---------|-------|-------|--------------|
| [MuggleNet.com](https://mugglenet.com) | 2003-2005 | Volunteer Developer | `LAMP` |
| [SawmillMag.com](https://sawmillmag.com) | 2005-2020 | Website Architect | `LAMP`, `Ruby` |
| [Cisco.com](https://cisco.com) | 2009-2016 | Senior Software Engineer | `Perl`, `LAMP`, `Ruby`, `JS` |
| [Oracle.com](https://oracle.com) | 2017-2020 | Senior Release Manager | `Ruby`, `Go`, `HCL`, _policy management_. |
| [PhoenixVault](https://jfkfiles.info) | 2019-2025 | Founder | `Ruby on Rails`, `Go`, `Bash`, `HCL` | 
| [BitFry.com](https://www.linkedin.com/company/bitfry) | 2021 | Senior DevOps Architect | `HCL`, `Bash`, `Ruby` |
| [WBGames.com](https://wbgames.com) | 2021-2024 | Senior Release Engineer | `Go`, `C++`, `C#`, `Bash` |
| [SurgePays.com](https://surgepays.com) | 2022-2025 | Consultant | `Go`, `C++`, `Bash` |
| [Beamable.com](https://beamable.com) | 2025 | Senior DevOps Engineer | `HCL`, `Go`, `Bash`, `C#` |
| [SurgePays.com](https://surgepays.com) | 2026 | Cloud Architecture | `Go`, `C++`, `Bash` |

## Projects 

### Products

#### PhoenixVault / Project Apario 

This is a compilation of 3 components called [reader](https://github.com/ProjectApario/reader) (that renders the collection of OSINT), the [writer](https://github.com/ProjectApario/writer) (the utility that renders the OSINT into a reader format) and the [search](https://github.com/ProjectApario/search) that indexes the OSINT using the [textee](https://github.com/andreimerlescu/textee) package while including extra indexes for [gematria](https://github.com/andreimerlescu/gematria). When utiltized, a CSV can be compiled container 4 columns of meta data and a clone of PhoenixVault - single binary with a better search functionality - can be launched for any collection of OSINT. No license is required. No consulting is required. Licensed under GPL-3 and AGPL-3. 

#### Summarize 

This package is called [summarize](https://github.com/andreimerlescu/summarize) and its responsible for taking the current working directory `pwd` that you're in on a linux or windows based system, and capturing the text files in that directory plus any sub directories and then aggregating them into an organized markdown file for an AI to ingest in the future. The `-ai -chat` flags, powered by [figtree](https://github.com/andreimerlescu/figtree), allow you to interact directly with Ollama and use your own _local_ AI LLM with extended 2M+ token context windows and _chat_ with your workspace, in a private "offline" manner that doesn't require subscriptions or an internet connection. The chat functionality is powered by a BubbleTea TUI that I customized and leveraged vi-like navigation in conjunction with arrows and other normal TUI navigation means. 

#### Terraform State Reconciliation

When Beamable's state file got corrupted after a contractor attempted to make their single region AWS Cloud infrastructure multi-region, the [reconcile-tfstate](https://github.com/andreimerlescu/reconcile-tfstate) and [tf-reconcile-reader](https://github.com/andreimerlescu/tf-reconcile-reader) utilites were created that could be directly injected into **GitHub Actions Workflows** to address any future `tfstate` related issue pertaining to resources either being renamed or missing, and how to go about recovering from that type of an error in the event that you renamed the regions. This product is open source, free to use, and designed to compliment your knowledge of Terraform and Infrastructure as Code; not just from the perspective of writing it for a web application then managing it, but writing modules for Terraform itself, or perform infrastructure surgeries themselves. That's what I do. That's why I was called. 

#### IP Is Here

I built [ip.ishere.dev](https://github.com/andreimerlescu/ip.ishere.dev) so that anybody who needed to set up their own "give me my IP address" without doxxing the fact that I just made that request can launch one of these applications and run a server that you can point to effectly anything and just run it as is. The demo was using `ip.ishere.dev` but its not required to use that, since the code allows you to run this very same technology behind a closed or private network.

#### IGO (Install Go)

Coming from Ruby and having `rbenv` and `rvm` to manage multiple copies of Ruby on any given system, as was needed for my Cisco days and my PhoenixVault project, I ended up writing a Go installer that replicates this idea by allowing me to `-i` [install] versions of Go on my system in an application managed directory `~/go` on a per-user basis without requiring sudo permissions. Useful if you need Go but don't have sudo. You can do a lot without sudo in Go. `igo` is my attempt to write a Go installer in Go since Go was written in Go. Why not install Go on a system that doesn't have Go with an installer compiled from Go in the first place to just close the loop on the paradox of that truth.

#### Developer Utilities + Go Learning Apps 

These packages are not any less than the products per-se, but they are more utilities than products. 

I use [bump](https://github.com/andreimerlescu/bump) in pipelines to increment version files in projects. 

I use [goini](https://github.com/andreimerlescu/goini) and [goenv](https://github.com/andreimerlescu/goenv) to manage configuration on disk and in the environment in order to extend the [figtree](https://github.com/andreimerlescu/figtree). 

I use [trimm](https://github.com/andreimerlescu/trimm) to split and merge large files in easy formats for transfer or archival purposes. 

When I need to log secrets to the console and want to see `***` natively, I use the [verbose](https://github.com/andreimerlescu/verbose) package I wrote. On disk, the secrets are written, but in console or STDOUT, they are censored for operational security. 

After managing Perforce's Helix Core P4 software for 4 years for multiple game studios, I became inspired by the [counter](https://github.com/andreimerlescu/counter) and decided to open source the idea of the [counter](https://github.com/andreimerlescu/counter) itself. Very useful for keeping track of things that are counted. Essential to running Perforce and managing it. 

When generating secure passwords, [entgenpass](https://github.com/andreimerlescu/entpassgen) offers entropy enforced password security whereas [genwordpass](https://github.com/ProjectApario/genwordpass) is a 5-language dictionary based password generator for strong memorable passwords. 

[checkfs](https://github.com/andreimerlescu/checkfs) expanded the [figtree](https://github.com/andreimerlescu/figtree) such that when files or directories are accessed, they are linquistically invoked with `checkfs.File` and `checkfs.Directory` which makes working with system data resources native with `file.Options` and `directory.Options` for customizing behavior across systems for given paths. This is essential to working with Go binaries that need to run on Windows Linux and MacOS systems. 

When testing cloud servers or application performance tuning, the [disk-speed](https://github.com/andreimerlescu/disk-speed) script lets you move around the file system structure, volume for volume, and check the disk-speeds quickly over the command line. 

When managing multiple servers in one pairing, whether for P4, or Project Apario or anything else that uses Terraform, [extra-ssh-bash](https://github.com/andreimerlescu/extra-ssh-bash) provides you the ability to run commands like `p4 info` or `systemctl status apache` on all systems at the same time in given inventory configuration options during runtime. 

For some applications, you need their configuration files to run in encrypted memory spaces, and thats where [encrypted-luks-workspace](https://github.com/andreimerlescu/encrypted-luks-workspace) comes into play. This easily creates `~/.elwork` for you that mounts / unmounts encrypted _LUKS_ volumes (encrypted virtual disks). This allows you to run a sensitive application, like the mission critical sensitive applications, and point all configurations to mounted projects like `apario` to symlink paths called `~/el-apario -> ~/.elwork/drives/apario/mounted`. When the system reboots, the `~/el-apario` will be missing until its mounted. Mounting fails if the directory already exists. 

When running in sensitive environments, having this utility to move application runtime configurations between hosts is easier when you combine it with [trimm](https://github.com/andreimerlescu/trimm). 

When days go between commits, you can use [aigcm](https://github.com/andreimerlescu/aigcm) to let the `git diff` and `git diff --staged` compose your commit message intelligently using AI. The defaults point to Ollama `qwen3:8b`. 

When interacting with AWS EC2 or EBS products, the [configure-ebs](https://github.com/andreimerlescu/configure-ebs) allows you to mount newly attached EBS drives to EC2 instances using the `vol-###` provided to the engineer by either the tooling interface output in order to configure and mount with options including fstrim and filesystem type. 

The heart of the concurrency in a lot of Go systems designs use the [sema](https://github.com/andreimerlescu/sema) package to provide semaphores for rate control. 

# Andrei's Curriculum Vitae

- **1988** - Born in Romania [as Andrei]
- **1990** - Adopted to America [as Michael]
- **2005** - Won 8th Place in SkillsUSA Web Design Nationals Competition
- **2006** - Started consulting for Walter at Sawmillmag.com
- **2007** - Attended Wentworth Institute of Technology in Computer Engineering Technology, joined IEEE Student Branch as elected President.
- **2008** - Summer Internship at Harvard University William James Hall 
- **2009** - Offered Employment by Cisco Systems for Rack/Stack Network Engineering w/ Web Design on the Side (Perl + Tcl)
- **2010** - Stepped down as Wentworth's IEEE Student Branch President after building engineering lab in attic of Dobb's Hall with Secure 24/7 badge access.
- **2011** - Graduated from Wentworth and Hired Full Time by Cisco's Advanced Services and relocated to RTP NC
- **2013** - Provided 9 months Consulting Services With Kadro Solutions for Magento Infrastructure Installation Automation
- **2014** - Joined Cisco Cloud Group from Advanced Services (transition from Web Design to DevOps + Engineering)
- **2015** - Created Trakify and Published in Barron's Magazine Oct 10th by Mike Hogan
- **2016** - Got Married to Dr Heather Ajzenman.
- **2017** - Joined Oracle Cloud as Senior Release Manager
- **2018** - Co-Founded _Play and Prosper Therapy PLLC_ with Heather to serve disabled children with equine assisted hippotherapy 
- **2019** - Legally changed name from _Michael_ to **Andrei**.
- **2020** - Laid off at Oracle due to pandemic reorganization, then I built PhoenixVault in Ruby on Rails + MongoDB to make JFK files searchable.
- **2020** - Started offering Consulting services for technical consultations in business development efforts in one on one capacities with business owners and executive leadership directly
- **2021** - Bit Fry Game Studios for replacing $17K/mo infra to $2K/mo infra. Studio closed due to licensing & funding complications in game title.
- **2021** - Joined WB Games as Application Engineer seeking P4 server automation services on Linux and Windows hosts under 18 month contract.
- **2022** - Joined SurgePays as a Consultant providing part time services. Renewed WB Games contract by joining ITSS team. Continued providing consulting services.
- **2023** - Rewrote PhoenixVault from Rails+Mongo to a custom reader|writer|search trio of Go applications made open source
- **2024** - Completed second contract with WB Games. Continued providing consulting services. Built "topobuilder" for SurgePays - a utility that bundles then deploys a cPanel website (functional landing page) and deploys it to a multi-region load balanced cluster that effectively takes a single YAML config file and deploys a clustered website with it. All that's required is a base layer in cPanel first.
- **2025** - Worked with Beamable for 6 months to fix their corrupted Terraform Infrastructure as Code. For SurgePays I developed Disaster Recovery Solutions and assisted in expanding their cloud into High Availability.
- **2026** - Available for collaboration! Still providing consulting services! _Inquire now!_



