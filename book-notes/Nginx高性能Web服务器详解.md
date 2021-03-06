## Nginx高性能Web服务器详解
> 苗泽编著

### 第1章 Nginx初探

Apache在设计时使用了以“进程”为基础的结构。大家都知道，进程要比线程消耗更多的系统开支，这导致Apache在多处理器环境中性能有所下降。因此，在对一个Apache Web站点进行扩容时，通常是增加服务器或扩充群集节点而不是增加处理器。

Tomcat是Sun公司官方推荐的Servlet和JSP容器，在中小型系统和并发访问用户不是很多的场合下，其作为轻量级应用服务器，被广泛地使用。它是开发和调试JSP程序的首选。

Lighttpd的急速发展得益于它专门针对高性能网站，提供了一套安全、快速、兼容性良好并且灵活的Web Server环境。同时，它具有非常低的内存开销、CPU占用率低以及模块丰富等特点，支持FastCGI、Output Compress（输出压缩）、URL重写等绝大多数Apache具有的重要功能，是Apache的绝好替代者。

在这段时间中， Nginx不断成长和发展，以其稳定的性能、丰富的功能集、低系统资源的消耗而逐渐被全球Web服务器使用者认可，在全球的市场份额节节攀升。

Nginx服务器以其功能丰富著称于世。它既可以作为HTTP服务器，也可以作为反向代理服务器或者邮件服务器；能够快速响应静态页面（HTML）的请求；支持FastCGI、SSL、Virtual Host、URL Rewrite、HTTP Basic Auth、Gzip等大量使用功能；并且支持更多的第三方功能模块的扩展。

Nginx服务器的负载均衡策略可以划分为两大类：即内置策略和扩展策略。内置策略主要包含轮询、加权轮询和IP hash三种；扩展策略主要通过第三方模块实现，种类比较丰富，常见的有url hash、fair等。

IP hash策略，是将前端的访问IP进行hash操作，然后根据hash结果将请求分配给不同的后端节点。事实上，这种策略可以看作是一种特殊的轮询策略。通过Nginx的实现，每个前端访问IP会固定访问一个后端节点。这样做的好处是避免考虑前端用户的session在后端多个节点上共享的问题。

url hash策略的优点在于，如果后端有缓存服务器，它能够提高缓存效率，同时也解决了session的问题；但其缺点是，如果后端节点出现异常，它不能自动排除该节点。在实际使用过程中笔者发现，后端节点出现异常会导致Nginx服务器返回503错误。

扩展的第三方模块fair则是从另一个角度来实现Nginx服务器负载均衡策略的。该模块将前端请求转发到一个最近负载最小的后台节点。那么，负载最小怎么判断呢？Nginx通过后端节点对请求的响应时间来判断负载情况。响应时间短的节点负载相对就轻。得出判断结果后，Nginx就将前端请求转发到选中的负载最轻的节点。

### 第2章 Nginx服务器的安装部署

获取PID有两个途径。一个是，在Nginx服务启动以后，默认在Nginx服务器安装目录下的logs目录中会产生文件名为nginx.pid的文件，此文件中保持的就是Nginx服务主进程的PID。

“-t”检查Nginx服务器配置文件是否有语法错误，可以与“-c”联用，使输出内容更详细，这对查找配置文件中的语法错误很有帮助，如果检查通过，将显示类似下面的信息：

HUP信号用于发送平滑重启信号。

events块涉及的指令主要影响Nginx服务器与用户的网络连接。常用到的设置包括是否开启对多worker process下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型处理连接请求，每个worker process可以同时支持的最大连接数等。

虚拟主机，又称虚拟服务器、主机空间或是网页空间，它是一种技术。该技术是为了节省互联网服务器硬件成本而出现的。这里的“主机”或“空间”是由实体的服务器延伸而来，硬件系统可以基于服务器群，或者单个服务器等。

每一个http块都可以包含多个server块，而每个server块就相当于一台虚拟主机，它内部可有多台主机联合提供服务

这些location块的主要作用是，基于Nginx服务器接收到的请求字符串（例如， server_name/uri-string），对除虚拟主机名称（也可以是IP别名，后文有详细阐述）之外的字符串（前例中“/uri-string”部分）进行匹配，对特定的请求进行处理

Nginx进程作为系统的守护进程运行，我们需要在某文件中保存当前运行程序的主进程号。Nginx支持对它的存放路径进行自定义配置，指令是pid，

指令worker_connections主要用来设置允许每一个worker process同时开启的最大连接数。其语法结构为：
￼
    worker_connections number;
此指令的默认设置为512。

与用户建立会话连接后，Nginx服务器可以保持这些连接打开一段时间，指令keepalive_timeout就是用来设置此时间的

在name中还可以使用正则表达式，并使用波浪号“～”作为正则表达式字符串的开始标记，如：
￼

Linux操作系统支持IP别名的添加。配置基于IP的虚拟主机，即为Nginx服务器提供的每台虚拟主机配置一个不同的IP，因此需要将网卡设置为同时能够监听多个IP地址。在Linux平台中可以使用ifconfig工具为同一块网卡添加多个IP别名。

eth1为使用中的网卡，其IP值为192.168.1.3。现在笔者需要为eth1添加两个IP别名192.168.1.31和192.168.1.32，分别用于Nginx服务器提供的两个虚拟主机，需要执行以下操作：
￼
    #ifconfig eth1:0 192.168.1.31 netmask 255.255.255.0 up￼
    #ifconfig eth1:1 192.168.1.32 netmask 255.255.255.0 up
命令中的up表示立即启用此别名

按照如上方法为eth1设置的别名在系统重启后将不予保存，需要重新设置。为了做到一劳永逸，我们可以将以上两条命令添加到Linux系统的启动脚本rc.local中。在笔者的系统中运行：
￼

=”，用于标准uri前，要求请求字符串与uri严格匹配。如果已经匹配成功，就停止继续向下搜索并立即处理此请求。

■ “～”，用于表示uri包含正则表达式，并且区分大小写。■ “～*”，用于表示uri包含正则表达式，并且不区分大小写。

问号被编码为“%!f(MISSING)”等。“^～”有一个特点是，它对uri中的这些符号将会进行编码处理。比如，如果location块收到的URI为“/html/%!/(MISSING)data”，则当Nginx服务器搜索到配置为“^～/html/ /data”的location时，可以匹配成功。

Web服务器接收到网络请求之后，首先要在服务器端指定目录中寻找请求资源。在Nginx服务器中，指令root就是用来配置这个根目录的，其语法结构为：

由于使用Nginx服务器多数情况下要配置多个location块对不同的请求分别做出处理，因此该指令通常在location块中进行设置。这个指令的一个示例为：

在location块中，除了使用root指令指明请求处理根目录，还可以使用alias指令改变location接收到的URI的请求路径

Nginx服务器设置网站错误页面的指令为error_page，语法结构为：

 uri，错误页面的路径或者网站地址。如果设置为路径，则是以Nginx服务器安装路径下的html目录为根路径的相对路径；如果设置为网址，则Nginx服务器会直接访问该网址获取错误页面，并返回给用户端。

只需要另外使用一个location指令定向错误页面到新的路径下面了就可以了。比如对于上面的第一个示例，我们希望Nginx服务器使用“/myserver/errorpages/404.html”页面响应404错误，那么在设置完：[插图]之后，我们再添加这样一个location块：

Nginx配置通过两种途径支持基本访问权限的控制，其中一种是由HTTP标准模块ngx_http_access_module支持的，其通过IP来判断客户端是否拥有对Nginx的访问权限，

Nginx还支持基于HTTP Basic Authentication协议的认证。该协议是一种HTTP性质的认证办法，需要识别用户名和密码，认证失败的客户端不拥有访问Nginx服务器的权限。该功能由HTTP标准模块ngx_http_auth_basic_module支持，这里有两个指令需要学习。auth_basic指令，用于开启或者关闭该认证功能，

auth_basic_user_file指令，用于设置包含用户名和密码信息的文件路径，语法结构为：

加密密码可以使用crypt()函数进行密码加密的格式，在Linux平台上可以使用htpasswd命令生成。在PHP和Perl等语言中，也提供crypt()函数。使用htpasswd命令的一个示例为：

### 第3章 Nginx服务器架构初探

Web服务器必须有能力同时为多个客户端提供服务。一般来说，完成并行处理请求工作有三种方式可供选择：多进程方式、多线程方式和异步方式。

多进程方式是指，服务器每当接收到一个客户端时，就由服务器主进程生成一个子进程出来和该客户端建立连接进行交互，直到连接断开，该子进程就结束了。
多进程方式的优点在于，设计和实现相对简单，各个子进程之间相互独立，处理客户端请求的过程彼此不受到干扰，并且当一个子进程产生问题时，不容易将影响漫延到其他进程中，这保证了提供服务的稳定性。当子线程退出时，其占用资源会被操作系统回收，也不会留下任何垃圾。而其缺点也是很明显的。操作系统中生成一个子进程需要进行内存复制等操作，在资源和时间上会产生一定的额外开销

多线程方式和多进程方式相似，它是指，服务器每当接收到一个客户端时，会由服务器主进程派生一个线程出来和该客户端进行交互。
由于操作系统产生一个线程的开销远远小于产生一个进程的开销，所以多线程方式在很大程度上减轻了Web服务器对系统资源的要求。

每个工作进程使用了异步非阻塞方式，可以处理多个客户端请求。当某个工作进程接收到客户端的请求以后，调用IO进行处理，如果不能立即得到结果，就去处理其他的请求；而客户端在此期间也无需等待响应，可以去处理其他的事情；当IO调用返回结果时，就会通知此工作进程；该进程得到通知，暂时挂起当前处理的事务，去响应客户端请求。

缓存索引重建进程完成的主要工作是，根据本地磁盘上的缓存文件在内存中建立索引元数据库。该进程启动后，对本地磁盘上存放缓存文件的目录结构进行扫描，检查内存中已有的缓存元数据是否正确，并更新索引元数据库。
缓存索引管理进程主要负责在索引元数据更新完成后，对元数据是否过期做出判断

Nginx服务器在使用Master-Worker模型时，会涉及主进程与工作进程（Master-Worker）之间的交互和工作进程（Worker-Worker）之间的交互。这两类交互都依赖于管道（channel）机制，交互的准备工作都是在工作进程生成时完成的

### 第4章 Nginx服务器的高级配置

1. net.core.netdev_max_backlog参数
参数net.core.netdev_max_backlog，表示当每个网络接口接收数据包的速率比内核处理这些包的速率快时，允许发送到队列的数据包的最大数目。一般默认值为128（可能不同的Linux系统该数值也不同）。Nginx服务器中定义的NGX_LISTEN_BACKLOG默认为511。我们可以将它调整一下：
￼
    net.core.netdev_max_backlog = 262144

2. net.core.somaxconn参数
该参数用于调节系统同时发起的TCP连接数，一般默认值为128。在客户端存在高并发请求的情况下，该默认值较小，可能导致链接超时或者重传问题，我们可以根据实际需要结合并发请求数来调节此值。笔者系统设置为：
￼
    net.core.somaxconn = 262144

于是适当增大此指令的赋值，允许Nginx服务器接收较大的请求头部，可以改善服务器对客户端的支持能力。笔者一般将此指令赋值为4KB大小，即：
￼
    client_header_buffer_size 4k;

### 第6章 Nginx服务器的Rewrite功能

name是给后端服务器组起的组名。花括号中列出后端服务器组中包含的服务器，其中可以使用下面介绍的其他指令。

如果所有的组内服务器都出错，则返回最后一个服务器的处理结果。当然我们可以根据各个服务器处理能力或者资源配置情况的不同，给各个服务器配置不同的权重，让能力强的服务器多处理请求，能力弱的少处理。配置权重的变量包含在server指令中。

· max_fails=number，设置一个请求失败的次数。在一定时间范围内，当对组内某台服务器请求失败的次数超过该变量设置的值时，认为该服务器无效（down）

· backup，将某台组内服务器标记为备用服务器，只有当正常的服务器处于无效（down）状态或者繁忙（busy）状态时，该服务器才被用来处理客户端请求。

指令用于实现会话保持功能，将某个客户端的多次请求定向到组内同一台服务器上，保证客户端与服务器之间建立稳定的会话。只有当该服务器处于无效（down）状态时，客户端请求才会被下一个服务器接收和处理。

量与正则表达式之间用“～”、“～*”、“!～”或“!～*”连接，“～”表示匹配过程中对大小写敏感，“～*”表示匹配过程中对大小写不敏感；使用“!～”和“!～*”，匹配失败时if指令认为条件为true，否则为false。在正则表达式中，可以使用小括号对变量值进行截取，在花括号中使用$1…$9引用截取的值

set指令该指令用于设置一个新的变量，其语法结构为：

就是说，Nginx服务器访问二级目录时不加斜杠“/”无法访问。但是我们不可能要求客户端始终按照我们的要求在末尾添加斜杠“/”，那么如何来解决这个问题呢？我们可以使用Rewrite功能为末尾没有斜杠“/”的URL自动添加一个斜杠“/”：

客户端只要输入“http://www.myweb.name/server-12-34-56-78-9.htm”即可访问到9.htm这个资源文件了。这个URL是不是“看上去”简单多了呢？这其实是充分利用了rewrite指令支持正则表达式的特性。

### 第7章 Nginx服务器的代理服务

代理（Proxy）服务，通常也称为正向代理服务

局域网内的机器借助代理服务访问局域网外的网站，这主要是为了增强局域网内部网络的安全性，使得网外的威胁因素不容易影响到网内，这里代理服务器起到了一部分防火墙的功能。同时，利用代理服务器也可以对局域网对外网的访问进行必要的监控和管理。

正向代理服务器与反向代理服务器的概念很简单，归纳起来就是，正向代理服务器用来让局域网客户机接入外网以访问外网资源，反向代理服务器用来让外网的客户端接入局域网中的站点以访问站点中的资源。理解这两个概念的关键是要明白我们当前的角色和目的是什么，在正向代理服务器中，我们的角色是客户端，目的是要访问外网的资源；在反向代理服务器中，我们的角色是站点，目的是把站点的资源发布出去让其他客户端能够访问。

该指令可以更改Nginx服务器接收到的客户端请求的请求头信息，然后将新的请求头发送给被代理的服务器。

负载均衡主要通过专门的硬件设备实现或者通过软件算法实现。通过硬件设备实现的负载均衡效果好、效率高、性能稳定，但是成本比较高。通过软件实现的负载均衡主要依赖于均衡算法的选择和程序的健壮性。

我们设置了两组被代理的服务器组，名为“videobackend”的一组用于对请求video资源的客户端请求进行负载均衡，另一组用于对请求file资源的客户端请求进行负载均衡。通过对location块uri的不同配置，我们就很轻易地实现了对特定资源的负载均衡。

### 第8章 Nginx服务器的缓存机制

我们在第7章学习Nginx服务器的反向代理服务时，已经对Proxy Cache缓存机制的原理和配置指令进行了详细介绍。该机制是Nginx服务器自己实现的类似于Squid的缓存机制，它使用md5算法将请求链接hash后生成文件系统目录保存响应数据。

### 第17章 Nginx在动态网站建设中的应用实例

Nginx服务器本身对流行的JSP、PHP、Perl等动态页面的支持并不完整，但是它可以通过反向代理完成对动态页面的请求，而其本身可以提供高效的负载均衡、代理缓存等功能。

从上面的描述可以看出，当出现大量动态请求时，CGI每次都必须生成新的工作进程，这样它的工作效率就会降低，而其对系统资源的占用也相当高。为了解决这一问题，就出现了FastCGI模式。可以将FastCGI理解为一个常驻在系统中的CGI，被激活以后，它不会每次都花费时间去生成新的进程。该模式还有一个优点，就是支持分布式的运算。FastCGI模式目前支持的语言非常多，有PHP、C/C++、Java、Perl、Tcl、Python、SmallTalk、Ruby等。Apache、IIS、Lighttpd、Nginx等流行的Web服务器上支持FastCGI模式。