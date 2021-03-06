## Kubernetes微服务实战
> 吉吉·赛凡

### 译者序

绝大多数企业的软件架构都是从一个单体架构开始的，而不是微服务架构。因为单体架构足够简单、容易上手，不需要复杂的流程就可以快速开发应用程序。但是伴随着企业的成长，单体架构的风险就会逐渐凸显出来。在需求量激增的情况下，整个应用程序会因为某一部分或者某一进程遇到瓶颈而受到限制。

因为单体架构的紧耦合设计，应用程序也面临着可用性上的挑战，面对日益复杂的事务处理几乎无法持续扩展，并且在构建、部署和测试等流程上都变得非常困难。然而，对企业来说影响更大的一点是，单体架构严重阻碍了创新发展。

因此，企业会在发展过程中逐渐转向微服务架构，将应用程序拆分成多个“微”小的服务运行。这些微服务都是面向业务功能或者某个子领域进行构建的，每个微服务实现一个单独的功能。微服务的理念也提倡每个服务分别由小型的、独立的团队进行开发并负责管理（可以参考亚马逊的两个比萨团队原则），所以说这不仅是技术管理上的更新，更是企业组织管理上的变革。

全面地介绍了如何进行微服务的开发，并尽可能地从各个维度对其进行描述。从微服务的架构设计、构建、配置、测试、监控、安全，到持续集成/持续交付（CI/CD）流水线

众所周知，Kubernetes是目前最流行的开源平台之一，主要用于在集群中自动化应用程序容器的编排，包括部署、扩展和维护等。Kubernetes提供了一个以容器为中心的基础设施框架

本书也涉及无服务器计算和服务网格这些热门话题，探讨了它们是如何发挥各自的优势为基于微服务的系统提供帮助的，

希望读完本书后，你可以获得基于Kubernetes与微服务的云原生系统的设计、开发及管理的知识和经验。

### 前言

会介绍如何开发微服务并将其部署在Kubernetes平台上，基于微服务的架构与Kubernetes的结合将会带来巨大影响。

第3章探讨了为什么我们应该选择Go作为示例应用程序Delinkcious的编程语言，并简要介绍了Go语言开发。

第7章使我们可以开放示例应用程序Delinkcious的访问，并允许用户从集群外部与其进行交互。此外，我们还添加了基于gRPC的新闻服务，用户可以访问该服务以获取其关注的其他用户的新闻。最后，我们再添加一个消息队列，使服务以松耦合的方式进行通信。

第13章回顾了服务网格（尤其是Istio）这一热门话题，服务网格是目前真正改变游戏规则的角色。

### 1.2.1 容器编排平台

Kubernetes的主要功能就是在一组服务器（物理机或者虚拟机）上部署和管理大量基于容器的工作负载，这意味着Kubernetes提供了将容器部署到集群的方法，确保在遵守各种调度约束下，可以将容器有效地打包到集群节点中。

Kubernetes会自动监控容器，如果容器发生故障，则会重新启动它们。Kubernetes还会把工作负载从有问题的节点转移到其他健康节点上。

Kubernetes是一个非常灵活的平台，通过有效地调配底层的计算、存储和网络基础设施资源

### 1.2.2 Kubernetes发展历史

2015年在OSCON上Kubernetes 1.0正式发布，从此为容器编排平台的快速发展拉开了序幕。

### 1.3 Kubernetes架构

Kubernetes可以说是软件工程历史上非常成功的项目，其架构和设计是其成功的重要组成部分。每个集群都有一个控制平面和数据平面。控制平面由几个组件组成，例如API服务器、用于保持集群状态的元数据存储以及多个负责管理数据平面中的节点并向用户提供访问权限的控制器。生产环境中的控制平面一般会分布在多台服务器上，以实现高可用性和鲁棒性。数据平面由多个节点组成，控制平面将在这些节点上部署并运行Pod（容器组），监控变更并做出响应。

### 1.3.1 控制平面

为了保证冗余性，通常由三个或五个etcd实例组成一个集群。如果你丢失了etcd存储中的数据，那么你会丢掉整个集群。

·节点控制器（node controller）：负责在节点出现故障时进行通知和响应。
·副本控制器（replication controller）：确保每个副本集（replica set）或副本控制器（replication controller）对象中有正确数量的Pod。


·服务账户（service account）和令牌控制器（token controller）：它们使用默认服务账户和相应的API访问令牌对新的命名空间进行初始化。

### 1.3.2 数据平面

kubelet是Kubernetes的代理，它负责与API服务器进行通信，并运行和管理在节点上的Pod。以下是kubelet的一些作用：
·从API服务器下载Pod密钥。
·挂载存储卷。
·通过容器运行时接口（Container Runtime Interface，CRI）运行Pod容器。
·报告节点和Pod的状态。
·探测容器的状态。

2.kube proxy
kube proxy负责节点的网络连接，它充当服务的本地前端，并且可以转发TCP和UDP数据包。它通过DNS或环境变量来发现服务的IP地址。


现在Kubernetes通过基于gRPC的CRI接口运行容器

·集群管理
·部署
·故障排除和调试
·资源管理（Kubernetes对象）
·配置和元数据

### 1.4.1 微服务打包和部署

每个微服务都会有一个Dockerfile，生成的镜像表示该微服务的部署单元。

ReplicaSet将管理具有与matchLabels匹配的标签的容器：

### 1.4.3 微服务安全

2.服务账户
服务账户为你的微服务提供身份，每个服务账户将具有与其账户关联的某些特权和访问权限。

你可以将服务账户与Pod相关联（例如在部署的Pod的spec中），在Pod中运行的微服务将具有该身份以及与该账户相关联的所有特权和限制。如果你未分配服务账户，则Pod会使用其命名空间的默认服务账户。每个服务账户都与用于对其进行身份验证的密钥关联。

密钥是按命名空间管理的，并作为文件挂载（密钥卷）或环境变量保存在Pod中。创建密钥的方法有很多种。密钥包含两种映射：data和stringData。data映射中的值类型可以是任意的，但必须是base64编码的

最终结果是，由Kubernetes在Pod外部管理的数据库密钥将显示为Pod内部的普通文件，通过/etc/db_creds路径访问。

Kubernetes利用客户端证书对任何外部通信（例如kubectl）进行双向认证。从外部到Kubernetes API的所有通信都通过HTTPS进行。API服务器与工作节点上的kubelet之间的集群内部通信也是通过HTTPS（kubelet端点）进行的。但是，默认情况下它不使用客户端证书（你可以启用它）。

### 1.4.4 微服务验证和授权

基于角色的访问控制
基于角色的访问控制（Role-based Access Control，RBAC）不是必需的！你可以使用Kubernetes中的其他机制进行授权。但是，RBAC是最佳实践，它有两个概念：角色和绑定。角色定义了一组特定权限的规则。角色有两种：Role适用于单个命名空间，ClusterRole适用于集群中的所有命名空间。

### 1.4.5 微服务升级

系统升级的目标通常是零停机时间，并在出现问题时可以安全回滚。Kubernetes部署提供了支持这个目标的基本原语，例如更新部署、暂停部署以及回滚部署。你可以在这些坚实的基础上构建特定的工作流程。

Kubernetes提供了基于CPU、内存或自定义指标的Pod水平自动扩展（Horizontal Pod Autoscaler，HPA）功能，可以自动扩展服务。

有几类相关的监控信息需要特别关注：
·第三方程序日志
·应用程序日志
·应用程序错误
·Kubernetes事件
·指标

此外，除了错误日志本身，通常将相关的元数据（例如栈跟踪）记录在特有的系统（例如sentry[1]或rollbar[2]）中也是非常有价值的。对于检测系统性能和运行状况或者变化趋势，很多指标都是有帮助的。

3.日志
以下几种方法可以实现Kubernetes的集中日志管理。
·在每个节点上运行日志代理。
·向每个应用程序Pod中注入日志边车（sidecar）容器。
·让应用程序将其日志直接发送到集中的日志服务。

### 1.5.1 安装Minikube

现在，你可以安装Minikube了，依然可以使用Homebrew安装：
￼

### 1.5.2 探索集群

要浏览仪表板，你可以运行Minikube特有的命令minikube dashboard。推荐使用kubectl命令，因为它更通用，可以在任何Kubernetes集群上运行：

### 1.6 小结

Minikube项目让每个开发人员都能够在本地运行Kubernetes集群，这非常适合了解Kubernetes自身的功能和特性，并且可以在与生产环境非常相似的环境中进行本地测试。

### 2.2 微服务编程——少即是多

考虑将这个简单的程序集成到一个大型企业系统中，如图2-1所示，你将不得不考虑许多方面。

### 2.5 通过API公开服务

我喜欢将API视为网络连接的接口。

微服务通常会通过Web传输协议公开其功能，例如HTTP（REST、GraphQL、SOAP）、HTTP/2（gRPC），或者WebSocket。

使用标准网络API的好处是，它使用与编程语言无关的传输协议，可以实现微服务的多语言支持。每个服务都可以用自己的编程语言来实现（例如，一个服务通过Go实现，而另一个服务使用Python），甚至可以迁移到完全不同的语言（比如Rust）而不会受到干扰，因为所有这些服务都通过网络API访问。

### 2.6 使用客户端库

客户端库模式封装了远程服务和所有这些决策，并提供了一个标准接口，你可以调用该接口作为服务的客户端。

gRPC的最大卖点之一是它可以生成一个客户端库。

### 2.7 管理依赖

通常系统存在两种依赖关系：
·库/软件包（链接到正在运行的服务进程）
·远程服务（可通过网络访问）

### 2.8 协调微服务

单个微服务相对简单，对单个服务进行编写、修改和故障排除要容易一些。

### 2.9 利用所有权

由于微服务很小，一个开发人员可能负责整个微服务并完全理解它。其他开发人员可能也熟悉它，但是即使只有一个开发人员熟悉一项服务，对于新开发人员来说，接管它也应该相对简单和轻松，因为范围有限，甚至在理想情况下可能是相似的。

开发人员需要通过服务API与其他开发人员和团队进行通信，但是这样可以实现非常快速的迭代。

由于每个微服务的范围都很小，因此潜在的损害也是有限的，因为系统都是通过定义良好的API与其余部分进行交互的。

### 2.10 理解康威定律

“设计系统的组织……其产生的设计和架构等价于组织间的沟通结构。”
这意味着系统的结构将反映构建该系统的团队的结构。一个著名的变体是埃里克·雷蒙德（Eric Raymond）提出的：

### 2.10.2 水平组织

水平组织将系统视为分层架构，团队结构根据这些层次组织。可能会有一个前端组、一个后端组和一个DevOps组。每个小组负责其层中的所有方面，垂直功能是通过跨所有层的不同组之间的协作来实现的。这种方法更适合于产品数量较少（有时可能只有一种）的小型组织。

水平组织的好处是组织可以建立专业领域并在整个水平层进行知识共享。通常，组织从水平层次开始，随着组织的发展，逐渐扩展到更多产品，或者可能分布在多个地理位置，那时它们会被划分为更垂直的结构。在每个孤岛中，结构通常都是水平的。

### 2.11 跨服务故障排除

跨服务故障排除
由于系统的大多数功能都涉及多个微服务之间的交互，因此能够跟踪所有这些微服务和各种数据存储中的请求很重要。实现这个需求的最佳方法之一是分布式跟踪，你可以标记每个请求并从头到尾对其进行跟踪。

### 2.13.2 多仓库

但是，这种方法会带来巨大的成本，尤其是随着服务和项目数量的增加，以及它们之间的依赖关系变得更加复杂时：
·应用更改通常需要跨多个代码仓库进行更改。
·经常需要维护代码仓库的多个版本，因为不同的服务依赖不同的代码仓库。
·很难跨所有代码仓库进行跨领域的更改。

### 2.14.1 每个微服务对应一个数据存储

每个微服务对应一个数据存储是微服务架构的关键要素。当两个微服务可以直接访问同一数据存储时，它们就是紧耦合的，不再独立。

我们同意两个微服务不应共享同一数据存储。

### 2.14.3 使用Saga模式管理跨服务事务

原子性（atomic）：事务中的所有操作要么成功，要么失败。
·一致性（consistent）：数据状态符合事务前后的所有约束。
·隔离性（isolated）：并发事务的行为就像串行化一样。
·持久性（durable）：事务成功完成后，该事务对数据库所做的更改将永久地保存在数据库中。

持久性是很明显的。如果你的数据无法安全地持久存储，那么就没有必要去解决其他所有的问题。持久性也包含不同的级别：
·磁盘持久性：在没有发生磁盘故障时，数据可以在节点重新启动后保留。
·多节点冗余：数据可以在单个节点重新启动或者磁盘故障后保留，但不能是所有节点的临时性故障。
·磁盘冗余：数据可以在某个磁盘故障后保留。
·跨地域的副本：数据可以在某个地域的整个数据中心宕机时保留。
·备份：可以存储大量信息并且便宜得多，但恢复速度较慢，并且经常落后于实时数据。

CAP定理指出，分布式系统不能同时具有以下三个属性：
·一致性（consistency）
·可用性（availability）
·分区容错性（partition resiliency）

### 3.2 为什么选择Go

·Go可以编译为一个没有外部依赖的二进制文件（对于希望编写简洁的Dockerfile来说太棒啦）。
·Go非常易读，也易于学习。
·Go对网络编程和并发具有出色的支持。
·Go是许多云原生数据存储、队列和框架（包括Docker和Kubernetes）的实现语言。


### 3.3 认识Go kit

在实际系统中会有大量共享或横切关注点，你希望它们可以保持一致：
·配置
·密钥管理
·集中日志管理
·指标
·身份验证
·授权
·安全
·分布式跟踪
·服务发现

### 3.3.1 使用Go kit构建微服务

Go kit都是关于最佳实践的。你的业务逻辑通过纯Go语言库实现，只需处理接口和Go结构体。API、序列化、路由和网络中涉及的所有复杂内容都被划分到清晰独立的层，这些层利用了Go kit和基础设施中的概念（例如传输、端点和服务）。

### 3.3.5 理解中间件

Go kit还使用装饰器模式选择性地包装带有横切关注点的服务和端点，例如：
·弹性（例如，使用指数回退的重试）
·身份验证和授权
·日志
·指标收集
·分布式跟踪
·服务发现
·速率限制

### 3.3.6 理解客户端

Go kit的客户端端点与服务端点相似，但工作方向相反。服务端点对请求解码，将工作委托给服务，并对响应进行编码，客户端端点对请求进行编码，通过网络调用远程服务，并对响应进行解码。

### 3.5.2 服务实现

social_graph_manager是完成所有繁重工作的服务的实现，log包用于记录日志，而net/http包用于提供HTTP服务。

### 4.2 理解CI/CD流水线

CI/CD流水线的基本思想是：每当开发人员提交更改到源代码控制系统（例如GitHub）时，这些更改都会立即被持续集成（Continuous Integration，CI）系统检测到并进行测试。

当有新镜像可用时，持续交付（Continuous Delivery，CD）系统将把它部署到目标环境中。通过预置和部署，CD可以确保整个系统处于目标状态。有时，如果系统不支持动态配置，则可能由于配置更改而发生部署。我们将在第5章中详细讨论配置问题。

该流水线的各阶段功能如下：
1）开发人员将他们的更改提交给GitHub（源代码控制）。
2）CI服务器运行测试、构建Docker镜像，并将镜像推到DockerHub（镜像仓库）。
3）Argo CD服务器检测到有一个新镜像可用，然后将其部署到Kubernetes集群。

### 4.3 选择CI/CD流水线工具

我们评估了一些选项，并最终选择了CircleCI用于持续集成，Argo CD用于持续交付。起初，我们考虑为整个CI/CD流水线提供一站式服务，但是在查看了某些选项后，决定将它们视为两个独立的部分，并为CI和CD选择了不同的解决方案。让我们简要介绍一下这些选项（还有很多选项没有列出）：

### 4.3.1 Jenkins X

当我尝试实际使用Jenkins X时，我感到有些失望：
·它不能开箱即用，并且故障排除很麻烦。
·它比较封闭。
·它不能很好地（或者说完全不）支持单体仓库方法。

### 4.3.3 Travis CI和CircleCI

概念上讲，CI流程的作用是生成容器镜像并将其推送到镜像仓库，它完全不需要了解Kubernetes。另一方面，CD解决方案必须支持Kubernetes，才能很好地在集群内运行。

### 4.3.5 Argo CD

CD解决方案需要针对Kubernetes设计。最终选择Argo CD的原因有很多：
·可感知Kubernetes。
·基于通用工作流引擎（Argo）。
·友好的UI。
·在Kubernetes集群上运行。
·使用Go实现（不是很重要，但是我个人很喜欢）。

### 4.4 GitOps

GitOps是一个新的流行词，尽管这个概念并不是一个新事物。它是基础设施即代码（Infrastructure as Code，IaC）的另一种变体，其基本思想是应该对所有代码、配置及其所需的资源进行描述，并将其存储在源代码控制仓库中。每当你对代码仓库进行更改时，CI/CD解决方案都会做出响应并采取正确的措施，甚至可以通过还原到代码仓库中的先前版本来启动回滚。

CircleCI和Argo CD都完全支持并推崇GitOps模型。当对代码进行git push更改时，CircleCI将被触发并开始构建镜像。当对Kubernetes清单进行git push更改时，Argo CD将被触发并将这些更改部署到Kubernetes集群。

### 4.5.3 理解构建脚本

通过命令set-eo pipefail设定，如果发生任何问题脚本将立即退出（即使处在流水线中）。

### 4.5.4 使用多阶段Dockerfile对Go服务容器化

你在微服务系统中构建的Docker镜像是非常重要的。你会构建许多镜像，并且每个镜像都会构建很多很多次，这些镜像也将通过网络来回发送，很容易成为攻击者的目标。考虑到这一点，构建具有以下属性的镜像是有意义的：
·轻巧
·最小的攻击面

### 4.6.1 部署Delinkcious微服务

4.6.1　部署Delinkcious微服务
每个Delinkcious微服务都在k8s子目录中的YAML清单中定义了一组Kubernetes资源，下面是link服务的k8s目录结构：

### 4.6.2 理解Argo CD

Argo CD是针对Kubernetes的开源持续交付解决方案。它由Intuit公司创建，并被许多公司采用，包括Google、NVIDIA、Datadog和Adobe等。它具有一系列令人印象深刻的功能：
·将应用程序自动部署到特定目标环境。
·CLI命令行工具。
·应用程序以及目标状态和实时状态之间的差异的Web可视化。
·支持高级部署模式（蓝/绿部署和金丝雀部署）。
·支持多种配置管理工具（YAML文件、ksonnet、kustomize、Helm等）。
·持续监控所有已部署的应用程序。

所有应用程序组件的健康状况评估。
·SSO继承。
·GitOps Webhook集成（GitHub、GitLab和BitBucket）。
·用于与CI流水线集成的服务账户/访问密钥管理。
·用于应用程序事件和API调用的审计。

2.借助GitOps方法
Argo CD遵循GitOps方法，其基本原则是将系统状态存储在Git中。Argo CD通过检查Git差异并使用Git原语来回滚和协调实时状态，以便管理应用程序的实时状态与目标状态。

### 4.6.3 Argo CD入门

Argo CD还创建了两个自定义资源定义（Custom Resource Definition，CRD）：


2）设置端口转发以访问Argo CD服务器：
￼
3）管理员用户的初始密码是Argo CD服务器的名称：


### 4.6.4 配置Argo CD

Argo CD支持多种清单格式和模板，例如Helm、ksonnet和kustomize，本书后面的章节会介绍其中一些出色的工具。为简单起见，我们为每个应用程序配置了Argo CD支持的原生k8s YAML清单目录。
接下来就可以使用Argo CD了！

使用同步策略
默认情况下，Argo CD会检测应用程序的清单是否同步，但是它不会自动同步，这是一个很好的默认值。有时候，在将更改推向生产环境之前，需要在特殊的环境中进行许多测试。还有一些情况必须要有人为参与的流程。但是，在环境允许的情况下，你也可以立即将更改自动部署到集群，而无须人工干预。Argo CD遵循GitOps使得回滚至任何以前的版本（包括最后一个版本）变得非常容易。

自动同步策略不能保证应用程序始终保持同步。控制自动同步过程有一些限制：
·处于错误状态的应用程序将不会尝试自动同步。
·Argo CD将仅对特定的提交SHA和参数尝试一次自动同步。
·如果由于任何原因自动同步失败，它将不会再次尝试。
·你无法使用自动同步功能回滚应用程序。

这些情况下，你必须对清单进行更改以触发另一个自动同步或手动同步。如果要进行回滚（或通常同步到以前的版本），则必须关闭自动同步。

Argo CD提供了另一种用于修剪部署资源的策略。当现有资源在Git中不再存在时，默认情况下Argo CD不会将其删除。这是一种安全保护机制，用于避免在编辑Kubernetes清单出错的时候破坏关键资源。但是，如果你知道自己在做什么（例如，对于无状态应用程序），那么可以启用自动修剪：

### 4.6.5 探索Argo CD

·仓库是应用程序的Git代码仓库。
·路径是k8s目录中YAML实时显示的代码仓库中的相对路径（Argo CD监控此目录以进行更改）。
以下是从argocd CLI获得的信息：

单击服务并查看包括清单（MANIFEST）的信息摘要（SUMMARY）

对于Pod，我们甚至可以从Argo CD的界面上查看日志，如图4-11所示。

### 第5章 使用Kubernetes配置微服务

·配置包含的内容。
·通过传统方式管理配置。
·动态管理配置。
·使用Kubernetes配置微服务


### 5.2 配置包含的内容

在考虑配置时，非常重要的一件事情是应该由谁来创建和更新配置数据。它可能是也可能不是代码的开发人员，例如，速率限制可能由DevOps团队成员决定，特性标志由开发人员设置。此外，在不同的环境中，不同的人可能会修改相同的值。所以通常情况下，在生产环境中你需要对此做严格的限制。

配置和密钥
密钥是用于访问数据库和其他服务（内部/外部）的凭据。从技术上讲，它们是配置数据，但在实践中，由于它们的敏感性，通常需要在存储时对它们进行加密并进行更精细的控制。将密钥与常规配置分开存储和管理是很常见的。

### 5.3.4 配置文件

5.3.4　配置文件
当你具有大量配置数据时，配置文件特别有用，尤其是当这些数据具有层次结构时。在大多数情况下，通过命令行参数或环境变量为应用程序配置数十个甚至数百个选项将令人不堪重负。配置文件还有另一个优点，那就是你可以链接多个配置文件。应用程序通常会在搜索路径中查找这些配置文件，例如首先会查找/etc/conf，然后是home目录，接着是当前目录。

由于Kubernetes清单通常写为YAML格式，因此你在本书前面的章节中已经看到了许多YAML（https://yaml.org/）文件。YAML是JSON的超集，但它还提供了更加简洁易懂的语法，具有极强的可读性并提供了更多功能，例如引用、类型的自动检测以及支持对齐的多行值。

### 5.3.5 混合配置和默认

默认情况下，Kubectl会到$HOME/.kube中查找其配置文件。你可以通过KUBECONFIG环境变量指定其他文件，也可以通过传递--config命令行标志来指定特殊的配置文件。
Kubectl也使用YAML作为其配置格式。

Kubectl支持在同一个配置文件中存在多个集群/上下文，你可以通过kubectl use-context在它们之间切换。

而更愿意为每个集群使用一个单独的文件，然后使用KUBECONFIG环境变量或通过命令行传递--config在它们之间进行切换。

### 5.4 动态管理配置

重新启动服务更改配置的好处是，你不必担心新配置更改对内存中状态和进行中的请求的处理的影响，因为这是从头开始进行的。但是，不好的地方是你会丢失所有运行中的请求（除非正在优雅地关机）以及所有预热的缓存或一次性的初始化工作，这可能会带来很大影响。但是，你可以使用滚动更新和蓝绿部署的方式来减轻这种情况。

### 5.4.1 理解动态配置

动态配置意味着服务可以保持相同的代码和相同的内存状态继续运行，但是它可以检测到配置已更改，并能够根据新的配置动态调整其行为。从运维工程师的角度来看，当需要更改配置时，他们只需更新集中的配置存储，而无须强制重新启动/重新部署那些代码一点没有更改的服务。

.什么时候建议使用动态配置
动态配置在以下情况中很有用：
·如果你只有一个服务实例，那么重新启动意味着系统短暂停机。
·如果你有需要快速来回切换的功能标志。
·如果你的服务中处理初始化或进行中请求资源消耗很大。
·如果你的服务不支持高级的部署策略，例如滚动更新、蓝绿部署或金丝雀部署。
·重新部署新的配置文件时，可能会从源代码管理中获取还没有部署就绪的不相关的代码更改。

在以下情况中，最好避免动态配置：
·受监管服务，其配置更改必须经过审核和批准流程。
·关键服务，其中静态配置的低风险胜过动态配置的任何好处。
·动态配置机制不存在，其收益也不足以支撑这种机制的发展。

### 5.4.2 远程配置存储

5.4.2　远程配置存储
动态配置的选项之一是使用远程配置存储。所有服务实例都可以定期查询配置存储，检查配置是否已更改，并在有新配置时进行读取。下面是一些可作为配置存储的参考选项：
·关系型数据库（Postgres、MySQL）
·键值存储（Etcd、Redis）
·共享文件系统（NFS、EFS）


### 5.4.3 远程配置服务

5.4.3　远程配置服务
一种更高级的方法是创建专用的配置服务，该服务的目的是为所有配置需求提供一站式服务。每项服务只有权访问自己的配置，这样也很容易为每个配置更改实施控制机制。配置服务的缺点是你需要构建并维护它，如果配置不当，它也可能成为系统的单点故障（Single Point Of Failure，SPOF）。

### 5.5 使用Kubernetes配置微服务

Kubernetes在动态配置方面有一些非常巧妙的技巧，其中最具创新性的动态配置机制是ConfigMaps。

### 5.5.1 使用Kubernetes ConfigMaps

注意，由于ConfigMap被用作环境变量，因此这属于静态配置。如果要更改其中任何一项内容，则需要重新启动服务。在Kubernetes中，这可以通过以下两种方式完成：
·终止Pod（部署的副本集将创建新的Pod）。


Kubernetes提供了多种创建ConfigMap的方法：
·通过命令行中的值。
·来自一个或多个文件。
·从整个目录。
·通过直接创建ConfigMap YAML清单。


另一个常见方案是为不同的环境（开发、预生产和生产）使用不同的配置。由于在Kubernetes中每个环境通常都有自己的命名空间，因此你需要在这里发挥你的创造力。ConfigMap的作用域为它们的命名空间，这意味着，即使你跨环境的配置相同，仍然需要在每个命名空间中创建一个副本

### 5.5.2 Kubernetes自定义资源

Kubernetes是一个高度可扩展的平台。你可以将自己的资源添加到Kubernetes API，并享受其API机制的所有好处，包括对kubectl的支持来管理它们。你需要做的第一件事是定义一个自定义资源（Custom Resource Definition，CRD）。该定义将指定Kubernetes API上的端点、版本、范围、种类和用于与这种新类型的资源进行交互的名称。

你可以使用自定义资源做些什么呢？好吧，可以做的事情非常多。考虑一下，你将获得具有CLI支持和可靠的持久存储的免费CRUD API，只需创造你的对象模型，然后就可以创建、获取、列出、更新和删除任意数量的自定义资源。但这还远不止于此：你可以拥有自己的控制器来监控自定义资源，并在必要时采取措施。

那么这对配置有何帮助呢？由于自定义资源在整个集群中可用，因此你可以将其用于跨命名空间的共享配置。正如前面在动态配置部分中讨论的那样，CRD可以用作集中的远程配置服务，但是你无须自己实现任何功能。另一个选择是创建一个监控这些CRD的控制器，然后将它们自动复制到每个命名空间的适当ConfigMap中。对Kubernetes的想象力是你唯一的限制。最重要的是，对于需要管理配置的大型复杂系统，Kubernetes提供了扩展配置的工具。让我们将注意力转移到配置的另一个方面，通常它在其他系统上造成很多困难，那就是服务发现。

### 5.7 扩展阅读

·12 Factor应用程序：https://12factor.net/。

### 6.2 应用完善的安全原则

·深度防御：深度防御意味着多层冗余的防护，其目的是使攻击者很难破坏你的系统。多因子身份认证就是一个很好的例子，除了用户名和密码，你还必须输入发送到手机的一次性认证码。

通过限制仅必要的权限，可以在发生破坏时最大限度地减少损害，并有助于审计、缓解和分析事件。

·最小化攻击面：这个原则很明确。你的攻击面越小，越容易被保护。关于这点，记住以下内容：
·不要公开不需要的API。
·不要保留不使用的数据。

不要相信任何人：这是你不应该信任的部分实体列表：
·你的用户
·你的合作伙伴
·你的供应商
·你的云服务提供商
·开源开发人员
·你的开发人员
·你的管理员
·你自己
·你的安全体系

当我们说不信任时，并不意味着带着恶意。每个人都容易犯错，无心之失可能与有针对性的攻击一样有害。不要相信任何人的伟大之处在于，你不必做出判断。同样，最小信任的方法将帮助你防止和减轻错误和攻击。

考虑以下注意事项：
·不要升级到最新版本（除非是明确修复安全漏洞）。
·相对于新特性，更需要考虑稳定性。
·优先考虑简单性而不是强大。

### 6.3 区分用户账户和服务账户

对Kubernetes API服务器的每个请求都必须来自某个特定账户，API服务器会在进行处理之前对其进行认证、授权和准入。Kubernetes有两种类型的账户：
·用户账户
·服务账户

### 6.3.2 服务账户

服务账户和用户账户不同，每个Pod都有一个与之关联的服务账户，并且在此Pod中运行的所有工作负载都使用该服务账户作为其身份标识。服务账户的作用域是命名空间。在创建Pod（直接创建或者通过部署）时，可以指定服务账户。如果在未指定服务账户的情况下创建Pod，则会使用命名空间的默认服务账户。每个服务账户都有一个与之关联的密钥，可以与API服务器进行通信。

服务账户允许Pod中运行的代码与API服务器交互。
你可以从目录/var/run/secrets/kubernetes.io/serviceaccount获取令牌和CA证书，然后通过授权标头传递这些凭据来构造REST HTTP请求。

结果显示是403 forbidden，因为默认的服务账户不允许列出Pod，实际上也不允许执行任何操作。在本章关于授权的部分中，我们将看到如何向服务账户授予特权。

### 6.4.1 Kubernetes密钥的三种类型

Kubernetes密钥的三种类型
密钥分为三种：
·服务账户API令牌（用于与API服务器通信的凭据）。
·仓库密钥（用于从私有仓库获取镜像的凭据）。
·Opaque密钥（Kubernetes不知道的密钥）。

可以通过多种方式创建Opaque密钥，例如：
·从纯文本
·从文件或目录
·从一个env文件中（每一行都是一个键值对）
·创建kind为Secret的YAML清单


### 6.4.2 创造自己的密钥

密钥类型是Opaque，返回值是base64编码的。要获取原始值需要进行解码，可以使用以下命令：
￼


### 6.4.3 将密钥传递到容器

将密钥传递给容器的方法有很多种，例如：
·可以将密钥打包在容器镜像中。
·可以将它们传递给环境变量。
·可以将它们挂载为文件。

最安全的方法是将密钥文件挂载。当你将密钥打包到镜像中时，有权访问镜像的任何人都可以获取你的密钥。

### 6.5 使用RBAC管理权限

RBAC模型由身份（用户账户和服务账户）、资源（Kubernetes对象）、操作（标准动作例如获取、列出和创建）、角色和角色绑定组成。

### 6.6.1 认证

深度防御原则也指导我们需要对通信进行加密、认证和管理。这里有几种参考方法，其中最健壮的方法是构建自己的公钥基础设施（Public Key Infrastructure，PKI）和证书颁发机构（Certificate Authority，CA），它们可以随着服务实例的增减进行证书的发行、吊销和更新，这种方法实现起来比较复杂（如果使用云提供商，他们可能会为你提供这种服务）。

### 6.7.1 镜像安全

1.强制拉取最新镜像
在容器规约中，有一个镜像拉取策略ImagePullPolicy的可选项，其默认值为IfNot-Present。该默认设置存在以下几个问题：
·如果使用诸如latest（建议不要这么做）之类的标签，那么你将无法用到更新后的镜像。
·可能与同一节点上的其他租户有冲突。
·同一节点上的其他租户也可以运行你的镜像。

4.固定基础镜像的版本
固定基础镜像的版本对于确保可重复的构建至关重要。如果未指定基础镜像版本，那么将会选择最新版本，而这可能不是你想要的版本。

Alpine是非常受欢迎的基础镜像。Delinkcious应用程序将这种方法发挥到了极致，并使用SCRATCH镜像作为基础镜像。几乎整个服务都是Go可执行文件，体积小、速度快而且安全。但是，当需要解决问题时也会付出一些代价，因为没有太好的工具可以帮助到你。

### 6.7.2 网络安全——分而治之

每个Pod都有自己的IP地址（即使在具有单个IP地址的同一物理节点或VM上运行多个Pod），这是网络策略凸显作用的地方。网络策略基本上是一组规则，既可以指定Pod之间的集群内部通信（东西流量），也能够指定集群中的服务与外部之间的通信（南北流量）。如果未指定网络策略，则默认情况下，每个Pod的所有端口上都允许所有传入流量（入站）。从安全角度来看，这是不可接受的。

除了安全优势外，网络策略还可以作为实时文档来记录整个系统中的信息流。你可以准确地指出哪些服务与哪些其他服务以及外部服务通信。

### 6.7.5 使用配额最小化爆炸半径

限制（limit）和配额（quota）是一种Kubernetes机制，你可以控制分配给集群、Pod和容器的各种资源限制，例如CPU和内存。它们非常有用，体现在以下几个方面：
·性能
·容量规划
·成本管理
·它们帮助Kubernetes根据资源利用率调度Pod

·限制：限制是工作负载可以访问的资源的上限。超出其内存限制的容器可能会被杀死，并且可能会将整个Pod从节点中驱逐出去。如果容器被杀死或者Pod被驱逐，Kubernetes将重启容器，重新调度容器Pod，就像处理其他类型的故障一样。如果一个容器超过其CPU限制，它不会被杀死，甚至可能侥幸维持了一段时间，但由于CPU是更容易控制，它可能不会得到所有的CPU请求并保持在限制的资源内。

将请求指定为限制是最好的方法，就像我们为用户管理器所做的那样。工作负载知道它已经拥有了将需要的所有资源，并且不必担心同一节点上的其他饥饿邻居争夺同一资源池。

CPU请求的分辨率可以为0.001，一个更方便的方法是使用milliCPU单位，并且只使用带m后缀的整数，例如100m是0.1个CPU。


### 6.7.6 实施安全上下文

·通过用户ID和组ID（runAsUser、runAsGroup）的访问控制。
·不配置无限制的root访问。

其中有许多细节和交互超出了本书讨论的范围，下面仅分享一个SecurityContext的示例：
￼
这个安全策略执行不同的操作，例如将容器内的用户ID设置为2000，并且不允许特权升级（获取root），如下所示：

### 6.7.8 强化工具链

1.通过JWT令牌对管理员用户进行认证
Argo CD具有内置的管理员用户，所有其他用户必须使用单点登录（Single-Sign On，SSO）。对Argo CD服务器的认证始终使用JSON Web Token（JWT），管理员用户凭据也将转换为JWT。

3.通过HTTPS进行安全通信
所有Argo CD入站、出站以及其自身组件之间的所有通信均通过HTTPS/TLS完成

### 第7章 API与负载均衡器

我们还将添加一个基于gRPC的消息服务，用户可以点击该消息服务获取他们关注的用户的消息。最后，我们将添加一个消息队列，使服务以松耦合的方式进行通信。

### 7.3 东西流量与南北流量

东西流量是指服务/Pod/容器在集群内的相互通信。你可能还记得，Kubernetes通过DNS和环境变量公开了集群内的所有服务，这解决了集群内的服务发现问题。

南北流量是关于对外公开服务的通信。理论上，你可以通过NodePort公开你的服务，但这种方法受到许多问题的困扰，比如：·你必须自己处理安全/加密传输。·你无法控制哪些Pod将实际为请求提供服务。·你必须让Kubernetes为服务选择随机端口，并且需要严格管理端口冲突。·每个端口只能公开一个服务（例如，多数服务渴望使用的80端口不能重复使用）。

### 7.4 理解ingress和负载均衡器

Kubernetes中的ingress概念涉及控制对服务的访问以及其他功能，例如：·SSL终止·认证·路由到不同服务ingress资源定义了路由规则，另外还有一个ingress控制器，它读取集群中定义的所有ingress资源（跨所有命名空间）。ingress资源接收所有请求并路由到目标服务，并将它们分发到后端Pod。ingress控制器用作集群范围的软件负载均衡器和路由器。通常，在集群前端有一个硬件负载均衡器，将所有流量发送到ingress控制器。

### 7.5.1 构建基于Python的API网关服务

7.5.1　构建基于Python的API网关服务API网关服务旨在接收来自集群外部的所有请求，并将它们路由到适当的服务。下面是API网关服务的目录结构：

Docker可以通过可重复使用的层和基础镜像发挥作用，我们可以在svc/shared/docker/python_flask_grpc/Dockerfile中把所有重量级的东西放到一个单独的基础镜像中：

### 7.6.3 将NewsManager公开为gRPC服务

·高度可扩展（定制你自己的认证、授权、负载均衡和健康状况检查）。·优秀的文档。最重要的是，对于内部微服务，它几乎在每个方面都优于基于HTTP的REST API。

gRPC要求你在受协议缓冲区启发的特殊DSL中为服务定义契约，它非常直观，可以让gRPC为你生成很多样板代码。这里选择将契约和生成的代码放在称为协议缓冲区（Protocol Buffer，PB）的独立顶级目录中，因为服务和使用者会使用代码的不同部分。通常建议将共享代码放在单独的位置，而不是随意放在服务或客户端中。

gRPC模型通过protoc工具生成服务存根和客户端库。

### 8.2.1 Kubernetes存储模型

存储类是一种描述供应可用存储类型的方法。通常，在配置卷而不指定特定存储类时使用默认存储类。

持久卷也可以用于直接在同一镜像的多个实例之间共享信息，因为使用相同的持久存储声明会将相同的持久存储挂载到所有容器。

### 8.4.1 理解StatefulSet

StatefulSet的Pod具有一个稳定的标识，它包括以下三个方面：一个稳定的网络标识、一个序号索引和一个稳定的存储，这些内容总是在一起的。每个Pod的名称组成是<statefulset name>-<ordinal>。与StatefulSet关联的无头服务提供了稳定的网络标识，其服务的DNS名称如下所示：[插图]每个Pod都有一个稳定的DNS名称，如下所示：

StatefulSet中的每个Pod都获得一个序号索引，它们是做什么用的呢？某些数据存储依赖于有序的初始化序列。StatefulSet确保在Pod初始化和扩展时，始终按固定的顺序进行操作。

### 8.4.2 什么时候应该使用StatefulSet

当你在云中需要自己管理数据存储并希望能够良好地控制时，你应该使用StatefulSet。StatefulSet的主要用例是分布式数据存储，但是即使你的数据存储只有一个实例或Pod，StatefulSet也是有帮助的。

·部署没有关联的存储，而StatefulSets有。·部署没有关联的服务，而StatefulSets有。·部署的Pod没有DNS名称，而StatefulSet的Pod有。·部署不以固定顺序启动和终止Pod，而StatefulSets遵循规定的顺序（默认情况下）。

### 8.4.3 一个大型StatefulSet示例

如果Cassandra节点出现故障，那么至少还有两个其他节点具有相同的数据并且可以响应查询。所有节点都相同，没有主节点，也没有从节点。节点通过gossip协议不断地交互，当新节点加入集群时，Cassandra在所有节点之间重新分配数据。

### 8.5 通过本地存储实现高性能

让我们考虑以下两个选项：·将数据存储在内存中。·将数据存储在本地SSD硬盘上。

### 8.6.1 了解数据的存储位置

对于PostgreSQL，它有一个data目录，可以使用PGDATA环境变量设置此目录。默认情况下，它设置为/var/lib/postgresql/data：

### 8.6.3 使用StatefulSet

使用StatefulSet，情况就不同了。数据目录挂载到容器，但是存储本身是在外部进行管理的。只要外部存储可靠且冗余，无论特定容器、Pod和节点发生了什么情况，我们的数据都是安全的。

### 8.6.4 帮助用户服务找到StatefulSet Pod

这个想法的基础是StatefulSet Pod具有稳定的DNS名称。对于用户数据库，其中一个Pod的DNS名称为user-db-0.user-db.default.svc.cluster.local。

### 8.7 在Kubernetes中使用非关系型数据存储

如果大多数数据都不会访问，那将其保存在内存中将是巨大的浪费。Redis可以用作热数据的快速分布式缓存。

通过使用ConfigMap向环境变量注入Redis Pod的DNS名称：

### 9.2 云中的Serverless

Serverless的第二个含义是代码没有部署为长期运行的服务，而是打包为可以按需以不同方式调用或触发的函数，该概念的一些很好的例子包括AWSLambda和Google Cloud Functions。

### 9.2.2 在Kubernetes上的Serverless函数模型

有两种主要的方式来表示Kubernetes中的Serverless函数。第一种就是代码本身，本质上需要开发人员以某种形式提供函数（文件或者Git代码仓库）。第二种是将其构建为实际的容器，开发人员构建一个常规容器，Serverless框架负责调度并作为一个函数运行。

### 9.4.2 创建一个链接检查Serverless函数

配置的唯一定制部分是build命令，用于安装ca-certificates软件包，这里使用了Alpine Linux软件包管理器（Alpine Linux Package Manager，APK）系统。这是必需的，因为链接检查器也需要检查HTTPS链接，并且需要根CA证书：

### 9.5.5 OpenFaas

还有一个OpenFaaS Cloud项目，它使用一套完整的基于GitOps的CI/CD流水线来管理Serverless函数。与其他Serverless函数项目类似，OpenFaas也有自己的CLI和UI进行管理和部署。

### 第10章 微服务测试

软件是人类创造的最复杂的东西。大多数程序员无法编写10行代码而不会发生任何错误。现在，有了这个常识，我们再考虑编写由数十个、数百个或数千个交互组件组成的分布式系统所需的内容，这些交互组件是由大型团队使用大量第三方依赖项和大量数据驱动逻辑来设计和实现的，并且还需要相当多的配置。随着时间的推移，许多最初构建系统的架构师和工程师可能已经离开组织或者转而担任其他职务。需求也在不断变化，新技术会被引入，同时过程中可能会发现更好的实践。系统必须不断发展以适应这些变化。

### 10.2.1 使用Go进行单元测试

Go是一种现代语言，并且它充分认识到测试的重要性。Go鼓励你拥有的每个go文件（比如foo.go）都拥有对应的测试文件（比如foo_test.go）。此外，它还提供了testing测试包，并且Go工具就有一个test测试命令。

### 10.2.5 你应该测试一切吗

答案是不！测试提供了很多价值，但它也有成本，增加测试的边际价值会逐渐下降。测试一切是极其困难的

### 10.3 集成测试

集成测试是一个包含多个相互交互的组件的测试。集成测试意味着测试完整的子系统，而不需要或很少进行模拟。

这些程序旨在测试跨服务的交互以及服务如何与第三方组件（例如实际的数据存储）集成。

### 10.6 端到端测试

端到端测试通常在专用环境（如预生产环境）上运行，但在某些情况下，它们也在生产环境上运行（需要投入大量精力）。由于端到端测试通常需要运行很长时间，而且设置起来可能很慢、开销很大，所以每次提交都运行它们是不太可取的。相反，定期（每晚、每个周末或每个月）或在特别时间点（例如，在重要的发布之前）运行它们是很常见的。

端到端测试有几种类型，我们将在下面的几个小节中探讨一些最重要的类别，例如：·验收测试·回归测试·性能测试

### 10.6.2 回归测试

当你只想确保新系统不会偏离当前系统的行为时，回归测试是一个不错的选择。如果你有全面的验收测试，那么你只需确保新版本的系统通过所有的验收测试，就像以前的版本所做的那样。但是，如果你的验收测试覆盖率不足，可以通过使用相同的输入对当前系统和新系统进行轮番轰炸式的测试，并验证输出是否相同，从而获得某种程度的信心。这也可以通过模糊测试来实现，即生成随机输入。

### 10.8 小结

我们讨论了测试的主题及其各种风格：单元测试、集成测试和各种端到端测试

测试也是有成本的，一味盲目地添加越来越多的测试并不能使你的系统变得更好或者具有更高的质量。在测试的数量和质量之间有许多重要的权衡，例如开发和维护测试所需的时间、运行测试所需的时间和资源，以及测试早期发现的问题的数量和复杂性。

### 11.2 Kubernetes部署

Deployment对象封装了部署的概念，包括部署策略和部署记录。它还提供了面向部署的操作，例如更新部署和回滚部署

### 11.4 理解部署策略

当且仅当部署约定中的Pod模板更改时，才会部署一组新的Pod。当你更改Pod模板的镜像版本或容器的标签时，通常会发生这种情况。

### 11.4.1 重新部署

另一种情况是，如果你想更改服务的公共API或者它的依赖项不能实现向后兼容。在这种情况下，当前的Pod必须全部终止，并且必须部署新的Pod。对于不兼容更改的多阶段部署有一些解决方案，但是在某些情况下，切断连接并支付短时间中断的成本更容易被接受。要启用此策略，请编辑部署的清单，将策略类型更改为Recreate，然后删除rollingUpdate部分（仅当类型为RollingUpdate时才使用）

### 11.4.2 滚动更新

Kubernetes的默认部署策略就是RollingUpdate（滚动更新）：

当新版本与当前版本兼容时，滚动更新才有意义。准备好处理请求的活动Pod的数量保持在你使用maxSurge和maxUnavailable指定的副本数的合理范围内，然后逐渐将所有旧的Pod替换为新的Pod，整体服务没有中断。

但是，有时你必须立即更换所有Pod，对于必须保持可用的关键服务，Recreate策略又不起作用。这就是蓝绿部署可以发挥作用的地方了。

### 11.4.3 蓝绿部署

蓝绿部署是一种常见的部署模式。它不更新现有部署，而是使用新版本创建一个全新的部署。最初新版本不接收流量，然后，当确认新部署已经启动并运行（甚至可以对其进行运行冒烟测试）时，只需将所有流量从当前版本切换到新版本。如果切换到新版本后遇到任何问题，则可以立即将所有流量切换回以前的部署，该部署仍在运行。如果确信新部署的运行良好，则可以销毁先前的部署。

1）添加deployment:blue标签到当前的link-manager部署中。2）更新link-manager服务选择器以匹配deployment:blue标签。3）实现新版本的LinkManager，该版本以[green]字符串为每个链接的描述添加前缀。4）添加deployment:green标签到部署的Pod模板规约中。5）修改版本号。

### 11.4.4 金丝雀部署

金丝雀部署是另一种复杂的部署模式，考虑到了具有大量用户的大型分布式系统的情况。你想引入该服务的新版本，并且已尽力测试了新的更改，但是生产系统过于复杂，无法在预生产环境中完全模仿，不能确定新版本不会引起一些问题。这时候应当使用金丝雀部署，其思想是必须在生产环境中对某些更改进行测试，然后才能合理地确定它们可以按预期进行。金丝雀部署模式可以在新版本出现问题时限制可能造成的损害范围。

但是，基础金丝雀部署有几个限制：·粒度为K/N（极端的情况是单例，其中N=1）。·无法控制对同一服务的不同请求的不同百分比（例如，仅对读请求的金丝雀部署）。·无法控制对同一用户的所有请求（使用相同版本）。

4）更新服务以选择标签svc:link和app:manager（忽略deployment:<color>）。5）针对该服务执行多次查询，并验证金丝雀部署所服务的请求比率是否为10%!。(MISSING)

### 11.5.3 回滚金丝雀部署

金丝雀部署的回滚可以说更加容易。大多数Pod都运行了经过验证的真实版本，金丝雀部署的Pod只处理少量请求。如果你发现金丝雀部署有问题，只需删除该部署，你的主要部署将继续处理传入的请求。如有必要（例如，你的金丝雀部署提供了少量但可观的流量），则可以扩展主要部署以弥补金丝雀部署Pod的缺失。

### 11.5.4 回滚模式、API或负载的更改

解决这些兼容性问题的方法是在多个部署中执行那些更改，其中每个部署都与先前的部署完全兼容，这需要一些计划和工作。

包含模式更改的更新非常复杂。管理它的方法是执行多阶段升级，其中每个阶段都与上一个阶段兼容。仅当你证明当前阶段可以正常工作时，才可以继续前进。每个微服务拥有自己的数据存储的好处在于，至少数据库模式更改被限制在单个服务中，并且不需要跨多个服务进行协调。

### 11.6.3 管理第三方依赖

系统通常存在三种第三方依赖：·构建软件需要的库和包（如第2章中所述）。·代码通过API访问的第三方服务。·用于维护和运行系统的服务。

选择第三方依赖项时，你放弃了某些（或很多）控制权。你应始终考虑当第三方依赖项突然变得不可用或不可接受时，会发生什么情况，出现这种情况的原因有很多：·开源项目被放弃或失去动力。·第三方服务提供商关闭。·类库有太多安全漏洞。·服务有太多宕机。

### 11.7.1 Ko

Ko提供了指定基础镜像并将静态数据包括在生成的镜像中的方法。

### 11.7.2 Ksync

Ksync是一个非常有趣的工具，它根本不构建镜像，而是直接在本地目录和集群中正在运行的容器内的远程目录之间同步文件。没有比这再精简的了，特别是同步到本地Minikube集群。不过，它的简单是有代价的。对于使用动态语言（例如Python和Node）实现的服务，Ksync尤其适用，这些动态语言可以在同步更改后重新加载应用程序。如果你的应用程序不能热重载，则Ksync可以在每次更改后重新启动容器。让我们开始体验下吧：

总结一下，Ksync是非常精简和快速的，它不需要构建镜像并将它们推送到镜像仓库，然后再部署到集群。如果你所有的工作负载都使用动态语言，那么使用Ksync无疑是很容易的。

### 11.7.5 Tilt

Tilt的特殊之处在于，它不仅可以自动构建镜像并将其部署到集群中或同步文件，还可以提供一个完整的实时开发环境，该环境可提供大量信息、高亮显示事件和错误，并使你能够深入了解集群中正在发生的事情。

### 第12章 监控、日志和指标

我们将重点介绍在Kubernetes上运行大型分布式系统的运维、如何设计系统以及应考虑哪些因素，以确保一流的运维状态。系统总是会发生故障，你必须准备好进行检测、排除故障并尽快做出响应。Kubernetes提供了一些开箱即用的最佳实践：
·自愈
·自动扩展
·资源管理

监控就是了解系统的最新情况。监控的信息源通常有以下几种：
·日志：在应用程序代码中明确记录相关日志信息（使用的库也可能会记录日志）。
·指标：收集有关系统的详细信息，例如CPU、内存、磁盘使用率、磁盘I/O、网络和自定义应用程序指标。
·跟踪：给请求附加ID以在多个微服务之间跟踪。

### 12.1 技术需求

·Prometheus：指标监控和警报解决方案。
·Fluentd：集中式日志代理。
·Jaeger：分布式跟踪系统。

### 12.2 Kubernetes的自愈能力

大多数自动化解决方案都是从手动流程开始的，随着重复手动干预的成本越来越明显，这些流程就会逐渐实现自动化。如果某些问题仅是小概率事件，那么可以通过人工干预的方式解决这些问题。

### 12.2.1 容器故障

我们可以看到API网关进入了CrashLoopBackOff状态。这意味着它不断失败，而Kubernetes也不断地重启它。回退部分是重启尝试之间的延迟，Kubernetes使用指数回退延迟，从10秒开始，之后每次都加倍，直到最大延迟5分钟

### 12.2.2 节点故障

当一个节点发生故障时，该节点上的所有Pod将变得不可用，并且Kubernetes将调度它们到集群中的其他节点上运行。假设你设计的系统具有适当的冗余性，并且发生故障的节点不是一个单点故障，那么系统应该能够自动恢复。

### 12.2.3 系统故障

系统也会发生各种故障，其中一些故障包括：
·整体网络故障（整个集群无法访问）
·数据中心中断
·可用区中断
·地区中断
·云提供商中断

如果对于你的组织而言，不惜一切代价保持在线状态最重要，那么Kubernetes会为你提供选择。这项工作是在federation v2项目下进行的（不推荐使用v1，因为它遇到了太多问题。）
你将能够在不同的数据中心、不同的可用区、不同的地区甚至不同的云提供商中建立完整的Kubernetes集群，甚至是一组集群。你将能够将那些物理上分布的集群作为单个逻辑集群运行和管理，并希望在这些集群之间无缝地进行故障转移。

### 12.3.1 Pod水平自动伸缩

Pod水平自动伸缩（Horizontal Pod Autoscaler，HPA）是一种控制器，旨在调整部署中Pod的数量，以匹配这些Pod上的负载。是否应按比例扩大（添加容器）或缩小（删除容器）不熟是基于指标的。开箱即用的Pod水平自动伸缩支持CPU利用率指标，也可以添加自定义指标。

部署了消耗CPU的代码后，CPU利用率提高了，并且创建了越来越多的Pod，直到最大值5个。图12-3是Kubernetes仪表板的截图，显示了CPU、Pod和Pod水平自动伸缩的信息。
让我们查看一下Pod水平自动伸缩本身：

Pod水平自动伸缩是一种通用机制，很久以来就是Kubernetes的一部分。它仅依赖于内部组件来收集指标，我们在此演示了默认的基于CPU的自动伸缩，但也可以配置为基于多个自定义指标来工作。现在，我们来查看下其他自动伸缩方法。

### 12.3.2 集群自动伸缩

调整集群大小的触发条件是Kubernetes由于资源不足而无法调度Pod，这与Pod水平自动伸缩配合使用时效果更好，它们结合在一起，可以为你提供真正具有弹性的Kubernetes集群，可以自动（在一定的范围内）增长和收缩以匹配当前负载。

并会在决定增加或缩小集群时将它们考虑在内：
·Pod中断预算。
·总体资源限制。
·亲和性和反亲和性。
·Pod优先级和优先权。

特别是，它不会删除具有以下一个或多个属性的节点：
·使用本地存储。
·包含"cluster-autoscaler.kubernetes.io/scale-down-disabled":"true"注释。
·主机上Pod包含"cluster-autoscaler.kubernetes.io/safe-to-evict":"false"注释。
·具有严格PodDisruptionBudget的节点。

添加节点的总时间通常少于5分钟。集群自动伸缩每十秒钟扫描一次未调度的Pod，并在必要时立即置备新节点。但是，云提供商需要3至4分钟才能提供该节点并将其附加到集群。

我们继续查看另一种自动伸缩形式：Pod垂直自动伸缩。

### 12.3.3 Pod垂直自动伸缩

12.3.3　Pod垂直自动伸缩
垂直Pod自动伸缩（Vertical Pod Autoscaler，VPA）在Kubernetes 1.15处于Beta阶段，它承担了与自动伸缩有关的另一项任务：微调CPU和内存请求。


Pod垂直自动伸缩可在多种模式下工作：
·Initial：在创建容器时分配资源请求。
·Auto：在创建容器时分配资源请求，并在容器的生命周期内更新资源请求。
·Recreate：类似于Auto，当需要更新其资源请求时，Pod需要重启。
·UpdatedOff：不修改资源请求，但可以查看建议。

### 12.4 使用Kubernetes供应资源

传统上，供应资源是运维或系统管理员的工作。但是，使用DevOps方法时，开发人员通常要承担自供应的任务。如果组织具有传统的IT部门，则他们通常会更加关注开发人员应具有的供应权限以及应设置的全局限制。在

### 12.4.1 应该提供哪些资源

像kubectl run和kubectl scale这样的命令对于集群的交互式探索和运行临时任务很有用，但它们与声明式基础设施即代码的方法有些背道而驰。

### 12.5.3 性能和成本

在这种情况下，你浪费了本来可以用于开发更有用的产品的时间，并且有可能破坏代码的稳定性、引入错误以及使代码变得难以理解。同样，良好的指标和性能分析将帮助确定系统中值得在性能和成本方面进行改进的热点。

### 12.6 日志

日志是在系统运行期间记录消息的能力。日志消息通常具有结构性和时间戳，在尝试诊断问题并对系统进行故障排除时，它们通常是必不可少的。在进行故障分析和根本原因分析时，它们也至关重要。在大型分布式系统中记录日志的组件很多。收集、组织和筛选它们并非易事，但是首先，让我们考虑一下记录哪些信息有用。

### 12.6.1 日志应该记录什么

一个好的起点是记录微服务之间以及微服务和第三方服务之间的所有传入请求和交互。

### 12.6.2 日志与错误报告

你可以像记录其他任何信息一样记录错误，但是将错误记录到专门的错误报告服务（如Rollbar或Sentry）中通常是值得的。错误的关键信息之一就是堆栈跟踪，其中包括堆栈中每个帧的状态（局部变量）。对于生产系统，除记录日志外，我建议你使用专门的错误报告服务。

### 12.6.5 使用Kubernetes集中管理日志

这就需要集中管理日志，其想法是运行日志代理，既可以作为每个Pod中的Sidecar容器，也可以作为每个节点上设置的守护程序运行，监听所有日志，然后将它们实时发送到一个集中位置，以便对它们进行聚合、过滤和排序。当然，也可以直接在容器中发送日志到集中式日志服务。
最简单、最可靠的方法是使用DaemonSet。集群管理员确保在每个节点上都安装了日志代理。无须更改你的Pod规约即可注入Sidecar容器，无须依赖特殊的库即可与远程日志服务进行通信，你的代码只需写入标准输出和标准错误就可以。

在Kubernetes上最受欢迎的日志代理之一是Fluentd（https://www.fluentd.org），它也是CNCF的毕业项目。除非有更充分的理由使用其他日志代理，否则建议你使用Fluentd。图12-4说明了Fluentd如何作为DaemonSet部署到Kubernetes中，并将其部署到每个节点中，拉取所有Pod的日志并将其发送到集中式日志管理系统。

### 12.7.3 使用Prometheus

·通过Prometheus节点导出器收集内核和操作系统指标。
·通过kube-state-metrics收集Kubernetes对象状态的各种指标。

### 12.8 警报

这都是定义服务级别目标（Service-Level Objective，SLO）和服务级别协议（Service-Level Agreement，SLA）的一部分

### 12.8.1 拥抱组件故障

拥抱故障意味着认识到组件将在大型系统中始终出现故障，这种情况并不罕见。你希望尽可能减少组件故障，因为即使整个系统继续运行，每次故障都会带来各种损失。通过配备适当的冗余，大多数组件故障可以自动处理，或者情况变得不那么紧急。但是，系统一直在发展，并且大多数系统都不是每个组件故障都可以缓解。

### 12.8.2 接受系统故障

因此，系统故障将会发生。即使是最大的云提供商，也会时不时地出现故障。有不同程度的系统故障，从一个非关键子系统的暂时性故障到整个系统的长时间停机，一直到大量数据丢失。

处理系统故障的常用方法是冗余、备份和隔离。这些都是可靠的方法，但是都很昂贵，而且正如我们前面提到的那样，不能防止所有故障。在最大限度地降低系统故障的可能性和影响之后，下一步就是计划快速的灾难恢复。

### 12.8.3 考虑人为因素

最好尽早发现问题（例如75%!）(MISSING)并通过某种机制发出警告。这将使系统运维人员有足够的时间进行响应，而不会引起不必要的危机。

不同的严重级别应得到不同的响应，不同的组织可以定义自己的级别。例如，PagerDuty使用1到5的严重级别。我个人更喜欢警报的两个级别：凌晨3点叫我起床和可以等到早晨。我喜欢从实际角度考虑严重程度。对每个严重级别执行什么样的响应或跟进？如果你始终对严重性级别3到5执行相同的操作，那么将它们分类有什么意义呢？

4.处理噪音警报
警报噪音是个问题。如果警报太多（尤其是低优先级警报），则存在两个主要问题：
·这会分散所有收到通知的人的注意力（尤其是可怜的工程师在半夜醒来）。
·这可能会导致人们忽略警报。

### 12.8.4 使用Prometheus警报管理器

Prometheus除了是出色的指标收集器之外，还提供了警报管理器

### 12.9 分布式跟踪

Jaeger可以帮助你解决以下问题：
·分布式事务监控
·性能和延迟优化
·根本原因分析
·服务依赖性分析
·分布式上下文传播

### 第13章 服务网格与Istio

介绍Istio。这是一个令人兴奋的话题，因为服务网格是一个真正的游戏规则改变者。它们将许多复杂的任务从服务中转移到独立的代理中，这是一个巨大的进步与成功，特别是在多语言环境中（不同的服务是用不同的编程语言实现的）

### 13.2.3 使用服务网格管理微服务的横切关注点

服务网格的一些职责如下：
·通过重试和自动故障转移在服务之间可靠地传递请求。
·延迟感知的负载均衡。
·根据灵活和动态的路由规则路由请求（也称为流量整形）。
·断路器截止时间。
·服务到服务的认证和授权。
·报告指标并支持分布式跟踪。

### 13.2.4 理解Kubernetes与服务网格之间的关系

服务网格视为Kubernetes的补充。Kubernetes主要负责Pod的调度并为其提供平面网络模型和服务发现，因此不同的Pod和服务可以相互通信，然后服务网格开始接管并以更精细的方式管理服务间通信。

### 13.3 Istio

在Kubernetes中，每个Envoy代理作为边车（Sidecar）容器注入网格内的每个Pod中。

### 13.3.1 了解Istio架构

2.Pilot
Pilot负责平台无关的服务发现、动态负载均衡和路由。它将高级路由规则和弹性设置从该平台自己的规则API转换为Envoy配置。

以下Citadel在Kubernetes中的工作流程：
·Citadel为现有服务账户创建证书和密钥对。
·Citadel监控Kubernetes API服务器中是否有新服务账户，以向证书提供密钥对。
·Citadel将证书和密钥存储为Kubernetes密钥。

### 13.3.2 使用Istio管理流量

Istio提供了很多处理故障的机制，包括以下几种：
·超时
·重试（包括抖动和回退）
·限速
·健康检查
·断路器
所有这些都可以通过Istio CRD进行配置

### 13.3.3 使用Istio保护集群

Istio使用Kubernetes的服务账户来表示身份。Istio使用其PKI（通过Citadel）为它管理的每个Pod创建一个强大的加密身份，并为每个服务账户创建一个x.509证书（采用SPIFEE格式）和一个密钥对，并将它们作为密钥注入到Pod中。

Istio可以通过mTLS提供对等身份认证，也可以通过JWT提供源认证。你可以通过peers部分配置对等身份认证，如下

Istio通过扩展Kubernetes基于角色的访问控制（Role-Based Access Control，RBAC）来支持此功能，Kubernetes用它来授权对API服务器的请求。

### 13.3.5 使用Istio收集指标

Istio在处理每个请求之后会收集指标，指标将发送到Mixer。Envoy是指标的主要生产者，但你也可以根据需要添加自定义的指标。指标的配置模型基于多个Istio概念：属性、实例、模板、处理程序、规则和Mixer适配器。

### 13.3.6 什么时候应该避免使用Istio

·代理会增加延迟并消耗CPU和内存资源。

### 13.4.1 简化服务间的认证

使用Istio，我们可以将其完全转化为角色和角色绑定。下面是一个允许你调用/following端点的GET方法的角色：

### 13.4.2 优化金丝雀部署

Istio利用子集概念完美地解决了流量整形问题。下面的示例虚拟服务将流量分成一个v0.5子集和另一个canary子集，它们以90/10的比例进行分配：

### 13.5.2 Envoy

Istio的数据平面是Envoy，它负责所有繁重的工作。你可能会发现Istio控制平面过于复杂，很多人宁愿去掉这一层并构建自己的控制平面以直接与Envoy进行交互。这在某些特殊情况下很有用，例如，当你要使用Envoy提供的但Istio不支持的负载均衡算法时。


### 第14章 微服务和Kubernetes的未来

对我们的世界产生更大的影响，你可以想象一下自动驾驶汽车和无处不在的机器人。然而，人们无法扩展处理复杂事务的能力。这意味着我们将必须采用分而治之的方法来构建那些复杂的软件系统。也就是说，基于微服务的架构将继续取代单体架构。然后，新的挑战将转移到如何协调所有这些微服务成为一个连贯的整体。这就是Kubernetes作为标准编排解决方案发挥作用的地方。

### 14.1.1 微服务与无服务器函数

无服务器函数可以带来诸多好处，但它也有一些严重的限制，比如冷启动问题和执行时间限制问题。

### 14.1.3 gRPC和gRPC-Web

gRPC又击败了REST。gRPC提供了一个基于契约的模型，该模型具有强类型、高效的基于protobuf的有效负载，并且能够自动生成客户端代码，这个组合可以说非常强大。REST最后的避难所是它的通用性，以及在浏览器中运行的Web应用程序调用带有JSON有效负载的REST API的便利性。

gRPC-web是一个成熟的JavaScript库，可以让Web应用程序简单地调用gRPC服务

### 14.1.4 GraphQL

GraphQL还解决了传统REST API中令人畏惧的N+1问题，即首先从REST端点获取一个包含N个资源的列表，然后必须再进行N次调用（每个资源一次），以获取列表中N个相关资源。

### 14.1.5 HTTP/3

2015年，HTTP/2发布，基于Google的SPDY协议，所有主流浏览器都增加了对它的支持。
gRPC建立在HTTP/2的基础上，它修复了HTTP之前版本的很多问题，并提供了以下特性：
·二进制帧和压缩
·多路复用（同一TCP连接上的多个请求）
·更好的流量控制
·服务器推送

而HTTP/3是基于QUIC协议，它是一种可靠版本的UDP传输协议。

广泛采用HTTP/3可能还需要一段时间，因为许多企业在他们的网络上阻止或限制UDP。然而，它的优势是非常吸引人的，而且与REST API相比，基于HTTP/3的gRPC在性能上拥有更大的优势。

### 14.2.1 Kubernetes的可扩展性

Kubernetes始终被设计为一个可扩展的平台。

为了简化云提供商的工作，Kubernetes引入了云控制器管理器（Cloud Controller Manager，CCM）。CCM通过一组稳定的接口抽象出云提供商需要实现的所有内容。现在，Kubernetes和云提供商之间的对接点已经正式形成，以后集成将会变得更加简单。

### 14.2.6 使用Operator

Kubernetes Operator是一种控制器，它封装了某些应用程序的操作知识。它可以管理安装、配置、更新、故障转移等。Operator通常依赖CRD来保持自己的状态，并可以自动响应事件。提供对应的Kubernetes Operator正迅速成为发布新的、复杂的软件的方式。

OperatorHub（https://operatorhub.io/）是Kubernetes Operator的索引，你可以在其中找到打包好的软件并将它们安装到集群中。OperatorHub由RedHat（现在是IBM的一部分）、亚马逊、微软和Google创立。内容按照类别和提供者组织，用户可以很方便地搜索，图14-3为OperatorHub的界面。

Operator非常有用，但是这需要非常了解Kubernetes的工作原理、控制器、Reconciliation机制、如何创建CRD以及如何与Kubernetes API服务器交互的知识。这不是特别复杂，但也没那么简单。如果你想开发自己的Operator，可以参考一个名为Operator Framework（https://github.com/operator-framework）的项目。Operator Framework提供了一个SDK，使你可以轻松地构建你自己的Operator。

### 14.2.7 集群联邦

许多第三方Kubernetes解决方案提供跨云甚至裸机的多集群管理。这一领域前景最好的项目之一是Gardener（https://gardener.cloud/），它允许你管理数千个集群。它通过创建一个garden cluster集群来管理seed clusters集群（作为自定义资源），这些seed clusters集群可以包含一些shoot clusters集群。
