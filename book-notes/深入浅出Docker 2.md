## 深入浅出Docker
> 奈吉尔·波尔顿

### Docker认证工程师（Docker Certified Associate）

Docker认证工程师（Docker Certified Associate, DCA

### 为什么要阅读本书，为什么要关注Docker

如今Docker无处不在，这是不争的事实。开发人员都很喜欢它，运维工程师也需要它。他们都需要深入了解如何在关键业务环境中构建和维护符合生产级别要求的容器化应用

### 内容组织

Docker（Moby）项目

### 1.3 虚拟机的不足

虚拟机技术也面临着一些其他挑战。比如虚拟机启动通常比较慢，并且可移植性比较差——虚拟机在不同的虚拟机管理器（Hypervisor）或者云平台之间的迁移要远比想象中困难。

### 1.4 你好，容器！

同时容器还具有启动快和便于迁移等优势。将容器从笔记本电脑迁移到云上，之后再迁移到数据中心的虚拟机或者物理机之上，都是很简单的事情。

### 1.9 Mac容器现状

在Mac系统上使用Docker for Mac来运行Linux容器。这是通过在Mac上启动一个轻量级LinuxVM，然后在其中无缝地运行Linux容器来实现的。这种方式在开发人员中很流行，因为这样可以在Mac上很容易地开发和测试Linux容器。

### 2.1 Docker——简介

Docker是一种运行于Linux和Windows上的软件，用于创建、管理和编排容器。Docker是在GitHub上开发的Moby开源项目的一部分。Docker公司，位于旧金山，是整个Moby开源项目的维护者。Docker公司还提供包含支持服务的商业版本的Docker。

### 2.2 Docker公司

有意思的是，Docker公司起初是一家名为dotCloud的平台即服务（Platform-as-a-Service, PaaS）提供商。底层技术上，dotCloud平台利用了Linux容器技术。为了方便创建和管理这些容器，dotCloud开发了一套内部工具，之后被命名为“Docker”。Docker就是这样诞生的！2013年，dotCloud的PaaS业务并不景气，公司需要寻求新的突破。于是他们聘请了Ben Golub作为新的CEO，将公司重命名为“Docker”，放弃dotCloud PaaS平台，怀揣着“将Docker和容器技术推向全世界”的使命，开启了一

### 2.3 Docker运行时与编排引擎

Docker引擎是用于运行和编排容器的基础设施工具。

### 2.4 Docker开源项目（Moby）

Moby项目的目标是基于开源的方式，发展成为Docker上游，并将Docker拆分为更多的模块化组件。Moby项目托管于GitHub的Moby代码库，包括子项目和工具列表。核心的Docker引擎项目位于GitHub的moby/moby，但是引擎中的代码正持续被拆分和模块化。

### 2.7 本章小结

Docker项目是开源的，其上游源码位于GitHub的moby/moby库。

### 5.2 Docker引擎——详解

在对Docker daemon的功能进行拆解后，所有的容器执行逻辑被重构到一个新的名为containerd（发音为container-dee）的工具中。它的主要任务是容器的生命周期管理——start | stop | pause | rm..

API是在daemon中实现的。这套功能丰富、基于版本的REST API已经成为Docker的标志，并且被行业接受成为事实上的容器API。一旦daemon接收到创建新容器的命令，它就会向containerd发出调用。daemon已经不再包含任何创建容器的代码了！daemon使用一种CRUD风格的API，通过gRPC与containerd进行通信。虽然名叫containerd，但是它并不负责创建容器，而是指挥runc去做。containerd将Docker镜像转换为OCIbundle，并让runc基于此创建一个新的容器。然后，runc与操作系统内核接口进行通信，基于所有必要的工具（Namespace、CGroup等）来创建容器。容器进程作为runc的子进程启动，启动完毕后，runc将会退出。

daemon的主要功能包括镜像管理、镜像构建、REST API、身份验证、安全、核心网络以及编排。

### 10.1 Docker Swarm——简介

Docker Swarm包含两方面：一个企业级的Docker安全集群，以及一个微服务应用编排引擎。

### 10.2 Docker Swarm——详解

节点会被配置为管理节点（Manager）或工作节点（Worker）。管理节点负责集群控制面（Control Plane），进行诸如监控集群状态、分发任务至工作节点等操作。工作节点接收来自管理节点的任务并执行。Swarm的配置和状态信息保存在一套位于所有管理节点上的分布式etcd数据库中。该数据库运行于内存中，并保持数据的最新状态。关于该数据库最棒的是，它几乎不需要任何配置——作为Swarm的一部分被安装，无须管理。

Swarm中的最小调度单元是服务。

执行docker service ps命令可以查看服务副本列表及各副本的状态。

2．副本服务vs全局服务
服务的默认复制模式（Replication Mode）是副本模式（replicated）。这种模式会部署期望数量的服务副本，并尽可能均匀地将各个副本分布在整个集群中。
另一种模式是全局模式（global），在这种模式下，每个节点上仅运行一个副本。

多亏了Docker服务，对一个设计良好的应用来说，实施滚动升级已经变得简单多了

覆盖网络是一个二层网络，容器可以接入该网络，并且所有接入的容器均可互相通信。即使这些容器所在的Docker主机位于不同的底层网络上，该覆盖网络依然是相通的。本质上说，覆盖网络是创建于底层异构网络之上的一个新的二层容器网络。

通过对服务声明-p 80:80参数，会建立Swarm集群范围的网络流量映射，到达Swarm任何节点80端口的流量，都会映射到任何服务副本的内部80端口。

默认的模式，是在Swarm中的所有节点开放端口——即使节点上没有服务的副本——称为入站模式（Ingress Mode）。此外还有主机模式（Host Mode），即仅在运行有容器副本的节点上开放端口。以主机模式开放服务端口，需要较长格式的声明语法，代码如下。

有些请求会被旧版本的副本处理，而有些请求会被新版本的副本处理。一段时间之后，所有的请求都会被新版本的服务副本处理。

Docker节点默认的配置是，服务使用json-file日志驱动，其他的驱动还有journald（仅用于运行有systemd的Linux主机）、syslog、splunk和gelf。
json-file和journald是较容易配置的，二者都可用于docker service logs命令。命令格式为docker service logs <service-name>。
若使用第三方日志驱动，那么就需要用相应日志平台的原生工具来查看日志。

通过在执行docker service create命令时传入--logdriver和--log-opts参数可以强制某服务使用一个不同的日志驱动，这会覆盖daemon.json中的配置。

### 11.2 Docker网络——详解

在Linux主机上，Docker网络由Bridge驱动创建，而Bridge底层是基于Linux内核中久经考验达15年之久的LinuxBridge技术。这意味着Bridge是高性能并且非常稳定的！同时这还表示可以通过标准的Linux工具来查看这些网络，代码如下。

能够将容器化应用连接到外部系统以及物理网络的能力是非常必要的

Docker内置的Macvlan驱动（Windows上是Transparent）就是为此场景而生。通过为容器提供MAC和IP地址，让容器在物理网络上成为“一等公民”。

Macvlan的优点是性能优异，因为无须端口映射或者额外桥接，可以直接通过主机接口（或者子接口）访问容器接口。但是，Macvlan的缺点是需要将主机网卡（NIC）设置为混杂模式（Promiscuous Mode），这在大部分公有云平台上是不允许的。所以Macvlan对于公司内部的数据中心网络来说很棒（假设公司网络组能接受NIC设置为混杂模式），但是Macvlan在公有云上并不可行。

Swarm支持两种服务发布模式，两种模式均保证服务从集群外可访问。•Ingress模式（默认）。•Host模式。通过Ingress模式发布的服务，可以保证从Swarm集群内任一节点（即使没有运行服务的副本）都能访问该服务；以Host模式发布的服务只能通过运行服务副本的节点来访问。图11.20展示了两种模式的区别。Ingress模式是默认方式，这意味着任何时候读者通过-p或者--publish发布服务的时候，默认都是Ingress模式；如果需要以Host模式发布服务，则读者需要使用--publish参数的完整格式，并添加mode=host。下

### 12.3 Docker覆盖网络——命令

•docker network ls用于列出Docker主机上全部可见的容器网络。Swarm模式下的Docker主机只能看到已经接入运行中的容器的网络。这种方式保证了网络Gossip开销最小化。

### 第14章 使用Docker Stack部署应用

大规模场景下的多服务部署和管理是一件很难的事情。
幸运的是，Docker Stack为解决该问题而生！Docker Stack通过提供期望状态、滚动升级、简单易用、扩缩容、健康检查等特性简化了应用的管理！这些功能都封装在一个完美的声明式模型当中。太赞了！

### 14.1 使用Docker Stack部署应用——简介

在真实的生产环境进行多服务的应用部署和管理，这才是专业选手的水平！

Stack能够在单个声明文件中定义复杂的多服务应用。Stack还提供了简单的方式来部署应用并管理其完整的生命周期：初始化部署 > 健康检查 > 扩容 > 更新 > 回滚，以及其他功能！步骤很简单。在Compose文件中定义应用，然后通过docker stack deploy命令完成部署和管理。就是这样！Compose文件中包含了构成应用所需的完整服务栈。此外还包括了卷、网络、安全以及应用所需的其他基础架构。然后基于该文件使用docker stack deploy命令来部署应用，这很简单。Stack是基于Docker Swarm之上来完成应用的部署。因此诸如安全等高级特性，其实都是来自Swarm。

简而言之，Docker适用于开发和测试。Docker Stack则适用于大规模场景和生产环境。


### 14.2 使用Docker Stack部署应用——详解

从体系结构上来讲，Stack位于Docker应用层级的最顶端。Stack基于服务进行构建，而服务又基于容器，

version代表了Compose文件格式的版本号。为了应用于Stack，需要3.0或者更高的版本。services中定义了组成当前应用的服务都有哪些。networks列出了必需的网络，secrets定义了应用用到的密钥。

Stack文件就是Docker Compose文件。唯一的要求就是version：一项需要是“3.0”或者更高的值。具体可以关注Docker文档中关于Compose文件的最新版本信息。

该文件中定义了3个网络：front-tier、back-tier以及payment。默认情况下，这些网络都会采用overlay驱动，新建对应的覆盖类型的网络。但是payment网络比较特殊，需要数据层加密。

注意，4个密钥都被定义为external。这意味着在Stack部署之前，这些密钥必须存在。

ports关键字定义了两个映射。
•80:80将Swarm节点的80端口映射到每个服务副本的80端口。
•443:443将Swarm节点的443端口映射到每个服务副本的443端口。
默认情况下，所有端口映射都采用Ingress模式。这意味着Swarm集群中每个节点的对应端口都会映射并且是可访问的，即使是那些没有运行副本的节点。另一种方式是Host模式，端口只映射到了运行副本的Swarm节点上。但是，Host模式需要使用完整格式的配置。例如，在Host模式下将端口映射到80端口的语法如下所示。

推荐使用完整语法格式，这样可以提高易读性，并且更灵活（完整语法格式支持Ingress模式和Host模式）。但是，完整格式要求Compose文件格式的版本至少是3.2。

ices.appserver.deploy.update_config定义了Docker在服务滚动升级的时候具体如何操作。对于当前服务，Docker每次会更新两个副本（parallelism），并且在升级失败后自动回滚。回滚会基于之前的服务定义启动新的副本。failure_action的默认操作是pause，会在服务升级失败后阻止其他副本的升级。failure_action还支持continue。

在本例中，会挂载Docker主机的/var/run/docker.sock目录到每个服务副本的/var/run/docker.sock路径。这意味着在服务副本中任何对/var/run/docker.sock的读写操作都会实际指向Docker主机的对应目录中。

/var/run/docker.sock恰巧是Docker提供的IPC套接字，Docker daemon通过该套接字对其他进程暴露其API终端。这意味着如果给某个容器访问该文件的权限，就是允许该容器接收全部的API终端，即等价于给予了容器查询和管理Docker daemon的能力。

4）列出所有密钥。
$ docker secret ls￼

在服务启动失败时，docker stack ps命令是首选的问题定位方式。该命令展示了Stack中每个服务的概况，包括服务副本所在节点、当前状态、期望状态以及异常信息。

### 14.3 使用Docker Stack部署应用——命令

•docker stsack deploy用于根据Stack文件（通常是docker-stack.yml）部署和更新Stack服务的命令。
•docker stack ls会列出Swarm集群中的全部Stack，包括每个Stack拥有多少服务。
•docker stack ps列出某个已经部署的Stack相关详情。该命令支持Stack名称作为其主要参数，列举了服务副本在节点的分布情况，以及期望状态和当前状态。
•docker stack rm命令用于从Swarm集群中移除Stack。移除操作执行前并不会进行二次确认。

### 14.4 本章小结

Stack是Docker原生的部署和管理多服务应用的解决方案。Stack默认集成在Docker引擎中，并且提供了简单的声明式接口对应用进行部署和全生命周期管理。

### 附录A 安全客户端与daemon的通信

Docker使用了客户端—服务端模型。客户端使用CLI，同时服务端（daemon）实现功能，并对外提供REST API。


默认使用2375作为客户端和服务端之间未加密通信方式的端口，而2376则用于加密通信。

Docker允许用户配置客户端和daemon间只接收安全的TLS方式连接。生产环境中推荐这种配置，即使在可信内部网络中，也建议如此配置！

•daemon模式：Docker daemon只接收认证客户端的链接。
•客户端模式：Docker客户端只接收拥有证书的Docker daemon发起的链接，其中证书需要由可信CA签发。

### A.1 实验环境准备

环境中包括3个Linux节点，分别为CA、Docker客户端以及Dockerdaemon。很关键的一点是，3个主机之间可以互相通过名称解析。

（1）配置CA和证书。•创建CA（自签名）。•创建并为daemon签发密钥。•创建并为客户端签发密钥。•分发密钥。（2）配置Docker使用TLS。•配置daemon模式。•配置客户端模式。

ca-key.pem的新文件，这就是CA私钥。

（1）创建私钥。（2）创建签名请求。（3）添加IP地址，并设置为服务端认证有效。（4）生成证书。在CA节点（node2）运行全部命令。

创建证书签名请求（CSR）并发送到CA，这样就可以完成daemon证书的创建和签名。要确保使用正确的DNS名称来指代想要运行Docker安全daemon的节点。示例中使用了node3。

其中包含了CA签发证书时需要加入到daemon证书的扩展属性。这些属性包括daemon的DNS名称和IP地址，同时配置证书使用服务端认证。

node1和node3节点只会信任由其CA公钥签名的CA以及证书。配置了正确的证书后，就可以开始配置Docker的客户端和daemon使用TLS了。

### A.2 配置Docker使用TLS

daemon模式保证daemon只处理来自拥有有效证书的客户端发起的连接，客户端模式使得客户端只能连接到拥有有效证书的daemon

配置下列环境变量，使客户端可以通过网络连接到远端daemon。
export DOCKER_HOST=tcp://node3:2376

该目录位于用户home目录下，名为.docker。同时密钥也修改为Docker期望的名称（ca.pem、cert.pem，以及key.pem）。可以通过配置环境变量DOCKER_CERT_PATH来指定其他的目录。

### A.3 Docker TLS回顾

daemon 模式会拒绝那些没有有效签名的客户端命令，客户端模式下客户端不会连接没有有效证书的远端daemon。