### Hey There 👋🏻
## I'm Andrei (formerly Michael Trimm)

**Staff Platform Engineer · Infrastructure Architect · AI Integration Specialist**  
17 Year Professional · Go · Terraform · AWS · Release Management · CI/CD
Entrepreneurship · Engineering Excellency · Own Without Ego · Women In Tech

I design and operate infrastructure that saves money at scale and ships software faster.
At Bit Fry Game Studios I reduced build pipeline costs from $17K/mo to $2K/mo — a $291K
saving over 72 months. At Oracle I took a release pipeline from a 1-in-5 failure rate to
99.9% success. At WB Games I drove concurrent hybrid in-studio plus multi-region perforce 
version control systems through infrastructure as code. I assisted in operating and teaching
during RailsGirlsRDU in 2014 to enable women in tech through building a rails twitter clone.
I'm available for one full-time engagement and one consulting retainer.

---

## Core skills

```
Go · Ruby · Bash · PHP · Terraform (HCL) · AWS · OCI · Swift · C++ · Web Design · Backend Systems
Docker · VMware vSphere/vCenter · Perforce Helix Core · GitHub Actions · GitLab Administration
CI/CD pipelines · Multi-region HA · Disaster recovery · IaC · MCP · RAG · Ollama
PostgreSQL · MongoDB · Linux · macOS · Windows · TeamCity · Unreal Engine Build Pipelines
```

---

## Full-time availability

I'm open to a Staff or Senior-level Platform / DevOps / Release Engineering role.
I bring my own workstation (M3 Mac Studio, 256 GB RAM, local 80B-parameter LLM inference
at 80+ tokens/sec) and require no onboarding equipment. I am based in the United States
and work across time zones.

Past titles: Senior DevOps|Release|Application Engineer · Network|Software|Application Engineer · Co-Founder · CEO
Target industries: Game studios · Cloud infrastructure · Fintech · Defense-adjacent · Corporate AI Adoption  · Medical-adjacent

---

## Consulting availability

I have capacity for one additional client. I take on engagements where infrastructure is
costing too much, breaking too often, or not deploying fast enough.

**Retainer:** $2,000/mo · covers up to 10 hours per month
**Additional hours:** $200/hr · billed $40 per 12-minute units
**Minimum engagement:** 3 months (longest was 15 years)
**What I work on:** Terraform state recovery · multi-region migrations · CI/CD rebuilds ·
Perforce at scale · AI integration (MCP, RAG, local LLM) · cloud cost reduction · web application development with AI · Go application development with AI · AI agent virtual company development

---

## Selected impact

In my career I've made significant contributions that have advanced the organizations that I've worked with in an internally facing capacity for the primary focus of my professional career. 

| Engagement | Problem | Outcome |
|---|---|---|
| Bit Fry Game Studios (2021) | $17K/mo AWS build pipeline, no redundancy, 8-hr cycles | Rebuilt on-prem w/ Mac Pros + vSphere; $2K/mo OPEX, 30-min builds, $291K saved over 6 yrs |
| Oracle Cloud (2017–2020) | Release pipeline failing 1 in 5 deploys to production | Redesigned release automation in Ruby + Go; 99.9% success rate |
| Beamable (2025) | Corrupted Terraform state after failed multi-region migration | Recovered state, built open-source reconcile-tfstate tooling, hardened IaC pipeline |
| SurgePays (2022–2026) | Single-region cloud, no disaster recovery | Designed HA architecture, built multi-region deployment tooling in Go |
| WB Games (2021–2024) | $7M+/yr Perforce OPEX, no cloud migration path | Built Terraform + Bash IaC automation for OCI migration; delivered P4 transfer rewrite in Go |

---

## Career

| Company | Years | Role | Stack |
|---|---|---|---|
| [MuggleNet](https://mugglenet.com) | 2004-2006 | Volunteer Developer | PHP · MySQL · JS |
| [S &amp; W Publishing - Sawmillmag](https://sawmillmag.com) | 2005-2022 | Webmaster | PHP · MySQL · JS · Ruby |
| [IEEE Student Branch President](https://ieee.wit.edu) | 2007-2010 | Elected President of Wentworth Student Branch |
[ [Wentworth Admissions](https://wit.edu) | Summary 2008 | Tour Guide | Gave tours of the campus to prospective students |
| [Harvard University](https://harvard.edu) | Summer 2009 | IT Support Internship | IT Services in William James Hall <br>+ Consulting with Dr Jill Hooley  |
| [Cisco](https://cisco.com) | 2009–2016 | Senior Software Engineer | Perl · Ruby · LAMP · JS |
| [Trakify](https://trakify.com) | 2015 | CEO | Ruby on Rails · MongoDB · Yahoo! Finance API |
| [Oracle](https://oracle.com) | 2017–2020 | Senior Release Manager | Ruby · Go · HCL |
| [Project Apario](https://projectapario.com) | 2020-2026 | Owner &amp; Founder | Ruby · Go · Bash · HCL · JS · VueJS | 
| [Bit Fry](https://www.linkedin.com/company/bitfry) | 2021 | Senior DevOps Architect | HCL · Bash · Ruby |
| [WB Games](https://wbgames.com) | 2021–2024 | Senior Release Engineer | Go · C++ · C# · Bash |
| [SurgePays](https://surgepays.com) | 2022–2026 | Consulting Architect | Go · C++ · Bash |
| [Beamable](https://beamable.com) | 2025 | Senior DevOps Engineer | HCL · Go · Bash · C# |

On October 10th 2015, Mike Hogan of Barron's Magazine published a piece featuring my company Trakify after we spoke on the telephone for several hours about the product and the vision itself. The piece was published the following month. While leadership has never been my go-to, it has never been something that I have stayed away from. Leading in engineering efforts comes naturally to me, as Trakify had 4 individuals that I was collaborating with that helped drive the product to market. PhoenixVault had a large online community and served over 800 concurrent daily users per month. PhoenixVault had 3 other individuals. I'm a leader, a builder, a team player, and a guy who genuinely loves building cool things with incredible technology.

**Education:** B.S. Computer Engineering Technology · Wentworth Institute of Technology, 2011  
IEEE Student Branch President · Harvard University summer research program, 2008

---

## Open source

I enjoy writing code so much that I'll come up with DevOps related challenges that are commonly scripted out on a case by case basis and made formal packages in order to learn Go and other related technologies like Bash in order to manipulate headless systems in an automated data driven manner.

### Infrastructure tooling

My professional career began in the data centers of Cisco's Boxborough Campus - infrastructure is built into my career since the first day I started professionally.

- [reconcile-tfstate](https://github.com/andreimerlescu/reconcile-tfstate) — recover corrupted Terraform state files; battle-tested at Beamable
- [configure-ebs](https://github.com/andreimerlescu/configure-ebs) — mount/unmount AWS EBS volumes on EC2 with filesystem and fstrim options
- [encrypted-luks-workspace](https://github.com/andreimerlescu/encrypted-luks-workspace) — LUKS encrypted volumes for sensitive application configs
- [extra-ssh-bash](https://github.com/andreimerlescu/extra-ssh-bash) — run the same command across N Linux hosts simultaneously
- [disk-speed](https://github.com/andreimerlescu/disk-speed) — per-volume disk speed benchmarking from the command line

### AI + developer workflow

I got the workstation I have so I can incorporate more AI + AI Agents into my workflow utilizing the hardware resources at my disposal for local and private LLM access through LMStudio and Ollama.

- [summarize](https://github.com/andreimerlescu/summarize) — aggregate a codebase into a single markdown file; chat with it via local Ollama LLM
- [aigcm](https://github.com/andreimerlescu/aigcm) — AI-generated git commit messages from `git diff`, powered by Ollama `qwen3:8b`

### Go packages + CLI utilities

I learned Go and built a handful of commercial and enterprise scale applications in Go (3 completed each over 10K lines). In addition to that, while learning Go, I built a handful of packages to help me master the patterns of Go. I use the packages I write. I am sure most engineers are like that. Making them open source, allows you to run them as well and work off from them as well. 

- [figtree](https://github.com/andreimerlescu/figtree) — unified CLI flag / env / config file management
- [sema](https://github.com/andreimerlescu/sema) — semaphore primitives for Go concurrency control
- [checkfs](https://github.com/andreimerlescu/checkfs) — cross-platform filesystem path abstractions (Linux, macOS, Windows)
- [bump](https://github.com/andreimerlescu/bump) — VERSION file management for CI pipelines
- [verbose](https://github.com/andreimerlescu/verbose) — logging package that censors secrets in stdout while writing them to disk
- [igo](https://github.com/andreimerlescu/igo) — install and manage multiple Go versions without sudo

### Project Apario ([@ProjectApario](https://github.com/ProjectApario))
A decentralized OSINT document platform built on a custom reader / writer / search trio,
all in Go. Indexed with full-text search via
[textee](https://github.com/andreimerlescu/textee) and integrity-verified with a
[Merkle tree](https://github.com/ProjectApario/merkel) package. GPL-3 / AGPL-3.
Originally built to make the JFK files searchable; deployable for any document collection
via a single CSV and a single binary.

---

*Available for conversations. Reach out via GitHub or
[LinkedIn](https://linkedin.com/in/andreimerlescu).*



