## The Go Blog
> able8

### Fuzzing is Beta Ready

Fuzzing is a type of automated testing which continuously manipulates inputs to a program to find issues such as panics or bugs. 

Since fuzzing can reach these edge cases, fuzz testing is particularly valuable for finding security exploits and vulnerabilities.


Please be aware that fuzzing can consume a lot of memory and may impact your machine’s performance while it runs. 

If you experience any problems or have an idea for a feature request, please file an issue.

### Go Developer Survey 2020 Results

Building API/RPC services (74%!)(MISSING) and CLIs (65%!)(MISSING) remain the most common uses of Go. We don't see any significant changes from last year

More respondents use Go for automation/scripts, agents and daemons, and data processing for work than web services returning HTML. 

A closer look at Go for data processing showed that Kafka is the only broadly adopted engine, but a majority of respondents said they use Go with a custom data processing engine.

We also asked about larger areas in which respondents work with Go. The most common area by far was web development (68%!)(MISSING), but other common areas included databases (46%!)(MISSING), DevOps (42%!)(MISSING) network programming (41%!)(MISSING) and systems programming (40%!)(MISSING).

Go was designed with modern distributed computing in mind, and we want to continue to improve the developer experience of building cloud services with Go.

### New module changes in Go 1.16

We hope you're enjoying Go 1.16! This release has a lot of new features, especially for modules. 

The go install command can now install an executable at a specific version by specifying an @version suffix.

### Go 1.16 is released

Go 1.16 also adds macOS ARM64 support (also known as Apple silicon). Since Apple’s announcement of their new arm64 architecture, we have been working closely with them to ensure Go is fully supported; see our blog post “Go on ARM and Beyond” for more.

Finally, there are many other improvements and bug fixes, including builds that are up to 25%!f(MISSING)aster and use as much as 15%!l(MISSING)ess memory. For the complete list of changes and more information about the improvements above, see the Go 1.16 release notes.

We hope you enjoy the new release!


### Gopls on by default in the VS Code Go extension

We're happy to announce that the VS Code Go extension now enables the gopls language server by default, to deliver more robust IDE features and better support for Go modules.

(gopls provides IDE features, such as as intelligent autocompletion, signature help, refactoring, and workspace symbol search.)

Gopls is the best way of working with Go code, especially with Go modules. With the upcoming arrival of Go 1.16, in which modules are enabled by default, VS Code Go users will have the best possible experience out-of-the-box.

If you use VS Code, you don’t need to do anything. When you get the next VS Code Go update, gopls will be enabled automatically.

To our existing users, thank you for bearing with us as we rewrote our caching layer for the third time. To our new users, we look forward to hearing your experience reports and feedback.


### A Proposal for Adding Generics to Go

Why generics?
Generics can give us powerful building blocks that let us share code and build programs more easily. Generic programming means writing functions and data structures where some types are left to be specified later.

We have reached the point where we think that the design draft is good enough, and simple enough, to propose adding it to Go.

### Go on ARM and Beyond

The industry is abuzz about non-x86 processors recently, so we thought it would be worth a brief post about Go’s support for them.

 The initial open source release of Go included support for two operating systems (Linux and Mac OS X) and three architectures (64-bit x86, 32-bit x86, and 32-bit ARM).

•  Go 1.5 (August 2015) added support for Linux on 64-bit ARM and 64-bit PowerPC, as well as iOS on 32-bit and 64-bit ARM.

•  Go 1.9 (August 2017) added official binary downloads for Linux on 64-bit ARM.

Go supports cross-compiling for all these systems out of the box with minimal effort. For example, to build an app for 32-bit x86-based Windows from a 64-bit Linux system:
GOARCH=386 GOOS=windows go build myapp  # writes myapp.exe

In the past year, several major vendors have made announcements of new ARM64 hardware for servers, laptops and developer machines. Go was well-positioned for this. For years now, Go has been powering Docker, Kubernetes, and the rest of the Go ecosystem on ARM64 Linux servers, as well as mobile apps on ARM64 Android and iOS devices.


Earlier this week, we released the first Go 1.16 beta, which includes native support for Macs using the M1 chip. 

### Redirecting godoc.org requests to pkg.go.dev

The next step in this migration is to redirect all requests from godoc.org to the corresponding page on pkg.go.dev.
This will happen in early 2021, once the work tracked at the pkgsite/godoc.org-redirect milestone is complete.

During this migration, updates will be posted to Go issue 43178.

We encourage everyone to begin using pkg.go.dev today. You can do so by visiting godoc.org?redirect=on, or clicking “Always use pkg.go.dev” in the top right corner of any godoc.org page.

Will godoc.org URLs continue to work?
Yes! We will redirect all requests arriving at godoc.org to the equivalent page on pkg.go.dev, so all your bookmarks and links will continue to take you to the documentation you need.

We will mark it archived to make clear that we will no longer accept contributions. However, you will be able to continue forking the repository.

Will api.godoc.org continue to work?
This transition will have no impact on api.godoc.org. Until an API is available for pkg.go.dev, api.godoc.org will continue to serve traffic. See Go issue 36785 for updates on an API for pkg.go.dev.

As always, feel free to file an issue on the Go issue tracker for any feedback.

### Eleven Years of Go

In February, the Go 1.14 release delivered the first officially “production-ready” implementation of Go modules, along with many performance improvements,

In August, the Go 1.15 release delivered mainly optimizations and bug fixes rather than new features. The most significant was the start of a rewrite of the linker, making it run 20%!f(MISSING)aster and use 30%!l(MISSING)ess memory on average for large builds.

Last month, we ran our annual Go user survey. We will post results on the blog once we’ve analyzed them.

We hope you are all staying safe and wish you all the best.

### Pkg.go.dev has a new look!

Since launching pkg.go.dev, we’ve received a lot of great feedback on design and usability. In particular, it was clear that the way information was organized confused users when navigating the site.

### Go 1.15 is released

For the complete list of changes and more information about the improvements above, see the Go 1.15 release notes.

### Pkg.go.dev is open source!

We’re excited to announce that the codebase for pkg.go.dev is now open source.
The repository lives at go.googlesource.com/pkgsite and is mirrored to github.com/golang/pkgsite. We will continue using the Go issue tracker to track feedback related to pkg.go.dev.

Thanks for being patient with us in the process of open sourcing pkg.go.dev. We’re looking forward to receiving your contributions and working with you on the future of the project.

### The VS Code Go extension joins the Go project

Through this collaborative work between the VS Code and Go teams, we realized that the Go team is uniquely positioned to evolve the Go development experience alongside the Go language.

As a result, we’re happy to announce the next phase in the Go team’s partnership with the VS Code team: The VS Code extension for Go is officially joining the Go project. With this come two critical changes:

1.  The publisher of the plugin is shifting from "Microsoft" to "Go Team at Google".
2.  The project’s repository is moving to join the rest of the Go project at https://github.com/golang/vscode-go.

We cannot overstate our gratitude to those who have helped build and maintain this beloved extension. We know that innovative ideas and features come from you, our users. The Go team’s primary aim as owners of the extension is to reduce the burden of maintenance work on the Go community. We’ll make sure the builds stay green, the issues get triaged, and the docs get updated.

We’re excited about this new step forward, and we hope you are too. By maintaining a major Go editor extension, as well as the Go tooling and language, the Go team will be able to provide all Go users, regardless of their editor, a more cohesive and refined development experience.

As always, our goal remains the same: Every user should have an excellent experience writing Go code.

### Go Developer Survey 2019 Results

•  Respondents are using Go to solve similar problems, particularly building API/RPC services and CLIs, regardless of the size of organization they work at.

•  VS Code and GoLand have continued to see increased use; they’re now preferred by 3 out of 4 respondents.

VS Code’s growth slowed, but it remains the most popular editor among respondents at 41%!.(MISSING)

Building API/RPC services (71%!)(MISSING) and CLIs (62%!)(MISSING) remain the most common uses of Go. 

A related question asked about the larger areas in which respondents work with Go. The most common area by far was web development (66%!)(MISSING), but other common areas included databases (45%!)(MISSING), network programming (42%!)(MISSING), systems programming (38%!)(MISSING), and DevOps tasks (37%!)(MISSING).

Pain points
The top reasons respondents say they are unable to use Go more remain working on a project in another language (56%!)(MISSING), working on a team that prefers to use another language (37%!)(MISSING), and the lack of a critical feature in Go itself (25%!)(MISSING).

Roughly two thirds of respondents used Stack Overflow to answer their Go-related questions (64%!)(MISSING). The other top sources of answers were godoc.org (47%!)(MISSING), directly reading source code (42%!)(MISSING), and golang.org (33%!)(MISSING).


### Go, the Go Community, and the Pandemic

Go always comes second to more basic concerns like personal and family health and safety. Around the world, the past couple months have been terrible, and we are still at the start of this awful pandemic. 

In this post we want to share a few important notes about how the pandemic is affecting the Go community, a few things we’re doing to help, what you can do to help, and our plans for Go itself.

We want to do everything we can to help support impacted Go conferences. We also want to support efforts to explore new ways for gophers to connect in the time of social distancing.

If you are organizing a Go conference and have been impacted, or if you are considering holding a virtual alternative, please reach out to Carmen Andoh at candoh@google.com.

Here on the Go team at Google, we recognize that the world around us is changing rapidly and that plans beyond the next couple weeks are not much more than hopeful guesses. 

The new Go package and module site pkg.go.dev keeps getting better.

Our Gopher values are what ground us, now more than ever. We are working extra hard to be friendly, welcoming, patient, thoughtful, respectful, and charitable. We hope everyone in the Go community will try to do the same.

We’ll continue to use this blog to let you know about important news for the Go ecosystem. 

### Go 1.14 is released

Today the Go team is very happy to announce the release of Go 1.14. You can get it from the download page.

### Next steps for pkg.go.dev

We’ve heard from users that running the godoc.org server is more complex than it should be, because it is designed for serving at public internet scale instead of just within a company. We believe the current pkg.go.dev server would have the same problem.


### Go.dev: a new hub for Go developers

Today we are launching go.dev, a new hub for Go developers, to help answer those questions. There you will find a wealth of learning resources to get started with the language, featured use cases, and case studies of companies using Go.

If you have any ideas, suggestions or issues, please let us know via the “Share Feedback” and “Report an Issue” links at the bottom of every page.

### Go Turns 10

Go’s original target was networked system infrastructure, what we now call cloud software. Every major cloud provider today uses core cloud infrastructure written in Go, such as Docker, Etcd, Istio, Kubernetes, Prometheus, and Terraform; the majority of the Cloud Native Computing Foundation’s projects are written in Go.

Countless companies are using Go to move their own work to the cloud as well, from startups building from scratch to enterprises modernizing their software stack.

In fact, people often tell us that learning Go helped them get their first jobs in the tech industry. In the end, what we’re most proud of about Go is not a well-designed feature or a clever bit of code but the positive impact Go has had in so many people’s lives.

### Working with Errors in Go 1.13

The errors.Is function compares an error to a value.
// Similar to:
//   if err == ErrNotFound { … }
if errors.Is(err, ErrNotFound) {
    // something wasn't found
}

In the simplest case, the errors.Is function behaves like a comparison to a sentinel error, and the errors.As function behaves like a type assertion.

### Experiment, Simplify, Ship

For example, append was originally defined to read only from slices. When appending to a byte slice, you could append the bytes from another byte slice, but not the bytes from a string. We redefined append to allow appending from a string, without adding anything new to the language.


Making error a simple interface and allowing many different implementations means we have the entire Go language available to define and inspect errors. We like to say that errors are values, the same as any other Go value.

### Why Generics?

Go was released on November 10, 2009. Less than 24 hours later we saw the first comment about generics. (That comment also mentions exceptions, which we added to the language, in the form of panic and recover, in early 2010.)


In three years of Go surveys, lack of generics has always been listed as one of the top three problems to fix in the language.


What some people new to Go find surprising is that there is no way to write a simple Reverse function that works for a slice of any type.
Most other languages do let you write that kind of function.

In a dynamically typed language like Python or JavaScript you can simply write the function, without bothering to specify the element type. This doesn't work in Go because Go is statically typed, and requires you to write down the exact type of the slice and the type of the slice elements.

In Go you can write a single function that works for different slice types by using an interface type, and defining a method on the slice types you want to pass in. That is how the standard library's sort.Sort function works.


For a statically typed language like Go, that better way is generics. What I wrote earlier is that generic programming enables the representation of functions and data structures in a generic form, with types factored out. That's exactly what we want here.

The first and most important thing we want from generics in Go is to be able to write functions like Reverse without caring about the element type of the slice. We want to factor out that element type. Then we can write the function once, write the tests once, put them in a go-gettable package, and call them whenever we want.

### What's new in the Go Cloud Development Kit

•  pubsub for publishing/subscribing of messages to a topic. Supported providers include: Amazon SNS/SQS, Google Pub/Sub, Azure Service Bus, RabbitMQ, and in-memory.

•  secrets, for encryption/decryption. Supported providers include AWS KMS, GCP KMS, Hashicorp Vault, and local symmetric keys.

### Go 2, here we come!

Go 2 will be much more community-driven. After almost 10 years of exposure, we have learned a lot about the language and libraries that we didn’t know in the beginning, and that was only possible through feedback from the Go community.

### Go 1.11 is released

There are many changes and improvements to the toolchain, runtime, and libraries, but two features stand out as being especially exciting: modules and WebAssembly support.

Go 1.11 also adds an experimental port to WebAssembly (js/wasm). This allows programmers to compile Go programs to a binary format compatible with four major web browsers. You can read more about WebAssembly (abbreviated “Wasm”) at webassembly.org and see this wiki page on how to get started with using Wasm with Go. Special thanks to Richard Musiol for contributing the WebAssembly port!

For more detail about the changes in Go 1.11, see the release notes.
Have a wonderful weekend and enjoy the release!

### Portable Cloud Programming with Go Cloud

Today, the Go team at Google is releasing a new open source project, Go Cloud, a library and tools for developing on the open cloud. With this project, we aim to make Go the language of choice for developers building portable cloud applications.


These teams want to deploy robust applications in multi-cloud and hybrid-cloud environments, and migrate their workloads between cloud providers without significant changes to their code.


As an alternative, teams can use Go Cloud, a set of open generic cloud APIs, to write simpler and more portable cloud applications. Go Cloud also sets the foundation for an ecosystem of portable cloud libraries to be built on top of these generic APIs. Go Cloud makes it possible for teams to meet their feature development goals while also preserving the long-term flexibility for multi-cloud and hybrid-cloud architectures. 

Today, Go Cloud is launching with blob storage, MySQL database access, runtime configuration, and an HTTP server configured with request logging, tracing, and health checking. Go Cloud offers support for Google Cloud Platform (GCP) and Amazon Web Services (AWS). We plan to work with cloud industry partners and the Go community to add support for additional cloud providers very soon.


At the core of Go Cloud is a collection of generic APIs for portable cloud programming. Let's look at an example of using blob storage.

### Go 2017 Survey Results

This post summarizes the result of our 2017 user survey along with commentary and insights. 

Another important shift: the #1 use of Go is now writing API/RPC services (65%!,(MISSING) up 5%!o(MISSING)ver 2016), taking over the top spot from writing CLI tools in Go (63%!)(MISSING). Both take full advantage of Go's distinguishing features and are key elements of modern cloud computing. As more companies adopt Go, we expect these two uses of Go to continue to thrive.

### Go 1.10 is released

The most exciting part of this release for many people will probably be that the go tool now does automatic caching of build & test results. Of course, one of the hundreds of smaller changes may be your favorite.

### Hello, 中国!

Hello, 中国!
Andrew Bonventre
22 January 2018
We are thrilled to announce that the content on golang.org is now available in mainland China through the name https://golang.google.cn. The growing Go developer community in China can now directly access official documentation, technical articles, and binaries.


Go adoption within China-based companies has also increased, with Qiniu, Huawei, Alibaba, and countless others using Go heavily in their production stacks.


We’re excited to provide even more resources for Go developers in China to supplement the excellent material already available to them, but this is just the beginning. We’ll be focusing on making Go more accessible to non-English speakers in 2018, so keep watching this space.

### Participate in the 2017 Go User Survey

Why: The Go project leadership depends on your feedback to guide the future of the Go project. Your responses will help to understand what's going well and what's not, as well as help us prioritize improvements for the Go language, libraries and tools.

### Eight years of Go

10 November 2017
Today we celebrate 8 years since Go was released as an open source project. 

Go is a key part of cloud companies like Alibaba, Cloudflare, and Dropbox. Go is a critical part of open infrastructure including Kubernetes, Cloud Foundry, Openshift, NATS, Docker, Istio, Etcd, Consul, Juju and many more.

Go’s impact on open source
Go has become a major force in the world of open source powering some of the most popular projects and enabling innovations across many industries. Find thousands of additional applications and libraries at awesome-go. Here are just a handful of the most popular:

•  Moby (formerly Docker) is a tool for packaging and running applications in lightweight containers.

"To put it simply, if Docker had not been written in Go, it would not have been as successful."

•  Kubernetes is a system for automating deployment, scaling and management of containerized applications. Initially designed by Google and used in the Google cloud, Kubernetes now is a critical part of every major cloud offering.


•  Prometheus is an open source monitoring solution and time series database that powers metrics and alerting designed to be the system you go to during an outage to allow you to quickly diagnose problems.

Many of these authors have said that their projects would not exist without Go. Some like Kubernetes and Docker created entirely new solutions.

The popularity of these applications alone is proof that Go is a ideal language for a broad set of use cases.

The Go team would like to thank everyone who has contributed to the project, whether you participate by contributing changes, reporting bugs, sharing your expertise in design discussions, writing blog posts or books, running events, attending or speaking at events, helping others learn or improve, open sourcing Go packages you wrote, contributing artwork, introducing Go to someone, or being part of the Go community. Without you, Go would not be as complete, useful, or successful as it is today.

### Go 1.9 is released

There are many changes to the language, standard library, runtime, and tooling. This post covers the most significant visible ones. Most of the engineering effort put into this release went to improvements of the runtime and tooling, which makes for a less exciting announcement, but nonetheless a great release.

### Contribution Workshop

The most common type of contribution was an example function to be used in the documentation. The Go User survey identified that our documentation was significantly lacking examples. In the presentation, we asked users to find a package they loved and to add an example. In the Go project, examples are written as code in Go files (with specific naming) and the go doc tool displays them along side the documentation. 

### Contributors Summit

For instance, it would be nice if io.Reader accepted a context so that blocking read operations could be canceled.


### Toward Go 2

Then, and this is the key point, Cloudflare reported their experience in a blog post by John Graham-Cumming titled “How and why the leap second affected Cloudflare DNS.” By sharing concrete details of their experience with Go in production, John and Cloudflare helped us understand that the problem of accurate timing across leap second clock resets was too significant to leave unfixed.

### Introducing the Developer Experience Working Group

For the next three months, this group will work together on delivering:
•  improvements to the Go installation experience
•  better guidelines to help new users
•  guides on tooling and developer environments (editors and IDEs)
•  running user studies to systematically analyze and measure friction points
•  improvements to the Go Tour and Go Playground

We are looking for additional people to help with contributing code, writing documentation, sharing feedback and experiences (user stories), reviewing contributions, and more. If you are interested in any of our current areas of focus, please subscribe to the golang-devexp mailing list.

### HTTP/2 Server Push

In HTTP/1.x, each of these resources must be requested explicitly. This can be a slow process. The browser starts by fetching the HTML, then learns of more resources incrementally as it parses and evaluates the page. Since the server must wait for the browser to make each request, the network is often idle and underutilized.

To improve latency, HTTP/2 introduced server push, which allows the server to push resources to the browser before they are explicitly requested. A server often knows many of the additional resources a page will need and can start pushing those resources as it responds to the initial request. This allows the server to fully utilize an otherwise idle network and improve page load times.

With Go 1.8, the standard library provides out-of-the-box support for HTTP/2 Server Push, giving you more flexibility to optimize your web applications.
Go to our HTTP/2 Server Push demo page to see it in action.


### Go 2016 Survey Results

We asked about people’s expertise and preference among programming languages. Unsurprisingly, Go ranked highest among respondents’ first choices in both expertise (26%!)(MISSING) and preference (62%!)(MISSING). With Go excluded, the top five first choices for language expertise were Python (18%!)(MISSING), Java (17%!)(MISSING), JavaScript (13%!)(MISSING), C (11%!)(MISSING), and PHP (8%!)(MISSING); and the top five first choices for language preference were Python (22%!)(MISSING), JavaScript (10%!)(MISSING), C (9%!)(MISSING), Java (9%!)(MISSING), and Ruby (7%!)(MISSING). Go is clearly attracting many programmers from dynamic languages.

Is there anything else you would like to share with us? 
 95 (2.6%!)(MISSING) thanks 
 94 (2.6%!)(MISSING) great 
 86 (2.4%!)(MISSING) thank you 
 47 (1.3%!)(MISSING) keep up the good work 
 47 (1.3%!)(MISSING) programming 
 43 (1.2%!)(MISSING) community 
 39 (1.1%!)(MISSING) c 
 37 (1.0%!)(MISSING) awesome 
 33 (0.9%!)(MISSING) i love 
 31 (0.9%!)(MISSING) people 
 29 (0.8%!)(MISSING) golang 
 27 (0.8%!)(MISSING) great work 
 27 (0.8%!)(MISSING) java 
 27 (0.8%!)(MISSING) languages 
 26 (0.7%!)(MISSING) fun 
 26 (0.7%!)(MISSING) job 
 26 (0.7%!)(MISSING) time 
 25 (0.7%!)(MISSING) love go 
 24 (0.7%!)(MISSING) generics 
 24 (0.7%!)(MISSING) team 
 23 (0.6%!)(MISSING) projects 
 22 (0.6%!)(MISSING) best 
 22 (0.6%!)(MISSING) wish 
 22 (0.6%!)(MISSING) years 
 21 (0.6%!)(MISSING) simple 
 2,886 (80.3%!)(MISSING) No response

### Go 1.8 is released

Go 1.8 includes many more additions, improvements, and fixes. Find the complete set of changes, and more information about the improvements listed above, in the Go 1.8 release notes.

To celebrate the release, Go User Groups around the world are holding release parties this week. Release parties have become a tradition in the Go community, so if you missed out this time, keep an eye out when 1.9 nears.

### Participate in the 2016 Go User Survey and Company Questionnaire

The Go project wants to hear from you! Our goal is to create the best language for developing simple, reliable, scalable software. We are asking you to help by participating in a survey and if applicable, a company questionnaire.


Who: If you use Go, have ever used Go, have ever stopped using Go, or have any interest in the language, please help by sharing your feedback to improve Go for you and your fellow Gophers.

The survey is anonymous and confidential.


Why: The Go project leadership depends on your feedback to guide the future of the Go project. Your responses will help to understand how we are doing and help plan improvements for the Go language, libraries, and tools.


### Introducing HTTP Tracing

In Go 1.7 we introduced HTTP tracing, a facility to gather fine-grained information throughout the lifecycle of an HTTP client request. Support for HTTP tracing is provided by the net/http/httptrace package. The collected information can be used for debugging latency issues, service monitoring, writing adaptive systems, and more.

The httptrace package provides a number of hooks to gather information during an HTTP round trip about a variety of events. These events include:
•  Connection creation
•  Connection reuse
•  DNS lookups
•  Writing the request to the wire
•  Reading the response

The program above will print the DNS information as soon as the DNS lookup is complete. It will similarly print connection information when a connection is established to the request's host.


Tracing with http.Client
The tracing mechanism is designed to trace the events in the lifecycle of a single http.Transport.RoundTrip. 

Connection reused for https://google.com? false
Connection reused for https://www.google.com/? false

HTTP tracing is a valuable addition to Go for those who are interested in debugging HTTP request latency and writing tools for network debugging for outbound traffic. By enabling this new facility, we hope to see HTTP debugging, benchmarking and visualization tools from the community — such as httpstat.

### Smaller Go 1.7 binaries

Go was designed for writing servers. That is how it is most widely used today, and as a result a lot of work on the runtime and compiler is focused on issues that matter to servers: latency, ease of deployment, precise garbage collection, fast startup time, performance.

As Go gets used for a wider variety of programs, there are new issues that must be considered. One of these is binary size. It has been on the radar for a long time (issue #6853 was filed over two years ago), but the growing interest in using Go for deploying binaries on smaller devices — such as the Raspberry Pi or mobile devices — means it received some attention for the Go 1.7 release.

The canonical hello world program goes from 2.3MB to 1.6MB:

These changes are all conservative, reducing binary size without increasing build time, startup time, overall execution time, or memory usage. 

### Go 1.7 is released

Over the past few years, the golang.org/x/net/context package has proven to be essential to many Go applications. Contexts are used to great effect in applications related to networking, infrastructure, and microservices (such as Kubernetes and Docker). They make it easy to enable cancelation, timeouts, and passing request-scoped data.

### Go 1.6 is released

The most significant change is support for HTTP/2 in the net/http package. HTTP/2 is a new protocol, a follow-on to HTTP that has already seen widespread adoption by browser vendors and major websites. In Go 1.6, support for HTTP/2 is enabled by default for both servers and clients when using HTTPS, bringing the benefits of the new protocol to a wide range of Go projects, such as the popular Caddy web server.

The template packages have learned some new tricks, with support for trimming spaces around template actions to produce cleaner template output, and the introduction of the {{block}} action that can be used to create templates that build on other templates. 

### Language and Locale Matching in Go

This post explains why this is a difficult decision and how Go can help.

The most common use of language tags is to select from a set of system-supported languages according to a list of the user's language preferences

For historical and political reasons, many language codes have changed over time, leaving languages with an older legacy code as well as a new one. But even two current codes may refer to the same language.

Go's golang.org/x/text/language package solves this complex problem while still presenting a simple, easy-to-use API. Enjoy.

### Six years of Go

Improvements to tools continue to boost developer productivity. 

The Go team would like to thank everyone who has contributed code, written an open source library, authored a blog post, helped a new gopher, or just given Go a try. Without you, Go would not be as complete, useful, or successful as it is today. Thank you, and celebrate!

### Golang UK 2015

It was a great conference, so congratulations to the organizers and see you next year in London!

### Go 1.5 is released

This release includes significant changes to the implementation. The compiler tool chain was translated from C to Go, removing the last vestiges of C code from the Go code base. The garbage collector was completely redesigned, yielding a dramatic reduction in garbage collection pause times. Related improvements to the scheduler allowed us to change the default GOMAXPROCS value (the number of concurrently executing goroutines) from 1 to the number of logical CPUs.

### Go, Open Source, Community

I am the tech lead for the Go project and the Go team at Google. I share that role with Rob Pike. In that role, I spend a lot of time thinking about the overall Go open source project, in particular the way it runs, what it means to be open source, and the interaction between contributors inside and outside Google. 

To get started, we have to go back to the beginning. Why did we start working on Go?
Go is an attempt to make programmers more productive. We wanted to improve the software development process at Google, but the problems Google has are not unique to Google.

Today, that kind of software has a shorter name: we call it cloud software. It's fair to say that Go was designed for the cloud before clouds ran software.


How does it make scalable concurrency and scalable software development easier?
Most people answer this question by talking about channels and goroutines, and interfaces, and fast builds, and the go command, and good tool support. Those are all important parts of the answer, but I think there is a broader idea behind them.

The way I would summarize Go's chosen balance is this: Do Less. Enable More.

Keeping the language small enables more important goals. Being small makes Go easier to learn, easier to understand, easier to implement, easier to reimplement, easier to debug, easier to adjust, and easier to evolve. Doing less enables more.


Next, types and interfaces. Having static types enables useful compile-time checking, something lacking in dynamically-typed languages like Python or Ruby. At the same time, Go's static typing avoids much of the repetition of traditional statically typed languages, making it feel more lightweight, more like the dynamically-typed languages. This was one of the first things people noticed, and many of Go's early adopters came from dynamically-typed languages.

Go does not have this problem. We designed the language to make gofmt possible, we worked hard to make gofmt's formatting acceptable for all Go programs, and we made sure gofmt was there from day one of the original public release. Gofmt imposes such uniformity that automated changes blend into the rest of the file.

Google pays me and others to work on Go, because, if Google's programmers are more productive, Google can build products faster, maintain them more easily, and so on. But why open source Go? Why should Google share this benefit with the world?

The business justification is that Go is open source because that's the only way that Go can succeed. We, the team that built Go within Google, knew this from day one. We knew that Go had to be made available to as many people as possible for it to succeed.


Closed languages die.
A language needs large, broad communities.
A language needs lots of people writing lots of software, so that when you need a particular tool or library, there's a good chance it has already been written, by someone who knows the topic better than you, and who spent more time than you have to make it great.


A language needs lots of people reporting bugs, so that problems are identified and fixed quickly. Because of the much larger user base, the Go compilers are much more robust and spec-compliant than the Plan 9 C compilers they're loosely based on ever were.


A language needs lots of people using it for lots of different purposes, so that the language doesn't overfit to one use case and end up useless when the technology landscape changes.


None of this could have happened if Go had stayed within Google. Go would have suffocated inside Google, or inside any single company or closed environment.

Fundamentally, Go must be open, and Go needs you. Go can't succeed without all of you, without all the people using Go for all different kinds of projects all over the world.

In the very early days, before Go was known to the public, the Go team at Google was obviously working by itself. We wrote the first draft of everything: the specification, the compiler, the runtime, the standard library.

But as Rob said at Gophercon last year, "the language is done." Now we need to see how it works, to see how people use it, to see what people build. The focus now is on expanding the kind of work that Go can help with.

Google's primarily role is now to enable the community, to coordinate, to make sure changes work well together, and to keep Go true to the original vision.

Google's primary role is: Do Less. Enable More.

More recently, a group of contributors led by Aram Hăvărneanu ported Go to the ARM 64 architecture, This was the first architecture port by contributors outside Google. This is significant, because in general support for a new architecture requires more design work than support for a new operating system. There is more variation between architectures than between operating systems.

I consider the many people releasing software for download using “go get,” sharing their insights via blog posts, or helping others on the mailing lists or IRC to be part of this broad open source effort, part of the Go community. Everyone here today is also part of that community.

For the next few days, please: tell us what we're doing right, tell us what we're doing wrong, and help us all work together to make Go even better.

Remember to be friendly, patient, welcoming, considerate, and respectful.
Above all, enjoy the conference.

### Qihoo 360 and Go

My team, the Push Service Team, provides fundamental messaging services for more than 50 products across the company (both PC and mobile), including thousands of Apps in our open platform.

The initial version was built with nginx + lua + redis, which failed to satisfy our requirement for real-time performance due to excessive load.

However, things didn’t go exactly as we planned, we ran into trouble migrating the code of Business Logic Layer. As a result, it was impossible for the only personnel at that time (myself) to maintain the Go system while ensuring the logic transfer to the C service framework.

Here are a few tweaks we made and key take-aways:
•  Replace short connections with persistent ones (using a connection pool), to reduce creation of buffers and objects during communication.

•  Use a Task Pool, a mechanism with a group of long-lived goroutines consuming global task or message queues sent by connection goroutines, to replace short-lived goroutines.

The great thing about this platform is that we can actually simulate the connection and behavior of millions of online users, by applying the Distributed Stress Test Tool (also built using Go), and observe all real-time visualized data. This allows us to evaluate the effectiveness of any optimization and preclude problems by identifying system bottlenecks.

Many other teams within Qihoo have already either got to know Go, or tried to use Go.

### GopherChina Trip Report

We have known for some time that Go is more popular in China than in any other country. According to Google Trends, most searches for the term “golang” come from The People’s Republic than anywhere else.

The first Go conference in China, GopherChina, seemed like an excellent opportunity to explore the situation by putting some Western Gopher feet on Chinese ground. An actual invitation made it real and I decided to accept and give a presentation about gofmt’s impact on software development.

As expected, many of the presentations were about web services, backends for mobile applications, and so on. Some of the systems appear to be huge by any measure.

The presentation discussed how his team managed to reduce an original heap size of 69GB (!) and the resulting long GC pauses of 3-6s to more manageable numbers, and how they run millions of goroutines per machine, on a fleet of thousands of machines. A future guest blog post is planned describing this system in more detail.

In another presentation, Feng Guo from DaoCloud talked about how they use Go in their company for what they call the “continuous delivery” of applications. DaoCloud takes care of automatically moving software hosted on GitHub (and Chinese equivalents) to the cloud. A software developer simply pushes a new version on GitHub and DaoCloud takes care of the rest: running tests, Dockerizing it, and shipping it using your preferred cloud service provider.


Qiniu is a cloud-based storage provider for mobile applications; Wei Hsu presented at the conference and also happens to be the author of one of the first Chinese books on Go (the leftmost one in the picture above).

Qiniu is an extremely successful all-Go shop, with about 160 employees, serving over 150,000 companies and developers, storing over 50 Billion files, and growing by over 500 Million files per day.

When asked about the reasons for Go’s success in China, Wei Hsu is quick to answer: PHP is extremely popular in China, but relatively slow and not well-suited for large systems. 

In his opinion, Go now plays the role that traditionally belonged to PHP, but Go runs much faster, is type safe, and scales more easily. He loves the fact that Go is simple and applications are easy to deploy. He thought the language to be “perfect” for them and his primary request was for a recommended or even standardized package to easily access database systems.

For Qiniu, Go appeared just at the right time and the right (open source) place.

According to Asta Xie, Qiniu is just one of many Go shops in the PRC. Large companies such as Alibaba, Baidu, Tencent, and Weibo, are now all using Go in one form or another. He pointed out that while Shanghai and neighboring cities like Suzhou are high-tech centres, even more software developers are found in the Beijing area.

### Testable Examples in Go

Having executable documentation for a package guarantees that the information will not go out of date as the API changes.

Godoc examples are a great way to write and maintain code as documentation. They also present editable, working, runnable examples your users can build on. Use them!

### Package names

Go code is organized into packages. Within a package, code can refer to any identifier (name) defined within, while clients of the package may only reference the package's exported types, functions, constants, and variables. Such references always include the package name as a prefix: foo.Bar refers to the exported name Bar in the imported package named foo.


Good package names are short and clear. They are lower case, with no under_scores or mixedCaps. They are often simple nouns, such as:
•  time (provides functionality for measuring and displaying time)
•  list (implements a doubly linked list)
•  http (provides HTTP client and server implementations)


Don't steal good names from the user. Avoid giving a package a name that is commonly used in client code. For example, the buffered I/O package is called bufio, not buf, since buf is a good variable name for a buffer.

Avoid repetition. Since client code uses the package name as a prefix when referring to the package contents, the names for those contents need not repeat the package name. The HTTP server provided by the http package is called Server, not HTTPServer. Client code refers to this type as http.Server, so there is no ambiguity.

Simplify function names. When a function in package pkg returns a value of type pkg.Pkg (or *pkg.Pkg), the function name can often omit the type name without confusion:


A common situation is a package with multiple New-like functions:
d, err := time.ParseDuration("10s")  // d is a time.Duration
elapsed := time.Since(start)         // elapsed is a time.Duration
ticker := time.NewTicker(d)          // ticker is a *time.Ticker
timer := time.NewTimer(d)            // timer is a *time.Timer

By convention, the last element of the package path is the package name:

If a source file needs to import both pprof packages, it can rename one or both locally. When renaming an imported package, the local name should follow the same guidelines as package names (lower case, no under_scores or mixedCaps).

Bad package names make code harder to navigate and maintain. Here are some guidelines for recognizing and fixing bad names.

Avoid meaningless package names. Packages named util, common, or misc provide clients with no sense of what the package contains. This makes it harder for clients to use the package and makes it harder for maintainers to keep the package focused. 

This helps clients understand and use your packages and helps maintainers to grow them gracefully.


### Errors are values

Its Scan method performs the underlying I/O, which can of course lead to an error. Yet the Scan method does not expose an error at all. Instead, it returns a boolean, and a separate method, to be run at the end of the scan, reports whether an error occurred.

if err := scanner.Err(); err != nil {
    // process the error
}

With the real API, the client's code therefore feels more natural: loop until done, then worry about errors. Error handling does not obscure the flow of control.

A separate method, Err, reports the error value when the client asks.

It's worth stressing that whatever the design, it's critical that the program check the errors however they are exposed. The discussion here is not about how to avoid checking errors, it's about using the language to handle errors with grace.

It is very repetitive. In the real code, which was longer, there is more going on so it's not easy to just refactor this using a helper function, but in this idealized form, a function literal closing over the error variable would help:
var err error
write := func(buf []byte) {
    if err != nil {
        return
    }
    _, err = w.Write(buf)
}

This pattern works well, but requires a closure in each function doing the writes; a separate helper function is clumsier to use because the err variable needs to be maintained across calls (try it).

I defined an object called an errWriter, something like this:
type errWriter struct {
    w   io.Writer
    err error
}

and gave it one method, write. It doesn't need to have the standard Write signature, and it's lower-cased in part to highlight the distinction. The write method calls the Write method of the underlying Writer and records the first error for future reference:
func (ew *errWriter) write(buf []byte) {
    if ew.err != nil {
        return
    }
    _, ew.err = ew.w.Write(buf)
}

Also, once errWriter exists, there's more it could do to help, especially in less artificial examples. It could accumulate the byte count. It could coalesce writes into a single buffer that can then be transmitted atomically. And much more.


The key lesson, however, is that errors are values and the full power of the Go programming language is available for processing them.


Use the language to simplify your error handling.
But remember: Whatever you do, always check your errors!

### GothamGo: gophers in the big apple

•  Mutexes and Locks by Jessie Frazelle - a member of the Docker core team tells us about concurrency and what to do when things go wrong.

•  Gobot.io by Ron Evans - awesome robots controlled by Go, with demos!

•  Doing Go by Bryan Liles - a DigitalOcean engineer delivers a hilarious comedy routine that happens to be about Go.

### Generating code

Programs that write programs are therefore important elements in software engineering, but programs like Yacc that produce source code need to be integrated into the build process so their output can be compiled. When an external build tool like Make is being used, this is usually easy to do. But in Go, whose go tool gets all necessary build information from the Go source, there is a problem. There is simply no mechanism to run Yacc from the go tool alone.

The , 1.4, includes a new command that makes it easier to run such tools. It's called go generate, and it works by scanning for special comments in Go source code that identify general commands to run. It's important to understand that go generate is not part of go build. It contains no dependency analysis and must be run explicitly before running go build. It is intended to be used by the author of the Go package, not its clients.


The go generate command is easy to use. As a warmup, here's how to use it to generate a Yacc grammar.

For more details about how go generate works, including options, environment variables, and so on, see the design document.

In short, generating the method automatically allows us to do a better job than we would expect a human to do.

There are lots of other uses of go generate already installed in the Go tree. Examples include generating Unicode tables in the unicode package, creating efficient methods for encoding and decoding arrays in encoding/gob, producing time zone data in the time package, and so on.


### Go 1.4 is released

The most notable new feature in this release is official support for Android. 

And, of course, there are many more improvements and bug fixes.

### Half a decade with Go

Significant open source cloud projects like Docker and Kubernetes have been written in Go, and infrastructure companies like Google, CloudFlare, Canonical, Digital Ocean, GitHub, Heroku, and Microsoft are now using Go to do some heavy lifting.


### Deploying Go servers with Docker

This week Docker announced official base images for Go and other major languages, giving programmers a trusted and easy way to build containers for their Go programs.


# Run the outyet command by default when the container starts.
ENTRYPOINT /go/bin/outyet

# Document that the service listens on port 8080.
EXPOSE 8080

With the onbuild variant, the Dockerfile is much simpler:
FROM golang:onbuild
EXPOSE 8080

To run a container from the resulting image:
$ docker run --publish 6060:8080 --name test --rm outyet

The --publish flag tells docker to publish the container's port 8080 on the external port 6060.


The --rm flag tells docker to remove the container image when the outyet server exits.

(To see what's happening on the new VM instance you can ssh into it with gcloud compute ssh outyet. From there, try sudo docker ps to see which Docker containers are running.)


To learn more about Docker and Go, see the official golang Docker Hub repository and Kelsey Hightower's Optimizing Docker Images for Static Go Binaries.

### Constants

These untyped string constants are strings, of course, so they can only be used where a string is allowed, but they do not have type string.

The answer is that an untyped constant has a default type, an implicit type that it transfers to a value if a type is needed where none is provided. For untyped string constants, that default type is obviously string, so
str := "Hello, 世界"

means exactly the same as
var str string = "Hello, 世界"

In summary, a typed constant obeys all the rules of typed values in Go. On the other hand, an untyped constant does not carry a Go type in the same way and can be mixed and matched more freely. It does, however, have a default type that is exposed when, and only when, no other type information is available.

### Go at OSCON

Finally, Josh Bleecher Snyder talked about his experience writing tools to work with Go source code in Gophers with hammers, and Francesc Campoy talked about all the things that could have gone wrong and what the Go team did to prevent them Inside the Go playground.

### Go Concurrency Patterns: Context

In Go servers, each incoming request is handled in its own goroutine. Request handlers often start additional goroutines to access backends such as databases and RPC services. The set of goroutines working on a request typically needs access to request-specific values such as the identity of the end user, authorization tokens, and the request's deadline. When a request is canceled or times out, all the goroutines working on that request should exit quickly so the system can reclaim any resources they are using.

At Google, we developed a context package that makes it easy to pass request-scoped values, cancelation signals, and deadlines across API boundaries to all the goroutines involved in handling a request. The package is publicly available as context. This article describes how to use the package and provides a complete working example.

The core of the context package is the Context type:
// A Context carries a deadline, cancelation signal, and request-scoped values
// across API boundaries. Its methods are safe for simultaneous use by multiple
// goroutines.

type Context interface {
    // Done returns a channel that is closed when this Context is canceled
    // or times out.
    Done() <-chan struct{}


    // Err indicates why this context was canceled, after the Done channel
    // is closed.
    Err() error


    // Deadline returns the time when this Context will be canceled, if any.
    Deadline() (deadline time.Time, ok bool)


    // Value returns the value associated with key or nil if none.
    Value(key interface{}) interface{}
}



The Deadline method allows functions to determine whether they should start work at all; if too little time is left, it may not be worthwhile. Code may also use a deadline to set timeouts for I/O operations.

### Go 1.3 is released

Godoc, the Go documentation server, now performs static analysis. When enabled with the -analysis flag, analysis results are presented in both the source and package documentation views, making it easier than ever to navigate and understand Go programs. See the documentation for the details.

### Go Concurrency Patterns: Pipelines and cancellation

What is a pipeline?
There's no formal definition of a pipeline in Go; it's just one of many kinds of concurrent programs. Informally, a pipeline is a series of stages connected by channels, where each stage is a group of goroutines running the same function. In each stage, the goroutines

•  receive values from upstream via inbound channels
•  perform some function on that data, usually producing new values
•  send values downstream via outbound channels

Each stage has any number of inbound and outbound channels, except the first and last stages, which have only outbound or inbound channels, respectively. The first stage is sometimes called the source or producer; the last stage, the sink or consumer.


### Inside the Go Playground

•  A back end that runs on Google's servers. It receives RPC requests, compiles the user program using the gc tool chain, executes the user program, and returns the program output (or compilation errors) as the RPC response.

•  A front end that runs on Google App Engine. It receives HTTP requests from the client and makes corresponding RPC requests to the back end. It also does some caching.
•  A JavaScript client that implements the user interface and makes HTTP requests to the front end.

### The cover story

Testing is an important part of software development and test coverage a simple way to add discipline to your testing strategy. Go forth, test, and cover.

### Four years of Go

•  Docker is a tool for packaging and running applications in lightweight containers. Docker makes it easy to isolate, package, and deploy applications, and is beloved by system administrators.

•  Packer is a tool for automating the creation of machine images for deployment to virtual machines or cloud services. 

•  CloudFlare built their distributed DNS service entirely with Go, and are in the process of migrating their gigabytes-per-minute logging infrastructure to the language.

We've found Go to be the perfect match for our needs: the combination of familiar syntax, a powerful type system, a strong network library and built-in concurrency means that more and more projects are being built here in Go."

•  SoundCloud is an audio distribution service that has "dozens of systems in Go, touching almost every part of the site, and in many cases powering features from top to bottom."

 "ngrok's success as a project is due in no small part to choosing Go as the implementation language," citing Go's HTTP libraries, efficiency, cross-platform compatibility, and ease of deployment as the major benefits.


In September 2012, Apcera CEO Derek Collison predicted that "Go will become the dominant language for systems work in [Infastructure-as-a-Service], Orchestration, and [Platform-as-a-Service] in 24 months." Looking at the list above, it's easy to believe that prediction.

### Strings, bytes, runes and characters in Go

In Go, a string is in effect a read-only slice of bytes. If you're at all uncertain about what a slice of bytes is or how it works, please read the ; we'll assume here that you have.

To find out what that string really holds, we need to take it apart and examine the pieces. There are several ways to do this. The most obvious is to loop over its contents and pull out the bytes individually, as in this for loop:
for i := 0; i < len(sample); i++ {
        fmt.Printf("%!x(MISSING) ", sample[i])
    }

As we saw, indexing a string yields its bytes, not its characters: a string is just a bunch of bytes. That means that when we store a character value in a string, we store its byte-at-a-time representation. Let's look at a more controlled example to see how that happens.

The output is:
plain string: ⌘
quoted string: "\u2318"
hex bytes: e2 8c 98

### A conversation with the Go team

At Google I/O 2013, several members of the Go team hosted a "Fireside chat." 

Rob: Yes. We are always thinking about ways to improve performance of the tools as well as the language and libraries.

### Go 1.1 is released

In March last year we released Go 1.0, and since then we have released three minor "point releases". The point releases were made to fix only critical issues, so the Go 1.0.3 you use today is still, in essence, the Go 1.0 we released in March 2012.

The most significant improvements are performance-related. We have made optimizations in the compiler and linker, garbage collector, goroutine scheduler, map implementation, and parts of the standard library. It is likely that your Go code will run noticeably faster when built with Go 1.1.

### Go maps in action

Go maps in action

One of the most useful data structures in computer science is the hash table. Many hash table implementations exist with varying properties, but in general they offer fast lookups, adds, and deletes. Go provides a built-in map type that implements a hash table.

The delete function doesn't return anything, and will do nothing if the specified key doesn't exist.
A two-value assignment tests for the existence of a key:
i, ok := m["route"]

To iterate over the contents of a map, use the range keyword:
for key, value := range m {
    fmt.Println("Key:", key, "Value:", value)
}

To initialize a map with some data, use a map literal:
commits := map[string]int{
    "rsc": 3711,
    "r":   2138,
    "gri": 1908,
    "adg": 912,
}

Concurrency
Maps are not safe for concurrent use: it's not defined what happens when you read and write to them simultaneously. If you need to read from and write to a map from concurrently executing goroutines, the accesses must be mediated by some kind of synchronization mechanism. One common way to protect maps is with sync.RWMutex.

This statement declares a counter variable that is an anonymous struct containing a map and an embedded sync.RWMutex.
var counter = struct{
    sync.RWMutex
    m map[string]int
}{m: make(map[string]int)}

To read from the counter, take the read lock:
counter.RLock()
n := counter.m["some_key"]
counter.RUnlock()
fmt.Println("some_key:", n)

To write to the counter, take the write lock:
counter.Lock()
counter.m["some_key"]++
counter.Unlock()

When iterating over a map with a range loop, the iteration order is not specified and is not guaranteed to be the same from one iteration to the next. If you require a stable iteration order you must maintain a separate data structure that specifies that order.

This example uses a separate sorted slice of keys to print a map[int]string in key order:
import "sort"

var m map[int]string
var keys []int
for k := range m {
    keys = append(keys, k)
}
sort.Ints(keys)
for _, k := range keys {
    fmt.Println("Key:", k, "Value:", m[k])
}

### go fmt your code

Gofmt is a tool that automatically formats Go source code.
Gofmt'd code is:
•  easier to write: never worry about minor formatting concerns while hacking away,
•  easier to read: when all code looks the same you need not mentally convert others' formatting style into something you can understand.

•  easier to maintain: mechanical changes to the source don't cause unrelated changes to the file's formatting; diffs show only the real changes.

### Concurrency is not parallelism

But when people hear the word concurrency they often think of parallelism, a related but quite distinct concept. In programming, concurrency is the composition of independently executing processes, while parallelism is the simultaneous execution of (possibly related) computations. Concurrency is about dealing with lots of things at once. Parallelism is about doing lots of things at once.

### The App Engine SDK and workspaces (GOPATH)

We hope these changes will make it easier to work on apps with external dependencies, and to maintain code bases that contain both stand-alone programs and App Engine apps.

### Go turns three

We use Go at Google in a variety of ways, many of them invisible to the outside world. A few visible ones include serving Chrome and other downloads, scaling MySQL database at YouTube, and of course running the Go home page on App Engine. Last year's Thanksgiving Doodle and the recent Jam with Chrome site are also served by Go programs.

Other companies and projects are using Go too, including BBC Worldwide, Canonical, CloudFlare, Heroku, Novartis, SoundCloud, SmugMug, StatHat, Tinkercad, and many others.


### Organizing Go code

Choose good names
The names you choose affect how you think about your code, so take care when naming your package and its exported identifiers.

Good documentation is an essential quality of usable and maintainable code. Read the Godoc: documenting Go code article to learn how to write good doc comments.


### Go videos from Google I/O 2012

Concurrency is the key to designing high performance network services. Go's concurrency primitives (goroutines and channels) provide a simple and efficient means of expressing concurrent execution. In this talk we see how tricky concurrency problems can be solved gracefully with simple Go code.

### Go version 1 is released

Today marks a major milestone in the development of the Go programming language. We're announcing Go version 1, or Go 1 for short, which defines a language and a set of core libraries to provide a stable foundation for creating reliable products, projects, and publications.

Forward compatibility is part of stability.

Go 1 is a representation of Go as it is used today, not a major redesign. In its planning, we focused on cleaning up problems and inconsistencies and improving portability. 

### Building StatHat with Go

StatHat is a tool to track statistics and events in your code. Everyone from HTML designers to backend engineers can use StatHat easily, as it supports sending stats from HTML, JavaScript, Go, and twelve other languages.

StatHat consists of two main services: incoming statistic/event API calls and the web application for viewing and analyzing stats. We wanted to keep these as separate as possible to isolate the data collection from the data interaction. 

Since StatHat is a multi-tiered system, we wanted an RPC layer so that all communication was standard. With Go, we are using the rpc package and the gob package for encoding Go objects. In Go, the RPC server just takes any Go object and registers its exported methods. There is no need for an intermediary interface description language. We've found it very easy to use and many of our core application servers are under 300 lines of code.

### From zero to Go: launching on the Google homepage in 24 hours

This article was written by Reinaldo Aguiar, a software engineer from the Search team at Google. He shares his experience developing his first Go program and launching it to an audience of millions - all in one day!

•  Make a copy of the background image as the base for the final image.
•  Draw each image element onto the background image using the layoutMap to determine where they should be drawn.
•  Encode the image as a JPEG
•  Return the image to user by writing the JPEG directly to the HTTP response writer.

Should any error occur, we serve the defaultImage to the user and log the error to the App Engine dashboard for later analysis.

I found Go's syntax to be intuitive, simple and clean. I have worked a lot with interpreted languages in the past, and although Go is instead a statically typed and compiled language, writing this app felt more like working with a dynamic, interpreted language.

Go's great documentation also helped me put this together fast. The docs are generated from the source code, so each function's documentation links directly to the associated source code. This not only allows the developer to understand very quickly what a particular function does but also encourages the developer to dig into the package implementation, making it easier to learn good style and conventions.


In writing this application I used just three resources: App Engine's Hello World Go example, the Go packages documentation, and a blog post showcasing the Draw package. Thanks to the rapid iteration made possible by the development server and the language itself, I was able to pick up the language and build a super fast, production ready, doodle generator in less than 24 hours.

### The Go Programming Language turns two

While 2010 was a year of discovery and experimentation, 2011 was a year of fine tuning and planning for the future.

### Writing scalable App Engine applications

Making your web application fast is important because it is well known that a web site's latency has a measurable impact on user happiness,

### Learn Go from your browser

The tour has three sections. The first section covers basic syntax and data structures; the second discusses methods and interfaces; and the third introduces Go's concurrency primitives. Each section concludes with a few exercises so you can practice what you've learned.

### The Laws of Reflection

Reflection in computing is the ability of a program to examine its own structure, particularly through types; it's a form of metaprogramming. It's also a great source of confusion.

In this article we attempt to clarify things by explaining how reflection works in Go. Each language's reflection model is different (and many languages don't support it at all), but this article is about Go, so for the rest of this article the word "reflection" should be taken to mean "reflection in Go".


Go is statically typed. Every variable has a static type, that is, exactly one type known and fixed at compile time: int, float32, *MyType, []byte, and so on.

type MyInt int

var i int
var j MyInt

then i has type int and j has type MyInt. The variables i and j have distinct static types and, although they have the same underlying type, they cannot be assigned to one another without a conversion.

One important category of type is interface types, which represent fixed sets of methods. An interface variable can store any concrete (non-interface) value as long as that value implements the interface's methods. 

// Reader is the interface that wraps the basic Read method.
type Reader interface {
    Read(p []byte) (n int, err error)
}

// Writer is the interface that wraps the basic Write method.
type Writer interface {
    Write(p []byte) (n int, err error)
}

For the purposes of this discussion, that means that a variable of type io.Reader can hold any value whose type has a Read method:

var r io.Reader
r = os.Stdin
r = bufio.NewReader(r)
r = new(bytes.Buffer)
// and so on

It's important to be clear that whatever concrete value r may hold, r's type is always io.Reader: Go is statically typed and the static type of r is io.Reader.


An extremely important example of an interface type is the empty interface:
interface{}

It represents the empty set of methods and is satisfied by any value at all, since any value has zero or more methods.

A variable of interface type stores a pair: the concrete value assigned to the variable, and that value's type descriptor. To be more precise, the value is the underlying concrete data item that implements the interface and the type describes the full type of that item. 

r contains, schematically, the (value, type) pair, (tty, *os.File).

After the assignment, w will contain the pair (tty, *os.File). That's the same pair as was held in r. 

var empty interface{}

our empty interface value empty will again contain that same pair, (tty, *os.File). That's handy: an empty interface can hold any value and contains all the information we could ever need about that value.

One important detail is that the pair inside an interface always has the form (value, concrete type) and cannot have the form (value, interface type). Interfaces do not hold interface values.

At the basic level, reflection is just a mechanism to examine the type and value pair stored inside an interface variable.

But it's there; as godoc reports, the signature of reflect.TypeOf includes an empty interface:
// TypeOf returns the reflection Type of the value in the interface{}.
func TypeOf(i interface{}) Type

When we call reflect.TypeOf(x), x is first stored in an empty interface, which is then passed as the argument; reflect.TypeOf unpacks that empty interface to recover the type information.

(We call the String method explicitly because by default the fmt package digs into a reflect.Value to show the concrete value inside. The String method does not.)


3. To modify a reflection object, the value must be settable.


### Error handling and Go

The following code uses os.Open to open a file. If an error occurs it calls log.Fatal to print the error message and stop.
f, err := os.Open("filename.ext")
if err != nil {
    log.Fatal(err)
}
// do something with the open *File f

// errorString is a trivial implementation of error.
type errorString struct {
    s string
}

func (e *errorString) Error() string {
    return e.s
}

// New returns an error that formats as the given text.
func New(text string) error {
    return &errorString{text}
}

The fmt package formats an error value by calling its Error() string method.

The error interface requires only a Error method; specific error implementations might have additional methods. 

type Error interface {
    error
    Timeout() bool   // Is the error a timeout?
    Temporary() bool // Is the error temporary?
}

if nerr, ok := err.(net.Error); ok && nerr.Temporary() {
    time.Sleep(1e9)
    continue
}
if err != nil {
    log.Fatal(err)
}

type appHandler func(http.ResponseWriter, *http.Request) error

func (fn appHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    if err := fn(w, r); err != nil {
        http.Error(w, err.Error(), 500)
    }
}

To do this we create an appError struct containing an error and some other fields:
type appError struct {
    Error   error
    Message string
    Code    int
}

### Profiling Go Programs

By using Go's profiling tools to identify and correct specific bottlenecks, we can make the Go loop finding program run an order of magnitude faster and use 6x less memory. (Update: Due to recent optimizations of libstdc++ in gcc, the memory reduction is now 3.7x.)

# for i in /sys/devices/system/cpu/cpu[0-7]
do
    echo performance > $i/cpufreq/scaling_governor
done
#

### Go at Heroku

In their own words, they "eat, drink, and sleep distributed systems." Here they discuss their experiences using Go.


A big problem that comes with building distributed systems is the coordination of physical servers. Each server needs to know various facts about the system as a whole. This critical data includes locks, configuration data, and so on, and it must be consistent and available even during data store failures, so we need a data store with solid consistency guarantees. Our solution to this problem is Doozer, a new, consistent, highly-available data store written in Go.

### Introducing Gofix

Gofix is a new tool that reduces the amount of effort it takes to update existing code. It reads a program from a source file, looks for uses of old APIs, rewrites them to use the current API, and writes the program back to the file. Not all API changes preserve all the functionality of an old API, so gofix cannot always do a perfect job. When gofix cannot rewrite a use of an old API, it prints a warning giving the file name and line number of the use, so that a developer can examine and rewrite the code. Gofix takes care of the easy, repetitive, tedious changes, so that a developer can focus on the ones that truly merit attention.

### Godoc: documenting Go code

The Go project takes documentation seriously. Documentation is a huge part of making software accessible and maintainable. 

Godoc parses Go source code - including comments - and produces documentation as HTML or plain text. The end result is documentation tightly coupled with the code it documents.

Notice this comment is a complete sentence that begins with the name of the element it describes. This important convention allows us to generate documentation in a variety of formats, from plain text to HTML to UNIX man pages, and makes it read better when tools truncate it for brevity, such as when they extract the first line or sentence.

•  Subsequent lines of text are considered part of the same paragraph; you must leave a blank line to separate paragraphs.

•  URLs will be converted to HTML links; no special markup is necessary.

### Gobs of data

To transmit a data structure across a network or to store it in a file, it must be encoded and then decoded again. There are many encodings available, of course: JSON, XML, Google's protocol buffers, and more. And now there's another, provided by Go's gob package.

Efficiency is also important. Textual representations, exemplified by XML and JSON, are too slow to put at the center of an efficient communications network. A binary encoding is necessary.

### C? Go? Cgo!

Cgo lets Go packages call C code. Given a Go source file written with some special features, cgo outputs Go and C files that can be combined into a single Go package.


Strings and things
Unlike Go, C doesn't have an explicit string type. Strings in C are represented by a zero-terminated array of chars.
Conversion between Go and C strings is done with the C.CString, C.GoString, and C.GoStringN functions. These conversions make a copy of the string data.


### JSON and Go

It is most commonly used for communication between web back-ends and JavaScript programs running in the browser, but it is used in many other places, too. Its home page, json.org, provides a wonderfully clear and concise definition of the standard.

To encode JSON data we use the Marshal function.
func Marshal(v interface{}) ([]byte, error)

•  JSON objects only support strings as keys; to encode a Go map type it must be of the form map[string]T (where T is any Go type supported by the json package).
•  Channel, complex, and function types cannot be encoded.

We must first create a place where the decoded data will be stored
var m Message

and call json.Unmarshal, passing it a []byte of JSON data and a pointer to m
err := json.Unmarshal(b, &m)

Unmarshal will decode only the fields that it can find in the destination type. In this case, only the Name field of m will be populated, and the Food field will be ignored. This behavior is particularly useful when you wish to pick only a few specific fields out of a large JSON blob. It also means that any unexported fields in the destination struct will be unaffected by Unmarshal.

But what if you don't know the structure of your JSON data beforehand?


The interface{} (empty interface) type describes an interface with zero methods. Every Go type implements at least zero methods and therefore satisfies the empty interface.
The empty interface serves as a general container type:
var i interface{}
i = "a string"
i = 2011
i = 2.777

A type assertion accesses the underlying concrete type:
r := i.(float64)
fmt.Println("the circle's area", math.Pi*r*r)

Or, if the underlying type is unknown, a type switch determines the type:
switch v := i.(type) {
case int:
    fmt.Println("twice i is", v*2)
case float64:
    fmt.Println("the reciprocal of i is", 1/v)

The json package uses map[string]interface{} and []interface{} values to store arbitrary JSON objects and arrays; it will happily unmarshal any valid JSON blob into a plain interface{} value. The default concrete Go types are:
•  bool for JSON booleans,
•  float64 for JSON numbers,
•  string for JSON strings, and
•  nil for JSON null.


Decoding arbitrary data
Consider this JSON data, stored in the variable b:
b := []byte(`{"Name":"Wednesday","Age":6,"Parents":["Gomez","Morticia"]}`)

Without knowing this data's structure, we can decode it into an interface{} value with Unmarshal:
var f interface{}
err := json.Unmarshal(b, &f)

At this point the Go value in f would be a map whose keys are strings and whose values are themselves stored as empty interface values:
f = map[string]interface{}{
    "Name": "Wednesday",
    "Age":  6,
    "Parents": []interface{}{
        "Gomez",
        "Morticia",
    },
}

To access this data we can use a type assertion to access f's underlying map[string]interface{}:
m := f.(map[string]interface{})

We can then iterate through the map with a range statement and use a type switch to access its values as their concrete types:
for k, v := range m {
    switch vv := v.(type) {
    case string:
        fmt.Println(k, "is string", vv)
    case float64:
        fmt.Println(k, "is float64", vv)
    case []interface{}:
        fmt.Println(k, "is an array:")
        for i, u := range vv {
            fmt.Println(i, u)
        }
    default:
        fmt.Println(k, "is of a type I don't know how to handle")
    }
}

Streaming Encoders and Decoders
The json package provides Decoder and Encoder types to support the common operation of reading and writing streams of JSON data. The NewDecoder and NewEncoder functions wrap the io.Reader and io.Writer interface types.

### Go Slices: usage and internals

Go's slice type provides a convenient and efficient means of working with sequences of typed data. Slices are analogous to arrays in other languages, but have some unusual properties. This article will look at what slices are and how they are used.


A slice is a descriptor of an array segment. It consists of a pointer to the array, the length of the segment, and its capacity (the maximum length of the segment).

To append one slice to another, use ... to expand the second argument to a list of arguments.


### Go: one year ago today

•  Semicolons are now optional in almost all instances. spec

•  The new built-in function recover complements panic and defer as an error handling mechanism. blog, spec

Go is certainly ready for production use, but there is still room for improvement. Our focus for the immediate future is making Go programs faster and more efficient in the context of high performance systems. This means improving the garbage collector, optimizing generated code, and improving the core libraries.

### Real Go Projects: SmartTwitter and web.go

I was introduced to Go by a post on Hacker News. About an hour later I was hooked. At the time I was working at a web start-up, and had been developing internal testing apps in Python. Go offered speed, better concurrency support, and sane Unicode handling, so I was keen to port my programs to the language. 

The entire program is written in Go, and uses Redis as its storage back-end. It is very fast and robust. It currently processes about two dozen tweets per second, and makes heavy use of Go's channels. It runs on a single Virtual Private Server instance with 2GB of RAM, which has no problem handling the load. Smart Twitter uses very little CPU time, and is almost entirely memory-bound as the entire database is kept in memory.

 any given time there are around 10 goroutines running concurrently: one accepting HTTP connections, another reading from the Twitter Streaming API, a couple for error handling, and the rest either processing web requests or re-posting incoming tweets.

### Defer, Panic, and Recover

A defer statement pushes a function call onto a list. The list of saved calls is executed after the surrounding function returns. Defer is commonly used to simplify functions that perform various clean-up actions.

By introducing defer statements we can ensure that the files are always closed:

Defer statements allow us to think about closing each file right after opening it, guaranteeing that, regardless of the number of return statements in the function, the files will be closed.

There are three simple rules:
1.  A deferred function's arguments are evaluated when the defer statement is evaluated.

Panic is a built-in function that stops the ordinary flow of control and begins panicking. When the function F calls panic, execution of F stops, any deferred functions in F are executed normally, and then F returns to its caller. To the caller, F then behaves like a call to panic. The process continues up the stack until all functions in the current goroutine have returned, at which point the program crashes. Panics can be initiated by invoking panic directly. They can also be caused by runtime errors, such as out-of-bounds array accesses.

Recover is a built-in function that regains control of a panicking goroutine. Recover is only useful inside deferred functions. During normal execution, a call to recover will return nil and have no other effect. If the current goroutine is panicking, a call to recover will capture the value given to panic and resume normal execution.

Try it out.

### Share Memory By Communicating

Go's concurrency primitives - goroutines and channels - provide an elegant and distinct means of structuring concurrent software.

Do not communicate by sharing memory; instead, share memory by communicating.

### Go at I/O: Frequently Asked Questions

How suitable is Go for production systems? Go is ready and stable now. We are pleased to report that Google is using Go for some production systems, and they are performing well. Of course there is still room for improvement - that's why we're continuing to work on the language, libraries, tools, and runtime.

Are there plans to support Go under Android? Both Go compilers support ARM code generation, so it is possible. While we think Go would be a great language for writing mobile applications, Android support is not something that's being actively worked on.

What can I use Go for? Go was designed with systems programming in mind. Servers, clients, databases, caches, balancers, distributors - these are applications Go is obviously useful for, and this is how we have begun to use it within Google. 

### Go: What's New in March 2010

Welcome to the official Go Blog. We, the Go team, hope to use this blog to keep the world up-to-date on the development of the Go programming language and the growing ecosystem of libraries and applications surrounding it.

More than 40,000 lines of code have been added to the standard library, including many entirely new packages, a sizable portion written by external contributors.

2010 will be an exciting year for Go, and we look forward to collaborating with the community to make it a successful one.