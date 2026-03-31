# Andrei Merlescu (formerly Michael Trimm)

**Staff Platform Engineer · Infrastructure Architect · AI Integration Specialist**  
17 Year Professional · Go · Terraform · AWS · Release Management · CI/CD · Engineering Excellency  · Own Without Ego

I design and operate infrastructure that saves money at scale and ships software faster.
At Bit Fry Game Studios I reduced build pipeline costs from $17K/mo to $2K/mo — a $291K
saving over 72 months. At Oracle I took a release pipeline from a 1-in-5 failure rate to
99.9% success. At WB Games I drove concurrent multi-platform game builds from 8-hour cycles
down to 30 minutes. I'm available for one full-time engagement and one consulting retainer.

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

Past titles: Senior DevOps Engineer · Senior Release Engineer · CEO · Co-Founder · Network Engineer · Application Engineer · Software Engineer · Senior Software Engineer
Target industries: Game studios · Cloud infrastructure · Fintech · Defense-adjacent · Corporate AI Adoption

---

## Consulting availability

I have capacity for one additional client. I take on engagements where infrastructure is
costing too much, breaking too often, or not deploying fast enough.

**Retainer:** $3,600/mo · covers up to ~21 hours  
**Additional hours:** $170/hr · billed in 20-minute units  
**Minimum engagement:** 3 months  
**What I work on:** Terraform state recovery · multi-region migrations · CI/CD rebuilds ·
Perforce at scale · AI integration (MCP, RAG, local LLM) · cloud cost reduction · web application development with AI · Go application development with AI · AI agent virtual company development

---

## Selected impact

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
| [Cisco](https://cisco.com) | 2009–2016 | Senior Software Engineer | Perl · Ruby · LAMP · JS |
| [Oracle](https://oracle.com) | 2017–2020 | Senior Release Manager | Ruby · Go · HCL |
| [Bit Fry](https://www.linkedin.com/company/bitfry) | 2021 | Senior DevOps Architect | HCL · Bash · Ruby |
| [WB Games](https://wbgames.com) | 2021–2024 | Senior Release Engineer | Go · C++ · C# · Bash |
| [SurgePays](https://surgepays.com) | 2022–2026 | Consulting Architect | Go · C++ · Bash |
| [Beamable](https://beamable.com) | 2025 | Senior DevOps Engineer | HCL · Go · Bash · C# |

**Education:** B.S. Computer Engineering Technology · Wentworth Institute of Technology, 2011  
IEEE Student Branch President · Harvard University summer research program, 2008

---

## Open source

### Infrastructure tooling
- [reconcile-tfstate](https://github.com/andreimerlescu/reconcile-tfstate) — recover corrupted Terraform state files; battle-tested at Beamable
- [configure-ebs](https://github.com/andreimerlescu/configure-ebs) — mount/unmount AWS EBS volumes on EC2 with filesystem and fstrim options
- [encrypted-luks-workspace](https://github.com/andreimerlescu/encrypted-luks-workspace) — LUKS encrypted volumes for sensitive application configs
- [extra-ssh-bash](https://github.com/andreimerlescu/extra-ssh-bash) — run the same command across N Linux hosts simultaneously
- [disk-speed](https://github.com/andreimerlescu/disk-speed) — per-volume disk speed benchmarking from the command line

### AI + developer workflow
- [summarize](https://github.com/andreimerlescu/summarize) — aggregate a codebase into a single markdown file; chat with it via local Ollama LLM
- [aigcm](https://github.com/andreimerlescu/aigcm) — AI-generated git commit messages from `git diff`, powered by Ollama `qwen3:8b`

### Go packages + CLI utilities
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



