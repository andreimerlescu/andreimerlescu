# Open Source

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

Much of my open source contributions have been under time management constraints because providing 
ends meet is required in any day in age, and therefore, the [summarize](https://github.com/andreimerlescu/summarize) 
doesn't do what you might _think_ it does. It doesn't _summarize_ something _per-se_ in any capacity, 
but rather what it does is _summarize a directory_ by capturing the status of every _file_ in the 
directory, recrusively, looking for plain text files (and omitting secrets), to render a summary file 
of a given directory.

This allows me to work in a manner that uses a Claude Pro subscription with a context window that lasts about
50 minutes while using `summarize`. Essentially, as I am building out functionality, I am looking at what 
each summary file contains and buildinf off from that. From chats with Claude, I can build well tested software 
that accomplishes the **systems engineering architecture** that I natively have been so gifted at my entire life.

In a given day, I can accomplish 50 minutes of sound dual purpose engineering with Claude to scaffold out an 
open source idea and then get it tested in the cool-off period in between. The [summarize](https://github.com/andreimerlescu/summarize)
package enables that. 

    go install github.com/andreimerlescu/summarize@latest

## `figtree`

This package is called [figtree](https://github.com/andreimerlescu/figtree) and its an attempt at me understanding 
the programming of life itself by simulating the life of a _fig tree_ in the lifecycle of a **configuration utility** 
in the language of Go, by offering callbacks, validations and type safety. I have other features planned for it, but 
it serves most purposes that I have needed in basic CLI utilities that are designed with DevOps in mind. 

    go get -u github.com/andreimerlescu/figtree/v2

## `counter`

While I was at WB Games I worked with **Perforce / P4**'s _Helix Core_ as a System Administrator and one functionality 
that I found very useful in how the runtime of `p4` itself relied on the `p4 counter` sub-program made me realize that 
in other applications, having a universal [counter](https://github.com/andreimerlescu/counter) could offer that type of
design functionality to other open source project designs that need a basic counter.

    go install github.com/andreimerlescu/counter@latest

## `sema`

When I was converting the **Ruby on Rails** _closed source_ project called **PhoenixVault** to Go, I needed to ingest 
over 700,000 pages of PDFs into an OCR engine that indexed the results. My `ruby` implementation took roughly 90-180 
seconds per page. Thats 17,500 hours of processing time divided among 12 machines, giving us ~1,450 hours per machine 
to compile the entire collection of what **PhoenixVault** had on it. It's about 2 months exactly of processing time. 

I observed that in the process of ingesting those pages, the pipeline that I built had a handful of steps between it 
that was respondible for various tasks. Without [sema](https://github.com/andreimerlescu/sema), I wouldn't be able to 
offset the load of the Go binary itself and the third party dependencies - some of which were multi-threaded and others 
were singlethreaded. Therefore, in order to keep the system resources in check, the `.Acquire()` and `.Release()` 
functionality was deeply depended on. 

In 2026, I decided to expand the `interface {}` of the `Semaphore` type in the [sema](https://github.com/andreimerlescu/sema) 
by offering observability into the semaphore and `Try*` and `TryWith*` methods that allow for non-blocking implementations 
that use the semaphores purpose. 

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

## Project Apario

In 2020 I was asked by a bunch of online folks to build a search engine for OSINT records. I released PhoenixVault, but it 
changed from a _Ruby on Rails_ closed-source _SaaS_ product that had voluntary subscriptions to finance the $7K/mo hosting 
bills for roughly 500 concurrent monthly users, funded by over 200 subscribers for **four (4) years** until I rewrote the 
_closed source_ offering into an **open source** offering written in Go. In my professional career, Project Apario offered
me a lot of growing opportunities to master DevOps in scale and load outside of my full time job. This allowed me to learn
new tech and try new concepts. 

### `reader`

The [reader](https://github.com/ProjectApario/reader) is the presentation layer of the **trio** that is _Project Apario_.

### `writer`

The [writer](https://github.com/ProjectApario/writer) is what builds a **database directory** of _Apario Assets_ that can be 
used in the presentation [reader](https://github.com/ProjectApario/reader) layer. 

### `search`

The [search](https://github.com/ProjectApario/search) acts similar to the [reader](https://github.com/ProjectApario/reader)
in that it uses the **database directory** of _Apario Assets_ to generate cached indexes of the raw text extracted and read
from OCR. Text is processed through [textee](https://github.com/andreimerlescu/textee) in order to chunk a string into small
substrings, then [gematria](https://github.com/andreimerlescu/gematria) is used in order to calculate the English, Jewish and
Simple _gematria_ value for each _textee_ substring. When searches are performed, the `textee` substring is used in matching 
potential `gematria` matches. In life we have coincidences and _gematria_ is one of the best. Search opens that up. It's 
interface is REST API and `wss://` | `ws://`.

### `merkel`

The [merkel](https://github.com/ProjectApario/merkel) project is the last in the chain for _Project Apario_ until the DAO
NFTs sell on XRP. I'm not necessarily trying to make _money_ on the project _per-se_, but the XRP collected simply goes 
into the `rAparioji3FxAtD7UufS8Hh9XmFn7h6AX` wallet address and would be used in conjunction with the `$APARIO` token that 
was minted. I've _built it_, it's ultimately a matter of _whether or not they will come_. Merkel attempts to introduce 
the **merkle** concept for verifying NFT participation chains. If _Project Apario_ continues by my hands, and its future
involves crypto, then the **merkel** package would end up being used because its important to have authenticity and 
reliability in what you're asserting. 

## PhoenixVault

The movement in 2020 was originally for the JFK files, and I found the technical challenge of NARA's release of the files
unsearchable to be a fun challenge to conquer. That I did. I started with a proof of concept and then I delivered on the 
real deal. The _closed source_ cost $7K/mo to serve 500K JFK files and the **open source** version costs $63/mo to serve 500K JFK files - both for roughly 500 concurrent users. 

### Roadmap for _Project Apario_

Since the **PhoenixVault** is the _product_ of _Project Apario_, I plan on integrating the skills acquired from my VueJS 
course, coupled with AI assisted development, coupled with [room](https://github.com/andreimerlescu/room) and the new 
[lemmings](https://github.com/andreimerlescu/lemmings), I should be able to build a brand new service for Project Apario
completely from start to finish. But doing it, without any support, or sponsorship, is difficult when I can only contribute
a few hours per month in any meaningful capacity. When I am able to increase that efficiency by increasing collaboration, 
the project will resume. But, until that happens, I'll be waiting on the DAO NFTs to sell, and when they sell, I'll know 
that I have a team of serious folks who want to help build out the future roadmap of the project. The door is open. 

