## 公有云容器化指南：腾讯云TKE实战与应用
> 邱宝 冯亮亮

### 前言

这是一个技术井喷的时代，人工智能（AI）、大数据（Big data）、云计算（Cloud）三大方向组成的ABC让每一位IT人都踔厉风发，一往无前。

近几年，容器技术非常火爆，且日趋成熟，众多企业慢慢开始容器化建设，迈向云原生。此外，公有云也逐渐被各大、中、小企业所接受，大家纷纷选择上云，享受云带来的便利。但

部分中小客户得不到专业架构师的指导，导致在上云过程中走了很多不必要的弯路。因此，为了加深中小客户对公有云产品的了解，传授给他们更多专业的上云方式，是很有必要的。

### 1.1 什么是容器技术

自从有了KVM、Xen这样的虚拟化技术，x86服务器的利用率以及管理得到了质的飞越。但是人们也没有停下继续研究更轻量级虚拟化技术的脚步，容器技术就是KVM、Xen之后更优秀的一种虚拟化技术

Container有集装箱、容器的含义，容器是被大家广为接受的译法。不过，我们可以形象地将Container技术类比作集装箱。我们知道，集装箱是用来运载货物的，它是一种按规格标准化的钢制箱子。集装箱的特色在于规格统一，并可以层层重叠，所以能大量放置在远洋轮船上。早期航运是没有集装箱概念的，那时候货物的摆放杂乱无章，很影响出货和运输效率。有了集装箱，货运公司就可以快捷方便地运输货物。码头集装箱如图1-2所示。

然而，随着时间推移，用户发现使用hypervisor越来越多麻烦。为什么？因为对于hypervisor环境来说，每个虚拟机都需要运行一个完整的操作系统，其中安装了大量应用程序。但在实际生产开发环境中，我们更关注的是自己部署的应用程序，如果每次发布都需要部署一个完整操作系统和附带的依赖环境，会降低工作效率。

在2015年，由Google、Docker、CoreOS、IBM、微软、红帽等厂商联合发起了OCI（Open Container Initiative）组织，并于2016年4月推出了第一个开放容器标准。该标准主要包括运行时（Runtime）标准和镜像（Image）标准。这一标准的推出，为容器市场带来稳定性，让企业能更加放心地采用容器技术。用户在打包、部署应用程序后，可以自由选择不同的容器运行时。同时，镜像打包、建立、认证、部署、命名也都能按照统一的规范进行。

1.提高现有应用的安全性和可移植性

通过持续集成（CI）和持续部署（CD），开发人员嵌入代码并通过测试之后，IT团队将新代码集成。作为开发运维的基础，CI/CD创造了一种实时反馈回路机制，持续传输小型迭代，从而加速软件迭代、提高发布质量。CI环境通常是完全自动化的，通过git推送命令触发测试，测试成功后自动构建新镜像，然后推送到Docker镜像库。通过后续的自动化运行，可以将新镜像的容器部署到预演环境，从而进一步测试。

4.充分利用基础设施节省资金
Docker和容器有助于优化IT基础设施的利用率和成本。优化不仅仅是指节约成本，还要确保在适当的时间有效地使用适当的资源。容器是一种轻量级的打包和隔离应用工作负载的方法，所以Docker允许在同一物理或虚拟服务器上毫不冲突地运行多项工作负载。

### 1.2 什么是Docker

图1-9　Docker架构图
1）Client：Docker客户端。
2）Docker Daemon：Docker服务器。
3）Image：Docker镜像。
4）Registry：Docker仓库。
5）Container：Docker容器。
Docker采用的是C/S架构模式，使用远程REST API来管理和创建Docker容器。既然是采用REST模式，所以客户端和服务端不用在同一个host上，可以分布式管理部署。


2.Docker服务器：Docker Daemon
Docker Daemon是Docker的主服务，docker.service。在Linux操作系统下，安装好Docker后我们就可以使用systemctl start/status/stop docker.service命令去操作Docker的服务了。

Container就是容器实例，可以类比为KVM生成的虚拟机。Container是运行时状态，我们可以通过docker命令和API去控制和改变Container的状态，比如启动、停止。上面所讲的Client、Daemon、Image和Registry都是为了Container运行稳定而服务的。

### 1.5 Docker镜像的基本操作

镜像下载后，默认是存在/var/lib/docker路径下的。需要注意的是，这个目录有很多结构，大家不要想着镜像就只放在一个目录下，Docker对于镜像的存储是分层管理的，最主要的目录在/var/lib/docker/overlay2下。

1）builder目录
builder就是用来构建的目录，其中的fscache.db是用来在构建镜像时缓存数据的，也就是缓存了相关镜像的数据，从而大大提高了创建镜像的速度，可以在底层的基础之上构建更高层的镜像。

（2）containerd目录
containerd的主要职责是镜像管理（镜像、元信息等）、容器运行（调用最终运行时组件运行），该目录存放着containerd相关信息。

（3）containers目录
主要用来存储创建容器的内容。我们新建一个容器之后，这里就会多出一个目录，我们创建了3个容器，这里就有3个目录，如图1-36所示。

（4）image目录
image目录主要用来存储镜像的相关分配、分布、imagedb、layerdb、repositories等信息。信息大部分都是sha256加密形式的。当我们使用docker images命令显示镜像的时候，其实读取的就是这个目录的信息。如果这个目录里没有信息，运行docker images命令就会显示空。

我们还可以运行docker diff命令查看都做了哪些操作，

想把当前的容器做成镜像（好比虚拟机做快照打包），可以运行docker commit命令，语法格式为：

docker commit命令，语法格式为：￼
docker commit [选项] <容器ID或容器名> [<仓库名> [:<标签>]]

### 1.6 用Dockerfile专业化定制镜像

11.ONBUILD指令
ONBUILD指令比较特殊，我们把这个单词拆分就是on build，意思是“在创建的路上”。但是，不是现在创建。这个怎么理解？既然不是现在创建，那就和当前的镜像没有关系，不会影响当前的环境，而是会在下一个镜像环境里有操作、有影响。
这个指令的作用是以当前镜像为基础，在构建下一个镜像的时候运行一些命令，也就是为下一个镜像做准备。

### 1.8 存储基本配置

4）数据卷可以在容器之间共享和重用。也就是说，多个不同的容器可以同时使用一个数据卷（类似NFS共享）。
5）数据卷、容器、镜像都能独立存在。
6）挂载数据卷的容器如果被删除，数据卷还会存在，不会跟着容器一起删除。

### 2.1 什么是容器云

从技术层面来看，云计算基本是按照“虚拟化、网络化、分布式技术成熟稳定→IaaS成熟稳定→PaaS成熟稳定→SaaS成熟稳定”这条路线发展的。

比如最开始的虚拟化阶段，典型的代表是Xen、vSphere、KVM等技术，IaaS层是OpenStack，PaaS层是Kubernetes，SaaS层开源界还没有典型的代表。

我们知道TCP/IP有7层协议，协议的出现和规定就是为了统一标准，这样无论是开发者、使用者还是网络设备厂商，都能按照公认的协议来学习和生产。

第四张图，云计算的终极形态，云厂商负责一切IT事物，用户能放心大胆地通过互联网随意调用想用的IT服务。

### 2.2 什么是Kubernetes

如果你之前了解过OpenStack，应该知道可以用OpenStack管理虚拟机资源。那么管理容器是否有对应的开源平台呢？有的，就是Kubernetes。

4）红帽与三大云巨头合作打造Kubernetes Operators中心。


了解它的调度规则和流程，总体来讲有两点，一个是预选调度过程，另外一个是确定最优节点。预选调度过程是遍历所有目标节点，筛选出符合要求的候选节点。Kubernetes内置了多种预选策略供用户选择。确定最优节点是在预选调度过程的基础上，采用优选策略计算出每个候选节点的积分，积分最高者胜出。后续我们会详细讲解Scheduler的策略，以帮助我们对资源进行规划和把控。

Label就是标签，标签有什么作用呢？很简单，标签用来区分某种事物。在Kubernetes里，资源对象分很多种，前面提到的Pod、node、RC都可以用Label来打上标签。打上标签后，用户就可以更好地区分和使用这些资源了。另外，Label是以key/values（键/值对）的形式存在的。用户可以对某项资源（比如一个Pod）打上多个Label，另外，一个Label也可以同时添加到多个资源上。

Selector就是起这个作用的。通过标签筛选，我们能快速找到想要的资源对象。

Kube-proxy也运行在work节点上，因为带有proxy字眼，我们可以很快联想到它跟代理、转发、规则等方面有关。是的，它的作用就是生成iptables和ipvs规则，处理一些访问流量相关的转发。

Ingress是一种规则，由Ingress controller控制和使用，主要作用就是解析Ingress的转发规则。一旦Ingress里面的规则有改动，Ingress controller也会及时更新，然后根据这些最新的规则把请求转发到指定的Service上。

Service上（图里直接到Kubelet-proxy），前面提到过Service的实现靠的是Kubelet-proxy。


3）接着Kubelet-proxy把流量分发在对应的Pod上。
4）每个work节点里面都运行着关键的Kubelet服务，它负责与master节点的沟通，把work节点的资源使用情况、容器状态同步给APIServer。

### 2.3 Kubernetes的基础知识

是网络虚拟层连接通信在Kubernetes集群维护、网络排障和网络调优环节中是必不可少的，因为Kubernetes的底层网络用了大量的网络虚拟化。如果你想加强学习网络运维，建议按照“传统网络技术→虚拟化技术→网络虚拟化SDN技术”这样的路线逐步深入学习。

随着容器微服务的发展，经常可以见到云原生（Cloud Native）这个词。Red Hat对云原生是这么定义的：云原生落地依靠云原生应用，云原生应用就是独立的小规模、松散耦合服务的集合，旨在提供被业界认可的业务价值，例如快速融合用户反馈和需求，以实现持续改进。简而言之，通过开发云原生应用，我们可以快速构建新应用，优化现有应用并将这些应用组合在一起，用最快的速度满足用户需求。云原生的结构如图2-7所示。

原生方法主要由DevOps、微服务、容器、持续交付4种理论和方法来实现。

中，CI/CD即持续集成与交付，Micro-services即微服务，Containers即容器，DevOps目前还没有权威的定义，更多强调的是团队协作、加速软件交付。云原生里包含的CI/CD、微服务、容器技术都直接跟Docker和Kubernetes有关系。所以，学好Kubernetes也是为掌握新一代的架构方法打基础。未来用到Kubernetes的地方会越来越多，云原生直接给出了一个IT技术发展的大方向，具有很强的指导意义。

### 2.4 深入理解Pod

2.2节对Pod有过简单的解释。我们可以把它想象成一个“豆荚”，里面包着一组有关联关系的“豆子”（容器），如图2-8所示。

OpenStack管理的VM可以说是OpenStack里的最小单元，我们知道虚拟机有隔离性，里面部署的应用只在虚拟机中运行，它们共享这个VM的CPU、MEM、网络和存储资源。Pod也是如此，Pod里面的容器共享着Pod的CPU、MEM、网络和存储资源。那么Pod是如何做到这一点的呢？我们接着学习。注意，Kubernetes集群的最小单元是Pod，而不是容器；Kubernetes直接管理的也是Pod，而不是容器。Pod里可以运行一个或多个容器。

1）扮演PID 1的角色，处理僵尸进程。2）在Pod里协助其他容器共享Linux Name-space。

Pause容器的代码是用C语言编写的，如下所示。Pause的代码里有个无限循环的for(;;)函数，函数里面运行的是pause()函数，pause()函数本身是睡眠状态的，直到被信号（signal）中断。正是因为这个机制，Pause容器会一直等待信号，一旦有了信号（进程终止或者停止时会发出这种信号），Pause就会启动sigreap方法，sigreap方法调用waitpid获取其子进程的状态信息，如此一来自然就不会在Pod里产生僵尸进程了。

2）livenessProbe探测的程序里不要执行其他逻辑，它很简单，就是探测服务是否运行正常。如果主线程运行正常，直接返回200，不正常就返回5xx。如果有其他逻辑存在，livenessProbe探测程序会把握不准。

readinessProbe主要是探测判断容器是否已经准备好对外提供服务。因此，实现一些逻辑来检查目标程序后端所有依赖组件的可用性非常重要。readinessProbe探测前，需要清楚地知道所探测的程序依赖哪些功能、这些功能什么时候准备好。如果应用程序需要先建立与数据库的连接才能提供服务，那么readinessProbe的处理程序必须已与数据库建立连接才能确认程序是否就绪。

另外，Kubernetes在创建资源对象时，可以使用lifecycle来管理容器在运行前和关闭前的一些动作。lifecycle有如下两种回调函数。1）postStart：容器创建成功后，运行前的任务用于资源部署、环境准备。2）preStop：容器被终止前的任务用于优雅关闭应用程序、通知其他系统。Waiting是容器的默认状态。如果容器没在运行或已终止运行，就是Waiting状态。处于Waiting状态的容器仍然可以拉取镜像、获取密钥。在这个状态下，Reason字段将显示容器处于Waiting状态的原因。

另外在容器进入Terminated状态之前，如果有preStop，则运行。

我们还要知道，Kubernetes CPU是一个可压缩的资源。如果容器达到了CPU的设定值，就会开始限制CPU的资源，容器性能也会随之下降，但是不会终止和退出。最后我们了解一下MEM的资源控制。单位换算方法：1 MiB=1024KiB，注意MiB≠MB，MB是十进制单位，MiB是二进制，很多人以为1MB=1024KB，其实1MB=1000KB，1MiB=1024KiB。在Kubernetes里，内存单位一般用的是MiB，当然你也可以根据具体的业务需求和资源容量使用KiB、GiB甚至PiB为单位。这里要注意的是，内存是可压缩资源，容器使用的内存资源到达了上限会造成内存溢出，容器就会终止运行并退出。

### 2.5 如何编写Pod YAML文件

YAML（Yet Another Markup Language）结合了我们之前接触过的properties、XML、JSON等数据格式标记语言的特性，具有以下特点。●层次分明、结构清晰。●使用简单、上手容易。●语义丰富。●大小写敏感。●禁止使用Tab键缩进，只能使用空格键缩进。

YAML里的这些参数其实是Kubernetes声明式的一种体现，我们可以简单地把它理解为用户操作Kubernetes的一个接口。YAML里设置的参数数值，最终都会持久化到ETCD里。

### 2.6 如何理解编排

个人理解，编排就是按照某种机制让各种组件自动化配合运作，然后得到我们想要的结果，如图2-13所示。

在物理机时代，人力运维“编排”（搬、挪）物理机；在虚拟机时代，人们用脚本、CMDB、OpenStack heat等“编排”虚拟机；在如今的容器时代，人们用Kubernetes、微服务编排容器。

套用网上的一句话：编排旨在简化并优化重复性的频发流程，以确保准确、快速地完成软件部署。产品上市速度越快，成功概率越大，只要流程是重复性的，便可通过自动运行相关任务来优化流程，消除重复操作。

2.6.2　Kubernetes与编排Kubernetes是容器资源管理、调度平台，换句话说，它就是容器资源的编排系统。那么容器编排都有哪些动作和流程呢？总的来说有以下六大部分。●资源调度●资源管理●服务发现●健康检查●自动伸缩●更新升级

Kubernetes通过可压缩和不可压缩的机制来区分计算资源，这里CPU资源是可压缩的，MEM资源是不可压缩的。Kubernetes主要是通过requests和limits参数来灵活控制Pod对资源的使用。

6.更新升级程序会随着业务发展不断迭代，我们可以架设一套完整的CI/CD来完善程序的发布、更新机制。Kubernetes目前在CI/CD里占据很重要的地位，如图2-15所示。另外，Kubernetes的内部控制器ReplicationController与Deployment也为服务的滚动和平滑升级提供了很好的机制。

编排其实是一直存在的，从最初的物理机时代，到如今的Kubernetes自编排，这些工具和系统在解放人力的同时也节省了成本。

### 2.7 五种Kubernetes控制器

应用=网络+载体+存储。应用组成可以用图2-16表示。

Kubernetes有5种控制器，分别对应处理无状态应用、有状态应用、守护型应用和批处理应用。

2.StatefulSetStatefulSet的出现使Kubernetes实现了“有状态”应用落地，Stateful这个单词本身就是“有状态”的意思。之前大家一直怀疑有状态应用落地Kubernetes的可行性，StatefulSet有效解决了这个问题。有状态应用一般都需要具备一致性，它们有固定的网络标记、持久化存储、顺序部署和扩展、顺序滚动更新。StatefulSet通过以下几个方面实现Pod的稳定、有序。1）给Pod一个唯一和持久的标识（如Pod名称）。2）给Pod一份持久化存储。3）部署Pod都是顺序性的，0～N-1。4）扩容Pod时，前面的Pod必须还存在着。5）终止Pod时，后面Pod也一并终止。

4.JobJob就是任务，我们时常批处理运行一些自动化脚本或者Ansible，在Kubernetes里我们用Job运行批处理任务。5.CronJob在IT环境里，经常遇到一些需要定时启动任务。传统的Linux里我们运行定义crontab即可，在Kubernetes里用CronJob控制器定时启动任务。其实CronJob就是上面Job的加强版，带时间定点运行功能。

### 2.8 Kubernetes的网络

Pod跟Service之间的互通，真正的操作者是kube-proxy，那么kube-proxy都是通过哪些方式做请求转发的呢？总的来说有3种模式：userspace、iptables和ipvs。

3）ipvs模式ipvs（IP Virtual Server，IP虚拟服务器）模式下，节点必须安装好ipvs内核模块，不然默认使用iptables模式。ipvs模式调用netlink接口创建相应的ipvs规则，同时会定期跟Service和Endpoints同步ipvs规则，以保证状态一致。流量访问Service后会重新定向到后端某一个Pod里，ipvs模式下有多种算法可以实现这个重定向过程（算法有轮询调度算法、最少连接数调度算法、目的地址散列调度算法、源地址散列调度算法），因此ipvs模式的效率和性能都很高。

4）ExternalName：结合kube-dns将相关资源映射成域名的形式，然后相互之间通过域名访问。

### 2.9 Kubernetes的存储

PVC（Persistent Volume Claim）相比PV多了一个Claim（声明、索要、申请）。PVC是在PV基础上进行资源申请。那么有了PV为何还要用PVC呢？因为PV一般是由运维人员设定和维护的，PVC则是由上层Kubernetes用户根据存储需求向PV侧申请的，我们可以联想Linux下的LVM，Kubernetes里的PV好比LVM的物理卷，Kubernetes里的PVC好比LVM里的逻辑卷。LVM逻辑架构如图2-27所示。

管理员创建StorageClass后会去寻找合适的PV，接着PVC会与PV绑定，分配给Pod使用。如果Pod不再使用，会释放PVC，最终删除PVC，PV解绑回归待绑定闲时状态。

2.8节我们学习Kubernetes网络的时候了解过CNI，那么在存储方面，Kubernetes也有一套接口管理规范机制，也就是CSI（Container Storage Interface，容器存储接口）。CNI是用来统一网络规范接口的，CSI自然是想统一标准化存储方面的接口，方便管理。

### 2.10 Kubernetes的安全机制

我们可以使用第三方工具检测Docker镜像是否有漏洞。发现漏洞后，及时更新补丁。大家可以在网上找到一些扫描工具，例如Docker Bench、Trivy、Clair、Cilium、Anchhore、Dagda等。

在表2-15提到的8种认证方式中，较为常用的是X509 Client Certs和Service Accout Tokens，2.14.1节会详细介绍基于X509 Client Certs的认证方式。

1.容器运行时限制1）容器中指定固定的用户：spec.containers.securityContext.runAsUser: uid2）容器内不允许root用户：spec.container.securityContext.runAsNonRoot:true3）使用特权模式运行容器：spec.containers.securityContext.privileged:true

### 2.11 Kubernetes监控

Docker自身提供了一种内存监控方式，可以通过docker stats命令对容器内存进行监控。该方式实际上是通过对Cgroup中相关数据进行取值并计算得到的，所以docker stats在/sys/fs/cgroup文件夹下获取容器监控的数据。我们知道在CentOS 7系统中，systemd默认挂载Cgroup系统，systemd提供了Cgroup的使用和管理接口。容器被Kubernetes Pod管理，对应的资源监控目录如下。●CPU：/sys/fs/cgroup/cpuset/kubepods/burstable。●内存：/sys/fs/cgroup/memory/kubepods/burstable。

### 2.15 Kubernetes的API与源码

Kubernetes提供了3种客户端认证方式。
1）HTTPS证书认证：基于CA根证书签名的双向数字证书认证方式，是最严格的认证方式。
2）HTTP Token认证：通过Token识别每个合法的用户。
3）HTTP Basic认证：官方不建议在实践中使用这种认证方式，这里不做介绍。

1.HTTPS证书认证
证书目录为/etc/kubernetes/pki，包含的证书如图2-50所示。
￼
图2-50　Kubernetes证书
在测试机器上使用证书认证方式访问集群API，如图2-51所示。

### 3.1 产品介绍

腾讯云容器服务（Tencent Kubernetes Engine，TKE）是对原生Kubernetes和Docker进行二次开发的运行环境，是打通运维、开发、测试之间壁垒的容器云平台，使用该服务，用户不用再关心集群管理等基础设施的工作。

1.弹性容器服务
弹性容器服务（Elastic Kubernetes Service，EKS）是腾讯云容器服务推出的服务模式，用户无须购买节点即可部署工作负载。EKS完全兼容原生Kubernetes，支持使用原生方式购买及管理资源，按照容器真实使用的资源量计费。

3.工作负载
工作负载是由多个服务组成的一个完整的应用程序，底层通过YAML模板快速部署。

4.自动伸缩
自动伸缩是一个Kubernetes HPA概念，可以理解为当容器CPU或内存等资源使用率达到触发策略时，触发容器扩容。

10.存储（Persistent Volume Claim，PVC）
PVC底层可以调用云硬盘和CFS资源，提供给Pod使用。

图3-3　TKE业务交付流程
首先利用Dockerfile基础文件制作Docker基础镜像，上传至TKE镜像仓库；然后开发人员在GitLab或其他代码平台提交新代码和业务Dockerfile文件；接着TKE镜像仓库对GitLab授权，构建打包生成Docker业务镜像；最后创建工作负载，选择Docker业务镜像进行部署。

### 3.5 腾讯云TKE网络

私有网络属于IaaS层网络，容器网络属于PaaS层网络，两个私有网络下容器资源通信，也是通过对等连接或云联网实现的。

在腾讯云TKE中默认的网络方式是VPC全局路由（GlobalRouter），这是基于腾讯云VPC实现的网络插件，无须在节点配置vxlan等网络虚拟化技术，容器路由直接经过VPC，容器与节点分布在同一网络平面上，就可使容器与容器之间从外网到容器畅通。相对于常见的网络虚拟模式，因为VPC全局路由没有额外的解封过程，网络性能损耗小，所以性能更佳。全局路由数据面网络架构如图3-6所示。

VPC侧通过在母机侧下发路由实现Pod之间、节点和Pod之间的互相访问，每个节点都会配置一个PodCIDR，该CIDR通过kube-controller-manager分配。在一开始创建集群时就需要指定Pod的CIDR，Pod IP会从Pod的CIDR中分配。GlobalRouter控制台配置如图3-7所示。

### 4.1 容器应用日志输出标准

3.容器日志输出文件标准化（推荐）日志命名规范（容器日志命令规范，组件日志）如下所示。1）PHP：php-fpm.log、php-fpm-slow.log、php-fpm-error.log。2）Nginx：${domain}_access.log，${domain}_error.log。3）Java：${应用名}_${date}.log，${应用名}_error_${date}.log。

在进行应用容器化改造时，需要收集应用日志，规定应用日志输出的格式和目录，这样方便规范化日志收集与管理工作。对中型及大型企业来说，容器日志标准化改造是必经之路。

### 4.2 容器日志采集

1）容器日志按类别分为标准输出和文本输出，其中标准输出是将容器业务日志输出打印至腾讯云TKE控制台；文件输出是将容器日志以文本的形式输出至容器本地，可以直接采集容器内的日志，也可以直接采集容器宿主机的日志（容器日志目录以hostpath方式挂载至宿主机目录）。

### 4.4 Dockerfile编写规范

3）Dockerfile开头几行命令应当固定下来，不建议频繁更改，以有效利用缓存。
4）多条RUN命令持续使用，有利于理解和维护Dockerfile。

### 4.5 容器业务类型

7）运行时选择：Docker方案相比containerd更成熟，如果对稳定性要求很高，建议选择Docker方案；注意以下场景只能使用Docker。
●Docker in Docker（通常在CI场景）
●节点上使用Docker命令
●调用Docker API


8）转发模式选择：对稳定性要求极高且Service数量小于2000，可选择iptables，其余场景首选IPVS。

### 5.3 无状态服务部署Java应用

另外，Java业务容器化若出现OOM提示，大多是由于request和limit内存设置不合理或Java程序启动没有设置合理的栈内存导致的。

### 5.4 有状态服务部署MySQL应用

3）由于PVC不会随着容器滚动更新或销毁而重建，所以MySQL容器每次写入的数据都永久保存在PVC底层存储系统中，实现了数据持久化。

### 5.5 Job任务型服务：Perl运算

Job是Kubernetes中用于处理任务的资源，常用于只运行一次的任务，是常见的控制器模式，大家可根据业务需求使用Job控制器。

### 5.7 DaemonSet守护任务：fluentd应用

5.7　DaemonSet守护任务：fluentd应用
Linux里有个概念叫Daemon，用来守护进程，会一直在后台运行。Kubernetes DaemonSet可以理解为守护进程的Pod，该Pod会部署在每台节点上

DaemonSet Pod常见业务场景如下。
1）日志类：在每台节点上运行fluentd、logstash。
2）存储类：在每台节点上运行glusterd、Ceph。
3）监控类：在每台节点上运行zabbix-agent，Prometheus Exporter。

### 5.10 部署案例之ELK

ELK架构说明如下。1）Logstash组件负责收集从数据源采集日志。2）Lgostash将采集后的日志推送至Elasticsearsh组件并进行处理。3）Elasticsearch对日志进行处理后，和Kibana配合使用，可视化展示收集的数据。

### 5.11 容器日志的三种采集配置方式

2.hostpath采集主机（hostpath）日志文件，即容器挂载（以hostpath访问挂载）节点目录后，将日志文件和内容输出至节点目录下，ccs-log组件收集该节点目录下的日志文件。

如果容器日志输出量小，应尽量将容器日志输出至控制台；如果容器日志输出量大，那么最好以hostpath形式输出在本地。

### 5.13 灰度发布

灰度部署是运维人员日常工作中的一种常见部署模式，因为是逐步按比例替换，对业务访问影响小，所以一般用于实际的生产环境中。

### 5.16 部署Ingress kong网关

5.16　部署Ingress kong网关传统的业务访问通常是用户先访问API网关，通过验证后，再由网关转发至请求的资源。这时，API网关相当于代理程序，如果用户有多个API网关，就可以使用Ingress kong将它们都管理起来。

Ingress kong可以理解为API网关的代理，也就是用户在访问API网关前，先要经过Ingress kong，在Ingress controller的上下文中，改用CRD方式进行管理，包括日志、限流、认证、鉴权等。

Ingress kong业务访问流程如下。1）用户创建Ingress规则，该规则被记录在Kubernetes资源中。2）由于kong-Ingress-controller容器检测Kubernetes资源，所以Ingress规则会同步到kong-Ingress-controller容器。

### 5.17 部署Istio

5.17　部署IstioKubernetes解决了运维部署的问题，随着业务量增大，服务网络的管理难度势必会越来越大。开发人员应该把更多的精力放在对业务和功能的实现上，而不是被烦琐的日志、监控、测试等辅助模块所禁锢，基于此，Istio应运而生。Kubernetes简化运维部署，Istio解决容器服务治理，是未来服务网络的发展趋势。

2）在对业务进行微服务改造时，Istio能够帮助微服务之间建立连接，形成mesh，且不需要对业务代码做任何改动。3）进行数据流程交互时，会先经过Istio，实现HTTP、TCP、WebSocket和GRPC流量自动负载均衡，对集群入口和出口的流量进行自动度量指标、跟踪和日志记录。在基于身份验证和授权的集群中实现安全的服务间通信。4）Istio proxy层能提供基础架构能力，例如负载均衡、服务发现、服务监控、故障恢复、故障测量、限流限速、A/B测试、灰度发布等。

所以最新发布的Istio 1.5更注重设计单体应用，重建整个控制面为Istio，摒弃了Mixer。未来的业务是“服务网格化”趋势，相信会有Istio的一席之地。

### 5.18 搭建Harbor仓库

Harbor是由VMware公司开源的企业级的Docker Registry管理项目，可以理解为Docker Registry的更高级版本。除了提供友好的可视化界面，Harbor还支持角色或用户进行访问控制、镜像漏洞扫描、行为审计、与企业LDAP集成。相比Docker Hub、Registry提供的简单存储功能，Harbor的出现可以说是解决了企业对于镜像仓库功能的需求。这里还有一个有趣的说法，Harbor是港湾的意思，把容器比喻成集装箱，把集装箱放在港湾，生动又形象。

### 6.9 腾讯云TKE健康检查

使用Kubernetes时经常会出现一些小问题，故腾讯云TKE在早期便推出了一项自助脚本工具用于排查Kubernetes，此脚本可收集Kubernetes层面、主机层面、网络层面的基础信息。收集完毕后统一放到tmp目录下，在排查Kubernetes问题时可参考脚本日志。

[插图]

图6-4　收集的日志文件解压后的日志文件说明如下（日志名组成格式：组件名-主机名-时间）。●dmesg：保存主机的内核信息。●sysctl：保存主机的内核参数配置。●docker：保存Docker层面的日志。●netstat：保存主机运行脚本时的网络情况。●kubelet：保存kubelet组件的日志。●messages：保存CentOS的主机日志。●iptables：保存运行主机上的iptables。●tkecheck：保存综合信息。

●kube-proxy：保存kube-proxy组件的log日志。●describeNode：保存describe node的全部信息。●cluster-dump：保存kube-system下Pod的基础信息。

1.自助健康检查概述为了增强运维性，腾讯云TKE推出了集群健康检查功能，并且后期会不断加强，功能如下。1）能够对资源状态（包括组件状态、节点状态、工作负载状态）和运行情况（包括集群参数、节点配置、工作负载配置、伸缩配置）进行检查。2）支持按天或按周定时扫描集群。

### 7.1 腾讯云TKE应用跨区高可用部署方案（一）

跨区域部署是很多高稳定性业务的刚需，理论上，当一个IDC因不可抗力导致崩溃，另外一个地域的IDC应能迅速代替。本节将介绍跨区域高可用的部署方法。

将云主机分散在不同的可用区，利用负载均衡（CLB）支持跨可用区分发的特性，实现业务流量跨可用区分发。当一个可用区（AZ1）出现故障时，流量切换到另一个可用区（AZ2），由此实现高可用部署。

本节将介绍如何使用腾讯云TKE进行业务高可用部署，部署架构如图7-2所示。两个节点分布在同一个地域的两个可用区，两个业务的Pod分布在这两个节点上，使用CLB实现流量负载均衡。下面详细介绍如何使用腾讯云容器快速实现应用的高可用部署。

图7-13　指定节点调度如果选择“自定义调度规则”，需要指定节点的标签，如图7-14所示。

图7-14　自定义调度规则我们给2个节点打了az的标签，分别是bj2、bj3，如图7-15所示。

通过腾讯云TKE控制台，使用Kubernetes节点亲和性的特性，可以快速实现业务跨可用区的高可用部署。通过节点亲和性的语法规则，可以实现复杂的部署逻辑。腾讯云TKE控制台大大简化了跨可用区部署的复杂性，可帮助用户快速实现业务的高可用。

### 7.2 腾讯云TKE应用跨区高可用部署方案（二）

podAntiAffinity指定了Redis的两个Pod的反亲和性，部署到标签为app=swagger-cache的不同节点上，并且topologyKey: "failure-domain.beta.kubernetes.io/zone"有两个值：800002和800003，分别对应腾讯云北京二区、北京三区。这样保证了两个Pod分布到不同的可用区。

### 7.3 腾讯自研业务上云：优化Kubernetes集群负载的技术方案探讨

Kubernetes的资源编排调度使用的是静态调度，将Pod Request Resource与Node Allocatable Resource进行比较来决定节点是否有足够资源容纳该Pod。静态调度带来的问题是，集群资源很快被业务容器分配完，但是集群的整体负载非常低，各个节点的负载也不均衡。本节将介绍优化Kubernetes集群负载的多种技术方案。

静态调度是根据容器请求的资源进行装箱调度，而不考虑节点的实际负载。静态调度最大的优点就是调度简单高效、集群资源管理方便。缺点也很明显，就是不考虑节点实际负载，极容易导致集群负载过低。

4）何时通过压缩比调整Pod Spec中的Request Resource？Kubernetes发展到现阶段，直接改Kubernetes代码是最笨的方式，我们要充分利用Kubernetes的扩展功能。这里，我们通过kube-apiserver的Mutating Admission Webhook对Pod的Create事件进行拦截，自研环境中webhook（pod-resource-compress-webhook）会检查Pod Annotations中是否设置为enable来打开压缩特性。如果打开并配置压缩比，则根据压缩比重新计算该Pod的Request Resource，通过补丁方式上传到APIServer。

### 7.4 腾讯IEG游戏营销活动在腾讯云TKE中的实践

（7）自动扩容在腾讯云TKE上设置了根据CPU使用率来自动调节容器副本数，如图7-47所示。

### 7.7 某视频公司基于腾讯云TKE的微服务实践

Spring Cloud微服务框架如图7-63所示，各组件架构说明如下。（1）Consul和Eureka这两种组件主要用于注册服务和发现服务。每个微服务启动时会向服务注册中心发送注册请求，注册中心会存储所有服务的信息。服务会周期性地向注册中心发送心跳检测，注册中心会检查超时且没有更新的服务，并注销该服务。因此，要访问其他服务时，可以先到服务注册中心查询被调用者的地址是否存在。

（6）分布式配置中心配置中心很重要，特别是在业务调用错综复杂的情况下，不可能对单个应用单独配置文件，Spring Cloud Config可以用来解决微服务场景下的配置问题。基于Spring Cloud的微服务架构有很多好处。●服务简单，业务功能单一，易理解、开放、维护。●每个微服务可由不同团队开发。●微服务是松散耦合的。●微服务持续集成、持续部署，服务独立部署、扩展。

下面我们看看在Kubernetes上如何解决上面的问题。1.服务注册和发现Kubernetes系统用于名称解析和服务发现的ClusterDNS是集群的核心附件之一，集群中创建的每个Service对象都会由其自动生成相关的资源记录。默认情况下，集群内各Pod资源会自动配置其作为名称解析服务器，并在DNS搜索列表中包含它所属名称空间的域名后缀。

创建Service资源对象时，ClusterDNS会为它自动创建资源记录，用于名称解析和服务注册。Pod资源可直接使用标准的DNS名称来访问这些Service资源。基于DNS的服务发现不受Service资源所在的命名空间和创建时间的限制。

3.配置中心通过创建ConfigMap，包含对应工作负载、Pod的配置文件、环境变量信息、工作负载Deployment/Dae-monSet等，引用并挂载ConfigMap到对应的容器中。

2）从功能上看，虽然两者存在一定程度的重叠，比如服务发现、负载均衡、配置管理、集群容错，但两者解决问题的思路完全不同。Spring Cloud面向的是开发者，开发者需要从代码级别考虑微服务架构的方方面面；而Kubernetes面向的是DevOps人员，提供的是通用解决方案，它试图在平台层解决微服务相关的问题，对开发者屏蔽复杂性。

关于服务发现，Spring Cloud给出的是传统的带注册中心Eureka的解决方案，需要开发者在维护Eureka服务器的同时，改造服务调用方与服务提供方的代码以接入服务注册中心，开发者须关心基于Eureka实现服务发现的所有细节。虽然可以通过Java注解降低代码量，但是本质上还是代码侵入方式。而Kubernetes提供的是一种去中心化方案，抽象了服务，通过DNS+ClusterIP+iptables解决服务暴露和发现问题，对服务提供方和服务调用方而言完全没有侵入。