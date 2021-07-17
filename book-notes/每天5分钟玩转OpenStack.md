## 每天5分钟玩转OpenStack
> CloudMan

### 前言

后来入职一家大型IT公司，公司的产品从中间件到操作系统，从服务器到存储，从虚拟化到云计算都有涉及。

本人所在的部门是专门做IT基础设施实施服务的，项目涉及服务器、存储、网络、虚拟化、云各个方面，而且这个部门的重要任务是为公司在IT市场最新和最热门的领域开疆扩土。比如前几年的虚拟化，这两年的云计算和大数据

1．OpenStack涉及的知识领域极广
可以说涵盖了IT基础设施的所有范围，计算、存储、网络、虚拟化、高可用、安全、灾备无所不包，即便是像我这种每天都在这个领域工作的人也感觉压力颇大。

OpenStack的各个组件都采用Driver的架构，支持各种具体的实现技术。比如OpenStack的存储服务Cinder只定义了上层抽象API，具体的实现交给下面的各种Driver，比如基于LVM的iSCSI Driver，EMC、IBM等商业存储产品的Driver，或者是开源的分布式存储软件，比如Ceph、GlusterFS的Driver。

之前说了，我在公司的职位是架构师，但骨子里我更把自己定位成一位能到一线攻城拔寨的实施工程师。

我觉得：对于知识，只有把它写出来并能够让其他人理解才能真正说明自己掌握了这项知识。

### 第1章　虚拟化

简单地说，虚拟化使得在一台物理的服务器上可以跑多台虚拟机，虚拟机共享物理机的CPU、内存、IO硬件资源，但逻辑上虚拟机之间是相互隔离的。

Hypervisor作为OS上的一个程序模块运行，并对虚拟机进行管理。KVM、VirtualBox和VMWare Workstation都属于这个类型，如图1-2所示。

1．型虚拟化一般对硬件虚拟化功能进行了特别优化，性能上比2型要高；
2．型虚拟化因为基于普通的操作系统，会比较灵活，比如支持虚拟机嵌套。嵌套意味着可以在KVM虚拟机中再运行KVM。

KVM全称是Kernel-Based Virtual Machine。也就是说KVM是基于Linux内核实现的。

KVM有一个内核模块叫kvm.ko，只用于管理虚拟CPU和内存。

那IO的虚拟化，比如存储和网络设备由谁实现呢？
这个就交给Linux内核和Qemu来实现。
说白了，作为一个Hypervisor，KVM本身只关注虚拟机调度和内存管理这两个方面。IO外设的任务交给Linux内核和Qemu。

如果有输出vmx或者svm，就说明当

一个KVM虚机在宿主机中其实是一个qemu-kvm进程，与其他Linux进程一样被调度。

即虚机的vCPU总数可以超过物理CPU数量，这个叫CPU overcommit（超配）。

如果每个虚机都很忙，反而会影响整体性能，所以在使用overcommit的时候，需要对虚机的负载情况有所了解，需要测试。

KVM需要实现VA（虚拟内存）→ PA（物理内存）→ MA（机器内存）之间的地址转换。虚机OS控制虚拟地址到客户内存物理地址的映射（VA →PA），但是虚机OS不能直接访问实际机器内存，因此KVM需要负责映射客户物理内存到实际机器内存（PA → MA）。具体的实现就不做过多介绍了，大家有兴趣可以查查资料。

还有一点提醒大家，内存也是可以overcommit的，即所有虚机的内存之和可以超过宿主机的物理内存。但使用时也需要充分测试，否则性能会受影响。

网络虚拟化中最重要的两个东西：Linux Bridge和VLAN。

（1）将物理网卡eth0直接分配给VM1。
但实施这个方案随之带来的问题很多：宿主机就没有网卡，无法访问了；新的虚机，比如VM2也没有网卡。

（2）给VM1分配一个虚拟网卡vnet0，通过Linux Bridge br0将eth0和vnet0连接起来，如图1-38所示。这个是我们推荐的方案。

Linux Bridge是Linux上用来做TCP/IP二层协议交换的设备，其功能大家可以简单地理解为是一个二层交换机或者Hub。多个网络设备可以连接到同一个Linux Bridge，当某个设备收到数据包时，Linux Bridge会将数据转发给其他设备。

反过来，VM1发送数据给vnet0，br0也会将数据转发到eth0，从而实现了VM1与外网的通信。

VM2的虚拟网卡vnet1也连接到了br0上。现在VM1和VM2之间可以通信，同时VM1和VM2也都可以与外网通信。

virbr0是KVM默认创建的一个Bridge，其作用是为连接其上的虚机网卡提供NAT访问外网的功能。

LAN表示Local Area Network，本地局域网，通常使用Hub和Switch来连接LAN中的计算机。
一般来说，两台计算机连入同一个Hub或者Switch时，它们就在同一个LAN中。
一个LAN表示一个广播域，其含义是：LAN中的所有成员都会收到任意一个成员发出的广播包。
VLAN表示Virtual LAN。一个带有VLAN功能的switch能够将自己的端口划分出多个LAN。

VLAN将一个交换机分成了多个交换机，限制了广播的范围，在二层上将计算机隔离到不同的VLAN中。

比方说，有两组机器，Group A和B。我们想配置成Group A中的机器可以相互访问，Group B中的机器也可以相互访问，但是A和B中的机器无法互相访问。
一种方法是使用两个交换机，A和B分别接到一个交换机。
另一种方法是使用一个带VLAN功能的交换机，将A和B的机器分别放到不同的VLAN中。
请注意，VLAN的隔离是二层上的隔离，A和B无法相互访问指的是二层广播包（比如arp）无法跨越VLAN的边界。

办法是将A和B连起来，而且连接A和B的端口要允许VLAN1、2、3三个VLAN的数据都能够通过。这样的端口就是Trunk口了。

eth0.10就是VLAN设备了，其VLAN ID就是VLAN 10。

一个母设备（eth0）可以有多个子设备（eth0.10，eth0.20 ……），而一个子设备只有一个母设备。

（1）VM2向VM1发Ping包之前，需要知道VM1的IP 192.168.100.10所对应的MAC地址。VM2会在网络上广播ARP包，其作用就是询问“谁知道192.168.100.10的MAC地址是多少？”

（2）ARP是二层协议，VLAN的隔离作用使得ARP只能在VLAN20范围内广播，只有brvlan20和eth0.20能收到，VLAN10里的设备是收不到的。VM1无法应答VM2发出的ARP包。

同一VLAN端口之间可以交换转发，不同VLAN端口之间隔离。所以交换机包含两层功能：交换与隔离。

（2）Linux的VLAN设备实现的是隔离功能，但没有交换功能。

一个VLAN母设备（比如eth0）不能拥有两个相同ID的VLAN子设备，因此也就不可能出现数据交换情况。

3）Linux Bridge专门实现交换功能。
将同一VLAN的子设备都挂载到一个Bridge上，设备之间就可以交换数据了。

总结起来，Linux Bridge加VLAN在功能层面完整模拟现实世界里的二层交换机。
eth0相当于虚拟交换机上的trunk口，允许vlan10和vlan20的数据通过。
eth0.10，vnet0和brvlan10都可以看着vlan10的access口。
eth0.20，vnet1和brvlan20都可以看着vlan20的access口。

### 第2章　云计算

我见过的客户早期都是这种架构，一套应用一套服务器，通常系统的资源使用率都很低，达到20%!的(MISSING)都是好的。

虚拟化技术的发展大大提高了物理服务器的资源使用率。这个阶段，物理机上运行若干虚拟机，应用系统直接部署到虚拟机上。虚拟化的好处还体现在减少了需要管理的物理机数量，同时节省了维护成本。

虚拟化提高了单台物理机的资源使用率，随着虚拟化技术的应用，IT环境中有越来越多的虚拟机，这时新的需求产生了：如何对IT环境中的虚拟机进行统一和高效的管理。有需求就有供给，云计算登上了历史舞台。计算（CPU/内存）、存储和网络是IT系统的三类资源。通过云计算平台，这三类资源变成了三个池子。当需要虚机的时候，只需要向平台提供虚机的规格。平台会快速地从三个资源池分配相应的资源，部署出这样一个满足规格的虚机。虚机的使用者不再需要关心虚机运行在哪里，存储空间从哪里来，IP是如何分配，这些云平台都搞定了。

IaaS的使用者通常是数据中心的系统管理员。典型的IaaS例子有AWS、Rackspace、阿里云等。

•  PaaS（Platform as a Service）提供的服务是应用的运行环境和一系列中间件服务（比如数据库、消息队列等）。
使用者只需专注应用的开发，并将自己的应用和数据部署到PaaS环境中。
PaaS负责保证这些服务的可用性和性能。PaaS的使用者通常是应用的开发人员。典型的PaaS有Google App Engine、IBM BlueMix等。

•  SaaS（Software as a Service）提供的是应用服务。
使用者只需要登录并使用应用，无须关心应用使用什么技术实现，也不需要关心应用部署在哪里。SaaS的使用者通常是应用的最终用户。典型的SaaS有Google Gmail、Salesforce等。

OpenStack对数据中心的计算、存储和网络资源进行统一管理

### 第4章　搭建实验环境

OpenStack是一个分布式系统，由若干不同功能的节点（Node）组成：控制节点（Controller Node）。管理OpenStack，其上运行的服务有Keystone、Glance、Horizon以及Nova和Neutron中管理相关的组件。控制节点也运行支持OpenStack的服务，例如SQL数据库（通常是MySQL）、消息队列（通常是RabbitMQ）和网络时间服务NTP。

存储节点（Storage Node）。提供块存储（Cinder）或对象存储（Swift）服务。

计算节点（Compute Node）。其上运行Hypervisor（默认使用KVM）。同时运行Neutron服务的agent，为虚拟机提供网络支持。

### 第6章　Image Service——Glance

Image Service的功能是管理Image，让用户能够发现、获取和保存Image。在OpenStack中，提供Image Service的是Glance，其具体功能如下：提供REST API，让用户能够查询和获取image的元数据和image本身。支持多种方式存储image，包括普通的文件系统、Swift、Amazon S3等。对Instance执行Snapshot创建新的image。

Glance自己并不存储image，真正的image是存放在backend中的。Glance支持多种backend，包括：A directory on a local file system（这是默认配置）GridFSCeph RBDAmazon S3

OpenStack排查问题的方法主要是通过日志，Service都有自己单独的日志。Glance主要有两个日志，glanceapi.log和glanceregistry.log，保存在/var/log/apache2/目录里。

### 第9章　Networking Service ——Neutron

传统的网络管理方式已经很难胜任这项工作，而“软件定义网络（software-defined networking，SDN）”所具有的灵活性和自动化优势使其成为云时代网络管理的主流。Neutron的设计目标是实现“网络即服务（Networking as a Service）”。为了达到这一目标，在设计上遵循了基于SDN实现网络虚拟化的原则，在实现上充分利用了Linux系统上的各种网络相关的技术。

Neutron为整个OpenStack环境提供网络支持，包括二层交换，三层路由，负载均衡，防火墙和VPN等。Neutron提供了一个灵活的框架，通过配置，无论是开源还是商业软件都可以被用来实现这些功能。

Nova的Instance是通过虚拟交换机连接到虚拟二层网络的。Neutron支持多种虚拟交换机，包括Linux原生的Linux Bridge和Open vSwitch。Open vSwitch（OVS）是一个开源的虚拟交换机，它支持标准的管理接口和协议。利用Linux Bridge和OVS，Neutron除了可以创建传统的VLAN网络，还可以创建基于隧道技术的Overlay网络，比如VxLAN和GRE（Linux Bridge目前只支持VxLAN）。

Instance可以配置不同网段的IP，Neutron的router（虚拟路由器）实现instance跨网段通信。router通过IPforwarding、iptables等技术来实现路由和NAT。