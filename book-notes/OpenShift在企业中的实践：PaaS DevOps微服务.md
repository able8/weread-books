## OpenShift在企业中的实践：PaaS DevOps微服务
> 魏新宇 郭跃军

### 赞誉

云计算、微服务和DevOps技术体系复杂，企业自主搭建和掌握相关技术面临的挑战很大。OpenShift为企业提供了这样一个集成度高、易于使用、与业界主流技术发展保持同步的平台，将为各个企业的数字化转型提供坚实的保障。

OpenShift是红帽基于Kubernetes的企业级PaaS平台。

### 推荐序

同时红帽根据企业客户的需求，在Kubernetes之上增加了诸多企业级功能特性，打造了OpenShift这一企业级PaaS平台。

### 前言

❏《使用Istio实现基于Kubernetes的微服务架构》
❏《通过Ansible实现数据中心自动化管理》
❏《通过Kubernetes和容器实现DevOps》

### 1.2 企业数字化转型之PaaS

对于企业而言，虚拟化实现了操作系统和底层硬件的松耦合，但虚拟机承载的是操作系统，我们依然需要在操作系统中安装应用软件。而Docker可以在容器中直接运行应用（如Tomcat容器镜像），这比虚拟机更贴近于应用，更容易实现应用的快速申请和部署，极大地促进了容器云PaaS的迅速发展。到目前为止，绝大多数的企业级PaaS产品是以Docker、Kubernetes为核心的，红帽（Red Hat）的OpenShift3也是如此

OpenShift4更进一步引入了CRI-O，这样OpenShift可以承载更多容器运行时：runc（由Docker服务使用）、libpod（由Podman使用）或rkt（来自CoreOS）。

### 1.3 企业数字化转型之DevOps

敏捷开发→持续集成→持续交付→持续部署→DevOps

持续集成（Continuous Integration）：代码集成到主干之前，必须全部通过自动化测试；只要有一个测试用例失败，就不能集成。持续集成要实现的目标是：在保持高质量的基础上，让产品可以快速迭代。

DevOps是一组完整的实践，涵盖自动化软件开发和IT团队之间的流程，以便他们可以更快、更可靠地构建、测试和发布软件。

### 1.4 企业数字化转型之微服务

❏ 应用快速开发：微服务由小团队开发和维护，每个小团队最大规模为10人，合理的团队规模是5～7名成员，也就是“双披萨团队”（亚马逊在2012年提出这个概念，意思是说5～7人吃两个披萨刚好吃饱）。

❏ IT团队重组为DevOps团队：由微服务团队负责其微服务的整个生命周期管理，从开发到运营。DevOps团队可以按照自己的节奏管理组员和产品，控制自己的节奏。
❏ 将服务打包为容器：通过将应用打包成容器，可以形成标准交付物，大幅提升效率。
❏ 使用弹性基础架构：将微服务部署到PaaS上而非传统的虚拟机，例如Kubernetes集群。

### 1.5 PaaS、DevOps与微服务的关系

OpenShift以容器技术和Kubernetes为基础，在此之上扩展提供了软件定义网络、软件定义存储、权限管理、企业级镜像仓库、统一入口路由、持续集成流程（S2I/Jenkins）、统一管理控制台、监控日志等功能，形成覆盖整个软件生命周期的解决方案。
所以说，OpenShift本身提供开箱即用的PaaS功能，还可以帮助客户快速实现微服务和DevOps，并且提供对应的企业级服务支持。

### 1.6 企业数字化转型的实现

第二步：建设多数据中心。随着业务规模的扩张和重要性的提升，企业通常会建设灾备或者双活数据中心，这样可以保证当一个数据中心出现整体故障时，业务不会受到影响。
第三步：构建混合云。随着公有云的普及，很多企业级客户，尤其是制造行业的客户，开始将一些前端业务系统向公有云迁移，这样客户的IT基础架构最终成为混合云的模式。

### 第2章 基于OpenShift构建企业级PaaS平台

Kubernetes作为容器编排系统的通用标准，它的出现促进了PaaS的飞速发展和企业中PaaS的落地。OpenShift是红帽基于Kubernetes推出的企业级PaaS平台，它提供了开箱即用的PaaS功能。

### 2.1 OpenShift与Kubernetes的关系

OpenShift是全球领先的企业级Kubernetes容器平台。目前OpenShift拥有大量客户（全球超过1000个），跨越行业垂直市场，并大量部署在私有云和公有云上。

2014年Kubernetes诞生以后，红帽决定对OpenShift进行重构，正是这一决定，彻底改变了OpenShift的命运以及后续PaaS市场的格局。
2015年6月，红帽推出了基于Kubernetes1.0的OpenShift3.0，它构建在以下三大强有力的支柱上：

❏ Linux：红帽RHEL作为OpenShift3基础，保证了其基础架构的稳定性和可靠性。

❏ Kubernetes：提供强大的容器编排和管理功能，并成为过去十年中发展最快的开源项目之一。

Kubernetes是一个开源项目，面向容器调度；OpenShift是企业级软件，面向企业PaaS平台。OpenShift除了包含Kubernetes，还包含很多其他企业级组件，如认证集成、日志监控等。

为解决这个问题，红帽在许多关键领域投入了大量资源。红帽推动了Kubernetes Namespaces和资源限制Qouta的开发，以便多个租户可以共享一个Kubernetes集群，并可以做资源限制。红帽推动了基于Kubernetes角色的访问控制（RBAC）的开发，以便可以为用户分配具有不同权限级别的角色。2

Knative是一种支持Kubernetes的Serverless架构。红帽是Knative开源项目的贡献者，红帽希望基于OpenShift实现开放的混合无服务器功能（FaaS）。

### 2.2 OpenShift的架构介绍与规划

❏ Etcd是一个分布式键值存储，Kubernetes使用它来存储有关Kubernetes集群元数据和其他资源的配置和状态信息。
❏ 自定义资源定义（CRD）是Kubernetes提供的用于扩展资源类型的接口，自定义对象同样存储在Etcd中并由Kubernetes管理。
❏ 容器化服务（Containerized Service）实现了PaaS功能组件的以容器方式于OpenShift上运行。

Nodeport是Servcie的一种类型，本质上是通过在集群的每个节点上暴露一个端口，然后将这个端口映射到Service的端口来实现的。

OpenShift利用Kubernetes Persistent Volume（PV）概念来管理存储。管理员可以快速划分卷提供给容器使用。开发人员通过命令行和界面申请使用存储，而不必关心后端存储的具体类型和工作机制。
Persistent Volume（PV）是一个开放的存储管理框架，提供对各种类型存储的支持。OpenShift默认支持NFS、GlusterFS、Cinder、Ceph、EBS、iSCSI和Fibre Channel等存储，用户还可以根据需求对PV框架进行扩展，从而使其支持更多类型的存储。

Persistent Volume Claim（PVC）是用户的一个Volume请求。用户通过创建PVC消费PV的资源。
PV只有被PVC绑定后才能被Pod挂载使用，PV和PVC的生命周期如图2-25所示。

3）接入层高可用
接入层主要是Apiserver服务。由于Apiserver本身是无状态服务，可以实现多活。通常采用在Apiserver前端加负载均衡实现，负载均衡软件由用户任意选择，可以选择硬件的，也可以选择软件的，至于负载均衡的高可用实现同样有很多选择，软件通常使用Keepalived，以Haproxy为例实现如图2-32所示。

通过负载均衡Haproxy负载均衡到多个Master节点，再通过Keepalived提供VIP的管理，实现Haproxy的高可用。当主Haproxy出现故障后，VIP会自动迁移到备用Haproxy，具体的技术细节，感兴趣的读者请自行查阅网络资料学习。
可以看到通过对三个层面高可用的实现保证控制节点任何一个宕机都不会影响整个集群的可用性。当然，如果故障节点过半，集群就会进入只读模式。

### 3.2 OpenShift在企业中的开发实践

❏ 以适当的顺序放置指令：Docker从上到下读取Dockerfile，每个指令成功执行之后才会创建新的层执行下一个指令，这样尽量将一些不变的指令放置到顶层，可以使用缓存加快构建速度。

RUN会在当前镜像上创建一个新的镜像层执行命令。RUN命令会增加镜像的层数，所以在Dockerfile中执行RUN的时候，使用&&命令分隔符在单个RUN指令中执行多个命令，以控制镜像的大小。

### 4.1 OpenShift在公有云和私有云上的区别

通常会优先考虑使用公有云厂商的负载均衡器，

### 4.4 OpenShift在AWS上的实践

❏ ELB（Elastic Load Balancing）：弹性负载均衡服务

### 4.5 OpenShift在阿里云上的实践

在阿里云上选择一个区域创建测试环境的VPC网络，分别在3个可用区创建3个交换机，对VPC划分子网。3个Master节点分别运行在3个可用区中，并与Etcd共用节点

### 第5章 在OpenShift上实现DevOps

敏捷开发→持续集成（CI）→持续交付（CD）→持续部署→DevOps。

### 5.2 DevOps的实现路径

（3）应用运维
主要负责应用层面的维护，如中间件，数据库等。
主要职责：参与产品版本发布评审，从运维交付提出发布意见；应用日常监控运维；应用线上问题处理。
（4）平台运维
主要负责基础设施的维护或者应用平台的维护，如PaaS平台。
主要职责：负责配置产品线所需要的基础设施；负责监控维护基础设施。

业务系统上线需要经历开发、测试、预发布、发布这几个阶段，每个阶段分别对应一套环境，这就意味着一个业务线有多套环境。

❏ 项目管理：禅道、JIRA、Trello等。
❏ 知识管理：MediaWiki、Confluence等。
❏ 源代码版本控制（SCM）:GitHub、GitLab、BitBucket、SubVersion、Gogs等。
❏ 代码复审：GitLab、Gerrit、Fisheye等。

❏ 配置管理：Ansible、Chef、Puppet、SaltStack等。
❏ 监控告警：Zabbix、Prometheus、Grafana、Sensu等。
❏ 二进制库：Artifactory、Nexus等。

### 5.5 Ansible实现混合云中的DevOps

❏ 不需要安装agent，分为管理节点和远程被管节点通过SSH认证。
❏ 纳管范围广泛，不仅仅是操作系统，还包括各种虚拟化、公有云、DevOps工具，甚至网络设备。

Ansible的核心组件包括：Modules、Inventory、Playbook、Roles和Plugins。

如果说Modules是我们使用Ansible进行自动化任务时调用的模块。那么Playbook就是Ansible自动化任务的脚本（YAML格式）。

### 7.2 微服务介绍

而在Service Mesh微服务治理框架下，应用开发人员只需要关注业务代码本身，而不需要关注微服务之间的调用。

Service Mesh中主要分为控制平面和数据平面，控制平面主要负责管理和配置路由策略，数据平面主要负责处理入站和出站数据包，并完成认证鉴权等，这样所有微服务治理的需求由微服务框架实现，对应用来说完全透明。

Istio提供的功能包括：流量控制，基于策略实现路由转发，服务间调用认证、鉴权，故障注入，断路器，分布式跟踪、遥测，网格可视化。

### 7.4 Spring Cloud在OpenShift上的落地

我们建议使用ConfigMap，它可以用于存储配置文件和JSON数据。然后我们把ConfigMap挂载到Pod中或者作为环境变量传入，这样应用就可以加载相关的配置参数。

微服务中的API网关，提供了一个或多个HTTP API的自定义视图的分布式机制。一个API网关是为特定的服务和客户端定制的，不同的应用程序通常使用不同的API网关。
API网关的使用场景包括：
❏ 聚合来自多个微服务的数据，以呈现基于Web浏览器的应用程序的统一视图。
❏ 桥接不同的消息传输协议，例如HTTP和AMQP。
❏ 实现同一个应用不同版本API的灰度发布。
❏ 使用不同的安全机制验证客户端。

Spring Cloud默认使用Zuul提供微服务API网关能力，将API网关作为所有客户端的入口。在OpenShift上运行Spring Cloud，可以使用Zuul。但我们更推荐使用Camel作为微服务的网关。Apache Camel是一个基于规则路由和中介引擎、提供企业集成模式（EIP）的Java对象（POJO）的实现，通过API或DSL（Domain Specific Language）来配置路由和中介的规则。

EFK（Elasticsearch+Flunetd+Kibana）或ELK（Elasticsearch+Logstash +Kibana）。红帽OpenShift的日志管理使用的是EFK, Fluentd是实时的日志收集、处理引擎，它汇聚数据到ElasticSearch; ElasticSearch会处理收集到的大量数据，用于全文搜索、结构化搜索以及分析；Kibana是日志前端展示工具。

分布式跟踪为每个请求分配唯一ID。此ID包含在所有相关服务调用中，并且这些服务在进行进一步的服务调用时也包含相同的ID。这样，就可以跟踪从始发请求到所有相关请求的调用链。

❏ 注册发现由OpenShift Etcd、Service和内部DNS实现。
❏ 配置中心由OpenShift ConfigMap实现。
❏ 微服务网关由Camel实现。
❏ 入口流量由OpenShift Router实现。
❏ 微服务之间的项目隔离通过OpenShift Project实现。
❏ 日志监控由OpenShift集成工具实现。
❏ 熔断由Hystrix实现。

### 8.1 Istio的技术架构

Istio数据平面由一组代理（Envoy）组成。这些代理以Sidecar的方式与每个应用程序协同运行，负责调解和控制微服务之间的所有网络通信，并且与控制平面的Mixer通信，接受调度策略。正是有了Envoy代理，才使Istio不必像Spring Cloud框架那样需要将微服务治理架构以annotation的方式写到应用源码中（如Spring Boot使用Hystrix），从而做到零代码侵入。如果用一种形象的方式比喻，Sidecard就像挂斗摩托车的挂斗

Envoy用于管理Istio中所有服务的入站和出站流量。Istio利用Envoy的许多内置功能，例如：
❏ 动态服务发现
❏ 负载均衡
❏ TLS终止
❏ HTTP / 2和gRPC代理
❏ 断路器
❏ 健康检查
❏ 流量分割
❏ 故障注入
❏ 监控指标

### 第9章 基于OpenShift和Istio实现微服务落地

我们将介绍如何将应用迁移到基于OpenShift的Istio中，并通过Istio对其进行高级管理。

### 9.1 Istio的基本功能

Istio最重要的是路由管理功能，而路由管理依赖于四个重要的资源对象：VirtualService、DestinationRule、Gateway和ServiceEntry。

限流、熔断、重试、超时等是微服务治理中很重要的一组概念。通过这些手段，我们保证微服务的整体可用性，微服务的限流和熔断通常是配套使用的。通过限流设置，设定在某一时间窗口内对某一个或者多个微服务的请求数进行限制，保持系统的可用性和稳定性，防止因流量暴增而导致的系统运行缓慢或宕机。通过熔断设置，当一个微服务出现故障或者由于流量冲击造成微服务无法响应时，打开断路器，使微服务对外停止服务。
❏ 熔断时：本微服务对外停止服务。
❏ 限流时：超过阈值的请求将不会被响应，但本微服务本身不对外停止服务。

### 9.3 企业应用向Istio迁移

1）微服务应用设计/拆分：有的客户是将现有单体应用迁移到Istio中，需要做微服务拆分；有的客户是将新的应用部署到Istio中，则需要进行微服务设计。关于微服务的设计/拆分原则，我们在第7章中已经做过介绍。

2）微服务应用构建：对微服务的应用源码进行编译打包。

3）微服务容器化：用编译打包好的微服务生成Docker Image。
4）容器环境部署：将Docker Image部署到容器环境中，针对OpenShift，我们需要配置应用的DeploymentConfig、Service等。

5）微服务Sidecar注入/部署：在OpenShift中部署微服务，部署的时候注入Sidecar。
6）Istio纳管微服务：通过Istio的策略对微服务进行路由管理，并进行监控。

❏ 微服务应用构建：使用maven应用本地编译，完成应用打包。
❏ 微服务容器化：使用Dockerfile的方式，构建包含应用的Docker Image。
❏ 容器环境部署：书写微服务在OpenShift上部署所需要的DeploymentConfig

❏ Istio纳管微服务：通过Istio管理三层微服务，并配置高级的路由策略，实现微服务可视化。

### 9.4 Istio纳管微服务

在微服务中，很重要的一部分就是路由管理，主要指通过策略配置实现对微服务之间访问的管理，主要的场景有：
❏ 灰度/蓝绿发布：在发布新版本应用时，通过路由管理实现先发布一部分用于测试，没有问题后再逐步迁移流量。
❏ 外部访问控制：管理外部访问Istio微服务的路由，并提供一些安全、权限上的控制。
❏ 访问外部服务控制：控制所有出站流量。
❏ 服务推广：通过逐步提升应用的稳定性和功能，替换线上应用。

在微服务的测试中，有时候需要进行混沌测试，也就是模拟各种微服务的故障。在混沌测试方面，Istio可以实现错误注入，目前支持的错误有延迟和退出，下面我们分别说明。
1．退出错误的实现

### 9.5 Istio生产使用建议

Envoy作为Istio的数据平面，负责数据流的处理。

❏ Sidecar在每秒处理1000个请求的情况下，使用0.6个vCPU以及50MB的内存。
❏ istio-telemetry在每秒1000个微服务间请求的情况下，消耗了0.6个vCPU。
❏ Pilot使用了1个vCPU以及1.5 GB的内存。
❏ Envoy在第90百分位上增加了8毫秒的延迟。

### 10.1 微服务的API管理

高德地图和首汽约车之间应用的调用使用的就是API调用的方式。两个公司之间API的调用可能产生一些对计费、限流、流量管理的需求。

所以说，API经济的本质是企业通过将自己企业内部应用的API暴露出去，被其他公司或者开发者调用，然后根据一定方式进行计费，从而企业实现创收的一种经济模式。

从技术角度而言，需要对企业的API进行有效管理，并对外暴露，供其他企业或者用户使用。从技术上应考虑以下几点（包括但不限于）：
❏ API灵活的访问控制。
❏ API的身份认证与授权。
❏ API合同和费率限制。
❏ API访问分析和报告。
❏ API的计费。

API网关是一个软件系统的唯一入口，它封装了软件系统内部体系结构，对外为客户端提供API。客户端不必关注软件系统的内部结构。而API管理是对API的安全、授权、限速、计费进行丰富的高级策略管理的企业级解决方案。