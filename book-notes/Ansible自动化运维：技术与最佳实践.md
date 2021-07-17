## Ansible自动化运维：技术与最佳实践
> 陈金窗 沈灿 刘政委编著

### 前言

随着DevOps运动的兴起，运维人员、研发人员、质量控制人员都从更大范围来看待自己的工作，打破运维、研发之间的壁垒，进行相互渗透、融合。DevOps项目在数量和体量上持续增长，支撑持续集成、持续交付的自动化工具不断涌现。

从系统管理者到开发者，都可使用Ansible自动化部署并维护整个应用的生命周期，实现持续交付。

### 第1章 Ansible架构及特点

IT行业的工作变得越来越有趣了，我们不再是把软件交付给客户，然后安装在单独的服务器上运行，我们都慢慢地变成了系统工程师。
我们现在部署应用软件的方式是通过服务串联起来，运行在一系列分布式的计算资源上并用各种不同的网络协议进行通信。常见的应用包括Web服务、应用服务、基于内存的缓存服务系统、任务队列、消息队列、SQL数据库、NoSQL数据存储、负载均衡等。

我们也需要确保采用合适的冗余，当故障发生时软件系统能够很好地处理、适应这些故障。另外有些辅助的服务需要部署、维护，例如日志管理、监控系统、分析系统，需要与第三方服务交互，如通过与IaaS接口交互来管理虚拟主机实例。

采用Ansible做为你的配置管理工具。无论你是一个开发人员想要把代码部署到生产环境，还是一个系统管理员寻找更好的自动化方法。我觉得Ansible对于这些问题都是很好的解决方案。

IT自动化配置管理最

Ansible的编排引擎可以出色地完成配置管理、流程控制、资源部署等多方面工作。与其他IT自动化产品相比较，Ansible为你提供一种不需要安装客户端软件、管理简便、功能强大的基础架构配置、维护工具。

管理主机便捷，支持多台主机并行管理。
·避免在被管理主机上安装客户代理，打开额外端口，采用无代理方式，只是利用现有的SSH后台进程。
·用于描述基础架构的语言无论对机器还是对人都是友好的。
·关注安全，很容易对执行的内容进行审计、评估、重写。
·能够立即管理远程被管理主机，不需要预先安装任何软件。

·成为最简单、易用的IT自动化系统。
在云计算时代的浪潮中，基础架构必须满足按需自动伸缩、按使用量计费的基本特性，IT自动化运维软件就是最重要的必备工具之一。下面来看看几个关键的领域中取得的巨大的进展。

配置管理领域已经涌现出多种工具，配置管理的目标就是确保被管理的主机尽可能快速、按照正确方式达到配置文件中描述的状态，这对管理IT环境至关重要。

通常所说的代码化基础架构（Infrastructure as code），因为构建基础架构所有必须的代码都存储在源码控制系统中。

2．服务即时开通
这个领域的工具主要是在数据中心、虚拟化环境、云计算中快速开通新的主机。几乎所有云计算的服务提供商都有相应的API接口，这些自动化工具通过这些API接口能够快速地创建主机实例。

3．应用部署
这个领域的工具重点关注如何尽可能地零停机部署应用。许多单位已经采用滚动式部署（rolling deployments）或金丝雀部署（canary deployments）, Ansible对这两种方式都支持。流水线式部署也是很常见的，常见的工具包括ThoughtWorks Go、Atlassian Bamboo、大量插件支持的Jenkins条，都是比较优秀的。

流程编排主要是进行部署时候如何保证基础架构中的各种组件协调一致。例如，在你对Web服务器部署新的软件版本时候，需要确保该Web服务器从负载均衡器上移出，这是很常见的场景。这类工具有Ansible、

一天，DeHaan在自己的沙发上开始用Python开发一个新工具，他的目标是：极为易用，连他自己都很想用；任何人可以在几分钟之内学会并使用。经过短短的6个月，第一个版本的工具诞生了，这就是Ansible。

Ansible只依赖SSH，无需在远程机器上安装代理，极为容易上手。Hacker News上有人称之为shell scripting++，很到位。

提供Ansible软件推广、咨询、培训服务，为支持Ansible发展的可持续提供保证。Ansible的服务团队成员都是经过精心挑选、久经沙场、经验丰富的IT专业人士，包括系统管理员、软件开发者、咨询服务精英、自动化运维专家。

Ansible提供老师讲解的课程，包括动手学习环境、实际问题处理、现场建设性问题解答。也致力于根据你的个性需求开发特定裁剪的课程，个性化的培训也可以根据要达到的目标签订专业的服务协议。

### 1.2 Ansible架构模式

被管机是运行业务服务的服务器，由控制机通过SSH来进行管理。

Ansible是一个模型驱动的配置管理器，支持多节点发布、远程任务执行。默认使用SSH进行远程连接。无需在被管节点上安装附加软件，可使用各种编程语言进行扩展。

这是Ansible用剧本（playbook）方式对3台运行Nginx服务的Ubuntu服务器进行配置管理。她编写一个叫webservers. yml的Ansible脚本。在Ansible中，这个脚本称为playbook。在playbook中描述了包含被管节点的hosts和对这些hosts按照顺序执行的任务列表（task）。

$ ansible-playbook webservers.yml

Ansible集合了众多优秀运维工具（Puppet、Cfengine、Chef、Func、Fabric）的优点，实现了批量系统配置、批量程序部署、批量运行命令等功能。

用户通过Ansible编排引擎操作公有云/私有云或CMDB（配置管理数据库）中的主机。

控制主机与被管节点之间支持local、SSH、ZeroMQ三种连接方式，默认使用基于SSH的连接。在规模较大的情况下使用ZeroMQ连接方式会明显改善执行速度。

根据需要找到适合你的角色后，只需要使用命令“ansible-galaxy install+作者ID．角色包名称”就可以安装到本地，

Ansible系统由控制主机对被管节点的操作方式可分为两类，即ad-hoc和playbook：
·ad-hoc模式使用单个模块，支持批量执行单条命令。

playbook通过多个task集合完成一类功能，如Web服务的安装部署、数据库服务器的批量备份等。可以简单地把playbook理解为通过组合多条ad-hoc操作的配置文件。

### 1.3 Ansible特性

Ansible是一款满足当代大规模、复杂环境的IT基础架构自动化管理的工具。Ansible相对与其他自动化解决方案，在核心能力上效率更高。也很好地解决了统一配置、统一部署、流程编排等复杂的IT自动化管理问题。

功能上看，Ansible可以实现以下目标：
·应用代码自动化部署。
·系统管理配置自动化。
·支持持续交付自动化。
·支持云计算、大数据平台（如AWS、OpenStack、CloudStack、ⅤMWare等）环境。
·轻量级，无需在客户端安装agent，更新时只需在控制机上进行一次更新即可。
·批量任务执行可以写成脚本，不用分发到远程就可以执行。
·使用Python编写，维护更简单，Ruby语法过于复杂。
·支持非root用户管理操作，支持sudo。

模块是包含一些参数的Ansible小程序，通过SCP或SFTP传送到远端被管机器的临时目录中进行执行，然后再删除。这些模块通过标准的输出方式返回JSON格式的数据，这些返回的数据通过控制主机上的Ansible程序进行处理。

使用Ansible，用户需要两个条件：能够访问到资源库，具有管理远端节点的凭证。当使用锁定的密钥（甚至是密码）时，不存在暴露安全漏洞中心被攻击点，也不允许从远端硬件来访问管理服务器。

而Ansible默认基于推送（push）方式，管理过程如下：
1）增加或修改playbook。
2）执行新的playbook。
3）控制节点的Ansible连接被管节点并执行修改被管节点状态的模块。

Ansible能够管理成千上万台主机，

使用Ansible能够在被管节点上执行任何shell命令，但是Ansible真正的威力在于内置大量的模块。使用这些模块可以完成如软件包安装、重启服务、拷贝配置文件等操作。Ansible模块是声明式的（declarative），你只需使用这些模块描述被管节点期望达到的状态。例

Ansible内置模块都是等幂性的（idempotent）。这就是如果系统中没有用户deploy, Ansible将会创建一个deploy账号；如果这个账号已经存在，则Ansible什么也不做。等幂性对于自动化维护是非常重要的特性，这样在被管节点上多次执行Ansible的playbook也能达到同样效果。相对于直接执行操作系统脚本的方式是巨大的改进，操作系统脚本再执行一次可能就会产生不同的、非预期的结果。

如果一个配置管理系统是收敛的，那么这个系统可以多次运行，都达到最终期望的状态，每次执行只是更加接近而已。

Ansible不同于这种方式，你需要使用apt模块来安装基于apt软件管理的系统，用yum模块来安装基于yum软件管理的系统。

Ansible社区中最主要的重用是模块，模块都是比较小的，与特定操作系统相关，具有良好的定义，容易实现，方便共享。Ansible项目是非常开放的，经常接纳社区贡献的模块代码。

这些自动化管理软件都具备强大的功能、灵活的系统管理和状态配置，都提供丰富的模板及API，对云计算平台、大数据都有很好的支持。
Puppet、Chef、SaltStack设置比较复杂，有代理和服务，有远程被管节点需要长期运行管理进程，而且很多术语和概念都是自己一套。Ansible相对于其他产品要简单得多，本质上混合了声明式和命令式。不仅适用于大规模的IT环境，而且对于一些规模较小的环境（20～50台机器）Ansible也是很实用的。


### 1.4 Ansible与DevOps

DevOps是一组过程、方法与系统的统称，用于促进软件开发（应用程序/软件工程）、技术运营和质量保障（QA）部门之间的沟通、协作与整合，如图1-6所示。

DevOps概念的引入能对产品交付、测试、功能开发和维护（包括常见的“热补丁”）起到意义深远的影响。在缺乏DevOps能力的组织中，开发与运营之间存在着信息“鸿沟”，例如运营人员要求更好的可靠性和安全性，开发人员则希望基础设施响应更快，而业务人员的需求则是更快地将更多的特性发布给最终用户使用。这种信息鸿沟就是最常出问题的地方。

需要频繁交付的单位更需要具有DevOps能力，大量互联网在线移动应用随时根据用户的反馈进行迭代开发，持续改进用户体验，这需要能够支撑业务部门每天可能都有几次、几十次部署的需求。这种能力也称为持续部署，并且经常与精益创业方法联系起来

·虚拟化和云计算基础设施日益普遍。
·数据中心自动化技术和配置管理工具的普及。

借助服务器自动化搭建和维护过程，可大大减少或完全消除服务器手工交付的过程，使用Ansible作为自动化工具，使用相同的playbook来搭建系统，就能够保证创建出来的系统是一样的。
甚至在基础架构层应用，也可以自动创建、交付、管理，将节省大量的时间，为单位的扩展团队提供额外的好处。

### 1.5 本章小结

Ansible关键的想法是，开发一个好的自动化系统，能认识到计算机是一组而不只是一个个分开的机器，也就是所谓“多层编排”。建

### 第2章 Ansible安装与配置

需要专门安装Python 2.Ⅹ解释器，并把资源清单（inventory）中的ansible_python_interpreter变量设置为该Python 2.Ⅹ。当然，也可以使用raw模块在被管节点上远程安装Python 2.Ⅹ。

### 2.2 安装Ansible

默认的资源清单inventory文件是 /etc/ansible/hosts，清单文件inventory可以指定其他位置：

### 2.3 配置运行环境

2）./ansible.cfg：其次，将会检查当前目录下的ansible.cfg配置文件。
3）～/.ansible.cfg：再次，将会检查当前用户home目录下的．ansible.cfg配置文件。
4）/etc/ansible/ansible.cfg：最后，将会检查在用软件包管理工具安装Ansible时自动产生的配置文件。

·forks——设置默认情况下Ansible最多能有多少个进程同时工作，默认设置最多5个进程并行处理。具体需要设置多少个，可以根据控制主机的性能和被管节点的数量来确定，可能是50或100，默认值5是非常保守的设置。配置实例如下：
￼
          forks = 5

timeout——这是设置SSH连接的超时间隔，单位是秒。配置实例如下：

·log_path——Ansible系统默认是不记录日志的，如果想把Ansible系统的输出记录到日志文件中，需要设置log_path来指定一个存储Ansible日志的文件。配

如果有台被管节点重新安装系统并在known_hosts中有了与之前不同的密钥信息，就会提示一个密钥不匹配的错误信息，直到被纠正为止。在使用Ansible时，如果有台被管节点没有在known_hosts中被初始化，将会在使用Ansible或定时执行Ansible时提示对key信息的确认。
如果你不想出现这种情况，并且你明白禁用此项行为的含义，只要修改home目录下～/.ansible.cfg或/etc/ansible/ansible.cfg的配置项：
￼
        [defaults]￼
        host_key_checking = False
或者直接在控制主机的操作系统中设置环境变量，如下所示：
￼
        $export ANSIBLE_HOST_KEY_CHECKING=False

推荐使用ssh-keygen与ssh-copy-id来实现快速证书的生成及公钥下发，其中ssh-keygen生产一对密钥，使用ssh-copy-id来下发生成的公钥。具体操作如下。

下发密钥就是控制主机把公钥id_rsa.pub下发到被管节点上用户下的．ssh目录，并重命名成authorized_keys，且权限值为400。接下来推荐常用的密钥拷贝工具ssh-copy-id，把公钥文件id_rsa.pub公钥拷贝到被管节点，命令格式如下：

### 2.4 Ansible小试身手

用ping模块对单台主机进行ping操作，结

这里测试时在控制主机与被管节点之间配置了SSH证书信任。如果没有用证书认证，则需要在执行Ansible命令时添加-k参数，在提示“SSH password:”时输入root（默认）账号密码。实际生产环境中，大多数更倾向于使用Linux普通用户账户进行连接并通过sudo命令实现root权限，格式为：
￼
                ansible webservers -m ping -u ansible -sudo

### 2.5 获取帮助信息

ansible-doc -l列出Ansible系统支持的模块，Ansible安装后能够列出259个模块，

在Ansible调试自动化脚本时候经常需要获取执行过程的详细信息，可以在命令后面添加-v或-vvv得到详细的输出结果。

### 第3章 Ansible组件介绍

然后使用Inventory内置变量定义了SSH登录密码。

定义了一个组叫docker。

docker组下面4台主机从172.17.42.101到172.17.42.103。

6行针对docker组使用Inventory内置变量定义了SSH登录密码。

Ansible还支持多个Inventory文件，这样我们就可以很方便地管理不同业务或者不同环境中的机器了。如何使用多个Inventoy文件呢？

最后我们修改了ansible.cfg文件中inventory的值，这里不再指向一个文件，而是指向一个目录，修改如下：
￼
        inventory   = /root/inventory/
这样我们就可以使用Ansible的list-hosts参数来进行如下验证：
￼
        [root@Master ～]# ansible 172.17.42.101:172.17.42.102--list-hosts￼

在实际应用部署中会有大量的主机列表。如果手动维护这些列表将是一个非常繁琐的事情。其实Ansible还支持动态的Inventory，动态Inventory就是Ansible所有的Inventory文件里面的主机列表和变量信息都支持从外部拉取。比如我们可以从CMDB系统和Zabbix监控系统拉取所有的主机信息，然后使用Ansible进行管理。这样一来我们就可以很方便地将Ansible与其他运维系统结合起来。

我们只需要把ansible.cfg文件中inventory的定义值改成一个执行脚本即可。这个脚本的内容不受任何编程语言限

了解动态Inventory实现流程。这里编写了一个简单hosts.py脚本

            parser = argparse.ArgumentParser()￼
            parser.add_argument(' -l' , ' --list' , help=' hosts list' , action=' store_￼
                                  true' )￼
            parser.add_argument(' -H' , ' --host' , help=' hosts vars' )￼
            args = vars(parser.parse_args())￼

### 3.2 Ansible Ad-Hoc 命令

Ansible命令都是并发执行的，我们可以针对目标主机执行任何命令。默认的并发数目由ansible.cfg中的forks值来控制。当然，也可以在运行Ansible命令的时候通过-f指定并发数。如果碰到执行任务时间很长的情况，也可以使用Ansible的异步执行功能来执行。下面我们简单地测试一下：

使用异步执行功能，-P 0的情况下会直接返回job_id，然后针对主机根据job_id查询执行结果

可以通过async_status模块查看异步任务的状态和结果

当-P参数大于0的时候，Ansible会自动根据jod_id去轮询查询执行结果：

首先通过openssl命令来生成一个密码，因为ansible user的password参数需要接受加密后的值，如下所示：
￼
        [root@Master ～]# echo ansible — openssl passwd -1-stdin￼

### 3.4 Ansible facts

facts组件是Ansible用于采集被管机器设备信息的一个功能，

facts组件默认已经收集了很多的设备基础信息，这些信息可以在做配置管理的时候进行引用。

然后运行facter模块查看facter信息：

如果直接运行setup模块也会采集facter信息

### 3.5 Ansible role

files目录里面存放一些静态文件，handlers目录里面存放一些task的handler。tasks目录里面就是我们平常写的playbook中的task。templates目录里面存放着jinja2模板文件。vars目录下存放着变量文件。

### 3.6 Ansible Galaxy

日常工作中我们使用ansible-galaxy install就可以，默认会安装到/etc/ansible/roles/目录下，其引用跟我们自己写的role引用方式一样。

### 第4章 playbook详解

第2行定义该playbook针对的目标主机，all表示针对所有主机

第3行定义该playbook所有的tasks集合

·第4行定义一个task的名称，非必须，建议根据task实际任务命名。
·第5行定义一个状态的action，比如这里使用yum模块实现Nginx软件包的安装。

·第6行到第9行使用template模板去管理/etc/nginx/nginx.conf文件，owner group定义该文件的属主以及属组，使用validate参数指文件生成后使用nginx -t-c %!s(MISSING)命令去做Nginx文件语法验证，notify是触发handlers，如果同步后，文件的MD5值有变化会触发ReStart Nginx Service这个handler。

关于nginx.conf.j2文件就是一个默认的nginx.conf文件，它只是针对Nginx的worker_processes参数通过facts信息中的CPU核心数目生成，其他的配置都是默认的。运行playbook之前我们需要确认playbook的语法信息是否正确。


        [root@python ～]# ansible-playbook   nginx.yaml --syntax-check￼
        playbook: nginx.yaml￼

可以指定task运行，这个时候只运行Copy Nginx.conf即可：

### 4.2 playbook变量与引用

比如上一节用到的那个hosts文件，然后分别针对每台主机设置一个变量名称叫作key，接着使用debug模块来查看变量的值

但是默认传进去的变量都是全局变量

Ansible playbook内task之间还可以互相传递数据，比如我们总共有两个tasks，其中第2个task是否执行是需要判断第1个task运行后的结果，这个时候我们就得在task之间传递数据，需要把第1个task执行的结果传递给第2个task。Ansible task之间传递数据使用register方式

info[' stdout' ]是一个标准的Python语言在字典中取值的用法，我们再来执行playbook如下所示：

### 4.3 playbook循环

直接使用标准的loops简单快速地实现10个软件包的安装了。

文件匹配loops是我们编写playbook的时候需要针对文件进行操作中最常用的一种循环，比如我们需要针对一个目录下指定格式的文件进行处理，这个时候直接在引用with_fileglob循环去匹配我们需要处理的文件即可

with_fileglob这个时候会匹配root目录下所有以yaml结尾的文件，当作{{ item}}变

                 until: host.stdout.startswith("Master")￼

                 debug: msg="{%!f(MISSING)or i in ret.results %!}(MISSING) {{ i.stdout }} {%!￼(MISSING)
                                endfor %!}(MISSING)"

### 4.4 playbook lookups

其实Ansible还支持从外部数据拉取信息，比如我们可以从数据库里面读取信息然后定义给一个变量的形式，这就是Ansible的lookups插件

pipe lookups的实现原理很简单，如果阅读过源码的读者能发现它其实就是在控制机器上调用subprocess.Popen执行命令，然后将命令的结果传递给变量

redis_kv是从Redis数据库中get数据，因为需要中控机连接Redis所以需要安装Redis Python库，使用pip方式安装就行，这里在本地安装了一个Redis服务器，然后给Ansible 设置了一个值，如下所示：

                conte n t s :   " { {   l o o k u p ( ' r e d i s _ k v ' ,   ' r e d i s : / /￼
                    localhost:6379, ansible' ) }}"￼

template跟file方式有点类似，都是读取文件，但是template在读取文件之前需要把jinja模板渲染完后再读取，下面我们指定一个jinja模板文件：

需要注意的是，lookups.j2里面定义的变量是每台主机自己的facts信息，不是控制机的facts信息，

### 4.5 playbook conditionals

目前Ansible的所有conditionals方式都是使用when进行判断，when的值是一个条件表达式，如果条件判断成立，这个task就执行某个操作，如果条件判断不成立，该task不执行或者某个操作会跳过。这里所说的成立与不成立就是Python语言里面的True与False，关于这个条件表达式也支持多个条件之间and或者or，

个when是判断facts信息，因为ansible_default_ipv4的数据结构是一个Python字典，ansible_default_ipv4.address是取IP地址然后与”192.168.1.118”进行判断

### 4.7 playbook内置变量

groups变量是一个全局变量，它会打印出Inventory文件里面的所有主机以及主机组信息，它返回的是一个JSON字符串，我们可以直接把它当作一个变量使用{{groups }}格式进行调用。当然我们还可以引用{{ groups }}字符串里面的数据

### 第5章 Ansible最佳实践

确实，使用默认的SSH方式通信，效率远低于SaltStack的zeromq消息队列

我们可以直接在ansible.cfg文件中设置SSH长连接即可。设置参数如下：
￼
￼
        sh_args = -o ControlMaster=auto -o ControlPersist=5d￼

ControlPersist=5d这个参数是设置整个长连接保持时间这里设置为5天。如果开启后，通过SSH连接过的设备都会在当前ansible/cp/目录下生成一个socket文件。也可以通过netstat命令查看，会发现有一个ESTABLISHED状态的连接一直与远端设备进行着TCP连接：
￼

pipelining也是OpenSSH的一个特性，我们在第1章的时候学习了Ansible的整个执行流程，其中有一个流程就是把生成好的本地Python脚本PUT到远端服务器。如果开启了pipelining，这个过程将在SSH的会话中进行，这样可以大大提高整个执行效率。当然开启pipelining，需要被控制机/etc/sudoers文件编辑当前Ansible SSH用户的配置为 requiretty。否则在执行Ansible的时候会提示sudo: sorry, you must have a tty to run sudo。下面我们通过一个示例展示整个过程。首先在ansible.cfg配置文件中设置pipelining为True。

gathering = smart￼
        fact_caching_timeout = 86400￼
        fact_caching = jsonfile￼
        fact_caching_connection = /dev/shm/ansible_fact_cache

### 5.2 目录结构


        production                   # production环境的inventory文件￼
        stage                        # stage环境的inventory文件￼


        group_vars/￼
            group1                   # group1定义的变量文件￼
            group2                   # group2定义的变量文件￼

host_vars/￼
            hostname1                # hostname1定义的变量文件￼
            hostname2                # hostname2定义的变量文件￼


        filter_plugins/              # 自定义filter插件存放目录￼

site.yml                     # playbook 统一入口文件￼
        webservers.yml               # 特殊任务playbook文件￼

roles/                       # role 存放目录￼
            common/                  # common 角色 目录￼
                tasks/               #￼
                    main.yml         # common 角色 task 入口文件￼
                handlers/            #￼
                    main.yml         # common 角色 handlers 入口文件￼

### 5.3 定义多环境

在笔者公司有一个CMDB系统里面存放着所有环境的机器，我们采用脚本的方式定期去CMDB里拉取每个环境每个业务角色的机器，然后生成三个Inventory文件（production、stage和feature），最后采用多Inventory方式进行引用。

### 5.4 灰度发布与检测

直接使用ansible-playbook命令的--syntax-check参数即可。

2．灰度发布
如果语法检测通过之后，我们需要对相关任务进行预运行，灰度发布是什么意思呢？就是先挑选一台机器进行测试，只有进行测试之后我们才知道整个配置流程是否达到我们想要结果。进行预运行时，我们只需要把一个或者多个task使用delegate_to参数指定到一台设备上进行测试。如果测试通过后，再进行接下来的工作。

此时我们就可以在运行playbook的时候使用--check和--diff参数去对比生成后的文件是否为我们所需的文件。

### 5.5 统一管理

在运维工作协作的时代，我们经常需要和别人一起去完成配置管理工作，如果Ansible作为统一配置管理工具使用，会有很多人参与到Ansible的playbook和role的编写。虽然前面已经介绍了如何去规范整个工作目录。但是每个人的工作方式不同，所以笔者建议把所有的Ansible工作目录使用gitlab或者GitHub私有仓库的形式进行管理。这样使得我们的远程工作协作变得很方便，每个人都可以进行自己的配置管理工作且互不影响。这里还需要做的就是规定一个git相关的规范，能保证每个git仓库按照规范去使用git进行相关的操作即可。

### 5.6 使用ansible-shell交互命令行

所以引入这个工具从很大程度上提高了我们使用Ad-Hoc命令的频率。

### 6.3 callback插件

callback是Ansible的一个回调功能，我们可以在运行Ansible的时候调用这个功能。比如希望在执行playbook失败后发邮件，或者希望将每次执行playbook的结果存到日志或者数据库中。在callback插件里面，我们可以很方便地拿到Ansible执行状态信息，然后可以定义一个callback动作，在playbook的某个运行状态下进行调用

当然Ansible本身也有日志记录功能，在ansible.cfg中开启log_path即可，但是callback可以记录到更加详细的信息，示例如下，脚本名称为log_plays.py：

如果想开启callback插件，需要把callback脚本放到callback_plugins指定的目录下。在ansible.cfg目录下可以指定callback_plugins的路径。下面我们就把官网的这个callback脚本放到callback_plugins下，然后执行Ansible playbook操作：

下面我们编写一个callback插件，把一些playbook执行状态信息写入MySQL里面。

### 6.5 Jinja2 filter

如果想深入学习Ansible，建议去深入研究Jinja2这个模板语言。我们平常编写Jinja2模板的时候经常会使用Jina2的语法以及它强大的filter。虽然Jinja2自带很多filter，但是在真正的应用场景中经常会遇到自带filter解决不了的问题，所以这节我们将讲解如何去定义自己的Jinja2 filter。

所以本节我们通过Ansible的filter plugins插件扩展方式去定义一个属于自己的filter。在ansible.cfg配置文件里面的filter_plugins值已经指定了filter_plugins的路径，我们只需在这个目录下编写我们的filter即可。

### 第7章 用ansible-vault保护敏感数据

Ansible提供了一个ansible-vault工具用于管理数据安全。ansible-vault可以通过调用编辑器界面创建新的加密文件，也可以加密已经创建好了的文件。不管哪一种方式，都需要输入vault口令，这个口令采用AES加密算法加密数据。加密后的内容可以存储在版本控制系统，不会泄密。由于AES是基于公共对称密钥，因此在解密时候需要提供相同的口令。对于提供口令有两种方式，在执行ansible时候，通过--ask-vault-pass选项提示输入口令，或者通过--vault-password-fi le选项提供包含口令的完整路径的文件。

高级加密标准（Advanced Encryption Standard, AES），在密码学中又称Rijndael加密法，是美国联邦政府采用的一种区块加密标准。这个标准用来替代原先的DES，已经被多方分析且广为全世界所使用

Ansible使用的AES是256位最长密钥长度。

下面是一些很好的加密对象：
·凭证，例如数据库口令、应用凭证。
·API密钥，例如远程访问密钥、私钥。
·Web服务器的SSL密钥。
·应用部署的SSH私钥。

### 7.2 使用ansible-vault

        $ansible-vault create aws_creds.yml￼
        Vault password:￼
        Confirm Vault password:

为了更新加密的aws _creds.yml，可以用anisble-vault的edit子命令进行修改

edit子命令将做以下操作：
1）提示输入口令。
2）使用AES对称密钥算法对文件进行解密。
3）打开编辑器界面，运行更新文件的内容。
4）在保存之后将文件再次加密。

先询问当前口令，输入正确之后才运行你进行设置新的口令和再次确认新口令。注意，如果你使用版本控制系统管理这个文件，即使你文件的内容没有变化，也需要再次进行提交，重置密钥的操作将会更新最终加密的文件。


### 7.3 典型应用场景

我们可以用mkpasswd命令使用SHA-512方法产生口令：
￼
        # Whois package contains "mkpasswd" command￼
        sudo apt-get install -y whois￼
        # Create a password for a user￼
        mkpasswd --method=SHA-512

5）执行playbook时带上--ask-vault-pass选项，将会提示输入口令：
￼

### 第8章 Ansible与云计算

我们在查看Ansible的自带模块时会发现Ansible对云计算这块是支持最多的一个配置管理工具

Ansible的云计算中的应用场景都是在本地使用Ansible已经封装好的云平台模块（API）去连接到云平台上新建一个或者多个实例，其自动根据云平台生成后的实例信息做下一步的任务，比如新建机器设备信息录入CMDB系统，对新建的机器进行自动化部署，对新建的机器添加监控，等等

### 8.2 Ansible AWS和OpenStack

如何使用Ansible来管理AWS

vars:￼
                ID: "AKIAI5F43DUPLEVF44wS"￼
                KEY: "3f6mzQAhiL0gnlgPaaentyyD3MfVv3Fwm9xsde"￼

                 environment:￼
                        AWS_ACCESS_KEY_ID: "{{ ID }}"￼
                        AWS_SECRET_ACCESS_KEY: "{{ KEY }}"￼

这里使用了Ansible的ec2模块去新建机器， 指定了该机器的一些属性，比如region、vpc_id、安全组id等信息。此task运行后，AWS会把新建这台机器的所有信息返回给ec2production。下面我们就可以针对这台机器做如下一些相关操作，例如：

### 8.3 Ansible与Docker

如果想远程使用Ansible管理Docker，一定得让dokcer服务监听一个TCP端口。

具体参数通过ansible-doc docker查看即可

### 8.4 Ansible Jenkins

Jenkins是一款基于Java开发的持续集成工具，Jenkins提供一个友好的平台可以让我们去完成那些重复的工作，目前Jenkins已经支持各种各样的插件，包括SaltStack、Ansible、 Puppet这类自动化配置管理工具。我们可以把经常重复的工作集成到Jenkins上，从而提高工作效率。Jenkins是一个强大的平台，它可以配合强大的插件库实现很多功能，

默认Jenkins服务是以Jenkins用户运行的，包括在Jenkins平台上的项目都是在Jenkins用户下运行的，这里为了下面调用Ansible插件，所以改成root用户了。最后启动服务即可：

### 第9章 部署Zabbix组件

Zabbix是目前最流行的一款企业级别的监控软件，它的易用性以及扩展性非常强大，加上自带丰富的API功能，使得它几乎成为每个互联网公司的标配运维组件。

### 9.2 编写业务roles

                 shell: mysql -u root -pansible -e ' create database zabbix_proxy￼
                      character set utf8 collate utf8_bin; '￼
              - name: Set Zabbix Master databases grant￼
                  shell:  mysql  -u  root  -pansible  -e  ' grant  all  privileges  on￼
                      zabbix_proxy.* to zabbix@localhost identified by "proxy"; '￼

### 9.3 安装部署

也可以通过制定tags来选择部署：

### 第10章 部署HAProxy+LAMP架构

通过Ansible部署一套HAProxy +LAMP架构，这个架构也是我们日常运维过程中经常遇到的一个架构，熟悉了一个架构的部署之后，可以非常容易地使用Ansible去扩展它。

HAProxy是一款性能卓越的提供高可用性、负载均衡以及基于TCP和HTTP应用的代理软件，目前很多大公司也在使用其做Web集群和cache集群的负载均衡以及代理。

### 10.2 编写业务roles

我们有4个设备角色然后创建5个roles，分别是初始化base角色、mysql角色、haproxy角色以及apache角色。

第一个task是引用yum模块进行haproxy包的安装。

第二个task是引用template模块进行动态管理haproxy.cfg文件，并且触发handlers。

### 10.3 配置部署以及测试

- name: Init base environment for all hosts￼
            hosts: all       #所有主机引用base角色￼
            roles:￼
                - base￼

 name: Install Haproxy￼
            hosts: haproxy #haproxy主机组引用haproxy角色￼
            roles:￼
                - haproxy

这里根据不同的主机组引用不同的roles进行相关的配置部署。

 --syntax-check￼

### 10.4 扩容与维护

比如上面我们部署的HAProxy+LAMP架构，如果哪天发现Apache业务有性能瓶颈，我们需要快速扩张整个架构，这个时候我们只需把新添加的机器添加到hosts文件中相应的业务角色组下即可。

### 第11章 大数据环境的应用实战

随着云时代的到来，大数据也吸引了越来越多的关注。大数据不在于掌握庞大的数据信息，而在于对这些含有意义的数据进行专业化处理，即在于提高对数据的“加工能力”，通过“加工”实现数据的“增值”。大数据的价值体现在以下方面：
·对大量消费者提供产品或服务的企业，可以利用大数据进行精准营销。
·做小而美模式的中长尾企业，可以利用大数据做服务转型。
·面临互联网压力之下必须转型的传统企业，需要与时俱进充分利用大数据的价值。

大数据与云计算就像一枚硬币的正反面一样密不可分。大数据必然需要采用分布式架构，对海量数据进行分布式数据挖掘，涌现出了大量创新、适用于大数据的技术，包括大规模并行处理（MPP）数据库、数据挖掘电网、分布式文件系统、分布式数据库、云计算平台、互联网和可扩展的存储系统等。

对于这种大规模的应用场景特别需要有自动化运维支撑手段，本章将结合某运营商大数据环境，介绍如何采用Ansible进行维护管理。

对运营商内部提供面向企业内部的客户行为和消费特征的分析挖掘，实现精确营销、精准维系、效益评价等数据应用业务需求；对合作伙伴通过数据出售、数据咨询、数据能力和数据解决方案四种业务形态实现数据资产的数据运营，最终实现数据资产的增值。

整个大数据平台系统涵盖了数据采集、导入和预处理、统计和分析、数据展现一系列过程。Hadoop已经是对大数据集进行分布式计算的标准工具，具有完整、充满活力的开源生态环境。构建整个大数据环境涉及部署Hadoop集群、HBase、Hive、MySQL、Nginx、Redis、Kafka、Flume、Zookeeper、Storm、Ganglia等软件。

### 11.2 准备大数据集群环境

参照Ansible最佳实践建议，整个配置管理主要包含在以下3个目录：
·group_vars：全局定义的变量参数。
·playbooks：下面又分为：conf（环境配置）、operation（日常维护）两个目录的脚本。
·roles：包含各个服务的角色，每个角色一般包含defaults、vars、files、tasks、handlers等目录。

1．定义环境变量参数
这些参数主要是配置系统句柄数、内存参数、TCP参数，将在模板文件中被引用，详见下面：
$cat ～/roles/commons/defaults/main.yml

# limits.conf variables￼
    #￼
    limits_conf:￼
        all_user_soft_limit: 1000001￼
        all_user_hard_limit: 1000001￼
￼
    #￼
    # sysctl.conf variables￼
    #￼
    sysctl_conf:￼


        vm_swappiness: 10￼
        vm_dirty_ratio: 10￼
        vm_min_free_kbytes: 65536￼
        vm_max_map_count: 262144￼
        # kernel parameter￼
        kernel_msgmnb: 100000￼
        kernel_msgmax: 100000￼
        # filesystem￼
        fs_file-max: 2097152￼
        # net core parameters￼


        net_core_somaxconn: 65536￼
        net_core_netdev_max_backlog: 65536￼
        # ipv4 Setting￼
        net_ipv4_tcp_moderate_rcvbuf: 0￼

            net_ipv4_tcp_tw_reuse: 1￼
            net_ipv4_tcp_tw_recycle: 1￼

在Linux环境中部署Hadoop，由于Hadoop有大量的连接请求，而Linux是有文件句柄限制的，默认配置不是很高，一般是1024，生产服务器很容易就达到这个数量，这将严重影响服务器的最大并发数：

设置主机节点的内核参数、内存参数、网络连接参数：
￼

### 14.3 使用Flask封装Ansible API

前面已经介绍了 Ansible的runner和palybook两个API，尽管这些API功能很强大，但是这些API功能我们不能远程调用。我们熟悉Ansible API后可以使用一些Python Web框架进行2次封装，方便我们远程调用。或者开发一个适合自己业务的Ansible Web平台等。

### 14.4 使用Celery实现任务异步化

虽然在上一节我们实现了远程调用Ansible API，但是细心的读者会发现，Ansible的API默认本身是阻塞的，例如我们在请求Ansible API的时候，如果Ansible本身没有完成这个工作，用户那边将一直处于等待状态。其实默认这样的应用对网民的体验感不是很好，下面我们来简单整合Ansible playbook API和Celery。

### 14.5 使用jQuery Ajax异步请求

在我们写Web平台的时候，如果提交一个任务运行时间过长，可能会导致504错误，我们已经使用Celery解决了这个问题。但是前端没法实时查看Celery的状态，这个时候就需要引入jQuery Ajax技术了。由于jQuery Ajax都是Web前端相关的技术，所以我这里只是简单介绍如何实现实时刷新的功能。

### 附录B YAML与Jinjia

Ansible 默认会使用 PyYAMl Python 库解析 playbook 的 YAML 文件内容。

>> print yaml.load(open(' ./test.yaml' , "r").read())￼

YAML 除了支持默认的缩进符号还支持 > 和 — 两种方式。这两种方式一般用于多行文本定义。定义的值经过 YAML load 之后都是 Python 的 str 数据类型。所以这两种缩进符号一般可以用作于具有多行值的定义，

### Jinja模板语言

在使用 Ansible 的时候，一般会用到 Jinja 的 filter 以及一些 for 和 if 判断语法。关于 Jinja 的 filter 前面的章节已经介绍过了，本附录只介绍 Jinja 的一些常用语法。Jinja 的语法默认都是在 {%} 包裹着。

Jinja中可以使用 set 定义临时变量，也可以直接使用Ansible其他地方定义变量。关于Jinja变量的引用都是采用 {{ 变量名 }}的方式

则只需 {{ dict[' key' ] }} 或者 {{ dict.get（' key' ） }}。其实这些都是 Python 的标准用法，在 Jinja 里面也可以直接使用。Python 的逻辑判断and or not在 Jinja 的判断中也可以直接使用。

Jinja 默认 {%} 定义的行渲染后都是空格，只需在 {%} 中使用 - 符号即可控制空白输出：

Jinja 的注释也非常简单，直接使用 # 注释即可，但是 # 注释的地方要加在大括号 { }内，例如：

Jinja 除了 filter 能对变量进行格式化或者加工，还支持直接对变量的值进行数学运算，比

### 附录C Ansiblepull模式

这个仓库会存放Ansible pull模式运行时所需的所有文件，包括Ansible的inventory和playbook文件

### a nsible-pull命令参数

            -i INVENTORY, --inventory-file=INVENTORY   指定inventory文件￼

            -o, --only-if-changed 只有git仓库有变更时才运行playbook￼

### 附录D SSHForward模式

因为Ansible 默认使用SSH服务进行远程管理，如果想要Ansible能实现跨机房的解决方案，首先我们得想办法解决SSH服务是否可行。其实SSH自带一种叫Agent Forward模式