## 循序渐进学Docker
> 李金榜 尹烨 刘天斯 陈纯

### 第1章 全面认识Docker

Docker是基于Linux3.8以上内核，在aufs分层文件系统下构建的，主要运行在Ubuntu的系统下。REHL/Centos当时最新版6系列还是基于Liunux2.6.32内核，无法运行Docker。为了让REHL/Centos尽快支持Docker, RedHat公司的工程师亲自出马，加班加点为Docker贡献代码，新增对devicemapper的支持来实现文件系统分层，终于顺利地让Docker在REHL/Centos运行起来。

虽然Docker迟迟没有发布1.0版，但好多公司已纷纷把Docker应用到生产环境。其中，美国奢饰品电商Gilt的CTO说：“使用Docker以后，突然之间，传统方式中的各种问题都消失了，我们接下来要考虑如何进一步提高软件生产效率，让软件开发更加安全和创新。这种转变太不可思议了！”

2014大会，大会上来自Google、IBM、RedHat、Rackspace等公司的核心人物均发表了主题演讲，纷纷表示支持并加入Docker的阵营。Docker的CTO Solomon Hykes充满雄心壮志地说：“我们能把互联网升级到下一代！”Google的基础架构部副总裁Eric Brewer也附和道：“容器技术曾是Google的基础，我们和Docker联手，把容器技术打造为所有云应用的基石。”

❑Puppet是集中的配置管理系统，它把文件、用户、cron任务、软件包、系统服务等抽象为资源，并通过自有的语言描述资源间的依赖关系，集中管理各类资源的安装配置。Puppet主要适用于需要大批量部署相同服务的应用场景。
❑OpenStack是开源的云计算管理平台项目，可以帮助企业内部实现类似于Amazon EC2的云基础架构服务。虽然灵活，但组件繁多、构建复杂，比较适合中大型企业使用。

### 第8章 Docker网络和存储管理

网络是虚拟化技术中最复杂的部分，也是Docker应用中的一个重要环节。Docker中的网络主要解决容器与容器、容器与外部网络、外部网络与容器之间的互相通信的问题

Docker使用网桥（bridge）+NAT的通信模型，

仅仅解决Host内部的容器之间的通信是不够的，还需要解决容器与外部网络之间的通信，为此，Docker引入NAT。
（1）容器访问外部网络
为了解决容器访问外部网络，Docker创建如下MASQUERADE规则：

（2）外部网络访问容器
如果容器提供的服务需要暴露给外部网络，Docker在启动容器时，就会创建SNAT规则

这会创建下面的SNAT规则：
￼
iptables -t nat-A PREROUTING -m addrtype --dst-type LOCAL -j DOCKER￼
iptables  -t  nat-A  DOCKER  !  -i  docker0  -p  tcp  -m  tcp  --dport80  -j  DNAT--to-destination 172.17.0.2:80
实际上，第一条规则是在Docker进程启动时默认创建的，第二条规则是在启动容器时创建的。

❑bridge：这个Docker中的容器默认的方式

❑none：容器没有网络栈，也就是说容器无法与外部通信。

❑container:＜name|id＞：使用其他容器（name或者id指定）的网络栈。实际上， Docker会将该容器加入指定容器的network namespace，这是一种非常有用的方式。

❑host：表示容器使用Host的网络，没有自己独立的网络栈。

不会给容器创建单独的网络名字空间（network namespace）。由于容器可以完全访问Host的网络，所以此方式也是不安全的。

### 第10章 Docker Swarm容器集群

Docker Swarm项目开始于2014年，是Docker公司推出的第一个容器集群项目。项目的核心设计是将几台安装Docker的机器组合成一个大的集群，该集群提供给用户管理集群所有容器的操作接口与使用一台Docker几乎相同

■ 自动容错，一个Worker节点挂了，容器自动迁移到其他Worker节点。

■ 基于DNS服务与LVS技术实现服务发现和负载均衡。

使用Docker Machine创建三台虚拟机，Docker Machine会自动下载最新的boot2docker. iso，启动的虚拟机已经安装好Docker 1.12版本。

docker node ls命令查看Manager节点的状态。docker info命令新增加了Swarm模式下的集群简要配置和状态信息。docker node ls命令可以查看集群所有Manager和Worker节点的状态。我们可以从下面的输出看到当前集群只有一个Manager节点。

我们可以使用docker service ls命令查看所有的service列表。

### 第13章 Etcd、Cadvisor和Kubernetes实践

Etcd（https://github.com/coreos/etcd）是一个高可用的键值存储系统，主要用于共享配置和服务发现。Etcd是由CoreOS开发并维护的，灵感来自ZooKeeper和Doozer，它使用Go语言编写，

Etcd，主要用于分享配置和服务发现。Etcd具备如下特点：❑简单：支持curl方式的用户API (HTTP+JSON)。❑安全：可选SSL客户端证书认证。❑快速：单实例可达每秒1000次写操作。❑可靠：使用Raft实现分布式。

Etcd提供了两种操作方法，一种是基于HTTP的RESTful API，是一个使用HTTP并遵循REST原则的Web服务，通过不同URI来封装业务逻辑，由于基于HTTP协议，因此我们可以使用curl命令来操作，比较适合程序的调用。另一种是通过命令etcdctl操作，适合运维及系统管理人员使用。下面将详细介绍两种方式操作Etcd的常用方法。

### 第15章 Docker Overlay Network实践

从1.9版本开始，Docker开始支持Overlay Network，解决了跨主机通信的问题。在这之前，Docker本身没有一种好的跨主机通信方案，只能通过许多第三方工具来解决，如weave、flannel等。本章主要介绍Docker自身的Overlay Network的实现。

当docker daemon启动后，会自动创建三个网络：bridge、host、none。
￼
[root@node1 ～]# docker network ls￼

docker network ls￼

Docker自身的Overlay Network是基于VXLAN实现的。VXLAN协议是一个隧道协议，设计出来是为了解决VLAN ID（只有4096个）不够用的问题。VXLAN ID有三个字节（24bit），最多可以支持16 777 216个隔离的VXLAN网络。

VXLAN将以太网包封装在UDP中，并使用物理网络的IP/MAC作为outer-header进行封装，然后在物理网络上传输，到达目的地后由隧道端点解封并将数据发送给目标机器。这些对报文做封装和解封装的隧道端点被称为VTEP（Vlan Transport End Point）。对于Docker容器，宿主机Host即为VTEP。


[root@node1 ～]# ip netns ls￼
1-1dc144293a￼

当容器vm1（192.168.10.2）想访问容器vm2（192.168.10.3）时，首先就是向二层网络发送ARP广播请求，获取192.168.10.3对应的MAC地址。

实际上，我们可以给VXLAN设备配置IP/MAC映射关系，这样就不需要下层的IP组播网络了。Docker采用的正是这种方式：
￼
[root@node1 ～]# ip netns exe 1-1dc144293a ip neigh show dev vxlan1￼
192.168.10.3 lladdr 02:42:c0:a8:0a:03 PERMANENT