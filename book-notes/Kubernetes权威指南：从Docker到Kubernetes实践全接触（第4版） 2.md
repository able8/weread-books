## Kubernetes权威指南：从Docker到Kubernetes实践全接触（第4版）
> 龚正等

### 推荐序

容器化技术已经成为计算模型演化的一个开端，Kubernetes作为谷歌开源的Docker容器集群管理技术，在这场新的技术革命中扮演着重要的角色。Kubernetes正在被众多知名公司及企业采用，例如Google、VMware、CoreOS、腾讯、京东等，因此，Kubernetes站在了容器新技术变革的浪潮之巅，将具有不可预估的发展前景和商业价值。

### 自序

Kubernetes是将“一切以服务（Service）为中心，一切围绕服务运转”作为指导思想的创新型产品，这是它的一个亮点

Kubernetes的另一个亮点是自动化。在Kubernetes的解决方案中，一个服务可以自我扩展、自我诊断，并且容易升级，在收到服务扩容的请求后，Kubernetes会触发调度流程，最终在选定的目标节点上启动相应数量的服务实例副本，这些服务实例副本在启动成功后会自动加入负载均衡器中并生效，整个过程无须额外的人工操作

Kubernetes则以Docker为基础打造了一个云计算时代的全新分布式系统架构。

### 第1章 Kubernetes入门

Kubernetes是什么？
首先，它是一个全新的基于容器技术的分布式架构领先方案。

Kubernetes是一个完备的分布式系统支撑平台。Kubernetes具有完备的集群管理能力，包括多层次的安全防护和准入机制、多租户应用支撑能力、透明的服务注册和服务发现机制、内建的智能负载均衡器、强大的故障发现和自我修复能力、服务滚动升级和在线扩容能力、可扩展的资源自动调度机制，以及多粒度的资源配额管理能力。

共享Pause容器的网络栈和Volume挂载卷，因此它们之间的通信和数据交换更为高效

在集群管理方面，Kubernetes将集群中的机器划分为一个Master和一些Node。在Master上运行着集群管理相关的一组进程kube-apiserver 、 kube-controller-manager和kube-scheduler，这些进程实现了整个集群的资源管理、Pod调度、弹性伸缩、安全控制、系统监控和纠错等管理功能，并且都是自动完成的。

### 1.2 为什么要用Kubernetes

2015年，谷歌联合20多家公司一起建立了CNCF（Cloud Native Computing Foundation，云原生计算基金会）开源组织来推广Kubernetes，并由此开创了云原生应用（Cloud Native Application）的新时代。作为CNCF“钦定”的官方云原生平台，Kubernetes正在颠覆应用程序的开发方式。

数百家厂商和技术社区共同构建了非常强大的云原生生态，市面上几乎所有提供云基础设施的公司都以原生形式将Kubernetes作为底层平台，可以预见，会有大量的新系统选择Kubernetes，不论这些新系统是运行在企业的本地服务器上，还是被托管到公有云上。

在采用Kubernetes解决方案之后，只需一个精悍的小团队就能轻松应对。在这个团队里，只需一名架构师负责系统中服务组件的架构设计，几名开发工程师负责业务代码的开发，一名系统兼运维工程师负责Kubernetes的部署和运维，因为Kubernetes已经帮我们做了很多。

全面拥抱微服务架构。微服务架构的核心是将一个巨大的单体应用分解为很多小的互相连接的微服务，一个微服务可能由多个实例副本支撑，副本的数量可以随着系统的负荷变化进行调整。微服务架构使得每个服务都可以独立开发、升级和扩展，因此系统具备很高的稳定性和快速迭代能力，开发者也可以自由选择开发技术。谷歌、亚马逊、eBay、Netflix等大型互联网公司都采用了微服务架构，谷歌更是将微服务架构的基础设施直接打包到Kubernetes解决方案中，让我们可以直接应用微服务架构解决复杂业务系统的架构问题。

在Kubernetes的架构方案中完全屏蔽了底层网络的细节，基于Service的虚拟IP地址（Cluster IP）的设计思路让架构与底层的硬件拓扑无关，我们无须改变运行期的配置文件，就能将系统从现有的物理机环境无缝迁移到公有云上。

Kubernetes内在的服务弹性扩容机制可以让我们轻松应对突发流量。在服务高峰期，我们可以选择在公有云中快速扩容某些Service的实例副本以提升系统的吞吐量，这样不仅节省了公司的硬件投入，还大大改善了用户体验。中国铁路总公司的12306购票系统，在客流高峰期（如节假日）就租用了阿里云进行分流。

Kubernetes系统架构超强的横向扩容能力可以让我们的竞争力大大提升。

### 1.4 Kubernetes的基本概念和术语

Kubernetes里的Master指的是集群控制节点，在每个Kubernetes集群里都需要有一个Master来负责整个集群的管理和控制，基本上Kubernetes的所有控制命令都发给它

Master通常会占据一个独立的服务器（高可用部署建议用3台服务器），主要原因是它太重要了，是整个集群的“首脑”，如果它宕机或者不可用，那么对集群内容器应用的管理都将失效。

Master上运行着以下关键进程。◎ Kubernetes API Server（kube-apiserver）：提供了HTTP Rest接口的关键服务进程，是Kubernetes里所有资源的增、删、改、查等操作的唯一入口，也是集群控制的入口进程。◎ Kubernetes Controller Manager（kube-controller-manager）：Kubernetes里所有资源对象的自动化控制中心，可以将其理解为资源对象的“大总管”。◎ Kubernetes Scheduler（kube-scheduler）：负责资源调度（Pod调度）的进程，相当于公交公司的“调度室”。另外，在Master上通常还需要部署etcd服务，因为Kubernetes里的所有资源对象的数据都被保存在etcd中。

◎ kubelet：负责Pod对应的容器的创建、启停等任务，同时与Master密切协作，实现集群管理的基本功能。◎ kube-proxy：实现Kubernetes Service的通信与负载均衡机制的重要组件。◎ Docker Engine（docker）：Docker引擎，负责本机的容器创建和管理工作。

一旦Node被纳入集群管理范围，kubelet进程就会定时向Master汇报自身的情报，例如操作系统、Docker版本、机器的CPU和内存情况，以及当前有哪些Pod在运行等，这样Master就可以获知每个Node的资源使用情况，并实现高效均衡的资源调度策略。而某个Node在超过指定时间不上报信息时，会被Master判定为“失联”，Node的状态被标记为不可用（Not Ready），随后Master会触发“工作负载大转移”的自动流程。

在一切正常时设置Node为Ready状态（Ready=True），该状态表示Node处于健康状态，Master将可以在其上调度新的任务了（如启动Pod）。

我们看到每个Pod都有一个特殊的被称为“根容器”的Pause容器。Pause容器对应的镜像属于Kubernetes平台的一部分

入业务无关并且不易死亡的Pause容器作为Pod的根容器，以它的状态代表整个容器组的状态，就简单、巧妙地解决了这个难题。

Pod里的多个业务容器共享Pause容器的IP，共享Pause容器挂接的Volume，这样既简化了密切关联的业务容器之间的通信问题，也很好地解决了它们之间的文件共享问题。

在Kubernetes里，一个Pod里的容器与另外主机上的Pod容器能够直接通信。

普通的Pod及静态Pod（Static Pod）。后者比较特殊，它并没被存放在Kubernetes的etcd存储里，而是被存放在某个具体的Node上的一个具体文件中，并且只在此Node上启动、运行。

一下Kubernetes的Event概念。Event是一个事件的记录，记录了事件的最早产生时间、最后重现时间、重复次数、发起者、类型，以及导致此事件的原因等众多信息。Event通常会被关联到某个具体的资源对象上，是排查故障的重要参考信息，

在Kubernetes里，一个计算资源进行配额限定时需要设定以下两个参数。
◎ Requests：该资源的最小申请量，系统必须满足要求。
◎ Limits：该资源最大允许使用的量，不能被突破，当容器试图使用超过这个量的资源时，可能会被Kubernetes“杀掉”并重启。

◎ kube-controller进程通过在资源对象RC上定义的Label Selector来筛选要监控的Pod副本数量，使Pod副本数量始终符合预期设定的全自动控制流程。

◎ kube-proxy进程通过Service的Label Selector来选择对应的Pod，自动建立每个Service到对应Pod的请求转发路由表，从而实现Service的智能负载均衡机制

Label和Label Selector共同构成了Kubernetes系统中核心的应用模型，使得被管理对象能够被精细地分组管理，同时实现了整个集群的高可用性

Pod的副本数量在任意时刻都符合某个预期值，所以RC的定义包括如下几个部分。
◎ Pod期待的副本数量。
◎ 用于筛选目标Pod的Label Selector。
◎ 当Pod的副本数量小于预期数量时，用于创建新Pod的Pod模板（template）。

在我们定义了一个RC并将其提交到Kubernetes集群中后，Master上的Controller Manager组件就得到通知，定期巡检系统中当前存活的目标Pod，并确保目标Pod实例的数量刚好等于此RC的期望值

通过RC，Kubernetes实现了用户应用集群的高可用性，并且大大减少了系统管理员在传统IT环境中需要完成的许多手工运维工作（如主机监控脚本、应用监控脚本、故障恢复脚本等）。

在运行时，我们可以通过修改RC的副本数量，来实现Pod的动态缩放（Scaling


        $ kubectl scale rc redis-slave --replicas=3￼

需要注意的是，删除RC并不会影响通过该RC已创建好的Pod。为了删除所有Pod，可以设置replicas的值为0，然后更新该RC。另外，kubectl提供了stop和delete命令来一次性删除RC和RC控制的全部Pod。

通过RC机制，Kubernetes很容易就实现了这种高级实用的特性，被称为“滚动升级”（Rolling Update）

“Replication Controller”无法准确表达它的本意，所以在Kubernetes 1.2中，升级为另外一个新概念——Replica Set，官方解释其为“下一代的RC”。Replica Set与RC当前的唯一区别是，Replica Sets支持基于集合的Label selector（Set-based selector），而RC只支持基于等式的Label Selector（equality-based selector），这使得Replica Set的功能更强。

此外，我们当前很少单独使用Replica Set，它主要被Deployment这个更高层的资源对象所使用，从而形成一整套Pod创建、删除、更新的编排机制。我们在使用Deployment时，无须关心它是如何创建和维护Replica Set的，这一切都是自动发生的。
Replica Set与Deployment这两个重要的资源对象逐步替代了之前RC的作用，是Kubernetes 1.3里Pod自动扩容（伸缩）这个告警功能实现的基础，也将继续在Kubernetes未来的版本中发挥重要的作用。

◎ 在大多数情况下，我们通过定义一个RC实现Pod的创建及副本数量的自动控制。
◎ 在RC里包括完整的Pod定义模板。
◎ RC通过Label Selector机制实现对Pod副本的自动控制。

Deployment相对于RC的一个最大升级是我们可以随时知道当前Pod“部署”的进度。

Deployment的典型使用场景有以下几个。
◎ 创建一个Deployment对象来生成对应的Replica Set并完成Pod副本的创建。
◎ 检查Deployment的状态来看部署动作是否完成（Pod副本数量是否达到预期的值）。
◎ 更新Deployment以创建新的Pod（比如镜像升级）。
◎ 如果当前Deployment不稳定，则回滚到一个早先的Deployment版本。

◎ UP-TO-DATE：最新版本的Pod的副本数量，用于指示在滚动升级的过程中，有多少个Pod副本已经成功升级。
◎ AVAILABLE：当前集群中可用的Pod副本数量，即集群中当前存活的Pod数量

Pod的管理对象，除了RC和Deployment，还包括ReplicaSet、DaemonSet、StatefulSet、Job等，分别用于不同的应用场景中，将在第3章进行详细介绍。

谷歌对Kubernetes的定位目标——自动化、智能化。在谷歌看来，分布式系统要能够根据当前负载的变化自动触发水平扩容或缩容，因为这一过程可能是频繁发生的、不可预料的，所以手动控制的方式是不现实的。

HPA与之前的RC、Deployment一样，也属于一种Kubernetes资源对象。通过追踪分析指定RC控制的所有目标Pod的负载变化情况，来确定是否需要有针对性地调整目标Pod的副本数量，这是HPA的实现原理。当前，HPA有以下两种方式作为Pod负载的度量指标。
◎ CPUUtilizationPercentage。
◎ 应用程序自定义的度量指标，比如服务在每秒内的相应请求数（TPS或QPS）。

但现实中有很多服务是有状态的，特别是一些复杂的中间件集群，例如MySQL集群、MongoDB集群、Akka集群、ZooKeeper集群等，这些应用集群有4个共同点。
（1）每个节点都有固定的身份ID，通过这个ID，集群中的成员可以相互发现并通信。
（2）集群的规模是比较固定的，集群规模不能随意变动。
（3）集群中的每个节点都是有状态的，通常会持久化数据到永久存储中。
（4）如果磁盘损坏，则集群里的某个节点无法正常运行，集群功能受损。

◎ StatefulSet里的每个Pod都有稳定、唯一的网络标识，可以用来发现集群内的其他成员。假设StatefulSet的名称为kafka，那么第1个Pod叫kafka-0，第2个叫kafka-1，以此类推。
◎ StatefulSet控制的Pod副本的启停顺序是受控的，操作第n个Pod时，前n-1个Pod已经是运行且准备好的状态。
◎ StatefulSet里的Pod采用稳定的持久化存储卷，通过PV或PVC来实现，删除Pod时默认不会删除与StatefulSet相关的存储卷（为了保证数据的安全）。

Headless Service与普通Service的关键区别在于，它没有Cluster IP，如果解析Headless Service的DNS域名，则返回的是该Service对应的全部Pod的Endpoint列表。StatefulSet在Headless Service的基础上又为StatefulSet控制的每个Pod实例都创建了一个DNS域名，这个域名的格式为：

Service服务也是Kubernetes里的核心资源对象之一，Kubernetes里的每个Service其实就是我们经常提起的微服务架构中的一个微服务，

拥有强大的分布式能力、弹性扩展能力、容错能力，程序架构也变得简单和直观许多

运行在每个Node上的kube-proxy进程其实就是一个智能的软件负载均衡器，负责把对Service的请求转发到后端的某个Pod实例上，并在内部实现服务的负载均衡与会话保持机制。

：Service没有共用一个负载均衡器的IP地址，每个Service都被分配了一个全局唯一的虚拟IP地址，这个虚拟IP被称为Cluster IP。

Kubernetes中的Volume与Pod的生命周期相同，但与容器的生命周期不相关，当容器终止或者重启时，Volume中的数据也不会丢失

hostPath为在Pod上挂载宿主机上的文件或目录，它通常可以用于以下几方面。
◎ 容器应用程序生成的日志文件需要永久保存时，可以使用宿主机的高速文件系统进行存储。
◎ 需要访问宿主机上Docker引擎内部数据结构的容器应用时，可以通过定义hostPath为宿主机/var/lib/docker目录，使容器内部应用可以直接访问Docker的文件系统。

◎ PV只能是网络存储，不属于任何Node，但可以在每个Node上访问。◎ PV并不是被定义在Pod上的，而是独立于Pod之外定义的。

如果某个Pod想申请某种类型的PV，则首先需要定义一个PersistentVolumeClaim对象

后，在Pod的Volume定义中引用上述PVC即可：

Kubernetes提供了一种内建机制，将存储在etcd中的ConfigMap通过Volume映射的方式变成目标Pod内的配置文件

Kubernetes ConfigMap就成了分布式系统中最为简单（使用方法简单，但背后实现比较复杂）且对应用无侵入的配置中心。

ConfigMap配置集中化的一种简单方案

### 2.2 使用kubeadm工具快速安装Kubernetes集群

执行kubectl get nodes命令，会发现Kubernetes提示Master为NotReady状态，这是因为还没有安装CNI网络插件：

### 3.8 Pod健康检查和服务可用性检查

LivenessProbe探针：用于判断容器是否存活（Running状态），如果LivenessProbe探针探测到容器不健康，则kubelet将杀掉该容器，并根据容器的重启策略做相应的处理。如果一个容器不包含LivenessProbe探针，那么kubelet认为该容器的LivenessProbe探针返回的值永远是Success

ReadinessProbe探针：用于判断容器服务是否可用（Ready状态），达到Ready状态的Pod才可以接收请求。对于被Service管理的Pod，Service与Pod Endpoint的关联关系也将基于Pod是否Ready进行设置。如果在运行过程中Ready状态变为False，则系统自动将其从Service的后端Endpoint列表中隔离出去，后续再把恢复到Ready状态的Pod加回后端Endpoint列表。这样就能保证客户端在访问Service时不会被转发到服务不可用的Pod实例上。

### 3.9 玩转Pod调度

（2）如果要选择多种合适的目标节点，比如SSD磁盘的节点或者超高速硬盘的节点，该怎么办？Kubernates引入了NodeAffinity（节点亲和性设置）来解决该需求。

3.9.2 NodeSelector：定向调度


### 4.3 Headless Service

开发人员希望自己控制负载均衡的策略，不使用Service提供的默认负载均衡的功能，或者应用程序希望知道属于同组服务的其他实例。Kubernetes提供了Headless Service来实现这种功能，即不为Service设置ClusterIP（入口IP地址），仅通过Label Selector将后端的Pod列表返回给调用的客户端。

Service就不再具有一个特定的ClusterIP地址，对其进行访问将获得包含Label“app=nginx”的全部Pod列表，然后客户端程序自行决定如何处理这个Pod列表。例如，StatefulSet就是使用Headless Service为客户端返回多个服务地址的。

### 4.6 Ingress：HTTP 7层路由机制

从Kubernetes 1.1版本开始新增Ingress资源对象，用于将不同URL的访问请求转发到后端不同的Service，以实现HTTP层的业务路由机制。Kubernetes使用了一个Ingress策略定义和一个具体的Ingress Controller，两者结合并实现了一个完整的Ingress负载均衡器。

使用Ingress进行负载分发时，Ingress Controller基于Ingress规则将客户端请求直接转发到Service对应的后端Endpoint（Pod）上，这样会跳过kube-proxy的转发功能，kube-proxy不再起作用。如果Ingress Controller提供的是对外服务，则实际上实现的是边缘路由器的功能。

为使用Ingress，需要创建Ingress Controller（带一个默认backend服务）和Ingress策略设置来共同完成。

在定义Ingress策略之前，需要先部署Ingress Controller，以实现为所有后端Service都提供一个统一的入口。Ingress Controller需要实现基于不同HTTP URL向后转发的负载分发规则，并可以灵活设置7层负载分发策略。

在Kubernetes中，Ingress Controller将以Pod的形式运行，监控API Server的/ingress接口后端的backend services，如果Service发生变化，则Ingress Controller应自动更新其转发规则。
下面的例子使用Nginx来实现一个Ingress Controller，需要实现的基本逻辑如下。

（2）基于Ingress的定义，生成Nginx所需的配置文件/etc/nginx/nginx.conf。（3）执行nginx -s reload命令，重新加载nginx.conf配置文件的内容。

本例使用谷歌提供的nginx-ingress-controller镜像来创建Ingress Controller。该Ingress Controller以daemonset的形式进行创建，在每个Node上都将启动一个Nginx服务。

这里为Nginx容器设置了hostPort，将容器应用监听的80和443端口号映射到物理机上，使得客户端应用可以通过URL地址“http://物理机IP:80”或“https://物理机IP:443”来访问该Ingress Controller。这使得Nginx类似于通过NodePort映射到物理机的Service，成为代替kube-proxy的HTTP层的Load Balancer：

需要说明的是，客户端只能通过域名mywebsite.com访问服务，这时要求客户端或者DNS将mywebsite.com域名解析到后端多个Node的真实IP地址上。


        # curl --resolve mywebsite.com:80:192.168.18.3 http://mywebsite.com/demo/
或
￼
        # curl -H 'Host:mywebsite.com' http://192.168.18.3/demo/￼

1．转发到单个后端服务上基于这种设置，客户端到Ingress Controller的访问请求都将被转发到后端的唯一Service上，在这种情况下Ingress无须定义任何rule。

3．不同的域名（虚拟主机名）被转发到不同的服务上这种配置常用于一个网站通过不同的域名或虚拟主机名提供不同服务的场景，例如foo.bar.com域名由service1提供服务，bar.foo.com域名由service2提供服务。

注意，使用无域名的Ingress转发规则时，将默认禁用非安全HTTP，强制启用HTTPS。例如，当使用Nginx作为IngressController时，在其配置文件/etc/nginx/nginx.conf中将会自动设置下面的规则，将全部HTTP的访问请求直接返回301错

为了Ingress提供HTTPS的安全访问，可以为Ingress中的域名进行TLS安全证书的设置。设置的步骤如下。
（1）创建自签名的密钥和SSL证书文件。
（2）将证书保存到Kubernetes中的一个Secret资源对象上。
（3）将该Secret对象设置到Ingress中。

### 第5章 核心组件运行机制

Kubernetes API Server的核心功能是提供Kubernetes各类资源对象（如Pod、RC、Service等）的增、删、改、查及Watch等HTTP Rest接口，成为集群内各个功能模块之间数据交互和通信的中心枢纽，是整个系统的数据总线和数据中心。

（1）是集群管理的API入口。
（2）是资源配额控制的入口。
（3）提供了完备的集群安全机制。

Kubernetes API Server通过一个名为kube-apiserver的进程提供服务，该进程运行在Master上。

通过命令行工具kubectl来与Kubernetes API Server交互，它们之间的接口是RESTful API。

在8001端口启动代理，并且拒绝客户端访问RC的API：

开发基于Kubernetes的管理平台。比如调用Kubernetes API来完成Pod、Service、RC等资源对象的图形化创建和管理界面，此时可以使用Kubernetes及各开源社区为开发人员提供的各种语言版本的ClientLibrary。后面会介绍通过编程方式访问API Server的一些细节技术。

采用了高性能的etcd数据库而非传统的关系数据库，不仅解决了数据的可靠性问题，也极大提升了APIServer数据访问层的性能。在常见的公有云环境中，一个3节点的etcd集群在轻负载环境中处理一个请求的时间可以低于1ms，在重负载环境中可以每秒处理超过30000个请求。

即可获取该节点上所有运行中的Pod：

需要说明的是：这里获取的Pod的信息数据来自Node而非etcd数据库，所以两者可能在某些时间点有所偏差。

说Kubernetes Proxy API里关于Pod的相关接口，通过这些接口，我们可以访问Pod里某个容器提供的服务（如Tomcat在8080端口的服务）：

每个Node上的kubelet每隔一个时间周期，就会调用一次API Server的REST接口报告自身状态，API Server在接收到这些信息后，会将节点状态信息更新到etcd中

kubelet也通过API Server的Watch接口监听Pod信息，如果监听到新的Pod副本被调度绑定到本节点，则执行Pod对应的容器创建和启动逻辑；如果监听到Pod对象被删除，则删除本节点上相应的Pod容器；如果监听到修改Pod的信息，kubelet就会相应地修改本节点的Pod容器。

执行Pod调度逻辑，在调度成功后将Pod绑定到目标节点上。

为了缓解集群各模块对API Server的访问压力，各功能模块都采用缓存机制来缓存数据。各功能模块定时从APIServer获取指定的资源对象信息（通过List-Watch方法），然后将这些信息保存到本地缓存中，功能模块在某些情况下不直接访问API Server，而是通过访问缓存数据来间接访问API Server。

### 5.2 Controller Manager原理解析

它们通过API Server提供的（List-Watch）接口实时监控集群中特定资源的状态变化，当发生各种故障导致某资源对象的状态发生变化时，Controller会尝试将其状态调整为期望的状态。比

Controller Manager是Kubernetes中各种操作系统的管理者，是集群内部的管理控制中心，也是Kubernetes自动化功能的核心。

Controller Manager内部包含Replication Controller、Node Controller、ResourceQuotaController、Namespace Controller、ServiceAccount Controller、Token Controller、Service Controller及Endpoint Controller这8种Controller，

kubelet进程在启动时通过API Server注册自身的节点信息，并定时向API Server汇报状态信息，API Server在接收到这些信息后，会将这些信息更新到etcd中。在etcd中存储的节点信息包括节点健康状况、节点资源、节点名称、节点地址信息、操作系统版本、Docker版本、kubelet版本等。节点健康状况包含“就绪”（True）“未就绪”（False）和“未知”（Unknown）三种。

答案是每个Node上的kube-proxy进程，kube-proxy进程获取每个Service的Endpoints，实现了Service的负载均衡功能

### 5.3 Scheduler原理解析

Kubernetes Scheduler在整个系统中承担了“承上启下”的重要功能，“承上”是指它负责接收Controller Manager创建的新Pod，为其安排一个落脚的“家”——目标Node；“启下”是指安置工作完成后，目标Node上的kubelet服务进程接管后继工作，负责Pod生命周期中的“下半生”。

### 5.4 kubelet运行机制解析

每个kubelet进程都会在API Server上注册节点自身的信息，定期向Master汇报节点资源的使用情况，并通过cAdvisor监控容器和节点资源。

kubelet通过API Server监听etcd目录，同步Pod列表。

kubelet监听etcd，所有针对Pod的操作都会被kubelet监听。如果发现有新的绑定到本节点的Pod，则按照Pod清单的要求创建该Pod。如果发现本地的Pod被修改，则kubelet会做出相应的修改，比如在删除Pod中的某个容器时，会通过DockerClient删除该容器。如果发现删除本节点的Pod，则删除相应的Pod，并通过Docker Client删除Pod中的容器。kubelet读取监听到的信息，如果是创建和修改Pod任务，则做如下处理。（1）为该Pod创建一个数据目录。（2）从API Server读取该Pod清单。（3）为该Pod挂载外部卷（External Volume）。（4）下载Pod用到的Secret。（5）检查已经运行在节点上的Pod，如果该Pod没有容器或Pause容器（“kubernetes/pause”镜像创建的容器）没有启动，则先停止Pod里所有容器的进程。如果在Pod中有需要删除的容器，则删除这些容器。

用“kubernetes/pause”镜像为每个Pod都创建一个容器。该Pause容器用于接管Pod中所有其他容器的网络。每创建一个新的Pod，kubelet都会先创建一个Pause容器，然后创建其他容器。“kubernetes/pause”镜像大概有200KB，是个非常小的容器镜像。

一类是LivenessProbe探针，用于判断容器是否健康并反馈给kubelet。如果LivenessProbe探针探测到容器不健康，则kubelet将删除该容器，并根据容器的重启策略做相应的处理。

ReadinessProbe探针，用于判断容器是否启动完成，且准备接收请求。如果ReadinessProbe探针检测到容器启动失败，则Pod的状态将被修改，Endpoint Controller将从Service的Endpoint中删除包含该容器所在Pod的IP地址的Endpoint条目。

### 5.5 kube-proxy运行机制解析

每个Node上都会运行一个kube-proxy服务进程，我们可以把这个进程看作Service的透明代理兼负载均衡器，其核心功能是将到某个Service的访问请求转发到后端的多个Pod实例上。此外，Service的Cluster IP与NodePort等概念是kube-proxy服务通过iptables的NAT转换实现的，kube-proxy在运行过程中动态创建与Service相关的iptables规则，这些规则实现了将访问服务（Cluster IP或NodePort）的请求负载分发到后端Pod的功能。

kube-proxy进程是一个真实的TCP/UDP代理，类似HA Proxy，负责从Service到Pod的访问流量的转发，这种模式被称为userspace（用户空间代理）模式。

通过API Server的Watch接口实时跟踪Service与Endpoint的变更信息，并更新对应的iptables规则，Client的请求流量则通过iptables的NAT机制“直接路由”到目标Pod。

iptables模式完全工作在内核态，不用再经过用户态的kube-proxy中转，因而性能更强。

iptables模式虽然实现起来简单，但存在无法避免的缺陷：在集群中的Service和Pod大量增加以后，iptables中的规则会急速膨胀，导致性能显著下降，在某些极端情况下甚至会出现规则丢失的情况，并且这种故障难以重现与排查，于是Kubernetes从1.8版本开始引入第3代的IPVS（IP Virtual Server）模式，如图5.16所示。IPVS在Kubernetes 1.11中升级为GA稳定版。

iptables与IPVS虽然都是基于Netfilter实现的，但因为定位不同，二者有着本质的差别：iptables是为防火墙而设计的；IPVS则专门用于高性能负载均衡，并使用更高效的数据结构（Hash表），允许几乎无限的规模扩张，因此被kube-proxy采纳为第三代模式。与iptables相比，IPVS拥有以下明显优势：◎ 为大型集群提供了更好的可扩展性和性能；◎ 支持比iptables更复杂的复制均衡算法（最小负载、最少连接、加权等）；◎ 支持服务器健康检查和连接重试等功能；◎ 可以动态修改ipset的集合，即使iptables的规则正在使用这个集合。由于IPVS无法提供包过滤、airpin-masquerade tricks（地址伪装）、SNAT等功能，因此在某些场景（如NodePort的实现）下还要与iptables搭配使用。在IPVS模式下，kube-proxy又做了重要的升级，即使用iptables的扩展ipset，而不是直接调用iptables来生成规则链。

iptables规则链是一个线性的数据结构，ipset则引入了带索引的数据结构，因此当规则很多时，也可以很高效地查找和匹配。

### 6.1 API Server认证管理

我们知道，Kubernetes集群提供了3种级别的客户端身份认证方式。
◎ 最严格的HTTPS证书认证：基于CA根证书签名的双向数字证书认证方式。
◎ HTTP Token认证：通过一个Token来识别合法用户。
◎ HTTP Base认证：通过用户名+密码的方式认证。

### 第8章 共享存储原理

8.1 共享存储机制概述
Kubernetes对于有状态的容器应用或者对数据需要持久化的应用，不仅需要将容器内的目录挂载到宿主机的目录或者emptyDir临时存储卷，而且需要更加可靠的存储来保存应用产生的重要数据，以便容器应用在重建之后仍然可以使用之前的数据。

PV是对底层网络共享存储的抽象，将共享存储定义为一种“资源”，比如Node也是一种容器应用可以“消费”的资源。PV由管理员创建和配置，它与共享存储的具体实现直接相关

PVC则是用户对存储资源的一个“申请”。就像Pod“消费”Node的资源一样，PVC能够“消费”PV资源。PVC可以申请特定的存储空间和访问模式。

通过StorageClass的定义，管理员可以将存储资源定义为某种类别（Class），正如存储设备对于自身的配置描述（Profile），例如“快速存储”“慢速存储”“有数据冗余”“无数据冗余”等。用户根据StorageClass的描述就能够直观地得知各种存储资源的特性，就可以根据应用对存储资源的需求去申请存储资源了。

### 8.2 PV详解

PV作为存储资源，主要包括存储能力、访问模式、存储类型、回收策略、后端存储类型等关键信息的设置。

声明的PV具有如下属性：5GiB存储空间，访问模式为ReadWriteOnce，存储类型为slow（要求在系统中已存在名为slow的StorageClass），回收策略为Recycle，并且后端存储类型为nfs（设置了NFS Server的IP地址和路径）：

某个PV在生命周期中可能处于以下4个阶段（Phaes）之一。◎ Available：可用状态，还未与某个PVC绑定。◎ Bound：已与某个PVC绑定。◎ Released：绑定的PVC已经删除，资源已释放，但没有被集群回收。◎ Failed：自动资源回收失败。

### 8.3 PVC详解

PVC作为用户对存储资源的需求申请，主要包括存储空间请求、访问模式、PV选择条件和存储类别等信息的设置。

PVC和PV都受限于Namespace，PVC在选择PV时受到Namespace的限制，只有相同Namespace中的PV才可能与PVC绑定。Pod在引用PVC时同样受Namespace的限制，只有相同Namespace中的PVC才能挂载到Pod内。

### 8.4 PV和PVC的生命周期

Kubernetes支持两种资源的供应模式：静态模式（Static）和动态模式（Dynamic）。资源供应的结果就是创建好的PV。

### 9.1 REST简述

（3）缓存：为了改善无状态性带来的网络的低效性，客户端缓存规范出现。缓存规范允许隐式或显式地标记一个response中的数据，赋予了客户端缓存response数据的功能，这样就可以为以后的request共用缓存的数据消除部分或全部交互，提高了网络效率。

### 9.2 Kubernetes API详解

Kubernetes开发人员认为，任何成功的系统都会经历一个不断成长和不断适应各种变更的过程。因此，他们期望Kubernetes API是不断变更和增长的。同时，他们在设计和开发时，有意识地兼容了已存在的客户需求

在Kubernetes 1.13版本及之前的版本中，Master的API Server服务提供了Swagger格式的API网页。Swagger UI是一款REST API文档在线自动生成和功能测试软件，

在Kubernetes API中，一个API的顶层（Top Level）元素由kind、apiVersion、metadata、spec和status这5部分组成，

（2）name：对象的名称，在一个命名空间中名称应具备唯一性。
（3）uid：系统为每个对象都生成的唯一ID，符合RFC 4122规范的定义。

（2）annotations：用户可定义的“注解”，键和值都为字符串的map，被Kubernetes内部进程或者某些外部工具使用，用于存储和获取关于该对象的特定元数据。

6）selfLink ：通过API访问资源自身的URL，例如一个Pod的link可能是“/api/v1/namespaces/ default/pods/frontend-o8bg4”。

spec是集合类的元素类型，用户对需要管理的对象进行详细描述的主体部分都在spec里给出，它会被Kubernetes持久化到etcd中保存，系统通过spec的描述来创建或更新对象，以达到用户期望的对象运行状态

（1）phase：描述对象所处的生命周期阶段，phase的典型值是Pending（创建中）、Running、Active（正在运行中）或Terminated（已终结），这几种状态对于不同的对象可能有轻微的差别，此外，关于当前phase附加的详细说明可能包含在其他域中。

Kubernetes从1.14版本开始，使用OpenAPI（https://www.openapis.org）的格式对API进行查询，其访问地址为http://<master-ip>: <master-port>/openapi/v2。例如，使用命令行工具curl进行查询：

### 9.4 Kubernetes API的扩展

CRD本身只是一段声明，用于定义用户自定义的资源对象。但仅有CRD的定义并没有实际作用，用户还需要提供管理CRD对象的CRD控制器（CRD Controller），才能实现对CRD对象的管理。CRD控制器通常可以通过Go语言进行开发，并需要遵循Kubernetes的控制器开发规范，基于客户端库client-go进行开发，需

CRD极大扩展了Kubernetes的能力，使用户像操作Pod一样操作自定义的各种资源对象。CRD已经在一些基于Kubernetes的第三方开源项目中得到广泛应用，包括CSI存储插件、Device Plugin（GPU驱动程序）、Istio（Service Mesh管理）等，已经逐渐成为扩展Kubernetes能力的标准。

### 10.6 Pod Disruption Budget（主动驱逐保护）

在Kubernetes集群运行的过程中，许多管理操作都可能对Pod进行主动驱逐，“主动”一词意味着这一操作可以安全地延迟一段时间，目前主要针对以下两种场景。
◎ 节点的维护或升级时（kubectl drain）。
◎ 对应用的自动缩容操作（autoscaling down）。

对于主动驱逐的场景来说，应用如果能够保持存活的Pod的数量，则会非常有用。通过使用PodDisruptionBudget，应用可以保证那些会主动移除Pod的集群操作永远不会在同一时间停掉太多Pod，从而导致服务中断或者服务降级等。

PodDisruptionBudget

◎ minAvailable：指定驱逐过程需要保障的最少Pod数量。minAvailable可以是一个数字，也可以是一个百分比，例如100%!就(MISSING)表示不允许进行主动驱逐。

（2）接下来创建一个PodDisruptionBudget对象：

PodDisruptionBudget使用的是和Deployment一样的Label Selector，并且设置存活Pod的数量不得少于3个。

### 10.9 集群统一日志管理

在容器中输出到控制台的日志，都会以*-json.log的命名方式保存在/var/lib/docker/containers/目录下，这就为日志采集和后续处理奠定了基础。

在各Node上都运行了一个Fluentd容器，采集本节点/var/log和/var/lib/docker/containers两个目录下的日志进程，将其汇总到Elasticsearch集群，最终通过Kibana完成和用户的交互工作。

◎ 利用DaemonSet让Fluentd Pod在每个Node上运行。

### 10.12 Helm：Kubernetes应用包管理工具

Helm是一个由CNCF孵化和管理的项目，用于对需要在Kubernetes上部署的复杂应用进行定义、安装和更新。Helm以Chart的方式对应用软件进行描述，可以方便地创建、版本化、共享和发布复杂的应用软件。

◎ Release：在Kubernetes集群上运行的一个Chart实例。在同一个集群上，一个Chart可以被安装多次。例如有一个MySQLChart，如果想在

helm status命令跟踪Release的状态：

通过helm inspect命令查看Chart的可配置内容：

还可以用来表达多层结构的变量--set outer.inner=value：

大括号（{}）可以用来表达列表数据，例如--set name={a,b,c}会被翻译成：

当一个Chart发布新版本或者需要修改一个Release的配置时，就需要使用helm upgrade命令了。helm upgrade命令会利用用户提供的更新信息来对Release进行更新。因为Kubernetes Chart可能会有很大的规模或者相对复杂的关系，所以Helm会尝试进行最小影响范围的更新，只更新相对于上一个Release来说发生变化的内容。

我们可以用Helm的list指令查看Release的信息，会发现revision一列发生了变化。接下来通过kubectl的getpods，deploy指令可以看到Pod已经更新；通过kubectl describe deploy指令还会看到Deployment的更新过程和一系列的ScalingReplicaSet事件。

可以通过helm list命令列出在集群中部署的Release。如果给list加上--deleted参数，则会列出所有删除的Release；--all参数会列出所有的Release，包含删除的、现存的及失败的Release。正因为Helm会保存所有被删除Release的信息，所以Release的名字是不可复用的

### 第11章 Trouble Shooting指导

对象关联的Event事件。这些事件记录了相关主题、发生时间、最近发生时间、发生次数及事件原因等，对排查故障非常有价值。

对于服务、容器方面的问题，可能需要深入容器内部进行故障诊断，此时可以通过查看容器的运行日志来定位具体问题。

kube-apiserver、kube-schedule、kube-controler-manager服务日志，以及各个Node上的kubelet、kube-proxy服务日志，通过综合判断各种信息，就能找到问题的成因并解决问题。

### 11.1 查看系统Event

◎ 没有可用的Node以供调度。◎ 开启了资源配额管理，但在当前调度的目标节点上资源不足。◎ 镜像下载失败。

### 11.2 查看容器日志

如果需要保留容器内应用程序生成的日志，则可以使用挂载的Volume将容器内应用程序生成的日志保存到宿主机，还可以通过一些工具如Fluentd、Elasticsearch等对日志进行采集。

### 11.3 查看Kubernetes服务日志

那么systemd的journal系统会接管服务程序的输出日志。在这种环境中，可以通过使用systemd status或journalctl工具来查看系统服务的日志。

kube-proxy经常被我们忽视，因为即使它意外停止，Pod的状态也是正常的，但会导致某些服务访问异常。这些错误通常与每个节点上的kube-proxy服务有着密切的关系。遇到这些问题时，首先要排查kube-proxy服务的日志，同时排查防火墙服务，要特别留意在防火墙中是否有人为添加的可疑规则。

### 11.4 常见问题

1 由于无法下载pause镜像导致Pod一直处于Pending状态

image pull failed fork8s.gcr.io/pause:3.1，说明系统在创建Pod时无法从gcr.io下载pause镜像，所以导致创建Pod失败。

则可以先通过一台能够访问gcr.io的机器下载pause镜像，将pause镜像导出后，再导入内网的Docker私有镜像库

Kubernetes集群中应尽量使用服务名访问正在运行的微服务，但有时会访问失败。由于服务涉及服务名的DNS域名解析、kube-proxy组件的负载分发、后端Pod列表的状态等，所以可通过以下几方面排查问题。

查看Service的后端Endpoint是否正常可以通过kubectl get endpoints <service_name>命令查看某个服务的后端Endpoint列表，如果列表为空，则可能因为：

 Service的Label Selector与Pod的Label不匹配；◎ 后端Pod一直没有达到Ready状态（通过kubectl get pods进一步查看Pod的状态）；◎ Service的targetPort端口号与Pod的containerPort不一致等。

Kubernetes集群的DNS服务工作异常

kube-proxy服务设置为IPVS或iptables负载分发模式。
对于IPVS负载分发模式，可以通过ipvsadm工具查看Node上的IPVS规则，查看是否正确设置Service ClusterIP的相关规则。
对于iptables负载分发模式，可以通过查看Node上的iptables规则，查看是否正确设置Service ClusterIP的相关规则。

### 12.2 对GPU的支持

随着人工智能和机器学习的迅速发展，基于GPU的大数据运算越来越普及。在Kubernetes的发展规划中，GPU资源有着非常重要的地位。用户应该能够为其工作任务请求GPU资源，就像请求CPU或内存一样，而Kubernetes将负责调度容器到具有GPU资源的节点上。

Kubernetes从1.8版本开始，引入了Device Plugin（设备插件）模型，为设备提供商提供了一种基于插件的、无须修改kubelet核心代码的外部设备启用方式，设备提供商只需在计算节点上以DaemonSet方式启动一个设备插件容器供kubelet调用，即可使用外部设备。目前支持的设备类型包括GPU、高性能NIC卡、FPGA、InfiniBand等，

（1）在Kubernetes的1.8和1.9版本中，需要在每个工作节点上都为kubelet服务开启--feature-gates="DevicePlugins=true"特性开关。该特性开关从Kubernetes 1.10版本开始默认启用，无须手动设置。

（2）在每个工作节点上都安装NVIDIA GPU或AMD GPU驱动程序

◎ 配置Docker默认使用NVIDIA运行时；

NVIDIA设备驱动的部署YAML文件

### 12.3 Pod的垂直扩缩容

Kubernetes在1.9版本中加入了对Pod的垂直扩缩容（简称为VPA）支持，这一功能使用CRD的方式为Pod定义垂直扩缩容的规则，根据Pod的运行行为来判断Pod的资源需求，从而更好地为Pod提供调度支持。