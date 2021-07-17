## Mastering Go
> Mihalis Tsoukalos

### Title Page

Mastering Go

Create Golang production applications using network libraries, concurrency, and advanced Go data structures

### About the author

Mihalis Tsoukalos is a technical author, a Unix administrator, a developer, and amathematician, who enjoys learning new things. He has written more than 250 technicalarticles for many publications, 

Mihalis is also the author of Go Systems Programming, by Packt Publishing, 2017 and thetechnical editor for MongoDB in Action, Second Edition, by Manning. 

### Preface

Please try to do the exercises located at the end of each chapter and do not hesitate to contact me with ways tomake any future editions of this book even better!

### Who this book is for

The main difference between these two books is that Go Systems Programming is about developing system tools using the capabilities of Go, whereas Mastering Go is about explaining the capabilities and the internals of Go in order to become a better Go developer. Both books can be used as a reference after reading them for the first or the second time.


### What this book covers

Chapter 1, Go and the Operating System, begins by talking about the history of Go and the advantages of Go before describing the godoc utility and explaining how you can compile and execute Go programs. 

The last topic of the first chapter is error handling, which plays a key role in Go.

Chapter 2, Understanding Go Internals, discusses the Go garbage collector and the way it operates

Chapter 3, Working with Basic Go Data Types, talks about the data types offered by Go, which includes arrays, slices, and maps as well as Go pointers, constants, loops, and working with dates and times.

, the switch statement, the strings package, the math/big package, and aboutdeveloping a key-value store in Go.

Chapter 5, Enhancing Go Code with Data Structures, is about developing your own data structures when the structures offered by Go do not fit a particular problem. 

The last topic of this chapter is random number generation.

Chapter 7, Reflection and Interfaces for All Seasons, discusses three advanced Go concepts: reflection, interfaces, and type methods. 

Chapter 9, Concurrency in Go – Goroutines, Channels, and Pipelines, discusses goroutines, channels and pipelines, which is the Go way of achieving concurrency. You will also learn about the differences between processes, threads, and goroutines, and the sync package and the way the Go scheduler operates.


You will learn more about the Go scheduler, the use of the powerful select keyword and the various types of Go channels as well as shared memory, mutexes, the sync.Mutex type, and the sync.RWMutex type. The last part of the chapter will talk about the context package, worker pools, and how to detect race conditions.

discusses code testing, code optimization, and code profiling as well as about cross compilation, creating documentation, benchmarking Go code, creating example function, and finding unreachable Go code.


You will even learn how to develop an entire website in Go! Furthermore, in this chapter, you will learn how to read the configuration of your network interfaces and how to perform DNS lookups in Go.

Chapter 13, Network Programming – Building Your Own Servers and Clients, talks about creating UDP and TCP servers and clients in Go, using the functionality offered by the net package.

### Download the example code files

7-Zip/PeaZip for LinuxThe code bundle for the book is also hosted on GitHub at https://github.com/PacktPublishing/Mastering-Go. In case there's an update to the code, it will be updated on the existingGitHub repository.

### Go and the Operating System

the best way to understand something is toexperiment with it. In this case, experimenting means writing Go code on your own, makingyour own mistakes, and learning from them! Just don't let the error messages discourageyou.

### The structure of the book

using Go basic types and Go composite types.

### Why learn Go?

Why learn Go?

Go wants tohave happy developers; therefore, by design, Go code looks attractive and familiar, and it iseasy to write.

### Go advantages

The most significant Go advantages and features are as follows:

Go code is easy to read and easy to understand.

Go code is portable, especially between Unix machines.

Go supports Garbage Collection, so you do not have to deal with memory allocation anddeallocation.

Go can build web applications, and it provides a simple web server for testing purposes.

come without bugs.Go uses static linking by default, which means that the binary files produced can be easilytransferred to other machines with the same OS. As a consequence, once a Go program iscompiled successfully and an executable file is generated, the developer does not need toworry about libraries, dependencies, and different library versions anymore.

### Is Go perfect?

Go is no exception to thisrule. 

Some of the disadvantages of Go are as follows:

C is still faster than any other programming language for systems programming, mainlybecause Unix is written in C.

### The godoc utility

One of these tools is the godoc utility, which allows you to see thedocumentation of existing Go functions and packages without needing an internetconnection.

### Compiling Go code

The main reason that aSourceFile is that big is because it is statically linked, which meansthat it does not require any external libraries in order to run.

### Executing Go code

This book mainly uses go run to execute the example code, primarily because it is simplerthan running go build and then running the executable file. Additionally, go run does notleave any files on your hard drive after the program has finished its execution.

### Two Go rules

 As aresult, the main purpose of the Go compiler is to compile and increase the quality of your Gocode.

### You either use a Go package or do not include it

Go has strict rules about package usage. Therefore, you cannot just include any packagethat you might think you will need and not use it afterwards. 

The Go compiler usually displaysuseful error messages and warnings that will most likely help you resolve an erroneoussituation, so do not underestimate these error messages and warnings.

 there is a wayto bypass this restriction

Using an underscore character in front of a package name in the import list will not createan error message in the compilation process, even if that package is not used in theprogram:

### Downloading Go packages

However, the go get command also compiles the package. The relevant files can be found atthe following place:

### Unix stdin, stdout, and stderr

As you know,Unix considers everything a file, even a printer or your mouse. Unix uses file descriptors,which are positive integer values, as an internal representation for accessing all of its openfiles, which is much more convenient than using long paths.

By default, all Unix systems support three special and standard filenames: /dev/stdin,/dev/stdout, and /dev/stderr, which can also be accessed using file descriptors 0, 1, and 2,respectively. 

Go uses os.Stdin for accessing standard input, os.Stdout for accessing standard output, andos.Stderr for accessing standard error.

it isbetter, safer, and more portable to stick with the os.Stdin, os.Stdout, and os.Stderr standardfilenames that Go offers.

### About printing output

The main difference between fmt.Print() and fmt.Println() is that the latter automatically adds anewline character each time you call it

You can find more information about verbs at https://golang.org/pkg/fmt/. 

which are the simplest functions that can be used forgenerating output on the screen, there is also the S family of functions that includes fmt.Sprintln(), fmt.Sprint(),and fmt.Sprintf(), which are used for creating strings based on the given format and the F family of functions.This includes fmt.Fprintln(), fmt.Fprint() and fmt.Fprintf(), which are used for writing to files using an io.Writer.

The next section will teach you how to print your data using standard output, which is pretty common in the Unixworld.

### Using standard output

In this case, the io.WriteString() function works in the same way as the fmt.Print() function; however, it takes onlytwo parameters. The first parameter is the file to which you want to write, which in this case is os.Stdout, and thesecond parameter is a string variable.

### Getting user input

There are three main ways to acquire user input:By reading the command-line arguments of a programBy asking the user for inputBy reading external files

### Reading from standard input

 The main advantage of the os package is that it is platformindependent. Put simply, its functions will work on both Unix and Microsoft Windows machines!

Here there is a call to bufio.NewScanner() using standard input (os.Stdin) as its parameter. This call returns abufio.Scanner variable, which is then used with the Scan() function for reading from os.Stdin line by line. Each linethat is read is printed on the screen before getting the next one. Note that each line printed by the programbegins with the > character.

In Unix, you can tell a program to stop reading data from standard input by pressing Ctrl + D.

### About error output

This section presents a technique for sending data to Unix standard error, which is the Unix way ofdifferentiating between actual values and error output.

Thus, when using bash(1), you can redirect the standard error output to a file as follows:[插图]

Similarly, you can discard error output by redirecting it to the /dev/null device, which is like telling Unix to ignoreit completely:

 If you want to save both standard output and standard error to the same file, you canredirect the file descriptor of standard error (2) to the file descriptor of standard output (1)! The followingcommand shows this technique, which is pretty common in Unix systems:

Last, you can send both standard output and standard error to /dev/null as follows:[插图]

### Writing to log files

The log package allows you to send log messages to the system logging service of your Unix machine,

Usually, most system log files on a Unix operating system can be found under the /var/log directory. However,the log files of many popular services, such as Apache and Nginx, can be found elsewhere, depending on theirconfiguration.

Generally speaking, using a log file to write some information is considered a better practice than writing thesame output on the screen for two reasons:

The log package offers many functions for sending output to the syslog server of a Unix machine. 

Logging functions can be extremely handy for debugging your programs, especially server processes written in Go, soyou should not undervalue their power.

### Log servers

All Unix machines have a separate server process that is responsible for receiving logging data and writing it tolog files. Various log servers exist that work on Unix machines; however, only two of them are used on most Unixvariants: syslogd(8) and rsyslogd(8).

The syslogd(8) server has a pretty similar configuration file that is usually /etc/syslog.conf. On macOS HighSierra, the /etc/syslog.conf file is almost empty and has been replaced by /etc/asl.conf.

### A Go program that sends information to log files

The log/syslog package is not implemented on the Microsoft Windows version of Go.

As a result, the preceding code sets the default logging to the local7 logging facility using the info logging level.The second parameter of the syslog.New() function is the name of the process that will appear on the logs as thesender of the message. Generally speaking, it is considered a good practice to use the real name of theexecutable in order to be able to find the information you want easily in the log files at another time.

If everything is OK, which means that the value of the error variable is equal to nil, you callthe log.SetOutput() function. This sets the output destination of the default logger, which in this case is thelogger you created earlier on (sysLog). Then you can use log.Println() to send information to the log server.

The important thing to remember from this section is that if the logging server of a Unix machine is notconfigured to catch all logging facilities, some of the log entries that you send to it might get discarded withoutany warnings.

### About log.Panic()

The output of log.Panic() includes additional low-level information that will hopefully help you resolve difficult andrare situations that happen in your Go code.

### Error handling in Go

It is up to thedeveloper to use common sense and decide what to do with each error value that the program might get.

### The error data type

Also presented here is the use of the err.Error() method, which allows you to convert anerror variable into a string variable. This function lets you compare an error variable with a string.

### Error handling

The panic function is a built-in Go function that stops the execution of a program and starts panicking! If youfind yourself using panic too often, you might want to reconsider your Go implementation

If you execute errors.go, you will get the following output:

### Additional resources

Additional resourcesHave a look at the following resources:

Have a look at https://golang.org/cmd/gofmt/, which is the documentation page of the gofmt tool that is usedfor formatting Go code

### Summary

This chapter addressed many interesting Go topics including compiling Go code, working with standard input,accessing standard output and standard error in Go, processing command-line arguments, printing on thescreen, and using the logging service of a Unix system as well as error handling and some general informationabout Go. You should consider all of these topics as foundational information about Go.

### Understanding Go Internals

you will learn about the Go garbage collector and how it works.

### The Go compiler

If you use the -pack command-line flag when executing go tool compile, you will get an archive file instead of anobject file:

An archive file is a binary file that contains one or more files that is primarily used for grouping multiple files intoa single file. The archive format used by Go is called ar.

### Garbage Collection

Garbage Collection is the process of freeing memory space that is not being used. In other words, the garbagecollector sees which objects are out of scope and can no longer be referenced, and it frees the memory spacethey consume. 

There is a trick that allows you to get even more detailed output about the way the Go garbage collectoroperates, which is illustrated by the next command:

So, if you put GODEBUG=gctrace=1 in front of any go run command, Go will print analytical data about theoperation of the garbage collector. The generated data will have the following format:

### The Tricolor algorithm

Now let's talk about the meaning of each color set. The objects of the black set are guaranteed to have nopointers to any object of the white set. However, an object in the white set can have a pointer to an object of theblack set, because this has no effect on the operation of the garbage collector! The objects of the grey set mighthave pointers to some objects of the white set. Also, the objects of the white set are candidates for garbagecollection.

Thus, there are three different colors: black, white, and grey. When the algorithm begins, all objects are coloredwhite. As the algorithm continues, white objects are moved into one of the other two sets. The objects that areleft in the white set are the ones that will be cleared at some point.

Go allows you to initiate a garbage collection manually by putting a runtime.GC() statement in your Go code.However, keep in mind that runtime.GC() will block the caller, and it might block the entire program, especially ifyou are running a very busy Go program with many objects. 

The Go garbage collector is always being improved by the Go team, mainly by trying to make it faster by lowering thenumber of scans it needs to perform over the data of the three sets . However, despite the various optimizations, thegeneral idea behind the algorithm remains the same.

### More about the operation of the Go Garbage Collector

The main concern of the Go garbage collector is low latency, which basically means short pauses in its operationin order to have a real-time operation. On the other hand, what a program does all the time is to create newobjects and manipulate existing objects with pointers. 

The way that the mark-and-sweep algorithm works is pretty simple and easy to understand: the algorithm stopsthe program execution (stop-the-world garbage collector) in order to visit all of the accessible objects of theheap of a program and marks them. After that, it sweeps the inaccessible objects. 

Go tries to lower that particular latency by running thegarbage collector as a concurrent process and using the tricolor algorithm described in the previous section.

It is really important to remember that the Go garbage collector is a real-time garbage collector, which runsconcurrently with the other goroutines of a Go program and only optimizes for low latency.

### About the unsafe package

Many low-level packages such as runtime, syscall, and os constantly use the unsafe package.

### Calling C code from Go using separate files

Calling C code from Go using separate files

### The defer keyword

It is very important to remember that deferred functions are executed in Last In First Out (LIFO) order after thereturn of the surrounding function. 

 However, this timethe anonymous function requires one integer parameter named n. The Go code tells us that the n parametertakes its value from the i variable used in the for loop.

 However, the trickypart here is that the deferred anonymous function is evaluated after the for loop ends, because it has noparameters. This means that is evaluated three times for an i value of 0, hence the generated output! This kind ofconfusing code is what might lead to the creation of nasty bugs in your projects, so try to avoid it!

Also, we will talk about the third line of the output, which is generated by the d3() function. Due to the parameterof the anonymous function, each time the anonymous function is deferred, it gets and uses the current value of i.As a result, each execution of the anonymous function has a different value to process, thus the generatedoutput.

After this, it should be clear that the best approach to the use of defer is the third one, which is exhibited in thed3() function. This is so because you intentionally pass the desired variable in the anonymous function in an easyto understand way.

### Panic and Recover

The most important part ofthe a() function is the defer block of code, which implements an anonymous function that will be called whenthere is a call to panic().

Nevertheless, the good thing is that panicRecover.go ended according to our will without panicking because theanonymous function used in defer took control of the situation! 

### Using the panic function on its own

Thus, using the panic() function on its own will terminate the Go program without giving you the opportunity torecover! Therefore use of the panic() and recover() pair is much more practical and professional than just usingpanic() alone.

The output of the panic() function looks like the output of the Panic() function from the log package. However, the panic() function sends nothing to thelogging service of your Unix machine.

### Two handy Unix utilities

Remember that, at the end of the day, all programs that work on Unix machines end up using C system calls tocommunicate with the Unix kernel and perform most of their tasks.

### The strace tool

The strace(1) command-line utility allows you to trace system calls and signals. As strace(1) is not available onmacOS, this section will use a Debian Linux machine to showcase strace(1).

The strace(1) output displays each system call with its parameters as well as its return value. Note that in theUnix world, a return value of 0 is a good thing!

In order to process a binary file, you will need to put the strace(1) command in front of the executable that youwant to process. 

### The dtrace tool

This subsection will use the dtruss(1) command-line utility that comes with macOS, which is just a dtrace(1) scriptthat shows the system calls of a process and saves you from having to write dtrace(1) code. Note that bothdtrace(1) and dtruss(1) need root privileges to run.

The output that dtruss(1) generates looks like the following:

So, dtruss(1) works in the same way as the strace(1) utility. Analogously to strace(1), dtruss(1) will print system callcounts when used with the -c parameter:

### The Go Assembler

If you are really interested in the Go assembler and you want to learn more, visit https://golang.org/doc/asm.

### Learning more about go build

If you want to learn more about what is happening behind the scenes when you execute a go build command, youshould add the -x flag to it, as in the next example:

Once again, there are many things happening in the background, and it is important to be aware of them.However, most of the time, you will not have to deal with the commands of the compilation process.

### General Go coding advices

If you have an error in a Go function, either log it or return it, do not do both unless you have a really goodreason for doing so!

Make sure that you pass a pointer to a variable of a function only when needed. The rest of the time, just passthe value of the variable.

If you don't really know a Go feature, test it before using it for the first time, especially if you are developing anapplication or a utility that will be used by a large number of users.

If you are afraid of making mistakes, you will most likely end up doing nothing really useful! So, experiment asmuch as you can!

### Working with Basic Go Data Types

these data types can help you store, retrieve, and alter the data in your programs in avery convenient and quick way. 

 you will learn the following topics:

### Go loops

Go offers the for loop, which allowsyou to iterate over many kinds of data types.

Go does not offer support for the while keyword. However, the for loops in Go can replace while loops!

### The for loop

This means that one of the most common ways for accessing all of the elements of an array, a slice, or a map is the for loop. The other way is with the use of the range keyword.

You can completely exit a for loop using the break keyword. 

Additionally, you can skip a single iteration of a for loop using the continue keyword. The continue keyword stops running the block, jumps back up to the top of the for loop, and carries on for the next item.

### Multi-dimensional arrays

The range keyword also works with Go maps, which makes it pretty handy and my preferred way of iteration.


One of the biggest problems with arrays is out-of-bounds errors, which means trying to access an element that does not exist. 

### The shortcomings of Go arrays

Putting it simply, if you need to add an element to an existing array that has no space left, you will need to create a bigger array and copy all of the elements of the old array to the new one.

Second, when you pass an array to a function as a parameter, you actually pass a copy of the array, which means that any changes you make to an array inside a function will be lost after the function exits. Last, passing a large array to a function can be pretty slow, mostly because Go has to create a copy of the array. The solution to all of these problems is to use Go slices, which will be presented in the next section.

WARNING: Because of their disadvantages, arrays are rarely used in Go!

### Go slices

. There are only a few occasions where you will need to use an array instead of a slice. The most obvious one is when you are absolutely sure that you will need to store a fixed number of elements.

As slices are passed by reference to functions, which means that what is actually passed is the memory address of the slice variable, any modifications that you make to a slice inside a function will not get lost after the function exits. Additionally, passing a big slice to a function is significantly faster than passing an array with the same number of elements because Go will not have to make a copy of the slice—it will just pass the memory address of the slice variable.

### Performing basic operations on slices

This means that slice literals are defined just like arrays but without the element count. If you put an element count in a definition, you will get an array instead!

You can define a new empty slice with 20 places that can be automatically expanded when needed as follows:
    integer := make([]int, 20)

If you want to empty an existing slice, the zero value for a slice variable is nil.
aSliceLiteral = nil

You can access the first element of the integer slice as integer[0], whereas you can access the last element of the integer slice as integer[len(integer)-1].

Also, you can access multiple continuous slice elements using the [:] notation. The next statement selects the second and the third elements of a slice:
integer[1:3]
Additionally, you can use [:] notation for creating a new slice from an existing slice or array:
s2 := integer[1:3]

altering the elements of a re-slice modifies the element of the original slice because both slices point to the same memory address! Put simply, the re-slice process does not make a copy of the original slice.

The second problem of re-slicing is that, even if you re-slice a slice in order to use a small part of the original slice, the underlying array from the original slice will be kept in memory for as long as the smaller re-slice exists because the original slice is being referenced by the smaller re-slice. 

it can cause problems when you are reading big files into slices and you only want to use a small part of them.

### Slices are being expanded automatically

Slices have two main properties: capacity and length. The tricky part is that usually these two properties have different values. 

As slices are dynamic in size, if a slice runs out of room, Go automatically doubles its current length to make room for more elements.


Put simply, if the length and the capacity of a slice have the same values and you try to add another element to the slice, the capacity of the slice will be doubled, whereas its length will be increased by one.

### Byte slices

A byte slice is a slice where its type is byte. You can create a new byte slice named s as follows:
s := make([]byte, 5)

### The copy() function

You should be very careful when using the copy() function on slices because the built-in copy(dst, src) copies the minimum of the len(dst) and len(src) elements.

As copy() only accepts slice arguments, you should also use the [:] notation to convert the array into a slice.

the program will fail to compile and will display one of the following error messages:

### Another example of slices

The second part of the program shows how to use the [:] notation to create a new slice that references an existing array. Remember that you are not creating a copy of the array, just a reference to it, which will be verified in the output of the program:

This happens because the zero value for the slice type is nil.

### Sorting slices using sort.slice()

The sort.Slice() function changes the order of the elements in the slice according to the sorting function.

If you try to execute sortSlice.go on a Unix machine with an older than 1.8 Go version, you will get the next error message:

### Go maps

The main advantage of maps is that they can use any data type as their index, which in this case is called a map key or just a key. 

You can create a new empty map with string keys and int values with the help of the make() function:
iMap = make(map[string]int)
Alternatively, you can use the next map literal in order to create a new map that will be populated with data:

You can iterate over all of the elements of a map using the following technique:
    for key, value := range iMap { 
        fmt.Println(key, value) 
    }

### Storing to a nil map

This means that trying to insert data into a nil map will fail, however, looking up, deleting, finding the length, and using range loops on nil maps will not crash your code!


### When you should use a map?

built-in Go structures are very fast, so do not hesitate using a Go map when you need to use one.

What you should remember is that Go maps are very convenient and can store many different kinds of data, while being both easy to understand and fast when you are working with them.


### Go constants

Constants in Go are defined with the help of the const keyword.

Generally speaking, constants are usually global variables. Thus, you might rethink your approach if you find yourself defining too many constant variables with a local scope!

Strictly speaking, the value of a constant variable is defined at compile time-not at run time. 

const s1 = "My String" 
    const s2 string = "My String"
Although both s1 and s2 are constants, s2 comes with a type declaration (string), which makes its declaration more restrictive than the declaration of s1. This is because a typed Go constant must follow all of the strict rules of a typed Go variable. 

As general advice, if you are using lots of constants in your programs, it might be a good idea to gather all of them in a Go package.

### The constant generator iota

The constant generator iota is used for declaring a sequence of related values that uses incrementing numbers without the need to type each one of them explicitly.

For p2_0, iota has the value of 0 and p2_0 is defined as 1. For p2_2, iota has the value of 2 and p2_2 is defined as the result of the expression 1 << 2, which is 00000100 in binary representation-the decimal value of 00000100 is 4, which is the result and the value of p2_2. Analogously, the value of p2_4 is 16, and the value of p2_6 is 32.

### Go pointers

When working with pointers, you need * to get the value of a pointer, which is called dereferencing the pointer, and & to get the memory address of a non-pointer variable.


### Dealing with times and dates

you will learn how to parse date and time strings in Go, how to convert between different date and time formats, and how to print dates and times in the format you desire.

    t := time.Now() 
    fmt.Println(t, t.Format(time.RFC3339)) 
    fmt.Println(t.Weekday(), t.Day(), t.Month(), t.Year()) 

The Format() function allows you to convert a time variable to another format-in this case, the RFC3339 format.

    formatT := t.Format("01 January 2006") 
    fmt.Println(formatT) 
    loc, _ := time.LoadLocation("Europe/Paris") 
    londonTime := t.In(loc) 
    fmt.Println("Paris:", londonTime) 
}

### The Uses of Composite Types

This chapter will investigate more advanced Go features such as tuples and strings, the strings standard Go package, the switch statement, but most importantly structures that are created using the struct keyword. 

•  Go strings, runes, byte slices, and string literals

### About composite types

Go has its own way of supporting tuples, which mainly allows functions to return multiple values without needing to group them in structures as is the case in C.

### Structures

Structures
Although arrays, slices, and maps are all very useful, they cannot group and hold multiple values in the same place. When you need to group various types of variables and create a new handy type, you can use a structure. 

A structure literal can be defined as follows:
p1 := aStructure{"fmt", 12, -2}
However, since remembering the order of the fields of a structure can be pretty hard, Go allows you to use another form for defining a structure literal:
p1 := aStructure{weight: 12, height: -2}

### Using the new keyword

new returns the memory address of the allocated object. Put simply, new returns a pointer!
You can create a fresh aStructure variable as follows:
pS := new(aStructure)

The main difference between new and make is that variables created with make are properly initialized without just zeroing the allocated memory space. Additionally, make can only be applied to maps, channels, and slices, and it does not return a memory address, which means that make does not return a pointer.


### Tuples

Strictly speaking, a tuple is a finite ordered list with multiple parts. The most important thing about tuples is that Go has no support for the tuple type, which means that Go does not officially care about tuples despite the fact that it has support for certain uses of tuples.


As you can see, tuples can do many intelligent things like swapping values without the need for a temporary variable as well as evaluating expressions.

### Regular expressions and pattern matching

If patternmatching is successful, it allows you to extract the desired data from the string, replace it, or delete it.

The Go package responsible for defining regular expressions and performing pattern matching is called regexp.You will see it in action in a while.

### A simple example

The program performs various tests to make sure that the text file does exist and that you can read it. 

 the bufio.ReadString() function reads a file untilthe first occurrence of its parameter. As a result, bufio.ReadString('\n') tells Go to read a file line by line because\n is the Unix newline character! The bufio.ReadString() function returns a byte slice.

the strings.Fields() function splits a string based on the whitespace characters thatare defined in the unicode.IsSpace() function and returns a slice of strings.

The important thing to remember here is that you should never trust your data, especially when it comesfrom nontechnical users. Put simply, always verify that the data you expect to grab is there!

### A more advanced example

how to match a date and time string as found in the log files of an Apache webserver.

### Strings

 However, there are subtle differences between a character, a rune, and a byte, as well as differencesbetween a string and a string literal, which will be clarified here.

A Go string is a read-only byte slice that can hold any type of bytes, and it can have an arbitrary length.

The output of len(s2) might surprise you a little. As the s2 variable contains Unicode characters, its byte size islarger than the number of characters in it.

### What is a rune?

A rune is an int32 value, and therefore it is a Go type that is used for representing a Unicode code point. AUnicode code point or code position is a numerical value that is usually used for representing single Unicodecharacters; however, it can also have alternative meanings, such as providing formatting information.

A rune literal is a character in single quotes. 

### The strings package

The strings standard Go package allows you to manipulate UTF-8 strings in Go and includes many powerfulfunctions. 

Another handy trick is that if you find yourself using the same function all of the time, and you want to usesomething shorter instead, you can assign a variable name to that function and use that variable name instead.

If you are working with text and text processing, you will definitely need to learn all of the gory details andfunctions of the strings package, so make sure that you experiment with all these functions a lot and create manyexamples that will help you clarify things.

### The switch statement

Last, the fallthrough keyword also tells Go to execute the branch that follows the current one, which in this caseis the default branch. 

### Calculating Pi with great accuracy

In this section, you will learn how to calculate Pi with great accuracy using a standard Go package namedmath/big and the special purpose types offered by that package.

The new(big.Float) call creates a new big.Float variable with the desired precision, which is set by SetPrec().

### Developing a key/value store in Go

The key-value store is stored in a native Go map because using a built-in Go structure is usually faster. The mapvariable is defined as a global variable, where its keys are string variables and its values are myElement variables.You can also see the definition of the myElement struct type here.

You can also improve keyValue.go by adding goroutines and channels to it. However, adding goroutines andchannels to a single user application has no practical meaning. Nevertheless, if you make keyValue.go capable ofoperating over TCP/IP networks, then the use of goroutines and channels will allow it to accept multipleconnections and serve multiple users. 

### Additional resources

You might find the following resources useful:

### Enhancing Go Code with Data Structures

using many well-known data structures in Goincluding binary trees, linked lists, hash tables, stacks, and queues and learning about their advantages. 

### About graphs and nodes

A cyclic graph is one where all or a number of its vertices are connected in a closed chain. In acyclic graphs, there are no closed chains. 

As a node may contain any kind of information, nodes are usually implemented using Go structures.

### Algorithm complexity

The worst algorithms, however, are the ones with an O(n!) running time, which makes them almost unusable for inputs with more than 300 elements.


Generally speaking, array operations are faster than map operations, which is the price you have to pay for the versatility of a map.

Although every algorithm has its drawbacks, if you do not have lots of data, the algorithm is not really important as long as it performs the desired job accurately.

### Binary trees in Go

A binary tree is a data structure where underneath each node there are two other nodes at most. At most means that a node can be connected to one, two, or no other nodes. 

### Implementing a binary tree in Go

type Tree struct { 
    Left  *Tree 
    Value int 
    Right *Tree 
}

What you see here is the definition of the node of the tree using a Go structure. The math/rand package is used for populating the tree with random numbers, as we do not yet have any real data.

func traverse(t *Tree) { 
    if t == nil { 
        return 
    } 
    traverse(t.Left) 
    fmt.Print(t.Value, " ") 
    traverse(t.Right) 
}

### Advantages of binary trees

Additionally, trees are ordered by design, which means that you do not have to make any special effort to order them; putting an element into its correct place keeps them ordered. However, deleting an element from a tree is not always trivial because of the way that trees are constructed.

### Hash tables in Go

Strictly speaking, a Hash table is a data structure that stores one or more key and value pairs and uses a hash function to compute an index into an array of buckets or slots from which the correct value can be discovered. Ideally, the hash function should assign each key to a unique bucket, provided that you have the required number of buckets, which is usually the case.

Additionally, the hash function should work consistently and output the same hash value for identical keys. Otherwise, it would be impossible to locate the information you want!

### Implementing a hash table in Go

The traverse() function is used for printing all of the values in the hash table. The function visits each one of the linked lists of the hash table and prints the stored values, linked list by linked list.

### Linked lists in Go

A Linked list is a data structure with a finite set of elements where each element uses at least two memory locations, one for storing the actual data and the other for storing a pointer that links the current element to the next one, thus creating a sequence of elements that construct the linked list.

### Advantages of linked lists

The greatest advantages of linked lists are that they are easy to understand and implement, and they are generic enough so that they can be used in many different situations. This means that they can be used to model many different kinds of data. Additionally, linked lists are really fast at sequential searching when used with pointers.


Linked lists cannot only help you sort your data, but they can also assist you in keeping your data sorted even after inserting or deleting elements.

### Doubly linked lists in Go

This is the price that you will have to pay for being able to access your doubly linked list both ways.

### Advantages of doubly linked lists

After all, your music player might be using a doubly linked list to represent your current list of songs and be able to go to the previous song as well as the next one!

### Queues in Go

The main advantage of queues is simplicity! You only need two functions to access a queue, which means that you have to worry about fewer things going wrong and you can implement a queue any way you want so long as you can offer support for those two functions!

### Stacks in Go

The main advantage of a stack is its simplicity because you only have to worry about implementing two functions in order to be able to work with a stack-adding a new node to the stack and removing a node from the stack.

### The container package

The container package supports three data structures: a heap, list, and ring. These data structures are implemented in container/heap, container/list, and container/ring, respectively.
For those of you who are unfamiliar with rings, a ring is a circular list, which means that the last element of a ring points to its first element. 

### Using container/heap

First of all, you should know that the container/heap package implements a heap, which is a tree where the value of each node of the tree is the smallest element in its subtree.

### Summary

It also includes information about how to define and use the various types of Go functions in your programs. So don't waste any more time—start reading it now!

### What You Might Not Know About Go Packages

The main focus of this chapter is Go packages, which is the Go way of organizing, delivering, and using code. The most common components of a Go package are functions, which are pretty flexible in Go.

•  The syscall standard Go package, which is a low-level package that, although you might not use directly, it is used extensively by other Go packages

### About Go packages

Everything in Go is delivered in the form of packages. A Go package is a Go source file that begins with the package keyword followed by the name of the package. Some packages have a structure. For example, the net package has several subdirectories, named http, mail, rpc, smtp, textproto, and url, which should be imported as net/http, net/mail, net/rpc, net/smtp, net/textproto, and net/url, respectively.

Packages are mainly used for grouping related functions, variables, and constants so that you can transfer them easily and use them in your own Go programs. 

This means that they need to be called directly or indirectly from a main package in order to be used. 

### About Go functions

, if you find yourselves writing functions that do multiple things, you might consider replacing them by multiple functions instead! 

### Anonymous functions

just do not use too many anonymous functions in your programs without having a good reason.

### Functions that return multiple values

It is not considered a good programming practice to alter the code of variables that hold anonymous functions because this might be the root cause of nasty bugs!

### The return values of a function can be named!

Additionally, when such a function has a return statement without any arguments, then the function automatically returns the current value of each named return value in the order in which they were declared in the definition of the function!


My personal advice is to name the return values of your functions unless there is a very good reason not to do so.


The purpose of the Go code in the main() function is to verify that all methods generate the same results.

### Functions that return pointers

 So, the scenario of functions returning pointers is very common. 

The only thing to remember is to use &y in the return statement in order to return the memory address of the y variable.


As you already know, the * character dereferences a pointer variable, which means that it returns the actual value stored at the memory address instead of the memory address itself.


The preceding code will return the memory address of the sq variable, not the int value stored in it!


### Functions that return other functions

In this code, you call funReturnFun() two times and assign its return value, which is a function, to two separate variables named i and j. As you will see in the output of the program, the two variables are totally unrelated to each other.

The important thing here is that although both i and j were created by calling funReturnFun(), they are totally independent of each other and share nothing. As a result, although their return values come from the same sequence, they do not interfere with each other in any way.
Executing returnFunction.go will produce the following output:

### Functions that accept other functions as parameters

The funFun() function accepts two parameters, a function parameter named f and an int value. The f parameter should be a function that takes one int argument and returns an int value.


### Developing your own Go packages

The source code of a Go package, which can contain multiple files and multiple directories, can be found within asingle directory that is named after the package name with the obvious exception of the main package that canbe located anywhere.

The first part of aPackage.go is shown in the following Go code:

As you can see, developing a new Go package is pretty easy! Right now you cannot use that package on its own,and you need to create a package named main with a main() function in it in order to create an executable file. 

you will get an error message, which means that youare not done yet:

Therefore, you will need to put the precedingpackage in the appropriate directory and make it available to the current Unix user. 

### Compiling a Go package

you must alsomake sure that you do not have any syntax errors in your code.

### Private variables and functions

Thisis the reason that fmt.Println() is named Println() instead of println().

However, this rule does not affect packagenames that are allowed to begin with uppercase and lowercase letters.

### Reading the Go code of a standard Go package

Now we are going to have a quick look at the Go code of net/url and log/syslog.

### Exploring the code of the net/url package

you can execute the following command and wait for its output:

The output of the preceding command on a Debian Linux machine is as follows:

Now let's say that you want to look at the implementation of one of the functions found in net/url, and thefunction that interests you is, say, User(). Its implementation is as follows:

The documentation of the User() function states that it returns a Userinfo, which is a structure defined in net/url:

Now things are becoming clearer. 

### Looking at the Go code of the log/syslog package

So, the definition of the netConn struct, which allows you to send logging data over the network, isas follows:

### Creating good Go packages

This section will provide some handy advice that will help you generate better Go packages.

Here are several good rules to follow to create superior Go packages:

Interfaces can improve the usefulness of your functions, so when you think it is appropriate, use an interfaceinstead of a single type as a function parameter or return type.

Do not create a package that already exists from scratch! Make changes to the existing package and maybecreate your own version of it.

Nobody wants a Go package that prints logging information on the screen. It would be more professional to havea flag for turning on logging when needed.

### Finding out how fmt.Println() really works

The implementation of the fmt.Println() function, as found in https://golang.org/src/fmt/print.go, is as follows:
func Println(a ...interface{}) (n int, err error) { 
   return Fprintln(os.Stdout, a...) 
}
This means that, at the end of the day, the fmt.Println() function calls fmt.Fprintln() to do its job.

This tells us that, at the end of the day, a call to the fmt.Println() function is implemented using a call tosyscall.Write(). This underscores how useful and necessary the syscall package is!

### Text and HTML templates

Templates are mainly used for separating the formatting part and the data part of the output. Please note that aGo template can be either a file or a string-the general idea is to use inline strings for smaller templates andexternal files for bigger ones.

### Generating text output

The range keyword allows you to iterate over the lines of theinput, which is given as a slice of structures.

So, a {{ range }} command is ended with {{ end }}. Accidentally putting {{ end }} in the wrong place will affect youroutput. Once again, keep in mind that empty lines in text template files are significant and will be shown in thefinal output.

### Constructing HTML output

To make thingseven easier, the example will populate a database table before reading from it!

Additionally, you are going to need an external template file, which will be named html.gohtml. It is going to beused for the generation of the output of the program.

Both text/template and html/template are powerful packages that can save you a lot of time, so I suggest thatyou use them when they fit the requirements of your applications.

### Additional resources

You will find the following resources very useful:

### Summary

we will discuss two important Go features: interfaces and reflection. 

### Type methods

A Go type method is a function with a special receiver argument. 

In Go, the receiver of a method is definedusing a regular variable name—usually using a single letter—without the need to use a dedicated keyword, suchas this or self.

### Go interfaces

Strictly speaking, a Go interface type defines the behavior of other types by specifying a set of methods thatneed to be implemented. For a type to satisfy an interface, it needs to implement all of the methods required bythat interface. Put simply, interfaces are abstract types that define a set of functions that need to beimplemented so that a type can be considered an instance of the interface.

When this happens, we say that thetype satisfies this interface. So, an interface is two things: a set of methods and a type, and it is used fordefining the behavior of other types.

The biggest advantage that you receive from having and using an interface is that you can pass a variable of atype that implements that particular interface to any function that expects a parameter of that specific interface.

Two very common Go interfaces are io.Reader and io.Writer, which are used in file input and output operations.More specifically, io.Reader is used for reading a file, whereas io.Writer is used for writing to a file.

In order for a type to satisfy the io.Reader interface, you will need to implement the Read() method as describedin the interface.

Generally speaking, interfacesshould be as simple as possible.

### About type assertion

A type assertion is the x.(T) notation where x is of interface type and T is a type. Additionally, the actual valuestored in x is of type T, and T must satisfy the interface type of x! 

Type assertions help you do two things. The first thing is to check whether an interface value keeps a particulartype. When used this way, a type assertion returns two values: the underlying value and a bool value. Although theunderlying value is what you might want to use, the Boolean value tells you whether the type assertion wassuccessful or not!

The second thing a type assertion does is to allow you to use the concrete value stored in an interface or assignit to a new variable. This means that if there is an int variable in an interface, you can get that value using typeassertion.

However, if a type assertion is not successful and you do not handle that failure on your own, your program willpanic. 

As the myInt variable does not contain a float64 value, the myInt.(float64) type assertion will fail unless handledproperly. Fortunately, in this case the correct use of the ok variable will save your program from panicking.

However, the second type assertion, which is myInt.(bool), will trigger a panic because the underlying value ofmyInt is not Boolean (bool). Therefore, executing assertion.go will generate the following output:

Go states pretty clearly the reason for panicking: interface {} is int, not bool.

### Developing your own interfaces

you will learn how to develop your own interfaces, This is a relatively easy process as long as youknow what you want to develop.

### Using a Go interface

In the second block, you see how you can find the values stored in a squareparameter. 

 you will get the following as output:

### Using switch with interface and data types

The trick presented here will help you differentiate between the different data types of the x parameter. All of themagic is performed by the use of the x.(type) statement that returns the type of the x element. The %!v(MISSING) verb usedin fmt.Printf() allows you to acquire the value of the type.

### Reflection

Reflection is an advanced Go feature that allows you to dynamically learn the type of an arbitrary object as wellas information about its structure. Go offers the reflect package for working with reflection. What you shouldremember is that you will most likely not need to use reflection in each one of your Go programs.

Reflection is necessary for the implementation of packages such as fmt, text/template, and html/template. In thefmt package, reflection saves you from having to deal explicitly with every data type that exists. However, even ifyou had the patience to write code for working with every data type that you knew, you still would not be able towork with all possible types! In this case, reflection makes it possible for the methods of the fmt package to findthe structure and to work with new types!

Additionally, reflection is handy when working with values of types that do not implement a commoninterface.

Reflection helps you work with unknown types and unknown values of types.

The stars of the reflect package are two types named reflect.Value and reflect.Type. The former type is used forstoring values of any type, whereas the latter type is used for representing Go types.

### A simple Reflection example

The Elem() method returns the value contained in the reflection interface (reflect.Value).

The NumField() method returns the number of fields in a reflect.Value structure, whereas theField() function returns the field of the structure that is specified by its parameter. The Interface() function returnsthe value of a field of the reflect.Value structure as an interface.

### The three disadvantages of reflection

The first reason is that extensive use of reflection will make your programs hard to read and maintain. A potentialsolution to this problem is good documentation; however, developers are famous for not having the time to writethe required documentation.

The second reason is that the Go code which uses reflection will make your programs slower. Generally speaking,Go code that is made to work with a particular data type will always be faster than Go code that uses reflectionto work dynamically with any Go data type. Additionally, such dynamic code will make it difficult for tools torefactor or analyze your code.

The last reason is that reflection errors cannot be caught at build time and are reported at runtime as a panic.This means that reflection errors can potentially crash your programs! This can happen months or even yearsafter the development of a Go program!

### Object-oriented programming in Go!

Composition allows you to create a structure in your Go elements using multiple struct types. In this case, datatype C groups an a variable and a b variable.

This technique allowsyou to share the same function name between multiple types.

### Summary

Although reflection is a very powerful Go feature, it might slow down your Go programs because it adds a layerof complexity at runtime. Additionally, your Go programs could crash if you use reflection carelessly.

### About Unix processes

a process is an execution environment that contains instructions, user data and system dataparts, and other types of resources that are obtained during runtime.

There are three categories of processes: user processes, daemon processes, and kernel processes. Userprocesses run in user space and usually have no special access rights. Kernel processes are executed in kernelspace only and can fully access all kernel data structures. Daemon processes are programs that can be found inthe user space and run in the background without the need for a terminal.

### Buffered and unbuffered file input and output

Buffered file input and output happens when there is a buffer for temporarily storing data before reading data orwriting data. Thus, instead of reading a file byte by byte, you read many bytes at once. You put it in a buffer andwait for someone to read it in the desired way. Unbuffered file input and output happens when there is no bufferto temporarily store data before actually reading or writing it.

### The bufio package

As the name suggests, the bufio package is about buffered input and output. 

### Reading text files

A text file is the most common kind of file that you can find on a Unix system. In this section, you will learn howto read text files in three ways: line by line, word by word, and character by character. 

### Reading a text file line by line

Going line by line is the most common method of reading a text file. This is the main reason it is being shownfirst. 

 After making sure that you can open the given filename forreading, you create a new reader using bufio.NewReader(). Then you use that reader with bufio.ReadString() inorder to read the input file line by line. The trick is done by the parameter of bufio.ReadString(), which is acharacter that tells bufio.ReadString() to keep reading until that character is found. 

The following command will verify the accuracy of the preceding output:

### Reading a text file word by word

 The regular expression defined in the regexp.MustCompile("[^\\s]+")statement states that empty characters will separate one word from another.

### Reading a text file character by character

Note that due to the fmt.Println(string(x)) statement, each character is printed in a distinct line, which means thatthe output of the program will be large. If you want a more compressed output, you should use the fmt.Print()function instead.

Note that the Go code presented here can be used for counting the number of characters found in the input file,which can help you implement a Go version of the handy wc(1) command-line utility.

### Reading from /dev/random

The purpose of the /dev/randomsystem device is to generate random data, which you might use for testing your programs or, in this case, youwill plant the seed for a random number generator. Getting data from /dev/random can be a little bit tricky, andthis is the main reason for specifically discussing it here.

### Reading the amount of data you want from a file

 You create a byte slice with the size you need and use thatbyte slice for reading. 

 The io.Reader.Read() method returns two parameters: the number of bytes read as well as an errorvariable. 

 Finally, there iscode that checks for io.EOF, which is an error that signifies that you have reached the end of a file. When thatkind of error occurs, the function returns.

### Why are we using binary format?

how you can read a file byte by byte, which is atechnique that best applies to binary files. So, you might ask why read data in binary format when text formatsare so much easier to understand? The main reason is space reduction.

Imagine that you want to store thenumber 20 as a string to a file. It is easy to understand that you will need two bytes for storing 20 using ASCIIcharacters, one for storing 2 and another for storing 0. Storing 20 in binary format requires just one byte, since20 can be represented as 00010100 in binary or as 0x14 in hexadecimal.

This difference might look insignificant when you are dealing with small amounts of data, but it could be prettysubstantial when dealing with data found in applications such as database servers.

### Reading CSV files

CSV files are plain text files with a format. 

 Additionally, you are also going touse an external Go library named Glot, which will help you create a plot of the points that you read from the CSVfile. Note that Glot uses Gnuplot, which means that you will need to install Gnuplot on your Unix machine in orderto use Glot.

In this part, you see a technique for checking whether a file already exists or not using the powerful os.Stat()function.

As you can guess, you will need to download the Glot Go library before being able to compile and execute theCSVplot.go source code, which requires the execution of the following command from your favorite Unix shell:

You can see the results of CSVplot.go in a much better format in the following figure:

### Writing to a file

In this case, f2.WriteString() is used for writing your data to a file.

In this case, bufio.NewWriter() opens a file for writing and bufio.WriteString() writes the data.

This method needs just a single function call named ioutil.WriteFile() for writing your data, and it does not requirethe use of os.Create().

The next section will show you how to save data to a file with the help of a specialized function of a package that is in the standard Go library.


### Loading and saving data on disk

how to save the data of a key-value store on disk and how to load it back into memory when you next start your application.


The process of converting your data into a stream of bytes is called serialization. The process of reading a data file and converting it into an object is called deserialization. 

The encoding/gob package uses the gob format for storing its data. The official name for such formats is stream encoding. The good thing about the gob format is that Go does all of the dirty work, so that you don't have to worry about the encoding and decoding stages.

Other Go packages that can help you serialize and deserialize data are encoding/xml, which uses the XML format, and encoding/json, which uses the JSON format.


encoder := gob.NewEncoder(saveTo) 
    err = encoder.Encode(DATA) 


    decoder := gob.NewDecoder(loadFrom) 
    decoder.Decode(&DATA) 

### The strings package revisited

This section will address the functions of the strings package that are related to file input and output.

The strings.NewReader() function creates a read-only Reader from a string. The strings.Reader object implements the io.Reader, io.ReaderAt, io.Seeker, io.WriterTo, io.ByteScanner, and io.RuneScanner interfaces.

Here you can see how you to use strings.Reader as an io.Reader interface in order to read a string byte by byte using the Read() function.

### About the bytes package

The bytes standard Go package contains functions for working with byte slices in the same way that the strings standard Go package helps you work with strings. 

    var buffer bytes.Buffer 
    buffer.Write([]byte("This is")) 
    fmt.Fprintf(&buffer, " a string!\n") 
    buffer.WriteTo(os.Stdout) 

First, you create a new bytes.Buffer variable, and you put data into it using buffer.Write() and fmt.Fprintf(). Then you call buffer.WriteTo() two times.

    buffer.Reset() 
    buffer.Write([]byte("Mastering Go!")) 

The Reset() method resets the buffer variable, and the Write() method puts some data into it again. Then you create a new reader using bytes.NewReader(), and after that you use the Read() method of the io.Reader interface to read the data found in the buffer variable.


### File permissions

The call to os.Stat(filename) returns a big structure with lots of data. As we are only interested in the permissions of the file, we will call the Mode() method and print its output. 

### Handling Unix signals

A program cannot handle all of the available signals: some signals cannot be caught, nor can they be ignored. The SIGKILL and SIGSTOP signals cannot be caught, blocked, or ignored. The reason for this is that they provide the kernel and the root user a way of stopping any process they want.

The most common way to send a signal to a process is by using the kill(1) utility. By default, kill(1) sends the SIGTERM signal. If you want to find all of the supported signals on your Unix machine, you should execute the kill -l command.

If you try to send a signal to a process without having the required permissions, kill(1) will not do the job and you will get an error message similar to the following:

### Handling two signals

Then you call signal.Notify() in order to state the signals that interest you. Next, you implement an anonymous function that runs as a goroutine in order to act when you receive any one of the signals you care about.

### Handling all signals

So, all the magic happens due to the signal.Notify(sigs) statement. As no signals are specified, all incoming signals will be handled!


It is very convenient to use one of the signals in order to exit your program. This gives you the opportunity to do some housekeeping in your program when needed. 

You still need to call time.Sleep() to prevent your program from terminating immediately.

### Programming Unix pipes in Go

According to the Unix philosophy, Unix command-line utilities should do one job and perform that job well! In practice, this means that instead of developing huge utilities that do lots of jobs, you should develop multiple smaller programs, which when combined, should perform the desired job. The most common way for two or more Unix command-line utilities to communicate is by using pipes.

you learned how to read from standard input and how to write to standard output. 

### Implementing the cat(1) utility in Go

if len(arguments) == 1 { 
        io.Copy(os.Stdout, os.Stdin) 
        return 
    } 

the process will fail and you will get the following error message instead:


Instead go run tries to compile cat.go twice, which causes the error message. The solution to this problem is to execute go build cat.go first, and then use cat.go or any other Go source file as the argument to the generated executable file.

### Traversing directory trees

Now imagine how difficult it would be to implement the same program using the standard library functions of the C programming language!

### Using eBPF from Go

eBPF stands for enhanced Berkeley Packet Filter, and it is an in-kernel virtual machine that is integrated into the Linux kernel and can be used for Linux tracing.

eBPF works on Linux machines with relatively new kernel versions but does not work on macOS or Mac OS X machines.

### About syscall.PtraceRegs

This means that this program will not work on machines running macOS and OS X.

### Tracing system calls

Tracing system calls
This section will present a pretty advanced technique that uses the syscall package and allows you to monitor the system calls executed in a Go program.

### Summary

Nevertheless, there are many more topics related to systems programming not mentioned in this chapter, such as working with directories, copying, deleting, and renaming files, dealing with Unix users

### Go Concurrency – Goroutines, Channels, and Pipelines

Goroutines are the smallest Go entities that can be executed on their own in a Go program, while channels can get data from goroutines in a concurrent and efficient way and thus allow goroutines to have a point of reference and communicate with each other. 

•  The differences between processes, threads, and goroutines

### About processes, threads, and goroutines

A thread is a smaller and lighter entity than a process or a program. Threads are created by processes and have their own flow of control and stack. A quick and simplistic way to differentiate a thread from a process is to consider a process as the running binary file and a thread as a subset of a process.


A goroutine is the minimum Go entity that can be executed concurrently. 

The main advantage of goroutines is that they are extremely lightweight and running thousands or hundreds of thousands of them is no problem.

### The Go scheduler

The Unix kernel scheduler is responsible for the execution of the threads of a program. On the other hand, the Go runtime has its own scheduler, which is responsible for the execution of the goroutines using a technique known as m:n scheduling, where m goroutines are executed using n operating system threads using multiplexing.

The Go scheduler is the Go component responsible for the way and the order in which the goroutines of a Go program get executed.

Be aware that as the Go scheduler only deals with the goroutines of a single program, its operation is much simpler, cheaper, and faster than the operation of the kernel scheduler.

### Concurrency and parallelism

It is only when you build software components concurrently that you can safely execute them in parallel, when and if your operating system and your hardware permit it. The Erlang programming language did this a long time ago—long before CPUs had multiple cores and computers had lots of RAM.


So, the developer should not think about parallelism, but about breaking things into independent components that solve the initial problem when combined.

### Goroutines

The go keyword makes the function call to return immediately, while the function starts running in the background as a goroutine and the rest of the program continues its execution.

you cannot control or make any assumptions about the order in which your goroutines are going to be executed because this depends on the scheduler of the operating system, the Go scheduler, and the load of the operating system.


### Creating a goroutine

, you will learn two ways for creating goroutines. The first one is using regular functions, while the second method is using anonymous functions—these two ways are equivalent.


However, if you have lots of code, it is considered better practice to create a regular function and execute it using the go keyword.

you will learn how to control the order in which your goroutines are executed as well as how to print the results of one goroutine before printing the results of the following one.

### Creating multiple goroutines

A for loop is used for creating the desired number of goroutines. Once again, you should remember that you cannot make any assumptions about the order in which they are going to be created and executed.

### Waiting for your goroutines to finish

waitGroup.Add(1) 
        go func(x int) { 
            defer waitGroup.Done() 
            fmt.Printf("%!d(MISSING) ", x) 
        }(i) 

Each call to sync.Add() increases a counter in a sync.WaitGroup variable. Notice that it is really important to call sync.Add(1) before the go statement in order to prevent any race conditions. When each goroutine finishes its job, the sync.Done() function will be executed, which will decrease the same counter.


The sync.Wait() call blocks until the counter in the relevant sync.WaitGroup variable is zero, giving your goroutines time to finish.

### What if the number of Add() and Done() calls do not agree?

When the number of the sync.Add() and sync.Done() calls are equal, everything will be fine in your programs. However, this section will tell you what will happen when these two numbers do not agree with each other.

The error message is pretty clear: fatal error: all goroutines are asleep - deadlock! So, this happened because you told your program to wait for n+1 goroutines by calling the sync.Add(1) function n+1 times while only n sync.Done() statements were executed by your n goroutines! As a result, the sync.Wait() call will wait indefinitely for one or more calls to sync.Done() without any luck, which is obviously a deadlock situation.


Once again, the root of the problem is stated pretty clearly: panic: sync: negative WaitGroup counter.

### Channels

A channel is a communication mechanism that allows goroutines to exchange data, among other things. 

First, each channel allows the exchange of a particular data type, which is also called the element type of the channel, and second, for a channel to operate properly, you will need someone to receive what is sent via the channel. You should declare a new channel using the chan keyword, and you can close a channel using the close() function.

Finally, a very important detail: when you are using a channel as a function parameter, you can specify its direction; that is, whether it is going to be used for sending or receiving. In my opinion, if you know the purpose of a channel in advance, you should use this capability because it will make your programs more robust as well as safer 

### Writing to a channel

All channels have a type associated with them.

### Reading from a channel

In the preceding code, you read from the c channel using the <-c notation. If you want to store that value to a variable named k instead of just printing it, you can use a k := <-c statement.

In the preceding code, you can see a technique for determining whether a given channel is open or not. The current Go code works fine when the channel is closed; 

### Channels as function parameters

, Go allows you to specify the direction of a channel when used as a function parameter; that is, whether it will be used for reading or writing. These two types of channels are called unidirectional channels, whereas, by default, channels are bidirectional.

### Pipelines

A pipeline is a virtual method for connecting goroutines and channels so that the output of one goroutine becomes the input of another goroutine, using channels to transfer your data.


Additionally, you are using fewer variables and therefore less memory space because you do not have to save everything as a variable. Finally, the use of pipelines simplifies the design of the program and improves its maintainability.


This program will have two channels. The first channel (channel A) will be used for getting the random numbers from the first function and sending them to the second function. The second channel (channel B) will be used by the second function for sending the acceptable random numbers to the third function. 

The third function will be responsible for getting the data from channel B, calculating it, and presenting the results.

### Additional resources

•  The documentation page of the sync package once more. Pay close attention to the sync.Mutex and sync.RWMutex types that will appear in the next chapter.

### Summary

Finally, you learned that channels can be used as parameters to Go functions. This allows developers to create pipelines where your data can flow.

Afterwards, you will learn about nil channels, signal channels, channel of channels, and buffered channels, as well as the context package.

popular among Go programmers because Go offers better, safer, and faster ways for goroutines to exchange data.

### Go Concurrency – Advanced Topics

•  Two techniques that allow you to time-out a goroutine which takes longer than expected to finish

•  Monitor goroutines
•  Channels of channels
•  Shared memory and mutexes

### The Go scheduler revisited

A goroutine in Go is a task, whereas everything after the calling statement of a goroutine is a continuation. 

The Go scheduler works using three main kinds of entities: OS threads (M) that are related to the operating system in use, goroutines (G), and logical processors (P). The number of processors that can be used by a Go program is specified by the GOMAXPROCS environment variable—at any given time there are most GOMAXPROCS processors.

OS threads are pretty expensive, however, which means that dealing with OS threads too much might slow down your Go applications.


The Go scheduler, as most Go components, is always evolving, which means that the people who work on the Go scheduler continually try to improve its performance by making small changes in the way it works. The core principles, however, remain the same.

### The GOMAXPROCS environment variable

However, using a GOMAXPROCS value that is larger than the number of the available cores will not necessarily make your program run 

You can modify the previous output by changing the value of the GOMAXPROCS environment variable prior to the execution of the program, however. The following commands are executed in the bash(1) Unix shell:


### The select keyword

As you will learn in a short while, the select keyword is pretty powerful and it can do many things in a variety of situations. The select statement in Go looks like a switch statement for channels.

This means that you should be extra careful during the design and the development process in order to avoid such deadlocks.

The time.After() function will return after the specified time has passed, and therefore it will unblock the select statement in case all of the other channels are blocked.

A select statement is not evaluated sequentially, as all of its channels are examined simultaneously. If none of the channels in a select statement is ready, the select statement will block until one of the channels is ready. If multiple channels of a select statement is ready, then the Go runtime will make a random selection over the set of these ready channels. The Go runtime tries to make this random selection between these ready channels as uniformly and as fairly as possible.


The biggest advantage of select is that it can connect, orchestrate, and manage multiple channels. As channels connect goroutines, select connects channels that connect goroutines. Therefore, the select statement is one of the most important, if not the single most important, part of the Go concurrency model.

### Timing out a goroutine

This section presents two very important techniques that will help you time out goroutines. Put simply, these two techniques will save you from having to wait forever for a goroutine to finish its job, and they will give you full control over the amount of time that you want to wait for a goroutine to end.

Both techniques use the capabilities of the handy select keyword combined with the time.After() function that you experienced in the previous section.


### Timing out a goroutine – take 1

The time.Sleep() call is used for emulating the time it will normally take for the function to finish its job. In this case, the anonymous function that is executed as a goroutine will take about 3 seconds (time.Second * 3) before writing a message to the c1 channel.


The third code segment from timeOut1.go contains the following Go code:

The purpose of the time.After() function call is to wait for the chosen time. In this case, you are not interested in the actual value returned by time.After(), but in the fact that the time.After() function call has ended, which means that the time has passed. 

### Timing out a goroutine – take 2

The time.Duration() function converts an integer value into a time.Duration variable that you can use afterwards.


### Go channels revisited

It helps to remember that the zero value of the channel type is nil, and that if you send a message to a closed channel, the program will panic. However, if you try to read from a closed channel, you will get the zero value of the type of that channel. So, after closing a channel, you can no longer write to it, but you can still read from it.


In order to be able to close a channel, the channel must not be receive-only. Additionally, a nil channel always blocks, which means that trying to read or write from a nil channel will block. This property of channels can be very useful when you want to disable a branch of a select statement by assigning the nil value to a channel variable.

Finally, if you try to close a nil channel, your program will panic.

### Signal channels

Signal channels should not be used for transferring data.

### Buffered channels

All incoming requests are forwarded to a channel, which processes them one by one. When the channel is done processing a request, it sends a message to the original caller saying that it is ready to process a new one. So, the capacity of the buffer of the channel restricts the number of simultaneous requests that it can keep.

### Nil channels

you will learn about nil channels. These are a special kind of channel because they will always block.

t := time.NewTimer(time.Second) 

         case <-t.c: 

The <-t.C statement blocks the C channel of the t timer for the time that is specified in the time.NewTimer() call. Do not confuse the c channel, which is the parameter of the function, with the t.C channel, which belongs to the t timer. When the time expires, the timer sends a value to the t.C channel.

The remaining Go code of nilChannel.go is as follows:

The time.Sleep() function is used for giving enough time to the two goroutines to operate.

### Channel of channels

A channel of channels is a special kind of channel variable that works with channels instead of other types of variables. Nevertheless, you still have to declare a data type for a channel of channels. You can define a channel of channels using the chan keyword two times in a row, as shown in the following statement:
c1 := make(chan chan int)

### Specifying the order of execution for your goroutines

The A() function is blocked by the channel stored in the a parameter. Once that channel is unblocked in the main() function, the A() function will start working. Finally, it will close the b channel, which will unblock another function, in this case function B().

However, if you call A() or B() more than once, you will most likely get an error message such as the following:


### Shared memory and shared variables

. A Mutex variable, which is an abbreviation for mutual exclusion variable, is mainly used for threadsynchronization and for protecting shared data when multiple writes can occur at the same time.

A mutex workslike a buffered channel of capacity one, which allows at most one goroutine to access a shared variable at anygiven time. This means that there is no way for two or more goroutines to try to update that variablesimultaneously.

Therefore,identifying the critical sections of your code will make the whole programming process so much simpler that youshould pay attention to this task.

### The sync.Mutex type

The sync.Mutex type is the Go implementation of a mutex. 

Locking amutex means that nobody else can lock it until it has been released using the sync.Unlock() function.

The critical section of this function is the Go code between the m.Lock() and m.Unlock() statements.

Similarly, the critical section of this function is defined by the m.Lock() and m.Unlock() statements.

If you remove the m.Lock() and m.Unlock() statements from the change() function, the program will generateoutput similar to the following:

### What happens if you forget to unlock a mutex?

 you will see what happens if you forget to unlock sync.Mutex. 

### The sync.RWMutex type

In other words, sync.RWMutex is based on sync.Mutex with the necessary additions and improvements.

you can have multiple readers owning a sync.RWMutex mutex.However, there is one thing of which you should be aware: until all of the readers of a sync.RWMutex mutexunlock that mutex, you cannot lock it for writing, which is the small price you have to pay for allowing multiplereaders.

The functions that can help you work with a sync.RWMutex mutex are RLock() and RUnlock(), which are used forlocking and unlocking the mutex, respectively, for reading purposes. 

Finally, it should be apparent that you should not make changes to any shared variables insidethe RLock() and RUnlock() blocks of code.

The show() function uses the RLock() and RUnlock() functions because its critical section is used for reading ashared variable. So, although many goroutines can read the shared variable, no one can change it without usingthe Lock() and Unlock() functions. However, the Lock() function will be blocked for as long as there is someonereading that shared variable using the mutex.

Note that the > /dev/null at the end of the preceding commands is for omitting the output of the two commands.Thus, the version that uses the sync.RWMutex mutex is much faster than the version that uses sync.Mutex.

### Sharing memory using goroutines

This means that othergoroutines must send messages to this single goroutine that owns the shared data, which prevents the corruptionof the data. Such a goroutine is called a monitor goroutine. In Go terminology, this is sharing by communicatinginstead of communicating by sharing.

The purpose of the set() function is to set the value of the shared variable, whereas the purpose of the read()function is to read the value of the saved variable.

Personally, I prefer to use a monitor goroutine instead of the traditional shared memory techniques because theimplementation that uses the monitor goroutine is safer and closer to the Go philosophy.

### Catching race conditions

Strictly speaking, a data race occurs whentwo or more instructions access the same memory address, where at least one of them performs a writeoperation.

Using the -race flag when running or building a Go source file will turn on the Go race detector, which will makethe compiler create a modified version of a typical executable file. This modified version can record all accessesto shared variables as well as all synchronization events that take place, including calls to sync.Mutex andsync.WaitGroup.

After analyzing the relevant events, the race detector prints a report that can help you identifypotential problems so that you can correct them.

If you execute raceC.go, you will get the following output without any warning or error messages:

So, the race detector found two data races. Each one begins with the WARNING: DATA RACE message in itsoutput.

After examining the related code, it is easy to seethat the actual problem is that the anonymous function takes no parameters, which means that the value of i thatis used in the for loop cannot be deterministically discerned, as it keeps changing due to the for loop, which is awrite operation.

The root of the problem is pretty obvious: concurrent map writes.

### The context package

The main purpose of the context package is to define the Context type and support cancellation! Yes, you heardthat right; the main purpose of the context package is supporting cancellation because there are times that, forsome reason, you want to abandon what you are doing. 

If you take a look at the source code of the context package, you will realize that its implementation is prettysimple—even the implementation of the Context type is pretty simple, yet the context package is very important.

The Context type is an interface with four methods, named Deadline(), Done(), Err(), and Value(). The good newsis that you do not need to implement all of these functions of the Context interface—you just need to modify aContext variable using functions such as context.WithCancel() and context.WithTimeout().

The f1() function requires just one parameter, which is the time delay, because everything else is defined insidethe function. Note that the type of the cancel variable is context.CancelFunc.

 The return value of Context.Done() is a channel, because otherwise you would have not been able touse it in a select statement.

This part showcases the use of the context.WithTimeout() function, which requires two parameters: a Contextparameter and a time.Duration parameter. When the timeout period expires, the cancel() function is automaticallycalled.

the use of the context.WithDeadline() function that requires two parameters: aContext variable and a time in the future that signifies the deadline of the operation. When the deadline passes,the cancel() function is automatically called.

### An advanced example of the context package

you will create an HTTP client that does not want to wait too long for a server response, which isnot an unusual scenario. 

The useContext.go program requires two command-line arguments: the URL of the server to which it is going toconnect and the delay for which it should wait. 

 the definition of a structurenamed myData for keeping together the response of the web server, along with an error variable in case there isan error somewhere.

### Worker pools

Generally speaking, a Worker pool is a set of threads that are about to process jobs assigned to them. More orless, the Apache web server works that way: the main process accepts all incoming requests that are forwardedto the worker processes for getting served. Once a worker process has finished its job, it is ready to serve a newclient. 

there is a central difference here because our worker pool will use goroutines instead ofthreads.

Additionally, threads do not usually die after serving a request because the cost of ending a thread and creating a new one is too high, whereas goroutines do die after finishing their job.

As you will see shortly, worker pools in Go are implemented with the help of buffered channels, because they allow you to limit the number of goroutines running at the same time.

Despite the deceptive simplicity of workerPool.go, the Go code of the program can be easily used as a template for implementing much more difficult tasks.

Put simply, the Client structure holds the input data of each request, whereas the Data structure holds the results of a request.

If you want your program to run faster, you can increase the value of the size parameter.

func makeWP(n int) { 
    var w sync.WaitGroup 
    for i := 0; i < n; i++ { 
         w.Add(1) 
         go worker(&w) 
    } 
    w.Wait() 

The purpose of the create() function is to create all requests properly using the Client type and then to write them to the clients channel for processing. Note that the clients channel is read by the worker() function.


In the preceding code, you read your command-line parameters. First, however, you see that you can use the cap() function to find the capacity of a channel.

### Summary

This chapter addressed many important topics related to goroutines. Mainly, however, it clarified the power of the select statement. Additionally, it demonstrated the use of the context standard Go package.

Due to the capabilities of the select statement, channels are the preferred Go way for interconnecting the components of a Go program.

So think in Go terms before deciding to use shared memory in your Go code. Nonetheless, if you really have to use shared memory, you might want to use a monitor goroutine instead.

The primary subjects of the next chapter will be code testing, code optimization, and code profiling with Go. Apart from these topics, you will learn about benchmarking Go code, cross compilation, and finding unreachable Go code.

### Code Testing, Optimization, and Profiling

The previous chapter discussed concurrency in Go and how the select statement allows you to use channels as glue for controlling goroutines and allowing goroutines to communicate.

This chapter primarily addresses code optimization, code testing, code documentation, and code profiling.

Code optimization is a process where one or more developers try to make certain parts of a program run faster, more efficient, or use fewer resources. Put simply, code optimization is about eliminating the bottlenecks of a program.

The best time to write code to test your program is during the development phase, as this can help reveal bugs in your code as early as possible.

The results of code profiling may help you decide which parts of your code need to change.


### Comparing Go version 1.10 with Go version 1.9

•  The go test command caches test results, which means that it might run a little faster.

•  The go fix tool replaces the import statements of golang.org/x/net/context with just context.


As you must realize, the Go programming language progresses without breaking things. This might look insignificant at first, but it is a very important feature of Go which guarantees that the existing Go code will continue to compile without changes while Go evolves. 

### About optimization

 If you have any bugs in your program, you should debug it first!

The real problem is that programmers have spent far too much time worrying about efficiency in the wrong places and at the wrong times; premature optimization is the root of all evil (or at least most of it) in programming.


### Profiling Go code

In this section, you will learn how to profile Go code in order to understand your code better and improve its performance. 

### A simple profiling example

Go supports two kinds of profiling: CPU profiling and memory profiling. It is not recommended that you profile an application for both kinds at the same time, because these two different kinds of profiling do not work well with each other. 

   pprof.StartCPUProfile(cpuFile) 
    defer pprof.StopCPUProfile() 

It is only after collecting the profiling data that you can start to inspect it. Thus, you can now start the command-line profiler to examine the CPU data as follows:
$ go tool pprof /tmp/cpuProfile.out
Main binary filename not available.
Type: cpu
Time: Mar 8, 2018 at 4:53pm (EET)
Duration: 19.12s, Total samples = 4.18s (21.86%!)(MISSING)

Do find the time to try out all of the commands of the go tool pprof utility and familiarize yourself with them.

You can use the list command followed by the function name, combined with the package name, and you'll get more detailed information about the performance of that function:
(pprof) list main.N1
Total: 4.18s

You can also create a PDF output of the profiling data from the shell of the Go profiler, using the pdf command:
(pprof) pdf

Finally, a warning: if your program executes too quickly, then the profiler will not have enough time to get its required samples and you might see the Total samples = 0 output when you load the data file. 

 This is the reason for using the time.Sleep() function in some of the functions of profileMe.go:

### A convenient external package for profiling

In the preceding code, you can see the use of an external Go package that can be found at, github.com/pkg/profile. You can download it with the help of the go get command, as follows:
$ go get github.com/pkg/profile

### The web interface of the Go profiler

You can start the interactive Go profiler as follows:
$ go tool pprof -http=[host]:[port] aProfile

### A profiling example that uses the web interface

A profiling example that uses the web interface

$ go tool pprof -http=localhost:8080 /tmp/cpuProfile.out

### A quick introduction to Graphviz

Graphviz is a very handy compilation of utilities and a computer language that allows you to draw complex graphs. Strictly speaking, Graphviz is a collection of tools for manipulating both directed and undirected graph structures and generating graph layouts. 

Graphviz has its own language, named DOT, which is simple, elegant, and powerful.

Compiling the preceding code using one of the Graphviz command-line tools in order to create a PNG image requires the execution of the following command in your favorite Unix shell:
$ dot -T png graph.dot -o graph.png

### The go tool trace utility

The go tool trace utility is a tool for viewing trace files that can be generated with any one of the following threeways:Using the runtime/trace packageUsing the net/http/pprof packageExecuting the go test -trace commandThis section will use the first technique only. 

 you first need to import the runtime/trace standard Go package in order to collect data forthe go tool trace utility.

First, you will create a new file that will hold the tracing data for the go tool trace utility. Thenyou can start the tracing process, using trace.Start(). When you are done, you will call the trace.Stop() function.The defer call to this function means that you want to terminate tracing when your program ends.

Examining the operation of the Go Garbage Collector using the go tool traceIn the preceding figure, you can see that the Go GC runs on its own goroutine, but it does not run all of the time.Additionally, you can see the number of goroutines used by the program. You can learn more about this byselecting certain parts of the interactive view. 

Note that although go tool trace is a very handy and powerful utility, it cannot solve every kind of performanceproblem.

### Testing Go code

Finally, you will need to import the testing standard Go package for the go test subcommand to work correctly.As you will soon see, this import requirement also applies to two additional cases.

Once the testing code is correct, the go test subcommand does all the dirty work for you, which includesscanning all the *_test.go files for special functions, generating a temporary main package properly, calling thesespecial functions, getting the results, and generating the final output.

Always put the testing code in a different source file. There is no need to create a huge source file that is hard to readand maintain.

### Benchmarking Go code

Benchmarking can give you information about the performance of a function or a program in order to understandbetter how much faster or slower a function is compared to another function, or compared to the rest of theapplication. Using that information, you can easily reveal the part of the Go code that needs to be rewritten inorder to improve its performance.

Go follows certain conventions regarding benchmarking. The most important convention is that the name of abenchmark function must begin with Benchmark.

### A wrong benchmark function

The BenchmarkFibo() function has a valid name and the correct signature. The bad news, however, is that thisbenchmark function is wrong, and you will not get any output from it after executing the go test command!

### Benchmarking buffered writing

It is extremely obvious that using a write buffer with a size of 1 byte is totally inefficient and slows everythingdown. Additionally, such a buffer size requires too many memory operations, which slows down the program evenmore!

Using a write buffer with 2 bytes makes the entire program twice as fast, which is a good thing. However, it isstill very slow. The same applies to a write buffer with a size of 4 bytes.

### Finding unreachable Go code

This means that go tool vet cannot catch every possible type of logical error.

### Cross-compilation

Cross-compilation is the process of generating a binary executable file for a different architecture than the oneon which you are working.

### Creating example functions

Therefore, if an example function contains an // Output: line, the go test tool will check whether thecalculated output matches the values found after the // Output: line.

Moreover, the name ofeach example function must begin with Example. Lastly, example functions take no input parameters and returnno results!

You will observe that the preceding output tells us that there is something wrong with the S1() function based onthe data after the // Output: comment.

### Generating documentation

Keep in mind that comments which begin with BUG(something) will appear in the Bugs section of thedocumentation of a package, even if they do not precede a declaration.

Note that you can use you any port number you want, provided that the port is not already in use by anotherprocess. If that is the case, you will see an error message similar to the following:

The following screenshot shows the root directory of the godoc server that we just started. There you can seethe documentMe package that you created in documentMe.go among the other Go packages:

### Summary

The go test command isused for testing and benchmarking Go code, as well as for offering extra documentation with the use of examplefunctions.

### The Foundations of Network Programming in Go

Wireshark and tsharkTiming out HTTP connections that take too long to finish either on the server or client endFeel free to skip some of the low-level information presented in this chapter—you can always revisit it at anothertime.

### About net/http, net, and http.RoundTripper

. The http.Get() and http.Get() methods can be used for making HTTP and HTTPSrequests, 

### The http.Response type

The definition of the http.Response structure, which can be found in the https://golang.org/src/net/http/response.go file, is as follows:

The goal of this pretty complex http.Response type is to represent the response of an HTTP request. The sourcefile contains more information about the purpose of each field of the structure, which is the case with most structtypes found in the standard Go library.

### The http.Request type

The purpose of the http.Request type is to represent an HTTP request as received by a server, or as it is about tobe sent to a server by an HTTP client.

### The http.Transport type

As you can see, the http.Transport structure is pretty complex and contains a very large number of fields. Thegood news is that you will not need to use the http.Transport structure in all of your programs and that you arenot required to deal with all of its fields each time that you use it.

### About TCP/IP

TCP/IP is a family of protocols that help the internet operate. Its name came from its two most well-knownprotocols—TCP and IP.

TCP stands for Transmission Control Protocol. TCP software transmits data between machines using segments,which are also called TCP packets. The main characteristic of TCP is that it is a reliable protocol, which meansthat it makes sure that a packet was delivered without needing any extra code from the programmer. If there isno proof of a packet delivery, TCP resends that particular packet. Among other things, a TCP packet can be usedfor establishing connections, transferring data, sending acknowledgements, and closing connections.

IP stands for Internet Protocol. The main characteristic of IP is that it is not a reliable protocol by nature. IPencapsulates the data that travels over a TCP/IP network because it is responsible for delivering packets fromthe source host to the destination host according to the IP addresses.

As aresult, UDP messages can be lost, duplicated, or arrive out of order. Furthermore, packets can arrive faster thanthe recipient can process them. So, UDP is used when speed is more important than reliability.

### About IPv4 and IPv6

The main problem with IPv4 is that it is at the verge of running out of IP addresses, which is the main reason forcreating the IPv6 protocol. 

### The nc(1) command-line utility

By default, nc(1) uses the TCP protocol. However, if you execute nc(1) with the -u flag, then nc(1) will use theUDP protocol.The -l option tells netcat(1) to act as a server, which means that netcat(1) will start listening for connections atthe given port number.

Finally, the -v and -vv options tell netcat(1) to generate verbose output, which can come in handy when you wantto troubleshoot network connections.

### Reading the configuration of network interfaces

There are four core elements in a network configuration: the IP address of the interface, the network mask of theinterface, the DNS servers of the machine, and the default gateway or default router of the machine. 

 Thismeans that there is no portable way to discover the DNS configuration and the default gateway information of aUnix machine.

As a result, in this section, you will learn how to read the configuration of the network interfaces of a Unixmachine with Go. For this purpose, I will present two portable utilities that allow you to find out information aboutyour network interfaces.

The net.Interfaces() function returns all of the interfaces of the current machine as a slice with elements of thenet.Interface type. This slice will be used shortly to acquire the desired information.

### Performing DNS lookups

The output of the last command shows that sometimes a single hostname (cnn.com) might have multiple public IPaddresses. Pay special attention to the word public here, because although www.google.com has multiple IPaddresses, it uses just a single public IP address (216.58.214.36).

### Getting the NS records of a domain

A very popular DNS request has to do with finding out the name servers of a domain, which are stored in the NSrecords of that domain. 

All the work is done by the net.LookupNS() function, which returns the NS records of a domain as a slice variableof the net.NS type. This is the reason for printing the Host field of each net.NS element of the slice.

You can verify the correctness of the preceding output with the help of the host(1) utility:

### Getting the MX records of a domain

The only difference is that you use the net.LookupMX() function instead of the net.LookupNS() function.

### Creating a web server in Go

What is really interesting here is that all URLs apart from /time are served by the myHandler() function becauseits first argument, which is /, matches every URL that is not matched by another handler!

### Profiling an HTTP server

you remember that the net/http/pprof package should be usedfor profiling web applications with an HTTP server, whereas the runtime/pprof standard Go package should beused for profiling all other kinds of applications.

The preceding Go code defines the HTTP endpoints related to profiling. Without them, you will not be able toprofile your web application.

Using the Go profiler to get data is a pretty simple task that requires the execution of the next command, whichwill take you to the shell of the Go profiler automatically:

### Creating a website in Go

The following screenshot presents the contents of the key-value store:

### HTTP tracing

Go supports HTTP tracing with the help of the net/http/httptrace standard package! This package allows you totrace the phases of an HTTP request. 

The preceding code is all about tracing HTTP requests. The httptrace.ClientTrace object defines the events thatinterest us. When such an event occurs, the relevant code is executed. You can find more information about thesupported events and their purpose in the documentation of the net/http/httptrace package.

you might get lots ofoutput when you test it on a real web server, which is the main reason for using the web server developed inwww.go here.

If you have the time to look at the source code of the net/http/httptrace package at https://golang.org/src/net/http/httptrace/trace.go, you will immediately realize that net/http/httptrace is a pretty low-level package that usesthe context, reflect, and internal/nettrace packages to implement its functionality.

### Testing HTTP handlers

The httptest.NewRecorder() function returns an httptest.ResponseRecorder object, and it is used for recordingthe HTTP response.

You first check that the response code is as expected, and then you verify that the body of the response is alsowhat you expected.

### Making your Go web client more advanced

The http.NewRequest() function returns an http.Request object given a method, a URL, and an optional body. Thehttp.Do() function sends an HTTP request (http.Request) using an http.Client and gets an HTTP response(http.Response). So, http.Do() does the job of http.Get() in a more comprehensive way.

Feel free to make any changes you want to advancedWebClient.go in order to make the output match yourrequirements!

### Timing out HTTP connections

This section will present a technique for timing out network connections that take too long to finish. 

You will learn more about the functionality of SetDeadline() in the next subsection. The Timeout() function will beused by the Dial field of a http.Transport variable.

### More information about SetDeadline

The SetDeadline() function is used by the net package to set the read and write deadlines of a given networkconnection. Due to the way the SetDeadline() function works, you will need to call SetDeadline() before any reador write operations. Keep in mind that Go uses deadlines to implement timeouts, so you do not need to reset itevery time your application receives or sends any data.

### Setting the timeout period on the server side

you will learn how to time out a connection on the server side. You need to do this becausethere are times that clients take much longer than expected to end an HTTP connection. This usually happens fortwo reasons: the first reason is when there are bugs in the client software, and the second reason is when aserver process is experiencing an attack!

The value of the WriteTimeout field specifies the maximum duration before timing out writes of the response. Putsimply, this is the time from the end of the request header read to the end of the response write.

### Yet another way to time out!

As you will see,this is the simplest form of timeout, because you will just need to define an http.Client object and set its Timeoutfield to the desired timeout value!

### Summary

You also learned about the http.Response, the http.Request, and the http.Transport structures that allow youto define the parameters of an HTTP connection.

Additionally, you learned how to get the network configuration of a Unix machine, using Go code, how to performDNS lookups in a Go program, including getting the NS and MX records of a domain.

 we will continue our discussion of network programming in Go. However, thistime, we will present lower-level Go code that allows you to develop TCP clients and servers as well as UDPclient and server processes. Additionally, you will learn about creating RCP clients and servers. I hope you'll enjoyit!

### Network Programming – Building Servers and Clients

The previous chapter discussed topics related to network programming; including developing web clients, webservers, and web applications; performing DNS lookups; and timing out HTTP connections.

### The net standard Go package

The net.Dial() function is used for connecting to a network as a client, whereas the net.Listen() function is usedfor telling a Go program to accept network connections and thus act as a server. 

### A TCP client

TCP stands for Transmission Control Protocol, and its principalcharacteristic is that it is a reliable protocol. The TCP header of each packet includes source port anddestination port fields. These two fields, plus the source and destination IP addresses, are combined to identifyuniquely a single TCP connection.

The net.Dial() function is used for connecting to the remote server. The first parameter of the net.Dial() functiondefines the network that will be used, while the second parameter defines the server address, which must alsoinclude the port number. 

Certainly, you should never do that on production software.

### A slightly different version of the TCP client

The net.DialTCP() function is equivalent to net.Dial() for TCP networks.

### A TCP server

The Accept() function waits for the next connection and returns a generic Conn variable. The only thing that iswrong with this particular TCP server is that it can only serve the first TCP client, which is going to connect to itbecause the Accept() call is outside the for loop that is coming next.

### A UDP client

The biggest difference between UDP and TCP is that UDP is not reliable by design. This also means that in general, UDP is simpler than TCP because UDP does not need to keep the state of a UDP connection.

The net.ResolveUDPAddr() function returns an address of a UDP end point as defined by its second parameter.

### Developing a UDP server

On the client side, the output will be as follows:

### A concurrent TCP server

you will learn how to develop a concurrent TCP server, using goroutines. For each incomingconnection to the TCP server, the TCP server will start a new goroutine to handle that request. This allows it toaccept more requests, which means that a concurrent TCP server can serve multiple clients simultaneously.

The handleConnection() function deals with each client of the concurrent TCP server.

The concurrency of the program is implemented by the go handleConnection(c) statement, which begins a newgoroutine each time a new TCP client comes. The goroutine is executed concurrently, which gives the server theopportunity to serve even more clients!

### Remote Procedure Call (RPC)

Remote Procedure Call (RPC) is a client-server mechanism for Interprocess communication that uses TCP/IP.Both the RPC client and the RPC server to be developed will use the following package, which is named

### The RPC client

Note the use of the rpc.Dial() function for connecting to the RPC server instead of the net.Dial() function, eventhough the RCP server uses TCP.

If you try to execute RPCclient.go without having a RPC server running, you will get the following type of errormessage:

### The RPC server

What makes this program an RPC server is the use of the rpc.Register() function. However, as the RPC serveruses TCP, it still needs to make function calls to net.ResolveTCPAddr() and net.ListenTCP().

The RemoteAddr() function returns the IP address and the port number used for communicating with the RPCclient. The rpc.ServeConn() function serves the RPC client.

Executing RPCclient.go will create the following output:

### Doing low-level network programming

Network packets come in binary format, which requires you to look for specific kinds of network packets and notjust any type of network packet. Put simply, when reading network packets, you should specify the protocol or protocols that you will support in your applications in advance.In order to send a network packet, you will have to construct it on your own.

Note that lowLevel.gocaptures ICMP packets, which use the IPv4 protocol and prints their contents. Also note that working with rawnetwork data requires root privileges for security reasons.

The ICMP protocol is specified in the second part of the first parameter (ip4:icmp) of net.ListenIP(). Moreover, theip4 part tells the utility to capture IPv4 traffic only.

The ICMP protocol is used by the ping(1) and traceroute(1) utilities, so one way to create ICMP traffic is to useone of these two tools. The network traffic will be generated using the following commands on both Unixmachines while lowLevel.go is already running:

 Note that on modernLinux machines you should execute ping(1) with the -4 flag in order to tell it to use the IPv4 protocol.

### Where to go next?

The good thing is that after reading this book, you will be ready to learn on your own, which is the biggest benefitthat you can get from any good computer programming book. The main purpose of this book is to help you learnhow to program in Go and gain some experience in doing so.

 You are now ready to start writing your own software in Go and learning new things!

### Additional resources

Take a look at the following resources:

Visit the documentation of the net standard Go package, which can be found at https://golang.org/pkg/net/.This is one of the biggest documentation found in the Go documentation.

### Summary

This chapter addressed many interesting things including developing UDP and TCP clients and servers, which areapplications that work over TCP/IP computer networks

 I hopethat you found it useful and that you will continue using it as a reference throughout your Go journey.