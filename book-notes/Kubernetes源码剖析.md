## Kubernetes源码剖析
> 郑东旭

### 内容简介

主要分析了Kubernetes核心功能的实现原理，是一本帮助读者了解Kubernetes架构设计及内部原理实现的书。由于Kubernetes代码量较大，源码不容易理解，所以本书将梳理相关知识点，帮助读者快速学习。

本书适合云计算领域的相关技术人员、Kubernetes开发者、Go语言开发者等阅读。

### 前言

几年，容器技术越来越普及。据Gartner预估，到2022年，全球将有75%!的(MISSING)公司使用容器技术，而在2017年，这个比例还不到20%!，(MISSING)这说明容器技术的发展非常迅速。

得益于Kubernetes系统的高扩展性，Kubernetes越来越像一个系统核心，对外提供通用接口，实现了众多标准化。另外，Kubernetes得到了许多云服务提供商（Cloud Provider）的支持，例如Google、Cisco、VMware、Microsoft、Amazon及许多其他大型公司。

### 第1章 Kubernetes架构

Kubernetes系统具有如下特点。
● 可移植：支持公有云、私有云、混合云、多重云（Multi-cloud）。
● 可扩展：模块化、插件化、可挂载、可组合。
● 自动化：自动部署、自动重启、自动复制、自动伸缩/扩展。

### 1.1 Kubernetes的发展历史

2015年左右，Google在美国波特兰的OSCON 2015大会上宣布并正式发布Kubernetes 1.0。Google与Linux基金会合作组建了云原生计算基金会（CNCF）。CNCF旨在为云原生软件构建可持续发展的生态系统，并围绕一系列高质量开源项目建立社区，整合这些开源技术来让编排容器成为微服务架构的一部分。CNCF自创立以来已经拥有非常多的高质量项目，其中包括Kubernetes、Prometheus、gRPC、CoreDNS等。

### 1.2 Kubernetes架构图

微服务是一种软件设计模式，适用于集群上的可扩展部署。开发人员使用这一模式能够创建小型、可组合的应用程序，通过定义良好的HTTP REST API接口进行通信。Kubernetes也是遵循微服务架构模式的程序，具有弹性、可观察性和管理功能，可以适应云平台的需求。

图1-1 Kubernetes总架构图

Master服务端也被称为主控节点，它在集群中主要负责如下任务。（1）集群的“大脑”，负责管理所有节点（Node）。（2）负责调度Pod在哪些节点上运行。（3）负责控制集群运行过程中的所有状态。

● kube-apiserver组件：集群的HTTP REST API接口，是集群控制的入口。● kube-controller-manager组件：集群中所有资源对象的自动化控制中心。● kube-scheduler组件：集群中Pod资源对象的调度服务。

● kubelet组件：负责管理节点上容器的创建、删除、启停等任务，与Master节点进行通信。

### 1.3 Kubernetes各组件的功能

用户可以通过kubectl以命令行交互的方式对Kubernetes API Server进行操作，通信协议使用HTTP/JSON。


kubectl发送相应的HTTP请求，请求由Kubernetes API Server接收、处理并将结果反馈给kubectl。kubectl接收到响应并展示结果。至此，kubectl与kube-apiserver的一次请求周期结束。

client-go简单、易用，Kubernetes系统的其他组件与Kubernetes API Server通信的方式也基于client-go实现。

在大部分基于Kubernetes做二次开发的程序中，建议通过client-go来实现与Kubernetes APIServer的交互过程。这是因为client-go在Kubernetes系统上做了大量的优化，Kubernetes核心组件（如kube-scheduler、kube-controller-manager等）都通过client-go与Kubernetes APIServer进行交互。

kube-apiserver组件也是集群中唯一与Etcd集群进行交互的核心组件。例如，开发者通过kubectl创建了一个Pod资源对象，请求通过kube-apiserver的HTTP接口将Pod资源对象存储至Etcd集群中。

kube-apiserver属于核心组件，对于整个集群至关重要，它具有以下重要特性。● 将Kubernetes系统中的所有资源对象都封装成RESTful风格的API接口进行管理。● 可进行集群状态管理和数据管理，是唯一与Etcd集群交互的组件。● 拥有丰富的集群安全访问机制，以及认证、授权及准入控制器。● 提供了集群各组件的通信和交互功能。

Controller Manager负责确保Kubernetes系统的实际状态收敛到所需状态，其默认提供了一些控制器（Controller），例如DeploymentControllers控制器、StatefulSet控制器、Namespace控制器及PersistentVolume控制器等，每个控制器通过kube-apiserver组件提供的接口实时监控整个集群每个资源对象的当前状态，当因发生各种故障而导致系统状态出现变化时，会尝试将系统状态修复到“期望状态”。

kubelet组件，用于管理节点，运行在每个Kubernetes节点上。kubelet组件用来接收、处理、上报kube-apiserver组件下发的任务。kubelet进程启动时会向kube-apiserver注册节点自身信息。它主要负责所在节点（Node）上的Pod资源对象的管理，例如Pod资源对象的创建、修改、监控、删除、驱逐及Pod生命周期管理等。

kubelet也会对所在节点的镜像和容器做清理工作，保证节点上的镜像不会占满磁盘空间、删除的容器释放相关资源。

● Container Runtime Interface：简称CRI（容器运行时接口），提供容器运行时通用插件接口服务。CRI定义了容器和镜像服务的接口。CRI将kubelet组件与容器运行时进行解耦，将原来完全面向Pod级别的内部接口拆分成面向Sandbox和Container的gRPC接口，并将镜像管理和容器管理分离给不同的服务。


● Container Network Interface：简称CNI（容器网络接口），提供网络通用插件接口服务。CNI定义了Kubernetes网络插件的基础，容器创建时通过CNI插件配置网络。

● Container Storage Interface：简称CSI（容器存储接口），提供存储通用插件接口服务。CSI定义了容器存储卷标准规范，容器创建时通过CSI插件配置存储卷。

### 1.4 Kubernetes Project Layout设计

后来Go语言社区提出Standard Go Project Layout方案，以对Go语言项目目录结构进行划分。目前该标准已经成为众多Go语言开源项目的选择。

### 第2章 Kubernetes构建过程

构建过程是指“编译器”读取Go语言代码文件，经过大量的处理流程，最终产生一个二进制文件的过程；也就是将人类可读的代码转化成计算机可执行的二进制代码的过程。

### 2.2 本地环境构建

而在实际的Go语言开发项目中使用Makefile是好的约束规范。Makefile是一个非常有用的自动化工具，可以用来构建和测试Go语言应用程序。

● Makefile：顶层Makefile文件，描述了整个项目所有代码文件的编译顺序、编译规则及编译后的二进制输出等。● Makefile.generated_files：描述了代码生成的逻辑。

### 2.3 容器环境构建

● make quick-release：快速构建，只构建当前平台，并略过单元测试过程。

### 3.1 Group、Version、Resource核心数据结构

 Kind：资源种类，描述Resource的种类，与Resource为同一级别。

资源组、资源版本、资源、子资源的完整表现形式为<group>/<version>/<resource>/<subresource>。以常用的Deployment资源为例，其完整表现形式为apps/v1/deployments/status。

另外，资源对象（Resource Object）在本书中也是一个常用概念，由“资源组+资源版本+资源种类”组成，并在实例化后表达一个资源对象，例如Deployment资源实例化后拥有资源组、资源版本及资源种类，其表现形式为<group>/<version>，Kind=<kind>，例如apps/v1，Kind=Deployment。

每一个资源都拥有一定数量的资源操作方法（即Verbs），资源操作方法用于Etcd集群存储中对资源对象的增、删、改、查操作。目前Kubernetes系统支持8种资源操作方法，分别是create、delete、deletecollection、get、list、patch、update、watch操作方法。

每一个资源都至少有两个版本，分别是外部版本（External Version）和内部版本（InternalVersion）。外部版本用于对外暴露给用户请求的接口所使用的资源对象。内部版本不对外暴露，仅在Kubernetes API Server内部使用。

Kubernetes资源也可分为两种，分别是Kubernetes Resource（Kubernetes内置资源）和CustomResource（自定义资源）。开发者通过CRD（即Custom Resource Definitions）可实现自定义资源，它允许用户将自己定义的资源添加到Kubernetes系统中，并像使用Kubernetes内置资源一样使用它们。

### 3.3 Group

● 将众多资源按照功能划分成不同的资源组，并允许单独启用/禁用资源组。当然也可以单独启用/禁用资源组中的资源。

● 没有组名的资源组：被称为Core Groups（即核心资源组）或Legacy Groups，也可被称为GroupLess（即无组）。其表现形式为/<version>/<resource>，例如/v1/pods。

### 3.4 Version

Kubernetes的资源版本控制可分为3种，分别是Alpha、Beta、Stable，它们之间的迭代顺序为Alpha→Beta→Stable，其通常用来表示软件测试过程中的3个阶段。Alpha是第1个阶段，一般用于内部测试；Beta是第2个阶段，该版本已经修复了大部分不完善之处，但仍有可能存在缺陷和漏洞，一般由特定的用户群来进行测试；Stable是第3个阶段，此时基本形成了产品并达到了一定的成熟度，可稳定运行。Kubernetes资源版本控制详情如下。

Beta版本为相对稳定的版本，Beta版本经过官方和社区很多次测试，当功能迭代时，该版本会有较小的改变，但不会被删除。在默认的情况下，处于Beta版本的功能是开启状态的。Beta版本命名一般为v1beta1、v1beta2、v2beta1。

Stable版本为正式发布的版本，Stable版本基本形成了产品，该版本不会被删除。在默认的情况下，处于Stable版本的功能全部处于开启状态。Stable版本命名一般为v1、v2、v3。

下面以apps资源组为例，该资源组下的所有资源分别属于v1、v1beta1、v1beta2资源版本，如图

### 3.5 Resource

Kubernetes系统虽然有相当复杂和众多的功能，但它本质上是一个资源控制系统——管理、调度资源并维护资源的状态。

● default：所有未指定命名空间的资源对象都会被分配给该命名空间。● kube-system：所有由Kubernetes系统创建的资源对象都会被分配给该命名空间。● kube-public：此命名空间下的资源对象可以被所有人访问（包括未认证用户）。

● apiVersion：指定创建资源对象的资源组和资源版本，其表现形式为<group>/<version>，若是core资源组（即核心资源组）下的资源对象，其表现形式为<version>

 spec：包含有关Deployment资源对象的核心信息，告诉Kubernetes期望的资源状态、副本数量、环境变量、卷等信息。

### 3.6 Kubernetes内置资源全图

● kubectl api-resources：列出当前Kubernetes系统支持的Resource资源列表。

### 3.8 Unstructured数据

预先知道数据结构的数据类型是结构化数据。例如，JSON数据：

我们无法事先得知description的数据类型，它可能是字符串，也可能是数组嵌套等。原因在于Go语言是强类型语言，它需要预先知道数据类型，Go语言在处理JSON数据时不如动态语言那样便捷。当无法预知数据结构的数据类型或属性名称不确定时，通过如下结构来解决问题：

每个字符串对应一个JSON属性，其映射interface{}类型对应值，可以是任何类型。使用interface字段时，通过Go语言断言的方式进行类型转换：

### 3.10 Codec编解码器

Protobuf（Google Protocol Buffer）是Google公司内部的混合语言数据标准，Protocol Buffers是一种轻便、高效的结构化数据存储格式，可以用于结构化数据序列化。它很适合做数据存储或成为RPC数据交换格式。它可用于通信协议、数据存储等领域，与语言无关、与平台无关、可扩展的序列化结构数据格式。

### 3.11 Converter资源版本转换器

在Kubernetes系统中，同一资源拥有多个资源版本，Kubernetes系统允许同一资源的不同资源版本进行转换，例如Deployment资源对象，当前运行的是v1beta1资源版本，但v1beta1资源版本的某些功能或字段不如v1资源版本完善，则可以将Deployment资源对象的v1beta1资源版本转换为v1版本。可通过kubectl convert命令进行资源版本转换，执行命令如下：

### 第4章 kubectl命令行交互

kubectl工具是Kubernetes API Server的客户端。它的主要工作是向Kubernetes API Server发起HTTP请求。Kubernetes是一个完全以资源为中心的系统，而kubectl会通过发起HTTP请求来操纵这些资源（即对资源进行CRUD操作），以完全控制Kubernetes系统集群。

### 4.1 kubectl命令行参数详解

● Troubleshooting and Debugging Commands：故障排查和调试命令。

### 4.2 Cobra命令行参数解析

Cobra是一个创建强大的现代化CLI命令行应用程序的Go语言库，也可以用来生成应用程序的文件。很多知名的开源软件都使用Cobra实现其CLI部分，例如Istio、Docker、Etcd等。Cobra提供了如下功能。

● 如果命令输入错误，将提供智能建议，例如输入app srver，当srver参数不存在时，Cobra会智能提示用户是否应输入app server。

在Kubernetes系统中，Cobra被广泛使用，Kubernetes核心组件（kube-apiserver、kube-controller-manager、kube-scheduler、kubelet等）都通过Cobra来管理CLI交互方式。Kubernetes组件使用Cobra的方式类似，下面以kubectl为例，分析Cobra在Kubernetes中的应用，kubectl命令行示例如图4-1所示。

1.创建Command
实例化cobra.Command对象，并通过cmds.AddCommand方法添加命令或子命令。每个cobra.Command对象都可设置Run执行函数，代码示例如下：
代码路径：pkg/kubectl/cmd/cmd.go

### 第5章 client-go编程式交互

Kubernetes系统使用client-go作为Go语言的官方编程式交互客户端库，提供对Kubernetes APIServer服务的交互访问。

开发者常使用client-go基于Kubernetes做二次开发，所以client-go是开发者应熟练掌握的必会技能。

### 5.2 Client客户端对象

RESTClient是最基础的客户端。RESTClient对HTTP Request进行了封装，实现了RESTful风格的API。ClientSet、DynamicClient及DiscoveryClient客户端都是基于RESTClient实现的。

kubeconfig中存储了集群、用户、命名空间和身份验证等信息，在默认的情况下，kubeconfig存放在$HOME/.kube/config路径下。

### 5.5 EventBroadcaster事件管理器

由于Kubernetes的事件是一种资源对象，因此它们存储在Kubernetes API Server的Etcd集群中。为避免磁盘空间被填满，故强制执行保留策略：在最后一次的事件发生后，删除1小时之前发生的事件。

### 第6章 Etcd存储核心实现

Kubernetes系统使用Etcd作为Kubernetes集群的唯一存储，Etcd在生产环境中一般以集群形式部署（称为Etcd集群）。Etcd集群是分布式K/V存储集群，提供了可靠的强一致性服务发现。Etcd集群存储Kubernetes系统的集群状态和元数据，其中包括所有Kubernetes资源对象信息、资源对象状态、集群节点信息等。Kubernetes将所有数据存储至Etcd集群前缀为/registry的目录下。

### 6.5 CacherStorage缓存层

缓存（Cache）的应用场景非常广泛，可以使用缓存来降低数据库服务器的负载、减少连接数等。

在生产环境下，我们常将Memcached或Redis等作为MySQL的缓存层对外提供服务，缓存层中的数据存储于内存中，所以大大提升了数据库的I/O响应能力。

### 第7章 kube-apiserver核心实现

Kubernetes Pod资源对象创建流程介绍如下。

（1）使用kubectl工具向Kubernetes API Server发起创建Pod资源对象的请求。（2）Kubernetes API Server验证请求并将其持久保存到Etcd集群中。（3）Kubernetes API Server基于Watch机制通知kube-scheduler调度器。（4）kube-scheduler调度器根据预选和优选调度算法为Pod资源对象选择最优的节点并通知Kubernetes API Server。（5）Kubernetes API Server将最优节点持久保存到Etcd集群中。（6）Kubernetes API Server通知最优节点上的kubelet组件。（7）kubelet组件在所在的节点上通过与容器进程交互创建容器。（8）kubelet组件将容器状态上报至Kubernetes API Server。（9）Kubernetes API Server将容器状态持久保存到Etcd集群中。

### 7.1 热身概念

Go语言中的RESTful框架有很多，例如echo、gin、iris、beego等。在Kubernetes源码中使用了go-restful框架，主要原因在于go-restful框架可定制程度高。

go-restful框架支持多个Container（容器）。一个Container就相当于一个HTTP Server，不同Container之间监控不同的地址和端口，对外提供不同的HTTP服务。

一次HTTP请求的完整生命周期过程如下。（1）用户向Kubernetes API Server发出HTTP请求。

OpenAPI规范（早期称为Swagger规范）通过定义一种用于描述API的格式（也可以理解成API定义的语言，使用JSON或YAML格式），来规范RESTful服务开发过程。OpenAPI规范能够更简单、更快速地表述API。OpenAPI规范的另一个好处是，它不需要重写现有的API。但

OpenAPI规范的目标是：定义标准的、独立于语言的、指向REST API的接口，无须访问源码、文档，或是借助于网络流量检查，可被人类和计算机发现并理解。通过对OpenAPI做适当定义，消费者可使用最小的成本理解远程服务的逻辑，并与远程服务交互。

Swagger是OpenAPI规范的落地实现，通过Swagger可以编写基于OpenAPI规范的REST API，从文档生成、编辑、测试到各种主流语言的的代码自动生成都能完成。

主要的Swagger工具如下。● Swagger Editor：基于浏览器的编辑器，可以在线编写OpenAPI规范。● Swagger UI：将OpenAPI规范呈现为交互式API文档。● Swagger Codegen：根据OpenAPI规范生成服务器和客户端库。

HTTPS协议与HTTP协议相比具有如下特征。● 所有信息都是加密传播的，第三方无法窃听。● 具有校验数据完整性机制，确保数据在传输过程中不被篡改。● 配备身份证书，防止身份造假。

gRPC基于HTTP/2协议标准实现，它复用了很多HTTP/2的特性，例如双向流、流控制、报头压缩，以及通过单个TCP连接的多路复用请求等。HTTP/2可以使程序更快、更简单、更强大。另外，Protocol Buffers协议在数据帧上内置了HTTP/2的流控制，这样用户可以方便地控制系统吞吐量，但在诊断架构中的问题时确实会增加额外的复杂性，因为客户端或服务端都可以设置自己的流量控制值。

Kubernetes使用开源库的组合，以gRPC协议和HTTP REST协议提供gRPC服务，使用API多路复用器（API Multiplexer，即Mux）为用户提供最佳服务。

### 7.6 认证

在开启HTTPS服务后，所有的请求都需要经过认证。kube-apiserver支持多种认证机制，并支持同时开启多个认证功能。当客户端发起一个请求，经过认证阶段时，只要有一个认证器通过，则认证成功。如果认证成功，用户名就会传入授权阶段做进一步的授权验证，而对于认证失败的请求则返回HTTP 401状态码。

kube-apiserver目前提供了9种认证机制，分别是BasicAuth、ClientCA、TokenAuth、BootstrapToken、RequestHeader、WebhookTokenAuth、Anonymous、OIDC、ServiceAccountAuth。每一种认证机制被实例化后会成为认证器（Authenticator），

kube-apiserver通过指定--basic-auth-file参数启用BasicAuth认证。AUTH_FILE是一个CSV文件，每个用户在CSV中的表现形式为password、username、uid，

ClientCA认证，也被称为TLS双向认证，即服务端与客户端互相验证证书的正确性。使用ClientCA认证的时候，只要是CA签名过的证书都可以通过验证。

kube-apiserver通过指定--token-auth-file参数启用TokenAuth认

### 7.7 授权

kube-apiserver目前提供了6种授权机制，分别是AlwaysAllow、AlwaysDeny、ABAC、Webhook、RBAC、Node，可通过指定--authorization-mode参数设置授权机制。

● RBAC：即Role-Based Access Control，基于角色的访问控制。

RBAC授权器现实了基于角色的权限访问控制（Role-Based Access Control），其也是目前使用最为广泛的授权模型。在RBAC授权器中，权限与角色相关联，形成了用户—角色—权限的授权模型。用户通过加入某些角色从而得到这些角色的操作权限，这极大地简化了权限管理。RBAC授权如图7-28所示。

### 7.9 进程信号处理机制

信号的用处非常广泛，例如在Prometheus源码中，会监控SIGHUP信号，若该信号被触发，会热加载配置文件（即在不重启进程的情况下重新加载配置文件）。

7.9.2 进程的优雅关闭当进程关闭时，kube-apiserver很可能正在处理很多连接，如果此时直接关闭服务，这些连接将会断掉，影响用户体验，所以需要在关闭进程之前做一些清理操作，以实现进程的优雅关闭。代码示例如下：

早期Linux系统使用initd进程负责Linux的进程管理，后期Systemd取代了initd，成为系统的第一个进程（即PID为1），其他进程都是它的子进程。如果进程被systemd管理，需要向该进程报告当前进程的状态。

### 8.2 kube-scheduler架构设计详解

kube-scheduler调度器在为Pod资源对象选择合适节点时，有如下两种最优解。● 全局最优解：是指每个调度周期都会遍历Kubernetes集群中的所有节点，以便找出全局最优的节点。● 局部最优解：是指每个调度周期只会遍历部分Kubernetes集群中的节点，找出局部最优的节点。

全局最优解和局部最优解可以解决调度器在小型和大型Kubernetes集群规模上的性能问题。目前kube-scheduler调度器对两种最优解都支持。当集群中只有几百台主机时，例如100台主机，kube-scheduler使用全局最优解。当集群规模较大时，例如其中包含5000多台主机，kube-scheduler使用局部最优解。具体源码实现请参考8.7.2节中“预选调度前的性能优化”部分。

### 8.5 亲和性调度

或者如果两个业务联系得很紧密，则可以将它们调度到同一个节点上，以减少因网络传输而带来的性能损耗等问题。

开发者只需要在这些节点上打上相应的标签，然后调度器就可以通过标签进行Pod资源对象的调度了。这种调度策略被称为亲和性和反亲和性调度，分别介绍如下。● 亲和性（Affinity）：用于多业务就近部署，例如允许将两个业务的Pod资源对象尽可能地调度到同一个节点上。● 反亲和性（Anti-Affinity）：允许将一个业务的Pod资源对象的多副本实例调度到不同的节点上，以实现高可用性。

● NodeAffinity：节点亲和性，Pod资源对象与节点之间的关系亲和性。● PodAffinity：Pod资源对象亲和性，Pod资源对象与Pod资源对象的关系亲和性。● PodAntiAffinity：Pod资源对象反亲和性，Pod资源对象与Pod资源对象的关系反亲和性。注意：Pod资源对象之间的亲和性和反亲和性需要大量逻辑处理，这在大型Kubernetes集群中会大大降低调度性能，因此官方不建议在大于数百个节点的群集中使用它们。

 RequiredDuringSchedulingIgnoredDuringExecution：Pod资源对象必须被部署到满足条件的节点上，如果没有满足条件的节点，则Pod资源对象创建失败并不断重试。该策略也被称为硬（Hard）策略。

PreferredDuringSchedulingIgnoredDuringExecution：Pod资源对象优先被部署到满足条件的节点上，如果没有满足条件的节点，则从其他节点中选择较优的节点。该策略也被称为软（Soft）模式。

### 8.7 调度器核心实现

（1）PDB中断次数最少的节点。PDB（PodDisruptionBudget）能够限制同时中断的Pod资源对象的数量，以保证集群的高可用性。

### 8.8 领导者选举机制

领导者选举就是要保证每个组件的高可用性，例如，在Kubernetes集群中，允许同时运行多个kube-scheduler节点，其中正常工作的只有一个kube-scheduler节点（即领导者节点），其他kube-scheduler节点为候选（Candidate）节点并处于阻塞状态。在领导者节点因某些原因而退出后，其他候选节点则通过领导者选举机制竞选，有一个候选节点成为领导者节点并接替之前领导者节点的工作。

领导者选举机制是分布式锁机制的一种，实现分布式锁有多种方式，例如可通过ZooKeeper、Redis、Etcd等存储服务。Kubernetes系统依赖于Etcd做存储服务，系统中其他组件也是通过Etcd实现分布式锁的。kube-scheduler组件在Etcd上实现分布式锁的原理如下。

Kubernetes支持3种资源锁，资源锁的意思是基于Etcd集群的key在依赖于Kubernetes的某种资源下创建的分布式锁。3种资源锁介绍如下。
● EndpointsResourceLock：依赖于Endpoints资源，默认资源锁为该类型。
● ConfigMapsResourceLock：依赖于Configmaps资源。
● LeasesResourceLock：依赖于Leases资源。

可通过--leader-elect-resource-lock参数指定使用哪种资源锁，如不指定则EndpointsResourceLock为默认资源锁。它的key（分布式锁）存在于Etcd集群的/registry/services/endpoints/kube-system/kube-scheduler中。该key中存储的是竞选为领导者节点的信息