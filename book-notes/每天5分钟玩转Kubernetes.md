## 每天5分钟玩转Kubernetes
> CloudMan

### 内容简介

Kubernetes是容器编排引擎的事实标准，是继大数据、云计算和Docker之后又一热门技术，而且未来相当一段时间内都会非常流行。对于IT行业来说，这是一项非常有价值的技术。对于IT从业者来说，掌握容器技术既是市场的需要，也是提升自我价值的重要途径。

服务软件开发人员，以及IT实施和运维工程师等相关人

### 作者简介

CloudMan希望通过本书大幅降低学习难度，帮助大家系统地学习和掌握Kubernetes。

### 前 言

AWS、Azure、Google、阿里云、腾讯云等主流公有云提供的是基于Kubernetes的容器服务。Rancher、CoreOS、IBM、Mirantis、Oracle、Red Hat、VMWare等无数厂商也在大力研发和推广基于Kubernetes的容器CaaS或PaaS产品。可以说，Kubernetes是当前容器行业最热门的。

每一轮新技术的兴起，无论对公司还是个人既是机会也是挑战。这项新技术未来必将成为主流，那么作为IT从业者，正确的做法就是尽快掌握。因为：

（1）新技术意味着新的市场和新的需求。初期掌握这种技术的人不是很多，而市场需求会越来越大，因而会形成供不应求的卖方市场，物以稀为贵，这对技术人员将是一个难得的价值提升机会。

对于Kubernetes这项平台级技术，覆盖的技术范围非常广，包括计算、网络、存储、高可用、监控、日志管理等多个方面，要掌握这些新技术对IT老兵尚有不小难度，更别说新人了。

基于容器的微服务架构（Microservice Architecture）会逐渐成为开发应用系统的主流，而Kubernetes将是运行微服务应用的理想平台，市场将需要大量具备Kubernetes技能的应用程序开发人员。

### 第1章 先把Kubernetes跑起来

Kubernetes（K8s）是Google在2014年发布的一个开源项目。
据说Google的数据中心里运行着20多亿个容器，而且Google十年前就开始使用容器技术。

### 1.1 先跑起来

创建Kubernetes集群、部署应用、访问应用、扩展应用、更新应用等最常见的使用场景，迅速建立感性认识。

### 1.2 创建Kubernetes集群

执行kubectl cluster-info查看集群信息，

### 1.4 访问应用

8080端口已经映射到host01的32320端口，端口号是随机分配的

### 1.5 Scale应用

Scale

### 1.6 滚动更新

如果要回退到v1版本也很容易，执行kubectl rollout undo命令

### 第2章 重要概念

Pod中的所有容器使用同一个网络namespace，即相同的IP地址和Port空间。它们可以直接用localhost通信。同样的，这些容器可以共享存储，当Kubernetes挂载volume到Pod，本质上是将volume挂载到Pod中的每一个容器。

Kubernetes通常不会直接创建Pod，而是通过Controller来管理Pod的。Controller中定义了Pod的部署特性，比如有几个副本、在什么样的Node上运行等。为了满足不同的业务场景，

Kubernetes提供了多种Controller，包括Deployment、ReplicaSet、DaemonSet、StatefuleSet、Job等，我们逐一讨论。

Deployment可以管理Pod的多个副本，并确保Pod按照期望的状态运行。
（2）ReplicaSet实现了Pod的多副本管理。使用Deployment时会自动创建ReplicaSet，也就是说Deployment是通过ReplicaSet来管理Pod的多个副本的

（4）StatefuleSet能够保证Pod的每个副本在整个生命周期中名称是不变的，而其他Controller不提供这个功能。当某个Pod发生故障需要删除并重新启动时，Pod的名称会发生变化，同时StatefuleSet会保证副本按照固定的顺序启动、更新或者删除。
（5）Job用于运行结束就删除的应用，而其他Controller中的Pod通常是长期持续运行。

Namespace可以将一个物理的Cluster逻辑上划分成多个虚拟Cluster，每个Cluster就是一个Namespace。不同Namespace里的资源是完全隔离的。

•  kube-system：Kubernetes自己创建的系统资源将放到这个Namespace中。

### 第3章 部署Kubernetes Cluster

注意：Kubernetes几乎所有的安装组件和Docker镜像都放在Google自己的网站上，这对国内的同学可能是个不小的障碍。建议是：网络障碍都必须想办法克服，不然连Kubernetes的门都进不了。

### 3.2 安装kubelet、kubeadm和kubectl

•  kubelet运行在Cluster所有节点上，负责启动Pod和容器。
•  kubeadm用于初始化Cluster。
•  kubectl是Kubernetes命令行工具。通过kubectl可以部署和管理应用，查看各种资源，创建、删除和更新各种组件。

### 3.3 用kubeadm创建Cluster

要让Kubernetes Cluster能够工作，必须安装Pod网络，否则Pod之间无法通信。
Kubernetes支持多种网络方案，这里我们先使用flannel，后面还会讨论Canal。

Pending、ContainerCreating、ImagePullBackOff都表明Pod没有就绪，Running才是就绪状态。我们可以通过kubectl describe pod <Pod Name>查看Pod的具体情况

### 4.1 Master节点

1．API Server（kube-apiserver）
API Server提供HTTP/HTTPS RESTful API，即Kubernetes API。API Server是Kubernetes Cluster的前端接口，各种客户端工具（CLI或UI）以及Kubernetes其他组件可以通过它管理Cluster的各种资源。

Scheduler负责决定将Pod放在哪个Node上运行。Scheduler在调度时会充分考虑Cluster的拓扑结构，当前各个节点的负载，以及应用对高可用、性能、数据亲和性的需求。

Controller Manager负责管理Cluster各种资源，保证资源处于预期的状态。Controller Manager由多种controller组成，包括replication controller、endpoints controller、namespace controller、serviceaccounts controller等。

etcd负责保存Kubernetes Cluster的配置信息和各种资源的状态信息。当数据发生变化时，etcd会快速地通知Kubernetes相关组件。

Pod要能够相互通信，Kubernetes Cluster必须部署Pod网络，flannel是其中一个可选方案。

### 4.2 Node节点

Node上运行的Kubernetes组件有kubelet、kube-proxy和Pod网络（例如flannel）

kubelet是Node的agent，当Scheduler确定在某个Node上运行Pod后，会将Pod的具体配置信息（image、volume等）发送给该节点的kubelet，kubelet根据这些信息创建和运行容器，并向Master报告运行状态。

kube-proxy


service在逻辑上代表了后端的多个Pod，外界通过service访问Pod。service接收到的请求是如何转发到Pod的呢？这就是kube-proxy要完成的工作。
每个Node都会运行kube-proxy服务，它负责将访问service的TCP/UPD数据流转发到后端的容器。如果有多个副本，kube-proxy会实现负载均衡。

### 4.3 完整的架构图

可能会问：为什么k8s-master上也有kubelet和kube-proxy呢？
这是因为Master上也可以运行应用，即Master同时也是一个Node。

### 4.4 用例子把它们串起来

①kubectl发送部署请求到API Server。②API Server通知Controller Manager创建一个deployment资源。③Scheduler执行调度任务，将两个副本Pod分发到k8s-node1和k8s-node2。④k8s-node1和k8s-node2上的kubectl在各自的节点上创建并运行Pod。

（1）应用的配置和当前状态信息保存在etcd中，执行kubectl get pod时API Server会从etcd中读取这些数据。

（2）flannel会为每个Pod都分配IP。因为没有创建service，所以目前kube-proxy还没参与进来。

### 5.1 Deployment

②kind是要创建的资源类型，这里是Deployment。
③metadata是该资源的元数据，name是必需的元数据项。
④spec部分是该Deployment的规格说明。

### 5.2 DaemonSet

DaemonSet的不同之处在于：每个Node上最多只能运行一个副本。

（3）在每个节点上运行监控Daemon，比如Prometheus Node Exporter或collectd。

①直接使用Host的网络。

### 5.3 Job

Linux中有cron程序定时执行任务，Kubernetes的CronJob提供了类似的功能，可以定时执行Job。

### 6.1 创建Service

⑤将Service的8080端口映射到Pod的80端口，使用TCP协议。


⑤将Service的8080端口映射到Pod的80端口，使用TCP协议。

通过kubectl describe可以查看httpd-svc与Pod的对应关系，如

### 6.2 Cluster IP底层实现

Cluster IP是一个虚拟IP，是由Kubernetes节点上的iptables规则管理的。

可以通过iptables-save命令打印出当前节点的iptables规则

（1）如果Cluster内的Pod（源地址来自10.244.0.0/16）要访问httpd-svc，则允许。
（2）其他源地址访问httpd-svc，跳转到规则KUBE-SVC-RL3JAE4GN7VOG DGP

（1）1/3的概率跳转到规则KUBE-SEP-C5KB52P4BBJQ35PH。

Cluster的每一个节点都配置了相同的iptables规则，这样就确保了整个Cluster都能够通过Service的Cluster IP访问Service

### 6.3 DNS访问Service

kube-dns是一个DNS服务器。每当有新的Service被创建，kube-dns会添加该Service的DNS记录。Cluster中的Pod可以通过<SERVICE_NAME>.<NAMESPACE_NAME>访问Service。

httpd-svc.default.svc.cluster.local是httpd-svc的完整域名。
如果要访问其他n

### 6.4 外网如何访问Service

Service通过Cluster内部的IP对外提供服务，只有Cluster内的节点和Pod可访问，这是默认的Service类型，前面实验中的Service都是ClusterIP。

### 7.2 回滚

--record的作用是将当前命令记录到revision记录中，这样我们就可以知道每个revison对应的是哪个配置文件了。通过kubectl rollout history deployment httpd查看revison历史记录，如图7-9所示。

kubectl rollout undo deployment httpd --to-revision=1，

### 8.2 Liveness探测

Liveness探测让用户可以自定义判断容器是否健康的条件。如果探测失败，Kubernetes就会重启容器。

（2）initialDelaySeconds：10指定容器启动10之后开始执行Liveness探测，我们一般会根据应用启动的准备时间来设置。比如某个应用正常启动要花30秒，那么initialDelaySeconds的值就应该大于30。

（3）periodSeconds：5指定每5秒执行一次Liveness探测。Kubernetes如果连续执行3次Liveness探测均失败，则会杀掉并重启容器。

### 8.3 Readiness探测

用户通过Liveness探测可以告诉Kubernetes什么时候通过重启容器实现自愈；Readiness探测则是告诉Kubernetes什么时候可以将容器加入到Service负载均衡池中，对外提供服务。

（2）15秒后（initialDelaySeconds + periodSeconds），第一次进行Readiness探测并成功返回，设置READY为可用。
（3）30秒后，/tmp/healthy被删除，连续3次Readiness探测均失败后，READY被设置为不可用。

通过kubectl describe pod readiness也可以看到Readiness探测失败的日志，

### 9.1 Volume

容器销毁时，保存在容器内部文件系统中的数据都会被清除。
为了持久化保存容器的数据，可以使用Kubernetes Volume。
Volume的生命周期独立于容器，Pod中的容器可能被销毁和重建，但Volume会被保留。
本质上，Kubernetes Volume是一个目录，这一点与Docker Volume类似。当Volume被mount到Pod，Pod中的所有容器都可以访问这个Volume。Kubernetes Volume也支持多种backend类型，

Volume提供了对各种backend的抽象，容器在使用Volume读写数据的时候不需要关心数据到底是存放在本地节点的文件系统中还是云硬盘上。对它来说，所有类型的Volume都只是一个目录。

emptyDir是最基础的Volume类型。正如其名字所示，一个emptyDir Volume是Host上的一个空目录。

emptyDir Volume的生命周期与Pod一致。
Pod中的所有容器都可以共享Volume，它们可以指定各自的mount路径。下面通过例子来实践emptyDir，配置文件如图9-1所示。

emptyDir是Host上创建的临时目录，其优点是能够方便地为Pod中的容器提供共享存储，不需要额外的配置。它不具备持久性，如果Pod不存在了，emptyDir也就没有了。根据这个特性，emptyDir特别适合Pod中的容器需要临时共享存储空间的场景，比如前面的生产者消费者用例。

hostPath Volume的作用是将Docker Host文件系统中已经存在的目录mount给Pod的容器。

这里定义了三个hostPath：volume k8s、certs和pki，分别对应Host目录/etc/kubernetes、/etc/ssl/certs和/etc/pki。

### 9.2 PersistentVolume & PersistentVolumeClaim

PersistentVolume（PV）是外部存储系统中的一块存储空间，由管理员创建和维护。与Volume一样，PV具有持久性，生命周期独立于Pod。

PersistentVolumeClaim （PVC）是对PV的申请（Claim）。PVC通常由普通用户创建和维护。需要为Pod分配存储资源时，用户可以创建一个PVC，指明存储资源的容量大小和访问模式（比如只读）等信息，Kubernetes会查找并提供满足条件的PV。

②accessModes指定访问模式为ReadWriteOnce，支持的访问模式有3种：ReadWriteOnce表示PV能以read-write模式mount到单个节点，ReadOnlyMany表示PV能以read-only模式mount到多个节点，ReadWriteMany表示PV能以read-write模式mount到多个节点。

③persistentVolumeReclaimPolicy指定当PV的回收策略为Recycle，支持的策略有3种：Retain表示需要管理员手工回收；Recycle表示清除PV中的数据，效果相当于执行rm -rf/thevolume/*；Delete表示删除Storage Provider上的对应存储资源，

④storageClassName指定PV的class为nfs。相当于为PV设置了一个分类，PVC可以指定class申请相应class的PV。

当PVC mypvc1被删除后，我们发现Kubernetes启动了一个新Pod recycler-for-mypv1，这个Pod的作用就是清除PV mypv1的数据。此时mypv1的状态为Released，表示已经解除了与mypvc1的Bound，正在清除数据，不过此时还不可用。
当数据清除完毕，mypv1的状态重新变为Available，此时可以被新的PVC申请

因为PV的回收策略设置为Recycle，所以数据会被清除，但这可能不是我们想要的结果。如果我们希望保留数据，可以将策略设置为Retain，

与之对应的是动态供给（Dynamical Provision），即如果没有满足PVC条件的PV，会动态创建PV。相比静态供给，动态供给有明显的优势：不需要提前创建PV，减少了管理员的工作量，效率高。

### 第10章 Secret & Configmap

Secret会以密文的方式存储数据，避免了直接在配置文件中保存敏感信息。Secret会以Volume的形式被mount到Pod，容器可通过文件的方式使用Secret中的敏感数据；此外，容器也可以环境变量的方式使用这些数据。

### 10.3 在Pod中使用Secret

通过Volume使用Secret，容器必须从文件读取数据，稍显麻烦，Kubernetes还支持通过环境变量使用Secret。

环境变量读取Secret很方便，但无法支撑Secret动态更新。


### 10.5 小结

Secret和ConfigMap支持四种定义方法。Pod在使用它们时，可以选择Volume方式或环境变量方式，不过只有Volume方式支持动态更新。

### 11.1 Why Helm

答案是：Kubernetes能够很好地组织和编排容器，但它缺少一个更高层次的应用打包工具，而Helm就是来干这件事的。

（1）很难管理、编辑和维护如此多的服务。每个服务都有若干配置，缺乏一个更高层次的工具将这些配置组织起来。（2）不容易将这些服务作为一个整体统一发布。部署人员需要首先理解应用都包含哪些服务，然后按照逻辑顺序依次执行kubectlapply，即缺少一种工具来定义应用与服务，以及服务与服务之间的依赖关系。

3）不能高效地共享和重用服务。比如两个应用都要用到MySQL服务，但配置的参数不一样，这两个应用只能分别复制一套标准的MySQL配置文件，修改后通过kubectl apply部署。也就是说，不支持参数化配置和多环境部署。（4）不支持应用级别的版本管理。虽然可以通过kubectl rollout undo进行回滚，但这只能针对单个Deployment，不支持整个应用的回滚。

（5）不支持对部署的应用状态进行验证。比如是否能通过预定义的账号访问MySQL。虽然Kubernetes有健康检查，但那是针对单个容器，我们需要应用（服务）级别的健康检查。

Helm帮助Kubernetes成为微服务架构应用理想的部署平台。

### 11.2 Helm架构

chart是创建一个应用的信息集合，包括各种Kubernetes对象的配置模板、参数定义、依赖关系、文档说明等。chart是应用部署的自包含逻辑单元。可以将chart想象成apt、yum中的软件安装包。release是chart的运行实例，代表了一个正在运行的应用。当chart被安装到Kubernetes集群，就生成一个release。chart能够多次安装到同一个集群，每次安装都是一个release。

### 11.5 chart详解

单个的chart可以非常简单，只用于部署一个服务，比如Memcached。chart也可以很复杂，部署整个应用，比如包含HTTP Servers、Database、消息中间件、Cache等。chart将这些文件放置在预定义的目录结构中，通常整个chart被打成tar包，而且标注上版本信息，便于Helm部署。

chart支持在安装时根据参数进行定制化配置，而values.yaml则提供了这些配置参数的默认值，如图11-24所示。

各类Kubernetes资源的配置模板都放置在这里。Helm会将values.yaml中的参数值注入模板中，生成标准的YAML配置文件。模板是chart最重要的部分，也是Helm最强大的地方。模板增加了应用部署的灵活性，能够适用不同的环境

7）templates/NOTES.txtchart的简易使用文档，chart安装成功后会显示此文档内容

Helm采用了Go语言的模板来编写chart。Go模板非常强大，支持变量、对象、函数、流控制等功能。

helm inspect values可能是更方便的方法

Helm提供了debug的工具：helm lint和helm install --dry-run --debug。helm lint会检测chart的语法，报告错误以及给出建议。

### 11.6 小结

chart是Helm的应用打包格式，它由一组文件和目录构成。其中最重要的是模板，模板中定义了Kubernetes各类资源的配置信息，Helm在部署时通过values.yaml实例化模板。

### 12.1 Kubernetes网络模型

为了保证网络方案的标准化、扩展性和灵活性，Kubernetes采用了Container Networking Interface（CNI）规范。

目前已有多种支持Kubernetes的网络方案，比如Flannel、Calico、Canal、Weave Net等。因为它们都实现了CNI规范，用户无论选择哪种方案，得到的网络模型都一样，即每个Pod都有独立的IP，可以直接通信。区别在于不同方案的底层实现不同，有的采用基于VxLAN的Overlay实现，有的则是Underlay，性能上有区别。再有就是是否支持Network Policy。

### 12.3 Network Policy

Network Policy是Kubernetes的一种资源。Network Policy通过Label选择Pod，并指定其他Pod或外界如何与这些Pod通信。

不过，不是所有的Kubernetes网络方案都支持Network Policy。比如Flannel就不支持，Calico是支持的。

Canal这个开源项目很有意思，它用Flannel实现Kubernetes集群网络，同时又用Calico实现Network Policy。

部署Canal与部署其他Kubernetes网络方案非常类似，都是在执行了kubeadm init初始化Kubernetes集群之后通过kubectl apply安装相应的网络方案。也就是说，没有太好的办法直接切换使用不同的网络方案，基本上只能重新创建集群。

②ingress中定义只有label为access: "true"的Pod才能访问应用。

### 12.4 小结

Network Policy赋予了Kubernetes强大的网络访问控制机制。

### 第13章 Kubernetes Dashboard

Kubernetes还开发了一个基于Web的Dashboard，用户可以用Kubernetes Dashboard部署容器化的应用、监控应用的状态、执行故障排查任务以及管理Kubernetes的各种资源。

### 13.4 典型使用场景

Kubernetes Dashboard界面设计友好，自解释性强，可以看作GUI版的kubectl，更多功能留给大家自己探索。

### 14.1 Weave Scope

Weave Scope是Docker和Kubernetes可视化监控工具。Scope提供了自上而下的集群基础设施和应用的完整视图，用户可以轻松对分布式的容器化应用进行实时监控和问题诊断。

Scope还提供了便捷的在线操作功能，比如选中某个Host，单击>_按钮可以直接在浏览器中打开节点的命令行终端

### 14.3 Prometheus Operator

Prometheus Server负责从Exporter拉取和存储监控数据，并提供一套灵活的查询语言（PromQL）供用户使用。

Exporter负责收集目标对象（host、container等）的性能数据，并通过HTTP接口供Prometheus Server获取

用户可以定义基于监控数据的告警规则，规则会触发告警。一旦Alermanager收到告警，就会通过预定义的方式发出告警通知，支持的方式包括Email、PagerDuty、Webhook等。

Grafana能够与Prometheus无缝集成，提供完美的数据展示能力。

Prometheus Operator的目标是尽可能简化在Kubernetes中部署和维护Prometheus的工作

Operator能够动态更新Prometheus的Target列表，ServiceMonitor就是Target的抽象。比如想监控Kubernetes Scheduler，用户可以创建一个与Scheduler Service相映射的ServiceMonitor对象。Operator则会发现这个新的ServiceMonitor，并将Scheduler的Target添加到Prometheus的监控列表中。
ServiceMonitor也是Prometheus Operator专门开发的一种Kubernetes定制化资源类型。

### 15.1 部署

DaemonSet fluentd-es从每个节点收集日志，然后发送给Elasticsearch

Elasticsearch以StatefulSet资源运行，并通过Service elasticsearch-logging对外提供接口。这里已经将Service的类型通过kubectl edit修改为NodePort。
可通过http://192.168.56.106:32607/验证Elasticsearch已正常工作

### 写在最后

本教程涵盖了Kubernetes最最重要的技术：集群架构、容器化应用部署、Scale Up/Down、滚动更新、监控检查、集群网络、数据管理、监控和日志管理，通过大量的实验探讨了Kubernetes的运行机制。