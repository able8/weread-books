## Kubernetes网络权威指南：基础、原理与实践
> 杜军

### 自序

云计算的世界里，计算最基础，存储最重要，网络最复杂。

本书特别注重提供理解容器网络所必需的基础知识，会由浅入深地从架构、使用、实现原理等多方面展开，试图为读者呈现整个云原生网络的知识体系。

全书的脉络是：以Linux网络虚拟化基础作为“暖场嘉宾”，以Docker原生的容器网络“承前启后”，随后是主角Kubernetes网络“粉墨登场”，在各类CNI插件“沙场点兵”过后，以代表容器下半场的服务网格Istio“谢幕”。

### 第1章 夯实基础：Linux网络虚拟化

Linux的namespace（名字空间）的作用就是“隔离内核资源”。在Linux的世界里，文件系统挂载点、主机名、POSIX进程间通信消息队列、进程PID数字空间、IP地址、user ID数字空间等全局系统资源被namespace分割，装到一个个抽象的独立空间里。

Linux的namespace给里面的进程造成了两个错觉：
（1）它是系统里唯一的进程。
（2）它独享系统的所有资源。
默认情况下，Linux进程处在和宿主机相同的namespace，即初始的根namespace里，默认享有全局系统资源。

Docker容器作为一项轻量级的虚拟化技术，它的隔离能力来自Linux内核的namespace技术。

由于每个容器都有自己的（虚拟）网络设备，并且容器里的进程可以放心地绑定在端口上而不必担心冲突，这就使得在一个主机上同时运行多个监听80端口的Web服务器变为可能，

与其他namespace需要读者自己写C语言代码调用系统API才能创建不同，network namespace的增删改查功能已经集成到Linux的ip工具的netns子命令中，因此大大降低了初学者的体验门槛。

面的命令将创建一对虚拟以太网卡，然后把veth pair的一端放到netns1 network namespace。

我们创建了veth0和veth1这么一对虚拟以太网卡。在默认情况下，它们都在主机的根network namespce中，将其中一块虚拟网卡veth1通过ip link set命令移动到netns1 network namespace。

我们知道通过Linux的network namespace技术可以自定义一个独立的网络栈，简单到只有loopback设备，复杂到具备系统完整的网络能力，这就使得network namespace成为Linux网络虚拟化技术的基石——不论是虚拟机还是容器时代

### 1.2 千呼万唤始出来：veth pair

veth是虚拟以太网卡（Virtual Ethernet）的缩写。veth设备总是成对的，因此我们称之为veth pair。veth pair一端发送的数据会在另外一端接收，非常像Linux的双向管道。根据这一特性，veth pair常被用于跨network namespace之间的通信，即分别将veth pair的两端放在不同的namespace里

如果容器需要访问网络，则需要使用网桥等技术将veth pair设备接收的数据包通过某种方式转发出去。

创建的veth pair在主机上表现为两块网卡，我们可以通过ip link命令查看：

### 1.3 连接你我他：Linux bridge

网桥是二层网络设备，两个端口分别有一条独立的交换信道，不共享一条背板总线，可隔离冲突域。网桥比集线器（hub）性能更好，集线器上各端口都是共享同一条背板总线的。后来，网桥被具有更多端口、可隔离冲突域的交换机（switch）所取代。

虚拟机通过tun/tap或者其他类似的虚拟网络设备，将虚拟机内的网卡同br0连接起来，这样就达到和真实交换机一样的效果，虚拟机发出去的数据包先到达br0，然后由br0交给eth0发送出去，数据包都不需要经过host机器的协议栈，效率高

由于目的IP是外网IP，且host机器开启了IP forward功能，数据包会通过eth0发送出去。因为容器所分配的网段一般都不在物理网络网段内（在我们的例子中，物理网络网段是10.20.30.0/24），所以一般发出去之前会先做NAT转换（NAT转换需要自己配置，可以使用iptables，1.5节会介绍iptables）。

混杂模式（Promiscuous mode），简称Promisc mode，俗称“监听模式”。混杂模式通常被网络管理员用来诊断网络问题，但也会被无认证的、想偷听网络通信的人利用。根据维基百科的定义，混杂模式是指一个网卡会把它接收的所有网络流量都交给CPU，而不是只把它想转交的部分交给CPU。在IEEE 802定的网络规范中，每个网络帧都有一个目的MAC地址。在非混杂模式下，网卡只会接收目的MAC地址是它自己的单播帧，以及多播及广播帧；在混杂模式下，网卡会接收经过它的所有帧！

·ifconfig eth0，查看eth0的配置，包括混杂模式。当输出包含PROMISC时，表明该网络接口处于混杂模式。

### 1.4 给用户态一个机会：tun/tap设备

tun/tap设备到底是什么？从Linux文件系统的角度看，它是用户可以用文件句柄操作的字符设备；从网络虚拟化角度看，它是虚拟网卡，一端连着网络协议栈，另一端连着用户态程序。

tun/tap设备可以将TCP/IP协议栈处理好的网络包发送给任何一个使用tun/tap驱动的进程，由进程重新处理后发到物理链路中。tun/tap设备就像是埋在用户程序空间的一个钩子，我们可以很方便地将对网络包的处理程序挂在这个钩子上，OpenVPN、Vtun、flannel都是基于它实现隧道包封装的。

普通的物理网卡通过网线收发数据包，而tun设备通过一个设备文件（/dev/tunX）收发数据包。所有对这个文件的写操作会通过tun设备转换成一个数据包传送给内核网络协议栈。当内核发送一个包给tun设备时，用户态的进程通过读取这个文件可以拿到包的内容。

·tun设备的/dev/tunX文件收发的是IP包，因此只能工作在L3，无法与物理网卡做桥接，但可以通过三层交换（例如ip_forward）与物理网卡连通；
·tap设备的/dev/tapX文件收发的是链路层数据包，可以与物理网卡做桥接。

（3）tun0网卡收到数据包之后，发现网卡的另一端被App2打开了（这也是tun/tap设备的特点，一端连着协议栈，另一端连着用户态程序），于是将数据包发送给App2。

VPN网络的报文真正从物理网卡出去要经过网络协议栈两次，因此会有一定的性能损耗。另外，经过用户态程序的处理，数据包可能已经加密，包头进行了封装，所以第二次通过网络栈内核看到的是截然不同的网络包。

### 1.5 iptables

·raw表：iptables是有状态的，即iptables对数据包有连接追踪（connection tracking）机制，而raw是用来去除这种追踪机制的；

### 1.7 Linux隧道网络的代表：VXLAN

VXLAN（Virtual eXtensible LAN，虚拟可扩展的局域网），是一种虚拟化隧道通信技术。它是一种overlay（覆盖网络）技术，通过三层的网络搭建虚拟的二层网络。

VXLAN是在底层物理网络（underlay）之上使用隧道技术，依托UDP层构建的overlay的逻辑网络，使逻辑网络与物理网络解耦，实现灵活的组网需求。它不仅能适配虚拟机环境，还能用于容器环境。由此可见，VXLAN这类隧道网络的一个特点是对原有的网络架构影响小，不需要对原网络做任何改动，就可在原网络的基础上架设一层新的网络。

VXLAN的报文就是MAC in UDP，即在三层网络的基础上构建一个虚拟的二层网络。为什么这么说呢？VXLAN的封包格式显示原来的二层以太网帧（包含MAC头部、IP头部和传输层头部的报文），被放在VXLAN包头里进行封装，再套到标准的UDP头部（UDP头部、IP头部和MAC头部），用来在底层网络上传输报文。

VXLAN的配置管理使用iproute2包，这个工具是和VXLAN一起合入内核的，我们常用的ip命令就是iproute2的客户端工具。VXLAN要求Linux内核版本在3.7以上，最好为3.9以上，所以在一些旧版本的Linux上无法使用基于VXLAN的封包技术。

·每个VXLAN报文都有额外的50字节开销，加上VXLAN字段，开销要到54字节。这对于小报文的传输是非常昂贵的操作。试想，如果某个报文应用数据才几个字节，那么原来的网络头部加上VXLAN报文头部就有100字节的控制信息；
·每个VXLAN报文的封包和解包操作都是必需的，如果用软件来实现这些步骤，则额外的计算量是不可忽略的影响因素。

### 1.8 物理网卡的分身术：Macvlan

Macvlan接口可以看作是物理以太网接口的虚拟子接口。Macvlan允许用户在主机的一个网络接口上配置多个虚拟的网络接口，每个Macvlan接口都有自己的区别于父接口的MAC地址，并且可以像普通网络接口一样分配IP地址。因此，使用Macvlan技术带来的效果是一块物理网卡上可以绑定多个IP地址，每个IP地址都有自己的MAC地址。

Macvlan的主要用途是网络虚拟化（包括容器和虚拟机）。

父接口发送到外部网络，广播帧将会被洪泛到连接在“网桥”上的所有其他子接口和物理接口。网桥带双引号是因为实际上并没有网桥实体的产生，而是指在这些网卡之间数据流可以实现直接转发，这有点类似于Linux网桥。但Macvlan的bridge模式和Linux网桥不是一回事，它不需要学习MAC地址，也不需要生成树协议（STP），因此性能要优于Linux网桥

Macvlan和overlay的网络作用范围不一样。overlay是全局作用范围类型的网络，而Macvlan的作用范围只是本地。举例来说，全局类型的网络可以作用于一个Docker集群，而本地类型的网络只作用于一个Docker节点。

·IEEE 802.11标准（即无线网络）不喜欢同一个客户端上有多个MAC地址，这意味着你的Macvlan子接口没法在无线网卡上通信。

### 1.9 Macvlan的救护员：IPvlan

与Macvlan类似，IPvlan也是从一个主机接口虚拟出多个虚拟网络接口。区别在于IPvlan所有的虚拟接口都有相同的MAC地址，而IP地址却各不相同。因为所有的IPvlan虚拟接口共享MAC地址，所以特别需要注意DHCP使用的场景。

### 第2章 饮水思源：Docker网络模型简介

我们将软件运行时依赖的环境统称为运行时（runtime）。既

虚拟机的快照（snapshot）就是带环境安装的一种解决方案。虽然用户可以通过虚拟机还原软件的原始环境，但每个虚拟机都包含完整的操作系统，因此有资源占用多、启动慢等缺点。

容器不是模拟一个完整的操作系统，而是对进程进行隔离，隔离用到的技术就是1.1节介绍的Linux namespace。对容器里的进程来说，它接触到的各种资源看似是独享的，但在底层其实是隔离的。容器是进程级别的隔离技术，因此相比虚拟机有启动快、占用资源少、体积小等优点。

容器包含应用程序和所依赖的软件包，并且不同容器之间共享内核，这与虚拟机相比大大节省了额外的资源占用。

虚拟机有Hypervisor层，Hypervisor即虚拟化管理软件，是整个虚拟机的核心所在。它为虚拟机提供了虚拟的运行平台，管理虚拟机的操作系统运行。每台虚拟机都有自己的操作系统及系统库。
容器没有Hypervisor这一层，也没有Hypervisor带来性能的损耗，每个容器和宿主机共享硬件资源及操作系统

Docker容器有Docker Engine这一层。其实Docker Engine远远比Hypervisor轻量，它只负责对Linux内核namespace API的封装和调用，真正的内核虚拟化技术是由Linux提供的。容器的轻量带来的一个好处是资源占用远小于虚拟机。同样的硬件环境，可以运行容器的数量远大于虚拟机，这对提供系统资源利用率非常有用。

每台虚拟机都有一个完整的Guest OS，Guest OS能为应用提供一个更加隔离和安全的环境，不会因为应用程序的漏洞给宿主机造成任何威胁。

传统虚拟化技术是对硬件资源的虚拟，容器技术则是对进程的虚拟，从而提供更轻量级的虚拟化，实现进程和资源的隔离。从架构来看，容器比虚拟机少了Hypervisor层和Guest OS层，使用Docker Engine进行资源分配调度并调用Linux内核namespaceAPI进行隔离，所有应用共用主机操作系统

### 2.2 打开万花筒：Docker的四大网络模式

docker run命令创建Docker容器时，可以使用--network选项指定容器的网络模式。Docker有以下4种网络模式：

在安装完Docker之后，Docker Daemon会在宿主机上自动创建三个网络，分别是bridge网络、host网络和none网络，可以使用docker network ls命令查看

Docker在安装时会创建一个名为docker0的Linux网桥。bridge模式是Docker默认的网络模式，在不指定--network的情况下，Docker会为每一个容器分配network namespace、设置IP等，并将Docker容器连接到docker0网桥上。严谨的表述是，创建的容器的veth pair中的一端桥接到docker0上。

连接在docker0上的所有容器的默认网关均为docker0，即访问非本机容器网段要经过docker0网关转发，而同主机上的容器（同网段）之间通过广播通信。

发到主机上要访问Docker容器的报文默认也要经过docker0进行广播

连接到host网络的容器共享Docker host的网络栈，容器的网络配置与host完全一样。host模式下容器将不会获得独立的network namespace，而是和宿主机共用一个network namespace。容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口。

host模式下的容器可以看到宿主机的所有网卡信息，甚至可以直接使用宿主机IP地址和主机名与外界通信，无须额外进行NAT，也无须通过Linux bridge进行转发或者进行数据包的封装。

host模式有利有弊，优点是没有性能损耗且配置方便，缺点也很明显，例如：
·容器没有隔离、独立的网络栈：容器因与宿主机共用网络栈而争抢网络资源，并且容器崩溃也可能使主机崩溃，导致网络的隔离性不好；
·端口资源冲突：宿主机上已经使用的端口就不能再用了。

none模式下的容器只有lo回环网络，没有其他网卡。none模式网络可以在容器创建时通过--network=none指定。这种类型的网络没有办法联网，属于完全封闭的网络。唯一的用途是客户有充分的自由度做后续的配置。

### 2.3 最常用的Docker网络技巧

默认Docker分配的IP地址范围是172.l7.0.0/16，那么第一个IP地址172.l7.0.1是分配给docker0的，第一个容器将会被分到172.17.0.2，第二个容器是172.17.0.3，依此类推。

使用-P（大写）不需要指定任何映射关系，默认情况下，Docker会随机将一个4900049900的端口映射到内部容器开放的网络端口。使用-p（小写）则需要指定主机的端口应该映射到容器的哪个端口。使用格式如下所示：

Docker容器端口映射原理都是在本地的iptable的nat表中添加相应的规则，将访问本机IP地址:hostport的网包进行一次DNAT，转换成容器IP:containerport。

如上所示，DNAT发生在DOCKER这条iptables链，它有两处引用，分别是PREROUTING链和OUTPUT链，意味着从外面发到本机和本地进程访问本机（由iptables匹配规则ADDRTYPE match dst-type LOCAL指定）的1234端口的包目的地址都会被修改成172.17.0.2:80。

其原理是通过Linux系统的转发功能实现的（把主机当交换机）。如果发现容器内访问不了外网，则需要确认系统的ip_forward是否已打开。

或者检查docker daemon启动的时候--ip-forward参数是不是被设置成false了，如果是的话，则需要设置--ip-forward=true重新启动Docker，Docker会打开主机的ip forward。

即从容器网段出来访问外网的包，都要做一次MASQUERADE，即出去的包都用主机的IP地址替换源地址。

如果想统一、持久化配置所有容器的DNS，通过修改主机Docker Daemon的配置文件（一般是/etc/docker/daemon.json）的方式指定除主机/etc/resolv.conf的其他DNS信息：

也可以在docker run时使用--dns=address参数来指定。
至于配置Docker容器的主机名，则可以在运行docker run创建容器时使用参数-h hostname或者--hostname hostname配置。

·--mtu=BYTES，配置docker0网桥的MTU（最大传输单元）。

Docker的service子命令是对Docker容器的一层封装，有点类似于Kubernetes的Service概念。

### 2.5 天生不易：容器组网的挑战

随着Docker的兴起，虚拟机被更轻量级的容器方式颠覆，从而大幅降低了开发、运维、测试和部署、维护的成本，当然也带来了很多在虚拟机里没有的问题。

·原生Docker容器不解决跨主机通信问题，而大规模的容器部署势必涉及不同主机上的网络通信；

·容器相较虚拟机生命周期更短，重启更加频繁，重启后IP地址将发生变化，需要进行高效的网络地址管理，因此静态的IP地址分配或DHCP（耗费数秒才能生效）将不起作用；

·大规模跨机部署带来多主机间的ARP洪泛，造成大量的资源浪费；

·大规模部署使用veth设备会影响网络性能，最严重时甚至会比裸机降低50%!；(MISSING)

·过多的MAC地址。当MAC地址超过限制时，主机中的物理网络接口卡虽然可以切换到混杂模式，但会导致性能下降，而且顶级机架（ToR）交换机可支持的MAC地址数量也有上限。

消除NAT的第二种方法是把主机变成全面的路由器，甚至是使用BGP的路由器。主机会将前缀路由到主机中的容器，每个容器会使用全球唯一的IP地址。尽

以上这些都只是针对某个具体问题的，而非整体的端到端的方案，例如IP地址管理、跨主机通信、消除NAT、安全策略等。Docker最初并没有提供必要的此类能力，在开始的很长一段时间内，只支持使用Linux bridge+iptables的单机网络部署方式，这种方式下容器的可见性只存在于主机内部，这严重地限制了容器集群的规模及可用性！

在意识到可扩展、自动化的网络对大规模容器部署的重要性后，Docker公司于2015年3月收购了初创企业SocketPlane，后者的主要业务就是将软件定义网络功能以原生方式添加到Docker中。同年6月，Docker将SocketPlane技术整合至其集群管理项目Swarm中，基于Linux桥接与VXLAN解决容器的跨主机通信问题。

随着Docker的兴起，一些专业的容器网络插件，例如flannel、Weave、Calico等也开始与各个容器编排平台进行集成，

### 2.6 如何做好技术选型：容器组网方案沙场点兵

而容器网络最基础的一环是为容器分配IP地址，主流的方案有本地存储+固定网段的host-local，DHCP，分布式存储+IPAM的flannel和SDN的Weave等。

而路由+IPAM初始化容器网络的步骤为：（1）向IPAM请求一个IP地址。（2）在主机和/或网络设备上创建veth设备和路由规则。（3）为veth设备配置IP地址。

隧道网络也称为overlay网络，有时也被直译为覆盖网络。其实隧道方案不是容器领域新发明的概念，它在IaaS网络中应用得也比较多。overlay网络最大的优点是适用于几乎所有网络基础架构，它唯一的要求是主机之间的IP连接。但overlay网络的问题是随着节点规模的增长，复杂度也会随之增加，而且用到了封包，因此出了网络问题定位起来比较麻烦。

·flannel：源自CoreOS，支持自研的UDP封包及Linux内核的VXLAN协议。

flannel的想法很好：每个主机负责一个网段，在这个网段里分配一个IP地址。访问另外一台主机时，通过网桥到达主机上的IP地址，这边会有一个设备，程序会把你发的包读出来，去判断你的目的地址是什么，归哪台机器管。flannel的UDP封包协议会在原始的IP包外面加一个UDP包，发到目的地址。收到之后，会把前面的UDP包扔掉，留下来的就是目标容器地址。

使用UDP封包的一个比较大的问题是性能较差

Weave和flannel用到的封包技术特点类似，不过使用的是VXLAN。另外，Weave的思路是共享IP而非绑定。在传输层先找到目的地址，然后把包发到对端，节点之间互相通过协议共享信息。

我们为什么要封包？其实它是改包，主要解决的问题是同一个问题，即在容器网络里，主机间不知道对方的目的地址，没有办法把IP包投递到正确的地方。传统的三层网络是用路由来互相访问的，不需要封包。

·Calico：源自Tigera，基于BGP的路由方案，支持很细致的ACL控制，对混合云亲和度比较高；

在容器里怎么实现基于路由的网络呢？以Calico为例，Calico是一个纯三层网络方案。不同主机上的每个容器内部都配一个路由，指向自己所在的IP地址；每台服务器变成路由器，配置自己的路由规则，通过网卡直接到达目标容器，整个过程没有封包。

Calico在每一个计算节点，利用Linux Kernel实现高效的vRouter来负责数据转发，而每个vRouter通过BGP把自己节点上的工作负载的路由信息向整个Calico网络传播。小规模部署可以直接互联，大规模下可通过指定的BGP Route Reflector完成。保证最终所有的容器之间的数据流量都是通过IP路由的方式完成互联的。

overlay网络是在传统网络上虚拟出一个虚拟网络，承载的底层网络不再需要做任何适配。在容器的世界里，物理网络只承载主机网络通信，虚拟网络只承载容器网络通信。overlay网络的任何协议都要求在发送方对报文进行包头封装，接收方剥离包头。

传统的二层网络的范围有限，L2 overlay网络是构建在底层物理网络上的L2网络，相较于传统的L2网络，L2overlay网络是个“大二层”的概念，其中“大”的含义是可以跨越多个数据中心（即容器可以跨L3 underlay进行L2通信），而“二层”指的是通信双方在同一个逻辑的网段内，例如172.17.1.2/16和172.17.2.3/16。

VXLAN就是L2 overlay网络的典型实现，其通过在UDP包中封装原始L2报文，实现了容器的跨主机通信。L2 overlay网络容器可在任意宿主机间迁移而不改变其IP地址的特性，使得构建在大二层overlay网络上的容器在动态迁移时具有很高的灵活性。

L3 overlay组网类似L2 overlay，但会在节点上增加一个网关。每个节点上的容器都在同一个子网内，可以直接进行二层通信。跨节点的容器间通信只能走L3，都会经过网关转发，性能相比于L2 overlay较弱。牺牲的性能获得了更高的灵活性，跨节点通信的容器可以存在于不同的网段中，例如192.168.1.0/24和172.17.16.0/24。flannel的UDP模式采用的就是L3 overlay模型。

underlay网络一般理解为底层网络，传统的网络组网就是underlay类型

（1）flannel仅仅作为单租户的容器互联方案是很不错的，但需要额外的组件实现更高级的功能，例如网络隔离、服务发现和负载均衡等。

### 第3章 标准的胜利：Kubernetes网络原理与实践

构建和运行可弹性扩展的应用。云原生的代表技术包括容器、服务网格、微服务、不可变基础设施和声明式API。这些技术能够构建容错性好、易于管理、便于观察的松耦合系统。结合可靠的自动化手段，云原生技术使工程师能够轻松地对系统做出频繁和可预测的重大变更。

但Docker毕竟只是一个运行时，当我们需要一个编排框架作为系统核心来串联开发、测试、发布、运行、升级等整个软件生命周期时，它就略显单薄。

谷歌照着鼎鼎大名的Borg系统开源的容器编排、调度和管理平台

Pod是Kubernetes中特有的一个概念，它是容器分组的一层抽象，也是Kubernetes最小的部署单元。Kubernetes基于Pod提供工作负载的概念及网络、存储等能力。

Node（工作节点）：这些机器在Kubernetes主节点的控制下将工作负载以容器的方式运行起来；

·Kubelet：守护进程，运行在每个工作节点上，保证该节点上容器的正常运行；

·跨主机编排容器；·更充分地利用硬件资源最大化地满足企业应用的需求；·控制与自动化应用的部署与升级；·为有状态的应用程序挂载和添加存储器；·线上扩展或裁剪容器化应用程序与它们的资源；·声明式的容器管理，保证所部署的应用按照我们部署的方式运作；·通过自动布局、自动重启、自动复制、自动伸缩实现应用的状态检查与自我修复。

我们将Kubernetes视为云计算时代的操作系统，它管理着集群中的节点和在之上运行的容器。Kubernetes的Master节点从管理员处接收命令，再把指令转交给其管理的工作节点，然后在该节点上分配资源并指派Pod来完成任务请求。

当Kubernetes把Pod调度到节点上，节点上的Kubelet会指示Docker启动特定的容器。接着，Kubelet会通过cgroup持续地收集容器的信息，然后提交到Kubernetes的管理面。Docker如往常一样拉取容器镜像、启动或停止容器。

着Docker中集成越来越多容器网络和编排层的功能，Docker不再是一个纯粹的容器引擎，而且稳定性也受到一定影响。Kubernetes作为编排层完全可以替用户屏蔽底层容器实现技术，提供一个更简单和通用的容器接口层，让containerd、cri-o、katacontainer等更轻量或更安全的容器技术实现CRI接口并集成Kubernetes。

### 3.2 终于等到你：Kubernetes网络

·各台服务器上的容器IP段不能重叠，所以需要有某种IP段分配机制，为各台服务器分配独立的IP段；

Kubernetes的容器网络重点需要关注两方面：IP地址分配和路由。

所有的Pod之间都可以保持三层网络的连通性，比如可以相互ping对方，相互发送TCP/UDP/SCTP数据包。CNI就是用来实现这些网络功能的标准接口。

Service总共有4种类型，其中最常用的类型是ClusterIP，这种类型的Service会自动分配一个仅集群内部可以访问的虚拟IP。

Kubernetes通过Kube-proxy组件实现这些功能，每台计算节点上都运行一个Kubeproxy进程，通过复杂的iptables/IPVS规则在Pod和Service之间进行各种过滤和NAT。

从Pod内部到集群外部的流量，Kubernetes会通过SNAT来处理。SNAT做的工作就是将数据包的源从Pod内部的IP:Port替换为宿主机的IP:Port。当数据包返回时，再将目的地址从宿主机的IP:Port替换为Pod内部的IP:Port，然后发送给Pod。当然，中间的整个过程对Pod来说是完全透明的，它们对地址转换不会有任何感知。

扁平化网络的优点在于：没有NAT带来的性能损耗，而且可追溯源地址，为后面的网络策略做铺垫，降低网络排错的难度等。

集群内访问Pod，会经过Service；集群外访问Pod，经过的是Ingress。Service和Ingress是Kubernetes专门为服务发现而抽象出来的相关概念

CNI并不支持Docker网络，也就是说，docker0网桥会被大部分CNI插件“视而不见”。

我们知道容器的隔离功能利用的是Linux内核的namespace机制，而只要是一个进程，不管这个进程是否处于运行状态（挂起亦可），它都能“占”用着一个namespace。因此，每个Pod内的第一个系统容器pause的作用就是占用一个Linux的networknamespace。

所有目的地址是本机上Pod的网络包，都发到cni0这个Linux网桥，进而广播给Pod。

10.1.2.0/24是Node2上Pod的网段，192.168.1.101又恰好是Node2的IP。意思是，目的地址是10.1.2.0/24的网络包，发到Node2上。这

bridge网络本身不解决容器的跨机通信问题，需要显式地书写主机路由表，映射目标容器网段和主机IP的关系，集群内如果有N个主机，需要N-1条路由表项。

至于overlay网络，它是构建在物理网络之上的一个虚拟网络，其中VXLAN是主流的overlay标准。VXLAN就是用UDP包头封装二层帧，即所谓的MAC in UDP。

这次是直接发给本机的tun/tap设备tun0，而tun0就是overlay隧道网络的入口。我们注意到，集群内所有机器都只需要这么一条路由表，不需要像bridge网络那样，写N-1条路由表项。如何将网络包正确地传递到目标主机的隧道口另一端呢？以flannel的实现为例，它会借助一个分布式的数据库，记录目的容器IP与所在主机的IP的映射关系，而且每个节点上都会运行一个agent。例如，flanneld会监听在tun0上进行的封包和解包操作。例如，Node1上的容器发包给Node2上的容器，flanneld会在tun0处将一个目的地址是192.168.1.101:8472的UDP包头（校验和置成0）封装到这个包的外层，然后借着主机网络的东风顺利到达Node2。监听在Node2的tun0上的flanneld捕获这个特殊的UDP包（检验和为0），知道这是一个overlay的封包，于是解开UDP包头，将它发给本机的Linux网桥cni0，进而广播给目的容器。

在Kubernetes 1.7版本以后，Kubernetes提供downward API，支持用户通过PodSpec的HostAliases字段添加这些自定义的条目

注：如果Pod启用了hostNetwork（即使用主机网络），那么将不能使用HostAliases特性，因为Kubelet只管理非hostNetwork类型Pod的hosts文件。

### 3.3 Pod的核心：pause容器

我们知道Kubernetes的Pod抽象基于Linux的namespace和cgroups，为容器提供了隔离的环境。从网络的角度看，同一个Pod中的不同容器犹如运行在同一个主机上，可以通过localhost进行通信。

pause容器被当作Pod中所有容器的“父容器”，并为每个业务容器提供以下功能：·在Pod中，它作为共享Linux namespace（Network、UTS等）的基础；·启用PID namespace共享，它为每个Pod提供1号进程，并收集Pod内的僵尸进程。

这个pause容器运行一个非常简单的进程，它不执行任何功能，一启动就永远把自己阻塞住了（见pause（）系统调用）。正如你看到的，它当然不会只知道“睡觉”。它执行另一个重要的功能——即它扮演PID 1的角色，并在子进程成为“孤儿进程”的时候，通过调用wait（）收割这些僵尸子进程。这样就不用担心我们的Pod的PID namespace里会堆满僵尸进程了。这也是为什么Kubernetes不随便找个容器（例如Nginx）作为父容器，让用户容器加入的原因。

在UNIX系统中，PID为1的进程是init进程，即所有进程的父进程。init进程比较特殊，它维护一张进程表并且不断地检查其他进程的状态。init进程的其中一个作用是当某个子进程由于父进程的错误退出而变成了“孤儿进程”，便会被init进程“收养”并在该进程退出时回收资源。

进程可以使用fork和exec两个系统调用启动其他进程。当启动了其他进程后，新进程的父进程就是调用fork系统调用的进程。fork用于启动正在运行的进程的另一个副本，而exec则用于启动不同的进程。每个进程在操作系统进程表中都有一个条目。这将记录有关进程的状态和退出代码。当子进程运行完成后，它的进程表条目仍然保留，直到父进程使用wait系统调用获得其退出代码后才会清理进程条目。这被称为“收割”僵尸进程，并且僵尸进程无法通过kill命令清除。

僵尸进程是已停止运行但进程表条目仍然存在的进程，父进程尚未通过wait系统调用进行检索。从技术层面来说，终止的每个进程都算是一个僵尸进程，尽管只是在很短的时间内发生的。当用户程序写得不好并且简单地省略wait系统调用，或者当父进程在子进程之前异常退出并且新的父进程没有调用wait检索子进程时，会出现较长时间的僵尸进程。系统中存在过多僵尸进程将占用大量操作系统进程表资源。

同时，init进程必须拥有“信号屏蔽”功能，不能处理某个信号逻辑，从而防止init进程被误杀。所以不是随随便便一个进程都能当init进程的。

当在主机上发送SIGKILL或者SIGSTOP（也就是docker kill或者docker stop命令）强制终止容器的运行时，其实就是在终止容器内的init进程。一旦init进程被销毁，同一PIDnamespace下的进程也随之被销毁。

在容器中，必须要有一个进程充当每个PID namespace的init进程，使用Docker的话，ENTRYPOINT进程是init进程。如果多个容器之间共享PID namespace，那么拥有PID namespace的那个进程须承担init进程的角色，其他容器则作为init进程的子进程添加到PIDnamespace中。

但是，Nginx并不是设计用来作为一个init进程运行并收割僵尸进程的。这意味着将会有很多这种僵尸进程，并且这种情况将持续整个容器的生命周期。

那为什么还要开放这个禁止PID namesapce共享的开关呢？那是因为当应用程序不会产生其他进程，而且僵尸进程带来的问题可以忽略不计时，就用不到PID namespace的共享了。

（1）PID namespace共享时，由于pause容器成了PID=1，其他用户容器就没有PID 1了。但像systemd这类镜像要求获得PID 1，否则无法正常启动。有些容器通过kill-HUP 1命令重启进程，然而在由pause容器托管init进程的Pod里，上面这条命令只会给pause容器发信号。

2）PID namespace共享Pod内不同容器的进程对其他容器是可见的。这包括/proc中可见的所有信息，例如，作为参数或环境变量传递的密码，这将带来一定的安全风险。

### 3.4 打通CNI与Kubernetes：Kubernetes网络驱动

Kubenet支持用户使用Kubelet的--network-plugin-mtu参数指定MTU。例如在AWS上，eth0MTU通常为9001，于是用户可以指定--network-plugin-mtu=9001。如果使用IPSEC，则可以因为封装开销而减少MTU，例如--network-plugin-mtu=8873。

Kubenet调用Linux tc实现了一个简单的ingress/egress的带宽控制器。

CNI是Kubernetes与底层网络插件之间的一个抽象层，为Kubernetes屏蔽了底层网络实现的复杂度，同时解耦了Kubernetes的具体网络插件实现。

上文的JSON文件是一个host-local+bridge插件组合的例子，在一个JSON文件中，我们定义了一个名为mynet的网络。它是一个bridge模型，而IP地址管理（ipam）使用的是host-local（在本地用一个文件记录已经分配的容器IP地址），且可供分配的容器网段是10.10.0.0/16。

至于在Kubernets中如何使用它们？Kubelet和CNI给出了两个默认的文件系统路径，/etc/cni/net.d用来存储CNI配置文件，/opt/cni/bin目录用来存放CNI插件的二进制文件。在我们这个例子中，至少要提前放好bridge和host-local两个插件的二进制，以及10-mynet.conf配置文件（叫什么名字随意，Kubelet只解析*.conf文件）。

### 3.5 找到你并不容易：从集群内访问服务

当有多个后端实例时：·如何做到负载均衡？·如何保持会话亲和性？·容器迁移，IP发生变化如何访问？·健康检查怎么做？·怎么通过域名访问？

Kubernetes提供的解决方案是在客户端和后端Pod之间引入一个抽象层：Service

在Kubernetes中，用户可以为任何Kubernetes资源分配成为Labels（标签）的任意键值。Kubernetes使用Labels将多个相关的Pod组合成一个逻辑单元，称为Service。Service具有稳定的IP地址（区别于容器不固定的IP地址）和端口，并会在一组匹配的后端Pod之间提供负载均衡，匹配的条件就是Service的Label Selector与Pod的Labels相匹配。

Kubernetes会从集群的可用服务IP池中为每个新创建的服务分配一个稳定的集群内访问IP地址，称为Cluster IP。Kubernetes还会通过添加DNS条目为Cluster IP分配主机名。Cluster IP和主机名在集群内是独一无二的，并且在服务的整个生命周期内不会更改。只有将服务从集群中删除，Kubernetes才会释放Cluster IP和主机名。用户可以使用服务的Cluster IP或主机名访问正常运行的Pod。

当Service的后端Pod准备就绪后，Kubernetes会生成一个新的Endpoints对象，而且这个Endpoints对象和Service同名。

如果觉得通过yaml方式创建Service太麻烦，则kubectl还提供了expose子命令直接将deployment暴露成服务。

在默认情况下，服务会随机转发到可用的后端。如果希望保持会话（同一个client永远都转发到相同的Pod），可以把service.spec.sessionAffinity设置为ClientIP，即基于客户端源IP的会话保持，而且默认会话保持时间是10800秒。这会起到什么样的效果呢？即在3小时内，同一个客户端访问同一服务的请求都会被转发给第一个Pod。

NodePort是Kubernetes提供给集群外部访问Service入口的一种方式（另一种方式是Load Balancer），所以可以通过Node IP:nodePort的方式提供集群外访问Service的入口。需要注意的是，我们这里说的集群外指的是Pod网段外，例如Kubernetes节点或因特网。

Kubernetes Service有几种类型：Cluster IP、Load Balancer和NodePort。其中，Cluster IP是默认类型，自动分配集群内部可以访问的虚IP——Cluster IP。

创建Service时，通过配置serviceSpec.loadBalancerSourceRanges字段，可以限制哪些IP地址范围可以访问集群内的服务。loadBalancerSourceRanges可以指定多个范围，并且支持随时更新。Kube-proxy会配置该节点的iptables规则，以拒绝与指定loadBalancerSourceRanges不匹配的所有流量。这样就不需要额外配置VPC的防火墙规则了。

外部可以使用EXTERNAL-IP加端口来访问该服务，这是一个云供应商提供的负载均衡器IP，在我们这个例子中就是10.13.242.236:8086。

NodePort是解决服务对外暴露的最简单方法，对于那些没有打通容器网络和主机网络的用户，NodePort成了他们从外部访问Service的首选。但NodePort的问题集中体现在性能和可对宿主机端口占用方面。一旦服务多起来，NodePort在每个节点上开启的端口会变得非常庞大且难以维护。

环境变量注入这种方式，对服务和环境变量的匹配关系有一定的规范，使用起来也相对简单。但这种方式的缺点也很明显：·容易环境变量洪泛，Docker启动参数过长会影响性能，甚至直接导致容器启动失败；·Pod想要访问的任何Service必须在Pod自己被创建之前创建，否则这些环境变量就不会被注入。

更理想的方案是，应用能够直接使用服务的名字，不需要关心它实际的IP地址，中间的转换能够自动完成。名字和IP之间的转换即DNS，DNS的方式并没有以上两个限制。

头（headless）Service即没有selector的Service

这个Service没有selector，就不会创建相关的Endpoints对象。可以手动将Service映射到指定的Endpoints：

ExternalName Service是Service的特例，它没有selector，也没有定义任何的端口和Endpoint。相反，对于运行在集群外部的服务，它通过返回该外部服务的别名这种方式提供服务。[插图]

要指定流量必须转到同一节点上的Pod，可以将serviceSpec.externalTraffic Policy设置为Local（默认是Cluster）：

访问NodePort和Load Balancer都能指定节点，但Cluster IP无法指定节点，因此Service流量就永远出不了发起访问的客户端的那个节点，这也不是externalTrafficPolicy这个特性的设计初衷。

### 3.6 找到你并不容易：从集群外访问服务

可以使用NodePort类型的Service，但这种做法除了要求集群内Node有对外访问IP，还有一些已知的性能问题

那使用Load Balancer类型的Service呢？它要求在特定的云服务上运行Kubernetes，而且Service只提供L4负载均衡功能，一些高级的L7转发功能，例如基于HTTP header、cookie、URL的转发就做不了。

在Kubernets中，L7的转发功能、集群外访问Service，都是专门交给Ingress的。虽然前文提到NodePort和Load Balancer类型的Service在功能上也能起到对外暴露服务的作用，但是都存在各种限制，而Ingress没有上述两种Service限制。

Ingress可能是暴露服务的最强大方式，也是最复杂的。Kubernetes Ingress提供了负载平衡器的典型特性：HTTP路由、黏性会话、SSL终止、SSL直通、TCP和UDP负载平衡等。

Ingress控制器有各种类型，包括Google Cloud Load Balancer、Nginx、Istio等。有些Ingress Controller可能还依赖各种插件，例如cert-manager，它可以为服务自动提供SSL证书。

何谓Ingress？从字面意思解读，就是“入站流量”。Kubernetes的Ingress资源对象是指授权入站连接到达集群内服务的规则集合。

Ingress是建立在Service之上的L7访问入口，它支持通过URL的方式将Service暴露到k8s集群外；支持自定义Service的访问策略；提供按域名访问的虚拟主机功能；支持TLS通信。Ingress的作用如图3-20所示。

Ingress可以基于客户端请求的URL做流量分发，转发给不同的Service后端。

ADDRESS即Ingress的访问入口地址，由Ingress Controller分配。一般由Ingress的底层实现Load Balancer的IP地址，例如Ingress、GCE LB、F5等；BACKEND是Ingress对接的后端Kubernetes Service IP+Port；RULE是自定义的访问策略，主要基于URL的转发策略，若为空，则访问ADDRESS的所有流量都转发给BACKEND。

Ingress是一个非常“极客”并需要DIY的产物，Kubernetes只负责提供一个API定义，具体的Ingress Controller需要用户自己实现！官方提供了Nginx和GCE的Ingress Controller示例供开发者参考。实现一个Ingress Controller的大致框架是：List/Watch Kubernetes的Service、Endpoints、Ingress对象，并根据这些信息刷新外部Load Balancer的规则和配置。

这还不算，如果想要通过域名访问Ingress，则需要用户自己配置域名和Ingress IP的映射关系，例如host文件、自己的DNS（不是Kube-dns）。下面章节会讲到，“高冷”的Kube-dns只负责集群内的域名解析，集群外的一概不管。

### 3.7 你的名字：通过域名访问服务

Kubernetes的DNS应用部署好后，会对外暴露一个服务，集群内的容器通过访问该服务的Cluster IP+53端口获得域名解析服务，而这个Service的Cluster IP一般情况下都是固定的。

那么刷新/etc/resolv.conf配置这个动作是谁完成的呢？答案是Kubelet。

除此之外，Kubelet的--cluster_domain=<default-local-domain>参数支持配置集群域名后缀，默认是cluster.local。

对于Service，Kubernetes DNS服务器会生成三类DNS记录，分别是A记录、SRV记录和CNAME记录。

SRV记录是通过描述某些服务协议和地址促进服务发现的。SRV记录通常定义一个符号名称和作为域名一部分的传输协议（如TCP），并定义给定服务的优先级、权重、端口和目标。

Pod的默认主机名由Pod的metadata.name值定义。用户可以通过在可选的hostname字段中指定一个新值更改默认主机名。用户还可以在subdomain字段中自定义子域名。

DNS Server的IP地址是10.0.0.1。options ndots:5的含义是当查询的域名字符串内的点字符数量超过ndots（5）值时，则认为是完整域名，直接解析，否则Linux系统会自动尝试用default.pod.cluster.local、default.svc.cluster.local或svc.cluster.local补齐域名后缀。例如，查询whoami会自动补齐成whoami.default.pod.cluster.local、whoami.default.svc.cluster.local和whoami.svc.cluster.local，查询过程中，任意一个记录匹配便返回，显然能返回DNS记录的匹配是whoami+default.svc.cluster.local。而查询whoami.default能返回DNS记录的匹配是whoami.default+svc.cluster.local。最后，运行DNS Pod可能需要特权，即配置Kubelet的参数：--allow-privileged=true。

Kubernetes域名解析策略对应到Pod配置中的dnsPolicy，有4种可选策略，分别是None、ClusterFirstWithHostNet、ClusterFirst和Default

它允许Pod忽略Kubernetes环境中的DNS设置。应使用dnsConfigPod规范中的字段提供所有DNS设置；

当Pod使用主机网络（hostNetwork:true）时，DNS策略需要设置成ClusterFirstWithHostNet。对于那些使用主机网路的Pod，它们是可以直接访问宿主机的/etc/resolv.conf文件的。因此，如果不加上dnsPolicy:ClusterFirstWithHostNet，则Pod将默认使用宿主机的DNS配置，这样会导致集群内容器无法通过域名访问Kubernetes的服务（除非在宿主机的/etc/resolv.conf文件配置了Kubernetes的DNS服务器）。

ClusterFirst策略就是优先使用Kubernetes的DNS服务解析，失败后再使用外部级联的DNS服务解析，工作流程如图3-21所示。

如果出现此错误，则需要做的第一件事是检查DNS配置是否正确。查看容器中的resolv.conf文件：

可能还需要检查后端DNS Endpoint是否准备好：

### 3.8 Kubernetes网络策略：为你的应用保驾护航

在默认情况下，Kubernetes底层网络是“全连通”的，即在同一集群内运行的所有Pod都可以自由通信。但是也支持用户根据实际需求以不同方式限制集群内Pod的连接。

网络策略就是基于Pod源IP（所以Kubernetes网络不能随随便便做SNAT）的访问控制列表，限制的是Pod之间的访问。通过定义网络策略，用户可以根据标签、IP范围和端口号的任意组合限制Pod的入站/出站流量。网络策略作为Pod网络隔离的一层抽象，用白名单实现了一个访问控制列表（ACL），从Label Selector、namespace selector、端口、CIDR这4个维度限制Pod的流量进出。

｛｝代表允许所有，［］代表拒绝所有。

podSelector:｛｝留空就是定义对Kubernetes当前namespace下的所有Pod生效。没有定义白名单的话，默认就是Deny ALL（拒绝所有）；

而Kubernetes网络是应用到每个节点上的，实现Network Policy的agent需要在每个节点上都List/Watch Kubernetes的Network Policy、namespace、Pod等资源对象，因此在实现上可能会有较大的性能影响。Network Policy agent一般通过KubernetesDaemonSet实现每个节点上都有一个部署。

### 3.9 前方高能：Kubernetes网络故障定位指南

网络可以说是Kubernetes部署和使用过程中最容易出问题的，通过前面的理论讲解我们已经掌握了Kubernetes网络的基本知识，本节将重点介绍Kubernetes网络故障定位的实战经验。

IP转发是一种内核态设置，允许将一个接口的流量转发到另一个接口，该配置是Linux内核将流量从容器路由到外部所必需的。有时，该项设置可能会被安全团队运行的定期安全扫描重置，或者没有配置为重启后生效。

容器启动后MY_POD_IP这个环境变量的值来自Pod的status.podIP，容器内的应用只要读取该环境变量即可获取Pod的真实IP地址。

（2）Docker主机将源地址由172.16.1.8:32000替换为10.0.0.1:32000，并把包转发给10.0.0.99:80。Linux用一个表格追踪转换过程，以便在包的响应中能够进行逆向转换。

这么做会带来一个问题，就是当并发访问NodePort时会导致丢包。除了NodePort，flannel的--ip-masq=true选项也会引起丢包。

### 第4章 刨根问底：Kubernetes网络实现机制

运行在每个节点上的Kube-proxy会监控Service和Endpoints的更新，并调用其Load Balancer模块在主机上刷新路由转发规则。每个Pod都可以通过Liveness Probe和Readiness probe探针解决健康检查问题，当有Pod处于非准备就绪状态时，Kube-proxy会删除对应的转发规则。需要注意的是，Kube-proxy的Load Balancer模块并不那么智能，如果转发的Pod不能正常提供服务，它不会重试或尝试连接其他Pod。

随着iptables在大规模环境下暴露出了扩展性和性能问题，越来越多的厂商开始使用IPVS模式。

这里利用了iptables的random模块，使连接有50%!的(MISSING)概率进入KUBE-SEP-ID6YWI T3F6WNZ47P链，50%!的(MISSING)概率进入KUBE-SEP-IN2YML2VIFH5RO2T链。因此，Kube-proxy的iptables模式采用随机数实现了服务的负载均衡。

但因为iptable使用NAT完成转发，也存在不可忽视的性能损耗。另外，当集群中存在上万服务时，Node上的iptables rules会非常庞大，对管理是个不小的负担，性能还会大打折扣。

使用IPVS做集群内服务的负载均衡可以解决iptables带来的性能问题。IPVS专门用于负载均衡，并使用更高效的数据结构（散列表），允许几乎无限的规模扩张。

IPVS是Linux内核实现的四层负载均衡，是LVS负载均衡模块的实现。上文也提到，IPVS基于netfilter的散列表，相对于同样基于netfilter框架的iptables有更好的性能表现和可扩展性

在内核中，所有由netfilter框架实现的连接跟踪模块称作conntrack（connection tracking）。在DNAT的过程中，conntrack使用状态机启动并跟踪连接状态。为什么需要记录连接的状态呢？因为iptables/IPVS做了DNAT后需要记住数据包的目的地址被改成了什么，并且在返回数据包时将目的地址改回来。除此之外，iptables还可以依靠conntrack的状态（cstate）决定数据包的命运。其中最主要的4个conntrack状态如下：

2）ESTABLISHED：匹配连接的响应包及后续的包，conntrack知道该数据包属于一个已建立的连接。通常发生在TCP握手完成之后。

Kube-proxy实现的是分布式负载均衡，而非集中式负载均衡。何谓分布式负载均衡器呢？就是每个节点都充当一个负载均衡器，每个节点上都会被配置一模一样的转发规则。

### 4.2 Kubernetes极客们的日常：DIY一个Ingress Controller

·Ingress Controller：Nginx Kubernetes官方维护的方案，安装Ingress Controller会自动安装Nginx；

Ingress Controller将Ingress入口地址和后端Pod地址的映射关系（规则）实时刷新到Load Balancer的配置文件中，再让负载均衡器重载（reload）该规则，便可实现服务的负载均衡和自动发现。

需要注意的是，Ingress API定义的后端虽然是Kubernetes的Service，但实际上Nginx Ingress Controller是直接负载到Service的Endpoints上的

Ingress API本身并没有关于L4负载均衡的相关定义，因此要想使用Nginx的L4代理功能（包括TCP和UDP），需要在Configmap中配置并传给Nginx Ingress Controller。上面例子中指定了存储转发信息的两个Configmap对象。

在微服务的开发模式下，外部网络要通过域名、UR路径、负载均衡等转发到后端私有网络中，微服务之所以称为微，是因为它是动态变化的，它会经常被增加、删除或更新。传统的反向代理对服务动态变化的支持不是很方便，也就是服务变更后，不是很容易马上改变配置和热加载。Nginx Ingress Controller的出现就是为了解决这个问题，它可以时刻监听服务注册或服务编排API，随时感知后端服务变化，自动重新更改配置并重新热加载，期间服务不会暂停或停止，这对用户来说是无感知的。

### 4.3 沧海桑田：Kubernetes DNS架构演进之路

·从Kubernetes API Server那边观察（watch）到的Service和Endpoints对象没有存在etcd，而是缓放在内存中，既提高了查询性能，也省去了维护etcd存储的工作量；·引入了dnsmasq容器，由它接受Kubernetes集群中的DNS请求，目的就是利用dnsmasq的cache模块，提高解析性能；

从Kubernetes 1.12开始，CoreDNS就成了Kubernetes的默认DNS服务器，但kubeadm默认安装CoreDNS的时间要更早。在Kuberentes1.9版本中，使用kubeadm方式安装的集群可以通过以下命令直接安装CoreDNS。

（3）一体化的解决方案。区别于Kube-dns“三合一”的架构，CoreDNS编译出来就是一个单独的可执行文件，内置了缓存、后端存储管理和健康检查等功能，无须第三方组件辅助实现其他功能，从而使部署更方便，内存管理更安全。

与Kube-dns的三进程架构不同，CoreDNS就一个进程，运维起来更加简单。而且采用Go语言编写，内存安全，高性能。值得称道的是，CoreDNS采用的是“插件链”架构，每个插件挂载一个DNS功能，保证了功能的灵活、易扩展。尽管资历不深，却“集万千宠爱于一身”，自然是有绝技的。

无论是Kube-dns还是CoreDNS，基本原理都是利用watch Kubernetes的Service和Pod，生成DNS记录，然后通过重新配置Kubelet的DNS选项让新启动的Pod使用Kube-dns或CoreDNS提供的Kubernetes集群内域名解析服务。

### 4.4 你的安全我负责：使用Calico提供Kubernetes网络策略

与Kubernetes Ingress API类似，Kubernetes只提供了Network Policy的API定义，不负责具体实现。实现Network Policy的控制器称之为Policy Controller。Network Policy是Kubernetes对Pod的网络隔离手段，对应到宿主机上是一系列iptables规则。这些iptables规则就是由Policy Controller配置的

（1）calico-kube-controllers。整个集群环境只有一个以calico-kube-controllers开头命名的Pod，用于从Kubernetes API Server中读取Network Policy等信息，并对Calico进行相应的配置。

（2）calico-node。在集群的每个节点上都会运行一个以calico-node开头命名的Pod，通过配置iptables实现节点上Pod的出/入（ingress/egress）网络策略。calico-node通常以Kubernetes DaemonSet方式部署运行。

### 第5章 百花齐放：Kubernetes网络插件生态

Docker自己的网络方案比较简单，就是每个宿主机上会跑一个非常纯粹的Linux bridge，这个bridge可以认为是一个二层的交换机，但它的能力有限，只能做一些简单的学习和转发。出网桥的流量会经过iptables，经过NAT，最后通过路由转发在宿主之间进行通信。

另外，NAT的存在会造成两端在通信时看到对方的地址是不真实的，而且NAT本身也有性能损耗。这些问题都对Docker自身网络方案的应用造成了障碍。

第2层网络会处理网络上两个相邻节点之间的帧传递。第2层网络的一个典型示例是以太网。

·第3层网络：OSI网络模型的“网络”层。第3层网络的主要关注点，是在第2层连接之上的主机之间路由数据包。IPv4、IPv6和ICMP是第3层网络协议的示例。

·VXLAN：即虚拟可扩展的LAN。首先，VXLAN用于通过在UDP数据包中封装第2层以太网帧帮助实现大型云部署。VXLAN虚拟化与VLAN类似，但提供更大的灵活性和功能（VLAN仅限于4096个网络ID）。VXLAN是一种overlay协议，可在现有网络之上运行。·overlay网络：是建立在现有网络之上的虚拟逻辑网络。overlay网络通常用于在现有网络之上提供有用的抽象，并分离和保护不同的逻辑网络。

在overlay网络中，封装被用于从虚拟网络转换到底层地址空间，从而能路由到不同的位置（数据包可以被解封装，并继续到其目的地）。

·BGP：代表“边界网关协议”，用于管理边缘路由器之间数据包的路由方式。BGP通过考虑可用路径、路由规则和特定网络策略等因素，将数据包从一个网络发送到另一个网络。BGP有时被用作容器网络的路由机制，但不会用在overlay网络中。

### 5.2 CNI标准的胜出：从此江湖没有CNM

在CNI标准中，网络插件是独立的可执行文件，被上层的容器管理平台调用。网络插件只有两件事情要做：把容器加入网络或把容器从网络中删除。调用插件的配置通过两种方式传递：环境变量和标准输入。

由此可见，CNI的最大价值在于提供了一致的容器网络操作界面，不论是什么网络插件都使用一致的API，提高了网络配置的自动化程度和一致性的体验。

促使Kubernetes选择CNI作为网络模型。这将带来诸如：docker inspect命令显示不了Pod的IP地址，直接被Docker启动的容器可能无法和被Kubernetes启动的容器通信等问题。
但Kubernetes必须做一个权衡：选择CNI，使Kubernetes网络配置更简单、灵活并且不需要额外的配置（例如，配置Docker使用Kubernetes或其他网络插件的网桥）。

### 5.3 Kubernetes网络插件鼻祖flannel

为了解决这个问题，flannel设计了一种全局的网络地址分配机制，即使用etcd存储网段和节点之间的关系，然后flannel配置各个节点上的Docker（或其他容器工具），只在分配到当前节点的网段里选择容器IP地址。这样就确保了IP地址分配的全局唯一性。

在overlay网络下，所有被发送到网络中的数据包会被添加上额外的包头封装。这些包头里通常包含了主机本身的IP地址，因为只有主机的IP地址是原本就可以在网络里路由传播的。

在Host-Gateway模式下，flannel通过在各个节点上运行的agent将容器网络的路由信息刷到主机的路由表上，这样一来，所有的主机就都有整个容器网络的路由数据了。Host-Gateway的方式没有引入overlay额外封包和解包操作，完全是普通的网络路由机制，通信效率与裸机直连相差无几。事实上，flannel的Gateway模式的性能甚至要比Calico好。然而，由于flannel只能修改各个主机的路由表，一旦主机之间隔了其他路由设备，比如三层路由器，这个包就会在路由设备上被丢掉。这样一来，Host-Gateway的模式就只能用于二层直接可达的网络，由于广播风暴的问题，这种网络通常是比较小规模的。

flannel的设计目的是为集群中的所有节点重新规划IP地址的使用规则，从而使得集群中的不同节点主机创建的容器都具有全集群“唯一”且“可路由的IP地址”，并让属于不同节点上的容器能够直接通过内网IP通信。那么节点是如何知道哪些IP可用，哪些不可用，即其他节点已经使用了哪些网段的呢？flannel用到了etcd的分布式协同功能。

并把该网段和主机IP地址等信息都记录在etcd中。

flannel通过etcd分配了每个节点可用的IP地址段后，修改了Docker的启动参数，例如--bip=172.17.18.1/24限制了所在节点容器获得的IP范围，以确保每个节点上的Docker会使用不同的IP地址段。需要注意的是，这个IP范围是由flannel自动分配的，

flannel的底层实现实质上是一种overlay网络（除了Host-Gateway模式），即把某一协议的数据包封装在另一种网络协议中进行路由转发。

性能最好的应该是Host-Gateway。

flannel在封包的时候是怎么知道目的容器所在主机的IP地址的呢？flanneld会观察etcd的数据，因此在其他节点向etcd更新网段和主机IP信息时，etcd就感知到了，在向其他主机上的容器转发网络包时，用对方容器所在主机的IP进行封包，然后将数据发往对应主机上的flanneld，再交由其转发给目的容器。

需要注意的是，flanneld要先于Docker启动。flanneld启动时主要做了以下几个动作：
·从etcd中获取network（大网）的配置信息；
·划分subnet（子网），并在etcd中进行注册；
·将子网信息记录到flannel维护的/run/flannel/subnet.env文件中；
·将subnet.env转写成一个Docker的环境变量文件/run/flannel/docker。

因为配置了bip，所以创建出来的容器会使用该网段的IP，即容器其实还是使用Docker的bridge网络模式。

如果你的环境中是先启动Docker后启动flannel，那么在准备好以上配置后还需要重启一次Docker daemon让配置生效

由于flannel没有Master和slave之分，每个节点上都安装一个agent，即flanneld。我们可以使用Kubernetes的DaemonSet部署flannel以达到每个节点部署一个flanneld实例的目的。我们在每一个flannel的Pod中都运行了一个flanneld进程，且flanneld的配置文件以ConfigMap的形式挂载到容器内的/etc/kube-flannel/目录，供flanneld使用，配置如下所示：

flannel通过在每一个节点上启动一个叫flanneld的进程，负责每一个节点上的子网划分，并将相关的配置信息（如各个节点的子网网段、外部IP等）保存到etcd中，而具体的网络包转发交给具体的backend实现。

当采用UDP模式时，flanneld进程在启动时会通过打开/dev/net/tun的方式生成一个tun设备。我们在第1章已经详细介绍过tun设备，tun设备可以简单理解为Linux中提供的一种内核网络与用户空间（应用程序）通信的机制，即应用可以通过直接读写tun设备的方式收发RAW IP包。

通过netstat-ulnp命令可以看到此时flanneld进程监听在8285端口：


当容器A发送到同一个subnet的容器B时，因为二者处于同一个子网，所以容器A和B位于同一个宿主机host上，而容器A和B也均桥接在docker0上

借助网桥docker0，即可实现同一个主机上容器A和B的直接通信。

：较早版本的flannel是直接复用Docker创建的docker0网桥，后面版本的flannel将使用CNI创建自己的cni0网桥。不管是docker0还是cni0，本质都是Linux网桥，功能也一样。

（3）flannel0为tun设备，发送给flannel0接口的RAW IP包（无MAC信息）将被flanneld进程接收，flanneld进程接收RAW IP包后在原有的基础上进行UDP封包，UDP封包的形式为172.16.130.140:｛系统管理的随机端口｝→172.16.130.164:8285。
注：172.16.130.164是10.244.2.194这个目的容器所在宿主机的IP地址，flanneld通过查询etcd很容易得到，8285是flanneld监听端口。

flanneld将封装好的UDP报文经eth0发出，从这里可以看出网络包在通过eth0发出前先是加上了UDP头（8个字节），再加上IP头（20个字节）进行封装，这也是flannel0的MTU要比eth0的MTU小28个字节的原因，即防止封包后的以太网帧超过eth0的MTU，而在经过eth0时被丢弃。

）主机B收到UDP报文后，Linux内核通过UDP端口号8285将包交给正在监听的flanneld。

flanneld将UDP包解封包后得到RAW IP包：10.244.1.96→10.244.2.194。

flanneld在其中主要起到的作用是：·UDP封包解包；·节点上的路由表的动态更新。

flannel的UDP封装是指原始数据由起始节点的flanneld进行UDP封装，经过主机网络投递到目的节点后就被另一端的flanneld还原成了原始的数据包，两边的Docker daemon都感觉不到这个过程的存在。flannel根据etcd的数据刷新本节点路由表，通过路由表信息在寻址时找到应该投递的目标节点。

看到容器A和容器B虽然在物理网络上并没有直接相连，但在逻辑上就好像是处于同一个三层网络中，这种基于底层物理网络设备通过flannel等软件定义网络技术构建的上层网络称之为overlay网络。

通过UDP这种backend实现的网络传输过程有没有问题呢？最明显的问题便是网络数据包先通过tun设备从内核中复制到用户态，再由用户态的应用复制到内核，仅一次网络传输就进行了两次用户态和内核态的切换，显然效率是不高的。

有没有比UDP模式更高效的办法呢？能否把封包/解包这些事情都交给Linux内核去做，而不是让flanneld代劳呢？事实上，Linux内核本身也提供了比较成熟的网络封包/解包（隧道传输）实现方案VXLAN，下面我们将分析内核的VXLAN与flanneld的UDP处理网络包时的区别。

反观flannel UDP模式下创建的flannel0是个tun设备。可以通过ip-d link查看VTEP设备flannel.1的配置信息。从以下输出可以看到，flannel.1配置的local IP为172.17.130.244（容器网段），flanneld为VXLAN外部UDP包目的端口配置的是Linux VXLAN默认端口8472，而不是IANA分配的4789端口。

在节点中添加一条该节点的IP及VTEP设备的静态ARP缓存。可以通过arp-n命令查看当前节点中已经缓存的其他节点上容器的ARP信息。

需要注意的是，上面输出的最后一栏显示的不是进程的ID和名称，而是一个破折号“-”，这说明8472这个UDP端口不是由用户态的进程监听的，而是flannel的VXLAN模式工作在内核态下。

flannel配置L3 overlay网络，它会创建一个大型内部网络，跨越集群中每个节点。在此overlay网络中，每个节点都有一个子网，用于在内部分配IP地址。在配置Pod时，每个节点上的Docker桥接口都会为每个新容器分配一个地址。同一主机中的Pod可以使用Docker桥接进行通信，而不同主机上的Pod会使用flanneld将其流量封装在UDP数据包中，以便路由到适当的目标。

### 5.4 全能大三层网络插件：Calico

Calico作为容器网络方案和我们前面介绍的那些方案最大的不同是它没有采用overlay网络做报文的转发，而是提供了纯3层的网络模型。三层通信模型表示每个容器都通过IP直接通信，中间通过路由转发找到对方。在这个过程中，容器所在的节点类似于传统的路由器，提供了路由查找的功能。要想路由能够正常工作，每个容器所在的主机节点扮演了虚拟路由器（vRouter）的功能，而且这些vRouter必须有某种方法，能够知道整个集群的路由信息。

Calico是一个基于BGP的纯三层的数据中心网络方案（也支持overlay网络），并且与Kubernetes、OpenStack、AWS、GCE等IaaS和容器平台有良好的集成。

由于采用的是标准协议，Calico模拟路由器的路由表信息可以被传播到网络的其他路由设备中，这样就实现了在三层网络上的高速跨节点网络。

虽然flannel被公认为是最简单的选择，但Calico以其性能、灵活性闻名于Kubernetes生态系统。Calico的功能更全面，正如Calico的slogan提到的：为容器和虚拟机工作负载提供一个安全的网络连接。

Calico基于iptables实现了Kubernetes的网络策略，通过在各个节点上应用ACL（访问控制列表）提供工作负载的多租户隔离、安全组及其他可达性限制等功能。此外，Calico还可以与服务网格Istio集成，以便在服务网格层和网络基础架构层中解释和实施集群内工作负载的网络策略。

Felix是一个守护程序，作为agent运行在托管容器或虚拟机的Calico节点上。Felix负责刷新主机路由和ACL规则等，以便为该主机上的Endpoint正常运行提供所需的网络连接和管理。进出容器、虚拟机和物理主机的所有流量都会遍历Calico，利用Linux内核原生的路由和iptables生成的规则。

Calico在每个运行Felix服务的节点上都部署一个BGP Client（BGP客户端）。BGP客户端的作用是读取Felix编写到内核中的路由信息，由BGP客户端对这些路由信息进行分发。具体来说，当Felix将路由插入Linux内核FIB时，BGP客户端将接收它们，并将它们分发到集群中的其他工作节点。

Calico可以创建并管理一个3层平面网络，为每个工作负载分配一个完全可路由的IP地址。工作负载可以在没有IP封装或NAT的情况下进行通信，以实现裸机性能，简化故障排除和提供更好的互操作性。我们称这种网络管理模式为vRtouer模式。

它的MAC地址为ee:ee:ee:ee:ee:ee，很明显是个固定的特殊地址。事实上，Calico为所有容器分配的容器MAC地址都一样，这么做是因为Calico只关心三层的IP地址，根本不关心二层MAC地址。

Calico为了简化网络配置将容器里的路由规则都配置成一样的，不需要动态更新。知道下一跳之后，容器会查询下一跳168.254.1.1的MAC地址，这个ARP请求发到哪里了呢？cali0是veth pair的一端，其另一端是主机上以caliXXXX命名的网卡，因此可以通过ethtool-S cali0列出对端的网卡index。

主机上这块网卡不管ARP请求的内容，直接用自己的MAC地址作为应答的行为被称为“ARP proxy”，可以通过以下内核参数检查：

由于我们ping报文的目的地址是192.168.196.129，也就是说会匹配路由表的最后一个表项，把172.17.8.101作为下一跳地址，并通过主机的eth0网卡发出。

注：在发送到另一台主机之前，报文还会经过Calico配置的iptables规则。如果被iptables规则拦住，包就会被内核丢弃。

同样地，这个报文会匹配最后一个路由规则，这个规则匹配的是一个IP地址，而不是网段。也就是说，主机上的每个容器都会有一个对应的路由表项。报文被发送到cali69b2b8 c106c这个veth pair，然后从另一端发送给目标容器。目标容器接收到报文之后，回复ICMP报文，应答报文原路返回。

### 5.5 Weave：支持数据加密的网络插件

用一个词评价Weave，就是“功能齐全”，有网络通信、有安全策略、有域名服务、有加密通信还有监控。

### 第6章 Kubernetes网络下半场：Istio

大多数云原生的应用都是微服务架构，微服务的注册。服务之间的相互调用关系，服务异常后的熔断、降级，调用链的跟踪、分析等一系列现实问题摆在各机构面前。Service Mesh就是解决这类微服务发现和治理问题的一个概念。

Service Mesh之于微服务架构就像TCP之于Web应用。我们大部分人写Web应用，关心的是RESTful、HTTP等上层协议，很少操心网络报文超时重传、分割组装、内容校验等底层细节。正是因为有了Service Mesh，企业只需在云原生和微服务架构的实践道路上根据自己的业务做适当的微服务拆分，无须过多关注底层服务发现和治理的复杂度。

Envoy对Service Mesh最大的贡献就是定义了xDS，Envoy虽然本质上是一个proxy，但是它的配置协议被众多开源软件支持

软件设计中的sidecar模式通过给应用服务加装一个“边车”达到控制和逻辑分离的目的。该设计模式通过给应用程序加上一个“边车”的方式拓展应用程序现有的功能，例如日志记录、监控、流量控制、服务注册、服务发现、服务限流、服务熔断等在业务服务中不需要实现的控制面功能，可以交给“边车”，业务服务只需要专注于实现业务逻辑即可。

sidecar模式一般有两种实现方式：·通过SDK的形式，在开发时引入该软件包依赖，使其与业务服务集成起来。这种方法可以与应用密切集成，提高资源利用率并且提高应用性能，但也对代码有侵入，受到编程语言和软件开发人员水平的限制；

·agent形式。服务所有的通信都是通过这个agent代理的，这个agent同服务一起部署，和服务一起有着相同的生命周期创建。这种方式对应用服务没有侵入性，不受编程语言和开发人员水平的限制，做到了控制与逻辑分开部署。但是会增加应用延迟，并且管理和部署的复杂度会增加。

Service Mesh作为sidecar运行时，对应用程序来说是透明的，所有应用程序间的流量都会通过sidecar，然后由sidecar转发给应用程序。换句话说，由于sidecar劫持了流量，所以对应用程序流量的控制都可以在sidecar中实现。

·解决服务之间调用越来越复杂的问题。随着分布式架构越来越复杂和微服务越拆越细，我们越来越迫切地希望有一个统一的控制面来管理我们的微服务，帮助我们维护和管理所有微服务，这时传统开发层面上的控制就远远不够了，而边sidecar模式可以很好地解决这个问题；

·控制与逻辑分离的问题。sidecar模式基于将控制与逻辑分离和解耦的思想。通俗地讲就是，让专业的人做专业的事，业务代码只需要关心其复杂的业务逻辑，其他的事情sidecar会帮其处理。例如，日志记录、监控、流量控制、服务注册、服务发现、服务限流、服务熔断、鉴权、访问控制和服务调用可视化等功能本质上讲和业务服务的关系并不大，完全可以交给sidecar。

Service Mesh有以下几个特点：·应用程序间通信的中间层；·轻量级网络代理；·应用程序无感知；·解耦应用程序的重试/超时、监控、追踪和服务发现。

Service Mesh将底层那些难以控制的网络通信统一管理，诸如流量管控、丢包重试、访问控制等。而上层的应用层协议只须关心业务逻辑。Service Mesh是一个用于处理服务间通信的基础设施层，它负责为构建复杂的云原生应用传递可靠的网络请求。

Kubernetes集群的每个节点都部署了一个Kube-proxy组件，该组件会与Kubernetes API Server通信，获取集群中的Service信息，然后设置iptables/ipvs规则，直接将对某个Service的请求发送到对应的后端Pod上。

Kube-proxy实现了流量在Kubernetes Service的负载均衡，但是没法对流量做细粒度的控制，例如灰度发布和蓝绿发布（按照百分比划分流量到不同的应用版本）等。

之所以说是透明代理，是因为应用程序容器完全无感知代理的存在。区别在于Kube-proxy拦截的是进出Kubernetes节点的流量，而Istio sidecar拦截的是进出该Pod的流量。

（8）Linkerd以metric和分布式追踪的形式捕获上述行为的各个方面，这些追踪信息将被发送到集中metric系统。

### 6.2 Istio：引领新一代微服务架构潮流

Istio分为两个平面：数据平面和控制平面。
数据平面由一组sidecar的代理（Envoy）组成。这些代理调解和控制微服务之间的所有网络通信，并且与控制平面的Mixer通信，接受调度策略。

控制平面通过管理和配置Envoy管理流量。此外，控制平面配置Mixers来实施路由策略并收集检测到的监控数据。

Envoy是Lyft开源的一个用C++开发的高性能代理，用于管理Service Mesh中所有服务的所有入站和出站流量。Envoy从功能上看是一个类似于Nginx的七层代理，但更加贴近Service Mesh的使用场景。Istio利用了Envoy的许多内置功能，例如：
·动态服务发现；
·负载均衡；
·TLS终止；
·HTTP/2和gRPC代理；
·断路器；
·健康检查；
·流量分割；
·故障注入；
·监控指标。

Pilot用于配置Envoy，提供服务发现、流量管理（例如A/B测试、金丝雀部署等）、异常控制（例如超时、重试、熔断）等功能。

Mixer是一个独立于平台的组件，负责在整个Service Mesh中执行访问控制和使用策略，并通过Envoy代理和其他服务收集监控到的数据。

Citadel通过内置身份和凭证管理，提供服务和用户的身份验证。我们可以使用Citadel升级Service Mesh中的未加密流量。

Istio对Pod和服务的要求。要成为服务网格的一部分，Kubernetes集群中的Pod和服务必须满足以下几个要求：

·需要对端口进行正确命名：服务端口必须进行命名。端口名称只允许是<协议>［-<后缀>-］模式，其中<协议>部分的可选择范围包括grpc、http、http2、https、mongo、redis、tcp、tls及udp，Istio可以通过对这些协议的支持提供路由能力。例如name:http2-foo和name:http都是有效的端口名，但name:http2foo就是无效的。如果没有对端口进行命名，或者命名没有使用指定前缀，那么这一端口的流量就会被视为普通TCP流量；

Pod端口：Pod必须包含每个容器将监听的明确端口列表。在每个端口的容器规范中使用containerPort。任何未列出的端口都将绕过Istio Proxy；

·Deployment应带有App及version标签：在使用Kubernetes Deployment进行Pod部署的时候，建议显式地为Deployment加上app及version标签。每个Deployment都应该有一个有意义的app标签和一个用于标识Deployment版本的version标签。

如果使用严格模式的mutual TLS方案，会在所有的客户端和服务器之间使用双向TLS。

在使用kubectl apply进行应用部署的时候，如果目标命名空间已经打上了标签istio-injection=enabled，Istio sidecar injector会自动把Envoy容器注入你的应用Pod中：

如果目标命名空间中没有打上istio-injection标签，则可以使用istioctl kubeinject命令，在部署之前手工把Envoy容器注入应用Pod中：

Virtual Services的作用是定义了针对Istio中一个微服务的请求的路由规则。Virtual Services既可以将请求路由到一个应用的不同版本，也可以将请求路由到完全不同的应用。

我们定义了熔断策略：

ServiceEntry用于将Istio外部的服务注册到Istio的内部服务注册表，以便Istio内部的服务可以访问这些外部的服务，如Istio外部的Web API。

定义了Istio外部的mongocluster与Istio内部的访问规则：

Gateway定义了Istio边缘的负载均衡器。所谓边缘，就是Istio的入口和出口。这个负载均衡器用于接收传入或传出Istio的HTTP/TCP连接。在Istio中会有ingressgateway和egressgateway，前者负责入口流量，后者负责出口流量。

### 6.3 一切尽在不言中：Istio sidecar透明注入

网格中的每个Pod都必伴随着一个Istio的sidecar一同运行。下文中将会介绍两种把sidecar注入Pod的方法：使用istioctl客户端工具进行注入，或者使用Istio sidecar injector自动完成注入过程，并且深入sidecar内部解析其工作原理

自动注入过程会在Pod的生成过程中进行注入，这种方法不会更改控制器的配置。手工删除Pod或者使用滚动更新都可以选择性地对sidecar进行更新。

Init容器是一种专用容器，它在应用程序容器启动之前运行，用来包含一些应用镜像中不存在的实用工具或安装脚本。

虽然Pod内所有容器都共享网络namespace，但Init容器和Pod内其他应用容器的文件系统是隔离的。例如，当赋予Init容器访问Kubernetes Secret的权限时，其他应用容器可能没有这个权限。

·Init容器istio-init：用于给sidecar容器（即Envoy代理）做初始化，设置iptables端口转发；·Envoy sidecar容器istio-proxy：运行Envoy代理。接下来将详细解析Init容器。

-p：指定重定向所有TCP流量的Envoy端口，默认Envoy的数据端口为15001；

如果确实需要查看Istio Pod中的iptables规则，我们需要登录sidecar容器查看。因为kubectl无法使用特权模式远程操作Docker容器，所以我们需要登录Pod所在的主机，使用docker exec命令登录容器查看。

Init容器启动时，命令行参数中指定了REDIRECT模式，因此只创建了nat表规则。

·PREROUTING链用于DNAT，将所有入站TCP流量跳转到ISTIO_INBOUND链上；

跟手工注入不同，自动注入过程是发生在Pod级别的，因此不会看到Deployment本身发生什么变化。但是可以使用kubectl describe观察单独的Pod，在其中能看到注入sidecar的相关信息。

给default命名空间设置标签istio-injection=enabled：

这样就会在Pod创建时触发sidecar的注入过程。删除运行的Pod，会产生一个新的Pod，新的Pod会被注入sidecar。原有的Pod只有一个容器，而被注入sidecar的Pod会有两个容器：

上面展示的内容中，可以清楚地看到，所有从80端口（red-demo应用监听的端口）进入的流量，被REDIRECTED到了端口15001。这是istio-proxy（Envoy代理）监听的端口。外发流量也是如此。

介绍了Istio将sidecar注入Pod，以及Istio将流量路由到代理服务器的全过程。

### 6.4 不再为iptables脚本所困：Istio CNI插件

istioinit的主要作用是运行脚本配置容器内的iptables规则

Istio CNI插件的主要设计目标是消除这个特权init container，使用Kubernetes CNI机制实现相同功能的替代方案，因此它是Kubernetes CNI的一个具体实现。

提到Kubernetes CNI插件是一条链，在创建和销毁Pod的时候会调用链上所有的插件来安装和卸载容器的网络。Istio CNI Plugin相当于在创建销毁Pod的这些钩子处针对Istio的Pod做网络配置，即配置iptables规则让访问该Pod的网络流量转发给Envoy。

Istio CNI插件工作原理
在每个节点上运行一个名为istio-cni-node的Daemonset，用于安装Istio CNI插件。启用Istio CNI插件后，sidecar的自动注入或手动注入（istioctl kube-inject）将不再注入init容器（istio-init）。

### 6.5 除了微服务，Istio还能做更多

它将在现代网络基础设施的路由和安全领域扮演非常重要的角色，甚至极有可能颠覆现有的网络基础设施，成为未来混合云的基石。

无论应用程序是运行在容器中，还是在虚拟机中，采用混合服务网格可以简化混合云应用程序管理，网络流量治理，并且实现安全性和可靠性。下面我们将讨论如何使用Istio来支持混合服务网格。

跨越两个不同的可用区（例如us-central和us-east，但底层网络仍然可达）部署多集群Istio。我们先在一个Kubernetes集群上安装Istio控制平面，在另一个Kubernetes集群上安装Istio的远程组件（包括sidecar代理注入器）。在这两个集群中，我们可以跨Kubernetes集群部署应用程序。

这种单一控制平面的部署无须改变任何有关微服务之间相互通信的信息。例如，只要两个集群底层网络打通，前端仍然可以使用本地Kubernetes DNS域名（cartservice:port）访问CartService。这是最基本的多集群Istio示例

使用两个Istio控制平面，每个集群各一个，形成一个双头逻辑服务网格。两个集群间的流量交互是通过Istio的IngressGateway，而不是使用sidecar代理直接相互通信。Istio Ingress Gateway也是一个Envoy代理，但它专门用于进出集群Istio网格的流

对于那些外部服务，流量在Istio Ingress网关之间透传，然后转移到相关服务。

为了使跨两个集群运行的微服务能够互相通信，我们还通过Istio ServiceEntry完成此操作。IstioServiceEntry用于手动添加不在当前服务网格内的服务，例如，我们将集群2的前端服务条目通过Istio ServiceEntry添加到集群1中，这样一来集群1就知道集群2中运行的服务。

这种双控制平面的Istio设置不需要集群之间的扁平网络，只需要把Istio网关暴露在Internet上即可。这意味着两个集群之间的Pod允许有重叠的网断，而且每个集群内的服务可以在各自的网络环境中安全运行。

该GCE虚拟机运行一组最小的Istio组件集，以便能够与中心的Istio控制平面通信。然后，我们将Istio ServiceEntry对象部署到GKE集群上，该集群在逻辑上将外部Product-CatalogService添加到Istio网格中。

### 推荐语

在Kubemetes技术体系飞速发展的过程中，其在弹性、可用性及可维护性方面曰趋成熟，同时在敏捷性、提升研发效率和降低系统复杂度方面也表现卓越。敏捷开发、基础设施服务化、DevOps、ContainerOps深度融合，这就让普通开发者也可以快速地进入软件的构建中，可以让企业更多地将精力聚焦在应用层逻辑开发中。