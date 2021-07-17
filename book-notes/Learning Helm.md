## Learning Helm
> Josh Dolitsky

### Preface

It is much easier and faster to install applications through Helm than it is to learn how to do so by hand with Kubernetes.

If you work for a company (or a project) that wants to distribute your applications to Kubernetes users in an easy-to-consume manner, this book will teach you how to do that with Helm.

Being able to quickly install your application makes getting started easier, and Helm can help you with that.

Helm provides powerful and advanced features that can be used as building blocks for other automation. These have been used to deploy large and complex applications onto Kubernetes, and this book will teach you how to leverage those features.

We, the authors, are maintainers of Helm, so we set out to write a book to help those who have questions about it.

we wanted to provide context and insight into what Helm does and why.

address using the Helm client, beginning with installing Helm and progressing to advanced usage.

### 1. Introducing Helm

Helm is the package manager for Kubernetes. That is the way the Helm developers have described Helm since the very first commits to the Git repository. And that sentence is the topic of this chapter.




We will take a fresh look at what Kubernetes has to offer to set the stage for describing Helm.

Next, we will look at the problems Helm sets out to solve. In this section, we will look at the concept of package management and why we have modeled Helm this way.

you will be familiar with the terminology and concepts we will be using throughout this book.

The Cloud Native Ecosystem
The emergence of cloud technologies has clearly changed the way the industry looks at hardware, system management, physical networking, and so on. 



Virtual machines replaced physical servers, storage services displaced talk of hard drives

Name An image name can range from simple to complex, 


depending on the registry that stores the image: nginx, servers/nginx, or example.com/servers/nginx

Tag The tag typically refers to the version of the software installed (v1.2.3), 


though tags are really just arbitrary strings. The tags latest and stable are often used to indicate “the most recent version” and “the most recent production-ready version,” respectively.


Digest Sometimes it is important to pull a very specific version of an image. 



Since tags are mutable, there is no guarantee that at any given time a tag refers to exactly a specific version of the software. So registries support fetching images by digest, which is a SHA-256 or SHA-512 digest of the image’s layer information.

 Then I test it to see if the container is ready. Then I mark it as available.

In a reconciliation loop, the scheduler says “here is the user’s desired state. Here is the current state. They are not the same, so I will take steps to reconcile them.” 

 Different subsystems in Kubernetes work to fulfill their individual part of the user’s overall declaration of desired state.

￼ The first two lines define the Kubernetes kind (v1 Pod).
￼ A pod can have one or more containers.

These are called sidecar containers. These are all considered part of the same pod.

In this book, we typically use resource because the word object is overloaded: YAML defines the word object to mean a named key/value structure.

A Secret is structurally similar to a ConfigMap, except that the values in the data section must be Base64 encoded.

￼ The volumes section tells Kubernetes which storage sources this pod needs.
￼ The name configuration-data is the name of our ConfigMap we created in the previous example.
￼ The env section injects environment variables into the container.


￼ Inside of the spec, we ask for three replicas of the following template.
￼ The template specifies how each replica pod should look.

￼ This Service will route to pods with the app: my-deployment label.

￼ TCP traffic to port 80 of this Service will be routed to port 8080 on the pods that match the app: my-deployment label.
The Service described will route traffic to the Deployment we created earlier.


In the next chapter we will begin working with these concepts more directly. But for now, armed with some generic information, we can introduce Helm.

we wanted to provide users with ready-made production-ready examples. Users could install those examples, see them in action, and then learn how Kubernetes worked.

our first priority with Helm: make it easier to get going with Kubernetes. In our view, a new Helm user with an existing Kubernetes cluster should be able to go from download to an installed application in five minutes or less.

Kubernetes is like an operating system. At its foundation, an operating system provides an environment for executing programs.




 It provides the tools necessary to store, execute, and monitor the life cycle of a program.

Instead of executing programs, it executes containers. But similar to an operating system, it provides the tools necessary to store, execute, and monitor those containers.

•  Helm provides package repositories and search capabilities to find what Kubernetes applications are available.
•  Helm has the familiar install, upgrade, and delete commands.
•  Helm defines a method for configuring packages prior to installing them.
•  Additionally, Helm has tools for seeing what is already installed and how it is configured.

Likewise, operations in Helm are namespace-sensitive. One can install the same application into two different namespaces, and Helm provides tools to manage these different instances of the application.


￼ The API family and version for this resource.
￼ The kind of resource. Combined with apiVersion, we get the “resource type”.
￼ The metadata section contains top-level data about the resource.
￼ A name is required for almost every resource type.
￼ Labels are used to give Kubernetes query-able “handles” to your resources.
￼ Annotations provide a way for authors to attach their own keys and values to a resource.

Thus, apps/v1 Deployment indicates that the API group “apps” has a “version 1” (stable) resource kind called “Deployment.”


Kubernetes supports two main formats for declaring the resources you want: JSON and YAML.

 Strictly speaking, YAML is a superset of JSON. All JSON documents are valid YAML, but YAML adds a number of additional features.

In this book, we stick to the YAML format. We find it easier to read and write, and almost all Helm users choose YAML over JSON. However, should your preferences differ, both Kubernetes and Helm support plain JSON.


 package is called a chart.








 

A chart contains a file called Chart.yaml that describes the chart. 


It has information about the chart version, the name and description of the chart, and who authored the chart.

A chart contains templates as well. These are Kubernetes manifests


 (like we saw earlier in this chapter) that are potentially annotated with templating directives. We will cover these in detail in Chapter 5.


A chart may also contain a values.yaml file that provides default configuration.



 This file contains parameters that you can override during installation and upgrade.

An unpacked Helm chart is just a directory. Inside, it will have a Chart.yaml, 
a values.yaml, a templates/ directory, and perhaps other things as well. A packed Helm chart contains the same information as an unpacked one, but it is tarred and gzipped into a single file.



Charts are stored in chart repositories, which we will cover in Chapter 7. 
Helm knows how to download and install charts from repositories.

2.  It sends the values into the templates, generating Kubernetes manifests.




3.  The manifests are sent to Kubernetes.
4.  Kubernetes creates the requested resources inside of the cluster.

 A new release of an installation is created each time we use Helm to modify the installation.


There is no mention of Tiller or gRPC. These things were removed from Helm 3, which is the subject of the present book. Also, this version of the book focuses on version 2 Helm charts. As confusing as it is, the Helm chart version increments separately from the Helm version. So Helm v2 used Helm Charts v1, and Helm v3 uses Helm Charts v2. 

Helm 2 and Helm Charts v1 are considered deprecated.

Helm is only successful if it makes Kubernetes more usable both for the first-time users and for the long-time operations teams and SREs that use Helm day to day. The remainder of this book is dedicated to explaining (with lots of examples) how to get the most out of Helm—and how to do so securely and idiomatically.


### 2. Using Helm


 In this chapter, we will discover the primary features of the helm client. Along the way, we’ll learn how Helm interacts with Kubernetes.


We will start by looking at how to install and configure Helm, and work our way through the main command groups in Helm. Then we will cover finding and learning about packages, and how to install, upgrade, and delete them.

Each time the Helm maintainers issue a new release of helm, the project provides new signed binary


 builds of helm for a number of common operating systems and architectures. 

The usual sequence of commands for installing this way is as follows:
$ curl -fsSL -o get_helm.sh \
https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh

Working with Kubernetes Clusters
Helm interacts directly with the Kubernetes API server. For that reason, 




Helm needs to be able to connect to a Kubernetes cluster. Helm attempts to do this automatically by reading the same configuration files used by kubectl (the main Kubernetes command-line client).

Helm will try to find this information by reading the environment variable $KUBECONFIG. If that is not set, it will look in the same default locations that kubectl looks in (for example, $HOME/.kube/config on UNIX, Linux, and macOS).

You can also override these settings with environment variables (HELM_KUBECONTEXT) and command-line flags (--kube-context). You can see a list of environment variables and flags by running helm help.

1.  Add a chart repository.
2.  Find a chart to install.
3.  Install a Helm chart.
4.  See the list of what is installed.
5.  Upgrade your installation.
6.  Delete the installation.

A Helm chart is an individual package that can be installed into your Kubernetes cluster.


 During chart development, you will often just work with a chart that is stored on your local filesystem.


Note
Helm 3 introduced an experimental feature for storing Helm charts in a different kind of repository:




 Open Container Initiative (OCI) registries (sometimes called Docker registries). In this backend, a Helm chart can be stored alongside Docker images.

In Helm 3, there is no default repository. Users are encouraged to use the Artifact Hub to find what they are looking for and then add their preferred repositories.

Note
A handful of Bitnami developers were among the core contributors who designed the Helm repository system. They have contributed to the establishment of Helm’s best practices for chart development and have written many of the most widely used charts.

Now we can verify that the Bitnami repository exists by running a second repo command:
$ helm repo list
NAME    URL
bitnami https://charts.bitnami.com/bitnami

By default, Helm tries to install the latest stable release of a chart, but you can override this behavior and install a specific verison of a chart. Thus it is often useful to see not just the summary info for a chart, but exactly which versions exist for a chart:

$ helm search repo drupal --versions


Chart and App Versions
A chart version is the version of the Helm chart. The app version is the version





 of the application packaged in the chart. Helm uses the chart version to make versioning decisions, such as which package is newest.

When working with namespaces and Helm, you can use the --namespace or -n flags to specify the namespace you desire.




The Helm core maintainers consider it a good practice to keep your configuration values in a YAML file. It is important to keep in mind, though, that if a configuration file has sensitive information (like a password or authentication token), you should take steps to ensure that this information is not leaked.

Both helm install and helm upgrade provide a --values flag that points to a YAML file with value overrides:

Before moving on to upgrades, though, we will take a quick look at one of the most helpful Helm commands.

Upgrading an Installation
When we talk about upgrading in Helm, we talk about upgrading an installation, not a chart. 



An installation is a particular instance of a chart in your cluster. When you run helm install, it creates the installation. To modify that installation, use helm upgrade.

•  You can upgrade the version of the chart
•  You can upgrade the configuration of the installation


a release is a particular combination of configuration and chart version for an installation.

During an upgrade, then, we can create a release with new configuration, with a new chart version, or with both.

For example, say we install the Drupal chart with the ingress turned off.

 (This will effectively prevent traffic from being routed from outside the cluster into the Drupal instance.)

In this case, we are running an upgrade that will only change the configuration.
In the background, Helm will load the chart, generate all of the Kubernetes objects in that chart, and then see how those differ from the version of the chart that is already installed. It will only send Kubernetes the things that need to change. In other words, Helm will attempt to alter only the bare minimum.

￼ Fetch the latest packages from chart repositories.
￼ Upgrade the mysite release to use the latest version of bitnami/drupal.

What is the result of this pair of operations? The installation will use all of the 


configuration data supplied in values.yaml, but the upgrade will not. As a result, some settings could be changed back to their defaults. This is usually not what you want.

Helm core maintainers suggest that you provide consistent configuration


 with each installation and upgrade. To apply the same configuration to both releases, supply the values on each operation:
$ helm install mysite bitnami/drupal --values values.yaml ￼
$ helm upgrade mysite bitnami/drupal --values values.yaml ￼

One of the reasons we suggest storing configuration in a values.yaml file is so that this pattern is easy to reproduce. Imagine how much more cumbersome these commands would be if you used --set to set three or four configuration parameters! For each release, you’d have to remember exactly which things to set.

helm upgrade mysite bitnami/drupal --reuse-values
The --reuse-values flag will tell Helm to reload the server-side copy of the last set of values, and then use those to generate the upgrade. This method is okay if you are always just reusing the same values. 

Doing so can make troubleshooting complicated and can quickly lead to unmaintainable installations in which nobody is sure how certain configuration parameters were set.

It simply needs the name of the installation. In this section, we will look at how deletion works and take a brief detour into a big change between Helm 2 and Helm 3.

This section briefly describes how installations are tracked and then concludes by explaining how and why Helm changed between version 2 and version 3.


we also create a special record that contains release information. 

By default, Helm stores these records as Kubernetes Secrets (though there are other supported storage backends).

We can see these records with kubectl get secret:


We can see multiple release records at the bottom, one for each revision. As you can see, we have created four revisions of mysite by running install and upgrade operations.

we will see how these extended records can be used to roll back to previous revisions of an installation. 

Then Helm will delete all of those things before returning and deleting the four release records:
$ helm uninstall mysite

The helm list command will no longer show mysite:

We now have no installations. And if we rerun the kubectl get secrets command, we will also see all records of mysite have been purged:

It is possible, though, to delete the application, but keep the release records:
$ helm uninstall --keep-history

In Helm 2, history was retained by default. In Helm 3, the default was changed to deleting history. Different organizations prefer different policies, but core maintainers found that when most people uninstalled, they expected all traces of the installation to be destroyed.






After looking at popular methods of getting Helm installed and configured, we added a chart repository and learned how to search for charts. Then we installed, listed, upgraded, and finally uninstalled the bitnami/drupal chart.

### 3. Beyond the Basics with Helm

keep track of history. 

1.  Load the entire chart, including its dependencies.
2.  Parse the values.
3.  Execute the templates, generating YAML.


4.  Parse the YAML into Kubernetes objects to verify the data.
5.  Send it to Kubernetes.


The API server will run a series of checks on the submitted YAML. If Kubernetes accepts the YAML data, Helm will consider the deployment a success. But if Kubernetes rejects the YAML, Helm will exit with an error.

But right now, we have enough information about workflow to understand two related Helm features: the --dry-run flag and the helm template command.

When






 you supply this flag, it will cause Helm to step through the first four phases (load the chart, determine the values, render the templates, format to YAML). 

The preceding tells us what the name of the installation is, when it was last deployed (in this case, the current date and time), which namespace it would have been deployed into, what phase of the release it is in (pending-install), and the revision number. Since this is an install, the revision is 1. On upgrade, it would be 2 or greater.

The example is truncated for brevity.

The principal purpose of the --dry-run flag is to give people a chance to inspect and debug output before sending it on to Kubernetes. But soon after it was introduced, Helm maintainers noticed a trend among users. People wanted to use --dry-run to use Helm as a template engine, and then use other tools (like kubectl) to send the rendered output to Kubernetes.

To remedy these problems, the Helm maintainers introduced a completely separate command: helm template.

The template command performs the first four phases (load the chart, determine the values, render the templates, format to YAML). But it does this with a few additional caveats:
•  During helm template, Helm never contacts a remote Kubernetes server.



Helm uses that built-in list instead of a list it fetches from the API server. For this reason, Helm does not have access to any CRDs during a helm template run, since CRDs are installed on the cluster and are not included in the Kubernetes libraries.

Running an old version of Helm against a chart that uses new kinds or versions

 can produce an error during helm template because Helm will not have the newest kinds or versions compiled into it.

Because Helm does not contact a Kubernetes cluster during a helm template run, it does not do complete validation of the output. It is possible that Helm will not catch some errors in this case. You may choose to use the --validate flag if you want that behavior, but in this case Helm will need a valid kubeconfig file with credentials for a cluster.

They were:
1.  Load the chart.
2.  Parse the values.
3.  Execute the templates.
4.  Render the YAML.
5.  Send it to Kubernetes.


During that fifth phase, Helm must monitor the state of the release. Moreover, since many individuals may be working on the same copy of that particular application installation, Helm needs to monitor the state in such a way that multiple users can see that information.
Helm provides this feature with release records.

Release Records
When we install a Helm chart (with helm install), the new installation is created in the namespace you specify, or the default namespace. We looked at this in the previous chapter.

At the end of that chapter, we also saw how helm install creates a special type of 




Kubernetes Secret that holds release information. We saw how we could inspect these Secrets with kubectl:

it uses a special type (helm.sh/release.v1) to indicate that it is a Helm secret. Helm automatically generated this secret to track version 1 of our mysite installation (which is a Drupal site).

Each time we upgrade that mysite installation, a new Secret will be created to track each release. In other words, a release record tracks each revision of an installation:


# Output omitted

By default, Helm tracks up to ten revisions of each installation. Once an installation exceeds ten releases, Helm deletes the oldest release records until no more than the maximum remain.


Each release record contains enough information to re-create the Kubernetes objects for that revision (an important thing for helm rollback). It also contains metadata about the release.

In short, this secret is stored inside of Kubernetes so that different users of the same cluster have access to the same release information.

deployed As soon as Kubernetes accepts the manifest from Helm, Helm updates the release record, marking it as deployed.

superseded When an upgrade is run, the last deployed release is updated, marked as superseded, and the newly upgraded release is changed from pending-upgrade to deployed.

pending-rollback If a rollback is created, a new release (e.g., v3) is created, and its status is set to pending-rollback until Kubernetes accepts the release manifest. Then it is marked deployed and the last release is marked superseded.

failed Finally, if during any operation, Kubernetes rejects a manifest submitted by Helm, Helm will mark that release failed.

To show the result of a failure, though, we can run an upgrade command that we know will break:

As the error message indicates, a pull policy cannot be set to NoSuchPolicy. This error came from the Kubernetes API server, which means Helm submitted the manifest, and Kubernetes rejected it. So our release should be in a failed state.

There are five helm get subcommands (hooks, manifests, notes, values, and all). Each subcommand retrieves some portion of the information Helm tracks for a release.


Using helm get notes
The helm get notes subcommand prints the release notes:


$ helm get notes mysite

This output should look familiar, as helm install and helm upgrade both print the release notes at the end of a successful operation. 

Using helm get values
One useful subcommand is values. You can use this to see which values were supplied during the last release. 

We know that revision 2 was successful, but revision 3 failed. So we can take a look at the earlier values to see what changed:
$ helm get values wordpress --revision 2

With this, we can see that one value was removed and one value was added. Features like this are designed to make it easier for Helm users to identify the source of errors.

This command is also useful for learning about the total state of a release’s configuration.

 We can use helm get values to see all of the values currently set for that release. To do this, we use the --all flag:

This inspects the chart itself (not the release) and prints out the documented default values.yaml file. Thus, we could use helm inspect values bitnami/wordpress to see the default configuration for the WordPress chart.

This 





sub-command retrieves the exact YAML manifest that Helm produced using the Chart templates:
$ helm get manifest wordpress

One important detail about this command is that it does not return the current state of all of your resources. It returns the manifest generated from the template.

The output of kubectl contains the record as it currently exists in Kubernetes. There are several fields that have been added since the template output. Some (like the annotations) are managed by Helm itself, and others (like managedFields and creationTimestamp) are managed by Kubernetes.

With helm get, we can closely inspect an individual release. But the next tool we will cover provides us a view of the progression of releases. In the next section, we will look at helm history and helm rollback.







We can investigate the release history of WordPress to see what happened. To do this, we will use helm history:
$ helm history wordpress

it would be nice to simply revert back to the release that worked before.
This is what helm rollback is for:




$ helm rollback wordpress 2

Because Helm has preserved the history, you can still examine the failed release after rolling back to a known-good configuration.

Normally, a deletion event will destroy all release records associated with that installation. But when --keep-history is specified, you can see the history of an installation even after it has been deleted:

Note that the last release is now marked as uninstalled. When history is preserved, you can roll back a deleted installation:


But without the --keep-history flag, this will not work:

However, Helm does let you override this consideration by explicitly stating that you want to create a namespace:

The helm upgrade --install command will install a release if it does not exist already, or will upgrade a release if a release by that name is found. Underneath the hood, it works by querying Kubernetes for a release with the given name. If that release does not exist, it switches out of the upgrade logic and into the install logic.

For example, we can run an install and an upgrade in sequence using exactly the same command:
$ helm upgrade --install wordpress bitnami/

The --wait flag modifies the behavior of the Helm client in a couple of ways. First, when Helm runs an installation, it remains active for a set window of time

 (modifiable with the --timeout flag) 

So Helm with --wait will track such objects, waiting until the pods they create are marked as Running by Kubernetes.

In a normal install or upgrade, Helm marks a release as successful as soon as the 



Kubernetes API server accepts the manifests. This is similar to package managers that consider a package successfully installed as soon as the package contents are written to the correct storage locations.

Thus, installs with --wait can fail for a wide variety of reasons, including

 network latency, a slow scheduler, busy nodes, slow image pulls, and outright failure of a container to start.

Instead of modifying the Deployment (or similar object), it will delete and re-create it. 
This forces Kubernetes to delete the old pods and create new ones. By design, using --force will cause downtime. 

the 
core maintainers do not recommend using --force in CI pipelines that deploy to production.


Another way to modify the behavior of an upgrade is to use the --cleanup-on-fail flag.



 Similarly to --force, this flag instructs Helm to do additional work.
Consider the case where you install a chart that creates one Kubernetes Secret.

The --cleanup-on-fail flag will attempt to fix this situation. On failure, it will request deletion on every object that was newly created during the upgrade. Using it may make it a little harder to debug 

The Helm command-line tool provides many useful commands. While the basic commands were introduced in the previous chapter, this chapter has focused on some of the other useful commands in Helm. Near the end, we also revisited the installation and upgrade commands, getting a taste of some of the more sophisticated features for working with those.

### 4. Building a Chart

The create command creates a chart for you, with all the required chart structure and files. These files are documented to help you understand what is needed, and the templates it provides showcase multiple Kubernetes manifests working together to deploy an application.

To create the new chart, run the following command from a command prompt:
$ helm create anvil
This will create a new chart as a subdirectory of your current directory with the name anvil.

The new chart is a directory containing a number of files and folders. This does not include every file and folder

￼ The Chart.yaml file contains metadata and some functionality controls for the chart.

￼ Dependent charts can optionally be held in the charts directory. Chart dependencies are covered in Chapter 6. For now this will be an empty directory.

￼ Templates used to generate Kubernetes manifests are stored in the templates directory.

Default values passed to the templates when Helm is rendering the manifests are in the values.yaml file. When you instantiate a chart, these values can be overridden.



You can install this newly created chart without any modifications by running the following command:
$ helm install myapp anvil

The output from this command includes content generated by rendering the NOTES.txt template, as shown here:


LoadBalancer A Kubernetes Service configuration option that exposes an application 
externally using a load balancer provided by the hosting provider.
Ingress Ingress resources are additional resources to Services that expose a 
service over HTTP and HTTPS. An Ingress Controller, such as ingress-nginx, is required for this to work.

￼ The apiVersion tells Helm what structure the chart is using. An apiVerison of v2 is designed for Helm v3.




￼ The name is used to identify the chart in various places.
￼ Charts can have many versions. Helm uses the version information to order and identify charts.


.





 Helm leverages the text template package as the foundation for its templates. This template language is similar to other template languages and includes loops, if/then logic, functions, and more. An example template of a YAML file follows:


product: {{ .Values.product | default "rocket" | quote }}

anvil.fullname and anvil.labels are two reusable templates included in the chart via the _helpers.tpl file. (The _ at the start of the name causes it to bubble up to the top of directory listings so you can easily find it among your templates; 

anvil.labels provides labels following Kubernetes best practices

￼ The location and version of the container image is configurable via values.


Conclusion
Creating a simple chart for your application is straightforward when you use the helm create command. Even when your applications are more complicated, the structure of charts is able to accommodate them, and the helm create command can help you.

### 5. Developing Templates

The Go template syntax is similar to those of other systems and has proven to be capable of handing the needs of Helm users.

Logic, control structures, and data evaluations are wrapped by {{ and }}. These are called actions.



 Anything outside of actions is copied to output.

When the curly brackets are used to start and stop actions they can be accompanied by a - to remove leading or trailing whitespace. The following example illustrates this:

 Inside the template that object is represented as a . (i.e., a period). It is referred to as a dot. This object has a wide variety of information available on it.


The properties on .Values do not have a schema and vary from chart to chart.


The information in the Chart.yaml file can also be found on the data 



object at .Chart. This information does follow the schema for the Chart.yaml file. This includes:
.Chart.Name Contains the name of the chart.
.Chart.Version The version of the chart.
.Chart.AppVersion The application version, if set.

You can’t add new fields to pass them in, but you can add your custom information to the annotations.


.Capabilities.KubeVersion.Minor The minor version of Kubernetes being used in the cluster.

Pipelines
A pipeline is a sequence of commands, functions, and variables chained together.

 The value of a variable or the output of a function is used as the input to the next function in a pipeline. The output of the final element of a pipeline is the output of the pipeline. 

There are three parts to this pipeline, each separated by a |

Pipelines are a powerful tool you can use to transform data you want in the template. They can be used for a variety of purposes, from creating powerful transformations to protecting against simple bugs.

A simple fix is to wrap the value in quotes:
id: "12345e2"
When the value is wrapped in quotes, the YAML parsers will interpret it as a string. 


This is a case where using the quote function on the end of a pipeline can fix or avoid a bug.

Unix Pipeline
In Unix and Unix-like systems (e.g., Linux) a pipeline is where the output of one application


 is used as an input in the next application. Applications that each do one thing can be chained together using their inputs and outputs as interfaces.

Two principles from the Unix philosophy include “make each program do one thing well” and “expect the output of every program to become the input to another, as yet unknown, program.”

Sprig Library
Many of the functions found in Helm templates are provided by a library named Sprig.




They were placed into a separate library because they were generic enough that other applications could use them, too.

Instead it is a data object. The toYaml function turns the data into YAML.


To the left of toYaml a - is used with {{ to remove all the whitespace up to the : on the previous line. The output of toYaml is passed to nindent. This function adds a newline at the start of the text it receives and then indents each line.

The indent function does not add a newline at the beginning. nindent is used so that the YAML under securityContext can be on a new line. 

. toYaml is often used when creating Kubernetes manifests, while toJson and toToml are more often used when creating configuration files to be passed to applications through Secrets and ConfigMaps.

Checking for the existence of resources and API groups is useful when dealing with custom resource definitions and multiple versions of Kubernetes resource types. 

Querying Kubernetes Resources In Charts
Helm contains a template function that enables you to look up resources in the Kubernetes cluster.




 The lookup template function is able to return either an individual object or a list of objects.

When a list of resources is returned, you will need to loop over the results


 to access the data on each of the individual objects. Where a lookup for an object returns a dict, a lookup for a list of objects returns a list. These are two different types Helm provides for use in templates.


A dry run does not interact with a cluster, so this function will return no results. When an upgrade is run it will return results.


The results returned when installing or upgrading in various clusters can also be different. For example, in a development environment and in a production environment the resources installed in a cluster will have differences that can lead to unequal responses.

In that chart the values.yaml file contains a section on ingress with an enabled property. It looks like:
ingress:
  enabled: false

When one of the elements to be used with and or or is a function or pipeline, you can use parentheses. 

The method to create a variable with an initial value is different from the method 

used to change the value of an existing variable. When you assign a new value to the existing variable, you use =. For example:
{{ $var := .Values.character }}
{{ $var = "Tweety" }}

The following example illustrates dicts and lists:
# An example list in YAML
characters:
  - Sylvester
  - Tweety
  - Road Runner
  - Wile E. Coyote

# An example map in YAML
products:
  anvil: They ring like a bell
  grease: 50%!s(MISSING)lippery
  boomerang: Guaranteed to return
You can think of a list as an array, while a map, with a key name and value,

 is similar to dictionaries in Python or a HashMap in Java. 



This will work on both lists and dicts. This next example creates the variables that you can use in the block:
products:
{{- range $key, $value := .Values.products }}
  - {{ $key }}: {{ $value | quote }}
{{- end }}

The $key variable contains the key in a map or dict and a number in a list. $value contains the value.

Within Go, lists are represented as slices that are backed by arrays. The 

value is an interface, so it could be a variety of types. For example, if you use the list function to create a list within a template the returned value would be typed as []interface{}. When actions are taken on the value, reflection is used to figure out the type and how to act on that type.

A map or dict is represented a little differently. They are typically 

represented as map[string]interface{}. 

The labels begin with the prefix app.kubernetes.io followed by / as a separator. The Kubernetes documentation for labels notes that a prefix should be used for any labels generated by automation and that those without a prefix are private to the user. These labels are for users, like you, and for various tools.

In the template for the Deployment, the image would be referenced using the new function:
image: "{{ include "anvil.getImage" . }}"
Templates can act like functions in a software program. They are a useful way for you to break off complex logic and have shared functionality.





To aid in creating maintainable templates that are easy to navigate, the Helm maintainers recommend several patterns. These patterns are useful for a few reasons:


Other people will look at the templates in charts. This may be team members who create the chart or those that consume it. Consumers can, and sometimes do, open up a chart to inspect it prior to installing it or as part of a process to fork it.

you use names such as statefulset-primary.yaml and statefulset-replica.yaml.


A second guideline is to put the named templates, which you include in your own 



templates, into a file named _helpers.tpl. Because these are essentially helper templates for your other templates, the name is descriptive. 

### 6. Advanced Chart Features


  - name: rocket
    tags:
      - faster
    version: ^1.0.0
    repository: https://raw.githubusercontent.com/Masterminds/learning-helm/main/
      chapter6/repository/

To illustrate this, we can use the booster chart. If you run the command from the root of the chart, you’ll see an error:
$ helm lint . --set image.pullPolicy=foo


•  The ability to work with collections of charts and only test those that have changed.

The Chart Testing tool is now used by a variety of companies and organizations to aid in the testing of their hosted charts.

### 7. Chart Repositories

•  They have no fine-grained access control; you either have access to all charts in the repo or none of them
•  Chart packages with different names but the exact same raw contents are stored twice


Starting in Helm 3.0.0, experimental support was added to push and pull charts to and from OCI-based container registries.

Enabling OCI Support
At the time of writing, Helm’s OCI support is still considered experimental.


For now, set the following in your environment to enable OCI support:

Storing a Chart in the Cache
Prior to uploading a chart to a registry, you must first save it into the cache.

 This converts a chart from its normal state into content-addressable blobs and also gives it a unique identifier.

ChartMuseum is a simple chart repository web server. Configure


 it to point to a storage location containing chart packages and it will dynamically generate index.yaml. It also exposes an HTTP API for uploading, querying, and deleting chart packages from storage. 

Chart Releaser
Project homepage: https://github.com/helm/chart-releaser
Chart Releaser, or cr, is a command-line tool that leverages GitHub 


releases for hosting chart packages. It has the ability to detect charts in a Git repo, package them, and upload each of them as artifacts to GitHub releases named after the unique chart version.

GCS Plugin
Project homepage: https://github.com/hayorov/helm-gcs
The GCS plugin is a Helm plugin that allows you to use a private Google Cloud Storage bucket as a chart repository.






Project homepage: https://github.com/aslafy-z/helm-git
The Git plugin is a Helm plugin that allows you to use a Git repository containing



 chart source files as a chart repository. It supports subpaths, custom references, and both HTTPS and SSH Git URLs.

### 8. Helm Plugins and Starters

In this chapter we will discuss two ways to further enhance and customize your usage of Helm: plugins and starters.

Helm plugins are external tools that are accessible directly from the Helm CLI. They allow you to add custom subcommands to Helm without making any modifications to Helm’s Go source code. 

jkroepke/helm-secrets Plugin for effectively managing secrets in YAML format





maorfr/helm-backup Plugin to backup/restore Helm releases to/from a text file






To install this plugin, run helm plugin install passing the version control URL as the first argument:
$ helm plugin install https://github.com/salesforce/helm-starter.git
Installed plugin: starter

To attempt to update the plugin, use the helm plugin update command:

$ helm plugin update starter
Updated plugin: starter

Shell Completion
Helm has built-in support for shell autocompletion for both Bash and 



Z shell (Zsh) (see helm completion --help). This is helpful in situations where you cannot remember the name of a subcommand or flag you are attempting to use

### B. Chart Repository API

￼ The repository API version (must always be v1).
￼ A map of unique chart names in the repository to a list of all available versions.