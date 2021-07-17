## Kubernetes Operators - Red Hat version
> Jason Dobies

### Preface

An Operator extends Kubernetes to automate the management of the entire lifecycle of a particular application.Operators serve as a packaging mechanism for distributing applications on Kubernetes, and they monitor,maintain, recover, and upgrade the software they deploy.

This book explains what an Operator is and how Operators extend the Kubernetes API. It shows how to deployand use existing Operators, and how to create and distribute Operators for your applications using the Red HatOperator Framework. We relate good practices for designing, building, and distributing Operators, and we explainthe thinking that animates Operators with Site Reliability Engineering (SRE) principles.

With a cluster running, you’ll deploy anOperator and observe its behavior when its application fails, scales, or gets upgraded to a new version.

we will discuss the SREideas from which Operators derive and the goals they share: reducing operations effort and cost, increasingservice reliability, and spurring innovation by freeing teams from repetitive maintenance work.

 We won’t discuss them in detail, but after you read this book you’ll be able to compareany of them with the Operator SDK and Framework.

Indicates new terms, URLs, email addresses, filenames, and file extensions.

### 1. Operators Teach Kubernetes New Tricks

An Operator is a way to package, run, and maintain a Kubernetes application. A Kubernetes application is not only deployed on Kubernetes

For application programmers, Operators make it easier to deploy and run the foundation services on which their apps depend. 

Because the server is not tracking state or storing input or data of any kind, when one server instance fails, Kubernetes can replace it with another.
 

The controllers of the control plane implement control loops that repeatedly compare the desired state of the cluster to its actual state. 


The kube let agent running on every node is part of the control plane, for example. Likewise, an Operator is a type of controller, usually thought of as a control plane component. 

In this first example, Kubernetes manages a relatively simple application and no Operators are involved.

Operators Are Software SREs
Site Reliability Engineering (SRE) is a set of patterns and principles for running large systems

An Operator is like an automated Site Reliability Engineer for its application. It encodes in software the skills of an expert administrator.

An Operator continues to monitor its application as it runs, and can back up data, recover from failures, and upgrade the application over time, automatically.

Operators work by extending the Kubernetes control plane and API. In its simplest form, an Operator adds an endpoint to the Kubernetes API, called

 a custom resource (CR), along with a control plane component that monitors and maintains resources of the new type.
 

Figure 1-2. Operators are custom controllers watching a custom resource


 A custom resource definition (CRD) defines a CR;

CRs provide endpoints for reading and writing structured data. A cluster user can interact with CRs with kubectl or another Kubernetes client, just like any other API resource.

. An Operator is a custom Kubernetes controller watching a CR type and taking application-specific actions to make reality match the spec in that resource.

Making an Operator means creating a CRD and providing a program that runs in a loop watching CRs of that kind. What the Operator does in response to changes in the CR is specific to the application the Operator manages.

etcd is a distributed key-value store. 

In other words, it’s a kind of lightweight database cluster. An etcd cluster usually requires a knowledgeable administrator to manage it.

•  Join a new node to an etcd cluster, including configuring its endpoints, making connections to persistent storage, and making existing members aware of it.

•  Back up the etcd cluster data and configuration.

•  Upgrade the etcd cluster to new etcd versions.


The etcd Operator knows how to perform those tasks. An Operator knows about its application’s internal state, and takes regular action to align that state with the desired state expressed in the specification of one or more custom resources.

 Assume there is a three-member etcd cluster managed by the etcd Operator.
 

￼ The replacement pod is in the PodInitializing state.


For now, it’s worth remembering that adding a member to a running etcd cluster isn’t as simple as just running a new etcd pod, and the etcd Operator hides that complexity and automatically heals the etcd cluster.

. Operators make it easier for cluster administrators to enable, and developers to use, foundation software pieces like databases and storage systems with less management overhead.

Application developers build Operators to manage the applications they are delivering, simplifying the deployment and management experience on their customers’ Kubernetes clusters.

Infrastructure engineers create Operators to control deployed services and systems.


A wide variety of developers and companies have adopted the Operator pattern, and there are already many Operators available that make it easier to use key services as components of your applications.


you will deploy the etcd Operator and see how it manages an etcd cluster on your behalf.

### 2. Running Operators

we outline the requirements for running the examples in this book, and offer advice on how to establish access to a Kubernetes cluster that satisfies those requirements.

you’ll use that cluster to investigate what Operators do by installing and using one.

To build, test, and run Operators in the following chapters, you’ll need cluster-admin access to a cluster running Kubernetes version v1.11.0 or later

met these requirements, you can skip ahead to the next section

we offer general advice to readers who need to set up a Kubernetes cluster, or who need a local environment for Operator development and testing.


You can ask the Kubernetes API about the cluster-admin role to see if it exists on your cluster. The following shell excerpt shows how to get a summary of the role with the kubectl’s describe subcommand:

Operators aim to make the complex applications they manage first-class citizens of the Kubernetes API. 

It runs a single-node Kubernetes cluster in a virtual machine (VM) on your local system’s hypervisor

or with Kubernetes in Docker (kind).

Once you’ve verified that you have privileged access to a Kubernetes cluster of a compatible version, you’reready to deploy an Operator and see what Operators can do.

most Operators follow the basic pattern discernable in the etcd Operator: a CR specifies some desired state, such as the version of an application, and a custom controller watches the resource, maintaining the desired state on the cluster.

### 3. Operators at the Kubernetes Interface

Operators extend two key Kubernetes concepts: resources and controllers. The Kubernetes API includes a mechanism, the CRD, for defining new resources.


Custom Resources
CRs, as extensions of the Kubernetes API, 

contain one or more fields, like a native resource, but are not part of a default Kubernetes deployment. 

ConfigMaps are best at providing a configuration to a program running in a pod on the cluster—think of an application’s config file, like httpd.conf or MySQL’s mysql.cnf.

Kubernetes provides CRs to represent new collections of objects in the API. CRs are created and accessed by standard Kubernetes clients, like kubectl, and they obey Kubernetes conventions, like the resources .spec and .status.

Custom Controllers
CRs are entries in the Kubernetes API database.
 They can be created, accessed, updated, and deleted with common kubectl commands—but a CR alone is merely a collection of data. 

This custom controller checks and maintains the application’s desired state, represented in the CR. Every Operator has one or more custom controllers implementing its application-specific management logic.


This makes it easier for multiple users or teams to share a single cluster. Resource limits and access controls can be applied per namespace. An Operator, in turn, can be limited to a namespace, or it can maintain its operand across an entire cluster.


Note
For details about Kubernetes namespaces, see the Kubernetes namespace documentation.

Cluster-Scoped Operators
There are some situations where it is desirable for an Operator to watch and manage an application or services throughout a cluster. 

It is possible to change most Operators to run in the cluster scope instead. 

A role is a set of capabilities to take certain actions on particular API resources, such as create, read, update, or delete.

The capabilities described by a role are granted, or bound, to a user by a RoleBinding.


Service accounts, on the other hand, are managed by Kubernetes and can be created and manipulated through the Kubernetes API. 

Creating a service account is a standard step in deploying an Operator. The service account identifies the Operator, and the account’s role denotes the powers granted to the Operator.


### 4. The Operator Framework

The Red Hat Operator Framework makes it simpler to create and distribute Operators. It makes building Operators easier with a software development kit (SDK) that automates much of the repetitive implementation work. 

Operator Lifecycle Manager (OLM) is an Operator that installs, manages, and upgrades other Operators.

 Operator Metering is a metrics system that accounts for Operators’ use of cluster resources. 

In other words, phase one is the prepared, automated installation of an application.

In Chapter 7 we will show how to implement application-specific management routines in Go to build a custom Operator with the SDK tool.


 From there, the SDK provides convenience commands for building an Operator and wrapping it in a Linux container, generating the YAML-format Kubernetes manifests needed to deploy the Operator on Kubernetes clusters.

Tip
With any rapidly evolving project like operator-sdk, it’s a good idea to check the project’s installation instructions for the latest installation method.

Installing from source
To get the latest development version, or for platforms with no binary distribution, build operator-sdk from source.
 We assume you have git and go installed:

A successful build process writes the operator-sdk binary to your $GOPATH/bin directory. Run operator-sdk version to check it is in your $PATH.


These are the two most common and least dependent ways to get the SDK tool. Check the project’s install documentation for other options.

Operator Lifecycle Manager takes the Operator pattern one level up the stack: it’s an Operator that acquires, deploys, and manages Operators on a Kubernetes cluster.

OLM can manage the Operator over its lifecycle: monitoring its state, taking actions to keep it running, coordinating among multiple instances on a cluster, and upgrading it to new versions. The Operator, in turn, can control its application with the latest automation features for the app’s latest versions.

 the Operator SDK for building and developing Operators; Operator Lifecycle Manager for distributing, installing, and upgrading them; and Operator Metering for measuring Operator performance and resource consumption.

To get started, we’ll first introduce the example application you will construct an Operator to manage, the Visitors Site.


### 5. Sample Application: Visitors Site

Maintaining these types of applications, including the individual components and their interactions, is a time-consuming and error-prone process. Operators are designed to reduce the difficulty in this process.

To really help you understand the capabilities of Operators, we need an application that requires multiple Kubernetes resources with configuration values that cross between them to use for demonstration.


We’ll take a look at the application architecture and how to run the site, as well as the process of installing it through traditional Kubernetes manifests.

we’ll create Operators to deploy this application using each of the approaches provided by the Operator SDK (Helm, Ansible, and Go), and explore the benefits and drawbacks of each.

The Visitors Site tracks information about each request to its home page.
 Each time the page is refreshed, an entry is stored with details about the client, backend server, and timestamp. 

The Visitors Site is a traditional, three-tier application, consisting of:
•  A web frontend, implemented in React
•  A REST API, implemented in Python using the Django framework
•  A database, using MySQL


As shown in Figure 5-2, each of these components is deployed as a separate container. The flow is simple, with users interacting with the web interface, which itself makes calls to the backend REST API. The data submitted to the REST API is persisted in a MySQL database, which also runs as its own container.

Installation with Manifests
Each component in the Visitors Site requires two


 Kubernetes resources:
Deployment Contains the information needed to create the containers, including the image name, exposed ports, and specific configuration for a single deployment.


The MySQL container uses this secret when it is started, and the backend containers use it to authenticate against the database when making requests.

For example, the backend needs to know the name of the database service to connect to. When deploying applications through manifests, awareness of these relationships is required to ensure that the values line up.

In the following manifests, the provided values will produce a working Visitors Site deployment. Each section will highlight specific instances where user intervention was required.
You can find all of the manifests in the book’s GitHub repository.

Deploying MySQL
The secret must be created before the database is deployed, since it is used during the 


container startup:

￼ When the database and backend deployments use the secret, it is referred to by this name.

Once the secret is in place, use the following manifest to deploy a MySQL instance into Kubernetes:

￼ The deployment name must be unique to the namespace in which it is deployed.

￼ Users must be aware of each port that the image exposes, and must explicitly reference them.


￼ The values used to configure the containers for this specific deployment are passed as environment variables.

Keep in mind the value of the container port, as well as each of the environment variables, as other manifests use these values.


For that, we will need a service. The following manifest will create a Kubernetes service that provides access to the MySQL deployment:

￼ The service maps to a port exposed by a deployment, so this value must be the same as in the ports section of the deployment.


Similar to the MySQL resources, the backend needs both a deployment and a service.

 

A single error could result in the backend not being able to communicate with the database.
 Here’s the manifest to deploy the backend:
apiVersion: apps/v1


 In the backend deployment shown here, the replicas field can be modified to scale the backend.

, the port referenced in the service definition must match up with that exposed by the deployment.


Deploying the Manifests
You can run the 


Visitors Site for yourself using the kubectl command:

Accessing the Visitors Site
Using these manifests, you can find the home page by using the IP address of the Minikube instance

 and specifying port 30686 in your browser. The minikube command provides the IP address to access:


For this Minikube instance, you can access the Visitors Site by opening a browser and going to http://192.168.99.100:30686.

Cleaning Up
Similar to deploying the manifests, you delete the created resources using the kubectl command:

We will use this sample application in the following chapters to demonstrate a variety of technologies on which you can build Operators.

All of the following Operator implementations create a custom resource definition that acts as the sole API for creating and configuring an instance of the Visitors Site.



### 6. Adapter Operators

Consider the numerous steps it would take to write an Operator from scratch.
 

Roles and service accounts would need to be created to permit the Operator to function in the capacity it needs. An Operator is run as a pod inside of a cluster, so an image would need to be built, along with its accompanying deployment manifest.

Many projects have already invested in application deployment and configuration technologies.

 The Helm project allows users to define their cluster resources in a formatted text file and deploy them through the Helm command-line tools.

Ansible is a popular automation engine for creating reusable scripts for provisioning and configuring a group of resources. Both projects have devoted followings of developers who may lack the resources to migrate to using Operators for their applications.

The Operator SDK provides a solution to both these problems through its Adapter Operators. 
Through the command-line tool, the SDK generates the code necessary to run technologies such as Helm and Ansible in an Operator. This allows you to rapidly migrate your infrastructure to an Operator model without needing to write the necessary supporting Operator code. 

•  Provides a consistent interface through CRDs, regardless of whether the underlying technology is Helm, Ansible, or Go.

•  Enables hosting of those solutions

 on Operator repositories like OperatorHub.io (see Chapter 10 for more information).

Custom Resource Definitions

Operators make heavy use of CRs and use watches on these objects to react to changes as they are made.

Helm Operator
Helm is a package manager for Kubernetes.

 It makes it easier to deploy applications with multiple components, but each deployment is still a manual process. 

If you’re deploying many instances of a Helm-packaged app, it would be convenient to automate those deployments with an Operator.

These configuration values are specified in a file named values.yaml. A Helm Operator can deploy each instance of an application with a different version of values.yaml.

To configure another instance of the application, you create a new CR containing appropriate values for the instance.

deploy/ This directory contains Kubernetes template files that are used to deploy and configure the Operator, including the CRD, the Operator deployment resource itself, and the necessary RBAC resources for the Operator to run.

watches.yaml This file maps each CR type to the specific Helm chart that is used to handle it.


The process for building an Operator from an existing Helm chart is similar to that for creating an Operator with a new chart.

 In addition to the --type=helm argument, there are a few additional arguments to take into consideration:


When using the --helm-chart argument, the --api-version and --kind arguments become optional. 

The following example demonstrates how to build an Operator and initialize it using an archive of the Visitors Site Helm chart:


The generated deployment files include the role the Operator will use to connect to the Kubernetes API. Bydefault, this role is extremely permissive. Appendix C talks about how to fine-tune the role definition to limit theOperator’s permissions.

An Operator is delivered as a normal container image. However, during the development and testing cycle, it isoften easier to skip the image creation process and simply run the Operator outside of the cluster. In this sectionwe describe those steps (

The simplest approach is to copy the existing watches.yaml file, which is located in the root of the Operatorproject:

Once you have finished creating the CRDs, start the Operator using the following SDK command:

This command starts a running process that behaves in the same way the Operator would if you had deployed itas a pod inside the cluster. (

Ansible is a popular management tool for automating the provisioning and configuration of commonly run tasks.Similar to a Helm chart, an Ansible playbook defines a series of tasks that are run on a set of servers. Reusableroles, which extend Ansible through custom functionality, may be used to enhance the set of tasks in a playbook.

One useful collection of roles is k8s, which provides tasks for interacting with a Kubernetes cluster. Using thismodule, you can write playbooks to handle the deployment of applications, including all of the necessarysupporting Kubernetes resources.

The SDK creates a deploydirectory that contains the same set of files, including the CRD and deployment template.There are a few notable differences from the Helm Operator:

 For simplicity whilefollowing along with the example application, the role files are available as a release there.

As with the Helm Operator, the easiest way to test and debug an Ansible Operator is to run it outside a cluster,avoiding the steps of building and pushing an image.Before you can do this, however, there are a few extra steps you need to take:

Additional Ansible-related packages must be installed as well, including the following (consult the documentationfor details on installation):

Now let’s walk through the steps of how to test an Operator.

Once the test is complete, end the running process by pressing Ctrl-C.

During development, repeat this process to test changes. On each iteration, be sure to restart the Operatorprocess to pick up any changes to the Helm or Ansible files.

You don’t need to be a programmer to write an Operator. The Operator SDK facilitates the packaging of twoexisting provisioning and configuration technologies, Helm and Ansible, as Operators. The SDK also provides away to rapidly test and debug changes by running an Operator outside of the cluster, skipping the time-consuming image building and hosting steps.

we’ll look at a more powerful and flexible way of implementing Operators by using the Golanguage.

### 7. Operators in Go with the Operator SDK

Advanced use cases, such as those that involve dynamically reacting to specificchanges in the application or the cluster as a whole, require a more flexible solution.

Create the necessary code that will tie in to Kubernetes and allow it to run the Operator as a controller.

Create a controller for each CRD to handle the lifecycle of its resources.Build the Operator image and create the accompanying Kubernetes manifests to deploy the Operator and itsRBAC components (service accounts, roles, etc.).

While you can write all these pieces manually, the Operator SDK provides commands that will automate thecreation of much of the supporting code, allowing you to focus on implementing the actual business logic of theOperator.

 Once the Operator is ready,we’ll run it in development mode for testing and debugging.

 In particular,the Operator code must be located in your $GOPATH. See the GOPATH documentation for more information.

The SDK’s new command creates the necessary base files for the Operator. 

The output is truncated for readability. The generation can take a few minutes as all of the Go dependencies aredownloaded. The details of these dependencies will appear in the command output.

Conveniently, you do not need tomanually edit most of them. We will show you how to generate the files necessary to fulfill custom logic for anOperator in “Custom Resource Definitions”.

One of the first decisions you need to make is the scope of the Operator. There are two options:NamespacedLimits the Operator to managing resources in a single namespaceClusterAllows the Operator to manage resources across the entire cluster

Make the following changes to enable the Operator to work at the cluster level:deploy/operator.yamlChange the value of the WATCH_NAMESPACE variable to "", indicating all namespaces will be watched instead ofonly the namespace in which the Operator pod is deployed.

deploy/role.yamlChange the kind from Role to ClusterRole to enable permissions outside of the Operator pod’s namespace.deploy/role_binding.yamlChange the kind from RoleBinding to ClusterRoleBinding.

Additionally, you need to update the generated CRDs (discussed in the following section) to indicate that thedefinition is cluster-scoped:

In the spec section of the CRD file, change the scope field to Cluster instead of the default value ofNamespaced.

 but before you can use the resource type, you mustdefine the set of configuration values that are specified when creating a new resource. You’ll also need to add adescription of the fields the CR will use when reporting its status. You’ll add these sets of values in the definitiontemplate itself as well as the Go objects. The following two sections go into more detail about each step.

It is the Operator’s responsibility to determine how to use the values.

BackendImageIndicates the image and version used to deploy the backend pods

Insideof the Operator pod itself, you need a controller to watch for changes to CRs and react accordingly.

You do not need to manually edit the other generated files.

The controller is responsible for “reconciling” a specific resource. 

Instead of having explicit handling for events suchas add, delete, or update, the controller is passed the current state of the resource. It is up to the controller todetermine the set of changes to update reality to reflect the desired state described in the resource. 

In addition to the reconcile logic, the controller also needs to establish one or more “watches.” A watch indicatesthat Kubernetes should invoke this controller when changes to the “watched” resources occur. 

The first watch listens for changes to the primary resource that the controller monitors.

The following snippet creates the watch for theVisitorsApp resource type:

For example, the following code creates two watches, one for deployments and one for serviceswhose parent resource is of the type VisitorsApp:

The Reconcile function, also known as the reconcile loop, is where the Operator’s logic resides. The purpose ofthis function is to resolve the actual state of the system against the desired state requested by the resource.

As Kubernetes invokes the Reconcile function multiple times throughout the lifecycle of a resource, it isimportant that the implementation be idempotent to prevent the creation of duplicate child resources. 

The Reconcile function returns two objects: a ReconcileResult instance and an error (if one is encountered).These return values indicate whether or not Kubernetes should requeue the request. In other words, the Operatortells Kubernetes if the reconcile loop should execute again. The possible outcomes based on the return valuesare:

The reconcile failed due to an error and Kubernetes should requeue it to try again.

Similar to the previous result, but this will wait for the specified amount of time before requeuing the request.

For example, if a backend service needs a running database prior to starting, the reconcile can be requeued witha delay to give the database time to start. Once the database is running, the Operator does not requeue thereconcile request, and the rest of the steps continue.

Since Go-based Operators make heavy use of the Go Kubernetes libraries, it may be useful to review the API documentation. In particular, the core/v1 and apps/v1 modules are frequently used to interact with the common Kubernetes resources.

Retrieving the Resource
The first step the Reconcile function typically performs is to retrieve the primary resource that triggered the reconcile request.


•  Retrieving configuration values about the resource from its Spec field

•  Setting the current state of the resource using its Status field, and saving that updated information into Kubernetes

provides a function to update a resource’s values. When updating a resource’s Status field, you’ll use this function to persist the changes to the resource back into Kubernetes. 

The handling forthem is similar and straightforward: check to see if the resource is present in the namespace and, if it is not,create it.

The following example snippet checks for the existence of a deployment in the target namespace:

 In real use cases, "myDeployment" is replaced with thesame name the Operator used when the deployment was created, taking care to ensure uniqueness relative to thenamespace as appropriate.

This is the value used in the earlier snippet when you are attempting to see if the deployment exists.

For this example, these are hardcoded values. Take care to generate randomized values as appropriate.

 Ifthe child resource’s owner type is correctly set to the primary resource, when the parent is deleted, Kubernetesgarbage collection will automatically clean up all of its child resources.

There are times, however, where specific cleanup logic is required. The approach in such instances is to block thedeletion of the primary resource through the use of a finalizer.A finalizer is simply a series of strings on a resource. If one or more finalizers are present on a resource, themetadata.deletionTimestamp field of the object is populated, signifying the end user’s desire to delete theresource. 

Once the Operator has finished with the necessary cleanup, it removes the finalizer,unblocking Kubernetes from performing its normal deletion steps.

While the end user provides the name of the CR when creating it, the Operator is responsible for generating thenames of any child resources it creates. Take into consideration the following principles when creating thesenames:Resource names must be unique within a given namespace.

Child resource names should be dynamically generated. Hardcoding child resource names leads to conflicts ifthere are multiple resources of the CR type in the same namespace.

One of the biggest hurdles many developers face when writing controllers is the idea that Kubernetes uses adeclarative API. End users don’t issue commands that Kubernetes immediately fulfills. Instead, they request anend state that the cluster should achieve.

 Instead, Kubernetes simply asks the controller to reconcile thestate of a resource. The Operator then determines what steps, if any, it will take to ensure that end state.

Therefore, it is critical that Operators are idempotent. Multiple calls to reconcile an unchanged resource mustproduce the same effect each time.

The following tips can help you ensure idempotency in your Operators:

Before creating child resources, check to see if they already exist. Remember, Kubernetes may call the reconcileloop for a variety of reasons beyond when a user first creates a CR. Your controller should not duplicate the CR’schildren on each iteration through the loop.

 If theOperator incorrectly handles these operations, they can negatively affect the performance of the entire cluster.

Be careful when making frequent calls to the Kubernetes API. Make sure you use sensible delays (on the order ofseconds rather than milliseconds) when repeatedly checking the API for a certain state being met.

When possible, try not to block the reconcile method for long periods of time. If, for instance, you are waiting fora child resource to be available before continuing, consider triggering another reconcile after a delay 

 Remember that other workloads are running concurrently on the cluster.Your Operator should not cause excessive stress on cluster resources by issuing many creation requests at once.

The Operator SDK provides a means of running an Operator outside of a running cluster. This helps speed updevelopment and testing by removing the need to go through the image creation and hosting steps. The processrunning the Operator may be outside of the cluster, but Kubernetes will treat it as it does any other controller.

The high-level steps for testing an Operator are as follows:Deploy the CRD. You only need to do this once, unless further changes to the CRD are needed. In those cases,run the kubectlapply command again (from the Operator project root directory) to apply any changes:

 The Operator SDK uses credentials from the kubectl configuration file toconnect to the cluster and attach the Operator. The running process acts as if it were an Operator pod runninginside of the cluster and writes logging information to standard output:

The --namespace flag indicates the namespace in which the Operator will appear to be running.

Deploy an example resource. The SDK generates an example CR along with the CRD. It is located in the samedirectory and is named similarly to the CRD, but with the filename ending in _cr.yaml instead to denote itsfunction.

In most cases, you’ll want to edit the spec section of this file to provide the relevant configuration values for yourresource. Once the necessary changes are made, deploy the CR (from the project root directory) using kubectl:

The Operator SDK generated many of the files in that repository. The files that were modified to run the VisitorsSite application are:

Thisincludes the deployments and services that the Operator maintains, as well as the logic to handle updatingexisting resources when the end user changes the CR.

common.goThis file contains utility methods used to ensure the deployments and services are running, creating them ifnecessary.

 letting you focus on the business logicaspects. The SDK also provides utilities for building and testing Operators, greatly reducing the effort needed togo from inception to a running Operator.

### 8. Operator Lifecycle Manager

Chapter 8. Operator Lifecycle Manager

Once you have written an Operator, it’s time to turn your attention to its installation and management.
 As there are multiple steps involved in deploying an Operator, including creating the deployment, adding the custom resource definitions, and configuring the necessary permissions, a management layer becomes necessary to facilitate the process.


the CSV contains information on how to install the Operator and any dependencies it requires. Following this analogy, you can think of OLM as a management tool similar to yum or DNF.


In the same way, a subscription to an Operator by name and its channel lets OLM resolve the version based on what is available in that particular channel.

As OLM is not installed by default in most Kubernetes distributions, the first step is to install the necessary resources to run it.

OLM is an evolving project. As such, be sure to consult its GitHub repository to find the latest installation instructions for the current release. You can find the releases on the project’s GitHub repository.

you can update these commands to use the most up-to-date version available at the time you’re reading the book.

You can verify the installation by looking at the resources that were created:

Now that we’ve introduced the basic concepts around OLM, let’s see how to use it to install an Operator.
 We’ll use OperatorHub.io as the source repository for Operators.


 

You can find
 further details about the source by using the describe command:

This catalog source is configured to read all of the Operators hosted on OperatorHub.io. You can use the packagemanifest utility API to get a list of the Operators 
that are found:


At the time of writing, there are close to 80 Operators on OperatorHub.io. We truncated the output of this command for brevity.

The creation
 of a subscription triggers the installation of an Operator. Before you can do that, you need to determine which channel you want to subscribe to. 

Here is an example manifest:


￼ The name of the Operator to be installed, as found by the packagemanifest API call.

Exploring the Operator
When you create the subscription, a number of things happen. At the highest level of 

the resource hierarchy, OLM creates a ClusterServiceVersion resource in the default namespace:

 OLM performs the Operator installation steps defined in the CSV to create the Operator pods themselves. Additionally, OLM will store information about events in this process, which you can view using the describe command:

The output here has been edited to fit the page. Your output will vary slightly and contain more data per event.

OLM is responsible for following the deployment template contained within the CSV to create the Operator pod itself.

•  OLM creates a CSV resource in the same namespace as the subscription. This CSV contains, among other things, the manifest for the deployment of the Operator itself.

•  The deployment causes the creation of replica sets and pods for the Operator itself.

Kubernetes detects the difference between the desired state of the deployment and the actual number of pods.

OLM takes care of deleting the resources that the CSV created when it was originally deployed, including the Operator’s deployment resource.

Additionally, you’ll need to delete the subscription to prevent OLM from installing new CSV versions in the future:

There are three types of files included in an OLM bundle: custom resource definitions, Cluster Service Version files, and package manifest files.


Keep in mind that only CRDs that are owned by the Operator should be included. Any dependent CRDs that are provided by other Operators will be installed automatically by OLM’s dependency resolution

•  Metadata about the Operator, including a description, logo, its maturity level, and related links

The CSV file is a standard Kubernetes manifest of kind ClusterServiceVersion, which is one of the custom resources that OLM provides.



The resources in this file provide OLM with information about a specific Operator version, including installation instructions and extra details on how the user interacts with the Operator’s CRDs.

Running the CSV generation command on the Visitors Site Operator produces the following output:

•  A set of RBAC rules that the Operator requires

maintainers A list of name and email pairings for the maintainers of the Operator codebase
provider The name of the publishing entity for the Operator


This snippet from the etcd Operator demonstrates the three required fields:

We also encourage you to provide the following metadata fields, which produce a more robust listing in catalogs such as OperatorHub.io:
displayName A user-friendly name for the Operator
description A string describing the Operator’s functionality; you can use YAML 

version The semantic version of the Operator, which should be incremented each time a new Operator image is published


icon A base64–encoded image used by compatible UIs

links A list of relevant links for the Operator, such as documentation, quick start guides, or blog entries

minKubeVersion The minimum version of Kubernetes that the Operator must be deployed on, using the format “Major.Minor.Patch” (e.g., 1.13.0)


Each entry should contain the following:
displayName The user-friendly name of the field
description Information about what the field represents
path The dot-delimited path of the field in the object

Each required CRD is specified using its:
name The full name used to identify the required CRD
version The version of the CRD desired
kind The Kubernetes resource kind; displayed to users in compatible UIs
displayName The user-friendly name of the field; displayed to users in compatible UIs

There are four options, all of which must be present in the installModes field with their own flag indicating whether or not they are supported.

The following installation modes are supported:
OwnNamespace The Operator can be deployed to an OperatorGroup that selects its own namespace.

The following snippet shows the proper way to structure this field, along with the default values set by the SDK during generation:


Subsequent versions of the Operator will each have their own CSV file. In many cases, this can be a copy of the previous version with the appropriate changes.

•  Change the new CSV filename to reflect the new version of the Operator.

•  Update the metadata.name field of the CSV file with the new version.
•  Update the spec.version field with the new version.
•  Update the spec.replaces field to indicate the previous version of the CSV that is being upgraded by the new version.

•  In most cases, the new CSV will refer to a newer image of the Operator itself. Be sure to update the spec.containers.image field as appropriate to refer to the correct image.


Warning
Do not modify existing CSV files once they are released and in use by OLM. Make changes in a new version of the file instead, and propagate it to users through the use of channels.




Compared to writing a Cluster Service Version file, writing a package manifest is significantly easier.

 A package file requires three fields:
packageName The name of the Operator itself; this should match the value used in the CSV file


name The name of the channel; this is what users will subscribe to
currentCSV The full name (including the Operator name but not the .yaml suffix) of the CSV file that is currently installed through the channel


You can verify the installation by ensuring the marketplace namespace was created:

In order for Operator Courier to push OLM bundles into your Quay.io account, you need an authentication token. While the token is accessible through the web UI, you can also use the following script to retrieve it from the command line, substituting your username and password as indicated:

An interactive version of this script is provided in this book’s GitHub 

You will use this token later when pushing the bundle to Quay.io, so save it somewhere accessible. The output of the script provides a command to save it as an environment variable.


Once you’ve written the OperatorSource manifest, create the resource using the following command (assuming the manifest file is named operator-source.yaml):


To verify the OperatorSource was deployed 
correctly, you can look in the marketplace namespace for a list of all known OperatorSources:

the status will be Failed. You can ignore this for now; you’ll refresh this list later, once you’ve uploaded a bundle.

your results may vary slightly.

When the OperatorSource is initially created, it may fail if there are no OLM bundles found in the user’s Quay.io application list.

No further action is required for this resource, but you can confirm its existence by checking in the marketplace namespace:


 test it by creating a subscription to oneof its supported channels. OLM reacts to the subscription and installs the corresponding Operator.

You’ll need an OperatorGroup to denote which namespaces the Operator should watch. It must exist in thenamespace where you want to deploy the Operator. For simplicity while testing, the example OperatorGroupdefined here deploys the Operator into the existing marketplace namespace:

A subscription links the previous steps together by selecting an Operator and one of its channels. OLM uses thisinformation to start the corresponding Operator pod.The following example creates a new subscription to the stable channel for the Visitors Site Operator:

Indicates the namespace the subscription will be created in.[插图]Selects one of the channels defined in the package manifest.[插图]Identifies which OperatorSource to look at for the corresponding Operator and channel.[插图]Specifies the OperatorSource’s namespace.

Testing the Running OperatorOnce OLM has started the Operator, you can test it by creating a custom resource of the same type that theOperator owns. Refer to Chapters 6 and 7 for more information about testing a running Operator.

bundleThis directory contains the actual OLM bundle files, including the CSV, CRD, and package files. You can use theprocess outlined in this chapter to build and deploy the Visitors Site Operator using these files.testing

This directory contains the additional resources required to deploy an Operator from OLM. These include theOperatorSource, OperatorGroup, subscription, and a sample custom resource to test the Operator.

Readers are welcome to submit feedback, issues, and questions on these files through the Issues tab in GitHub.

As with any piece of software, managing installation and upgrades is critical for Operators. Operator LifecycleManager fills this role, giving you a mechanism for discovering Operators, handling updates, and ensuringstability.

### 9. Operator Philosophy

we talked about Operators as software SREs. Let’s review some key SRE tenets to understand how Operators apply them.

SRE began at Google in response to the challenges of running large systems with ever-increasing numbers of users and features.

 

SREs write code to handle deployment, operations, and maintenance tasks. SREs create software that runs other software, keeps it running, and manages it over time. 

SRE is a wider set of management and engineering techniques with automation as a central principle. 

Operators and the Operator Framework make it easier to implement this kind of automation for a

: “If a human operator needs to touch your system during normal operations, you have a bug.”1 SREs write code to fix those bugs. Operators are a logical place to program those fixes for a broad class of applications on Kubernetes. An Operator reduces human intervention bugs by automating the regular chores that keep its application running.

SRE tries to reduce toil by creating software to perform the tasks required to operate a system.

Work that could be automated by software should be automated by software if it is also repetitive. The cost of building software to perform a repetitive task can be amortized over a lifetime of repetitions.

Yet despite having no enduring value, backups are clearly worth doing. This kind of work often makes a good job for an Operator.

But if adding a new instance requires an engineer to configure computers and wire them to a network, scaling the service is anything but automatic. In the worst cases of this kind of toil, ops work might scale linearly with your service.

If you deploy your stateless web server on Kubernetes instead, you can scale it up and down with little more than a kubectl command. 



You don’t have to program the load balancer to deliver traffic to a new instance. Software does those chores instead.


Operators extend Kubernetes to extend the principle of automation to complex, stateful applications running on the platform.

 

If you are on a team responsible for managing internal services, Operators will enable you to capture expert procedures in software and expand the system’s “normal operations”: 

You can build Operators that not only run and upgrade an application, but respond to errors or slowing performance.


Control loops in Kubernetes watch resources and react when they don’t match some desired state. 

The Operator can then be extended to observe key application metrics and act to tune, repair, or report on them.

Site Reliability Engineering lists the four golden signals as latency, traffic, errors, and saturation.

An Operator can monitor these signals and take application-specific actions when they depict a known condition, problem, or error. Let’s take a closer look:

Latency is how long it takes to do something. It is commonly understood as the elapsed time between a request and its completion. 

Traffic measures how frequently a service is requested. HTTP requests per second is the standard measurement of web service traffic.

It makes more sense to monitor something like transactions per second for a database or file server.

 Errors are failed requests, like an HTTP 500-series error. In a web service, you might have an HTTP success code but see scripting exceptions or other client-side errors on the successfully delivered page. 

1.  An Operator should run as a single Kubernetes deployment.
Y

To illustrate this, although you usually need to configure RBAC and a service account, you can add the etcd Operator to a Kubernetes cluster with a single command. It is just a deployment:


2.  Operators should define new custom resource types on the cluster.
Think of the etcd examples back in Chapter 2. You created a CRD, and in it you defined
 a new kind of resource, the EtcdCluster. 

Don’t write new code when API calls can do the same thing in a more consistent and widely tested manner.
 

Note that removing a CRD does affect the operand application. In fact, deleting a CRD will in turn delete its CR instances.

5.  An Operator should understand the resource types created by any previous versions.


7.  Operators should be thoroughly tested, including chaos testing.

Each phase aims to end a little more human toil.

By monitoring its application’s golden signals, an Operator can make informed decisions and free engineers from rote operations tasks.


### 10. Getting Involved

One of the simplest ways of interacting with both users and developers of the Operator Framework is through its Special Interest Group, or SIG.
 The SIG uses a mailing list to discuss topics including upcoming release information, best practices, and user questions. The SIG is free to join from their website.

Additionally, the project teams use the issue tracker to track feature requests. The New Issue button prompts submitters to select between bug reports and feature requests, which are then automatically tagged appropriately.

There are a few general principles1 to keep in mind when submitting a new issue:

•  Be specific. For bugs, provide as much information as possible about the running environment, including project versions and cluster details. When possible, include detailed reproduction steps. For feature requests, start by including the use case being addressed by the requested feature. 

•  Keep the scope limited to a single bug. 

•  Use an existing issue if one is found. Use GitHub’s issue tracker’s search ability to see if a similar bug or feature request is found before creating a new report. Additionally, check the list of closed issues and reopen an existing bug if possible.

OperatorHub.io is a hosting site for community-written Operators.
 The site contains Operators from a wide variety of categories, including:
•  Databases
•  Machine learning
•  Monitoring
•  Networking
•  Storage

### A. Running an Operator as a Deployment Inside a Cluster

2.  Configure the deployment. Update the deploy/operator.yaml file that the SDK generates with the name of the image. The field to update is named image and can be found under:

4.  Deploy the service account and role. The SDK generates the service account and role required by the Operator. Update these to limit the permissions of the role to the minimum of what is needed for the Operator to function.


5.  Create the Operator deployment. The last step is to deploy the Operator itself. You can use the previously edited operator.yaml file to deploy the Operator image into

 the cluster:


### B. Custom Resource Validation

You’ll need to manually add this validation to the CRD to describe the allowed values for both the spec and status sections.

### C. Role-Based Access Control (RBAC)

regardless of whether it is a Helm, Ansible, or Go-basedOperator), it creates a number of manifest files for deploying the Operator. Many of these files grant permissionsto the deployed Operator to perform the various tasks it does throughout its lifetime.

Instead of authenticating as a user, Kubernetes provides a programmatic authentication method in the form ofservice accounts. A service account functions as the identity for the Operator pod when making requests againstthe Kubernetes API. This file simply defines the service account itself, and you do not need to manually edit it.More information on service accounts is available in the Kubernetes documentation.

 The role dictates what permissions the serviceaccount has when interacting with the cluster APIs. The Operator SDK generates this file with extremely widepermissions that, for security reasons, you will want to edit before deploying your Operator in production. In thenext section we explain more about refining the default permissions in this file.

For example, the following rolegrants view (but not create or delete) permissions for deployments:

The following snippet, taken from anSDK-generated Operator project, illustrates this. The * wildcard allows all actions on the given resources:

Not surprisingly, it is considered a bad practice to grant such open and wide-reaching permissions to a serviceaccount. The specific changes you should make vary depending on the scope and behavior of your Operator.

Generally speaking, you should restrict access as much as possible while still allowing your Operator to function.

For example, the following role snippet provides the minimal functionality needed by the Visitors Site Operator:

Full details on configuring Kubernetes roles are outside the scope of this book. You can find
 more information in the Kubernetes RBAC documentation.


### Index

•  service accounts
▪  about, Service Accounts
▪  defining Operator service account, 

◦  deploying with manifest, Backend


•  Special Interest Group (SIG) for Operator framework, Getting Involved

•  TLS certificates, Cluster-Scoped Operators

Jason has worked in the software industry for close to 20 years, developing in a variety of languages, including Python, Java, and Go. In addition to his career as an engineer, he is also an adjunct professor at Villanova University, where he currently teaches software engineering and senior projects. 

When not sitting at a computer, Jason enjoys spending time with his wife and two children, playing video games, and working out.