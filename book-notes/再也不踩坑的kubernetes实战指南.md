## 再也不踩坑的kubernetes实战指南
> 杜宽

### 版权信息

第3章主要讲解Kubernetes常见应用的容器化，并部署至Kubernetes集群实现高可用，同时介绍了Kubernetes的各个组件和资源。第4章主要介绍持续集成和持续部署，包括Jenkins最新的功能Pipeline的使用，从Pipeline的语法到项目实操，传统Java和Spring Cloud应用的容器化以及自动化构建部署。

的Server Mesh，使用Istio代替微服务架构中的网络功能、实现限速、分流和路由等内容。

### 内容简介

Kubernetes技术爱好者，现就职于国内某知名集团公司，主要负责Kubernetes架构、业务容器化设计等工作。

### 作者简介

Kubernetes在近几年，乃至未来5到10年，都会是技术圈一个很火的名词，Kubernetes由谷歌（Google）开源，它构建在谷歌15年生产环境经验的基础之上，开源的背后有着来自社区的强大技术团队共同维护和更新。Kubernetes的诞生不仅解决了公司架构带来的问题，而且也大大减少了运维成本，可以轻轻松松管理上万个容器节点。目前很多公司都在致力于对容器和Kubernetes的推进，将公司现有的业务拆分为微服务，然后对其进行容器化，所以目前对容器和容器编排工具的学习，是每个技术人员义不容辞的责任。

### 前言

经测试，当集群全部宕机（发生此种情况的机会很小）时，二进制安装方式恢复能力较强，速度较快。

### 第1章 Kubernetes高可用安装

笔者公司大部分线下测试环境均采用Kubeadm安装，这也是目前官方默认的安装方式，比二进制安装方式更加简单，可以让初学者快速上手并测试。

有很多基于Ansible的自动化安装方式，但是为了更好地学习Kubernetes，还是建议体验一下Kubernetes的手动安装过程，以熟悉Kubernetes的各个组件。

所有节点配置limit：

的Kube-Proxy均采用ipvs模式，该模式也是新版默认支持的代理模式，性能比iptables要高，如果服务器未配置安装ipvs，将转换为iptables模式。所有节点安装ipvsadm：

所有节点配置ipvs模块，在内核4.19版本nf_conntrack_ipv4已经改为nf_conntrack，本例安装的内核为4.18，使用nf_conntrack_ipv4即可：

检查是否加载，并将其加入至开机自动加载（在目录/etc/sysconfig/modules/下创建一个k8s.modules写上上述命令即可）：

开启一些K8S集群中必须的内核参数，所有节点配置K8S内核：

默认配置的pause镜像使用gcr.io仓库，国内可能无法访问，所以这里配置Kubelet使用阿里云的pause镜像，使用kubeadm初始化时会读取该文件的变量：

本例高可用采用的是HAProxy+Keepalived，HAProxy和KeepAlived以守护进程的方式在所有Master节点部署。通过yum安装HAProxy和KeepAlived：

所有Master节点配置HAProxy（详细配置参考HAProxy文档，所有Master节点的HAProxy配置相同）：

所有Master节点配置KeepAlived。注意修改interface（服务器网卡）、priority（优先级，不同即可）、mcast_src_ip（本机IP），详细配置参考keepalived文档。

高可用方式不一定非要采用HAProxy和KeepAlived，在云上的话可以使用云上的负载均衡，比如在阿里云上可以使用阿里云内部的SLB，就无须再配置HAProxy和KeepAlived，只需要将对应的VIP改成SLB的地址即可。在企业内部可以使用F5硬件负载均衡，反向代理到每台Master节点的6443端口即可。

Calico作为Kubernetes集群的网络组件，主要用来为Kubernetes创建和管理一个三层网络，为每个容器分配一个可路由的IP地址，实现集群中Pod之间的通信。

本节介绍Kubernetes的高可用配置，如果暂时不需要高可用集群可以略过此节，然后将其VIP改为Master01节点的IP地址即可。在生产线上Master组件的高可用是很重要的一部分，可用于防止Master节点宕机后对集群造成的影响。

使用kubeadm join将Node节点加入集群，使用的是刚才初始化Master生成的Token：

在新版的Kubernetes中系统资源的采集均使用Metrics-server，可以通过Metrics采集节点和Pod的内存、磁盘、CPU和网络的使用率。

### 1.1 kubeadm高可用安装K8S集群（1.11.x和1.12.x）

高可用方式不一定非要采用HAProxy和KeepAlived，在云上的话可以使用云上的负载均衡，比如在阿里云上可以使用阿里云内部的SLB，就无须再配置HAProxy和KeepAlived，只需要将对应的VIP改成SLB的地址即可。

Calico作为Kubernetes集群的网络组件，主要用来为Kubernetes创建和管理一个三层网络，为每个容器分配一个可路由的IP地址，实现集群中Pod之间的通信。

### 1.3 二进制高可用安装K8S集群（1.13.x和1.14.x）

CNI（Container Network Interface，容器网络接口）是CNCF旗下的一个项目，由一组用于配置容器的网络接口的规范和库组成。CNI主要用于解决容器网络互联的配置并支持多种网络模式。CNI的安装步骤如下。

CoreDNS用于集群中Pod解析Service的名字，Kubernetes基于CoreDNS用于服务发现功能。

查看Node资源使用：

### 1.4 小结

有条件的情况下，对Docker的存储目录（/var/lib/docker）也可以单独挂一个高效的存储。

完成扩容后最好先使用Taint禁止调度，对新节点进行各方面的测试后，再加入到集群调度中。

### 第2章 Docker及Kubernetes基础

Docker提供了一个额外的软件抽象层及操作系统层虚拟化的自动管理机制。Docker运行名为“Container（容器）”的软件包，容器之间彼此隔离，并捆绑了自己的应用程序、工具、库和配置文件。所有容器都由单个操作系统内核运行，因此比虚拟机更轻量级。

Docker利用Linux资源分离机制，例如cgroups及Linux Namespace来创建相互独立的容器（Container），可以在单个Linux实体下运行，避免了启动一个虚拟机造成的额外负担。Linux核心对Namespace（命名空间）的支持完全隔离了不同Namespace下的应用程序的“视野”（即作用范围），包括进程树、网络、用户ID与挂载的文件系统等，而核心cgroups则提供了资源隔离，包括CPU、存储器、Block I/O与网络。

Dockerfile是用来快速创建自定义镜像的一种文本格式的配置文件，在持续集成和持续部署时，需要使用Dockerfile生成相关应用程序的镜像，然后推送到公司内部仓库中，再通过部署策略把镜像部署到Kubernetes中

使用ENV定义环境变量并用CMD执行命令：

使用ADD添加一个压缩包，使用WORKDIR改变工作目录：

使用COPY拷贝指定目录下的所有文件到容器，不包括本级目录。此时只会拷贝webroot下的所有文件，不会将webroot拷贝过去：

设置启动容器的用户，在生产环境中一般不建议使用root启动容器，所以可以根据公司业务场景自定义启动容器的用户：

使用Volume创建容器可挂载点：

挂载Web目录到/data，注意，对于宿主机路径，要写绝对路径：

### 2.1 Docker基础

Kubernetes致力于提供跨主机集群的自动部署、扩展、高可用以及运行应用程序容器的平台，其遵循主从式架构设计，其组件可以分为管理单个节点（Node）组件和控制平面组件。Kubernetes Master是集群的主要控制单元，用于管理其工作负载并指导整个系统的通信。

APIServer。APIServer是整个集群的控制中枢，提供集群中各个模块之间的数据交换，并将集群状态和信息存储到分布式键－值（key-value）存储系统Etcd集群中。同时它也是集群管理、资源配额、提供完备的集群安全机制的入口，为集群各类资源对象提供增删改查以及watch的REST API接口。APIServer作为Kubernetes的关键组件，使用Kubernetes API和JSON over HTTP提供Kubernetes的内部和外部接口。

Scheduler。Scheduler是集群Pod的调度中心，主要是通过调度算法将Pod分配到最佳的节点（Node），它通过APIServer监听所有Pod的状态，一旦发现新的未被调度到任何Node节点的Pod（PodSpec.NodeName为空），就会根据一系列策略选择最佳节点进行调度，对每一个Pod创建一个绑定（binding），然后被调度的节点上的Kubelet负责启动该Pod。

Controller Manager。Controller Manager是集群状态管理器（它的英文直译名为控制器管理器），以保证Pod或其他资源达到期望值。当集群中某个Pod的副本数或其他资源因故障和错误导致无法正常运行，没有达到设定的值时，Controller Manager会尝试自动修复并使其达到期望状态。Controller Manager包含NodeController、ReplicationController、EndpointController、NamespaceController、ServiceAccountController、ResourceQuotaController、ServiceController和TokenController，该控制器管理器可与API服务器进行通信以在需要时创建、更新或删除它所管理的资源，如Pod、服务断点等。

Etcd。Etcd由CoreOS开发，用于可靠地存储集群的配置数据，是一种持久性、轻量型、分布式的键－值（key-value）数据存储组件。Etcd作为Kubernetes集群的持久化存储系统，集群的灾难恢复和状态信息存储都与其密不可分，所以在Kubernetes高可用集群中，Etcd的高可用是至关重要的一部分，在生产环境中建议部署为大于3的奇数个数的Etcd，以保证数据的安全性和可恢复性。Etcd可与Master组件部署在同一个节点上，大规模集群环境下建议部署在集群外，并且使用高性能服务器来提高Etcd的性能和降低Etcd同步数据的延迟。

Kubelet作为守护进程运行在Node节点上，负责监听该节点上所有的Pod，同时负责上报该节点上所有Pod的运行状态，确保节点上的所有容器都能正常运行。当Node节点宕机（NotReady状态）时，该节点上运行的Pod会被自动地转移到其他节点上。Node节点包括：Kubelet，负责与Master通信协作，管理该节点上的Pod。Kube-Proxy，负责各Pod之间的通信和负载均衡。Docker Engine，Docker引擎，负载对容器的管理。

当Kubernetes对系统的任何API对象如Pod和节点进行“分组”时，会对其添加Label（key=value形式的“键－值对”）用以精准地选择对应的API对象。而Selector（标签选择器）则是针对匹配对象的查询方法。注：键－值对就是key-value pair。例如，常用的标签tier可用于区分容器的属性，如frontend、backend；或者一个release_track用于区分容器的环境，如canary、production等。

当且仅当Deployment的Pod模板（即.spec.template）更改时，才会触发Deployment更新，例如更新label（标签）或者容器的image（镜像）。

所有Deployment的rollout历史都保留在系统中，可以随时回滚。假设我们又进行了几次更新：[插图]使用kubectl rollout history查看部署历史：[插图]查看Deployment某次更新的详细信息，使用--revision指定版本号：

使用kubectl rollout undo回滚到上一个版本：

使用--to-revision参数回到指定版本：

Deployment支持暂停更新，用于对Deployment进行多次修改操作。使用kubectl rollout pause暂停Deployment更新：

通过rollout history可以看到没有新的更新：

使用kubectl rollout resume恢复Deployment更新：

在默认情况下，revision保留10个旧的ReplicaSet，其余的将在后台进行垃圾回收，可以在.spec.revisionHistoryLimit设置保留ReplicaSet的个数。当设置为0时，不保留历史记录。

.spec.strategy.type==Recreate，表示重建，先删掉旧的Pod再创建新的Pod。.spec.strategy.type==RollingUpdate，表示滚动更新，可以指定maxUnavailable和maxSurge来控制滚动更新过程。[插图]　.spec.strategy.rollingUpdate.maxUnavailable，指定在回滚更新时最大不可用的Pod数量，可选字段，默认为25%!，(MISSING)可以设置为数字或百分比，如果maxSurge为0，则该值不能为0。[插图]　.spec.strategy.rollingUpdate.maxSurge可以超过期望值的最大Pod数，可选字段，默认为25%!，(MISSING)可以设置成数字或百分比，如果maxUnavailable为0，则该值不能为0。

StatefulSet（有状态集）常用于部署有状态的且需要有序启动的应用程序。

StatefulSet主要用于管理有状态应用程序的工作负载API对象。比如在生产环境中，可以部署ElasticSearch集群、MongoDB集群或者需要持久化的RabbitMQ集群、Redis集群、Kafka集群和ZooKeeper集群等。

每个Pod都有一个持久的标识符，在重新调度时也会保留，一般格式为StatefulSetName-Number。比如定义一个名字是Redis-Sentinel的StatefulSet，指定创建三个Pod，那么创建出来的Pod名字就为Redis-Sentinel-0、Redis-Sentinel-1、Redis-Sentinel-2。而StatefulSet创建的Pod一般使用Headless Service（无头服务）进行通信，和普通的Service的区别在于Headless Service没有ClusterIP，它使用的是Endpoint进行互相通信，Headless一般的格式为：statefulSetName-{0..N-1}.serviceName.namespace.svc.cluster. local。

一个Redis主从架构，Slave连接Master主机配置就可以使用不会更改的Master的Headless Service，例如Redis从节点（Slave）配置文件如下：[插图]其中，redis-sentinel-master-ss-0.redis-sentinel-master-ss.public-service.svc.cluster.local是Redis Master的Headless Service

一般StatefulSet用于有以下一个或者多个需求的应用程序：需要稳定的独一无二的网络标识符。需要持久化数据。需要有序的、优雅的部署和扩展。需要有序的、自动滚动更新。

为了确保数据安全，删除和缩放StatefulSet不会删除与StatefulSet关联的卷，可以手动选择性地删除PVC和PV（关于PV和PVC请参考2.2.12节）。

StatefulSet目前使用Headless Service（无头服务）负责Pod的网络身份和通信，但需要创建此服务。

On Delete策略OnDelete更新策略实现了传统（1.7版本之前）的行为，它也是默认的更新策略。当我们选择这个更新策略并修改StatefulSet的.spec.template字段时，StatefulSet控制器不会自动更新Pod，我们必须手动删除Pod才能使控制器创建新的Pod。（2）RollingUpdate策略RollingUpdate（滚动更新）更新策略会更新一个StatefulSet中所有的Pod，采用与序号索引相反的顺序进行滚动更新。

DaemonSet（守护进程集）和守护进程类似，它在符合匹配条件的节点上均部署一个Pod。1．什么是DaemonSetDaemonSet确保全部（或者某些）节点上运行一个Pod副本。当有新节点加入集群时，也会为它们新增一个Pod。当节点从集群中移除时，这些Pod也会被回收，删除DaemonSet将会删除它创建的所有Pod。使用DaemonSet的一些典型用法：运行集群存储daemon（守护进程），例如在每个节点上运行Glusterd、Ceph等。在每个节点运行日志收集daemon，例如Fluentd、Logstash。在每个节点运行监控daemon，比如Prometheus Node Exporter、Collectd、Datadog代理、New Relic代理或Ganglia gmond。

如果指定了.spec.template.spec.nodeSelector，DaemonSet Controller将在与Node Selector（节点选择器）匹配的节点上创建Pod，比如部署在磁盘类型为ssd的节点上（需要提前给节点定义标签Label）：

在生产环境中，公司业务的应用程序一般无须使用DaemonSet部署，一般情况下只有像Fluentd（日志收集）、Ingress（集群服务入口）、Calico（集群网络组件）、Node-Exporter（监控数据采集）等才需要使用DaemonSet部署到每个节点。本节只演示DaemonSet的使用。

相对于Secret，ConfigMap更倾向于存储和共享非敏感、未加密的配置信息，如果要在集群中使用敏感信息，最好使用Secret。

可以使用kubectl create configmap命令从同一个目录中的多个文件创建ConfigMap。创建一个配置文件目录并且下载两个文件作为测试配置文件：

Secret对象类型用来保存敏感信息，例如密码、令牌和SSH Key，将这些信息放在Secret中比较安全和灵活。用户可以创建Secret并且引用到Pod中，比如使用Secret初始化Redis、MySQL等密码。

在拉取私有镜像库中的镜像时，可能需要认证后才可拉取，此时可以使用imagePullSecret将包含Docker镜像注册表密码的Secret传递给Kubelet，然后即可拉取私有镜像。

HPA（Horizontal Pod Autoscaler，水平Pod自动伸缩器）可根据观察到的CPU、内存使用率或自定义度量标准来自动扩展或缩容Pod的数量。HPA不适用于无法缩放的对象，比如DaemonSet。

比如公司网站流量突然升高，此时之前创建的Pod已不足以撑住所有的访问，而运维人员也不可能24小时守着业务服务，这时就可以通过配置HPA，实现负载过高的情况下自动扩容Pod副本数以分摊高并发的流量，当流量恢复正常后，HPA会自动缩减Pod的数量。

CephFS卷允许将一个已经存在的卷挂载到Pod中，和emptyDir卷不同的是，当移除Pod时，CephFS卷的内容不会被删除，这意味着可以将数据预先放置在CephFS卷中，并且可以在Pod之间切换该数据。CephFS卷可以被多个写设备同时挂载。

和上述volume不同的是，如果删除Pod，emptyDir卷中的数据也将被删除，一般emptyDir卷用于Pod中的不同Container共享数据。它可以被挂载到相同或不同的路径上。

hostPath卷可将节点上的文件或目录挂载到Pod上，用于Pod自定义日志输出或访问Docker内部的容器等。使用hostPath卷的示例。将主机的/data目录挂载到Pod的/test-pd目录：

PersistentVolume（简称PV）是由管理员设置的存储，它同样是集群中的一类资源，PV是容量插件，如Volumes（卷），但其生命周期独立使用PV的任何Pod，PV的创建可使用NFS、iSCSI、GFS、CEPH等。

回收策略会告诉PV如何处理该卷，目前卷可以保留、回收或删除。Retain：保留，该策略允许手动回收资源，当删除PVC时，PV仍然存在，volume被视为已释放，管理员可以手动回收卷。Recycle：回收，如果volume插件支持，Recycle策略会对卷执行rm -rf清理该PV，并使其可用于下一个新的PVC，但是本策略已弃用，建议使用动态配置。Delete：删除，如果volume插件支持，删除PVC时会同时删除PV，动态卷默认为Delete。

1）iptables代理模式这种模式下kube-proxy会监视Kubernetes Master对Service对象和Endpoints对象的添加和移除。对每个Service它会创建iptables规则，从而捕获到该Service的ClusterIP（虚拟IP）和端口的请求，进而将请求重定向到Service的一组backend中的某个Pod上面。对于每个Endpoints对象，它也会创建iptables规则，这个规则会选择一个backend Pod。

默认的策略是随机选择一个backend，如果要实现基于客户端IP的会话亲和性，可以将service.spec.sessionAffinity的值设置为ClusterIP（默认为None）。

（2）ipvs代理模式在此模式下，kube-proxy监视Kubernetes Service和Endpoint，调用netlink接口以相应地创建ipvs规则，并定期与Kubernetes Service和Endpoint同步ipvs规则，以确保ipvs状态与期望保持一致。访问服务时，流量将被重定向到其中一个后端Pod。与iptables类似，ipvs基于netfilter钩子函数，但是ipvs使用哈希表作为底层数据结构并在内核空间中工作，这意味着ipvs可以更快地重定向流量，并且在同步代理规则时具有更好的性能，此外，ipvs为负载均衡算法提供了更多的选项，例如：rr　轮询lc　最少连接dh　目标哈希sh　源哈希sed　预计延迟最短nq　从不排队

对于应用程序的某些部分（例如前端），一般要将服务公开到集群外部供用户访问。这种情况下都是用Ingress通过域名进行访问。Kubernetes ServiceType（服务类型）主要包括以下几种：ClusterIP　在集群内部使用，默认值，只能从集群中访问。NodePort　在所有节点上打开一个端口，此端口可以代理至后端Pod，可以通过NodePort从集群外部访问集群内的服务，格式为NodeIP:NodePort。LoadBalancer　使用云提供商的负载均衡器公开服务，成本较高。ExternalName　通过返回定义的CNAME别名，没有设置任何类型的代理，需要1.7或更高版本kube-dns支持。

Ingress为Kubernetes集群中的服务提供了入口，可以提供负载均衡、SSL终止和基于名称的虚拟主机，在生产环境中常用的Ingress有Treafik、Nginx、HAProxy、Istio等。

在Kubernetesv 1.1版中添加的Ingress用于从集群外部到集群内部Service的HTTP和HTTPS路由，流量从Internet到Ingress再到Services最后到Pod上，通常情况下，Ingress部署在所有的Node节点上。

Ingress可以配置提供服务外部访问的URL、负载均衡、终止SSL，并提供基于域名的虚拟主机。但Ingress不会暴露任意端口或协议。

RBAC API声明了4种顶级资源对象，即Role、ClusterRole、RoleBinding、ClusterRoleBinding，管理员可以像使用其他API资源一样使用kubectl API调用这些资源对象。例如：kubectl create -f(resource).yml。

Role和ClusterRole的关键区别是，Role是作用于命名空间内的角色，ClusterRole作用于整个集群的角色。在RBAC API中，Role包含表示一组权限的规则。权限纯粹是附加允许的，没有拒绝规则。

ClusterRole也可将上述权限授予作用于整个集群的Role，主要区别是，ClusterRole是集群范围的，因此它们还可以授予对以下内容的访问权限：集群范围的资源（如Node）。非资源端点（如/healthz）。跨所有命名空间的命名空间资源（如Pod）。

RoleBinding作用于命名空间，ClusterRoleBinding作用于集群。

RoleBinding也可以引用ClusterRole来授予对命名空间资源的某些权限。管理员可以为整个集群定义一组公用的ClusterRole，然后在多个命名空间中重复使用。

等待1分钟可以查看执行的任务（Jobs）：

Suspend　如果设置为true，则暂停后续的任务，默认为false。

successfulJobsHistoryLimit　保留多少已完成的任务，按需配置。failedJobsHistoryLimit　保留多少失败的任务。

相对于Linux上的计划任务，Kubernetes的CronJob更具有可配置性，并且对于执行计划任务的环境只需启动相对应的镜像即可。比如，如果需要Go或者PHP环境执行任务，就只需要更改任务的镜像为Go或者PHP即可，而对于Linux上的计划任务，则需要安装相对应的执行环境。此外，Kubernetes的CronJob是创建Pod来执行，更加清晰明了，查看日志也比较方便。可见，Kubernetes的CronJob更加方便和简单。

### 3.1 安装GFS到K8S集群中

Helm是一个由CNCF孵化和管理的项目，用于对需要在K8S上部署复杂应用进行定义、安装和更新。Helm以Chart的方式对应用软件进行描述，可以方便地创建、版本化、共享和发布复杂的应用软件。

Release　在K8S集群上运行一个Chart实例。在同一个集群上，一个Chart可以安装多次，例如有一个MySQL Chart，如果想在服务器上运行两个数据库，就可以基于这个Chart安装两次。每次安装都会生成新的Release，并有独立的Release名称。

Helm由以下两个组件组成：
HelmClinet　客户端，拥有对Repository、Chart、Release等对象的管理能力。
TillerServer　负责客户端指令和K8S集群之间的交互，根据Chart的定义生成和管理各种K8S的资源对象。

### 3.2 安装Helm到K8S集群中

在生产环境中，对Redis集群实现持久化部署并不是必须的，可以采用hostPath模式将宿主机的本地目录挂载至Redis存储目录，再加上Pod互斥，不让Redis实例部署在同一个宿主机上，之后再利用节点亲和力，尽量将Redis实例部署在原有的宿主机上，此种方式和直接在宿主机上部署Redis并无太大区别，并且实现了Redis的自动容灾功能。

### 3.5 安装GitLab到K8S集群中

Jenkins在DevOps工具链中是核心的流程管理中心，可用来复制串联系统的构建流程、测试流程、镜像制作流程、部署流程等。

### 3.8 安装Prometheus+Grafana到K8S集群中

日志的采集使用Fluentd，需要在每个收集日志的宿主机上部署一个Fluentd容器，然后对容器的日志进行收集，默认的日志输出在宿主机的/var/lib/docker目录下。

### 第4章 持续集成与持续部署

CI（Continuous Integration，持续集成）/CD（Continuous Delivery，持续交付）是一种通过在应用开发阶段引入自动化来频繁向客户交付应用的方法。CI/CD的核心概念是持续集成、持续交付和持续部署。作为一个面向开发和运营团队的解决方案，CI/CD主要针对在集成新代码时所引发的问题（亦称“集成地狱”）。

持续交付通常是指开发人员对应用的更改会自动进行错误测试并上传到存储库（如GitLab或容器注册表），然后由运维团队将其部署到实时生产环境中，旨在解决开发和运维团队之间可见性及沟通较差的问题，因此持续交付的目的就是确保尽可能减少部署新代码时所需的工作量。

### 4.1 CI/CD介绍

Pipeline是用户定义的一个持续提交（CD）流水线模型。流水线的代码定义了整个的构建过程，包括构建、测试和交付应用程序的阶段。另外，Pipeline块是声明式流水线语法的关键部分。

3．Stage（阶段）
Stage块定义了在整个流水线的执行任务中概念不同的子集（比如Build、Test、Deploy阶段），它被许多插件用于可视化Jenkins流水线当前的状态／进展。

4．Step（步骤）
本质上是指通过一个单一的任务告诉Jenkins在特定的时间点需要做什么，比如要执行shell命令，可以使用sh SHELL_COMMAND。

### 4.4 Jenkinsfile的使用

（4）如有配置钩子，推送（Push）代码会自动触发Jenkins构建，如没有配置钩子，需要手动构建。

（9）Jenkins再次控制Kubernetes进行最新的镜像部署。

### 4.7 自动化构建NodeJS应用

Spring Cloud是基于Spring Boot的一整套实现微服务的框架，目前已经被很多公司用于快速开发及迭代业务应用。Spring Cloud可将公司大项目拆分成多个独立功能的微服务，减少了升级某个业务功能对整个业务的影响。Spring Cloud提供了开发所需的分布式会话、配置管理、服务发现、断路器、智能路由、控制总线和全局锁等组件。

Config作为Spring Cloud的统一配置中心，可以对各个应用的配置进行统一管理，在应用启动时通过-Dspring.profiles.active=profilename参数指定对应的配置文件，即可读取到相应的配置。在实际使用时，Config Server应该第一个被部署，以便其他应用能成功读取到相对应的配置。

### 4.8 自动化构建Spring Cloud应用

Webhook是用于自动化触发Jenkins进行构建的工具，一般开发提交代码后，会自动触发Jenkins进行构建，不用进入到Jenkins中执行构建。

上述配置是在K8S-ci-cd分支推送（Push）和合并（Merge）代码后，该配置会触发Jenkins进行自动构建，可按需配置。

### 第5章 Nginx Ingress安装与配置

通常访问一个业务的流程为：
（1）用户在浏览器中输入域名。
（2）域名解析至业务的入口（一般为外部负载均衡器，比如阿里云的SLB）。
（3）外部负载均衡器反向代理至Kubernetes的入口（一般为Ingress）。
（4）通过Ingress再到对应的Service上。
（5）最后到达Service对应的某一个Pod上。
可见，在一般情况下，Ingress主要是一个用于Kubernetes集群业务的入口。可以使用Traefik、Istio、Nginx、HAProxy作为Ingress，因为相对于其他Ingress，管理人员更熟悉Nginx或者HAProxy作为Ingress

### 5.1 Nginx Ingress的安装

由于业务Pod一般部署在Node节点上，所以可以在每个Node上部署一个Nginx Ingress，然后将域名解析至任意一个Node上即可访问业务Pod。可以使用DaemonSet将Nginx Ingress部署至每个Node节点。

### 5.2 Nginx Ingress的简单使用

创建Ingress指向上面创建的Service：

假如公司要实现蓝绿发布，可以先部署新版本的Web容器，比如部署v2版本的Web，访问该Web，会返回v2：

更新Ingress指向Webv2的容器：

### 5.4 Nginx Ingress Rewrite

Rewrite主要用于地址重写，比如访问nginx.test.com/rewrite跳转到nginx.test.com，访问nginx.test.com/rewrite/foo会跳转到nginx.test.com/foo等。

### 5.5 Nginx Ingress错误代码重定向

节主要演示当访问链接返回值为404、503等错误时，如何自动跳转到自定义的错误页面。

### 5.7 Nginx Ingress匹配请求头

请求头匹配主要用于匹配用户来源（比如使用iPhone或者iPad的用户和使用手机或者电脑的用户），然后将其访问转发到对应的业务上。

### 5.8 Nginx Ingress基本认证

有些网站可能需要通过密码来访问，对于这类网站可以使用Nginx的basic-auth设置密码访问，

### 5.9 Nginx Ingress黑／白名单

配置黑名单禁止某一个或某一段IP，需要在Nginx Ingress的ConfigMap中配置，具体方法如下。

### 5.11 使用Nginx实现灰度／金丝雀发布

本节演示利用Nginx简单实现灰度发布。5.11.1　创建v1版本

此时通过nginx.ingress.kubernetes.io/canary-weight: "10"设置的权重是10，即v1:v2为9:1。

使用Ruby

更改权重，将v2权重改为20：

以上演示了简单的灰度发布，根据业务需求匹配请求头等内容，读者在实际使用过程中可以根据自己的业务需求进行配置。

### 5.12 小结

本章介绍使用了Nginx Ingress的配置规则，Ingress作为集群的入口，可以在Ingress上进行简单的流量、权限控制等。

### 第6章 Server Mesh服务网格

专用基础设施层：独立的运行单元。
包括数据层和控制层：数据层负责交付应用请求，控制层控制服务如何通信。
轻量级透明代理：实现形式为轻量级网络代理。
处理服务间通信：主要目的是实现复杂网络中服务间通信。
可靠地交付服务请求：提供网络弹性机制，确保可靠交付请求。
与服务部署一起，但服务无须感知：尽管跟应用部署在一起，但对应用是透明的。

### 6.2 服务网格产品

Envoy：同Linkerd一样，Envoy也是一款高性能的网络代理程序，为云原生应用而设计。

Istio：Istio受Google、IBM、Lyft及RadHat等公司的大力支持和推广，于2017年5月发布，底层为Envoy。

数据平面由一组sidecar方式部署的智能代理（Envoy）组成，这些代理可以调节和控制微服务及Mixer之间所有的网络通信。
控制平面负责管理和配置代理来路由流量，此外控制平面配置Mixer以实施策略和收集遥测数据。

Istio使用Envoy代理的扩展版本，Envoy是以C++开发的高性能代理，用于调解服务网格中所有服务的所有入站和出站流量。Envoy内置功能如下：
动态服务发现
负载均衡
TLS终止
HTTP/2 & gRPC代理
熔断器
健康检查、基于百分比流量拆分的灰度发布
故障注入
丰富的度量指标

目前仅允许3种负载平衡模式：轮询、随机和带权重的最少请求。

Istio能在不杀死Pod的情况下将协议特定的故障注入网络中，在TCP层制造数据包的延迟或损坏。无论网络级别的故障如何，应用层观察到的故障都是一样的，并且可以在应用层注入更有意义的故障，例如HTTP错误代码，以检验和改善应用的弹性。