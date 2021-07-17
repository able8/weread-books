## Jenkins 2.x实践指南
> 翟志军编著

### 内容简介

在Jenkins pipeline中如何实现持续集成、持续交付的各个阶段，包括构建、测试、制品管理、部署等，以及当现有pipeline的步骤不能满足需求时，扩展Jenkins pipeline的多种方式。最后介绍Jenkins如何整合多个第三方系统，以实现ChatOps及自动化运维；

希望通过Jenkins实现持续集成、持续交付、DevOps，以提升团队生产力的技术人员和管理人员。

### 1.1 从另一个角度看“提高软件工程生产力”

• 生产工具：是电脑太慢了？是编译工具本身太慢了？
• 劳动力（程序员的能力）：是构建逻辑写得不合理？是编译过程中的某个阶段的问题影响了整体编译速度？
• 劳动对象：是不是缺少对当前构建工具（技术知识）的了解？

### 1.2 Jenkins介绍

Jenkins是一款使用Java语言开发的开源的自动化服务器。我们通过界面或Jenkinsfile告诉它执行什么任务，何时执行。理论上，我们可以让它执行任何任务，但是通常只应用于持续集成和持续交付。

使用Jenkins能提升软件工程生产力的根本原因就在于它提供的是一个自动化平台。一个团队引入了Jenkins就像原来手工作坊式的工厂引入了生产流水线。由于知识的特殊性，它还能帮助我们将知识固化到自动化流水线中，在一定程度上解决了知识被人带走的问题。

### 1.3 Jenkins与DevOps

DevOps集文化理念、实践和工具于一身，可以提高组织高速交付应用程序和服务的能力，与使用传统软件开发和基础设施管理流程相比，能够帮助组织更快地发展和改进产品。这种速度使组织能够更好地服务于客户，并在市场上更高效地参与竞争。

DevOps（Development和Operations的组合）是一种重视软件开发人员（Dev）和IT运维技术人员（Ops）之间沟通合作的文化、运动或惯例。通过自动化软件交付和架构变更的流程，使得构建、测试、发布软件能够更加快捷、频繁和可靠。

### 2 pipeline入门

从某种抽象层次上讲，部署流水线（Deployment pipeline）是指从软件版本控制库到用户手中这一过程的自动化表现形式。——《持续交付——发布可靠软件的系统方法》[1]（下称《持续交付》）

Jenkins 1.x只能通过界面手动操作来“描述”部署流水线。Jenkins 2.x终于支持pipeline as code了，可以通过“代码”来描述部署流水线。
使用“代码”而不是UI的意义在于：
• 更好地版本化：将pipeline提交到软件版本库中进行版本控制。
• 更好地协作：pipeline的每次修改对所有人都是可见的。除此之外，还可以对pipeline进行代码审查。
• 更好的重用性：手动操作没法重用，但是代码可以重用。
本书全面拥抱pipeline as code，放弃依赖手动操作的自由风格的项目（FreeStyle project）。

### 2.1 pipeline是什么

部署流水线（Deployment pipeline）是指从软件版本控制库到用户手中这一过程的自动化表现形式

### 2.2 Jenkinsfile又是什么

Jenkinsfile就是一个文本文件，也就是部署流水线概念在Jenkins中的表现形式。像Dockerfile之于Docker。所有部署流水线的逻辑都写在Jenkinsfile中。
Jenkins默认是不支持Jenkinsfile的。我们需要安装pipeline插件，

### 2.3 pipeline语法的选择

Jenkins团队在一开始实现Jenkins pipeline时，Groovy语言被选择作为基础来实现pipeline。所以，在写脚本式pipeline时，很像是（其实就是）在写Groovy代码。这样的确为用户提供了巨大的灵活性和可扩展性

以上写法被称为脚本式（Scripted）语法。Jenkins pipeline还支持另一种语法：声明式（Declar-ative）语法。

脚本式语法的确灵活、可扩展，但是也意味着更复杂。再者，Groovy语言的学习成本对于（不使用Groovy的）开发团队来说通常是不必要的。所以才有了声明式语法，一种提供更简单、更结构化（more opinionated）的语法。示例如下：

本书所有的示例都将使用声明式语法。因为声明式语法更符合人类的阅读习惯、更简单。声明式语法也是Jenkins社区推荐的语法。

### 2.5 从版本控制库拉取pipeline

接下来，我们让Jenkins从Git仓库拉取pipeline并执行。
首先需要安装Git插件，然后使用SSH的clone方式拉取代码。所以，需要将Git私钥放到Jenkins上，这样Jenkins才有权限从Git仓库拉取代码。

将Git私钥放到Jenkins上的方法是：进入Jenkins→Credentials→System→Global credentials页，然后选择Kind为“SSH Username with private key”，接下来按照提示设置就好了，如图2-5所示。关于Credential的更多内容，我们会在第9章中进行详细介绍。目前只需要理解：Jenkins从Git仓库拉取代码时，需要SSH key就可以了，然后Jenkins本身提供了这种方式让我们设置。

### 2.6 使用Maven构建Java应用

Maven是非常流行的一个Java应用构建工具。下面我们再来看一个使用Maven构建Java应用的例子。Jenkins默认支持Maven。

接下来，需要在Jenkins上安装JDK和Maven。我们可以登录Jenkins服务器手动安装，也可以让Jenkins自动安装。这里选择后者。方法如下：
（1）进入Manage Jenkins→Global Tool Configuration→Maven页，设置如图2-7所示。

### 2.7 本章小结

Jenkins pipeline支持两种语法。node为根节点的是脚本式语法，而pipeline为根节点的是声明式语法。本书使用的是Jenkins社区推荐的声明式语法。

### 3 pipeline语法讲解

• 支持单引号、双引号。双引号支持插值，单引号不支持。比如：


• 支持三引号。三引号分为三单引号和三双引号。它们都支持换行，区别在于只有三双引号支持插值

### 3.2 pipeline的组成

Jenkins pipeline其实就是基于Groovy语言实现的一种DSL（领域特定语言），用于描述整条流水线是如何进行的。流水线的内容包括执行编译、打包、测试、输出测试报告等步骤。

 pipeline：代表整条流水线，包含整条流水线的逻辑。
• stage部分：阶段，代表流水线的阶段。每个阶段都必须有名称。本例中，build就是此阶段的名称。

• stages部分：流水线中多个stage的容器。stages部分至少包含一个stage。

• agent部分：指定流水线的执行位置（Jenkins agent）。流水线中的每个阶段都必须在某个地方（物理机、虚拟机或Docker容器）执行，agent部分即指定具体在哪里执行。我们会在第14章中进行详细介绍。

### 3.3 post部分

• fixed：上一次完成状态为失败或不稳定（unstable），当前完成状态为成功时执行。
• regression：上一次完成状态为成功，当前完成状态为失败、不稳定或中止（aborted）时执行。
• aborted：当前执行结果是中止状态时（一般为人为中止）执行。
• failure：当前完成状态为失败时执行。
• success：当前完成状态为成功时执行。
• unstable：当前完成状态为不稳定时执行。

### 3.4 pipeline支持的指令

• input：定义在stage部分，会暂停pipeline，提示你输入内容。

• parallel：并行执行多个step。在pipeline插件1.2版本后，parallel开始支持对多个阶段进行并行执行。
• parameters：与input不同，parameters是执行pipeline前传入的一些参数。
• triggers：用于定义执行pipeline的触发器。
• when：当满足when定义的条件时，阶段才执行。

### 3.6 在声明式pipeline中使用脚本

如果在script步骤中写了大量的逻辑，则说明你应该把这些逻辑拆分到不同的阶段，或者放到共享库中。共享库是一种扩展Jenkins pipeline的技术，我们会在后面的章节中讲到。

### 3.7 pipeline内置基础步骤

dir：切换到目录
默认pipeline工作在工作空间目录下，dir步骤可以让我们切换到其他目录。使用方法如下：


### 4.2 构建工具

构建是指将源码转换成一个可使用的二进制程序的过程。这个过程可以包括但不限于这几个环节：下载依赖、编译、打包。

虽然Jenkins只负责执行构建工具提供的命令，本身没有实现任何构建功能，但是它提供了构建工具的自动安装功能。

### 5 代码质量

静态代码分析是指在不运行程序的前提下，对源代码进行分析或检查，范围包括代码风格、可能出现的空指针、代码块大小、重复的代码等。

### 5.4 SonarQube：持续代码质量检查

SonarQube是一个代码质量管理工具，能对20多种编程语言源码进行代码味道（Code Smells）、Bug、安全漏洞方面的静态分析。SonarQube有4个版本：开源版、开发者版、企业版、数据中心版（Data Center Edition）。各

### 6.2 时间触发

tigger指令只能被定义在pipeline块下，Jenkins内置支持cron、pollSCM，upstream三种方式。

### 6.3 事件触发

由GitLab主动通知进行构建的好处是显而易见的，这样很容易就解决了我们之前提到的轮询代码仓库时“多久轮询一次”的问题，实现每一次代码的变化都对应一次构建。

### 6.4 将构建状态信息推送到GitLab

当Jenkins执行完构建后，我们还可以将构建结果推送到GitLab的相应commit记录上，这样就可以将构建状态与commit关联起来

提交代码后，需要手动触发一次构建，pipeline才会生效。
笔者做了一次成功构建、一次失败构建的实验。在GitLab上的项目的commit列表中，显示了最近两次commit的构建状态，如

### 9.3 创建凭证

• Scope：凭证的作用域。有两种作用域：
◦ Global，全局作用域。如果凭证用于pipeline，则使用此种作用域。
◦ System，如果凭证用于Jenkins本身的系统管理，例如电子邮件身份验证、代理连接等，则使用此种作用域。

### 9.4 常用凭证

Secret file指需要保密的文本文件。添加方法如图9-5所示。使用Secret file时，Jenkins会将文件复制到一个临时目录中，再将文件路径设置到一个变量中。构建结束后，所复制的Secret file会被删除。

### 9.5 优雅地使用凭证

通过credentials helper方法，我们可以像使用环境变量一样使用凭证。
但遗憾的是，credentials helper方法只支持Secret text、Username with password、Secret file三种凭证。

### 9.6 使用HashiCorp Vault

HashiCorp Vault是一款对敏感信息进行存储，并进行访问控制的工具。敏感信息指的是密码、token、密钥等。它不仅可以存储敏感信息，还具有滚动更新、审计等功能。


### 9.7 在Jenkins日志中隐藏敏感信息

如果使用的是credentials helper方法或者withCredentials步骤为变量赋值的，那么这个变量的值是不会被明文打印到Jenkins日志中的。除非使用以下方法：

### 10 制品管理

An artifact is one of many kinds of tangible by-products produced during the development of software.
换句话说，制品是软件开发过程中产生的多种有形副产品之一。另外，直译artifact，是人工制品的意思。所以，广义的制品还包括用例、UML图、设计文档等。

而狭义的制品就可以简单地理解为二进制包。虽然有些代码是不需要编译就可以执行的，但是我们还是习惯于将这些可执行文件的集合称为二进制包。
本章讨论的是狭义的制品。行业内有时也将制品称为产出物或工件。


### 10.2 制品管理仓库

制品管理涉及两件事情：一是如何将制品放到制品库中；二是如何从制品库中取出制品。由于每种制品的使用方式不一样，因此下面我们分别进行介绍。

### 12 自动化部署

部署就是将应用服务软件“放”在远程服务器上，但是并不代表真正的用户可以看到这些新功能。当用户能看到这些新功能时，才代表发布了新功能。

进而你可以解释如何做到部署，但是不发布：通过一些技术，即使把最新的应用服务软件“放”到服务器上，但是用户也看不到这些功能。这些技术就像是开关一样，能在后台控制开和关。只要打开某个功能的“开关”，这个功能就可以呈现给用户。
对于部署与发布的理解达成共识，有助于团队成员之间的和谐。

### 12.2 Jenkins集成Ansible实现自动化部署

Ansible是导演，受控机器列表（inventory）为演员列表，开发者则是编剧。开发者只要把剧本（playbook.yml）写好，Ansible拿着剧本与invenstory一对上号，演员就会按照剧本如实表演，不会有任何个人发挥。

（2）在主控机器上安装 Ansible，并设置不进行 host key 检查。主控机器指的是真正执行ansible命令的机器，也就是Jenkins。我们需要在主控机器上自行安装Asible，然后修改主控机器的Ansible配置，不进行host key检查。

ansibleVault步骤
放在配置文件中的MySQL连接密码，想必是不希望被所有人看见的。Ansible vault是Ansible的一个特性，它能帮助我们加解密配置文件或者某个配置项。

### 12.3 手动部署比自动化部署更可靠吗

自动化部署的优势在于：
• 人只需要保证部署逻辑的正确性，自动化逻辑的正确性由工具保证。
• 在一天内工具重复执行同一个部署任务1000次，不会有任何怨言，而且每次执行都会保证结果是一样的。

### 13.2 钉钉通知

钉钉（DingTalk）是阿里巴巴集团开发的企业协同办公软件。本节介绍Jenkins与钉钉的集成。原理很简单，就是在钉钉群中增加一个钉钉机器人，我们将消息发送到钉钉机器人的API就可以了。

### 13.3 HTTP请求通知

使用HTTP Request插件（https：//plugins.jenkins.io/http_request），我们能在Jenkins pipeline中发送HTTP请求给第三方系统。这是最通用的Jenkins与第三方系统集成的方式之一。

### 14 分布式构建与并行构建

解决办法就是将Jenkins项目分配到多台机器上执行，这就是分布式构建。

### 14.1 Jenkins架构

Jenkins采用的是“master+agent”架构（有时也称为“master+slave”架构），如图14-1所示。Jenkins master负责提供界面、处理HTTP请求及管理构建环境；构建的执行则由Jenkins agent负责（早期，agent也被称为slave。目前还有一些插件沿用slave的概念）。

基于这样的架构，只需要增加agent就可以轻松支持更多的项目同时执行。这种方式称为Jenkins agent的横向扩容。

对于Jenkins master，存在单节点问题是显而易见的，但是目前还没有很好的解决方案。

• agent：代理，在概念上指的是相对于Jenkins master的一种角色，实际上是指运行在机器或容器中的一个程序，它会连接上Jenkins master，并执行Jenkins master分配给它的任务

对于slave，可以等同于agent。

### 14.2 增加agent

实现分布式构建最常用、最基本的方式就是增加agent。Jenkins agent作为一个负责执行任务的程序，它需要与Jenkins master建立双向连接。连接方式有多种，这也代表有多种增加agent的方式。

当agent数量变多时，如何知道哪些agent支持JDK 8、哪些agent支持Node.js环境呢？我们可以通过给agent打标签（有时也称为tag）来确定

Java网络启动协议（JNLP）是一种允许客户端启动托管在远程Web服务器上的应用程序的协议。Jenkins master与agent通过JNLP协议进行通信。而Java Web Start（JWS）可以被理解为JNLP协议的一个客户端。

（1）进入Manage Jenkins→Global Security→TCP port for JNLP配置页面，如图14-2所示。我们可以选择开放固定端口或者随机开放Jenkins master的一个端口来提供JNLP服务

（2）进入Manage Jenkins→Manage Nodes→New Node页面，如图14-3所示。选项“Permanent Agent”指的是常驻代理客户端。

• Launch method：agent的运行方式。JNLP协议的agent选择“Launch agent via Java Web Start”。

（4）SSH登录到Jenkins agent机器，下载agent.jar文件（JNLP协议的客户端），下载路径为：＜Jenkins master地址>/jenkins/jnlpJars/agent.jar。假设这台机器已经安装好JDK，则执行命令：java-jar agent.jar-jnlpUrl http：//192.168.23.11：8667/jenkins/computer/node1/slave-agent.jnlp-workDir "/app"。其中-workDir参数用于指定agent的工作目录。当命令提示连接成功后，我们打开Jenkins master页面，查看node1的详情页，如图14-8所示，表示已经连接成功。

agent部分描述的是整个pipeline或在特定阶段执行任务时所在的agent。换句话说，Jenkins master根据此agent部分决定将任务分配到哪个agent上执行。agent部分必须在pipeline块内的顶层定义，而stage块内的定义是可选的。
any
到此为止，pipeline的agent部分都是这样写的：


注意：pipeline块内的agent部分是必需的，不能省略。

当pipeline需要在JDK 8环境下进行构建时，就需要通过标签来指定agent。

如果希望每个stage都运行在指定的agent中，那么pipeline就不需要指定agent了。示例如下：

### 14.3 将构建任务交给Docker

Jenkins master要将构建任务分配给Docker，就必须在Jenkins agent上安装Docker。建议给这些agent打上docker的标签。
14.3.1 在Jenkins agent上安

所以，安装pipeline插件后，就可以直接在pipeline的agent部分指定使用什么Docker镜像进行构建了。

• image：字符串类型，指定构建时使用的Docker镜像。

Docker拉取镜像时，默认是从Docker官方中心仓库拉取的。那么如何实现从私有仓库拉取呢？比如在“制品管理”章节中在Nexus上创建的私有仓库。
Docker插件为我们提供了界面操作，具体步骤如下：
进入Manage Jenkins→Configure System页面，找到“Pipeline Model Definition”部分，如图14-10所示。

• Docker registry URL：Docker私有仓库地址。
• Registry credentials：登录Docker私有仓库的凭证。

### 16 Jenkins运维

想象一下，新员工入职，你必须给他创建Jenkins、GitLab、SonarQube等各个系统的用户。你刚登录过Jenkins，想用GitLab时，还得输入用户名和密码再登录一次。当员工离职后，你还必须到各个系统中注销用户。这样的操作必定是低效的。单点登录服务可以为我们解决问题。单点登录（Single Sign On，SSO），是指在多系统应用中登录其中一个系统，便可以在其他所有系统中得到授权而无须再次登录，包括单点登录与单点注销两部分。

轻型目录访问协议（Lightweight Directory Access Protocol，LDAP）是一个开放的、中立的、工业标准的应用协议，通过IP协议提供访问控制和维护分布式信息的目录信息。

### 16.6 Jenkins配置即代码

Jenkins用久了，会有一种莫名的紧张感。因为没有人清楚Jenkins都配置了什么，以至于最终没有人敢动它。但凡使用界面进行配置的，都会有这样的后果。解决办法就是通过代码进行配置——Config-uration as Code。

### 17 自动化运维经验

现在市面上有很多监控系统，如 Zabbix、Open-Falcon、Prometheus 等。最终笔者选择了Prometheus。有以下几个理由。

之前我们介绍过，人少机器多，所以安装Prometheus的过程也必须要自动化，同时版本化。笔者先是以自己的工作电脑为控制机器，使用Ansible部署整个监控系统，示意图如图17-1所示。

可是，我们不可能24小时盯着屏幕看CPU负载有没有超吧？这时候就要做告警了。Prome-htues默认集成了多种告警渠道，钉钉除外。由于当时笔者所在团队使用的是钉钉，所以告警通道只能选择钉钉。有好心人开源了钉钉集成Prometheus告警的组件：prometheus-webhook-dingtalk（https：//github.com/timonwong/prometheus-webhook-dingtalk）。于是，我们做了告警，示意图

笔者推崇一开始就做配置版本化。在搭建监控系统的过程中，已经将配置抽离出来，放到一个单独的代码仓库进行管理。以后所有部署，我们都会将配置和部署逻辑分离。这样，只需要复制一份配置，改一改，就可以在新的环境中快速搭建一套新的监控系统。

采用Jenkins进行自动化编译打包后，我们遇到的第一个问题就是将打包出来的制品放在哪里。所以，在搭建好Jenkins后，就需要搭建Nexus了。

• 职责清晰。Jenkinsfile负责构建逻辑，deploy目录负责部署逻辑。
• 标准化。所有需要部署的业务系统都可以使用此目录结构，而不论是Go项目还是Node.js项目。

小团队自动化运维实施的顺序大致为：
（1）做基础监控。
（2）搭建GitLab。
（3）搭建Jenkins，并集成GitLab。
（4）使用Jenkins实现自动编译打包。
（5）将制品交给制品库管理。
（6）使用Jenkins执行Ansible。
完成以上步骤后，自动化运维的基本框架就算搭建完成了。虽然它算不上一个平台，但是足以满足大多数团队的需求。

### 17.2 ChatOps实践

2013年，ChatOps由GitHub工程师Jesse Newland提出。表面上，ChatOps就是在一个聊天窗口中发送一个命令给运维机器人bot，然后bot执行预定义的操作，并返回执行结果。
笔者认为，ChatOps更深层次的意义在于将重复性的手动运维工作自动化了，开发人员、运维人员可以自助实施一些简单的运维。