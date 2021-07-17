## 容器云运维实战：Docker与Kubernetes集群
> 黄靖钧 冯立灿

### 作者简介

全栈开发者。长期以来一直使用容器技术作为应用部署方案，在Docker容器实战方面经验丰富。曾参与多个PaaS与CaaS（容器即服务）项目开发，现从事Serverless与SDN等领域的研究。

### 内容简介

Linux系统中传统服务器运维的基础知识以及集群管理工

容器技术在DevOps中的实际应用场景；

### 1.1 Linux基础

systemd是目前Linux系统中最流行的初始化系统之一，能提高系统的启动效率与质量，它不仅可以让系统进程并行启动，还能很好地守护init进程，减少系统内存的不必要开销。

Linux系统启动要执行的程序是非常多的，比如挂载文件系统、加载硬件设备、启动后台服务、激活交换分区、启动用户程序等。每一个执行任务被systemd称为一个配置单元（Unit）。换句话来说，挂载一个文件系统、加载硬件设备、启动系统后台服务均被称为一个配置单元。

systemctl是一个systemd工具，主要负责控制systemd系统和服务管理器，systemctl工具集成了诸多的命令，使得管理员更好地管理Linux系统。

### 2.1 高可用集群基础

高可用（High Available, HA）集群通过系统的可靠性（reliability）和可维护性（maintainability）来衡量。通常使用平均无故障时间（MTTF）来衡量系统的可靠性，用平均维修时间（MTTR）来衡量系统的可维护性。

● 99.9%!为(MISSING)一年宕机时间不超过10小时；

共享存储一般有两种模式，一种是通过网络传输数据实现在多台设备上存储数据，以达到数据高可用，另一种是通过直接附加存储（Direct attached storage, DAS）实现在多个硬件设备上存储备份数据文件。

复制备份是最原始有效的“高可用”方案，常见的Rsync等软件就是比较经典的代表，通过对存储的数据文件复制传输还原实现。

故障转移机制（Failover）一般不会单独作为高可用方案使用，通常融合到其他高可用方案中，作为一种辅助的技术。这种技术方案可以把故障设备迅速从服务一线撤下来，然后再逐步排查解决

负载均衡是一个很广泛的概念，但从它的作用来看的确又是一种高效的高可用方案之一。一方面负载均衡可以减轻单一或者多个节点的负载压力，将整体负载均衡地分配到多个节点上去，即便其中一个节点失效，负载工具也会迅速使用备用节点顶替。另一方面，负载工具通过和DNS配合，把域名解析到各个节点，也可以实现跨地域的高可用方案。

集群是当前高可用领域应用最广泛的方案，集群可以和其他任一方案共同完成服务高可用。集群的各节点之间通过网络实现进程间的通信，应用程序可以通过网络共享内存进行消息传送，实现分布式计算。虽然集群也是通过冗余来保证系统高可用性的，但它是侧重服务的冗余，而不是状态的冗余（当然两者都有）。

高可用集群有如下三大特点：
● 高可用性。利用集群管理软件，当主服务器故障时，备份服务器自动接管主服务器的工作，以实现对用户的不间断服务。
● 分布式计算。充分利用集群中每一台计算机的资源，实现复杂运算的并行处理，这种特性并非所有集群都会实现，这通常用于科学计算领域或者分布式计算领域，比如基因分析、化学分析、分布计算等。
● 负载均衡。即把负载压力根据特定算法合理分配到集群中的每一台计算机上，以减轻某些服务器的压力，降低对部分服务器的硬件和软件要求。

### 2.3 LVS负载均衡

LVS的全称是Linux Virtual Server（Linux虚拟服务器）

### 3.3 Docker镜像

上面已经说到了ENTRYPOINT命令，这个命令和CMD很相似，ENTRYPOINT相当于把镜像变成一个固定的命令工具，ENTRYPOINT一般是不可以通过docker run来改变的，而CMD不同，CMD可以通过启动命令修改内容。

可以看到在ENTRYPOINT命令下，容器就像一个echo程序，docker run后续的参数就成了echo的参数。

国内像Daocloud、阿里云、灵雀云、时速云、网易蜂巢等公司都有开放的第三方镜像仓库，不过本节推荐的是一个由中国科学技术大学（简称中科大）搭建的镜像仓库。中科大镜像仓库是官方仓库的镜像缓存，也就是说，它本身不只是一个仓库，还是一个仓库镜像。

我们如果在海外有自己的服务器，并且经常有用不完的流量，不妨把那些闲置资源打造成一个镜像加速器。要实现这一功能只需要一个命令：

如果你是个人开发者，没有公开仓库分享的需求，那么搭建一个自己的仓库就非常简单了，只需要运行一个容器就可以实现私有仓库的搭建：
￼
    user@ops-admin:~$ docker run -d -p 5000:5000--restart=always --name registry￼
    registry:2

### 4.2 Docker网络模式

这个模式表示不为容器配置任何网络功能，启用该模式只需要在启动时添加--net=none即可。使用该命令启动的容器完全失去网络功能，即便设置了网络参数，

这个模式表示与另一个运行中的容器共享一个Network Namespace，共享意味着拥有相同的网络视图

--net=container:nginx

对比上面两个容器的eth0信息，可以发现网络配置完全相同，因为它们使用的是同一个Network Namespace。使用cat/etc/hosts可以看到两个容器的hosts文件都拥有同一个hostname：

它可以与主机共享Root Network Namespace，容器有完整的权限操纵主机的网络配置，出于安全考虑，不推荐使用这种模式。

它基于Docker容器技术，为了有更好的使用体验，启动Eclipse Che时必须使用host模式。
启动host模式非常简单，依旧是在docker run中加入--net=host参数即可。

这个容器使用了host模式，因此可以操作宿主机的网络配置，但这是一件十分危险的事情，请慎用。

bridge模式是Docker默认的网络模式，属于一种NAT网络模型，

每个容器使用bridge模式启动时，Docker都会为容器创建一对虚拟网络接口（veth pair）设备，这对设备一端在容器的Network Namespace，另一端在docker0，这样就实现了容器与宿主机之间的通信（就像我们上面手动配置网络的例子一样）。

在bridge模式下，Docker容器与外部网络通信都是通过iptables规则控制的，这也是Docker网络性能低下的一个重要原因。使用iptables -vnL -t nat可以查看NAT表，在Chain DOCKER中可以看到容器桥接的规则。

这是Docker原生的跨主机多子网网络模型，当创建一个新的网络时，Docker会在主机创建一个Network Namespace, Network Namespace内有一个网桥，网桥上有一个vxlan接口，每个网络占用一个vxlan ID，当容器被添加到网络中时，Docker会分配一对veth网卡设备，与bridge模式类似，一端在容器里面。另一端在本地的Network Namespace中。

### 4.3 Docker网络配置

其实容器中的主机名和DNS配置信息都是通过三个系统配置文件来维护的，这些文件就放在“初始化层”中，分别是：/etc/hosts、/etc/resolv.conf、/etc/hostname。
启动一个容器的时候，在容器中使用mount命令可以查看三个文件的挂载信息：

### 7.2 容器编排调度

在容器集群中，服务发现可以让容器在没有管理员干预的情况下了解运行状态，可以自行发现并注册各种组件的运行状态，一旦注册成功便可以在服务发现系统上找到连接该组件的方法，以便各组件之间的交互通畅顺利。服务发现通常是全局分布式配置存储服务，可以存储基础设施中任意的服务配置信息。

● etcd:etcd是一个服务发现项目，提供全局分布式键值对存储项目，它来自CoreOS团队。
● consul：是一个服务发现项目，提供全局分布式键值对存储项目，它来自HashiCorp公司。
● zookeeper：是一个服务发现项目，提供全局分布式键值对存储项目。

### 7.6 监控与日志

来自Google的cAdvisor可以说是监控Docker集群的不二选择，目前Kubernetes已经集成了cAdvisor组件，部署Kubernetes集群后可以直接使用cAdvisor监控集群状态。

由于cAdvisor是将数据缓存在内存中，数据展示能力有限，故一般我们选择把cAdvisor+Influxdb+Grafana三者结合起来，实现一个集监控、收集、显示于一体的监控平台。

### 7.8 Docker持续集成

Drone使用SQLite作为数据库，资源占用非常小

### 8.4 Kubernetes命令行详解

回滚到先前的部署：
￼
    kubectl rollout undo deployment/abc
回滚到daemonset第3个修订版：
￼
    kubectl rollout undo daemonset/abc --to-revision=3

将文件和目录复制到容器中。注意，cp命令要求容器中存在tar命令，如果tar不存在，kubectl cp将执行失败。

### 9.1 Pod详解

restartPolicy表示Pod的重启策略，默认值为Always。
● Always:Pod一旦终止运行，则无论容器是如何终止的都将自动重启容器。
● Onfailure：当Pod以非零退出码终止时自动重启容器。退出码为0则不重启（正常退出）。
● Never:Pod终止后不重启容器。

nodeSelector表示将该Pod调度到包含这些label的Node上，以键值对格式指定，在后面的调度实战中会使用到。

hostNetwork表示是否使用主机网络模式，默认为false。如果设置为true，则表示容器使用宿主机网络，不再使用Docker网桥，这样的话，这个Pod只能在宿主机上启动一个实例。

．升级Pod
使用kubectl replace -f /path/nginx.yml可以更新一个Pod。但Pod的很多属性是无法修改的，所以更新Pod时修改配置文件后往往没有改变Pod的属性，可以使用--force强制更新，这种操作等同于重建一个Pod。

这种替换Pod的方式并不适用于大规模集群，为了实现一个服务的滚动升级，往往有大量的Pods需要升级，而且还要保证服务不间断，因此滚动升级可以通过执行kubectl rolling-update命令

### 9.2 Service详解

服务要让外部访问，有两种方式，一种是使用NodePort，另一种是负载均衡服务。

在通常情况下，service和pod的IP仅可在集群内部访问。集群外部的请求需要通过负载均衡转发到service在节点暴露的NodePort上，然后再由kube-proxy将其转发给相关的Pod。而Ingress就是为进入集群的请求提供路由规则的集合。
Ingress可以给service提供集群外部访问的URL、负载均衡、SSL终止、HTTP路由等。为了配置这些Ingress规则，集群管理员需要部署一个Ingress controller，它监听Ingress和service的变化，并根据规则配置负载均衡并提供访问入口。

为什么需要Ingress，因为在对外访问的时候，NodePort类型需要在外部搭建额外的负载均衡，而LoadBalancer要求kubernetes必须跑在特定的云服务提供商上面。

每个Ingress都需要配置rules，目前Kubernetes仅支持http规则。上面的示例表示请求/testpath时转发到服务test的80端口中。

虚拟主机Ingress即根据名字的不同转发到不同的后端服务上，而它们共用同一个IP地址，一个基于Host header路由请求的Ingress如下：

### 9.3 集群进阶

LimitRange资源对象可以限制特定namespace下所有对象使用的资源

kubelet会进行垃圾清理工作，即定期清理过期的镜像和死亡的容器。
不推荐使用其他管理工具或手工进行容器和镜像的清理，因为kubelet需要通过容器来判断pod的运行状态，如果使用其他方式清除容器，有可能影响kubelet的正常工作。

kubelet定时执行容器清理，每次根据表9-3中的3个参数选择容器进行删除，通常情况下优先删除创建时间最久的已退出容器，kubelet不会删除非Kubernetes管理的容器。

镜像清理的策略是：当硬盘空间使用率超过一定的值就开始执行，kubelet执行清理的时候优先清理最久没被使用的镜像。磁盘空间使用率的阈值通过kubelet的启动参数--image-gc-high-threshold和--image-gc-low-threshold指定。

### 9.4 监控与日志

Heapster是Kubernetes集群的容器集群监控和性能分析工具，它可以很容易扩展到其他集群管理解决方案。在Kubernetes中，cAdvisor agent负责在集群节点运行，收集节点以及监控容器的数据。Heapster则是一个汇总角色，将每个Node上的cAdvisor的数据进行汇总，然后导入到第三方工具（

Prometheus的核心是一个时间序列数据库，我们可以通过它抓取和存储数据，并通过Prometheus定义的一些查询语句来获取我们需要的数据。

Prometheus架构如图9-1所示，整个系统工作流程大体可以概述为：Prometheus Server通过拉取（pull）的方式从监控目标的指标（metrics）获取数据，或者通过中间网关（Pushgateway）间接地拉取监控目标推送给网关的数据，后面这种情况一般出现于监控目标的网络无法直接联通Prometheus Server的时候，通过网关中转监控数据是一个常见的方法。

Prometheus Server在本地存储抓取到的数据，通过一定规则进行清理和整理数据，然后把得到的结果存储起来。接下来各种Web UI就可以通过Server提供API，使用PromQL查询语言获取任意监控数据了。当Server监测到有异常时会推送报警给Alertmanager, Alertmanager处理各类报警信息之后，再把报警信息分别发送给相关人员。

● Prometheus Server：用于收集和存储时间序列数据。

● Client Library：客户端库，为需要监控的服务生成相应的指标并暴露给Prometheus server。当Prometheus server拉取时，直接返回实时状态的指标。

● Push Gateway：主要用于短期任务。由于这类任务存在时间较短，可能在Prometheus拉取之前就消失了。为此，这次任务可以直接向Prometheus server端推送它们的指标。这种方式主要用于服务层面的指标，对于机器层面的指标，则需要使用node exporter。

Prometheus客户端库主要提供4种主要的指标类型：
Counter
● 一种累加的指标，典型的应用如请求的个数、结束的任务数、出现的错误数等。