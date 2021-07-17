## Cloud Native DevOps with Kubernetes
> John Arundel

### Foreword

It’s a core component in the DevOps world today.

 John and Justin cover all the major aspects of deploying, configuring, and operating Kubernetes and the best practices for developing and running applications on it.

They also give a great overview of the related technologies, including Prometheus, Helm, and continuous deployment. This is a must-read book for everyone in the DevOps world.

The entire IT industry is changing because of the cloud native revolution, and it’s hugely exciting to be living through it.

### Preface

As more and more applications and businesses move from traditional servers to the Kubernetes environment, people are asking how to do DevOps in this new world.

This book explains what DevOps means in a cloud native world where Kubernetes is the standard platform. It will help you select the best tools and frameworks from the Kubernetes ecosystem. 

You’ll learn what Kubernetes is, where it comes from, and what it means for the future of software development and operations. You’ll learn how containers work, how to build and manage them, and how to design cloud native services and infrastructure.


You’ll understand the trade-offs between building and hosting Kubernetes clusters yourself, and using managed services. You’ll learn the capabilities, limitations, and pros and cons of popular Kubernetes installation tools such as kops, kubeadm, and Kubespray. You’ll get an informed overview of the major managed Kubernetes offerings from the likes of Amazon, Google, and Microsoft.

migrating existing applications to Kubernetes and cloud. We assume no prior knowledge of Kubernetes or containers—don’t worry, we’ll walk you through all that.

it covers advanced topics such as RBAC, continuous deployment, secrets management, and observability. Whatever your level of expertise, we hope you’ll find something useful in these pages.


“I’d like to learn why I should invest my time in this technology. What problems will it help to solve for me and my team?”

“Kubernetes seems great, but it’s quite a steep learning curve. Setting up a quick demo is easy, but operating and troubleshooting it seems daunting. 

Using Code Examples
Supplemental material (code examples, exercises, etc.) is available for download at https://github.com/cloudnativedevops/demo.

### 1. Revolution in the Cloud

The first revolution is the creation of the cloud, and we’ll explain what that is and why it’s important. The second is the dawn of DevOps, and you’ll find out what that involves and how it’s changing operations. The third revolution is the coming of containers. Together, these three waves of change are creating a new software world: the cloud native world. The operating system for this world is called Kubernetes.

We’ll outline what cloud native means, and what changes you can expect to see in this new world if you work in software development, operations, deployment, engineering, networking, or security.


Thanks to the effects of these interlinked revolutions, we think the future of computing lies in cloud-based, containerized, distributed systems, dynamically managed by automation, on the Kubernetes platform (or something very like it). The art of developing and running these applications—cloud native DevOps—is what we’ll explore in the rest of this book.


The central idea of the cloud is this: instead of buying a computer, you buy compute. That is, instead of sinking large amounts of capital into physical machinery, 

When you use cloud infrastructure to run your own services, what

 you’re buying is infrastructure as a service (IaaS).

The revolution in the cloud has also triggered another revolution in the people who use it: the DevOps movement.

 Software development was a very specialist job, and so was computer operation, and there was very little overlap between the two.

Developers tend to be focused on shipping new features quickly, while operations teams care about making services stable and reliable over the long term.

have to understand how it relates to the rest of the system, and the people who operate the system have to understand how the software works—or fails.


The origins of the DevOps movement lie in attempts to bring these two groups together: to collaborate, to share understanding, to share responsibility for systems reliability and software correctness, and to improve the scalability of both the software systems and the teams of people who build them.

Some people think that cloud and containers mean that we no longer need DevOps—a point of view sometimes called NoOps. The idea is that since all IT operations are outsourced to a cloud provider or another third-party service, businesses don’t need full-time operations staff.

Every release includes monitoring, logging, and A/B testing. CI/CD pipelines automatically run unit tests, security scanners, and policy checks on every commit. Deployments are automatic. Controls, tasks, and non-functional requirements are now implemented before release instead of during the frenzy and aftermath of a critical outage.

DevOps has been described as “improving the quality of your software by speeding up release cycles with cloud automation and practices, with the added benefit of software that actually stays up in production”

Now that hardware is in the cloud, everything, in a sense, is software. The DevOps movement brings software development skills to operations: tools and workflows for rapid, agile, collaborative building of complex systems.

cloud infrastructure can be automatically provisioned by software. Instead of manually deploying and upgrading hardware, operations engineers have become the people who write the software that automates the cloud.

how to mitigate their consequences, and how to design software that degrades gracefully and fails safe.


Even more importantly, they’re learning to improve the experience for their users, and to deliver better value for the business that funds them.

To deploy a piece of software, you need not only the software itself, but its dependencies. That means libraries, interpreters, subpackages, compilers, extensions, and so on.

You also need its configuration. Settings, site-specific details, license keys, database passwords: everything that turns raw software into a usable service.


Earlier attempts to solve this problem include using configuration management systems
, such as Puppet or Ansible, which consist of code to install, run, configure, and update the shipping software.

Servers need to be provisioned, networked, deployed, configured, kept up to date with security patches, monitored, managed, and so on.


To solve these problems, the tech industry borrowed an idea from the shipping industry: the container.

The container format contains everything the application needs to run, baked into an image file that can be executed by a container runtime.


Because the virtual machine contains lots of unrelated programs, libraries, and things that the application will never use, most of its space is wasted. Transferring VM images across the network is far slower than optimized containers.

And because containers only hold the files they need, they’re much smaller than VM images. They also use a clever technique of addressable filesystem layers, which can be shared and reused between containers.


The container runtime will assemble all the necessary layers and only download a layer if it’s not already cached locally. This makes very efficient use of disk space and network bandwidth.

the unit of reuse (the same container image can be used as a component of many different services), the unit of scaling, and the unit of resource allocation (a container can run anywhere sufficient resources are available for its own specific needs).

Developers no longer have to worry about maintaining different versions of the software to run on different Linux distributions, against different library and language versions, and so on. The only thing the container depends on is the operating system kernel (Linux, for example).


Google was running containers at scale for production workloads long before anyone else. Nearly all of Google’s services run in containers: Gmail, Google Search, Google Maps, Google App Engine, and so on.

To
 solve the problem of running a large number of services at global scale on millions of servers, Google developed a private, internal container orchestration system it called Borg
.

 While other systems are still in use, from now on companies looking to move their infrastructure to containers only need to target one platform: Kubernetes.


Kubernetes does the things that the very best system administrator would do: automation, failover, centralized logging, monitoring. It takes what we’ve learned in the DevOps community and makes it the default, out of the box.


Many of the traditional sysadmin tasks like upgrading servers, installing security patches, configuring networks, and running backups are less of a concern in the cloud native world. Kubernetes can automate these things for you so that your team can concentrate on doing its core work.

Some of these features, like load balancing and autoscaling, are built into the Kubernetes core; others are provided by add-ons, extensions, and third-party tools that use the Kubernetes API. The Kubernetes ecosystem is large, and growing all the time.

Ops staff love Kubernetes for these reasons, but there are also some significant advantages for developers. Kubernetes greatly reduces the time and effort it takes to deploy. Zero-downtime deployments are common, because Kubernetes does rolling updates by default (starting containers with the new version, waiting until they become healthy, and then shutting down the old ones).


Secondly, some things don’t actually need Kubernetes, and can run on what are sometimes called serverless platforms, better named functions as a service, or FaaS platforms.

Operations, infrastructure engineering, and system administration are highly skilled jobs. Are they at risk in a cloud native future? We think not.


Instead, these skills will only become more important. Designing and reasoning about distributed systems is hard. Networks and container orchestrators are complicated. Every team developing cloud native applications will need operations skills and knowledge. Automation frees up staff from boring, repetitive manual work to deal with more complex, interesting, and fun problems that computers can’t yet solve for themselves.

In a software-defined world, the ability to write, understand, and maintain software becomes critical. If you can’t or won’t learn new skills, the world will leave you behind—and it’s always been that way.


She will be a developer, too, but she will also be the domain expert on networking, Kubernetes, performance, resilience, and the tools and systems that enable the other developers to deliver their code to the cloud.


Instead of calling this team operations, we like the name developer productivity engineering (DPE). DPE teams do whatever’s necessary to help developers do their work better and faster: operating infrastructure, building tools, busting problems.

### 2. First Steps with Kubernetes

let’s start working with Kubernetes and containers.

you’ll build a simple containerized application and deploy it to a local Kubernetes cluster running on your machine. In the process, you’ll meet some very important cloud native technologies and concepts: Docker, Git, Go, container registries, and the kubectl tool.


If you’re already very familiar with containers, skip straight to “Hello, Kubernetes”, where the real fun starts.

It includes a single-node Kubernetes cluster that you can use to test your applications.


Let’s install Docker Desktop now and use it to run a simple containerized application.

If you already have Docker installed, skip this section and go straight on to “Running a Container Image”.


The exact output will be different depending on your platform, but if Docker is correctly installed and running, you’ll see something like the example output shown. On Linux systems, you may need to run sudo docker version instead.




Docker is actually several different, but related things: a container image format, a container runtime library, which manages the life cycle of containers, a command-line tool for packaging and running containers, and an API for container management. 

all you need to specify is a container image ID or URL, and the system will take care of finding, downloading, unpacking, and starting the container for you.

Run the following command to try it out:


Leave this command running, and point your browser to http://localhost:9999/.
You should see a friendly message:

Any time you make a request to this URL, our demo application will be ready and waiting to greet you.

So how does it work? Let’s download the source code for the demo application that runs in this container and have a look.


You’ll need Git installed for this part.1 If you’re not sure whether you already have Git, try the following command:


To make it easier to see what’s going on at each stage, the repo contains each successive version of the app in a different subdirectory. 

You’ll see this source code:

Go is a modern programming language (developed at Google since 2009) that prioritizes simplicity, safety, and readability, and is designed for building large-scale concurrent applications, especially network services. It’s also a lot of fun to program in.2

Kubernetes itself is written in Go, as are Docker, Terraform, and many other popular open source projects. This makes Go a good choice for developing cloud native applications.

As the name suggests, it handles HTTP requests. The request is passed in as an argument to the function (though the function doesn’t do anything with it, yet).

The rest of the program takes care of registering the handler function as the handler for HTTP requests, and actually starting the HTTP server to listen and serve on port 8888.

One of the key benefits of containers is the ability to build on existing images to create new images. For example, you could take a container image containing the complete Ubuntu operating system, add a single file to it, and the result will be a new image.

FROM golang:1.11-alpine AS build￼
WORKDIR /src/￼
COPY main.go go.* /src/RUN CGO_ENABLED=0 go build -o /bin/demo￼
FROM scratch￼
COPY --from=build /bin/demo /bin/demoENTRYPOINT ["/bin/demo"]

but it uses a fairly standard build process for Go containers called multi-stage builds
. The first stage starts from an official golang container image, which is just an operating system (in this case Alpine Linux)
 with the Go language environment installed. It runs the go build command to compile the main.go file we saw earlier.


The result of this is an executable binary file named demo. The second stage takes a completely empty container image (called a scratch image, as in from scratch) and copies the demo binary into it.


To run the program, all it takes is the demo binary, so the Dockerfile creates a new scratch container to put it in. The resulting image is very small (about 6 MiB)—and that’s the image that can be deployed in production.


Without the second stage, you would have ended up with a container image about 350 MiB in size, 98%!o(MISSING)f which is unnecessary and will never be executed. The smaller the container image, the faster it can be uploaded and downloaded, and the faster it will be to start up.

Minimal containers also have a reduced attack surface for security issues. The fewer programs there are in your container, the fewer potential vulnerabilities.


Because Go is a compiled language that can produce self-contained executables, it’s ideal for writing minimal (scratch) containers. By comparison, the official Ruby container image is 1.5 GiB; about 250 times bigger than our Go image, and that’s before you’ve added your Ruby program!


Let’s go ahead and try it.

Naming Your Images

In the previous example you named the image myhello, so you should be able to use that name to run the image now.

Let’s see if it works:
docker container run -p 9999:8888 myhello


Programs running in a container are isolated from other programs running on the same machine, which means they can’t have direct access to resources like network ports.

The demo application listens for connections on port 8888, but this is the container’s own private port 8888, not a port on your computer. In order to connect to the container’s port 8888, you need to forward a port on your local machine to that port on the container. 

It could be any port, including 8888, but we’ll use 9999 instead, to make it clear which is your port, and which is the container’s.

To tell Docker to forward a port, you can use the -p switch, just as you did earlier in “Running a Container Image”:

docker container run -p HOST_PORT:CONTAINER_PORT ...

it’s much more useful if you can push and pull images from a container registry. The registry allows you to store images and retrieve them using a unique name (like cloudnatived/demo:hello).

The default registry for the docker container run command is Docker Hub, but you can specify a different one, or set up your own.



For now, let’s stick with Docker Hub. While you can download and use any public container image from Docker Hub

Authenticating to the Registry

Once you’ve got your Docker ID, the next step is to connect your local Docker daemon with Docker Hub, using your ID and password:



Naming and Pushing Your Image
In order to be able to push a local image to the registry, you need to name it using this format: YOUR_DOCKER_ID/myhello.

To create this name, you don’t need to rebuild the image; instead, run this command:


If not, don’t worry. Docker Desktop includes Kubernetes support (Linux users, see “Minikube” instead). To enable it, open the Docker Desktop Preferences, select the Kubernetes tab, and check Enable (see Figure 2-1).

It will take a few minutes to install and start Kubernetes. Once that’s done, you’re ready to run the demo app!


You’ll need to do the same thing here, using kubectl port-forward:



If the STATUS is not shown as Running, there may be a problem. For example, if the status is ErrImagePull or ImagePullBackoff, it means Kubernetes wasn’t able to find and download the image you specified. You may have made a typo in the image name; check your kubectl run command.


If the status is ContainerCreating, then all is well; Kubernetes is still downloading and starting the image. Just wait a few seconds and check again.


Like Docker Desktop, Minikube provides a single-node Kubernetes cluster that runs on your own machine (in fact, in a virtual machine, but that doesn’t matter).

•  The Docker tool lets you build containers locally, push them to or pull them from a container registry such as Docker Hub, and run container images locally on your machine.

•  A container image is completely specified by a Dockerfile: a text file that contains instructions about how to build the container.


1 If you’re not familiar with Git, read Scott Chacon and Ben Straub’s excellent book Pro Git (Apress).

### 3. Getting Kubernetes

Kubernetes
 is the operating system of the cloud native world, providing a reliable and scalable platform for running containerized workloads.

Instead, we’ll focus on helping you understand the basic architecture of a cluster, and give you the information you need to decide how to run Kubernetes.

The control plane is actually made up of several components:
kube-apiserver This is the frontend server for the control plane, handling API requests
.

etcd This is the database where Kubernetes stores all its information: what nodes exist, what resources exist on the cluster, and so on
.


cloud-controller-manager This interacts with the cloud provider (in cloud-based clusters), managing resources such as load balancers and disk volumes
.


Each worker node in a Kubernetes cluster runs these components:

kubelet This is responsible for driving the container runtime to start workloads that are scheduled on the node, and monitoring their status
.


kube-proxy This does the networking magic that routes requests between Pods on different nodes, and between Pods and the internet
.


Container runtime This actually starts and stops containers and handles their communications. Usually Docker, but Kubernetes supports other container runtimes, such as rkt and CRI-O
.


A correctly configured Kubernetes control plane has multiple master nodes, making it highly available; that is, if any individual master node fails or is shut down, or one of the control plane components on it stops running, the cluster will still work properly. 

due to a network failure 

For example, if you were to stop all the master nodes in your cluster, the Pods on the worker nodes would keep on running—at least for a while. But you would be unable to deploy any new containers or change any Kubernetes resources, and controllers such as Deployments would stop working.


 You should assume that a single node failure will happen at any time, especially in the cloud, and two simultaneous failures are not unheard of.



Cloud vendors like AWS and Google Cloud provide multiple availability zones in each region

For this reason, rather than having all your worker nodes in the same zone, it’s a good idea to distribute them across two or even three zones.

it’s always wise to actually test this. During a scheduled maintenance window, or outside of peak hours, try rebooting a worker and see what happens. (Hopefully, nothing, or nothing that’s visible to users of your applications.)

You can decide what versions of Kubernetes to run, what options and features are enabled, when and whether to upgrade clusters, and so on. But there are some significant downsides, as we’ll see in the next section.

It’s More Work Than You Think


The self-hosted option also requires the maximum resources, in terms of people, skills, engineering time, maintenance, and troubleshooting. Just setting up a working Kubernetes cluster is pretty simple, but that’s a long way from a cluster that’s ready for production. You need to consider at least the following questions:

•  Is the control plane highly available? That is, if a master node goes down or becomes unresponsive, does your cluster still work? Can you still deploy or update apps?

Will it be able to automatically provision new nodes to heal itself, or will it require manual intervention?

Are all services in your cluster secure?

Is the data in your cluster properly backed up, including any persistent storage? What is your restoreprocess? How often do you test restores?

 How do you provision new nodes?

 Scale in response to demand?

to get Kubernetes up and running in a production configuration from scratch

Now bear in mind that you need to pay attention to these factors not just when setting up the first cluster for thefirst time, but for all your clusters for all time. When you make changes or upgrades to your Kubernetesinfrastructure, you need to consider the impact on high availability, security, and so on.

You’ll need to have monitoring in place to make sure the cluster nodes and all the Kubernetes components areworking properly, and an alerting system so that staff can be paged to deal with any problems, day or night.

 understand how the changes affect your existing setup. 

 Youneed to test and verify the configuration on a regular basis—by killing a master node and making sure everythingstill works, for example.

There are tools—lots and lots of tools—to help you set up and configure Kubernetes clusters,

 The sad fact is that inour opinion, the large majority of these tools solve only the easy problems, and ignore the hard ones.

This will help you to make an informed decision about the costs and benefits of self-hosting, as opposed to using managed services.

this maynot be such a big problem. But for small to medium enterprises, or even startups with a handful of engineers, theadministration overhead of running your own Kubernetes clusters may be prohibitive.

Would those resources be better used to support yourbusiness’s workloads instead? Can you operate Kubernetes more cost-effectively with your own staff, or by usinga managed service?

 For the reasons we’ve outlined in the previous sections, we think that using managedservices is likely to be far more cost-effective than self-hosting Kubernetes clusters.

If you’re considering whether Kubernetes is even an option for you, using a managed service is a great way to tryit out.

 in a few minutes

 recommend our favorite

 The opinions here are our own,based on personal experience, and the views of hundreds of Kubernetes users we spoke to while writing thisbook.

The list presented here is notcomplete, but we’ve tried to include the services we feel are the best, the most widely used, or otherwiseimportant.

Google Kubernetes Engine (GKE)

Just choose the number of worker nodes, and click abutton in the GCP web console to create a cluster, or use the Deployment Manager tool to provision one. Within afew minutes, your cluster will be ready to use.

Google takes care of monitoring and replacing failed nodes, auto-applying security patches, and high availabilityfor the control plane and etcd. 

GKE gives you a production-grade, highly available Kubernetes cluster with none of the setup and maintenanceoverhead associated with self-hosted infrastructure. 

Naturally, GKE is fullyintegrated with all the other services in Google Cloud.

you can create multizone clusters, which spread worker nodes across multiplefailure zones 

. Your workloads will keep on running, even if a wholefailure zone is affected by an outage.

With autoscaling enabled, if thereare pending workloads that are waiting for a node to become available, the system will add new nodesautomatically to accommodate the demand.

Conversely,

remove the unused nodes.

they’re catching up fast.

As with GKE and EKS, you have no access to the master nodes, which are managed internally, and your billing isbased on the number of worker nodes in your cluster.

OpenShift is more than just a managed Kubernetes service: it’s a full Platform-as-a-Service (PaaS) product,which aims to manage the whole software development life cycle, including continuous integration and build tools,test runner, application deployment, monitoring, and orchestration.

OpenShift can be deployed to bare-metal servers, virtual machines, private clouds, and public clouds,

You can access and manage your IBM Cloud cluster through the default Kubernetes CLI and the providedcommand-line tool, or a basic GUI. 

It uses declarative configuration, just like Kubernetes resources themselves, and it can not only provision thenecessary cloud resources and set up a cluster, but also scale it up and down, resize nodes, perform upgrades,and do other useful admin tasks.

, kops is under rapid development

 it aims to help you install and maintain a Kubernetes clusteraccording to best practices.

Many of the other tools and services we’ll mention in this chapter use kubeadm internally to handle cluster adminoperations, but there’s nothing to stop you using it directly, if you want to.

 Installing, configuring, maintaining, securing, upgrading, and making yourKubernetes cluster reliable is undifferentiated heavy lifting, so it makes sense for almost all businesses not to doit themselves:

Cloud native is not a cloud provider, it’s not Kubernetes, it’s not containers, it’s not a technology. It’s the practice ofaccelerating your business by not running stuff that doesn’t differentiate you.

. Kubernetes is a standard platform, so anyapplications and services you build to run on Google Kubernetes Engine will also work on any other certifiedKubernetes provider’s system. Just using Kubernetes in the first place is a big step toward escaping vendor lock-in.

 if your budget is limited, you can even run it on a stackof Raspberry Pis (Figure 3-3). 

 it’s not worth managing Kubernetes clusters yourself if a serviceprovider can do it better and cheaper.

It’s a long way from a simple demo cluster to one that’s ready for critical production workloads. Highavailability, security, and node management are just some of the issues involved.

Managing your own clusters requires a significant investment of time, effort, and expertise. Even then, youcan still get it wrong.

Don’t self-host your cluster without sound business reasons. If you do self-host, don’t underestimate theengineering time involved for the initial setup and ongoing maintenance overhead.

### 4. Working with Kubernetes Objects

 In this chapter, you’ll learn about thefundamental Kubernetes objects involved in that process: Pods, Deployments, and Services. 

the kubectl run command creates aKubernetes resource called a Deployment. 

But suppose that the container exits for some other reason; maybe the program crashed, or there was a systemerror, or your machine ran out of disk space, 

 Either way, the controller makes surethat the real state matches the desired state.)

If your 

 If it crashes, or if you kill it with a signal, or terminate it with kubectl, theDeployment will restart it. 

containers can exit for all sorts of reasons, and in most cases all a human operator would do is restart them, sothat’s what Kubernetes does by default.

 If there are too many, it will terminate some.

To get more detailed information on this specific Deployment, run the following command:

For example, a blog application might have one container that syncs content with a Git repository, and an Nginxweb server container that serves the blog content to users. Since they share data, the two containers need to bescheduled together in a Pod. 

So a Pod specification (spec for short) has a list of Containers

 since Deployments do the work for you—but it’s useful toknow what they are.

Kubernetes controllers continually check the desired state specified by each resource against the actual state ofthe cluster, and make any necessary adjustments to keep them in sync. 

Let’s verify that right now by stopping the Pod manually. First, check that the Pod is indeed running:

Once you’ve finished experimenting with the Deployment, shut it down and clean up using the followingcommand:

Once the Pod has been scheduled on a node, the kubelet running on that node picks it up and takes care ofactually starting its containers (see “Node Components”).

Resource Manifests in YAML Format

 Suppose you want to change somethingabout the Deployment spec: 

submit YAML manifests tothe cluster yourself, using the kubectl apply command.

Try it with our example Deployment manifest

After a few seconds, a demo Pod should be running:

You can think of a Service as being like a web proxy or a load balancer, forwarding requests to a set of backendPods (Figure 4-2). 

Figure 4-2. A Service provides a persistent endpoint for a group of Pods

Here’s the YAML manifest of the Service for our demo app:

the Service is forwarding its port 9999 to the Pod’s port 8888:

For now, the main thing to remember is that a Deployment manages a set of Pods for your application, and aService gives you a single entry point for requests to those Pods.

Go ahead and apply the manifest now, to create the Service:

 it applies configuration, creates, modifies, and destroysresources, and can also query the cluster for information about the resources that exist, as well as their status.

just the most common types,

a Helm chart is really just a convenient wrapper around Kubernetes YAML manifests.

Once this command succeeds, you’re ready to start using Helm. If you see an Error: cannot connect to Tillermessage, wait a few minutes and try again.

. First, clean up the resources from any previous deployments:

A chart is a Helm package, containing all the resource definitions necessary to run an application inKubernetes.A repository is a place where charts can be collected and shared.

A release is a particular instance of a chart running in a Kubernetes cluster.

Each separate instance of the chart is adistinct release.

To see the exact status of a particular release, run helm status followed by the name of the release. You’ll seethe same information that you did when you first deployed the release: a list of all the Kubernetes resourcesassociated with the release, including Deployments, Pods, and Services.

Our aim is to show you what Kubernetes can do,and bring you quickly to the point where you can run real workloads in production. 

A Service is the Kubernetes equivalent of a load balancer or proxy, routing traffic to its matching Pods viaa single, well-known, durable IP address or DNS name.

 instead of having to maintain the raw YAML files yourself.

### 5. Managing Resources

we’ll look at how to make the most of your cluster: how to manage and optimize resource usage,how to manage the life cycle of containers, and how to partition the cluster using namespaces. 

some techniques and best practices for keeping down the cost of your cluster, while getting the most for yourmoney.

how to use resource requests, limits, and defaults

The scheduler’s job is to decide where to run a given Pod. Are there any nodes with enough free resources to runthe Pod?

In order to schedule Pods effectively, thescheduler has to know the minimum and maximum allowable resource requirements for each Pod.

A Kubernetes resource request specifies the minimum amount of that resource that the Pod needs to run. 

 If there isn’t any node with enough capacityavailable, the Pod will remain in a pending state until there is.

A Pod that tries to usemore than its allocated CPU limit will be throttled, reducing its performance.

A Pod that tries to use more than the allowed memory limit, though, will be terminated. If the terminated Pod canbe rescheduled, it will be. In practice, this may mean that the Pod is simply restarted on the same node.

Specifying resource limits is a good way to prevent such hungry Pods from using more thantheir fair share of the cluster’s capacity.

Here’s an example of setting resource limits on the demo application:

 This is a kind of gamble: the scheduler is betting that, most ofthe time, most containers will not need to hit their resource limits.

 if Kubernetes needs to terminate Pods, it will start with the ones that have mostexceeded their requests. 

Always specify resource requests and limits for your containers. This helps Kubernetes schedule and manage yourPods properly.

keeping your container images as small as possible is a goodidea, for lots of reasons:Small containers build faster.The images take up less storage.Image pulls are faster.The attack surface is reduced.

when it’s functioning properly and ready tohandle requests.

It’s quite common for containerized applications to get into a stuck state, where the process is still running, butit’s not serving any requests.

Kubernetes needs a way to detect this situation, so that it can restart the containerto fix the problem.

a health check that determineswhether or not the container is alive (that is, working).

The httpGet probe makes an HTTP request to a URI and port you specify; in this case, /healthz on port 8888.

 (Why the z? Just tomake sure it doesn’t collide with an existing path like health, which could be a page about health information, forexample).

If the application responds with an HTTP 2xx or 3xx status code, Kubernetes considers it alive. If it responds withanything else, or doesn’t respond at all, the container is considered dead, and will be restarted.

The initialDelaySeconds field lets you tell Kubernetes how long to wait before trying the first liveness probe,avoiding this loop of death situation.

If a TCP connection to the specified port succeeds, the container is alive.

You can also run an arbitrary command on the container, using an exec probe:

 gRPC is an efficient, portable, binary network protocol developedby Google and hosted by the Cloud Native Computing Foundation.

httpGet probes will not work with gRPC servers, and although you could use a tcpSocket probe instead, that onlytells you that you can make a connection to the socket, not that the server itself is working.

you can use the grpc-health-probe tool. If you add the tool to yourcontainer, you can check it using an exec probe.

no traffic will be sent to the Pod until its readiness probe startssucceeding again.

Normally, when a Pod starts, Kubernetes will start sending it traffic as soon as the container is in a running state.However, if the container has a readiness probe, Kubernetes will wait until the probe succeeds before sending itany requests, so that users won’t see errors from unready containers. This is critically important for zero-downtime upgrades

A container that is not ready will still be shown as Running, but the READY column will show one or more unreadycontainers in the Pod:

Make sure your readiness probes only return a 200 status code.

will fail, and Kubernetes will remove the container from any matching Services. (A better way to do this, though, isto adjust the container’s labels so that it no longer matches the service

You can now inspect and troubleshoot the container at your leisure. 

 A container or Pod will not be consideredready until its readiness probe has been up for minReadySeconds (default 0).

a process called eviction

Perhaps the node they’re running on is being drained prior to an upgrade, for example, and the Pods need to bemoved to another node.

Conversely, you can use maxUnavailable to limit the total number or percentage of Pods that Kubernetes isallowed to evict:

Set PodDisruptionBudgets for your business-critical applications to make sure there are always enough replicasto maintain the service, even when Pods are evicted.

A namespace groups related resources together,and makes it easier to work with them. Unlike folders, however, namespaces can’t be nested.

Deleting a namespace deletes all the resources within it.

Create separate namespaces for each of your applications or each logical component of your infrastructure.Don’t use the default namespace: it’s too easy to make mistakes.

If you need to block all network traffic in or out of a particular namespace, you can use Kubernetes NetworkPolicies to enforce this.

Service DNS names always follow this pattern:[插图]

The .svc.cluster.local part is optional, and so is the namespace. But if you want to talk to the demo Service inthe prod namespace, for example, you can use:

restrict the resource usage of a given namespace. The way to do this is tocreate a ResourceQuota in the namespace.

Applying this manifest to a particular namespace (for example, demo) sets a hard limit of 100 Pods running at oncein that namespace. 

Using ResourceQuotas is a good way to stop applications in one namespace from grabbing too many resourcesand starving those in other parts of the cluster.

However, a Pod limit is useful to prevent a misconfiguration or typing error from generating a potentially unlimitednumber of Pods. 

Use ResourceQuotas in each namespace to enforce a limit on the number of Pods that can run in the namespace.

To check if a ResourceQuota is active in a particular namespace, use the kubectl get resourcequotas command:

You canset default requests and limits for all containers in a namespace using a LimitRange resource:

A LimitRange or ResourceQuota takes effect in a particular namespaceonly when you apply the manifest to that namespace.

Any container in the namespace that doesn’t specify a resource limit or request will inherit the default value fromthe LimitRange. 

 Always specify explicit requests and limits in the container spec itself.

You can also build your own dashboards and statistics using Prometheus and Grafana, and we’ll cover this indetail in Chapter 15.

Without them, a container that has a memory leak, or that uses too much CPU, can gobble upall the resources available on a node, starving other containers.

There is a Kubernetes add-on called the Vertical Pod Autoscaler, which can help you work out the ideal values forresource requests. It will watch a specified Deployment and automatically adjust the resource requests for itsPods based on what they actually use. 

Larger nodes, therefore, can be more cost-effective, because a greater proportion of their resources are availablefor your workloads. The trade-off is that losing an individual node has a bigger effect on your cluster’s availablecapacity.

The default limit in Kubernetes is 110 Pods per node. Although you can increase this limit by adjusting the --max-pods setting of the kubelet, this may not be possible with some managed services, and it’s a good idea to stick tothe Kubernetes defaults unless there is a strong reason to change them.

Look at the percentage resource utilization of each node, using your cloud provider’s dashboard or kubectl topnodes. 

Larger nodes tend to be more cost-effective, because less of their resources are consumed by system overhead.

On the other hand, if your application is performing poorlybecause it’s spending a lot of time waiting for storage I/O, you may want to provision more IOPS to handle this.

Cleaning Up Unused Resources

Over time, if these lost resources are not cleaned up, they will start to represent a significant fraction of youroverall costs.

 it’s easy to forget to terminatea machine when it’s no longer in use.

Other types of cloud resources, such as load balancers, public IPs, and disk volumes, also cost you money eventhough they’re not in use. You should regularly review your usage of each type of resource, to find and removeunused instances.

. Fortunately, Kubernetes willautomatically clean up unused images when the node starts running short of disk space.2

 each resource must betagged with information about its owner. You can use Kubernetes annotations to do this (see “Labels andAnnotations”).

Set owner annotations on all your resources, giving information about who to contact if there’s a problem withthis resource, or if it seems abandoned and liable for termination.

Every Pod should expose the number of requests it receives as a metric (see Chapter 16 for more about this). Usethese metrics to find Pods that are getting low or zero traffic, and make a list of resources that can potentially beterminated.

once there are a significant number of completed Jobs, thiscan affect API performance. A handy tool for cleaning up completed Jobs is kube-job-cleaner.

There should always be enough spare capacity in the cluster to handle the failure of a single worker node. Tocheck this, try draining your biggest node (see “Scaling down”). 

. If this is not thecase, you need to add more capacity to the cluster.

Some cloud providers offer different instance classes depending on the machine’s life cycle. Reserved instancesoffer a trade-off between price and flexibility.

Use reserved instances when your needs aren’t likely to change for a year or two—but choose your reservationswisely, because they can’t be altered or refunded once they’re made.

A spot instance is cheap, but may be paused or resumed at any time, and may be terminated altogether.

Fortunately, Kubernetes is designed to provide high-availability services despite the loss of individual clusternodes.

Never bet more than you can afford to lose. 

replace the failed Node 2. Even once it’s available, there will be no Podson it. The scheduler never moves running Pods from one node to another.

We now have an unbalanced cluster, where all the Pods are on Node 1, and none are on Node 2 (Figure 5-2).

. Although you have two nodes, you have no highavailability. The failure of either Node 1 or Node 2 will result in a service outage.

The key to this problem is that the scheduler never moves Pods from one node to another unless they arerestarted for some reason.

Descheduler has various strategies and policies that you can configure. 

Kubernetes is pretty good at running workloads for you in a reliable, efficient way with no real need for manualintervention. 

Understanding how Kubernetes manages resources is key to building and running your cluster correctly. The mostimportant points to take away:

Minimal container images are faster to build, push, deploy, and start. The smaller the container, the fewerthe potential security vulnerabilities.

Liveness probes tell Kubernetes whether the container is working properly. If a container’s liveness probefails, it will be killed and restarted.

Readiness probes tell Kubernetes that the container is ready and able to serve requests. If the readinessprobe fails, the container will be removed from any Services that reference it, disconnecting it from usertraffic.

PodDisruptionBudgets let you limit the number of Pods that can be stopped at once during evictions,preserving high availability for your application.

ResourceQuotas let you set overall resource limits for a given namespace.

LimitRanges specify default resource requests and limits for containers in a namespace.

Set owner annotations on all your resources, and scan the cluster regularly for unowned resources.Find and clean up resources that aren’t being used (but check with their owners).

Reserved instances can save you money if you can plan your usage long-term.

 You can customize this behavior by adjusting the kubelet garbage collection settings.

### 6. Operating Clusters

How do youscale to cope with demand,

 there are many important things to consider about your Kubernetes cluster:

you’ll learn how to size and scale thecluster, check it for conformance, find security problems, and test the resilience of your infrastructure with chaosmonkeys.

 If the capacity is too large,you’re wasting money.

Capacity Planning

a single node. 

If you’re using a managed Kubernetes service like GKE (see “Google Kubernetes Engine (GKE)”), then you don’tneed to worry about provisioning master nodes: this is done for you. If, on the other hand, you’re building yourown cluster, you’ll need to decide how many master nodes to use.

The minimum number of master nodes for a resilient Kubernetes cluster is three. One wouldn’t be resilient, andtwo could disagree about which was the leader, so three master nodes are needed.

protect against node failure

Federation provides the ability to keep two or more clusters synchronized, running identical workloads. This canbe useful if you need Kubernetes clusters in different cloud providers, for resilience, or in different geographicallocations, to reduce latency for your users. A group of federated clusters can keep running even if an individualcluster fails.

For most Kubernetes users, federation isn’t something they need to be concerned with

If you need to replicate workloads across multiple clusters, perhaps for geographical redundancy or latencyreasons, use federation. Most users don’t need to federate their clusters, though.

Unless you’re operating at very large scale, as we mentioned in the previous section, you probably don’t needmore than one or two clusters: maybe one for production, and one for staging and testing.

For convenience and ease of resource management, you can divide your cluster into logical partitions usingnamespaces, 

 If you just want to partition your cluster for ease of management, usenamespaces instead.

The answer depends on your cloud or hardwareprovider, and on your specific workloads.

Use the most cost-effective node type that your provider offers. 

Because the Kubernetes components themselves, such as the kubelet, use a given amount of resources, and youwill need some spare capacity to do useful work, the smallest instance sizes offered by your cloud provider willprobably not be suitable for Kubernetes.

leaving smaller nodes free to handle smaller Pods 

to provide a single, unified, logical machine on which workloads can run. 

To avoid reducing the number of Pod replicas too much for a given service, you can use PodDisruptionBudgets tospecify a minimum number of available Pods, or the maximum number of Pods that can be unavailable at anytime (see “Pod Disruption Budgets”).

If draining a node would cause Kubernetes to exceed these limits, the drain operation will block until you changethe limits or free up some more resources in the cluster.

 Drain them first to ensure their workloads aremigrated to other nodes, and to make sure you have enough spare capacity remaining in the cluster.

When demand falls again, the group can be scaleddown to save money.

 Kubernetes itself includes a test suite which verifies that a given Kubernetes cluster is conformant;

The Cloud Native Computing Foundation (CNCF) is the official owner of the Kubernetes project and trademark

 provides various kinds of certifications for Kubernetes-related products, engineers, andvendors.

This indicates that the vendor and service meet the Certified Kubernetesstandard, as specified by the Cloud Native Computing Foundation (CNCF).

Certified Kubernetes Administrator

To become a Certified Kubernetes Administrator, you need to demonstrate that you have the key skills to manageKubernetes clusters in production, including installation and configuration, networking, maintenance, knowledgeof the API, security, and troubleshooting. 

 If you run your business onKubernetes, consider putting some of your staff through the CKA program, especially those directly responsiblefor managing clusters.

vendor has to be a CNCF member, provide enterprise support (for example by supplying field engineers to acustomer site), contribute actively to the Kubernetes community, and employ three or more CKA-certifiedengineers.

to double-check that it’sconfigured properly and up to date, you can run the Kubernetes conformance tests to prove it. 

the log messages will give you some helpful information about how to fix the problem.

but there are manycommon problems with Kubernetes configurations and workloads that conformance testing won’t pick up. Forexample:

Deployments that specify only a single Pod replica are not highly available.Running processes in containers as root is a potential security risk (see “Container Security”).

In this section, we’ll look at some tools and techniques that can help you find problems with your cluster, and tellyou what’s causing them.

, can check for common problems with your Kubernetes cluster and eithertake corrective action, or just send you a notification about them. 

K8Guard also exports metrics, which can be collected by a monitoring system like Prometheus 

It’s a good idea to leave K8Guard running on your cluster so that it can warn you about any violations as soon asthey happen.

Copper is a tool for checking your Kubernetes manifests before they’re deployed, and flagging common problemsor enforcing custom policies. It 

kube-bench is a tool for auditing your Kubernetes cluster against a set of benchmarks produced by the Centerfor Internet Security (CIS). 

Suppose you find a problem on your cluster, such as a Pod you don’t recognize, and you want to know where itcame from. How do you find out who did what on the cluster when? The Kubernetes audit log will tell you.

With audit logging enabled, all requests to the cluster API will be recorded, with a timestamp, saying who madethe request (which service account), the details of the request, such as which resources it queried, and what theresponse was.

The audit events can be sent to your central logging system, where you can filter and alert on them as you wouldfor other log data 

This kind of automated, random interference with production services is sometimes known as Chaos Monkeytesting, after the tool of the same name developed by Netflix to test its infrastructure:

Imagine a monkey entering a data center, these farms of servers that host all the critical functions of our onlineactivities. The monkey randomly rips cables, destroys devices...

The challenge for IT managers is to design the information system they are responsible for so that it can workdespite these monkeys, which no one ever knows when they arrive and what they will destroy.

kube-monkey runs at a preset time (by default, 8 a.m. on weekdays), and builds a schedule of Deployments thatwill be targeted during the rest of the day

If your applications require high availability, run a chaos testing tool such as chaoskube regularly to make surethat unexpected node or Pod failures don’t cause problems. Make sure you clear this first with the peopleresponsible for operating the cluster and the applications under test.

You can scale your cluster up and down manually without too much trouble, and you probably won’t haveto do it very often. Autoscaling is nice to have, but not that important.

Chaos testing is a process of knocking out Pods at random and seeing if your application still works. It’suseful, but the cloud has a way of doing its own chaos testing anyway, without you asking for it.

### 7. Kubernetes Power Tools

One of the first things that most Kubernetes users do to make their life easier is to create a shell alias for thekubectl command.

For example, we have the following alias set up in our .bash_profile files:

Now instead of having to type out kubectl in full for every command, we can just use k:

It’s very common to have kubectl operate on resources matching a set of labels, with the --selector flag (see“Labels”). Fortunately, this can be shortened to -l (labels):

To speed this up, kubectl supports short forms of these resource types:

Other useful abbreviations include no for nodes, cm for configmaps, sa for serviceaccounts, ds for daemonsets, andpv for persistentvolumes.

kubectl is no exception. You can get acomplete overview of the available commands with kubectl -h:

jq is a very powerful tool for querying and transforming JSON data.

 kubectl also supports JSONPath queries. JSONPath is a JSON query languagethat isn’t quite as powerful as jq, but useful for quick one-liners:

The kubectl edit command gives you the power to view and modify any resource:

, Bob should have checked the diff using kubectl diff (see “DiffingResources”) to see what would change. But if you’re not expecting something to be different, it’s easy tooverlook it. And maybe Bob hasn’t read this book.

Don’t use kubectl imperative commands such as create or edit on production clusters. Instead, always manageresources with version-controlled YAML manifests, applied with kubectl apply (or Helm charts).

Rather than typing a lot of boilerplate into an empty file, you can use kubectl to give you a head start, bygenerating the YAML manifest for you:

The --dry-run flag tells kubectl not to actually create the resource, but merely to print out what it would havecreated. The -o yaml flag gives you the resource manifest in YAML format. You can save this output to a file, edit

To do this, use the --export flag with kubectl get:

Before you apply Kubernetes manifests using kubectl apply, it’s very useful to be able to see exactly what wouldchange on the cluster. The kubectl diff command will do this for you.

When there are multiple containers in a Pod, you can specify which one you want to see the logs for using the--container flag (-c for short):

 Everything following the -- willbe passed to the container as a command line, complete with arguments.

Because the pattern for running 

 using the Dockerfile COPY --from command. A lesser-known feature of this command is thatyou can also copy a file from any public image, not just one that you built locally.

Now you still have a very small container, but you can also get a shell on it, by running:

To solve this problem, kubectl has the concept of contexts. A context is a combination of a cluster, a user, and anamespace (see “Using Namespaces”).

One nice feature of kubectx is that kubectx - will switch to your previous context, so you can quickly togglebetween two contexts:

kubectl diff will tell you what would change if you applied a manifest, without actually changing it.

You can see the output and error messages from any container with kubectl logs, stream themcontinuously with the --follow flag, or do more sophisticated multi-Pod log tailing with Stern.

To troubleshoot problem containers, you can attach to them with kubectl attach or get a shell on thecontainer with kubectl exec -it ... /bin/sh.

### 8. Running Containers

deploy your applications in a secure way, according to best practices. Finally, we’ll look at how tomount disk volumes on Pods, allowing containers to share and persist data.

talked about how Deployments use ReplicaSets to maintain aset of replica Pods

A container also has an entrypoint: a command that is run when the container starts. That usually results in thecreation of a single process to run the command, though some applications often start a few subprocesses to actas helpers or workers. 

you should think about splitting yourapplication up into multiple, communicating containers.

The registry hostname in this example is docker.io; in fact, that’s the default for Docker images, so wedon’t need to specify it. If your image is stored in another registry, though, you’ll need to give itshostname.

the default namespace (called library) is used. This is a set of official images, which areapproved and maintained by Docker, Inc. 

A semantic version tag, like v1.3.0. This usually refers to the version of the application.

If you don’t specify a tag when pulling an image, the default tag is latest. For example, when you run an alpineimage, with no tag specified, you’ll get alpine:latest.

Images can have many tags, but only one digest. This means that if your container manifest specifies the imagedigest, you can guarantee deterministic deployments. An image identifier with a digest looks like this:cloudnatived/demo@sha256:aeae1e551a6cbd60bcfd56c3b4ffec732c45b8012b7cb758c6c4a34...

Environment variables are a common, if limited, way to pass information to containers at runtime. 

The total size of a process’senvironment is also limited to 32 KiB, so you can’t pass large data files in the environment.

The value for runAsUser is a UID (a numerical user identifier). On many Linux systems, UID 1000 is assigned to thefirst non-root user created on the system, so it’s generally safe to choose values of 1000 or above for containerUIDs. It doesn’t matter whether or not a Unix user with that UID exists in the container

If a runAsUser UID is specified, it will override any user configured in the container image. If there is no runAsUser,but the container specifies a user, Kubernetes will run it as that user. If no user is specified either in the manifestor the image, the container will run as root (which, as we’ve seen, is a bad idea).

For maximum security, you should choose a different UID for each container. That way, if a container should becompromised somehow, or accidentally overwrite data, it only has permission to access its own data, and not thatof other containers.

block root containers from running, using the runAsNonRoot: true setting.

. It’s possible to imagine a container taking advantage of a bug in Docker or Kubernetes, forexample, where writing to its filesystem could affect files on the host node. If its filesystem is read-only, thatcan’t happen; the container will get an I/O error:

Many containers don’t need to write anything to their own filesystem, so this setting won’t interfere with them.It’s good practice to always set readOnlyRootFilesystem unless the container really does need to write to files.

Pod Security PoliciesRather than have to specify all the security settings for each individual container or Pod, you can specify them atthe cluster level using a PodSecurityPolicy resource.

It’s a little more complicated to use PodSecurityPolicies, as you have to create the policies, grant the relevantservice accounts access to the policies via RBAC (see “Introducing Role-Based Access Control (RBAC)”), andenable the PodSecurityPolicy admission controller in your cluster. 

Pods run with the permissions of the default service account for the namespace, unless you specify otherwise(see “Applications and Deployment”). If you need to grant extra permissions for some reason (such as viewingPods in other namespaces), create a dedicated service account for the app, bind it to the required roles, andconfigure the Pod to use the new service account.

To do that, set the serviceAccountName field in the Pod spec to the name of the service account:

There are many different types of Volume that you can attach to a Pod. 

Storing persistent data significantly complicates the Kubernetes configuration for your app,uses extra cloud resources, and it also needs to be backed up.

As you know, Kubernetes will download your specified image from the container registry if it isn’t already present on the node. 

The imagePullSecrets field on a Pod allows you to configure this. First, you need to store the registry credentials in a Secret object

A Linux container, at the kernel level, is an isolated set of processes,

Containers are not virtual machines. Each container should run one primary process.

Containers in the same Pod can share data by reading and writing a mounted Volume. The simplestVolume is of type emptyDir, which starts out empty and preserves its contents only as long as the Pod isrunning.

### 9. Managing Pods

There are no big problems, there are just a lot of little problems.

 Let’s take a closer look at labels and selectors in this section.

Labels are key/value pairs that are attached to objects, such as pods. Labels are intended to be used tospecify identifying attributes of objects that are meaningful and relevant to users

In other words, labels exist to tag resources with information that’s meaningful to us, but they don’t meananything to Kubernetes. 

Now, by itself, this label has no effect.

If you want to see what labels are defined on your Pods, use the --show-labels flag to kubectl get

 Annotations, on the other hand, are for non-identifying information, to be used by tools orservices outside Kubernetes. For example, in “Helm Hooks” there’s an example of using annotations to controlHelm workflows.

The long names of the hard and soft affinity types make the point that these rules apply during scheduling, butnot during execution. 

This adds a taint called dedicated=true to the docker-for-desktop node, with the effect NoSchedule: no Pod cannow be scheduled there unless it has a matching toleration.

 Because the toleration matches the taint, the Pod can be scheduled. Any Pod without this tolerationwill not be allowed to run on the tainted node.

If the container exits for some reason, you have to manually restart it.

When you update the container, you have to take care of stopping each running image in turn, pulling thenew image and restarting it.

That’s the kind of work that Kubernetes is designed to take off your hands using controllers. In “ReplicaSets”, weintroduced the ReplicaSet controller, which manages a group of replicas of a particular Pod. It works continuouslyto make sure there are always the specified number of replicas, starting new ones if there aren’t enough, andkilling off replicas if there are too many.

Suppose you want to send logs from all your applications to a centralized log server

The term daemon traditionally refers to long-running background processes on a server that handle things likelogging

DaemonSets run a daemon container on each node in the cluster.

 What a StatefulSet adds is the abilityto start and stop Pods in a specific sequence.

With a Deployment, for example, all your Pods are started and stopped in a random order. This is fine forstateless services, where every replica is identical and does the same job.

A StatefulSet is ideal for this.

the replicas will be shut down in reverse order, waiting for each Pod to finish beforemoving on to the next.

With a headless service, you still get that single service DNS name, but you also get individual DNS entriesfor each numbered Pod, like redis-0, redis-1, redis-2, and so on.

Probably the most common way to run a Job, though, is to start it periodically, at a given time of day or at agiven interval. Kubernetes has a special type of Job just for this: the Cronjob.

A Cronjob looks like this:

A Horizontal Pod Autoscaler (HPA) watches a specified Deployment, constantly monitoring a given metric to see ifit needs to scale the number of replicas up or down.

This is easy to do, as we saw in“Building Your Own Kubernetes Tools”. Such a program is called an operator (perhaps because it automates thekinds of actions that a human operator might perform).

You don’t need any custom objects in order to write an operator; DevOps engineer Michael Treacher has writtena nice example operator that watches for namespaces being created, and automatically adds a RoleBinding toany new namespace

 The Ingress receivesrequests from clients and sends them on to the Service. The Service then sends them to the right Pods, based onthe label selector 

This Ingress forwards traffic to a Service named demo-service on port 80.

In addition, Ingress can handle secure connections using TLS (the protocol formerly known as SSL). 

If you run cert-manager in your cluster, it will automatically detect TLS Ingresses that have no certificate, andrequest one from the specified provider (for example, LetsEncrypt).

An Ingress controller is responsible for managing Ingress resources in a cluster. Depending on where you arerunning your clusters, the controller you use may vary.

Usually, customizing the behavior of your Ingress is done by adding specific annotations that are recognized bythe Ingress controller.

GKE have the option to use Google’s Compute Load Balancer for Ingress. 

These are good places to start if you need to use Ingress 

You also have the option to install and run your own Ingress controller inside your cluster, or even run multiplecontrollers if you like. Some popular options include:

Read about the different options and try themout in your own cluster

Istio is an example of what’s often referred to as a service mesh, and becomes very useful when teams havemultiple applications and services that communicate with each other. It handles routing and encrypting networktraffic between services, and adds important features like metrics, logs, and load balancing.

Labels are key-value pairs that identify resources, and can be used with selectors to match a specifiedgroup of resources.

DaemonSets allow you to schedule one copy of a Pod on every node (for example, a logging agent).

StatefulSets start and stop Pod replicas in a specific numbered sequence, allowing you to address eachby a predictable DNS name. This is ideal for clustered applications, such as databases.

Ingress resources route requests to different services, depending on a set of rules, for example, matchingparts of the request URL. They can also terminate TLS connections for your application.

### 10. Configuration and Secrets

It’s very useful to be able to separate the logic of your Kubernetes application from its configuration: that is, anyvalues or settings that might change over the life of the application. 

environment-specific settings, DNS addresses of third-party services, and authentication credentials.

For one thing,changing a configuration value would require a complete rebuild and redeploy of the application. It’s much betterto separate these values out from the code and read them in from a file, or from environment variables.

Kubernetes provides a few different ways to help you manage configuration. 

Another is to storeconfiguration data directly in Kubernetes, using the ConfigMap and Secret objects.

One way is to specify that data, as literal YAML values, in the ConfigMap manifest. This is what the manifest fora ConfigMap object looks like:

You can create a ConfigMap directly from aYAML file as follows:[插图]

This time, though, instead of hard coding the string Hello into the application, we’d like to make the greetingconfigurable. 

Don’t worry about the exact details of the Go code; it’s just a demo. 

 it will use that value when responding to requests.

You’ll find the manifest file for the ConfigMap,along with the modified Go application, in the hello-config-env directory of the demo repo.

It looks like this:

we need to modify the Deployment slightly.

Here’s the relevant part of the demo Deployment:

We still have name here, but instead of value, we’ve specified valueFrom. This tells Kubernetes that, rather thantaking a literal value for the variable, it should look elsewhere to find the value.

If the ConfigMap doesn’t exist, the Deployment won’t be able to run (its Pod will show a status ofCreateContainerConfigError).

As before, to see the application in your web browser, you’ll need to forward a local port to the Pod’s port 8888:

If you point your web browser to http://localhost:9999/ you should see, if all is well:

 Reapply the file with kubectl. Refresh the web browser. Does the greetingchange? If not, why not? 

Fortunately, there’s an easy way to take all the keys from a ConfigMap and turn them into environment variables,using envFrom:

Kubernetes allows you touse either env, envFrom, or both at once, to set environment variables.

For example, if you setthe variable GREETING in both env and a ConfigMap referenced in envFrom, the value specified in env will overridethe one from the ConfigMap.

Kubernetes replaces anything of the form $(VARIABLE) in a manifest with the value of the environment variableVARIABLE. Since we’ve created the GREETING variable and set its value from the ConfigMap, it’s available for use inthe container’s command line.

More complex applications, however, often expect to read theirconfiguration from files on disk.

Looking at the volumes section, you can see that we create a Volume named demo-config-volume, from theexisting demo-config ConfigMap.

In the container’s volumeMounts section, we mount this volume on the mountPath: /config/, select the key config,and write it to the path demo.yaml. The result of this will be that Kubernetes will create a file in the container at/config/demo.yaml, containing the demo-config data in YAML format:

If you update a ConfigMap and change its values, the corresponding file (/config/demo.yaml in our example) willbe updated automatically. Some applications may autodetect that their config file has changed and reread it;others may not.

One option is to redeploy the application to pick up the changes 

 if the application has a way to trigger a live reload, such as a Unix signal (for exampleSIGHUP) or running a command in the container.

Updating Pods on a Config Change

there’s a neat trick to have itautomatically detect a config change and reload your Pods. Add this annotation to your Deployment spec:

When you run helm upgrade, Helm will detect that the Deployment spec has changed, and restartall the Pods.

most applications have some config data that is secret and sensitive

While we could use ConfigMaps to store these, that’s not an ideal solution.

Instead, Kubernetes provides a special type of object intended to store secret data: the Secret. Let’s see anexample of how to use it with the demo application.

First, here’s the Kubernetes manifest for the Secret

In this example,

 As with a ConfigMap, you can put multiple keys and values into a Secret. 

Just like ConfigMaps, Secrets can be made visible to containers by putting them into environment variables, ormounting them as a file on the container’s filesystem. In this example, we’ll set an environment variable to thevalue of the Secret:

Run the following command in the demo repo directory to apply these manifests:

As before, forward a local port to the Deployment so you can see the results in your web browser:

Browse to http://localhost:9999/ and you should see:The magic word is "xyzzy"

In this example we’ll mount the Secret on the container as a file. You’ll find the code for this example in thehello-secret-file folder of the demo repo.

The mountPath is /secrets, andKubernetes will create one file in this directory for each of the key-value pairs defined in the Secret.

This prevents secret data being exposedaccidentally.

In fact, it’s a base64 representation ofthe Secret. Base64 is a scheme for encoding arbitrary binary data as a character string.

Because the secret data could be nonprintable binary data (for example, a TLS encryption key), KubernetesSecrets are always stored in base64 format.

if youhave permission to read the Secrets in a particular namespace, you can get the data in base64 format and thendecode it.

What about someone with access to the etcd database where all Kubernetes information is stored? Could theyaccess the secret data, even without API permissions to read the Secret object?

 In a properly configured cluster,encryption at rest should be enabled.

If you don’t see the experimental-encryption-provider-config flag, then encryption at rest is not enabled. (Ifyou’re using Google Kubernetes Engine, or some other managed Kubernetes services, your data is encryptedusing a different mechanism and you won’t see this flag. Check with your Kubernetes provider to find out whetheretcd data is encrypted or not.)

Using a Helm-specific annotation, you can prevent a resource from being removed:

Where do you store secrets so that they are highly available?

How do you make secrets available to your running applications?

What needs to happen to your running applications when you rotate or change secrets?

In this section we’ll look at three of the most popular secrets management strategies, and examine how each ofthem tackles these questions.

The first option for secrets management is to store your secrets directly in code, in version control repositories,but in encrypted form, and decrypt them at deploy time.

This is probably the simplest choice. Secrets are put directly into source code repos, but never in plain text.Instead, they are encrypted in a form that can only be decrypted with a certain trusted key.

To change or rotate secrets, just decrypt them in your local copy of the source, update them, re-encrypt, andcommit the change to version control.

there’s one potential drawback.

You may want to restrict access to the encryption key to onlycertain individuals, rather than handing it out to all developers.

 we’ll outline some options forencryption/decryption tools you can use to do this, but first, let’s briefly describe the other secrets managementstrategies.

While the encrypt secrets in source code and keep secrets in a bucket strategies are fine for most organizations,at very large scale you may need to think about using a dedicated secrets management tool, such as Hashicorp’sVault, 

These tools handle securely storing all ofyour application secrets in one central place in a highly available way, and can also control which users andservice accounts have permissions to add, remove, change, or view secrets.

In a secrets management system, all actions are audited and reviewable, making it easier to analyze securitybreaches and prove regulatory compliance.

Some of these tools also provide the ability to automatically rotatesecrets on a regular basis, which is not only a good idea in any case, but is also required by many corporatesecurity policies.

How do applications get their data from a secrets management tool? One common way is to use a serviceaccount with read-only access to the secrets vault, so that each application can only read the secrets it needs.

While a central secrets management system is the most powerful and flexible option available, it also addssignificant complexity to your infrastructure. As well as setting up and running the secrets vault, you will need toadd tooling or middleware to each application and service that consumes secrets.

Of the various options, one of the most popular is Vault, from Hashicorp.

While, at first glance, a dedicated secrets management system such as Vault might seem to be the logical choice,we don’t recommend you start with this.

Instead, try out a lightweight encryption tool such as Sops (see“Encrypting Secrets with Sops”), encrypting secrets directly in your source code.

Why? Well, you may not actually have that many secrets to manage. Unless your infrastructure is very complexand interdependent, which you should be avoiding anyway, any individual application should only need one or twopieces of secret data: API keys and tokens for other services, for example, or database credentials. If a given appreally needs a great many different secrets, you might consider putting them all in a single file and encrypting thatinstead.

We take a pragmatic approach to secrets management, as we have with most issues throughout this book. If asimple, easy-to-use system solves your problem, start there. You can always switch to a more powerful orcomplicated setup later. 

Rather than encrypting the whole file, Sops encrypts only theindividual secret values. For example, if your plain-text file contains:

the resulting file will look like this:

This makes it easy to edit and review code, especially in pull requests, without needing to decrypt the data inorder to understand what it is.

Visit the Sops project home page for installation and usage instructions.

 When you run helm upgrade or helm install,helm-secrets will decrypt your secrets for deployment. For more information about Sops, including installation andusage instructions, consult the GitHub repo.

Let’s try out Sops by encrypting a file. 

We won’t get into the details of how PGP encryption works, but just know that, like SSH and TLS, it’s a publickey cryptosystem. Instead of encrypting data with a single key, it actually uses a pair of keys: one public, oneprivate. You can safely share your public key with others, but you should never give out your private key.

 if you haven’t got it already.

Once that’s installed, run this command to generate a new key pair:

Now let’s create a test secret file to encrypt:

Another nice feature of Sops is that only the value of password is encrypted, so the YAML format of the file ispreserved, and you can see that the encrypted data is labeled password. If you have a long list of key-value pairsin your YAML file, Sops will encrypt only the values, leaving the keys alone.

Configuration and secrets is one of the topics that people ask us about the most in relation to Kubernetes. 

Separate your configuration data from application code and deploy it using Kubernetes ConfigMaps andSecrets. That way, you don’t need to redeploy your app every time you change a password.

Don’t overthink secrets management, especially at first. Start with something simple that’s easy to set upfor developers.

For enterprise-level secrets management, you’ll need a dedicated service such as Vault. But don’t startwith Vault, because you may end up not needing it. You can always move to Vault later

### 11. Security and Backups

 We’ll also look at some useful ways toget information about what’s happening in your cluster.

 everyone has administrator access onevery system.

There are generally two groups of people who need to access Kubernetes clusters: cluster operatorsand application developers, and they often need different permissions and privileges as part of their job function.

 it’s often a good idea to have separate clusters for production andstaging or testing. 

 it willnot impact production.

many small clusters tend to run less efficiently than larger clusters.

RBAC is designed to grant specific permissions to specific users

If you’re running a self-hosted cluster, try this command to see whether or not RBAC is enabled on your cluster:

Check thedocumentation for your service provider or installer to see how to rebuild the cluster with RBAC enabled.

Without RBAC, anyone with access to the cluster has the power to do anything, including running arbitrary codeor deleting workloads. This probably isn’t what you want.

So, assuming you have RBAC enabled, how does it work? The most important concepts to understand are users,roles, and role bindings.

A role describes a specific set of permissions. Kubernetes includessome predefined roles to get you started. For example, the cluster-admin role, intended for superusers, hasaccess to read and change any resource in the cluster. By contrast, the view role can list and examine mostobjects in a given namespace, but not modify them.

Here’s an example of a ClusterRole manifest that grants read access to secrets in anynamespace:

Here’s the RoleBinding manifest that gives the daisy user the edit role in the demo namespace only:[插图]

users start with no permissions, and you can add them using Roles andRoleBindings. You can’t subtract permissions from someone who already has them.

You can read more about the details of RBAC, and the available roles and permissions, in the Kubernetesdocumentation.

So what roles and bindings should you set up in your cluster? The predefined roles cluster-admin, edit, and viewwill probably cover most requirements. To see what permissions a given role has, use the kubectl describecommand:

Be very careful about who has access to the cluster-admin role. This is the cluster superuser, equivalent to theroot user on Unix systems. It can do anything to anything. Never give this role to users who are not clusteroperators, and especially not to service accounts for apps which might be exposed to the internet, such as theKubernetes Dashboard (see “Kubernetes Dashboard”).

You’ll find some bad advice about this on sites likeStack Overflow. When faced with a Kubernetes permissions error, a common response is to grant the cluster-admin role to the application. Don’t do this. Yes, it makes the errors go away, but at the expense of bypassing allsecurity checks and potentially opening up your cluster to an attacker.

Instead, grant the application a role withthe fewest privileges it needs to do its job.

 all Pods willrun as the default service account in their namespace, which has no roles associated with it.

If your app needs access to the Kubernetes API for some reason (for example, a monitoring tool that needs to listPods), create a dedicated service account for the app, use a RoleBinding to associate it with the necessary role(for example, view), and limit it to specific namespaces.

What about the permissions required to deploy applications to the cluster? The most secure way is to allow only acontinuous deployment tool to deploy apps (see Chapter 14). It can use a dedicated service account, withpermission to create and delete Pods in a particular namespace.

The edit role is ideal for this. Users with the edit role can create and destroy resources in the namespace, butcan’t create new roles or grant permissions to other users.

. People who don’t need to deploy apps should have only the viewrole by default.

Make sure RBAC is enabled in all your clusters. Give cluster-admin rights only to users who actually need thepower to destroy everything in the cluster. If your app needs access to cluster resources, create a service accountfor it and bind it to a role with only the permissions it needs, in only the namespaces where it needs them.

 if you’re still working out the requiredpermissions for your own application, you may run into RBAC permission errors. What do these look like?

If an application makes an API request for something it doesn’t have permission to do (for example, list nodes) itwill see a Forbidden error response (HTTP status 403) from the API server:

If the application doesn’t log this information, or you’re not sure which application is failing, you can check theAPI server’s log 

 It will record messages like this,containing the string RBAC DENY with a description of the error:

 see the documentation for your Kubernetes provider to find out how to access APIserver logs.)

Just grant users the minimum privileges theyneed, keep cluster-admin safe, and you’ll be fine.

If you’re running third-party software in your cluster, it’s wise to check it for security problems and malware. 

Clair is an open source container scanner produced by the CoreOS project. It statically analyzes containerimages, before they are actually run, to see if they contain any software or versions that are known to beinsecure.

You can run Clair manually to check specific images for problems, or integrate it into your CD pipeline to test allimages before they are deployed (see Chapter 14).

Alternatively, Clair can hook into your container registry to scan any images that are pushed to it and reportproblems.

MicroScanner is installed by adding it to a Dockerfile, like this:

Don’t run containers from untrusted sources or when you’re not sure what’s in them. Run a scanning tool likeClair or MicroScanner over all containers, even those you build yourself, to make sure there are no knownvulnerabilities in any of the base images or dependencies.

Replication is not backup. While replication may protect you from the failure of the underlying storage volume, itwon’t protect you from accidentally deleting the volume by mis-clicking in a web console, for example.

Nor will replication prevent a misconfigured application from overwriting its data, or an operator from running acommand with the wrong environment variables and accidentally dropping the production database instead of thedevelopment one. (

If you run your own master nodes, you are responsible for managing etcd clustering, replication, and backup.Even with regular data snapshots, it still takes a certain amount of time to retrieve and verify the snapshot,rebuild the cluster, and restore the data. During this time your cluster will likely be unavailable or seriouslydegraded.

Apart from etcd failures, there is also the question of saving the state of your individual resources. If you deletethe wrong Deployment, for example, how would you re-create it?

applying YAML manifests or Helm charts stored inversion control.

We’ve recommended throughout this book that you should avoid making direct changes to resources, and insteadapply changes from the updated manifest files 

In any case, it’s likely that during initial deployment and testing of apps, engineers may be adjusting settings likereplica count and node affinities on the fly, and only storing them in version control once they’ve arrived at theright values.

One way to help ensure this is to make a snapshot of the running cluster, which you can refer to later in case ofproblems.

 specify the wrong set of labels to a kubectl delete command, removing morethan you intended.

Whatever the cause, disasters do happen, so let’s look at a backup tool that can help you avoid them.

Follow the instructions to set up Velero for your platform.

Velero can also back up the contents of your persistent volumes. To tell it where to store them, you need tocreate a VolumeSnapshotLocation object:

When you create a backup using the velero backup command, the Velero server queries the Kubernetes API toretrieve the resources matching the selector you provided (by default, it backs up all resources). You can back upa set of namespaces, or the whole cluster:

an incremental backup

So, to restore a backup, you only need themost recent backup file.

If the resource does exist, but is different from the one in the backup, Velero will warn you, but not overwrite theexisting resource. So, for example, if you want to reset the state of a running Deployment to the way it was in themost recent snapshot, delete the running Deployment first, then restore it with Velero.

You should write a detailed, step-by-step procedure describing how to restore data from backups, and makesure all staff know where to find this document. When a disaster happens, it’s usually at an inconvenient time, thekey people aren’t available, everyone’s in a panic, and your procedure should be so clear and precise that it canbe carried out by someone who isn’t familiar with Velero or even Kubernetes.

Each month, run a restore test by having a different team member execute the restore procedure against atemporary cluster. This verifies both that your backups are good and that the restore procedure is correct, andmakes sure everyone is familiar with how to do it.

Scheduling Velero backups

All backups should be automated, and Velero is no exception. You can schedule a regular backup using thevelero schedule create command:

While Velero is extremely useful for disaster recovery, you can also use it to migrate resources and data from onecluster to another—a process sometimes called lift and shift.

Making regular Velero backups can also help you understand how your Kubernetes usage is changing over time;comparing the current state to the state a month ago, six months ago, and a year ago, for example.

The snapshots can also be a useful source of audit information: for example, finding out what was running in yourcluster at a given date or time, and how and when the cluster state changed.

Use Velero to back up your cluster state and persistent data regularly: at least nightly. Run a restore test at leastmonthly.

we’ll be concerned only with monitoring the Kubernetes cluster itself: the health of thecluster, the status of individual nodes, and the utilization of the cluster and the progress of its workloads.

The kubectl get componentstatuses command (or kubectl get cs for short) gives health information for thecontrol plane components—the scheduler, the controller manager, and etcd:

Another useful command is kubectl get nodes, which will list all the nodes in your cluster, and report their statusand Kubernetes version:

Accordingly, kubectl get nodes lists the worker nodes only (a role of <none> indicates a worker node).


If any of the nodes shows a status of NotReady, there is a problem. A reboot of the node may fix it, but if not, it may need further debugging—or you could just delete it and create a new node instead.


For detailed troubleshooting of bad nodes, you can use the kubectl describe node command to get more information:


This will show you, for example, the memory and CPU capacity of the node, and the resources currently in use by Pods.


If any Pods are not in Running status, like the permissions-auditor Pod in the example, it may need further investigation.


The reason is shown in the STATUS column: CrashLoopBackOff. The container is failing to start properly.



When a container crashes, Kubernetes will keep trying to restart it at increasing intervals, starting at 10 seconds and doubling each time, up to 5 minutes. This strategy is called exponential backoff, hence the CrashLoopBackOff status message.

how much of each is currently in use:



For Pods, it will show how much CPU and memory each specified Pod is using:
kubectl top pods -n kube-system

Cloud Provider Console
If you’re using a managed Kubernetes service that is offered by your cloud provider, then you will have access to a web-based console that can show you useful information about your cluster, its nodes, and workloads.


You can also list workloads, services, and configuration details for the cluster. This is much the same information as you can get from using the kubectl tool, but the GKE console also allows you to perform administration tasks: create clusters, upgrade nodes, and everything you’ll need to manage your cluster on a day-to-day basis.

The Dashboard lets you view the contents of ConfigMaps and Secrets, which could contain credentials and crypto keys, so you need to control access to the Dashboard as tightly as you would to those secrets themselves.

make sure it has minimum privileges, and never expose it to the internet. Instead, access it via kubectl proxy.

Instead, it gives you a visualization of what’s happening in your cluster: what nodes there are, the CPU and memory utilization on each, how many Pods each one is running, and the status of those Pods (Figure 11-3).

Although Kubernetes currently does not take any action in response to events from the node-problem-detector, there may be further integration in the future that will allow the scheduler to avoid running Pods on problem nodes, for example.

We recommend it highly.






The main things to keep in mind:

Make sure it’s enabled, and use RBAC roles to grant specific users and apps only the minimum privileges they need to do their jobs.


Use a scanning tool to check any containers you run in production.


•  kubectl is a powerful tool for inspecting and reporting on all aspects of your cluster and its workloads. Get friendly with kubectl. You’ll be spending a lot of time together.

### 12. Deploying Kubernetes Applications

we’ll deal with the question of how to turn your manifest files into running applications. 

but it’s not ideal. Not only is it difficult to maintain these files, but there is also a problem ofdistribution.

To do this, they will have to make their own copy of the Kubernetes configs, find where the various settings aredefined (perhaps duplicated in several places), and edit them.

Over time, they will need to maintain their own copies of the files, and when you make updates available, they willhave to pull and reconcile them manually with their local changes.

This eventually starts to become painful. What we want is the ability to separate the raw manifest files from theparticular settings and variables that you or any user of the application might need to adjust. 

In the demo repo, open up the hello-helm/k8s directory to see what’s inside our Helm chart.

There is also a file named values.yaml, which contains user-modifiable settings that the chart author hasexposed:

 The values.yaml file iscompletely free-form YAML, with no predefined schema: it’s up to you to choose what variables are defined, theirnames, and their values.

instead of referring to things like the container name directly, they contain a placeholderthat Helm will replace with the actual value from values.yaml.

The curly braces indicate a place where Helm should substitute the value of a variable, but they’re actually part ofGo template syntax.

(Yes, Go is everywhere. Kubernetes and Helm themselves are written in Go, so it’s no surprise that Helm chartsuse Go templates.)

The Go template format is very powerful, and you can use it to do much more than simple variable substitutions:it supports loops, expressions, conditionals, and even calling functions. 

You can use the quote function in Helm to automatically quote values in your templates:

Only string values should be quoted; don’t use the quote function with numeric values like port numbers.

What if your chart relies on other charts? 

To see the list of values a chart makes available for you to set, run helm inspect valueswith a chart name:

The helm upgrade command will do this for you. 

 it’s easy to rollback to a previous version, using the helm rollback command, and specifying the number of a previous release(as shown in helm history output):

Once all your charts are collected together under a single directory, run helm repo index on that directly to createthe index.yaml file that contains the repo metadata.

Your chart repo is ready to use! See the Helm documentation for more details on managing chart repos.

Sops decrypts the staging-secrets file and writes the decrypted output to temp-staging-secrets.

It’s a good idea to add kubeval to your continuous deployment pipeline, so that manifests are automaticallyvalidated whenever you make changes to them. You can also use kubeval to test

While you can deploy applications to Kubernetes using just raw YAML manifests, it’s inconvenient. Helm is apowerful tool that can help with this, provided you understand how to get the best out of it.

A quick way to test and validate manifests is to use kubeval, which will check for valid syntax andcommon errors in manifests.

### 13. Development Workflow

Draft also introduces the concept of Draft Packs: prewritten Dockerfiles and Helm charts for whatever languageyour app is using. Currently there are packs available for .NET, Go, Node, Erlang, Clojure, C#, PHP, Java, Python,Rust, Swift, and Ruby.

If you are just starting out with a new application, and you don’t yet have a Dockerfile or Helm chart, Draft maybe the perfect tool for you to get up and running quickly. When you run draft init && draft create, Draft willexamine the files in your local application directory and try to determine which language your code uses. It willthen create a Dockerfile for your specific language, as well as a Helm chart.

To apply these, run the draft up command. Draft will build a local Docker container using the Dockerfile itcreated and deploy it to your Kubernetes cluster.

Knative integrates with both Kubernetes and Istio (see “Istio”) to provide a complete application/functiondeployment platform, including setting up a build process, automated deployment

so that there’s no interruptionin service: a so-called zero-downtime deployment.

RollingUpdate is the zero-downtime, Pod-by-Pod option, while Recreate is the fast, all-Pods-at-once option. 

The default isRollingUpdate, so if you don’t specify a strategy, this is what you’ll get. To change the strategy to Recreate, set itlike this:

In a rolling update, Pods are upgraded one at a time, until all replicas have been replaced with the new version.

During a rolling update, both old and new versions of your application will be in service at the same time. 

if your changes involve adatabase migration, for example (see “Handling Migrations with Helm”), a normal rolling update won’t bepossible.

If your Pods can sometimes crash or fail after a short time in a ready state, use the minReadySeconds field to havethe rollout wait until each Pod is stable (see “minReadySeconds”).

In Recreate mode, all running replicas are terminated at once, and then new ones are created.

One advantage of Recreate isthat it avoids the situation where two different versions of the application are running simultaneously (see “RollingUpdates”).

. Two important settings govern this behavior: maxSurge and maxUnavailable:

maxSurge sets the maximum number of excess Pods. 

These can be set as either a whole number, or as a percentage:

The bigger the value of maxSurge, the faster your rollout, but the more extra load it places on your cluster’sresources. Large values of maxUnavailable also speed up rollouts, at the expense of your application’s capacity.

On the other hand, small values of maxSurge and maxUnavailable reduce the impact on your cluster and your users,but can make your rollouts take much longer. Only you can decide what the right trade-off is for your application.

In a blue/green deployment, rather than killing and replacing Pods one at a time, a whole new Deployment is created, and a new separate stack of Pods running v2 is launched beside the existing v1 Deployment.


Kubernetes uses labels to decide which Pods should receive traffic from a Service. One way to implement a blue/green deployment is to set different labels on your old and new Pods (see “Labels”).


When deploying a new version, you can label it deployment: green. It won’t receive any traffic, even when it’s fully up and running, because the Service only sends traffic to blue Pods. You can test it and make sure it’s ready before making the cutover.

To switch over to the new Deployment, edit the Service to change the selector to deployment: green. Now the new green Pods will start receiving traffic, and once all the old blue Pods are idle, you can shut them down.


Stateless
 applications are easy to deploy and upgrade, but when a database is involved, the situation can be more complicated. Changes to the schema of a database usually require a migration task to be run at a specific point in the rollout

For example, with Rails apps, you need to run rake db:migrate before starting the new Pods.


On Kubernetes, you can use a Job resource to do this (see “Jobs”). You could script this using kubectl commands as part of your upgrade process, or if you are using Helm, then you can use a built-in feature called hooks.


The pre-upgrade setting tells Helm to apply this Job manifest before doing an upgrade. The Job will run the standard Rails migration command.



If the Job returns a nonzero exit code, this is a sign that there was an error and the migration did not complete successfully. Helm will leave the Job in place in its failed state, so that you can debug what went wrong.

see what happened.

Once the issue has been resolved, you can delete the failed job (kubectl delete job <job-name>) and then try the upgrade again.

•  pre-install executes after templates are rendered, but before any resources are created.


•  post-upgrade executes on an upgrade after all resources have been upgraded.

rolling out changes to production is far easier with Kubernetes than with traditional servers

•  You can adjust the maxSurge and maxUnavailable fields to fine-tune rolling updates.

•  Rainbow deployments are similar to blue/green deployments, but with more than two versions in service simultaneously.


Hooks can define the order in which resources should be applied during a deployment, and cause the deployment to halt if something does not succeed.


### 14. Continuous Deployment in Kubernetes

The machinery of continuous deployment is often referred to as a pipeline: a series of automated actions thattake code from the developer’s workstation to production, via a sequence of test and acceptance stages.

A developer pushes her code changes to Git.

The newly built container is deployed automatically to a staging environment.The staging environment undergoes some automated acceptance tests.The verified container image is deployed to production.

The great benefit of CD is no surprises in production; nothing gets deployed unless the exact binary image hasalready been successfully tested in staging.

If you are migratingexisting applications to Kubernetes, you can almost certainly do this with a few changes to your existing buildsystem.

Jenkins is a very widely adopted CD tool and has been around for years. It has plugins for just about everythingyou could want to use in a CD workflow, including Docker, kubectl, and Helm.

also a newer 

that can be used for testing and deploying your code.

If you are already using GitLab, it makes sense to look at GitLab CI for implementing your continuous deploymentpipeline.

 One interesting featureis the ability to deploy temporary staging environments for every feature branch.

The pattern of triggering a CD pipeline (or other automated processes) on Git branches or tags is sometimescalled GitOps. Flux extends this idea by watching for changes to a container registry, instead of a Git repo. Whena new container is pushed, Flux will deploy it automatically to your Kubernetes cluster.Keel

 wait for approvals before deploying, and do other usefulworkflows.

run helm template to generate the Kubernetes manifests,and then pipe them through the kubeval tool to check them (see “kubeval”):

In Git, every commit has a unique identifier, called a SHA (named for the SecureHash Algorithm that generates it). A SHA is a long string of hex digits, like5ba6bfd64a31eb4013ccaba27d95cddd15d50ba3.

 By building your own custom image with a multi-stage build, youcan reduce the image size to around 20 MiB. Since this image will be pulled on every build, making it 40 timessmaller is worth it.

Because there are so many options for CD software and techniques, we can’t give you a single recipe that’ll workfor everybody.

Deciding which CD tools to use is an important process when building a new pipeline. All of the tools wemention throughout this book could likely be incorporated into almost any existing CD tool.

Defining the build pipeline steps with code allows you to track and modify these steps alongsideapplication code.

 The New York Times dev team wrote a useful blog post on deploying to GKE with Drone.

### 15. Observability and Monitoring

 we’ll consider the question of observability and monitoring for cloud native applications.

 How do you do monitoring, logging, metrics, and tracing inKubernetes?

Observability may not be a familiar term to you

 But when we talk aboutmonitoring in a DevOps context, we mostly mean automated monitoring.

Automated monitoring is checking the availability or behavior of a website or service, in some programmatic way,usually on a regular schedule, and usually with some automated way of alerting human engineers if there’s aproblem. But what defines a problem?

 So the simplest possible monitoring check for this site is to fetch the home page and checkthe HTTP status code (200 indicates a successful request). 

 If the exit status from the client is nonzero, there was a problem fetching the website.

 the site is actually down for users

the monitoring check might also try to log in with a precreated user account and alert ifthe login fails. 

How long does it take to load for most of my users?

If it relies on a third-party service, what happens to my application when that external service is faulty orunavailable?

What happens when my cloud provider has an outage?

To be sure, having any kind of automated monitoringof your systems is a huge improvement on having none. But there are a few limitations of black-box checks:

They only check the behavior of the parts of the system that are exposed to the outside.

 five nines (99.999%!)(MISSING) is about five minutes.

. But looking at things this way misses an important point:Nines don’t matter if users aren’t happy.

 It might work fine after that, but if it’s tooslow to respond, it might as well be down completely. Users will just go elsewhere.

With a hard threshold like this, youcould consider the service down for some users, but up for others. What if load times are fine for users in NorthAmerica, but unusable from Europe or Asia?

Let’s see if we can do better.

The URI requestedThe IP address of the client

The HTTP status of the response

If the application encounters an error, it usually logs this fact

 will be aggregated into a central database

 Tools likeLogstash and Kibana, or hosted services such as Splunk and Loggly, are designed to help you gather and analyzelarge volumes of log data.

It can also be hard to extract information from logs, because every application writes logs in a different format

can become a bottleneck.

The same applies to self-hosted logging solutions: the more data you store, the more hardware, storage, andnetwork resources you have to pay for

This is a useful debugging technique for individual containers.

As the name suggests, ametric is a numerical measure of something. Depending on the application, relevant metrics might include:

The number of requests currently being processedThe number of requests handled per minute (or per second, or per hour)The number of errors encountered when handling requestsThe average time it took to serve requests (or the peak time, or the 99th percentile)

It’s also useful to gather metrics about your infrastructure as well as your applications:The CPU usage of individual processes or containersThe disk I/O activity of nodes and serversThe inbound and outbound network traffic of machines, clusters, or load balancers

Unlike logs, metrics can easily be processed in all sorts of useful ways: drawing graphs, takingstatistics, or alerting on predefined thresholds. For example, your monitoring system might alert you if the errorrate for an application exceeds 10%!f(MISSING)or a given time period.

 You check your metrics, and you see that the spike in thelatency metric coincides with a similar spike in the CPU usage metric for a particular machine or component.

Thatimmediately gives you a clue about where to start looking for the problem. 

For example, the disk usage metric for a server may creep up, and up over time eventually reach the point wherethe disk actually runs out of space and things start failing. If you alerted on that metric before it got into failureterritory, you could prevent the failure from happening at all.

metrics allow application developers to export key information about the hidden aspects of the system, based ontheir knowledge of how it actually works (and fails):

Tools like Prometheus, statsd, and Graphite, or hosted services such as Datadog, New Relic, and Dynatrace, arewidely used to gather and manage metrics data.

While metrics and logs tell you what’s going on with each individual component of your system, tracing follows asingle user request through its whole life cycle.

Suppose you’re trying to figure out why some users are experiencing very high latency for requests. You checkthe metrics for each of your system components: load balancer, ingress, web server, application server, database,message bus, and so on, and everything appears normal. So what’s going on?

 Although the database is working fine and itsmetrics show no problems, for some reason the application server is having to wait a very long time for requeststo the database to complete.

Without the request’s eye view provided by distributed tracing, it’shard to find problems like this.

monitoring tells you whether the system isworking, while observability prompts you to ask why it’s not working.

understanding what your system does and how it does it.

For example, if you roll out a code change that is designed to improve the performance of a particular feature by10%!,(MISSING) then observability can tell you whether or not it worked. 

how to query and display them.

Software is opaque by default; it must generate data in order to clue humans in on what it is doing. Observablesystems allow humans to answer the question, “Is it working properly?”, and if the answer is no, to diagnosethe scope of impact and identify what is going wrong.

The goal of an observability team is not to collect logs, metrics or traces. It is to build a culture of engineeringbased on facts and feedback, and then spread that culture within the broader organization.

 It doesn’t make sense to put engineering time into making all ofthose different kinds of connections stable and reliable.

With an observability pipeline, we decouple the data sources from the destinations and provide a buffer. Thismakes the observability data easily consumable. 

 This also gives us greater flexibility in termsof adding or removing data sinks, and it provides a buffer between data producers and consumers.Tyler Treat

Because the pipeline buffers data, nothing gets lost.

If there’s a sudden surge in traffic and an overload ofmetrics data, the pipeline will buffer it rather than drop samples.

Engineers can work on fixing internal problems like slow queries and elevated errorrates, without users really being aware of an issue.

 In order to detect an outage, your monitoring needs to consume the service in the sameway that a user would.

For example, if it’s an HTTP service, the monitoring system needs to make HTTP requests to it, not just TCPconnections. 

Bad DNS recordsNetwork partitionsPacket lossMisconfigured routersMissing or bad firewall rulesCloud provider outage

In all these situations, your internal metrics and monitoring might show no problems at all. 

 There are many third-party services that can do this kind of monitoring for you (sometimescalled monitoring as a service, or MaaS), including Uptime Robot, Pingdom, and Wormly.

 Don’t bother trying to build your own external monitoringinfrastructure; it’s not worth it. The cost of a year’s Pro subscription to Uptime Robot likely would not pay for asingle hour of your engineers’ time.

Look for the following critical features in an external monitoring provider:HTTP/HTTPS checksDetect if your TLS certificate is invalid or expired

Keyword matching (alert when the keyword is missing or when it’s present)

Automatically create or update checks via an API

Alerts by email, SMS, webhook, or some other straightforward mechanism

Throughout this book we champion the idea of infrastructure as code, so it should be possible to automate yourexternal monitoring checks with code as well. For example, Uptime Robot has a simple REST API for creating newchecks, and you can automate it using a client library or command-line tool like uptimerobot.

 But don’t stop there. In thenext section we’ll see what we can do to monitor the health of applications inside the Kubernetes cluster itself.

To solve this problem, applications can, and should, do their own health checking.

If it doesn’t respond, therefore, Kubernetesconsiders it to be down or unready.

 they shouldn’t do anything too expensive that might affect serving requests from real users.)

As you know, if a container’s liveness check fails, Kubernetes will restart it automatically

The semantics of a failed readiness check, on the other hand, are “I’m fine, but Ican’t serve user requests at the moment.”

In this situation, the container will be removed from any Services it’s a backend for, and Kubernetes will stopsending it requests until it becomes ready again. This is a better way to deal with a failed dependency.

When an application detects a downstream failure, it takesitself out of service (via the readiness check) to prevent any more requests being sent to it until the problem isfixed.

Instead, try to makeyour services degrade gracefully: even if they can’t do everything they’re supposed to, maybe they can still dosome things.

 distributed systems, we have to assume that services, components, and connections will fail mysteriously andintermittently more or less all the time. 

Metrics form an important part of this picture, and in the next and final chapter, we’ll take you on a deep diveinto the world of metrics in Kubernetes.

 you might like to recall these key points:

Black-box monitoring checks observe the external behavior of a system, to detect predictable failures.

Logs can be useful for post-incident troubleshooting, but they’re hard to scale.

Metrics can help you answer the why question, as well as identify problematic trends before they lead tooutages.

Tracing records events with precise timing through the life cycle of an individual request, to help youdebug performance problems.

Nines don’t matter if users aren’t happy.

### 16. Metrics in Kubernetes

how do you turn raw metrics data into useful dashboards and alerts? Finally, we’ll outline some of the options for metrics tools and platforms.

Memory usage goes up and down all the time as workloads start and stop, but sometimes what we’re interested in is the change in memory usage over time.

For example, if your error rate suddenly goes up (or requests to your support page suddenly spike), that may indicate a problem. You can generate alerts automatically for certain metrics based on a threshold.

storage/object_count The total number of objects in each storage bucket
container/cpu/utilization The percentage of its CPU allocation that a container is currently using


What’s useful to know about a request-driven system?
•  One obvious thing is the number of requests you’re getting.


•  Another is the number of requests that failed in various ways; that is to say, the number of errors.

•  A third useful metric is the duration of each request. This gives you an idea how well your service is performing and how unhappy your users might be getting.

The Requests-Errors-Duration pattern (RED for short) is a classic observability tool that goes back to the earliest days of online services. 

it’s more useful to look at request rate: the number of requests per second, for example. This gives us a meaningful idea of how much traffic the system is handling over a given time interval.

Because error rate is related to request rate, it’s a good idea to measure errors as a percentage of requests.

•  Requests received per second
•  Percentage of requests that returned an error
•  Duration of requests (also known as latency)


Utilization The average time that the resource was busy serving requests, or the amount of resource capacity that’s currently in use.

Errors The number of times an operation on that resource failed. For example, a disk with some bad sectors might have an error count of 25 failed reads.

The USE Method is a simple strategy you can use to perform a complete a check of system health, identifying common bottlenecks and errors. It can be deployed early in the investigation and quickly identify problem areas, which then can be studied in more detail other methodologies, if need be.


•  Funnel analytics (how many people hit the landing page, how many click through to the sign-up page, how many complete the transaction, and so on)

What metrics is it worth tracking for Kubernetes clusters, and what kinds of decisions can they help us make?

For example, it would be useful to track how much CPU and memory each container is using.

To
 monitor the health and performance of your cluster at the top level, you should be looking at least at the following:
•  Number of nodes
•  Node health status
•  Number of Pods per node, and overall
•  Resource usage/allocation per node, and overall

If you’re using a managed Kubernetes service such as GKE, unhealthy nodes will be detected automatically and autorepaired (providing autorepair is enabled for your cluster and node pool). 

•  Network in/out traffic and errors for each container

An excessive number of restarts may tell you there’s a problem with a particular container. 

For example, if your service consumes messages from a queue, processes them, and takes some action based on the message, you might want to report the following metrics:
•  Number of messages received
•  Number of successfully processed messages

It can be difficult to know what metrics are going to be useful when you’re at an early stage of development. If in doubt, record everything. Metrics are cheap; 

At the runtime level, most metrics libraries will also report useful data about what the program is doing, such as:

•  Number of processes/threads/goroutines
•  Heap and stack usage
•  Nonheap memory usage
•  Network/I/O buffer pools
•  Garbage collector runs and 

This kind of information can be very valuable for diagnosing poor performance, or even crashes. For example, it’s quite common for long-running applications to gradually use more and more memory until

The way to get this information is to break down the data into percentiles. The 90th percentile latency (often referred to as P90) is the value that is greater than that experienced by 90%!o(MISSING)f your users. To put it another way, 10%!o(MISSING)f users will experience a latency higher than the P90 value.

the P99 data is likely to give us a more realistic picture of the latency experienced by users. 

If the P99 latency is 10 seconds, then 10,000 page views take longer than 10 seconds. That’s a lot of unhappy users.

The answer is simple: we’re going to graph them, group them into dashboards, and possibly alert on them. We’ll talk about alerting in the next section, but for now, let’s look at some tools and techniques for graphing and dashboarding.

•  To make people familiar with what normal looks like

For example, if you have a production outage caused by a server running out of disk space, it’s possible that a graph of disk space on that server would have warned you beforehand that the available space was trending downward and heading into outage territory.

After an incident, always ask “What would have warned us about this problem in advance, if only we’d been aware of it?”

While alerts can tell you that some value has exceeded a preset threshold, you may not always know in advance what the danger level is.

False alarms are not only annoying, but dangerous: they reduce trust in the system, and condition people that alerts can be safely ignored.

An alert should mean one very simple thing: Action needs to be taken now, by a person.
If no action is needed, no alert is needed.

If action needs to happen sometime, but not right now, the alert can be downgraded to an email or chat message. If the action can be taken by an automated system, then automate it: don’t wake up a valuable human being.

While the idea of being on-call for your own services is key to the DevOps philosophy, it’s equally important that being on-call should be as painless an experience as possible.

Alert pages should be a rare and exceptional occurrence. When they do happen, there should be a well-established and effective procedure for handling them, which puts as little strain as possible on the responder.

Nobody should be on-call all the time. If this is the case, add more people to the rotation. 

your main task is to triage the problem, decide if it needs action, and escalate it to the right people.

In general, if a problem has real or potential business impact, and action needs to be taken now, by a person, it’s a possible candidate for an alert page.

Pages should be restricted to only urgent, important, and actionable alerts:

•  Alerts that are important, but not urgent, can be dealt with during normal working hours. Only things that can’t wait till morning should be paged.

•  Alerts that are urgent, but not important, don’t justify waking someone up. For example, the failure of a little-used internal service that doesn’t affect customers.

If there’s no immediate action that can be taken to fix it, there’s no point paging about it.

send asynchronous notifications: emails, Slack messages, support tickets, projectissues, and so on. They will be seen and dealt with in a timely fashion, if your system is working properly. 

Your people are just as critical to your infrastructure as your cloud servers and Kubernetes clusters

The number of alerts sent in a given week is a good indicator of the overall health and stability of your system.

If you’re regularlyexceeding this, you need to fix the alerts, fix the system, or hire more engineers.

Review all urgent pages, at least weekly, and fix or eliminate any false alarms or unnecessary alerts. If you don’ttake this seriously, people won’t take your alerts seriously. And if you regularly interrupt people’s sleep andprivate life with unnecessary alerts, they will start looking for better jobs.

 so it’s the first thing youshould consider when you’re thinking about metrics monitoring options.

Thecore of Prometheus is a server that collects and stores metrics. It also has various other optional components,such as an alerting tool (Alertmanager), and client libraries for programming languages such as Go, which you canuse to instrument your applications.

It all sounds rather complicated, but in practice it’s very simple. You can install Prometheus in your Kubernetescluster with one command, using a standard Helm chart 

Prometheus scrapes metrics by making an HTTP connection to your application on a prearranged port, anddownloading whatever metrics data is available. It then stores the data in its database, where it will be availablefor you to query, graph, or alert on.

Prometheus’s approach to collecting metrics is called pull monitoring. In this scheme, the monitoring servercontacts the application and requests metrics data. The opposite approach, called push, and used by some othermonitoring tools such as StatsD, works the other way: applications contact the monitoring server to send itmetrics.

You can read more about Prometheus on its site, including instructions on how to install and configure it for yourenvironment.

The Prometheus project includes a tool called Alertmanager, which works well with Prometheus but can alsooperate independently of it. Alertmanager’s job is to receive alerts from various sources, including Prometheusservers, and process them (see “Alerting on Metrics”).

Stackdrivercan collect, graph, and alert on metrics and log data from a variety of sources. It will autodiscover and monitoryour cloud resources, including VMs, databases, and Kubernetes clusters. Stackdriver brings all this data into acentral web console where you can create custom dashboards and alerts.

 Stackdriver is free for all GCP-related metrics; for custom metrics, or metrics fromother cloud platforms, you pay per megabyte of monitoring data per month.

 Stackdriver isa great way to get started with metrics without having to install or configure anything.

 It collects logs and metrics datafrom all your Azure resources, including Kubernetes clusters, and allows you to visualize and alert on it.

Datadog also offers an application performance monitoring (APM) component, designed to help you monitor andanalyze how your applications are performing. Whether you use Go, Java, Ruby, or any other software platform,Datadog can gather metrics, logs, and traces from your software, and answer questions for you like:

New Relic is a very well established and widely used metrics platform focused on application performancemonitoring (APM). 

The trick is to gather the right data in the first place, process it in the right way, use it to answer the rightquestions, visualize it in the right way, and use it to alert the right people at the right time about the right things.

Focus on the key metrics for each service: requests, errors, and duration (RED). For each resource:utilization, saturation, and errors (USE).

If you alert on metrics, alerts should be urgent, important, and actionable. Alert noise creates fatigue anddamages morale.

The de facto standard metrics solution in the cloud native world is Prometheus, and almost everythingspeaks the Prometheus data format.

Third-party managed metrics services include Google Stackdriver, Amazon Cloudwatch, Datadog, andNew Relic.

### Afterword

The official Kubernetes Slack organization. This is a good place to ask questions, and chat with other users.

 None of us knows everything, but everybody knows something. Together,we just might figure things out.

To the extent that we’ve learned anything about Kubernetes, it’s because we’ve failed a lot. We hope you will too.Have fun!