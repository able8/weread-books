## Docker and Kubernetes for Java Developers
> Jaroslaw Krochmalski

### Who this book is for

The reader will learn how Docker and Kubernetes can help with deployment and management of Java applications on clusters, either on their own infrastructure or in the cloud.

### Conventions

Any command-line input or output is written as follows:
docker rm $(docker ps -a -q -f status=exited)

### Introduction to Docker

Next, we will install Docker and learn how to pull a sample, basic Java application image from the remote registry and run it on the local machine.


Docker was created as the internal tool in the platform as a service company, dotCloud. In March 2013, it was released to the public as open source.

Kubernetes is a tool developed by Google for deploying containers across clusters of computers based on best practices learned by them on Borg (Google's homemade container system).

it manages workloads for Docker nodes by keeping container deployments balanced across a cluster. Kubernetes also provides ways for containers to communicate with each other, without the need for opening network ports. 

### The idea behind Docker

The idea behind Docker is to pack an application with all the dependencies it needs into a single, standardized unit for the deployment. Those dependencies can be binaries, libraries, JAR files, configuration files, scripts, and so on. 

With Docker, you can run Java applications without having to install a Java runtime on the host machine. All the problems related to incompatible JDK or JRE, wrong version of the application server, and so on are gone. 

If you need to do some cleanup, you can just destroy the Docker image and it's as though nothing ever happened.

Anywhere means also on more than one machine, and this is where Kubernetes comes in handy; we will shortly get back to it.


### Virtualization and containerization compared

However, nothing comes without a price. Virtual machines contain all the features that an operating system needs to have to be operational: core system libraries, device drivers, and so on. 

Also, the startup time of a containerized Java application is usually very low due to the minimal overhead of the container. You can also roll-out hundreds of application containers in seconds to reduce the time needed for provisioning your software. 

### Benefits from using Docker

As we have said before, the major visible benefit of using Docker will be very fast performance and short provisioning time. 

You can create or destroy containers quickly and easily. Containers share resources such as the operating system's kernel and the needed libraries efficiently with other Docker containers. 

The result is faster deployment, easier migration, and startup times.

Microservices break an app into a large number of small processes. They are the opposite of the monolithic applications, which run all operations as a single process or a set of large processes.

Developers can create the software without worrying about the platform it will later be run on. 

You can quickly boot up identical environments to run the tests. And because the container images are all identical each time, you can distribute the workload and run tests in parallel without a problem. 

By having a lightweight Docker host that needs almost no configuration management, you manage your applications simply by deploying and redeploying containers to the host. And again, because the containers are very lightweight, it takes only seconds.


We have been talking a lot about images and containers, without getting much into the details. Let's do it now and see what Docker images and containers are.


### Docker concepts - images and containers

Let's begin with the concept of Docker images.

### Images

Think of an image as a read-only template which is a base foundation to create a container from. 

you can also pick an already prepared image from the hundreds available on the Internet. 

Images are created using a series of commands, called instructions. Instructions are placed in the Dockerfile. The Dockerfile is just a plain text file, containing an ordered collection of root filesystem changes 

Docker will read the Dockerfile when you start the process of building an image and execute the instructions one by one. The result will be the final image.

The Dockerfile is used to create the image when you run the docker build command. Also, if you publish your image to the Docker Hub, you publish a resulting image with its layers, not a source Dockerfile itself.

### Layers

Docker combines all these layers into a single image entity. 

We will be using the command chaining technique later on, when creating our Java application images.


Layers and images are closely related to each other. 

### Containers

A running instance of an image is called a container. Docker launches them using the Docker images as read-only templates. If you start an image, you have a running container of this image. 

To run a container, we use the docker run   command:


In this phase, Docker will also set up a container's IP address by finding and attaching an available IP address from a pool. 

A container when stopped will retain all settings and filesystem changes (in the top layerthat is writeable).

 If you want to remove a couple of them at once, you can use the list ofcontainers (given by the docker ps command) and a filter:

The docker commit command saves changes you have made to the container in thewritable layer. 

Creating images by altering the top writable layer in the container is useful when debugging and experimenting, but it's usually better to use a Dockerfile to manage your images in a documented and maintainable way.


### Docker registry, repository, and index

Docker utilizes a hierarchical system for storing images, shown in the following screenshot:


The repository, on the other hand, is a collection (namespace) of related images, usually providing different versions of the same application or service. It's a collection of different Docker images with the same name and different tags.

You can tag an image and store multiple versions of that image with different IDs in a single named repository and access different tagged versions of an image with a special syntax such as username/image_name:tag. 

To search an image in the registry, execute the docker search command; for example:

Without specifying the remote registry, Docker will conduct a search on the Docker Hub and output the list of images matching your search criteria:

The official ubuntu repository doesn't use a username, so the namespace part is omitted in this example.

Let's summarize what we have learned so far:

•  Containers are runtime instances of an image. They can be running or stopped. You can have multiple containers of the same image running

•  You can make changes to the filesystem on a container and commit them to make them persisted. Commit always creates a new image

•  Only the filesystem changes can be committed, memory changes will be lost

But Docker is not just a Dockerfile processor and the runtime engine. Let's look at what else is available.

### Additional tools

 No matter which development environment you pick, these add-ons let youdownload and build Docker images, create and start containers, and carry out other relatedtasks straight from your favorite IDE.

As Docker captures more attention, more and more Docker-related tools pop-up almostevery month. 

 There's also the Stats API that will expose live resource usage information (such asCPU, memory, network I/O, and block I/O) for your containers. This API endpoint can beused create tools that show how your containers behave; for example, on a productionsystem.

### Installing Docker

In this section, we will find out how to install Docker on Windows, macOS, and Linuxoperating systems. Next, we will run a sample hello-world image to verify the setup andcheck if everything works fine after the installation process.

Docker installation is quite straightforward, but there are some things you will need tofocus on to make it run smoothly. 

 Let's begin withinstalling on macOS.

### Installing on macOS

 Docker for Mac is a standard, native dmg package you canmount. You will find just a single application inside the package:

Now just move the Docker.app into your Applications folder, and you are all set. Couldn't beeasier. 

The Proxies pane gives you the possibility to setup a proxy, if you need it on your machine.You can opt for using system or manual settings, as you can see in the followingscreenshot:

On the next page, you can edit some Docker daemon settings. This will include addingregistries and registry mirrors. Docker will use them when pulling the image. The Advancedtab contains a text field, in which you can enter the JSON text containing the daemonconfig:

 If you have images thathave not been pushed anywhere yet, having a backup first is always a good idea.

If you do not have Kitematic installed already, Docker will give you a linkwith the installation package:

If you run Kitematic, it will present you the Docker Hub login screen first. You can now Signup to the Docker Hub and then log in providing your username and password:

Let's test our installation by pulling and running an image. Let's search for hello-java-world, as seen on the following screenshot:

That's it for running the container in Kitematic. Let's try to do the same from the shell.Execute the following in the terminal:

That's it, we have a native Docker up and running on our macOS. Let's install it on Linux, aswell.

### Installing on Linux

There are a lot of various Linux distributions out there and the installation process can be alittle bit different for each Linux distribution.

First, we need to allow the apt package manager to use a repository over the HTTPSprotocol. Execute from the shell:

Now we need to make sure the apt installer will use the official Docker repository instead ofthe default Ubuntu repository (which may contain the older version of Docker):

The reason for that is the Dockerdaemon always runs as the root user, and since Docker version 0.5.2, the Docker daemonbinds to a Unix socket instead of a TCP port. By default, that Unix socket is owned by theuser root, and so, by default, you can access it with sudo.

Let's fix it to be able to run theDocker command as a normal user:First, add the Docker group if it doesn't already exist:[插图]Then, add your own user to the Docker group. Change the username to match yourpreferred user:

Now let's log out and log in again, and execute the docker run command one more time,without sudo this time. As you can see, you are now able to work with Docker as a normal,non-root user:

### Installing on Windows

That's it for installing Docker for Windows. It's a pretty painless process. As a last step ofthe installation process, let's check if Docker can be run from the command prompt,

As you can see on the previous screenshot, we have a Hello World message coming fromthe Java application started as a Docker container.

### Summary

it's now time to move on and learn about more advanced Docker features,such as networking and persistent storage.

### Networking and Persistent Storage

 It always needs to communicate with other servers(as a database), or expose itself to others (as a web application running on the applicationserver which needs to accept requests coming from the user or from the otherapplications). 

In this chapter, you are going to learn how to configurenetworking, and expose and map network ports.

We are going to coverKubernetes' specific networking later on. 

we will also focus on data volumes as away to persist the data between container run and stop cycles.

This chapter covers the following topics:Docker network typesNetworking commands

### Networking

To make your container able to communicate with the outside world, whether anotherserver or another Docker container, Docker provides different ways of configuringnetworking. 

### Docker network types

Docker will output the list of available networks containing the unique network identifier, itsname, and a driver which powers it behind the scenes:

### Bridge

This is the default network type in Docker. When the Docker service daemon starts, itconfigures a virtual bridge, named docker0. If you do not specify a network with the dockerrun -net=<NETWORK> option, the Docker daemon will connect the container to the bridgenetwork by default.

 For each container that Docker creates, it allocates a virtual Ethernet device whichwill be attached to the bridge. The virtual Ethernet device is mapped to appear as eth0 inthe container, using Linux namespaces, as you can see in the following diagram:

 From now on, if the newcontainer wants to, for example, connect to the Internet, it will use the bridge; the host'sown IP address. The bridge will automatically forward packets between any other networkinterfaces that are attached to it and also allow containers to communicate with the hostmachine, as well as with the containers on the same host. The bridge network will probablybe the most frequently used one.

### Host

This type of network just puts the container in the host's network stack. 

If you start your container using the -net=host option, then the container will use the hostnetwork. It will be as fast as normal networking: there is no bridge, no translation, nothing.That's why it can be useful when you need to get the best network performance. 

Using the host network type also means that you will need to use port mapping to reachservices inside the container. 

### None

To cut a long story short, the none network does not configure networking at all. There isno driver being used by this network type. It's useful when you don't need your container tohave network access; the -net=none switch to docker run command completely disablesnetworking.

Docker provides a short list of commands to deal with networking. You can run them fromthe shell (Linux or macOS) or the command prompt and PowerShell in Windows. Let's getto know them now.

### Networking commands

The parent command for managing networks in Docker is docker network. You can list thewhole command set using the docker network help command, as you can see in thefollowing screenshot:

The docker network inspect command displays detailedinformation about the network.It's very useful, if you have network issues. We are going tocreate and inspect our network now

### Creating and inspecting a network

Execute the following from the shell or command line:[插图]In response, you will get a lot of detailed information about your network. As you can see inthe screenshot, our newly created network uses the bridge driver, even if we haven'texplicitly asked for it:

As you can see, the container list is empty, and the reason why is that we haven'tconnected any container to this network yet. Let's do it now.

### Connecting a container to the network

There's another option to attach thenetwork to the container, you can inform Docker that you would like the container toconnect to the same network as other containers use. This way, instead of specifying anetwork explicitly, you just instruct Docker that you want two containers run on the samenetwork. To do this, use the container: prefix, as in the following example:

In the previous example, we run the myTomcat image using the bridge network. The nextcommand will run the myPostgreSQL image, using the same network as myTomcat uses.This is a very common scenario; your application will run on the same network as thedatabase and this will allow them to communicate. 

. Each container in the networkcan directly communicate with other containers in the network. Though, the network itselfisolates the containers from external networks, as seen in the following diagram:

If you run your containers in a bridge, isolated network, we need to instruct Docker on howto map the ports of our containers to the host's ports. 

### Exposing ports and mapping ports

For the purpose of checking if containers on the same network can communicate, we willuse another image, busybox. BusyBox is software that provides several stripped-down Unixtools in a single executable file. 

Let's check if they indeed can communicate. Execute the following in the shell window withbusybox running:

To bind a port (or group of ports) from a host to the container, we use the -p flag of thedocker run command, as in the following example:

The Docker image can expose a whole range of ports to other containers using either theEXPOSE instruction in a Dockerfile (the same as EXPOSE 7000-8000, for example) or thedocker run command, for example:

Let's verify if we can access the Tomcat container from outside of Docker. To do this, let'srun Tomcat with mapped ports:

Then, we can simply enter the following address in our favorite web browser:http://localhost:8080.

As a result, we can see Tomcat's default welcome page, served straight from the Dockercontainer running, as you can see in the following screenshot:

Well, the --expose will expose a port at runtime but willnot create any mapping to the host. Exposed ports will be available only to anothercontainer running on the same network, on the same Docker host.

 Note that if you do -p, but there is no EXPOSE in the Dockerfile, Docker will do animplicit EXPOSE. This is because, if a port is open to the public, it is automatically alsoopen to other Docker containers.

To check exactly which host port has been mapped, you can use the docker ps command.This is probably the quickest way of determining the current port mapping. The docker pscommand is used to see the list of running containers. 

In the output, Docker will list all running containers, showing which ports have been mappedin the PORTS column:

Mapping ports is a wonderful feature. It gives you flexible configuration possibilities to openyour containers to the external world.

### Persistent storage

But, this is not very convenient when itcomes to storing and retrieving data. The best option would be to separate the containerlife cycle and your application from the data. Ideally, you would probably want to keep theseseparate, so that the data generated (or being used) by your application is not destroyed ortied to the container life cycle and can thus be reused.

The perfect example would be a web application server: the Docker image contains webserver software, the same as Tomcat for example, with your Java application deployed,configured, and ready to use.

Volumes live outside of the union filesystem and exist as normal directories andfiles on the host filesystem.

There are three main use cases for Docker data volumes:To share data between the host filesystem and the Docker containerTo keep data when a container is removedTo share data with other Docker containers

Let's begin with a list of volume-related commands at our disposal.

### Volume-related commands

The basis of volume-related commands isdocker volume. The commands are as follows:

$ docker volume inspect: Displays detailed information on one or more volumes

docker volume rm: removes one or more volumes$ docker volume prune: removes all unused volumes, which is all volumes that are nolonger mapped into any container

Let's begin with creating a volume.

### Creating a volume

The -v option can be used not only for directories but for a single file as well. This can bevery useful if you want to have configuration files available in your container. 

Executing the previous command will give you the same bash history between your localmachine and a running Ubuntu container. And best of all, if you exit the container, the bashhistory on your own local machine will contain the bash commands you have beenexecuting inside the container.

Mapping a single file from a host allows exposing a configuration of yourapplication.

Apart from creating a volume when starting a container, there is a command to create avolume prior to starting a container. We will use it now.

As the output, Docker will give you the volume identifier, which you can later use to refer tothis volume. It's better to give a volume a meaningful name. To create a standalone, namedvolume, execute the following command:

Note that this time, we do not specify a path on the host. Instead, we instruct Docker touse the named volume we created in the previous step. 

Docker named volumes are an easy way of sharing volumes between containers. They arealso a great alternative to data-only containers that used to be a common practice in theold days of Docker. 

You can use the -v multiple times to mount multiple data volumes.

Another option to share the volume between containers is the -volumes-from switch. If oneof your containers has volumes mounted already, by using this option we can instructDocker to use the volume mapped in some other container, instead of providing the nameof the volume. 

You cannot map a port in a Dockerfile. Dockerfiles are meant to beportable, shareable, and host-independent. 

If you need to specify a host directory when creating a volume, you need tospecify it at runtime.

### Removing a volume

The same as with creating volumes, there are two ways of removing a volume in Docker.Firstly, you can remove a volume by referencing a container's name and executing thedocker rm -v command:

Docker will not warn you, when removing a container without providing the -v option, todelete its volumes. As a result, you will have dangling volumes—volumes that are no longerreferenced by a container. 

If the volume happens to be in use by the container, Docker Engine will not allow you todelete it and will give you a warning message:

Thevolumes we have created so far were using the local filesystem driver (the files were beingstored on the local drive of the host system); you can see the driver name when inspectinga volume using the volume inspect command. 

### Volume drivers

The same as with network driver plugins, volume plugins extend the capabilities of theDocker engine and enable integration with other types of storage. 

IPFS: Open source volume plugin that allows the use of an IPFS filesystem as a volume.IPFS is a very interesting and promising storage system; it makes it possible to distributehigh volumes of data with high efficiency. 

Keywhiz: You can use this driver to make your container talk to a remote Keywhiz server.Keywhiz is a system for managing and distributing secret data, the same as TLScertificates/keys, GPG keys, API tokens, and database credentials.

Instead of putting thisdata in config files or copying files (which is similarly to be leaked or difficult to track),Keywhiz makes managing it easier and more secure: Keywhiz servers in a cluster centrallystore secrets encrypted in a database. Clients use mutually authenticated TLS (mTLS) toretrieve secrets they have access to.

If you need a volume plugin that is not yet available, you can just write your own. Theprocess is very well documented on Docker's GitHub page, together with extensibleexamples.

We can use the network plugins to further extend the networking data exchangeVolumes persist the data, even through container restarts

Data volumes persist even if the container itself is deleted

Volumes allow of sharing data between the host filesystem and the Docker container, orbetween other Docker containers

Containers from the same Docker host see each other automatically on thedefault bridge network.

### Summary

In this chapter, we have learned about Docker networking and storage volume features. Weknow how to differentiate between various network types, how to create a network, andexpose and map network ports.

Working with Microservices, we are going to focus on the software that we aregoing to deploy using Docker and Kubernetes, and later, Java microservices.

### Working with Microservices

By reading this chapter, you will find out why a transition to microservices and clouddevelopment is necessary and why monolithic architecture is not an option anymore. Themicroservices architecture is also where Docker and Kubernetes will be especially useful.

How Docker and Kubernetes fits into the microservices worldWhen to use microservices architecture

### An introduction to microservices

. In other words, each of theservices will have its own responsibilities, independent of others, each one of them willprovide a specific functionality.

These services should be isolated and autonomous.

They usually communicateusing REST exposures or by publishing and subscribing events in the publish/subscribeway.

Take a look at the following diagram presenting the monolithic application and distributedapplication consisting of microservices:

 Let's compare the two approachesand point out their advantages and flaws.

### The monolithic architecture

 It couldprobably also accept and send the JSON content via the REST endpoints. This kind of anapplication is built as a single unit. As you can see, we have a couple of layers here.

Enterprise Applications are built in three parts usually: a client-side user interface(consisting of HTML pages and JavaScript running in a browser), a server-side parthandling the HTTP requests (probably constructed using some spring-like controllers), thenwe have a service layer, which could probably be implemented using EJBs or Springservices. 

The whole application is amonolith, a single logical executable. To make any changes to the system, we must buildand deploy an updated version of the whole server-side application; this kind of applicationis packaged into single WAR or EAR archive, altogether with all the static content such asHTML and JavaScript files. 

 Scaling this kind of application usually requires deploying multiple copies of theexact same application code to multiple machines in a cluster, behind some load balancerperhaps.

Because all the application code runs in the sameprocess on the server, it's often almost impossible to scale individual portions of theapplication. 

They have a long release cycle, we all know the process of release management,permissions, regression testing, and so on. It's almost impossible to create a continuousdelivery flow having a huge, monolith application.

They are difficult to scale; it typically takes a considerable amount of work to put in a newapplication instance in the cluster by the operations team.

You are locked into the specific programming language or technology stack. 

As you can see, monolithic applications are only good for small scale teams and smallprojects. If you need something that has a larger scale and involves many teams, it's betterto look at the alternative. 

. This will speed up the development process and increasetestability. It will also make you application easier to scale. 

### The microservices architecture

The main difference is that the services defined in the monolithicapplication are decomposed into individual services. Best of all, they are deployedseparately from one another on separate hosts.

When creating an application using the microservices architecture, each microservice isresponsible for a single, specific business function and contains only the implementationthat is required to perform exactly that specific business logic.

 The key advantage of microservices is that a single service can bescaled individually, depending on the resource requirements. 

 As the resource requirement grows, scaling is easy both ways,horizontally and vertically. To scale horizontally, you just deploy as many instances as youneed to handle load on a specific component.

For example,if there is a memory leak in one service or some other problem, only this service will beaffected and can then be fixed and upgraded without interfering with the rest of thesystem. 

From a developer's perspective, having your application split into separate pieces deployedindividually gives a huge advantage. A developer skilled in server-side JavaScript candevelop its piece node.js, while the rest of the system will be developed in Java. It's allrelated to the API exposed by each microservice; apart from this API, each microservicedoesn't need to know anything about the rest of the services. This makes the developmentprocess a lot easier. Separate microservices can be developed and tested independently.

As wehave said before, each microservice should be responsible for storing its own data, becauseagain, it should be independent. This leads to another feature of the microservicesarchitecture, a possibility to have a polyglot persistence. Microservices should own theirdata.

While microservices communicate and exchange data with other microservices using RESTendpoints or events, they can store their own data in the form that is best suitable for thejob.

If a document database is better suited for the job, amicroservice can use MongoDB for example, or Neo4j if it's graph as data.

Of course, having its own data can lead to a challengein the microservices architecture, data consistency. 

Let's summarize the benefits of using the microservices architecture from the developmentprocess perspective:

Services can be written using a variety of languages, frameworks, and their versions

Each microservice is relatively small, easier to understand by the developer (which results inless bugs), easy to develop, and testable

The deployment and start up time is fast, which makes developers more productive

Each service can be deployed independently of other services, easier to deploy newversions of services frequently

It is easier to organize the development process; each team owns and is responsible forone or more service and can develop, release, or scale their service independently of all ofthe other teams

You can choose whatever programming language or framework you think is best for thejob. There is no long-term commitment to a technology stack. If needed, the service can berewritten in the new technology stack, and if there are no API changes, this will betransparent for the rest of the system

It is better for continuous delivery as small units are easier to manage, test, and deploy. 

### Maintaining data consistency

Services must be loosely coupled so that they can be developed, deployed, and scaledindependently. They of course, need to communicate, but they are independent of eachother.

On the other hand, in the microservices world, different services need to be independent.This also means that they can have different data storage requirements. For some services,it will be a relational database, others might need a document database such as MongoDB,which is good at storing complex, unstructured data.

So, when building microservices and thus splitting up our database into multiple smallerdatabases, how do we manage these challenges? We have also said that services shouldown their data. That is, every microservice should depend only on its own database. 

You could use apublish/subscribe pattern to dispatch events from one microservice to be processed byothers, as you can see in the following diagram:

The publish/subscribe mechanism you would want to use should provide retry and rollbackfeatures for the event processing. In a publish/subscribe scenario, the service that modifiesor generates the data allows other services to subscribe to events. 

As you can see, there are a lot of challenges, but also a lot of advantages behind using microservices architecture. 

As services are independent of each other, they can be implemented in different programming languages. This means the deployment process of each may vary: it will be totally different for a Java web application and for a node.js application.

### The Docker role

Let's summarize the Docker features that are useful when dealing with microservices:
•  It is easy to scale up and scale down a service, you just change the running container instances count

All containers with our services are started and stopped in exactly the same way, no matter what technology stack they use


•  Containers are fast to build and start.

but there's one problem. Because our microservices are spread out across multiple hosts, it can be difficult to track which hosts are running certain services and also monitor which of them need more resources or, in the worst case, are dead and not functioning properly.

### Kubernetes' role

While Docker provides the lifecycle management of containers, Kubernetes takes it to the next level by providing orchestration and managing clusters of containers. 

your application created using the microservice architecture will contain a couple of separated, independent services

 Of course, you will be able to specify the host manually, but having this automatic feature can make the best of the processing power and resources available.

Kubernetes can monitor the resource usage (CPU and RAM) at the container, pod, and cluster level. The resource usage and performance analysis agent runs on each node, auto-discovers containers on the node, and collects CPU, memory, filesystem, and network usage statistics.


Kubernetes also manages the lifecycle of your container instances. If there are too many of them, some of them will be stopped. If the workload increases, new containers will be started automatically. This feature is called container auto-scaling.

It will automatically change the number of running containers, based on memory, CPU utilization, or other metrics you define for your services, as the number of queries per second, for example.

Kubernetes can monitor the health of your services, it can do it by executing a specified HTTP method (the same as GET for example) for the specified URL and analyzing the HTTP status code given back in response. 

a TCP probe can check if a specified port is open which can also be used to monitor the health of your service.

When you need to update your software, Kubernetes supports rolling updates. This feature allows you to update a deployed, containerized application with minimal downtime. 

The rolling update feature lets you specify the number of old replicas that may be down while they are being updated. 

Deployments can be updated, rolled out, or rolled back. 

### When to use the microservice architecture

Java is an excellent choice when building microservices. You can create a microservice as a single executable JAR, self-contained Spring Boot application, or fully featured web application deployed on an application server such as Wildfly or Tomcat. 

Docker Repository contains a lot of useful images you can use freely as a base for your microservice.

### Creating Java Microservices

It's time to do some hands-on practice; we are going to implement our own microservice. This will be a simple REST service, accepting HTTP methods such as GET and POST to retrieve and update entities.

We will not focus on advanced microservice features such as authentication, security, filters, and so on, as this is outside the scope of this book. 

•  Creating a REST service in Java using Java EE7 annotations
•  Creating a REST service using Spring Boot
•  Running the service and then calling it with different HTTP clients

At the end of the chapter, we will become familiar with some useful tools-

Before we start coding our own microservice, let's explain briefly what REST is.

### Introduction to REST

describes how one system can communicate a state with another. This fits perfectly well into the microservice world. 

Chapter 3, Working with Microservices, the software applications based on themicroservices architecture is a bunch of separated, independent services talking to eachother.

server: A service provider. It exposes services which can be consumed by clients

client: A service consumer. This could be another microservice, application, or just a user'sweb browser running an Angular application, for example

Of course, REST is not the only option formicroservices communication; there are others, such as Google's very good gRPC, forexample, which brings a lot of advantages such as HTTP/2 and protobuff. 

REST calls are most oftenbeing made using the most popular HTTP or HTTPS protocol. In the case of HTTP, thisuniform interface consists of standard HTTP methods such as GET, PUT, POST,and DELETE.

### HTTP methods

GET gives a read access to the resource. Calling GET should not create any side-effects.

the request has no side effects. 

The DELETE operation should not givedifferent results when called repeatedlyPOST will update an existing resource or create a new one

 or server session state. In other words, all states must be managed on the clientside. Each REST service should be stateless. Subsequent requests should not depend onsome data from a previous request being temporarily stored. Messages should be self-descriptive.

### Application entry point

So far so good. 

### REST controller

In a real-world service, you will probably have a lot of controllers with a lot of pathsmapped. When exposing such a service to the world, it's usually a good practice todocument the API of the service.

This API documentation is the service contract. Doing thismanually could be a tedious process. Also, if you make changes, it's good to have the APIdocumentation in sync. There is a tool that can make it a lot easier, Swagger.

### Documenting the API

The Swagger UI is a collection of HTML, JavaScript, and CSS assets that dynamicallygenerate beautiful documentation from a Swagger-compliant API. 

I guess our little service is ready; it's time to bring it to life.

### Running the application

To run the application, execute the following from the command shell (terminal on MacOS or cmd.exe on Windows):

Having the main() method defined is very convenient when debugging your service. Just start a debugging session with BookstoreApplication as the entry point

it's time to make some calls to its exposed endpoints.

### Making calls

The first choice could be cURL, a command-line tool for transferring data using various protocols. Let's look at other options we have.

### Spring RestTemplate

If you need to call a service from another service, you will need a HTTP client. Springprovides the very useful RestTemplate class. 

 Ithandles HTTP connections, leaving application code to provide URLs (with possibletemplate variables) and extracts results. By default, RestTemplate relies on standard JDKfacilities to establish HTTP connections. 

### Postman

You can define request headers, cookies, and body. If your service supports authentication,Postman contains a lot of authentication helpers: it can be basic Auth, digest Auth, andOAuth. The response body can be viewed in one of three views: pretty, raw, and preview.

The pretty mode formats JSON or XML responses so that they are easier to look at andheaders are displayed as key/value pairs in the header tab.

### Paw for Mac

 As you can see in the following screenshot, it alsocontains a powerful editor to compose your requests:

The interesting feature is that Paw can generate client code toexecute your requests. It can generate code for cURL, HTTPie, Objective-C, Python,JavaScript, Ruby, PHP, Java, Go, and many others. 

If you need to quickly start with your new service, there are a couple of tools that maycome in handy. One of them is Initializr.

### Summary

In Chapter 5, Creating Images withJava Applications, we are going to package our bookstore service into a Docker image andrun it using Docker Engine.

### Creating Images with Java Applications

Now that we have a simple, but functional Java microservice based on Spring Bootstrap, wecan go further. Before we deploy it using Kubernetes, let's package it as a Docker image. 

Let's begin with the definition of a Dockerfile, which will be the definition of our container.

### Dockerfile

First, you prepare a text file named Dockerfile, which contains a series of instructions onhow to build the image. 

Next, you execute the docker build command to create a Docker image based on theDockerfile that you have just created. 

Acontext is processed recursively. PATH will include any subdirectories. The URL will includethe repository and its submodules.

### FROM

. It sets the base image for every subsequentinstruction coming next in the file. The syntax for the FROM instruction is straightforward.

openjdk: An official repository containing an open-source implementation of the Javaplatform, Standard Edition. 

### MAINTAINER

 This is a so-called, non-executing command, meaning that it will not make anychanges to the generated image. The syntax, again, is very simple:

### WORKDIR

The WORKDIR instruction adds a working directory for any CMD, RUN, ENTRYPOINT,COPY, and ADD instructions that comes after it in the Dockerfile. 

### ADD

What ADD basically does is copy the files from the source into the container's ownfilesystem at the desired destination. It takes two arguments: the source (<source path orURL>) and a destination (<destination path>):

The source and destination paths can contain wildcards. Those are the same as in aconventional file system: * for any text string, or ? for any single character.

The ADD command is not only about copying files from the local file system, you can use itto get the file from the network. If the source is a URL then the contents of the URL will beautomatically downloaded and placed at the destination. Note that file archives that weredownloaded from the network will not be decompressed.

Note that ADD shouldn't be used if you don't need its special features, such as unpackingarchives, you should use COPY instead.

### COPY

COPY supports only the basic copying of local files into the container. Onthe other hand, ADD gives some more features, such as archive extraction, downloadingfiles through URL, and so on. Docker's best practices say that you should prefer COPY ifyou do not need those additional features of ADD. 

Otherwise, if the source file has not changed, an existing imagelayer is being reused.

For files and directories that do notrequire the ADD feature of archive unpacking or fetching from the URL, you should alwaysuse COPY.

### RUN

 theRUN instruction will execute a command (or commands) in a new layer on top of thecurrent image and then commit the results. 

RUN however, will be executed at build time and no matter what you do at runtime,its effects will be here.

Each RUN instruction creates a new layer in the image.

. There are times when the caching can be dangerous and provideunexpected results. 

. Imagine a case when you use the RUN command for pulling source codefrom the Git repository, by using the git clone as the first step of building the image.

In our example, we should always combine RUN apt-get update with apt-get install in the same RUN statement, which will create just a singlelayer; for example:[插图]

### CMD

CMD ["executable","parameter1","parameter2"]: This is a so called exec form. It's also thepreferred and recommended form. The parameters are JSON array, and they need to beenclosed in square brackets.

The important note is that the exec form does not invoke acommand shell when the container is run. It just runs the executable provided as the firstparameter. 

CMD command parameter1 parameter2: This a shell form of the instruction. This time, theshell (if present in the image) will be processing the provided command. The specifiedbinary will be executed with an invocation of the shell using /bin/sh -c. It means that if youdisplay the container's hostname, for example, using CMD echo $HOSTNAME, you shoulduse the shell form of the instruction.

We have said before that the recommended form of CMD instruction is the exec form.Here's why: everything started through the shell will be started as a subcommand of /bin/sh-c, which does not pass signals. This means that the executable will not be the container'sPID 1, and will not receive Unix signals, so your executable will not receive a SIGTERM fromdocker stop <container>.

When Docker is executing the command, it doesn't check if the shell is availableinside the container. If there is no /bin/sh in the image, the container will fail tostart.

On the other hand, if we change the CMD to the exec form, Docker will be looking for anexecutable named echo, which, of course, will fail, because echo is a shell command.

 Unlike CMD, the RUNinstruction is actually used to build the image, by creating a new layer on top of theprevious one which is committed.RUN is a build-time instruction, the CMD is a runtime instruction.

Let's write the basic Dockerfile using the command we already know and place it in theroot of our project (

 let's quickly runit now to prove it's working. To run the image, execute the following:[插图]After a while, you should see the familiar Spring Boot banner as a sign that our service isrunning from inside the Docker container:

Building an application jar archive using Maven and then copying it using a DockerfileCOPY instruction simply works. 

Thedrawback of building a Java app using the Docker daemon is that the image will contain allof the JDK (including the Java compiler), Maven binaries, and our application source code. Iwould recommend building a single artifact (a JAR or WAR file), testing it thoroughly (usinga release-oriented QA cycle), and deploying the sole artifact (with its dependencies ofcourse) onto the target machine. 

There's a Dockerfile instruction which is kind of related to CMD instruction: theENTRYPOINT. Let's look at it now.

### The ENTRYPOINT

 Let's explain it a bit. The ENTRYPOINT specifies acommand that will always be executed when the container starts. The CMD, on the otherhand, specifies the arguments that will be fed to the ENTRYPOINT. Docker has a defaultENTRYPOINT which is /bin/sh -c but does not have a default CMD. For example, considerthis Docker command:

ENTRYPOINT ["executable", "parameter1", "parameter2"] is the exec form, preferred andrecommended. Exactly the same as the exec form of the CMD instruction, this will notinvoke a command shell. This means that the normal shell processing will not happen. 

ENTRYPOINT [ "echo", "$HOSTNAME" ] will not do variable substitution on the$HOSTNAME variable. If you want shell processing then you need either to use the shellform or execute a shell directly. For example:

ENTRYPOINT command parameter1 parameter2 is a a shell form. Normal shell processingwill occur. This form will also ignore any CMD or docker run command line arguments. Also,your command will not be PID 1, because it will be executed by the shell. As a result, if youthen run docker stop <container>, the container will not exit cleanly, and the stop commandwill be forced to send a SIGKILL after the timeout.

 the ENTRYPOINT instruction will beprocessing arguments from the supplied CMD argument

The CMD instruction, as you will remember from its description, sets the default commandand/or parameters, which can be overwritten from the command line when you run thecontainer. The ENTRYPOINT is different, its command and parameters cannot beoverwritten using the command line. Instead, all command line arguments will be appendedafter the ENTRYPOINT parameters. 

Unlike the CMD parameters, the ENTRYPOINT command and parameters are notignored when a Docker container runs with command-line parameters.

Because the command-line parameter will be appended to the ENTRYPOINT parameters,we can run our ping image with different parameters passed to the ENTRYPOINT. Let's tryit, by running our ping example with different input:

And, last but not least, anENTRYPOINT can be also overridden when starting the container using the --entrypointparameter for the docker run command.

Only the last CMD and ENTRYPOINT in a Dockerfile will be used

The best use for ENTRYPOINT is to set the image's main command, allowing that image tobe run as though it was that command (and then use CMD as the default flags)

 Our basic image runs, but doesn't expose any network ports so no oneand nothing can access the service. Let's continue learning about the remaining Dockerfileinstructions to fix it.

### EXPOSE

The EXPOSE instruction informs Docker that the container listens on the specified networkports at runtime. 

 We already know thatEXPOSE does not make the ports of the container automatically accessible on the host. Todo that, you must use either the -p flag to publish a range of ports or the -P flag to publishall of the exposed ports at once.

Exposed ports will be available for the other containers on this Docker host, and, if you mapthem during runtime, also for the external world. Well, let's try it

### VOLUME

. You can create, modify, anddelete files as you wish, then commit the layer to persist the changes. 

The VOLUME command will mount a directory inside your container and store any filescreated or edited inside that directory on your host's disk outside the container filestructure. 

If you now run your container and save some files in the /var/myVolume, they will beavailable for other containers for sharing.

Basically, VOLUME and -v are almost equal. The difference between VOLUME and -v isthat you can use -v dynamically and mount your host directory on your container whenstarting it by executing docker run. 

The fundamental difference between VOLUME and -v is this: -v will mountexisting files from your operating system inside your Docker containerand VOLUME will create a new, empty volume on your host and mount it insideyour container.

### LABEL

To add the metadata to our image, we use the LABEL instruction. A single label is a key-value pair. If you need to have spaces in the label value, you will need to wrap it in a pair ofquotes. 

 To prevent naming conflicts, Docker recommends using namespaces to labelkeys using reverse domain notation. On the other hand, keys without namespaces (dots) arereserved for command-line use.

To have a multiline value, separate the lines with backslashes; for example:[插图]

Each LABEL instruction creates a new layer. If your image has many labels, usethe multiple form of the single LABEL instruction.

If you want to inspect what labels an image has, use the docker inspect command youalready know about from the previous chapters.

### ENV

ENV <key> <value>, will set a single variable to a value. 

The second one, with an equal sign, is ENV <key>=<value>. This form allows settingmultiple environment variables at once. If you need to provide spaces in the values, you willneed to use quotes. If you need quotes in the values, use backslashes:

Note that you can use ENV to update the PATH environment variable, and then CMDparameters will be aware of that setting. This will result in a cleaner form of CMDparameters in the Dockerfile. 

This will ensure that CMD ["startup.sh"] will work, because it will find the startup.sh file inthe system PATH. You can also use ENV to set the often-modified version numbers so thatupgrades are easier to handle, as seen in the following example:

The ENV values can also be overridden just before thestart of the container, using docker run --env <key>=<value>.

### USER

The USER instruction sets the username or UID to use when running the image. It will affectthe user for any RUN, CMD, and ENTRYPOINT instructions that will come next in theDockerfile.

### ONBUILD

The ONBUILD instruction specifies an additional instruction which will be triggered whensome other image is built by using this image as its base image. 

This is useful if you are building an image which will be used as a base to build otherimages. 

. Basically, all your project's Dockerfile needs to do is reference thebase container containing the ONBUILD instructions:

The ONBUILD instruction adds to the image a trigger instruction to be executed at a latertime, when the image is used as the base for another build. The trigger will be executed inthe context of the child build, as if it had been inserted immediately after the FROMinstruction in the child Dockerfile.

### HEALTHCHECK

The HEALTHCHECK instruction can be used to inform Docker how to test a container tocheck that it is still working. 

A container can have several statuses which can be listed using the docker ps command.These can be created, restarting, running, paused, exited, or dead. 

The exit code of the <command> is being used by Docker to determine if a health checkfailed or succeeded. The values can be 0, meaning the container is healthy and ready foruse and 1 meaning that something is wrong and the container is not working correctly. 

[插图]In the previous example, the command curl -f http://localhost/ping will be executed every 5minutes, for the maximum timeout of 2 seconds. 

 If three consecutive retries fail,the container will get the unhealthy status.

There can only be one HEALTHCHECK instruction in a Dockerfile. If you list morethan one then only the last HEALTHCHECK will take effect.

Now that we have an understanding of Dockerfile instructions, we are ready to prepare ourimages. Let's automate things a bit. We are going to create and run our image usingMaven.

### Creating an image using Maven

Our use case will be using Maven to package the Spring Boot executable JAR, and then have that build artifact copied into the Docker image. Using the Maven plugin for Docker focuses on two major aspects:

•  Building and pushing Docker images which contain build artifacts

•  docker:remove: This is for cleaning up the images and containers


•  docker:logs: This prints out the output of the running containers


•  A <build>configuration specifying how images are built

As you can see, we no longer deliver a Dockerfile. Instead, we just provide the Dockerfile instructions as plugin configuration elements. It's very convenient because we no longer need to hardcode an executable jar name, version, and so on. 

As you can see, the plugin configuration is very flexible, it contains a complete set of equivalents for Dockerfile instructions. 

### Building the image

. You'll encounter errors if you try to run these in two separate steps. While the Docker image is building, you will see the following output in the console:

### Creating and removing volumes

. Indeed, it provides two ways to handle volumes: docker:volume-create and docker:volume-remove. 

### Summary

In this chapter, we looked at how to get started with Docker containers and packaging Java applications. We can do it manually by hand using the docker build command and a Dockerfile or we can use Maven to automate things.

For Java developers, Docker helps isolate our apps in a clean environment. Isolation is important because it reduces the complexity of the software environment we're using.

### Running Containers with Java Applications

Actually, we did run the containers several times, butwithout getting much into details. We built the image manually, using a Dockerfile, and thenissuing a docker build command. We have also been using Maven to automate the buildprocess. 

We've alreadybeen running it for the purpose of checking if it really works. This time, however, we aregoing into some more detail about running the containers from our images. This chapterwill include the following concepts:

Starting and stopping containersContainer running modesMonitoring containers

### Starting and stopping containers

Let's go back a little and begin with the basics: how to run and stop the Docker containermanually, from the shell or the command line.

### Starting

The running container will have its own file system, networkingstack, and isolated process tree separate from the host. 

The syntax of the docker run command is as follows:

Ofcourse, you can execute the docker run command without almost any arguments except theimage name. It will run and take the default options defined in the image.

### Stopping

To stop one or more running Docker containers we use the docker stop command. Thesyntax is simple:

The only option for docker stop is -t(--time) which allows us to specify a time to wait before stopping a container. 10 secondsis the default value, which is supposed to be enough for the container to gracefully stop. 

docker stop: The main process inside the container will first receive a SIGTERM, and after agrace period, a SIGKILL

docker kill: The main process inside the container will be sent SIGKILL (by default) or anysignal specified with option --signal

In other words, docker stop attempts to trigger a graceful shutdown by sending thestandard POSIX signal SIGTERM, whereas docker kill just brutally kills the process and,therefore, shuts down the container.

### Listing the running containers

To list the running containers, simply execute the docker ps command:

To include all containers present on your Docker host, include the -a option:

You can also filter the list using -f option to specify a filter. The filter needs to be providedas a key=value format. Currently available filters include:

status: Filters by status, which can be created, restarting, running, removing, paused, exitedor dead

Consider the following example, which will take all containers present on the Docker hostand filter them out by running status:

### Removing the containers

There's a solution for that: instead of cleaning manually by hand, tell Docker toautomatically clean up the container and remove the file system when the container exits.

You do this by adding the --rm flag, so that the container data is removed automaticallyafter the process has finished.--rm flag will make Docker remove container after it has been shut down.

The preceding command tells Docker to remove the container if it's shut down.

When starting a Docker container, you can decide if you want to run the container in thedefault mode, in the foreground, or in the background, in the so called detached mode.

### Container running modes

Docker has two container running modes, foreground and detached. Let's begin with thedefault one, the foreground mode.

### Foreground

In the foreground mode, the console you are using to execute docker run will be attachedto standard input, output, and error streams. This is the default; Docker will attach STDIN,STDOUT and STDERR streams to your shell console.

The useful docker run options are the -i or --interactive (for keeping STDIN stream open,even if not attached) and -t or -tty (for attaching a pseudo-tty) switches, commonly usedtogether as -it which you will need to use to allocate a pseudo-tty console for the processrunning in the container. 

This way you can see what's going on in the running container in yourshell console and interact with the container, if needed.

### Detached

The container starts up and runs in background, the same as adaemon or a service. Let's try to run our rest-example in the background, executing thefollowing command:[插图]

the Spring Boot application listens on port 8080 for HTTP GET orPOST requests. 

### Attaching to running containers

If the container is running in the background, it would be nice to be able to monitor itsbehavior. Docker provides a set of features for doing that. Let's see how we can monitorrunning containers.

### Monitoring containers

There are some ways of monitoring running Docker containers. 

Access to the logentries is crucial, especially if you have your container running in the detached runtimemode. Let's see what Docker can offer when it comes to a logging mechanism.

### Viewing logs

Most applications output their log entries to the standard stdout stream. 

You can display it by using the docker logs command. 

The docker logs command will output just a few last lines of the log into the console. 

When you are done, hit CTRL + C to stop displaying log files on theconsole. 

where CTRL + C would kill the process running within the container. This time, it will juststop displaying the log file and it's safe.

The log file is permanent and available even after the container stops, as long as its filesystem is still present on disk (until it is removed with the docker rm command). By default,the log entries are stored in a JSON file located in the /var/lib/docker directory.

The default driver is the json-file driver,which just writes out the entries into the JSON file. Each driver can take additionalparameters. 

The max-sizespecifies the maximum file size that can be created; after reaching the specified size,Docker will create a new file. 

Splitting a log into separate files makes iteasier to transfer, archive, and so on. Also, searching through the log file is a lot moreconvenient if the file is smaller.

none: It will just switch off logging completely

syslog: It's a syslog logging driver for Docker. It will write log messages to the systemsyslog

splunk: Provides the writing of log messages to Splunk using Event Http Collector. Splunkcan be used as an enterprise-grade log analyzer. You can read more about it at https://www.splunk.com

fluentd: Writes log messages to fluentd. Fluentd is an open source data collector for aunified logging layer. The main feature of Fluentd is that it separates data sources frombackend systems by providing a unified logging layer in between. It's small, fast and hashundreds of plugins that make a very flexible solution out of it. 

gcplogs: Will send the log entries to Google Cloud logging

To store log entries in the syslog for example, executethe following:

Note that the docker logs command works only for the json-file and journald drivers. Toaccess logs written to another log engine, you will need to use the tool matching the driveryou have chosen. 

To display thecontainer properties, we use the docker inspect command, which is extremely useful.

### Inspecting a container

By default, the docker inspect command will output information about the container orimage in a JSON array format. Because there are many properties, it may not be veryreadable. 

The Go templating engine is quite powerful, so, instead of pipingthe output through grep, which is quick but messy, you can use the template engine tofurther process the result.

 For example, the following will find the names of all containers with anon-zero exit code:

Note that we provide $(docker ps -aq) instead of the container ID or name. As a result, allof the running containers' IDs will be piped to the docker inspect command, which can bequite a handy shortcut. 

If you are inside the bound context, the dollar sign ($) will always get you the root context.We can execute this command:

Sometimes the huge output of the docker inspect command can be quite confusing. Sincethe output comes in JSON format, the jq tool can be used to get an overview of the outputand pick out interesting parts.

The jq tool is available for free at https://stedolan.github.io/jq/. It's a lightweight and flexiblecommand-line JSON processor, such as sed command for the JSON data. For example,let's extract the container IP address from the metadata:

But there's another source of valuable information apart from the metadata. It'sruntime statistics, which we are going to focus on now.

### Statistics

To see the CPU, memory, disk i/o and network i/o statistics for containers, use the dockerstats command. The syntax for the command is as follows:

You can limit the statistics measure to one or more specific containers by specifying a listof container IDs or names separated by a space. By default, if no containers are specified,the command will display statistics for all running containers, as you can see on thefollowing screenshot:

--no-stream: This will disable streaming stats and only pull the first result-a (--all): This will show statistics for all (not only running) containers

The statistics can be used to see if our containers behave well when running. Theinformation can be useful to check if we need some constraints on the resources to beapplied to containers, we are going to cover the runtime constraints in a while in thischapter.

Viewing logs, container metadata and runtime statistics, give you almost infinitepossibilities when monitoring your running containers. Apart from this, we can see what'shappening on your docker host globally. 

### Container events

To observe the events coming to the docker engine in real time, we use the docker eventscommand. If the container has been started, stopped, paused, and so on, the event will bepublished.

 It's a powerful monitoring feature. Docker containers report a huge list ofevents, which you can list with the docker events command. The list includes:

If no filter is provided, all events will be reported. Currentlythe list of possible filters includes:

As a result, you will get a timestamp and the name of the event, together with the ID of thecontainer that has caused an event. The docker events command can take additionaloptions, such as --since and --until, which can be used to specify a timeframe that youwant to get the events from. Monitoring container events is a great tool to see what's goingon the docker host. However, there's more. You can also influence, how your containersbehave in case of a crash, for example. We use container restart policies for that.

### Restart policies

By using the --restart option with the docker run command you can specify a restartpolicy. This tells Docker how to react when a container shuts down.

It's possible to automaticallyrestart crashed containers by specifying a restart policy when starting the container.

### no

The no policy is the default restart policy and simply will not restart a container under anycase. 

### always

 Whenever the Docker service isrestarted, containers using the always policy will also be restarted, it doesn't matterwhether they were executing or not.

With the always restart policy, the Docker daemon will try to restart thecontainer indefinitely.

### on-failure

This is a kind of special restart policy and probably the most often used. By using the on-failure restart policy, you instruct Docker to restart your container whenever it exits with anon-zero exit status and not restart otherwise. 

The main benefit of the on-failuresrestart policy is that, when an application exits with a successful exit code (that meansthere were no errors in the application; it just finished executing), the container will not berestarted. 

If a container is successfully restarted, the delay is reset to the default value of100 milliseconds.

### unless-stopped

 That also depends on the kind of software that willbe running on the container. A database, for example, should probably have the always orunless-stopped policy applied. If your container has some restart policy applied, it will beshown as Restarting or Up status when you list your container using the docker pscommand.

### Updating a restart policy on a running container

Sometimes, there's a need to update the Docker runtime parameters after the container hasalready started, on the fly. 

 if you want to prevent containers fromconsuming too many resources on the Docker host. To set the policy during runtime, wecan use the docker update command. 

the docker update command gives you the option to update the restart policy on arunning container. 

A new restart policy will take effect immediately after you run the docker update commandon a container. On the other hand, if you execute the update command on a container thatis stopped, the policy will be used when you start the container later on. 

If you have more than one container running on the Docker host, and want tospecify a new restart policy on all of them at once, just provide all of their IDs ornames, separated by a space.

 If you want to check therestart policy of a running container use docker inspect with the container ID or name withthe --format argument set for the path of the value:

orchestration tasks. The restart policy is not the only parameter you can change on runningcontainers.

### Runtime constraints on resources

It may be useful to restrict the Docker container usage of resources when running. Dockergives you a many possibilities to set constraints on the memory, CPU usage or disk accessusage. Let's begin with setting the memory constraints.

### Memory

It's worth knowing that, by default, that is, if you use the default settings without anyconstraints, the running container can use all of the host memory. 

 It takesthe usual suffixes k, m, or g for kilobytes, megabytes and gigabytes, respectively.

If you do not set the limit on memory that the container can allocate, this canlead to random issues where a single container can easily make the whole hostsystem unstable and/or unusable. So it's a wise decision to always use thememory constraints on the container.

Memory reservation is not a hard limit feature. There's no guarantee the limit won't beexceeded. 

Apart from setting the memory usage constraint, you can also instruct Docker how theprocessor power should be assigned to containers it's going to run.

### Processors

This is a relative weight and hasnothing to do with the real processor speed. In fact, there is no way to say precisely that acontainer should have the right to use only 2 GHz of the host's processor.

CPU share is just a number, it's not related at all to the CPU speed.

 But if you constrain one container's processor shares to512, it will receive just a half of the CPU time. This does not mean that it can use only halfof the CPU; the proportion will only apply when CPU-intensive processes are running. If theother container (with 1024 shares) is idle, our container will be allowed to use 100%!o(MISSING)f theprocessor time. The real amount of CPU time will differ depending on the number ofcontainers running on the system.

Limiting CPU shares and usage is not the only processor-related constraint we can set onthe container. We can also assign the container's processes to a particular processor orprocessor core. 

To start the container and only allow usage of one processor core, youcan change the --cpuset value to 1:

### Updating constraints on a running container

if you see your containers eating too much of theDocker host system resources and would like to limit this usage. Again, we use the dockerupdate command to do this.

 Of course, you canapply CPU and memory constraints at the same time

 It's important to read logs printed out by Docker, to make sure yourconstraints are in place.

Always take note of the warnings Docker outputs during the container startup orafter updating your constraints on the fly, it may happen that your constraints willnot take action!

### Starting and stopping containers

To start-up the container, execute the following:

. As the output, we will be given the ID of the container, as you can seeon the following screenshot:

The container is now running in the background. To test if it's running, we could issue adocker ps to list all the running containers, or just call the service by executing some HTTPmethods such as GET or POST on the mapped 8080 port. 

 This isconvenient, isn't it? But what if we would like to see the container's output instead ofrunning it in the background? That's also easy; let's stop it first by issuing the followingcommand:

After 10 seconds (as you'll remember, it's a default timeout before stopping the container),Maven will output a statement that the container has been stopped:

The docker:run willalso automatically switch on the showLogs option, so that you can see what is happeningwithin the container. 

 TheDocker containers can be now used during the build, the integration testing, and thecontinuous delivery flow you may have; you name it.

### Summary

In this chapter we have learned how to manage the container's life, start it using differentrun modes (foreground and detached), stop or remove it. 

by limiting the CPUand RAM usage using runtime constraints. 

 If you are using Maven, and as the Java developer youprobably are, you can now configure the Docker Maven plugin to start or stop containersfor you automatically.

We are going automate deployment, scaling, and management of containerized applicationsusing Kubernetes. And this is the moment where the real fun begins.

### Introduction to Kubernetes

Let's begin with answering the question, why do we even need Kubernetes?

### Why do we need Kubernetes?

As you already know, Docker containers provide great flexibility for running Java servicespackaged into small, independent pieces of software. Docker containers make componentsof your application portable--you can move individual services across differentenvironments without needing to worry about the dependencies or the underlying operatingsystem.

Your application consists of multiple,independent microservices. The number of services can, and probably will, grow in time.

Also, if your application starts to experience a higher load, it would be nice to increase thenumber of containers with the same service, just to distribute the load. It doesn't mean youonly need to use your own server infrastructure--your containers can go to the cloud.

Today we have a lot of cloud providers, such as Google or Amazon. 

, but monitoring your server usage, especially with an application orapplications running with a huge number of components, can be tricky. 

On the other hand, if another service experiences higher loads and it'scritical, you will want to move it to a more powerful machine or spin up more instances ofit. Best of all, by using Kubernetes, it can be automated. 

 you can do a rolling update on the fly.

Also, the risk of doing releases is smaller so you can release smallerchanges more often, minimizing the possibility of a huge failure which may happen if yourelease everything in one go.

Deploying applications quickly and predictablyScaling on the fly

Releasing new features seamlessly

### Basic Kubernetes concepts

A cluster is a group of nodes; they can be physical servers or virtual machines that have theKubernetes platform installed. 

### Pods

Containers running in the same Pod share the same common network namespace, disk, andsecurity context. In fact, the communication over localhost is recommended betweencontainers running on the same Pod. Each container can also communicate with any otherPod or service within the cluster.

The idea behind labels is that they can be used to identify objects--labels aremeaningful and relevant to users. 

. Via a label selector, which is the core grouping primitive in Kubernetes, theclient or user can identify an object or a set of objects. A selector, similar to a label, is alsoa key-value expression to identify resources using matching labels. 

Annotations, on the other hand, are a kind of metadata you can attach toPods. They are not intended to be identifying attributes; they are such properties that canbe read by tools of libraries. 

Labels are intended for identifying information about Kubernetes objects such as Pods.Annotations are just metadata attached to an object.

The Pod's definition is a JSON or YAML file called a Pod manifest. 

It's very important to be aware that a Pod's life is fragile. Because the Pods are treated asstateless, independent units, if one of them is unhealthy or is just being replaced with anewer version, the Kubernetes Master doesn't have mercy on it--it just kills it and disposesof it.

 Of course, we could start and stop Pods manually,but it's always better to automate. This brings us to the concept of ReplicaSets.

### ReplicaSets

Scaling: When load increases and becomes too heavy for the number of existing instances,Kubernetes enables you to easily scale up your application, creating additional instances asneeded.

ReplicaSets are used by Deployments. Let'ssee what Deployments are.

### Deployment

The Deployment is responsible for creating and updating instances of your application.

The Deployment allowsfor easy updating of a Replica Set as well as the ability to roll back to a previousdeployment.

The main purpose of Deployments is to do rolling updates and rollbacks. A rolling update isthe process of updating an application to a newer version, in a serial, one-by-one fashion.

By updating one instance at a time, you are able to keep the application up and running. Ifyou were to just update all instances at the same time, your application would likelyexperience downtime.

In addition, performing a rolling update allows you to catch errorsduring the process so that you can roll back before it affects all of your users.

Deployment also allows us to do an easy rollback. To do the rollback, we simply set therevision that we want to roll back to. Kubernetes will scale up the corresponding ReplicaSetand scale down the current one, and this will result in a rollback to a specified revision ofour service.

Using Kubernetes withJava, to roll out an update of our service to the cluster.

### Services

We use label selectors to selectPods with particular labels and apply services or ReplicaSets to them. Other applicationscan find our service through Kubernetes service discovery.

 Services have an integrated load-balancer that will distribute network traffic toall Pods. 

Services are persistent and permanentThey provide discoveryThey offer load balancingThey expose a stable network IP addressThey find Pods to group by usage of labels

Kubernetes supports twoprimary modes of finding a service: environment variables and DNS. Service discovery is theprocess of figuring out how to connect to a service. Kubernetes contains a built-in DNSserver for that purpose: the kube-dns.

### kube-dns

Kubernetes offers a DNS cluster add-on, started automatically each time the cluster isstarted up. 

 It utilizes DNS queries to discover availableservices. It supports forward lookups (A records), service lookups (SRV records), andreverse IP address lookups (PTR records). 

Kubernetes generates an internal DNS entry thatresolves to a service's IP address. Services are assigned a DNS A record for a name in theform service-name.namespace-name.svc.cluster.local. This resolves to the cluster IP of theservice. 

Note that when using the hostNetwork=true option, Kubernetes will use thehost's DNS servers and will not use the cluster's DNS server.

### Namespace

Well, namespaces let you manage different environments within thesame cluster. For example, you can have different test and staging environments in thesame cluster of machines.

 By havingnamespaces available, you can act on different environments in the same cluster withoutworrying about affecting other environments.

Because Kubernetes uses the default namespace, using namespaces is optional, butrecommended.

### Nodes

 It may be a virtual or physical machine,depending on your infrastructure. 

As you can see in the previous diagram, a node in Kubernetes has some processes runninginside, and each is very important. Let's explain their purposes, one by one.

### Kubelet

Kubelet is probably the most important controller in Kubernetes. It's a process thatresponds to the commands coming from the Master node (we are going to explain what theMaster node is in a second). Each node has this process listening. The Master calls it tomanage Pods and their containers. 

It's worth knowing that each Kubelet also has an internal HTTP server which listens forHTTP requests and responds to a simple API call to submit a new manifest.

### Proxy

 The Kubernetes network proxy runs on each node. Without it, we would not beable to access the service.

kube-proxy knows only UDP and TCP, does not understand HTTP, provides loadbalancing, and is just used to reach services.

### Docker

 InKubernetes, the role of the node manager is being performed by one special node: theMaster node.

### The Master node

The Master node does not run any containers--it just handles and manages the cluster.

### etcd

Kubernetes stores all of its cluster state in etcd, a distributed data store with a strongconsistency model. etcd is a distributed, reliable key-value store for the most critical dataof a distributed system, with a focus on being:

Secure: Automatic TLS with optional client cert authenticationFast: Benchmarked for 10,000 writes/secReliable: Properly distributed using Raft

The whole cluster state is stored in aninstance of etcd. This provides a way to store configuration data reliably. Another crucialcomponent running on the Master node is the API server.

### The API server

One of the main components residing on the Master node is the API server. It's soimportant that sometimes, you may find out that the Master node is being referred to asthe API server in general. Technically, it's a process named kube-apiserver which acceptsand responds to HTTP REST requests using JSON. 

 The API server is the central management entity and is the onlyKubernetes component that connects to etcd. All the other components must go throughthe API server to work with the cluster state.

The Master node does not run any containers--it just handles and manages thewhole cluster. The nodes that actually run the containers are the worker nodes.

### The scheduler

 Once theapplication instances are up and running, the Deployment Controller will be continuouslymonitoring those instances. This is kind of a self-healing mechanism--if a node goes downor is deleted, the Deployment Controller replaces it.

### kubectl

 Usingkubectl, you will be interacting with your cluster. Of course, having the API exposed by theMaster node and the API server, we could do it using an HTTP client of our choice, butusing kubectl is a lot faster and more convenient.

kubectl provides a lot of functionalities,such as listing resources, showing detailed information about the resources, prints log,managing cluster, and executing commands on a container in a Pod.

### Dashboard

Kubernetes Dashboard is a nice, clean web-based UI for Kubernetes clusters. Using theDashboard, you can manage and troubleshoot the cluster itself as well as the applicationsrunning in it. 

 For those who prefer to usethe graphical UI, the Dashboard can be a handy tool for deploying containerizedapplications and getting an overview of applications running on your cluster, as well as forcreating or modifying individual resources such as Deployments, Pods, and services. 

 you can scale a Deployment, initiate a rolling update, restart a Pod, or deploy newapplications using a deploy wizard.

### Minikube

Running a cluster seems to be a complicated process that needs a lot of setup. This is notnecessarily the truth. Actually, it's quite easy to have the Kubernetes cluster up and runningon the local machine, for learning, testing, and development purposes.

 It's available for all major platforms, which includes Linux, macOS,and Windows. 

before we start deploying our REST service into the cluster, we are going to run Kubernetes locally.

### Summary

This chapter introduced a lot of new concepts. Let's briefly summarize what we have learned about the Kubernetes architecture.

Kubernetes (k8s) is an open source platform for automating container operations such as deployment, scheduling, and scalability across a cluster of nodes.

•  Automate the deployment and replication of containers


•  Scale up and down containers on the fly

•  Organize containers in groups and provide load balancing between them

•  Easily roll out new versions of application containers

•  Kubernetes consists of:
•  A Cluster: A group of nodes.
•  Nodes: Physical or virtual machines that act as workers. Each node runs the kubelet, proxy, and a Docker engine process.

API server. The API server provides a REST endpoint that can be used to interact with the cluster. The Master also includes the controllers used to create and replicate Pods.

Pods: Scheduled to nodes. Each Pod runs a single container or a group of containers and volumes. Containers in the same Pod share the same network namespace and volumes and can communicate with each other using localhost. Their life is fragile; they will be born and die all the time.

Services find their group of Pods by using label selectors. Because the IP of the single Pod can change, the service provides a permanent IP address for its client to use.


### Using Kubernetes with Java

For learning purposes, we will use the Minikube tool to create a cluster on the local machine. It's easier to learn Kubernetes on a local machine instead of going to the cloud in the first place. 

Because Minikube runs locally, instead of through a cloud provider, certain provider-specific features such as load balancers and persistent volumes, will not work out of the box. 

•  Starting up the local Kubernetes cluster using Minikube
•  Deploying a Java application on a local cluster
•  Interacting with containers: scaling, autoscaling, and viewing cluster events

we will go into some more details, starting with the installation process.

### Installing on Mac

The following sequences of commands will download the minikube binary, set the executable flag and copy it to the /usr/local/bin folder, which will make it available in the macOS shell:
$

### Installing on Linux

The installation process on Linux is identical to the macOS one. The only difference is the executable name. The following command will download the latest Minikube release, set the executable bit, and move it to the /usr/local/bin directory:

### Starting up the local Kubernetes cluster

We're using the local Kubernetes cluster provided by minikube. Start your cluster with:
$ minikube start

When starting Minikube for the first time, you will see it downloading the Minikube ISO, so the process will take a little longer.

The Minikube configuration will be saved in the .minikube folder in your home directory, for example ~/.minikube on Linux or macOS. 

As you can see, the dashboard is empty now. 

Of course, having the cluster running empty is quite useless, so we need a tool to manage it.

### Installing on Mac

To update, use the following command:

### Installing on Linux

The installation process is, again, very similar to the macOS. The following commands will fetch the kubectl binary, give it an executable flag, and then move it to the /usr/local/bin to make it available in the shell:

To verify if your local cluster is up and running and kubectl is properly configured, execute the following command:


In the output, you will be given basic information about the cluster, which includes its IP address, and running Minikube addons (we will get back to addons later in this chapter):

To list the nodes we have running in our cluster, execute the get nodes

Our cluster is up and running; it's time to deploy our service on it.

### Deploying on the Kubernetes cluster

We begin the process of deploying our software on the Kubernetes cluster by defining a service. 

### Creating a service

To create a service, we are going to use the simple .yaml file, with a service manifest. 

The kube-proxy, running in userspace, terminates the connection, establishes a new connection to a backend for the service, and then forwards requests to the backend and responses back to the local process. 

•  NodePort: By specifying a service type of NodePort, we declare to expose the service outside the cluster. 

•  Load balancer: This would create a load balancer on cloud providers which support external load balancers (for example, on Amazon AWS cloud). This feature is not available when using Minikube


This is the default value which will be used if you don't provide another


Having our service.yml file ready, we can create our first Kubernetes service, by executing the following kubectl command:

To see if our service is created properly, we can execute the kubectl get services command:

To see the details of a specific service, we use the describe command.

In the output, we are presented with the most useful service properties, especially the endpoints (our internal container IP and port, just one in this case, because we have one Pod running in the service), service internal port, and proxied NodePort:

Having all of the settings in a .yaml file is very convenient. Sometimes, though, there is a need to create a service in a more dynamic way;

 in some automation flows. In this case, instead of creating a .yaml file first, we can create a service manually, by providing all the parameters and options to the kubectl command itself. 

### Creating a deployment

Before creating a deployment, we need to have our Docker image ready and published to a registry, the same as the Docker Hub for example.

You can change this behavior by specifying a value for the imagePullPolicy property in a deployment descriptor. 

•  IfNotPresent: With this setting, the image will be pulled from the registry only if not present on the local host


•  Never: With this one, kubelet will use only local images

Setting imagePullPolicy with a value IfNotPresent when creating a deployment is useful; otherwise, Minikube will try to download the image before looking for an image on the local host.

It is important that you provide a tag in the image name. Otherwise, Kubernetes will use the latest tag when looking for your image in a repository, the same as Docker does.

To create this deployment on the cluster using kubectl, you will need to execute the following command, which is exactly the same as when creating a service, with a difference in the filename:


You can look at the deployment properties with:
$ kubectl describe deployment res

You can also look at the Pods with:
$ kubectl get pods
The output of get pods command will give you the names of Pods running in the deployment. 

As an alternative to the deployment descriptor in .yaml file, you can create deployments from the command line using kubectl run command with options, as you can see in the following example:

Having the complete URL of the endpoint, we can access the service, using any of the HTTP clients, such as curl, for example:


When the service is running, application logs can often help you understand what is happening inside your cluster. The logs are particularly useful for debugging problems and monitoring cluster activity. Let's see how we can access our container logs.


### Interacting with containers and viewing logs

The easiest and most simple logging method for containerized applications is just to write to the standard output and standard error streams. 

To get the Pod's name, use the kubectl get pods command. After that, you can show logs of the specified Pod:

As you can see in the following screenshot, we will get access to a well-known Spring Boot banner coming from a service running in a Pod:


Similar to Docker (a Pod is running Docker, actually), we can interact with a container by using the kubectl exec command. For example, to get a shell to the running container:

The previous command will attach your shell console into the shell in the running container, where you can interact with it, such as listing the processes, for example, as you can see in the following screenshot:

Pod can run more than one container. In such case, we can use --container or -c command switch to specify a container in the kubectl exec command.

Let's summarize the kubectl commands related to viewing logs and interacting with the Pods in a table:


As you already know, Pods and containers are fragile. They can crash or be killed. You can use kubectl logs to retrieve logs from a previous instantiation of a container with the --previous 

in case the container has crashed. 

Let's say our service is running fine, but for the reasons described in the Chapter 7, Introduction to Kubernetes, such as higher load, for example, you decide to increase the number of containers running

Kubernetes gives you the possibility to increase the number of Pod instances running in each service. This can be done manually or automatically. Let's focus on the manual scaling first.

### Scaling manually

since it knows that it is supposed to be one (defined in the deployment manifest), it will reset it back to one. 

You need to scale your deployment instead of the replica set directly.

$ kubectl scale deployment rest-example --replicas=3
After a short while, in order to check, execute the following commands, you will see that now three Pods are running in the deployment:

Scaling can be done automatically by Kubernetes, if, for example, the service load increases.

### Autoscaling

The Kubernetes controller periodically adjusts the number of Pod replicas in a deployment to match the observed average CPU utilization to the target you specified.

The Horizontal Auto Scaler is just another type of resource in Kubernetes, so we can create it as any other resource, using the kubectl commands:
•  kubectl get hpa: List autoscalers

Viewing cluster events can be helpful when monitoring what exactly is being performed on our cluster.

### Viewing cluster events

To view cluster events, type the following command:
$ kubectl get events
It will present a huge table, with all the events registered on the cluster:

It can be very handy to see the picture of the whole cluster.

### Using the Kubernetes dashboard

It allows users to manage applications running in the cluster and troubleshoot them, as well as manage the cluster itself. We can also edit the manifest files of deployment, services, or Pods. The changes will be picked up immediately by Kubernetes, so it gives us the capability to scale down or up the deployment, for example.


If you click on its name, you will be taken to the deployment details page, which will show the same information you could get with the kubectl describe deployment command, with a nice UI:


The dashboard is not only read-only utility. Each resource has a handy menu which you can use to delete it or to edit its manifest:

If you pick the view/edit YAML menu option, you will be able to edit the manifest with a handy editor:


Note that if you change a value, for example the number of replicas, and click Update, the change will be sent to the Kubernetes and executed. This way you can also, for example, scale your deployment.


As deployment has created a ReplicaSet automatically, the ReplicaSet will also be visible in the dashboard:

The same applies to services. If you browse to the Services menu, it will present a list of all services created on a cluster:


Clicking on the name of service will take you to the details page:

On the details screen, all important information is listed. This includes label selector, that will be used to find Pods, port type, cluster IP, internal endpoints, and of course the list of Pods running inside the service.

By clicking Pod's name, you can see details of a running Pod, including its log output, as you can see in the following screenshot:


### Minikube addons

We can list the available addons by executing the following command:

The output of the previous command will list the available addons with their current status, for example:

### Cleaning up

If you finish playing with your deployment and services or would like to start from the beginning, you can do some cluster cleaning by removing the deployment or services:

$ kubectl delete service,deployment rest-example

The kubectl delete supports label selectors and namespaces.

If you would like to delete the current minikube cluster, you can issue the following command to do it:
$ minikube delete

### Summary

As you can see, the Minikube is an easy way to try out Kubernetes and use it for local development. 

If you get to know Kubernetes by playing with it locally, you will be able to deploy your applications in the real cloud without any issues.

•  Next, put the code into Docker image. You can do it by hand by creating a Dockerfile or you can use Docker Maven plugin to automate this


•  Create Kubernetes metadata, such as deployment manifest and service manifest

•  Apply the metadata by rolling out the deployment and creating the service

•  Scale your applications to your needs

•  Manage your cluster either from the command line or from the dashboard

Having the API, you are not limited only to existing tools to deploy your software to Kubernetes. Things can get more interesting.

### Working with the Kubernetes API

The API server's main purpose is to validate and process data of cluster resources, such as Pods, services, or deployments. 

If you want to scale up or down your application, this will be done by modifying the deployment resource. Kubernetes will pick up the change on the fly and apply it to the resource. 

In fact, you can see what REST calls are being made by the kubectl command if you run it with a higher level of verbosity, with the --v=6 or --v=9 option, 

Well, you can create a REST call in every programming or scripting 

•  Authentication (determining who is who)
•  Authorization (determining who can do what)

•  Using the API by making some example calls
•  OpenAPI Swagger documentation

Let's gets started with an API overview.


### API versioning

To deal with those changes and to not break compatibility with existing clients over an extended period of time, Kubernetes supports multiple API versions, 

### Alpha

The alpha version level is disabled by default, as with the other software, an alpha version should be considered as buggy and not production ready.

 You should not use the alpha version, unless you are very eager to test new features or do some experiments.

### Beta

code is tested (it still may have some bugs, as it is still not the stable release)

If there is a breaking, not backward compatible change in the API, Kubernetes team will provide a guide on how to migrate. Using beta on a production environment is not the best idea, but you can safely use beta on a non-business critical cluster.

### Stable

The stable level of the API is a tested, production-ready software. 

Kubernetes API utilizes a concept of API groups. API groups have been introduced to make it easier to extend the Kubernetes API in the future.

. Currently, there are several API groups in use: core, batch, and extensions. 

By using the API, you can do almost anything with your cluster, as you would normally do using the kubectl command. This can be dangerous; 

Kubernetes supports authentication (determining who you are) and authorization (what you can do). The basic flow of calling the API service is presented in the following diagram:

### Authentication

By default, the Kubernetes API server serves HTTP requests on two ports:


•  Localhost, unsecured port: By default, the IP address is localhost and a port number is 8080. There is no TLS communication, all requests on this port bypasses authentication and authorization plugins. This is intended for testing and bootstrap, and for other components of the master node. 

•  Secure port: The default port number is 6443 (it can be changed with the `--secure-port switch), usually it's 443 on Cloud providers. It uses TLS communication. A certificate can be set with a --tls-cert-file switch. A private SSL key can be provided with a --tls-private-key-file switch.

By having your API clients verify the TLS certificate presented by the api-server, they can verify that the connection is both encrypted and not susceptible to man-in-the-middle attacks.

•  With minikube, to access the API server directly, you'll need to use the custom SSL certs that have been generated by minikube. The client certificate and key are typically stored in ~/.minikube/

You'll have to load them into your HTTP'S client when you make HTTP requests. If you're using curl use the--cert and the --key options to use the cert and key file.

If you want to send requests to the Kubernetes API from a different domain, you will need to enable cors on api-server. You do that by adding a --cors-allowed-origins=["http://*"] argument to kube-apiserver configuration, typically in the /etc/default/kube-apiserver file and restart kube-apiserver.

Note that Kubernetes cluster does not manage users by itself. Instead, users are assumed to be managed by an outside, independent service. There is no resource in Kubernetes cluster that represents normal user accounts

Kubernetes does not manage user accounts by itself.

The Kubernetes API supports multiple forms of authentication: HTTP basic auth, bearer token, and client certificates. They are called authentication strategies. 

### HTTP basic auth

To use this authentication strategy, you will need to start the api-server with the --basic-auth-file=<path_to_auth_file> switch. It should be a csv file with the following entry for each user:
password, user name, userid

If there is more than one group for the user, the whole column contents must be enclosed in double quotes, for example:

If the api-server utilizes the basic auth strategy, it will expect all REST calls to be made with the Authorization header containing username and password encoded in BASE64 (similar to ordinary basic auth protected web calls), for example:

To generate the authorization header value, you can use the following command in the shell, it will generate the value for user having password secret:

Note that any changes to the basic auth file will require a restart of the api-server to pick up the changes.

. For example, once you launch your container cluster on Google Container Engine, you will have a master running the api-server on a VM in your GCP project.

### Client certificates

In order to use this scheme, the api-server needs to be started with the following switch:

The CA_CERTIFICATE_FILE must contain one or more certificates authorities that can be used to validate client certificates presented to the api-server. 

For example, using the openssl command-line tool to generate a certificate signing request:

This would create a certificate signing request for the username user, belonging to two groups, group1 and group2.

### OpenID

It allows clients to verify the identity of the end-user based on the authentication performed by an authorization server

The main difference with OAuth2 is the additional field returned with the access token called an id_token. This token is a JSON Web Token (JWT) with well-known

To use the OpenID authentication, you will need to log in to your identity provider, which will provide you with an id_token (and also standard OAuth 2.0 access_token and a refresh_token).


Since all of the data needed to do the authentication is contained within the id_token, Kubernetes does not need to make an additional call to the identity provider. This is very important from the scalability purposes, every request is stateless.

•  The API server will validate the JWT signature by checking against the certificate named in the configuration

•  The API server will check to make sure the id_token hasn't expired
•  The API server will make sure the user is authorized, and returns a response to kubectl if so

### Authorization

The next step after the successful authentication is to check what operations are allowed for the authenticated user. Kubernetes supports four types of authorization policy schemes as of today. 

The default authorization mode is AlwaysAllow, which allows all requests.

The following authorization schemes are supported:
•  Attribute-based control
•  Role-based control
•  Webhook

Let's describe them, one by one, 

### Role-based access control (RBAC)

The Role-Based Access Control (RBAC), policy implementation is deeply integrated into Kubernetes. In fact, Kubernetes uses it internally for the system components, to grant the permissions necessary for them to function. 

This mode allows you to create and store policies using the Kubernetes API. In the RBAC API, a set of permission is represented by the concept of role. 

 A ClusterRole can define the same all permissions a Role can define, but also some cluster-related permission, such as managing cluster nodes or modifying resources across all available namespaces. Note that once RBAC is enabled, every aspect of the API is disallowed access.

A Role and ClusterRole defines the set of permissions, but does not assign them to users or groups directly. There is another resource for that in Kubernetes API, which is RoleBinding or ClusterRoleBinding. They bind Role or ClusterRole to the specific subject, which can be user, group, or service user. 

### AlwaysDeny

AlwaysDeny
This policy denies all requests. 

This can be useful if you are doing some testing or would like to block incoming requests without actually stopping the api-server.

### AlwaysAllow

What role does the admission control play? Let's find out.


### Using the API

To see REST requests being executed by kubectl, run it with a higher level of verbosity, for example with a --v=6 or --v=9 option.


Before we start making actual REST calls, let's briefly see what API operations are possible.

### API operations

•  Update: The update operation can be either Replace or Patch. A Replace will simply replace the whole resource object (a Pod, for example) with the provided spec. A Patch, on the other hand, will apply a change only to a specific field.


Executing List will retrieve all resource objects of a specific type within a namespace. You can use the selector query.

This includes Rollback, which rollbacks a Pod template to a previous version or read /write scale, which reads or updates the number of replicas for the given resource.

### Example calls

The kubectl needs to be configured first, to be able to communicate with your cluster.

the kubectl command will be automatically configured. To start a proxy to the api-server, execute the following command:

 kubectl proxy --port=8080
While the proxy session is running, any request sent to localhost:8000 will be forwarded to the Kubernetes API server. To check if our api-server is running, let's ask for the API version it supports:

It seems to be running fine; let's continue and make some use of the exposed API, starting, the same as previously, by creating a service.


### Creating a service using the API

When using curl with larger payloads, it's more convenient to have the payload in the external file and not type it in the command-line.

Having our JSON file ready, the next step is to create the service resource in our cluster by invoking the following command:

### Creating a deployment using the API

Creating a deployment is very similar to creating a service, it's creating another type of Kubernetes resource, after all.

Let's save the file using the deployment.json filename. Again, all we need to do now is to post this file to the api-server. This process is very similar to the creation of the service, it will be just a POST to the different endpoint with a different payload. To create a deployment from the shell using curl, execute the following command:

After executing those two REST HTTP requests, we should have our service and deployment created in the cluster. 

, because of the deployment manifest contains the number of replicas with the value 1, one Pod will be created as well. Let's check if it's true, by executing the following commands:


We have said before that we can observe what HTTP requests are being executed by the kubectl tool. Let's verify that.

The output should be similar to the following:


As the output, you will be given the JSON response containing detailed 

The API is working, as you can see in the following screenshot:


### Deleting a service and deployment

If you decide it's time to do some clean up, you may delete the service and the deployment by executing the HTTP DELETE request, for example:


### Swagger docs

Note that the SwaggerUI is not enabled by default if you are running the local cluster using Minikube. You will need to enable it during the cluster startup, using the following command:

You will be presented with a list of available API commands, grouped into API groups:

Expanding each API section will give you all the available endpoints with the description of each operation. The SwaggerUI is a great tool to explore an API in a clear and readable form.


### Summary

Using the API you can create and retrieve cluster resources not only from the command-line using kubectl, but also from your own application, build scripts, or continuous delivery pipelines.

### Deploying Java on Kubernetes in the Cloud

However, if you decide to run your clustered software in a production, the cloud is one of the best solutions.

Configuring AWS and running Kubernetes on it is not the easiest and most straightforward process from the start but, following this chapter will give you an overview of the process, you will be able to run your own cloud cluster quickly and deploy your own or third-party Docker images on it.

•  The benefits of using cloud, Docker, and Kubernetes
•  Installing the needed tools
•  Configuring AWS
•  Deploying the cluster

### Benefits of using the cloud, Docker, and Kubernetes

Having an application deployed on a Kubernetes cluster has its advantages.

 What's the difference between having your own infrastructure and using the cloud? 

First, it can be a significant cost reduction. For small services or applications, which could be shut down when not in use, the price of deploying applications in the cloud can be lower, due to lower hardware costs, there will be more effective usage of physical resources. You will not have to pay for the nodes that do not use the computing power or network bandwidth.

Cloud providers update their software stack often; you can benefit from this by having the latest and greatest versions of the operating system software.

When it comes to the computing power or network bandwidth, large cloud providers such as, Amazon or Google cannot be easily beaten. Their cloud infrastructure is huge. Since they provide services to many different clients, they buy large, high-performance systems that offer performance levels much higher than a small company can afford to run internally. 

If your application needs to handle a lot of requests, sometimes having it deployed in the cloud can be the only option.


No single accident such as power outage, fire, or an earthquake, can stop your application from running.

even reducing the chance of complete failure to zero.

Let's move our software to the cloud. To do this, we need to create a toolset first, by installing the required software.

### Installing the tools

Spinning up a cluster is quite a complicated process; 

Doing everything manually is possible, but can be time consuming and error prone. Luckily, we have tools that can automate most of the things for us, this will be the AWS command-line client (awscli) and kops, Kubernetes operations, production Grade K8s installation, upgrades, and management. 

let's focus on Python installation first.

### Python and PIP

To run the AWS command-line tools (awscli), we will need python3 present on our machine.

It may be present already, you can verify that using the command:
$ python3 --version

If the output is command not found, the fastest way of installing it will be using the package manager you have on your system, such as apt on Debian/Ubuntu, yum on Fedora, or Homebrew on macOS.

If you work on macOS and do not have Homebrew installed, I highly recommend doing so; it's a wonderful tool that gives you the possibility to easily install thousands of packages together with all the needed dependencies. Homebrew is available freely at https://brew.sh/. To install it, execute the following:

From now on, you should have the brew command available in your macOS terminal.

The process of installing Python depends on the speed of your machine and internet connection, but it should not take long. Once Python is installed, we will need another tool, which is pip. pip is the recommended tool for installing Python packages. 

An alternative way of installing pip is using the installation script. In this case, the process is exactly the same for Linux and macOS.

After a while, we need to run the installation script by executing the following:

After a while, pip should be available for you in the terminal shell. To verify if it's working, execute the following command:


Now that we have Python and pip installed and working properly, it's time to move on to more interesting things, installing Amazon AWS command-line utilities.

### AWS command-line tools

The awscli is built on top of the AWS SDK for Python, which provides commands for interacting with AWS services. 

we will do it in a while

This is straightforward; you will need to edit the PATH variable in one of those files, depending on the shell you use:

you can verify if the aws command is available, by executing the following command:

As you can see in the output, this will give you a detailed aws command-line tools version also with the Python version it's running on:


The awscli is ready to use, but we have one more tool to add to our tool setup. It will be Kubernetes kops.

### Kops

It's a command-line utility that helps you create, destroy, upgrade, and maintain highly available Kubernetes clusters on AWS. AWS is officially supported by the tool. 

To install on either macOS or Linux, you will just need to download the binary, change the permission to executable and you are done. 

Note that you must have kubectl (https://kubernetes.io/docs/tasks/tools/install-kubectl/) installed in order for kops to work. If you use the package manager, the dependency to kubectl will be probably defined in the kops package, so the kubernetes-cli will be installed first.

The last tool is the jq. Although not mandatory, it's very useful when dealing with JSON data. All the AWS, Kubernetes, and kops commands will post and receive JSON objects, so having a tool for parsing JSON comes in handy, I highly recommend installing jq.

### jq

 It works like sed for JSON data; you can use it to filter, parse, and transform structured data with the same ease that sed, awk, or grep let you do with raw text.

Assuming we have all the tools installed before we start using kops, we will need to configure our AWS account first. 

creating the user for running kops.

### Configuring Amazon AWS

The configuration of AWS before setting up a Kubernetes cluster goes down to creating a user, basically.

### Creating an administrative user

After logging in, go to the IAM page of the Security, Identity, and Compliance section, then switch to the Users page, then click on the Add user button.


Do not close the page, we will need both in a short while to authenticate using awscli:


Running kops using the admin user is probably not the best idea, so let's create a separate user for that. This time, however, we will do it from the command-line. It will be a lot easier in comparison to UI clicking on the Web Console. 

### Creating a user for kops

The kops user will need to have the following permissions in AWS to function properly:

First, we are going to create a group named kops and give the needed permissions to the group. Execute the following list of commands to create a group and assign permissions:

If you are curious you can now login into the web AWS console. You will see that our kops user has all the permissions we need:


All we need to do now is to authenticate using the aws configure command, providing the AccessKeyId and SecretAccessKey we got in the response. Execute the following:

### Creating the cluster

•  DNS configured. This means we will need a Route 53 hosted zone in the same AWS account. Amazon Route 53 is a highly available and scalable cloud Domain Name System (DNS) web service. 

### Root domain on AWS hosted domain

 you have your domain bought and hosted on AWS, you will probably have the Route 53 configured for you automatically already. 

### Checking the zones' availability

To list the zones available for the specific region

As you can see on the following screenshot, AWS will give you the list of zones in the response:

### Creating the storage

Our cluster needs to store its state somewhere. Kops uses Amazon S3 buckets for that purpose. 

### Creating a cluster

Let's first export our cluster name to the environment variable. This will be useful, because we are going to refer to the cluster's name often. Execute the following command to export the cluster name:

If you would like to make your cluster private (it's public by default) you will need to consider using these options additionally:


If you are satisfied with your cluster template, it's time to spin it up to create real cloud-based resources, such as networks and EC2 instances.

### Starting up clusters

If all is looking correct, execute the update command with the --yes switch:


Your cluster is starting and should be ready in a few minutes. If you now log in into the WAS Management Console, you will see your EC2 instances starting up,

You can also check the whole cluster state, issuing the following command:
$ kops validate cluster
The output will contain information about the number and status of the cluster's nodes, including the master node:


### Updating a cluster

The following will start the update or recreation process of the cluster's infrastructure:


Our fresh cluster is running, but it's empty. Let's deploy something.


### Installing the dashboard

Having the cluster running, it would be nice to have a dashboard deployed, to see the status of your services, deployments, pods and so on.

This is a straightforward process. 

The next thing would be to proxy the network traffic, using the following kubectl proxy command we already know:
$ kubectl proxy
That's it! After a while the dashboard will be deployed and we will be able to access it using the localhost address:


From now on, you can use the kubectl and the dashboard to manage your cluster as we did before in the Chapter 9, W

If you decide to remove the cluster, execute the following command:


If the cluster is already created on Amazon, the process of deleting it will take longer, as all EC2 instances for master and worker nodes needs to be shutdown first.

### Summary

we have set up a cluster in the real cloud

 It can be a test or a production-grade cluster; kops will make the creation and management of it a breeze.

### More Resources

We are at the end of our Docker and Kubernetes journey.

Docker becomes more and more popular and a lot of people use it during the development or production deployments. 

. It's mature enough to be run on production and I hope you will use it too to deploy and run your Java applications with success.

, I will present the most useful if you want to further extend your Docker and Kubernetes knowledge.

### Awesome Docker

 Apart from this list, it's really hard to find some more that could be useful.


### Blogs

You will find a lot of useful stuff here, related to Java development and Docker. He also authored a great Docker tutorial, available on GitHub: https://github.com/arun-gupta/docker-tutorial.

Next comes the official Docker blog, available at https://blog.docker.com. You will not find many tutorials on how to use Docker, but there will be announcements about new releases and their features, more advanced Docker usage tips, and community news such as Docker events.

The Red Hat developer program, under the category containers, available at https://developers.redhat.com/blog/category/containers/ also contains a lot of useful articles around Docker and container technology in general.

### Interactive tutorials

It's interactive; all you need is a modern browser, you do not even need to install Docker on your local machine. It's very complete and fun to learn with

### Awesome Kubernetes

Similar to its Docker counterpart, the awesome Kubernetes list, available at GitHub https://github.com/ramitsurana/awesome-kubernetes contains a lot of useful resources regarding Kubernetes. You will find a lot here; staring from the introduction to Kubernetes, through the list of useful tools and developer platforms, up to the enterprise Kubernetes products.

### Tutorials

The official Kubernetes site contains a lot of interesting tutorials, starting from the basics and going through the whole Kubernetes feature lists. 

### Extensions

If you need, for example, to integrate some cert manager into your architecture, you will probably find a proper extension there.


### Tools

Apart from useful articles and tutorials, there are also a couple of useful tools or platforms that make using Kubernetes more enjoyable. Let's briefly present them now.


### Rancher

Rancher, available at http://rancher.com, is a platform that deserves a separate section in our book. It's open source software that makes it easy to deploy and manage Docker containers and Kubernetes in production on any infrastructure. You can easily deploy and run containers in production on any infrastructure with the most complete container management platform.

### Kompose

Kompose (https://github.com/kubernetes/kompose) is a tool to help move Compose configuration files into Kubernetes.

If you are a Kompose user, you can use it to move your multi-containers configuration straight into Kubernetes setup by translating a Docker Compose file into Kubernetes objects. Note that the transformation of the Docker Compose format to Kubernetes resources manifest may not be exactly precise, but it helps tremendously when first deploying an application on Kubernetes.


### Kubetop

Kubetop, again available on GitHub https://github.com/LeastAuthority/kubetop, is the same as the top command for Kubernetes cluster. It's extremely useful; it lists all your cluster's running nodes, all pods on them and all containers in those pods. The tool gives you information about the CPU and memory utilization for each node

 If you need to know quickly what's consuming the most resources on your cluster, the quick command-line tool is a very handy option.

### Kube-applier

The kube-applier runs itself as a Pod in your cluster and continuously watches the Git repository to ensure that the cluster objects are up to date with their associated spec files (JSON or YAML) in the repository. The tool also contains a status page and provides metrics for monitoring. I find it extremely useful in the daily development, where your deployment, services, or pod definition change often.

 It will be amazing to see how your Java application scales itself and becomes fail proof. Docker and Kubernetes enable it and you now have the knowledge to use it. 

I hope it will also change your development and release flow for the better.