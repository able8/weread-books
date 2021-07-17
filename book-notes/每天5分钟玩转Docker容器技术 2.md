## 每天5分钟玩转Docker容器技术
> CloudMan

### 第1章 鸟瞰容器生态系统

（1）容器规范
容器不光是Docker，还有其他容器，比如CoreOS的rkt。为了保证容器生态的健康发展，保证不同容器之间能够兼容，包含Docker、CoreOS、Google在内的若干公司共同成立了一个叫Open Container Initiative（OCI）的组织，其目的是制定开放的容器规范。
目前OCI发布了两个规范：runtime spec和image format spec。


（2）容器runtime
runtime是容器真正运行的地方。runtime需要跟操作系统kernel紧密协作，为容器提供运行环境。

lxc、runc和rkt是目前主流的三种容器runtime，如图1-4所示。
￼

lxc是Linux上老牌的容器runtime。Docker最初也是用lxc作为runtime。
runc是Docker自己开发的容器runtime，符合oci规范，也是现在Docker的默认runtime。

lxd是lxc对应的管理工具。
runc的管理工具是docker engine。docker engine包含后台deamon和cli两个部分。我们通常提到Docker，一般就是指的docker engine。

所谓编排（orchestration），通常包括容器管理、调度、集群定义和服务发现等。通过容器编排引擎，容器被有机地组合成微服务应用，实现业务需求。

（3）基于容器的PaaS
基于容器的PaaS为微服务应用开发人员和公司提供了开发、部署和管理应用的平台，使用户不必关心底层基础设施而专注于应用的开发。

（2）服务发现
动态变化是微服务应用的一大特点。当负载增加时，集群会自动创建新的容器；负载减小，多余的容器会被销毁。容器也会根据host的资源使用情况在不同host中迁移，容器的IP和端口也会随之发生变化。
在这种动态的环境下，必须要有一种机制让client能够知道如何访问容器提供的服务。这就是服务发现技术要完成的工作。
服务发现会保存容器集群中所有微服务最新的信息，比如IP和端口，并对外提供API，提供服务查询功能。
etcd、consul和zookeeper是服务发现的典型解决方案，如图1-15所示。

### 2.2 Why——为什么需要容器

任何货物，无论钢琴还是保时捷，都被放到各自的集装箱中。集装箱在整个运输过程中都是密封的，只有到达最终目的地才被打开。标准集装箱可以被高效地装卸、重叠和长途运输。现代化的起重机可以自动在卡车、轮船和火车之间移动集装箱。集装箱被誉为运输业与世界贸易最重要的发明，如图2-7所示。


Docker将集装箱思想运用到软件打包上，为代码提供了一个基于容器的标准化运输系统。Docker可以将任何应用及其依赖打包成一个轻量级、可移植、自包含的容器。

2.2.3 容器的优势
对于开发人员：Build Once、Run Anywhere。
容器意味着环境隔离和可重复性。开发人员只需为应用创建一次运行环境，然后打包成容器便可在其他机器上运行。另外，容器环境与所在的Host环境是隔离的，就像虚拟机一样，但更快更简单。

对于运维人员：Configure Once、Run Anything。
只需要配置好标准的runtime环境，服务器就可以运行任何容器。这使得运维人员的工作变得更高效、一致和可重复。容器消除了开发、测试、生产环境的不一致性。

### 2.4 小结

使用Docker以及容器技术，我们可以快速构建一个应用服务器、一个消息中间件、一个数据库、一个持续集成环境。因为Docker Hub上有我们能想到的几乎所有的镜像。
不知大家是否意识到，潘多拉盒子已经被打开。容器不但降低了我们学习新技术的门槛，更提高了效率。

### 3.1 镜像的内部结构

（1）FROM scratch镜像是从白手起家，从0开始构建。
（2）COPY hello/将文件“hello”复制到镜像的根目录。
（3）CMD["/hello"]容器启动时，执行/hello。
镜像hello-world中就只有一个可执行文件“hello”，其功能就是打印出“Hello from Docker ......”等信息。
/hello就是文件系统的全部内容，连最基本的 /bin、/usr、/lib、 /dev都没有。
hello-world虽然是一个完整的镜像，但它并没有什么实际用途。通常来说，我们希望镜像能提供一个基本的操作系统环境，用户可以根据需要安装和配置软件。
这样的镜像我们称作base镜像。

内核空间是kernel, Linux刚启动时会加载bootfs文件系统，之后bootfs会被卸载掉。
用户空间的文件系统是rootfs，包含我们熟悉的 /dev、/proc、/bin等目录。
对于base镜像来说，底层直接用Host的kernel，自己只需要提供rootfs就行了。
而对于一个精简的OS, rootfs可以很小，只需要包括最基本的命令、工具和程序库就可以了。相比其他Linux发行版，CentOS的rootfs已经算臃肿的了，alpine还不到10MB。

3．支持运行多种Linux OS
不同Linux发行版的区别主要就是rootfs。
比如Ubuntu 14.04使用upstart管理服务，apt管理软件包；而CentOS 7使用systemd和yum。这些都是用户空间上的区别，Linux kernel差别不大。

1）base镜像只是在用户空间与发行版一致，kernel版本与发行版是不同的。
例如CentOS 7使用3.x.x的kernel，如果Docker Host是Ubuntu 16.04（比如我们的实验环境），那么在CentOS容器中使用的实际上是Host 4.x.x的kernel，如图3-9所示。

所有容器都共用host的kernel，在容器中没办法对kernel升级。如果容器对kernel版本有要求（比如应用只能在某个kernel版本下运行），则不建议用容器，这种场景虚拟机可能更合适。

### 3.3 RUN vs CMD vs ENTRYPOINT

RUN指令通常用于安装应用和软件包。
RUN在当前镜像的顶部执行命令，并创建新的镜像层。Dockerfile中常常包含多个RUN指令。
RUN有两种格式：
（1）Shell格式：RUN
（2）Exec格式：RUN ["executable", "param1", "param2"]
下面是使用RUN安装多个包的例子：
RUN apt-get update && apt-get install-y\bzr\cvs\git\mercurial\￼
    subversion
注意：apt-get update和apt-get install被放在一个RUN指令中执行，这样能够保证每次安装的是最新的包。如果apt-get install在单独的RUN中执行，则会使用apt-get update创建镜像层，而这一层可能是很久以前缓存的。

CMD有三种格式：
（1）Exec格式：CMD ["executable", "param1", "param2"]
这是CMD的推荐格式。
（2）CMD["param1", "param2"]为ENTRYPOINT提供额外的参数，此时ENTRYPOINT必须使用Exec格式。
（3）Shell格式：CMD command param1 param2


但当后面加上一个命令，比如docker run -it [image] /bin/bash, CMD会被忽略掉，命令bash将被执行：
root@10a32dc7d3d3:/#

ENTRYPOINT指令可让容器以应用程序或者服务的形式运行。
ENTRYPOINT看上去与CMD很像，它们都可以指定要执行的命令及其参数。不同的地方在于ENTRYPOINT不会被忽略，一定会被执行，即使运行docker run时指定了其他命令。

（2）如果Docker镜像的用途是运行应用程序或服务，比如运行一个MySQL，应该优先使用Exec格式的ENTRYPOINT指令。CMD可为ENTRYPOINT提供额外的默认参数，同时可利用docker run命令行替换默认参数。

（3）如果想为容器设置默认的启动命令，可使用CMD指令。用户可在docker run命令行中替换此默认命令。

### 3.5 小结

rmi只能删除host上的镜像，不会删除registry的镜像。
如果一个镜像对应了多个tag，只有当最后一个tag被删除时，镜像才被真正删除。

### 4.1 运行容器

4.1.1 让容器长期运行
如何让容器保存运行呢？
因为容器的生命周期依赖于启动时执行的命令，只要该命令不结束，容器也就不会退出。

1. docker attach
通过docker attach可以attach到容器启动命令的终端，

3. attach VS exec
attach与exec主要区别如下：
（1）attach直接进入容器启动命令的终端，不会启动新的进程。
（2）exec则是在容器中打开新的终端，并且可以启动新的进程。
（3）如果想直接在终端中查看启动命令的输出，用attach；其他情况使用exec。
当然，如果只是为了查看启动命令的输出，可以使用docker logs命令，如图4-13所示。

### 4.3 pause / unpause容器

4.3 pause / unpause容器
有时我们只是希望让容器暂停工作一段时间，比如要对容器的文件系统打个快照，或者dcoker host需要使用CPU，这时可以执行docker pause

### 4.4 删除容器

docker rm是删除容器，而docker rmi是删除镜像。


### 4.6 资源限制

（1）-m或 --memory：设置内存的使用限额，例如100MB,2GB。

### 6.1 storage driver

Ubuntu默认driver用的是AUFS，底层文件系统是extfs，各层数据存放在/var/lib/docker/aufs。
Redhat/CentOS的默认driver是Device Mapper, SUSE则是Btrfs。

### 6.2 Data Volume

Data Volume本质上是Docker Host文件系统中的目录或文件，能够直接被mount到容器的文件系统中。Data Volume有以下特点：
（1）Data Volume是目录或文件，而非没有格式化的磁盘（块设备）。
（2）容器可以读写volume中的数据。
（3）volume数据可以被永久地保存，即使使用它的容器已经销毁。

bind mount是将host上已存在的目录或文件mount到容器。

可见，即使容器没有了，bind mount也还在。这也合理，bind mount是host文件系统中的数据，只是借给容器用用，哪能随便就删了啊。
另外，bind mount时还可以指定数据的读写权限，默认是可读可写，可指定为只读

使用单一文件有一点要注意：host中的源文件必须要存在，不然会当作一个新目录bind mount给容器。

6.2.2 docker managed volume
docker managed volume与bind mount在使用上的最大区别是不需要指定mount源，指明mount point就行了

每当容器申请mount docker manged volume时，docker都会在 /var/lib/docker/volumes下生成一个目录（例子中是 /var/lib/docker/volumes/f4a0a1018968f47960efe760829e3c5738c 702533d29911b01df9f18babf3340/_data），这个目录就是mount源。


简单回顾一下docker managed volume的创建过程：
（1）容器启动时，简单地告诉docker"我需要一个volume存放数据，帮我mount到目录/abc"。
（2）docker在/var/lib/docker/volumes中生成一个随机目录作为mount源。
（3）如果/abc已经存在，则将数据复制到mount源。
（4）将volume mount到/abc。
除了通过docker inspect查看volume，我们也可以用docker volume命令

目前，docker volume只能查看docker managed volume，还看不到bind mount；同时也无法知道volume对应的容器，这些信息还得靠docker inspect。
我们已经学习了两种data volume的原理和基本使用方法，下面做个对比：
（1）相同点：两者都是host文件系统中的某个路径。
（2）不同点：如表6-1所示。


### 6.3 数据共享

docker cp可以在容器和host之间复制数据，当然我们也可以直接通过Linux的cp命令复制到 /var/lib/docker/volumes/xxx。

### 6.4 volume container

volume container是专门为其他容器提供volume的容器。它提供的卷可以是bind mount，也可以是docker managed volume。

我们将容器命名为vc_data（vc是volume container的缩写）。注意这里执行的是docker create命令，这是因为volume container的作用只是提供数据，它本身不需要处于运行状态。容器mount了两个volume：
（1）bind mount，存放Web Server的静态文件。
（2）docker managed volume，存放一些实用工具（当然现在是空的，这里只是做个示例）。

其他容器可以通过--volumes-from使用vc_data这个volume container，如

可见，三个容器已经成功共享了volume container中的volume。
下面我们讨论一下volume container的特点：
（1）与bind mount相比，不必为每一个容器指定host path，所有path都在volume container中定义好了，容器只需与volume container关联，实现了容器与host的解耦。
（2）使用volume container的容器，其mount point是一致的，有利于配置的规范和标准化，但也带来一定的局限，使用时需要综合考虑。

### 6.6 Data Volume生命周期管理

对于docker managed volume，在执行docker rm删除容器时可以带上 -v参数，docker会将容器使用到的volume一并删除，但前提是没有其他容器mount该volume，目的是保护数据，非常合理。

### 8.7 比较各种网络方案

最朴素的判断是：Underlay网络性能优于Overlay网络。
Overlay网络利用隧道技术，将数据包封装到UDP中进行传输。因为涉及数据包的封装和解封，存在额外的CPU和网络开销。虽然几乎所有Overlay网络方案底层都采用Linux kernel的vxlan模块，这样可以尽量减少开销，但这个开销与Underlay网络相比还是存在的。所以Macvlan、Flannel host-gw、Calico的性能会优于Docker overlay、Flannel vxlan和Weave。


### 9.1 Docker自带的监控子命令

9.1.3 stats
docker container stats用于显示每个容器各种资源的使用情况

### 9.3 Weave Scope

Weave Scope的最大特点是会自动生成一张Docker Host地图，让我们能够直观地理解、监控和控制容器。

除了监控容器，Weave Scope还可以监控Docker Host。单击顶部HOSTS菜单项，地图将显示当前host，

前面我们已经领略了Weave Scope的丰富功能和友好的操作界面，不过它还有一个重要功能：多主机监控。

### 9.4 cAdvisor

9.4 cAdvisor
cAdvisor是google开发的容器监控工具，我们来看看cAdvisor有什么能耐。
在host中运行cAdvisor容器。

cAdvisor会显示当前host的资源使用情况，包括CPU、内存、网络、文件系统等，如图9-39～图9-42所示。

### 9.5 Prometheus

Prometheus是一个非常优秀的监控工具。准确地说，应该是监控方案。Prometheus提供了监控数据搜集、存储、处理、可视化和告警一套完整的解决方案。

### 9.6 比较不同的监控工具

3．多Host监控
Weave Scope和Prometheus可以监控整个集群，而其余的工具只提供单个Host的监控能力。

### 9.7 几点建议

（4）Weave Scope流畅简洁的操控界面是其最大亮点，而且支持直接在Web界面上执行命令。
（5）Prometheus的数据模型和架构决定了它几乎具有无限的可能性。Prometheus和Weave Scope都是优秀的容器监控方案。除此之外，Prometheus还可以监控其他应用和系统，更为综合和全面。

### 第10章 日志管理

高效的监控和日志管理对保持生产系统持续稳定的运行以及排查问题至关重要。
在微服务架构中，由于容器的数量众多以及快速变化的特性使得记录日志和监控变得越来越重要。考虑到容器短暂和不固定的生命周期，当我们需要debug问题时有些容器可能已经不存在了。因此，一套集中式的日志管理系统是生产环境中不可或缺的组成部分。

### 10.2 Docker logging driver

Docker的默认logging driver是json-file。

json-file会将容器的日志保存在json文件中，Docker负责格式化其内容并输出到STDOUT和STDERR。
我们可以在Host的容器目录中找到这个文件，容器路径为/var/lib/docker/containers/<contariner ID>/<contariner ID>-json.log。

### 10.3 ELK

10.3 ELK
在开源的日志管理方案中，最出名的莫过于ELK了。ELK是三个软件的合称：Elasticsearch、Logstash、Kibana。


1. Elasticsearch
一个近乎实时查询的全文搜索引擎。Elasticsearch的设计目标就是要能够处理和搜索巨量的日志数据。
2. Logstash
读取原始日志，并对其进行分析和过滤，然后将其转发给其他组件（比如Elasticsearch）进行索引或存储。Logstash支持丰富的Input和Output类型，能够处理各种应用的日志。
3. Kibana
一个基于JavaScript的Web图形界面程序，专门用于可视化Elasticsearch的数据。Kibana能够查询Elasticsearch并通过丰富的图表展示结果。用户可以创建Dashboard来监控系统的日志。

Logstash负责从各个Docker容器中提取日志，Logstash将日志转发到Elasticsearch进行索引和保存，Kibana分析和可视化数据。

前面我们已经知道Docker会将容器日志记录到 /var/lib/docker/containers/<contariner ID>/<contariner ID>-json.log，那么只要我们能够将此文件发送给ELK就可以实现日志管理。

因为ELK提供了一个配套小工具Filebeat，它能将指定路径下的日志文件转发给ELK。同时Filebeat很聪明，它会监控日志文件，当日志更新时，Filebeat会将新的内容发送给ELK。

2．配置Filebeat
Filebeat的配置文件为 /etc/filebeat/filebeat.yml，我们需要告诉Filebeat两件事：
● 监控哪些日志文件？
● 将日志发送到哪里？

在paths中我们配置了两条路径：
（1）/var/lib/docker/containers/*/*.log是所有容器的日志文件。
（2）/var/log/syslog是Host操作系统的syslog。
接下来告诉Filebeat将这些日志发送给ELK。
Filebeat可以将日志发送给Elasticsearch进行索引和保存；也可以先发送给Logstash进行分析和过滤，然后由Logstash转发给Elasticsearch。
为了不引入过多的复杂性，我们这里将日志直接发送给Elasticsearch，

### 10.4 Fluentd

Fluentd是一个开源的数据收集器，它目前拥有超过500种plugin，可以连接各种数据源和数据输出组件。在接下来的实践中，Fluentd会负责收集容器日志，然后发送给Elasticsearch。

这里用Filebeat将Fluentd收集到的日志转发给Elasticsearch。这当然不是唯一的方案，Fluentd有一个plugin fluent-plugin-elasticsearch可以直接将日志发送给Elasticsearch。条条道路通罗马，开源世界给予了我们多种可能性，可以根据需要选择合适的方案。

### 10.5 Graylog

10.5 Graylog
Graylog是与ELK可以相提并论的一款集中式日志管理方案，支持数据收集、检索、可视化Dashboard。本节将实践用Graylog来管理Docker日志。

Graylog负责接收来自各种设备和应用的日志，并为最终用户提供Web访问接口。
Elasticsearch用于索引和保存Graylog接收到的日志。
MongoDB负责保存Graylog自身的配置信息。

### 第11章 数据管理

从业务数据的角度看，容器可以分为两类：无状态（stateless）容器和有状态（stateful）容器。
无状态是指容器在运行过程中不需要保存数据，每次访问的结果不依赖上一次访问，比如提供静态页面的Web服务器。
有状态是指容器需要保存数据，而且数据会发生变化，访问的结果依赖之前请求的处理结果，最典型的就是数据库服务器。

### 11.1 从一个例子开始

Docker是如何实现这个跨主机管理data volume方案的呢？
答案是volume driver。
任何一个data volume都是由driver管理的，创建volume时如果不特别指定，将使用local类型的driver，即从Docker Host的本地目录中分配存储空间。如果要支持跨主机的volume，则需要使用第三方driver。