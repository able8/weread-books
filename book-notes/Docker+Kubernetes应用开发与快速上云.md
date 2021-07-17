## Docker+Kubernetes应用开发与快速上云
> 李文强

### 前言

Bug和问题往往与加班时间成正比，修复问题的时间可能远远大于开发功能的时间。

做好了DevOps实践，团队必然可以更快、更可靠地交付软件，提高客户的满意度。但是，做好DevOps实践不但对团队文化有很高的要求，而且对相关工具、技术、部署环境等也有很高的要求，这对于大部分团队来说是一个极大的挑战——既没有精力，也没有实力。

抛开团队文化等，有没有更易于落地的配合DevOps的解决方案呢？有，那就是以Docker为代表的容器技术作为基础保障、以Kubernetes（简称k8s）为代表的容器编排技术作为支撑的解决方案！

Docker+k8s短短几年就脱颖而出，除了更易配合DevOps落地之外，还有众多原因（比如Docker更轻、更快、开源、隔离应用，以及k8s便携、可扩展、自动修复等），但是其中很重要的一个原因是，在虚拟机时代那些无法解决或者说很难解决的问题以及那些积压已久的需求（比如分布式系统的部署和运维，物联网边缘计算的快速开发、测试、部署和运维，大规模的云计算，等等）在Docker+k8s的组合下找到了突破口，并且极大地促进了云计算的发展。

开发者普遍将Docker+k8s应用于敏捷开发、DevOps实践、混合云和微服务架构。

### 1.2 什么是Docker

简单地理解，Docker类似于集装箱（见图1-7）。各式各样的货物经过集装箱的标准化进行托管，而集装箱和集装箱之间没有影响。也就是说，Docker平台就是一个软件集装箱化平台。这就意味着我们自己可以构建应用程序，将其依赖关系一起打包到一个容器中，然后这个容器就很容易运送到其他的机器上运行，而且非常易于装载、复制、移除，非常适合软件弹性架构。

Docker是一个开放平台，使开发人员和管理员可以在称为容器的松散、隔离的环境中构建镜像、交付和运行分布式应用程序，以便在开发、QA和生产环境之间进行高效的应用程序生命周期管理。

### 1.3 容器简史

2007年谷歌实现了Control Groups（Cgroups），能够限制和隔离一系列进程的资源使用（CPU、内存、磁盘I/O、网络等）。同年，Cgroups被加入Linux内核中，这是划时代的，为后期容器的资源配额提供了技术保障。

2008年基于Cgroups和Linux Namespaces推出了第一个最为完善的Linux容器LXC。LXC指代的是Linux Containers，其功能通过Cgroups和Linux Namespaces实现，也是第一套完整的Linux容器管理实现方案。

2013年DotCloud（后更名为Docker）推出了到现在为止最为流行和使用最广泛的容器Docker，其理念是，“一次构建，随处运行”。Docker在起步阶段使用LXC，而后利用自己的libcontainer库（谷歌工程师一直在与Docker合作研发libcontainer，并将核心概念和抽象移植到了libcontainer）将其替换下来。

2014年CoreOS推出了一个类似于Docker的容器Rocket。CoreOS是一个更加轻量级的Linux操作系统，Rocket在安全性上比Docker更严格。

容器技术趋于成熟，并且迎来了容器云的发展，由此衍生出多种容器云的平台管理技术，其中以Kubernetes（一个容器编排平台）最为出众。这些细粒度的容器集群管理技术为微服务的发展奠定了基石。

容器技术的出现是历史的必然，是技术演进的一种创新结果，也是人们追求高效生产活动的一个解决方案、思想、工具和愿景。

### 1.6 Docker的三个基本概念

容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间中。前面讲过镜像使用的是分层存储，容器也是如此。

### 2.2 Docker的主要应用场景

2.2.5 整合服务器资源
如同通过虚拟机来整合多个应用，Docker隔离应用的能力使得Docker可以整合多个服务器，以降低成本。没有多个操作系统的内存占用，并能在多个实例之间共享没有使用的内存，因此Docker可以比虚拟机提供更好的服务器整合解决方案。

2.2.6 现代应用
Docker非常适合于微服务架构、分布式架构和弹性架构，使用Docker会让架构师搭建现代架构时事半功倍！其适应性主要体现在以下几点：
● 应用隔离优势。
● 轻量和快，支持秒级启动、秒级伸缩。
● 低成本优势。
● 环境一致性。
● 一次编译，到处运行。

2.2.8 快速部署
在虚拟机之前，引入新的硬件资源需要消耗几天的时间。虚拟化（Virtualization）技术将这个时间缩短到了分钟级别。Docker为进程创建一个容器，而无须启动一个操作系统，再次将这个过程缩短到了秒级。

Docker除了支持秒级启动之外，部署时镜像的拉取还支持差异化更新，不仅能够减少镜像的拉取时间（节约部署时间），还可以降低更新部署时的流量使用。

### 3.2 Ubuntu下的安装

Ubuntu起初基于Debian发行版和GNOME桌面环境，而从11.04版起Ubuntu发行版放弃了GNOME桌面环境，改为Unity，与Debian的不同在于它每6个月会发布一个新版本。Ubuntu的目标在于为一般用户提供一个最新、同时又相当稳定的主要由自由软件构建而成的操作系统。

OpenSSH是Secure Shell（SSH）协议工具的免费版本，用于远程控制或在计算机之间传输文件。OpenSSH提供服务器守护程序和客户端工具，以促进安全、加密的远程控制和文件传输操作，有效地取代传统工具。

### 3.3 CentOS下的安装

CentOS（Community Enterprise Operating System，社区企业操作系统）是Linux发行版之一，由Red Hat Enterprise Linux（RHEL）依照开放源代码规定释出的源代码编译而成。由于出自同样的源代码，因此有些要求高度稳定性的服务器以CentOS来替代商业版的Red Hat Enterprise Linux。两者的不同在于CentOS完全开源。

### 3.4 基于树莓派搭建个人网盘

dpkg是Debian Packager的简写，是为Debian专门开发的套件管理系统，方便软件的安装、更新及移除。所有源自Debian的Linux发行版都使用dpkg，例如Ubuntu、KNOPPIX等。

一行Docker命令就打开了个人云盘之旅，但是整个行程其实并不是这么简单的，我们还需要考虑以下问题：
● 如何实现容器数据的持久化？就如以上示例所示，当我们的容器重启之后就发现刚刚上传的文件都不见了，该如何处理呢？

### 4.3 列出本地镜像


        docker images --format "{{.ID}}({{.CreatedSince}}): {{.Repository}}"

### 5.1 基于Docker容器的内部循环开发工作流

WORKDIR指令用于为其他Dockerfile指令（如RUN、CMD）设置一个工作目录，并且设置用于运行容器镜像实例的工作目录。

ENTRYPOINT是配置容器启动后执行的指令，并且不可被docker run提供的参数覆盖。每个Dockerfile中只能有一个ENTRYPOINT，当指定多个时，只有最后一个生效。

ENV指令用于设置环境变量。这些变量以“key=value”的形式存在，并可以在容器内被脚本或者程序调用。这个机制为在容器中运行应用带来了极大的便利。

EXPOSE用来指定端口，使容器内的应用可以通过端口和外界交互。

（6）使用多阶段构建。
多阶段构建可以由多个FROM指令组成，每一个FROM语句表示一个新的构建阶段，阶段名称可以用AS参数指定。

Docker Compose是一个用于定义和运行多个Docker应用程序的工具。利用Docker Compose，可以使用YAML文件来配置应用程序的服务。然后，使用单个命令就可以从配置中创建并启动所有服务了。

● 禁止使用tab缩进，只能使用空格键。
● 缩进长度没有限制，只要元素对齐就表示这些元素属于一个层级。
● 使用#表示注释。
● 字符串可以不用引号标注。

● build：定义镜像生成，可以指定Dockerfile文件所在的目录路径，支持绝对路径和相对路径。

● environment：定义环境变量和配置。
● ports：定义端口映射，比如上面配置中将容器上的公开端口80转接到主机上的外部端口9901和9902。

● context：指定Dockerfile的文件路径，也可以是链接到git仓库的URL。

### 5.3 使用Visual Studio Code玩转Docker

5.3.2 Docker Compose扩展插件
我们可以按Ctrl + Shift + X组合键打开“扩展”视图，然后搜索Docker Compose来安装此插件，扩展如图5-40所示。

### 6.1 使用.NET Core开发云原生应用

云原生（Cloud Native）并不是新技术，而是一种理念，它是不同思想的集合，集目前各种热门技术之大成，它的意义在于让上云成为潮流而不是阻碍。云原生应用是指专门为在云平台部署和运行而设计的应用。

云原生定义了云端应用应该具备的基础特性，包括敏捷、可靠、可扩展、高弹性、故障恢复、不中断业务持续更新等。总的来说，云原生是一个思想的集合，包括DevOps、持续交付（Continuous Delivery）、微服务（MicroServices）、敏捷基础设施（Agile Infrastructure）、康威定律（Conways Law）等，以及根据商业能力对公司进行重组。

.NET Core是一个开源（MIT开源协议，仅保留版权，无任何限制）的通用开发平台，由Microsoft和.NET社区在GitHub上共同维护，主要特性如下所示：

● 跨平台：可以在Windows、MacOS和Linux操作系统上运行。
● 跨体系结构保持一致：在多个体系结构（包括x64、x86和ARM）上以相同的行为运行代码。

（1）用于开发和生成.NET Core、ASP.NET Core应用的镜像，请使用SDK镜像。
（2）用于运行.NET Core、ASP.NET Core应用的镜像，请使用运行时镜像。


在开发期间，我们侧重的是开发更改的速度以及调试的能力。在生产环境中，我们侧重的是应用部署和容器启动的速度和效率。


        docker stats --no-stream

（2）修改环境变量。环境变量“TZ”代表时区设置，对于东八区，我们通常设置为“Asia/Shanghai”。关于环境变量的查看和修改，我们已经讲解过多种方式了，这里就不重复讲解了。将环境变量“TZ”设置为“Asia/Shanghai”之后，

### 6.2 使用Docker搭建Java开发环境

Java是一门面向对象的编程语言，不仅吸收了C++语言的各种优点，还摒弃了C++里难以理解的多继承、指针等概念，因此Java语言具有功能强大和简单易用两个特征。Java语言作为静态面向对象编程语言的代表，极好地实现了面向对象理论，允许程序员以优雅的思维方式进行复杂的编程。

OpenJDK（Open Java Development Kit）是Java平台标准版（Java SE）的免费开源实现。OpenJDK是自版本7以来Java SE的官方参考实现。我们可以使用此镜像来编译或运行Java应用。

6.2.2 使用Docker搭建Java开发环境
本篇仅做探索，主要解决以下问题：
● 无须搭建Java开发环境。
● 开发环境变化只需更新镜像即可（比如从Java 8改为Java 9）。
● 无须安装IDE（比如Eclipse）。
● 提供一个极简Demo。


        # 编译￼
        RUN ["/usr/lib/jvm/java-9-openjdk-amd64/bin/javac", "Hello.java"]￼
￼
        # 运行￼
        ENTRYPOINT ["/usr/lib/jvm/java-9-openjdk-amd64/bin/java", "Hello"]

Docker Linux容器使用Linux内核的CGroup机制来实现限制容器的资源（CPU、内存等）使用，当系统资源不足时，系统就会根据优先级杀掉相关进程来维持系统的正常运行，导致常见的OOM（Out Of Memory）错误。
为了确保Java容器应用正常运行，我们需要确保JVM（Java Virtual Machine, Java虚拟机）获得可用的正确的CPU核心数和RAM容量，以相应地调整内部参数，在有限的资源下正常运行。

OpenJDK 8及更高版本可以正确检测容器的CPU内核数量和可用RAM。在OpenJDK 11中，这是默认打开的。在版本8、9和10中，我们必须使用以下配置选项启用容器限制RAM的检测，否则JVM将使用主机配置而不是容器资源配置（其并不识别容器）：
￼
        java -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap …

### 6.3 使用Go推送钉钉消息

Docker就是基于Go语言编写的。由于以上特性，Go特别适合云计算相关服务开发（关于这一点，大家可以关注各大云厂商（见图6-15）的开源项目）、服务器编程、分布式系统、网络编程、内存数据库等。

6.3.3 使用Go推送钉钉消息
接下来，我们使用Go编写一个简单的Demo：通过钉钉机器人WebHooks推送消息到钉钉。

● 从环境变量获取参数并校验：
￼
        //获取环境变量￼
        envs := make(map[string]string)￼
        for _, envName := range envList {￼
            envs[envName] = os.Getenv(envName)￼
            //参数检查￼
            if envs[envName]=="" && envName ! = "AT_MOBILES" && envName ! = "IS_AT_ALL"￼
{￼
              fmt.Println("envionment variable "+envName+" is required")￼
              os.Exit(1)￼
            }￼
        }￼


        FROM alpine￼
        RUN apk update && apk add ca-certificates￼
￼
        COPY --from=builder /go/bin/component-dingding /usr/bin/￼
        CMD ["component-dingding"]￼
        # 注意不要单独使用MAINTAINER指令，MAINTAINER已被Label标签代替￼
        LABEL MAINTAINER ="xinlai@xin-lai.com"￼
        # LABEL指令用于将元数据添加到镜像，支持键值对和JSON，可以使用docker inspect命令查看￼
        LABEL DingtalkComponent='{\￼
          "description": "使用钉钉发送通知消息．", \￼
          "input": [\￼
            {"name": "WEBHOOK", "desc": "必填，钉钉机器人Webhook地址"}, \￼
            {"name": "AT_MOBILES", "desc": "非必填，被@人的手机号"}, \￼
            {"name": "IS_AT_ALL", "desc": "非必填，@所有人时：true，否则为：false"}, \￼
            {"name": "MESSAGE", "desc": "必填，自定义发送的消息内容"}, \￼
            {"name": "MSG_TYPE", "desc": "必填，自定义发送的消息类型，目前仅支持text和￼
              markdown"}\￼
          ]\￼
        }'

这里我们使用标签来说明参数，可以使用以下命令来查看标签（见图6-20）：
￼
        docker inspect go-dingtalk

Alpine Linux是一个社区开发的面向安全应用的轻量级Linux发行版。从图6-21可以看出，它非常小，只有5MB。这是其最大的优势。因此，Alpine Linux非常适合用来做Docker镜像、路由器、防火墙、VPNs、VoIP盒子以及服务器的操作系统。

--rm用于自动清理，也就是“用之即来，用完即走”。

### 6.4 使用Python实现简单爬虫

5．编写Dockerfile
代码写完，按照惯例，我们仍然是使用Docker实现本地无SDK开发，因此编写Dockerfile：
￼
        # 使用官方镜像￼
        FROM python:3.7-slim￼
￼
        # 设置工作目录￼
        WORKDIR /app￼
￼
        # 复制当前目录￼
        COPY . /app￼
￼
        # 安装模块￼
        RUN pip install --trusted-host pypi.python.org -r requirements.txt￼
￼
        # Run app.py when the container launches￼
        CMD ["python", "app.py"]
注意

### 6.6 使用Node.js搭建团队技术文档站点

使用Node.js编写一个简单的Web服务器非常简单，主要需要用到http模块。http模块主要用于搭建HTTP服务端和客户端，全部

#指定node镜像的版本￼
        FROM node:8.9-alpine￼
￼
        #对外暴露的端口￼
        EXPOSE 80￼
￼
        # 复制文件￼
        COPY . .￼
￼
        # 运行￼
        ENTRYPOINT ["node", "app.js"]

### 7.4 数据库容器化

数据库容器化是实现数据库弹性调度的基础条件，如图7-1所示。因此，数据库容器化绝不是一个伪命题，是值得我们探索的一个方向，而且是一种必然的趋势。在

### 7.5 SQL Server容器化

必填项：
● ACCEPT_EULA = Y（表示接受最终用户许可协议，否则无法启动）。

密码应符合SQL Server默认密码策略，否则容器无法设置SQL Server，将停止工作。默认情况下，密码必须至少为8 个字符长，且包含大写字母、小写字母、十进制数字和符号4种字符集中的3种字符。可以通过执行docker logs命令检查错误日志。

### 7.6 如何持久保存数据

是保存Docker容器中数据的首选机制。虽然绑定挂载依赖于主机的目录结构，但是卷完全由Docker管理。主要有如下好处：
● 可以使用Docker CLI命令或Docker API管理卷。
● 易于备份或迁移。
● 适用于Linux和Windows容器。
● 可以在多个容器之间更安全地共享卷。

Docker的数据持久化主要有两种方式：一种是使用主机目录存储，也就是图中的bind mount；另一种是数据卷（volume）。这两种方式保存的数据均不会随着Docker容器的停止而丢失。接下来我们一起来实践。

方式一：使用主机目录
首先，我们可以将主机目录加载为容器的数据卷，用来存储数据库文件。例如，可以通过“-v<host directory>:/var/opt/mssql”命令来完成需求。

方式二：使用数据卷
我们可以使用docker volume命令来创建卷，然后执行如下命令：
￼
        docker volume create my-data￼
        docker volume ls￼

我们可以使用以下命令来检查数据卷：
￼
        docker volume inspect my-data

### 7.8 Redis容器化

Redis容器化的优势如下：
● 性能极高——Redis能读的速度是110000次/s，写的速度是81000次/s。
● 丰富的数据类型——Redis支持二进制案例的strings、lists、hashes、sets及ordered sets数据类型操作。
● 原子——Redis的所有操作都是原子性的，意思就是要么成功执行、要么完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。

● 丰富的特性——Redis还支持publish/subscribe、通知、key过期等特性。

docker run --name myRedis `￼
          -p 6379:6379 `￼
          -v d:/temp/data/redis:/data `￼
          -d redis

### 7.9 MySQL容器化

phpMyAdmin是一个B/S架构的MySQL数据库管理工具，让管理者可用Web接口管理MySQL数据库。

### 8.1 Docker+ Kubernetes已成为云计算的主流

● 可扩展性
支持模块化、插件化、可挂载、可组合，支持各种形式的扩展，并且k8s的扩展和插件在社区开发者和各大公司的支持下高速增长。

● 自动化和可伸缩性
支持自动部署、自动重启、自动复制、自动伸缩/扩展，可以定义复杂的容器化应用程序并将其部署在服务器集群甚至多个集群上——因为k8s会根据所需状态优化资源。通过内置的自动缩放器，k8s可轻松地水平缩放应用程序，同时自动监视和维护容器的正常运行。

### 8.2 Kubernetes主体架构

（5）cloud-controller-manager
云控制器管理器负责与底层云提供商的平台交互。云控制器管理器是Kubernetes版本1.6中引入的。

2）kube-proxy
kube-proxy为节点的网络代理，通过在主机上维护网络规则并执行连接转发来实现Kubernetes的服务抽象。
kube-proxy负责请求转发。kube-proxy允许通过一组后端功能进行TCP和UDP流转发或循环TCP和UDP转发。

● Web UI（Dashboard）
Dashboard（仪表盘）是Kubernetes集群基于Web的通用UI，允许用户管理集群以及管理集群中运行的应用程序。

Kubernetes要求底层网络支持集群内任意两个Pod之间的TCP/IP直接通信，通常采用虚拟二层网络技术来实现，例如Flannel、Open vSwitch等。因此，在Kubernetes中，一个Pod的容器与另外主机上的Pod容器能够直接通信。

Kubernetes的Volume定义在Pod上，被一个Pod中的多个容器挂载到具体的文件目录下，当容器终止或者重启时，Volume中的数据也不会丢失。也就是说，在Kubernetes中，Volume是Pod中能够被多个容器访问的共享目录。

● emptyDir
使用emptyDir时，Pod分配给节点时就会首先创建卷，并且只要Pod在该节点上运行，这个卷就会一直存在。当Pod被删除时，emptyDir中的数据也不复存在。

Local Storage同HostPath的区别在于对Pod的调度上，使用Local Storage可以由Kubernetes自动对Pod进行调度，而使用HostPath只能人工手动调度Pod，因为Kubernetes已经知道了每个节点上kube-reserved和system-reserved设置的本地存储限制。

### 8.5 使用kubeadm创建集群

kubeadm是一个命令行工具，提供“kubeadm init”和“kubeadm join”两个命令来快速创建和初始化kubernetes集群。

### 8.6 集群故障处理

组件、插件健康状态检查
使用命令：
￼
        kubectl get componentstatus
或
￼
        kubectl get cs

kube-apiserver对外暴露了Kubernetes API，如果kube-apiserver出现异常就可能会导致：
● 集群无法访问，无法注册新的节点。
● 资源（Deployment、Service等）无法创建、更新和删除。
● 现有的不依赖Kubernetes API的Pods和services可以继续正常工作。

journalctl命令来查看服务日志（比如docker）：
￼
        journalctl -u docker

查看所有节点：
￼
        kubectl describe nodes

# 假如Node没有公网IP或无法直接访问(防火墙等原因)，请使用port-forward模式￼
        kubectl debug  (POD | NAME) --port-forward --daemonset-ns=kube-system￼
    --daemonset-name=debug-agent

一般来说，大家遇到的Pod问题比较多，这里做个经验总结。
（1）Pod一直处于Pending状态
Pending一般情况下表示这个Pod没有被调度到一个节点上，通常使用“kubectl describe”命令来查看Pod事件以得到具体原因。通常情况下，这是因为资源不足引起的。
如果是资源不足，那么解决方案有：
● 添加工作节点。
● 移除部分Pod以释放资源。
● 降低当前Pod的资源限制。

（2）Pod一直处于Waiting状态，经诊断判为镜像拉取失败
如果一个Pod卡在Waiting状态，就表示这个Pod已经调试到节点上，但是没有运行起来。解决方案有：

（3）Pod一直处于CrashLoopBackOff状态，经检查判为健康检查启动超时而退出
CrashLoopBackOff状态说明容器曾经启动但又异常退出了，通常此Pod的重启次数是大于0的。解决方案有：
● 重新设置合适的健康检查阈值。
● 优化容器性能，提高启动速度。
● 关闭健康检查。

### 9.2 应用伸缩和回滚

kubectl autoscale”命令可以创建一个自动调节器，用于自动设置Deployment、ReplicaSet、StatefulSet或ReplicationController的Pod数量，具体语法如下：


        #自动扩展部署“demo-deployment”，其中Pod数在2到10之间￼
        #由于未指定目标CPU利用率，因此将使用默认的自动扩展策略￼
        kubectl autoscale deployment/demo-deployment --min=2--max=10￼
￼
        #自动扩展部署“demo-deployment”，其中Pod数在1到5之间，目标CPU利用率为70%!￼(MISSING)
        kubectl autoscale deployment/demo-deployment -max=5--cpu-percent=70

直接使用“kubectl run”命令来快速运行容器应用，其会自动创建相关的部署（Deployment）

“kubectl set”命令用于配置已经存在的资源对象。常用的子命令如下所示。
● env：修改环境变量

● image：设置镜像
语法：
￼
        kubectl set image (-f FILENAME | TYPE NAME)￼


        #查看部署对象“mssqldemo”的版本历史￼
        kubectl rollout history deployment/mssqldemo

ClusterIP Service是默认的Service类型，其通过集群的内部IP暴露服务，因此仅能在集群内部访问，常用于数据库等应用。

type: ClusterIP #服务类型，不填写此字段则默认为ClusterIP类型，也就是集群IP类型￼
          selector: #标签选择器￼
            app: demo #标签￼

 - protocol: TCP #协议，能够支持TCP和UDP￼
            port: 80  #当前端口￼
            targetPort: 80 #目标端口

LoadBalancer Service是暴露服务到外部（Internet）的标准方式，可以完美地解决我们上面的问题，不过使用之前，我们得有一个loadBalancerIP——负载均衡IP。一般的云厂商都能够提供这个服务。

首先，我们需要在腾讯云的k8s集群创建一个Demo Deployment，相关配置参考上文。
接下来，我们需要创建一个负载均衡服务，以便得到负载均衡IP，

### 9.4 使用Ingress负载分发微服务

NodePort Service存在太多缺陷，不适合生产环境。LoadBlancer Service则不太灵活，比如针对微服务架构，那么不同服务是否需要多个负载均衡服务呢？我们还有其他选择么？有，那就是Ingress。

Ingress将集群外部的HTTP和HTTPS路由暴露给集群中的Service，相当于集群的入口，而入口规则由Ingress定义的规则来控制，如图9-26所示。在使用Ingress之前，我们需要有一个Ingress Controller（入口控制器），例如ingress-nginx。

创建完成之后，腾讯云同样会自动创建负载均衡服务并且提供负载均衡IP，

当然这仅仅是微服务架构万里长征的第一步，毕竟NginxIngress控制器仅仅解决了服务的分发，并不具备完整的接口网关功能。笔者推荐大家使用Kong+Kong IngressController，架构如图9-38所示。

### 9.5 利用Helm简化Kubernetes应用部署

Helm是Kubernetes生态系统中的一个软件包管理工具，有点类似于Linux操作系统里面的“apt-get”和“yum”。

Helm的出现就是为了很好地解决上面这些问题。Helm Chart是用来封装Kubernetes原生应用程序的一系列YAML文件。我们可以在部署应用的时候自定义应用程序的一些Metadata，以便于应用程序的分发。对于应用发布者而言，可以通过Helm打包应用、管理应用依赖关系、管理应用版本并发布应用到软件仓库。对

● Chart
Helm的软件包，采用TAR格式，类似于APT的DEB包或者YUM的RPM包，其包含了一组定义Kubernetes资源相关的YAML文件。

使用helm install命令在Kubernetes集群中部署的Chart称为Release。Helm中提到的Release和我们通常概念中的版本有所不同，这里的Release可以理解为Helm使用Chart包部署的一个应用实例。在同一个集群中，一个Chart可以使用不同的配置（Config）安装多次，每次安装都会创建一个Release。


        helm rollback zeroed-rodent 1￼
        #回滚到版本1

### 10.1 什么是云计算

混合云组合了公有云和私有云，通过允许在这二者之间共享数据和应用程序的技术将它们绑定到一起。混合云允许数据和应用程序在私有云和公共云之间移动，使用户能够更灵活地处理业务并提供更多部署选项，有助于优化现有基础结构、安全性和符合性。

### 10.2 Docker+k8s是上云的不二选择

Docker + k8s的组合促进了全面容器化浪潮的到来，也标志着云原生应用浪潮的到来，传统应用升级缓慢、架构臃肿、不能快速迭代、故障不能快速定位、问题无法快速解决、无法自愈等一系列问题都将得到更好的解决。

### 10.3 主流云计算容器服务介绍

Amazon Web Services（AWS）是亚马逊公司旗下云计算服务平台，为全世界范围内的客户提供云解决方案。AWS面向用户提供包括弹性计算、存储、数据库、应用程序在内的一整套云计算服务，帮助企业降低IT投入成本和维护成本。

飞天（Apsara）是由阿里云自主研发、服务全球的超大规模通用计算操作系统。它可以将遍布全球的百万级服务器连成一台超级计算机，以在线公共服务的方式为社会提供计算能力。从PC互联网到移动互联网再到万物互联网，互联网成为世界新的基础设施。

### 10.5 一般应用服务部署流程

● NFS盘：可以使用腾讯云的文件存储CFS，也可以使用自建的文件存储NFS，只需要填写NFS路径即可。NFS数据卷适用于多读多写的持久化存储，适用于大数据分析、媒体处理、内容管理等场景。

HTTP请求检查：针对的是提供HTTP/HTTPS服务的容器，集群周期性地对该容器发起HTTP/HTTPS GET请求，如果HTTP/HTTPS response返回码属于200～399，就证明探测成功，

配置镜像触发器
镜像触发器可以在每次生成新的Tag（镜像版本）时自行执行动作，如自动更新使用该镜像仓库的服务。

### 10.6 如何节约云端成本

Ingress是k8s集群的流量入口，即外部流量进入k8s集群的必经之路，其公开了从集群外部到集群内服务的HTTP和HTTPS路由。

上所示，我们通过一个Ingress服务配置了多个转发，也就是说我们可以通过一个Ingress和一个负载均衡服务完成多个应用的域名服务转发，也可以完成一个应用的多个域名多个微服务的域名转发。

容器服务的数据卷支持本地硬盘（主机目录）、云硬盘、NFS盘和配置项。通常情况下，我们会使用云硬盘，但是一个云硬盘仅能挂载到一个容器服务实例，既不利于存储数据的共享，也不利于存储资源的最大化利用。

在对I/O性能要求不高的情况下，推荐使用NFS盘（见图10-30）。NFS数据卷适用于多读多写的持久化存储，适用于大数据分析、媒体处理、内容管理等场景，可以使用腾讯云的文件存储CFS，也可以使用自建的文件存储NFS。

### 10.7 问题处理

在云端，我们往往会使用云硬盘来存储应用数据，但是由于云硬盘仅能同时绑定一个Pod，因此在某些情况下，比如当前节点资源不足驱逐Pod时，旧有的Pod杀不掉，新的Pod起不来，这时该怎么办呢？

### 第11章 容器化后DevOps之旅

以Docker为代表的容器技术的出现，将复杂的环境依赖、配置信息、环境变量等打包进镜像，统一了交付和部署标准，终结了DevOps中交付和部署环节因环境、配置、程序本身的不同而造成的各种部署配置不同的困境，大大降低了部署的复杂性。

### 11.1 DevOps基础知识

DevOps是一组过程、方法与系统的统称，用于促进开发（应用程序/软件工程）、技术运营和质量保障（QA）部门之间的沟通、协作与整合。它是一种重视“软件开发人员（Dev）”和“IT运维技术人员（Ops）”之间沟通合作的文化、运动或惯例。通过自动化“软件交付”和“架构变更”的流程来使得构建、测试、发布软件能够更加快捷、频繁和可靠。它的出现是由于软件行业日益清晰地认识到为了按时交付软件产品和服务，开发和运维工作必须紧密合作。

瀑布开发模式，最典型的预见性的方法，严格遵循预先计划的需求、分析、设计、编码、测试的步骤顺序进行。

2）Agile Development，敏捷开发模式，敏捷开发以用户的需求进化为核心，采用迭代、循序渐进的方法进行软件开发。

精益软件开发，侧重于最大限度地提高客户价值，并精简一切无用、多余的内容。

（4）Continuous Integration（CI，持续集成）是一种软件开发实践，即团队开发成员经常集成他们的工作，通常每个成员每天至少集成一次，也就意味着每天可能会发生多次集成。每次集成都通过自动化的构建（包括编译、发布、自动化测试）来验证，以便尽早地发现集成错误。持续集成是DevOps实践中非常重要的一部分，这里特地用一个段子来帮助大家理解：

早集成、频繁地集成可帮助项目在早期发现项目风险和质量问题，如果到后期才发现这些问题，那么解决问题代价很大，很有可能导致项目延期或者项目失败。

（5）Continuous Delivery（持续交付）让软件产品的

（6）Continuous Deployment（CD，持续部署）是在持续交付的基础上把部署到生产环境的过程自动化。

DevOps的引入能对产品交付、测试、功能开发和维护（包括曾经罕见但如今已屡见不鲜的——“热补丁”）起到意义深远的影响。

DevOps经常被描述为“开发团队与运营团队之间更具协作性、更高效的关系”。由于团队间协作关系的改善，整个组织的效率因此得到提升，伴随频繁变化而来的生产环境的风险也能得到降低。

通过DevOps，各专业团队之间的协调和协作得到改善，缩短了将更改提交到系统与将更改投入到生产之间的时间，但是它还可确保此过程符合安全性和可靠性标准。

借助DevOps，团队可以更快、更可靠地交付软件，可以提高客户的满意度。

与传统的瀑布式开发模型相比，采用敏捷或迭代式开发意味着更频繁的发布、每次发布包含的变化更少。由于经常进行部署，因此每次部署都不会对生产系统造成巨大影响，应用程序会以平滑的速率逐渐生长。

（3）自动化
强大的部署自动化手段确保部署任务的可重复性、减少部署出错的可能性。

DevOps是一种理念、一种思维和一种工作方式，更是一种团队文化。一方面，我们要加强整个开发生命周期的自动化过程，涉及整个开发生命周期中的持续开发、持续测试、持续集成、持续部署、持续监控、持续反馈和持续改进，以便保障快速、安全和高质量的软件开发和发布。另一方面，我们需要保障所有利益相关者在一个循环中，加强团队协作和沟通，促进团队学习和成长。

要成功地转换为DevOps，团队文化才是关键，技术和工具仅仅是辅助和手段而已。每个团队都是独一无二的，因此没有一刀切的能够提高软件质量和效率的方法。

● 持续地促进团队协作、交流和沟通，建立良好的协作、沟通机制。
● 确保整个团队了解软件生命周期，并持续改进过程和目标

● 选择合适的工具和工作流程来支持和增强团队的DevOps，并保证足够的自动化。
● 从故障和问题中吸取经验和教训。
● 持续学习，为团队提供一些时间来培养技能、开展试验，以及学习和分享新的工具和技术。

### 11.2 Docker与持续集成和持续部署

Docker可以让我们非常容易和方便地以“容器化”的方式去构建和部署应用，大幅度提升了构建效率。它就像集装箱一样，打包了所有依赖，再在其他服务器上部署时就很容易，不至于换服务器后发现各种配置文件散落一地。这样就解决了编译时依赖和运行时依赖的问题。

Docker+k8s完全可以标准化我们的部署流程，自动部署完全没有压力，配合高可用的k8s集群，基于应用的滚动更新策略，持续运营也变得非常简单而高效，应用的升级和回滚也变得毫无压力。

### 11.5 使用内部管理工具完成CI/CD流程

Jenkins是一款用Java编写的开源的CI工具。当Oracle收购Sun Microsystems时，它作为Hudson的分支被开发出来。Jenkins是一个跨平台的CI工具，通过GUI界面和控制台命令进行配置。