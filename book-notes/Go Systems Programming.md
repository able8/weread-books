## Go Systems Programming
> Mihalis Tsoukalos

### Title Page

Go Systems Programming

Master Linux and Unix system level programming with Go

### Preface

Go Systems Programming is a book that will help you develop systems software using Go, which is a systems programming language that started as an internal Google project before becoming popular.

What makes Go really popular is that it keeps the developer happy by being easy to write, easy to read, easy to understand, and by having a compiler that is there to help you

### What this book covers


Chapter 4, Go Packages, Algorithms, and Data Structures, talks about algorithms and sorting in Go and about the sort.Slice() function, which requires Go version 1.8 or newer. 

### What you need for this book

This book requires a computer running a Unix variant with a relatively recent Go version, which includes any machine running Mac OS X, macOS, or Linux.

You can learn more about the various versions of macOS by visiting https://en.wikipedia.org/wiki/MacOS.

All of the Go code in this book has been tested with Go 1.8.x running on a iMac using macOS 10.12 Sierra and with Go version 1.3.3 running on a Debian Linux machine. Most of the code can run on both Go versions without any code changes. 

### Downloading the example code

The code bundle for the book is also hosted on GitHub at https://github.com/PacktPublishing/Go-Systems-Programming

### Finding the sum of the command-line arguments

 command-line arguments are stored as strings, you will also need the srtconv package for converting them into integers.

Note that most Go functions return an error variable, which should always be examined, especially on production software.


Go also has int and uint, which are the most efficient signed and unsigned integers for your current platform. Therefore, when in doubt, use either int or uint.

Chapter 3, Advanced Go Features, will talk about error handling in Go in more detail and will present a better and safer version of the previous program. 

### Getting user input

The simplest function for getting user input is called fmt.Scanln() and reads an entire line. 

if strings.Compare(arguments[i], "-i") == 0

var answer string 
         fmt.Scanln(&answer) 
         fmt.Println("You entered:", answer) 

Note that the fmt.Scanln() function uses a pointer to the answer variable. Since Go passes its variables by value, we have to use a pointer reference here in order to save the user input to the answer variable. 

### Printing output

The main difference between fmt.Print() and fmt.Println() is that the latter automatically prints a newline character each time you call it. The biggest difference between fmt.Println() and fmt.Printf() is that the latter requires a format specifier for everything it will print, just like the C printf(3) function.

you can find out more about supported verbs at https://golang.org/pkg/fmt/.

### Naming the return values of a Go function

Naming return values is a very handy Go feature that can save you from various types of bugs, so use it.

### Anonymous functions

When an anonymous function is suitable for a job, then it is extremely convenient and makes your life easier; just do not use too many anonymous functions in your programs without a good reason.

### Illustrating Go functions

The next function shows how to sort two integers without having to use a temporary variable:
func sort(x, y int) (int, int) { 
   if x > y { 
         return x, y 
   } else { 
         return y, x 
   } 
}

### The defer keyword

The defer keyword defers the execution of a function until the surrounding function returns, and is widely used in file I/O operations. This is because it saves you from having to remember when to close an open file.


The first line of output verifies that deferred functions are executed in Last In First Out (LIFO) order after the return of the surrounding function. 

The a2() function is a tricky one because due to defer, the function body is evaluated after the for loop ends while it is still referencing the local i variable, which at that time was equal to 3 for all evaluations of the body. As a result, a2() prints the number 3 three times. 

However, this is not the case with the a3() function because the current value of i is passed as an argument to the deferred function, due to the (i) code at the end of the a3() function definition. So, each time the deferred function is executed, it has a different i value to process.

### Using pointer variables in functions

Pointers are memory addresses that offer improved speed in exchange for difficult-to-debug code and nasty bugs.

As the withPointer() function uses a pointer variable, you do not need to return any values because any changes to the variable you pass to the function are automatically stored in the passed variable.

Note that you need to put & in front of the variable name to pass it as a pointer instead of as a value. 

### Go data structures

Go comes with many handy data structures that can help you store your own data, including arrays, slices, and maps. 

### Arrays

Should you wish to declare an array with two or three dimensions, you can use the following notation:
twoD := [3][3]int{{1, 2, 3}, {4, 5, 6}, {7, 8, 9}} 
threeD := [2][2][2]int{{{1, 2}, {3, 4}}, {{5, 6}, {7, 8}}}

The most common way to access all the elements of an array is by finding its size using the len() function and then using a for loop.

However, there exist cooler ways to visit all the elements of an array that involve the use of the range keyword inside a for loop and allow you to bypass the use of the len() function, which is pretty handy when you have to deal with arrays with two or more dimensions.


•  First, once you define an array, you cannot change its size, which means that Go arrays are not dynamic. Put simply, if you want to include an additional element to an existing array that has no space, you will need to create a bigger array and copy all the elements from the old array to the new one.

•  Second, when you pass an array to a function, you actually pass a copy of the array, which means that any changes you make to an array inside a function will be lost after the function finishes.


•  Last, passing a large array to a function can be pretty slow, mostly because Go has to create a second copy of the array. The solution to all these problems is to use slices instead.


### Slices

A slice has many similarities with an array, and it allows you to overcome the shortcomings of an array.


As slices are dynamic in size, if a slice runs out of room, Go automatically doubles its current length to make room for more elements.


As slices are passed by reference to functions, any modifications you make to a slice inside a function will not be lost after the function ends. Additionally, passing a big slice to a function is significantly faster than passing the same array because Go will not have to make a copy of the slice; it will just pass the memory address of the slice variable.

The code of this subsection is saved in slices.go, and it can be separated into three main parts.

func printSlice(x []int) { 
   for _, number := range x {

 
         fmt.Printf("%!d(MISSING) ", number) 
   } 
   fmt.Println() 
}

Note that when you use range over a slice, you get a pair of values in its iteration. The first one is the index number and the second one is the value of the element.

The change() function just changes the fourth element of the input slice, whereas printSlice() is a utility function that prints the contents of its slice input variable. 

fmt.Printf("Before. Cap: %!d(MISSING), length: %!d(MISSING)\n", cap(aSlice), len(aSlice)) 
   aSlice = append(aSlice, -100) 
   fmt.Printf("After. Cap: %!d(MISSING), length: %!d(MISSING)\n", cap(aSlice), len(aSlice)) 
   printSlice(aSlice) 

As you can see from the third line of the output, the capacity and the length of a slice were the same at the time of its definition. However, after adding a new element to the slice using append(), its length goes from 6 to 7 but its capacity doubles and goes from 6 to 12. The main advantage you get from doubling the capacity of a slice is better performance because Go will not have to allocate memory space all the time.


### Maps

However, the single most important operation you can perform on an existing map is illustrated in the following Go code:
   _, ok := aMap["Tuesday"] 
   if ok { 
         fmt.Printf("The Tuesday key exists!\n") 

This is the proper and safe way of trying to get the value of a map key because asking for a value for which there is no key will result in returning zero. This gives you no way of determining whether the result was zero because the key you requested was not there or because the element with the corresponding key actually had the zero value.

### Converting an array into a map

The second part, which implements the core functionality, is as follows:

The for loop is used for visiting all the array elements and adding them to map. The strconv.Itoa() function converts the index number of array into a string.

### Structures

When you need to group various types of variables and create a new handy type, you can use a structure--the various elements of a structure are called fields.



   p1 := message{23, 12, "A Message"} 
   p2 := message{} 
   p2.Label = "Message 2" 
 

how to print all the fields of a structure without knowing their names using a for loop and the Type() function:


This happens because lowercase fields do not get exported; therefore, they cannot be used by the reflect.Value.Interface() method. You will learn more about reflection in the next chapter.


### Interfaces

 will talk about methods, which are functions with a special receiver argument. 

This particular parameter connects the function to the type of that extra parameter. As a result, that parameter is called the receiver of the method. 

### Creating random numbers

The seed is used for initializing the entire process and is extremely important because if you always start with the same seed, you will always get the same sequence of random numbers.

rand.Seed(time.Now().Unix()) 

Lastly, random.go does not examine the error variable returned by strconv.Atoi() to save book space, not because it is not necessary.


### Summary

do not get discouraged if you fail in some of them.

The next chapter will talk about many advanced Go features, including error handling, pattern matching, regular expressions, reflection, unsafe code, calling C code from Go, and the strace(1) command-line utility. I will compare Go with other programming languages and give you practical advice in order to avoid some common Go pitfalls.

### Advanced Go Features

starting with error handling and ending with how to avoid some common Go mistakes. If you are familiar with Go, you can skip what you already know

•  How to use the strace(1) and dtrace(1) tools to watch the system calls of Go executable files

•  How to avoid various common Go mistakes


### Error handling in Go

Error handling in Go
Errors happen all the time, so it is our job to both catch and handle them, especially when writing code that deals with sensitive system information and files.

if an error variable has a nil value, then there is no error situation.


However, this is not considered good practice and should be avoided, especially on systems software and other kinds of critical software, such as server processes.

even End of File (EOF) is a type of error that is returned when there is nothing left to read from a file. As EOF is defined in the io package, you can handle it as follows:
if err == io.EOF {

### Functions can return error variables

The relevant Go code can be found in funErr.go and will be presented in three parts.
The first part contains the following Go code:

if y == 0 { 
         return 0, nil, errors.New("Cannot divide by zero!") 
   } 

The errors.New() function from the errors Go package that you see in funErr.go creates a new error variable, using the provided string as the error message.


It is a very common Go practice to compare an error variable with nil to quickly find out whether there is an error condition or not.


As the name log.Fatal() implies, this logging function should be used for critical errors only because when called, it automatically terminates your program. 

exit status 1
The last line is automatically generated by the log.Fatal() function, just before terminating the program. It is important to understand that any Go code after the call to log.Fatal() will not be executed.


### About error logging

Generally speaking, log.Fatal() should be used instead of the os.Exit() function because it allows you to print an error message and exit your program using just one function call.

Please note that logging functions can be handy for debugging purposes so do not underestimate their power.

As you can see, logging.go does not need the fmt package because it has its own functions for printing the output. 

Additionally, the log.Panicf() function works in a similar way to log.Fatal()--they both terminate the current program. However, log.Panicf() prints some additional information, useful for debugging purposes.

### The addCLA.go program revisited

So, the new and improved version works as expected, behaves reliably, and allows us to differentiate between valid and invalid input.

### Pattern matching and regular expressions

The regexp.Compile() function reads the provided regular expression and tries to parse it. If the parsing of the regular expressing is successful, then regexp.Compile() returns a value of the regexp.Regexp variable type that you can use afterward. 

### Printing all the values from a given column of a line

var s [3]string 
   s[0] = "1 2 3" 
   s[1] = "11 12 13 14 15 16" 
   s[2] = "-1 2 -3 -4 -5 6"
Here, you import the required Go packages and define a string with three lines using an array with three elements.

All of the hard work is done by the handy strings.Fields() function that splits a string based on whitespace characters, as defined in unicode.IsSpace(), and returns a slice of strings. A

An important thing to remember is that you should never trust your data. Put simply, always verify that the data you expect to grab is there.

### Creating summaries

As you can see, most of the Go code in summary.go is about dealing with exceptions and potential errors.

### Finding the number of occurrences

               _, ok := counts[word] 
               if ok { 
                     counts[word] = counts[word] + 1 

Here, we use the knowledge from the previous chapter to create a map named counts and populate it with the desired data using two for loops.


The last part is pretty small as it just prints the contents of the counts map:
   for key, _ := range counts {

 
         fmt.Printf("%!s(MISSING) -> %!d(MISSING) \n", key, counts[key]) 
   } 
}

### Find and replace

search the provided text for two variations of a given string and replace it with another string.

parse, err := regexp.Compile("[bB]")

However, regexp.MustCompile() does not return an error variable; it just panics if the given expression is erroneous and cannot be parsed. As a result, regexp.Compile() is a better choice.

Here you replace each match with an uppercase C using parse.ReplaceAllString().

### Reflection

Here, you find the type of the x1, x2, and x3 variables using reflect.ValueOf() and Type().


### Calling C code from Go

Go allows you to call C code because there are times when the only way to perform some tasks, such as communicating with a hardware device or a database server, is by using C. 

### Unsafe code

You will most likely never need to use unsafe code in your Go programs but if for some strange reason you ever need to, it will probably have to do with pointers.


If you are not completely sure that you need it, then do not use it.

### Comparing Go to other programming languages

However, it has some critical drawbacks, including the fact that C pointers, which are great and fast, can lead to difficult-to-detect bugs and memory leaks. Additionally, C does not offer garbage collection; back when C was created, garbage collection was a luxury that had the ability slow down computers. 

However, neither C nor C++ have good support for concurrent programming.


 Currently, the syntax of Rust is changing too fast, but this will end in the near feature. If for some reason you do not like Go, you should try Rust.

If you ask my opinion, I think that Go is a modern, portable, mature, and safe programming language for writing systems software. You should try Go before looking for any alternatives. 

Learning the basics of systems programming is more important than the programming language you select.

Still, delivering systems software in a scripting language is rarely accepted, unless there is a really good reason to do so.

### Analysing software

So, this section will talk about strace(1) and dtrace(1) , which allow you to see what is going on behind the scenes when you execute a program on a Unix machine. 

### Using the strace(1) command-line utility

The strace(1) command-line utility allows you to trace system calls and signals. As strace(1) is not available on Mac machines, this section will use a Linux machine to showcase strace(1). However, as you will see in a later, macOS machines have the dtrace(1) command-line utility that can do many more things.


strace ./addCLAImproved 1 a b 2>&1 | grep write

So, the reason for needing to put 2>&1 at the end of the command is to redirect all of the output, from standard error (file descriptor 2) to standard output (file descriptor 1), in order to be able to search it using the grep(1) command, which searches standard output only.

### The DTrace utility

can trace system calls produced by a process

It also allows you to work on production systems and watch running programs or server processes dynamically without introducing a big overhead.

### Disabling System Integrity Protection on macOS

There is a big chance that you will have trouble running dtrace(1) and dtruss(1) on your Mac OS X machine the first time you try them and get the following error message:

### Unreachable code

As the Go compiler itself cannot catch such logical errors, you will need to use the go tool vet command to help.


let the go tool vet tool discover the unreachable code. The process is simple and includes the execution of the following command:
$ go tool vet cannotReach.go
cannotReach.go:9: unreachable code

### Avoiding common Go mistakes

Avoiding common Go mistakes
This section will briefly talk about some common Go mistakes so that you can avoid them in your programs:

•  If you have an error in a Go function, either log it or return it; do not do both unless you have a really good reason to do so.
•  Go interfaces define behaviors, not data and data structures.

•  Use the io.Reader and io.Writer interfaces because they make your code more extensible.

•  Make sure that you pass a pointer to a variable to a function only when needed. The rest of the time, just pass the value of the variable.

•  Error variables are not strings; they are error values.


•  If you are afraid of making mistakes, you will most likely end up doing nothing useful. So experiment as much as you can.


•  If you do not really know a Go feature, test it before using it for the first time, especially if you are developing a systems utility
•  Do not test systems software on production machines


•  When you deploy your systems software on a production machine, do it when the production machine is not busy and make sure that you have a backup plan

### Go Packages, Algorithms, and Data Structures

If you combine all of these, you will end up with a complete program because Go programs come in packages that contain algorithms that deal with data structures. 

### About algorithms

This was extremely important in the early Unix days because the total amount of RAM on a Unix machine then was about 64K or even less!


### Sorting algorithms

if you want to have full control over sorting, you should write your own implementation.

### The sort.Slice() function

The second part contains the definition of a slice, which has four elements:

### Linked lists in Go

A linked list is a structure with a finite set of elements where each element uses at least two memory locations: one for storing the data and the other for a pointer that links the current element to the next one in the sequence of elements that make the linked list.

The first thing you should do when defining a linked list is to keep the head of the list in a separate variable because the head is the only thing that you need to access the entire linked list.

Note that if you lose the pointer to the first node of a single linked list, there is no possible way to find it again.

When creating your own data structures, the single most important element is the definition of the node, which is usually implemented using a structure.

type Node struct { 
   Value int 
   Next  *Node 
} 

In order to avoid duplicate entries, you should check whether a value already exists in the list or not. 

for t != nil {

 
         fmt.Printf("%!d(MISSING) -> ", t.Value) 
         t = t.Next 
   } 

This happens because integer values in Go are initialized as 0; so new(Node) is in fact {0 <nil>}, which makes it impossible to tell whether the head of the list is nil or not without passing an extra variable to each function that manipulates the linked list.

### Trees in Go

The most commonly used type of tree is called a binary tree because each node can have up to two children. 

type Tree struct { 
   Left  *Tree 
   Value int 
   Right *Tree 
}

The second part contains functions that allow you to traverse a tree in order to print all of its elements, create a tree with randomly generated numbers, and insert a node into it:

### About Go packages

Packages are for grouping related functions and constants so that you can transfer them easily and use them in your own Go programs.

There exist many useful Go packages that come with each Go distribution including the following:

The http package: This is a part of the net package and offers HTTP server and client implementations

•  The io package: This deals with primitive input and output operations
•  The os package: This gives you a portable interface to the operating system functionality

I strongly advise you to look into all the packages that come with Go before you start developing your own functions and packages because there is a realistic chance that the functionality you are looking for is already available in a standard Go package.

### Creating your own packages

Packages make the design, implementation, and maintenance of large software systems easier and simpler. 

You should perform all these actions for every Go package you create, apart from the first two mkdir commands, which should only be executed once.

### Private variables and functions

Go follows a simple rule which states that functions, variables, types, and so on that begin with an uppercase letter are public

### The init() function

The init() function
Every Go package can have a function named init() that is automatically executed at the beginning of the execution.

However, there are times when you want to perform important initializations before you start using a package such as opening database and network connections: in these relatively rare cases the init() function is invaluable.

### Using your own Go packages

the compilation process will fail with an error message similar to the following:

f you try to call a private function or use a private variable from anotherPackage, your Go program privateFail.go will fail to run with the following error message:

When I was learning Go, it took me about 3 hours of debugging until I found that the reason for an error message I could not explain was the name of a variable!

Additionally, you will see that the init() function actually gets executed automatically:

### Using external Go packages

In order to compile useMySQL.go, you should first perform the following steps:
$ go get github.com/go-sql-driver/mysql

/Users/mtsouk/go/pkg/darwin_amd64/github.com/go-sql-driver:
total 728
-rw-r--r--  1 mtsouk  staff  372694 Apr  6 21:32 mysql.a

### The go clean command

Go allows you to clean up the files of a package in order to recreate it later. The following command cleans up a package without affecting the code of the package:
$ go clean -x -i aSimplePackage
cd /Users/mtsouk/go/src/aSimplePackage
rm -f aSimplePackage.test aSimplePackage.test.exe
rm -f /Users/mtsouk/go/pkg/darwin_amd64/aSimplePackage.a

### Garbage collection

Garbage collection
In this section, we will briefly talk about how Go deals with GC, which tries to free unused memory efficiently. 

runtime.ReadMemStats(&mem) 
   fmt.Println("mem.Alloc:", mem.Alloc) 
   fmt.Println("mem.TotalAlloc:", mem.TotalAlloc) 
   fmt.Println("mem.HeapAlloc:", mem.HeapAlloc) 
   fmt.Println("mem.NumGC:", mem.NumGC) 

Every time you want to read the latest memory statistics, you should make a call to the runtime.ReadMemStats() function.

So, the output presents information about properties related to the memory used by the garbageCol.go program. If you want to get an even more detailed output, you can execute garbageCol.go, as shown here:
$ GODEBUG=gctrace=1 go run garbageCol.go

### Your environment

Your environment
In this section, we will show how to find out things about your environment using the runtime package: this can be useful when you have to take certain actions depending on the OS and the Go version you are using.

fmt.Print("You are using ", runtime.Compiler, " ") 
   fmt.Println("on a", runtime.GOARCH, "machine") 
   fmt.Println("with Go version", runtime.Version()) 
   fmt.Println("Number of Goroutines:", runtime.NumGoroutine())

### Go gets updated frequently!

So, I decided to include this information in this book in order to give a better sense of how often Go gets updated:

### Exercises

1.  Visit the documentation of the runtime package.


### Summary

In this chapter, you learned many things related to algorithms and data structures. You also learned how to use existing Go packages and how to develop your own Go packages. 

talk about how to work with files and directories in Go, how to painlessly traverse directory structures, and how to process command-line arguments using the flag package. But more importantly, we will start developing Go versions of various Unix command-line utilities.

### Files and Directories

•  Processing command-line arguments and options easily using the flag package
•  Developing a version of the which(1) command-line utility in Go
•  Developing a version of the pwd(1) command-line utility in Go


### Useful Go packages

If you consider files as boxes with contents, the os package allows you to move them, put them into the wastebasket, change their names, visit them, and decide which ones you want to use, whereas the io package, which will be presented in the next chapter, allows you to manipulate the contents of a box without worrying too much about the box itself!


The flag package, which you will see in a while, lets you define and process your own flags and manipulate the command-line arguments of a Go program.

The filepath package is extremely handy as it includes the filepath.Walk() function that allows you to traverse entire directory structures in an easy way.

### The flag package

The flag package does the dirty work of parsing command-line arguments and options for us; so, there is no need for writing complicated and perplexing Go code.

you do not have to perform any data type conversions.

for index, val := range flag.Args() { 
         fmt.Println(index, ":", val) 
   } 

or the provided value is of the wrong type, you will get the following messages and the program will terminate:

### Dealing with directories

As a result, a directory contains an entry for itself, its parent directory, and each of its children

What you should remember is that an inode holds metadata about a file, not the actual data of a file.

### About symbolic links

Nothing special is happening here: you just need to make sure that you get one command-line argument in order to have something to test. 

### Implementing the pwd(1) command

fileinfo, err := os.Lstat(pwd) 
   if fileinfo.Mode()&os.ModeSymlink != 0 { 
         realpath, err := filepath.EvalSymlinks(pwd) 
         if err == nil { 
               fmt.Println(realpath) 
         } 

On macOS machines, the /tmp directory is a symbolic link, which can help us verify that pwd.go works as expected:

### Developing the which(1) utility in Go

In order to check the return value of a Unix command-line utility in the shell, you should do the following:
$ which -s ls
$ echo $?

The strings package is needed in order to split the contents of the PATH variable after you read it. 


   flag.Parse() 
   flags := flag.Args() 
   if len(flags) == 0 { 
         fmt.Println("Please provide an argument!") 
         os.Exit(1) 
   } 
   file := flags[0] 

for _, directory := range pathSlice { 
         fullPath := directory + "/" + file

Here, the call to os.Stat() tells whether the file we are looking for actually exists or not. 

As you can see, there are lots of tests involved in which.go, which is not rare for systems software. Nevertheless, you should always examine all possibilities in order to avoid surprises later. 

### Printing the permission bits of a file or directory

fmt.Println("Error:", err) 
         os.Exit(1) 

The Go code that does the actual job is mainly the call to the os.Stat() function, which returns a FileInfo structure that describes the file or directory examined by os.Stat(). From the FileInfo structure, you can discover the permissions of a file by calling the Mode() function.

### Deleting a file

arguments := os.Args 
   if len(arguments) == 1 { 
         fmt.Println("Please provide an argument!") 
         os.Exit(1) 
   } 

If rm.go is executed without any problems, it will create no output according to the Unix philosophy. 

### Renaming and moving files

mode := fileInfo.Mode() 
         if mode.IsRegular() == false { 
               fmt.Println("Sorry, we only support regular files as source!") 
               os.Exit(1) 
         } 

This part makes sure the source file exists, is a regular file, and is not a directory or something else like a network socket or a pipe.

If rename.go is executed correctly, it will create no output. However, if there are problems, rename.go will generate some output:

### Traversing a directory tree

All the dirty jobs here are automatically done by the filepath.Walk() function with the help of the walkFunction() function that was defined previously.

The filepath.Walk() function takes two parameters: the path of a directory and the walk function it will use.


### Visiting directories only!


   mode := fileInfo.Mode() 
   if mode.IsDir() { 
         fmt.Println(path) 
   } 

### Adding some command-line options

•  -d: This is for printing directories
•  -f: This is for printing files

### Using regular expressions

The previous output can be verified by using the following command:

### File Input and Output

The main purpose of this chapter is to teach how the Go standard library permits us to open files, read their contents, process them if we like, create new files, and put the desired data into them.

There are two main ways to read and write files: using the io package and using the functions of the bufio package. However, both packages work in a comparative way.

•  Using the bufio package for buffered input and output


Converting tabs into space characters and vice versa


Working with System Files, to learn more about putting data at the end of a file without destroying its existing data.

### Byte slices

Byte slices are a kind of slices used for file reading and writing. Putting it simply, they are slices of bytes used as a buffer during file reading and writing operations. 

aByteSlice := []byte("Mihalis Tsoukalos!\n") 
   ioutil.WriteFile(filename, aByteSlice, 0644)

Here, you define another byte slice named anotherByteSlice with 100 places that will be used for reading from the file you created previously. Note that %!s(MISSING) used in fmt.Printf() forces anotherByteSlice to be printed as a string: using Println() would have produced a totally different output.

### About binary files

  "bytes" 
   "encoding/binary" 

buf := new(bytes.Buffer) 
   err := binary.Write(buf, binary.LittleEndian, aNumber) 
   if err != nil { 
         fmt.Println("Little Endian:", err) 
   } 
 
   fmt.Printf("%!d(MISSING) is %!x(MISSING) in Little Endian\n", aNumber, buf) 
   buf.Reset() 

### Useful I/O packages in Go

The io package is for performing primitive file I/O operations, whereas the bufio package is for executing buffered I/O.
In buffered I/O, the operating system uses an intermediate buffer during file read and write operations in order to reduce the number of filesystem calls. As a result, buffered input and output is faster and more efficient.


### The io package

The io package offers functions that allow you to write to or read from files. 

The io.ReadFull() function here reads from the reader of an open file and puts the data into a byte slice that has 8 places. You can also see here the use of the io.WriteString() function for printing data to a standard output (os.Stdout) that is also a file. However, this is not a very common practice as you can simply use fmt.Println() instead.

### The bufio package

One of the handy features of the bufio package is that it allows you to read a text file line by line, word by word, and character by character without too much effort.

scanner := bufio.NewScanner(f)
The default behavior of bufio.NewScanner is to read its input line by line, which means that each time you call the Scan() method that reads the next token, a new line will be returned.

   for scanner.Scan() { 
         line := scanner.Text() 
 
         if scanner.Err() != nil { 
               fmt.Printf("error reading file %!s(MISSING)", err) 
               os.Exit(1) 
         } 
         fmt.Println(line) 
   } 

### Writing to files using fmt.Fprintf()

The use of the fmt.Fprintf() function allows you to write formatted text to files in a way that is similar to the way the fmt.Printf() function works. Note that fmt.Fprintf() can write to any io.Writer interface 

Note that the os.Create() function will truncate the file if it already exists.

In other words, you can create plain text files using fmt.Fprintf().

### About io.Writer and io.Reader

func countChars(r io.Reader) int { 

n, err := r.Read(buf) 
         if err != nil && err != io.EOF { 
               return 0 
         } 
         if err == io.EOF { 
               break 
         } 

func writeNumberOfChars(w io.Writer, x int) { 
   fmt.Fprintf(w, "%!d(MISSING)\n", x) 
}

### Finding out the third column of a line

      r := bufio.NewReader(f)

 
         for { 
               line, err := r.ReadString('\n') 

However, if a file fails to open for some reason, the program will not stop its execution, but it will continue processing the rest of the files if they exist. However, the program expects that its input files end in a newline and you might see strange results if an input file ends differently.


### There is more than one way to copy a file!

but file copying is the most characteristic example of this rule because you can copy a file by reading it line by line, byte by byte, or all at once! However, this rule does not apply to the way Go likes to format its code!


### Using io.Copy

   if !sourceFileStat.Mode().IsRegular() { 
         return 0, fmt.Errorf("%!s(MISSING) is not a regular file", src) 
   } 
 
   source, err := os.Open(src) 
   if err != nil { 
         return 0, err 
   } 
   defer source.Close() 

nBytes, err := io.Copy(destination, source) 
   return nBytes, err 

### Reading a file all at once!

input, err := ioutil.ReadFile(sourceFile) 
   if err != nil { 
         fmt.Println(err) 
         os.Exit(1) 
   } 

err = ioutil.WriteFile(destinationFile, input, 0644) 

Note that the ioutil.ReadFile() function reads the entire file, which might not be efficient when you want to copy huge files. Similarly, the ioutil.WriteFile() function writes all the given data to a file that is identified by its first argument.

### An even better file copy program

buf := make([]byte, BUFFERSIZE) 

you just keep calling source, Read() until you reach the end of the input file. Each time you read something, you call destination. Write() to save it to the output file. The buf[:n] notation allows you to read the first n characters from the buf slice.


BUFFERSIZE, _ = strconv.ParseInt(os.Args[3], 10, 64)

The filepath.Base() is used for getting the name of the executable file.


If there is a problem with the copy operation, you will get a descriptive error message.

### Benchmarking file copying operations

The size of the buffer you use in file operations is really important and affects the performance of your system tools, especially when you are dealing with very big files.

time ./notGoodCP 100MB copy

you can consider readAll.go as a quick and dirty solution!


### Counting words

r := regexp.MustCompile("[^\\s]+") 
for range r.FindAllString(line, -1) { 
numberOfWords++ 
}
Here, the provided regular expression separates the words of a line based on whitespace characters in order to count them afterwards!

### The wc.go code!

numberOfLines++ 
         r := regexp.MustCompile("[^\\s]+") 
         for range r.FindAllString(line, -1) { 
               numberOfWords++ 
         } 
         numberOfCharacters += len(line) 

Counting lines is easy because each time the bufio reader reads a new line,

The ReadString() function tells the program to read until the first occurrence of '\n' in the input: multiple calls to ReadString() mean that you are reading a file line by line.

The for loop terminates when you get the io.EOF error message, which signifies that there is nothing left to read from the input file.


   for _, filename := range flag.Args() {
The last for statement is for processing all the input files given to the program.

   if (len(flags) != 1) && printAll 

using Go source files as command-line arguments to the go run wc.go command will fail. This will happen because the compiler will try to compile the Go source files instead of treating them as command-line arguments to the go run wc.go command. The following output proves this:


### Reading a text file character by character

Here, ScanRunes is a split function that returns each character (rune) as a token. Then, the call to Scan() allows us to process each character one by one. There also exist ScanWords and ScanLines for getting words and lines, respectively.

### Doing some file editing!

if convertTabs == true { 
               newLine = strings.Replace(line, "\t", "    ", -1) 
         } else if convertSpaces == true { 
               newLine = strings.Replace(line, "    ", "\t", -1) 
         } 

### Sparse files in Go

The strconv.ParseInt() function is used for converting the command-line argument that defines the size of the sparse file from its string value to its integer value. Additionally, the os.Stat() call makes sure that you will not accidentally overwrite an existing file.


Then, you call fd.Seek() in order to make the file bigger without adding actual data

### Reading and writing data records

The Go code of records.go will save data in the CSV format

reader := csv.NewReader(f) 
   reader.FieldsPerRecord = -1 
   allRecords, err := reader.ReadAll() 

### File locking in Go

Various techniques exist for file locking. One of them is by creating an additional file that signifies that another program or process is using a given resource. The presented technique is more suitable for programs that use multiple go routines.

The locking of the file is done using the mu.Lock() statement and the unlocking of the file with the mu.Unlock() statement.

var w *sync.WaitGroup = new(sync.WaitGroup) 
   w.Add(number) 
 
   for r := 0; r < number; r++ { 
         go writeDataToFile(r, file, w) 
   } 
 
   w.Wait() 

### A simplified Go version of the dd utility

one for specifying the block size in bytes (-bs) and the other for specifying the total number of blocks that will be written (-count). Multiplying these two values will give you the size of the generated file in bytes.

intByte := byte(random(0, 9)) 
         *buf = append(*buf, intByte) 

buf = nil 

The reason for emptying the buf byte slice each time you want to call createBytes() is that you do not want the buf byte slice to get bigger and bigger each time you call the createBytes() function.

It took me a while and a couple of fmt.Println(buf) statements to find out the reason for this unforeseen behavior!


### Summary

 Additionally, you will learn how to read and change the Unix file permissions as well as how to find the owner and the group of a file. Also, we will talk about log files and how you can use pattern matching to acquire the information you want from log files.

### Working with System Files

you will also learn many other things, including pattern matching, file permissions, working with users and groups, and dealing with dates and times in Go. 

•  Sending information to Unix log files
•  Working with dates and times in Go
•  Working with Unix file permissions
•  Working with user IDs and group IDs
•  Learning more information about files and directories

### Which files are considered system files?

Usually, most system log files can be found inside /var/log. 

depending on their configuration.


### Logging in Go

The log package provides a general way to log information on your Unix machine, whereas the log/syslog Go package allows you to send information to the system logging service using the logging level and the logging facility you want. 

### Putting data at the end of a file

   f, err := os.OpenFile(filename, 
os.O_RDWR|os.O_APPEND|os.O_CREATE, 0660)
The desired task is done by the os.O_APPEND flag of the os.OpenFile() function that tells Go to write at the end of the file. Additionally, the os.O_CREATE flag will make os.OpenFile() to create the file if it does not exist, which is pretty handy because it saves you from having to write Go code that tests whether the file is already there or not.


The fmt.Fprintf() function is used here in order to write the message to the file as plain text. As you can see, appendData.go is a relatively small Go program that does not contain any surprises.


### Altering existing data

reads its input file all at once using ioutil.ReadFile(), which might not be so efficient when processing huge text files. However, with today's computers, this should not be a problem.

utput := strings.Join(lines, "\n") 
         err = ioutil.WriteFile(filename, []byte(output), 0644) 

As the range loop will introduce an extra line at the end of the file, you have to delete the last line in the lines slice using the lines[len(lines)-1] = "" statement, which means that the program assumes that all the files it processes end with a new line.

### About log files

log files are necessary for server processes because there is no other way for a server process to send information to the outside world, as it has no Terminal to send any output.


Generally speaking, using a log file is better than displaying the output on the screen for two reasons: first, the output does not get lost, as it is stored on a file, and second, you can search and process log files using Unix tools

### About logging

All Unix machines have a separate server process for logging log files. On macOS machines, the name of the process is syslogd(8). On the other hand, most Linux machines use rsyslogd(8), which is an improved and more reliable version of syslogd(8), which was the original Unix system utility for message logging.

### The syslog Go package

You have to use the log package for logging and the log/syslog package for defining the logging facility and the logging level of your program.

sysLog, e := syslog.New(syslog.LOG_INFO|syslog.LOG_LOCAL7, programName) 

The syslog.New() function call, which returns a writer, tells your program where to direct all log messages. 

/var/log/system.log

$ tail -5 /var/log/syslog

### Processing log files

   flag.Parse() 
   flags := flag.Args() 

_, ok := myIPs[ip] 
               if ok { 
                     myIPs[ip] = myIPs[ip] + 1 
               } else { 
                     myIPs[ip] = 1 
               } 

### A simple pattern matching example

func findIP(input string) string { 
   partIP := "(25[0-5]|2[0-4][0-9]|1[0-9][0-9]|[1-9]?[0-9])" 
   grammar := partIP + "\\." + partIP + "\\." + partIP + "\\." + partIP 
   matchMe := regexp.MustCompile(grammar) 
   return matchMe.FindString(input) 
}

### Playing with dates and times

 t := time.Now() 
   fmt.Println(t, t.Format(time.RFC3339)) 
   fmt.Println(t.Weekday(), t.Day(), t.Month(), t.Year()) 
   time.Sleep(time.Second) 

### Processes and Signals

The central subject of this chapter is developing Go applications that can handle the Unix signals that can be caught and handled. Go offers the os/signal package for dealing with signals, which uses Go channels. Although channels are fully explored in the next chapter, this will not stop you from learning how to work with Unix signals in Go programs.


Signal handling in Go

•  Converting a big program into two smaller ones that will cooperate with the help of Unix pipes

### About Unix signals

Have you ever pressed Ctrl + C in order to stop a program from running? If yes, then you are already familiar with signals because Ctrl + C sends the SIGINT signal to the program.

The SIGKILL and SIGSTOP signals cannot be caught, blocked, or ignored. The reason for this is that they provide the kernel and the root user a way of stopping any process. The SIGKILL signal, which is also known by the number 9, is usually called in extreme conditions where you need to act fast; 

The most important thing to remember here is that not all Unix signals can be handled!

### The kill(1) command

Keep in mind that the fact that you can send a signal to a process does not mean that the process can or has code to handle this signal.

If you want to find out all the supported signals of your Unix machine, you should execute the kill -l command.

-bash: kill: (2908) - Operation not permitted

### A simple signal handler in Go

func handleSignal(signal os.Signal) { 
   fmt.Println("Got", signal) 
}

2.  Calling signal.Notify() in order to define the list of signals you want to be able to catch.

The bad thing here is that h1s.go will stop when it receives the SIGHUP signal because the default action for SIGHUP when it is not being specifically handled by a program is to kill the process! 

show how to handle three signals in a better way

### Handling three different signals!

..* Got: interrupt
* Got: terminated
.Got: hangup
.Killed: 9

If you want to improve h2s.go, you can make it call os.Getpid() in order to print its process ID, which will save you from having to find it on your own.

### Catching every signal that can be handled

In this case, all the difference is made by the way you call signal.Notify() in your code. As you do not define any particular signals, the program will be able to handle any signal that can be handled. 

### Unix pipes in Go

As a result, pipes can be used for streaming data between two processes without creating any temporary files.

### Reading from standard input

var f *os.File 
   arguments := os.Args 
   if len(arguments) == 1 { 
         f = os.Stdin 
   } else { 
         filename = arguments[1] 

If you do not have a file to process, you will try to read data from os.Stdin. 

   scanner := bufio.NewScanner(f) 
   for scanner.Scan() { 
         fmt.Println(">", scanner.Text()) 
   } 

### Sending data to standard output

io.WriteString(os.Stdout, myString) 
   io.WriteString(os.Stdout, "\n") 

The only subtle thing is that you need to put your text into a slice before using io.WriteString() to write data to os.Stdout.

### Implementing cat(1) in Go

scanner := bufio.NewScanner(f) 
   for scanner.Scan() { 
         fmt.Println(scanner.Text()) 
   } 

### The plotIP.go utility revisited

Personally, I prefer to have two separate utilities and combine them when needed than having just one utility that does two or more tasks.

### Unix sockets in Go

So, this section will just present the Go code of a Unix socket client, which is a program that uses a Unix socket, which is a special Unix file, to read and write data. 

func readSocket(r io.Reader) { 
   buf := make([]byte, 1024) 
   for { 
         n, _ := r.Read(buf[:]) 
         fmt.Print("Read: ", string(buf[0:n])) 
   } 
}

   c, _ := net.Dial("unix", "/tmp/aSocket.sock") 

### Programming a Unix shell in Go

scanner := bufio.NewScanner(os.Stdin) 
   fmt.Print("> ") 
   for scanner.Scan() { 
 
         line := scanner.Text() 
         words := strings.Split(line, " ") 
         command := words[0]

 go run UNIXshell.go
> version
0.2
> ls -l
ls -l
> exit
Exiting...
Should you wish to learn more about creating your own Unix shell in Go, you can visit https://github.com/elves/elvish.

### Concurrency and parallelism

So, the developer should not think about parallelism, but about breaking things into independent components that solve the initial problem when combined.

### A simple example

As you can see, goroutines that run this way are totally isolated from each other and cannot exchange any kind of data, which is not always the operational style that is desired.

### Creating multiple goroutines

you cannot be sure about the order the goroutines will get executed.

### Waiting for goroutines to finish their jobs


   var waitGroup sync.WaitGroup 
   waitGroup.Add(10)
Here, you create a new variable with a type of sync.WaitGroup, which waits for a group of goroutines to finish. The number of goroutines that belong to that group is defined by one or multiple calls to the sync.Add() function.
Calling sync.Add() before the Go statement in order to prevent race conditions is important.

         go func(x int64) { 
               defer waitGroup.Done() 
               fmt.Printf("%!d(MISSING) ", x) 
         }(i) 

The good thing here is that there is no need to call time.Sleep() because sync.Wait() does the necessary waiting for us.

### About channels

A channel, putting it simply, is a communication mechanism that allows goroutines to exchange data. However, some rules exist here. First, each channel allows the exchange of a particular data type, which is also called the element type of the channel, and second, for a channel to operate properly, you will need to use some Go code to receive what is sent via the channel.

### Writing to a channel

The reason for this is pretty simple: the c <- x statement is blocking the execution of the rest of the writeChannel() function because nobody is reading from the c channel.


### Reading from a channel

fmt.Println("Read:", <-c) 

Here, the <-c statement in the fmt.Println() function is used for reading a single value from the channel: the same statement can be used for storing the value of a channel into a variable. However, if you do not store the value you read from a channel, it will be lost.


### Pipelines

Go programs rarely use a single channel. One very common technique that uses multiple channels is called a pipeline. So, a pipeline is a method for connecting goroutines so that the output of a goroutine becomes the input of another with the help of channels.

•  Additionally, you are using less variables and therefore less memory space because you do not have to save everything

func findSquares(out chan<- int64, in <-chan int64) { 
   for x := range in { 
         out <- x * x 
   } 
   close(out) 
}
This time, the function takes two arguments that are both channels. However, out is an output channel, whereas in is an input channel used for reading data.

### A better version of wc.go

As you can see, each input file is being processed by a different goroutine. As expected, you cannot make any assumptions about the order the input files will be processed.


### Calculating totals

The main reason that the current version of dWC.go cannot calculate totals is that its goroutines have no way of communicating with each other. This can be easily solved with the help of channels and pipelines. 

### Summary

we will talk about more advanced features related to goroutines and channels including shared memory, buffered channels, the select keyword, the GOMAXPROCS environment variable, and signal channels.

### Goroutines - Advanced Features

Thus, you will learn how to use various types of channels, including buffered channels, signal channels, nil channels, and channels of channels! Additionally, you will learn how you can utilize shared memory and mutexes with goroutines as well as how to time out a program when it is taking too long to finish.


•  Timing out a program and avoiding waiting forever for it to end
•  Shared memory and goroutines
•  Using sync.Mutex in order to guard shared data


### The Go scheduler

The Go runtime has its own scheduler that is responsible for the execution of the goroutines using a technique known as m:n scheduling, where m goroutines are executed using n operating system threads using multiplexing. 

### The select keyword

A select statement in Go is like a switch statement for channels and allows a goroutine to wait on multiple communication operations.

Note that a message sent to a closed channel panics, whereas a message received from a closed channel returns the zero value immediately.


Note that once you close a channel, you cannot write to this channel. However, you can still read from that channel!

### Signal channels

The A() function is blocked by the channel defined in the a parameter. This means that until this channel is closed, the A() function cannot continue its execution. 

Defining a signal channel as an empty struct with no fields is a very common practice because empty structures take no memory space. In such a case, you could have used a bool channel instead.

### Buffered channels

Here, you create a new channel named numbers with 5 places, which is denoted by the last parameter of the make statement. This means that you can write five integers to that channel without having to read any one of them in order to make space for the others. 

This happens because when there is nothing left to read from the numbers channel, the num := <-numbers statement will block, which makes the case statement to go to the default branch.

### About timeouts

you will learn how to implement timeouts in Go with the help of the select statement.

The time.Sleep() statement in the goroutine is used for simulating the time it will take for the goroutine to do its real job.

case <-time.After(time.Second * 1): 

### Channels of channels

we will talk about creating and using a channel of channels. Two possible reasons to use such a channel are as follows:
•  For acknowledging that an operation finished its job
•  For creating many worker processes that will be controlled by the same channel variable

func f1(cc chan chan int, finished chan struct{}) { 
   c := make(chan int) 

As you cannot use a channel of channels directly, you should first read from it (cc <- c) and get a channel that you can actually use. The handy thing here is that although you can close the c channel, the channel of channels (cc) will be still up and running.

### Nil channels

Nil channels
This section will talk about nil channels, which are a special sort of channel that will always block.

There, it makes c a nil channel, which means that the channel will stop receiving new data and that the function will just wait there.

Here, the sendIntegers() function keeps generating random numbers and sends them to the c channel as long as the c channel is open.

### Shared memory

Go comes with built-in synchronization features that allow a single goroutine to own a shared piece of data. This means that other goroutines must send messages to this single goroutine that owns the shared data, which prevents the corruption of the data! Such a goroutine is called a monitor goroutine. In Go terminology, this is sharing by communicating instead of communicating by sharing.


The ReadValue() function is used for reading the shared variable, whereas the SetValue() function is used for setting the value of the shared variable. Also, the two channels used in the program need to be global variables in order to avoid passing them as arguments to all the functions of the program.

### Using sync.Mutex

the Mutex variables are mainly used for thread synchronization and for protecting shared data when multiple writes can occur at the same time. A mutex works like a buffered channel of capacity 1 that allows at most one goroutine to access a shared variable at a time.

Although this is a perfectly valid technique, the general Go community prefers to use the monitor goroutine technique presented in the previous section.


The sync.Lock() method gives you exclusive access over the shared variable for a region of code that finishes when you call the Unlock() method and is called a critical section.

Each critical section of a program cannot be executed without locking it first using sync.Lock(). However, if a lock has already been taken, everybody should wait for its release first. Although multiple functions might wait to get a lock, only one of them will get it when it will be released.


You should try to make critical sections as small as possible; in other words, do not delay releasing a lock because other goroutines might want to use it. Additionally, forgetting to unlock Mutex will most likely result in a deadlock.

Note that a critical section is not always obvious and you should be very careful when specifying it. Also note that a critical section cannot be embedded in another critical section when both critical sections use the same Mutex variable! 

However, as the string should be altered simultaneously by multiple goroutines, you use a sync.Mutex variable to protect it. As the critical section contains just one command, the waiting period for getting access to the mutex will be fairly small, if not instantaneous.

this kind of locking prevents the shared variable from changing while you are reading it. This might look like a small issue here but imagine reading the balance of your bank account instead!

### Using sync.RWMutex

Go offers another type of mutex, called sync.RWMutex, that allows multiple readers to hold the lock but only a single writer - sync.RWMutex is an extension of sync.Mutex that adds two methods named sync.RLock and sync.RUnlock, which are used for locking and unlocking for reading purposes. 

type secret struct { 
   sync.RWMutex 
   counter  int 
   password string 
}

func Change(c *secret, pass string) { 
   c.Lock() 
   fmt.Println("LChange") 
   time.Sleep(20 * time.Second) 
   c.counter = c.counter + 1 
   c.password = pass 
   c.Unlock() 
}

This means that multiple instances of them can get the sync.RWMutex lock.

Although shared memory and the use of a mutex are still a valid approach to concurrent programming, using goroutines and channels is a more modern way that follows the Go philosophy. Therefore, if you can solve a problem using channels and pipelines, you should prefer that way instead of using shared variables.

The problem is that the use of RLock() and RUnlock() in Change() allows multiple goroutines to read the shared variable and therefore get the wrong output from the Counts() function.

### Using shared memory

The good thing with shared memory and mutexes is that, in theory, they usually take a very small amount of the code, which means that the rest of the code can work concurrently without any other delays.

### More benchmarking

However, apart from the speed of a program, what also matters is the clarity of its design and how easy it is to make code changes to it! Additionally, the presented way also times the compile times of both utilities, which might make the results less accurate.

### Detecting race conditions

Detecting race conditions
If you use the -race flag when running or building a Go program, you will turn on the Go race detector, which makes the compiler create a modified version of the typical executable file. This modified version can record the accesses to shared variables as well as all synchronization events that take place, including calls to sync.Mutex, sync.WaitGroup, and so on.

WARNING: DATA RACE
Read at 0x00c420074168 by goroutine 7:

Found 2 data race(s)

So, the race detector found two data races. The first one happens when number 1 was not printed at all and the second when number 4 was printed two times. Additionally, number 0 was not printed despite being the initial value of i.

which cannot be determined with any certainty as it depends on the operating system and the Go scheduler: this is where the race situation happens! So, have in mind that one of the easiest places to have a race condition is inside a goroutine spawned from an anonymous function!

As a result, if you have to solve such as situation, start by converting the anonymous function into regular functions with defined arguments!

### About GOMAXPROCS

Starting with Go version 1.5, the default value of GOMAXPROCS should be the number of cores available on your Unix system.

Although using a GOMAXPROCS value that is smaller than the number of the cores a Unix machine has might affect the performance of a program, specifying a GOMAXPROCS value that is bigger than the number of the available cores will not make your program run faster!

The way to change the value of GOMAXPROCS on the fly is as follows:
$ export GOMAXPROCS=80; go run goMaxProcs.go 
GOMAXPROCS: 80

### Writing Web Applications in Go

•  Dealing with JSON data in Go


### About the net/http Go package

The net/http package offers a built-in web server as well as a built-in web client that are both pretty powerful. The http.Get() method can be used for making HTTP and HTTPS requests, whereas the http.ListenAndServe() function can be used for creating naive web servers by specifying the IP address and the TCP port the server will listen to, as well as the functions that will handle incoming requests.

### Developing web clients

Developing web clients
In this section, you will learn how to develop web clients in Go and how to time out a web connection that takes too long to finish.


### Fetching a single URL

defer data.Body.Close() 
         _, err := io.Copy(os.Stdout, data.Body) 

However, not all HTTP requests are simple. If the response streams a video or something similar, it would make sense to be able to read it one piece at a time instead of getting all of it in a single data piece.

### Setting a timeout

var timeout = time.Duration(time.Second)

conn, err := net.DialTimeout(network, host, timeout) 

conn.SetDeadline(time.Now().Add(timeout)) 

Here, you define two types of timeouts, the first one is defined with net.DialTimeout() and is for the time it will take your client to connect to the server. The second one is the read/write timeout, which has to do with the time you want to wait to get a response from the web server after connecting to it: this is defined with the call to the conn.SetDeadline() function.

client := http.Client{ 
         Transport: &t, 
   } 

### Developing better web clients

URL, err :=url.Parse(os.Args[1]) 


   c := &http.Client{} 
 
   request, err := http.NewRequest("GET", URL.String(), nil) 


   if err != nil { 
         fmt.Println(err) 
         os.Exit(100) 
   } 
 
   httpData, err := c.Do(request) 

In this Go code, you first create an http.Client variable. Then, you construct a GET HTTP request using http.NewRequest(). Last, you send the HTTP request using the Do() function, which returns the actual response data saved in the httpData variable.

Last, you check the value of the ContentLength property, which equals -1 for dynamic pages: this means that you do not know the page size in advance.

### A small web server

the function takes two arguments, a http.ResponseWriter variable and a pointer to an http.Request variable. The first argument will be used for constructing the HTTP response, whereas the http.Request variable holds the details of the HTTP request that was received by the server, including the requested URL and the IP address of the client.


If you do not specify an IP address in the http.ListenAndServe() function call, then the web server will listen to all configured network interfaces of the machine.

Executing webServer.go will generate no output, unless you try to fetch some data from it: in this case, it will print logging information on your Terminal

### The http.ServeMux type

 Putting it simply, http.ServeMux is a HTTP request router.

### Using http.ServeMux

m := http.NewServeMux() 
   m.HandleFunc("/about", about) 
   m.HandleFunc("/CV", cv) 
   m.HandleFunc("/time", timeHandler) 
   m.HandleFunc("/", home) 
 
   http.ListenAndServe(":8001", m) 

Putting it simply, the order of the m.HandleFunc() statements matters. Also, note that if you want to support both /about and /about/, you should have both m.HandleFunc("/about", about) and m.HandleFunc("/about/", about).


$ wget -qO- http://localhost:8001/time/
Unknown page: /time/ from localhost:8001

it is better to use the http.NewServeMux() version instead of the default one except in the simplest of cases.

### The html/template package

The html/template package
Templates are mainly used for separating the formatting and data parts of the output. Note that a Go template can be either a file or string: the general idea is to use strings for smaller templates and files for bigger ones.

The key distinction between the two packages is that html/template does sanitization of the data input for HTML injection, which means that it is more secure.

myT := template.Must(template.ParseGlob("template.gohtml"))
The template.ParseGlob() function is used for reading the external template file, which can have any file extension you want. 

myT.ExecuteTemplate(w, "template.gohtml", Data) 
}
The third parameter to the ExecuteTemplate() function is the data you want to process. In this case, you pass a slice of records to it.


   myT := template.Must(template.ParseGlob("static.gohtml")) 
   myT.ExecuteTemplate(w, "static.gohtml", nil) 

arguments := os.Args 
 
   if len(arguments) == 1 { 
         filename = "" 
   } else { 
         filename = arguments[1] 
   }

{{ range . }} 
<tr> 
   <td><a href="{{ .WebSite }}">{{ .WebName }}</a></td> 
   <td> {{ .Quality }} </td> 
</tr> 
{{ end }} 

The dot (.) character represents the current data being processed: to put it simply, the dot (.) character is a variable. The {{ range . }} statement is equivalent to a for loop that visits all the elements of the input slice, which are structures in this case. You can access the fields of each structure as .WebSite, .WebName, and .Quality.

The good thing here is that if you change the contents of the /tmp/sites.html file and reload the web page, you will see the updated contents!

### About JSON

JSON stands for JavaScript Object Notation. This is a text-based format designed as an easy and light way to pass information between JavaScript systems.


Additionally, the encoding/json package offers the Marshal() and Unmarshal() functions that work similarly to Encode() and Decode() and are based on the Encode() and Decode() methods.


The main difference between Marshal()-Unmarshal() and Encode()-Decode() is that the former functions work on single objects, whereas the latter functions can work on multiple objects as well as streams of bytes.

### Saving JSON data

Note that only the members of a structure that begin with an uppercase letter will be in the JSON output because members that begin with a lowercase letter are considered private

### Parsing JSON data

Here, you define a new function named loadFromJSON() that is used for decoding a JSON file according to a data structure that is given as the second argument to it. You first call the json.NewDecoder() function to create a new JSON decode variable that is associated with a file, and then you call the Decode() function for actually decoding the contents of the file.

### Using Marshal() and Unmarshal()

rec, err := json.Marshal(&myRecord) 

Note that json.Marshal() requires a pointer for passing data to it even if the value is a map, array, or slice.

As you can see, the Marshal() and Unmarshal() functions cannot help you store your data into a file: you will need to implement that on your own.


### Using MongoDB

Using MongoDB
A relational database is a structured collection of data that is strictly organized into tables.

### Basic MongoDB administration

If you want to use MongoDB on your Go applications, it would be very practical to know how to perform some basic administrative tasks on a MongoDB database.

You can find the list of databases on a running MongoDB instance as follows:
>show databases;
LXF   0.203125GB
go    0.0625GB
local 0.078125GB

### Using the MongoDB Go driver

As the driver is not part of the standard Go library, you should first download the required packages using the following two commands:
$ go get labix.org/v2/mgo
$ go get labix.org/v2/mgo/bson

If you try to execute the program without having the two packages on your Unix system, you will get an error message similar to the following:

mongoDBDialInfo := &mgo.DialInfo{ 
         Addrs:   []string{"127.0.0.1:27017"}, 
         Timeout: 20 * time.Second, 
   } 
 
   session, err := mgo.DialWithInfo(mongoDBDialInfo) 

collection := session.DB("goDriver").C("someData")

Here, it is important to understand that MongoDB documents are presented in JSON format, which you already know how to handle in Go.

### Creating a Go application that displays MongoDB data

The utility will connect to a MongoDB instance, read a collection, and display the documents of the collection as a web page. 

The good thing is that if the data of the collections is changed, you will not need to recompile your Go code in order to see the changes: you will just need to reload the web page.

Have in mind that extra data in the MongoDB structure that does not have corresponding fields in the Go structure is ignored.

### Creating an application that displays MySQL data

Note that showMySQL.go will use the database/sql package that provides a generic SQL interface to relational databases for querying the MySQL database.

connectString := username + ":" + password + "@unix(/tmp/mysql.sock)/information_schema" 
   db, err := sql.Open("mysql", connectString) 
 
   rows, err := db.Query("SELECT DISTINCT(TABLE_SCHEMA) FROM TABLES;") 

For reasons of security, a default MySQL installation works with a socket (/tmp/mysql.sock) instead of a network connection. 

You will most likely have to adjust these parameters for your own database.


  for rows.Next() { 
         var databaseName string 
         err := rows.Scan(&databaseName) 
         if err != nil { 
               fmt.Println(err) 
               os.Exit(100) 
         } 
         DATABASES = append(DATABASES, databaseName) 
   } 
   db.Close()

The Next() function iterates over all the records returned from the select query and returns them one by one with the help of the for loop.

   t := template.Must(template.New("t1").Parse(` 
   {{range $k := .}} {{ printf "\tDatabase Name: %!s(MISSING)" $k}} 
   {{end}} 
   `)) 
   t.Execute(os.Stdout, DATABASES) 
   fmt.Println() 

This time, instead of presenting the data as a web page, you will receive it as plain text. Additionally, as the text template is small, it is defined in line with the help of the t variable.

### A handy command-line utility

The Data struct type will be used for passing information between channels.

The monitor() function is where all the information is collected and printed on the screen.

### Network Programming

•  Unix sockets
•  Performing DNS lookups from Go programs


### About TCP/IP

Note that port numbers 0-1023 are restricted and can only be used by the root user, so it is better to avoid using them and choose something else, provided that it is not already in use by a different process.

### About UDP and IP

As a result, UDP messages can be lost, duplicated, or arrive out of order. Furthermore, packets can arrive faster than the recipient can process them. So, UDP is used when speed is more important than reliability! An example for this is live video and audio applications where catching up is way more important than buffering and not losing any data.

### About the netcat utility

There are times that you will need to test a TCP/IP client or a TCP/IP server: the netcat(1) utility can help you with that by playing the role of the client or server in a TCP or UDP application.

The -l option tells netcat(1) to listen for incoming connections, which makes netcat(1) to act as a TCP or UDP server. If you try to use netcat(1) as a server with a port that is already in use, you will get the following output:
$ netcat -vv -l localhost -p 80
Can't grab 0.0.0.0:80 with bind : Permission denied

### The net Go standard package

The return value of the net.Dial() function is of the net.Conn interface type, which implements the io.Reader and io.Writer interfaces! This means that you already know how to access the variables of the net.Conn interface!


their access methods are the same because the net.Conn interface implements the io.Reader and io.Writer interfaces. Therefore, as network connections are treated as files

### A Unix socket server

If you cannot create the desired socket file, for instance, if it already exists, you will get an error message similar to the following:
$ go run socketServer.go /tmp/aSocket
listen unix /tmp/aSocket: bind: address already in use

### A Unix socket client

buf := make([]byte, 1024) 
   for { 
         n, err := r.Read(buf[:]) 

If you do not have enough Unix file permissions to read the desired socket file, then socketClient.go will fail with the following error message:


### Performing DNS lookups

While using standard Go libraries is ideal, there exist external Go libraries that allow you to choose the DNS server you desire, which is mainly required when troubleshooting DNS configurations.

### Using an IP address as input

addr := net.ParseIP(IP) 
   if addr == nil { 
         fmt.Println("Not a valid IP address!") 
         os.Exit(100) 
   }

The net.ParseIP() function allows you to verify the validity of the given IP address and is pretty handy for catching illegal IP addresses such as 288.8.8.8 and 8.288.8.8.


### Getting NS records for a domain

You can use the host(1) utility to verify the previous output:
$ host -t ns mtsoukalos.eu

### Developing a simple TCP server

Only after a successful call to Accept(), the TCP server can start interacting with TCP clients. 

### Developing a simple TCP client

In this part, you get data from the TCP server using bufio.NewReader().ReadString(). The reason for using the strings.TrimSpace() function is to remove any spaces and newline characters from the variable you want to compare with the static string (STOP).

### Using other functions for the TCP server

Additionally, net.ListenTCP() returns a TCPListener value that when used with net.AcceptTCP() instead of net.Accept() will return TCPConn, which offers more methods that allow you to change more socket options.

### Developing a simple UDP server

In the UDP case, you use ReadFromUDP() to read from a UDP connection and WriteToUDP() to write to an UDP connection. Additionally, the UDP connection does not need to call a function similar to net.Accept().

### A concurrent TCP server

Each time the program accepts a new network request using Accept(), a new goroutine gets started and concTCP.go is immediately ready to accept more requests. 

### Remote procedure call (RPC)

Remote Procedure Call (RPC) is a client-server mechanism for interprocess communication. Note that the RPC client and the RPC server communicate using TCP/IP, which means that they can exist in different machines.

### An RPC server

what is being exchanged between an RPC server and an RPC client are function names and their arguments.

   rpc.Register(myInterface) 
 
   t, err := net.ResolveTCPAddr("tcp", PORT) 

As our RPC server uses TCP, you need to make calls to net.ResolveTCPAddr() and net.ListenTCP(). However, you will first need to call rpc.Register() in order to be able to serve the desired interface.


rpc.ServeConn(c) 
   } 
}
Here, you accept a new TCP connection using Accept() as usual, but you serve it using rpc.ServeConn().

### An RPC client

   c, err := rpc.Dial("tcp", CONNECT) 

args := sharedRPC.MyInts{17, 18, true, false} 

Moreover, you call rpc.Dial() to connect to the RPC server instead of net.Dial().


   err = c.Call("MyInterface.Add", args, &reply) 

Here, you use the Call() function to execute the desired function in the RPC server. The result of the MyInterface.Add() function is stored in the reply variable, which was previously declared.


As you can guess, you cannot test the RPC client without having an RCP server and vice versa: netcat(1) cannot be used for RPC.

Of course, RPC is not for adding integers or natural numbers, but for doing much more complex operations that you want to control from a central point.

### Summary

 You are now ready to start developing useful Unix command-line utilities in Go; so, go ahead and start programming your own tools immediately!