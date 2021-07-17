## Kubernetes Best Practices
> Brendan Burns

### Preface

Kubernetes is the de facto standard for cloud native development. It is a powerful tool that can make your next application easier to develop, faster to deploy, and more reliable to operate. 

unlocking the power of Kubernetes requires using it correctly.

how to deploy specific applications andworkloads on Kubernetes.

When sitting down to write this book, we attempted to capture these experiences so thatmany more people could learn by reading the lessons that we learned from these real-world experiences. 

we can scale our knowledge and allow you to be successfuldeploying and managing your application on Kubernetes on your own.

 We expect people to dive intothe book to learn about a specific topic or interest, and then leave the book alone, only to return when a newtopic comes up.

### 1. Setting Up a Basic Service

This chapter describes the practices for setting up a simple multitier application in Kubernetes. 

It uses the Let’s Encrypt service for managing SecureSockets Layer (SSL) certificates. 

Throughout this chapter, webuild up this application, first using YAML configuration files and then Helm charts.

This is because YAML is somewhat less verbose and more human editable than JSON. 

These days most people store their Kubernetes configurations in Git. 

 to use the folderorganization that comes with the filesystem to organize your components. 

 we lay out the files as follows:

 as webegin to deploy our application to multiple different regions or clusters, this file layout will become morecomplicated.

The other best practices for images relate to naming.

you should treat the version tag as immutable. In particular, some combination ofthe semantic version and the SHA hash of the commit where the image was built is a good practice for namingimages (e.g., v1.0.1-bfeda01f).

, it is a bad idea for production usage because latest is clearly being mutated everytime a new image is built.

Our frontend application is stateless; it relies entirely on the Redis backend for its state. 

 it’sstill a good idea to run with at least two replicas so that you can handle an unexpected crash or roll out a newversion of the application without downtime.

By using aDeployment you can use Kubernetes’ built-in tooling to move from one version of the application to the next.

There are several things to note in this Deployment. First is that we are using Labels to identify the Deploymentas well as the ReplicaSets and the pods that the Deployment creates. 

. Although these comments don’t make itinto the Kubernetes resource stored on the server, just like comments in code, they serve to help guide peoplewho are looking at this configuration for the first time.

 we’ve set Request equal to Limit. 

It is also a best practice to ensure that the contents of your cluster exactly match the contents of your sourcecontrol. The best pattern to ensure this is to adopt a GitOps approach and deploy to production only from aspecific branch of your source control, using Continuous Integration (CI)/Continuous Delivery (CD) automation.

 And the second is an Ingress resource, which provides HTTP(S) loadbalancing with intelligent routing of requests based on HTTP paths and hosts.

Before the Ingress resource can be defined, there needs to be a Kubernetes Service for the Ingress to point to.

Unlike Service resources, Ingress requiresan Ingress controller container to be running in the cluster. 

 If youchoose to install an open source ingress provider, it’s a good idea to use the Helm package manager to installand maintain it. The nginx or haproxy Ingress providers are popular choices:

Typically, separating such configuration information from the application itself is a best practice to follow.There are a couple of different reasons for this separation. The first is that you might want to configure the sameapplication binary with different configurations depending on the setting.

 if you turn on these features via code, the only way to modify the active featuresis to build and release a new binary, which can be an expensive and slow process.

 Features can be rolled out androlled back on a per-feature basis. 

To configure your application, you expose the configuration information as an environment variable in theapplication itself. To do that, you can add the following to the container resource in the Deployment that youdefined earlier:

changing the configuration doesn’t actually trigger anupdate to existing pods. Only when the pod is restarted is the configuration applied. Because of this, the rolloutisn’t health based and can be ad hoc or random.

A better approach is to put a version number in the name of the ConfigMap itself. Instead of calling it frontend-config, call it frontend-config-v1. When you want to make a change, instead of updating the ConfigMap in place,you create a new v2 ConfigMap, and then update the Deployment resource to use that configuration.

, it is essential to prevent mistakes like connecting a development frontend with aproduction database.

More important,you want your developers thinking differently when they are accessing secrets than when they are accessingconfiguration. For these reasons, Kubernetes has a built-in Secret resource for managing secret data.

Secrets in Kubernetes are stored unecrypted by default.

, you still need to ensure that accessvia the Kubernetes API server is properly secured.

A Volume iseffectively a file or directory that can be mounted into a running container at a user-specified location. In thecase of secrets, the Volume is created as a tmpfs RAM-backed filesystem and then mounted into the container.

Configuring Redis to use this password is similar; we mount it into the Redis pod and load the passwordfrom the file.

To prevent this, when runningstateful workloads in Kubernetes its important to use remote PersistentVolumes to manage the state associatedwith the application.

 having a dedicated team provide storage as a service to theentire organization is definitely a better practice than allowing each team to roll its own.

To obtain a PersistentVolume for our Redis, we use a PersistentVolumeClaim. You can think of a claim as a“request for resources.” 

 To do this, create a headless Service. Aheadless Service doesn’t have a cluster IP address; instead, it programs a DNS entry for every pod in theStatefulSet. 

The final component in our application is a static file server. The static file server is responsible for serving HTML,CSS, JavaScript, and image files. It’s both more efficient and more focused for us to separate static file servingfrom our API serving frontend described earlier.

The Deployment resource looks as follows:

It’simportant to note that you must place the / path after the /api path, or else it would subsume /api and direct APIrequests to the static file server. The new Ingress looks like this:

 Even if you are a single developer working on a single application, youlikely want to have at least a development version and a production version of your application so that you caniterate and develop without breaking production users.

Consequently, most people end up with a templating system. A templating system combines templates, whichform the centralized backbone of the application configuration, with parameters that specialize the template to aspecific environment configuration. 

you can have a generally shared configuration, with intentional(and easily understood) customization as needed. 

Putting this all together, you can deploy this chart using the helm tool, as follows:[插图]

 But setting up a basic application for success can bestraightforward if you use the following best practices:

Most services should be deployed as Deployment resources. Deployments create identical replicas forredundancy and scale.

Eventually you will want to parameterize your application to make its configuration more reusable indifferent environments. Packaging tools like Helm are the best choice for this kind of parameterization.

keep this foundational information in mind.

### 2. Developer Workflows

It simplifies deploying and managing applications with anapplication-oriented API, self-healing properties, and useful tools like Deployments for zero downtime rollout ofsoftware.

. Obviously, the ultimate goal is to enable developers to rapidly and easily build applications onKubernetes

The first phase is onboarding. This is when a new developer joins the team. This phase includes giving the user alogin to the cluster as well as getting them oriented to their first deployment.

 You should set a key performance indicator (KPI) goal for thisprocess. 

The second phase is developing. This is the day-to-day activities of the developer. The goal for this phase is toensure rapid iteration and debugging. 

. Second, all tests should automatically run before code is mergedinto the repository. 

As your project becomes more complex, it’s natural for more and more tests to take a longer time. 

 Note that this choice only makessense in an environment in which dynamic cluster creation is easy, such as the public cloud. In physicalenvironments, its possible that one large cluster is the only choice.

If you do have a choice you should consider the pros and cons of each option. If you choose to have adevelopment cluster per user, the significant downside of this approach is that it will be more expensive and lessefficient, and you will have a large number of different development clusters to manage.

 The downside of a shared development cluster is the process of user managementand potential interference between developers. Because the process of adding new users and namespaces to theKubernetes cluster isn’t currently streamlined, you will need to activate a process to onboard new developers.

. Additionally, you will still need toensure that developers don’t leak and forget about resources they’ve created. This is somewhat easier, though,than the approach in which developers each create their own clusters.

 But you will need to invest in a process for onboarding developers, resource management,and garbage collection. Our recommendation would be to try a single large cluster as a first option. As yourorganization grows (or if it is already large), you might consider having a cluster per team or group (10 to 20people) rather than a giant cluster for hundreds of users. This can make both billing and management easier.




When setting up a large cluster, the primary goal is to ensure that multiple users can simultaneously use the cluster without stepping on one another’s toes

 Namespaces can serve as scopes for the deployment of services so that one user’s frontend service doesn’t interfere with another user’s frontend service. Namespaces are also scopes for RBAC, ensuring that one developer cannot accidentally delete another developer’s work. 

The processes for onboarding users and creating and securing a namespace are described in the following sections.


Before you can assign a user to a namespace, you have to onboard that user to the Kubernetes cluster itself. Toachieve this, there are two options. You can use certificate-based authentication to create a new certificate forthe user and give them a kubeconfig file that they can use to log in, or you can configure your cluster to use anexternal identity system (for example, Microsoft Azure Active Directory or AWS Identity and Access Management[IAM]) for cluster access.

In general, using an external identity system is a best practice because it doesn’t require that you maintain twodifferent sources of identity, 

A simple script to do this looks like:

To do this, you can bind a role to a user in the context of that namespace. You do this bycreating a RoleBinding object within the namespace itself. The RoleBinding might look like this:

 Generally, in adevelopment cluster this is OK because everyone is in the same organization and the secrets are used only fordevelopment; however, if this is a concern, then you can create a more fine-grained role that eliminates theability to read secrets.

Now that you have seen how to onboard a new user and how to create a namespace to use as a workspace,

 This is useful because after a user is onboarded, they always have a dedicated workspace inwhich they can develop and manage their applications. 

Developing the tooling to allocate namespaces on demand can seem like a challenge, but simple tooling isrelatively simple to develop. 

You can build this script into a container and use a ScheduledJob to run it at an interval like once per hour.

The first is log aggregation to a central Logging as a Service (LaaS) system. 

Now that we succesfully have a shared cluster setup and we can onboard new application developers to thecluster itself, we need to actually get them developing their application. 

For example, a setup script might look like thefollowing:

After you have built and pushed a container image, the task is to roll it out to the cluster.

As before, you can integrate this with existing programming language tooling so that (for example) a developercan simply run npm run deploy to deploy their new code into the cluster.

 they need to test it and, ifthere are problems, debug any issues with the application. 

In addition to being able to see your cluster state at a glance, the integration allows a developer to use the toolsavailable via kubectl in an intuitive, discoverable way. 

Setting up successful workflows on Kubernetes is key to productivity and happiness. Following these bestpractices will help to ensure that developers are up and running quickly:

Think about developer experience in three phases: onboarding, developing, and testing. Make sure that thedevelopment environment you build supports all three of these phases.

When managing namespaces, think about how you can reap old, unused resources. Developers will havebad hygiene about deleting unused things. Use automation to clean it up for them.

Think about cluster-level services like logs and monitoring that you can set up for all users. Sometimes,cluster-level dependencies like databases are also useful to set up on behalf of all users using templateslike Helm charts.

 Likewise, it pays to invest insome basic tooling specific to user onboarding, namespace provisioning, and cluster services like basic logaggregation. 

### 3. Monitoring and Logging in Kubernetes

Chapter 3. Monitoring and Logging in Kubernetes

In this chapter, we discuss best practices for monitoring and logging in Kubernetes. We’ll dive into the details of different monitoring patterns, important metrics to collect, and building dashboards from these raw metrics.

Metrics Versus Logs



You first need to understand the difference between log collection and metrics collection. They are complementary to each other but serve different purposes.


Metrics A series of numbers measured over a period of time
Logs Used for exploratory analysis of a system


An example of where you would need to use both metrics and logging is when an application is performing poorly. Our first indication of the issue might be an alert of high latency on the pods hosting the application, but the metrics might not give a good indication of the issue. We then can look into our logs to perform an investigation of errors that are being emitted from the application.



Black-box monitoring focuses on monitoring from the outside of an application and is what’s been used traditionally when monitoring systems for components like CPU, memory, storage, and so on.

Black-box monitoring can still be useful for monitoring at the infrastructure level, but it lacks insights and context into how the application is operating. 


White-box monitoring focuses on the details in the context of the application state, such as total HTTP requests, number of 500 errors, latency of requests, and so on. With white-box monitoring, we can begin to understand the “Why” of our system state. It allows us to ask, “Why did the disk fill up?” and not just, “The disk filled up.”

For example, when monitoring a virtual machine (VM) you expect that VM to be up 24/7 and all its state preserved. In Kubernetes, pods can be very dynamic and short-lived, so you need to have monitoring in place that can handle this dynamic and transient nature.


The USE method, popularized by Brendan Gregg, focuses on the following:
•  U—Utilization
•  S—Saturation
•  E—Errors


For every resource, check utilization, saturation, and error rates.” This method lets you quickly identify resource constraints and error rates of your systems.

The RED method approach is focused on the following:
•  R—Rate
•  E—Errors
•  D—Duration

The philosophy was taken from Google’s Four Golden Signals:
•  Latency (how long it takes to serve a request)
•  Traffic (how much demand is placed on your system)
•  Errors (rate of requests that are failing)
•  Saturation (how utilized your service is)


As an example, you could use this method to monitor a frontend service running in Kubernetes to calculate the following:
•  How many requests is my frontend service processing?
•  How many 500 errors are users of the service receiving?
•  Is the service overutilized by requests?


The control-plane components consist of the API Server, etcd, scheduler, and controller manager. 
The worker nodes consist of the kubelet, container runtime, kube-proxy, kube-dns, and pods. You need to monitor all these components to ensure a healthy cluster and application.


so let’s take a look at different components that you can use to collect metrics within your cluster.





Container Advisor, or cAdvisor, is an open source project that collects resources and metrics for containers running on a node. cAdvisor is built into the Kubernetes kubelet, which runs on every node in the cluster. It collects memory and CPU metrics through the Linux control group (cgroup) tree. If you are not familiar with cgroups, it’s a Linux kernel feature that allows isolation of resources for CPU, disk I/O, or network I/O. 

You should consider cAdvisor as the source of truth for all container metrics.





The Kubernetes metrics server and Metrics Server API are a replacement for the deprecated Heapster. 

There are two aspects to understand in the Metrics Server API and metrics server.

The metrics server gathers resource metrics such as CPU and memory. It gathers these metrics from the kubelet’s API and then stores them in memory. 


Kubernetes uses these resource metrics in the scheduler

kube-state-metrics is a Kubernetes add-on that monitors the object stored in Kubernetes. Where cAdvisor andmetrics server are used to provide detailed metrics on resource usage, kube-state-metrics is focused onidentifying conditions on Kubernetes objects deployed to your cluster.

Following are some questions that kube-state-metrics can answer for you:
•  Pods
▪  How many pods are deployed to the cluster?


How many pods are in a pending state?Are there enough resources to serve a pods request?

▪  How many pods are in a running state versus a desired state?
▪  How many replicas are available?
▪  What deployments have been updated?

▪  When did a job start?

▪  When did a job complete?
▪  How many jobs failed?

As of this writing, there are 22 object types that kube-state-metrics tracks. These are always expanding, and you can find the documentation in the Github repository.




 When we think about monitoring in Kubernetes, we wantto take a layered approach that takes into account the following:Physical or virtual nodesCluster componentsCluster add-onsEnd-user applications

It allows you to approach issues with a more targeted approach.

NodesCPU utilizationMemory utilizationNetwork utilizationDisk utilizationCluster componentsetcd latency

ApplicationContainer memory utilization and saturationContainer CPU utilizationContainer network utilization and error rateApplication framework-specific metrics

Following are a few popular tools that integrate with Kubernetes:

Prometheus is an open source systems monitoring and alerting toolkit originally built at SoundCloud. Since its inception in 2012, many companies and organizations have adopted Prometheus, and the project has a very active developer and user community.

InfluxDB is a time-series database designed to handle high write and query loads.

Cloud provider toolsGCP StackdriverStackdriver Kubernetes Engine Monitoring is designed to monitor Google Kubernetes Engine (GKE)clusters. It manages monitoring and logging services together and features an interface that provides adashboard customized for GKE clusters. 

 It collects metrics, events, and metadata fromGoogle Cloud Platform (GCP), Amazon Web Services (AWS), hosted uptime probes, and applicationinstrumentation.

Always evaluate monitoring tools you already have, because taking on a new monitoring tool has a learning curve and a cost due to the operational implementation of the tool. Many of the monitoring tools now have integration into Kubernetes, so evaluate which ones you have today and whether they will meet your requirements.


In this section we focus on monitoring metrics with Prometheus, which provides good integrations withKubernetes labeling, service discovery, and metadata. The high-level concepts we implement throughout thechapter will also apply to other monitoring systems.

To collect metrics, Prometheus uses a pull model in which it scrapes a metrics endpoint to collect and ingest the metrics into the Prometheus server. 

Systems like Kubernetes already expose their metrics in a Prometheus format, making it simple to collect metrics. Many other Kubernetes ecosystem projects (NGINX, Traefik, Istio, LinkerD, etc.) also expose their metrics in a Prometheus format. Prometheus also can use exporters, which allow you to take emitted metrics from your service and translate them to Prometheus-formatted metrics.

You can install Prometheus within the cluster or outside the cluster. It’s a good practice to monitor your cluster from a “utility cluster” to avoid a production issue also affecting your monitoring system. There are tools like Thanos that provide high availability for Prometheus and allow you to export metrics into an external storage system.

you should refer to another one of the dedicated books on this topic. Prometheus: Up & Running (O’Reilly) is a good in-depth book to get you started.

Prometheus Operator Makes the Prometheus configuration Kubernetes native, and manages and operates Prometheus and Alertmanager clusters. Allows you to create, destroy, and configure Prometheus resources through native Kubernetes resource definitions.


Node Exporter Exports host metrics from Kubernetes nodes in the cluster.
kube-state-metrics Collects Kubernetes-specific metrics.
Alertmanager Allows you to configure and forward alerts to external systems.
Grafana Provides visualization on dashboard capabilities for Prometheus.


helm install --name prom stable/prometheus-operator
After you’ve installed the Operator, you should see the following pods deployed to your cluster:

Now that we have Prometheus deployed, let’s explore some Kubernetes metrics through the Prometheus PromQL query language. There is a PromQL Basics guide available.

We talked earlier in the chapter about employing the USE method, so let’s gather some node metrics on CPU utilization and saturation.


This will return the average CPU utilization for the entire cluster.
If we want to get the CPU utilization per node, we can write a query like the following:

This returns average CPU utilization for each node in the cluster.

The great thing about the Prometheus Operator you installed is that it comes with some prebuilt Grafana dashboards that you can use.


This dashboard gives you a good overview of the utilization and saturation of the Kubernetes cluster, which is at the heart of the USE method. 

Go ahead and take some time to explore the different dashboards and metrics that you can visualize in Grafana.

Logging Overview

Up 
to this point, we have discussed a lot about metrics and Kubernetes, but to get the full picture of your environment, you also need to collect and centralize logs from the Kubernetes cluster and the applications deployed to your cluster.


With logging, it might be easy to say, “Let’s just log everything,” but this can cause two issues:
•  There is too much noise to find issues quickly.
•  Logs can consume a lot of resources and come with a high cost.

 you require longer-term storage for compliance reasons, you’ll want to archive the logs to more cost-effective resources.

With node logs, you want to collect events that happen to essential node services. For example, you will want to collect logs from the Docker daemon running on the worker nodes. A healthy Docker daemon is essential for running containers on the worker node. Collecting these logs will help you diagnose any issues that you might run into with the Docker daemon, and it will give you information into any underlying issues with the daemon. 

you’ll want to aggregate the logs that it stores on the host in /var/log/kube-APIserver.log, /var/log/kube-scheduler.log, and /var/log/kube-controller-manager.log. The controller manager is responsible for creating objects defined by the end user.

as a user you create a Kubernetes service with type LoadBalancer and it just sits in a pending state; the Kubernetes events might not give all the details to diagnose the issue. If you collect the logs in a centralized system, it will give you more detail into the underlying issue and a quicker way to investigate the issue.

You can think of Kubernetes audit logs as security monitoring because they give you insight into who did what within the system. These logs can be very noisy, so you’ll want to tune them for your environment. In many instances these logs can cause a huge spike in your logging system when first initialized, so make sure that you follow the Kubernetes documentation guidance on audit log monitoring.

Application container logs give you insight into the actual logs your application is emitting. You can forward these logs to a central repository in multiple ways. The first and recommended way is to send all application logs to STDOUT because this gives you a uniform way of application logging, and a monitoring daemon set can gather the logs directly from the Docker daemon. 


There are many options and configurations for managing Kubernetes audit logs. These audit logs can be very noisy and it can be expensive to log all actions. You should consider looking at the audit logging documentation, so that you can fine-tune these logs for your environment.


When looking for a tool to centralize logs, hosted solutions can provide a lot of value because they offload a lot of the operational cost. Hosting your own logging solution seems great on day N, but as the environment grows, it can be very time consuming to maintain the solution.


Logging by Using an EFK Stack


For the purposes of this book, we use an Elasticsearch, Fluentd, and Kibana (EFK) stack to set up monitoring for our cluster. Implementing an EFK stack can be a good way to get started, but at some point you’ll probably ask yourself, “Is it really worth managing my own logging platform?” Typically it’s not worth the effort because self-hosted logging solutions are great on day one, but they become overly complex by day 365. 

Self-hosted logging solutions become more operationally complex as your environment scales. There is no one correct answer, so evaluate whether your business requirements need you to host your own solution. 

You will deploy the following for your monitoring stack:
•  Elasticsearch Operator
•  Fluentd (forwards logs from our Kubernetes environment into Elasticsearch)
•  Kibana (visualization tool to search, view, and interact with logs stored in Elasticsearch)

You should see the following pods deployed to your cluster:
kubectl get pods -n logging

After all pods are “Running,” let’s go ahead and connect to Kibana through port forwarding to our localhost:

Now point your web browser at http://localhost:5601 to open the Kibana dashboard.
To interact with the logs forwarded from our Kubernetes cluster, you first need to create an index.

The first time you start Kibana, you will need to navigate to the Management tab, and create an index pattern for Kubernetes logs. The system will guide you through the required steps.
After you create an index, you can search through logs using a Lucene query syntax, such as the following:

This returns all logs containing the fields warn, info, error, or fatal. You can see an example in Figure 3-4.

Alerting on too much causes alert fatigue, and important events will be lost in all the noise.

Well, the beauty of Kubernetes is that it provides features to automatically check the health of a container and restart the container automatically. 

You really want to focus alerting on events that affect your Service-Level Objectives (SLOs). SLOs are specific measurable characteristics such as availability, throughput, frequency, and response time that you agree upon with the end user of your service.

You need to decide what alerts are good and require intervention. In typical monitoring, you might be accustomed to alerting on high CPU usage, memory usage, or processes not responding. 

An alert to an on-call engineer should be an issue that needs immediate human attention and is affecting the UX of the application. If you have ever experienced a “That issue resolved itself” scenario, then that is a good indication that the alert did not need to contact an on-call engineer.

For example, when a disk fills up, you could automate the deletion of logs to free up space on the disk. 
Also, utilizing Kubernetes liveness probes in your app deployment can help autoremediate issues with a process that is not responding in the application.


When building notifications for alerts you want to ensure that you provide relevant information in the notification, for example, providing a link to a “playbook” that gives troubleshooting or other helpful information on resolving the issue.

When thinking about “Who do I notify when an alert is triggered?” you should ensure that notifications are not just sent to a distribution list or team emails.

You should route notifications to the user who is going to take responsibility for the issue.


With alerting, you’ll never get it perfect on day one, and we could argue it might never be perfect. 

Following are the best practices that you should adopt regarding monitoring, logging, and alerting.

  
Monitor nodes and all Kubernetes components for utilization, saturation, and error rates, and monitor applications for rate, errors, and duration.

  
You should use logging in combination with metrics monitoring to get the full picture of how your environment is operating.
•  Be cautious of storing logs for more than 30 to 45 days and, if needed, use cheaper resources for long-term archiving.

•  Alert for symptoms that affect your SLO and customers and not for transient issues that don’t need immediate human attention.


In this chapter we discussed the patterns, techniques, and tools that can be used for monitoring our systems with metric and log collection. The most important piece to take away from this chapter is that you need to rethink how you perform monitoring and do it from the outset.

Monitoring distributed applications and distributed systems like Kubernetes requires a lot of work, so you must be ready for it at the beginning of your journey.

### 4. Configuration, Secrets, and RBAC

 As a developer, it is important to take into consideration the dynamic nature of this behavior andallow for the use of environment variables or the reading of configuration data from a specific path available tothe application runtime user.

When moving sensitive data such as secrets into a native Kubernetes API object, it is important to understandhow Kubernetes secures access to the API. The most commonly implemented security method in use inKubernetes is Role-Based Access Control (RBAC) to implement a fine-grained permission structure aroundactions that can be taken against the API by specific users or groups. 

Kubernetes allows you to natively provide configuration information to our applications through ConfigMaps orsecret resources. 

how the data is stored in the etcd data store.

 Containers allow thedeveloper to decouple this configuration information from the application, which allows for true applicationportability. 

The ConfigMaps not only provide configuration information for pods, but can also provide information to beconsumed for more complex system services such as controllers, CRDs, operators, and so on. 

 If your applicationrequires more sensitive data, the Secrets API is more appropriate.

For your application to use the ConfigMap data, it can be injected as either a volume mounted into the pod or asenvironment variables.

Secret data is meant to be small amounts of data, limited by default in Kubernetes to 1 MB in size, for thebase64-encoded data, so ensure that the actual data is approximately 750 KB because of the overhead of theencoding. There are three types of secrets in Kubernetes:

genericThis is typically just regular key/value pairs that are created from a file, a directory, or from string literalsusing the --from-literal= parameter, as follows:

docker-registryThis is used by the kubelet when passed in a pod template if there is an imagePullsecret to provide thecredentials needed to authenticate to a private Docker registry:

Secrets are also mounted into tmpfs only on the nodes that have a pod that requires the secret and are deletedwhen the pod that needs it is gone. 

 More recent versions of Kubernetes use etcd3 and have the ability to enableetcd native encryption; however, this is a manual process that must be configured in the API server configurationby specifying a provider and the proper key media to properly encrypt secret data held in etcd. 

To support dynamic changes to your application without having to redeploy new versions of the pods,mount your ConfigMaps/Secrets as a volume and configure your application with a file watcher to detectthe changed file data and reconfigure itself as needed.

 Second, avoid mounting ConfigMaps/Secretsusing the volumeMounts.subPath property. This will prevent the data from being dynamically updated in the volumeif you update a ConfigMap/Secret with new data.

ConfigMap/Secrets allows an entire block of raw data by using the | symbol, as demonstrated here:

If you’re using the configMapKeyRef or secretKeyRef method, be aware that if the actual key does not exist,this will prevent the pod from starting.

updates to the data in the ConfigMap/Secret will not update in the pod and will require a pod restarteither through deleting the pods and letting the ReplicaSet controller create a new pod, or triggering aDeployment update, which will follow the proper application update strategy as declared in theDeployment specification.

It is easier to assume that all changes to a ConfigMap/Secret require an update to the entire deployment;this ensures that even if you’re using environment variables or volumes, the code will take the newconfiguration data. 

Use CI/CD capabilities to get secrets from a secure vault or encrypted store with a Hardware SecurityModule (HSM) during the release pipeline. This allows for separation of duties. Security managementteams can create and encrypt the secrets, and developers just need to reference the names of the secretexpected. 

 The subject is usually auser, a service account, or a group. 

Service accounts in Kubernetes are different than user accounts in that they are namespace bound, internallystored in Kubernetes; they are meant to represent processes, not people, and are managed by native Kubernetescontrollers.

Roles allow the definition of scope of the rules defined. Kubernetes has two types of roles, role and clusterRole,the difference being that role is specific to a namespace, and clusterRole is a cluster-wide role across allnamespaces. 

The RoleBinding allows a mapping of a subject like a user or group to a specific role. Bindings also have twomodes: roleBinding, which is specific to a namespace, and clusterRoleBinding, which is across the entire cluster.Here’s an example RoleBinding with namespace scope:

### 5. Continuous Integration, Testing, and Deployment

Building a well-integrated pipeline will enable you todeliver applications to production with confidence, so here we look at the methods, tools, and processes toenable CI/CD in your environment.

it can be very error prone.

Manually managing application updates inKubernetes leads to configuration drift and fragile deployment updates, and overall agility delivering anapplication is lost.

We also go through an example CI/CD pipeline, which consists of the following tasks:Pushing code changes to the Git repository

Running a test against a deployed applicationPerforming rolling upgrades on Deployments

There are many ways to set up a branching strategy, and the setup will be very dependent on the organizationstructure and separation of duties. 

Having both application developers and operation engineers collaborate in a single repository builds confidence ina team to deliver an application to production.

CI is the process of integrating code changes continuously into a version-control repository. Instead ofcommitting large changes less often, you commit smaller changes more often. Each time a code change iscommitted to the repository, a build is kicked off. 

You’ll want to ensure that if tests fail in the pipeline,the build fails after the test suite runs. You don’t want to build the container image and push it to a registry if youhave failing tests against your code base.

Helm includes a tool called helm lint, which runs a series of tests against a chart to examine anypotential issues with the chart provided. 

When building your images, you should optimize the size of the image. Having a smaller image decreases the timeit takes to pull and deploy the image, and also increases the security of the image. There are multiple ways ofoptimizing the image size, but some do have trade-offs. The following strategies will help you build the smallestimage possible for your application:

Multistage buildsThese allow you to remove the dependencies not needed for your applications to run. 

 You might think this is great, but it can be a pain to debug an application. Distrolessimages contain no package manager, shell, or other typical OS packages, so you might not have access to thedebugging tools you are accustomed to with a typical OS.

 like Debian Slim. 

It’s important to have an image tagging strategy so that you can easily identify the versioned imagesyou have deployed to your environments.

Using that as an image tag is not a version and will lead to not having the abilityto identify what code change belongs to the rolled-out image. Every image that is built in the CI pipeline shouldhave a unique tag for the built image.

When a CI build kicks off, it has a buildID associated with it. Using this part of the tag allows you to referencewhich build assembled the image.

On new code commits, a Git hash is generated, and using the hash for the tag allows you to easily referencewhich commit generated the image.

CD is the process by which changes that have passed successfully through the CI pipeline are deployed toproduction without human intervention. 

One thing to keep in mind is that you need to have a solid CI pipeline set up before focusing on CD. 

 let’s take a look at the different rollout strategies that you can use.Kubernetes provides multiple strategies to roll out new versions of your application. And even though it has abuilt-in mechanism to provide rolling updates, you can also utilize some more advanced strategies. 

Rolling updatesBlue/green deploymentsCanary deployments

Rolling updates are built into Kubernetes and allow you to trigger an update to the currently running applicationwithout downtime.

A Deployment object also lets you configure the maximum amount of replicas to be updated and the maximumunavailable pods during the rollout. The following manifest is an example of how you specify the rolling updatestrategy:

whereas the preStop hook can ensure that connections aredrained on the current deployed application. The life cycle hook is called before the container exits and issynchronous, so it must complete before the final termination signal is given.

The preStop life cycle hook in this example will gracefully exit NGINX, whereas a SIGTERM conducts anongraceful, quick exit.

. There are somethings that you need to consider with this deployment strategy, however:Database migrations can become difficult with this deployment option because you need to consider in-flight transactions and schema update compatibility.

There is the risk of accidental deletion of both environments.

Canary deployments are very similar to blue/green deployments, but they give you much more control overshifting traffic to the new release. Most modern ingress implementations will give you the ability to release apercentage of traffic to a new release, 

The ability to shift traffic to a percentage of usersA firm knowledge of steady state to compare against a new release

Metrics to understand whether the new release is in a “good” or “bad” state

Canary releases also suffer from having multiple versions of the application running at the same time. Yourdatabase schema needs to support both versions of the application. 

You also need a high degree of automation in place to beable to automatically recover from failures that you inject into your systems.

By this point, you might be asking, “Why wouldn’t I just test in staging?” We find there are some inherentproblems when testing in staging, such as the following:

Lack of monitoring implemented in staging.The data services deployed contain differing data and load than in production.

The first step in the process is to get a GitHub repository forked so that you can have your own repository to usethrough the chapter. You will need to use the GitHub interface to fork the repository.

You can retrieve your API endpoint by using the following command:

Now let’s create a service account that Drone will use to connect to the cluster. Use the following command tocreate the serviceaccount:

Now use the following command to create a clusterrolebinding for the serviceaccount:

Next, retrieve your serviceaccount token:

When the build completes, you’ll want to test it, so we include a test step, which will run npm against the newlybuilt app:

For the deployment step in your pipeline, you will push your application to your Kubernetes cluster. You will usethe deployment manifest that is under the frontend app folder in your repository:

You can also add a test step that will retrieve the status of the deployment by adding the following step in yourDrone pipeline:

Let’s demonstrate a rolling upgrade by changing a line in the frontend code. In the server.js file, change thefollowing line and then commit the change:

You will see the deployment rolling out and rolling updates happening to the existing pods. After the rollingupdate finishes, you’ll have the new version of the application deployed.

Hosted chaos service that provides advanced features for running chaos experiments

KubeMonkeyOpen source tool that provides basic resiliency testing for pods in your clusterLet’s set up a quick chaos experiment to test the resiliency of your application by automatically terminating pods.For this experiment, we’ll use Chaos Toolkit:

Your CI/CD pipeline won’t be perfect on day one, but consider some of the following best practices to iterativelyimprove on the pipeline:

With CI, focus on automation and providing quick builds. Optimizing the build speed will providedevelopers quick feedback if their changes have broken the build.

Avoid using “latest” as an image tag, and utilize a tag that can be referenced back to the buildID or Gitcommit.

Testing in production will help you build reliability into your application, and ensure that you have goodmonitoring in place. With testing in production, also start at a small scale and limit the blast radius of theexperiment.

### 6. Versioning, Releases, and Rollouts

. Being able to quickly iterate on new code, solve new problems, or fix hidden problems before theybecome major issues, as well as the promise of zero-downtime upgrades,

. As the system grew, more brittlenesswas introduced, and systems engineers began to build complex automation processes to deliver on complexrelease, upgrade, and failure detection mechanisms. 

The main thing is to pick a pattern and stick with it.

For those new to semantic versioning, the basics are that it follows a three-part version number in a pattern ofmajor version, minor version, and patch, usually expressed in a dot notation such as 1(major).2(minor).3(patch).

If a rolling update is specified, the deployment will create a new ReplicaSet to scale to the number of requiredreplicas, and the old ReplicaSet will scale down to zero based on specific values for maxUnavailble and maxSurge.

In essence, those two values will prevent Kubernetes from removing older pods until a sufficient number of newerpods have come online, and will not create new pods until a certain number of old pods have been removed. Thenice thing is that the Deployment controller will keep a history of the updates, and through the CLI, you can rollback deployments to previous versions.

The recreate strategy is a valid strategy for certain workloads that can handle a complete outage of the pods in aReplicaSet with little to no degradation of service. 

Within a single service deployment, a few key areas are affected by versioning, release, and rollout management.

Effective CI/CD and the ability to offer reduced or zero downtime deployments are both dependent on using consistent practices for versioning and release management. 

### 7. Worldwide Application Distribution and Staging

There are many different reasons why an application might need to scale to a global deployment. 

Examples of such applications include a worldwide API gateway for a public cloud provider, a large-scale IoT product with a worldwide footprint, a highly successful social network, and more.

Although there are relatively few of us who will build out systems that require worldwide scale, many more applications require a worldwide footprint for latency.

it is sometimes necessary to distribute our applications around the world to minimize the distance to our users.


The first thing to consider is whether your image registry has automatic geo-replication. Many image registries provided by cloud providers will automatically distribute your image around the world and resolve a request for that image to the storage location nearest to the cluster from which you are pulling the image.

Another concern about a single registry is that it can be a single point of failure. 

The first thing to consider is how to organize your different configurations on disk. A common way to achieve this is by using a different directory for each global region. 

Load-Balancing Traffic Around the World


Now that your application is running around the world, the next step is to determine how to direct traffic to the application. 

Though testing catches many problems, in the real world, application problems are often first noticed when they are rolled out to production traffic. 

The first is complete integration testing. This means that you assemble the entirety of your stack into a full-scale deployment of your application but without any real-world traffic.

Integration testing will validate the correct operation of your application, but you should also load-test the application.

Assuming that you have crafted a load test, the next question is the metrics to watch when load-testing your application. The obvious ones are requests per second and request latency because those are clearly the user-facing metrics.


but if 10%!o(MISSING)f your users are having a bad time, it can have a significant impact on the success of your product.


In addition, it’s worth looking at the resource usage (CPU, memory, network, disk) of the application under load test. Though these metrics do not directly contribute to the UX, large changes in resource usage for your application should be identified and understood in preproduction testing. 

Because the goal of a canary is to get early feedback on a release, it is a good idea to leave the release in the canary region for a few days. 

Worldwide Rollout Best Practices
•  
Distribute each image around the world. A successful rollout depends on the release bits (binaries, images, etc.) being nearby to where they will be used.

Document and practice your response to any problem or process (e.g., a rollback) that you might encounter. Trying to remember what to do in the heat of the moment is a recipe for forgetting something and making a bad problem worse.

### 8. Resource Management

Chapter 8. Resource Management

In this chapter, we focus on the best practices for managing and optimizing Kubernetes resources. We discuss workload scheduling, cluster management, pod resource management, namespace management, and scaling applications.

We show you how to implement resource limits, resource requests, pod Quality of Service, PodDisruptionBudgets, LimitRangers, and anti-affinity policies.

The Kubernetes scheduler is one of the main components that is hosted in the control plane. 

An example would be when a pod requests 4 GB of memory and a node cannot satisfy this requirement. The node would return a false value and would be removed from viable nodes for the pod to be scheduled to

Another example would be if the node is set to unschedulable; it would then be removed from the scheduling decision.

For example, you might want to schedule pods across availability zones to mitigate a zonal failure causing downtime to your application. You might also want to colocate pods to a specific host for performance benefits.

These rules allow you to modify the scheduling behavior and override the scheduler’s placement decisions.

This ensures that if a node fails, there are still enough replicas of NGINX to serve data from its cache.

There are multiple taint types that affect scheduling and running containers:
NoSchedule A hard taint that prevents scheduling on the node
PreferNoSchedule Schedules only if pods cannot be scheduled on other nodes
NoExecute Evicts already-running pods on the node


When a pod cannot be scheduled due to tainted nodes, you’ll see an error message like the following:


For example, if a node becomes unhealthy due to a bad disk drive, the taint-based eviction can reschedule the pods on the host to another healthy node in the

 cluster

.




Managing pod resources consists of managing CPU and memory to optimize the overall utilization of your Kubernetes cluster. 

A Kubernetes resource request defines that a container requires X amount of CPU or memory to be scheduled.

If the pod is not able to be scheduled, it will go into a pending state until the required resources are available.


So let’s take a look at how this works in our cluster.

To determine the available free resource in your cluster, use kubectl top:
kubectl top nodes
The output should look like this (the memory size might be different for your cluster):

Notice that the pod will stay pending, and if you look at the events on the pods, you’ll see that no nodes are avalaible to schedule the pods:
kubectl describe pods memory-request
The output of the event should look like this:

When you specify limits for CPU and memory, each takes a different action when it reaches the specified limit. With CPU limits, the container is throttled from using more than its specified limit. With memory limits, the pod is restarted if it reaches its limit. The pod might be restarted on the same host or a different host within the cluster.


When a pod is created, it’s assigned one of the following Quality of Service (QoS) classes:
•  Guaranteed
•  Burstable
•  Best effort

A pod is assigned best effort when no request or limits are set for the containers in the pod.

Involuntary disruptions can be caused by hardware failure, network partitions, kernel panics, or a node being out of resources.

To minimize the impact to your application, you can set a PodDisruptionBudget to ensure uptime of the application when pods need to be evicted.

An example of a voluntary eviction would be when draining a node to perform maintenance on the node.

Namespaces in Kubernetes give you a nice logical separation of resources deployed to a cluster. This allows you to set resource quotas per namespace, Role-Based Access Control (RBAC) per namespace

When designing how you want to configure a namespace, you should think about how you want to control access to a specific set of applications. 

After deploying a Kubernetes cluster, you’ll see the following namespaces in your cluster:
kube-system Kubernetes internal components are deployed here, such as coredns, kube-proxy, and metrics-server.

default This is the default namespace that is used when you don’t specify a namespace in the resource object.

You’ll want to avoid using the default namespace because it can make it really easy to make mistakes when managing resources within your cluster.

When working with namespaces, you need to use the –namespace flag, or -n for short, when working with kubectl:
kubectl create ns team-1

When dealing with multiple namespaces and clusters, it can be a pain to set different namespaces and cluster context. We’ve found that using kubens and kubectx can help make it easy to switch between these different namespaces and contexts.

As you can see from this list, Kubernetes gives you fine-grained control over how you carve up resource quotas per namespace. 

Let’s see how these quotas actually work by setting up a quota on a namespace. Apply the following YAML file to the team-1 namespace:


Now let’s try to deploy an application to see how the resource quotas affect the deployment:

This deployment will fail with the following error due to the memory quota exceeding 2Gi of memory:

As this example demonstrates, setting resource quotas can let you deny deployment of resources based on policies you set for the namespace.





You should see the following requests and limits set on the pod specification:

But perhaps you don’t want to manually do this and want it to autoscale. There are things that you need to take into consideration with cluster autoscaling, and we have found that most users are better off starting with just manually scaling their nodes proactively when resources are needed. If your workloads are highly variable, cluster autoscaling can be very useful.

After the pod is pending, the Cluster Autoscaler will add a node to the cluster. As soon as the new node is added to the cluster, the pending pod is scheduled to the node.

The Cluster Autoscaler can also reduce the size of the cluster after resources are no longer needed. When the resources are no longer needed, it will drain the node and reschedule the pods to new nodes in the cluster. You’ll want to use a PodDisruptionBudget to ensure that you don’t negatively affect your application when it performs its drain operation to remove the node from the cluster.


Happily, Kubernetes also provides a Horizontal Pod Autoscaler (HPA) to automatically scale workloads for you.
Let’s first take a look at how you can manually scale a deployment by applying the following Deployment manifest:


The HPA has the following default setting for sync metrics, upscaling, and downscaling replicas:
horizontal-pod-autoscaler-sync-period Default of 30 seconds for syncing metrics


The VPA frees you from manually adjusting these requests, and automatically scales up and scales down pod requests for you. For workloads that can’t scale out due to their architecture, this works well for automatically scaling the resources.

Recommender Monitors the current and past resource consumption, and provides recommended values for the container’s CPU and memory requests

•  
Utilize pod anti-affinity to spread workloads across multiple availability zones to ensure high availability for your application.


•  Ensure that you set memory and CPU limits for all pods deployed to your cluster

we discussed how you can optimally manage Kubernetes and application resources. Kubernetes provides many built-in features to manage resources that you can use to maintain a reliable, highly utilized, and efficient cluster. Cluster and pod sizing can be difficult at first, but through monitoring your applications in production you can discover ways to optimize your resources.


### 9. Networking, Network Security, and Service Mesh

Kubernetes is effectively a manager of distributed systems across a cluster of connected systems. 

Understanding how Kubernetes facilitates communication among the distributed services it manages is important for the effective application of interservice communication.

Container-to-container communication in the same pod All containers in the same pod share the same network space. This effectively allows localhost communication between the containers. It also means that containers in the same pod need to expose different ports.

This also extends to the node being able to communicate directly to the pod with no NAT involved.

Service-to-pod communication Services in Kubernetes represent a durable IP address and port that is found on each node that will forward all traffic to the endpoints that are mapped to the service. Over the different iterations of Kubernetes, the method in favor of enabling this has changed, but the two main methods are via the use of iptables or the newer IP Virtual Server (IPVS). Most implementations today use the iptables implementation to enable a pseudo-Layer 4 load balancer on each node.
 

The second type of plug-in follows the Container Network Interface (CNI) specification, which is a generic plug-in network solution for containers.

Some CNI plug-ins give the pods IPs on the same network space as the nodes, so technically, after the IP of a pod is known, it can be accessed directly from outside the cluster. 

The controller that manages this is called the kube-proxy service, which actually runs on each node in the cluster. 

When a service object is defined, the type of service needs to be defined. The service type will dictate whether the endpoints are exposed only within the cluster or outside of the cluster. There are four basic service types that we will discuss briefly in the following sections.

This IP is as long lasting as the service object, so it provides an IP and port and protocol mapping to backend pods using the selector field;

As an example, if you have the service definition shown in the following example and need to access that service from another pod inside the cluster via an HTTP call, the call can simply use http://web1-svc if the client is in the same namespace as the service:


If it is required to find services in other namespaces, the DNS pattern would be <service_name>.<namespace_name>.svc.cluster.local.

If no selector is given in a service definition, the endpoints can be explicitly defined for the service by using an endpoint API definition. This will basically add an IP and port as a specific endpoint to a service instead of relying on the selector attribute to automatically update the endpoints from the pods that are in scope by the selector match.

This is sometimes called a headless service because it is not managed by kube-proxy as other services are, but you can directly manage the endpoints, as shown in Figure 9-4.

To directly access the service from outside the cluster, use NodeIP:NodePort, as depicted in Figure 9-5.


The ExternalName service type is seldom used in practice, but it can be helpful for passing cluster-durable DNS names to external DNS named services. A common example is an external database service from a cloud provider that has a unique DNS provided by the cloud provider, such as mymongodb.documents.azure.com. 

however, it might be more advantageous to use a more generic name in the cluster, such as prod-mongodb, which enables the change of the actual database it points to by just changing the service specification instead of having to recycle the pods because the Environment variable has changed:


Service Type LoadBalancer



LoadBalancer is a very special service type because it enables automation with cloud providers and other programmable cloud infrastructure services. 

The Ingress API is basically an HTTP-level router that allows for host- and path-based rules to direct to specific backend services. 

An additional feature is the ability to declare a Kubernetes secret that holds the certificate information for 
Transport Layer Security (TLS) termination on port 443. When a path is not specified, there is usually a default backend that can be used to give a better user experience than the standard 404 error.


The details around the specific TLS and default backend configuration are actually handled by what is known as the Ingress controller. The Ingress controller is decoupled from the Ingress API and allows for operators to deploy an Ingress controller of choice, such as NGINX, Traefik, HAProxy, and others.

The most common implementation of an Ingress controller is NGINX because it is partly maintained by the Kubernetes project; however, there are numerous examples of both open source and commercial Ingress controllers:

it is best to use an Ingress API and Ingress controller to route traffic to backing services with TLS termination. Depending on the type of Ingress controller used, features such as rate limiting, header rewrites, OAuth authentication, observability, and other services can be made available without having to build them into the applications themselves.

If you want to dig deeper into the NetworkPolicy specification, it might sound confusing, especially given that it is defined as a Kubernetes API, but it requires a network plug-in that supports the NetworkPolicy API.

Each policy specification has podSelector, ingress, egress, and policyType fields. The only required field is podSelector, which follows the same convention as any Kubernetes selector with a matchLabels.

•  Observability of the traffic and services, including tracing across the distributed services using tracing systems like Jaeger or Zipkin that follow the OpenTracing standards.



In many cases, the first service mesh that comes to mind is Istio, a project by Google, Lyft, and IBM that uses Envoy as its data-plane proxy 

•  Rate the importance of the key features service meshes offer and determine which current offerings provide the most important features with the least amount of overhead. Overhead here is both human technical debt and infrastructure resource debt. 

we looked at the details of how Kubernetes works, including how pods get their IP addresses through CNI plug-ins, how those IPs are grouped together to form services, and how more application or Layer 7 routing can be implemented via Ingress resources (which in turn use services).

In addition to setting up your application to run and be deployed reliably, setting up the networking for your application is a crucial piece of using Kubernetes successfully.

### 10. Pod and Container Security

The PodSecurityPolicy API is under active development. 

Please visit the upstream documentation for the latest updates on the feature state.

There are two main components that you need to complete in order to start using PodSecurityPolicy:

It’s extremely important to remember that having no PodSecurityPolicies defined will result in an implicit deny. This means that without a policy match for the workload, the pod will not be created.


Let’s first test the experience without making any changes or creating any policies. The following is a test workload that simply runs the trusty pause container in a Deployment 

If you describe the ReplicaSet, you can confirm the cause from the event log:

Now that we have defined these policies, we need to grant the service accounts access to use these policies via Role-Based Access Control (RBAC).


You can see that the pod is now up and running:

Privileged containers are not allowed. Let’s delete the test workload deployment.

Another common place is Helm charts: do they ship with the appropriate policies in place?

PodSecurityPolicy is complex and can be error prone. Refer to the following best practices before implementing PodSecurityPolicy on your clusters:


Your policies can be cluster-wide, namespaced, or workload-specific in scope. 

If a cluster administrator has set up different RuntimeClasses, you can use them simply by specifying runtimeClassName in the pod specification; for example:


Other Pod and Container Security Considerations
In addition to PodSecurityPolicy and workload isolation, here are some other tools you may consider when determining how to handle pod and container security.

### 11. Policy and Governance for Your Cluster

Rather than implementing policy at runtime, when we talk about policy in the context of governance, what we mean is defining policy that controls the fields and values in the Kubernetes resource specifications themselves. 

To be able to make decisions about what resources are compliant, we need a policy engine that is flexible enough to meet a variety of needs. 
The Open Policy Agent (OPA) is an open source, flexible, lightweight policy engine that has become increasingly popular in the cloud-native ecosystem.


Gatekeeper is an open source customizable Kubernetes admission webhook for cluster policy and governance. 


Gatekeeper is still under active development and is subject to change. For the most recent updates on the project, visit the official upstream repository.

•  Allow containers only from trusted container registries.
•  All containers must have resource limits.
•  Ingress hostnames must not overlap.
•  Ingresses must use only HTTPS.

•  Mutation (modifying resources based on policy; for example, add these labels)

•  Dry run (allow users to test policy before making it active in a cluster)

If these sound like interesting problems that you might be willing to help solve, the Gatekeeper community is always looking for new users and contributors to help shape the future of the project. If you would like to learn more, head over to the upstream repository on GitHub.


You should consider the following best practices when implementing policy and governance on your clusters:

Let’s consider the case of Deployments, for example. Deployments manage ReplicaSets, which manage pods. 

We would strongly recommend scoping the constraint to the resources to which you want it to be applied as tightly as possible. 

Kubernetes secrets is not recommended. Given that OPA will hold this in its cache (if it is configured to replicate that data) and resources will be passed to Gatekeeper, it leaves surface area for a potential attack vector.


In this chapter, we covered why policy and governance are important and walked through a project that’s built upon OPA, a cloud-native ecosystem policy engine, to provide a Kubernetes-native approach to policy and governance. 

### 12. Managing Multiple Clusters

We dive into the details of the differences between multicluster management and federation, tools to manage multiple clusters, and operational patterns for managing multiple clusters.

This is true, but there are scenarios such as workloads across regions, concerns of blast radius, regulatory compliance, and specialized workloads.



When adopting Kubernetes, you will likely have more than one cluster, and you might even start with more than one cluster to break out production from staging, user acceptance testing (UAT), or development. 


Following are concerns to think about when deciding to use multicluster versus a single-cluster architecture:


Regional-based workloads

This is one of the main concerns that we see with users designing for multicluster architectures. 

multiple clusters can help with preventing the impact of cascading failures due to software issues.

For example, if you have one cluster that serves 500 applications and you have a platform issue, it takes out 100%!o(MISSING)f the 500 applications. If you had a platform layer issue with 5 clusters serving those 500 applications, you affect only 20%!o(MISSING)f the applications. The downside to this is that now you need to manage five clusters, and your consolidation ratios will not be as good with a single cluster. 

It’s just much easier to separate these workloads than have to treat the cluster in such a specialized fashion.


in large Kubernetes clusters can become difficult to manage. As you start onboarding more and more teams to a Kubernetes cluster each team may have different security requirements and it can become very difficult to meet those needs in a large multi-tenant cluster. 

With multiple clusters you can limit the security impact with a misconfiguration. If you decide that a larger Kubernetes cluster fits your requirements, then ensure that you have a very good operational process for making security changes and understand the blast radius of making a change to RBAC, network policy, and pod security policies.

 Hard multitenancy is not a requirement for a lot of users; they trust the workloads that will be running within the cluster.

When running workloads that need to serve traffic from in-region endpoints, your design will include multiple clusters that are based per region. When you have a globally distributed application, it becomes a requirement at that point to run multiple clusters. 

Specialized workloads, such as high-performance computing (HPC), machine learning (ML), and grid computing, also need to be addressed in the multicluster architecture. 

With multicluster, you get isolation for “free,” but it also has design concerns that you need to address at the outset.



Some of the common challenges we find users running into are:
•  Data replication
•  Service discovery
•  Network routing
•  Operational management
•  Continuous deployment


Most databases have built-in tools to perform the replication, but you need to design the application to be able to handle the replication strategy.

Some cloud services, such as Google Cloud Spanner and Microsoft Azure CosmosDB, have built database services to help with the complications of handling data across multiple geographic regions.


Tools such as HashiCorp’s Consul can transparently synchronize services from multiple clusters and even services that reside outside of Kubernetes. 

If you need to route traffic in and out of the cluster, this becomes more complicated.

You’ll also need to consider the egress traffic between clusters and how to route that traffic. When your applications reside within a single cluster this is easy, but when introducing multicluster, you need to think about the latency of extra hops for services that have application dependencies in another cluster.

For applications that have tightly coupled dependencies, you should consider running these services within the same cluster to remove latency and extra complexity.

One of the most important aspects to managing multiclusters is ensuring that you have good automation practices in place because this will help to reduce the operational burden. When automating your clusters, you need to take into account the infrastructure deployment and managing add-on features to your clusters. 


For managing the infrastructure, using a tool like HashioCrp’s Terraform can help with deploying and managing a consistent state across your fleet of clusters.


Using an Infrastructure as Code (IaC) tool like Terraform will give you the benefit of providing a reproducible way to deploy your clusters. On the other hand, you also need to be able to consistently manage add-ons to the cluster, such as monitoring, logging, ingress, security, and other tools. 

This can cause challenges in the distribution of applications. You can easily manage multiple pipelines, but suppose that you have a hundred different pipelines to manage, which can make application distribution very difficult. 

Automation is key to successfully managing multiple clusters in your environment. You might not have everything automated on day one, but you should make it a priority to automate all aspects of your cluster deployments and operations.

As of this writing, the project is still in development, so make sure to keep an eye out for it as it matures.

You would also need to maintain the fundamental configurations of Prometheus, such as versions, persistence, retention policies, and replicas. As you can imagine, the maintenance of such a solution could be difficult across a large number of clusters and teams.

Instead of dealing with so many objects and configurations, you could install the prometheus-operator. This extends the Kubernetes API, exposing multiple new object kinds called Prometheus, ServiceMonitor, PrometheusRule, and AlertManager, which allow you to specify all of the details of a Prometheus deployment using just a few objects. You can use the kubectl tool to manage such objects, just as it manages any other Kubernetes API object.

Figure 12-1 shows the architecture of the prometheus-operator.

Utilizing the Operator pattern for automating key operational tasks can help improve your overall cluster management capabilities. The Operator pattern was introduced by the CoreOS team in 2016 with the etcd operator and prometheus-operator. The Operator pattern builds on two concepts:
•  Custom resource definitions
•  Custom controllers


Custom controllers are built on the core Kubernetes concepts of resources and controllers. Custom controllers allow you to build your own logic by watching events from Kubernetes API objects such as namespaces, Deployments, pods, or your own CRD. With custom controllers, you can build your CRDs in a declarative way.

The Elasticsearch operator can perform the following operations:
•  Replicas for master, client, and data nodes
•  Zones for highly available deployments
•  Volume sizes for master and data nodes
•  Resizing of cluster
•  Snapshot for backups of the Elasticsearch cluster

As you can see, the operator provides automation for many tasks that you would need to perform when managing Elasticsearch, such as automating snapshots for backup and resizing the cluster. The beauty of this is that you manage all of this through familiar Kubernetes objects.

GitOps takes the concepts of the software development life cycle and applies them to operations.

With GitOps, your Git repository becomes your source of truth, and your cluster is synchronized to the configured Git repository. For example, if you update a Kubernetes Deployment manifest, those configuration changes are automatically reflected in the cluster state.

By using this method, you can make it easier to maintain multiclusters that are consistent and avoid configuration drift across the fleet. GitOps allows you to declaratively describe your clusters for multiple environments and drives to maintain that state for the cluster. The practice of GitOps can apply to both application delivery and operations, but in this chapter, we focus on using it to manage clusters and operational tooling.

You now need to make a change to the Deployment manifest to configure it with your forked repo from Chapter 6. Modify the following line in the Deployment file to match your forked GitHub repository:

Modify the following line with your Git repository:

Multicluster Management Tools

When working with multiple clusters, using Kubectl can immediately become confusing because you need to set different contexts to manage the different clusters. 

Two tools that you will want to install right away when dealing with multiple clusters are kubectx and kubens, which allow you to easily change between multiple contexts and namespaces.


  
Rancher centrally manages multiple Kubernetes clusters in a centrally managed user interface (UI). It monitors, manages, backs up, and restores Kubernetes clusters across on-premises, cloud, and hosted Kubernetes setups. 

The introducton of Kubernetes CRDs allowed a different way of thinking about how Federation could be designed.


Federation v2 (now called KubeFed) requires Kubernetes 1.11+ and is currently in alpha as of this writing. Federation v2 is built around the concept of CRDs and custom controllers, which allows you to extend Kubernetes with new 

KubeFed is not necessarily about multicluster management, but providing high availability (HA) deployments across multiple clusters. It allows you to combine multiple clusters into a single management endpoint for delivering applications on Kubernetes. For example, if you have a cluster that resides in multiple public cloud environments, you can combine these clusters into a single control plane to manage deployments to all clusters to increase the resiliency of your application.


As you can see from how KubeFed works, the use cases Kubefed supports are multiregion, multicloud, and global application deployments to Kubernetes.

Setting up federated Kubernetes clusters is beyond the scope of this book, but you can learn more about the subject by referring to the KubeFed User Guide.


KubeFed is still in alpha, so keep an eye on it, but embrace the tools that you already have or can implement now so that you can be successful with Kubernetes HA and multicluster deployments

.




Managing Multiple Clusters Best Practices

Consider the following best practices when managing multiple Kubernetes clusters:


•  Limit the blast radius of your clusters to ensure cascading failures don’t have a bigger impact on your applications.


•  If you have regulatory concerns such as PCI, HIPPA, or HiTrust, think about utilizing multiclusters to ease the complexity of mixing these workloads with general workloads.


•  If hard multitenancy is a business requirement, workloads should be deployed to a dedicated cluster.

•  If multiple regions are needed for your applications, utilize a Global Load Balancer to manage traffic between clusters.

•  Utilize Kubernetes operators like the prometheus-operator or Elasticsearch operator to handle automated operational tasks.

•  Be sure that your CD strategy can handle multiple rollouts between regions or multiple clusters.

•  Investigate utilizing a GitOps approach to managing multiple cluster operational components to ensure consistency between all clusters in your fleet.

In this chapter, we discussed different strategies for managing multiple Kubernetes clusters. It’s important to think about what your needs are at the outset and whether those needs match a multicluster topology. 

### 13. Integrating External Services and Kubernetes

The most common pattern for connecting Kubernetes with external services consists of a Kubernetes service that is consuming a service that exists outside of the Kubernetes cluster. 

When we consider the task of making an external service accessible from Kubernetes, the first challenge is simplyto get the networking to work correctly. 

the next challenge is to make the external service look and feel like a Kubernetes service. InKubernetes, service discovery occurs via Domain Name System (DNS) lookups and, thus, to make our externaldatabase feel like it is a native part of Kubernetes, we need to make the database discoverable in the same DNS.

The first way to achieve this is with a selector-less Kubernetes Service.

When you create a Kubernetes Servicewithout a selector, there are no Pods that match the service; thus, there is no load balancing performed. Instead,you can program this selector-less service to have the specific IP address of the external resource that you wantto add to the Kubernetes cluster. 

 Here is anexample of a selector-less service for an external database:

When the service exists, you need to update its endpoints to contain the database IP address serving at 24.1.2.3:


Figure 13-1 depicts how this integrates together within Kubernetes.


The previous example assumed that the external resource that you were trying to integrate with your Kubernetescluster had a stable IP address. 

In such a situation, you can define a CNAME-based Kubernetes Service. If you’re not familiar with DNS records, aCNAME, or Canonical Name, record is an indication that a particular DNS address should be translated to adifferent Canonical DNS name. 

Youcan use Kubernetes Services to define CNAME records in the Kubernetes DNS server. For example, if you have anexternal database with a DNS name of database.myco.com, you might create a CNAME Service that is namedmyco-database. Such a Service looks like this:

To set up the cluster’s DNS server to communicate with an alternate DNS resolver, you need to adjust itsconfiguration. You do this by updating a Kubernetes ConfigMap with a configuration file for the DNS server.

This server is configured by writing a Corefileconfiguration into a ConfigMap named coredns in the kube-system namespace. 

CNAME records are a useful way to map external services with stable DNS names to names that are discoverablewithin your cluster. 

In the previous section, we explored how to import preexisting services to Kubernetes, but you might also need toexport services from Kubernetes to the preexisting environments. This might occur because you have a legacyinternal application for customer management that needs access to some new API that you are developing in acloud-native infrastructure. 

. Consequently,setting up routing between a traditional application and Kubernetes pods is the key task to enable the export ofKubernetes-based services.

You activate an internal load balancer byadding a cloud-specific annotation to your Service load balancer. For example, in Microsoft Azure, you add theservice.beta.kubernetes.io/azure-load-balancer-internal: "true" annotation. 

You place annotations inthe metadata field in the Service resource as follows:

When you export a Service via an internal load balancer, you receive a stable, routeable IP address that is visibleon the virtual network outside of the cluster. You then can either use that IP address directly or set up internalDNS resolution to provide discovery for your exported service.

Unfortunately, in on-premises installations, cloud-based internal load balancers are unavailable. In this contextusing a NodePort-based service is often a good solution. A Service of type NodePort exports a listener on everynode in the cluster that forwards traffic from the node’s IP address and selected port into the Service that youdefined, as shown in Figure 13-3.

To access services outside of the cluster, you can use selector-less services and directly program in theIP address of the machine (e.g., the database) with which you want to communicate. If you don’t havefixed IP addressess, you can instead use CNAME services to redirect to a DNS name. If you have neither aDNS name nor fixed services, you might need to write a dynamic operator that periodically synchronizesthe external service IP addresses with the Kubernetes Service endpoints.

 This chapter described how you can integrateKubernetes with legacy applications and also how to integrate different services running across multiple distinctKubernetes clusters. 

The techniques described in this chapter will help you achieve that.

### 14. Running Machine Learning in Kubernetes

In this chapter, we will cover why Kubernetes is a great place for machine learning and provide best practices for both cluster administrators and data scientists alike on how to get the most out of Kubernetes when running machine learning workloads.

because deep learning has fast become the area of innovation on platforms like Kubernetes.

Why Is Kubernetes Great for Machine Learning?

Kubernetes has quickly become the home for rapid innovation in deep learning. The confluence of tooling and libraries such as TensorFlow make this technology more accessible to a large audience of data scientists. What makes Kubernetes such a great place to run your deep learning workloads? Let’s cover what Kubernetes provides:

Ubiquitous Kubernetes is everywhere. All of the major public clouds support it, and there are distributions for private clouds and infrastructure. 

Scalable Deep learning workflows typically need access to large amounts of computing power in order to efficiently train machine learning models.

Kubernetes ships with native autoscaling capabilities that make it easy for data scientists to achieve and fine-tune the level of scale they need to train their models.

Self-service Data scientists can use Kubernetes to perform self-service machine learning workflows on demand, without needing specialized knowledge of Kubernetes itself.


Portable Machine learning models can be run anywhere, provided that the tooling is based on the Kubernetes API. This allows machine learning workloads to be portable across Kubernetes providers.

Machine Learning Workflow

To effectively understand the needs of deep learning, you must understand the complete workflow. Figure 14-1 represents a simplified machine learning workflow.


For the purposes of this book, we consider only the storage aspect. Datasets vary in size, from hundreds of megabytes to hundreds of terabytes. 

Serving This is the process of making the trained model accessible to service requests from clients so that it can make predictions based on the the data supplied from the client.

if you have an image-recognition model that’s been trained to detect dogs and cats, a client might submit a picture of a dog, and the model should be able to determine whether it is a dog, with a certain level of accuracy.


In this section, we discuss topics you will need to consider before running machine learning workloads on your Kubernetes cluster. 

The largest challenge you will face as a cluster administrator responsible for a team of data scientists is understanding the terminology.

Typically, the more resources you apply, the faster the training will be completed.

Let’s confirm that your Kubernetes cluster has GPUs available. The following output shows that this Kubernetes cluster has four GPUs available:
$ kubectl get nodes -o yaml | grep -i 

To run your training, you are going to using the Job kind in Kubernetes, given that training is a batch workload. 

Create a file called mnist-demo.yaml using the following manifest, and save it to your filesystem:


Now, create this resource on your Kubernetes cluster:

Check the status of the job you just created:


If you take a look at the pods, you should see the training job running:

Looking at the pod logs, you can see the training happening:
$ kubectl logs mnist-de

Finally, you can see that the training has completed by looking at the job status:


To clean up the training job, simply run the following command:

•  Kubeflow is a machine learning toolkit for Kubernetes. It is native to Kubernetes and ships with several tools necessary to complete the machine learning workflow. 

### 15. Building Higher-Level Application Patterns on Top of Kubernetes


Kubernetes is a complex system. Although it simplifies the deployment and operations of distributed applications, it does little to make the development of such systems easy. 

Additionally, in many large companies, it makes sense to standardize the way in which applications are configured and deployed so that everyone adheres to the same operational best practices. 

developers automatically adhere to these principles. 

However, developing these abstractions can hide important details from the developer and might introduce a walled garden that limits or complicates the development of certain applications or the integration of existing solutions.

A good example of such a use case would be building a machine learning pipeline. The domain is relatively well understood. The data scientists who are your users are likely not familiar with Kubernetes. Enabling these data scientists to rapidly get their work done and focus on their domains rather than distributed systems is the primary goal. Thus, building a complete abstraction on top of Kubernetes makes the most sense.


it is a far better choice to extend Kubernetes rather than wrap it.

Extending rather than replacing the Kubernetes API ensures that you can continue to use these tools and new ones as they are developed.

There are three related technical solutions. 
The first is the sidecar. Sidecar containers (shown in Figure 15-1) have been popularized in the context of service meshes. They are containers that run alongside a main application container to provide additional capabilities that are decoupled from the main application and often maintained by a separate team. 

in service meshes, a sidecar might provide transparent mutual Transport Layer Security (mTLS) authentication to a containerized application.


Fortunately, there are additional tools for extending Kubernetes that simplify things. In particular, 
Kubernetes features admission controllers. Admission controllers are interceptors that read Kubernetes API requests prior to them being stored (or “admitted”) into the cluster’s backing store. You can use these admission controllers to validate or modify API objects.

In the context of sidecars, you can use them to automatically add sidecars to all pods created in the cluster so that developers do not need to know about the sidecars in order to reap their benefits.

The utility of admission controllers isn’t limited to adding sidecars. You can also use them to validate objects submitted by developers to Kubernetes. 
For example, you could implement a linter for Kubernetes that ensures developers submit pods and other resources that follow best practices for using Kubernetes. 

CRDs are a way to dynamically add new resources to an existing Kubernetes cluster. For example, using CRDs, you could add a new ReplicatedService resource to a Kubernetes cluster. When a developer creates an instance of a ReplicatedService, it turns around to Kubernetes and creates corresponding Deployment and Service resources.

CRDs are generally implemented by a control loop that is deployed into the cluster itself to manage these new resource types.



Adding new resources to your cluster is a great way to provide new capabilities, but to truly take advantage of them, it’s often useful to extend the Kubernetes user experience (UX) as well.

Following these design guidelines can help to ensure that the platform you build is a successful one instead of a “legacy” dead end from which you must eventually move away.


The problem with this approach, however, comes when the developer encounters the limitations of the programming environment that you have given them. 

No matter the reason, hitting this wall is an ugly moment for the developer, because it is a moment when they suddenly must learn a great deal more about how to package their application, when all they really wanted to do was to extend it slightly to fix a bug or deliver a new feature.


Another common story of platforms is that they evolve and interconnect with other systems. Many developers might be very happy and productive in your platform, but any real-world application will span both the platform that you build and lower-level Kubernetes applications as well as other platforms.

Connections to legacy databases or open source applications built for Kubernetes will always become a part of a sufficiently large application.


Because of this need for interconnectivity, it’s critically important that the core Kubernetes primitives for services and service discovery are used and exposed by any platform that you construct. 

Thus, it is often necessary to build platforms on top of Kubernetes to make developers more productive and/or Kubernetes easier. When building such platforms, you’ll benefit from keeping the following best practices in mind:

•  Use admission controllers to limit and modify API calls to the cluster. An admission controller can validate (and reject invalid) Kubernetes resources. A mutating admission controller can automatically modify API resources to add new sidecars or other changes that users might not even need to know about.

Kubernetes is a fantastic tool for simplifying the deployment and operation of software, but unfortunately, it is not always the most developer-friendly or productive environment. Because of this, a common task is to build a higher-level platform on top of Kubernetes in order to make it more approachable and usable by the average developer. 

### 16. Managing State and Stateful Applications

This chapter focuses on best practices for managing state, from simple patterns such as saving a file to a network share, to complex data management systems like MongoDB, mySQL, or Kafka. 

The ability to inject data into a volume that can be read by containers in a pod is covered in Chapter 5; however, data mounted from ConfigMaps or secrets is usually read-only, and this section focuses on giving containers volumes that can be written to and will survive a container failure or, even better, a pod failure.

Why would this be needed, you might wonder? A useful example is that of a legacy application that was written to log application-specific information to a local filesystem. 

Use hostDir when access to the data is required by node-based agents or services.


To allow for dynamically created volumes, there must be a StorageClass defined in Kubernetes. 

only when a PersistentVolumeClaim matches the PersistentVolume will it actually be assigned to a pod. 

There are numerous plug-ins supported directly in Kubernetes, and each has different configuration parameters to adjust:


Selectors can also be used to match certain PersistentVolumes that meet a certain criteria will be allocated:

The preceding claim will match the PersistentVolume created earlier because the storage class name, the selector match, the size, and the access mode are all equal.


Now to use the volume, the pod.spec should just reference the claim by name, as follows:

Some cloud providers will include a default storage class to map to the cheapest storage allowed by their instances.

The FlexVolume interface has been the traditional method used to add additional features for a storage provider. It does require specific drivers to be installed on all of the nodes of the cluster that will use it. 

The CSI plug-in solves this by basically exposing the same functionality and being as easy to use as deploying a pod into the cluster.

•  If possible, enable the DefaultStorageClass admission plug-in and define a default storage class. Many times, Helm charts for applications that require PersistentVolumes default to a default storage class for the chart, which allows the application to be installed without too much modification.

•  When designing the architecture of the cluster, either on-premises or in a cloud provider, take into consideration zone and connectivity between the compute and data layers using the proper labels for both nodes and PersistentVolumes, and using affinity to keep the data and workload as close as possible. 

•  Consider very carefully which workloads require state to be maintained on disk. Can that be handled by an outside service like a database system or, if running in a cloud provider, by a hosted service that is API consistent with currently used APIs, say a mongoDB or mySQL as a service?

•  Determine how much effort would be involved in modifying the application code to be more stateless.

The CSI specification has added an API for vendors to plug in native snapshot technologies if the storage backend can support it.

•  Verify the proper life cycle of the data that volumes will hold. By default the reclaim policy is set to for dynamically provisioned persistentVolumes which will delete the volume from the backing storage provider when the pod is deleted. Sensitive data or data that can be used for forensic analysis should be set to reclaim.



•  Pods in a StatefulSet are scaled down in reverse sequence.

•  Pods in a StatefulSet can be addressed individually by name behind a headless Service.

The headless Service is the same as a regular Service but does not do the normal load balancing:

clusterIP: None #This creates the headless Service￼

All of the other complex operations, like backups, failover, leader registration, new replica registration, and upgrades, are all operations that need to happen quite regularly and will require some careful consideration when running as StatefulSets.

Early on in the growth of Kubernetes, CoreOS site reliability engineers (SREs) created a new class of cloud native software for Kubernetes called Operators.

The proper creation, backup, and restore configuration of Prometheus or etcd instances can be handled by an Operator and are basically new Kubernetes-managed objects just like a pod or Deployment.

In mid-2018, RedHat created the Operator Framework, which is a set of tools including an SDK life cycle manager and future modules that will enable features such as metering, marketplace, and registry type functions. Operators are not only for stateful applications, but because of their custom controller logic they are definitely more amenable to complex data services and stateful systems.

•  If a node in the cluster becomes unresponsive, any pods that are part of a StatefulSet are not not automatically deleted; they instead will enter a Terminating or Unkown state after a grace period. The only way to clear this pod is to remove the node object from the cluster

•  Even after force deleting a pod, it might stay in an Unknown state, so a patch to the API server will delete the entry and cause the StatefulSet controller to create a new instance of the deleted pod: kubectl patch pod nginx-0 -p '{"metadata":{"finalizers":null}}'.


  
If you’re running a complex data system with some type of leader election or data replication confirmation processes, use preStop hook to properly close any connections, force leader election, or verify data synchronization before the pod is deleted using a graceful shutdown process.

•  When the application that requires stateful data is a complex data management system, it might be worth a look to determine whether an Operator exists to help manage the more complicated life cycle components of the application. 

but the reality of running them in clusters has been accelerated by the introduction of StatefulSets and Operators.

### 17. Admission Control and Authorization


Controlling access to the Kubernetes API is key to ensuring that your cluster is not only secured but also can be used as a means to impart policy and governance for all users, workloads, and components of your Kubernetes cluster.

Figure 17-1 provides insight on how and where admission control and authorization take place.

It depicts the end-to-end request flow through the Kubernetes API server until the object, if accepted, is saved to storage.

Admission Control
Have you ever wondered how namespaces are automatically created when you define a resource in a namespace that doesn’t already exist? Maybe you’ve wondered how a default storage class is selected? These changes are powered by a little-known feature called admission controllers.

Admission controllers sit in the path of the Kubernetes API server request flow and receive requests following the authentication and authorization phases. They are used to either validate or mutate (or both) the request object before saving it to storage.

The difference between validating and mutating admission controllers is that mutating can modify the request object they admit, whereas validating cannot.

Why Are They Important?


Given that admission controllers sit in the path of all API server requests, you can use them in a variety of different ways. Most commonly, admission controller usage can be grouped into the following three groups:


Policy and governance 
Admission controllers allow policy to be enforced in order to meet business requirements; 

•  Only internal cloud load balancers can be used when in the dev namespace.

•  All containers in a pod must have resource limits.

•  Add predefined standard labels or annotations to all resources in order to make them discoverable to existing tools.

•  All Ingress resources only use HTTPS. For more details on how to use admission webhooks in this context, see Chapter 11.

Resource management 
Admission controllers allow you to validate in order to provide best practices for your cluster users, for example:

•  Ensure all ingress fully qualified domain names (FQDN) fall within a specific suffix.
•  Ensure ingress FQDNs don’t overlap.
•  All containers in a pod must have resource limits.

Dynamic controllers, on the other hand, are configurable at runtime and are developed outside the core Kubernetes codebase. The only type of dynamic admission control is admission webhooks, which receive admission requests via HTTP callbacks.

Kubernetes ships with more than 30 admission controllers, which are enabled via the following flag on the Kubernetes API server:

You can find the list of Kubernetes admission controllers and their functionality in the Kubernetes documentation.


Configuring Admission Webhooks


As previously mentioned, one of the main advantages of admission webhooks is that they are dynamically configurable. It is important that you understand how to effectively configure admission webhooks because there are implications and trade-offs when it comes to consistency and failure modes.



The snippet that follows is a ValidatingWebhookConfiguration resource manifest. This manifest is used to define a validating admission webhook. The snippet provides detailed descriptions on the function of each field:


It’s also important to note that mutating admission controllers are always run prior to running validating admission controllers. If you think about it, this makes good sense: you probably don’t want to validate objects that you are going to subsequently modify. 

Figure 17-2. An API request flow via admission webhooks


  
Don’t mutate the same fields. Configuring multiple mutating admission webhooks also presents challenges. There is no way to order the request flow through multiple mutating admission webhooks, so it’s important to not have mutating admission controllers modify the same fields, because this can result in unexpected results. 

we generally recommend configuring validating admission webhooks to confirm that the final resource manifest is what you expect post-mutation because it’s guaranteed to be run following mutating webhooks.


raise an alert when the API server logs that it cannot reach a given admission webhook.

To protect against this you can scope the rules to ensure that only specific resource requests are set to the admission webhook. As a tenet, you should never have any rules that apply to all resources in the cluster.

All admission webhook calls are configured with a 30-second timeout, after which time the failurePolicy takes effect. Even if it takes several seconds for your admission webhook to make an admit/deny decision, it can severely affect user experience when working with the cluster. Avoid having complex logic or relying on external systems such as databases in order to process the admit/deny logic.


Scoping admission webhooks. There is an optional field that allows you to scope the namespaces in which the admission webhooks operate on via the NamespaceSelector field. This field defaults to empty, which matches everything, but can be used to match namespace labels via the use of the matchLabels field. We recommend that you always use this field because it allows for an explicit opt-in per namespace.


  
The kube-system namespace is a reserved namespace that’s common across all Kubernetes clusters. It’s where all system-level services operate. We recommend never running admission webhooks against the resources in this namespace specifically, and you can achieve this by using the NamespaceSelector field and simply not matching the kube-system namespace. You should also consider it on any system-level namespaces that are required for cluster operation.

Failure to do so can result in a broken cluster or, even worse, an injection attack on your application workloads.


Authorization

We often think about authorization in the context of answering the following question: “Is this user able to perform these actions on these resources?” In Kubernetes, the authorization of each request is performed after authentication but before admission. 

Figure 17-3 illustrates where authorization sits in the request flow.



Authorization modules are responsible for either granting or denying permission to access. They determine whether to grant access based on policy that must be explicitly defined; otherwise all requests will be implicitly denied.


As of version 1.15, Kubernetes ships with the following authorization modules out of the box:


RBAC Allows authorization policy to be configured via the Kubernetes API (refer to Chapter 4)
Webhook Allows the authorization of a request to be handled via a remote REST endpoint

The modules are configured by the cluster administrator via the following flag on the API server: --authorization-mode. Multiple modules can be configured and are checked in order. 




Let’s take a look at a policy definition in the context of using the ABAC authorization module. The following grants user Mary read-only access to a pod in the kube-system namespace:

The really cool part is that you can query these APIs by creating resources as you typically would.

Consider the following best practices before making changes to the authorization modules configured on your cluster:


Given these details, we recommend using only the RBAC module for user authorization because the rules are configured and stored in Kubernetes itself.


Given that every request is subject to the authorization process, a failure of a webhook service would be devastating for a cluster. Therefore, we generally recommend not using external authorization modules unless you completely vet and are comfortable with your cluster failure modes if the webhook service becomes unreachable or unavailable.



Summary
In this chapter, we covered the foundational topics of admission and authorization and covered best practices. 

Put these skills to use by determining the best admission and authorization configuration that allows you to customize the controls and policies needed for the life of your cluster.