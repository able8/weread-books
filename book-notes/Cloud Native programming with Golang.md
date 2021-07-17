## Cloud Native programming with Golang
> Mina Andrawos

### Cloud Native programming with Golang

Cloud Native programming with Golang
 
Copyright © 2017 Packt Publishing


### About the Authors

Besides software development, he has working experience of scrum mastering, sales engineering, and software product management.

He works as a software architect, specializing in building distributed applications using web technologies and Microservice Architectures. Besides programming in Go, PHP, Python, and Node.js, he also builds infrastructures using configuration management tools such as SaltStack and container technologies such as Docker and Kubernetes.

### About the Reviewer

Jelmer Snoeck is a software engineer with a focus on performance, reliability, and scaling. He's very passionate about open source and maintains several open source projects. 

He's taken a special interest in containers and Kubernetes, and is currently working on several projects to help with the deployment flow for these tools. Jelmer understands how to operate and scale distributed systems and is excited to share his experience with the world.

### Preface

Cloud computing and microservices are two very important concepts in modern software architecture. They represent key skills that ambitious software engineers need to acquire in order to design and build software applications capable of performing and scaling.

Go is a modern cross-platform programming language that is very powerful yet simple; it is an excellent choice for microservices and cloud applications. Go is gaining increasing popularity and becoming a very attractive skill.

The book will take you on a journey into the world of microservices and cloud computing with the help of Go.

as well as practical concepts regarding how to scale, distribute, and deploy those applications.

After completing this book, you will have learned how to write effective production-grade microservices that are deployable to the cloud, practically understand the world of Amazon Web Services, and know how to build non-trivial Go applications.

### What this book covers

Chapter 2, Building Microservices Using REST APIs, discusses how to build modern microservices with the Go language. We will cover important and non-trivial topics. By the end of this chapter, you will have enough knowledge to build microservices that can expose RESTFul APIs, support persistence, and can effectively communicate with other services.

Chapter 3, Securing Microservices, shows you how to secure your microservices. You will get to learn about how to handle certificates and HTTPS in the Go language.

Chapter 4, Asynchronous Microservice Architectures, presents how to implement an asynchronous microservice architecture using message queues. 

In this chapter, we will cover AWS in practical details. You will get exposed to several important concepts like how to setup AWS server instances , how to utilize the AWS API features, and how to write Go applications that are capable of interacting with AWS.

how to implement a basic Continuous Delivery pipeline for your Go applications. For this, we will describe the basic principles of CD and how to implement a simple pipeline using tools such as Travis CI and Gitlab.

We will use Docker images as deployment artifacts and deploy these images into a Kubernetes cluster, thus building on the topics and skills

Chapter 10, Monitoring Your Application, shows you how to monitor your microservice architecture using Prometheus and Grafana. We will cover the basic architecture of Prometheus and describe how to set up a Prometheus instance using Docker. 

how to adjust your Go applications to expose metrics that can be scraped by Prometheus. We will also describe how to set up a graphical user interface for Prometheus using Grafana.

Chapter 11, Migration, covers practical factors and approaches to consider when migrating from legacy monolithic applications into modern microservices cloud-ready applications.

 It will cover other modern cloud-related technologies that deserve to be explored, such as alternative communication protocols, other cloud providers

### Who this book is for

This book is targeted at Go developers who want to build secure, resilient, robust, and scalable applications that are cloud native. Some knowledge of web services and web programming should be sufficient to get you through the book.

### Downloading the example code

The code bundle for the book is also hosted on GitHub at https://github.com/PacktPublishing/Cloud-Native-Programming-with-Golang

### Questions

If you have a problem with any aspect of this book, you can contact us at questions@packtpub.com, and we will do our best to address the problem.

### Modern Microservice Architectures

For the sake of efficiency, we'll provide a straightforward answer to this question,

For businesses, it's all about making money and saving costs. Cloud computing drives costs down significantly for most organizations. That's because cloud computing saves you the cost of building your own data center. No expensive hardware needs to be bought, and no expensive buildings with fancy air-conditioning systems need to be commissioned.

Additionally, almost all cloud computing offerings give you the ability to pay for only what you use and no more. Cloud computing also offers massive flexibility for software engineers and IT administrators to do their jobs quickly and efficiently, thus achieving developer happiness and increased productivity.

•  Design goals of cloud-native applications, especially scalability
•  Different cloud service models
•  The twelve-factor app
•  Microservice architectures
•  Communication patterns, especially synchronous versus asynchronous communication

### Why Go?

It was developed by Google to facilitate the construction of its backend software services. However, it's now being used by numerous enterprises and start-ups to write powerful applications. 

The Go runtime offers garbage collection; however, it does not rely on virtual machines to achieve that. Go programs are compiled into native machine code.

Go is perfect for microservice architectures, which we will be seeing a lot of in the future. A microservice architecture is an architecture where you divide the responsibilities of your application between smaller services that only focus on specific tasks. These services can then communicate among themselves to obtain the information they need to produce results.

The goal of this book is to build the knowledge bridge between the Go programming language and the cloud technologies of modern computing. In this book, you will gain practical knowledge of Go microservice architectures, message queues, containers, cloud platform Go APIs, SaaS applications design, monitoring cloud applications, and more.


### Basic design goals

In order to fully benefit from the advantages of modern cloud platforms, we need to consider their characteristic properties when developing applications that should run on these platforms.


One of the main design goals of cloud applications is scalability. On the one hand, this means growing your application's resources as needed in order to efficiently serve all your users. On the other hand, it also means shrinking your resources back to an appropriate level when you do not need them anymore. This allows you to run your application in a cost-efficient manner without having to constantly overprovision for peak workloads.


This method of scaling is called horizontal scaling or scale out—as opposed to vertical scaling or scale up, where you would not increase the number of instances, but provision more resources to your existing instances. Horizontal scaling is often preferred to vertical scaling for several reasons.

First, horizontal scaling promises unlimited linear scalability. On the other hand, vertical scaling has its limits due to the fact that the number of resources that you can add to an existing server cannot grow infinitely. Secondly, horizontal scaling is often more cost-efficient since you can use cheap commodity hardware (or, in cloud environments, smaller instance types), whereas larger servers often grow exponentially more expensive.

Horizontal scaling versus vertical scaling; the first works by adding more instances and load-balancing the workload across them, whereas the latter works by adding more resources to existing instances


All major cloud providers offer the ability to perform horizontal scaling automatically, depending on your application's current resource utilization. This feature is called auto-scaling. 

In order to be able to scale out, your application needs to adhere to some very important design goals that often need to be considered from the start, as follows:

•  Statelessness: Each instance of a cloud application should not have any kind of internal state

. In a scale-out scenario, subsequent requests might be served by another instance of the application and, for this reason, must not rely on any kind of state being present from previous requests.

In order to achieve this, it is usually necessary to externalize any kind of persistent storage, such as databases and filesystems. Both database services and file storage are often offered as managed services by the cloud provider that you use in your application.


•  Ease of deployment: When scaling out, you will need to deploy new instances of your application quickly. Creating a new instance should not require any kind of manual setup, but should be automated as much as possible (ideally completely).

•  Resiliency: In a cloud environment, especially when using auto-scaling, instances may be shut down at a moment's notice

Cloud providers often support you in this task by offering managed services (for example, highly scalable database services of distributed file storage) that otherwise you would have to worry about yourself. 

### Cloud service models

•  IaaS (Infrastructure as a Service): This is the model where the cloud service provider gives you access to infrastructure on the cloud, such as servers (virtual and bare metal), networks, firewalls, and storage devices. 

IaaS is used by start-ups and organizations that want full control over their application's layer. Most IaaS offerings come with a dynamic or elastic scaling option, which would scale your infrastructure based on your consumption. This, in effect, saves organizations costs since they only pay for what they use.

With PaaS, you don't have to worry about updates and patches for your application environment; it gets taken care of by the cloud provider.

•  SaaS (Software as a Service): This is the highest layer offering you can obtain as a cloud solution. A SaaS solution is when a fully functional piece of software is delivered over the web.

 A very famous example of a SaaS platform is Netflix—a complex piece of software hosted in the cloud, which is available to you via the web. Another popular example is Salesforce. Salesforce solutions get delivered to customers through web browsers with speed and efficiency.


### Cloud application architecture patterns

However, there are a few architectural patterns that are particularly common when targeting a cloud environment, which you will learn in the following section.


### The twelve-factor app

The twelve-factor app methodology is a set of rules for building scalable and resilient cloud applications. It was published by Heroku, one of the dominant PaaS providers. However, it can be applied to all kinds of cloud applications, independent of concrete infrastructure or platform providers.

The twelve-factor app methodology describes (unsurprisingly) twelve factors that you should consider in your application for it to be easily scalable, resilient, and platform independent. 

 Dependencies should be explicitly declared (for example, using an npm package.json file for a Node.js application) so that a package manager can pull all these dependencies when deploying a new instance of the application. In Go, an application is typically deployed as a statically compiled binary that already contains all required libraries. 

Configuration is any kind of data that might vary for different deployment, for example, connection data and credentials for external services and databases. These kinds of data should be passed to the application via environment variables.

For heavy lifting, you can use the github.com/spf13/viper library.

•  Factor IV: Backing Services—Treat backing services as attached resources: Ensure that services that your app depends on (such as databases, messaging systems, or external APIs) are easily swappable by configuration. For example, your app could accept an environment variable, such as DATABASE_URL, that might contain mysql://root:root@localhost/test for a local development deployment and mysql://root:XXX@prod.XXXX.eu-central-1.rds.amazonaws.com in your production setup.

•  Factor VI: Processes—Execute the app as one or more stateless processes: Running application instances should be stateless; any kind of data that should persist beyond a single request/transaction needs to be stored in an external persistence service.

Instead, try to keep user sessions stateless or move the session state into an external data store, such as Redis or Memcached.


Maximize robustness with fast startup and graceful shutdown: In a cloud environment, sudden termination (both intentional, for example, in case of downscaling, and unintentional, in case of failures) needs to be expected. A twelve-factor app should have fast startup times (typically in the range of a few seconds), allowing it to rapidly deploy new instances.

•  Factor XI: Logs—Treat logs as event streams: Log data is often useful for debugging and monitoring your application's behavior. However, a twelve-factor app should not concern itself with the routing or storage of its own log data. The easiest and simplest solution is to simply write your log stream to the process's standard output stream (for example, just using fmt.Println(...)). 

In production setups, you can configure the execution environment to catch the process output and send the log stream to a place where it can be processed (the possibilities here are endless—you could store them in your server's journald, send them to a syslog server, store your logs in an ELK setup, or send them to an external cloud service).


### What are microservices?

When an application is maintained by many different developers over a longer period of time, it tends to get more and more complex. Bug fixes, new or changing requirements, and constant technological changes result in your software continually growing and changing. When left unchecked, this software evolution will lead to your application getting more complex and increasingly difficult to maintain.

In a microservice architecture, a software system is split into a set of (potentially a lot of) independent and isolated services. These run as separate processes and communicate using network protocols (of course, each of these services should in itself be a twelve-factor app).

In contrast to traditional Service-Oriented Architectures (SOA), which have been around for quite a while, microservice architectures focus on simplicity. Complex infrastructure components such as ESBs are avoided at all costs, and instead of complicated communication protocols such as SOAP, simpler means of communication such as REST web services

Splitting complex software into separate components has several benefits. For instance, different services can be built on different technology stacks. For one service, using Go as runtime and MongoDB as persistence layer may be the optimal choice, whereas a Node.js runtime with a MySQL persistence might be a better choice for other components.

Encapsulating functionality in separate services allows developer teams to choose the right tool for the right job. Other advantages of microservices on an organizational level are that each microservice can be owned by different teams within an organization. Each team can develop, deploy, and operate their services independently, allowing them to adjust their software in a very flexible way.

### Deploying microservices

With their focus on statelessness and horizontal scaling, microservices work well with modern cloud environments.

However, each individual service will be easier to deploy than a big monolithic application. Depending on the service's size, it will also be easier to upgrade a service to a new runtime or to replace it with a new implementation entirely. Also, you can scale each microservice individually. This allows you to scale out heavily used parts of your application while keeping less utilized components cost-efficient. Of course, this requires each service to support horizontal scaling.


Deploying microservices gets (potentially) more complex when different services use different technologies. A possible solution for this problem is offered by modern container runtimes such as Docker or RKT. Using containers, you can package an application with all its dependencies into a container image and then use that image to quickly spawn a container running your application on any server that can run Docker (or RKT) containers. 

Running container workloads is a service offered by many major cloud providers 

 These orchestration engines offer the possibility to distribute container workloads over entire server clusters, and offer a very high degree of automation. For example, the cluster manager will take care of deploying containers across any number of servers, automatically distributing them according to their resource requirements and usages. Many orchestration engines also offer auto-scaling features and are often tightly integrated with cloud environments.

### REST web services and asynchronous messaging

When building a microservice architecture, your individual services need to communicate with one another. One widely accepted de facto standard for microservice communication is RESTful web services

These are usually built on top of HTTP (although the REST architectural style itself is more or less protocol independent) and follow the client/server model with a request/reply communication model.

This architecture is typically easy to implement and to maintain. It works well for many use cases.

An alternative communication pattern that can solve these issues is the publish/subscribe pattern. Here, services emit events that other services can listen on. The service emitting the event does not need to know which other services are actually listening to these events.

Again, consider the second part of the preceding diagram—here, the user service publishes an event stating that a new user has just been created. Other services can now subscribe to this event and are notified whenever a new user has been created. These architectures usually require the use of a special infrastructure component: the message broker. This component accepts published messages and routes them to their subscribers (typically using a queue as intermediate storage).


The publish/subscribe pattern is a very good method to decouple services from one another—when a service publishes events, it does not need to concern itself with where they will go, and when another service subscribes to events, it also does not know where they came from.

Furthermore, asynchronous architectures tend to scale better than ones with synchronous communication. Horizontal scaling and load balancing are easily accomplished by distributing messages to multiple subscribers.

Unfortunately, there is no such thing as a free lunch; this flexibility and scalability are paid for with additional complexity. Also, it becomes hard to debug single transactions across multiple services. Whether this trade-off is acceptable for you needs to be assessed on a case-by-case basis.

In Chapter 4, Asynchronous Microservice Architectures Using Message Queues, you will learn more about asynchronous communication patterns and message brokers.

### The MyEvents platform

Throughout this book, we will build a useful SaaS application called MyEvents. MyEvents will utilize the technologies that you'll be learning in order to become a modern, scalable, cloud-native, and snappy application. 

We will make use of microservices, message queues, ReactJS, MongoDB, AWS, and more to construct MyEvents.

 They will be managed by multiple microservices in order to establish a clear separation of concerns and to achieve the flexibility and scalability that we need:

We will have multiple users; each User can have multiple bookings for events, and each Booking will correspond to a single Event. For each one of our events, there will be a Location where the event is taking place. Inside the Location, we will need to identify the Hall or room where the event is taking place.

Microservice architecture

We will use a ReactJS frontend to interface with the users of our applications. The ReactJS UI will use an API gateway (AWS  or local) to communicate with the different microservices that form the body of our application.

There are two main microservices that represent the logic of MyEvents:
•  Event Service: This is the service that handles the events, their locations, and changes that happen to them
•  Booking Service: This service handles bookings made by users


All our services will be integrated using a publish/subscribe architecture based on message queues. Since we aim to provide you with practical knowledge in the world of microservices and cloud computing, we will support multiple types of message queues. We will support Kafka, RabbitMQ, and SQS from AWS.

The persistence layer will support multiple database technologies as well, in order to expose you to various practical database engines that empower your projects. We will support MongoDB, and DynamoDB</span>.


All of our services will support metrics APIs, which will allow us to monitor the statistics of our services via Prometheus.

exposure to the powerful world of microservices and cloud computing.

### Summary

you learned about the basic design principles of cloud-native application development. This includes design goals, such as supporting (horizontal) scalability and resilience, and also architectural patterns, such as the twelve-factor app and microservice architectures.

In Chapter 2, Building Microservices Using Rest APIs, you will learn how to implement a small microservice that offers a RESTful web service using the Go programming language. In the next chapters, you will continue to extend this small application and learn how to handle the deployment and operate on of this application in various cloud environments.

### Building Microservices Using Rest APIs

we'll go on a journey to learn about the world of microservices. We'll learn about how they are structured, how they communicate, and how they persist data.

The concept of microservices is a key concept to cover due to the fact that most of the modern cloud applications in production today rely on microservices to achieve resiliency and scalability.

### The background

To fully appreciate microservices, let's start by telling the story of their rise. Before the idea of microservices became popular, most applications used to be monolithic. A monolithic application is a single application that tries to get numerous tasks accomplished at once.

This, in effect, produced unmaintainable applications in the long run. With the emergence of cloud computing, and distributed applications with massive loads, the need for a more flexible application architecture became obvious.


•  Handle search: Our application needs to be capable of performing efficient searches to retrieve our bookings and events.

If you are trying to build an application that is expected to have a limited set of tasks, and not expected to grow by much, then a single well-built application might be all you need. If on the other hand, you are looking to build a complex application that is expected to perform numerous independent tasks, being maintained by multiple people, while handling massive data loads, then the microservices architecture is your friend.

### So, what are microservices?

Simply put, microservices is the idea that instead of putting all of your code in one basket (monolithic application), you write multiple small software services or microservices. Each service is expected to focus on one task and perform it well. The accumulation of those services will make up your application.

Since those services collaborate to build a complex application, they need to be able to communicate via protocols that they all understand. Microservices that use web Restful APIs for communication make use of the HTTP protocol extensively. We'll cover Restful APIs in more detail in this chapter.


### Microservices internals

•  The microservice will need to be able to send and receive messages with other services and the outside world so that tasks can be carried out in harmony. The communication aspect of a microservice takes different forms.

Restful APIs are very popular when interacting with the outside world, and message queues are very helpful when communicating with other services. 

•  The microservice will need a configuration layer; this could be via environmental variables, a file or database. This configuration layer will tell the microservice how to operate. 

•  The microservice will need to log events that happen to it so that we can troubleshoot issues and understand behaviors. For example, if a communication issue occurs while sending a message to another service, we'll need the error to be logged somewhere in order for us to be able to identify the problem.

•  The microservice will need to be able to persist data by storing it in a database or other forms of data stores; we'll also need to be able to retrieve data at a later time.

•  Finally, there is the core, the most important piece of our microservice. The core is the code responsible for the task that our microservice is expected to do. 

### RESTful Web APIs

REST is simply a way for different services to communicate and exchange data. The core of the REST architecture consists of a client and a server. The server listens for incoming messages, then replies to it, whereas the client starts the connection, then sends messages to the server.

HTTP is a client-server, request-response protocol. This means that the data flow works as follows:
•  An HTTP client sends a request to an HTTP server
•  The HTTP server listens to incoming requests, then responds to them as they come


In the world of communicating microservices, REST applications usually use the HTTP protocol in combination with the JSON data format in order to exchange data messages.


•  DELETE: It is used to delete a resource.

### Gorilla web toolkit

The Gorilla web toolkit consists of a collection of Go packages that together helps build powerful web applications quickly and efficiently.

### Implementing a Restful API

The first step to utilize the package is to make use of the go get command in order to obtain the package to our development environment:
$ go get github.com/gorilla/mux

Inside our code, we now need to create a router using the Gorilla mux package. This is accomplished via the following code:
r := mux.NewRouter()

Since we are in the process of designing a web RESTful API for the microservice, each task will need to translate into an HTTP method combined with a URL and an HTTP body if need be.

 you will notice that their relative URLs all share a common property, which is the fact that it starts with /events. In the Gorilla web toolkit, we can create a subrouter for the /events—relative URL. A subrouter is basically an object that will be in charge of any incoming HTTP request directed towards a relative URL that starts with /events.

eventsrouter := r.PathPrefix("/events").Subrouter()
The preceding code makes use of the router object we created earlier, then calls the PathPrefix method, which is used to capture any URL path that starts with /events. Then, finally, we call the Subrouter() method, which will create a new router object for us to use from now on to handle any incoming requests to URLs that start with /events. The new router is called eventsrouter.

http.ListenAndServe(":8181", r)
In this single line of code, we created a web server. It will listen for incoming HTTP requests on local port 8181 and will use the r object as the router for the requests. We created the r object earlier using the mux package.

### Persistence layer

In an efficient and complex production environment, the code needs to be capable of switching from one data store to another without too much refactoring.

It is worth mentioning that in microservices architectures, different services can require different types of datastores, so it is normal for one microservice to use MongoDB, whereas another service would use MySQL.


To achieve flexible code design, we would need the preceding three functionalities to be defined in an interface. It would look like this:
type DatabaseHandler interface {
    AddEvent(Event) ([]byte, error)
    FindEvent([]byte) (Event, error)
    FindEventByName(string) (Event, error)
    FindAllAvailableEvents() ([]Event, error)
}

For now, let's focus on the DatabaseHandler interface. It supports four methods that represent the required tasks from the events service persistence layer. We can then create numerous concrete implementations from this interface. 

### MongoDB

MongoDB is a NoSQL document store database engine. The two keywords to understand MongoDB are NoSQL and document store.

NoSQL is a relatively recent keyword in the software industry that is used to indicate that a database engine does not deeply rely on relational data. Relational data is the idea that there are webs of relations between different pieces of data in your database, following the relations between your data will build a full picture of what the data represents.

Data gets stored in numerous tables, then, primary and foreign keys are used to define the relations between the different tables. MongoDB doesn't work this way, which is why MySQL is considered as a SQL database, whereas MongoDB is considered NoSQL.

Generally, when installed, Mongodb provides two key binaries: mongod and mongo. The mongod command is what you need to execute, in order to run your database. Any software you then write will communicate with mongod to access Mongodb’s data. The mongo command, on the other hand, is basically a client tool you can use to test the data on Mongodb, the mongo command communicates with mongod, similarly to any application you write that accesses the database.

 If we are using a document store in the microservice persistence layer, each event will be stored in a separate document with a unique ID. 

Models are basically data structures containing fields that match the data we are expecting from the database. In the case of Go, we'd use struct types to create our models.

Since the event location needs to hold more information than just a single field, we will create a struct type called location to model a location. 

The bson.ObjectId type in the event struct is a special type that represents MongoDB document ID. The bson package can be found in the mgo adapter, which is the Go third part framework of choice to communicate with MongoDB. The bson.ObjectId type also provides some helpful methods that we can use later in our code to verify the validity of the ID.


bson is a data format used by MongoDB to represent data in stored documents. It could be simply considered as binary JSON because it is a binary-encoded serialization of JSON-like documents.

### MongoDB and the Go language

In order to make use of mgo, the first step is to make use of the go get command to retrieve the package:
go get gopkg.in/mgo.v2
With the preceding command executed, we get the ability to use mgo in our code.

To make use of *mgo.session inside our code, we will wrap it with a struct type called MongoDBLayer, as follows:

In the Go language, it is typically preferred to use a pointer type when implementing an interface because pointers preserve references to the original memory addresses of the underlying objects as opposed to copying the entire object around when we make use of it.

return &MongoDBLayer{
        session: s,
    }, err

The session.Copy() is the method that is called whenever we are requesting a new session from the mgo package connection pool.

### Implementing our RESTful APIs handler functions

The eventServiceHandler type now needs to support the DatabaseHandler interface type we created earlier in the chapter in order to be capable of performing database operations.

In the first line, we use mux.Vars() with the request object as an argument; this will return a map of keys and values, which will represent our request URL variables and their values. 

Notice here how we report the error in a JSON like format? That is because it is typically preferred for RESTful APIs with JSON body formats to return everything in JSON form, including errors. Another way to do this is to create a JSONError type and feed it our error strings; however, I will just spell out the JSON string here in the code for simplicity:

if !ok {
    fmt.Fprint(w, `{error: No search criteria found, you can either search by id via /id/4 to search by name via /name/coldplayconcert}`)
    return
}
fmt.Fprint allows us to write the error message directly to the w variable, which contains our HTTP response writer. The http.responseWriter object type supports Go's io.Writer interface, which can be used with fmt.Fprint().

### Securing Microservices

In this chapter, we will secure the restful API service that was authored in the preceding chapter. 

HTTP by itself is not secure, which means that it sends data over plain text. Obviously, if we are trying to send credit card information or sensitive personal data, we would never want to send it as a clear text. Fortunately, HTTP communications can be secured via a protocol known as TLS (Transport Layer Security). The combination of HTTP and TLS is known as HTTPS.

### HTTPS

 TLS relies on two types of cryptography algorithms to achieve its goals—symmetric cryptography and public-key cryptography.
Public-key cryptography is also known as asymmetrical cryptography. 

### Symmetric cryptography

Symmetric cryptography or symmetric-key algorithms are the algorithms that make use of the same key to encrypt and decrypt the data, which is why they are called symmetric. The following diagram shows where an encryption key is utilized to encrypt the word Hello into an encoded form, then the same key is used with the encoded data to decrypt it back to the word Hello.

Symmetric cryptography


### Symmetric-key algorithms in HTTPS

This sounds all good and well, but how exactly would a web client and web server securely agree on the same encryption key before starting to use it to send encrypted data?

The answer to that question as we mentioned earlier is that the TLS protocol relies on not one, but two types of cryptography algorithms to secure HTTP. The symmetric-key algorithms, which we have covered so far, are utilized to secure most of the communication; however, the public-key algorithms are used for the initial handshake. This is where the client and the server say hi and identify each other, then agree on an encryption key to use thereafter.


### Asymmetric cryptography

Unlike symmetric-key algorithms, asymmetric cryptography or public-key algorithms that utilize two keys for protection of data. One key to encrypt the data is known as the public key, and it can be shared safely with other parties. Another key to decrypt the data is known as the private key, and it must not be shared. 


The public key can be used by any person to encrypt data. However, only the person with the private key that corresponds to the public key can decrypt the data back to its original human-readable form. The public and private keys are generated using complex computational algorithms.

The other people would then use the public key to encrypt the data being sent to the key owner. The key owner, in turn, can use their private key to decrypt this data back to its original content.

Alice first needs to send Bob a copy of her public key so that Bob can use it to encrypt his message before sending it to Alice. Then, when Alice receives the message, she can use her private key, which is not shared with anyone, to decrypt the message back to the human-readable text and see that Bob said hello. 

### Asymmetrical cryptography in HTTPS

The server then replies with a digital certificate. If you are not familiar with the concept of digital certificates, then now is the time to shed some light on what it is. A digital certificate (or a public-key certificate) is an electronic document that proves the ownership of a public key. To understand the importance of digital certificates, let's take a couple of steps back to remember what a public key is.

A digital certificate is a digital document that gets issued by a trusted third-party entity. The document contains a public encryption key, the server name that the key belongs to, and the name of the trusted third-party entity who verifies that the information is correct and that the public key belongs to the expected key owner (also called the issuer of the certificate). 

For larger organizations or government bodies, they issue their own certificates; this process is known as self-signing, and hence, their certificates are known as self-signed certificates. Certificates can have expiry dates by which the certificates will need to be renewed; this is for extra protection to protect in case the entity that owned the certificate in the past had changed.

web client typically contains a list of certificate authorities that it knows of. So, when the client attempts to connect to a web server, the web server responds with a digital certificate. The web client looks for the issuer of the certificate and compares the issuer with the list of certificate authorities that it knows. If the web client knows and trusts the certificate issuer, then it will continue with the connection to that server and make use of the public key in the certificate.


### Secure web services in Go

Now it's time to find out how to write secure web services in the Go language. Fortunately, Go was built from the grounds up with modern software architectures in mind, which includes secure web applications. 

### Obtaining a certificate

There are also certificate authorities who provide the service for free. For example, in 2016, the Mozilla Foundation along with the Electronic Frontier Foundation and the University of Michigan collaborated to found a certificate authority called Let's Encrypt, which can be found at: https://letsencrypt.org/. Let's Encrypt is a free service that performs the validation, signing, and issuing of certificates in an automated fashion. 

•  A digital certificate which will contain the following:
◦  A public key that can be shared with other parties.
◦  The server name or domain name who owns the certificate.
◦  The issuer of the certificate. In case of a self-signed certificate, the issuer would just be us. In case of a certificate issued by a trusted certificate authority, the issuer will be the CA.
•  A private key that we need to keep a secret and not share with anyone

### OpenSSL

here is an example of how to make use of it to generate a digital certificate in addition to its private key:
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365

•  req: Stands for the request; it indicates that we request a certificate.
•  -x509: This will indicate that we want to output a self-signed certificate. In the world of cryptography, the notion X.509 is a standard that defines the format of public key certificates. Digital certificates used in many internet protocols utilize this standard.

a paired private key. As mentioned before, a certificate is nothing but a public key combined with a bunch of identifiers. So, to perform asymmetric cryptography, we will need a private key paired with this public key.

key.pem: This is the argument to the -keyout option. It indicates that we would like the private key to be stored in a file called key.pem. This key needs to be kept private and not shared with anyone, as mentioned earlier.

•  -out: This option provides the filename to write the newly created self-signed certificate to

•  -days: The number of days that the certificate should be valid for.

### generate_cert.go

The tool supports a number of arguments, but typically the most commonly used argument is --host, which indicates the name of the web server that we would like to generate the certificate and the key for. The following is how we would run the tool via the go run command:
go run %!G(MISSING)OROOT%!/(MISSING)src/crypto/tls/generate_cert.go --host=localhost

We will now instruct the command to build a certificate and a private key for a server called localhost. The command will generate the certificate and the key for us, then place them in the current folder, as mentioned earlier. Here is a screenshot showing a successful execution of the command:

•  --start-date: This option indicates the start validation date of the certificate. The argument to this option needs to be formatted as Jan 1 15:04:05 2011, for example.

•  --rsa-bits: This option indicates the number of bits to be utilized in the rsa encryption of the keys. The default value is 2,048.

Once the certificate and key files are generated, we can obtain and use them in our web server application in order to support HTTPS. We'll see how to do just that in the next section.

### Building an HTTPS server in Go

To convert the web server from the preceding chapter from HTTP to HTTPS, we will need to perform one simple change—instead of calling the http.ListenAndServer() function, we'll utilize instead another function called http.ListenAndServeTLS(). The code will look as follows:
http.ListenAndServeTLS(endpoint, "cert.pem", "key.pem", r)

However, what if we would like to support both HTTP and HTTPS in our microservice? For this, we will need to get a little creative. The first logical step would be to run both the http.ListenAndServe() and the http.ListenAndServeTLS() functions in our code, but then we come across an obvious challenge: how would both functions listen on the same local port? We simply solve this by picking a listening port for HTTPS that is different than the listening port of HTTP. 

So, the solution for this is simple. We call one of the functions in a different goroutine. This can be simply achieved by placing the word go before the function name. Let's run the http.ListenAndServe() function in a different goroutine. Here is what the code would look like:
go http.ListenAndServe(endpoint,r)
http.ListenAndServeTLS(tlsendpoint, "cert.pem", "key.pem", r)

Now, let's address another question: both of the http.ListenAndServe() and the http.ListenAndServeTLS() functions return error objects to report any issues in case of failure; so, can we capture errors produced from either function in case of failure, even though they run on different goroutines? For this, we'll need to make use of Go channels, which is the Go idiomatic way to communicate between two goroutines. Here is how the code will look like:
httpErrChan := make(chan error) 
httptlsErrChan := make(chan error) 
go func() { httptlsErrChan <- http.ListenAndServeTLS(tlsendpoint, "cert.pem", "key.pem", r) }() 

//RESTful API start 
httpErrChan, httptlsErrChan := rest.ServeAPI(config.RestfulEndpoint, config.RestfulTLSEndPint, dbhandler) 
select { 
case err := <-httpErrChan: 
     log.Fatal("HTTP Error: ", err) 
case err := <-httptlsErrChan: 
     log.Fatal("HTTPS Error: ", err) 
}

The code will call the ServeAPI() function, which will then capture the two returned error channels into two variables. We will then use the power of the Go's select statement to handle those channels. 

A select statement in Go can block the current goroutine to wait for multiple channels; whatever channel returns first will invoke the select case that corresponds to it. In other words, if httpErrChan returns, the first case will be invoked, which will print a statement in the standard output reporting that an HTTP error occurred with the error found. 

There is another type of channels in Go beside regular channels called buffered channels, which can allow you to pass values without blocking your current goroutine. However, in our case here, we use regular channels.


func ExtractConfiguration(filename string) (ServiceConfig, error) { 
   conf := ServiceConfig{ 
               DBTypeDefault, 
               DBConnectionDefault, 
               RestfulEPDefault, 
               RestfulTLSEPDefault, 
              }
   file, err := os.Open(filename) 
   if err != nil { 
       fmt.Println("Configuration file not found. Continuing with default values.") 
       return conf, err 
    }
   err = json.NewDecoder(file).Decode(&conf) 
   return conf, err
}

•  In the ExtractConfiguration() function, we set the default value of the RestfulTLSEndPint field of the initialized ServiceConfig struct object to RestfulTLSEPDefault.


Any code that will call the ExtractConfiguration() function will get access to this functionality and be able to obtain either a default or a configured value for the HTTPS endpoint. 

### Summary

In the next chapter, we will cover a key topic in the world of distributed microservices architectures: message queues. 


### Asynchronous Microservice Architectures Using Message Queues

The REST architectural style is both simple and flexible at the same time, which makes it an excellent choice for many use cases. However, being built on top of HTTP, all communication in a REST architecture will follow the client/server model with request/reply transactions. In some use cases, this might be restrictive and other communication models might be better suited.


we will introduce the publish/subscribe communication model, along with the technologies that you need to implement it.

Typically, publish/subscribe architectures require a central infrastructure component—the message broker. In the open source world, there are many different implementations of message brokers; so, in this chapter, we will introduce two different message brokers that we feel to be among the most important ones—RabbitMQ and Apache Kafka. Both are suited for specific use cases; you will learn how to set up each of these two message brokers, how to connect your Go application, and when you should use one or the other.


In this chapter, we will cover the following topics:
•  The publish/subscribe architectural pattern
•  Event collaboration
•  Event sourcing
•  AMQP with RabbitMQ
•  Apache Kafka

### The publish/subscribe pattern

The publish/subscribe pattern is a communication pattern alternative to the well-known request/reply pattern. Instead of a client (issuing a request) and a server (replying with a response to that request), a publish/subscribe architecture consists of publishers and subscribers.


Each publisher can emit messages. It is of no concern to the publisher who actually gets these messages. This is the concern of the subscribers; each subscriber can subscribe to a certain type of message and be notified whenever a publisher publishes a given type of message. In reverse, each subscriber does not concern itself with where a message actually came from.

In practice, many publish/subscribe architectures require a central infrastructure component—the message broker. Publishers publish messages at the message broker, and subscribers subscribe to messages at the message broker. One of the broker's main tasks then is to route published messages to the subscribers that have expressed interest in them.
Typically, messages will be routed topic-based.

Each subscriber will also subscribe to a certain topic. Often, a broker will also allow a subscriber to subscribe to an entire set of topic using wildcard expressions such as user.*.


•  A pub/sub architecture is very flexible. It is possible to add new subscribers (and, therefore, extend existing processes) without having to modify the publisher. The inverse also applies; you can add new publishers without having to modify the subscribers.


•  In case the messages are routed by a message broker, you also gain resiliency. Usually, the message broker stores all messages in a queue, in which they are kept until they have been processed by a subscriber. If a subscriber becomes unavailable (for example, due to a failure or an intentional shutdown), the messages that should have been routed to that subscriber will become queued until the subscriber is available again.


For example, RabbitMQ guarantees reliable delivery by requiring each subscriber to acknowledge a received message. Only when the message has been acknowledged, the broker will remove the message from the queue. If the subscriber should fail (for example, by disconnection) when a message had already been delivered, but not yet acknowledged, the message will be put back into the message queue.

otherwise, it will remain in the queue until the subscriber is available again.

•  You can easily scale out. In case that too many messages are published for a single subscriber to efficiently handle them, you can add more subscribers and have the message broker load-balance the messages sent to these subscribers.


Of course, introducing a central infrastructure component such as a message broker brings its own risk. When not done right, your message broker might become a single point of failure, taking your entire application down with it in case it fails. When introducing a message broker in a production environment, you should take appropriate measures to ensure high-availability (usually by clustering and automatic failover).


In case your application is run in a cloud environment, you may also take advantage of one of the managed message queuing and delivery services that are offered by the cloud providers, for example, AWS Simple Queue Service (SQS) or the Azure Service Bus.


In this chapter, you will learn how to use two of the most popular open source message brokers—RabbitMQ and Apache Kafka.

### Event collaboration

Event collaboration describes an architectural principle that works well together with an event-driven publish/subscribe architecture.


Since the events are managed by another microservice (the EventService), the BookingService will need to request information on both the event and its location from the EventService. Only then can the BookingService check whether there are still seats available and save the user's booking in its own database. 

Now, consider the same scenario in a publish/subscribe architecture, in which the BookingService and EventService are integrated using events: every time data changes in the EventService, it emits an event (for example, a new location was created, a new event was created, an event was updated, and so on).

Now, the BookingService can listen to these events. It can build its own database of all currently existing locations and events. Now, if a user requests a new booking for a given event, the BookingService can simply use the data from its own local database, without having to request this data from another service. Refer to the following diagram for another illustration of this principle:

BookingService using the data from its own local database

This is the key point of an event collaboration architecture. In the preceding diagram, a service almost never needs to query another service for data, because it already knows everything it needs to know by listening to the events emitted by other services.

In the preceding example, the EventService would be the publisher and the BookingService (potentially, among others) the subscriber. 

Also, this increases the system's overall resiliency; for example, if the event service suffers a sudden failure, the BookingService would stay operational since it does not rely on the event service to be working anymore.

### Implementing publish/subscribe with RabbitMQ

In the following section, you will learn how to implement a basic publish/subscribe architecture. For this, we will take a look at the Advanced Message Queueing Protocol (AMQP) and one of its most popular implementations, RabbitMQ.

### The Advanced Message Queueing Protocol

On a protocol level, RabbitMQ implements the AMQP. Before getting started with RabbitMQ, let's get started by taking a look at the basic protocol semantics of AMQP.

An AMQP message broker manages two basic kinds of resources—Exchanges and Queues. Each publisher publishes its messages into an exchange. Each subscriber consumes a queue. The AMQP broker is responsible for putting the messages that are published in an exchange into the respective queue.

Where messages go after they have been published to an exchange depends on the exchange type and the routing rules called bindings. AMQP knows three different types of exchanges:

Topic exchanges usually assume routing keys to be segmented with the dot character '.'. As an example, your routing keys could follow the "<entityname>.<state-change>.<location>" pattern (for example, "event.created.europe"). 

◦   event.# (listen to whenever any change is made to an event anywhere in the world)
◦   event.*.europe (listen to whenever any change is made to an event in Europe)

In this case, we have one service that publishes messages, the EventService. We have two queues in which messages will be routed. The first queue, evts_booking, will receive any and all messages that are related to any kind of change made to an event. The second queue, evts_search, will receive messages only regarding the creation of new events.

AMQP specifies several methods that clients can use to declare the exchanges and queues they need. 

On the other hand, a subscriber might use the queue.declare and queue.bind methods to declare a queue that it wants to subscribe and bind it to an exchange.

Although we will use RabbitMQ in this example, the code written in this chapter should work will all kinds of AMQP implementations.


### RabbitMQ quickstart with Docker

You can start a new RabbitMQ broker using the following command on your command line:
$ docker run --detach \ 
    --name rabbitmq \ 
    -p 5672:5672 \ 
    -p 15672:15672 \ 
    rabbitmq:3-management

The -p 5672:5672 flag will instruct Docker to map the TCP port 5672 (which is the IANA-assigned port number for AMQP) to your localhost address. The -p 15672:15672 flag will do the same for the management user interface.

After starting the container, you will be able to open an AMQP connection to amqp://localhost:5672 and open the management UI in your browser at http://localhost:15672

The RabbitMQ image ships a default guest user whose password is also guest. When running RabbitMQ in production, this is, of course, the first thing that you should change. For development purposes, it will do fine.

### Advanced RabbitMQ setups

For a production setup, you might want to consider setting up RabbitMQ as a cluster to ensure high availability. Take a look at the official documentation at http://www.rabbitmq.com/clustering.html for more information on how to set up a RabbitMQ cluster.

### Connecting RabbitMQ with Go

For connecting to a RabbitMQ broker (or any AMQP broker, for that matter), we recommend that you use the github.com/streadway/amqp library (which is the de facto standard Go library for AMQP). Let's start by installing the library:
$ go get -u github.com/streadway/amqp

You can then start by importing the library into your code. Open a new connection using the amqp.Dial method:

import "github.com/streadway/amqp" 
 
func main() { 
  connection, err := amqp.Dial("amqp://guest:guest@localhost:5672") 
  if err != nil { 
    panic("could not establish AMQP connection: " + err.Error()) 
  } 
 
  defer connection.Close() 
}

The amqp.Dial method returns a connection object on success, or nil and an error, otherwise (as usual in Go, make sure that you actually check for this error). Also, do not forget to close the connection using the Close() method when you do not need it anymore.

let's introduce an environment variable AMQP_URL that we can use to dynamically configure the AMQP broker:
import "github.com/streadway/amqp" 
import "os" 
 
func main() { 
  amqpURL := os.Getenv("AMQP_URL"); 
  if amqpURL == "" { 
    amqpURL = "amqp://guest:guest@localhost:5672" 
  } 
 
  connection, err := amqp.Dial(amqpURL) 
  // ... 
}

In AMQP, most operations are done not directly on the connection, but on channels. Channels are used to multiplex several virtual connections over one actual TCP connection.

### Publishing and subscribing to AMQP messages

let's take a look at the basic AMQP methods that we can use. For this, we will start by building a small example program that is capable of publishing messages to an exchange.

After opening a channel, a message publisher should declare the exchange into which it intends to publish messages. For this, you can use the ExchangeDeclare() method on the channel object:
err = channel.ExchangeDeclare("events", "topic", true, false, false, false, nil) 

•  The exchange name
•  The exchange type (remember that AMQP knows direct, fanout, and topic exchanges)
•  The durable flag will cause the exchange to remain declared when the broker restarts
•  The autoDelete flag will cause the exchange to be deleted as soon as the channel that declared it is closed

The Publish() method takes the following parameters:
•  The name of the exchange to publish to
•  The message's routing key
•  The mandatory flag will instruct the broker to make sure that the message is actually routed into at least one queue

In order to actually process it, you will need a subscriber.

After having declared and bound a queue, you can now start consuming this queue. For this, use the channel's Consume() function:

•  A string that uniquely identifies this consumer. When left empty (like in this case), a unique identifier will be automatically generated.

•  When the autoAck flag is set, received messages will be acknowledged automatically. When it is not set, you will need to explicitly acknowledge messages after processing them using the received message's Ack() method (see the following code example).

•  When the exclusive flag is set, this consumer will be the only one allowed to consume this queue. When not set, other consumers might listen on the same queue.

•  The noWait flag instructs the library not to wait for confirmation from the broker.
•  The args argument may contain a map with additional configuration parameters.

In order to receive messages from the queue, we can simply read values from that channel. If you want to read messages continuously, the easiest way to do this is using a range loop:
for msg := range msgs { 
  fmt.Println("message received: " + string(msg.Body)) 
  msg.Ack(false) 
}

Note that we explicitly acknowledge the message using the msg.Ack function in the preceding code. This is necessary because we have set the Consume() function's autoAck parameter to false, earlier.


Explicitly acknowledging a message serves an important purpose—if your consumer fails for whatever reason between receiving and acknowledging the message, the message will be put back into the queue, and then redelivered to another consumer (or stay in the queue, if there are no other consumers). For this reason, a consumer should only acknowledge a message when it has finished processing it. If a message is acknowledged before it was actually processed by the consumer (which is what the autoAck parameter would cause), and the consumer then unexpectedly dies, the message will be lost forever. For this reason, explicitly acknowledging messages is an important step in making your system resilient and failure-tolerant.

### Building an event emitter

In general, each AMQP message is simply a string of bytes. To submit structured data, we can use serialization formats, such as JSON or XML. Also, since AMQP is not limited to ASCII messages, we could also use binary serialization protocols such as MessagePack or ProtocolBuffers.

Regarding the serialization format, we will take the easy choice in this chapter and use the JSON serialization format. It is widely adopted; serializing and unserializing messages are easily done using Go standard libraries and also in other programming languages

We also need to make sure that both publisher and subscribers know how the messages will be structured. For example, a LocationCreated event might have a name property and an address property. To solve this issue, we will introduce a shared library that will contain struct definitions for all possible events, together with instructions for the JSON (un)serialization. This library can then be shared between the publisher and all subscribers.


Also, we need a possibility to generate a topic name for each event (in RabbitMQ, the topic name will also be used as a routing key for the messages). For this, add a new method—EventName()—to your newly defined struct:
func (e *EventCreatedEvent) EventName() string { 
  return "event.created" 
}

For a proper instantiation, we will provide a constructor method, instead.

While it is, in theory, possible to reuse the same channel for publishing multiple messages, we need to keep in mind that a single AMQP channel is not thread-safe. This means that calling the event emitter's Emit() method from multiple go-routines might lead to strange and unpredictable results. This is exactly the problem that AMQP channels are there to solve; using multiple channels, multiple threads can use the same AMQP connection.

This allows you to specify the AMQP broker via the JSON configuration file. In the ExtractConfiguration() function, we can also add a fallback that optionally extracts this value from an environment variable, if set:


### Building an event subscriber

type EventListener interface { 
  Listen(eventNames ...string) (<-chan Event, <-chan error, error) 
}

However, an event listener is typically active for a long time and needs to react to incoming messages whenever they may be received. This reflects in the design of our Listen() method: first of all, it will accept a list of event names for which the event listener should listen. It will then return two Go channels: the first will be used to stream any events that were received by the event listener. The second will contain any errors that occurred while receiving those events.

Note that the name of the queue that the listener will consume is configurable using the amqpEventListener struct's queue field. This is because later, multiple services will use the event listener to listen to their events, and each service will require its own AMQP queue for this.

for _, eventName := range eventNames { 
    if err := channel.QueueBind(a.queue, eventName, "events", false, nil); err != nil { 
      return nil, nil, err 
    } 
  } 

### Building the booking service

This function uses a type switch to determine the actual type of the incoming event. Currently, our event listener processes the two events, EventCreated and LocationCreated, by storing them in their local database.

### Event sourcing

When using messaging, publish/subscribe, and event collaboration, every change in the entire system's state is reflected in the form of an event that is emitted by one of the participating services. 

In theory (and also in practice), you can use this event log to reconstruct the entire system state, without having to rely on any other kind of database.


By replaying these events, it is easy to reconstruct the state of your system at the end of the day—there is one user named Cedric. However, there is more. Since each event is timestamped, you can reconstruct the state that your application had at any given point in time (for example, at 10:00 am, your application had two users, Alice and Bob).

Besides point-in-time recovery, event sourcing offers you a complete audit log over everything that happened in your system. Audit logging often is an actual requirement on its own in many cases, but also makes it easier to debug the system in case of errors. Having a complete event log allows you to replicate the system's state at the exact point in time and then replay events step by step to actually reproduce a specific error.

### Implementing publish/subscribe and event sourcing with Apache Kafka

However, RabbitMQ only handles message dispatching, so if you need an event log containing all events, you will need to implement it yourself by listening to all events and persisting them. You will also need to take care of event replaying yourself.

Apache Kafka is a distributed message broker that also ships with an integrated transaction log. It was originally built by LinkedIn and is available as an open source product licensed under the Apache License.


### Kafka quickstart with Docker

Apache Kafka is a bit more complex to set up. Kafka itself requires a working Zookeeper setup in order to perform leader election, managing cluster state, and persisting cluster-wide configuration data. However, for development purposes, we can use the spotify/kafka image. This image comes with a built-in Zookeeper installation, allowing quick and easy setup.

 docker run -d --name kafka -p 9092:9092 spotify/kafka

### Basic principles of Apache Kafka

The first basic concept in Kafka is the topic. A topic is something like a category or event name that subscribers can write to. It contains a complete log of all messages that were ever published into this topic. Each topic is divided into a configurable number of partitions.

Each Kafka topic consists of a configurable number of partitions; each published message has a partition key, which is used to decide into which partition a message should be saved

For each topic, messages will be kept for a configurable retention period. However, the broker's performance does not degrade significantly when the transaction logs get larger. For this reason, it is entirely possible to operate Kafka with an infinite retention period, and by this way use it as an event log. 

In both cases, every message that is published in the exchange/topic will be routed to every consumer.


This can be used to build some kind of load-balancing between different subscriber instances.

If you should decide to have multiple consumers within the same consumer group subscribe the same partition of a topic, the broker will simply dispatch all messages in that partition to the consumer that connected last.

### Consuming messages from Kafka

Using 0 as an offset will instruct the event listener to read the entire event log right from the start. This is the ideal solution if you want to implement event sourcing with Kafka. If you just want to use Kafka as a message broker, your service will need to remember the offset of the last message read from the event log. When your service is restarted, you can then resume consuming from that last known position.

### Summary

In this chapter, you learned how to integrate multiple services with asynchronous communication using message queues such as RabbitMQ and Apache Kafka. You also learned about architectural patterns such as event collaboration and event sourcing that help you to build scalable and resilient applications that are well-suited for cloud deployments.

### Building a Frontend with React

However, even the most scalable cloud application is only half as useful without an interface that your users can easily interact with (unless, of course, offering a REST API to your users is your actual product). In order to make the APIs built in the previous chapters more tangible, we will now add a web-based frontend to our application.

### Setting up Node.js and TypeScript

we will use TypeScript in this example. TypeScript is a type-safe superset of JavaScript that adds static typing and class-based OOP to JavaScript. You can compile TypeScript to JavaScript using the TypeScript compiler (or short, tsc).

$ npm install -g typescript
This will download and install the TypeScript compiler into your system's PATH. After running the preceding command, you should be able to call tsc on your command line.

### Initializing the React project

We can now use npm to add dependencies to our project. Let's start by installing the React and ReactDOM packages:
$ npm install --save react@16 react-dom@16 @types/react@16 @types/react-dom@16

The @types packages are needed for the TypeScript compiler. Since React is a JavaScript (not TypeScript) library, the TypeScript compiler will need additional information about the classes defined by the react library and their method signatures. For example, these typings might contain information on which parameter types are needed for certain functions provided by React and their return types.


This file configures Webpack to use the TypeScript loader on all .ts and .tsx files, compile them, and bundle all modules into the dist/bundle.js file. Before you can actually do that, though, you will need to add some source files to compile.

### Basic React principles

A React application is built from Components. A component is a JavaScript class that accepts a set of values (called properties, or in short, props) and returns a tree of DOM elements that can be rendered by the browser.

### Kick-starting the MyEvents frontend

To make our application a bit nicer to look at, we will also add the Twitter Bootstrap framework to our frontend. As usual, you can use npm to install Bootstrap:
$ npm install --save bootstrap@^3.3.7
After installing Bootstrap, you can include the respective CSS file in the header section of your index.html file:

 However, directly opening local files in your browser will cause issues later when making HTTP requests to our backend services. You can use the http-server npm's package to quickly set up an HTTP server that can serve these local files. Simply install it via npm and then run it in your project directory:
$ npm install -g http-server
$ http-server

The Node.js HTTP server will listen at the TCP port 8080 by default, so you can access it by navigating to http://localhost:8080 in your browser:

### Bringing your own client

use the fetch API. The fetch API is a newer JavaScript API for doing AJAX calls to backend services that are implemented in many modern browsers (primarily, Firefox and Chrome). For older browsers that do not yet implement the fetch API, there is a polyfill library that you can add to your application via npm:
$ npm install --save whatwg-fetch promise-polyfill

The fetch polyfill library will use the browser's fetch API when it is available, and provide its own implementation when it's not available. In a few years, when more browsers support the fetch API, you will be safely able to remove the polyfill.


### Enabling CORS in the backend services

Before testing the frontend application, you will need to make sure that the backend services (more precisely, both the event service and the booking service) support Cross-Origin Resource Sharing (CORS). Otherwise, your browser will not execute HTTP requests to any of your backend services, when the frontend is served on http://localhost:8080 and the backend services run on other TCP ports.

For example, to allow AJAX requests from another domain, the HTTP response needs to contain an Access-Control-Allow-Origin header. An HTTP response with such a header might look like this:

After that, we can use the handlers.CORS function to add the CORS functionality to an existing HTTP server. This allows us to adjust the event service's rest.go file as follows:

### Testing the event list

Then, start the Node.js http-server in your frontend application directory and navigate to http://localhost:8080 in your browser:


### Adding routing and navigation

Before we add more functionalities to our frontend application, let's take the time to add a robust navigation and routing layer. This will allow our application to stay easily maintainable when more features are added.

### Deploying Your Application in Containers

In the past few chapters, we focused on the actual development of our Go application. However, there is more to software engineering than just writing code. Usually, you will also need to concern yourself with the question of how you will deploy your application into its runtime environment. Especially in microservice architectures, where each Service may be built on a completely different technology stack, deployment can quickly become a challenge.

Using containers, you can package an application with all its dependencies into a container image and then use that image to quickly spawn a container running your application on any server that can run these containers.

allowing you to make your application deployment more resilient and scalable.

### What are containers?

Container technologies such as Docker use isolation features offered by modern operating systems, such as namespaces and control groups (cgroups) in Linux. Using these features allows the operating system to isolate multiple running processes from each other to a very large extent. 

### Introduction to Docker

Currently, the de facto standard for application container runtimes is Docker, although there are other runtimes, for example, RKT (pronounced rocket). 

This means that even if you decide to deploy your application using Docker images, you are not running into a vendor lock-in.

### Running simple containers

If you are running an older version of Docker, use docker run instead of docker container run. You can test your current Docker version using the docker version command. Also, note that Docker changed its versioning scheme after version 1.13, so the next version after 1.13 will be 17.03.

Remember that a Docker container has no init system and typically has one process running in it. As soon as that process terminates, the container will stop running. Since we created the container with the --rm flag, the Docker engine will also delete the container automatically after it has stopped running.

### Networking containers

In this section, we will take a look at how you can have multiple Docker containers communicate with each other over their network interfaces.
In order to enable container-to-container communication, Docker offers a network management feature. The command line allows you to create new virtual networks and then add containers to these virtual networks. Containers within one network can communicate with each other and resolve their internal IP addresses via Docker's built-in DNS server.


### Building containers for the backend services

Both the event and booking service are Go applications that are compiled into single executable binaries. For this reason, it is not necessary to include any kind of source files or even the Go tool chain in the Docker image.

FROM debian:jessie 
 
COPY eventservice /eventservice 
RUN  useradd eventservice 
USER eventservice 
 
ENV LISTEN_URL=0.0.0.0:8181 
EXPOSE 8181 
CMD ["/eventservice"]

The USER command causes all subsequent RUN statements and the CMD statement to run as that user (and not root). The ENV command sets an environment variable that will be available to the application. Finally, the EXPOSE statement declares that containers created from this image will need the TCP port 8181.

### Using static compilation for smaller images

However, our application does not use most of these tools and libraries, so our container images could be much smaller. By this, we can optimize the local disk space usage (although the Docker Engine is already quite efficient at this), optimize the transfer times when the image is downloaded from an image repository, and reduce the attack surface against malicious users.

 If your binary is linked against the C standard library, you will receive the following output:
$ ldd ./eventservice 
    linux-vdso.so.1 (0x00007ffed09b1000) 
    libpthread.so.0 => /lib/x86_64-linux

This means that your Go application actually needs these Linux libraries to run and that you cannot just arbitrarily delete them from your image to make it smaller.


On Linux, you can force the Go compiler to create statically linked binaries by setting the CGO_ENABLED=0 environment variable for your Go build command:
$ CGO_ENABLED=0 go build 
$ ldd ./eventservice 
    not a dynamic executable

What is special about the scratch image is that it is completely empty, empty as in does not contain one single file—this means no standard libraries, no system utilities, and not even a shell. Although these properties typically make the scratch image cumbersome to use, it is perfectly suited for building minimal container images for statically linked applications.

After that, verify your image size using the docker image ls command:
￼

### Deploying your application to the cloud

Container engines, such as, Docker allow you to provision multiple Services in isolated environments, without having to provision separate virtual machines for individual Services. However, as typical for Cloud applications, our container architecture needs to be easily scalable and also resilient to failure.

### Introduction to Kubernetes

The API Server is the component that offers a REST API that can be used by both internal components (such as the scheduler, controllers, or Kubelets) and external users (you!). The scheduler tracks available resources on the individual nodes (such as memory and CPU usage) and decides on which node in the cluster new containers should be scheduled. Controllers are components that manage high-level concepts such as replication controllers or autoscaling groups.

For local development and testing, we will use the Minikube tool, which automatically creates a virtualized Kubernetes environment on your local machine. When you are running your application in a public cloud environment, you can also use tools that automatically set up a production-ready Kubernetes environment for you.

### Core concepts of Kubernetes

Kubernetes resources (such as Pods and Services) are usually defined in YAML files that declaratively describe the desired state of your cluster (similar to the Docker Compose configuration files that you have worked with before).

As you can see, the spec.containers section is formatted as a list; in theory, you could add additional containers here that would then run within the same Pod.

After having created this file, use the kubectl apply command to create the Pod:


After this, you can use the kubectl get pods command to verify that your Pod has successfully been created. Note that it may take a few seconds to minutes until the Pod changes its status from ContainerCreating to Running:
$ kubectl get pods

There are more things that you can configure for a single Pod. For example, you might want to restrict your application's memory or CPU usage. In this case, you could add the following settings to your newly created Pod manifest:

The resources.limits section will instruct Kubernetes to create a container with a memory limit of 128 MB and a CPU limit of one half CPU core.

Pods may be terminated at a moment's notice and may get lost whenever a node fails. For this reason, it is recommended to use a Kubernetes controller (such as the Deployment controller) to create Pods for you.

 that a given number of Pods of a given configuration is running at any time

### Services

Note that the spec.selector property matches the metadata.labels property that you have specified in the Deployment manifest. 

Next, call kubectl get services to inspect the newly created Service definition.
In the kubectl get services output, you will find your newly created nginx Service (along with the Kubernetes Service, which is always there).

The effect of this property is that the Kube proxy on each node now opened up a TCP port. The port number of this port is chosen randomly. In the preceding example, this is the TCP port 31455. You can use this Port to connect to your Service from outside the Kubernetes cluster (for example, from your local machine). 

Any and all traffic received on this port is forwarded to one of the Pods matched by the selector specified in the Service's specification.


### Persistent volumes

Often, you will need a place to store files and data in a persistent way. Since individual Pods are fairly short-lived in a Kubernetes environment, it is usually not a good solution to store files directly in a container's filesystem. In Kubernetes, this issue is solved using persistent volumes, which are basically a more flexible abstraction of the Docker volumes that you have already worked with before.

Create the volume using kubectl apply -f example-volume.yaml. After this, you can find it again by running kubectl get pv.
The preceding manifest file creates a new volume that stores its files in the /data/volume01 directory on the host that the volume is used on.

### Deploying MyEvents to Kubernetes

we can work on deploying the MyEvents application into a Kubernetes cluster.

### Creating the RabbitMQ broker

Since RabbitMQ is not a stateless component, we will use a special controller offered by Kubernetes—the StatefulSet Controller. This works similar to a Deployment controller, but will create Pods with a persistent identity.

For this reason, we should also declare a persistent volume that can be used by this StatefulSet. Instead of manually creating a new PersistentVolume and a new PersistentVolumeClaim, we can simply declare a volumeClaimTemplate for the StatefulSet and let Kubernetes provision the new volume automatically. 

The volumeClaimTemplate will instruct the StatefulSet controller to automatically provision a new PersistentVolume and a new PersistentVolumeClaim for each instance of the StatefulSet. If you increase the replica count, the controller will automatically create more volumes.

As usual, create the Service using kubectl apply -f rabbitmq-service.yaml. After creating the Service, you will be able to resolve it via DNS using the hostname amqp-broker (or in its long form, amqp-broker.default.svc.cluster.local).

### Creating the MongoDB containers

Just as before, we will use a StatefulSet with automatically provisioned volumes. Place the following contents in a new file called events-db-statefulset.yaml and then call kubectl apply on this file:

### Making images available to Kubernetes

If you are using Minikube and want to save yourself the hassle of setting up your own image registry, you can do the following instead:
$ eval $(minikube docker-env) 
$ docker image build -t myevents/eventservice .
The first command will instruct your local shell to connect not to your local Docker Engine, but the Docker Engine within the Minikube VM, instead. Then, using a regular docker container build command, you can build the container image you are going to use directly on the Minikube VM.


### Deploying the MyEvents components

Since the event service itself is stateless, we will deploy it using a regular Deployment object, not as StatefulSet.

Create the corresponding Service with the following manifest:


### Configuring HTTP Ingress

This will provision the appropriate cloud provider resources (for example, an Elastic Load Balancer in AWS) to make your service publicly accessible at a standard port.

However, Kubernetes also offers another feature that allows you to handle incoming HTTP traffic called Ingress. Ingress resources offer you a more fine-grained control of how your HTTP services should be accessible from the outside world. 

Before using Ingress resources, you will need to enable an Ingress controller for your Kubernetes clusters. This is highly specific to your individual environment; some cloud-providers offer special solutions for handling Kubernetes Ingress traffic, while in other environments, you will need to run your own. Using Minikube, however, enabling Ingress is a simple command:
$ minikube addons enable ingress

If instead, you intend to run your own Ingress controller on Kubernetes, take a look at the official documentation of the NGINX Ingress controller. It may seem complicated at first, but just as many internal Kubernetes services, an Ingress controller also just consists of Deployment and Service recources.


### Summary

In this chapter, you learned how to use container technologies such as Docker to package your application, including all its dependencies into container images. You learned how to build container images from your application and deploy them in a production container environment built on Kubernetes.

We will get back to building container images in Chapter 9, where you will learn how to further automate your container build tool chain, allowing you to completely automate your application deployment, starting with a git push command and ending with an updated container image running in your Kubernetes cloud.

### AWS I – Fundamentals, AWS SDK for Go, and EC2

Due to the large size of the topic, we will divide the material into two chapters. 

•  How to set up and secure EC2 instances


### AWS fundamentals

EC2 allows auto-scaling, which means that applications can automatically scale up and down based on the user's needs.

Typically, developers store images, photos, videos, and similar types of data on S3. The service is reliable, scales well, and easy to use. The use cases for S3 are plentiful; it can be used for web sites, mobile applications, IOT sensors, and more. 

Amazon API Gateway: Amazon API gateway is a hosted service that enables developers to create secure web APIs at scale

### The AWS console

The AWS console is the web portal that provides us access to the multitude of services and features that AWS offers. To access the portal, you first need to navigate to aws.amazon.com, and then choose the Sign In to the Console option, as follows:

### AWS command-line interface (CLI)

AWS CLI is an open source tool that provides commands to interact with AWS services. AWS CLI is cross-platform; it can run on Linux, macOS, and Windows.

AWS CLI can perform tasks that are similar to those performed by the AWS console; this includes configuration, deployment, and monitoring of AWS services. The tool can be found at https://aws.amazon.com/cli/.

### AWS regions and zones

Each region contains multiple isolated internal locations known as availability zones.

For complex application deployments in AWS, developers typically deploy their microservices into multiple regions. This ensures that the application will enjoy high availability, even if any Amazon data center in a certain region suffers from a failure.

### AWS tags

AWS tags is another important concept in the AWS universe. It allows you to categorize your different AWS resources properly.

A tag is a key value pair; the value is optional. 


### AWS services

Now, it's time to learn how to utilize the power of Go to interact with AWS and build cloud native applications. 

### AWS SDK for Go

In order to utilize the SDK, there are some key concepts we need to cover first.
The first step we will need to do is to install the AWS SDK for Go; this is done by running the following command: 
go get -u github.com/aws/aws-sdk-go/...

### Configuring the AWS region

The second step is to specify the AWS region; this helps identify where to send the SDK requests when making calls. There is no default region for the SDK, which is why we must specify one. There are two ways to do that:
•  Assigning the region value to an environmental variable called AWS_REGION. An example of a region value is us-west-2 or us-east-2.
•  Specifying it in the code—more to that later.

### Configuring AWS SDK authentication

There are two main ways to generate the credentials you need to make your code works when talking to AWS via the SDK:

You can assign individual permissions to users directly or assemble multiple users into a group that allow users to share permissions. 

However, a role is not meant to be assigned to people; it instead is assigned to whoever needs it based on specific conditions.

### Creating IAM Users

If you are running your application from your own local machine, the recommended way to create access keys is to create a user that has specific permissions to access the AWS services that you would like your code to utilize. This is done by creating a user in the AWS identity and access management (IAM).


To create a user in IAM, we will first need to log in to the AWS main web console, then click on IAM, which should be under the Security, Identity & Compliance category:

The next step will involve attaching permissions to the user being created. There are three approaches for assigning permissions to users.

•  Adding the user to a group: A group is an entity that can have its own policies. Multiple users can be added to one or more groups. You can think of a group simply as a folder of users. Users under a specific group will enjoy access to all permissions allowed by the policies of the said group.

Once a new user gets created, we will get an option to download the user's access keys as a CSV file. We must do that in order to be able to utilize those access keys later in our applications. An access key is composed of the access key ID and the secret access key value. 

Utilizing a credentials file: The credentials file is a plain text file that will host your access keys. The file must be named credentials and needs to be located in the .aws/ folder of the home directory of your computer. 

The names between the square brackets are called profiles. As shown from the preceding snippet, your credentials file can specify different access keys mapped to different profiles.

If the AWS_PROFILE environmental variable is not set, the default profile gets picked up by default.

Hardcoding the access keys in your application: This is typically not recommended for security reasons. So although it is technically possible, do not try this in any production systems since anyone who would have access to your application code (maybe in GitHub) will be able to retrieve and use your access keys.

### Creating IAM Roles

1.  We first log in to the AWS console (aws.amazon.com)
2.  We then select IAM from under the Security, Identity & Compliance category

This time, we click on Roles on the right-hand side, then select Create new role:
￼

### Sessions

The first concept is the idea of sessions. A session is an object from the SDK that contains configuration information that we can then use with other objects to communicate with AWS services. 


The session objects can be shared and used by different pieces of code. The object should be cached and reused. Creating a new session object involves loading the configuration data, so reusing it saves resources. session objects are safe to use concurrently as long as they don't get modified.


To create a new session object, we can simply write the following code:
session, err := session.NewSession()

If we need to override a configuration, we can pass a pointer to an object of the aws.Config type struct as an argument to the NewSession() struct.

### Service clients

Service client objects are created from session objects. Here is an example of a piece of code that makes use of an S3 service client to obtain a list of buckets 

//Don't forget to import github.com/aws/aws-sdk-go/service/s3

Service client objects are typically safe to use concurrently as long as you ensure that you don't change the configuration in your concurrent code. 

### Native datatypes 

For example, instead of using a string datatype for a string value, the SDK tends to use *string instead, same with ints and other types. In order to make life easier for the developer, the AWS SDK for Go provides helper methods to convert between native datatypes and their pointers while ensuring nil checks are performed to avoid runtime panics. 

func IntValue(v *int) int {
  if v != nil {
    return *v
  }
  return 0
}

### Shared configuration

the AWS provides an option to make use of what is called shared configuration. Shared configuration is basically a configuration file that is stored locally. The filename and path is .aws/config. Remember that the .aws folder would exist in the home folder of our operating system; the folder was covered before when discussing the credentials file. 

### Pagination methods

Some API operations can return a huge number of results. For example, let's say that we need to issue an API call to retrieve a list of items from an S3 bucket. 

The second argument is a function that gets called with the response data for each page. Here is what the function signature looks like:
func(*ListObjectsOutput, bool) bool

### Handling Errors

The awserr.Error supports three main methods:
•  Code() : Returns the error code related to the problem
•  Message(): Returns a string description of the error
•  OrigErr(): Returns the original error that is wrapped with the awserr.Error type; for example, if the problem is related to networking, OrigErr() returns the original error that probably belonged to the Go net package

If it's indeed a resource not found problem, we print a message indicating as such then return.

### Creating EC2 instances

In the AWS console main screen, we will need to choose EC2 in order to start a new EC2 instance:

Once we are satisfied with all the settings, we can go ahead and click on Launch. This will show a dialog requesting a public-private key pair. The concept of public key encryption was discussed in Chapter 3 in more detail. In a nutshell, we can describe public-private key encryption as a method of encryption, where you can share a public key with other people so that they encrypt messages before they send them to you. The encrypted message can then only be decrypted via the private key that only you possess.

For AWS, in order to allow developers to connect to their services securely, AWS requests developers to select a public-private key pair to secure access. The public key is stored in AWS, whereas the private key is stored by the developer. 
￼

If you select the option to create a new key pair, you will get the option to name your key pair and to download the private key. You must download the private key and store it in a secure location so that you can make use of it later:

Finally, after we download the private key and are ready to launch the instance(s), we can click on the Launch Instances button. This will initiate the process of starting the instance(s) and show us a status indicating as such. Here is what the next screen typically looks like:

### Accessing EC2 instances

In order to get access to an EC2 instance that we have already created, we need to first log in to the AWS console, then select EC2 as before. This will provide you access to the EC2 dashboard. 

The preceding screenshot shows us that the instance is currently running on AWS. We can connect to it like any remote server if we so desire. Let's explore how to do that. 

### Accessing EC2 instances from a Linux or macOS machine

In order to access EC2 instances created on AWS from a Linux or a macOS machine, we will need to use the SSH command.

After we ensure that the key is protected against public access, we will need to make use of the SSH command to connect to our EC2 instance.

### Security groups

You can think of a security group as a collection of firewall rules around your EC2 instance. For example, by adding a security rule, you can allow applications running on your EC2 to accept HTTP traffic. You can create rules to allow access to specific TCP or UDP ports, and much more. 

Perfect; with this, our EC2 instance now allows HTTP access to applications running inside it.


### Simple Queue Service (SQS)

Let's start by discussing how to configure an SQS from the Amazon console. As usual, the first step is to log in to the Amazon console and then select our service from the main dashboard. The service name in this case will be called Simple Queue Service:

The queue creation page will offer us the ability to configure the behavior of the new queue. 

### DynamoDB 

DynamoDB is a distributed high-performance database hosted in the cloud, which is offered as a service by AWS. 


### Continuous Delivery

In this chapter, you will learn how to adopt continuous integration (CI) and continuous delivery (CD)

Since technologies such as Docker and Kubernetes are easily automated, they usually integrate very well with CD.

you will learn how to set up your project for adopting CI and CD (for example, by setting up proper version control and dependency management). We will also introduce a few popular tools that you can use to trigger new builds and releases automatically whenever your application's code changes.

•  Using dependency vendoring for reproducible builds
•  Using Travis CI and/or GitLab to automatically build your application
•  Automatically deploying your application to a Kubernetes cluster

### Vendoring your dependencies

To solve this issue, Go 1.6 introduced the concept of vendoring. Using vendoring allows you to copy libraries that your project requires into a vendor/ directory within your package

### Using Travis CI

It is free to use for open source projects, which, together with its good GitHub integration, makes it the go-to choice for many popular projects. For building private GitHub projects, there is a paid usage model.

The configuration of your Travis build is done by a .travis.yml file that needs to be present at the root level of your repository. Basically, this file can look like this:

### Deploying to Kubernetes

Using GitHub and Travis, we have now automated the entire workflow from changing the application's source code over building new binaries to creating new Docker images and pushing them to a container registry. That is great, but we are still missing one crucial step, that is, getting the new container images to run in your production environment.

First of all, Travis CI will need to access your Kubernetes cluster. For this, you can create a service account in your Kubernetes cluster. This Service Account will then receive an API token that you can configure as a secret environment variable in your Travis build. To create a service account, run the following command on your local machine (assuming that you have kubectl set up to communicate with your Kubernetes cluster):
$ kubectl create serviceaccount travis-ci

The preceding command will create a new service account named travis-ci and a new secret object that contains that account's API token.

You will need both the ca.crt and the token properties. Both of these values are BASE64-encoded, so you will need to pipe both values through base64 --decode to access the actual values:

After having kubectl configured, you can now add an additional step to the after_success command of both your projects:

### Using GitLab

GitHub and Travis are excellent tools for building and deploying open source projects (and also private projects if you do not mind paying for their services). However, in some cases, you might want to host your source code management and CI/CD systems in your own environment instead of relying on an external service provider.
This is where GitLab comes into play.

### Setting up GitLab

You can set up your own GitLab instance easily using the Docker images provided by the vendor. To start a GitLab CE server, run the following command:
$ docker container run --detach \
  -e GITLAB_OMNIBUS_CONFIG="external_url 'http://192.168.2.125/';" \
  --name gitlab \
  -p 80:80 \
  -p 22:22 \
  gitlab/gitlab-ce:9.1.1-ce.0

Note the GITLAB_OMNIBUS_CONFIG environment variable that is passed into the container. This variable can be used to inject configuration code (written in Ruby) into the container; in this case, it is used to configure the GitLab instance's public HTTP address. 

If you are setting up GitLab on a server for production usage (as opposed to on your local machine for experimentation), you might want to create two data volumes for configuration and repository data that you can then use in your container. This will allow you to easily upgrade your GitLab installation to a newer version later:
$ docker volume create gitlab-config
$ docker volume create gitlab-data

After creating the volumes, use the -v gitlab-config:/etc/gitlab and -v gitlab-data:/var/opt/gitlab flags in your docker container run command to actually use these volumes for your Gitlab instance.

The GitLab server running in the newly created container will probably take a few minutes to start up entirely. After that, you can access your GitLab instance at http://localhost:


For demo purposes, it is also alright to continue working as root.

### Setting up GitLab CI

While GitLab itself is responsible for managing your application's source code and deciding when to trigger a new CI build, the CI Runner is the component that is responsible for actually executing these jobs. Separating the actual GitLab container from the CI Runner allows you to distribute your CI infrastructure and have, for example, multiple runners on separate machines.

The GitLab CI Runner can also be set up using a Docker image. To set up the CI Runner, run the following command:
$ docker container run --detach \ 
    --name gitlab-runner \ 
    --link gitlab:gitlab \ 
    -v /var/run/docker.sock:/var/run/docker.sock \ 
    gitlab/gitlab-runner:v1.11.4

Finally, the artifacts property defines that the eventservice executable that was created by Go build should be archived as a build artifact. This will allow users to download this build artifact later. Also, the artifact will be available in later jobs of the same pipeline.

Right now, our pipeline consists of only one stage (build) with one job (build:eventservice). You can see this in the Stages column of the Pipelines overview. To inspect the exact output of the build:eventservice job, click on the pipeline status icon and then on the build:eventservice job:

Next, we can extend our .gitlab-ci.yml configuration file to also include the build for the booking service:

Luckily, GitLab CI offers a similar feature to Travis CI's secret environment variables. For this, open the project's Settings tab in the GitLab web UI, then select the CI/CD Pipelines tab and search for the Secret Variables section:

These steps are similar to our Travis CI deployment, which we configured in the preceding section, and require the environment variables $KUBE_CA_CERT and $KUBE_TOKEN to be configured as secret variables in the GitLab UI.

in the preceding section.
At this point, our GitLab-based build and deployment pipeline is complete. 

GitLab's pipeline feature is a nearly perfect solution for implementing complex build and deployment pipelines. While other CI/CD tools constrain you into a single build job with one environment, GitLab pipelines allow you to use an isolated environment for each step of your build, and to even run these in parallel if possible.

### Summary

In this chapter, you learned how to easily automate your application's build and deployment workflow. Having an automated deployment workflow is especially important in microservice architectures where you have many different components that are deployed often. Without automation, deploying complex distributed application would become increasingly tedious and would eat away your productivity.


Now that the deployment problem of our application is solved (in short, containers + continuous delivery),

This is why we need to monitor applications that are run in production environments. Monitoring enables you to track your application's behavior at runtime and note errors quickly, which is why the focus of the next chapter will be on monitoring your application.

### Monitoring Your Application

It is often used together with Grafana, a frontend for visualizing metrics data collected by Prometheus. 

You will learn how to set up Prometheus and Grafana and how to integrate them into your own applications.
In this chapter, we will cover the following topics:
•  Installing and using Prometheus
•  Installing Grafana
•  Exporting metrics to Prometheus from your own application

### Prometheus's basics

Unlike other monitoring solutions, Prometheus works by pulling data (called metrics in Prometheus jargon) from clients at regular intervals. This process is called scraping. 

Metrics exported to Prometheus by applications can get quite complex. For example, using labels and different metrics names, a client could export a histogram where data is aggregated in different buckets:

Do not worry, you will not need to build these complex histogram metrics by yourself; that's what the Prometheus client library is for. However, first, let's get started by actually setting up a Prometheus instance.

### Creating an initial Prometheus configuration file

Later, we will add more configuration items to the scape_configs property. 

### Running Prometheus on Docker

After having created the configuration file, we can use a volume mount to inject this configuration file into the Docker containers we are about to start.


In our configuration file, we have already configured our first scraping target—the Prometheus server itself. You will find an overview of all configured scraping targets by selecting the Status menu item and then the Targets item:

As you can see in the preceding screenshot, Prometheus reports the current state of the scrape target (UP, in this case) and when it was last scraped.


For this, PromQL offers the rate() method that calculates the per-second average increase of a time series. Try this out using the following expression:
rate(process_cpu_seconds_total[1m])

### Running Grafana on Docker

After this, you will be able to access Grafana in your browser on http://localhost:3000. The default credentials are the username admin and the password admin.

### Exporting metrics

For this reason, it is usually a good idea to use the Prometheus client library for Go, which takes care of collecting all possible Go runtime metrics.
As a matter of fact, Prometheus is itself written in Go and also uses its own client library to export metrics about the Go runtime (for example, the go_memstats_alloc_bytes or process_cpu_seconds_total metrics that you have worked with before).


### Using the Prometheus client in your Go application

For security reasons, we will expose our metrics API on a different TCP port than the event service's and booking service's REST APIs. Otherwise, it would be too easy to accidentally expose the metrics API to the outside world.

h.Handle("/metrics", promhttp.Handler()) 

Now, compile your application and run it. Try opening the address at http://localhost:9100/metrics in your browser, and you should see a large number of metrics being returned by the new endpoint:


Now, make the same adjustment to the booking service. Also, remember to add an EXPOSE 9100 statement to both service's Dockerfiles and to recreate any containers with an updated image and the -p 9100:9100 flag (or -p 9101:9100 to prevent port conflicts).

### Configuring Prometheus scrape targets

 can configure Prometheus to scrape these services. For this, we can modify the prometheus.yml file that you created earlier. Add the following sections to the scrape_configs property:


global: 
  scrape_interval: 15s 
 
scrape_configs: 
  - job_name: prometheus 
    static_configs: 
      - targets: ["localhost:9090"] 
  - job_name: eventservice 
    static_configs: 
      - targets: ["events:9090"] 

### Running Prometheus on Kubernetes

This works well for testing, but becomes tedious quickly in larger production setups (and completely pointless as soon as you introduce feature such as autoscaling).

Prometheus can automatically find all Pods that are managed by this Deployment and scrape them all. If the Deployment is scaled up, the additional instances will be automatically added to Prometheus.

To enable the automatic scraping of Pods, add the following section to the scrape_configs section of your prometheus.yml file in your ConfigMap:

Yes, this is quite a lot of configuration, but do not panic. Most of these configurations is for mapping the properties of known Kubernetes pods (such as the Pod names and labels defined by users) to Prometheus labels that will be attached to all metrics that are scraped from these Pods.

$ kubectl delete pod -l app=prometheus

annotations: 
        prometheus.io/scrape: true 
        prometheus.io/port: 9100 

### Summary

In this chapter, you learned how to use Prometheus and Grafana to set up a monitoring stack to monitor both your application's health on a technical level (by keeping an eye on system metrics, such as RAM and CPU usage) and custom, application-specific metrics, such as, in this case, the amount of booked tickets.

### Migration

In this chapter, we'll cover some practical techniques to migrate applications from monolithic architectures to microservice architectures. 

•  Techniques for migrating from monolithic applications to microservices applications
•  Advanced microservices design patterns
•  Data consistency in microservices architectures


### What are microservices?

In a microservice architecture, the tasks are distributed among multiple smaller software services, which are known as microservices. In a well-designed microservice architecture, each microservice should be self-contained, deployable, and scalable. 

A typical microservice contains multiple essential layers in the inside to handle logging, configuration, APIs to communicate with other microservices, and persistence. There is also the core code of the microservice, which covers the main task the service is supposed to do. The following is what a microservice should internally look like:


Microservice architectures have major advantages over monolithic applications when it comes to scalability and flexibility. Microservices allow you to scale indefinitely, utilize the power of more than one programming language, and gracefully tolerate failures.

### Humans and technology

•  Developers who are used to working in very specific pieces of the application in a single programming language and nothing else


•  Team leads who own a piece of an application as opposed to an entire software service from A to Z

•  Developers will need to be divided into smaller teams, where each team should be in charge of one or more microservices. Developers will need to be comfortable being in charge of an entire software service as opposed to a bunch of software modules or classes.

•  IT infrastructure teams will need to learn about horizontal scaling, redundancy, expandable cloud platforms, and the planning process involved with deploying a massive number of services distributed among numerous servers.

•  Team leads will carry the responsibility of an entire software service from A to Z. They will need to consider implementation details such as how to scale the service, whether to share a database with other services or have its own database, and how the service would communicate with other service.

### How do we break the code?

•  If an application is well-written, there will be clean and obvious separations between the different classes or software modules.  This makes cutting up the code an easier task.


•  On the other hand, if there are no clean separations in the code, we'll need to do some refactoring to the existing code before we can start moving pieces of code to new microservices.


•   It is typically preferred not to add new code or features to the monolithic application without trying to separate the new feature into a new microservice.

### Glue code

The glue code might be temporary or permanent, depending on our application. 

### Microservices design patterns

we will discuss some important design patterns and architectural approaches that can help us build robust and effective cloud-ready microservices. Let's get started.


### A four-tier engagement platform

•  The delivery layer: The delivery layer is responsible for delivering optimized data to the users as requested by the client layer. This is achieved by doing on-the-fly optimizations, such as image compression or bandwidth reduction.

The layer can make use of monitoring tools to track user activity, then utilize algorithms to use this information to deliver better customer experience. This layer is also where we would using caching algorithms and techniques to ensure better performance for our customers.  

•  The aggregation layer: This layer is where data from different sources is aggregated to form stable and uniform data models, which can then be handed over to the preceding layers. Tasks undertaken by this layer include the following:

### Bounded contexts in domain-driven designs

Domain-driven design (DDD) is a popular design pattern that we can use to internally design microservices. Domain-driven design typically targets complex applications that are likely to grow exponentially over time. 

The idea of domain-driven design is that a complex application should be considered to be functioning within a domain. A domain is simply defined as a sphere of knowledge or activity. The domain of our software application can be described as everything related to the purpose of the software. So, for example, if our software application's main goal is to facilitate planning social events, then planning social events becomes our domain. 

A domain contains contexts; each context would represent a logical piece of the domain, where people talk the same language. Language used within a context can only be understood based on the context from which it belongs.

The fact that the same language might mean different things in different contexts introduces one of the key ideas in the world of DDD, which is bounded contexts. Bounded contexts are contexts that share a concept, but then they implement their own models of that concept.

### Event-driven architecture for data consistency

A key design patterns that we can utilize to protect data consistency in a microservices architecture is an event-driven design. The reason why data consistency is difficult to maintain with microservices is that each microservice is typically responsible for a piece of data from the overall application. 

This event will also need to be communicated to the support microservice so that it can update its own database to reflect the fact that there is a new paying customer who deserves customer or technical support whenever needed. 

This kind of event communication between microservices is what event-driven design is about in the world of microservices. A message queue or a message broker between the microservices can be utilized to communicate event messages between microservices. 

Now, this all sounds good, but what if the support microservice fails before it can receive the new customer event? This means that the support service will end up not knowing about the new customer, and hence it will not apply any logic to add the relevant information about the new customer into the support database. 

 One approach would be to have a central database that stores customer data, which would be shared between different microservices, but what if we seek a flexible design where each  microservice is fully responsible for it's entire state. This is where the concepts of event sourcing and CQRS come into the picture.

### Events sourcing

The basic idea behind event sourcing is that instead of fully relying on a local database to read the state, we need to make use of a recorded stream of events to form the state. To make that work, we will need to store all current and past events so that we can retrieve them at a later time.

Let's say that the support service had failed and crashed before it could receive the new customer event. If the support service doesn't use event sourcing, then when it restarts, it will not find the customer information in its own database and will never know about the customer.

However, if it uses event sourcing, then instead of only looking at its local database, it will look at an event store which is shared with all the other microservices. The event store will record any event that was ever triggered between our microservices.

the support service will be able to replay the new customer event that got triggered recently and will see that this customer doesn't currently exist in the local support microservice database. The support service can then take this information and process it as normal. 

Again, a key trick for this design to work is to never discard any events, past or new. This is achieved by saving them in an event store; here is what this would look like:

There are multiple ways to implement an event store; it can be a SQL database, a NoSQL database, or even a message queue that supports having events saved forever. Kafka is an example of a message queue that claims to also be a good engine for event sourcing. 

### CQRS

A key advantage of CQRS is separation of concerns. That is because we separate write concerns from read concerns and allow them to scale independently. F

### Where to Go from Here?

•  Additional microservice communication patterns and protocols, such as Protocol Buffers and GRPC
•  More useful features offered by cloud providers
•  Other cloud providers (Azure, GCP, and OpenStack)
•  Serverless Computing

### Microservices communications

One advantage to this approach is that it empowers microservices to communicate with the outside world when needed, since HTTP is now a universal protocol that is supported by all software stacks out there. The disadvantage of this approach, however, is the fact that HTTP can be a heavy protocol with multiple layers, which may not be the best choice when the requirement is fast efficient communications between internal microservices.


•  The second approach is via message queues, where a message broker software such as RabbitMQ or Kafka will facilitate the exchange of messages between microservices. Message brokers receive messages from sending microservices, queue the messages, and then deliver them to microservices that previously indicated their interests in those messages.

However, if our scaling requirements are relatively simple in scope, this approach may be too complex for our needs. This is because it requires us to maintain a message broker software with all its configurations and backends. In those cases, direct microservice to microservice communication may be all what we need.


There are two popular choices for technologies that we can employ for direct microservices communications: Protocol buffers and GRPC.

### Protocol buffers

In their official documentations, protocol buffers are defined as a language-neutral, platform-neutral mechanism for serializing structured data. Let's take a look at an example to help build a clear picture of what protocol buffers are.

Inside service 1, protocol buffers will take the customer object, then serialize it into a compact form. From there, we can take this encoded compact piece of data and send it to service 2 via an efficient communications protocol such as TCP or UDP.

Note that we described protocol buffers as inside the service in the preceding example. This is true because protocol buffers come as software libraries that we can import and include in our code. There are protocol buffer packages for a wide selection of programming languages (Go, Java, C#, C++, Ruby, Python, and more).

The way how protocol buffers work is as follows:
1.  You define your data in a special file, known as the proto file.
2.  You use a piece of software known as the protocol buffer compiler to compile the proto file into code files written in the programming language of your choice.
3.  You use the generated code files combined with the protocol buffer software package in your language of choice to build your software.


There are currently two commonly used versions for protocol buffers: protocol buffers 2 and protocol buffers 3.

### GRPC

One key feature missing from the protocol buffers technology is the communications part. Protocol buffers excel at encoding and serializing data into compact forms that we can share with other microservices.

However, what if we can't spare the time and effort to worry about an efficient communication layer? This is where GRPC comes into picture.

GRPC can simply be described as protocol buffers combined with an RPC layer on top. 

A Remote Procedure Call (RPC) layer is a software layer that allows different piece of software such as microservices to interact via an efficient communications protocol such as TCP. With GRPC, your microservice can serialize your structured data via protocol buffers version 3 and then will be able to communicate this data with other microservices without you having to worry about implementing a communications layer. 

If your application architecture needs efficient and fast interactions between your microservices, and at the same time you can't use message queues or Web APIs, then consider GRPC for your next application.


### DynamoDB streams

In practice, this means that we can react to data changes that happen to the database in real time. As usual, let's take an example to solidify the meaning.


The way DynamoDB streams work is simple. They capture changes that happen to a DynamoDB table item in order. The information gets stored in a log that goes up to 24 hours. Other applications written by us can then access this log and capture the data changes. 


In other words, if an item gets created, deleted, or updated, DynamoDB streams would store the item primary key and the data modification that occurred. 

### Autoscaling on AWS

In the world of AWS, the word autoscaling means three main things:
•  The ability to automatically replace unhealthy applications or bad EC2 instances without your intervention.


•  The ability to automatically create new EC2 instances to handle increased loads on your microservices application without your intervention. Then, the ability to shut down EC2 instances when the application loads decrease.

•  Fleet management for EC2 instances: This feature allows you to monitor the health of running EC2 instances, automatically replaces bad instances without manual intervention, and balances Ec2 instances across multiple zones when multiple zones are configured.

For example, monitor CPU utilization or capture the number of incoming requests. Then, the dynamic scaling feature can automatically add or remove EC2 instances based on your configured target limits. 

### Amazon Relational Database Service

Amazon Relational Database Service (RDS). AWS RDS allows developers to easily configure, operate, scale, and deploy a relational database engine on the cloud. 

### Other cloud providers

Besides these, there are many other providers that also offer IaaS solutions, more often than not based on the open source platform OpenStack.


### Google Cloud Platform

•  Relational databases are provided by the Cloud SQL service. GCP supports both MySQL and PostgreSQL instances.
•  For NoSQL databases, you can use the Cloud Datastore service.
•  The Cloud Pub/Sub service offers the possibility to implement complex publish/subscribe architectures (in fact, superceding the possibilities that AWS offers with SQS).


### Summary

By now, you should have enough knowledge to build sophisticated microservices cloud native applications that are resilient, distributed, and scalable. With this chapter, you should also develop ideas of where to go next to take your newly acquired knowledge to the next level. 