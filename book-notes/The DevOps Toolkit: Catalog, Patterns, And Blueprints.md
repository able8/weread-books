## The DevOps Toolkit: Catalog, Patterns, And Blueprints
> Viktor Farcic and Darin Pope

### Title Page

The DevOps Toolkit: Catalog, Patterns, And Blueprints Viktor Farcic and Darin Pope This book is for sale at http://leanpub.com/the-devops-toolkit-catalogThis version was published on 2020-12-30

### Introduction

Instead of going to great lengths trying to help someone become proficient in one thing, this time, I am trying to give you a quick introduction into many different tools and processes. 

I will assume that you don’t have time to read hundreds of pages to learn something that you are not even sure is useful. Instead, I will guess that you got up to one hour to read a summary, and then decide if a tool is worthwhile a more significant investment.

This is a catalog of the tools, and the processes I believe are useful in this day and age.

I always believed that the best way to learn something is through practice, and I am not giving up on that. This is a book full of real-world hands-on examples, and each chapter will let you dive into a different tool or a process. At the end of each, you will be able to say, “now I know what this is about, and now I can make a decision whether it is a worthwhile investment.”

The critical thing to note is that I want to keep it alive and to keep adding tools. But, I will do that only if you tell me to. So, it’s up to you to let me know what to add. And, to do that, you will need a way to contact me. So, here it goes.

If you prefer a more one-on-one conversation, you can use Slack to send me a private message 

I currently work in Codefresh. However, things are changing and, by the time you are reading this, I might be working somewhere else. Change is constant, and one can never know what the future brings. At the time of this writing, I am a Principal DevOps Architect.


You can probably guess from those that I am focused on containers, Cloud, Kubernetes, and quite a few other things.

 am very passionate about DevOps, Kubernetes, microservices, continuous integration, and continuous delivery, and quite a few other topics. I like coding in Go.

Whether it is figuring out the latest changes with Kubernetes or creating more content to share with our listeners and viewers, I’m always learning. Always have, always will.

You will find the requirements near the beginning of each chapter. They vary from one subject to another, and the only constants are a laptop, Git, and a Bash terminal.

Every chapter starts from scratch and ends with the destruction of everything we created. That way, you should be able to go through any chapter independently from others. Each is self-sufficient. 

All in all, this is the beginning of an adventure. I do not know where I am going. All I know is that I hope to enjoy the journey and that you will find it useful to retrace the steps I will be taking.

### Infrastructure as Code (IaC)

Infrastructure as Code (IaC)
You might have already used one of the tools to manage your infrastructure as code. That might have been Terraform, or something else.

If that’s the case, you might be wondering if there is anything wrong with creating your cluster through a browser-based console provided by your hosting vendor. The short answer is “yes”. It’s very wrong.

Such actions result in undocumented and unreproducible processes. You surely want to document what you did so that you can refer to that later. 

you’ll quickly find out that it is easier to write something like “execute aws ...” than to write pages filled with “go to this page”, “fill in this field with that value”, “click that button”, and similar tedious entries that are often accompanied with screenshots. 

While those options are better than the click-click-click type of operations, those are also not good options.

They cannot show us which changes will be applied before we execute a command. 

If you’ve been in this industry for a while, you probably know that it was common to just create infrastructure. One of the sysadmins would get a server and install and configure everything.

That process could take anything from hours to days or even months. 

Everything took so long that we’d order more than we needed, just to avoid a long waiting time when our needs would increase. It was a mess, and it was painful.

Over time, people realized that it is a horrible idea to SSH into a server and “fix it”. We had undocumented and unreliable infrastructure, so we thought that it would be a good idea to document everything.

 the documentation was accurate, while, in reality, it only provided a false sense of confidence. 

Then we moved into scripts. Instead of reading some instructions and copying and pasting commands and configurations, we’d define (almost) everything as scripts.

But that was a failure as well. Scripts were not idempotent. They would work only on “virgin” servers. They were suitable for installing something, but not for upgrading.

If this does not exist, then do that. If that does exist, and if it looks like this, then do something else. That was painful as well, and it wasn’t much help.

that maintaining such scripts was harder than SSH-ing into a server and doing everything manually.

Over time, we adopted configuration management tools like CFEngine, Chef, Puppet, Ansible, and a few others. They allowed us to describe the desired state, and that was a game-changer.

Those tools would check the actual state in each of the servers, and, depending on the differences between the two states (desired and actual), they would install, upgrade, reconfigure, or delete resources.

We moved our workloads into virtual machines, and that simplified quite a lot of things. At the same time, the adoption of configuration management tools increased. We thought that the two were a good match. We thought we had a winner, but we were wrong.

They all tried to modify the state of servers at runtime. While that’s the best we can do when using physical servers, that goes against one of the main benefits of virtualization.

When virtual machines appeared, they provided quite a few benefits. We could split physical servers into virtual machines, and that would give us better utilization of resources, as well as separation required by different workloads. 

The ability to easily create or destroy a VM allowed us to move towards immutable infrastructure. Instead of trying to converge the actual into the desired state by changing what’s running inside the servers, we could adopt the principles of immutability

Whenever we needed to apply a change, we could create a new image. Instead of modifying servers from inside, we could converge the actual into the desired state by replacing the old VMs with new ones based on different images. 

When we want to upgrade an application, we do not upgrade binaries inside a container. Instead, we build a new image and replace the existing containers with new ones based on that image. The same is valid for infrastructure. 

we tend to create a new VM image, and we do rolling upgrades of the nodes.

That allows us to have uniform and reliable servers, while also increasing the velocity of the process.

IaC allows us to define everything as code, while immutability brings uniformity, reliability, and speed. Servers are defined as code in the form of configurations and scripts used to create VM images. Clusters are defined as code in the form of instructions on how to create and manage VMs based on those images, and how to tie them together through different services.

We can think of them as the first generation of such tools. Even though some are newer and more advanced than others, I’m putting them in the same bucket since they are all based on the same principles. Today we have better options at our disposal.

We could adopt vendor-specific tools like CloudFormation for AWS, or whatever your hosting vendor provides.

Today’s “king of infrastructure as code” is Terraform. It is designed from ground up as an immutable infrastructure as code solution. It is, by far, the most widely used, and almost every respectable service vendor created modules for their platforms. 

Everyone that matters is there. If your provider is not on that list, you should not look for a different tool, but rather change the provider. 

Terraform’s ability to use different providers and manage various resources is combined with templating.

The feature I like the most is Terraform’s ability to output a plan. It shows us which resources will be created, which will be modified, and which will be destroyed if we choose to apply the changes in the definitions. 

Nevertheless, storing its state locally is insecure, and it could prevent us from working as a team. Fortunately, Terraform allows us to utilize different backends where its state can be stored. Those can be a network drive, a database, or almost any other storage.

It is not an accident that it is the de facto standard and popular choice of many.

The goal is to provide you with quick know-how and allow you to make a decision whether a tool or a process is the right choice for your needs. So, we’ll dive straight into Terraform hands-on exercises. 

Even if you are already familiar with Terraform, you might discover a few new things, like, for example, the proper way to manage its state.

The primary goal is to dive into Terraform.

Besides the obvious need to have terraform CLI, you’ll also need to install and set up kubectl.

### Creating And Managing Google Kubernetes Engine (GKE) Clusters With Terraform

That will help you make a decision whether Terraform is the right choice for managing resources in Google Cloud Platform (GC) and give you sufficient base knowledge that you’ll be able to extend on your own.

The code and the configurations that will be used in this chapter are available in the GitHub repository vfarcic/devops-catalog-code. Let’s clone it.

The code for this chapter is located in the terraform-gke directory.

we’ll focus on variables, and leave the rest for later.

The output is as follows

To be more precise, we’ll need to create a project and a service account with sufficient permissions. But, before we do that, I need to make sure that you are registered and logged in Google Cloud Platform, and that you have gcloud CLI.


please make sure that you have the permissions to create all the resources we’ll use in the exercises that follow.


A new page should have opened in the browser asking for authentication. Follow the instructions.

If you’re new to Google Cloud, you should know that everything is organized in projects. They provide means to group resources. 

You might have other projects listed in that output. They do not matter in this context. As long as the project is there, we should be able to proceed.

The output should be similar to the one that follows.

As you can probably guess from the name, an owner can do anything withinthat project. That is too permissive, and we should have fine-tuned it by assigning a rolewith the minimum set of permissions. Or, we could have assigned multiple less permissiveroles. 

Being an owner capable of doing anything is easier, but is it also less secure.

Those that start with TF_VARwill be automatically converted into Terraform variables. So, in this case, the value of thatenvironment variable will be used as the value of the Terraform variable project_id. 

It allows us to configure the credentials used to authenticate with GCP, and a few other things.

We’re specifying the credentials, and the project and region where we’d like to create a cluster.

The other two fields (project and region) are using variables project_id and region.

In Terraform terms, apply means “create what’s missing, update what’s different, and delete what’s not needed anymore.”

1 terraform apply

Terraform will do the job of downloading the plugin. We just need to initialize the project.

1 terraform init

Terraform maintains its internal information about the current state.

Keeping Terraform’s state locally is a bad idea. If it’s on a laptop, we won’t be able to allow others to modify the state of our resources. 

But, before we proceed, I want you to confirm that billing for storage is enabled. Otherwise, Google would not allow us to create one.


Finally, we are asked whether we want to perform these actions?. Be brave and type yes, followed with the enter key.

Upgrading The Cluster
Changing any aspect of the resources we created is easy and straight forward. All we have to do is modify Terraform definitions, and apply the changes.

Type yes and press the enter key.
It will take a while until the rest of the process is finished. It is performing the rolling upgrade by draining and shutting down one node at a time and creating new ones based on the newer version.

What makes Terraform unique is its dependency management. No matter how we organize definitions of different resources, it will figure out the dependency tree, and it will create, update, or delete resources in the correct order.

### Packaging, Deploying, And Managing Applications

Containers do not scale by themselves, they do not enable external communication, and they do not magically attach external volumes. Thinking that all we need is a command like docker container run is a misunderstanding, at best.

We often need to replicate the application in an attempt to make it highly available. We might need to define when and how it should scale up and down. We might need to attach external storage. We might need to provide it with environment-specific configuration and, potentially, inject a secret or two. The list of “we might need to” type of things can be quite large.

There was a war, and they all lost. The one that matters today (May 2020) is Kubernetes. It is the de facto standard, and it allows us to define everything we need for an application in YAML format. But that is also not enough.


We need some form of templating so that the differences can be defined and modified effortlessly, instead of constantly modifying endless lines in YAML files. That’s where Helm comes in.

While there are quite a few tools we can use to package, install, and manage applications inKubernetes, none is as widely used as Helm. It is the de facto standard in the Kubernetesecosystem.

It organizes packages into charts. We can create them from scratch with helm create command, or we canconvert existing Kubernetes definitions into Helm templates.

instead of hard-codedvalues. 

 It accomplishes that by allowing us to define different properties of ourapplication running in different environments. It does that through the ability to overwrite default values, which, inturn, modify the definitions propagated to Kubernetes. As such, it greatly simplifies the process of deployingvariations of our applications to different environments, which can be separate Namespaces or even otherclusters.

Once a chart is packaged, it can be stored in a registry and easily retrieved and used by people and teams thathave sufficient permissions to access it.

It is a helpful and easy to learn tool thatsolves quite a few problems that were not meant to be solved with Kubernetes alone.

What matters is that applications in different environments tend to have different needs.

Let’s move into permanent environments. The domains could be fixed to staging.go-demo-9.acme.com whenrunning in staging, and go-demo-9.acme.com when in production. Those are permanent environments, so thedomains can be permanent as well.

Now, let us see whether we can package, deploy, and manage our application in a way that fulfills thoserequirements. But, first, we need to make sure that we have the prerequisites required for the exercises thatfollow.

. Feel free to use it if you’re too lazy totype. There’s no shame in copy & paste.

The code and the configurations that will be used in this chapter are available in the GitHub repositoryvfarcic/devops-catalog-code. Let’s clone it.

For your convenience, I created scripts that will create a Kubernetes cluster in the flavors I mentioned. All youhave to do is follow the instructions from one of the Gists that follows.

You will also need Helm CLI. If you do not have it already, please visit the Installing Helm page and follow theinstructions for your operating system.

Helm uses a packaging format called charts. A chart is a collection of files that describe a related set ofKubernetes resources. Helm relies heavily on naming conventions, so charts are created as files laid out in aparticular directory tree, and with some of the files using pre-defined names.

But that’s not our current focus. We’ll explorepackaging later. For now, our goal is to create a chart.

More often than not, theyshould be stored in the same repository as the application it defines. 

Chart.yaml contains meta-information about the chart. It is mostly for Helm’s internal use, and it does notdefine any Kubernetes resources.

The version field is mandatory, and it defines the version of the chart. The appVersion, on the other hand, isoptional, and it contains the version of the application that this chart defines. What matters is that both must usesemantic versioning 2.

The _helpers.tpl field defines “template” partials or,to put it in other words, functions that can be used in templates. We’ll skip explaining both by giving you thehomework to explore them later on your own.

If you ignore the entries surrounded with curly braces ({{ and }}), that would be a typical definition of aKubernetes Deployment. The twist is that we replaced parts of it with variables, functions, and conditionals, withvalues and functions surrounded by curly braces. 

In other words, those templateswill be converted into typical Kubernetes YAML files.

Templating itself is a combination of Go template language and Sprig template library. It might take a while tolearn them both, but the good news is that you might never have to go that deep. Most of the Helm definitionsyou will find, and most of those you will define will use a few simple syntaxes.

While at the subject of Git, we should keep Helm charts close to applications, preferably in the same repositorywhere the code and other application-specific files are located. 

Finally, we should not run helm commands at all, except, maybe, while developing. They should all be automatedthrough whichever continuous delivery (CD) tool you’re using. The commands are simple and straightforward, soyou shouldn’t have a problem extending your pipelines.

Finally, let’s get out of the local repository, and get back to where we started.

### Setting Up A Local Development Environment

This time, we will create a productive local development environment.

 To be more precise, bash is a superset of sh. It adds additional features that we will not have time to discuss, especially since it is not the option we’ll choose. Instead, we’ll go with zsh.
Just like bash, Z Shell (zsh) is a superset of sh. However, it brings a few additional features that bash does not have.

Oh My Zsh is an open-source community-driven framework for managing zsh configuration. It makes things simple with fantastic out-of-the-box experience, which can be fine-tuned to our liking.

We have zsh set up and managed with Oh My Zsh. It will be our Shell of choice, and we’ll explore it soon. 

I have two monitors, and one always has Visual Studio Code occupying the whole screen. It allows me to browse files, to edit whatever I need editing, and it contains a terminal that I use to run whichever commands I need to execute.


Visual Studio Code provides everything I need to develop software, operate my systems, and do whatever else engineers are doing.

In this case, we are trying to redefine the default Shell.

Installing a plugin or, to be more precise, downloading it is not enough. We need to tell zsh that we want to use it.

Almost all Shells have an rc file that defines the variables, init processes, and everything else we need when a Shell starts. Z Shell is not an exception. If we want to add plugins installed in Oh My Zsh, we need to change the .zshrc file.

Please consider watching one of the videos that follow, and bear in mind the list will likely grow over time.

### Exploring Serverless Computing

Serverless computing is the future. Or, maybe, it’s just the hype. It’s hard to tell right now, mostly because we do not yet know what serverless is. For some, it is about developing functions (nano services?). For others, it is about not worrying about infrastructure. Some claim that serverless is all about reducing cost, while some say that it is about scalability.

For a user, it is as if servers do not exist. Hence, it is serverless from the user’s perspective. But that is only part of the story. It’s not only about infrastructure, but the whole management of our applications.

I tend to think about serverless as “my job is to write code, everything else should just happen.” That is certainly a worthy goal. If we can get that far, we can enable developers to focus on writing code and assume that everything else is just working. But such attempts are not new. In the past, we could accomplish that goal in many different ways.

We give code (or binaries) to someone, and that someone (or something) ensures that it is running, that it is highly available, that it is responsive, and so on.

•  Containers as a Service (CaaS)


What matters for FaaS is that there are severe limitations. It needs to be tiny (a function), it cannot run forever, it might need to be stateless, and so on and so forth. 

It will be up to you to decide whether FaaS makes sense for your use cases.

Stateless is better than stateful. Those that initialize faster are better than those that are slow to boot. And so on and so forth.

We will split the solutions into Functions as a Service (FaaS) and Containers as a Service (CaaS). The problem is that those are often mixed, so I’ll make an easy distinction between the two. The difference is in the form of the deliverables we need to provide to a service. They can be code or binaries, and, in that case, we will call those FaaS.

Another distinction we will make is whether you or someone else is in charge of maintaining the platform. We can choose a managed solution from one of the vendors (e.g., AWS, Azure, Google Cloud) or assemble and manage it ourselves as part of our platform of choice. 

All in all, we will split serverless computing into FaaS and CaaS and managed and self-managed. You are likely to choose one from both groups, and that will give you a clearer indication of what would be the right solution for you.

Still, the best way to know that is to learn more about serverless.

We’ll start with managed Functions as a Service or simply managed FaaS.

### Using Managed Functions As A Service (FaaS)

The output is uneventful, so we’ll skip commenting on it.

 The function responds with Hello World!. It could hardly be anysimpler than that.

The serverless.yml file is the key. It defines everything serverless needs to deploy and manage ourfunctions.

We can retrieve almost any information related to the functions in a project by outputting the info.[插图]The output is as follows.

They are smaller applications that areloosely coupled, independently deployable, organized around business capabilities, and owned by small teams. 

they might keep your functions “warm” for a while. Nevertheless, all that only improved the situation, but did notentirely remove the issues with initialization.

But, if we do have constant usage ofa resource (e.g., CPU), that is usually much cheaper without FaaS. Under certain conditions, the difference can beexpressed with a multiplier of five, ten, or even more.

Another potentially problematic issue is related to vendor lock-in. How important is it to avoid being locked into asingle provider? 

Think twice before using a language that needs significant time to initialize processes. In a “normal” situation,waiting for a second for a Java or C# process to start might be okay. 

I prefer using JavaScript, Python, and Go for functions. They start fast and work well on a small codebase. 

Go ahead. Ping me, send me an email, contact me on Slack, or Tweet. I 

Personally, I do not think that managed Functions as a Service are a good idea. Functions are too small for mytaste. The execution model in which each request is served by a fresh instance is deeply flawed. The pricing istoo high for my budget.

 Lambda is to serverless what Docker is to containers.Both brought the implementation of existing concepts to mainstream.

Use FaaS if you have a good use case for it. But limit your usage of it only to “special” cases. Do not go crazyand convert everything into functions. If you are eager to use serverless deployments, give me a bit more time tointroduce you to other options before deciding which is the right one for you, if any.

All in all, I do believe that the future is in Serverless computing. However, Functions as a Service (FaaS) is not it.FaaS is not going to be the most commonly used nor the best serverless flavor. 

### Using Managed Containers As A Service (CaaS)

Serverless is the future!You probably heard me repeat that sentence quite a few times. However, you also probably heard me saying thatmanaged Functions as a Service (FaaS) is not it. Or, at least, it is unlikely to represent a significant percentageof your workload.

In a “traditional” Cloud setup, we pay our vendor for virtual machines, storage, networking, and quite a few otherthings. It does not matter whether someone is using those. We are paying for all the resources we create. To bemore precise, we pay for every minute of the existence of those resources, and it is our responsibility to shutdown things we do not use. Therefore, we pay for what we use. 

We are responsible for scaling servers and applications, and it is in our interest to always have justthe right amount of resources. We need to pay for idle VMs. It does not matter if none of our users are usingresources.

. Our service provider mightkeep them “warm”. From our perspective, they do not exist or, to be more precise, we do not pay for them. 

No need to manage infrastructureOut-of-the-box scalability and high-availability“Pay what your users use” model

 I believe that theability to work locally is still essential. That might change in the future, but that future is still not here.

The primaryquestion is whether that should be done by executing docker-compose, kubectl, or something else. 

It should allow local development

Google Cloud Run is a fully managed compute platform for deploying and scaling containerized applicationsquickly and securely.Since it uses container images, we can write code in any language we want. We can use any application serverand any dependencies. Long story short, if an application and everything it needs can be packaged into acontainer image, it can be deployed to Google Cloud Run. 

It has integrated logging, monitoring, and strictisolation. The pricing is based on pay-per-use.

The best part of Google Cloud Run is that it is based on Knative. It is an open-source project that aims atbecoming a standard for running serverless applications in Kubernetes. As a result, using Google Cloud Runsimplifies accomplishing objectives that we could do without it as well.

As such, we might not be locked into the service nearly as much as with other managedserverless solutions, especially FaaS.

But, in the case of Knative, it is much more than that. It aims todefine a standard and become the de facto implementation of serverless deployments in Kubernetes.

We can define Cloud Run as YAML. To be more precise,Cloud Run uses Knative, which is a Kubernetes Custom Resource Definition (CRD). Instead of using gcloudcommands to create or update Cloud Run apps, we can define them as Knative YAML. 

That is, actually, a preferable method since we couldstore the definitions in a Git repository, and hook them into whichever CI/CD solution we are using. 

Now that we explored managed Containers as a Service in the three major Cloud computing providers, we shouldprobably comment on what we learned and try to compare them. Stay tuned.

It scales to zero when not in use, so it does adhere to the “pay what your users use”model. Google Cloud Run is, without doubt, the best of the three.

I am confident that the future of serverless computing is in Containers as a Service. The ability to deploy anycontainer image combined with letting compute providers handle infrastructure, scaling, and other demandingtasks is the winning combination. It allows us to focus on our business goals while letting others handle the restof the work.

 New solutions will come, and the existingones will improve. That much is certain.

### Using Self-Managed Containers As A Service (CaaS)

All those solutions were managed CaaS. They areprovided and managed by other vendors. When using them, we do not need to think about the underlyinginfrastructure, services, plumbing, scaling, and other things not directly related to developing applications. 

The self-managed serverless computing solution we should use should be simple. It should not require muchmore than a container image and a few essential arguments or parameters. It should scale automatically, even tozero replicas, if there is no traffic.

Knative is a platform sitting on top of Kubernetes. It tries to help with the deployment and management ofserverless workloads.

It is split into two major components; Serving and Eventing. For our purposes, we’ll focus on Knative Serving, andleave Eventing for some other time. 

A short version of the description of Knative would be that it provides means to run serverless workloads inKubernetes. We can qualify it as a self-managed serverless solution. It allows anyone to run applications asContainers as a Service, except that it is not a service managed by a vendor. It is a solution for self-managedserverless deployments of applications packaged as container images.

it is becoming the de facto standard for serverless computing,

If I had to make a bet on which solution will become the de facto standard for serverless workloads, it would beKnative. Time will tell whether I’m right. But, for now, that seems like the safest bet we could make.

 believe that practice is the best way to learn something and that there is no better way to evaluate the feasibility of using something than to use it.

The initial installation of Knative is straightforward. All we have to do is apply YAML definitions that will create Custom Resource Definitions (CRDs) and deploy the core components. 

We’ll explore some of those Pods later on. For now, the important thing is to confirm that they are all running. If one of them is still pending, please wait for a few moments, and repeat the previous command.

Knative depends on a service mesh for parts of its functionality. 

Given that this is not a section dedicated to service mesh, we’ll choose Istio since it is the most commonly used.Functionally, Knative works the same no matter which service mesh we use, so the choice of using Istio shouldnot result in a different experience than using any other.

Now that we have the YAML definition and istioctl, we can finally deploy the Istio operator, which, in turn, will deploy and configure everything we need.

It will take a few moments until everything is up and running. Be patient.

We should enable mutual TLS (mTLS) so that Knative and Istio can communicate securely. Fortunately, that is very easy. All we have to do is add the label istio-injection with the value enabled to the knative-service Namespace.

Now that we have both Istio and Knative installed and configured, we need to figure out through which address we’ll be able to access the application we will deploy. 

Finally, to be on the safe side, let’s confirm that all the Pods related to Knative are indeed running.

From the external LB, requests are forwarded to the cluster and picked up by the Istio Gateway. Its job is to forward requests to the destination Service associated with our application. 

Figure 4-3-1: Flow of a request from a user to the Istio Gateway

You might receive an error message similar to RevisionFailed: Revision "devops-toolkit-...-1" failed with message: 0/3 nodes are available: 3 Insufficient cpu. If you did, your cluster does not have enough capacity. If you have Cluster Autoscaler, that will correct itself soon. 

For now, I hope you already saw that simplicity is one of the enormous advantages of Knative, even without diving into the part that makes our applications serverless.

Knative detected that no one was using our application for a while and decided that it is pointless to keep it running. That would be a massive waste of resources (e.g., memory and CPU). As a result, it scaled the app to zero replicas. Typically, that would mean that our users, when they decide to continue interacting with the application, would start receiving 5XX responses. That’s what would usually happen when none of the replicas are running.

Knative is a solution for serverless workloads, and, as such, it not only scales our application, but it also queues the requests when there are no replicas to handle incoming requests. Let’s confirm that.


As you can see, the application is available. From the user’s perspective, it’s as if it was never scaled to zero replicas.


When we sent a request, it was forwarded to the Ingress Gateway. But, since none of the replicas were available, instead of forwarding it to the associated Service, it sent it to Knative Activator.

Once a Pod was operational, it forwarded the queued requests to the Service, and we got the response.
The Autoscaler knew what to do because it was configured by the PodScaler created when we deployed the application.

In our case, only one Pod was created since the amount of traffic was very low. If the traffic increased, it could have been scaled to two, three, or any other number of replicas. The exact amount depends on the volume of concurrent requests.

Maintaining a system created through ad-hoc commands is a nightmare. 

But you already know that. You already understand the benefits of defining everything as code, storing everything in Git, and reconciling the actual and the desired state. I’m sure that you know the importance of the everything-as-code approach combined with GitOps.

The containerConcurrency field is set to 100, meaning that, in a simplified form, there should be one replica for every hundred concurrent requests, while never going above the maxScale value.

We created a single resource. We did not specify a Deployment, nor we created a Service. We did not define a HorizontalPodAutoscaler. We did not create any of the things we usually do. Still, our application should have all those and quite a few others. It should be fully operational, it should be scalable, and it should be serverless. Knative created all those resources, and it made our application serverless through that single short YAML definition. That is a very different approach from what we typically expect from Kubernetes.

An application runs in Pods, Pods need ReplicaSets to scale, ReplicaSets need Deployments for applying new revisions. Communication is done through Services. External access is provided through Ingress. And so on and so forth. 

It provides a layer on top of Kubernetes that, among other things, aims to simplify the way we define our applications.


You already saw a similar result before. The major difference is that, this time, we applied a YAML definition instead of relying on kn service create to do the work. As such, we can store that definition in a Git repository. We can apply whichever process we use to make changes to the code, and we can hook it into whichever CI/CD tool we are using.


The Configuration resource contains and maintains the desired state of our application. Whenever we change Knative Service, we are effectively changing the Configuration, which, in turn, creates a new Revision.

Each has a separate Service, a Deployment, a Knative PodAutoscaler, and, potentially, a few other resources. Creating revisions allows Knative to decide which request goes where, how to rollback, and a few other things.

Knative decided that there is no point running it. So, it scaled it to zero replicas. As you already saw, it will scale back up when we start sending requests to the associated Service. Let’s take a look at them as well.


We can see that Knative created Kubernetes Services, but also Istio VirtualServices. Since we told it that we want to combine it with Istio, it understood that we need not only Kubernetes core resources, but also those specific to Istio. If we chose a different service mesh, it would create whatever makes sense for it.

PodAutoscaler is, as you can guess by its name, in charge of scaling the Pods to comply with the changes in traffic, or whichever other criteria we might use. By default, it measures the incoming traffic, but it can be extended to use formulas based on queries from, for example, Prometheus.


We saw that when we do not interact with the app, it is scaled down to zero replicas. We also saw that when we send a request to it, it scales up to one replica. But, what would happen if we start sending five hundred concurrent requests? Take another look at devops-toolkit.yaml and try to guess. It shouldn’t be hard.

We’ll use Siege to send requests to our application. To be more specific, we’ll use it to send a stream of five hundred concurrent requests over sixty seconds. We’ll also retrieve all the Pods from the production Namespace right after siege is finished “bombing” the application.

Please execute the command that follows if you are using minikube or EKS.

1 kubectl run siege \
2     --image yokogawa/siege \
3     --generator run-pod/v1 \
4     -it --rm \
5     -- --concurrent 500 --time 60S \
6     --header "Host: devops-toolkit.production.example.com" \
7     "http://$INGRESS_HOST" \
8     && kubectl --namespace production \
9     get pods

3 Availability:            100.00 %!
(MISSING) 4 Elapsed time:             59.53 secs
 5 Data transferred:         83.72 MB
 6 Response time:             0.22 secs
 7 Transaction rate:        683.64 trans/sec
 8 Throughput:                1.41 MB/sec
 9 Concurrency:             149.94

But we also set the maxScale annotation to 3, so it reached the limit of the allowed number of replicas.


It probably scaled it to one replica, to keep it warm in case new requests come in. After a while, it should scale down to nothing (zero replicas) as long as traffic keeps being absent.

It changes things gradually, and it uses both current and historical metrics to figure out what to do next.

This chapter’s goal was to introduce you to Knative so that you can see whether it is a tool worth investing in. I tried to provide as much essential information as I could while still being quick and concise. If you think that Knative is the right choice for some, if not all of your applications, please visit its documentation and start digging deeper.


Destroying The Resources
We are finished with our quick overview of Knative, and all that’s left is to remove the application.

Please note that we did not uninstall Knative and Istio. I assumed that you used a throw-away cluster for the exercises and that there is no need to provide the commands to remove them.


Now, if you do have to run a Kubernetes cluster, the overhead of using self-managed CaaS like, for example, Knative, is negligible. 

Most of the work involves managing infrastructure and the cluster as a whole. If you are using Kubernetes, you have that overhead, no matter whether you are using Knative, Google Cloud Run, or something completely different.


That is not to say that there are no benefits in using managed CaaS if we already have a Kubernetes (and we want to keep it). But there are other potential reasons. Integrating self-managed serverless CaaS workload with the rest of the system tends to be easier if everything is in one place. 

The good news is that deploying applications as Containers as a Service is, most of the time, simple and straightforward. There is very little code involved, so switching from one solution to another is not a big deal. For example, you could use Knative in your Kubernetes cluster and later switch to Google Cloud Run while using almost the same YAML definitions. That’s the advantage of using a service from a vendor (e.g., Google Cloud Run) that relies on an open-source project (e.g., Knative).

### Using Centralized Logging

More importantly, everything became dynamic. Nodes are being created and destroyed. Westarted scaling them up and down. We began using schedulers, and that means that our applications started“floating” inside our clusters. Everything became dynamic and volatile. 

As a result, we got “log collectors” that wouldgather logs from all the parts of the systems, and ship them to a central database.Centralized logging became the de-facto standard and a must in any modern infrastructure.

There are many managed third party solutions like Datadog, Splunk, and others. 

When it comes to self-hosted options, ELK stack (ElasticSearch, LogStash, and Kibana)became the de-facto standard. Elasticsearch is used as the database, Logstash fortransferring logs, and Kibana for querying and visualizing them.

I’m a DevOps engineer at Digitalpine

 I’m fascinated by ever-growing Cloud Native Landscape and continuouslylooking for tools, technologies, and practices that would make my life easier and provide myteam with a more robust, more efficient, and worry-free experience. 

It handles complex tasks, resulting in a CPU and memoryintensive solution. 

logging requirement is just a “distributed grep”. There was increasing demand for suchtools and several attempts at creating distributed “grep-style” log aggregation platforms.Many failed due to lack of “their own Kibana”, even when aggregation and storage weredone right, but querying and visualization were lack-luster or completely absent.

All in all, the ELK stack is demanding on resources and hard to manage.

Given that Prometheus is the most promising toolfor storing metrics, at least among open source projects, that description certainly sparksinterest.

To be more precise, Loki uses LogQL, which is a subset ofPromQL. 

Further on, the UI for exploring logs is based on Grafana, which happens to be the de-factostandard in the observability world. 

. It allows us to query and correlate both logsfrom Loki and metrics from Prometheus in the same view.

That’s enough talk. Let’s get our hands dirty. The first step is to set up a cluster and installthe Loki stack and a few other tools.

We’ll need a Kubernetes cluster with the NGINX Ingress controller. The address throughwhich we can access Ingress should be stored in the environment variable INGRESS_HOST.

Inside each of the nodes, we installed Loki Promtail Pods (loki-promtail-...) deployedthrough a DaemonSet. Each of those ships contents of local logs. It does that by discoveringtargets using the same service discovery mechanism as Prometheus. It also attaches labelsto all the log streams, before pushing them to either a Loki instance (as in our case) or toGrafana Cloud.

It is the primary serverresponsible for storing logs and processing queries. We are currently running a single replica,but it could be scaled horizontally to meet whichever needs we might have. 

We’ll use it as theuser interface to query and visualize information collected across the whole cluster.

Prometheus is capable of discovering all the apps running in a cluster and scrapingmetrics, if available. On top of that, it also collects information from Kubernetes itself.

To be on the safe side, we’ll send a request to the newly deployed app to validate that itworks and can be accessed.

I just realized that xip.io works with local addresses. That’s good news since that meansthat it can work with Docker Desktop and Minikube. 

The password is stored in the field admin-password inside the Secret grafana. All we haveto do is retrieve it and decode it.

If you are familiar with Prometheus, you should have no problems with that syntax. Actually,it’s straightforward enough, so you shouldn’t have an issue with it, no matter the familiaritywith Prometheus.

I will skip reminding you to run queries. 

Please modify the query with the snippet that follows.

We can think of Loki as a solution for “distributed grep” commands.

Most of us tend to look at logs only when things go wrong. We find out that there is an issueby receiving alerts based on metrics. Only after that, we might explore logs to deduce whatthe problem is. Even in those cases, logs rarely provide value alone. We can have meaningfulinsights only when logs are combined with metrics.

We are done with this chapter, so let’s clean up all the tools we installed. There’s probablyno need to comment on what we’ll do since it’s similar to what we did at the end of all theprevious sections. We’ll just do it.

you will find the instructions on how todestroy the cluster at the bottom. Feel free to use those commands unless you plan to keepthe cluster up and running for other reasons.

### Deploying Applications Using GitOps Principles

Metrics are exposed, and logs are shipped. Our apps became fault-tolerant and scalable. They are deployed without any downtime, and they can roll back automatically if there are potential issues.

Yet, we are still moving forward and constantly improving the way we work, including how we deploy applications. Today, running commands ourselves is considered a bad practice. No one is supposed to ever deploy anything by executing kubectl apply, helm upgrade, or any other command, especially not in production. Processes are supposed to be run by machines, and not by us.

It is compliant with some of the core principles behind GitOps. Specifically, we are trying to establish Git as a boundary between the tasks performed by us (humans) and machines.


So, long story short, go and watch What Is GitOps And Why Do We Want It?.

There were usually a few “privileged” people, and that was about it. Those were horrible times. We had to open JIRA issues and write a ton of documents and justifications of why something should be deployed to production so that one of the “privileged” few would do it

An environment is a collection of applications and their associated resources. Production can be considered an environment. It can be located in a single Namespace, it could span the whole cluster, or it can be distributed across the fleet of clusters.

What matters is that it is a collection of applications. Similar can be said for staging, pre-production, integration, previews, and many other environments. What they all have in common is that there are one or more logically grouped applications.

We need to be able to define permissions, restrictions, limitations, and similar policies and constraints.

If we make a new release of a single independently deployable application (e.g., a microservice), we need to be able to update the state of an environment to reflect the desire to have a new release of an application in it.

We might need to be able to recreate the whole environment from scratch. Or, we might have to duplicate it so that it serves as, let’s say, staging used for testing before promoting a release to production.

That allows the team in charge of that application to be in full control and develop it effectively.

Instead, environment repos often contain references to all the manifests of individual applications and environment-specific parameters applied to those apps.

The only thing that matters is that everything is code. The code is the source of truth. It is the desired state, and it is stored in Git repositories. Your job is to push changes to Git, and it’s up to machines to figure out how to convert the actual state into the desired one.

We will try to set up a system in which no one will ever execute kubectl apply, helm install, or any similar command, at least not after the initial setup.

We will take a completely different approach, and you’ll love it. I bet that, in the end, you will ask yourself: “why didn’t I do this years ago?”

### Applying GitOps Principles Using Argo CD

Applying GitOps Principles Using Argo CD
Argo CD describes itself as “a declarative, GitOps continuous delivery tool for Kubernetes.” That is, in my opinion, wrong and misleading. Argo CD is not a “continuous delivery tool”. Much more is needed for it to be able to claim that.

Continuous delivery is about automating all the steps from a push to a code repository all the way until a release is deployed to production. As such, Argo CD does not fit that description.

It is based on GitOps principles, and it is a perfect fit to be a part of continuous delivery pipelines.

Argo CD is a tool that helps us forget the existence of kubectl apply, helm install, and similar commands. It is a mechanism that allows us to focus on defining the desired state of our environments and pushing definitions to Git. It is up to Argo CD to figure out how to converge our desires into reality.

we’ll discuss the process, the patterns, and the architecture.

As you can probably guess, we’ll run Argo CD inside a Kubernetes cluster. That is our first requirement.

To simplify the process, I created a few definitions to help us out. They are stored in the vfarcic/devops-catalog-code repository, so let’s clone it.

Don’t worry if git clone threw an error stating that the destination path 'devops-catalog-code' already exists and is not an empty directory. You likely already have it from the previous exercises.


Before we proceed, we’ll install argocd CLI. It is not mandatory. We can do everything without it by using kubectl. Still, argocd CLI can simplify some operations. You already have quite a few CLIs, so one more should not be an issue.

The first step is to create a Namespace where Argo CD will reside.

1 kubectl create namespace argocd

Next, we need to add the repo with Argo charts to the local Helm repository.

1 helm repo add argo \
2     https://argoproj.github.io/argo-helm

We are enabling ingress so that we can access Argo CD UI from a browser. Given that we do not have SSL certificates, we will let it know that it is okay to be insecure.


The process should finish a few moments later,

We stored the password in the environment variable PASS. Now we can use it to login to Argo CD from the CLI.

1 argocd login \
2     --insecure \
3     --username admin \
4     --password $PASS \
5     --grpc-web \

You will be asked to provide the existing password. Copy and paste the output of echo $PASS. Further on, you will be requested to enter a new password twice.

Figure 6-1-1: Argo CD sign in screen


After signing in, you will be presented with the Argo CD home screen that lists all the applications.

For now, we have a Kubernetes cluster with Argo CD running inside. It can be accessed through the UI or through Kube API.


Kubernetes YAML files that define the application are in the k8s directory. Let’s take a peek at what’s inside.

1 ls -1 k8s
The output is as follows.

We’ll take a different approach. Instead of telling Kube API that we’d like to have the resources defined in those files, we will inform Argo CD that there is a Git repository vfarcic/devops-toolkit it should use as the desired state of that application. But, before we do that, let’s create a Namespace where we’d like that application to reside

The reason for that lies in the Status that is, currently, set to OutOfSync. We told Argo CD about the existence of a repository where the desired state is defined, but we never told it to sync it. So, it did not yet even attempt to converge the actual into the desired state.

Now you must feel like a kid in front of a cookie jar. You are tempted to click that SYNC button, aren’t you? Do it. Press it, and let’s see what happens.

You should be able to observe that the status changed to Progressing. After a while, it should switch to Healthy and Synced.

We could have accomplished the same result through the argocd CLI. Everything we can do through the UI can be done with the CLI, and vice versa.

I will be mixing Argo CD UI with argocd and kubectl commands. That way, you will be able to experience different approaches to accomplish the same result.

 can confirm that by listing all the resources in the devops-toolkit Namespace.

The problem is that we might have gone in the wrong direction. 

As you can see, the application is gone.

There’s one more thing I almost forgot to mention. This application repository also contains a Helm chart located in the directory helm.

We won’t go deeper into it than that. Helm is not the subject of this section, and I will assume that you are familiar with it. If you’re not, you probably skipped over the Packaging, Deploying, And Managing Applications section.


let’s try to automate everything instead of relying on the SYNCHRONIZE and similar buttons. After all, UIs are supposed to help us gain insights, not convert us into button-clicking machines.

So, we need environment-specific manifests. Given that an environment usually contains more than one application, we cannot have those in a repo of one of the apps. So, it makes sense to keep separate repositories for environments. It could be one repo for all the environments. 

Instead, we’ll create a separate repository for each environment. As you will see later, it is easy to configure Argo CD to use a different strategy, so do not take one-repo-per-env as the only option. Think of it as my preference, and not much more. I had to choose one for the examples.


If you do not know how to fork a GitHub repo, the only thing I can say is “shame on you”. Google how to do it. I will not spend time explaining that.

I already mentioned that we need to find a way to restrict the environment. We might need tobe able to define which Namespaces can be used, which resources we are allowed to create,what the quotas and limits are, and so on. 

That is okay initially,but, as the number of applications managed by Argo CD increase, we might want to startorganizing them inside different projects.

So, what are Argo CD Projects?Projects provide a logical grouping of applications. They are useful when Argo CD is used bymultiple teams.

We are specifying that the production Project can use any of the repositories(sourceRepos set to '*'). Further on, we defined that the applications can be deployed onlyto two destinations. Those are Namespaces production and argocd. 

Please note that we could have accomplished the same outcome through the argocd projcreate command. We’re using kubectl apply mostly to demonstrate that everythingrelated to Argo CD can be done by applying Kubernetes YAML manifests. When working withmanifests, I feel that there is no sufficient advantage in using argocd CLI. I tend to use itmostly for observing the outcomes of what ArgoCD did, and even that not very often.

To be on the safe side, we’ll list all the Argo CD projects and confirm that the newly createdone is indeed there. 

The output is as follows.

you can retrieve the projects throughthe UI as well.

Next, let’s take a look at the manifests that will define our applications. They are located inthe helm directory.

The definition is accomplishing a similar objective as the argocd app create command weexecuted earlier. The major difference is that this time, it is defined as a file and stored inGit. By doing that, we are complying with the most important principle of GitOps. Instead ofexecuting ad-hoc commands, we are defining the desired state and storing it in Git.

Every timewe want to promote a new release to production, we can just change that value and let ArgoCD do whatever needs to be done for the state of the production environment to convergeto it.

In this context,https://kubernetes.default.svc means that it will run in the same cluster as Argo CD.

Finally, syncPolicy is set to automated and, within it, with selfHeal and prune set totrue.

Having syncPolicy.automated means that, no matter the sub-values, it will sync anapplication whenever there is a change in manifests stored in Git. Its function is the same asthe function of the SYNCHRONIZE button we clicked earlier.

When syncPolicy.automated.selfHeal is set to true, it will synchronize the actual state(the one in the cluster) with the desired state (the Git repo) whenever there is a drift. It willsynchronize not only when we change a manifest in a Git repo, but also if we make anychanges to the actual state. If we manually change what is running in the cluster, it will auto-correct that.

It will assume that any manual intervention is unintentional andwill undo the changes by converging the actual into the desired state. You, or any otherhuman, will effectively lose the ability to “play” with the live cluster, at least when thatapplication is concerned.

The selfHeal option might sound “dangerous”, but it is a good thing to enable. If nothingelse, it enforces the rule that no one should change anything in a cluster manually throughthe direct access.

Finally, when syncPolicy.automated.prune is set to true, Argo CD will sync even if wedelete files from the repo. It will assume that the deletion of the files is an expression of thedesire to remove the resources defined in them from the live cluster.

Both selfHeal and prune are set to false by default. Actually, evensyncPolicy.automated is disabled by default. As a security precaution, the projectauthors decided that we need to be explicit about whether we want automation and whichlevel of it we desire.

Application definition. The major differenceis that it references the helm directory of the vfarcic/argocd-production repository.

That’s my repo. It belongs to the vfarcic organization. We need tochange that address to be your fork of that repo, or, to be more precise, to use your GitHuborganization. As usual, we’ll use a bit of sed magic for that.[插图]

Let’s persist those changes by pushing them to the repo.

We are finally ready to apply that definition.

Let’s see what happens if we click the production application box.You should see that, from the Argo CD point of view, the production app consists of tworesources. Expand them by clicking the show 2 hidden resources link.

 We can see that production consists of devops-paradoxand devops-toolkit applications. In this context, even though Argo CD sees production as yetanother application, it is realistically the full production environment.

Now, let’s say that we want to see the details of the devops-toolkit app. For example, wemight want to see which resources are defined as part of that application, the status ofeach, and so on. We can do that easily by clicking the Open application icon in devops-toolkit. 

That is the picture worth looking at. We can see that the Argo CD application consists of aService, a Deployment, and an Ingress. The Service has an endpoint, and the Deploymentcreated a ReplicaSet, which created a Pod.

Figure 6-1-4: Argo CD application view

On the bright side, it worked. It opened the address defined in Ingress. 

Ingress of that app is configured with that domain. So, even thoughthe app runs in your cluster, the Ingress has my hostname. 

We can see that all the resources were indeed created and that the application’s Pods areindeed running.

Let’s see how does the management of applications through Argo CD look like beyond theinitial setup.

Updating Applications Through GitOps PrinciplesAs we already discussed, any change to the cluster should start with a change of the desiredstate and end with the push of that change to Git. After that, it’s up to the system toconverge the actual into the desired state. We already have such a system set up, and allwe’re missing is to see it in action.

 We need to be specific, or we risk losing control of what isrunning where.

With those two issues in mind, we can fix our problems by changing the latest to a specificversion and devopstoolkitseries.com to a domain that will be pointing to your cluster.

We only made changes to a file and pushed it to the Gitrepository. The rest should be done by Gremlins working behind the hood of Argo CD.

As you can see, it is still the latest tag. That can be explained in two ways. Maybesomething went wrong, or maybe I was too impatient. For now, we’ll assume that it is thelatter case, so I’ll wait for a while. If you’re getting the same output, I suggest you take thisopportunity to stretch your legs. Take a short walk, go grab a coffee, or take this opportunityto check your emails.

After a while, we can repeat the same command hoping that “gremlins” did their job.

 Unlike before, this time, you areseeing the response from the app running inside your cluster.

We saw that we can create and update resources in an environment. How about removingsomething? What happens, for example, if we remove the whole devops-paradox.yaml filefrom the Git repo?

Let’s go back to the UI and observe what’s going on.[插图]

Figure 6-1-5: Argo CD home screen with an environment and an application

We can confirm that devops-paradox is indeed gone by retrieving all the pods from theproduction Namespace.

It all happened without any outside intervention because Argo CD is based on a “pull model”.Instead of waiting for notifications that the desired state changed, it is monitoring therepositories and waiting for us to make a change. That makes it a much more securesolution given that we can block all ingress traffic.

That’s it. The process is simple yet very powerful. Even though Argo CD is a very misleadingname, it does a great job. It’s not doing CD, as the name suggests. Instead, it is in charge ofdeployments through GitOps principles. It is a piece of the puzzle that makes applicationlifecycle automation much easier. From now on, we do not need to worry about figuring outwhat to deploy and how to do it. All that is expected from us is to modify manifests andpush them to Git repositories. Everything else related to deployments will be handled by ArgoCD.

We are done with this chapter, so let’s clean up all the tools we installed. 

If you created a cluster using one of my Gists, you will find the instructions on how todestroy the cluster at the bottom. Feel free to use those commands unless you plan to keepthe cluster up and running for other reasons.

### Applying Progressive Delivery

Progressive delivery is a deployment practice that aims at rolling out new features gradually.It enforces the gradual release of a feature while, at the same time, tries to avoid anydowntime. It is an iterative approach to deployments.

since progressive deliveryencompasses quite a few practices, like blue-green deployments, rolling updates, canarydeployments, and so on.

For now, the most crucial question is notwhat it is, but why we want it and which problems does it solve?

The “traditional” deployment mechanism consists of shutting down the old release anddeploying a new one in its place. I call it “big bang” deployments, even though the morecommonly used term is “recreate strategy”.

The major problem with the “big bang” deployments is downtime. Between shutting downthe old release and deploying a new one in its place, there is a period during which theapplication is not available.

 Such downtime might be one of the main reasons why we had infrequent releases inthe past.

If there is inevitable downtime associated with the deployment of new releases, it makessense not to do it often. The fewer releases we do during a year, the less downtime causedby new deployments we have. But that is also bad. Users do not like it when a service is notavailable, but they also do not like not getting new features, nor are they thrilled with theprospect of not having the bugs fixed.

Right now, you might be asking yourself, “why would anyone use the “big bang” deploymentstrategy if it produces downtime?” There are two common answers to that question.

Sometimes, deploying new releases in a way that produces downtime is the only option wehave. Sometimes, the architecture of our applications does not permit anything but the shut-it-down-first-and-deploy-later approach.

To begin with, if an application cannot scale, there is no other option. It is impossible todeploy a new release with zero-downtime without running at least two replicas of anapplication in parallel. No matter which zero-downtime deployment strategy we choose, theold and new releases will run concurrently, even if only for few milliseconds. If an applicationcannot scale horizontally, it cannot have more than one replica.

To begin with, stateful applications that cannot replicate data between replicas cannot scalehorizontally.

That means that weeither have to change our application to be stateless or figure out how to replicate data. Thelatter option is a horrible one, and close to impossible to do well. Consistent and reliablereplication is hard. It’s so complicated that even some databases have a hard time doingthat. Accomplishing that for our apps is a waste of time. It’s much easier and better to usean external database for all the state of our applications, and, as a result, they will becomestateless.

if an application can scale, it can use progressivedelivery. That would be too hasty. There’s more to it than being stateless.

Progressive delivery means not only that multiple replicas of an application need to run inparallel, but also that two releases will run in parallel. That means that there is no guaranteewhich version a user will see, nor with which release other applications are communicatingwith. As a result, each release needs to be backward compatible. 

If it is going to be deployed without downtime, two releases will run inparallel, even if only for a few milliseconds, and there is no telling which one will be “hit” bya user or a process. Even if we employ feature toggles (feature flags, or whatever else wecall it these days), backward compatibility is a must.

We know that horizontal scaling and backward compatibility are requirements. They areunavoidable.

 Also, I promised that this will be the shortest introduction to a subject. Asyou can see, I’m already failing on that promise, so let’s move on.

Optionally, you might need to have continuous delivery or continuous deployment pipelines.You might need to have a firm grasp of traffic management. You might have to invest inobservability and alerting. Many things are not strict requirements for progressive delivery,but your life will only become harder without them. 

All in all, progressive delivery is an advanced technique of deploying new releases thatlowers the risk, reduces the blast radius of potential issues, allows us to test inproduction, and so on and so forth.

Progressive delivery is an umbrella for different deployment practices, with only one thing incommon. They are all rolling out new releases progressively. That can be over a fewmilliseconds, a few minutes, or even a few days. The duration varies from one case toanother.

What matters is that progressive delivery is an iterative approach to the deploymentprocess. It can be rolling updates, blue-green deployments, canary deployments, and quite afew others. They are all variations of the same idea.

I will not even attempt to explain in detail how each of those deployment strategies works. Ifyou are familiar with them, great. If you are not, I prepared a video that you can watch onYouTube. Please visit

 you do not need it because you are lucky to work in acompany that does not have any legacy application.

Similarly, I will not go through the rolling updates strategy because you are almost certainlyusing it, even if you might not know that you do. That is the default deployment strategy inKubernetes. Nearly all the examples from the other sections used it.

Finally, we will not explore blue-green deployments because they are pointless today. Theymade sense in the past when we had the static infrastructure, when applications we mutable,when it was expensive to roll back, and so on and so forth. It is the first commonly usedprogressive delivery strategy that made a lot of sense in the past, but not anymore.

That leaves us with only one progressive delivery strategy worth exploring, and you probablyalready guessed which one it is.We are about to dive into canary deployments.

We’ll explore a completely different tool. That book/coursewas based on Flagger, and now we will explore what I believe is a better tool. We’ll dive intocanary deployments with Argo Rollouts.

### Using Argo Rollouts To Deploy Applications

Argo Rollouts provides advanced deployment capabilities. It supports blue-green and canarystrategies.

On top of that, it can query metrics from various providers and make decisions whether toroll forward or to roll back based on the results. Those metrics can come from Kubernetes,Prometheus, Wavefront, Keyenta, and a few other sources.

Now, I could continue for a long time explaining the benefits and the downsides of ArgoRollouts. We could start comparing it with other solution, or debate for a while why weshould adopt it instead of others.

Once you get the “feel” of how it works, you should be ableto make the decision whether it is a tool worth adopting.

I am assuming that you are looking for hands-on examples instead of lengthy theory. Giventhat this is not a conversation and that you cannot tell me that I’m wrong, I’ll have to assumethat you agree with me. So, let’s get going.

To be on the safe side, we can validate that the plugin indeed works as expected by listingall the additional commands we just added to kubectl.

There’s only one more thing missing. We need to install Argo Rollouts inside the Kubernetescluster.

Let’s deploy a demo application and see Argo Rollouts in action.

Let’s get inside the local repo and pull the latest version, just in case you cloned it beforeand I changed something in the meantime.[插图]

Everything directly related to Argo Rollouts is in therollout.yaml file, so let it be the first one we’ll look at.

While it might be easier to explore Argo Rollouts through “pure” Kubernetes YAML, I believethat it is better to use Helm templates since they will allow us to apply different variations ofthe strategies by changing a few values instead of creating new definitions.

The Rollout is almost the same definition as what we’d expect from a KubernetesDeployment. As a matter of fact, everything that we can define as a Deployment, can bedefined as a Rollout, with a few differences.

The more important change, when comparedwith the Deployment is the addition of two new strategies. Instead of typical Recreate andRollingUpdate strategies, Rollout supports spec.strategy.blue-green andspec.strategy.canary fields. 

Inside the spec.strategy.canary entry are a few fields that provide the information to theRollout how to perform the process. There is a reference to the canaryService. That’sthe one it will use to redirect part of the traffic to the new release. Further on, we got thestableService which is where the bulk of the traffic goes. It’s the Service used for therelease that is rolled out fully.

The primary Service is defined in service.yaml. 

The one we see right now is theService referenced in the canaryService field of the Rollout. It will be used only duringthe process of rolling out a new release. For all other cases, the one defined inservice.yaml will be used. 

There are a few other changes we might need to make to the typical definitions. Forexample, the reference in a HorizontalPodAutoscaler needs to reference Rolloutinstead of a Deployment or whichever other resource we might normally use. Let’s take aquick look at the one we will use.

The only difference is in the apiVersion and kind fields set insidespec.scaleTargetRef. In this specific case, we have different values depending onwhether it is a Rollout or a Deployment. As you can probably guess, we’ll have the valuerollout.enabled set to true, so the Kind field will be set to Rollout.

That’s typical forIstio, so we won’t spend time on them, except to note that the weight of the primarydestination is set to 100, while the weight of the one with the -canary suffix is 0. Thatmeans that all the traffic will go to the primary Service which will be the release that is fullyrolled out. Later on, we’ll see how Argo Rollouts manipulates that field at run time during theprocess of canary deployments.

I will not comment on that output since we will not use most of it. Those are the defaultvalues that we can consider “production ready”

As a matter of fact, we will use those default values later when we gain enough confidenceto fully automate the process. 

The goal is to start simple, andprogress towards a more complicated later. Simple, in this context, means manual approvalsfor rolling forward releases.Let’s take a look at the set of values we’ll use to overwrite those set by default in the chartof the demo app.

We can see that ingress.enabled is set to false. 

I am assuming that you have at least basic understanding of Helm and thatyou should have no problem matching the values with their usage inside the templates.Now we are getting to the important part. We are enabling the definitions specific to ArgoRollouts by setting the value of rollout.enabled to true.

The steps can be described as follows.Redirect 20%!o(MISSING)f the requests to the new releasePause the process indefinitely

Redirect 80%!o(MISSING)f the requests to the new releasePause the process for 10 secondsRoll out the new release fully and shut down the old one

 Instead, that step means that the process will be paused until we “promote”that release to the next step manually. When the process reaches the pause step withoutany arguments ({}) it will wait there until we perform a manual action of “promotion” which,in turn, will be a signal for Argo Rollouts to move to the next step of the process.

You might see the InvalidSpecSTATUS for a few moments. Don’t panic! That’s (usually)normal if you were too fast and retrieved the rollout before the process startedprogressing.

it did not pause waiting for us to “promote” the process to the next step. Do not panic! Wedid not forget anything, and we did not found a bug.

When deploying the first release of an application, Argo Rollouts ignores the steps. It wouldbe pointless to start rolling out a new release to a fraction of the users, if that is the onlyrelease we have. The canary deployment strategy makes sense only when applied to thesecond release, and all those coming afterward. 

 So, it rolled out the first release fully without anydelay, without pauses, and without any analysis. It rolled it out as quickly as it couldcompletely ignoring that we told it to use the canary strategy.

We will see the canary deployment in action soon when we initiate the deployment of thesecond release. But, before we do that, we will confirm that the application is indeed up-and-running and accessible to our users. We’ll do that by opening the app in the defaultbrowser.

We will deploy a second release, and that should kick offthe canary deployment process.

[插图]We changed the tag of the image to 2.9.9 while reusing all the other values.Let’s watch the rollout and see what’s going on.

The process executed the first step which set the weight of the new release to twentypercent. After that, it executed the second step which is set to pause: {}. So, the Statuschanged to Paused and is waiting for us to promote it to the next step.

We sent 100 requests to the application and piped each to the grep command that looks forcatalog, patterns, and blueprints. 

 At the end of the loop, we pipedthe whole output to wc -l effectivelly counting the number of lines. As a result, the outputshould show how many of those 100 requests were sent to the new release.

That is close enough to twenty percent. If we send more requests, the sample wouldbe larger, and the result would be closer to twenty percent.

We can see that Argo Rollouts changed the weight of each of the destinations. It set themain (the one associated with the old release) to 80 percent, and the canary (the oneassociated with the new release) to 20 percent.

As we already commented, the process paused waiting for us to promote it to the next step.Let’s roll forward the release.

Just as before, we can watch the rollout to observe what’s going on.

We can see that the process paused again but, this time, with the weight set to 40 percent.From now on, the new release should be receiving forty percent of the traffic, while the restshould be going to the old release. We can confirm that by sending another round arequests. But, before we do, please stop watching the rollout by pressing ctrl+c.

The process continued executing the steps we specified. After we promoted the secondpause, it set the weight to 60 percent, paused for 10 seconds, set the weight to 80 percent,and paused for another 10 seconds. After the last step was executed, it rolled out the newrelease fully, and it ScaledDown the old one.Let’s confirm that the application was indeed rolled out completely.

The output is 100 meaning that every single request got a response from the new release.That should be enough of a confirmation that the new release was rolled out fully.Now that we saw how we can roll forward, let’s explore how we can reverse the process.

No matter how well we do our job, bad things happen. Wemight introduce a bug, we might have a “broken” release, or we might forget somethingcritical. 

That might mean that we will create a new “patch” release and roll it forward, orthat we will need to roll back.

We should hope that we will never need to go back, but be prepared just incase. “Hope for the best, prepare for the worst” should be the motto.

Let’s see how we can roll back to the previous release.We’ll repeat the same process again, but with a different tag.

For now, we’ll do it manually, just as wepromoted steps manually as well. Later on, we’ll explore how to automate everything,including rolling forward and rolling back.

A better way to roll back would be to change the desired state so that it specifies that wewant the previous release after all. That means that we should revert a commit in Git, orsimply set image.tag value to whichever version we want to roll back to, and push thechange to Git. 

We can see that Argo rolled back to the previous release right away. It aborted the rolloutprogress and went back to the tag 2.9.9. 

We’ll take a short break from Argo Rollouts, with a quick jump into metrics. We’ll need themif we are ever to fully automate the rollout process. 

We are about to deploy Prometheus. But, before we do, let’s start generating some traffic sothat there are metrics we can explore.

Now that we are sending requests and, through them, generating metrics, we can deployPrometheus and see them in action.

However, since Prometheus is not themain subject of this chapter, we’ll take the easier route and port-forward to theprometheus-server Deployment. That will allow us to open it through localhost on aspecific port.

If we would like to automate rollout decision making, the first step is to define the criteria.One simple, yet effective, strategy could be to measure the error rate of requests. We canuse the istio_requests_total metric for that. It provides the total number of requests.

That’s not very useful given that our goal is to see the percentage of errors, or to calculatethe percentage of successful requests. 

We’ll get back to deploying releases using Argo Rollouts. But, before we do, we will not needto access Prometheus UI any more, so let’s kill port forwarding

We explored how to promote rollouts manually. That might be a great solution for many, butit shouldn’t be the end goal. We should strive for more. In this context, and most of theothers, “more” means removal of manual repetitive actions.

We are going to try to automate the whole deployment process, including potential rollbacks.We’ll try to instruct the machines how to judge whether the process is progressing in theright direction, and whether there is an issue that might require them to roll back. We will doall that by adding analysis based on metrics stored in Prometheus.

Prometheus is not the only supported analysis engine. It could be Wavefront, Datadog, and afew others. Even if your metrics are not in one of the supported engines, you can always usethe web provider that allows fetching metrics from any service reachable through a URL. Weare using Prometheus mostly because I had to pick one for the examples.

One important difference is that none of the steps have pause: {}. Instead, there is aduration in each. That means that the process will not pause indefinitely waiting for amanual promotion. Instead, it will wait for the specified duration, before moving to the nextstep. During that duration, the system will be evaluating metrics. We’ll see them soon. Fornow, what matters, is that there are no manual interventions. The system will not wait for usto do anything.

If you were wondering why the first pause is set to 2m while the others are much shorter(30s), the explanation is relatively simple. Prometheus does not collect metrics at real-time.

 The analysis would need to fail three times for the Rollout to abortthe process and roll back.

Deploying Releases With Fully Automated Steps

Let’s see what happens if there is an issue with a new release. Does it indeed roll back if thefailureCondition is reached at least 3 times?We’ll do the simulation by sending a constant stream of requests to a non-existing path.That way, the analysis of the percentage of successful requests will surely discover that thesuccessCondition is not met.

Now that we are simulating issues, please go back to the first terminal session and initiatethe deployment of the second release.

Soon after the AnalysisRun reached the failureLimit currently set to 3, the process wasaborted and the roll back started. The Status changed to Degraded, the ReplicaSet ofthe new release was ScaledDown, and the ActualWeight changed to 0. As a result, all thetraffic is redirected to the old release. Canary deployments failed the analysis, and theprocess reverted to the previous state.

Now that we experienced how unsuccessful rollouts look like, we can just as well confirmthat a “good” release will be rolled out fully.

The process will keep progressing by moving through all the steps. It will keep increasing theweight and keep pausing for specified duration. In parallel, it will keep running new analysisqueries. They should all be successful since all the requests are getting 200 response, andtheir average duration is below the threshold we specified.

Once all the steps were executed, the new release should be fully rolled out without us doingany manual intervention. Instead of promoting from one step to another manually anddeciding whether to roll back ourselves, we instructed the machines to perform those tasksfor us.

No matter whether we (humans) or the machines make decisions when to roll forward andwhen to roll back, those are always made based on some information. 

We always make them based on some data, and there is nobetter information than metrics. 

Truth be told, the two metrics we used are almost never sufficient. Relying on a few simplequeries is far from enough to give us confidence that machines can make decisions for us.

Figure out the patterns you use to make decisions, convert those patterns intoqueries, instruct Argo Rollouts how to use them, and spend your valuable time on moreproductive and creative tasks.

I suggest you start with manual promotions and rollbacks. That’s a verygood way to gain the experience you will need to automate the whole process. Over time,you will understand which queries are reliable, and which are not. You’ll understand whichmetrics are missing. You will learn how to go beyond those provided out of the box, and startinstrumenting your applications to provide fine-tuned metrics. Those are crucial for thedecision-making process.

All in all, automate everything only after you become confident in your manual processes andtheir reliability.

Before we move on, there is one important note I might have forgotten to mention. All ourexamples were based on manual execution of helm commands. That should not be the endgoal. Instead, we should combine Argo Rollouts with Argo CD or some similar tool. Weshould be pushing changes of the desired state to Git. Argo CD should be watching for suchchanges and initiate deployments. 

Finally, all that should be wrapped in continuous delivery pipelines so thatchanges to Git repos initiate pipeline builds that will create binaries, images, and releases,run tests, push changes to the desired state in Git, and other steps associated withapplication lifecycle.

### This Is NOT The End

This is NOT the end. This is a work in progress.

There are other tools and processes I will add.

In the meantime, while waiting for me to add more “stuff”, please contact me and let meknow what is missing. What do you need? Which tool would you like me to add? Whichprocess are you interested in exploring? Which area of software development is not covered?What would you like to explore next?

This book is a community effort. As such, I would love you to get involved. Please contact meby sending a public or a private message on DevOps20 Slack Workspace. 

Don’t be a stranger! Thedirection of this book depends on your participation and your suggestions.