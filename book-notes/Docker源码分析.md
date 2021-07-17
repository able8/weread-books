## Docker源码分析
> 孙宏亮

### 赞誉

当我发现这上面刊登的“Docker源码分析”系列文章的作者居然是我们课程组的研究生助教孙宏亮时，惊喜之情溢于言表。宏亮对Docker的理解十分深刻，他本人是Docker的积极拥护者、倡导者和贡献者。他在研究生毕业以后投身到了创业公司DaoCloud，去为Docker的梦想开创美好的未来。

崇尚源码至上的工程师文化里，文档介绍、发布会材料都是苍白的，唯有研读源码，才能深刻理解软件背后的原理。与所有其他软件一样，读源码并不是学习Docker最快的途径，但是如果有人通读源码后给出了详细分析，你就可以轻松地站在巨人的肩膀上。

### 序

提笔写“源码分析”，需要一定的勇气，阅读源码需要耗费大量的精力，需要从数十万行代码中整理出清晰的逻辑，从中抽丝拨茧、概括精华，这是一份非常辛苦的工作。

这本书非常适合以下三类读者学习和阅读。
□ 希望以Docker容器交付软件的程序员。
Docker是未来互联网软件的交付件

□ Go语言程序员。
即使不在项目中使用Docker，本书也能够为Go语言程序员带来帮助。Docker项目中大量采用了Go语言，尤其是在处理并发场景时，Docker对Go语言的运用可谓出神入化。本书可以帮助Go语言程序员亲身体验特大型项目中Go语言的威力，以及实战场景中Golang模式和功能的用法。

### 前言

Docker官方如此描述Docker：“Build，Ship，Run.An open platform for distributed applications for developers and sysadmins”。换言之，Docker为开发者与系统管理者提供了分布式应用的开放平台，从而可以便捷地构建、迁移与运行分布式应用。

笔者一直坚信，了解软件或者系统最直接、最透彻的方式就是研读它们的源码。“源码即文档”也是鼓励开发者能更多地从源码的角度去学习软件或系统的本质。

### 第1章　Docker架构

本书关于Docker的分析均基于Docker 1.2.0版本的源码。
本章目的是在理解Docker源码的基础上分析Docker架构，分析过程主要按照以下三个部分进行：
□ Docker的总架构图。
□ Docker架构内部各模块功能与实现的分析。

### 1.2　Docker总架构图

作为Linux平台上的一种容器的管理引擎，Docker并不像其他大型分布式系统那样复杂。Docker的源码总量并不多，而且清晰的源码结构使得Docker的学习成本并不高。换言之，Docker源码的学习过程并不枯燥，我们可以从中学到很多东西，如Go语言的运用、Docker架构的设计原理等。

Docker对用户而言是一个简单的C/S架构，用户通过客户端与服务器端建立通信，而Docker的后端是一个松耦合的架构，架构中的模块各司其职、有机组合，支撑着Docker运行。

对用户而言，Docker Client是与Docker Daemon建立通信的最佳途径。用户通过Docker Client发起容器的管理请求，请求最终发往Docker Daemon。

Docker Daemon内部所有的任务均由Engine来完成，且每一项工作都以一个Job的形式存在。
Docker Daemon需要完成的任务很多，因此Job的种类也很多。若用户需要下载容器镜像，Docker Daemon则会创建一个名为“pull”的Job，运行时从Docker Registry中下载镜像，并通过镜像管理驱动graphdriver将下载的镜像存储在graph中；

图1-1　Docker总架构图

### 1.3.1　Docker Client

Docker Client可以通过以下三种方式和Docker Daemon建立通信，分别为：tcp：//host：port、unix：//path_to_socket和fd：//socketfd。

### 1.3.2　Docker Daemon

DockerDaemon的作用主要有以下两方面：
□ 接收并处理Docker Client发送的请求。
□ 管理所有的Docker容器。
Docker Daemon运行时，会在后台启动一个Server，Server负责接收Docker Client发送的请求；接收请求后，Server通过路由与分发调度，找到相应的Handler来处理请求。

启动Docker Daemon所使用的可执行文件同样是docker，与Docker Client启动所使用的可执行文件docker相同。

通过传入的参数可以辨别Docker Daemon与Docker Client，如docker–d代表Docker Daemon的启动，dockerps则代表创建Docker Client，并发送ps请求。

Docker Daemon的架构大致可以分为三部分：Docker Server、Engine和Job。Daemon的架构如图1-2所示。

在Docker Daemon的启动过程中，DockerServer第一个完成。Docker Server通过包gorilla/mux，创建了一个mux.Router路由器，提供请求的路由功能。在Go语言中，gorilla/mux是一个强大的URL路由器以及调度分发器。创建路由器之后，Docker Server为mux.Router中添加有效的路由项，每一个路由项由HTTP请求方法（PUT、POST、GET或DELETE）、URL和Handler三部分组成。

对于每一个Docker Client请求，DockerServer均会创建一个全新的goroutine来服务。在goroutine中，Docker Server首先读取请求内容，然后做请求解析工作，接着匹配相应的路由项，随后调用相应的Handler来处理，最后Handler处理完请求之后给Docker Client回复响应。

Engine存储着大量的容器信息，同时管理着Docker大部分Job的执行。换言之，Docker中大部分任务的执行都需要Engine协助，并通过Engine匹配相应的Job完成Job的执行。

举例说明，Engine的handlers对象中有一项为：{"create"：daemon.ContainerCreate，}，则说明当执行名为“create”的Job时，执行的是daemon.ContainerCreate这个handler。

Job可以认为是Docker架构中Engine内部最基本的工作执行单元。DockerDaemon可以完成的每一项工作都会呈现为一个Job。

有关Job接口的设计，与UNIX进程非常相仿。比如说，Job有一个名称，有运行时参数，有环境变量，有标准输入与标准输出，有标准错误，还有返回状态等。
对于Job而言，定义完毕之后，运行才能完成Job自身真正的使命。Job的运行函数Run（）则用以执行Job本身。

### 1.3.4　Graph

Graph在Docker架构中扮演的角色是容器镜像的保管者。不论是Docker下载的镜像，还是Docker构建的镜像，均由Graph统一化管理。由于Docker支持多种不同的镜像存储方式，如aufs、devicemapper、Btrfs等，故Graph对镜像的存储也会因以上种类而存在一些差异。

### 1.3.5　Driver

Driver是Docker架构中的驱动模块。通过Driver驱动，Docker可以实现对Docker容器运行环境的定制，定制的维度主要有网络环境、存储方式以及容器执行方式。

Docker Driver的实现可以分为以下三类驱动：graphdriver、networkdriver和execdriver。

graphdriver主要用于完成容器镜像的管理，包括从远程Docker Registry上下载镜像并进行存储，也包括本地构建完镜像后的存储。当用户下载指定的容器镜像时，graphdriver将容器镜像分层存储在本地的指定目录下；

networkdriver的作用是完成Docker容器网络环境的配置，其中包括Docker Daemon启动时为Docker环境创建网桥；Docker容器创建前为其分配相应的网络接口资源；以及为Docker容器分配IP、端口并与宿主机做NAT端口映射，设置容器防火墙策略等。

### 1.3.6　libcontainer

libcontainer是Docker架构中一个使用Go语言设计实现的库，设计初衷是希望该库可以不依靠任何依赖，直接访问内核中与容器相关的系统调用。

正是由于libcontainer的存在，Docker可以直接调用libcontainer，而最终操作容器的namespaces、cgroups、apparmor、网络设备以及防火墙规则等。这一系列操作的完成都不需要依赖LXC或者其他包

### 1.3.7　Docker Container

Docker Container（Docker容器）是Docker架构中服务交付的最终体现形式。Docker通过DockerDaemon的管理，libcontainer的执行，最终创建Docker容器

### 1.4　Docker运行案例分析

本节将从实际的Docker运行案例出发，串联Docker各模块，从而学习Docker的运行流程。

### 1.4.1　docker pull

docker pull命令的执行流程如图1-10所示。

1）Docker Client处理用户发起的docker pull命令，解析完请求以及参数之后，发送一个HTTP请求给Docker Server，HTTP请求方法为POST，请求URL为"/images/create？"+"xxx"，实际意义为下载相应的镜像。

3）mux.Router将请求路由分发至相应的handler，具体为PostImagesCreate。

4）在PostImageCreate这个handler之中，创建并初始化一个名为"pull"的Job，之后触发执行该Job。
5）名为"pull"的Job在执行过程中执行pullRepository操作，即从Docker Registry中下载相应的一个或者多个Docker镜像。

7）graphdriver负责存储Docker镜像，一方面将实际镜像存储至本地文件系统中，另一方面为镜像创建对象，由Docker Daemon统一管理。

### 1.4.2　docker run

docker run命令的作用是创建一个全新的Docker容器，并在容器内部运行指定命令。Docker Daemon处理用户发起的这条命令时，所做工作可以分为两部分：第一，创建Docker容器对象，并为容器准备所需的rootfs；第二，创建容器的运行环境，如网络环境、资源限制等，最终真正运行用户指令。

图1-11　docker run命令执行流程示意图

）在PostContainersCreate这个handler之中，创建并初始化一个名为"create"的Job，之后触发执行该Job。
5）名为"create"的Job在运行过程中执行Container.Create操作，该操作需要获取容器镜像来为Docker容器准备rootfs，通过graphdriver完成。
6）graphdriver从Graph中获取创建Docker容器rootfs所需要的所有镜像。
7）graphdriver将rootfs的所有镜像通过某种联合文件系统的方式加载至Docker容器指定的文件目录下。

8）若以上操作全部正常执行，没有返回错误或异常，则Docker Client收到Docker Server返回状态之后，发起第二次HTTP请求。请求方法为"POST"，请求URL为"/containers/"+container_ID+"/start"，实际意义为启动时才创建完毕的容器对象，实现物理容器的真正运行。

10）mux.Router将请求路由分发至相应的handler，具体为PostContainersStart。
11）在PostContainersStart这个handler之中，创建并初始化名为"start"的Job，之后触发执行该Job。
12）名为"start"的Job执行需要完成一系列与Docker容器相关的配置工作，其中之一是为Docker容器网络环境分配网络资源，如IP资源等，通过调用networkdriver完成。
13）networkdriver为指定的Docker容器分配网络资源，其中有IP、port等，另外为容器设置防火墙规则。

Job开始执行用户指令，调用execdriver。
15）execdriver被调用，开始初始化Docker容器内部的运行环境，如命名空间、资源控制与隔离，以及用户命令的执行，相应的操作转交至libcontainer来完成。
16）libcontainer被调用，完成Docker容器内部的运行环境初始化，并最终执行用户要求启动的命令。

### 1.5　总结

本章从Docker 1.2.0的源码入手，首先分析抽象出Docker的架构图，并对该架构图中的各个模块进行功能与实现的简要分析，最后通过Docker的两个基础命令展示了Docker内部的运行。

另外，熟悉Docker现有架构以及设计思想，也能为云计算PaaS领域带来更多的启示，催生出更多创新想法。

### 2.2.1　Docker命令的flag参数解析

对于Docker请求中的参数，我们可以将其分为两类：第一类为命令行参数，即docker程序运行时所需提供的参数，如：-D、--daemon=true、--daemon=false等；第二类为docker发送给Docker Server的实际请求参数，如：ps、pull NAME等。
对于第一类，我们习惯将其称为flag参数，在Go语言的标准库中，专门为该类参数提供了一个flag包，方便进行命令行参数的解析。


位于./docker/docker/docker.go。这个go文件包含了整个Docker的main函数，也就是整个Docker（不论Docker Daemon还是Docker Client）的运行入口。

flVersion = flag.Bool([]string{"v", "-version"}, false, "Print version information and quit")￼
    flDaemon = flag.Bool([]string{"d", "-daemon"}, false, "Enable daemon mode")￼
    flDebug = flag.Bool([]string{"D", "-debug"}, false, "Enable debug mode")￼

关于Golang中的init函数，深入分析可以得出以下特性：
□ init函数用于程序执行前包的初始化工作，比如初始化变量等；
□ 每个包可以有多个init函数；
□ 包的每一个源文件也可以有多个init函数；
□ 同一个包内的init函数的执行顺序没有明确的定义；
□ 不同包的init函数按照包导入的依赖关系决定初始化的顺序；
□ init函数不能被调用，而是在main函数调用前自动被调用。

□ 访问flDaemon的值时，使用指针*flDaemon解引用访问。
在解析命令行flag参数时，以下语句为合法的（以flDaemon为例）：
□ -d，--daemon
□ -d=true，--daemon=true
□ -d="true"，--daemon="true"
□ -d='true'，--daemon='true'

遇到第一个非定义的flag参数ps时，flag包会将ps及其之后所有的参数存入flag.Args（），以便之后执行Docker Client具体的请求时使用。

### 2.2.2　处理flag信息并收集Docker Client的配置信息

若Docker发现flag参数flVersion为真，则说明Docker用户希望查看Docker的版本信息。此时，Docker调用showVersion（）显示版本信息，并从main函数退出；否则的话，继续往下执行。

if *flDebug {￼
     os.Setenv("DEBUG", "1")￼
}
若flDebug参数为真的话，Docker通过os包中的Setenv函数创建一个名为DEBUG的环境变量，并将其值设为"1"；继续往下执行。

// If we do not have a host, default to unix socket￼
           defaultHost = fmt.Sprintf("unix://%!s(MISSING)", api.DEFAULTUNIXSOCKET)￼

验证该defaultHost的合法性之后，将defaultHost的值追加至flHost的末尾，继续往下执行。当然若flHost的长度不为0，则说明用户已经指定地址，同样继续往下执行。

// If we should verify the server, we need to load a trusted ca￼
if *flTlsVerify {￼
     *flTls = true￼

### 2.2.3　如何创建Docker Client

if tlsConfig != nil {￼
     scheme = "https"￼
}￼

if in != nil {￼
     if file, ok := out.(*os.File); ok {￼
          terminalFd = file.Fd()￼
          isTerminal = term.IsTerminal(terminalFd)￼
     }￼
}￼

tlsConfig，安全传输层协议的配置。若tlsConfig不为空，则说明需要使用安全传输层协议，DockerCli对象的scheme设置为“https”，另外还有关于输入、输出以及错误显示的配置等。最终函数返回DockerCli对象。


### 2.3　Docker命令执行

Docker Client主要完成两方面的工作：解析请求命令，得出请求类型；执行具体类型的请求。本节将从这两个方面深入分析Docker Client。


### 2.3.1　Docker Client解析请求命令

return method(args[1:]...)￼

Docker Client首先通过请求参数列表中的第一个元素args[0]来获取具体的请求方法method。若上述method方法不存在，则返回docker命令的Help信息，若存在，调用具体的method方法，参数为args[1]及其之后所有的请求参数。

### 2.3.2　Docker Client执行请求命令

例子为：docker pull Image_Name。该命令的作用为：DockerClient发起下载镜像的请求，最终由Docker Server接收请求，由Docker Daemon完成镜像的下载与存储。

### 第3章　启动Docker Daemon

Docker Daemon是Docker架构中运行在后台的守护进程，大致可以分为Docker Server、Engine和Job三部分。三者的关系大致如下：Docker Daemon通过Docker Server模块接收Docker Client的请求，并在Engine中处理请求，然后根据请求类型，创建出指定的Job并运行。

### 3.2　Docker Daemon的启动流程

Docker Daemon和Docker Client的启动均通过可执行文件docker完成，因此两者的启动流程非常相似。Docker可执行文件运行时，程序运行通过不同的命令行flag参数，区分两者，并最终运行两者各自相应的部分。

### 3.3.1　配置初始化

通过Go语言的特性，变量的定义与init函数均会在mainDaemon（）之前运行。

首先，Docker声明一个名为daemonCfg的变量，代表整个Docker Daemon的配置信息。定义完毕之后，init函数的存在，使得daemonCfg变量能获取相应的属性值。

Docker声明daemonCfg之后，init函数实现了daemonCfg变量中各属性的赋值，具体的实现为：daemonCfg.InstallFlags（），位于./docker/docker/daemon/config.go，

### 3.3.3　创建engine对象

Engine结构体中最为重要的是handlers属性，handlers属性为map类型，key的类型是string，value的类型是Handler。

type Handler func(*Job) Status
可见，Handler为一个定义的函数。该函数传入的参数为Job指针，返回为Status状态。

1）创建一个Engine结构体实例eng，并初始化部分属性，如handlers、id、标准输出stdout、日志属性Logging等。


### 3.3.4　设置engine的信号捕获

Docker Daemon作为Linux操作系统上的一个后台进程，原则上应该具备处理信号的能力。信号处理能力的存在，能保障Docker管理员可以通过向Docker Daemon发送信号的方式，管理Docker Daemon的运行。

Docker Daemon关闭时，做一些必要的善后工作。
善后工作主要有以下4项：
□ Docker Daemon不再接受任何新的Job。
□ Docker Daemon等待所有存活的Job执行完毕。
□ Docker Daemon调用所有shutdown的处理方法。
□ 在15秒时间内，若所有的handler执行完毕，则Shutdown（）函数返回，否则强制返回。

### 3.3.6　使用goroutine加载daemon对象并运行

AcceptConnections函数的重点是go systemd.SdNotify（"READY=1"）的实现，位于./docker/docker/pkg/system/sdnotify.go#L12-L33，主要作用是通知init守护进程Docker Daemon的启动已经全部完成，潜在的功能是要求Docker Daemon开始接收并服务Docker Client发送来的API请求。

### 3.3.8　serveapi的创建与运行

最后通过job.Run（）运行该serveapi的Job。


### 3.4　总结

Docker的运行载体为Daemon，调度管理由Engine负责，任务执行靠Job。

### 第4章　Docker Daemon之NewDaemon实现

本章从源码角度，分析Docker Daemon加载过程中NewDaemon的实现，整个分析过程如图4-1所示。

Docker Daemon中NewDaemon的执行流程主要包含12个独立的步骤：处理配置信息、检测系统支持及用户权限、配置工作路径、加载并配置graphdriver、创建Docker Daemon网络环境、创建并初始化graphdb、创建execdriver、创建daemon实例、检测DNS配置、加载已有容器、设置shutdown处理方法，以及返回daemon实例。

图4-1　Docker Daemon中NewDaemon执行流程图

### 4.2　NewDaemon具体实现

在加载并运行daemon对象时，所做的第一个工作是：
d, err := daemon.NewDaemon(daemonCfg, eng)
简单分析一下这行代码。
□ 函数名：NewDaemon
□ 调用此函数的包名：daemon
□ 函数具体实现源文件：./docker/docker/daemon/daemon.go
□ 函数传入实参：daemonCfg，定义Docker Daemon运行过程中所需的众多配置信息；eng，在mainDaemon中创建的Engine对象实例
□ 函数返回内容：d，具体的Daemon对象实例；err，错误状态

### 4.3.1　配置Docker容器的MTU

func GetDefaultNetworkMtu() int {￼
     if iface, err := networkdriver.GetDefaultRouteIface(); err == nil {￼
          return iface.MTU￼
     }￼
     return defaultNetworkMtu￼
}

Docker通过networkdriver包的GetDefaultRouteIface方法获取具体的网络接口，若该网络接口存在，则返回该网络接口的MTU属性值；否则返回默认的MTU值defaultNetworkMtu，值为1500。

### 4.3.2　检测网桥配置信息

  return nil, fmt.Errorf("You specified -b & --bip, mutually exclusive options. Please specify only one.")￼
}
以上代码的含义为：若config中BridgeIface和BridgeIP两个属性均不为空，则返回nil对象，并返回错误信息，错误信息内容为：用户同时指定了BridgeIface和BridgeIP，这两个属性属于互斥类型，只能指定其中之一。

### 4.3.3　查验容器间的通信配置

若InterContainerCommunication的值为false，则Docker Daemon会在宿主机iptables的FORWARD链中添加一条Docker容器间流量均DROP的规则；若InterContainerCommunication为true，则Docker Daemon会在宿主机iptables的FORWARD链中添加一条Docker容器间流量均ACCEPT的规则。

### 4.3.5　处理PID文件配置

若不为空，则首先在文件系统中创建具体的Pidfile，然后向eng的onShutdown属性添加一个处理函数，函数具体完成的工作为utils.RemovePidFile（config.Pidfile），即在Docker Daemon进行shutdown操作的时候，删除Pidfile文件。在默认配置文件中，Pidfile文件的初始值为"/var/run/docker.pid"。

### 4.4　检测系统支持及用户权限

系统支持与用户权限检测的实现较为简单，源码实现如下：
if runtime.GOOS != "linux" {￼
     log.Fatalf("The Docker daemon is only supported on linux")￼
}￼
if os.Geteuid() != 0 {￼
     log.Fatalf("The Docker daemon needs to be run as root")￼
}￼
if err := checkKernelAndArch(); err != nil {￼
     log.Fatalf(err.Error())￼
}

通过os.Geteuid（），检测程序用户是否拥有足够权限。os.Geteuid（）返回调用者所在组的组id。结合具体源码分析可知，若返回不为0，则说明docker程序不是以root用户的身份运行，报出Fatal错误日志。

### 4.5　配置工作路径

Docker Daemon的root目录作用非常大，几乎涵盖Docker在宿主机上运行的所有信息，包括：所有的Docker镜像内容、所有Docker容器的文件系统、所有Docker容器的元数据、所有容器的数据卷内容等。
在默认配置文件中，Root属性的值为”/var/lib/docker”。

### 4.6　加载并配置graphdriver

graphdriver用于完成Docker镜像的管理，包括获取、存储以及容器rootfs的构建等。

### 4.6.1　创建graphdriver

graphdriver.DefaultDriver = config.GraphDriver￼
driver, err := graphdriver.New(config.Root, config.GraphOptions)
首先，Docker对graphdriver包中的DefaultDriver对象赋值，值为config中的GraphDriver属性，在默认配置文件中，GraphDriver属性的值为空；

第一，遍历数组选择graphdriver，数组内容为os.Getenv（"DOCKER_DRIVER"）和DefaultDriver。若数组不为空，graphdriver则通过GetDriver函数直接返回相应的Driver对象实例；若为空，则继续往下执行。这部分内容的作用是：让graphdriver的加载首先满足用户的自定义选择，用户可以通过定义环境变量的方式定义graphdriver的类型，其次Docker采用默认值，源码如下：
for _, name := range []string{os.Getenv("DOCKER_DRIVER"), DefaultDriver} {￼
     if name != "" {￼
          return GetDriver(name, root, options)￼
     }￼
}

GetDriver成功，则直接返回当前的Driver对象实例；若不成功，则继续往下执行。这部分内容的作用是：在没有指定以及默认的驱动时，从优先级数组中选择驱动，目前优先级最高的为aufs

### 4.6.2　验证btrfs与SELinux的兼容性

// As Docker on btrfs and SELinux are incompatible at present, error on both being enabled￼
if config.EnableSelinuxSupport && driver.String() == "btrfs" {￼
     return nil, fmt.Errorf("SELinux is not supported with the BTRFS graph driver!")￼
}

### 4.6.3　创建容器仓库目录

创建容器仓库目录
Docker Daemon在创建Docker容器之后，需要将容器的元数据信息放置于某个仓库目录下，统一管理。而这个目录即为daemonRepo，值为："/var/lib/docker/containers"。Docker通过daemonRepo创建对应的目录。

### 4.6.5　创建镜像graph

graph目录下的文件以镜像ID为单位，分别存储单个镜像的json文件以及镜像的大小文件layersize。其中镜像的json文件包含镜像自身的元数据信息。实现源码如下：
g, err := graph.NewGraph(path.Join(config.Root, "graph"), driver)
NewGraph的具体实现位于./docker/docker/graph/graph.go，实现过程中返回的对象为Graph类型

### 4.6.6　创建volumesdriver以及volumes graph

在Docker中数据卷（volume）的概念是：可以从Docker宿主机上挂载到Docker容器内部的特定目录。一个数据卷可以被多个Docker容器挂载，从而使Docker容器可以实现互相共享数据等。

第一种数据卷通常可以将其称为bind-mount volume，而第二种通常称为data volume。

### 4.6.7　创建TagStore

需要阐述的是TagStore类型中的多个属性的含义。
□ path：TagStore中记录镜像仓库的文件所在路径，如aufs类型的TagStore path的值为"/var/lib/docker/repositories-aufs"。
□ graph：相应的Graph实例对象。
□ Repositories：记录镜像仓库的映射数据结构。
□ sync.Mutex：TagStore的互斥锁。
□ pullingPool：记录池，记录有哪些镜像正在被下载，若某一个镜像正在被下载，则驳回其他Docker Client发起下载该镜像的请求。

### 4.7　配置Docker Daemon网络环境

job := eng.Job("init_networkdriver")￼
￼
     job.SetenvBool("EnableIptables", config.EnableIptables)￼

     job.SetenvBool("EnableIpForward", config.EnableIpForward)￼
     job.Setenv("BridgeIface", config.BridgeIface)￼
     job.Setenv("BridgeIP", config.BridgeIP)￼
     job.Setenv("DefaultBindingIP", config.DefaultIp.String())￼
     if err := job.Run(); err != nil {￼
          return nil, err￼
     }￼

配置DockerDaemon网络环境的工作主要通过init_networkdriver这个Job来完成。Docker首先创建名为init_networkdriver的Job，随后为此Job设置环境变量，环境变量的值如下：
□ 环境变量EnableIptables，使用config.EnableIptables来赋值，默认值为true。

### 4.7.2　启用iptables功能

其中setupIPtables的调用过程中，addr地址为Docker网桥的网络地址，icc为true，即允许Docker容器间互相访问。假设网桥设备名为docker0，网桥网络地址为docker0_ip，设置iptables规则，具体操作步骤如下：
1）使用iptables工具开启新建网桥的NAT功能，使用如下命令：
iptables -I POSTROUTING -t nat -s docker0_ip ! -o docker0 -j MASQUERADE

Docker容器之间建立通信，说明数据包从源容器内发出后，经过docker0，并且还需要在docker0处发往docker0，最终转向目标容器。换言之，从docker0出来的数据包，如果需要继续发往docker0，则说明是Docker容器间的通信数据包。使用命令如下：
iptables -I FORWARD -i docker0 -o docker0 -j ACCEPT

3）允许接受从容器发出，且目标地址不是容器的数据包。换言之，允许所有从docker0发出且不是继续发向docker0的数据包，使用命令如下：
iptables -I FORWARD -i docker0 ! -o docker0 -j ACCEPT

4）对于发往docker0，并且属于已经建立的连接的数据包，Docker无条件接受这些连接上的数据包，使用命令如下：
iptables -I FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT

### 4.7.3　启用系统数据包转发功能

数据包转发，即当宿主机存在多块网络设备时，如果其中一块网络设备接收到数据包，则无条件将其转发给另外的网络设备。通过修改/proc/sys/net/ipv4/ip_forward的值，将其置为1，则可以保证系统内数据包可以实现转发功能，代码如下：
if ipForward {￼
     // Enable IPv4 forwarding￼
          if err := ioutil.WriteFile("/proc/sys/net/ipv4/ip_forward", []byte{'1', '\n'}, 0644); err != nil {￼
          job.Logf("WARNING: unable to enable IPv4 forwarding: %!s(MISSING)\n", err)￼
     }￼
}

### 4.8　创建graphdb并初始化

graphdb是一个构建在SQLite之上的图形数据库，通常用来记录节点命名以及节点之间的关联。在Docker的世界中，用户可以通过link操作，使得Docker容器之间建立一种关联，而Docker Daemon正是使用graphdb来记录这种容器间的关联信息。Docker创建graphdb的源码如下：

### 4.11　检测DNS配置

若宿主机上DNS文件resolv.conf中有127.0.0.1，而Docker容器在自身内部不能使用该地址，故采用默认外在DNS服务器，为8.8.8.8，8.8.4.4；若宿主机上的resolv.conf有Docker容器可以使用的DNS服务器地址，则Docker Daemon采用该地址。最终Docker Daemon将DNS服务器地址赋值给config文件中的Dns属性。

### 4.12　启动时加载已有Docker容器

Docker Daemon重启时，很有可能之前有遗留的Docker容器。对于这部分容器，DockerDaemon重启前，一直将元数据信息存储在daemon.repository（目录为/var/lib/docker/containers）中。为了保证重启之后Docker容器的信息不丢失，DockerDaemon首先会进入该目录去查看，是否存在遗留的Docker容器。若存在，则Docker Daemon加载这部分容器，将容器信息收集，并做相应的维护。

### 4.13　设置shutdown的处理方法

□ 通过portallocator.ReleaseAll（），释放所有之前占用的端口资源。
□ 通过daemon.driver.Cleanup（），通过graphdriver实现unmount所有有关镜像layer的挂载点。
□ 通过daemon.containerGraph.Close（）关闭graphdb的连接。

### 第5章　Docker Server的创建

Docker Server最主要的功能是：接收用户通过Docker Client发送的请求，并按照相应的路由规则实现请求的路由分发，最终将请求处理后的结果返回至Docker Client。

### 5.2　Docker Server创建流程

Docker Server的创建与运行，代表了Job“serveapi”的整个生命周期。整个生命周期包括：创建Docker Server的Job，配置Job环境变量，以及触发执行Job。

### 5.2.3　运行Job

if err := job.Run(); err != nil {￼
     log.Fatal(err)￼
}

### 5.3　ServeApi运行流程

根据chErrors的值运行，若chErrors这个管道中有错误内容，则ServeApi的一次循环结束；若无错误内容，则循环被阻塞。chErrors这个管道的作用是：确保ListenAndServe所对应的协程能和主函数ServeApi进行协调

### 5.4　ListenAndServe实现

ListenAndServe的实现可以分为以下4个部分：
1）创建router路由实例。
2）创建listener监听实例。
3）创建http.Server。
4）启动API服务。

### 5.4.1　创建router路由实例

源码位于./docker/docker/vendor/src/github.com/gorilla/mux/mux.go#L16，如下所示：
func NewRouter() *Router {￼
     return &Router{namedRoutes: make(map[string]*Route), KeepContext: false}￼
}

Router路由实例r创建完毕，下一步工作是为Router实例r添加所需要的路由记录。路由记录存储着用来匹配请求的信息，包括对请求的匹配规则，以及匹配之后的处理方法执行入口。

包pprof将Docker Server运行时的分析数据通过“/debug/pprof/”这个URL向外暴露。这些运行时信息包括以下内容：可得的信息列表、正在运行的命令行信息、CPU信息、程序函数引用信息、ServeHTTP函数三部分信息的使用情况（堆使用、协程使用和线程使用）。

if logging {￼
               log.Infof("%!s(MISSING) %!s(MISSING)", r.Method, r.RequestURI)￼
          }￼

if enableCors {￼
               writeCorsHeaders(w, r)￼
          }￼

func writeCorsHeaders(w http.ResponseWriter, r *http.Request) {￼
     w.Header().Add("Access-Control-Allow-Origin", "*")￼
          w.Header().Add("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept")￼
          w.Header().Add("Access-Control-Allow-Methods", "GET, POST, DELETE, PUT, OPTIONS")￼
}

w.Header().Set("Content-Type", "application/json")￼
    eng.ServeHTTP(w, r)￼
    return nil￼

### 5.4.2　创建listener监听实例

在创建Listener之前，Docker先判断Docker Server允许的协议，若协议为fd形式，则直接通过ServeFd来服务请求；若协议不为fd形式，则继续往下执行。

判断serveapi这个Job的环境中BufferRequests的值，若为true，则通过包listenbuffer创建一个Listener的实例l，否则的话直接通过包net创建Listener实例l。

if job.GetenvBool("BufferRequests") {￼
     l, err = listenbuffer.NewListenBuffer(proto, addr, activationLock)￼
} else {￼
     l, err = net.Listen(proto, addr)￼
}
由于在ma

Listenbuffer的作用是：让Docker Server立即监听指定协议地址上的请求，但是将这些请求暂时先缓存下来，等Docker Daemon全部启动完毕之后，才让Docker Server开始接受这些请求。这样设计有一个很大的好处，那就是可以保证在Docker Daemon还没有完全启动完毕之前，接收并缓存尽可能多的用户请求。


实现TLS协议，首先需要建立一个tls.Config类型实例tlsConfig，然后在tlsConfig中加载证书、认证信息等，最终通过tls包中的NewListener函数，创建出适应于接收HTTPS协议请求的Listener实例l，

### 5.4.3　创建http.Server

在ListenAndServe实现中第三个重要的工作就是创建http.Server，实现源码如下：
httpSrv := http.Server{Addr: addr, Handler: r}
其中addr为需要监听的地址，r为mux.Router路由实例。

### 第6章　Docker Daemon网络

Docker借助容器技术彻底释放了轻量级虚拟化技术的威力，让容器的伸缩、应用的运行都变得前所未有的方便与高效。同时，Docker借助强大的镜像技术，让应用的分发、部署与管理变得史无前例的便捷。

在原生态的Docker世界中，Docker的网络却不具备跨宿主机的能力，这也或多或少制约着Docker在云计算领域的高速发展。

CoreOS团队针对Kubernetes设计的网络覆盖工具Flannel，

### 6.3　Docker Daemon网络配置接口

□ EnableIptables：确保Docker Daemon启动时，能对宿主机上的iptables规则进行修改。
□ EnableIpForward：确保net.ipv4.ip_forward功能开启，使得宿主机在多网络接口模式下，数据包可以在网络接口之间转发。

### 6.4.3　预处理flag参数

换言之，若用户指定了新建网桥的设备名，那么该网桥已经存在，无须指定网桥的IP地址BridgeIP；若用户指定了新建网桥的网络IP地址BridgeIP，那么该网桥肯定还没有新建成功，则Docker Daemon在新建网桥时使用默认网桥名docker0，无须在另行指定网桥的设备名。

EnableIptables和InterContainerCommunication配置信息的兼容性。具体表现为不能同时指定这两个flag参数为false。原因很简单，若指定InterContainerCommunication为false，则说明Docker Daemon不允许创建的Docker容器之间进行互相通信。但是为了达到以上目的，Docker正是使用iptables防火墙的过滤规则。因此，再次设定EnableIptables为false，关闭iptables的使用，即出现了自相矛盾的结果。

### 6.5.4　bridgeIface已创建

/ validate that the bridge ip matches the ip specified by BridgeIP￼

### 6.5.5　bridgeIface未创建

/ If the iface is not found, try to create it￼

libcontainer的netlink包中的CreateBridge实现了创建实际的网桥设备，具体使用系统调用的代码如下：
syscall.Syscall(syscall.SYS_IOCTL, uintptr(s), SIOC_BRADDBR, uintptr(unsafe.Pointer(nameBytePtr)))

### 6.5.6　获取网桥设备的网络地址

网桥网络地址的作用为：Docker Daemon在创建Docker容器时，使用该网络地址为Docker容器分配IP地址。Docker使用源码network=addr.（*net.IPNet）获取网桥设备的网络地址。

### 6.5.9　注册网络Handler

注册4个与网络相关的Handler。这4个处理方法分别是allocate_interface、release_interface、allocate_port和link，作用分别是为Docker容器分配IP网络地址，释放Docker容器网络设备，为Docker容器分配端口资源，以及在Docker容器之间执行link操作。

### 6.6　总结

Docker的网络环境可以分为Docker Daemon网络和Docker Container网络。本章从Docker Daemon的网络入手，分析了大家熟知的Docker桥接模式。

### 第7章　Docker容器网络

说到运行环境的“隔离”，相信大家肯定对Linux内核中的namespaces和cgroups略有耳闻，namespaces主要负责命名空间的隔离，cgroups主要负责资源使用的限制。

）父进程通过fork创建子进程时，使用namespaces技术，实现子进程与父进程以及其他进程之间命名空间的隔离。
2）子进程创建完毕之后，使用cgroups技术来处理进程，实现进程的资源限制。
3）namespaces和cgroups这两种技术都用上之后，进程所处的“隔离”环境才真正建立，此时“容器”真正诞生！

“使用Docker创建一个进程，为这个进程创建隔离的环境，这样的环境可以称为Docker容器，然后再在容器内部运行用户应用进程。

另外，如果子进程A再次fork出子进程B，而fork时没有传入相应的namespaces参数标志时，子进程B将会与A共享相同的命名空间（namespaces）。

Docker Daemon可以获知容器内主进程的PID信息，随后将该PID放置在cgroups文件系统的指定位置，做相应的资源限制。如此一来，当容器主进程再fork新的子进程时，新的子进程同样受到与主进程相同的资源限制，效果就是整个进程组受到资源限制。

可以说Linux内核的namespaces和cgroups技术，实现了资源的隔离与限制。

### 7.2　Docker容器网络模式

可以总结出Docker容器共有以下4种网络模式：bridge桥接模式、host模式、other container模式和none模式。

### 7.2.1　bridge桥接模式

bridge桥接模式的实现步骤如下。
1）利用veth pair技术，在宿主机上创建两个虚拟网络接口，假设为veth0和veth1。而veth pair技术的特性可以保证无论哪一个veth接收到网络报文，都会将报文传输给另一方。
2）Docker Daemon将veth0附加到Docker Daemon创建的docker0网桥上。保证宿主机的网络报文有能力发往veth0。

3）Docker Daemon将veth1添加到Docker容器所属的网络命名空间（namespaces）下，veth1在Docker容器看来就是eth0。一方面，保证宿主机的网络报文若发往veth0，可以立即被veth1收到，实现宿主机到Docker容器之间网络的联通性；另一方面，保证Docker容器单独使用veth1，实现容器之间以及容器与宿主机之间网络环境的隔离性。

为使Docker容器有能力让宿主机以外的世界感受到容器内部暴露的服务，Docker采用NAT（Network Address Translation，网络地址转换）的方式让宿主机以外的世界可以将网络报文发送至容器内部。

Docker使用NAT方法，将容器内部的服务与宿主机的某一个端口port_1进行“绑定”。
如此一来，外界访问Docker容器内部服务的流程为：
1）外界访问宿主机的IP以及宿主机的端口port_1。
2）当宿主机接收到这类请求之后，由于存在DNAT规则，会将该请求的目的IP（宿主机eth0的IP）和目的端口port_1进行替换，替换为容器IP和容器端口port_0。
3）由于能够识别容器IP，故宿主机可以将请求发送给veth pair。

2）请求通过容器内部eth0发送至veth pair的另一端，也就是到达网桥docker0处。
3）docker0网桥开启了数据报转发功能（/proc/sys/net/ipv4/ip_forward），故docker0将请求发送至宿主机的eth0处。
4）宿主机处理请求时，使用SNAT对请求进行源地址IP替换，即将请求中源地址IP（容器eth0的IP地址）替换为宿主机eth0的IP地址。

容器内部发起请求的响应不会在宿主机上经过DNAT处理。
其实，这一环节的关键在于iptables，具体的iptables规则如下：
iptables -I FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
此iptables规则的意思是：在宿主机上发往docker0网桥的网络数据报文，如果该数据报文所处的连接已经建立，则无条件接受，并由Linux内核将其转至原来的连接上，即回到Docker容器内部。

### 7.2.2　host模式

host模式，是因为该模式下的Docker容器会和host宿主机使用同一个网络命名空间，故Docker容器可以和宿主机一样，使用宿主机的eth0和外界进行通信。如此一来，Docker容器的IP地址自然也是宿主机eth0的IP地址。

Docker容器的host网络模式在实现过程中，由于不需要额外的网桥以及虚拟网卡，故不会涉及docker0以及veth pair。上文namespace的介绍中曾经提到：父进程在创建子进程的时候，如果不使用CLONE_NEWNET这个参数标志，那么创建出的子进程会与其父进程共享同一个网络命名空间。

### 7.2.3　other container模式

在Docker容器的other container网络模式实现过程中，不涉及网桥，同样也不需要创建虚拟网卡veth pair。完成other container网络的创建只需要两个步骤：
1）查找other container（即需要被共享网络环境的容器）的网络命名空间。
2）新创建的Docker容器的网络命名空间使用其他容器的网络命名空间。

Docker容器的other container网络模式，可以用来更好地服务于容器间的通信。需要互相通信的Docker容器在这种模式下，虽然网络隔离性没有bridge桥接模式优秀，但是相比host模式自然要好不少，同时网络隔离性稍有弱化的背后，带来的却是容器间高效的传输效率，以及容器间互访时简约的网络配置，即只需要使用localhost来访问其他容器。同时，多个容器共享同一个网络命令空间的形式，使得这部分容器的粘性大大提高。

other container网络模式似乎为容器组的实现提供了方向。值得思考的是，Google推出的Kubernetes平台中的Pod的原理，即使用了容器间共享网络命名空间的思路，实现了组的概念。

### 7.2.4　none模式

一旦Docker容器采用了none网络模式，那么容器内部就只能使用loopback网络接口，不会再有其他的网络资源。

### 7.3.2　runconfig包解析

□ Config结构体：描述Docker容器独立的配置信息。独立的含义是：Config这部分信息描述的是容器本身，而不会与容器所在宿主机相关。
□ HostConfig结构体：描述Docker容器与宿主机相关的配置信息。

函数parseRun返回config、hostConfig与cmd，代表着runconfig包解析配置参数工作的完成，下一节将回到CmdRun的运行。

### 7.3.3　CmdRun执行

若容器镜像还不存在，Docker Daemon返回一个404的错误，表示镜像不存在；Docker Client收到错误响应之后，再发起一个请求pull image，使Docker Daemon首先下载镜像，下载完之后Docker Client再次发起请求create container，Docker Daemon创建完之后，Docker Client最终发起请求start container。

### 7.4.1　创建容器之网络配置

容器创建的工作内容主要有以下两点：
1）创建与Docker容器对应的Container类型实例container。
2）创建Docker容器的rootfs。

### 7.4.2　启动容器之网络配置

容器对象container中有属性hostConfig，属性hostConfig中有属性NetworkMode，初始化容器网络配置initializeNetworking（）的主要工作就是：通过NetworkMode属性为Docker容器的网络做相应的初始化配置工作。

     content, err := ioutil.ReadFile("/etc/hosts")￼
     if os.IsNotExist(err) {￼
           return container.buildHostnameAndHostsFiles("")￼
     } else if err != nil {￼
           return err￼
     }￼

hostsPath, err := container.getRootResourcePath("hosts")￼

宿主机上添加到Docker容器内部的指定位置。这样的网络信息，主要有以下三种：
□ 宿主机的hostname文件，代表容器的主机名。
□ 宿主机的hosts文件，代表容器的主机名配置文件。
□ 宿主机resolv.conf文件，属于容器的域名文件。


由于Docker容器与宿主机共享网络，因此Docker容器的hostname文件、hosts文件以及resolv.conf文件原则上与宿主机上的这些文件内容应该保持一致。结果就是：Docker Daemon将宿主机的hostname文件、hosts文件以及resolv.conf内容写入Docker容器的指定目录下。目录一般为/var/lib/docker/containers/<container_id>，当容器启动时，再将这部分文件挂载进容器内部的指定路径。

第一步，从container对象的hostConfig属性中找出NetworkMode，并找到相应的容器，即17adef的容器对象container，实现源码如下：
nc, err := container.getNetworkedContainer()
第二步，将17adef容器对象的HostsPath、ResolveConfPath、Hostname和Domainname赋值给当前容器对象container，

第三，none网络模式。Docker容器的none网络模式意味着不给该容器创建任何网络环境，容器只能使用127.0.1.1的环回接口。


第一，通过allocateNetwork函数为容器分配一个网络接口可用的IP地址，并将网络配置（包括IP、bridge、Gateway等）赋值给container对象的NetworkSettings；第二，通过NetworkSettings为容器创建hosts、hostname等文件。

图7-7　Command类型关系图

case "container":￼
     nc, err := c.getNetworkedContainer()￼
     if err != nil {￼
          return err￼
     }￼
     en.ContainerID = nc.ID￼

若为none模式，则对于Network对象（即en，*execdriver.Network）不做任何操作。若为host模式，则将Network对象的HostNetworking置为true；若为bridge桥接模式，则首先创建一个NetworkInterface对象，完善该对象的Gateway、Bridge、IPAddress和IPPrefixLen信息，最后将NetworkInterface对象作为Network对象的中Interface属性的值；

Docker Daemon后续环节中实现启动容器的请求会被发送至execdriver，再经过libcontainer，最后实现Linux内核级别的进程启动。

func (container *Container) waitForStart() error {￼
     container.monitor = newContainerMonitor(container, container.hostConfig.RestartPolicy)￼
￼
     select {￼
     case <-container.monitor.startSignal:￼
     case err := <-utils.Go(container.monitor.Start):￼
          return err￼
     }￼
     return nil￼
}

Docker Daemon首先通过函数newContainerMonitor返回一个初始化的containerMonitor对象，该对象中带有容器进程的重启策略。总体而言，containerMonitor对象用以监视容器进程的执行。容器进程指的是容器pid namespace内进程号为1的进程，这个进程的状态代表容器的状态。一旦该进程停止运行，则容器内部所有进程都将收到一个终止信号，最终导致容器的运行失败。

网络部分在Docker Daemon内部的执行已经结束，紧接着程序的运行陷入execdriver，进一步运行容器启动的相关步骤。

### 7.5　execdriver网络执行流程

7.5　execdriver网络执行流程
Docker架构中execdriver的作用是启动容器内部进程，最终启动容器。目前，在Docker中execdriver作为执行驱动，可以有两种选项：lxc与native。其中，lxc驱动会调用lxc工具实现容器的启动，而native驱动会使用Docker官方发布的libcontainer来启动容器。

Docker Daemon启动容器的最后一步，即调用了execdriver的Run函数来执行。通过分析Run函数的具体实现，可知关于Docker容器的网络执行流程主要包括两个环节：
1）创建libcontainer的Config对象。
2）通过libcontainer中的namespaces包执行启动容器。

### 7.5.1　创建libcontainer的Config对象

createContainer函数的作用是：使用execdriver.Command来填充libcontainer.Config。简要介绍libcontainer.Config的作用就是，它定义了在一个容器化的环境中执行一个进程所需要的所有配置项。createContainer函数使用Docker Daemon层创建的execdriver.Command，创建更底层libcontainer所需要的Config对象。

execdriver更像是封装了libcontainer对外的接口，实现了将Docker Daemon特有的容器启动信息转换为底层libcontainer能真正使用的容器配置选项。

create-Container所做的第一个操作就是执行template.New（），意为创建一个libcontainer.Config的实例容器。

Template.New（）函数最后返回类型为libcontainer.Config的实例container，该实例中只包含默认配置项

createContainer的实现流程中，为了完善container对象（类型为libcontainer.Config），后续还有很多步骤，如与网络相关的createNetwork函数调用，与Linux内核Capabilities相关的setCapabilities函数调用，与cgroups相关的setupCgroups函数调用，以及与挂载目录相关的setupMounts函数调用等。

由于Docker容器的4种网络模式彼此互斥，故以上Network类型中Interface、ContainerID与HostNetworking最多只有一项会被赋值。
execdriver.Command中Network的类型定义如下：
type Network struct {￼
     Interface      *NetworkInterface￼
     Mtu            int              ￼
     ContainerID    string            ￼
     HostNetworking bool             ￼
}

if c.Network.HostNetworking {￼
     container.Namespaces["NEWNET"] = false￼
     return nil￼

Docker Daemon将container对象中代表网络命名空间的NEWNET设为false，最终导致libcontainer中不创建新的网络命名空间。

               Address:    fmt.Sprintf("%!s(MISSING)/%!d(MISSING)", c.Network.Interface.IPAddress, c.Network.Interface.IPPrefixLen),￼
          Gateway:    c.Network.Interface.Gateway,￼
          Type:       "veth",￼
          Bridge:     c.Network.Interface.Bridge,￼
          VethPrefix: "veth",￼

container.Networks = append(container.Networks, &vethNetwork)￼

execdriver首先需要在activeContainers中查找需要被共享网络环境的容器active；并通过active容器的启动执行命令cmd找到容器主进程在宿主机上的PID；随后在proc文件系统中找到该进程PID的关于网络命名空间的路径nspath，也是整个容器的网络命名空间路径；最后为类型为libcontainer.Config的container对象添加Networks属性，Network的类型为netns。

实现过程中，同样为类型libcontainer.Config的container对象添加Networks属性，Network的类型为loopback，源码如下：
container.Networks = []*libcontainer.Network{￼
     {￼
          Mtu:     c.Network.Mtu,￼
          Address: fmt.Sprintf("%!s(MISSING)/%!d(MISSING)", "127.0.0.1", 0),￼
          Gateway: "localhost",￼
          Type:    "loopback",￼
     },￼
}

### 7.6　libcontainer实现内核态网络配置

libcontainer是一个Linux操作系统上容器技术的解决方案。libcontainer指定了创建一个容器时所需要的配置选项，同时它利用Linux中namespaces和cgroups等技术为使用者提供了一套Golang原生态的容器实现方案，并且没有使用任何外部依赖。用户借助libcontainer，可以感受到众多操作命名空间、网络等资源的便利。

当execdriver调用libcontainer中namespaces包的Exec函数时，libcontainer开始发挥其实现容器功能的作用。Exec函数位于./docker/libcontainer/namespaces/exec.go#L24-L113。

### 7.6.1　创建exec.Cmd

提到exec.Cmd，就不得不提Go语言标准库中的os包和os/exec包。前者提供了与平台无关的操作系统功能集，后者则提供了功能集里与命令执行相关的部分。

Go语言中exec.Cmd的定义，

Path string                        //所需执行命令在系统中的路径￼
     Args []string                      //传入命令的参数￼
     Env []string                       //进程运行时的环境变量￼
     Dir string                         //命令运行的工作目录￼
     Stdin io.Reader￼
     Stdout io.Writer￼
     Stderr io.Writer￼
     ExtraFiles []*os.File              //进程所需打开的额外文件￼

Process *os.Process                //代表Cmd启动后，操作系统底层的具体进程￼
     ProcessState *os.ProcessState      //进程退出后保留的信息￼

### 7.6.3　为容器进程初始化网络环境

初始化网络环境需要两个非常重要的参数：container对象以及容器进程的Pid号。类型为libcontainer.Config的实例container包含用户对Docker容器的网络配置需求，另外容器进程的Pid可以使得创建的网络环境与进程新创建的namespace进行关联。

首先通过一个循环，遍历libcontainer.Config类型实例container中的网络属性Networks；随后使用GetStrategy函数处理Networks中每一个对象的Type属性，得出Network的类型，这里的类型有3种，分别为loopback、veth和netns。loopback环回接口针对bridge模式和none模式；veth针对bridge模式；而netns针对other container模式。

得到Network的类型之后，libcontainer创建相应的网络栈，具体实现使用每种网络栈类型下的Create函数。

容器网络栈的创建不在容器网络命名空间之内，而是在Docker Daemon所在的网络命名空间内。

当libcontainer执行command.Start（）时，由于创建了一个新的net namespace，故Linux内核会自动为新的net namespace创建一个loopback接口。当Linux内核创建完loopback接口之后，libcontainer所做的工作即只保留loopback设备的默认配置，并在后续libcontainer的Initialize函数中实现启动该接口。

2.veth网络栈的创建
veth是Docker容器实际使用的网络策略之一，实现手段是：使用网桥docker0并创建veth pair虚拟网络接口对，最终使一个veth附加在宿主机的docker0网桥之上，而另一个veth安置在容器的net namespace内部。

主要的流程包含以下四个步骤：
1）在宿主机上创建veth pair。
2）将一个veth附加至docker0网桥上。
3）启动第一个veth。
4）将第二个veth附加至libcontainer创建进程的namespace下（注意：并未启动第二个veth）。


使用Create函数实现veth pair的创建之后，libcontainer在Initialize函数中实现将网络namespace中的veth改名为“eth0”、设置网络设备的MTU、以及启动网络接口等。

netns针对Docker容器的other container网络模式服务。netns完成的工作是：将其他容器的net namespace路径，传递给需要创建other container网络模式的容器使用。

libcontainer使用Create函数先将NsPath传递给新建容器，再在Initialize函数中实现将net namespace的文件描述符交由新创建容器使用，最终实现两个Docker容器共享同一个网络栈。

### 第8章　Docker镜像

2007年下半年，当时cgroups被合并至Linux内核2.6.24版本。这整整6年时间并不是Linux平台“容器”技术发展的真空期。2008年LXC（Linux Container）诞生，它简化了容器的创建与管理；之后业界一些PaaS平台也初步尝试采用容器技术作为其云应用的运行环境。

不论是云计算大潮催生了Docker技术，抑或是Docker技术赶上了云计算的大时代，毋庸置疑的是，Docker作为技术领域的新宠儿，必将继续受到业界的广泛青睐。云计算时代，分布式应用逐渐流行，大部分应用对自身的构建、交付与运行都有着与传统不一样的需求。借助Linux内核的namespace和cgroup等特性，自然可以实现应用运行环境的资源隔离与限制等；

Docker另外采用了神奇的“镜像”技术作为Docker管理文件系统以及运行环境强有力的补充。Docker灵活的“镜像”技术，在笔者看来，也是其大红大紫最重要的因素之一。

### 8.2　Docker镜像介绍

image（镜像）是Docker术语的一种，对于容器而言，它代表一个只读的layer。而layer则具体代表Docker容器文件系统中可叠加的一部分。

### 8.3　rootfs

rootfs代表一个Docker容器在启动时（而非运行后）其内部进程可见的文件系统视角，或者Docker容器的根目录。当然，该目录下含有Docker容器所需要的系统文件、工具、容器文件等。

暂且把Docker容器的文件系统这么理解：只含只读的rootfs和可读写的文件系统。

容器虽然只有一个文件系统，但该文件系统由“两层”组成，分别为读写文件系统和只读文件系统。通过这样的理解，文件系统已然有了层级（layer）的意味。

### 8.4　Union Mount

一般情况下，若通过某种文件系统挂载内容至挂载点，挂载点目录中原先的内容将会被隐藏。而Union Mount则不会将挂载点目录中的内容隐藏，反而是将挂载点目录中的内容和被挂载的内容合并，并为合并后的内容提供一个统一独立的文件系统视角。通常来讲，被合并的文件系统中只有一个会以读写（read-write）模式挂载，其他文件系统的挂载模式均为只读（read-only）。实现这种Union Mount技术的文件系统一般称为联合文件系统（Union Filesystem），较为常见的有UnionFS、aufs、OverlayFS等。

从用户视角来看，容器内文件系统和rootfs完全一样，用户完全可以按照往常习惯，无差别地使用自身视角下文件系统中的所有内容；然而，从内核的角度来看，两者有非常大的区别。追溯区别存在的根本原因，那就不得不提及aufs等文件系统的COW（copy-on-write）特性。

COW文件系统首先不会覆写只读文件系统中的文件，即不会覆写rootfs中的/etc/bash.bashrc，其次反而会将该文件复制至读写文件系统中，即将/etc/bash.bashrc复制至读写文件系统中的/etc/bash.bashrc（此时，rootfs文件系统和读写文件系统中各含有一份/etc/bash.bashrc），最后再对后者进行更新操作。

删除操作执行时同样不会删除rootfs实际存在的MySQL，而是在读写文件系统中删除该部分内容，导致最终rootfs中的MySQL对容器用户不可见，也不可访。由于读写文件系统中根本不存在MySQL的相关内容，因此似乎在读写文件系统中找不到需要删除的对象。此时，aufs保障在读写文件系统中对这些文件内容做相关的标记（whiteout），确保用户在查看文件系统内容时，读写文件系统中的whiteout将遮盖住rootfs中的相应内容，导致这些内容不可见，以达到与删除这部分内容相类似的效果。

### 8.5　image

对于最下层的镜像，即没有父镜像的镜像，在Docker中我们习惯称之为基础镜像。

通过image的形式，原先较为臃肿的rootfs被逐渐打散成轻便的多层。除了轻便的特性之外，image同时还有前面提到的只读特性，如此一来，在不同的容器、不同的rootfs中image完全可以用来复用。

图8-4　多image组织关系示意图

### 8.6　layer

容器镜像的rootfs是容器只读的文件系统，rootfs又由多个只读的image构成。于是，rootfs中每个只读的image都可以称为一个layer。


Docker容器中每一层只读的image以及最上层可读写的文件系统，均称为layer。如此一来，layer的范畴比image多了一层，即多包含了最上层的读写文件系统。

的top layer是否可以转变为image？
答案是肯定的。Docker的设计理念中，top layer转变为image的行为（Docker中称为commit操作）进一步释放了容器rootfs的灵活性。Docker的开发者完全可以基于某个镜像创建容器做开发工作，并且无论在开发周期的哪个时间点，都可以对容器进行commit，将所有top layer中的内容打包为一个image，构成一个新的镜像。

不仅Docker commit的原理如此，基于Dockerfile的docker build最为核心的思想，也是不断将容器的top layer转化为image。

### 8.7　总结

Docker风暴席卷全球并非偶然。如今的云计算时代下，轻量级容器技术与灵活的镜像技术相结合，似乎颠覆了以往的软件交付模式，为持续集成（Continuous Integration，CI）与持续交付（Continuous Delivery，CD）的发展带来了全新的契机。

### 第9章　Docker镜像下载

Docker Hub作为Docker官方支持的Docker Registry，拥有全球成千上万的Docker Image。全球的Docker爱好者除了可以下载Docker Hub开放的镜像资源之外，还可以向Docker Hub贡献镜像资源。在Docker Hub上，用户不仅可以享受公有镜像带来的便利，而且可以创建私有镜像库。

### 9.2　Docker镜像下载流程

Docker Image的下载流程可以归纳为以下3个步骤：
1）用户通过Docker Client发送pull请求，用于让Docker Daemon下载指定名称的镜像；
2）Docker Server接收Docker镜像的pull请求，创建下载镜像任务并触发执行；
3）Docker Daemon执行镜像下载任务，从Docker Registry中下载指定镜像，并将其存储于本地的Graph中。

### 9.3　Docker Client

第一个接受并处理的Docker组件为Docker Client，执行内容包括以下三个步骤：
1）解析命令中与Docker镜像相关的参数；
2）配置Docker下载镜像时所需的认证信息；
3）发送RESTful请求至Docker Daemon。

### 9.3.1　解析镜像参数

通过docker二进制文件执行docker pull ubuntu：14.04时，Docker Client首先会创建，随后通过参数处理分析出请求类型pull，最终执行pull请求相应的处理函数。

ParseRepositoryTag函数首先从repos参数的尾部往前寻找“：”，若不存在，则说明用户没有显式指定Docker镜像的tag，返回整个repos作为Docker镜像的repository；

则说明用户显式指定了Docker镜像的tag，“：”前的内容作为repository信息，“：”后的内容作为tag信息，并返回两者。

if strings.Contains(reposName, "://") {￼
          // It cannot contain a scheme!￼
          return "", "", ErrInvalidRepositoryName￼
     }￼

### 9.3.2　配置认证信息

cli（Docker Client）加载ConfigFile，ConfigFile是Docker Client用于存放用户在Docker Registry上认证信息的对象。

DockerCli结构体的属性configFile是一个指向registry.ConfigFile的指针，而ConfigFile结构体的属性Configs属于map类型，其中key为string，代表Docker Registry的地址，value的类型为AuthConfig。AuthConfig类型的具体含义为用户在某个Docker Registry上的认证信息，包含用户名、密码、认证信息、邮箱地址以及服务器地址等。

### 9.3.3　发送API请求


     registryAuthHeader := []string{￼
          base64.URLEncoding.EncodeToString(buf),￼
     }￼

最后发送POST请求，请求的URL为“/images/create？”+v.Encode（），在URL中传入查询参数，包括“fromImage”与“tag”，另外在请求的HTTP Header中添加认证信息registryAuthHeader。

### 9.4.2　创建并配置Job

Docker Server只负责接收Docker Client发送的请求，并将其路由分发至相应的处理方法来处理，最终的请求执行还需要Docker Daemon来协作完成。Docker Server在处理方法中，通过创建Job并触发job执行的形式，把控制权交给Docker Daemon。

### 9.5.1　解析Job参数

如：在TagStore类型中设计了pullingPool对象，用于保存正在下载的Docker镜像，下载完毕之前禁止其他Docker Client发起相同镜像的下载请求，下载完毕之后pullingPool中的该记录被清除。

// Another pull of the same repository is already taking place; just wait for it to finish￼
               job.Stdout.Write(sf.FormatStatus("", "Repository %!s(MISSING) already being pulled by another client. Waiting.", localName))￼
          <-c￼
          return engine.StatusOK￼

### 9.5.2　创建session对象

9.5.2　创建session对象
为了下载Docker镜像，Docker Daemon与Docker Registry需要建立通信。为了保障两者之间通信的可靠性，Docker Daemon采用了session机制。Docker Daemon每收到一个Docker Client的镜像下载请求，都会创建一个与之相应的Docker Registry的session，之后所有的网络数据传输都在该session上完成。

type Session struct {￼
     authConfig    *AuthConfig￼
     reqFactory    *utils.HTTPRequestFactory￼
     indexEndpoint string￼
     jar           *cookiejar.Jar￼
     timeout       TimeoutType￼
}

创建的session对象为r，在执行镜像下载过程中，多数与镜像相关的数据传输均在r这个seesion的基础上完成。


### 9.5.3　执行镜像下载

GetRepositoryData执行过程中，会为指定repository中的每一个image创建一个ImgData对象，并最终将所有ImgData存放在RepositoryData的ImgList属性中，ImgList的类型为map，key为image的ID，value指向ImgData对象。

GetRemoteTags的作用则是获取镜像名称所在repository中所有tag的信息。

此时，RepositoryData的ImgList属性中，有的ImgData对象中有Tag内容，有的ImgData对象中没有Tag内容。这也和实际情况相符，如果下载一个ubuntu：14.04镜像，该镜像的rootfs中只有最上层的layer才有tag信息，这一个layer的父镜像并不一定存在tag信息。

Docker Daemon获得下载镜像的镜像ID之后，首先查验pullingPool，判断是否有其他Docker Client同样发起了该镜像的下载请求，若没有Docker Daemon才继续下载任务。

GetRemoteHistory负责获取指定image及其所有祖先image的id。

对于命令RUN apt-get update，Docker Daemon需要执行apt-get update操作，对应的rootfs上必定会有内容更新，导致新建的image所代表的layer中有新添加的内容。而如ENV path=/bin，WORKDIR/home这样的命令，仅仅配置了一些容器的运行参数，并没有镜像内容的更新，对于这种情况，Docker Daemon同样创建一个新的layer，并且这个新的layer中内容为空，而命令内容会在这层image的json信息中做更新。

可以认为Docker的image包含两部分内容：image的json信息、layer内容。当layer内容为空时，image的json信息被更新。

而NewImgJSON函数位于包image中，函数返回类型为一个Image对象，Image类型的定义如下：
type Image struct {￼
     ID              string             `json:"id"`￼
     Parent          string             `json:"parent,omitempty"`￼
     Comment         string             `json:"comment,omitempty"`￼
     Created         time.Time          `json:"created"`￼

GetRemoteImageLayer函数返回当前image的layer内容。image的layer内容指的是：该image在parent image之上做的文件系统内容更新，包括文件的增添、删除、修改等。

Docker镜像下载完毕之后，Docker Daemon需要在TagStore中指定的repository中添加相应的tag。每当用户查看本地镜像时，都可以从TagStore的repository中查看所有含tag信息的image。

本地的repository文件位于Docker的根目录，根目录一般为/var/lib/docker，如果使用aufs的graphdriver，则repository文件名为repositories-aufs。


### 9.6　总结

Docker镜像的下载需要Docker Client、Docker Server、Docker Daemon以及Docker Registry四者协同合作完成。

### 第10章　Docker镜像存储

关于Docker镜像在宿主机中的存储，关键点在于：在本地文件系统中以何种组织形式被Docker Daemon有效地统一化管理。这种管理可以使得Docker Daemon创建Docker容器服务时，方便获取镜像并完成Union Mount操作，为容器准备初始化的文件系统。

### 10.2　镜像注册

存储工作分为两个部分：
1）存储镜像内容；
2）在Graph中注册镜像信息。

每一个layer的DockerImage内容都可以认为由两个部分组成：镜像中每一个layer中存储的文件系统内容，这部分内容一般可以认为是未来Docker容器的静态文件内容；另一部分内容指的是容器的json文件，json文件代表的信息除了容器的基本属性信息之外，还包括未来容器运行时的动态信息，如ENV等信息。

存储镜像内容，意味着Docker Daemon所在宿主机上已经存在镜像的所有内容，除此之外，Docker Daemon仍需要对所存储的镜像进行统计备案，以便用户在后续的镜像管理与使用过程中，可以有据可循。为此，Docker Daemon设计了Graph，使用Graph来接管这部分的工作。Graph负责记录有哪些镜像已经正确存储，供Docker Daemon调用。

func (graph *Graph) Register(jsonData []byte, layerData archive.ArchiveReader, img *image.Image) (err error)

2）函数调用者类型为Graph；
3）传入函数的参数有3个：第一个为jsonData，类型为数组；第二个为layerData，类型为archive.ArchiveReader；第三个为img，类型为*image.Image；
4）函数返回对象为err，类型为error。

### 10.3　验证镜像ID

此步骤确保镜像ID命名的合法性。就功能而言，这部分内容提高了Docker镜像存储环节的鲁棒性。验证镜像ID由三个环节组成。
1）验证镜像ID的合法性；
2）验证镜像是否已存在；
3）初始化镜像目录。

验证镜像ID的合法性使用包utils中的ValidateID函数完成

if err := utils.ValidateID(img.ID); err != nil {￼
     return err￼
}

if graph.Exists(img.ID) {￼
     return fmt.Errorf("Image %!s(MISSING) already exists", img.ID)￼
}

// If the driver has this ID but the graph doesn't, remove it from the driver to start fresh.￼
// (the graph is the source of truth).￼

以AUFS这种类型的graphdriver为例，镜像内容存放在/var/lib/docker/aufs/diff目录下，而镜像会被挂载至目录/var/lib/docker/aufs/mnt下的指定位置。

### 10.4.1　创建mnt、diff和layers子目录

章内容全部以aufs类型为例，即在graph.driver为aufs的情况下，阐述Docker镜像的存储。在Ubuntu 14.04系统上，Docker Daemon的根目录一般为/var/lib/docker，而aufs类型driver的镜像存储路径一般为/var/lib/docker/aufs。

/ Three folders are created for each id￼
// mnt, layers, and diff￼

layers、diff以及mnt为目录/var/lib/docker/aufs下的三个子目录，1、2、3是镜像ID，分别代表三个镜像，三个目录下的1均代表同一个镜像ID。其中layers目录下保留每一个镜像的元数据，这些元数据是这个镜像的祖先镜像ID列表；diff目录下存储每一个镜像所在的layer，具体包含的文件系统内容；mnt目录下每一个文件都是一个镜像ID，代表在该层镜像之上挂载的可读写layer。

为layers目录下的镜像ID文件填充元数据。元数据内容为该镜像的所有祖先镜像ID列表。填充元数据的流程如下：
1）Docker Daemon首先通过f，err：=os.Create（path.Join（a.rootPath（），"layers"，id））打开layers目录下镜像ID文件；
2）然后，通过ids，err：=getParentIds（a.rootPath（），parent）获取父镜像的祖先镜像ID列表ids；
3）其次，将父镜像ID写入文件f；

### 10.4.2　挂载祖先镜像并返回根目录

Docker Daemon为当前层的镜像完成所有祖先镜像的Union Mount。挂载完毕之后，当前镜像的read-write层位于/var/lib/docker/aufs/mnt/image_ID。

type Driver struct {￼
     root       string￼
     sync.Mutex // Protects concurrent modification to active￼
     active     map[string]int￼
}
Driver结构体中root属性代表graphdriver所在的根目录，即/var/lib/docker/aufs。active属性为map类型，key为string，具体运用时key为DockerImage的ID，value为int类型，代表该层镜像layer被引用的次数总和。

属性sync.Mutex用于多个Job同时操作active属性时，确保active数据的同步工作。

进入挂载操作的分析。一旦Get参数传入的镜像ID参数不是一个基础镜像，那么说明该镜像存在父镜像，Docker Daemon需要将该镜像所有的祖先镜像都挂载到指定的位置，指定位置为/var/lib/docker/aufs/mnt/image_ID。所有祖先镜像的原生态文件系统内容分别位于/var/lib/docker/aufs/diff/<ID>。

其中out的值为/var/lib/docker/aufs/mnt/image_ID，即使用该层Docker镜像时其根目录所在路径，也可以认为是镜像的RW层所在路径，但一旦该层镜像之上还有镜像，那么在挂载后者之后，在上层镜像看来，下层镜像仍然是只读文件系统。

### 10.5　存储镜像内容

StoreImage亦可以分为三个步骤：
1）解压镜像内容layerData至diff目录；
2）收集镜像所占空间大小，并记录；
3）将jsonData信息写入临时文件。

### 10.5.1　解压镜像内容

StoreImage函数传入的镜像内容是一个压缩包，Docker Daemon理应在镜像存储时将其解压，为后续创建容器时直接使用镜像创造便利。

压缩包为传入StoreImage的参数layerData，而目标路径为/var/lib/docker/aufs/diff/<image_ID>

/ If layerData is not nil, unpack it into the new layer￼

start := time.Now().UTC()￼

log.Debugf("Untar time: %!v(MISSING)s", time.Now().UTC().Sub(start).Seconds())￼

一旦aufs driver实现了graphdriver包中的接口Diff，Docker Daemon就会使用aufs driver的接口方法实现后续的解压操作。解压操作的源代码如下：
if differ, ok := driver.(graphdriver.Differ); ok {￼
      if err := differ.ApplyDiff(img.ID, layerData); err != nil {￼
             return err￼
     }￼

func (a *Driver) ApplyDiff(id string, diff archive.ArchiveReader) error {￼
     return archive.Untar(diff, path.Join(a.rootPath(), "diff", id), nil)￼
}

解压过程中，Docker Daemon通过aufs driver的根目录/var/lib/docker/aufs、diff目录与镜像ID，拼接出镜像的解压路径，并执行解压任务。

### 10.5.2　收集镜像大小并记录

Docker Daemon还统计了镜像所占磁盘空间的大小，以便记录镜像信息，最终将这类信息传递给Docker用户。

SaveSize函数在root目录（临时目录/var/lib/docker/graph/_tmp）下创建文件layersize，并写入镜像大小的值img.Size。

### 10.5.3　存储jsonData信息

以Dockerfile中的ENV参数为例，ENV指定了Docker容器运行时内部进程的环境变量。而这些只有容器运行时才存在的动态信息，并不会被记录在静态的镜像文件系统中，而是以jsonData的形式先存储在宿主机的文件系统中，并与镜像文件系统泾渭分明，存储在不同的位置。当Docker Daemon启动Docker容器时，Docker Daemon会准备好挂载完毕的镜像文件系统环境；接着加载jsonData信息，并在运行Docker容器内部进程时，使用动态的jsonData内部信息为容器内部进程配置环境。

镜像大小信息layersize信息统计完毕，jsonData信息也成功记录，两者的存储文件均位于/var/lib/docker/graph/_tmp下，文件名分别为layersize和json。使用临时文件夹来存储这部分信息并非偶然，10.6节将阐述其中的原因。

### 10.6　注册镜像ID

关于镜像的注册行为，第一步就是将_tmp文件（/var/lib/docker/graph/_tmp）重命名为graph.ImageRoot（img.ID），实则为/var/lib/docker/graph/<img.ID>。使得Docker Daemon在而后的操作中可以通过img.ID在/var/lib/docker/graph目录下搜索到相应镜像的json文件与layersize文件。

// TruncIndex allows the retrieval of string identifiers by any of their unique prefixes.￼
// This is used to retrieve image and container IDs by more convenient shorthand prefixes.￼

记录如此长的镜像ID，对于Docker用户来说，稍显不切实际，而TruncIndex的概念则极大地帮助Docker用户可以通过简短的ID定位到指定的镜像，使得Docker镜像的使用变得尤为方便。原理是：Docker用户指定镜像ID的前缀，只要前缀满足在全局所有的镜像ID中唯一，Docker Daemon就可以通过TruncIndex定位到唯一的镜像ID。而graph.idIndex.Add（img.ID）正式完成将img.ID添加并保存至TruncIndex中。


### 10.7　总结

Docker镜像并非仅仅包含文件系统中的静态文件，除此之外，它还包含镜像的json信息，json信息中有Docker容器的配置信息，如暴露端口、环境变量等。

### 第11章　docker build实现

Docker正是凭借着其灵活的镜像技术，以及革命性的镜像托管服务Docker Hub，在云计算时代占据了行业内极为有利的位置。其自身的Dockerfile技术甚至有可能取代传统软件发布的模式，成为行业内更快捷、更有效、更易于部署与管理的软件发布模式。

Docker诞生之后，软件发布模式似乎有了新的转机。软件代码依然是软件的重要组成部分，然而，与以往不同的是，Docker生态圈中软件的发布使得代码与运行环境并不分离，而是将软件运行环境也圈入软件范畴，一并交付给客户。客户在获得软件之后，并不需要额外地配置环境，而是能够直接运行软件。

本章从Docker 1.2.0的源码出发，分析用户通过Dockerfile构建出一个Docker镜像的来龙去脉。

用户可以通过一个自定义的Dockerfile文件以及相关内容，从一个基础镜像起步，对于Dockerfile中的每一条命令，都在原先的镜像layer之上再额外构建一个新的镜像layer，直至构建出用户所需的镜像。

### 11.2　docker build执行流程

docker build可以帮助Docker用户通过Dockerfile的形式构建出自己的Docker镜像。更为细致的描述是：用户通过Docker Client向Docker Server发送一条docker build命令，发送命令时，用户需要首先指定Dockerfile以及相关内容，并通过Docker Client将请求发出；Docker Server接收请求之后，将其路由转发至相应的处理方法；Docker Daemon负责执行请求处理的处理方法，解析Dockerfile并构建出最终的镜像。

### 11.2.1　Docker Client与docker build

图11-2　Docker Client处理docker build命令的流程图

1.定义并解析flag参数
用户发起的Docker命令中，很多情况下都会带flag参数。一般情况下，Docker Client会通过定义与解析，对这些flag参数进行转义，从而将用户的请求按需转换，使其适应于Docker Server暴露的API接口。

noCache := cmd.Bool([]string{"#no-cache", "-no-cache"}, false, "Do not use cache when building the image")￼

forceRm := cmd.Bool([]string{"-force-rm"}, false, "Always remove intermediate containers, even after unsuccessful builds")
5个flag参数的具体说明参见表11-1。


命令docker build命令执行时，镜像缓存会大大缩短相似Dockerfile的build时间。比如：Dockerfile1中第4条命令为编译、安装某一款软件，执行该命令本身需要占用的时间极长；若Dockerfile2中的前4条命令与Dockerfile1完全一致，并且不涉及内容复制，则Dockerfile2在构建前4条命令时，完全可以使用Dockerfile1构建时的镜像缓存，从而大大压缩了本次docker build所消耗的时间

定义flag参数完毕之后，Docker Client随即解析命令中的flag参数，并对处理结果进行处理，源码实现如下：
if err := cmd.Parse(args); err != nil {￼
       return nil￼
}￼
if cmd.NArg() != 1 {￼
     cmd.Usage()￼
     return nil￼
}

分析build的源码实现，大家就可以发现：Docker的build命令支持的Dockerfile源比指定目录要丰富得多。除此之外，Docker还支持从STDIN读取Dockerfile、从远程URL获取Dockerfile，以及从git源获取Dockerfile

if cmd.Arg(0) == "-" {￼

        dockerfile, err := ioutil.ReadAll(buf)￼
          if err != nil {￼
               return fmt.Errorf("failed to read Dockerfile from STDIN: %!v(MISSING)", err)￼
          }￼

if output, err := exec.Command("git", "clone", "--recursive", remoteURL, root).CombinedOutput(); err != nil {￼
               return fmt.Errorf("Error trying to use git: %!s(MISSING) (%!s(MISSING))", err, output)￼
          }￼

简要分析该流程。Docker Client解析完flag参数之后，通过第一个参数cmd.Arg（0）来确定Dockerfile源。流程大致如下：
1）若该参数为"-"，则代表Docker用户指定STDIN作为Dockerfile的输入源，Docker Client随即通过docker build命令的标准输入读入数据，压缩后最终得到结果context；


### 11.2.2　Docker Server与docker build

因此对于docker build请求，Docker Server执行的处理方法为postBuild。

此处理方法的职责与其他处理方法无异：解析请求参数，创建并触发执行Job。创建的Job名为build，代码如下：
job = eng.Job("build")

### 11.2.3　Docker Daemon与docker build

Docker Daemon作为Docker体系中的“大脑”部分，任务真正的执行都由它来完成，请求docker build也不例外。处理docker build请求，意味着Docker Daemon会接管build这个Job的执行权，并将任务完成。
Docker Daemon开始执行的任务无外乎也是解析Job环境变量，获取Dockerfile内容。

Dockerfile及其相关内容可以通过三种源向Docker Daemon交付，因此，Docker Daemon获取相关内容时，也需要首先通过参数分辨是哪种具体方式，并实现内容的最终提取。

if !strings.HasPrefix(remoteURL, "git://") {￼
          remoteURL = "https://" + remoteURL￼
     }￼

f, err := utils.Download(remoteURL)￼
     if err != nil {￼
          return job.Error(err)￼
     }￼
     defer f.Body.Close()￼
     dockerFile, err := ioutil.ReadAll(f.Body)￼
     if err != nil {￼
          return job.Error(err)￼
     }￼
     c, err := archive.Generate("Dockerfile", string(dockerFile))￼

### 11.3　Dockerfile命令解析流程

for _, line := range strings.Split(dockerfile, "\n") {￼
     line = strings.Trim(strings.Replace(line, "\t", " ", -1), " \t\r\n")￼
     if len(line) == 0 {￼
          continue￼
     }￼

if b.forceRm {￼
               b.clearTmp(b.tmpContainers)￼
          }￼

tmp := strings.SplitN(expression, " ", 2)￼
     if len(tmp) != 2 {￼
          return fmt.Errorf("Invalid Dockerfile format")￼
     }￼
     instruction := strings.ToLower(strings.Trim(tmp[0], " "))￼
     arguments := strings.Trim(tmp[1], " ")￼

数组中，tmp[0]代表Dockerfile中相应行的命令类型，tmp[1]代表命令参数。两者解析完毕后，分别赋值给instruction与arguments。命令类型获取之后，Docker Daemon巧妙地使用了Golang中的反射（reflect）机制获取具体的执行方法。执行方法提取后，执行时传入命令参数，即完成了一条Dockerfile指令的执行

reflect.TypeOf(b).MethodByName("Cmd" + strings.￼
               ToUpper(instruction[:1]) + strings.ToLower(instruction[1:]))￼

接着反射机制将通过字符串“CmdFrom”找到方法CmdFrom，即method为CmdFrom；最后通过method.Func.Call（）函数传入参数，完成命令的执行。

### 11.4　Dockerfile命令分析

第一类命令修改上一层image的文件系统内容，比如：命令RUN在基于上一层image的容器中运行一条指令，对于用户而言，该指令很有可能修改上一层image的内容；

第二类命令仅仅修改镜像的config信息，比如：命令ENV不会修改镜像文件系统中的内容，而仅仅修改镜像的config的ENV信息，以便后续使用该镜像启动进程时，ENV信息作为进程的环境变量加载；

### 11.4.1　FROM命令

若镜像存在，则获取镜像信息，并开始buildFile配置流程。配置buildFile的作用是将镜像的信息加载至buildFile

if image.Config != nil {￼
     b.config = image.Config￼
}￼
if b.config.Env == nil || len(b.config.Env) == 0 {￼
     b.config.Env = append(b.config.Env, "PATH="+DefaultPathEnv)￼
}

回到buildFile的配置，以上代码表明若镜像config信息存在，则将镜像的config信息传递至buildFile的config属性；而config属性中的Env属性若为空，则在Env属性中添加默认PATH，常量为DefaultPathEnv

const DefaultPathEnv = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

### 11.4.2　RUN命令

hit, err := b.probeCache()￼
     if err != nil {￼
          return err￼
     }￼
     if hit {￼
          return nil￼
     }￼

1.镜像cache机制
首先分析probeCache，一起来领略Docker在build流程中巧妙使用镜像cache的精彩。镜像cache的重用意味着构建相应的命令时，不再执行RUN命令后的整条命令，而从Docker Daemon本地的镜像中找出效果与执行该命令相同的镜像，作为结果返回。

Docker Daemon只需遍历本地所有镜像，只要存在一个镜像，此镜像的父镜像ID与当前buildFile的image值相等，同时此镜像的config内容与buildFile.config相同，则完全可以认为执行RUN命令产生的结果与此镜像的效果一致，直接使用本地存储的此镜像即可，无须额外再创建。

运行Docker容器之前，Docker Daemon会为其创建一个Container对象，RUN命令执行流程中c，err：=b.create（）即创建了一个Container类型的实例c。在Docker Daemon的范畴内，创建一个Container对象，只需要基础镜像的ID，以及运行容器时所需的runconfig信息，而这些信息在build流程中都被buildFile统一管理

if b.image == "" {￼
               return nil, fmt.Errorf("Please provide a source image with 'from' prior to run")￼

3.挂载文件系统
RUN命令需要在容器中运行指定的程序，故仅仅创建Container类型实例c还不足以支撑容器的运行。CmdRun仍然需要为容器的运行挂载文件系统，c.Mount（）即实现了这部分功能。由于Container类型实例c中包含镜像的ID，因此Docker Daemon根据该镜像ID，追溯出此ID的所有祖先镜像，并将所有镜像通过指定的graphdriver完全联合起来，挂载到同一个目录下。而后容器的运行将使用该挂载点，作为容器的根目录。

2）把容器文件系统的Read-Write层打成tar包。由于在容器的运行过程中所有写操作都只会作用于文件系统中的top layer（即Read-Write层），故容器运行过程中文件系统的增量变化都在Read-Write层，将该层打包即可获得新镜像的文件系统内容。

3）创建image对象，并在Graph中注册。新的tar包即为镜像的原材料，通过tar包，以及众多配置信息，DockerDaemon为其创建一个image对象。最后通过graph.Register（）函数实现在Graph中注册该镜像。graph.Register（）函数相信大家一定不会陌生，第10章已经分析了该函数的实现。

回到buildFile的CmdRun函数，执行commit操作之后，立即返回创建的新镜像，而将该镜像的ID作为下一个Dockerfile命令执行的基础镜像，源代码为b.image=image.ID，同时CmdRun函数也执行完毕。

### 第12章　Docker容器创建

本章主要从源码的角度分析用户发起docker run命令之后，整个Docker体系中的组件如何协同工作，最终实现Docker容器的运行。

2）详细分析Docker Daemon创建容器对象的过程；
3）详细分析Docker Daemon启动容器的过程。

### 12.2　Docker容器运行流程

Docker Client共发送了两次POST请求至Docker Daemon。第一次请求试图让Docker Daemon通过请求中的镜像信息，创建容器对象；第二次请求则通知Docker Daemon启动容器，使得容器处于运行状态。

### 12.3　Docker Daemon创建容器对象

Docker容器的运行并非只是简单地调用内核接口，Docker的设计者有意将配置信息与启动容器的操作进行区分，实现数据与逻辑的有机分离。

/ Create creates a new container from the given configuration with a given name.￼


     if warnings, err = daemon.mergeAndVerifyConfig(config, img); err != nil {￼
          return nil, nil, err￼
     }￼

### 12.3.1　LookupImage

repos, tag := parsers.ParseRepositoryTag(name)￼
     if tag == "" {￼
          tag = DEFAULTTAG￼
     }￼
     img, err := store.GetImage(repos, tag)￼

### 12.3.2　CheckDepth

若用户指定的Docker镜像加上所有祖先镜像之后，镜像layer总数达到很大数量，则通过aufs对所有layer执行Union Mount操作将会造成不小的性能问题。最大的问题就是：系统实现文件读写时，查找文件inode的时间变长。

if depth+2 >= MaxImageDepth {￼
               return fmt.Errorf("Cannot create container with more than %!d(MISSING) ￼
                            parents", MaxImageDepth)￼
     }￼

Docker Daemon启动容器前，需要通过镜像为容器准备rootfs，由于通过aufs联合layer时，会在镜像的top layer之上，再叠加两个layer，分别为init layer和容器的read-write layer。

最大镜像深度MaxImageDepth。MaxImageDepth的值为常量127。

### 12.3.3　mergeAndVerifyConfig

Docker镜像含有config信息，docker run命令传入的许多参数信息也是以runconfig.Config的形式转交至Docker Daemon。
两份Config信息如何在运行容器时各自起到相应的作用，是Docker Daemon应该考虑的问题。而mergeAndVerifyConfig函数则巧妙地将两者有机结合在一起，实现函数为runconfig包中的Merge函数。

if img.Config != nil {￼
          if err := runconfig.Merge(config, img.Config); err != nil {￼
               return nil, err￼
          }￼
     }￼

if len(config.Entrypoint) == 0 && len(config.Cmd) == 0 {￼
          return nil, fmt.Errorf("No command specified")￼
     }￼

函数mergeAndVerifyConfig除了merge之外，自然还包括Verify和Config，若合并之后的config对象中不存在Entrypoint并且也没有Cmd，则说明整个容器没有启动入口，Docker Daemon必须对这种情况返回错误。

### 12.3.4　newContainer

Path属性的值为entrypoint。众所周知，entrypoint是Docker容器运行时非常重要的概念。用户一旦指定entrypoint，则Docker运行容器时，可以首先执行entrypoint的内容（一般为脚本），而entrypoint的最后一行脚本命令一般是exec用户指定的Cmd，届时才开始运行用户指定的应用进程。这样的好处很明显，启动容器时的工作分为两部分：第一，运行entrypoint，完成与系统配置或初始化相关的工作；第二，运行Cmd，开始执行用户应用程序。

### 12.3.5　createRootfs

graph包的SetupInitLayer的作用是：在镜像基础上挂载一系列与镜像无关而与容器运行环境相关的目录和文件，如/dev/pts、proc、.dockerinit、/etc/hosts、etc/hostname以及/etc/resolv.conf等。

### 12.3.6　ToDisk

Docker Daemon对于每一个创建的容器，均会将其json化后持久化至本地文件系统中。ToDisk函数的功能正是如此。

data, err := json.Marshal(container)￼
     if err != nil {￼
          return err￼
     }￼
     pth, err := container.jsonPath()￼
     if err != nil {￼
          return err￼
     }￼
     err = ioutil.WriteFile(pth, data, 0666)￼
     if err != nil {￼
          return err￼
     }￼
     return container.WriteHostConfig()￼

pth，err：=container.jsonPath（）即获取json文件的放置目录/var/lib/docker/container/containers/<container_id>。

### 12.3.7　Register

container对象创建之后，并根据一些规则对其进行预处理之后，Docker Daemon将container对象注册至daemon的containers属性。注册的内容为container对象的容器ID。注册的意义在于，Docker Daemon此后可以通过daemon对象调度并管理这个有效的container对象。

### 12.4　Docker Daemon启动容器

就顺序而言，Docker Daemon执行Start函数，包含以下11个步骤。

### 12.4.1　setupContainerDns

Docker Daemon启动容器的第一个步骤就是配置容器的DNS服务。
一旦Docker容器的网络模式为host模式，则容器和宿主机共享同一个网络命名空间，容器无须为自身的DNS服务考虑，完全使用宿主机的DNS服务。如若不然，Docker Daemon则需要为容器内部配置DNS服务。

当用户使用docker run命令启动容器时，若使用-dns参数，则表明用户需要为容器指定DNS地址（在Docker Daemon中对应的数据结构为对象config.Dns），Docker Daemon优先满足Docker用户指定DNS的需求。若用户没有指定-dns参数，则Docker Daemon会为容器配置Docker Daemon的DNS地址

用户启动Docker Daemon时，若没有指定-dns参数，并且宿主机/etc/resolv.conf文件中第一条记录的DNS地址为127.0.0.1或者127.0.1.1（容器内部使用这两个DNS地址无效），则Docker Daemon采用默认的DNS地址8.8.8.8和8.8.4.4。

特殊情况下，8.8.8.8和8.8.4.4并不能提供稳定可靠的域名解析服务，很大程度上影响Docker容器的使用。若用户没有指定任何-dns参数，且宿主机的resolv.conf中第一条DNS记录地址不为127.0.0.1和127.0.1.1，则Docker Daemon会为容器配置宿主机的DNS地址。

### 12.4.3　initializeNetworking

函数intializeNetworking的作用与实现在7.4.2节中分析过。总而言之，Docker Daemon需要处理用户指定的网络模式（bridge、host、other container和none模式中的一种），进行相应的网络配置与初始化，最终将所有初始化后的信息都存入container对象中。

### 12.4.4　verifyDaemonSetting

log.Infof("WARNING: Your kernel does not support memory limit capabilities. Limitation discarded.")￼

验证的维度主要有三个：系统内核是否支持cgroup内存限制，系统内核是否支持cgroup的swap内存限制，以及系统内核是否支持网络接口间IPv4数据包的转发。

如何判断系统内核在这三个维度上是否支持。对于cgroup内存限制和swap内存限制，Docker Daemon在启动时即会前往cgroup文件系统的挂载点扫描，一旦发现memory.limit_in_bytes文件和memory.soft_limit_in_bytes文件，则说明系统内核支持cgroup内存限制；

### 12.4.5　prepareVolumesForContainer

用户可以为其配置两种类型的volume，第一种是bind-mount类型的volume，即用户指定容器外部目录挂载到容器内部的指定目录；另一种是data volume，即用户只指定volume在容器内部的目录，关于volume在宿主机上的目录，Docker Daemon接管创建工作。

Docker Daemon使用docker rm命令删除容器时，如果不指定-v参数，则默认不会删除容器的data volume，即目录/var/lib/docker/vfs/dir/<some_id>。长此以往，如果不对data volume的磁盘空间进行清理，很容易消耗磁盘空间。

结构体Volume的定义如下：
type Volume struct {￼
     HostPath    string￼
     VolPath     string￼
     Mode        string￼
     isBindMount bool￼
}

### 12.4.6　setupLinkedContainers

setupLinkedContainers
容器间的link操作允许容器通过环境变量的形式发现另一个容器，并在这两个容器间安全传输信息。用户在启动容器时可以通过设置--link参数来完成这项工作，如docker run--link db：aliasubuntu：14.04，作用是启动容器时为容器db设置一个别名alias，并将别名alias以及容器db的网络信息配置到新启动容器的环境变量中。

第一条规则用于接受父容器到子容器指定端口的连接，其中也指定了协议类型，并且表明是容器间的通信，理由是从bridgeIface（docker0）发出，从bridgeIface（docker0）接收。


步骤4，将link对象Env化，只有最终的Env信息，才能被容器内部的进程使用。实现源码为link.ToEnv（）

### 12.4.9　populateCommand

Docker Daemon启动容器的多个阶段都对container对象的Config属性进行配置和处理，而最终这些变化都会体现在Command对象实例中，如initializeNetworking完成的网络配置将修改Command对象中的网络部分

### 12.4.10　setupMountsForContainer

mounts := []execdriver.Mount{￼
          {container.ResolvConfPath, "/etc/resolv.conf", true, true},￼
     }￼
     if container.HostnamePath != "" {￼
               mounts = append(mounts, execdriver.Mount{container.HostnamePath, ￼
          "/etc/hostname", true, true})￼
     }￼
     if container.HostsPath != "" {￼
               mounts = append(mounts, execdriver.Mount{container.HostsPath, "/etc/hosts", true, true})￼
     }￼

// want this new mount in the container￼
     for r, v := range container.Volumes {￼
               mounts = append(mounts, execdriver.Mount{v, r, container.VolumesRW[r], false})￼
     }￼
     container.command.Mounts = mounts￼

容器内部的挂载点一般包括/etc/resolv.conf、/etc/hostname、/etc/hosts以及所有的volume等。最终，mounts对象赋予container.command的Mounts属性，为Command类型实例服务。

### 12.4.11　waitForStart

创建容器对象，仅仅是为Docker容器组织了必需的配置信息，并未将配置信息落实至Docker容器运行环境中的具体资源与能力。因此，libcontainer在启动容器时，一方面为容器初始化具体的物理资源并提供相应的容器能力，另一方面启动容器的主进程。

### 12.5　总结

对于Docker用户而言，仅仅能感受到自身应用的正常运行。然而，对于Docker Daemon而言，需要完成的工作要远比这复杂，从最初的镜像查找，到rootfs配备，还有网络、volume、容器link等配置信息的收集，以及最后启动代表容器主进程的Command命令。

### 13.2　dockerinit介绍

可以认为dockerinit是Docker容器中的init进程。Linux操作系统中，init进程是一个非常重要的进程，它负责初始化系统，并且是所有用户进程的祖先进程。相同的概念，在Docker容器中也有很好的体现，dockerinit作为Docker容器中的第一个进程，同样扮演初始化容器的角色，同样是Docker容器内所有进程的祖先进程。

### 13.2.1　dockerinit初始化内容

dockerinit需要为容器初始化网络命名空间，配置独立的网络栈；
□ 挂载资源。容器都有独立的mount namespace，初始化挂载资源包括设置容器内部的设备、挂载点以及文件系统等；
□ 用户设置。容器都属于一个用户，dockerinit负责为容器设置组（group）、组ID（GID）与用户ID（UID）；
□ 环境变量。容器中的环境变量使得容器内进程拥有更多的运行参数。

容器Capability。容器中用户使用的内核与宿主机无差别，Linux的Capability机制则可以确保容器内进程以及文件的Capability得到限制。

### 13.2.2　dockerinit与Docker Daemon

dockerinit是Docker容器中的第一个进程。Linux操作系统中，进程的诞生往往都由fork系统调用产生，dockerinit进程自然也不会例外。如此说来，dockerinit的父进程最有可能是Docker Daemon，现实情况的确如此。

既然是父子关系，dockerinit被派生出来时，Docker Daemon即可以将众多的配置信息通过很多有效的途径传递至dockerinit。为了使得dockerinit得到配置信息，Docker Daemon将容器所有的Config配置先json化之后，存入本地文件系统中，而dockerinit在初始化mount命令空间前，提取这部分信息。另外，Docker Daemon为dockerinit传递命令参数也是一种简单的方式。

### 13.3.1　createCommand分析

由于在dockerinit的运行初期仍然需要与之通信，故Docker Daemon创建了一个管道，并将管道的一端交给dockerinit。

.c.Env。Env用于指定dockerinit进程运行时的环境变量。故用户运行docker run命令时通过-e设定的ENV参数或者Dockerfile中的ENV参数，都会在此时用来设定dockerinit进程的环境变量。

### 13.3.2　namespace.exec

/ create a pipe so that we can syncronize with the namespaced process and￼
     // pass the veth name to the child￼
     syncPipe, err := syncpipe.NewSyncPipe()￼

创建syncPipe的作用是：使得Docker Daemon与dockerinit可以通过管道的形式同步资源。由于一旦dockerinit进程启动，dockerinit进程的运行就处于多个全新的namespace内，此时Docker Daemon与dockerinit处于相互隔离的namespace中，两者的通信变得不便（虽然dockerinit处于全新的网络命名空间下，但网络命名空间下网络栈仍未初始化，没有网络接口用于通信），故Docker采用更为底层的管道机制完成操作系统上分别处于不同namespace进程的通信。syncPipe主要用于Docker Daemon向dockerinit传递容器信息，如容器内网络设备的信息等。

### 13.4.1　reexec.Init（）的分析

首先进入reexec包中的Init（）函数定义，源码实现位于./docker/docker/reexec/reexec.go#L23-L32，如下：


### 13.4.2　dockerinit的执行流程

dockerinit的执行流程，可以说就是initializer的执行流程。在功能方面，initializer依旧完成一部分初始化工作，并在最后把控制权交给libcontainer的namespace，由后者完成剩余容器的初始化工作。

root    = flag.String("root", ".", "root path for configuration files")￼

if err := namespaces.Init(container, rootfs, *console, syncPipe, flag.Args()); err != nil {￼
          writeError(err)￼

2）声明libcontainer.Config实例，并通过解析container.json文件，获取实例内容。文件container.json：execdriver中run函数执行namespaces.exec函数之前，将libcontainer.Config对象实例持久化至本地目录/var/lib/docker/execdriver/native/<c.ID>/container.json；

）通过libcontainer中namespaces包的Init函数，最终完成容器初始化工作。

### 13.5　libcontainer的运行

libcontainer是一套容器技术的实现方案，借助于libcontainer的调用，可以完成容器的创建与管理。dockerinit就通过libcontainer来完成容器的创建与初始化。

dockerinit通过namespaces包的Init函数来调用libcontainer的API接口并完成所有工作，但是Init完成的内容绝不仅仅只有与Linux namespace相关的内容。Init是全新namespace下运行的第一部分内容，同时会完成mount信息、容器用户以及网络等多方面的配置。

// We always read this as it is a way to sync with the parent as well￼
     var networkState *network.NetworkState￼
     if err := syncPipe.ReadFromParent(&networkState); err != nil {￼
          return err￼
     }￼
￼

dockerinit通过向syncPipe读取父进程Docker Daemon传递的内容，完成两者信息的同步。Docker Daemon与dockerinit的同步流程如图13-1所示。

1）SetupCgroups，设置dockerinit进程的cgroups，完成dockerinit进程的资源限制；
2）InitializeNetworking，创建dockerinit所在容器的网络栈资源；
3）syncPipe.ReadFromChild，与子进程dockerinit同步。

### 13.5.1　Docker Daemon设置cgroups参数

13.5.1　Docker Daemon设置cgroups参数
SetupCgroups通过类型为libcontainer.Config的container对象以及dockerinit进程在宿主机上的PID号，为dockerinit设置cgroups参数，从此cgroups对dockerinit进程的资源限制生效，而之后dockerinit进程派生出的子进程也将受到相同的资源限制。

### 13.5.2　Docker Daemon创建网络栈资源

原则上，在go语言的运行时中，并不具备跨namespace的资源操纵能力，故Docker Daemon将创建完毕的网络栈资源通过syncPipe的形式传递给处于新建namespace下的dockerinit，由dockerinit负责接收并初始化新建namespace下的网络栈。关于InitializeNetworking函数的具体实现，可参考第7章。

### 13.5.3　dockerinit配置网络栈

虽然Docker Daemon已经完成自己的使命，但是dockerinit依旧处于阻塞状态，而且等待着SyncPipe中Docker Daemon传来的内容。只要从syncPipe中读到内容，dockerinit即从阻塞状态恢复执行。在设置SID（Session ID）、处理tty等一系列操作之后，dockerinit最为重要的工作就是配置网络栈，即通过Docker Daemon在syncPipe中传递来的NetworkState信息，配置容器所在网络命名空间下的网络栈。

type NetworkState struct {￼
     // The name of the veth interface on the Host.￼
     VethHost string `json:"veth_host,omitempty"`￼
     // The name of the veth interface created inside the container for the child.￼
     VethChild string `json:"veth_child,omitempty"`￼
     // Net namespace path.￼
     NsPath string `json:"ns_path,omitempty"`￼
}

VethHost和VethChild属于bridge网络模式下的veth pair，前者是宿主机上依附在网桥docker0上的veth，而后者是容器内部的veth；NsPath属于other container网络模式下其他容器的网络命名空间路径。之所以NetworkState中没有host网络模式以及none网络模式的信息，原因很简单，host网络模式不需要为容器创建新的网络命名空间，而none网络模式不做任何网络配置。

SetupNetwork函数正是利用NetworkState中的网络接口（veth pair）配置信息，在容器的网络命名空间下初始化内部网络接口，将其改名为eth0，并对网络接口的MTU、IP地址以及默认网关地址进行配置。

if err := SetInterfaceIp(defaultDevice, config.Address); err != nil {￼
          return fmt.Errorf("set %!s(MISSING) ip %!s(MISSING)", defaultDevice, err)￼
     }￼
     if err := SetMtu(defaultDevice, config.Mtu); err != nil {￼
          return fmt.Errorf("set %!s(MISSING) mtu to %!d(MISSING) %!s(MISSING)", defaultDevice, config.Mtu, err)￼
     }￼
     if err := InterfaceUp(defaultDevice); err != nil {￼
          return fmt.Errorf("%!s(MISSING) up %!s(MISSING)", defaultDevice, err)￼
     }￼

分析以上Initialize函数的实现，很快可以总结出以下执行流程：
1）将名为VethChild的网络接口停止运行；
2）将名为VethChild的网络接口改名为defaultDevice，即eth0；
3）为网络接口eth0设置IP地址；
4）为网络接口eth0设置MTU值；
5）启动网络接口eth0；
6）若需设置网关地址，则为网络接口设置默认网关地址。

通过以上6个步骤的初始化与配置，容器网络命名空间内的网络接口即成功运行，为容器内部提供完整的网络栈，并且该veth网络接口对其他容器不可见，处于独立的容器环境中。

### 13.5.5　dockerinit完成namespace配置

if err := utils.CloseExecFrom(3); err != nil {￼
          return fmt.Errorf("close open file descriptors %!s(MISSING)", err)￼
     }￼

1）CloseExecFrom（3），关闭除标准输入（文件描述符为0）、标准输出（文件描述符为1）、标准错误（文件描述符为2）之外所有打开的文件描述符（文件描述符大于等于3）；

### 13.5.6　dockerinit执行用户命令Entrypoint

system.Execv函数的定义位于./docker/libcontainer/system/linux.go#L11-L18，如下：
func Execv(cmd string, args []string, env []string) error {￼
     name, err := exec.LookPath(cmd)￼
     if err != nil {￼
          return err￼
     }￼
     return syscall.Exec(name, args, env)￼
}

### 13.6　总结

Docker容器的成功创建，可以说是Docker Daemon与dockerinit协同合作的结果。Docker Daemon创建一个初步的容器环境，并将容器环境的初始化工作交给dockerinit来完成，同时两者之间完成起码的通信。dockerinit的运行起到一个承前启后的作用，它不仅接受Docker Daemon的使命，完成容器环境的初始化，同时还负责将容器的运行转变为用户应用程序Cmd的运行，并将容器交付给Docker用户。

### 第14章　libcontainer介绍

简单而言，libcontainer是一套实现容器管理的Go语言解决方案。这套解决方案实现过程中使用了Linux内核特性namespace与cgroup，同时还采用了capability与文件权限控制等其他一系列技术。基于这些特性，除了创建容器之外，libcontainer还可以完成管理容器生命周期的任务。

### 14.2　Docker、libcontainer以及LXC的关系

图14-1　Docker、LXC与libcontainer的关系

### 14.3　libcontainer模块分析

14.3　libcontainer模块分析
Docker容器范畴内一切与Linux内核相关的技术实现，都可以认为是通过libcontainer来完成的。libcontainer指定了所有与容器相关的配置信息，首先自然是namespace与cgroup信息，除此之外，还有容器的网络栈配置信息，当然，还包括容器的挂载点配置、设备信息等。为实现与Linux内核的通信，libcontainer还包含模块netlink，完成内核态进程与用户态进程的IPC（进程间通信）等。


### 14.3.1　namespace

由于容器的本质也是进程，故在派生进程时，Docker Daemon传入了与namespace相关的flag参数，实现namespace的创建，确保容器（进程）被执行之后，也就是在进程在运行时达到namespace隔离的效果。

namespace的完全生效，并不仅仅依靠namespace的创建，同时还需要对每一个namespace进行初始化。如mount命名空间、网络命名空间、uts命名空间等命名空间需要Docker Daemon提供初始化信息，而其他命令空间则采用默认值。

Docker Daemon将容器所需要的资源配置信息通过两种途径传递至容器，分别为：携带网络资源信息的syncpipe以及Docker Daemon持久化到宿主机的container.json文件。

### 14.3.2　cgroup

Freezer则可以使容器挂起，节省CPU资源，Docker命令中pause与unpause命令即采用了cgroup的Freezer子系统。需要注意的是，容器进程组挂起，并不意味着进程已经终止，从Linux内核的角度来看，被挂起的进程拥有完整的task_struct，占用相应的内存，但是Freezer子系统保证容器中的进程不会被CPU调度到。

### 14.3.3　网络

libcontainer为了标识网络接口的数据接口，抽象出network结构体，位于./docker/libcontainer/network/types.go#L7-L30，定义如下：
type Network struct {￼
     // Type sets the networks type, commonly veth and loopback￼
     Type string `json:"type,omitempty"`￼
￼
     // Path to network namespace￼
     NsPath string `json:"ns_path,omitempty"`￼
￼
     // The bridge to use.￼
     Bridge string `json:"bridge,omitempty"`￼

### 14.3.5　设备

默认情况下，libcontainer会为容器创建某些必备的设备，如/dev/null、/dev/zero、/dev/full、/dev/tty、/dev/urandom、/dev/console等。

### 14.3.6　nsinit

nsinit是一个功能强大的应用程序，该应用程序巧妙利用了libcontainer，使得容器的创建变得异常简单。若需要创建一个容器，则按照libcontainer的API接口，必须要向其提供容器的rootfs以及容器的参数配置。因此，nsinit的运行需要提供一个rootfs以及一个容器的配置文件container.json。JSON文件container.json需要处于rootfs的根目录下，并且文件中含有的配置信息需要包括：容器的环境变量、网络和容器Capability等内容。

### 14.3.7　其他模块

由于Docker的namespace没有完全实现用户命名空间，因此Docker容器中的超级管理员用户root与宿主机上的root用户是同一个用户。没有隔离用户，的确是一个很大的安全隐患，然而，Docker通过Capability特性尽量降低这方面的影响。可以说，使用了Capability特性之后，在权限方面，Docker容器内的root用户和宿主机上的root用户有很大的差别，容器内root用户的权限自然会小很多。与此同时，Docker还支持SELinux以及Apparmor，为容器的安全保驾护航。

### 14.4　总结

libcontainer作为容器技术的一种解决方案，很好地衔接了Docker Daemon这个管理引擎与Linux的内核特性。更为直接的评价是：Docker的运行不能没有libcontainer，而在libcontainer的基础上，任何开发者或团队都可以开发或者再造类似Docker的容器管理引擎。相信Docker官方也希望更多开发者参与libcontainer的维护工作，并努力推动其发展，使libcontainer成为容器技术的标准，从而在容器市场上占据更大的份额。

### 第15章　Swarm架构设计与实现

Swarm是Docker公司在2014年12月初新发布的容器管理工具。和Swarm一起发布的Docker管理工具还有Machine以及Compose。
Swarm是一套较为简单的工具，用于管理Docker集群，使得Docker集群暴露给用户时相当于一个虚拟的整体。Swarm使用标准的Docker API接口作为其前端访问入口

### 第16章　Machine架构设计与实现

Machine使得Docker的部署异常简易，不论是用户的单个主机，还是用户的数据中心，以及可能是第三方云平台提供商提供的云主机。

### 16.2.4　Driver

Machine使用不同的Driver来完成任务，openstack driver则完全适配OpenStack平台的接口，完成OpenStack平台上虚拟机的创建；对于VirtualBox这种虚拟化技术，Machine采用virtualbox driver，进而使用VBoxManage管理工具完成VirtualBox下虚拟机的申请……

### 16.2.5　Provisioner

Machine的使命就是在裸环境中创建装有Docker的机器，并统一化管理。因此，通过Driver创建虚拟机之后，Provisioner完成Docker安装与配置的任务。

### 16.3　Machine与Swarm的结合

Machine在Docker体系中的作用是：创建装有Docker的虚拟机。

### 第17章　Compose架构设计与实现

在2014年7月双方爆出新闻：Docker收购Fig。收购完成之后，Fig改名为Compose，命令改为docker-compose。

### 17.2　Compose介绍

Compose则将所有容器参数通过精简的配置文件来存储，用户最终通过简短有效的docker-compose命令管理该配置文件，完成Docker容器的部署。

### 17.3　Compose架构

Compose最终必须与Docker Daemon建立连接，并在该连接之上完成Docker的API请求。事实上，Compose借助docker-py来完成此任务。docker-py是一个使用Python开发并调用Docker DaemonAPI的Docker Client包。

Service，代表配置文件中的每一项服务。何为服务？即以容器为粒度，用户需要Compose所完成的任务。再次以本节前面配置文件为例，配置文件中共包含了两个service，第一个名为web，第二次名为db。一个service包含的内容，无非是用户对服务的定义。定义一个服务，可以为服务容器指定镜像，设定构建的Dockerfile，可以为其指定link的其他容器，还可以为其指定端口的映射等。

容器依赖关系会存在以下三种情况：存在links参数，容器的启动需要链接到另一个容器；存在volumes_from参数，容器的启动需要挂载另一个容器的data volume；存在net参数，容器的启动过程中网络模式采用other container模式，使用另一个容器的网络栈。

### 17.4.1　Compose单机能力

Compose带来的是单机的便利，面对跨宿主机的容器部署与管理，Compose的能力依然薄弱。