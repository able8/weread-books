## Effective Go 中英双语版
> Bingo

### 前言

 On the other hand, thinking about the problem from a Go perspective could produce a successful but quite different program. In other words, to write Go well, it's important to understand its properties and idioms. It's also important to know the established conventions for programming in Go, such as naming, formatting, program construction, and so on, so that programs you write will be easy for other Go programmers to understand.

This document gives tips for writing clear, idiomatic Go code. It augments the language specification, the Tour of Go, and How to Write Go Code, all of which you should read first.

在中国，对绝大多数人来说，English + Computer Skills = Freedom（英语 + 计算机技能 = 自由）
我非常的赞同。英语和计算机技能是相辅相成，学习好一门编程语言（如 Go）的同时，还能加强英语学习，何乐而不为。所以我决定将本书改版成中英双语版，方便更多的人来学习阅读。

### 引言

The Go package sources are intended to serve not only as the core library but also as examples of how to use the language. Moreover, many of the packages contain working, self-contained executable examples you can run directly from the golang.org web site

If you have a question about how to approach a problem or how something might be implemented, the documentation, code and examples in the library can provide answers, ideas and background.

### 格式化

With Go we take an unusual approach and let the machine take care of most formatting issues. 

As an example, there's no need to spend time lining up the comments on the fields of a structure. Gofmt will do that for you. Given the declaration
举例来说，你无需花时间将结构体中的字段注释对齐，gofmt 将为你代劳。 假如有以下声明：

gofmt will line up the columns:
gofmt 会将它按列对齐为：

All Go code in the standard packages has been formatted with gofmt.
标准包中所有的 Go 代码都已经用 gofmt 格式化过了。
Some formatting details remain. Very briefly:
还有一些关于格式化的细节，它们非常简短：

Line length
  Go has no line length limit. Don't worry about overflowing a punched card. If a line feels too long, wrap it and indent with an extra tab.
Parentheses
  Go needs fewer parentheses than C and Java: control structures (if, for, switch) do not have parentheses in their syntax. 

### 注释

Go provides C-style /* */ block comments and C++-style // line comments. Line comments are the norm; block comments appear mostly as package comments, but are useful within an expression or to disable large swaths of code.

Every package should have a package comment, a block comment preceding the package clause. For multi-file packages, the package comment only needs to be present in one file, and any one will do.

The package comment should introduce the package and provide information relevant to the package as a whole. It will appear first on the godoc page and should set up the detailed documentation that follows.


If the package is simple, the package comment can be brief.

// Package path implements utility routines for
// manipulating slash-separated filename paths.

// path 包实现了一些常用的工具，以便于操作用反斜杠分隔的路径.

Comments do not need extra formatting such as banners of stars. The generated output may not even be presented in a fixed-width font, so don't depend on spacing for alignment—godoc, like gofmt, takes care of that.

Inside a package, any comment immediately preceding a top-level declaration serves as a doc comment for that declaration. Every exported (capitalized) name in a program should have a doc comment.
在包中，任何顶级声明前面的注释都将作为该声明的文档注释。 在程序中，每个可导出（首字母大写）的名称都应该有文档注释。

Doc comments work best as complete sentences, which allow a wide variety of automated presentations. The first sentence should be a one-sentence summary that starts with the name being declared.
文档注释最好是完整的句子，这样它才能适应各种自动化的展示。 第一句应当以被声明的东西开头，并且是单句的摘要。

// Compile parses a regular expression and returns, if successful, a Regexp
// object that can be used to match against text.
func Compile(str string) (regexp *Regexp, err error) {

If all the doc comments in the package began, "This function...", grep wouldn't help you remember the name. But because the package starts each doc comment with the name, you'd see something like this, which recalls the word you're looking for.

Go's declaration syntax allows grouping of declarations. A single doc comment can introduce a group of related constants or variables. Since the whole declaration is presented, such a comment can often be perfunctory.


Grouping can also indicate relationships between items, such as the fact that a set of variables is protected by a mutex.

### 命名

It's therefore worth spending a little time talking about naming conventions in Go programs.


By convention, packages are given lower case, single-word names; there should be no need for underscores or mixedCaps. Err on the side of brevity, since everyone using your package will be typing that name. 

Another convention is that the package name is the base name of its source directory; the package in src/encoding/base64 is imported as "encoding/base64" but has name base64, not encoding_base64 and not encodingBase64.

For instance, the buffered reader type in the bufio package is called Reader, not BufReader, because users see it as bufio.Reader, which is a clear, concise name.

Similarly, the function to make new instances of ring.Ring—which is the definition of a constructor in Go—would normally be called NewRing, but since Ring is the only type exported by the package, and since the package is called ring, it's called just New, which clients of the package see as ring.New. Use the package structure to help you choose good names.


Another short example is once.Do; once.Do(setup) reads well and would not be improved by writing once.DoOrWaitUntilDone(setup). Long names don't automatically make things more readable. A helpful doc comment can often be more valuable than an extra long name.

Go doesn't provide automatic support for getters and setters. There's nothing wrong with providing getters and setters yourself, and it's often appropriate to do so, but it's neither idiomatic nor necessary to put Get into the getter's name. 

A setter function, if needed, will likely be called SetOwner. Both names read well in practice:

By convention, one-method interfaces are named by the method name plus an -er suffix or similar modification to construct an agent noun: Reader, Writer, Formatter, CloseNotifier etc.
按照约定，只包含一个方法的接口应当以该方法的名称加上 - er 后缀来命名，如 Reader、Writer、 Formatter、CloseNotifier 等。


Conversely, if your type implements a method with the same meaning as a method on a well-known type, give it the same name and signature; call your string-converter method String not ToString.

Finally, the convention in Go is to use MixedCaps or mixedCaps rather than underscores to write multiword names.
最后，Go 中约定使用驼峰记法 MixedCaps 或 mixedCaps。

### 分号

One consequence of the semicolon insertion rules is that you cannot put the opening brace of a control structure (if, for, switch, or select) on the next line. If you do, a semicolon will be inserted before the brace, which could cause unwanted effects. Write them like this

### 控制结构

The control structures of Go are related to those of C but differ in important ways. There is no do or while loop, only a slightly generalized for; switch is more flexible; if and switch accept an optional initialization statement like that of for; break and continue statements take an optional label to identify what to break or continue; and there are new control structures including a type switch and a multiway communications multiplexer, select. 

In Go a simple if looks like this:

Since if and switch accept an initialization statement, it's common to see one used to set up a local variable.
由于 if 和 switch 可接受初始化语句， 因此用它们来设置局部变量十分常见。
if err := file.Chmod(0664); err != nil {
    log.Print(err)
    return err
}

This is an example of a common situation where code must guard against a sequence of error conditions. The code reads well if the successful flow of control runs down the page, eliminating error cases as they arise.

Since error cases tend to end in return statements, the resulting code needs no else statements.

, err := os.Open(name)

This statement declares two variables, f and err. A few lines later, the call to f.Stat reads,
该语句声明了两个变量 f 和 err。在几行之后，又通过
d, err := f.Stat()

which looks as if it declares d and err. Notice, though, that err appears in both statements. This duplication is legal: err is declared by the first statement, but only re-assigned in the second. This means that the call to f.Stat uses the existing err variable declared above, and just gives it a new value.

This unusual property is pure pragmatism, making it easy to use a single err value, for example, in a long if-else chain. You'll see it used often.


It's worth noting here that in Go the scope of function parameters and return values is the same as the function body, even though they appear lexically outside the braces that enclose the body.
§ 值得一提的是，即便 Go 中的函数形参和返回值在词法上处于大括号之外， 但它们的作用域和该函数体仍然相同。

The Go for loop is similar to—but not the same as—C's. It unifies for and while and there is no do-while. There are three forms, only one of which has semicolons.
Go 的 for 循环类似于 C，但却不尽相同。它统一了 for 和 while，不再有 do-while 了。它有三种形式，但只有一种需要分号。

// Like a C for
for init; condition; post { }

// Like a C while
for condition { }

// Like a C for(;;)
for { }

If you're looping over an array, slice, string, or map, or reading from a channel, a range clause can manage the loop.

If you only need the first item in the range (the key or index), drop the second:
若你只需要该遍历中的第一个项（键或下标），去掉第二个就行了：
for key := range m {
    if key.expired() {
        delete(m, key)
    }
}

If you only need the second item in the range (the value), use the blank identifier, an underscore, to discard the first:
若你只需要该遍历中的第二个项（值），请使用空白标识符，即下划线来丢弃第一个值：

For strings, the range does more work for you, breaking out individual Unicode code points by parsing the UTF-8. Erroneous encodings consume one byte and produce the replacement rune U+FFFD. 

对于字符串，range 能够提供更多便利。它能通过解析 UTF-8， 将每个独立的 Unicode 码点分离出来。错误的编码将占用一个字节，并以符文 U+FFFD 来代替。 （名称 “符文” 和内建类型 rune 是 Go 对单个 Unicode 码点的成称谓。 详情见语言规范）。循环

character U+FFFD '�' starts at byte position 6

for pos, char := range "日本\x80語" { // \x80 是个非法的UTF-8编码
    fmt.Printf("字符 %!U(MISSING) 始于字节位置 %!d(MISSING)\n", char, pos)
}

将打印
字符 U+65E5 '日' 始于字节位置 0
字符 U+672C '本' 始于字节位置 3
字符 U+FFFD '�' 始于字节位置 6
字符 U+8A9E '語' 始于字节位置 7

若 switch 后面没有表达式，它将匹配 true，因此，我们可以将 if-else-if-else 链写成一个 switch，这也更符合 Go 的风格。

There is no automatic fall through, but cases can be presented in comma-separated lists.
switch 并不会自动下溯，但 case 可通过逗号分隔来列举相同的处理条件。

A switch can also be used to discover the dynamic type of an interface variable. Such a type switch uses the syntax of a type assertion with the keyword type inside the parentheses.

switch 也可用于判断接口变量的动态类型。如 类型选择 通过圆括号中的关键字 type 使用类型断言语法。若 switch 在表达式中声明了一个变量，那么该变量的每个子句中都将有该变量对应的类型。在这些 case 中重用一个名字也是符合语义的，实际上是在每个 case 里声明了一个不同类型但同名的新变量。

var t interface{}
t = functionOfSomeType()
switch t := t.(type) {
default:
    fmt.Printf("unexpected type %!T(MISSING)", t)       // %!T(MISSING) 输出 t 是什么类型
case bool:
    fmt.Printf("boolean %!t(MISSING)\n", t)             // t 是 bool 类型
case int:
    fmt.Printf("integer %!d(MISSING)\n", t)             // t 是 int 类型

### 函数

One of Go's unusual features is that functions and methods can return multiple values. This form can be used toimprove on a couple of clumsy idioms in C programs: in-band error returns such as -1 for EOF and modifying anargument passed by address.

In Go, Writecan return a count and an error: “Yes, you wrote some bytes but not all of them because you filled the device”. Thesignature of the Write method on files from package os is:

and as the documentation says, it returns the number of bytes written and a non-nil error when n != len(b). This is acommon style; see the section on error handling for more examples.

The return or result "parameters" of a Go function can be given names and used as regular variables, just like theincoming parameters. When named, they are initialized to the zero values for their types when the function begins; ifthe function executes a return statement with no arguments, the current values of the result parameters are used asthe returned values.

The names are not mandatory but they can make code shorter and clearer: they're documentation. If we name theresults of nextInt it becomes obvious which returned int is which.此名称不是强制性的，但它们能使代码更加简短清晰：它们就是文档。若我们命名了 nextInt 的结果，那么它返回的 int就值如其意了。

Go's defer statement schedules a function call (the deferred function) to be run immediately before the functionexecuting the defer returns. It's an unusual but effective way to deal with situations such as resources that must bereleased regardless of which path a function takes to return. 

Deferring a call to a function such as Close has two advantages. First, it guarantees that you will never forget to close the file, a mistake that's easy to make if you later edit the function to add a new return path. Second, it means that the close sits near the open, which is much clearer than placing it at the end of the function.

The arguments to the deferred function (which include the receiver if the function is a method) are evaluated when the defer executes, not when the call executes. 

被推迟函数的实参（如果该函数为方法则还包括接收者）在推迟执行时就会求值， 而不是在调用执行时才求值。这样不仅无需担心变量值在函数执行时被改变， 同时还意味着单个已推迟的调用可推迟多个函数的执行。

Deferred functions are executed in LIFO order, so this code will cause 4 3 2 1 0 to be printed when the function returns.

We can do better by exploiting the fact that arguments to deferred functions are evaluated when the defer executes. The tracing routine can set up the argument to the untracing routine. This example:
我们可以充分利用这个特点，即被推迟函数的实参在 defer 执行时才会被求值。 跟踪例程可针对反跟踪例程设置实参。

 In the section on panic and recover we'll see another example of its possibilities.


### 数据

new 分配
Go has two allocation primitives, the built-in functions new and make. They do different things and apply to different types, which can be confusing, but the rules are simple. Let's talk about new first. It's a built-in function that allocates memory, but unlike its namesakes in some other languages it does not initialize the memory, it only zeros it. That is, new(T) allocates zeroed storage for a new item of type T and returns its address, a value of type *T. In Go terminology, it returns a pointer to a newly allocated zero value of type T.


For example, the documentation for bytes.Buffer states that "the zero value for Buffer is an empty buffer ready to use." Similarly, sync.Mutex does not have an explicit constructor or Init method. Instead, the zero value for a sync.Mutex is defined to be an unlocked mutex.


既然 new 返回的内存已置零，那么当你设计数据结构时， 每种类型的零值就不必进一步初始化了，这意味着该数据结构的使用者只需用 new 创建一个新的对象就能正常工作。例如，bytes.Buffer 的文档中提到 “零值的 Buffer 就是已准备就绪的缓冲区。" 同样，sync.Mutex 并没有显式的构造函数或 Init 方法， 而是零值的 sync.Mutex 就已经被定义为已解锁的互斥锁了。

SyncedBuffer 类型的值也是在声明时就分配好内存就绪了。后续代码中， p 和 v 无需进一步处理即可正确工作。
p := new(SyncedBuffer)  // type *SyncedBuffer
var v SyncedBuffer      // type  SyncedBuffer

Sometimes the zero value isn't good enough and an initializing constructor is necessary, as in this example derived from package os.
有时零值还不够好，这时就需要一个初始化构造函数，如来自 os 包中的这段代码所示。

There's a lot of boiler plate in there. We can simplify it using a composite literal, which is an expression that creates a new instance each time it is evaluated.
这里显得代码过于冗长。我们可通过复合字面来简化它， 该表达式在每次求值时都会创建新的实例。
func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    f := File{fd, name, nil, 0}
    return &f
}

Note that, unlike in C, it's perfectly OK to return the address of a local variable; the storage associated with the variable survives after the function returns. In fact, taking the address of a composite literal allocates a fresh instance each time it is evaluated, so we can combine these last two lines.
请注意，返回一个局部变量的地址完全没有问题，这点与 C 不同。该局部变量对应的数据 在函数返回后依然有效。实际上，每当获取一个复合字面的地址时，都将为一个新的实例分配内存， 因此我们可以将上面的最后两行代码合并：
    return &File{fd, name, nil, 0}

As a limiting case, if a composite literal contains no fields at all, it creates a zero value for the type. The expressions new(File) and &File{} are equivalent.
少数情况下，若复合字面不包括任何字段，它将创建该类型的零值。表达式 new(File) 和 &File{} 是等价的。

The built-in function make(T, args) serves a purpose different from new(T). It creates slices, maps, and channels only, and it returns an initialized (not zeroed) value of type T (not *T). The reason for the distinction is that these three types represent, under the covers, references to data structures that must be initialized before use. A slice, for example, is a three-item descriptor containing a pointer to the data (inside an array), the length, and the capacity, and until those items are initialized, the slice is nil. 

For slices, maps, and channels, make initializes the internal data structure and prepares the value for use. For instance,

Remember that make applies only to maps, slices and channels and does not return a pointer. To obtain an explicit pointer allocate with new or take the address of a variable explicitly.
请记住，make 只适用于映射、切片和信道且不返回指针。若要获得明确的指针， 请使用 new 分配内存。

There are major differences between the ways arrays work in Go and C. In Go,
•  Arrays are values. Assigning one array to another copies all the elements.
•  In particular, if you pass an array to a function, it will receive a copy of the array, not a pointer to it.
•  The size of an array is part of its type. The types [10]int and [20]int are distinct.

The value property can be useful but also expensive; if you want C-like behavior and efficiency, you can pass a pointer to the array.
数组为值的属性很有用，但代价高昂；若你想要 C 那样的行为和效率，你可以传递一个指向该数组的指针。

array := [...]float64{7.0, 8.5, 9.1}
x := Sum(&array)  // Note the explicit address-of operator

Slices wrap arrays to give a more general, powerful, and convenient interface to sequences of data. 

切片通过对数组进行封装，为数据序列提供了更通用、强大而方便的接口。

Slices hold references to an underlying array, and if you assign one slice to another, both refer to the same array. If a function takes a slice argument, changes it makes to the elements of the slice will be visible to the caller, analogous to passing a pointer to the underlying array.

func (file *File) Read(buf []byte) (n int, err error)

The method returns the number of bytes read and an error value, if any. To read into the first 32 bytes of a larger buffer buf, slice (here used as a verb) the buffer.
该方法返回读取的字节数和一个错误值（若有的话）。若要从更大的缓冲区 b 中读取前 32 个字节，只需对其进行切片即可。
    n, err := f.Read(buf[0:32])

Such slicing is common and efficient. In fact, leaving efficiency aside for the moment, the following snippet would also read the first 32 bytes of the buffer.
这种切片的方法常用且高效。若不谈效率，以下片段同样能读取该缓冲区的前 32 个字节。

The capacity of a slice, accessible by the built-in function cap, reports the maximum length the slice may assume. Here is a function to append data to a slice. If the data exceeds the capacity, the slice is reallocated. The resulting slice is returned. The function uses the fact that len and cap are legal when applied to the nil slice, and return 0.

func Append(slice, data[]byte) []byte {
    l := len(slice)
    if l + len(data) > cap(slice) {  // reallocate
        // Allocate double what's needed, for future growth.
        newSlice := make([]byte, (l+len(data))*2)
        // The copy function is predeclared and works for any slice type.
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0:l+len(data)]
    for i, c := range data {
        slice[l+i] = c
    }
    return slice
}



We must return the slice afterwards because, although Append can modify the elements of slice, the slice itself (the run-time data structure holding the pointer, length, and capacity) is passed by value.
最终我们必须返回切片，因为尽管 Append 可修改 slice 的元素，但切片自身（其运行时数据结构包含指针、长度和容量）是通过值传递的。

Because slices are variable-length, it is possible to have each inner slice be a different length. That can be a common situation, as in our LinesOfText example: each line has an independent length.
由于切片长度是可变的，因此其内部可能拥有多个不同长度的切片。在我们的 LinesOfText 例子中，这是种常见的情况：每行都有其自己的长度。

If the slices might grow or shrink, they should be allocated independently to avoid overwriting the next line; if not, it can be more efficient to construct the object with a single allocation. For reference, here are sketches of the two methods. First, a line at a time:

Maps are a convenient and powerful built-in data structure that associate values of one type (the key) with values of another type (the element or value) The key can be of any type for which the equality operator is defined, such as integers, floating point

Slices cannot be used as map keys, because equality is not defined on them. Like slices, maps hold references to an underlying data structure. If you pass a map to a function that changes the contents of the map, the changes will be visible in the caller.


切片一样，映射也是引用类型。 若将映射传入函数中，并更改了该映射的内容，则此修改对调用者同样可见。

Maps can be constructed using the usual composite literal syntax with colon-separated key-value pairs, so it's easy to build them during initialization.
映射可使用一般的复合字面语法进行构建，其键 - 值对使用逗号分隔，因此可在初始化时很容易地构建它们。

Assigning and fetching map values looks syntactically just like doing the same for arrays and slices except that the index doesn't need to be an integer.
赋值和获取映射值的语法类似于数组，不同的是映射的索引不必为整数。
offset := timeZone["EST"]

For instance, if the map contains integers, looking up a non-existent key will return 0. A set can be implemented as a map with value type bool. Set the map entry to true to put the value in the set, and then test it by simple indexing.


attended := map[string]bool{
    "Ann": true,
    "Joe": true,
    ...
}

if attended[person] { // will be false if person is not in the map
    fmt.Println(person, "was at the meeting")
}



Sometimes you need to distinguish a missing entry from a zero value. Is there an entry for "UTC" or is that the empty string because it's not in the map at all? You can discriminate with a form of multiple assignment.


seconds, ok = timeZone[tz]

For obvious reasons this is called the “comma ok” idiom. In this example, if tz is present, seconds will be set appropriately and ok will be true; if not, seconds will be set to zero and ok will be false. Here's a function that puts it together with a nice error report:


if seconds, ok := timeZone[tz]; ok {
        return seconds
    }

To test for presence in the map without worrying about the actual value, you can use the blank identifier (_) in place of the usual variable for the value.
若仅需判断映射中是否存在某项而不关心实际的值，可使用 空白标识符 （_）来代替该值的一般变量。
_, present := timeZone[tz]

To delete a map entry, use the delete built-in function, whose arguments are the map and the key to be deleted. It's safe to do this even if the key is already absent from the map.
要删除映射中的某项，可使用内建函数 delete，它以映射及要被删除的键为实参。 即便对应的键不在该映射中，此操作也是安全的。
delete(timeZone, "PDT")  // Now on Standard Time

The string functions (Sprintf etc.) return a string rather than filling in a provided buffer.


字符串函数（Sprintf 等）会返回一个字符串，而非填充给定的缓冲区。

The Println versions also insert a blank between arguments and append a newline to the output while the Print versions add blanks only if the operand on neither side is a string. In this example each line produces the same output.

以下示例中各行产生的输出都是一样的。
fmt.Printf("Hello %!d(MISSING)\n", 23)
fmt.Fprint(os.Stdout, "Hello ", 23, "\n")
fmt.Println("Hello", 23)
fmt.Println(fmt.Sprint("Hello ", 23))

The formatted print functions fmt.Fprint and friends take as a first argument any object that implements the io.Writer interface; the variables os.Stdout and os.Stderr are familiar instances.
fmt.Fprint 一类的格式化打印函数可接受任何实现了 io.Writer 接口的对象作为第一个实参；变量 os.Stdout 与 os.Stderr 都是人们熟知的例子。

像 %!d(MISSING) 这样的数值格式并不接受表示符号或大小的标记， 打印例程会根据实参的类型来决定这些属性。
var x uint64 = 1<<64 - 1
fmt.Printf("%!d(MISSING) %!x(MISSING); %!d(MISSING) %!x(MISSING)\n", x, x, int64(x), int64(x))

If you just want the default conversion, such as decimal for integers, you can use the catchall format %!v(MISSING) (for “value”); 

Moreover, that format can print any value, even arrays, slices, structs, and maps. Here is a print statement for the time zone map defined in the previous section.

For maps the keys may be output in any order, of course. When printing a struct, the modified format %!v(MISSING) annotates the fields of the structure with their names, and for any value the alternate format %!v(MISSING) prints the value in full Go syntax.
当然，映射中的键可能按任意顺序输出。当打印结构体时，改进的格式 %!v(MISSING) 会为结构体的每个字段添上字段名，而另一种格式 %!v(MISSING) 将完全按照 Go 的语法打印值。

That quoted string format is also available through %!q(MISSING) when applied to a value of type string or []byte. The alternate format %!q(MISSING) will use backquotes instead if possible. (The %!q(MISSING) format also applies to integers and runes, producing a single-quoted rune constant.) Also, %!x(MISSING) works on strings, byte arrays and byte slices as well as on integers, generating a long hexadecimal string, and with a space in the format (%!x(MISSING)) it puts spaces between the bytes.

Another handy format is %!T(MISSING), which prints the type of a value.
另一种实用的格式是 %!T(MISSING)，它会打印某个值的类型.
fmt.Printf("%!T(MISSING)\n", timeZone) prints
会打印
map[string] int

If you want to control the default format for a custom type, all that's required is to define a method with the signature String() string on the type. For our simple type T, that might look like this.
若你想控制自定义类型的默认格式，只需为该类型定义一个具有 String() string 签名的方法。对于我们简单的类型 T，可进行如下操作。
func (t *T) String() string {
    return fmt.Sprintf("%!d(MISSING)/%!g(MISSING)/%!q(MISSING)", t.a, t.b, t.c)
}
fmt.Printf("%!v(MISSING)\n", t)

don't construct a String method by calling Sprintf in a way that will recur into your String method indefinitely. This can happen if the Sprintf call attempts to print the receiver directly as a string, which in turn will invoke the method again. It's a common and easy mistake to make, as this example shows.

请勿通过调用 Sprintf 来构造 String 方法，因为它会无限递归你的的 String 方法。

It's also easy to fix: convert the argument to the basic string type, which does not have the method.

解决这个问题也很简单：将该实参转换为基本的字符串类型，它没有这个方法。
type MyString string
func (m MyString) String() string {
    return fmt.Sprintf("MyString=%!s(MISSING)", string(m)) // OK: note conversion.
}

在 Printf 函数中，v 看起来更像是 []interface{} 类型的变量，但如果将它传递到另一个变参函数中，它就像是常规实参列表了。 以下是我们之前用过的 log.Println 的实现。它直接将其实参传递给 fmt.Sprintln 进行实际的格式化。
// Println prints to the standard logger in the manner of fmt.Println.
func Println(v ...interface{}) {
    std.Output(2, fmt.Sprintln(v...))  // Output takes parameters (int, string)
}

We write ... after v in the nested call to Sprintln to tell the compiler to treat v as a list of arguments; otherwise it would just pass v as a single slice argument.
在该 Sprintln 嵌套调用中，我们将 ... 写在 v 之后来告诉编译器将 v 视作一个实参列表，否则它会将 v 当做单一的切片实参来传递。

See the godoc documentation for package fmt for the details.

By the way, a ... parameter can be of a specific type, for instance ...int for a min function that chooses the least of a list of integers:


The signature of append is different from our custom Append function above. 

func append(slice []T, elements ...T) []T

where T is a placeholder for any given type. You can't actually write a function in Go where the type T is determined by the caller. That's why append is built in: it needs support from the compiler.
其中的 T 为任意给定类型的占位符。实际上，你无法在 Go 中编写一个类型 T 由调用者决定的函数。这也就是为何 append 为内建函数的原因：它需要编译器的支持。 

What append does is append the elements to the end of the slice and return the result. The result needs to be returned because, as with our hand-written Append, the underlying array may change. 

But what if we wanted to do what our Append does and append a slice to a slice? Easy: use ... at the call site, just as we did in the call to Output above. This snippet produces identical output to the one above.
但如果我们要像 Append 那样将一个切片追加到另一个切片中呢？ 很简单：在调用的地方使用 ...，就像我们在上面调用 Output 那样。以下代码片段的输出与上一个相同。
x := []int{1,2,3}
y := []int{4,5,6}
x = append(x, y...)
fmt.Println(x)

Without that ..., it wouldn't compile because the types would be wrong; y is not of type int.
如果没有 ...，它就会由于类型错误而无法编译，因为 y 不是 int 类型的。

### 初始化

Constants in Go are just that—constant. They are created at compile time, even when defined as locals in functions, and can only be numbers, characters (runes), strings or booleans. Because of the compile-time restriction, the expressions that define them must be constant expressions, evaluatable by the compiler. For instance, 1<<3 is a constant expression, while math.Sin(math.Pi/4) is not because the function call to math.Sin needs to happen at run time.


Go 中的常量就是不变量。它们在编译时创建，即便它们可能是函数中定义的局部变量。 常量只能是数字、字符（符文）、字符串或布尔值。由于编译时的限制， 定义它们的表达式必须也是可被编译器求值的常量表达式。例如 1<<3 就是一个常量表达式，而 math.Sin(math.Pi/4) 则不是，因为对 math.Sin 的函数调用在运行时才会发生。

The ability to attach a method such as String to any user-defined type makes it possible for arbitrary values to format themselves automatically for printing. Although you'll see it most often applied to structs, this technique is also useful for scalar types such as floating-point types like ByteSize.

The use here of Sprintf to implement ByteSize's String method is safe (avoids recurring indefinitely) not because of a conversion but because it calls Sprintf with %!f(MISSING), which is not a string format: Sprintf will only call the String method when it wants a string, and %!f(MISSING) wants a floating-point value.

Variables can be initialized just like constants but the initializer can be a general expression computed at run time.


Besides initializations that cannot be expressed as declarations, a common use of init functions is to verify or repair correctness of the program state before real execution begins.
除了那些不能被表示成声明的初始化外，init 函数还常被用在程序真正开始执行前，检验或校正程序的状态。

### 方法

, methods can be defined for any named type (except a pointer or an interface); the receiver does not have to be a struct.

We can define it as a method on slices instead. To do this, we first declare a named type to which we can bind the method, and then make the receiver for the method a value of that type.
在之前讨论切片时，我们编写了一个 Append 函数。 我们也可将其定义为切片的方法。为此，我们首先要声明一个已命名的类型来绑定该方法， 然后使该方法的接收者成为该类型的值。

This still requires the method to return the updated slice. We can eliminate that clumsiness by redefining the method to take a pointer to a ByteSlice as its receiver, so the method can overwrite the caller's slice.
我们仍然需要该方法返回更新后的切片。为了消除这种不便，我们可通过重新定义该方法， 将一个指向 ByteSlice 的指针作为该方法的接收者， 这样该方法就能重写调用者提供的切片了。

func (p *ByteSlice) Append(data []byte) {
    slice := *p
    // Body as above, without the return.
    *p = slice
}

then the type *ByteSlice satisfies the standard interface io.Writer, which is handy. For instance, we can print into one.


We pass the address of a ByteSlice because only *ByteSlice satisfies io.Writer. The rule about pointers vs. values for receivers is that value methods can be invoked on pointers and values, but pointer methods can only be invoked on pointers.
我们将 ByteSlice 的地址传入，因为只有 *ByteSlice 才满足 io.Writer。以指针或值为接收者的区别在于：值方法可通过指针和值调用， 而指针方法只能通过指针来调用。

This rule arises because pointer methods can modify the receiver; invoking them on a value would cause the method to receive a copy of the value, so any modifications would be discarded. The language therefore disallows this mistake. 

There is a handy exception, though. When the value is addressable, the language takes care of the common case of invoking a pointer method on a value by inserting the address operator automatically. In our example, the variable b is addressable, so we can call its Write method with just b.Write. The compiler will rewrite that to (&b).Write for us.

该值是可寻址的， 那么该语言就会自动插入取址操作符来对付一般的通过值调用的指针方法。在我们的例子中，变量 b 是可寻址的，因此我们只需通过 b.Write 来调用它的 Write 方法，编译器会将它重写为 (&b).Write。

y the way, the idea of using Write on a slice of bytes is central to the implementation of bytes.Buffer.
顺便一提，在字节切片上使用 Write 的想法已被 bytes.Buffer 所实现。

### 接口和其他类型

Interfaces in Go provide a way to specify the behavior of an object: if something can do this, then it can be used here. We've seen a couple of simple examples already; custom printers can be implemented by a String method while Fprintf can generate output to anything with a Write method.

在 Go 代码中， 仅包含一两种方法的接口很常见，且其名称通常来自于实现它的方法， 如 io.Writer 就是实现了 Write 的一类对象。


The String method of Sequence is recreating the work that Sprint already does for slices. We can share the effort if we convert the Sequence to a plain []int before calling Sprint.

Because the two types (Sequence and []int) are the same if we ignore the type name, it's legal to convert between them. The conversion doesn't create a new value, it just temporarily acts as though the existing value has a new type. (There are other legal conversions, such as from integer to floating point, that do create a new value.

It's an idiom in Go programs to convert the type of an expression to access a different set of methods. As an example, we could use the existing type sort.IntSlice to reduce the entire example to this:
在 Go 程序中，为访问不同的方法集而进行类型转换的情况非常常见。 例如，我们可使用现有的 sort.IntSlice 类型来简化整个示例：

type Sequence []int

// Method for printing - sorts the elements before printing
func (s Sequence) String() string {
    sort.IntSlice(s).Sort()
    return fmt.Sprint([]int(s))
}

Now, instead of having Sequence implement multiple interfaces (sorting and printing), we're using the ability of a data item to be converted to multiple types (Sequence, sort.IntSlice and []int), each of which does some part of the job.

That's more unusual in practice but can be effective.

现在，不必让 Sequence 实现多个接口（排序和打印）， 我们可通过将数据条目转换为多种类型（Sequence、sort.IntSlice 和 []int）来使用相应的功能，每次转换都完成一部分工作。 这在实践中虽然有些不同寻常，但往往却很有效。

Type switches are a form of conversion: they take an interface and, for each case in the switch, in a sense convert it to the type of that case. Here's a simplified version of how the code under fmt.Printf turns a value into a string using a type switch. If it's already a string, we want the actual string value held by the interface, while if it has a String method we want the result of calling the method.

类型选择 是类型转换的一种形式：它接受一个接口，在选择 （switch）中根据其判断选择对应的情况（case）， 并在某种意义上将其转换为该种类型。以下代码为 fmt.Printf 通过类型选择将值转换为字符串的简化版。若它已经为字符串，我们需要该接口中实际的字符串值； 若它有 String 方法，我们则需要调用该方法所得的结果。

var value interface{} // Value provided by caller.
switch str := value.(type) {
case string:
    return str
case Stringer:
    return str.String()
}

The first case finds a concrete value; the second converts the interface into another interface. It's perfectly fine to mix types this way.


A type assertion takes an interface value and extracts from it a value of the specified explicit type.

str := value.(string)

But if it turns out that the value does not contain a string, the program will crash with a run-time error. To guard against that, use the "comma, ok" idiom to test, safely, whether the value is a string:
但若它所转换的值中不包含字符串，该程序就会以运行时错误崩溃。为避免这种情况， 需使用 “逗号, ok” 惯用测试它能安全地判断该值是否为字符串：
str, ok := value.(string)
if ok {
    fmt.Printf("string value is: %!q(MISSING)\n", str)
} else {
    fmt.Printf("value is not a string\n")
}

If the type assertion fails, str will still exist and be of type string, but it will have the zero value, an empty string.
若类型断言失败，str 将继续存在且为字符串类型，但它将拥有零值，即空字符串。

若某种现有的类型仅实现了一个接口，且除此之外并无可导出的方法，则该类型本身就无需导出。 仅导出该接口能让我们更专注于其行为而非实现，其它属性不同的实现则能镜像该原始类型的行为。 

在这种情况下，构造函数应当返回一个接口值而非实现的类型。例如在 hash 库中，crc32.NewIEEE 和 adler32.New 都返回接口类型 hash.Hash32。要在 Go 程序中用 Adler-32 算法替代 CRC-32， 只需修改构造函数调用即可，其余代码则不受算法改变的影响。

crypto/cipher 包中的 Block 接口指定了块密码算法的行为， 它为单独的数据块提供加密。接着，和 bufio 包类似，任何实现了该接口的密码包都能被用于构造以 Stream 为接口表示的流密码，而无需知道块加密的细节。

Since almost anything can have methods attached, almost anything can satisfy an interface. One illustrative example is in the http package, which defines the Handler interface. Any object that implements Handler can serve HTTP requests.
由于几乎任何类型都能添加方法，因此几乎任何类型都能满足一个接口。一个很直观的例子就是 http 包中定义的 Handler 接口。任何实现了 Handler 的对象都能够处理 HTTP 请求。
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}



ResponseWriter is itself an interface that provides access to the methods needed to return the response to the client. Those methods include the standard Write method, so an http.ResponseWriter can be used wherever an io.Writer can be used. Request is a struct containing a parsed representation of the request from the client.

 Here's a trivial but complete implementation of a handler to count the number of times the page is visited.

这里有个短小却完整的处理程序实现， 它用于记录某个页面被访问的次数。
// Simple counter server.
type Counter struct {
    n int
}

func (ctr *Counter) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    ctr.n++
    fmt.Fprintf(w, "counter = %!d(MISSING)\n", ctr.n)
}

For reference, here's how to attach such a server to a node on the URL tree.
（紧跟我们的主题，注意 Fprintf 如何能输出到 http.ResponseWriter。） 作为参考，这里演示了如何将这样一个服务器添加到 URL 树的一个节点上。
import "net/http"
...
ctr := new(Counter)
http.Handle("/counter", ctr)

But why make Counter a struct? An integer is all that's needed. (The receiver needs to be a pointer so the increment is visible to the caller.)
但为什么 Counter 要是结构体呢？一个整数就够了。 An integer is all that's needed. （接收者必须为指针，增量操作对于调用者才可见。）

What if your program has some internal state that needs to be notified that a page has been visited? Tie a channel to the web page.
当页面被访问时，怎样通知你的程序去更新一些内部状态呢？为 Web 页面绑定个信道吧。

// A channel that sends a notification on each visit.
// (Probably want the channel to be buffered.)
type Chan chan *http.Request

func (ch Chan) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    ch <- req
    fmt.Fprint(w, "notification sent")
}



Since we can define a method for any type except pointers and interfaces, we can write a method for a function. The http package contains this code:

 既然我们可以为除指针和接口以外的任何类型定义方法，同样也能为一个函数写一个方法。 http 包包含以下代码：

// HandlerFunc 类型是一个适配器，它允许将普通函数用做 HTTP 处理程序。
// 若 f 是个具有适当签名的函数，HandlerFunc(f) 就是个调用 f 的处理程序对象。
type HandlerFunc func(ResponseWriter, *Request)

// ServeHTTP calls f(c, req).
func (f HandlerFunc) ServeHTTP(w ResponseWriter, req *Request) {
    f(w, req)
}



HandlerFunc is a type with a method, ServeHTTP, so values of that type can serve HTTP requests. Look at the implementation of the method: the receiver is a function, f, and the method calls f. That may seem odd but it's not that different from, say, the receiver being a channel and the method sending on the channel.

To make ArgServer into an HTTP server, we first modify it to have the right signature.
为了将 ArgServer 实现成 HTTP 服务器，首先我们得让它拥有合适的签名。
// Argument server.
func ArgServer(w http.ResponseWriter, req *http.Request) {
    fmt.Fprintln(w, os.Args)
}

ArgServer now has same signature as HandlerFunc, so it can be converted to that type to access its methods, just as we converted Sequence to IntSlice to access IntSlice.Sort. The code to set it up is concise:
ArgServer 和 HandlerFunc 现在拥有了相同的签名， 因此我们可将其转换为这种类型以访问它的方法，就像我们将 Sequence 转换为 IntSlice 以访问 IntSlice.Sort 那样。 建立代码非常简单：
http.Handle("/args", http.HandlerFunc(ArgServer))

In this section we have made an HTTP server from a struct, an integer, a channel, and a function, all because interfaces are just sets of methods, which can be defined for (almost) any type.
在本节中，我们通过一个结构体，一个整数，一个信道和一个函数，建立了一个 HTTP 服务器， 这一切都是因为接口只是方法的集和，而几乎任何类型都能定义方法。

### 空白标识符

It's a bit like writing to the Unix /dev/null file: it represents a write-only value to be used as a place-holder where a variable is needed but the actual value is irrelevant.

For instance, when calling a function that returns a value and an error, but only the error is important, use the blank identifier to discard the irrelevant value.


Occasionally you'll see code that discards the error value in order to ignore the error; this is terrible practice. Always check error returns; they're provided for a reason.
你偶尔会看见为忽略错误而丢弃错误值的代码，这是种糟糕的实践。请务必检查错误返回， 它们会提供错误的理由。
// Bad! This code will crash if path does not exist.
fi, _ := os.Stat(path)

It is an error to import a package or to declare a variable without using it. Unused imports bloat the program and slow compilation, while a variable that is initialized but not used is at least a wasted computation and perhaps indicative of a larger bug.

The blank identifier provides a workaround.

To silence complaints about the unused imports, use a blank identifier to refer to a symbol from the imported package. Similarly, assigning the unused variable fd to the blank identifier will silence the unused variable error. This version of the program does compile.


var _ = fmt.Printf // For debugging; delete when done. // 用于调试，结束时删除。

By convention, the global declarations to silence import errors should come right after the imports and be commented, both to make them easy to find and as a reminder to clean things up later.
按照惯例，我们应在导入并加以注释后，再使全局声明导入错误静默，这样可以让它们更易找到， 并作为以后清理它的提醒。

An unused import like fmt or io in the previous example should eventually be used or removed: blank assignments identify code as a work in progress.

To import the package only for its side effects, rename the package to the blank identifier:


只为了其副作用来哦导入该包， 只需将包重命名为空白标识符：
import _ "net/http/pprof"

This form of import makes clear that the package is being imported for its side effects,

Instead, a type implements the interface just by implementing the interface's methods. In practice, most interface conversions are static and therefore checked at compile time. For example, passing an *os.File to a function expecting an io.Reader will not compile unless *os.File implements the io.Reader interface.

If it's necessary only to ask whether a type implements an interface, without actually using the interface itself, perhaps as part of an error check, use the blank identifier to ignore the type-asserted value:

### 内嵌

Go does not provide the typical, type-driven notion of subclassing, but it does have the ability to “borrow” pieces of an implementation by embedding types within a struct or interface.
Go 并不提供典型的，类型驱动的子类化概念，但通过将类型 <内嵌到结构体或接口中， 它就能 “借鉴” 部分实现。

For instance, there is io.ReadWriter, an interface containing both Read and Write. We could specify io.ReadWriter by listing the two methods explicitly, but it's easier and more evocative to embed the two interfaces to form the new one, like this:

// ReadWriter is the interface that combines the Reader and Writer interfaces.
type ReadWriter interface {
    Reader
    Writer
}

 it is a union of the embedded interfaces (which must be disjoint sets of methods). Only interfaces can be embedded within interfaces.

而通过直接内嵌结构体，我们就能避免如此繁琐。 内嵌类型的方法可以直接引用，这意味着 bufio.ReadWriter 不仅包括 bufio.Reader 和 bufio.Writer 的方法，它还同时满足下列三个接口： io.Reader、io.Writer 以及 io.ReadWriter。

The Job type now has the Log, Logf and other methods of *log.Logger. We could have given the Logger a field name, of course, but it's not necessary to do so. And now, once initialized, we can log to the Job:
Job 类型现在有了 Log、Logf 和 *log.Logger 的其它方法。我们当然可以为 Logger 提供一个字段名，但完全不必这么做。现在，一旦初始化后，我们就能记录 Job 了：
job.Log("starting now...")

f we need to refer to an embedded field directly, the type name of the field, ignoring the package qualifier, serves as a field name, as it did in the Read method of our ReaderWriter struct. Here, if we needed to access the *log.Logger of a Job variable job, we would write job.Logger, which would be useful if we wanted to refine the methods of Logger.


若我们需要直接引用内嵌字段，可以忽略包限定名，直接将该字段的类型名作为字段名， 就像我们在 ReaderWriter 结构体的 Read 方法中做的那样。 若我们需要访问 Job 类型的变量 job 的 *log.Logger， 可以直接写作 job.Logger。若我们想精炼 Logger 的方法时， 这会非常有用。

Embedding types introduces the problem of name conflicts but the rules to resolve them are simple. First, a field or method X hides any other item X in a more deeply nested part of the type. If log.Logger contained a field or method called Command, the Command field of Job would dominate it.

Second, if the same name appears at the same nesting level, it is usually an error; 

### 并发

Concurrent programming is a large topic and there is space only for some Go-specific highlights here.
并发编程是个很大的论题。但限于篇幅，这里仅讨论一些 Go 特有的东西。

Go encourages a different approach in which shared values are passed around on channels

Only one goroutine has access to the value at any given time. Data races cannot occur, by design. 

在任意给定的时间点，只有一个 Go 程能够访问该值。数据竞争从设计上就被杜绝了。 

Do not communicate by sharing memory; instead, share memory by communicating.
不要通过共享内存来通信，而应通过通信来共享内存。

But as a high-level approach, using channels to control access makes it easier to write clear, correct programs.

A goroutine has a simple model: it is a function executing concurrently with other goroutines in the same address space. It is lightweight, costing little more than the allocation of stack space. And the stacks start small, so they are cheap, and grow by allocating (and freeing) heap storage as required.


Go 程具有简单的模型：它是与其它 Go 程并发运行在同一地址空间的函数。它是轻量级的， 所有小号几乎就只有栈空间的分配。而且栈最开始是非常小的，所以它们很廉价， 仅在需要时才会随着堆空间的分配（和释放）而变化。

Goroutines are multiplexed onto multiple OS threads so if one should block, such as while waiting for I/O, others continue to run. Their design hides many of the complexities of thread creation and management.
Go 程在多线程操作系统上可实现多路复用，因此若一个线程阻塞，比如说等待 I/O， 那么其它的线程就会运行。Go 程的设计隐藏了线程创建和管理的诸多复杂性。

Prefix a function or method call with the go keyword to run the call in a new goroutine. When the call completes, the goroutine exits, silently. (The effect is similar to the Unix shell's & notation for running a command in the background.)
在函数或方法前添加 go 关键字能够在新的 Go 程中调用它。当调用完成后， 该 Go 程也会安静地退出。（效果有点像 Unix Shell 中的 & 符号，它能让命令在后台运行。）

go func() {
        time.Sleep(delay)
        fmt.Println(message)
    }()  // Note the parentheses - must call the function.

Like maps, channels are allocated with make, and the resulting value acts as a reference to an underlying data structure. If an optional integer parameter is provided, it sets the buffer size for the channel. The default is zero, for an unbuffered or synchronous channel.

cj := make(chan int, 0)         // unbuffered channel of integers
cs := make(chan *os.File, 100)  // buffered channel of pointers to Files

<-c   // Wait for sort to finish; discard sent value.

Receivers always block until there is data to receive. If the channel is unbuffered, the sender blocks until the receiver has received the value. If the channel has a buffer, the sender blocks only until the value has been copied to the buffer; if the buffer is full, this means waiting until some receiver has retrieved a value.


信道缓冲区的容量决定了同时调用 process 的数量上限，因此我们在初始化时首先要填充至它的容量上限。

The bug is that in a Go for loop, the loop variable is reused for each iteration, so the req variable is shared across all goroutines. That's not what we want. We need to make sure that req is unique for each goroutine. Here's one way to do that, passing the value of req as an argument to the closure in the goroutine:

    for req := range queue {
        sem <- 1
        go func(req *Request) {
            process(req)
            <-sem
        }(req)
    }

Compare this version with the previous to see the difference in how the closure is declared and run. Another solution is just to create a new variable with the same name, as in this example:

     req := req // Create new instance of req for the goroutine.
        sem <- 1
        go func() {
            process(req)
            <-sem
        }()

It may seem odd to write
它的写法看起来有点奇怪
req := req

but it's a legal and idiomatic in Go to do this. You get a fresh version of the variable with the same name, deliberately shadowing the loop variable locally but unique to each goroutine.

type Request struct {
    args        []int
    f           func([]int) int
    resultChan  chan int
}

The client provides a function and its arguments, as well as a channel inside the request object on which to receive the answer.

There's clearly a lot more to do to make it realistic, but this code is a framework for a rate-limited, parallel, non-blocking RPC system, and there's not a mutex in sight.
要使其实际可用还有很多工作要做，这些代码仅能实现一个速率有限、并行、非阻塞 RPC 系统的 框架，而且它并不包含互斥锁。

If the calculation can be broken into separate pieces that can execute independently, it can be parallelized, with a channel to signal when each piece completes.

We launch the pieces independently in a loop, one per CPU. They can complete in any order but it doesn't matter; we just count the completion signals by draining the channel after launching all the goroutines.

// Drain the channel.
    for i := 0; i < NCPU; i++ {
        <-c    // wait for one task to complete
    }
    // All done.

The current implementation of the Go runtime will not parallelize this code by default. It dedicates only a single core to user-level processing. An arbitrary number of goroutines can be blocked in system calls, but by default only one can be executing user-level code at any time.

There are two related ways to do this. Either run your job with environment variable GOMAXPROCS set to the number of cores to use or import the runtime package and call runtime.GOMAXPROCS(NCPU). A helpful value might be runtime.NumCPU(), which reports the number of logical CPUs on the local machine. Again, this requirement is expected to be retired as the scheduling and run-time improve.

To avoid allocating and freeing buffers, it keeps a free list, and uses a buffered channel to represent it. If the channel is empty, a new buffer gets allocated. Once the message buffer is ready, it's sent to the server on serverChan.


（select 语句中的 default 子句在没有条件符合时执行，这也就意味着 selects 永远不会被阻塞。）

### 错误

It is good style to use this feature to provide detailed error information. For example, as we'll see, os.Open doesn't just return a nil pointer on failure, it also returns an error value that describes what went wrong.
By convention, errors have type error, a simple built-in interface.


type error interface {
    Error() string
}

As mentioned, alongside the usual *os.File return value, os.Open also returns an error value. If the file is opened successfully, the error will be nil, but when there is a problem, it will hold an os.PathError:


operation and
// file path that caused it.
type PathError struct {
    Op string    // "open", "unlink", etc.
    Path string  // The associated file.
    Err error    // Returned by the system call.
}

func (e *PathError) Error() string {
    return e.Op + " " + e.Path + ": " + e.Err.Error()
}

PathError's Error generates a string like this:
PathError 的 Error 会生成如下错误信息：
open /etc/passwx: no such file or directory

Such an error, which includes the problematic file name, the operation, and the operating system error it triggered, is useful even if printed far from the call that caused it; it is much more informative than the plain "no such file or directory".

When feasible, error strings should identify their origin, such as by having a prefix naming the operation or package that generated the error. For example, in package image, the string representation for a decoding error due to an unknown format is "image: unknown format".

错误字符串应尽可能地指明它们的来源，例如产生该错误的包名前缀。例如在 image 包中，由于未知格式导致解码错误的字符串为 “image: unknown format”。

若调用者关心错误的完整细节，可使用类型选择或者类型断言来查看特定错误，并抽取其细节。 对于 PathErrors，它应该还包含检查内部的 Err 字段以进行可能的错误恢复。

for try := 0; try < 2; try++ {
    file, err = os.Create(filename)
    if err == nil {
        return
    }
    if e, ok := err.(*os.PathError); ok && e.Err == syscall.ENOSPC {
        deleteTempFiles()  // Recover some space.
        continue
    }
    return
}



The second if statement here is another type assertion. If it fails, ok will be false, and e will be nil. If it succeeds, ok will be true, which means the error was of type *os.PathError, and then so is e, which we can examine for more information about the error.

The usual way to report an error to a caller is to return an error as an extra return value.

But what if the error is unrecoverable? Sometimes the program simply cannot continue.

This is only an example but real library functions should avoid panic. If the problem can be masked or worked around, it's always better to let things continue to run rather than taking down the whole program.

However, it is possible to use the built-in function recover to regain control of the goroutine and resume normal execution.

Because the only code that runs while unwinding is inside deferred functions, recover is only useful inside deferred functions.
调用 recover 将停止回溯过程，并返回传入 panic 的实参。 由于在回溯时只有被推迟函数中的代码在运行，因此 recover 只能在被推迟的函数中才有效。

func safelyDo(work *Work) {
    defer func() {
        if err := recover(); err != nil {
            log.Println("work failed:", err)
        }
    }()
    do(work)
}



In this example, if do(work) panics, the result will be logged and the goroutine will exit cleanly without disturbing the others. There's no need to do anything else in the deferred closure; calling recover handles the condition completely.

By the way, this re-panic idiom changes the panic value if an actual error occurs. However, both the original and new failures will be presented in the crash report, so the root cause of the problem will still be visible. 

### 一个Web服务器

Google provides a service at http://chart.apis.google.com that does automatic formatting of data into charts and graphs. 

saving you typing the URL into the phone's tiny keyboard.

Here's the complete program. An explanation follows.

flag.Parse()
    http.Handle("/", http.HandlerFunc(QR))
    err := http.ListenAndServe(*addr, nil)
    if err != nil {
        log.Fatal("ListenAndServe:", err)
    }

<img src="http://chart.apis.google.com/chart?chs=300x300&cht=qr&choe=UTF-8&chl={{.}}" />

Go is powerful enough to make a lot happen in a few lines.
