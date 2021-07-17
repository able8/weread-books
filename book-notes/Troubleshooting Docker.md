## Troubleshooting Docker
> John Wooten

### Troubleshooting Docker

No part of this book may be reproduced, stored in a retrieval system, or transmitted in any form or by any means

### About the Authors

He is an active open source code contributor, repository manager and has also published many online Docker & Kubernetes tutorials. 

He has also recently published a book on Docker Networking.

Rajdeep Dua has over 18 years of experience in the Cloud and Big Data space. He has worked extensively on Cloud Infrastructure and Machine Learning related projects as well evangelized these stacks at Salesforce, Google, VMWare.

Currently, he leads Developer Relations team at Salesforce India. He also works with the Machine Learning team at Salesforce.


### About the Reviewer

He has over 5 years of experience. He is a full stack developer and has worked on various open source technologies, such as Python, Django

He loves to explore and try out new technologies. He is a foodie and loves to explore new restaurants and ice cream parlors.


### www.PacktPub.com

For support files and downloads related to your book, please visit www.PacktPub.com.

### Why subscribe?

•  Fully searchable across every book published by Packt
•  Copy and paste, print, and bookmark content
•  On demand and accessible via a web browser


### What this book covers

Managing the Networking Stack of a Docker Container, will explain how Docker networking is powered with Docker0 bridge and its troubleshooting issues and configuration. 

We will also look at troubleshooting the communication issues between Docker networks and the external network.

### What you need for this book

Public cloud accounts of AWS, Azure and GCE might be required, which are mentioned in the respective sections of the chapters.

### Who this book is for

If you are looking to build production-ready Docker containers for automated deployment, you will be able to master and troubleshoot both the basic functions and the advanced features of Docker.

### Downloading the example code

You can download the example code files for this book from your account at http://www.packtpub.com. 

You can download the code files by following these steps:
1.  Log in or register to our website using your e-mail address and password.


7.  Click on Code Download.


Once the file is downloaded, please make sure that you unzip or extract the folder using the latest version of:

### 1. Understanding Container Scenarios and an Overview of Docker

Docker is one of the most recent successful open source projects which provides the packaging, shipping, and running of any application as lightweight containers. 

with the help of this book it will be easy to troubleshoot some of the common problems which Docker users face while installing and using Docker containers.

### Application containers

Application containers are designed to run a single service in the package, while OS containers which were explained previously, can support multiple processes.

### Docker lifecycle

These are some of the basic steps involved in the lifecycle of a Docker container:
1.  Build the Docker image with the help of Dockerfile which contains all the commands required to be packaged. It can run in the following way:

2.  After the image creation, in order to deploy the container Docker run can be used. The running containers can be checked with the help of the Docker ps command, which lists the currently active containers. There are two more commands to be discussed:

  Docker pause: This command uses cgroups freezer to suspend all the processes running in a container. Internally it uses the SIGSTOP signal. Using this command process can be easily suspended and resumed whenever 

◦  Docker start: This command is used to start one or more stopped containers.

3.  After the usage of container is done, it can either be stopped or killed; the Docker stop: command will gracefully stop the running container by sending the SIGTERM and then SIGKILL command. In this case, the container can still be listed by using Docker ps -a command. Docker kill will kill the running container by sending SIGKILL to the main process running inside the container.

### Docker design patterns

Dockerfile is the base structure from which we define a Docker image, it contains all the commands to assemble an image. Using the Docker build command, we can create an automated build that executes all the preceding mentioned command-line instructions to create an image:

### Base image sharing

A Docker image is a zipped file which is a snapshot of all the configuration parameters as well as the changes made in the base image (kernel of the OS).


### Shared volume

Sharing the volume at host level allows other containers to pick up the shared content that they require. This helps in faster rebuilding of the Docker image or when adding, modifying, or removing dependencies. 

### Development tools container

RUN mkdir /var/run/sshd 
ENTRYPOINT /usr/sbin/sshd -D 

### Test environment containers

Test environment containers
Testing the code in different environments always eases the process and helps find more bugs in isolation. We can create a Ruby environment in a separate container to spawn a new Ruby shell and use it to test the code base:


### 2. Docker Installation

Docker binary installation is mostly oriented for hackers who want to try out Docker on a variety of OS. I

Docker Toolbox is an installer, which can be used to quickly install and set up a Docker environment on your Windows or Mac machine.

•  Docker client: It executes commands, such as build and run, and ship containers by communicating with the Docker daemon
•  Docker Machine: It is a tool used to install Docker Engine on virtual hosts and manages them with the help of Docker Machine commands


Also, it'll be faster to get the environment up and running in AWS. 

help you to troubleshoot and speed up the deployment on AWS.


### Prerequisites

The kernel must be 3.10 at minimum.
Let's check our kernel version, using the following command:


The output is a kernel version of 3.13.x, which is fine:

3.13.0-74-generic

### Updating package information

Perform the following steps to update the APT repository and have necessary certificates installed:
1.  Docker's APT repository contains Docker 1.7.x or higher. To set APT to use packages from the new repository:


2.  Run the following command to ensure that APT works with the HTTPS method and CA certificates are installed:

        $ sudo apt-get install apt-transport-https ca-certificates

The apt-transport-https package enables us to use deb https://foo distro main lines in the /etc/apt/sources.list so that package managers, which use the libapt-pkg library, can access metadata and packages available in sources accessible over HTTPS.

The ca-certificates are container's PEM files of CA certificates, which allow SSL-based applications to check for the authenticity of SSL connections.


### Adding a new package source for Docker


Adding a new package source for Docker
The Docker repository can be added in the following way to the APT repository:
1.  Update /etc/apt/sources.list.d with a new source as Docker repository.
2.  Open the /etc/apt/sources.list.d/docker.list file and update it with the following entry:

### Updating Ubuntu packages

The Ubuntu packages after adding Docker repository can be updated, as shown here:

### Docker installation

4.  Verify that Docker is installed correctly:


5.  This is how the output would look like:


### Installing Docker on Red Hat Linux

Using these packages ensures that you will be able get the latest release of Docker.

### Checking kernel version

The Linux kernel version can be checked with the help of the following command:

### Updating the YUM packages

Output listing is given; ensure that it shows Complete! at the end, as follows:


### Testing the Docker installation

The following is the output for the preceding command:



Check the Docker version to make sure that it is the latest:


### Troubleshooting tips

Ensure that you are using the latest version of Red Hat Linux to be able to deploy Docker 1.11. 

### Deploy CentOS VM on AWS to run Docker containers

we'll take a look at deploying CentOS VM on AWS to get the environment up and running fast and deploy Docker containers. 

Once the instance is up, get the public IP address from the AWS EC2 console.
SSH into the instance and follow the following steps for installation:

### Starting the Docker service

The Docker service can be started in the following way:

$ sudo service docker start

### Installation channels of CoreOS

In our case, we will use stable  Release Channels:


These mentioned parameters can be set in the default template, as follows:

### Troubleshooting

Here are some troubleshooting tips and guidelines, which should be followed during the preceding installation:

•  KeyPair should exist; if it doesn't, the cluster will not launch
SSH into the instance and check the Docker version:


### Installing with DNF

Update the existing DNF package by using the following command:

### Installing the Docker package

To generate this message, Docker took the following steps:
1.  The Docker client contacted the Docker daemon.
2.  The Docker daemon pulled the hello-world image from the Docker Hub.
3.  The Docker daemon created a new container from that image, which runs the executable that produces the output you are currently reading.

.  The Docker daemon streamed that output to the Docker client, which sent it to your terminal.

### Running the Docker installation script

The Docker installation can also be done in a quick and easy way by executing the shell script and getting it from the official Docker website:

To create a Docker group and add a user, follow the steps mentioned, as follows:

$ sudo groupadd docker

$ sudo usermod -aG docker your_username



Log out and log in with the user to make sure that your user is created successfully:

The truncated output of the preceding command is listed as follows:

### Launching the SUSE Linux VM on AWS

We chose an existing keypair to SSH into the instance:

Once the VM is up, log in to the VM from a terminal:

Since we have launched the VM, let's focus on getting docker installed. The following diagram outlines the steps for installing docker on SUSE Linux:


### 3. Building Base and Layered Images

 we will learn about building base and layered images for production-readycontainers.

 Docker containers provide us with ideal environments in which wecan build, test, automate, and deploy. 

The images a developer locally builds, tests,and debugs can then be pushed directly into staging and production environments as thetest environment is nearly a mirror image under which the application code runs.

Building container imagesBuilding base images from scratch

Automated image building with testing

### Official images from the Docker Registry

Further, standardized base images are well maintained and updated frequently to address security advisories and critical bug fixes.


These base images are built, validated, and supported by Docker Inc. and are easily recognized by their single word names (for example, centos). 

images can be searched for and retrieved simply using the docker search and docker pull Terminal commands.

$ sudo docker run centos: This will first look for this image localized on your host and, if not found, it will download the image to host. The run parameters for the image will have been defined in its Dockerfile.


Try it out: Practice an image pull 

### Building our own base images

Let's check to ensure our new image is listed:


Now, let's push the image up to our Docker Hub repository:

### Building layered images

This is very powerful when building multiple images, using the Docker Registry to push and pull images.

### Dockerfile construction

The first command of a Dockerfile is typically the FROM command. FROM specifies the base image to be pulled. This base image can be located in the public Docker registry 

In most cases, it is best to put each Dockerfile in an empty directory. Then, only add the files needed for building the Dockerfile to the directory. To increase the build's performance, a .dockerignore file can be added to the context directory to properly exclude files and directories.

### Dockerfile commands and syntax

This instruction sets the environment variable in Dockerfile. An environment variable set can be used in subsequent instructions.


Use quotes for value having spaces for environment variable.

•  Values of environment variables can be changed at runtime by passing the --env <key>=<value> option to the docker run command


RUN <command1>;<command2> 

Actually, this is a special form of example, where you specify multiple commands separated by ;. The RUN instruction executes such commands together and builds a single layer for all of the commands specified.


Docker Cache: Docker will first check against the host's image cache for any matching layers from previous builds. If found, the given build step within the Dockerfile will be skipped to utilize the previous layer, from cache. 

The -y flag skips interactive confirmation.

Clean up any files used by apt-get 
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* 


 The main purpose of a CMD is to provide defaults for an executing container. These defaults can either include or omit an executable, the latter of which must specify an ENTRYPOINT instruction as well. If the user specifies arguments to Docker run, then they will override the default specified in CMD. If you would like your container to run the same executable every time, then you should consider using ENTRYPOINT in combination with CMD.

An ENTRYPOINT allows you to configure a container that will run as an executable. From Docker's documentation, we will use the provided example; the following will start nginx with its default content, listening on port 80:

Command-line arguments to docker run <image> will be appended after all elements in an executable form ENTRYPOINT, and will override all elements specified using CMD. 

Use quotes in labels if the label value has spaces:


If the value of the label is long, use backslash to extend the label value to a new line.

•  If key in labels are repeated, later one will override the earlier defined key's value.

•  To view the labels for a built image, use the docker inspect <image> command

WORKDIR
This instruction is used to set the working directory for subsequent RUN, ADD, COPY, CMD, and ENTRYPOINT instructions in Dockerfile.


The following are examples of using the WORKDIR instruction:
WORKDIR /opt/myapp 

The preceding instruction defines the work directory twice. Note that the second WORKDIR will be relative to the first WORKDIR. The result of the pwd command will be /opt/myapp:


USER
This sets the user for running any subsequent RUN, CMD, and ENTRYPOINT instructions. This also sets the user when a container is created and run from the image.

Notes about USER instructions:
•  One can override the user using --user=name|uid[:<group|gid>] in the docker run command for container


### Image testing and debugging


Image testing and debugging

that allow us to monitor and troubleshoot containers from the outside.


### Docker details for troubleshooting

let's do some testing to make sure that all is copacetic with our build.

### Docker version

Let's ensure that we firstly recognize what version of Docker, Go, and Git we are running:

$ sudo docker version

### Docker info

Knowing these things can help us troubleshoot from our top-down perspective:


### A troubleshooting note for Debian/Ubuntu

From a $ sudo docker info command, you may receive one or both of the following warnings:

WARNING: No memory limit support 
WARNING: No swap limit support

Locate the following line:
GRUB_CMDLINE_LINUX="" 

Then, replace it with the following one:
GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1" 


Then, run update-grub and reboot the host machine.


### Listing installed Docker images

SSH into the docker host and execute the docker images command. You should see your docker image listed, as follows:


What if my image does not appear? Check the agent logs and make sure that your container instance is able to contact your docker registry by curling the registry and printing out the available tags:

curl [need to add in path to registry!]

### Manually crank your Docker image

provides accessible output logs for further troubleshooting.

When a container fails to start, it does not log anything. 

### Image layer IDs as debug containers

Consider the following Dockerfile as an example:

We would get output similar to the following:


We can then use the preceding image layer IDs to start new containers from b750fe79269d, de1d48805de2, and 40fd00ee38e1:

$ docker run -rm b750fe79269d cat /tmp/trouble.txt 
cat: /tmp/trouble.txt No such file or directory 

Once inside the container, execute the failing command in attempt to reproduce the issue, fix the command and test, and finally update the Dockerfile with the fixed command.


### Checking failed container processes

Run the following command to check for failed or no-longer running containers and note the CONTAINER ID to inspect a given container's configuration:

$ sudo docker ps -a

Finally, let's also analyze the container's application logs. Error messages for container start failures are output here:

$ sudo docker logs <containerId>

### Other potentially useful resources

$ sudo docker top gives us a list of processes running inside a container.


### Using sysdig to debug

Sysdig provides data on CPU usage, I/O, logs, networking, performance, security, and system state.

### Single step installation

Single step installation
Installation of sysdig can be accomplished in a single step by executing the following command as root or with sudo:

curl -s https://s3.amazonaws.com/download.draios.com/stable/install-sysdig | sudo bash



### Troubleshooting - an open community awaits you

In general, most issues you may face have likely been experienced by others, somewhere and sometime before. 

### 4. Devising Microservices and N-Tier Applications

Instead of using web frameworks to invoke services and produce web pages, applications today are built by consuming and producing APIs. 

### N-tier application architecture

A common N-tier application consists of three layers: a PRESENTATION TIER (providing basic user interface and application services access), a DOMAIN LOGIC TIER (providing the mechanism used to access and process data), and a DATA STORAGE TIER (which holds and manages data that is at rest).


### Common characteristics of microservices

•  Independent
•  Stateless

•  Loosely coupled
•  Interchangeable


### Advantages of microservices

•  Microservices enable each service to be developed independently:

•  Microservices enable each service to be deployed continuously

•  Microservices enable each service to be scaled independently: You need to deploy only the instances of each service necessary to satisfy the capacity and availability constraint

### Disadvantages of microservices

•  Testing a microservices application is much more complex: Writing test classes for a microservices application does not only require that service to be started, but also its dependency services.

### Automated testing

The preceding diagram represents a DevOps pipeline starting with code compilation and moving to integration test, performance test and, finally, the app getting deployed in a production environment.

### Docker public repository (Docker Hub)

•  WebHooks: It is a new feature that allows to trigger an action after successful image push to repository

In order to log out, the following command can be used:

$ docker logout

### Private Docker registry

Private Docker registry can be deployed inside the local organization; it is open-source under Apache license and is easy to deploy.


Using private Docker registry, you have the following advantages:
•  The organization can control and keep a watch on the location where Docker images are stored

### Pushing images to Docker Hub

Go to the directory containing the Dockerfile and execute the following command to build an image:

If the push to Docker Hub fails with a 500 Internal Server Error, the issue is related to Docker Hub infrastructure and a repush might be helpful. If the issue persists while pushing the Docker image, Docker logs should be referred at /var/log/docker.log in in order to debug in detail.


### Moving images in between hosts

If the image is required to be moved from one host to another, then it can be simply achieved with the help of the docker save command, without the overhead of uploading and downloading the image.

### 6. Making Containers Work

We will take a deep dive into various deployment management tools, such as Chef, Puppet, and Ansible, which provide integration with Docker in order to ease the pain of deploying thousands of containers for a production environment.


Managing Docker containers with Ansible
•  Building Docker and Ansible together


Automating the Docker container's deployment with the help of the preceding management tools has the following advantages:

### Privileged containers

The privileged containers can be started with the following command:

    $
docker run -it --privileged ubuntu /bin/bash 

As we can see, after starting the container in privileged mode, we can list all the devices connected to the host machine.


### Troubleshooting tips

but when we run the container in privileged mode using the --privileged flag it is able to change the kernel parameters easily, which can cause a security vulnerability on the host system:

$ docker run --privileged -it centos /bin/bash
[root@930aaa93b4e4 /]#  sysctl -a | wc -l
sysctl: reading key "net.ipv6.conf.all.stable_secret"
sysctl: reading key "net.ipv6.conf.default.stable_secret"
sysctl: reading key "net.ipv6.conf.eth0.stable_secret"
sysctl: reading key "net.ipv6.conf.lo.stable_secret"
638
[root@930aaa93b4e4 /]# sysctl -w net.ipv4.ip_forward=0
net.ipv4.ip_forward = 0

$ docker ps -q | xargs docker inspect --format '{{ .Id }}: 
    Privileged={{ 
    .HostConfig.Privileged }}'
930aaa93b4e44c0f647b53b3e934ce162fbd9ef1fd4ec82b826f55357f6fdf3a: 
    Privileged=true



### Ansible

•  Configuration Management: Keeping a common configuration file is one of the primary use cases of Ansible, and helps manage and deploy in the required environment.

•  Application Deployment: Ansible helps to manage the complete lifecycle of an application, from deployment to production.

### 7. Managing the Networking Stack of a Docker Container

•  Troubleshooting Docker bridge configuration
•  Configuring DNS
•  Troubleshooting communication between containers and the external network

### Docker networking

Each Docker container has its own network stack, and this is due to the Linux kernel net namespace, where a new net namespace for each container is instantiated and cannot be seen from outside the container or other containers.


### docker0 bridge

Multiple containers on the same host can talk to each other through the Linux bridge.

### Connecting containers to the external world

The iptables NAT table on the host is used to masquerade all external connections, as shown here:


### Reaching containers from the outside world

Docker server, by default, creates a docker0 bridge inside the Linux kernel that can pass packets back and forth between other physical or virtual network interfaces so that they behave as a single ethernet network:


Here is a host with two different containers connected:

To change the default settings in Docker, modify the /etc/default/docker file.
Change the default bridge from docker0 to br0:


The following command displays the new bridge name and the IP address range of the Docker service:

### Configuring DNS

Docker provides hostname and DNS configuration for each container without building a custom image. 

### Flannel

Flannel runs an agent, flanneld, on each host and is responsible for allocating a subnet lease out of a preconfigured address space. 

### Troubleshooting OVS single host setup

8.  Test the connection between the two containers connected using the OVS bridge with the ping command. First, find out their IP addresses:


### 8. Managing Docker Containers with Kubernetes

In the previous chapter, we learned about Docker networking and how to troubleshoot networking issues. In this chapter, we will introduce Kubernetes.


•  Deploying Kubernetes in a production environment
•  Debugging Kubernetes issues


•  Label: Labels are used to identify and organize pods and services based on key-value pairs:

### Deploying Kubernetes on Bare Metal machine

3.  In /etcd/hosts, set the Fedora master and Fedora node:


6.  Edit the contents of the /etc/kubernetes/apiserver file:

7.  The /etc/etcd/etcd.conf file should have the following line uncommented in order to listen on port 2379

13.  After some time, node should be ready to deploy pods:

### Deploying Kubernetes using Minikube

Minikube is still in development; it is a tool that makes it easy to run Kubernetes locally

Minikube helps developers to learn Kubernetes and do day-to-day development and testing with ease.


### Deploying Kubernetes on AWS

6.  In order to configure the AWS-CLI, use the following command:

$ aws configure

### Debugging Kubernetes issues

Also, verify that all nodes are in the ready state.


3.  If the pod stays in the pending state

4.  To see all the cluster events, use the following command:

$ cluster/kubectl.sh get events

Check if the current time matches on the client and server. 

### 9. Hooking Volume Baggage

The three main use cases for Docker data volumes are as follows:
•  To keep data persistent after a container is deleted
•  To share data between the host and the Docker container
•  To share data across Docker containers

If the current file needs to be modified, it is copied from the read-only layer to the read-write layer, where changes are applied. The version of the file in the read-write layer hides the underlying file but doesn't destroy it.

### Troubleshooting - AWS ECS deployment

4.  Provide the repository name, and we'll be able to see the repository address where container images need to be pushed being generated:


Use the aws configure command and provide an AWS access key ID and AWS secret access key to log in:

        $ aws configure 

13.  The Welcome to nginx page can be seen as we access the load balancer public URL:

### Updating Docker containers in the ECS cluster

The blue-green deployment helps to give a rapid rollback. We can switch the router to the blue environment if we face any issues in the latest green environment. Now, as the green environment is live and handling all the requests, the blue environment can be used as a staging environment for the final testing step of the next deployment. 

Advanced container configuration can also be used to set up the Environment Variables:

### Troubleshooting - The Microsoft Azure Container Service

1.  We need to create an RSA key, which will be requested in the deployment steps. The key will be required to log in to the deployed machines post installation:

Once generated, the keys can be found in ~/root/id_rsa


The following is the command to connect via SSH to the swarm-master:

### Docker Beta for AWS and Azure

 docker service scale helloworld=5 
helloworld scaled to 5 
 
$ docker service ps helloworld 