## 基于Kubernetes的容器云平台实战
> 陆平等

### 前言

人们逐渐认识到IaaS技术所带来的显著优势，如资源按需灵活定制、低成本、弹性伸缩、统一管控等，但这仅解决了应用系统对IT资源需求的按需交付问题，应用系统本身的DevOps、CI/CD、编排、自动化部署、配置管理、服务发现与路由、弹性伸缩、自动化监控与日志采集等工作只能由应用自身完成或依赖于第三方工具，这将大大增加应用系统研发、运维和集成难度。

最终一个以“Docker+Kubernetes”为核心的容器云平台让人们看到了希望，它可以满足大多数应用对PaaS平台的期望。

### 5.1 MySQL Docker部署实践

■ 提高资源利用率。通常数据库服务器只启动一个实例，资源利用率不高，借助Docker容器资源隔离能力可在同一宿主上部署多个MySQL实例，并且将这些实例间资源隔离开，极大提高数据库实例部署密度，提高了资源利用率。
■ 平滑扩容。当用户访问量大、对数据库的性能要求超出单个实例的极限时，能够借助Docker容器资源平滑升级能力，方便数据库实例平滑扩容，满足数据库方案的性能需求。
■ 通过对部署MySQL的容器暴露API服务，可以有效进行数据库的生命周期管理，用户能自主实现数据库上线、下线、资源调整等自动化运维工作。

说明MySQL1同MySQL2两个实例是隔离开的。

### 9.4 Headless服务

创建一个Headless类型的Service，将spec.clusterIP字段设置为“None”。对于这样的Service，系统不会为它们分配对应的IP，也不会在Pod中为其创建对应的全局变量。DNS则会为Service的name添加一系列的A记录（IP地址指向），直接指向后端映射的Pod。

### 9.5 Kubernetes服务

Service（服务）是一个虚拟概念，逻辑上代理后端Pod。众所周知，Pod生命周期短，状态不稳定，Pod异常后新生成的Pod IP会发生变化，之前Pod的访问方式均不可达。通过Service对Pod做代理，因为Service有固定的IP和Port, IP:Port组合自动关联后端Pod，所以，即使Pod发生改变，Kubernetes内部会更新这组关联关系，使得Service能够匹配到新的Pod

集群中每个Node节点都有一个组件kube-proxy，它实际上是为Service服务的。通过kube-proxy，实现流量从Service到Pod的转发，kube-proxy也可以实现简单的负载均衡功能。

GKE默认的Ingress控制器将启动一个HTTP（S）的负载均衡器，这将使用户可以基于访问路径和子域名将流量路由到后端服务。

如果希望在同一个IP地址下暴露多个服务，并且它们都使用相同的L7协议（通常是HTTP），则Ingress方式最有用。如果使用本地GCP集成，则只需支付一台负载均衡器费用，并且是“智能”性Ingress，可以获得许多开箱即用的功能（如SSL、Auth、路由等）。

### 9.7 完整的Kubernetes服务发布实践

1）ClusterIP：是Kubernetes默认的服务发布方式，由于ClusterIP属于Kubernetes内部的虚拟网络IP地址，外部无法寻址，因此主要适用于Kubernetes集群内部的服务调用。

2）NodePort:NodePort是Kubernetes提供给集群外部客户访问服务的一种方式，用于弥补ClusterIP只供Kubernetes集群内部调用的不足。

LoadBalancer服务建立在NodePort服务的基础上，而且需要通过第三方平台创建负载均衡实例，并将每个Node节点加为该负载均衡的成员，这样负载均衡实例接收到请求后，会转发请求到各Node节点。

Kubernetes开始引入Federation提供跨集群的高可用，通过将不同AZ、不同Region的Kubernetes集群组成同一个联邦，我们可以实现使Kubernetes满足生产系统的要求。

### 10.2 跨主机Docker网络通信

■ Host模式：容器直接使用宿主机的网络，这样天生就可以支持跨主机通信。这种方式虽然可以解决跨主机通信问题，但应用场景很有限，容易出现端口冲突，也无法做到隔离网络环境，一个容器崩溃很可能引起整个宿主机的崩溃。

Flannel是由CoreOS维护的一个虚拟网络方案，由Golang语言编写，是Kubernetes默认的网络。Flannel之所以可以搭建Kubernetes依赖的底层网络，是因为：
1）它为每个Node上的Docker容器分配互不冲突的IP地址。
2）它能为这些IP地址之间建立一个叠加网络，通过叠加网络将数据包原封不动地传递到目标容器内。

由于Calico是一种纯三层的实现，因此可以避免与二层方案相关的数据包封装的操作，中间没有任何的NAT，没有任何的Overlay，所以它的转发效率可能是所有方案中最高的。因为它的包直接走原生TCP/IP的协议栈，它的隔离也因为这个栈而变得好做。因为TCP/IP的协议栈提供了一整套的防火墙规则，所以它可以通过iptables的规则达到比较复杂的隔离逻辑。

■ BGP Client（BIRD），主要负责把Felix写入Kernel的路由信息分发到当前Calico网络，确保workload间通信的有效性。

此外，Calico基于iptables还提供了丰富而灵活的网络策略，保证通过各个节点上的ACL来提供Workload的多租户隔离、安全组以及其他可达性限制等功能，如图10-10所示。

■ Calico目前只支持TCP、UDP、ICMP、ICMPv6协议，如果使用其他四层协议（如NetBIOS协议），建议使用Weave、原生叠加等其他叠加网络实现。
■ 基于三层实现通信，在二层上没有任何加密包装，因此只能在私有的可靠网络上使用。
■ 流量隔离基于iptables实现，并且从etcd中获取需要生成的隔离规则，因此会有一些性能上的隐患。

### 第16章 金融容器云平台总体设计方案

随着互联网金融行业的发展，大批创新业务涌现，为了赢得市场先机往往需要应用快速上线，需求快速迭代研发，版本快速持续部署，上线周期从原来的几个月变成现在需要1～2周甚至几天；

金融行业IT设备多，应用数量也很多，虽然建立了运维体系，部署了一些自动化工具，但是运维中还有大量的手工操作，工作量很大，运维投入也很大。Docker的出现使得构造一个对开发和运维人员更加开放的容器平台成为可能。Docker是一个开源的应用容器引擎，本质是操作系统层的虚拟化，开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的Linux机器上。

### 16.2 容器及编排技术选型

1）Docker EE版不开源，无法做到技术的自主掌控。
2）Docker EE版包括底层的Containered和完整的Datacenter, Datacenter与很多企业要建设的云平台功能重复。而企业要支付整体费用，随着项目的推广，License费用问题将会变得突出。
3）Docker EE版捆绑Swarm等模块，目前技术上还不成熟，且开源社区有更强大的工具支持。
4）Docker EE版的更新频率较慢，依赖阿里云技术支持，出现问题无法得到及时的修复。

### 17.1 用Docker实现DevOps的优势

3）易于构建、迁移和部署。Dockerfile实现镜像构建的标准化和可复用性，镜像本身的分层机制也提高了镜像构建及传输的效率。使用Registry可以将构建好的镜像迁移到任意环境，而且环境的部署仅需要将静态只读的镜像转换为动态可运行的容器即可。

### 18.5 进程间通信

微服务架构是一种分布式架构，微服务部署在不同的节点上，微服务间的通信采用进程间通信（IPC）方式。微服务之间的通信可以采用同步方式，即请求/响应模式，如HTTP的Rest或Thrift，也可以采用异步方式，通常是基于消息队列方式。

消息队列是微服务应用间进行异步通信的一种重要方式，消息可以存储在内存或者磁盘上，消息队列的优点为客户端与服务器不需要直接通信，不需要知道各自的位置，可以解决解耦、异步、流控等问题。常用的消息队列有ZeroMQ、RabbitMQ、RocketMQ、Kafka等。其

### 18.7 微服务框架

进行微服务开发时，通常采用微服务开发框架固定开发模式，以减少开发工作量。国内比较好的微服务框架就是阿里巴巴的DubboX，国外的就是Spring Cloud，

### 20.1 Serverless发展史简介

2014年11月，AWS预发布了新产品AWS Lambda, Lambda为云中运行的应用程序提供了一种全新的系统体系结构——Serverless, Lambda被描述为：一种计算服务，根据事件驱动用户的代码，无需关心底层的计算资源。2015年5月AWS正式发布Lambda通用版。

2017年4月，阿里云正式宣布函数计算（Function Compute）启动邀测，代表了国内首个事件驱动的Serverless计算平台的诞生。

Serverless产品为开发人员创造了新的机会——专注于创建应用程序而无需考虑底层基础架构成为可能，不再需要配置或管理服务器，把这些统统交给云服务商来解决。

### 20.2 Serverless的工作原理

Serverless下包含的两个概念：函数即服务，即Function as a Service，简称FaaS；后端即服务，即Backend as a Service，简称BaaS。

### 20.4 Serverless适用场景

5．低频请求场景
在物联网行业中，由于物联网设备传输数据量小，且往往是固定时间间隔进行数据传输，因此经常涉及低频请求场景，通过这种Serverless方式就能够有效解决效率问题，降低使用成本。

### 21.2 Linkerd

■ 熔断：Linkerd包含自动熔断功能，启动后将停止把流量发送到被认为不健康的实例中，从而使它们有机会恢复并避免连锁反应故障。

Istio作为第二代Service Mesh，通过控制平面带来了前所未有的控制力

### 21.3 Istio

Istio支持将协议特定的故障注入到网络中，而不是“杀死”Pod、延迟或破坏TCP层的数据包。基本原理是应用层观察到的故障无论网络级别多少，故障现象都是相同的，并且可以在应用层注入更有意义的故障（如HTTP错误代码）以实现应用程序的弹性。

### 21.4 Service Mesh发展展望

Kubernetes已成为容器调度编排的事实标准，Docker容器作为微服务的最小载体可以发挥微服务架构的最大优势，而Istio等Service Mesh架构的出现刚好弥补了Kubernetes在微服务间通信上的短板，从而形成一个完整的、健壮的、高性能微服务架构。