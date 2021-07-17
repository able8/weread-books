## 12 Factor Applications with Docker and Go
> Tit Petric

### Title Page

12 Factor Applications with Docker and Go
A book filled with examples on how to use Docker and Go to create the ultimate 12 Factor applications


### Introduction

I have built numerous APIs for my own content management products. Several products I’ve been involved with as a lead developer have been sold and are used in multiple countries. 

Who is this book for?
This book is for everyone who writes applications for a living. I cover a wide area of subjects dedicated to development of software in general, trying to establish some good development practices, while at the same time referencing the 12 Factor App manifesto.

Covering these concepts should give you a good overview of the 12 Factor App designs and what problems they are trying to solve. The book tries to give you a set of best practices to follow and guide you through individual chapters shedding light and hands on examples of individual approaches.


### Requirements

This is a book which demonstrates 12 factor application development on the use case of developing and deploying microservices.

The examples of the book rely on a recent docker-engine installation on a Linux host.

Please refer to the official docker installation instructions on how to install a recent docker version.


After signing up, creating a Linux instance with a running Docker engine is simple, and only takes a few clicks.

 Click it, and on the page it opens, navigate to “One-click apps” where you choose a “Docker” from the list.

 would definitely advise choosing an instance with at least 30GB of disk space

you will have to keep an eye out for disk usage. It’s been known to fill up.

If you don’t need an instance 24/7, you can use the API to spin it up and shut it down based on your needs. It’s good for having better quality development machines, which you delete after you’re done for the day, saving about half of the costs.


We can use the docker-16-04 image to quickly spin up a droplet. We will create create.sh and destroy.sh, which will check for existance of the droplet before it’s created or destroyed.

You can adjust the parameters based on the size of the droplet you would like and the region where you want it to run.


Of course, when your droplet spins up, you’ll want to list it’s public IP, or connect to it via SSH.

As soon as you SSH into the instance, you can use any docker run command you like. At this point it’s up to you to set up any git checkouts or somehow populate the running droplet.

Networking
When dealing with microservices in docker, it’s a very common practice to enable communication between one service and another. For example, many microservices may and do communicate to each other. For this purpose we create a network called “party”, on which the microservices from this book will run on.

You may define multiple networks to isolate docker containers away from each other.

Please consult that chapter for more examples.


### I. Codebase - One codebase tracked in revision control, many deploys

Commits can be recalled at a later time in case you need to roll-back your code for any reason, including getting a deleted section of source code restored at a later time.


And when it comes to editing the same file - users work on local copies, and when they push their commits, a conflict might occur. Resolving the conflict may be as simple as editing a file, picking the lines which should stay and committing the changes.

Make sure that you perform a full backup on occasion and have procedures in place which allow you to restore it from a backup. This should go without saying.


Your application project or microservice should use one repository just for the sake of simplicity.

You can use multiple repositories in a project, but it will increase complexity, usually not with good results. 

. I’m going to walk you through the process of setting up Gogs. And since this is a book that relies heavily on docker, we’ll use it to set up our Gogs instance.


it’s always an easy task to migrate from Gogs to GitLab, GitHub or Bitbucket at a later time, and I’m going to show you how to do that as well.


After executing this script, Gogs is running in a docker container with the same name, exposing ports 10022 for ssh access, and port 3000 for http access.

In the options, you may configure it by connecting to the MySQL instance

or you may use a SQLite3 back-end, to store it’s data locally without a database server.


When it comes to production use, individual usernames should be created for individual microservices, only giving access to the database of the microservice. That way, a gogs user could only access the database with the same name.

A better method is to use the ssh endpoint, where authentication is done with an ssh key.

several environments: development, staging, testing, production.


Ideally, the master branch would be read-only, and feature branches should be merged with pull requests. Pull requests enable code reviews, commenting and discussion before accepting some change into production.


We can inspect that the individual histories no longer exist:

5.  Submit a pull request

1.  Use fully-qualified notation when importing other packages from subpackages,

When you’re starting out with deployments, it’s a very good idea to enforce some automation from the very beginning. Using a CI system is a must-have.


. If you’re building a Docker image, it’s a good idea that you set up a testing procedure for it as well.


Your testing procedures must be a condition of deploying an application. If the tests don’t pass, the person who triggers the deploy has to be notified. It doesn’t really matter who fixes the application or tests or when the fix is made, but until it’s done - no deployments should be possible. 

It’s recommended to move the testing to the server side and test after some code is pushed. Again, if a failing test is detected - it shouldn’t be a cause for stopping work, it should just be a cause of not performing a deployment.

Keep a practice of not assigning blame - when something breaks, just assign who is responsible for a fix. 

Accept the issue, learn from it, and solve it - creating a problem is a learning opportunity.


### II. Dependencies - Explicitly declare and isolate dependencies

As you want to have a repeatable build process, using go get is not advised, but you should look to vendoring to find an alternative.

You can list your current dependencies by issuing gvt list.

$ gvt list

If you don’t want to keep the packages committed to your source repository, you can add this .gitignore rule:

If you commit your complete vendor folder, it guarantees that you will be able to reproduce the build at any later time.

keep in mind that gvt update will update all packages to the latest revision - this might be problematic, if you’re bound to a specific tag or commit.

System dependencies
When building docker images, you use the Dockerfile to declare your dependencies.

When your docker image contains multiple services, you might have to deal with several graceful shutdowns to effectively migrate data, which will introduce operational complexity and risks. 

If you can make a coffee and drink it before the image finishes downloading, that’s a good hint that you should optimize it.

### III. Config - Store config in the environment

III. Config - Store config in the environment

One of the most important 12 Factor Application principles is dictating how your configuration should be declared and passed to your application.

If you’re using a database instance, the location and credentials for that database instance should come from the environment where your application is run. This means that it should be passed via an environment variable, via a command line argument, or even via a configuration file which must be passed as a command line argument.

People defining constants in the code have disabled themselves from changing the environment without rebuilding the whole application. 

it may be added to source control by mistake, causing the same problem.

be managed outside of your application. Imagine a basic requirement that your application can be open sourced or the source code may just be bought by a third party. You don’t want to expose your AWS or other sensitive credentials. It’s always worth to ask yourself: can I publish my application code to the public, without getting burned?


The flags example shows just how easy it is to pass ENV variables and use them inside Go programs. Using docker, you can pass the ENV variables with the -e option, like this:

Best practices
I would recommend that you should define your flags in the func main() block. By doing this, they become unavailable for reading/writing by other functions from the global scope, and you have full control how this configuration is passed to packages and other functions.

This package loads ENV variables from a .env file which should be located in the root of your project. Keep in mind, that this file shouldn’t be committed in your git repository, but should come to your development, staging or production environments from your infrastructure management solution or simply as part of your testing or deployment environments.

To use the package, import github.com/joho/godotenv and add the following line to the beginning of your main function. It should be added before using the namsral/flag package:

1 godotenv.Load()

Viper is a popular configuration library that’s designed with 12 factor applications in mind.

Keep in mind, if no config files are present, you will end up with an error. If you have an empty .env.json you will end up with an error as the contents are not a valid JSON document, you need at least {} to correctly parse JSON.

### IV. Backing services - Treat backing services as attached resources

The main point of why we treat backing services as attached resources is that they may be replaced based on any criteria. If a database is misbehaving, it’s possible to upgrade it or migrate it to a different host.

Data volumes are designed to persist data, independent of the container’s life cycle. Docker therefore never automatically deletes volumes when you remove a container, nor will it “garbage collect” volumes that are no longer referenced by a container.

Data volume
The second and more common option in on premises deployments is to create volume mounts of a host directory to store it’s data. When you stop the container, the data is available as is on the host, where it can be archived or copied to another machine. 

For example: if you have a web server, which stores file in a writeable upload folder - these files are safe to copy without turning off the server.

Redis provides redis-cli, a command line client where you can issue manual commands against it, without resorting to telnet or a custom-built client.


Connecting to and using a MySQL instance is very straightforward.

We don’t need to close the connection as sqlx maintains an internal database connection pool. 

Redis has disk-based storage (“rdb”) which you can snapshot and replicate. 

To have private docker image storage available, you can set up your own registry server, much like you can run MySQL, Redis or anything else.

connecting to it via SSL is the default option.

The great LetsEncrypt CA can be used to generate SSL certificates for any domains you might have.


Logging into the Docker hub is needed if you would like to push your images to your username on Docker hub.

After you have made a successful login, you are able to “push” your images to your Docker Hub account. 

Hosting your own private Docker registry can be a bit confusing, as there is no interface provided currently for it. Basically, what it does is expose a full API to the docker command line client, but it doesn’t give you a GUI or a simple way that would let you - for example - delete an image:tag that you pushed to it. The project kwk/docker-registry-frontend might solve this issue.


Docker hub also provides a CI platform where you can create automated builds for your Docker images (Dockerfile).

Using Google container registry is quite simple

then charge you on used storage and used egress traffic.

The images are referenced with gcr.io/my-project/my-image which gives a more friendly feeling in comparison with Amazon ECR.


### V. Build, release, run - Strictly separate build and run stages

The 12 Factor Application guidelines advocate separating your software developmentprocess into three cycles:build - where you prepare your code into a bundle (executable or docker image),release - where you release this bundle (release to GitHub or push docker image),run - the final stage where you can take any release and run it

The separation between these stages ensures that development takes place in the correct environment for it - if we would be dealing with a scripting language like Node or PHP, there would be an opportunity to modify it in the runtime environment but it would add complications like how to propagate these changes back to the build stage. By creating releases of your software, you also enable rolling back any changes that might disrupt your production environment.

the “build” stage takes care oftranslating source code into machine-code executables, and the “release” stage publishesthese executables for example, on GitHub, or as a docker container on Docker Hub.

amd64 - a 64bit x86 architecture,386 - a 32bit x86 architecture,arm - an ARM architecture (Raspberry PI for example)

When your application is compiled, it uses the libraries that are available in your system. Thisprocess is called dynamic linking. When you transfer your executable from one system toanother (or from one Docker image to another), it will likely have a different set of librariesavailable, causing the application not to work.

When building docker images you should use golang:1.8-alpine for example to build yourimage, and then use the underlying base image alpine:3.5 to run the resulting binaries. 

Another (better) approach is called static linking where the resulting application embeds thelibraries which are needed. This way, the binary is truly portable and you can move it to othercompatible operating systems and platforms, regardless of which libraries they provide.

 If you use the commit ID, it means that you wouldhave a way to rebuild the exactly same Docker image based on the source code in thatcommit.

gets

With head -n 1 we extract the latest commit, and with cut-c1-6 we only keep the first 6 letters of the commit hash.

This sameversion information may also be used for reverting the build, as long as you know the orderof the images, as they were built or deployed to production environments.

This process is calledcontinuous integration.

GitHub will notify Codeship that I made a push via Webhook,Codeship makes a git checkout

 signing up for Codeship is simple, and their free tier gives you 100 builds permonth. 

let me walk you through creating our first project:

They support GitHub, Bitbucket and GitLab repositories out of the box.

 The AES key can be foundunder your project general settings

Save these environment variables into a file named .env:

The GITHUB_TOKEN should be generated on GitHub under “Developer Settings”, “PersonalAccess Tokens”, or by following this link directly. The “repo” permission should be set forthe token as it’s marked on the screenshot.

so we can push ourbuilt docker image when we finish building it. Be sure to add /.env to the .gitignore file.

To encrypt these secrets, you can simply issue:

The release script provides the same git variables but they are used to provide GitHubrelease tags and to tag our produced docker image and push them to GitHub. I

And the final part of the release - we tag our image with the short commit ID, and we push itand the latest image to Docker Hub. 

### VI. Processes - Execute the app as one or more stateless processes

VI. Processes - Execute the app as one or more statelessprocessesThe principle says that the application itself shouldn’t keep a local state - a local cache orin-memory values that should persist after shutdown of your application. An applicationwhich keeps state and has persistence is considered a backing-service.

The 12 Factor App guidelines however envision an application which may be scaled tothousands of instances without much effort. In order for that to work, each application has tobe able to start just with the configuration it receives from the environment. 

The additional intent of this guideline is to improve the reliability of your service in face ofrandom outages.

If individual applications don’t have their own caches or data, it’s morelikely that they can tolerate unexpected crashes or outages without and impact.

All long-lasting or persistent state should come from your environment, or from backingservices.

In the same way - if your application takes a lot of timecalculating some values on startup, it is wasting valuable time - the data should just beretrieved from a backing-service as needed, and your app startup should be as fast aspossible. This saves RAM and CPU resources.

Your app must bedesigned to handle such scenarios

Docker will route traffic from all swarm nodes on exposed portsto the actual nodes that are running the service. 

In case wedidn’t know this, we could run something like this to find out the IP:

The main difference is that this server uses the default machine ID which is createdfrom the network interface the container is running on.

When it comes to running software in a docker service, the traffic is distributed betweenreplicas. As a replica goes away, Docker Swarm makes sure that a new container starts upon one of the hosts available. 

When you’re dealing with keep-alive connections, they will stay open longer and the loadmay be unevenly distributed between your back-end containers. Smarter logic is needed toroute such requests and docker doesn’t provide much towards this end. 

 I’musing the above example to demonstrate a service which keeps absolutely no local state andcan be scaled with next to zero effort.

### VII. Port binding - Export services via port binding

With docker, you are enabled to define your own, user-defined networks. You can createthese networks on single hosts

The none network doesn’t add any network interfaces to your container. This means thatwhatever code that’s running in your container can’t use any network capabilities. Thecontainer can’t connect to the internet, and no services from the container can be exposedto other containers.

As you see, only the “lo” (loopback/localhost) interface is available in the container. Thisnetworking mode (or lack thereof) is usable when you’re considering running untrusted code,where you don’t want to have any access to the internet.

The bridge network is the default network when you’re running docker containers on a singlehost. This is the network that is routed over the docker0 interface on your docker host.

If you run multiple containers on the default network, they do not see each-other via DNS,but you can ping/connect to other containers, if you know their IP.

There’s a key difference between this network and the default bridge network. Thecontainers that will run on the party network can immediately be visible to other containerson the network via internal docker DNS service.

This network is however visible only on a single docker host.

When you run a service on the host network, the container has full access to the hostnetworking stack. This means that the container may control creation, modification and evendeletion of network interfaces. This is very useful in the case of VPN clients, network sniffinglike tcpdump, or providing higher level networking tools and monitoring of your networkinfrastructure.

So, if the application you are running listens on port 3000, but you want to expose port 80,you can pass -p 80:3000. This makes it easier to run applications which aren’t completely12FA compliant and you can’t set their port via environment variables.

to completelyfollow 12FA guidelines - you must read the port on which you listen on from the environment,but you can provide a reasonable default. With docker you have the option to be lazy a bit,and expose the service on different ports as mentioned above.

There are other considerations as well. When running software on Linux, ports below thenumber 1024 are privileged ports. This means that as a non-privileged user (i.e.. non-root),you can run a server only above these ports. 

When it comes to debugging network connectivity there are usually a couple of things that Icheck whenever I am having some issues.

There are many tools available in Linux that aid with debugging network connectivity as wellas program execution. It’s good that you have at least basic understanding of the followingtools:

 I checked the config file on thehost - as I wasn’t connected to the VPN any more, the “search mtk.lan” option wasn’t in theresolv.conf any more. But it was still available inside the docker container.

The option was burned into Docker somewhere, and I don’tknow a way to get rid of it! After some searching, I’ve located a filed issue against Docker,which describes similar behaviour (Issue #8425). The issue is closed but it gave me enoughinfo to fix it for myself:

 When thedaemon started, it picked up the current resolv.conf from the host, and when it started thecontainers - the network connectivity issues have gone away.

It’s good to know the basic networking and system tools so you can inspect softwareoperation. Of course, many times prevention is better - I never set up hosts withoutmonitoring of some kind. There are great software packages that I use and can recommendto set up monitoring and alerting:

If you need to force use of your own internal DNS for whatever reason, docker provides anoption to pass the DNS servers you want to the container.

In order to test that, I need tooverride the DNS resolver so point the public domain to the development server.

The service has another great feature - the management of the entries for it is done with ahosts file. You can literally pass your /etc/hosts to go-dnsmasq and have a DNS serverthat resolves your hosts entries available anywhere. There is also a sweet candy available indnsmasq, which isn’t available in your hosts files: you can create wildcard entries:[插图]

 The best part is,that I can start using a subdomain right away, without having to add it to the hosts file orrestarting the dnsmasq service. I just run dnsmasq once, and then set up a nginx reverseproxy that can handle any subdomain.

 All you need to do is startup a mail container on the same network, or define it in a docker container with --add-host.

If running your own mail server on the host is not an option, it’s very simple to install ssmtpinto any container which you’re running. 

Installing it is simple, it’s relatively small and the most important part, you can set it up withonly a few environment variables:

this GitHub 

 The basic ideais, you should run it from your ENTRYPOINT before you start up your application. This way,you can move your container anywhere, or you can move your smtp server anywhere and allit needs is a container restart. This is Chapter III. at work.

 If you feel like configuring yourown alternative, Amazon SES is a cost-effective option

### VIII. Concurrency - Scale out via the process model

 it is worth to look at this option as away to provide redundancy via the process model, and with this also a graceful upgrade pathwhen deploying new application versions.

By using Docker we can also satisfy basic processmanagement requirements set forth in the 12FA guidelines - respond to crashed processesand handle user initiated restarts and shutdowns.

When it comes to making sure that your application is alive, docker has the built-in switch--restart available, which controls the life of your process after it may unexpectedly exitor if your VPS or server unexpectedly restarts.

no - don’t restart the container when the application exists (the default),on-failure - restart the container only if the application exists with a non-zero exit status,on-failure[:max-retries] - optionally set how many times the application may restart,unless-stopped - if you manually stop the container, it will not restart when docker startsup,always - restart your application when it exits or when docker starts up (like after reboot).

Obviously, the default for any application could be always, unless you have a really goodreason for keeping the application unavailable. I’m an advocate of docker stop && dockerrm which will erase the container completely, so even with an aggressive restart policy it willhave no side-effects 

In special cases, the restart policy protects you from unexpected crashes of your instances.

I’ve had the misfortune to be in just that situation where Redis would crash with an OOM (out of memory) error

I moved Redis to docker, where I can take advantage of this restart policy immediately.

We can even test our restart policy with Redis quite effectively. 

we can see how docker will respond to it. 

Supervisord allows you to restart individual services via a web interface, and there are even attempts to provide a clustering layer above it, to see and manage processes in your deployment.

PM2
Another process manager of note is PM2. When it comes to this process manager, it serves a different purpose than supervisord. Since Node is a single-threaded application it means that it may in the best case utilize a maximum of one CPU core. Scaling beyond 1 CPU core should be done with a process manager.

the obvious is that it can scale your service over many CPUs, the less obvious one is that it can provide zero-downtime restarts (rolling update).

Of course, when developing with Go, the goroutines already enable you to scale your application beyond 1 CPU, so the obvious benefit is mainly to provide a zero-downtime upgrade.

To run our Go application with PM2, we will need to create a process.yml file. This file holds the configuration of our Go application and how it should run.

There are only a few notable options here:
•  exec_mode is ‘fork’, meaning PM2 will run processes with exec (syscall),
•  instances specifies how many processes to run

For example, we can list the status of our application with ./pm2_exec status.

And the startup script

Keep in mind, this is the compressed size, the actual size will be reported using docker images | grep gotwitter:

Sysctl tunable settings
Running a 1000 containers is not a small feat. In fact, in a single host, you’re somewhat limited to running slightly less than 1024 containers because of a variety of reasons. 

When running applications in Linux, system parameters can be tuned with sysctl:
sysctl is a feature of some Unix-like operating systems that reads and modifies the attributes of the system kernel such as its version number, maximum limits, and security settings.

After researching the cause on google, I did find suggestions of which sysctl values I should increase in order to increase ARP caches, the underlying reason for the error:
ARP cache is overflowing. Most possible reason - too much traffic on the network (generated by some application, by some hosts, or by related factors).

A bit of digging around lead me to some sysctl tunable settings which can be used to work around the problem; basically by increasing the thresholds where ARP cache collection occurs.
Issue this on every docker swarm host:

sysctl -w net.ipv4.neigh.default.gc_thresh1=8096

And there are other sysctl tunable settings which might be useful for you:

 1 # Have a larger connection range available
 2 net.ipv4.ip_local_port_range=1024 65000
 3 
 4 # Reuse closed sockets faster
 5 net.ipv4.tcp_tw_reuse=1
 6 net.ipv4.tcp_fin_timeout=15
 7 
 8 # The maximum number of "backlogged sockets".  Default is 128.
 9 net.core.somaxconn=4096
10 net.core.netdev_max_backlog=4096

These sysctl settings deal mainly with how many connections can be open at a given time, which ports are used for the connections, how fast connections are recycled,…

If you want to do a rolling upgrade, it can be as simple as this:

root@swarm3:~# docker service update --image titpetric/gotwitter gotwitter

1.  Swarm shut down [update-parallelism] instances,
2.  Swarm started new [update-parallelism] instances,
3.  Swarm waited for health checks to pass,
4.  Swarm started routing traffic to new instances,
5.  Back to step 1 until there are some old containers left

After some time, when all the health check have passed and all the containers are up and running, you can inspect what happened on a single host with a docker ps -a. I’m shortening the output a bit here.

As you can see, it took 2 minutes to perform the rolling upgrade. The image ID changed, because a newer version of the gotwitter image was available on the docker registry.

This will restart all the existing containers, in order to turn off health checks. And now we’re ready to scale up!

I documented this issue in the Docker GitHub and also provided a work-around for you:

While this still has significant overhead (of one ‘sh’ processes anyway), it should be reasonable enough to reach an active 1000/1000 containers for our service.


just perhaps that your health check is failing.

My first suggestion would be to increase your health check interval to at least 60s. I’m running my health check every 60 seconds, with a 60 second timeout. While this may not give the best response time when a service starts failing, it’s reasonable enough that it didn’t give false-positive reports of a failing service in our case.

### IX. Disposability - Maximize robustness with fast startup and graceful shutdown

IX. Disposability - Maximize robustness with fast startup and graceful shutdown
The twelve-factor appâ€™s processes are disposable, meaning they can be started or stopped at a momentâ€™s notice. This facilitates fast elastic scaling, rapid deployment of code or configuration changes, and robustness of production deploys.

1.  There should be no data loss as there is no data stored by the container,
2.  A new container can be started in the place of the old one

The failures are detected by docker right away, but due to the health checks, it will take a minute before the new containers are marked as healthy.

by actively testing their systems to tolerate failure.


Docker swarm handles rolling upgrades for us, but only recently docker added support for running health checks for the containers you run. You will need at least Docker 1.12 for this functionality.

Writing HEALTHCHECK instructions enables you to inspect the state of your docker container, giving docker additional information if your application is ready for processing requests. 

A curl check should be enough to know if your application is ready to serve requests. With the option -f (short for --fail), curl will produce a non-zero exit code which will tell docker that the container is not yet ready.

1 HEALTHCHECK --interval=60s --timeout=60s CMD curl -f http://localhost:$PORT/api/\
2 health || exit 1

--timeout - this option specifies the timeout where it should be obvious that there’s something wrong with your application if the health check doesn’t complete within this time frame. If the check takes longer than this time, the health check will be considered failed.

--retries=N - this option specifies after how many failed health checks should fail before the container is marked as unhealthy.

This value is passed via environment variables. Of course, a default value should be provided in case there’s no configuration coming from the environment:

1 ENV PORT=3000
This statement in the Dockerfile will declare the environment variable PORT,

Finally, the final || exit 1 ensures that if curl returns an unexpected non-zero exit code, that the exit code returned to docker will be the expected 1, indicating a failed test. This would also be true if for example curl wouldn’t be installed in the system - also triggering a failed test.

### X. Dev/prod parity - Keep development, staging, and production as similar as possible

X. Dev/prod parity - Keep development, staging, and production as similar as possible

This application might need external data which you pass via the environment, the filesystem, or backing services. Depending on which environment you run a container, any of these external sources might change. 

But at some point - even infrastructure needs to be tested, not just software.

At Uber, there are many technology stacks in production, some from people who are no longer with the company. There’s no complete overview of what runs where and who is responsible for it. But to be fair, Uber is not “brownfield” software, the critical path is well tested and understood.


 Since the tests are performed on build of the docker image, problems will be detected before they can have any impact on production environments.


When we’re talking about having development and production be as similar as possible, this is also one aspect of it - we’re not talking about just one container, but how all the containers interact with each-other.

Then, using a single command, you create and start all the services from your configuration. To learn more about all the features of Compose see the list of features.

We can use Docker compose to start up the web server, the database server and your application if needed. 

### XI. Logs - Treat logs as event streams

 Itshould not attempt to write to or manage logfiles. Instead, each running process writes itsevent stream, unbuffered, to stdout. During local development, the developer will view thisstream in the foreground of their terminal to observe the appâ€™s behaviour.

Your application should have a sense of singleresponsibility, and if it has to collect, store and manage logs in addition to it’s primarypurpose, it may be less reliable overall, because a failure in one part of the application mightmean disruption over all parts.

If an error occurs and your application exists - this must be visible in the stdout/stderr eventstreams. These events should be forwarded to a specific service that deals with operations.

You should replace logs5.papertrailapp.com:41062 with the log destination that youcreated. The containers that produce output on stdout will immediately be visible on theDashboard.

 A structured log entry would be something like this:

This JSON payload is something that can be directly read and consumed by applications.The structure of the log entry also makes it easier for applications to extend log entries withadditional fields, without worry of breaking a parser or providing the final entry fields in adifferent order.

I adjusted the example to provide logging to two destinations - the Stdout as required bythis chapter, as well as a backing service with Papertrail. The log package provides severalhandlers, and I used the multi handler to combine logging to text as well as papertrailhandlers. 

 Elastic search

### XII. Admin processes - Run admin/management tasks as one-off processes

The main benefit of separating administration tasks into one-off processes is that they may operate on certain parts of your infrastructure, without requiring the complete application or service environment to be present

These tasks may be any of the following:
1.  Running a database migration,
2.  Console access to your containers,
3.  Running one-time scripts from your app

And in case you forgot to add them - no worries. It’s possible to add software to your running container instance - just keep in mind, that this falls under “making things work”, and not under “best practice”. C

When you modify them as they are running, it can be useful during their lifetime, but as soon as they die you’d have to start again.


This is perhaps far from best practice. I’m pretty sure that people with 10 thousands of running containers never do such a thing in production. But for development purposes (or for a very critical issue which needs diagnostic tools), it’s good to know how to approach the diagnosis of the problem.

3.  When you add, modify or delete files outside of the volumes, an additional filesystem layer is created,

.  Docker uses ‘overlay’ to “merge” the original image layer and the new filesystem layer into a consistent view (the running container filesystem)

After all - if you’re experiencing issues, you don’t want to be locked into the running container; rebuilding it might take at least a few minutes and any issues you might be experiencing might go away after restarting the container.


A note on keeping container size down

Keep in mind that choosing a smaller base image to work with has performance implications as well. When docker starts a container, it makes a copy of the image into a new, unique location on disk. 

Alpine bundles their package manager, apk, which you can use to install things like bash, telnet, tcpdump, jq or other command line tools that may assist you in debugging almost anything you can think of.

Don’t have same build/run environments: Don’t run a full build environment in production. 

Using it would also go against the 12 Factor App guidelines - build and run stages should be separated.


you can use your application image to start a new isolated container, running tests completely independently. This way, the one-off process runs in an identical environment as your application.

A trivial example of a unit test that uses docker to orchestrate additional containers would be similar to this:

The exit code of the unit tests is caught and passed on. In case of non-zero exit codes, your CI solution will stop the build process - and in the case of development, notify the developer of the failing tests directly.