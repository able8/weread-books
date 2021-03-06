## 深入浅出Istio：Service Mesh快速入门与实践
> 崔秀龙

### 第1章 服务网格的历史

微服务架构在很大程度上提高了应用的伸缩性，方便了部门或业务之间的协作，使技术岗位能够更好地引入新技术并提高自动化程度，最终达到减耗增效的目的。然而和所有新方法一样，微服务架构在解决老问题的同时，也带来了一些新问题，例如：
◎ 实例数量急剧增长，对部署和运维的自动化要求更高；
◎ 用网络调用代替内部API，对网络这一不可靠的基础设施依赖增强；
◎ 调用链路变长，分布式跟踪成为必选项目；
◎ 日志分散严重，跟踪和分析难度加大；
◎ 服务分散，受攻击面积更大；
◎ 在不同的服务之间存在协作关系，需要有更好的跨服务控制协调能力；
◎ 自动伸缩、路由管理、故障控制、存储共享，等等。

为了解决微服务架构产生的一些问题，以Kubernetes为代表的容器云系统出现了。这类容器云系统以容器技术为基础，在进程级别为微服务提供了一致的部署、调度、伸缩、监控、日志等功能。

### 1.1 Spring Cloud

诞生于2015年的Spring Cloud应该是Service Mesh的老前辈了。事实上，时至今日，Spring Cloud仍是Service Mesh的标杆。
Spring Cloud最早在功能层面为微服务治理定义了一系列标准特性，例如智能路由、熔断机制、服务注册与发现等，并提供了对应的库和组件来实现这些标准特性。

◎ 需要在代码级别对诸多组件进行控制，包括Sidecar在内的组件都依赖Java的实现，这和微服务的多语言协作目标是背道而驰的；

### 1.3 Istio

2016年，Lyft开始了对现代网络代理软件Envoy的内部研发，并在同年9月将Envoy开源。由C++语言开发而成的Envoy在开源之后，迅速获得了大量关注。它除了具备强大的性能，还提供了众多现代服务网格所需的功能特性，并开放了大量精雕细琢的编程接口，为后面的广泛应用埋下了伏笔

2017年5月，Google、IBM和Lyft宣布了Istio的诞生。Istio以Envoy为数据平面，通过Sidecar的方式让Envoy同业务容器一起运行，并劫持其通信，接受控制平面的统一管理，在此基础上为服务之间的通信提供丰富的连接、控制、观察、安全等特性。本书后续会对Istio进行较为详细的讲解，这里不再赘述。

### 1.4 国内服务网格的兴起

蚂蚁金服的SOFAMesh针对其大流量生产场景，在Istio的架构基础上采用自研的Sidecar代替了Envoy，并在强力推进。

### 第2章 服务网格的基本特性

曾经给出对服务网格的定义：服务网格是一个独立的基础设施层，用来处理服务之间的通信。现代的云原生应用是由各种复杂技术构建的服务组成的，服务网格负责在这些组成部分之间进行可靠的请求传递。目前典型的服务网格通常提供了一组轻量级的网络代理，这些代理会在应用无感知的情况下，同应用并行部署、运行。

◎ 安全：为网格内部的服务之间的调用提供认证、加密和鉴权支持，在不侵入代码的情况下，加固现有服务，提高其安全性。

### 2.2 安全

（3）如果是共享集群，则服务的认证和加密变得尤为重要，例如：
◎ 服务之间的通信要防止被其他服务监听；
◎ 只有提供有效身份的客户端才可以访问指定的服务；
◎ 服务之间的互访应该提供更细粒度的控制功能。

### 2.4 观察

因此，微服务系统监控，除了有传统的主机监控，还更加重视高层次的服务健康监控。

### 第3章 Istio基本介绍

Istio出自名门，由Google、IBM和Lyft在2017年5月合作推出，它的初始设计目标是在Kubernetes的基础上，以非侵入的方式为运行在集群中的微服务提供流量管理、安全加固、服务监控和策略管理等功能。

### 3.1 Istio的核心组件及其功能

◎ 数据面被称为“Sidecar”，可将其理解为旧式三轮摩托车的挂斗。Sidecar通过注入的方式和业务容器共存于一个Pod中，会劫持业务应用容器的流量，并接受控制面组件的控制，同时会向控制面输出日志、跟踪及监控数据。

◎ 读取Istio的各项控制配置，在进行转换之后，将其发给数据面进行实施。

Pilot的配置内容会被转换为数据面能够理解的格式，下发给数据面的Sidecar, Sidecar根据Pilot指令，将路由、服务、监听、集群等定义信息转换为本地配置，完成控制行为的落地。如

网格中的服务在每次调用之前，都向Mixer发出预检请求，查看调用是否允许执行。在每次调用之后，都发出报告信息，向Mixer汇报在调用过程中产生的监控跟踪数据。

它是用于证书管理的。在集群中启用了服务之间的加密之后，Citadel负责为集群中的各个服务在统一CA的条件下生成证书，并下发给各个服务中的Sidecar，服务之间的TLS就依赖这些证书完成校验过程。

Istio中的默认Sidecar是由Envoy派生出来的，在理论上，只要支持Envoy的xDS协议，其他类似的反向代理软件就都可以代替Envoy来担当这一角色。

在Istio的默认实现中，Istio利用istio-init初始化容器中的iptables指令，对所在Pod的流量进行劫持，从而接管Pod中应用的通信过程，如此一来，就获得了通信的控制权，控制面的控制目的最终得以实现。

在同一个Pod内的多个容器之间，网络栈是共享的，这正是Sidecar模式的实现基础。从图3-4中可以看到，Sidecar在加入之后，原有的源容器→目的容器的直接通信方式，变成了源容器→Sidecar→Sidecar→目的容器的模式。而Sidecar是用来接受控制面组件的操作的，这样一来，就让通信过程中的控制和观察成为可能。

### 3.2 核心配置对象

用户利用Istio控制微服务通信，是通过向Kubernetes提交CRD资源的方式完成的。Istio中的资源分为三组进行管理，分别是networking.istio.io、config.istio.io及authentication.istio.io，

网络边缘的Ingress流量，会通过对应的Istio Ingress Gateway Controller进入；网格内部的服务互访，则通过虚拟的mesh网关进行（mesh网关代表网格内部的所有Sidecar）。

### 4.2 快速部署Istio

查看istio-system命名空间中的Pod启动状况，其中的-w参数用于持续查询Pod状态的变化：

### 4.3 部署两个版本的服务

接下来使用istioctl进行注入。之后会一直用到istioctl命令，它的基本作用就是修改Kubernetes Deployment，在Pod中注入在前面提到的Sidecar容器

每个Pod都变成了两个容器，这也就是Istio注入Sidecar的结果。可以使用kubectl describe po命令查看Pod的容器：

在这个Pod中多了一个容器，名称为istio-proxy，这就是注入的结果。另外，前面还有一个名称为istio-init的初始化容器，这个容器是用于初始化劫持的。对于这些内容，之后会进行详细讲解。

### 4.4 部署客户端服务

这个应用并没有提供对外服务的能力，我们也还是给它创建了一个Service对象，这同样是Istio的注入要求：没有Service的Deployment是无法被Istio发现并进行操作的。

### 4.6 创建目标规则和默认路由

apiVersion: networking.istio.io/v1alpha3￼
kind: DestinationRule￼

接下来就需要为flaskapp服务创建默认的路由规则了，不论是否进行进一步的流量控制，都建议为网格中的服务创建默认的路由规则，以防发生意料之外的访问结果。

定义了一个VirtualService对象，将其命名为flaskapp-default-v2，它负责接管对“flaskapp”这一主机名的访问，会将所有流量都转发到DestinationRule定义的v2 subset上。

### 4.7 小结

较为典型的Istio服务上线流程：注入→部署→创建目标规则→创建默认路由。绝大多数Istio网格应用都会遵循这一流程进行上线。

### 第5章 用Helm部署Istio

这种包含复杂依赖关系的应用部署过程，需要由功能足够强大的模板系统提供支持，因此Istio官方推荐使用Helm对Istio进行部署。

### 5.1 Istio Chart概述

Helm是目前Istio官方推荐的安装方式。除去安装后，我们还可以对输入值进行一些调整，完成对Istio的部分配置工作。Istio Chart是一个总分结构，其分级结构和设计结构是一致的

在Istio的发行包中包含一组values文件，提供Istio在各种场景下的关键配置范本，这些范本文件可以作为Helm的输入文件，来对Istio进行典型定制。

在改写完成后使用helm template命令生成最终的部署文件，这样就能用kubectl完成部署了

如果修改全局变量grafana.enabled为False，就不会安装Grafana了。

◎ certmanager：一个基于Jetstack Cert-Manager项目的ACME证书客户端，用于自动进行证书的申请、获取及分发。

### 5.2 全局变量介绍

Istio Chart分为父子两层，因此变量也具有全局和本地两级。全局变量使用保留字global进行定义，子Chart可以通过values.global的方式引用全局变量，而在主Chart中也可以用chart.var的方式为子Chart指定变量值。

这两个变量对于内网部署是非常有必要的，将Istio的镜像拉取回来，并推送到私库之后，只要在values.yaml中进行修改，就可以将Istio所需镜像的引用指向内网私库，省去了逐个修改Deployment文档的麻烦。

### 5.3 Istio安装清单的生成和部署

在完成对values.yaml的编辑之后，就可以使用helm template命令来生成最终的部署清单文件了，例如我们生成的输入文件为my-values.yaml，那么可以用如下命令生成我们需要的YAML文件：

### 5.4 小结

Helm还有一种常见的部署方式，就是通过helm install命令进行部署。但是，采用这种部署方式时，需要在Kubernetes中部署Tiller服务端，而且不会生成部署清单文件，这对于配置管理来说是很不方便的，因此这里不做推荐。

### 6.1 在网格中部署应用

不难发现其中的Service对象没有发生任何变化，两个Deployment对象则有很大的改变：

### 6.3 使用Istio Dashboard

我们可以将其服务类型修改为LoadBalance，或者为其创建Ingress对象，开放外网访问。
另外，如果要在外网访问Grafana，则应该将security.enabled修改为true，并设置用户名和密码。

### 6.5 使用Jaeger

微服务之间的调用关系往往比较复杂，在深度和广度方面都会有较长的调用路线，因此需要有跨服务的跟踪能力。
在Istio中提供了Jaeger作为分布式跟踪组件。Jaeger也是CNCF的成员，是一个分布式跟踪工具，提供了原生的OpenTracing支持，向下兼容ZipKin，同时支持多种存储后端。

### 6.6 使用Kiali

这里可以清楚地看到各个服务的调用关系和流量状况。例如，我们在前面使用sleep pod的Shell中，使用HTTP客户端工具通过flaskapp应用调用httpbin的过程，在这张图中的表现就很明显。

### 第7章 HTTP流量管理

◎ 在微服务环境中，如何将大量的微服务连接在一起，进行有效的远程服务调用，是业务运转的基本要求；

Istio提供了强大的流量控制能力，可以在业务应用无感知的情况下，对应用的通信行为进行干涉和保护，将很多原本需要在应用中处理的通信功能，下沉到Istio进行配置和管理，能够有效降低开发和运维成本，并提高对服务的控制能力。

### 7.1 定义目标规则

Istio的服务网格中对服务进行了进一步抽象：
◎ 可以使用Pod标签对具体的服务进程进行分组；
◎ 可以定义服务的负载均衡策略；
◎ 可以为服务指定TLS要求；
◎ 可以为服务设置连接池大小。

Istio中，这种同一服务不同组别的后端被称为子集（Subset），也经常被称为服务版本。

host：是一个必要字段，代表Kubernetes中的一个Service资源，或者一个由ServiceEntry（会在7.9节讲解出站流量时介绍）定义的外部服务。为了防止Kubernetes不同命名空间中的服务重名，这里强烈建议使用完全限定名，也就是使用FQDN来赋值。

### 7.2 定义默认路由

Istio建议为每个服务都创建一个默认路由，在访问某一服务的时候，如果没有特定的路由规则，则使用默认的路由规则来访问指定的子集，以此来确保服务在默认情况下的行为稳定性。

在Istio中部署一个业务应用时，建议做到以下几点：
◎ 使用app标签表明应用身份；
◎ 使用version标签表明应用版本；
◎ 创建目标规则；
◎ 创建默认路由规则。

### 7.3 流量的拆分和迁移

下面定义一个分流规则：对flaskapp服务的访问，有70%!进(MISSING)入v1版本，有30%!进(MISSING)入v2版本。

### 7.5 根据来源服务进行路由

来自不同版本的服务访问不同版本的目标服务，比如v1版本的sleep向flaskapp发出的请求由flaskapp的v1版本提供响应，其他版本由flaskapp的v2版本负责

sourceLabels对来源路由进行了匹配

### 7.7 通信超时控制

通过Istio的流量控制功能可以对服务间的超时进行控制，在应用无感知的情况下，根据VirtualService的配置，动态调整调用过程中的超时上限，从而达到控制故障范围的目的。相对于代码修改，这种非入侵的调整方式能够节省大量的成本。

### 7.9 入口流量管理

Ingress Gateway在逻辑上相当于网络边缘的一个负载均衡器，用于接收和处理网格边缘出站和入站的网络连接，其中包含开放端口和TLS的配置等内容。

这里的mesh是Istio内部的虚拟Gateway，代表网格内部的所有Sidecar，换句话说：所有网格内部服务之间的互相通信，都是通过这个网关进行的。

◎ selector实际上是一个标签选择器，用于指定由哪些Gateway Pod来负责这个Gateway对象的运行；

参照官方文档可以发现，tls secret只能包含一个证书对，因此一个Gateway是无法处理两个域名的HTTPS的，可以换用Generic类型的证书。

### 7.10 出口流量管理

◎ 注册ServiceEntry：把网格外部的服务使用ServiceEntry的方式注册到网格内部。

可以为外部服务设置ServiceEntry，相当于将外部服务在网格内部进行了注册。相对于使用CIDR白名单的方式，这种方式让Istio对外部服务的访问有了更大的管理能力。

使用ServiceEntry的一个好处就是，可以利用流量管理特性，对外部访问进行监控和管理。

### 7.12 设置服务熔断

服务熔断是一种保护性措施，即在服务实例无法正常提供服务的情况下，将其从负载均衡池中移除，不再为其分配任务，避免在故障实例上积压更多的任务，并且可以在等待服务能力恢复后，重新将发生故障的Pod加入负载均衡池。这也是一种常见的服务降级方法。

### 7.13 故障注入测试

借助Istio的故障注入能力，测试人员可以使用VirtualService配置方式，在任意调用中加入模拟故障，从而测试应用在故障状态下的响应情况，甚至可以在请求中注入中断，来阻止某些情况下的服务访问。

同我们设计的执行过程一致：来自sleep服务v1版本的流量，会被注入一个HTTP 500错误；来自sleep服务v2版本的流量，则不会受到影响。和延迟注入一样，中断注入也可以使用percent字段来设置注入百分比。

### 7.14 流量复制

流量复制是另一个用于测试的强大功能，它可以把指向一个服务版本的流量复制一份出来，发送给另一个服务版本。这一功能能够将生产流量导入测试应用，在复制出来的镜像流量发出之后不会等待响应，因此对正常应用的性能影响较小，又能在不影响代码的情况下，用更实际的数据对应用进行测试。

### 8.2 基于Denier适配器的访问控制

如果有流量的预检请求通过Rule对象传递给了这个Handler，就会调用失败，返回错误码7及错误信息“Not allowed”。

接下来就可以创建一个Rule对象，把二者连接起来。编辑denier.rule.yaml：
￼

在match字段中使用源和目标的标签对服务进行鉴别，整个表达式实现了对来自sleep服务的v1版本向httpbin服务发起调用的流量的识别。

### 8.3 基于Listchecker适配器的访问控制

Listechecker适配器中会保存一个列表，并可以声明这一列表是黑名单还是白名单，在有数据输入后，首先判断该数据是否属于列表成员，然后根据列表的黑名单或白名单属性来返回是否许可此次调用

### 8.4 使用MemQuota适配器进行服务限流

在Istio中，我们可以利用Mixer的MemQuota或RedisQuota适配器，在预检阶段对流量进行配额判断，根据为特定流量设定的额度规则来判断流量请求是否已经超过指定的额度（简称超额），如果发生超额，就进行限流处理。

### 8.6 为Prometheus定义监控指标

Mixer提供了内置的Prometheus适配器，该适配器会开放服务端点，使用来自metrics模板实例的数据供Prometheus进行指标抓取，

◎ envoy-stats：从网格的Pod中抓取Envoy的统计数据。

### 8.7 使用stdio输出自定义日志

◎ Fluentd等日志抓取工具用mount的方式加载节点文件系统；
◎ 日志抓取工具在处理原始日志之后将其发送给Elasticsearch。

### 9.1 Istio安全加固概述

安全加固能力是Istio各个组件协作完成的，如下所述：
◎ Citadel提供证书和认证管理功能；
◎ Sidecar建立加密通道，为其代理的应用进行协议升级，为客户端和服务端之间提供基于mTLS的加密通信；
◎ Pilot负责传播加密身份和认证策略。

### 9.3 设置RBAC

RBAC（Role Based Access Control）是目前较为通用的一种访问控制方法。Istio也提供了这样的方式来支持服务间的授权和鉴权。

RBAC系统中的授权过程都是通过以下几步进行设置的：
（1）在系统中定义原子粒度的权限；
（2）将一个或者多个权限组合为角色；
（3）将角色和用户进行绑定，从而让用户具备绑定的角色所拥有的权限。

### 第10章 Istio的试用建议

个人浅见：Istio的应用在现阶段只能小范围试探性地进行，在进行过程中要严格定义试用范围，严控各个流程。

◎ 方案部署：根据范围定义的决策，制定和执行相关的部署工作。其中包含Istio自身的部署和业务服务、后备服务的部署工作。

◎ 切换演练：防御措施，用于在业务失败时切回到原有的稳定环境。

### 10.1 Istio自身的突出问题

最后就是Istio的Sidecar注入模式，这种模式一定会增加服务间调用的网络延迟，目前是一个痼疾。Sidecar的固定延迟和Mixer的不确定行为相结合，有可能会产生严重后果。

### 10.2 确定功能范围

（4）mTLS和RBAC功能：大幅提高安全性，带来的代价是因为配置错误或者证书轮转等问题，造成服务中断的风险。

### 10.3 选择试用业务

◎ 所有网格之间的通信都要经过Sidecar的中转，会造成大约10毫秒的延迟。

◎ 所有流量都会经由Mixer处理，也有造成瓶颈的可能。

◎ 能够容忍一定的中断时间：不管是Istio还是其他新技术的采用，都可能会造成服务中断，应该避免选择使用关键业务进行试点。
◎ 对延迟不敏感：对于高频度、低延迟的服务类型，Sidecar造成的固定延迟总体来看会非常可观，因此不建议采用这种负载类型的业务作为试用服务。

◎ 调用深度较浅：一个试用服务如果包含太多层次，则每个层次之间都会因为Sidecar的延迟变慢少许，但是叠加起来会使得整个业务产生比较明显的延迟。

◎ 能够方便地回滚和切换：应该在前端负载均衡器、反向代理方面做好切换准备，在服务发生中断时，能够迅速切回原有环境。

◎ 具备成熟完善的功能、性能和疲劳测试方案：试用服务的上线必须有一个明确的标准，并以符合标准的测试结果来对标准进行验证。

### 10.4 试用过程

10.4.4 切换演练
在功能和性能测试全部通过之后，就应该进行试用服务和后备服务之间的双向切换的演练，在双方切换之后都应该重复进行在10.4.3节提到的验证过程，防止故障反复。
切换演练是试点应用的最后一道保险，在网格严重故障之后能否迅速恢复业务，全靠这一步的支持。