## kubectl: Command-Line Kubernetes in a Nutshell
> Rimantas Mocevicius

### Why subscribe?

  Spend less time learning and more time coding with practical eBooks and Videos from over 4,000 industry professionals

### About the author

Rimantas Mocevicius is an IT professional with over 25 years' experience in DevOps, which includes Linux, containers, Kubernetes, and cloud-native technologies. He is also co-founded Helm – the Kubernetes package manager.

Since 2018 he has worked at JFrog Ltd (Nasdaq: FROG) as a senior software engineer in the Community Engineering team

He is a big fan and supporter of open source software. His passion for new technologies drives him forward, and he never wants to stop learning about them.

 would like to say very huge thank you my wife Vilma for all her support while I was writing the book, and for giving me the space and time to complete it on time.

Also, I would like to say a big thank you to the technical reviewer

### About the reviewer

Eldad Assis is an experienced developer who turned into an infrastructure geek about 20 years ago. Linux, automation, CI/CD, cloud-native, and DevOps principles have been key parts of his professional life. He takes applications to the public cloud, from simple virtual machines all the way to containerized microservices in Kubernetes.

Today, he's a DevOps architect at JFrog, mostly working on leading the journey to cloud-native microservices in Kubernetes in public clouds as part of JFrog's SaaS offering.

We have worked with thousands of developers and tech professionals, just like you, to help them share their insight with the global tech community. 

### Preface

Introducing and Installing kubectl, provides a brief overview of kubectl and how to install and set it up.

how to get info about the cluster nodes.

Working with kubectl Plugins, explains how to install kubectl plugins.

We recommend accessing the code via the GitHub repository (link available in the next section). Doing so will help you avoid any potential errors related to the copying and pasting of code.


You can download the example code files for this book from GitHub at https://github.com/PacktPublishing/kubectl-Command-Line-Kubernetes-in-a-Nutshell. In case there's an update to the code, it will be updated on the existing GitHub repository.

### Section 1: Getting Started with kubectl

This section contains the following chapter:

### Chapter 1: Introducing and Installing kubectl

Kubernetes provides mechanisms for application deployment, scheduling, updating, maintenance, and scaling. A key feature of Kubernetes is that it actively manages containers to ensure that the state of the cluster always matches the user's expectations.


Kubernetes enables you to respond quickly to customer demand by scaling or rolling out new features. It also allows you to make full use of your hardware.


◦  Self-healing: Auto-placement, auto-restart, and auto-replication

One of the ways to manage Kubernetes clusters is kubectl—Kubernetes' command-line tool for management, it is a tool for accessing a Kubernetes cluster that allows you to run different commands against Kubernetes clusters to deploy apps, manage nodes, troubleshoot deployments, and more.


To learn kubectl, you will need access to a Kubernetes cluster

Alternatively, it can be a local one:

You can use kubectl to deploy
 applications, inspect and manage them, check cluster resources, view logs, and more.

It is a very automation-friendly tool.


kubectl looks for a configuration file named .kube in the $HOME folder. In the .kube file, kubectl stores the cluster configurations needed to access a Kubernetes cluster. You can also set the KUBECONFIG environment variable or use the --kubeconfig flag to point to the kubeconfig file.

Let's take a look at how you
 can install kubectl on macOS, on Windows, and in CI/CD pipelines.

The 
easiest way to install kubectl on macOS is 
using the Homebrew
 package manager (https://brew.sh/):
1.  To install, run this:
$ brew install kubectl


2.  To see the version you have installed, use this:
$ kubectl version –client --short

3.  Create the .kube directory in your home directory:


When
 you want to use kubectl on Linux, you have
 two options:
◦  Use curl:
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl

To get a list of supported kubectl commands, run this:
$ kubectl --help
kubectl commands are 
grouped by 
category. Let's look at each category.

set: Set specific features on objects—for example, set environment variables, update a Docker image in a pod template, and so on.

explain: Get the documentation of resources—for example, the documentation on deployments.

The following are kubectl
 deploy commands:
◦  rollout: Manage the rollout of a
 resource.
◦  scale: Set a new size for a deployment, ReplicaSet, or StatefulSet.
◦  autoscale: Auto-scale a deployment, ReplicaSet, or StatefulSet.

The following 
are the kubectl cluster 
management commands:
◦  certificate: Modify certificate resources.
◦  cluster-info: Display cluster information.
◦  top: Display resource (CPU/memory/storage) usage.
◦  cordon: Mark a node as unschedulable.
◦  uncordon: Mark a node as schedulable.
◦  drain: Drain a node in preparation for maintenance.
◦  taint: Update the taints on one or more nodes.

The 
following are the kubectl troubleshooting 
and debugging commands:
◦  describe: Show the details of a specific resource or group of resources.

port-forward: Forward one or more local ports to a pod.
◦  proxy: Run a proxy to the Kubernetes API server.

The following are the kubectl advanced 
commands:
◦  diff: Show difference of live
 version against a would-be applied version.
◦  apply: Apply a configuration to a resource by filename or stdin.
◦  patch: Update the field(s) of a resource using a strategic merge patch.

convert: Convert config files between different API versions.

The following
 are the settings
 commands in kubectl:
◦  label: Update the labels on a resource.
◦  annotate: Update the annotations on a resource.


◦  api-resources: Print the supported API resources on the server.
◦  api-versions: Print the supported API versions on the server, in the form of group/version.
◦  config: Modify kube-config files.
◦  plugin: Provide utilities for interacting with plugins.
◦  version: Print the client and server version information.

with more recent versions, the commands might have changed.

### Section 2: Kubernetes Cluster and Node Management

This section explains how to manage Kubernetes clusters, how to get information about clusters and nodes, and how to work with nodes.

  Chapter 2, Getting Information about a Cluster
◦  Chapter 3, Working with Nodes


### Chapter 2: Getting Information about a Cluster

not setting the right/unsupported API version for your, for example, Ingress, will cause the deployment to fail.


In this chapter, we're going to cover the following topics:
◦  Cluster information
◦  Cluster API versions
◦  Cluster API resources

It is 
always good to know which version of the Kubernetes server (API) is installed for a Kubernetes cluster as you might want to use particular features available in that version. To check the server version, run the following:
$ kubectl version --short


as the latest version is usually backward compatible. However, it is not recommended to use an older kubectl version with a more recent server version.


Kubernetes master is running at https://35.223.200.75

In the preceding output log, we see the following:
◦  The master endpoint IP (35.223.200.75), where your kubectl connects to the Kubernetes API.

The output of the preceding command is as shown in the following screenshot:

The preceding
 command shows a list of the nodes available in the cluster with their status and Kubernetes version.


It is good practice to check
 the available cluster API versions because each new Kubernetes version usually brings with it new API versions and deprecates/removes some old ones.


To get an API list, run the following command:
$ kubectl api-versions
The output for the preceding command gives us a list of APIs, as shown in the following screenshot:

You need to
 know which APIs can be used in your application, as otherwise, the deployment could fail if the API version you use is not supported anymore.


To get the resources list, run the following command:
$ kubectl api-resources

As the list is quite long, we are only showing part of it in the preceding screenshot.


In this chapter, we have learned how to use kubectl to get information about a Kubernetes cluster, the available APIs, and the API resources in a cluster.


### Chapter 3: Working with Nodes

Everyone familiar with Kubernetes knows that the cluster workload runs in nodes, where all Kubernetes pods get scheduled, deployed, redeployed, and destroyed.

The main components of the node are as follows:kubelet: An agent that registers/deregisters the node with the Kubernetes API.Container runtime: This runs containers.kube-proxy: Network proxy.

And when the loadincreases, the required amount of nodes will be deployed to accommodate the newly scheduled pods.

There are times when you need to troubleshoot, get information about the nodes in the cluster, find out whichpods they are running, see how much CPU and memory they are consuming, and so on.

Displaying node resource usage

To start working with nodes, you need to get a list of them first. To get the nodes list, run the followingcommand:$ kubectl get nodes

We get the following list of nodes using the preceding command:

The preceding list shows we have three nodes in our Kubernetes cluster with a Ready status and Kubernetesversion 1.17.5-gke.9. 

The kubectl describe command allows us to get the state, metadata, and events of an object in a Kubernetescluster. 

To describe a node, run the following command:$ kubectl describe node gke-kubectl-lab-default-pool-b3c7050d-6s1l

In the following screenshot, we see the assigned Labels (which can be used to organize and select subsetsof objects) and Annotations (extra information about the node is stored there) for the node, andUnschedulable: false means that the node accepts pods to be scheduled on to it. 

In the following screenshot, we see the assigned internal and external IPs, the internal DNS name, and thehostname:

The following screenshot shows the running pods on the node with CPU/memory requests and limits perpod:

The following screenshot shows the allocated resources for the node:

It is handy to know what resources are consumed by nodes. To display the resources used by nodes, run thefollowing command:$ kubectl top nodes

The previous command shows node metrics such as CPU cores, memory (in bytes), and CPU and memorypercentage usage.

Also, by using $ watch kubectl top nodes, you can watch and monitor nodes in real time when, for example, loadtesting your application or doing other node operations.

The watch command willrun the specified command and refresh the screen every few seconds.

but whatever pods are running there will stay running on that node.

A DaemonSet cannot be deleted from the Kubernetes node, so the --ignore-daemonsets flag mustbe used to force draining the node.

We have learned new skills that can be applied in real-world scenarios to conduct maintenance on Kubernetesnodes.

### Section 3: Application Management

This section explains how to manage Kubernetes applications, including creating, updating, deleting, viewing, anddebugging applications.

Chapter 4, Creating and Deploying Applications

Chapter 6, Debugging an Application

### Chapter 4: Creating and Deploying Applications

The applications in a pod all use the same network namespace, IP address, and port space. They can find andcommunicate with each other using localhost. 

Let's move on to deployments, which are more suited to real-world application deployments.

Let's redeploy using the deployment.yaml file this time:$ kubectl apply –f deployment.yaml

Kubernetes services provide a single stable name and address for a set of pods. They act as basic in-cluster loadbalancers.

 Each service gets assigned a virtual IPaddress that remains constant until the service dies. 

Type: ClusterIP is the default service type when no –type flag is provided.

For testingpurposes, this is fine, but not for running a real-world application.

Copy its contents to a file, and there you should remove the following parts, which were generated by kubectl andaren't needed there:

To scale our deployment,run the following commands:$ kubectl scale deployment nginx –replicas=2

The service will distribute all incoming requests between the three pods in a round-robin manner.

 we going to learn how to do more advanced updates to deployed applications.

### Chapter 5: Updating and Deleting Applications

if the release was a bad one,how to roll it back.

We're going to cover the following main topics in this chapter:

Releasing a new application versionRolling back an application releaseAssigning an application to a specific node (node affinity)

The $ kubectl rollout status deployment nginx command will show the rollout status as a success, failed, orwaiting:

This is a handy way to check the deployment's rollout status.

There are always cases (such as bugs in the code, the wrong Docker tag supplied for the latest release, andmore) when you need to roll back an application release to a previous version.

This can be done using the $ kubectl rollout undo deployment nginx command followed by the get and describecommands:

We can also check the deployment rollout history:$ kubectl rollout history deployment nginx

Let's update the deployment.yaml file with the nodeAffinity rule, so the nginx application only getsscheduled to gke-kubectl-lab-we-app-pool:

 nodeAffinity:      requiredDuringSchedulingIgnoredDuringExecution:        nodeSelectorTerms:        - matchExpressions:          - key: node-pool            operator: In            values:            - "web-app"

To deploy the changes, run the

We have used the –o wide flag, which allows us to show more information about a pod, such as its IP andthe node it's scheduled on.

for realapplication high availability, the best practice is to ensure that application pods are scheduled onto separatenodes. If one of the nodes is down/rebooted/replaced, having all the pods running on that node will cause theapplication to go down and its services to be unavailable.

 podAntiAffinity:      requiredDuringSchedulingIgnoredDuringExecution:      - labelSelector:          matchExpressions:          - key: app            operator: In            values:            - nginx          topologyKey: "kubernetes.io/hostname"

As you can see, the pods are running on separate nodes, and the podAntiAffinity rule will ensure that pods willnot be scheduled onto the same node.

We need to update service.yaml with type: LoadBalancer, which will create a LoadBalancer with an external IP.

The LoadBalancer capability is dependent on the vendor integration because an external LoadBalancer is createdby the vendor. 

Update the service.yaml file with the following content:

 it can take up to 5 minutes for theLoadBalancer to be provisioned. Running the get command again after some time, you can see that the IP isassigned, as shown in the following screenshot:

We can also use the same approach to delete the application. To delete the deployment and service with onecommand, run the following:$ kubectl delete –f code/

As you can see, we used just one command to clean up all of the application's installed resources.

In this chapter, we learned how to release a new application version, roll back an application version, assign anapplication to a particular node, schedule application replicas between different nodes, and expose an applicationto the internet. We also learned how to delete an application in a few different ways.

### Chapter 6: Debugging an Application

debug an application to troubleshoot production-related issues. 

executing in a container (executing into a container means getting shell access in therunning container) and running a command there.

Oops, what happened there? By running the $ kubectl get pods command we are seeing an ErrImagePull error.Let's look into it. 

Right here, we see why it fails to pull the image:

which is crashing too. 

You can use kubectl logs with a flag to print the logs for the previous instance of the container in a pod if itexists:$ kubectl logs -p some_pod

 To check the pod logs in real time, run thefollowing command:$ kubectl logs postgresql-57578b68d9-b6lkv -f

Nice, the PostgreSQL deployment is up and running and is ready to accept connections. 

Let's see how to get the postgresql.conf file content using the kubectl exec command:$ kubectl exec postgresql-57578b68d9-6wvpw cat \ /opt/bitnami/postgresql/conf/postgresql.conf

how to describe the pod, check logs, and troubleshoot issues

These techniques can be used to help you to fix any issues you encounter.

### Chapter 7: Working with kubectl Plugins

A plugin in kubectl is just an executable file 

The easiest way to find and install plugins is by using Krew (https://krew.sigs.k8s.io/), the Kubernetes pluginmanager. Krew is available for macOS, Linux, and Windows.

 kubectl krew installctx ns view-allocations 

Let's check out how to use them.

 It is just a verysimple example of how to create the plugin:

Make sure it is executable:$ chmod +x ~/bin/ kubectl-toppods

Nice! You can see that the plugin is working, and it is not very difficult to create a kubectl plugin.

### Chapter 8: Introducing Kustomize for Kubernetes

Kustomize allows us to patch Kubernetestemplates without changing the application's original templates. We are going to learn about Kustomize and howto patch Kubernetes deployments with its help.

The following code sets which resources to apply the settings to. As service does not have images, Kustomizewill only apply to the deployment, but we will need service in the later steps, so we are setting it anyway:

Now, it is time to actually perform an installation using Kustomize:$ kubectl create ns nginx-prodnamespace/nginx-prod created$ kubectl apply –k overlays/production/

please refer to the following link for more information: https://kustomize.io/.

All that was done without changing the application'soriginal templates by using kustomization.yaml files with Kustomize to make the required changes.

### Chapter 9: Introducing Helm for Kubernetes

Helm is the de facto Kubernetes package manager, and one of the best and easiest ways to install any kind ofcomplex application on Kubernetes.

 it plays a big role in the Kubernetes space andis a must-know tool.

We are going to use Helm v3 as it was the latest version of Helm at the time of writing.

Rolling back to a previous Helm releaseUsing Helm's template command

an easy way to package, configure,share, and deploy Kubernetes applications onto Kubernetes clusters.

 So where kubectl works, theHelm CLI will work too, using the same kubectl capabilities and permissions.

A chart: This is a collection of template files that describe Kubernetes resources.

A repository: A Helm repository is a location where packaged charts are stored and shared.A release: A specific instance of a chart deployed to a Kubernetes cluster.

Installing on macOS is done as follows:$ brew install helm

You can get all available Helm CLI commands with helm –h. Let's list the most used ones, along with theirdescriptions:

helm repo update: Gets the latest information about chart repositories; the information is stored locally.

helm upgrade -i: If there is no release then install it, otherwise upgrade the release.

helm rollback: Rolls back a release to a previous revision.helm template: Renders chart templates locally and displays the output.

helm create: Creates a chart.

A chart is a Helm package. It is a collection of template files that describe Kubernetes resources. It usestemplating to create Kubernetes manifests.An example Helm chart structure is shown as follows:[插图]

Chart.yaml: The file that contains information about the chart's metadata.charts: The folder where sub-charts get stored.

templates: The folder where template files get stored.

values.yaml: A YAML-formatted file with configuration values used by the chart templates. These values canbe resources, replica counts, or an image repository and tag, among other things.

To change values, it is recommended to use the override-values.yaml file, in which you just enter the valuesyou want to change. 

 It can be any web server capable ofserving files. Charts in a repository are stored in the compressed .tgz format.

A Helm release contains all Kubernetes templates from the chart, which make it much easier to track them (fromthe perspective of releases) as one single unit.

This is a very handy command to check the templates' outputs, especially when you aredeveloping a new chart, making changes to the chart, debugging, and so on.So, let's check it out by running the following command:$ helm template postgresql center/bitnami/postgresql --version=9.3.2 -f password-values.yamlThe preceding command will print all templates on the screen. Of course, you can pipe it out to the file as well.

helm template is a powerful command for checking a chart's templates and printing the output so you read itthrough. helm template doesn't connect to the Kubernetes cluster, it only fills the templates with values andprints the output.

Another strong use case for using helm template is to render templates to a file and then compare them. This isuseful for comparing chart versions or the impact of customized parameters on the final output.

 we can use the helm lint <CHART NAME> command, which will check theHelm chart content by running a series of tests to verify the chart integrity.

In this chapter, we have learned how to use Helm for installing, upgrading, rolling back releases, checking charttemplates' output, creating a chart, linting a chart, and extending Helm with plugins.

### Chapter 10: kubectl Best Practices and Docker Commands

how to use shell aliases to shorten kubectl commands, and other handy tips for usingkubectl commands, as well.

kg for kubectl get—this is useful to get a list of pods, deployments, statefulsets, services, nodes, and otherdetails, as shown in the following example command:

kd for kubectl describe—this is useful to describe pods, deployments, statefulsets, services, nodes, and soon.

The -o wide flagshows a given pod's assigned IP and the node it has been scheduled to:

ke for kubectl exec—this executes a command in the running pod:$ ke nginx-fcb5d6b64-x4kwg -- ls -alh

ktn for watch kubectl top nodes—use this to watch a node's resource consumption:

ktp for watch kubectl top pods—use this to watch a pod's resources consumption:

kpf for kubectl port-forward—use this to do a port forward for the pod so we can access the pod fromlocalhost:

  kl for kubectl logs—this shows the logs of a pod or deployment:
$ kl deploy/nginx --tail 10

The preceding screenshot shows the output of kl with the logs for the nginx deployment.

An example snippet of .zsh_aliases is shown in the following code block:
$ cat .zsh_aliases
# aliases
alias a="atom ."
alias c="code ."
alias d="docker"
alias h="helm"
alias k="kubectl"
alias ke="kubectl exec -it"
alias kc="kubectl create -f"
alias ka="kubectl apply -f"
alias kd="kubectl describe"
alias kl="kubectl logs"
alias kg="kubectl get"
alias kp="kubectl get pods -o wide"
alias kap="kubectl get pods --all-namespaces -o wide"
alias ktn="watch kubectl top nodes"
alias ktp="watch kubectl top pods"
alias ktc="watch kubectl top pods --containers"
alias kpf="kubectl port-forward"
alias kcx="kubectx"
alias kns="kubectl-ns"
Using aliases will help you to 
be more productive 
by typing a few letters instead of a few words. Also, not all commands are easy to remember, so using aliases will help to overcome that too.


Getting information is done with the following commands:docker infokubectl cluster-info

In this final chapter, we learned some kubectl best practices by examining how to use aliases to run variouscommands with kubectl, and then saw some equivalents for Docker commands in kubectl.

Using aliases shortens the time required for typing, and of course, aliases are easier to remember instead ofsome long commands.

Throughout this book, we have learned a lot of useful information, such as how to install kubectl; gettinginformation about the cluster and nodes; installing, updating, and debugging an application; working with kubectlplugins; and also learned about Kustomize and Helm.

I hope the book will help you to master Kubernetes, kubectl, and Helm.