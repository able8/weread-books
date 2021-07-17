## 阿里云数字新基建系列：云原生操作系统Kubernetes
> 罗建龙等

### 前言

包括了容器、编排调度、服务网格、持续集成交付（CICD）等在内的云原生技术，是“上好云”这个新阶段的核心技术，也是解决“上好云”这个问题的最佳实践策略和方法论。

之后，随着业务微服务化的落地，服务治理会越来越重要。像服务网格这类把服务治理逻辑下沉到基础设施层的思路，势必成为下一个趋势。

对于服务网格技术来说，一些企业（比如蚂蚁集团）其实已经有线上业务在使用了。大家可以通过蚂蚁集团一些技术专家的公开分享来了解他们的实践过程。

未来几年，Kubernetes肯定会像Linux一样，作为集群的操作系统无处不在。

微服务会把单体应用拆分成低耦合的小应用。这些应用各自负责一块相对独立的业务，每个小应用实例独占一台服务器，它们之间通过网络互相调用。这就是图0-4中第二个架构图的模式。

这里最关键的是，我们可以通过增加实例个数，来对小应用做横向扩容。这就解决了单台服务器无法扩容的问题。

因为Kubernetes是一个通用的计算平台，所以它会被用到各种业务场景中去，比如数据库、边缘计算、机器学习、流计算等。

首先，我们的一项重要工作是技术服务，我们需要用创新的方式，把一些生涩难懂的技术解释给用户听。这本书中对技术的解释是比较形象也比较易懂的。

### 1.2 云资源层

解决方法是使用一种特殊的弹性网卡ENI。这种网卡逻辑上位于客户集群所在的VPC里，所以可以和VPC里的节点通信，而物理上被安装在API Server Pod里，即位于Master集群里。这就完美解决了API Server Pod与托管版集群节点之间的通信问题。

### 1.5 功能扩展层

基于云解析的域名解析服务，主要依靠阿里云云解析产品来注册Serverless集群内服务域名。具体步骤如图1-12所示。首先，在Serverless集群创建的时候，集群管控会为集群创建一个对应的PrivateZone。其次，在用户创建服务的时候，集群管控会为服务域名创建对应的Private Zone记录。最后，在Pod尝试访问服务域名的时候，Pod会直接通过云DNS服务来做域名解析，而域名解析所使用的数据，正是上一步创建的Private记录。

### 2.1 从控制器视角看集群

这些组件可以被分为三个部分：核心组件Etcd、对Etcd进行直接操作的入口组件API Server，以及其他组件。

### 2.2 控制器的产生与演进

同时为了方便管理，可以设置一个控制器管理器来统一维护所有这些控制器，以确保这些控制器在正常工作

### 2.3 控制器示例

2.3.1 服务控制器
首先，用户请求API Server创建一个Load Balancer类型的服务，API Server收到请求并把这个服务的详细信息写入Etcd数据库。
其次，这个变化被服务控制器“观察”到了。

### 第3章 网络与通信原理

集群网络目前有两种实现方案，分别是Flannel和Terway。Terway和Flannel的不同之处在于，Terway支持Pod使用ENI（弹性网卡），并支持Network Policy特性。


### 4.1 节点增加原理

4.1.2 自动添加已有节点
自动添加已有节点，不需要人为把脚本拷贝到ECS命令行来完成节点准备的过程。管控使用了ECS Userdata的特性，把类似以上节点准备的脚本写入ECS Userdata，然后重启ECS并更换系统盘。当ECS重启之后，会自动执行Userdata里边的脚本，来完成节点添加的过程。

即集群调度器衡量资源是否充足的标准是“预订率”，而不是“使用率”。这两者的差别，类似酒店房间预订率和实际入住率：完全有可能有人预订了酒店，但是并没有实际入住。在开启自动伸缩功能的时候，我们需要设置缩容阈值，就是“预订率”的下限。之所以不需要设置扩容阈值，是因为Autoscaler扩容集群依靠的是Pod的调度状态：当Pod因为节点资源“预订率”太高而无法被调度的时候，Autoscaler就会扩容集群。

### 5.1 “关在笼子里”的程序

5.1.2 “笼子”
为了让这个程序不依赖于操作系统自身的库文件，我们需要制作容器镜像，即隔离的运行环境。

### 5.2 得其门而入

5.2.1 入口
Kubernetes作为操作系统，和普通的操作系统一样有API的概念。有了API，集群就有了入口；有了API，我们使用集群才能得其门而入。

5.2.2 双向数字证书验证
阿里云Kubernetes集群API Server组件，使用基于CA签名的双向数字证书认证来保证客户端与API Server之间的安全通信。这句话很拗口，初学者不太好理解，我们来深入解释一下。

在学校和学生之间，学校是可信第三方CA，而学生是通信参与者。如果人们普遍信任一个学校的话，那么这个学校颁发的毕业证书也会得到社会认可。参与者证书和CA证书好比毕业证和学校的办学许可证。

客户端和API Server作为通信的普通参与者，各有一张证书，如图5-5所示。这两张证书都是由CA签发的，我们简单地称它们为集群CA和客户端CA。客户端信任集群CA，所以它信任拥有集群CA签发证书的API Server；反过来，API Server需要信任客户端CA，然后才愿意与客户端通信。

阿里云Kubernetes集群CA证书和客户端CA证书，在实现上其实是一张证书，所以我们有图5-5所示的关系图。


### 5.3 择优而居

集群调度算法被实现为运行在Master节点上的系统组件，这一点和API Server类似。其对应的进程名是kube-scheduler。


5.3.7 优选
调度算法的第二个阶段是优选阶段。在这个阶段，kube-scheduler会根据节点可用资源及其他一些规则给剩余节点打分。
目前，CPU和内存是调度算法考量的两种主要资源，但考量的方式并不是简单地认为剩余CPU、内存资源越多得分就越高。

### 第6章 简洁的服务模型

在CNCF（云原生计算基金会）对云原生的定义中，容器、微服务、服务网格、不可变基础设施和声明式API被作为云原生的代表性技术。

### 6.3 让服务照进现实

kube-proxy作为部署在集群节点上的控制器，通过集群API Server监听着集群状态变化。当有新的服务被创建的时候，kube-proxy会把集群服务的状态、属性，翻译成反向代理的配置

### 6.4 基于Netfilter的实现

Kubernetes集群节点实现服务反向代理的方法目前主要有三种，即userspace、iptables以及ipvs。本章以阿里云Flannel集群网络为范本，仅对基于iptables的服务实现做深入讨论。

6.4.4 用自定义链实现服务的反向代理
集群服务的反向代理，实际上就是利用自定义链，模块化地实现了数据包的DNAT转换。KUBE-SERVICE是整个反向代理的入口链，其对应所有服务的总入口；KUBE-SVC-XXXX链是具体某一个服务的入口链。KUBE-SERVICE链会根据服务IP地址，跳转到具体服务的KUBE-SVC-XXXX链。KUBE-SEP-XXXX链代表着某一个具体Pod的地址和端口，即Endpoint，具体服务链KUBE-SVC-XXXX会按照一定的负载均衡算法跳转到Endpoint链。

### 7.1 阿里云容器服务Kubernetes的监控总览

SLS主要负责日志的采集、分析。在阿里云容器服务Kubernetes中，SLS可以采集三种不同类型的日志。
● API Server等核心组件的日志。
● Service Mesh和Ingress等接入层的日志。
● 应用的标准日志。

ARMS（实时监控服务）主要负责采集、分析、展现应用的性能指标。ARMS目前主要支持Java与PHP两种语言的集成，可以采集虚拟机（JVM）层的指标，例如GC的次数、应用的慢SQL查询操作、调用栈等，对于后期性能调优可以起到非常重要的作用。

### 7.2 阿里云容器服务Kubernetes的弹性总览

.HPA
HPA是Pod水平伸缩的组件，除了社区支持的Resource Metrics和Custom Metrics，阿里云容器服务Kubernetes还提供了external-metrics-adapter，支持云服务的指标作为弹性伸缩的判断条件，目前已经支持多个产品不同维度的监控指标，例如Ingress的QPS、RT，ARMS中应用的GC次数、慢SQL次数，等等。


1.Cluster Autoscaler
Cluster Autoscaler是目前比较成熟的节点伸缩组件，主要应用场景是当Pod资源不足时进行节点的伸缩，并将无法调度的Pod调度到新弹出的节点上。

### 8.1 镜像下载这件小事

这个过程是比较烦琐的。我们能想到的优化方法是，让相册直接访问网盘来获取我们的照片。而这需要我们把用户名和密码的使用权授予相册。
这样的授权方式，优点显而易见，但缺点也是很明显的：我们把网盘的用户名和密码给了相册，相册就拥有了读写网盘的能力，从数据安全角度来看，这是很可怕的。

### 8.2 理解OAuth 2.0协议

协议OAuth是为了解决上述问题设计的一种标准方案，我们的讨论针对2.0版。与把用户名和密码直接给第三方应用不同，此协议采用了一种间接的方式来达到同样的目的。
如图8-3所示，这个协议包括三个部分，分别是第三方应用获取用户授权、第三方应用获取临时Token以及第三方应用存取资源。


这个协议其实就做了两件事情：在用户授权的情况下，第三方应用获取Token所表示的临时访问权限，然后第三方应用使用这个Token去获取资源。

实际上OAuth 2.0各个变种的核心差别在于第一件事情，就是用户授权给资源服务器的方式。

第四种，完整实现OAuth 2.0协议，也是最安全的。第三方应用首先获取以验证码表示的用户授权，然后用此验证码向资源服务器换取临时Token，最后使用Token存取资源。

### 8.3 Docker扮演的角色

镜像仓库Registry的实现目前使用“把用户名和密码给第三方应用”的方式。即假设用户对Docker足够信任，用户直接将用户名和密码交给Docker，然后Docker使用用户名和密码向鉴权服务器申请临时Token。

8.3.3 拉取镜像是怎么回事镜像一般会包括两部分内容：一是manifests文件，这个文件定义了镜像的元数据；二是镜像层，是实际的镜像分层文件。

最后，使用上面的Token，以Authorization头字段的方式下载manifests文件。这对应的是图8-6中下载资源这一步。当然因为镜像还有分层文件，所以实际上docker还会用这个临时Token多次下载文件才能实现完整的镜像下载。

### 8.4 Kubernetes实现的私有镜像自动拉取

Kubernetes集群一般会管理多个节点，每个节点都有自己的docker环境。如果让用户分别到集群节点上登录镜像仓库，显然是很不方便的。为了解决这个问题，Kubernetes实现了自动拉取镜像的功能。如图8-7所示，这个功能的核心，是把docker.json内容编码，并以Secret的方式作为Pod定义的一部分传给Kubelet。

第一步，创建Secret。这个Secret的.dockerconfigjson数据项包括了一份Base64编码的docker.json文件。第二步，创建Pod，且使Pod编排中imagePullSecrets指向第一步创建的Secret。第三步，Kubelet作为集群控制器，监控着集群的变化。当它发现新的Pod被创建时，就会通过API Server获取Pod的定义，这包括imagePullSecrets引用的Secret。第四步，Kubelet调用docker创建容器且把.dockerconfigjson传给docker。第五步，运行时docker使用解码得到的用户名和密码拉取镜像，这和上一节的方法一致。

一是在第一步创建Secret之后，添加default service account对imagePull Secrets的引用。二是Pod默认使用default service account，而service account变更准入控制器会在default service account引用imagePullSecrets的情况下，在Pod的编排文件里添加imagePullSecrets配置。

### 8.5 阿里云实现的ACR credential helper

有了临时用户名和密码，ACR credential helper就为命名空间创建对应的Secret以及更改default SA来引用这个Secret。这样，控制器和Kubernetes集群本身的功能，一起使阿里云Kubernetes集群拉取阿里云容器镜像服务上的镜像的全部流程自动化了。

### 9.1 日志服务介绍

● 可靠：经历了“金融级别”的考验，不丢失一条日志。
● 稳定：客户端性能强劲，资源占用率低，在上百万台机器上运行。

### 9.2 采集方案介绍

Kubernetes的每个节点都会运行一个Logtail容器，该容器可采集宿主机以及该宿主机上容器的日志，包括标准输出和日志文件。

Kubernetes内部会注册自定义资源CRD AliyunLogConfig，并部署Alibaba Log Controller控制器。

1）用户使用kubectl或其他工具应用aliyunlogconfigs CRD配置。
（2）alibaba-log-controller监听到配置更新。
（3）alibaba-log-controller根据CRD内容以及服务端状态，自动向日志服务提交创建Logstore、创建配置以及创建应用机器组的请求。
（4）以DaemonSet模式运行的Logtail会定期请求配置服务器，获取新的或已更新的配置并进行热加载。

### 9.3 核心技术介绍

不同于其他开源日志采集Agent，日志服务Logtail从设计之初就已经考虑到配置管理的难题。因此Logtail从第一个版本开始就支持中心化的配置管理，支持在日志服务控制台或者SDK中对所有采集配置进行统一管理，大大降低了日志采集的管理负担

### 9.4 总结

首先是打造出了极致的部署体验，用户只需一条命令、一个参数即可完成整个Kubernetes集群的日志解决方案部署。

### 第10章 集群与存储系统

为了能够屏蔽底层不同存储厂商存储实现的细节，Kubernetes引入了Persistent Volume Claim（PVC）、Persistent Volume（PV）、Storage Class（SC）等API原语（Primitive），以及抽象出来一套标准的可以与Kubernetes交互的接口（FlexVolume、SI），将具体的存储实现与Kubernetes自身解耦。

### 10.1 从应用的状态谈起

10.1.2 有状态的应用
对于有状态的应用，Kubernetes引入了StatefulSet。StatefulSet配合PVC和PV，可以将应用的状态存储到远端。这样的应用Pod在遇到宿主机等的故障迁移后，通过复用之前的远端存储，可实现应用带状态的迁移。

而Stateful Set是通过保持其管理的每个Pod的名字的有序性和不变性的方式，建立了Pod名字与该Pod使用的存储（通过PVC来描述）的一一对应关系，从而保证了同名的Pod发布或迁移过程中始终可以使用“同一份”远程存储。


### 10.2 基本单元：Pod Volume

（3）Projected Volume：Secret、ConfigMap、DownwardAPI、ServiceAccountToken，将Kubernetes集群中的一些配置信息以Volume的方式挂载到Pod的容器中，即应用可以通过POSIX接口来访问这些对象中的数据。

### 10.3 核心设计：PVC与PV体系

用户只需通过PVC声明自己需要的存储Size、AccessMode（单Node独占还是多Node共享？只读还是读写访问？）等业务真正关心的需求，而不用关心存储系统的实现细节，PV和其对应的后端复杂信息完全可以交由Kubernetes Cluster或Administrator统一维护和管控。

（1）预先声明PV的静态方式：所需存储类型、大小等预先定义和分配好，一般来说相应的存储是预先分配好的，这种使用方式不够灵活，如图10-1所示。

### 10.4 与特定存储系统解耦

我们对存储系统的核心需求有两个：申请存储空间并将其最终挂载到应用容器中。对用户来说，只需要创建资源对象（PVC）。而真正替我们实现这两个核心需求的，是集群中的存储相关控制器。存储相关控制器会“观察”到资源字段的变化，并触发相应的动作来完成存储申请和挂载等操作。

（6）Volume Manager等待存储设备挂载完成后，将Volume挂载到Pod可访问的目录下。
（7）Kubelet启动Pod并将存储挂载到相应的容器中。
总的来说就是通过PV Controller监控PV、PVC、SC等资源对象，然后调用相应的存储插件去申请存储空间，并通过Attach-Detach Controlle或Kubelet Volume Manager将相应存储空间挂载到指定节点上，然后Pod在启动的过程中将其挂载到容器可访问的目录上。

10.4.2 in-tree（内置）Volume Plugin
早期与特定存储交互的逻辑是直接写到Kubernetes的代码中的（如图10-3中的GCE、Azure存储插件），这种被称为in-tree的方式，随着支持Kubernetes的云厂商的不断增加，会对Kubernetes本身的维护和发展造成难以控制的影响。

### 11.1 基本原理

Kubernetes集群有四种类型的服务，分别是ClusterIP、NodePort、LoadBalancer以及ExternalName。

ClusterIP类型的服务只能在集群内访问，而NodePort和LoadBalancer两种类型的服务都可以从集群外部访问。这三种服务有一个共同特点，就是理论上只能通过四层协议来访问。LoadBalancer类型的服务虽然也可以配置对应负载均衡的HTTP/HTTPS属性，但也只提供了简单的代理，无法应对复杂的七层的策略路由场景。

Ingress的存在，正是为了解决以上问题。Ingress的职责是将不同URL的请求转发给不同的服务，以实现复杂路由策略。典型的Ingress组件，是基于Nginx七层代理实现的Nginx Ingress Controller。如图11-1所示，这是一个典型的七层路由的例子。

如果是测试环境，通常只需要自签名证书即可，具体操作有以下几个步骤。
（1）使用openssl工具生成私钥和证书文件。
￼
（2）创建Secret，并将私钥和证书存放到其中。

制器会作为Ingress对象和Nginx之间的翻译官，把Ingress中定义的路由配置转换成Nginx的配置。
如果Ingress定义有错误，翻译工作会失败，导致最新的规则没有办法下发到Nginx里。控制器日志和Nginx的Access/Error日志，都会汇总到Nginx Ingress Controller这个Pod的标准输出，可以从Pod的标准输出查看控制器的日志或者Nginx的访问日志。

### 11.2 场景化需求

为了节约SLB的费用，可以将Ingress入口SLB改成内网类型，然后手动在SLB上绑定一个弹性公网IP地址，这样内网和外网都可以访问Ingress，同时只需要一个SLB。

若集群有多套Ingress控制器，在创建Ingress的时候，怎么区分由哪一个控制器来配置Ingress呢？那就要通过IngressClass来区分。
部署Ingress Controller的时候，会传入一个--ingress-class来标记控制器，然后在创建Ingress的时候，通过在Annotation中加入kubernetes.io/ingress.class参数，Ingress控制器将kubernetes.io/ingress.class参数的值和设定的--ingress-class的值相比较，如匹配得上，则由自己来负责配置Ingress，如匹配不上，则忽略这个Ingress，

### 11.3 获取客户端真实IP地址

理解客户端真实IP地址的传递过程这里以阿里的Kubernetes为例，最简单的访问链路是：Client→SLB→Ingress-nginx→Pod。

为80。即Ingress-nginx“看到”的是Client在访问自己，而非SLB，因此Ingress-nginx能获取到客户端的真实IP地址，无须做任何配置改动。接着Ingress-nginx会将客户端真实IP地址放在X-Real-IP和X-Forwarded-For这两个Header里传给Pod。虽然Ingress-nginx的remote_addr也会存放客户端真实IP地址，但remote_addr并不会作为Header传递下去，Pod中不能以此Header作为获取客户端IP地址的方式。需要说明一点，为了简化原理，第2步省去了SLB转发给节点，再由节点转发给Ingress-nginx的中间环节，事实上，某些情况下SLB也确实能直接转发给Pod，不经过节点。

第二种情况，SLB配置为七层，如图11-5所示。此种情况下，Client发请求到SLB，SLB会将Client的IP地址放到X-Forwarded-For（阿里云的SLB七层监听默认开启了X-Forwarded-For）这个Header里，然后传递给Ingress-nginx。而Ingress-nginx收到请求报文的源IP地址是SLB内部的IP地址，因此默认情况下，Ingress-nginx会将SLB的内部IP地址视为客户端真实IP地址，需要配置Ingress-nginx来开启X-Forwarded-For特性。

（2）第2步，二级代理Ingress-nginx收到请求，会继续将前一级代理的IP地址（这里是2.2.2.2）“append”到X-Forwarded-For中（此时看到的是“X-Forwarded-For:1.1.1.1, 2.2.2.2”）并将此Header通过请求一并发送给Pod。这里有两点要注意：每经过一层代理，Nginx都会把前一级的IP地址“append”到X-Forwarded-For中，而不是直接覆盖；“append”意为追加，即把前一级的IP地址追加到后面，所以无论经过多少级代理，Client的真实IP地址都是放在X-Forwarded-For中的第一个IP地址。

在Cluster模式下，SLB会将流量随机转发到任意一个Worker节点，然后Worker节点再随机转发到其中一个Pod上，既可能是转发到本节点的Pod上，也可能是转发到其他节点的Pod上。跨节点转发是由SNAT实现的，而SNAT会修改掉报文的Source IP地址，Pod收到的报文的Source IP地址就是节点的IP地址，这样就把前一级的真实IP地址替换掉了。前面讲过，SLB在四层的情况下，Client的真实IP地址是透传（不经过代理修改，直接转发）的，因此，Ingress-nginx无法获取客户端的真实IP地址。


需要将ExternalTrafficPolicy的值设置为Local，才能保证Ingress-nginx可以获取客户端的真实IP地址。这里分两种情况。

（1）SLB为四层转发时，Ingress-nginx可以正确获取到客户端真实IP地址，不需要做任何改动，Pod需要从Header里获取客户端真实IP地址。

### 11.4 白名单功能

Nginx Ingress Controller官方网站给出了Ingress白名单配置的一般方法：
● 通过Annotation配置单个Ingress，作用范围是本Ingress，对应的Annotation：nginx.ingress.kubernetes.io/whitelist-source-range，值填写CIDR，用逗号分隔符分隔，如“192.168.0.0/24, 10.0.0.10”。


### 12.1 升级预检

大家把为正在对外提供服务的Kubernetes集群升级比作“给飞行中的飞机换引擎”，所以升级的难度可想而知。
升级难度主要源自两点：一是集群经过长时间的运行，积累了复杂的运行时状态；二是集群已经被进行了各种个性化配置。这两点都会带来升级流程中难以处理的情况，从而导致升级失败。

具体的资源检查项主要有剩余可运行Pod数量、磁盘剩余容量、可用句柄数、进程数，以及可用内存。

Kubernetes社区推荐的Kubernetes 1.16版所对应的CoreDNS为1.6版。与之前版本的CoreDNS相比，其主要的配置变化为将默认的转发插件从proxy切换到了forward组件，并从镜像中移除了proxy插件。这就导致如果我们不提前修改Corefile的话，在我们升级完Kubernetes与CoreDNS之后，CoreDNS会因为无法识别Corefile中的proxy插件而导致程序崩溃。

升级到1.18版之前需要对集群和本地工作流水线中的工作负载进行检查，需要确保所有工作负载都不再使用extensions/v1beta1、apps/v1beta1和apps/v1beta2这三个APIVersion。因为这三个APIVersion会在1.18版中被彻底删除，

位于extensions/v1beta1下的资源daemonsets、deployments、replicasets使用apps/v1替代，位于extensions/v1beta1下的资源networkpolicies使用networking.k8s.io/v1替代。

### 12.2 原地升级与替代升级

替代升级的优点在于，通过将旧版本的节点替换为新版本的节点从而完成集群升级。这个替换的过程相较于原地升级更为“原子化”，也不存在那么复杂的中间状态，所以也不需要在升级之前进行太多的前置检查。

替代升级的缺点在于，将集群内的节点全部进行替换和重置，所有节点都会经历排水的过程，这就会使集群内的Pod大量迁移重建。在对Pod重建容忍度比较低的业务中可能会引发故障。另一个缺点为节点经历重置后，储存在节点本地磁盘上的数据都会丢失。最后就是这种升级方式可能会带来宿主机IP地址变化等问题，对包年、包月用户也不够友好。

### 12.3 升级三部曲

（1）滚动升级Master节点。
（2）分批升级Worker节点。
（3）升级核心系统组件。

12.3.2 升级Worker节点
在完成Master升级之后，我们就可以开始对Worker节点进行升级了。节点的版本是通过节点上的Kubelet进行上报的，也就是说Kubelet的版本号决定了节点的版本号。

● 慢启动：再开始启动升级后，第一批次先对一个节点进行升级，后面每批次升级的节点数按2的幂指数规律增长，直到达到设定的批次最大值（默认为10%!）(MISSING)。

目前跟随Kubernetes一起进行升级的核心组件主要为kube-proxy和CoreDNS。

而CoreDNS有着自己的版本声明管理周期，其版本号并不与Kubernetes保持一致。为了保证CoreDNS与Kubernetes的版本兼容性，Kubernetes社区为CoreDNS的版本与Kubernetes版本设定了一个对应矩阵。在升级Kubernetes集群的过程中，我们会按照版本矩阵将CoreDNS升级到对应的版本。

### 12.4 总结

12.4 总结
随着软件的不断更新与迭代，如何安全、“平滑”地将现存软件系统升级到最新的版本也越来越受到广大用户的重视

### 下篇 实践篇（诊断之美）

从技术上来说，问题和一些底层的操作系统组件有关系，比如Systemd和D-Bus。

### 13.1 问题介绍

随着Kubernetes集群出货量剧增，线上用户零星地发现，集群会非常低概率地出现节点NotReady（集群就绪状态异常）的情况。

在节点NotReady之后，集群Master没有办法对这个节点做任何控制，比如下发新的Pod，又如抓取节点上正在运行的Pod的实时信息，如图13-1所示。

Master节点主要用来承载集群管控组件，比如调度器和控制器。而Worker节点主要用来“跑”业务。Kubelet是“跑”在各个节点上的代理，它负责与管控组件沟通，并按照管控组件的指示，直接管理Worker节点，如图13-2所示。

当集群节点进入NotReady状态的时候，我们需要做的第一件事情，是检查运行在节点上的Kubelet是否正常。在这个问题出现的时候，使用systemctl命令查看Kubelet状态（Kubelet是Systemd管理的一个daemon）发现它是正常运行的。当我们用journalctl查看Kubelet日志的时候，会发现图13-3中的错误。

 关于PLEG机制这个报错清楚地告诉我们，容器运行时是不工作的，且PLEG是不健康的。这里容器运行时指的就是docker daemon。Kubelet通过操作docker daemon来控制容器的生命周期。

而这里的PLEG指的是Pod lifecycle event generator，PLEG是Kubelet用来检查运行时的健康检查机制。这件事情本来可以由Kubelet使用polling的方式来做，但是polling有其成本高的缺陷，所以PLEG应运而生。

我们基本上可以确认容器运行时出了问题。在有问题的节点上，通过docker命令尝试运行新的容器，命令会没有响应，这说明上面的报错是准确的。

### 13.2 Docker栈

Docker作为阿里云Kubernetes集群使用的容器运行时，在1.11版之后，被拆分成了多个组件以适应OCI标准。拆分之后，其包括docker daemon、containerd、containerd-shim和runC。组件containerd负责集群节点上容器的生命周期管理，并向上为docker daemon提供gRPC接口，

### 13.3 什么是D-Bus

在Linux中，D-Bus是进程间互相通信的总线机制

我们可以使用busctl命令列出系统现有的所有bus。如图13-11所示，在问题发生的时候，我们看到问题节点bus name编号非常大。所以我们倾向于认为，是dbus某些相关的数据结构（比如name）的耗尽引起了这个问题。

### 13.4 Systemd是硬骨头

排查Systemd的问题我们用到了四个方法：使用（调试级别）日志、core dump、代码分析，以及live debugging。其中第一个、第三个和第四个结合起来使用，让我们在经过几天的“鏖战”之后，找到了问题的原因。但是这里我们先从“没用”的core dump说起。

这个问题在线上所有Kubernetes集群中，发生的频率大概是一个月两例。问题一直在发生，且只能在问题发生之后通过重启Systemd来处理，风险极大。我们分别向Systemd和runC社区提交了bug，但是一个很现实的问题是，他们并没有像阿里云这样的线上环境，重现这个问题的概率几乎是零，所以这个问题没有办法指望社区来解决。硬骨头还得我们自己啃。

### 13.5 问题的解决

这个问题的解决并没有那么直截了当。原因之一，是Systemd使用了同一个cookie变量来兼容dbus1和dbus2。对于dbus1来说，cookie是32位的，这个值在经过Systemd在三五个月中频繁创建和删除unit之后，是肯定会溢出的；而dbus2的cookie是64位的，可能到了“时间的尽头”它也不会溢出。

### 14.8 总结

在节点就绪状态这种场景下，Kubelet实际上实现了节点的“心跳”机制。Kubelet会定期把节点相关的各种状态同步到集群管控中，这些状态包括内存、PID、磁盘，当然也包括本章中关注的就绪状态等。Kubelet在监控或者管理集群节点的过程中，使用了各种插件来直接操作节点资源，包括网络、磁盘，甚至容器运行时等插件，这些插件的状况，会直接影响Kubelet甚至节点的状态。

### 第15章 命名空间回收机制失效

但如果要评选什么问题看起来微不足道事实上却足以让人绞尽脑汁，我相信答案中肯定有“删不掉”一类的问题，比如文件删不掉、进程结束不了、驱动卸载不了等。

### 15.1 问题背景介绍

即命名空间的状态被标记成了“Terminating”，却没有办法被完全删除。

### 15.2 集群管控入口

15.2 集群管控入口
因为删除操作是通过集群API Server来执行的，所以我们要分析API Server的行为。跟大多数集群组件类似，API Server提供了不同级别的日志输出。为了理解API Server的行为，我们将日志级别调整到最高级。然后，通过创建、删除tobedeletedb这个命名空间来重现问题。

### 15.3 命名空间控制器的行为

我们通过开启Kube Controller Manager最高级别日志，来研究这个组件的行为。在Kube Controller Manager的日志里，可以看到命名空间控制器在不断地尝试一个失败的操作，就是清理tobedeletedb这个命名空间里“收纳”的资源。

### 15.4 回到集群管控入口

以Metrics Server为例，它实现了metrics.Kubernetes.io/v1beta1这个API分组/版本。所有针对这个分组/版本的调用，都会被转发到Metrics Server。如下面命令行输出所示，Metrics Server的实现，主要用到一个服务和一个Pod。

### 15.5 节点与Pod的通信

这个问题实际上是API Server和Metrics Server Pod之间的通信问题。在阿里云Kubernetes集群环境里，API Server使用的是主机网络，即ECS的网络，而Metrics Server使用的是Pod网络。这两者之间的通信，依赖于VPC路由表的转发，如图15-5所示。

如果API Server运行在Node A上，那它的IP地址就是192.168.0.193。假设Metrics Server的IP是地址172.16.8.249，那么从API Server到Metrics Server的网络连接，必须要通过VPC路由表第一条路由规则转发。
检查集群VPC路由表，发现指向Metrics Server所在节点的路由表项缺失，所以API Server和Metrics Server之间的通信出了问题。

为了维持集群VPC路由表项的正确性，阿里云在Cloud Controller Manager内部实现了Route Controller。这个Controller在时刻监听着集群节点状态，以及VPC路由表状态。当发现路由表项缺失的时候，它会自动把缺失的路由表项填写回去。

### 15.6 集群节点访问云资源

Cloud Controller Manager获取VPC信息，是通过阿里云开放API来实现的。这基本上等于从云上一台ECS内部获取一个VPC实例的信息，而这需要ECS有足够的权限。目前的常规做法是，为ECS服务器授予RAM角色，同时给RAM角色绑定相应的角色授权，如图15-6所示。


如果集群组件以其所在节点的身份不能获取云资源的信息，那基本上有两种可能性：一是ECS没有绑定正确的RAM角色；二是RAM角色绑定的RAM角色授权没有定义正确的授权规则。

当我们把Effect修改成Allow之后，没多久，所有的Terminating状态的namespace全部消失了。

### 15.7 问题回顾

通过分析前三个组件的行为，我们发现是集群网络问题导致了API Server无法连接到Metrics Server；通过排查后三个组件，我们发现导致问题的根本原因是VPC路由表被删除且RAM角色授权策略被改动。

### 16.1 安全组扮演的角色

与专有集群不同的是，托管集群的Master系统组件以Pod的形式运行在管控集群里。这就是用Kubernetes管理Kubernetes的概念。

因为托管集群在用户的VPC里，而管控集群在阿里云管控账号的VPC里。所以这样的架构需要解决的一个核心问题，就是跨账号、跨VPC通信问题。


为了解决这个问题，此处用到类似传送门的技术。托管集群会在集群VPC里创建两个弹性网卡，这两个弹性网卡可以像普通云服务器一样通信。但是这两个网卡被挂载到托管集群的API Server Pod上，这就解决了跨VPC通信问题。

### 17.1 在线一半的微服务

这里的“2”说明这些Pod里都有两个容器，“1/2”则表示每个Pod里只有一个容器是就绪的，即通过readiness测试的。关于“2”这一点，我们下一节会深入讲，这里我们先看一下，为什么所有的Pod里都有一个容器没有就绪。

对于未定义readiness probe的容器，Kubelet认为，只要容器里的进程开始运行，容器就进入就绪状态了。所以1/2个就绪Pod意味着，有定义了readiness probe的容器没有通过Kubelet的测试。

### 17.2 认识服务网格

所以sidecar模式其实是“自带通信员”模式。有趣的是，在我们把sidecar和Pod绑定在一起的时候，sidecar在出流量转发时扮演着反向代理的角色，而在入流量接收的时候，可以做一些超过反向代理职责的一些事情。

### 17.3 代理与代理的生命周期管理

istio-proxy会启动两个进程，pilot-agent和Envoy。
如图17-4所示，Envoy是实际上负责流量管理等功能的代理，从业务容器出、入的数据流，都必须经过Envoy；而pilot-agent负责维护Envoy的静态配置，并且管理Envoy的生命周期。

### 17.5 控制面和数据面

DHCP服务器统一管理，动态分配IP地址给网络中的电脑，这里的DHCP服务器就是控制面，而每个动态获取IP的电脑就是数据面。

Istio的控制面Pilot使用gRPC协议对外暴露接口istio-pilot.istio-system:15010，而Envoy无法从Pilot处获取动态配置的原因是，在所有的Pod中，集群DNS都无法使用。

### 17.6 简单的原因

这个问题的原因其实比较简单，在sidecar容器istio-proxy里，Envoy不能访问Pilot的原因是集群DNS无法解析istio-pilot.istio-system这个服务名字。在容器里看到resolv.conf配置的DNS服务器地址是172.19.0.10，这个是集群默认的kube-dns服务地址。


### 17.7 阿里云服务网格（ASM）介绍

阿里云ASM中，Istio控制平面的组件全部托管，降低您使用的复杂度，您只需要专注于业务应用的开发部署。

### 18.1 连续重启的Citadel

Citadel作为证书分发中心，负责服务网格中每个服务身份证书的创建任务，以保障服务之间安全交流。

Citadel再也无法启动了。这直接导致集群无法创建新的虚拟服务和Pod实例。观察Citadel，发现其连续重启，并输出以下报错信息。

### 18.2 一般意义上的证书验证

证书的签发关系，会把证书连接成一棵证书签发关系树，如图18-1所示。顶部是根证书，一般由可信第三方持有，根证书都是自签名的，即其不需要其他机构来对其身份提供证明。其他层级的CA证书，都是由上一层的CA证书签发的。最底层的证书，是给具体应用使用的证书。

● 根证书验证。因为根证书既是签发者，又是被签发者，所以根证书验证只需要提供根证书本身，一般都能验证成功。

● 其他证书验证。需要提供证书本身，以及其上层的所有CA证书，包括根证书。

### 18.4 大神定理

有一条“定理”，就是有些问题我们绞尽脑汁，耗费大量时间也无法解决，但求助于资深的专家，可能只需要几分钟的时间就能解决。

因为实在想不通上面的逻辑，所以我请教了一位资深的专家。他看了一下报错，判断这是CA证书过期问题。使用Istio官方提供的证书验证脚本root-transition.sh，很快证实了他的判断。

### 18.5 Citadel证书体系

● 根证书和证书之间的签发关系。这种关系，保证了信任的传递性质。

### 18.6 经验

一条是，很多工程师写的代码，报错很容易词不达意。这个问题的报错，说的是“创建”证书失败，但实际上代码用的是验证集群已有CA证书的逻辑，所以我们排查问题时，需要特别留意这一点。

另一条是，不要放过任何一条相关信息，也就是我经常说的，“Every single bit matters”。这个问题实际上有另外一条线索，就是Pod和虚拟服务创建失败，其报错有更明显的提示信息。

### 18.7 总结

在Istio比较早期的版本中，自签名CA证书有效期只有一年时间，如果使用老版本Istio超过一年，就会遇到这个问题。当证书过期之后，我们创建新的虚拟服务或者Pod，都会因为CA证书过期而失败。而这时如果要重启Citadel，它会读取过期证书并验证其有效性，就会出现以上Cidatel不能启动的问题。

这个CA证书在Kubernetes集群中是以istio-ca-secret命名的Secret，我们可以使用openssl解码证书来查看有效期。这个问题比较简单的处理方法，就是删除这个Secret，并重启Citadel，这时Citadel会转而采用新建和验证自签名CA证书的逻辑并刷新CA证书。我们也可以参考官方网站页面extending self-signed certificate lifetime中的方式进行处理。