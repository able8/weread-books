## Linux系统架构与运维实战
> 明哲

### 前言

虚拟化一般分为硬件级虚拟化（Hardware-Level-Virtualization）和操作系统级虚拟化（OS-Level-Virtualization）。硬件级虚拟化是运行在硬件上的虚拟化技术，其管理软件是Hypervisor或Virtual MachineMonitor，需要模拟一个完整的操作系统，也就是通常所说的基于 Hyper-V 的虚拟化技术，VMWare、Xen、VirtualBox、亚马逊AWS和阿里云用的都是这种技术。操作系统级虚拟化是运行在操作系统上的，模拟的是运行在操作系统上的多个不同的进程，并将其封装在一个密闭的容器里，也称为容器化技术。Docker 正是容器虚拟化中目前较流行的一种实现。

随着云概念的出现，越来越多的商家意识到卖硬件是不可能获得长期利润的，只有服务才能持续盈利。

· 软件即服务（Software as a Service，SaaS），通常指在云端为用户提供软件，如CRM系统、邮件系统、在线协作、在线办公等。国内的有道、麦客、Tower都是这个领域的产品。

### 第1章 Linux日常运维管理

load average表示系统负载，load average后面有三个数字，分别表示1分钟、5分钟和15分钟时间段内系统的负载值

可运行 vmstat 命令。使用vmstat 命令可查看CPU、内存、虚拟磁盘、交换分区、I/O磁盘和系统进程的信息

Linux操作系统中有一个服务是logrotate，它是用来切割日志的。为了防止日志无限制地在一个文件中增加，于是产生了这种机制。/var/log/message 文件后加上*会列出很多带有日期的日志文件，这些带有日期的日志文件就是用logrotate切割的，logrotate服务在/etc/logrotate.conf中，使用cat命令可以查看该配置文件中的具体配置信息，操作命令如下。

使用dmesg命令可以把系统中与硬件相关的日志列出来，保存在内存中，注意它并不是某个文件。例如，硬盘损坏、网卡出现问题都会记录在dmesg命令中。使用-c选项可清空日志，清空后遇到某些问题或重启操作系统还会生成日志，操作命令如下。

### 第6章 Linux集群架构

Linux集群从功能上可以分为两大类：高可用集群和负载均衡集群。
高可用集群通常为两台服务器，一台工作，另一台冗余。当提供服务的服务器宕机时，冗余服务器将接替宕机的服务器继续提供服务。

实现高可用集群的开源软件有Heartbeat和Keepalived。

负载均衡集群，需要有一台服务器作为分发器，它负责把用户的请求分发给后端的服务器处理。在负载均衡集群中，除分发器外，就是给用户提供服务的服务器了，这些服务器数量至少是两台。

负载均衡集群由LVS、Keepalived、Haporxy、Nginx等开源软件实现。LVS基于4层（OSI网络7层模型），Nginx 基于 7 层，Haporxy既可以用作 4 层，也可以用作7层。Keepalived的负载均衡功能就是LVS。

LVS这种4层的结构更稳定，能承载的并发量更高，而Nginx这种7层的结构更加灵活，能实现更多的个性化需求。

轮询（Round-Robin，简称 rr）、加权轮询（Weight Round-Robin，简称wrr）、最小连接（Least-Connection，简称lc）、加权最小连接（Weight Least-Connection，简称 wlc）、基于局部性的最小连接（Locality-Based Least Connections，简称 lblc）、带复制的基于局部性最小连接（Locality-Based Least Connections with Replication，简称lblcr）、目标地址散列调度（Destination Hashing，简称dh

### 第7章 Zabbix运维监控

Zabbix是C/S架构，抓取数据是通过客户端抓取的，在客户端必须有服务启动，该服务负责采集数据，数据会主动上报给服务端，也可让服务端连接客户端去抓取数据。客户端分为两种模式，即主动模式和被动模式。

### 第8章 NoSQL非关系型数据库

关系型数据库有一个共同的特点是可以使用SQL语句，而非关系型数据库没有SQL语句。

NoSQL因为没有复杂的数据结构，所以扩展也非常容易，而且还支持分布式。

### 第9章 Jenkins持续化集成

Jenkins，原名 Hudson，2011 年改为现在的名字，它是一个基于 Web 界面平台开源的、可扩展的持续集成、交付、部署（软件/代码的编译、打包、部署）的工具。

### 第10章 Docker容器实践

Docker 对外宣称的作用是“Build，Ship and Run”，Docker要解决的核心问题就是快速地做这三件事情。它通过将运行环境和应用程序打包到一起，来解决部署的环境依赖问题，真正做到跨平台的分发和使用。

最后得出的结论是，要和那些大厂商硬干肯定是不行的，那么干脆就把Docker项目开源了吧。即使赚不到钱，至少也在开源社区得到一个好名声。因此，在 2013 年 3 月，Docker 正式以开源软件的形式被发布了。正是由于这次开源，让容器领域焕发了第二春，截至 2015 年 11 月，Docker 在 GitHub 上收到了25 600个赞，超过 6800次复制，以及拥有超过1100名的贡献者，成为20个最具影响力的GitHub开源项目。

Docker是继Linux之后，最让人感到兴奋的系统层面的开源项目。据

镜像是一个只读的模板，类似于安装系统时用到的ISO文件，通过镜像来完成各种应用的部署。容器可以被启动、开始、停止、删除等，每个容器间都是相互隔离的。

-i 选项表示让容器的标准输入打开，-t 选项表示分配一个伪终端，-d 选项表示后台启动。-i、-t、-d选项需放在镜像名字前面。

### 第11章 搭建Kubernetes集群

K8S主要用于自动化部署、扩展和管理容器应用，提供了资源调度、部署管理、服务发现、扩容缩容、监控等一系列功能，这些功能是将容器推送到生产环境很关键的一步。2

· StatefulSet：适合持久性的应用程序，有唯一的网络标识符（IP），持久存储，能够有序地部署、扩展、删除和滚动更新。

· Job：一次性任务，运行完成后用Pod销毁，不再重新启动新容器，还可以让任务定时运行。

kube-apiserver：Kubernetes API，集群的统一入口，各组件的协调者，以HTTP API提供接口服务，所有对象资源的增、删、改、查和监听操作都交给API Server处理后，再提交给Etcd存储。

Docker或rocket/rkt：运行容器。

Etcd是第三方服务，是分布式键值存储系统，用于保存集群状态，比如保存Pod、Service等对象信息。

在自签TLS证书前需安装生成证书的工具cfssl，该工具极大地简化了证书的生成过程。OpenSSL工具也可以生成证书，但是步骤较多、较烦琐，容易出现问题。cfssl工具能够很方便地生成证书，可生成证书的配置模板，只要稍作修改即可。

不使用证书可以不加密传输，减少传输的时间；加密则会进行双向认证，认证会增加时间开销。但是在生成环境中还是建议使用证书，安全才是第一位的

11.4.5 Flannel集群网络工作原理
Overlay Network即覆盖网络，是在基础的网络上叠加的一种虚拟网络技术模式，该网络中的主机通过虚拟链路实现连接。

VXLAN是Overlay Network技术的一种实现。VXLAN是将源数据包封装到UDP中，并使用基础网络的 IP/MAC 作为外层报文头进行封装，然后在以太网上传输，到达目的地后由隧道端点解封装并将数据发送给目标地址。

Flannel是CoreOS团队针对Kubernetes设计的一个网络规划服务，简单来说，它的功能是让集群中的不同节点主机创建的 Docker 容器都具有全集群唯一的虚拟IP地址。Flannel实质上是Overlay Network的一种，也是将源数据包封装在另一种网络包里面进行路由转发和通信，目前支持 UDP、VXLAN、AWS VPC和GCE路由等数据转发方式。


Flannel 设计的目的就是为集群中的所有节点重新规划 IP 地址的使用规则，从而促使不同节点上的容器能够获得“同属一个内网”且“不重复的”IP地址，让属于不同节点上的容器能够直接通过内网IP通信。

Flannel通过Etcd服务维护了节点间的路由。源主机的Flanneld服务将原本的数据内容进行UDP封装后，根据自己的路由表投递给目的节点的Flanneld服务，数据到达后被解包，接着直接进入目的节点的 flannel0 虚拟网卡，然后被转发到目的主机的Docker0虚拟网卡，最后就像本机容器通信一样由Docker0路由到达目标容器。这样整个数据包的传递就结束了。

Flannel从Etcd中获取到分配的网段后会生成一个Flannel的虚拟网卡

### 第12章 Kubernetes管理维护与运用

YAML是配置文件的格式。YAML文件是由一些易读的字段和指令组成的。K8S使用YAML配置文件需要注意如下事项。

Probe支持以下三种检查方法。
· httpGet：发送HTTP请求，若返回200～400状态码，则表示成功

将容器“杀”死。
· exec：执行shell命令，返回状态码为0表示成功。容器内运行的应用服务如果不是HTTP服务，则可以通过exec执行脚本判断某个文件是否存在。

tcpSocket：发起TCP Socket并建立成功。

使用describe命令可以查看Pod的具体信息，需要关注的是Events（事件），通过Events可以查看nginx-pod执行到了哪一步。使用logs命令可以查看具体输出的日志信息。使用exec命令可以进入容器进一步排查应用层的问题。

Service有三种网络代理模式：Userspace、Iptables和Ipvs。

· ClusterIP是分配的一个内部集群的IP地址，只能在集群内部访问，同NameSpace内的Pod，默认是ServiceType。

NodePort是分配的一个内部集群的IP地址，在每个节点上启用一个端口来暴露服务，可以在集群外部进行访问。访问地址是“＜NodeIP＞：＜NodePort＞”。


· LoadBalancer是分配的一个内部集群的IP地址，在每个节点上启用一个端口来暴露服务，除此之外，Kubernetes会请求底层云平台上的负载均衡器，将每个节点（[NodeIP]：[NodePort]）作为后端添加进去。

Volume 对 Kubernetes 集群应用数据进行管理，有几个常见的类型，分别是emptyDir、hostPath、NFS和GlusterFS。

emptyDir是在主机上创建的临时目录，其优点是能够方便地为Pod中的容器提供共享存储，而不需要进行额外的配置。但它不具备持久性，如果 Pod不存在了，emptyDir也会随之删除。所以，emptyDir适合Pod中的容器需要临时共享存储空间的场景。

PersistentVolume 有两个概念，一个是 PersistentVolume （PV），另一个是PersistentVolumeClaim（PVC）。PV是对后端存储的一种抽象，后端可以是NFS，也可以是GlusterFS。PVC会消费PV，也就是消费后端的存储。将存储进行抽象作为集群的资源进行管理，那么就要创建 PVC 去消费 PV。

PersistentVolume工作流程是：Pod申请PVC作为卷来使用，集群通过PVC查找对应的PV，最终挂载给Pod。


### 第13章 Kubernetes高可用架构和项目案例

dashboard-deployment.yaml是用来创建UI应用的控制器，可以管理Pod副本。dashboard-service.yaml 可以将 UI 进行发布，达到用户访问的目的。

Kubernetes在之前做的演示和实践中采用的服务器是一台Master和五台Node，而在实际的生产环境中需要考虑集群的高可用，集群中的任意一个节点宕掉后，都不会影响整个集群的使用。

Kubernetes的Node服务器已经具备了高可用，因为Pod副本会分布在每个节点上，当任意节点宕掉后，其他节点就会启用，所以，此处我们无须去考虑。要重点考虑的是Master服务器，Master节点宕掉后可能会导致集群不可管理或直接影响某些服务的正常使用，这在实际的生产环境中是不允许的。

综上所述，可以大致理解为从客户端连接LB，LB将请求转发到后端的apiServer中，右边则是Kubernetes的Node，Node连接LB。从图13-23可以看出，高可用架构其实就是一个单独的 LB（负载均衡）代理的 apiServer，而 Node 连接 apiServer实时地进行工作，然后获取API中的一些配置信息，并更新当前的状态。

图 13-23 这种集群架构的好处是，比如在阿里云上部署，因为阿里云有 SLB，所以可以在SLB上做四层的内网负载均衡，这样也不会被收取费用。在企业架构中如果有LB，就可以用现有的LB代理apiServer。


将 Master节点上的/opt/kubernetes/目录下的所有数据文件复制到 Master-2 节点的/opt/目录下，如图 13-26 所示。在初期部署 Kubernetes 集群时，就要考虑后期的可维护性，所以将Kubernetes集群所用到的组件全部放到了/opt/kubernetes/目录下，再去用时会更加方便、快捷。

四层和七层的区别在于，四层是通过IP+Port直接转发的，而七层需要先分区应用层的数据再进行转发。

集群的监控在企业生产环境中是非常重要的，可以观察当前所有集群节点的运行状态和一些性能的指标信息；也可以在一些应用或节点出现问题时，通过监控去分析、定位问题；还可以查阅历史数据图去追溯一些问题。

cAdvisor和Heapster是Google公司自主研发的工具。cAdvisor主要用于容器资源的收集。Heapster 主要用于 Kubernetes 集群节点之间的数据汇总

Kubelet是管理容器和资源的，kube-proxy用于管理网络规则。cAdvisor集成到Kubelet中，默认已经安装，无须再次安装。

一套 ELK 日志开源解决方案，如图 13-51 所示。最左边是Kubernetes的Node，使用Filebeat进行日志收集，然后传输到Logstash中，最后在Kibana中展示。

Filebeat：日志采集工具，从指定的日志中进行增量式的采集，可以将日志传输到Elasticsearch，也可以直接传输到Logstash。

Logstash本身是过滤数据、分析数据的，当企业生产环境中节点非常多时，单个Logstash肯定处理不完数据，可以通过写多个 Logstash 进行分散，当然也可以增加缓存，如使用 Redis 和Kafka平缓网络的传输，这就是一个整体可伸缩的解决方案。