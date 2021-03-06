## cURL必知必会
> 丹尼尔·斯坦伯格

### 1.1 它是如何开始的

为了让汇率尽可能准确，机器人每天从汇率网站下载汇率数据。要想完成这个任务，需要使用一个小工具通过HTTP下载数据。当时，我找到了一款名为HttpGet的小工具（由巴西人Rafael Sagula开发）。这个工具可以用于下载数据，只是需要做一些调整。我很快就接管了这个工具几百行代码的维护任务。

### 1.2 命名问题

命名是一件很困难的事。
这个工具用于上传和下载URL指定的数据。它会显示数据（默认情况下），用户可以看到（see）URL，并且see可以简写为单个字母c。此外，它是一个客户端程序，一个URL客户端，字母c也可以表示客户端（client），因此就有了cURL这个名字。

1.2.1 发音问题
大多数人用k作为curl的开头音，就像英语单词curl那样。它与girl和earl很押韵。


### 1.3 curl可以做什么

展开：
❑ 命令行工具curl；
❑ 提供C API的libcurl库。
这个工具和库都基于网络协

libcurl库可以用于各种需要网络传输的嵌入式设备：车载信息娱乐系统、电视机、蓝光播放器、机顶盒、打印机、路由器、游戏系统等

2000年，curl的核心引擎被抽离成一个库，同年8月，libcurl 7.1发布，其中包含了所有沿用至今的概念。从那时起，curl就成了一个基于libcurl构建命令行工具的逻辑层。
想要在软件、平台、架构中添加客户端文件传输功能的人都可以使用libcurl，这也是libcurl的设计目标。libcurl的许可协议非常自由，避免给人们带来任何麻烦。
libcurl是用传统的C语言开发的，不过也有使用其他语言开发的版本，人们为其他语言创建了相应的libcurl绑定。


### 1.5 curl的用户

用于PHP的libcurl绑定是libcurl的第一批真正得到了广泛应用的绑定之一。它很快就成了PHP用户传输数据的默认方式，由于这样的状态保持了十多年，PHP已经被证明是互联网领域相当流行的技术（最近的数据显示，大概四分之一的网站都在使用PHP）。
一些流量很大的站点也在使用PHP，并在后端使用了libcurl，比如Facebook和雅虎。

### 第2章 命令行基础

curl是一个可执行的二进制文件，但cURL项目本身不提供二进制文件。curl的二进制文件需要根据不同的操作系统进行构建，而且通常受不同版本系统的约束。

### 2.1 命令行选项

curl的命令行解析器会解析整行命令，你可以将选项放在任意位置，它们甚至可以出现在URL之后。

curl -d '{ "name": "Darth" }' http://example.com
如果想要避免使用单引号，则可以通过文件将数据传给curl，这样就无须使用额外的引用。假设json文件包含了上述数据：
￼
        curl -d @json http://example.com

### 2.3 URL

即使是在高带宽的网络中，建立TCP连接（尤其是TLS连接）也是一个缓慢的过程。
curl在内部维护着一个连接池，这可以让之前使用过的连接继续存活一段时间，因此后续发给相同主机的请求可以重用这些已经建立的连接。
连接池中的连接可以在curl运行期间保持活跃状态，但最好还是在同一个命令行中完成多次传输，而不是单独运行多个curl命令行

### 2.4 URL通配

curl提供了“通配”（globbing）的方式来指定这类URL。

有时URL的不同部分不会遵循这些简单的模式，那么你可以指定完整的列表，但要放在花括号，而不是中括号中。
￼
        curl -O http://example.com/{one, two, three, alpha, beta}.html

URL中的每个通配都对应一个单独的变量，可以通过 ’#[num]’ 来引用，即在 ’#’ 后面跟上与通配对应的数字，从1（对应第一个通配）开始，以最后一个通配结束。
保存两个不同网站的主页：
￼
        curl http://{one, two}.example.com -o "file_#1.txt"

### 2.8 进度指示器

curl有一个内置的进度指示器。当调用curl来传输数据（上传或下载）时，它可以在终端上显示传输的进度，比如当前的传输速率、已经用掉的时间以及还需要多长时间才能完成传输。
如果curl需要在终端上输出内容，那么进度指示器就会被禁用，否则进度指示器会干扰输出内容，把要显示的内容弄得一团糟。用户还可以通过-s或--silent选项强制关闭进度指示器。
如果调用curl时没有看到进度指示器，则需要确保输出已经被重定向到终端以外的位置。
curl还提供了一个更简单的进度指示器，可以通过-#或--progress-bar来启用。正如其名字所暗示的那样，它使用进度条的方式来显示传输进度。
有时候，在

### 3.1 详细模式

＊ Connected to example.com (93.184.216.34) port 80 (#0)
curl连接到了目标网站，还说明了域名是如何映射到IP地址的，以及它连接到了哪个端口上。'(#0)’是curl为这个连接分配的内部数字。如果在同一命令行中指定多个URL，那么你就会看到它将使用多个连接或重用已有的连接，因此连接的计数器可能会增加，也可能不会增加，具体取决于curl需要执行的操作。

--trace [filename]选项可以将完整的跟踪信息保存在指定的文件中，也可以使用’-'（单个减号）代替文件名，将内容打印到stdout。你可以按照以下方式使用：
￼
        $ curl --trace dump http://example.com
命令执行完后，会生

3.1.2 --trace-time
如果使用了这个选项，则所有的输出信息前面都会被加上高精度的时间戳。它可以与常规的-v和--verbose选项以及--trace和--trace-ascii选项一起使用，如下所示

在使用HTTP/2进行文件传输时，curl将发送和接收被压缩的标头信息。因此，为了以可读和可理解的方式显示传出和传入的HTTP/2标头信息，curl将显示未经压缩的版本，就像它们在HTTP/1.1版本中显示的那样。

如果使用’-’作为文件名，那么curl就会从标准输入（stdin）读取字符串：

❑ %!{(MISSING)remote_ip}显示最近一次连接的远程IP地址——可以是IPv4或IPv6地址。

❑ %!{(MISSING)ssl_verify_result}显示请求的SSL对等证书验证结果，0表示验证成功。

### 3.2 持久连接

在建立TCP连接时，curl将保留旧连接一段时间，如果下一次要连接到同一主机，那么就可以重用相同的连接，从而节省大量时间。我们称之为持久连接。curl将始终尝试保留连接，并尽可能重用现有连接。

### 3.3 下载

这就是-O（大写字母O）选项，或者--remote-name。-O选项从你提供的URL中提取文件名部分作为本地文件名。这点非常重要。

文件重定向会将curl的所有输出重定向到单个文件中，因此，即使你要求将多个URL输出发送到stdout，重定向机制也会将所有URL的输出保存在单个文件中。
￼
        curl http://example.com/1 http://example.com/2 > files

3.3.11 客户端差异
curl只会获取你要求它获取的内容，但永远不会解析获取到的内容，也就是服务器发送的数据。

浏览器在获取数据后会根据内容类型激活不同的解析器。例如，如果数据是HTML，那么它会进行解析并显示网页，还可能会下载其他子资源，如图像、JavaScript和CSS文件。curl在下载HTML时只会获取单个HTML资源，而在浏览器中，则可能会触发更多下载。如果你希望curl也下载子资源，那么需要将这些URL传给curl并要求它获取这些URL，就像其他URL一样。

如果要确保curl命令行不会下载过大文件，那么可以令curl在开始下载前就停止此操作，前提是curl要在传输前知道文件大小！这样做是为了避免占用太多带宽、花费太长时间或是因为你的硬盘没有足够的存储空间：

curl在开始重试传输前会先等待一秒，对于后续的重试，等待时间加倍，直到达到10分钟，然后剩余的重试延迟就是10分钟。--retry-delay选项可以禁用这种指数退避算法，并设置自己的重试延迟。--retry-max-time选项可以限制所有重试的总时间。--max-time选项用于指定单个传输允许的最长时间。

### 3.5 连接

基于TCP的客户端（如curl）必须先找到目标主机的IP地址，然后连接到这台主机上。“连接”意味着需要进行TCP协议握手。

HTTP客户端一般会通过Host：标头告诉HTTP服务器它要连接到哪个服务器，因为通常同一个HTTP服务器实例会有多个名字。
因此，将自定义的Host：标头传给curl，就可以让服务器返回目标站点的内容，即使没有使用目标站点的主机名进行连接。

连接超时只能限制curl在建立连接前花费的时间，一旦成功建立TCP连接，则不再受这个时间的限制。

在具有多个网络接口并连接到多个网络的计算机上，有时你可以决定使用哪个网络接口来传出网络流量，或者在通信中使用哪个原始IP地址（在你拥有的多个IP地址之外）。
--interface选项可以告诉curl你希望将通信的本地端“绑定”到哪个网络接口、哪个IP地址或主机名：

避免慢连接（或空闲连接）被视为死亡并被错误杀死的其中一种方法是确保使用了TCP的keepalive。keepalive是TCP协议的一项特性，它会来回发送“ping数据帧”，避免连接进入完全空闲的状态。它帮助空闲连接检测中断（即使在没有流量的情况下），并让中间系统不会认为连接已死亡。
因此，curl默认使用TCP keepalive。但有时你可能希望禁用keepalive，或者希望更改TCP“ping”之间的时间间隔（curl默认为60秒）。你可以用以下命令关闭keepalive：

3.6.1 允许的最长时间
在curl抛出超时错误码（28）并退出前，可以用-m或--max-time选项告诉curl最长有多少时间（以秒为单位）可用。当指定的时间耗尽，无论当时发生什么，curl都会退出，即使它正在传输数据。
指定的最长时间可以用小数表示，0.5表示500毫秒，2.37表示2370毫秒：

作为固定超时时间的替代方案，你可以告诉curl，如果传输速率低于某个特定值，并且在某个时间段内一直低于这个值，那么就放弃传输。
例如，如果15秒内的传输速率低于1000字节/秒，则停止传输：
￼

.6.4 保持连接活跃
curl默认启用TCP keepalive。TCP栈会在没有流量时向另一端发送探测，以确保对方仍然存在，并且还“活着”。通过使用keepalive, curl更有可能发现已死亡的TCP连接。
你可以使用--keepalive-time来指定发送探测的频率，默认为60秒。
有时这种探测机制会干扰你正在做的事情，此时可以用--no-keepalive来禁用它。

### 3.8 代理

代理是代表客户端执行某项操作的机器或软件。
你可以将它视为中间人，位于你和目标服务器之间。你连接的是这个中间人，而不是实际的远程服务器。

某些网络环境为不同场景提供了不同类型的代理，浏览器为此提供了一种名为“代理自动配置”（Proxy Auto Config, PAC）的定制化处理方式。
PAC文件包含了一个JavaScript函数，用于决定给定的网络连接（URL）应该使用哪个代理，或者也可以不使用代理。通常浏览器通过一个URL从本地网络读取PAC文件。

因为curl不支持JavaScript，所以也不支持PAC文件。如果你的浏览器和网络使用了PAC文件，最简单的办法是手动读取PAC文件，并找出需要指定给curl的代理。

### 3.9 退出状态

(6) 无法解析主机。给定的远程主机地址未被解析。无法解析给定服务器的地址。要么给定的主机名是错误的，要么DNS服务器行为异常，无法识别给定的主机，或者运行curl的系统配置错误，找不到或无法使用正确的DNS服务器。
(7) 无法连接主机。curl设法获取主机的IP地址，并尝试建立TCP连接，但失败了。这可能是因为你指定了错误的端口号，输入了错误的主机名、错误的协议，或者中间有防火墙或其他网络设备阻止流量通过。

(47) 太多重定向。在进行HTTP重定向时，libcurl达到了应用设置的最大重定向次数。libcurl没有限制重定向的最大次数，但curl默认设置了50次的上限。设置限制主要是为了避免无限的重定向循环。可以用--max-redirs修改这个限制。

### 3.10 FTP

SCP和SFTP都是建立在SSH之上的协议，SSH是一种类似于TLS的安全加密数据协议，但在某些方面有所不同。例如，SSH不使用任何类型的证书，而是使用公钥和私钥。如果使用得当，SSH和TLS都可以提供强大的安全传输。

当连接到SFTP或SCP主机时，curl会先确认主机的密钥散列已存在于已知主机的文件中，否则它将拒绝后续操作，因为它不相信现在连接的服务器就是真正的目标主机。如果known_hosts中存在正确的散列，curl就会开始执行传输。
要想强制curl跳过检查，可以使用-k或--insecure选项。在使用这两个选项时必须非常小心，因为这样有可能检测不到中间人的攻击。

### 3.13 TLS

如果不确定是否正在与正确的主机通信，那么就没有必要与服务器建立安全连接。

为了确认是否与正确的TLS服务器通信，curl会使用一组存储在本地的CA证书来验证服务器的证书签名。作为TLS握手的一部分，所有服务器都需要向客户端提供证书，并且所有使用TLS的公共服务器都会从证书颁发机构获取证书。
应用一些加密技术之后，curl知道连接的服务器是对的服务器，它持有curl所连接的目标主机的证书。如果未能验证服务器证书，则表示TLS握手失败，curl将返回错误并退出。

在极少数情况下，即使证书验证失败，你可能仍然希望与TLS服务器展开通信。如果是这样，则必须接受你的通信可能受到中间人攻击的事实。你可以通过-k或--insecure选项来降低安全级别。

可以用--cacert选项指定要在TLS握手中使用的CA捆绑包，这个捆绑包需要采用PEM格式，

TLS客户端证书是客户端以加密的方式向服务器证明自己就是真正对等方的一种方式。在命令行中指定证书和相应的密钥，然后在与服务器进行TLS握手时传递它们。


### 3.15 复制为curl命令

这些工具将为你提供命令行以及需要在请求中发送的静态cookie内容，也就是通过浏览器请求发送的cookie内容。你很可能希望重写命令行，以便动态修改服务器在之前的响应中告诉你的cookie内容，等等。


需要使用-F选项时，复制为curl命令行这一功能生成的命令行并不好用，于是curl提供了--data-binary选项，这个选项可以包含mime分隔符分隔的字符串等。

### 4.6 HTTP POST


        curl -d 'name=admin&shoesize=12' http://example.com/
在命令行上指定多个-d选项时，curl会将它们串联起来，并在它们之间插入 & 符号。上面的示例也可以变成：

用-d选项从文件读取内容时，回车符和换行符将被移除。如果想让curl从文件读取二进制内容，可以使用--data-binary：

 转换为GET
这个时候可以考虑使用一个能够带来些许便利的特性（即使它不适用于POST），也就是-G或--get选项，它将-d选项指定的数据附加到URL右边，并使用“? ”分隔，然后让curl用GET方法发送数据。

### 4.9 重定向

临时重定向的返回码是302。服务器希望客户端将GET请求发送给B，但客户端不应该缓存这个信息，下一次仍然使用原始的URI。


指定遵循重定向的选项后，curl默认最多会执行50次重定向。设置最大次数限制是为了避免陷入无限循环。如果50次对你来说还不够，可以通过--max-redirs选项修改最大重定向次数。

### 4.10 修改HTTP请求

可以在-X或--request选项后面跟上方法名，让curl使用其他方法。例如，你可以按照以下方式发送DELETE，而不是GET：
￼
        curl http://example.com/file -X DELETE


当用户点击网页上的链接时，浏览器会将用户带到下一个URL，它会在新的请求中添加referer标头，表明请求的来源。虽然referer拼写错误，但它就是这个意思！

POST是指将数据传给现有的远程资源，而PUT是指创建新的资源。

HTTP cookie是存储在客户端的键值对。在后续请求中，它们被发回服务器，用于保持请求间的状态。HTTP本身是没有状态的，客户端必须在后续请求中重新发送需要服务器知道的所有数据。

-c指示curl将cookie写入文件，-b指示curl从文件读取cookie。通常需要同时使用它们。


路复用
HTTP/2协议的主要特性之一是能够在同一物理连接上复用多个逻辑流。在使用curl命令行工具时，你无法利用这个很酷的特性，因为curl严格按照串行的方式执行网络请求，一个接一个，后一个要在前一个结束后才能开始。
希望未来的curl版本可以支持这项特性。