## Introducing Go
> Caleb Doxsey

### Introduction

Although originally designed by Google for the kinds of problems Google works on—large,distributed network applications—Go is now a general-purpose programming languageuseful in a wide variety of software domains. Many companies have started using Gobecause of its simplicity, ease of use, performance, low barrier of entry, and powerfultooling.

My goalhere is to provide a more gentle introduction to the language.

This book is organized as follows:
▪  Chapters 1 through 4 introduce the Go toolset and the basics of the language
▪  Chapters 5 through 7 describe more complex types and functions
▪  Chapters 8 and 9 describe packages and testing

For best results, the book should be read in order

### 1. Getting Started

Go is a general-purpose programming language with advanced features and a clean syntax. Because of its wide availability on a variety of platforms, its robust well-documented common library, and its focus on good software engineering principles, Go is a great programming language to learn.


Open your text editor, create a new file, and enter the following:

Open up a new terminal and type in the following:

The next line is blank. Computers represent newlines with a special character (or several characters). Newlines, spaces, and tabs are known as whitespace (because you can’t see them). Go mostly doesn’t care about whitespace—we use it to make programs easier to read (you could remove this line and the program would behave in exactly the same way).

Notice that fmt is surrounded by double quotes. The use of double quotes like this is knownas a string literal, which is a type of expression. 

The line that starts with // is known as a comment. Comments are ignored by the Gocompiler and are there for your own sake (or whoever picks up the source code for yourprogram). 

. First, we access another function inside of thefmt package called Println (that’s the fmt.Println piece); Println means “print line.” Thenwe create a new string that contains Hello, World andinvoke (also known as call orexecute)that function with the string as the first and only argument.

Create a new executable program that references the fmt library and contains one function called main.That function takes no arguments and doesn’t return anything. It accesses the Println function containedinside of the fmt package and invokes it using one argument—the string Hello, World.

 You can find out more about it bytyping the following in your terminal:[插图]Among other things, you should see the output shown in Figure 1-1.

Go is a very well-documented programming language, but this documentation can bedifficult to understand unless you are already familiar with programming languages.

### 2. Types

Generally, we split numbers into twodifferent kinds: integers and floating-point numbers.

Go’s integer types are uint8, uint16, uint32, uint64, int8, int16, int32, and int64. 8, 16, 32,and 64 tell us how many bits each of the types use. uintmeans “unsigned integer” while intmeans “signed integer.” Unsigned integers only contain positive numbers (or zero).

In addition, there two alias types: 
byte (which is the same as uint8) and
 rune (which is the same as int32). Bytes are an extremely common unit of measurement used on computers (1 byte = 8 bits, 1,024 bytes = 1 kilobyte, 1,024 kilobytes = 1 megabyte, etc.) and therefore Go’s byte data type is often used in the definition of other types. 

Generally, if you are working with integers, you should just use the int type.

String literals can be created using double quotes "Hello, World" or backticks `Hello,World`. The difference between these is that double-quoted strings cannot contain newlinesand they allow special escape sequences. For example, \n gets replaced with a newline and\t gets replaced with a tab character.

Strings are indexed starting at 0, not 1. [1] gives you the second element, not the first.Also notice that you see 101 instead of e when you run this program. This is because thecharacter is represented by a byte (remember a byte is an integer).

### 3. Variables

A variable is a storage location, with a specific type and an associated name. 

Notice that we moved the variable outside of the main function. This means that otherfunctions can access this variable:

 They are created in the same way you create variables, but instead ofusing the var keyword we use the const keyword:

Constants are a good way to reuse common values in a program without writing them outeach time. For example, Pi in the math package is defined as a constant.

### 4. Control Structures

Now that you know how to use variables, it’s time to start writing some useful programs.First, let’s write a program that counts to 10, starting from 1, with each number on its ownline. Using what you’ve learned so far, you could write this:

but Go only has one that can be used in a variety of different ways. The previousprogram could also have been written like this:

if statements can also have else if parts:

Suppose you wanted to write a program that printed the English names for numbers. Usingwhat you’ve learned so far, you might start by doing this:

### 5. Arrays, Slices, and Maps

An array is a numbered sequence of elements of a single type with a fixed length. In Go, theylook like this:[插图]

The issue here is that len(x) and total have different types. total is a float64 while len(x)is an int. So we need to convert len(x) into a float64:

This is an example of a type conversion. In general, to convert between types, you use thetype name like a function.

The Go compiler won’t allow you to create variables that you never use. Because we don’tuse i inside of our loop, we need to change it to this:

Go also provides a shorter syntax for creating arrays:
x := [5]float64{ 98, 93, 77, 82, 83 }

We no longer need to specify the type because Go can figure it out. Sometimes arrays likethis can get too long to fit on one line, so Go allows you to break it up like this:

Notice the extra trailing , after 83. This is required by Go and it allows us to easily remove anelement from the array by commenting out the line:

Because the length of an array is part of its type name, working with arrays can be a littlecumbersome. Adding or removing elements as we did here requires also changing the lengthinside the brackets. Because of this and other limitations, you rarely see arrays used directlyin Go code. Instead, you will usually use a slice, which is a type built on top of an array.

A slice is a segment of an array. Like arrays, slices are indexable and have a length. Unlikearrays, this length is allowed to change. 

The only difference between this and an array is the missing length between the brackets. Inthis case, x has been created with a length of zero.

If you want to create a slice, you should use the built-in make function:

This creates a slice that is associated with an underlying float64 array of length 5. Slices arealways associated with some array, and although they can never be longer than the array,they can be smaller. The make function also allows a third parameter:

As illustrated in Figure 5-1, 10 represents the capacity of the underlying array that the slicepoints to:

Another way to create slices is to use the [low : high] expression:

For convenience, we are also allowed to omit low, high, or even both low and high. arr[0:] isthe same as arr[0:len(arr)], arr[:5] is the same as arr[0:5], and arr[:] is the same asarr[0:len(arr)].In addition to the indexing operator, Go includes two built-in functions to assist with slices:append and copy.

append adds elements onto the end of a slice. If there’s sufficient capacity in the underlyingarray, the element is placed after the last element and the length is incremented. However, ifthere is not sufficient capacity, a new array is created, all of the existing elements are copiedover, the new element is added onto the end, and the new slice is returned.

copy takes two arguments: dst and src. All of the entries in src are copied into dstoverwriting whatever is there. If the lengths of the two slices are not the same, the smaller ofthe two will be used.

Slices are typically used to represent lists of items, particularly when you need to access thenth item quickly—for example, player #33, or the 10th most popular search query. But what ifyou want to look up an entry by something other than integer? What if you wanted to look upa player on a team by last name? Go has another built-in type that can do this: a map.

A map is an unordered collection of key-value pairs (maps are also sometimes calledassociative arrays, hash tables, or dictionaries). Maps are used to look up a value by itsassociated key. Here’s an example of a map in Go:

The map type is represented by the keyword map, followed by the key type in brackets andfinally the value type. If you were to read this out loud, you would say “x is a map of stringsto ints.”

Up until now, we have only seen compile-time errors. This is an example of a runtime error.As the name would imply, runtime errors happen when you run the program, while compile-time errors happen when you try to compile the program.

The problem with our program is that maps have to be initialized before they can be used.We should have written this:

We can also delete items from a map using the built-in delete function:

 Technically, a map returns the zero value forthe value type (which for strings is the empty string). Although we could check for the zerovalue in a condition ( elements["Un"] == ""), Go provides a better way:[插图]

Accessing an element of a map can return two values instead of just one. The first value isthe result of the lookup, the second tells us whether or not the lookup was successful. In Go,we often see code like this:[插图]

Like we saw with arrays, there is also a shorter way to create maps:

### 6. Functions

Notice that we changed the fmt.Println to be a return instead. The return statement causesthe function to immediately stop and return the value after it to the function that called thisone. Modify main to look like this:

Running this program should give you exactly the same result as the original. A few things tokeep in mind:Parameter names can be differentThe calling and callee functions are allowed to use different names for the parameters.

Variables must be passed to functionsFunctions don’t have access to anything in the calling function unless it’s passed inexplicitly. This won’t work:

By using an ellipsis (...) before the type name of the last parameter, you can indicate that ittakes zero or more of those parameters. In this case, we take zero or more ints. 

ClosureIt is possible to create functions inside of functions. Let’s move the add function we sawbefore inside of main:

add is a local variable that has the type func(int, int) int (a function that takes two intsand returns an int). When you create a local function like this, it also has access to otherlocal variables (remember scope from Chapter 3):

Go has a special statement called defer that schedules a function call to be run after thefunction completes. Consider the following example:

defer is often used when resources need to be freed in some way. For example, when weopen a file, we need to make sure to close it later. With defer:

This has three advantages:It keeps our Close call near our Open call so it’s easier to understand.If our function had multiple return statements (perhaps one in an if and one in an else),Close will happen before both of them.Deferred functions are run even if a runtime panic occurs.

Pointers reference a location in memory where a value is stored rather than the value itself.By using a pointer (*int), the zero function is able to modify the original variable.

An asterisk is also used to dereference pointer variables. Dereferencing a pointer gives usaccess to the value the pointer points to. When we write *xPtr = 0, we are saying “store theint 0 in the memory location xPtr refers to.” If we try xPtr = 0 instead, we will get acompile-time error because xPtr is not an int; it’s a *int, which can only be given another*int.

Finally, we use the & operator to find the address of a variable. &x returns a *int (pointer toan int) because x is an int. This is what allows us to modify the original variable. &x in mainand xPtr in zero refer to the same memory location.

newAnother way to get a pointer is to use the built-in new function:

new takes a type as an argument, allocates enough memory to fit a value of that type, andreturns a pointer to it.

 You don’t haveto worry about this with Go—it’s a garbage-collected programming language, which meansmemory is cleaned up automatically when nothing refers to it anymore.Pointers are rarely used with Go’s built-in types, but as we will see in the next chapter, theyare extremely useful when paired with structs.

### 7. Structs and Interfaces

Chapter 7. Structs and Interfaces
Although it would be possible for us to write programs only using Go’s built-in data types, at some point it would become quite tedious. Consider a program that interacts with shapes:

An easy way to make this program better is to use a struct. A struct is a type that contains named fields. For example, we could represent a circle like this:

The type keyword introduces a new type. It’s followed by the name of the type (Circle), the keyword struct to indicate that we are defining a struct type, and a list of fields inside of curly braces.

Fields are like a set of grouped variables. Each field has a name and a type and is stored adjacent to the other fields in the struct. 

Like with other data types, this will create a local Circle variable that is by default set to zero. For a struct, zero means each of the fields is set to their corresponding zero value (0 for ints, 0.0 for floats, "" for strings, nil for pointers, etc.) 

We can also use the new function:
c := new(Circle)
This allocates memory for all the fields, sets each of them to their zero value, and returns a pointer to the struct (*Circle). Pointers are often used with structs so that functions can modify their contents.

The second option is to leave off the field names if we know the order they were defined:
c := Circle{0, 0, 5}

This creates the same Circle as the previous example. If you want a pointer to the struct, use &:
c := &Circle{0, 0, 5}

Fields

We can access fields using the . operator:


One thing to remember is that arguments are always copied in Go. If we attempted to modify one of the fields inside of the circleArea function, it would not modify the original variable. Because of this, we would typically write the function using a pointer to the Circle:
func circleArea(c *Circle) float64 {

And change main to use & before c:

c := Circle{0, 0, 5}
fmt.Println(circleArea(&c))

Although this is better than the first version of this code, we can improve it significantly by using a special type of function known as a method:


In between the keyword func and the name of the function, we’ve added a receiver. The receiver is like a parameter—it has a name and a type—but by creating the function in this way, it allows us to call the function using the . operator:
fmt.Println(c.area())

This is much easier to read. We no longer need the & operator (Go automatically knows to pass a pointer to the circle for this method), and because this function can only be used with Circles, we can rename the function to just area.

We use the type (Person) and don’t give it a name. When defined this way, the Person struct can be accessed using the type name:
a := new(Android)
a.Person.Talk()

The is-a relationship works this way intuitively: people can talk, an android is a person, therefore an android can talk.




type Shape interface {
    area() float64
}

Like a struct, an interface is created using the type keyword, followed by a name and the keyword interface. But instead of defining fields, we define
 a method set. A method set is a list of methods that a type must have in order to implement the interface.

In our case, both Rectangle and Circle have area methods that return float64s, so both types implement the Shape interface. By itself, this wouldn’t be particularly useful, but we can use interface types as arguments to functions.

This works, but it has a major issue—whenever we define a new shape, we have to change our function to handle it (a third parameter for Polygons, a fourth for Squares, etc.).
This is the problem interfaces are designed to solve. Because both of our shapes have an area method, they both implement the Shape interface and we can change our function to this:

We would call this function like this:
fmt.Println(totalArea(&c, &r))

It’s sufficient to merely have a method with the same name and signature.
Interfaces can also be used as fields. Consider a MultiShape that is made up of several smaller shapes:
type MultiShape struct {
    shapes []Shape
}
We can create a MultiShape like this:


When building new programs, you often won’t know what your types should look like when you start—and that’s OK. In Go, you generally focus more on the behavior of your program than on a taxonomy of types. Create a few small structs that do what you want, add in methods that you need, and as you build your program, useful interfaces will tend to emerge. There’s no need to have them all figured out ahead of time.


Interfaces are particularly useful as software projects grow and become more complex. They allow us to hide the incidental details of implementation (e.g., the fields of our struct), which makes it easier to reason about software components in isolation. 

1.  What’s the difference between a method and a function?

### 8. Packages

An important part of high-quality software is code reuse—embodied in the principle “Don’t Repeat Yourself.”

fmt is the name of a package that includes a variety of functions related to formatting and output to the screen. Bundling code in this way serves three purposes:
▪  It reduces the chance of having overlapping names, and in turn keeps our function names short and succinct.
▪  It organizes code so that it’s easier to find code you want to reuse.

▪  It speeds up the compiler by only requiring recompilation of smaller chunks of a program. Although we use the package fmt, we don’t have to recompile it every time we change our program.


Instead of writing everything from scratch, most real-world programming depends on our ability to interface with existing libraries. This chapter will take a look at some of the most commonly used packages included with Go.

Strings


Go includes a large number of functions to work with strings in the strings package.
To search for a smaller string in a bigger string, use
 the Contains function:


To determine if a bigger string starts with a smaller string, use 
the HasPrefix function:

To take a list of strings and join them together in a single string separated by another string (e.g., a comma), use 
the Join function:

To repeat a string, use 
the Repeat function:


To replace a smaller string in a bigger string with some other string, use 
the Replace function. In Go, Replace also takes a number indicating how many times to do the replacement (pass -1 to do it as many times as possible):

To split a string into a list of strings by a separating string (e.g., a comma), use
 the Split function (Split is the reverse of Join):


To convert a string to all lowercase letters, use 
the ToLower function:

To convert a string to all uppercase letters, use 
the ToUpper function:

Sometimes we need to work with strings as binary data. To convert a string to a slice of bytes (and vice versa), do this:




Input/Output



Before we look at files, we need to understand Go’s io package. The io package consists of a few functions, but mostly interfaces used in other packages. The two main interfaces are Reader and Writer. Readers 
support reading via the Read method. Writers 
support writing via the Write method. Many functions in Go take Readers or Writers as arguments. For example, the io package has a Copy function that copies data from a Reader to a Writer:
func Copy(dst Writer, src Reader) (written int64, err error)

To read or write to a []byte or a string, you can use 
the Buffer struct found in the bytes package:



To open a file in Go, use the Open function from the os package. Here is an example of how to read the contents of a file and display them on the terminal:

    defer file.Close()


    // read the file
    bs := make([]byte, stat.Size())
    _, err = file.Read(bs)
    if err != nil {
        return
    }

    str := string(bs)
    fmt.Println(str)

We use defer file.Close() right after opening the file to make sure the file is closed as soon as the function completes. Reading files is very common, so there’s a shorter way to do this:


bs, err := ioutil.ReadFile("test.txt")
    if err != nil {
        return
    }
    str := string(bs)

returns an os.File and possibly an error (if it was unable to create it for some reason). Here’s an example program:

   defer file.Close()

    file.WriteString("test")

    fileInfos, err := dir.Readdir(-1)
    if err != nil {
        return
    }
    for _, fi := range fileInfos {
        fmt.Println(fi.Name())
    }

Readdir takes a single argument that limits the number of entries returned. By passing in -1, we return all of the entries.


Often, we want to recursively walk a folder (read the folder’s contents, all the subfolders, all the sub-subfolders, etc.). To make this easier, there’s a Walk function
 provided in the path/filepath package:


 It’s passed three arguments: path, which is the path to the file; info, which is the information for the file (the same information you get from using os.Stat); and err, which is any error that was received while walking the directory. The function returns an error and you can return filepath.SkipDir to stop walking immediately.



Go has a built-in type for errors that we have already seen (the error type). We can create our own errors by using the New function in the errors package:
package main

import "errors"

func main() {
    err := errors.New("error message")
}

List

The container/list package implements a doubly linked list. A linked list is a type of data structure that looks like Figure 8-1.

Each node of the list contains a value (1, 2, or 3, in this case) and a pointer to the next node. Because this is a doubly linked list, each node will also have pointers to the previous node. This list could be created by this program:

The zero value for a List is an empty list (a *List can also be created using list.New). Values are appended to the list using PushBack. 

Sort


The sort package contains functions for sorting arbitrary data. There are several predefined sorting functions (for slices of ints and floats) Here’s an example for how to sort your own data:

To define our own sort, we create a new type (ByName) and make it equivalent to a slice of what we want to sort. We then define the three methods.


A hash function takes a set of data and reduces it to a smaller fixed size. Hashes are frequently used in programming for everything from looking up data to easily detecting changes. 

Hash functions in Go are broken into two categories: cryptographic and non-cryptographic.


Cryptographic hash functions are similar to their non-cryptographic counterparts, but they have the added property of being hard to reverse. Given the cryptographic hash of a set of data, it’s extremely difficult to determine what made the hash. These hashes are often used in security applications.

The main difference is that whereas crc32 computes a 32-bit hash, sha1 computes a 160-bit hash. There is no native type to represent a 160-bit number, so we use a slice of 20 bytes instead.



Servers


Writing distributed, networked applications in Go is relatively straightforward. We will briefly take a look at three common approaches to communicating between multiple computers: TCP servers, HTTP servers, and RPC.

Once we have a Listener, we call Accept, which waits for a client to connect and returns a net.Conn. A net.Conn implements the io.Reader and io.Writer interfaces, so we can read from it and write to it just like a file.

This example uses the encoding/gob package, which makes it easy to encode Go values so that other Go programs (or the same Go program, in this case) can read them. 

HTTP servers are even easier to set up and use:

The net/rpc (remote procedure call) and net/rpc/jsonrpc packages provide an easy way to expose methods so they can be invoked over a network (rather than just in the program running them):

The flag package allows us to parse arguments and flags sent to our program. 

Any additional non-flag arguments can be retrieved with flag.Args(), which returns a []string.



Inside that folder, create a file called main.go using the following code:

▪  When we import our math library, we use its full name (import "golang-book/chapter8/math"), but inside of the math.go file, we only use the last part of the name (package math).

We also only use the short name math when we reference functions from our library. If we wanted to use both libraries in the same program, Go allows us to use an alias (m is the alias):


Go has the ability to automatically generate documentation for packages we write in a similar way to the standard package documentation. In a terminal, run this command:
godoc golang-book/chapter8/math Average

We can improve this documentation by adding a comment before the function:
// Finds the average of a series of numbers
func Average(xs []float64) float64 {

This documentation is also available in web form by running this command:
godoc -http=":6060"

### 9. Testing


Programming is not easy; even the best programmers are incapable of writing programs that work exactly as intended every time. Therefore, an important part of the software development process is testing. Writing tests for our code is a good way to ensure quality and improve reliability.

Go includes a special program that makes writing tests easier: go test. 

The Go compiler knows to ignore code in any files that end with _test.go, so the code defined in this file is only used by go test (and not go install or go build).

Then we import the special testing package and define a function that starts with the word Test (case matters) followed by whatever we want to name our test. 

For the body of the function, we invoke the Average function on a hardcoded slice of floats (Average([]float64{1,2})). We then take that value and compare it to 1.5 and if they’re not the same, we use the special t.Error function (which is very much like fmt.Println) to signal an error to the go test program.


To actually run the test, run the following in the same directory:
go test
You should see this:
$ go test
PASS
ok      golang-book/chapter8/math      0.032s
The go test 
command will look for any tests in any of the files in the current folder and run them. Tests are identified by starting a function with the word Test and taking one argument of type *testing.T.

Once we have set up the testing function, we write tests that use the code we’re testing. 

It’s probably a good idea to test many different combinations of numbers, so let’s slightly modify our test program:

We create a struct to represent the inputs and outputs for the function:
type testpair struct {
    values []float64
    average float64
}

 But even a small set of basic tests is better than none.


### 10. Concurrency

It would be ideal for programs like these to be able to run their smaller components at the same time (in the case of the web server, to handle multiple requests). Making progress on more than one task simultaneously is known as concurrency. Go has rich support for concurrency using goroutines and channels.

A goroutine is a function that is capable of running concurrently with other functions. Tocreate a goroutine, we use the keyword go followed by a function invocation:

This program consists of two goroutines. The first goroutine is implicit and is the mainfunction itself. The second goroutine is created when we call go f(0). Normally, when weinvoke a function, our program will execute all the statements in a function and then returnto the next line following the invocation. With a goroutine, we return immediately to the nextline and don’t wait for the function to complete. This is why the call to the Scanln functionhas been included; without it, the program would exit before being given the opportunity toprint all the numbers.

Goroutines are lightweight and we can easily create thousands of them. We can modify ourprogram to run 10 goroutines by doing this:

Channels provide a way for two goroutines to communicate with each other and synchronizetheir execution. Here is an example program using channels:

A channel type is represented withthe keyword chan followed by the type of the things that are passed on the channel (in thiscase, we are passing strings). The left arrow operator (<-) is used to send and receivemessages on the channel. c <- "ping" means send "ping". msg := <- c means receive amessage and store it in msg. The fmt line could also have been written like fmt.Println(<-c),in which case we could remove the previous line.

When pinger attempts to send amessage on the channel, it will wait until printer is ready to receive the message (this isknown as blocking). 

We can specify a direction on a channel type, thus restricting it to either sending orreceiving. For example, pinger’s function signature can be changed to this:

Now pinger is only allowed to send to c. Attempting to receive from c will result in acompile-time error. Similarly, we can change printer to this:

A channel that doesn’t have these restrictions is known as bidirectional. A bidirectionalchannel can be passed to a function that takes send-only or receive-only channels, but thereverse is not true.

Go has a special statement called select that works like a switch but for channels:

 select picksthe first channel that is ready and receives from it (or sends to it). If more than one of thechannels are ready, then it randomly picks which one to receive from. If none of the channelsare ready, the statement blocks until one becomes available.

The select statement is often used to implement a timeout:

we weren’t interested in the time, so we didn’t store it in a variable

The default case happens immediately if none of the channels are ready.

It’s also possible to pass a second parameter to the make function when creating a channel:[插图]This creates a buffered channel with a capacity of 1. Normally, channels are synchronous;both sides of the channel will wait until the other side is ready. A buffered channel isasynchronous; sending or receiving a message will not wait unless the channel is already full.If the channel is full, then sending will wait until there is room for at least one more int.

Here is an example program that uses goroutines and channels. It fetches several web pagessimultaneously using the net/http package, and prints the URL of the biggest home page(defined as the most bytes in the response):

We define a type that will store home page sizes:[插图]Then we create a list of URLs:[插图]

Then we create a channel and start a new goroutine for each URL (so we will be fetchingfour URLs simultaneously). For each URL, we make an HTTP get request:

Notice that this is an unnamed function that is immediately invoked. This is a commonpattern with goroutines, but also notice that we defined the function as taking a singleparameter (the url). The reason this function doesn’t reference the url directly—which it isallowed to do—is that the rules of closure are such that if we did that, all four goroutineswould probably end up seeing the same value for url. This is because url is changed by thefor loop.

Finally, at the end of our program, we loop four times to pull the four results off of thechannel, compare that results to the current biggest, and swap if it’s bigger:

What is a buffered channel? How would you create one with a capacity of 20?

### 11. Next Steps

You now have all the information necessary to write most Go programs. However, there arestill some nuances and advanced techniques you must learn—programming is as much acraft as it is just having knowledge. This chapter will provide you with some suggestionsabout how best to master the craft of programming.

Part of becoming a good artist or writer is studying the works of the masters. It’s nodifferent with programming. One of the best ways to become a skilled programmer is tostudy the source code produced by others. Go is well suited to this task because the sourcecode for the entire project is freely available.

Read the code slowly and deliberately. Try to understand every line and take a look at thesupplied comments. 

it’s important to supply comments with thosechanges. The source code for all of the packages is available at golang.org/src/pkg/.

One of the best ways to hone your skills is to practice coding. There are a lot of ways to dothis—you could work on challenging programming problems from sites like Project Euler, ortry your hand at a larger project. Perhaps try to implement a web server or write a simplegame.

Most real-world software projects are created by teams, so learning how to work on a teamis crucial. If you can, find a friend or a classmate and team up on a project. Learn how todivide a project into pieces you can both work on simultaneously.

Another option is to work on an open source project. Find a third-party library, write somecode (perhaps fix a bug), and submit it to the maintainer. Go has a growing community thatcan be reached via the mailing list.

### A. Answers

Whitespace is the space between characters made up of space, tab, and newlinecharacters.

A comment is a section of code ignored by the compiler that can be used as a note foranyone reading the code. The two types of comments are // (which goes to the end ofthe line) and /* */.

The difference between a method and a function is that a method has a receiver while afunction does not.

You would use an embedded anonymous field instead of a normal named field in orderto use methods directly on the containing type.

Packages are a good software engineering practice. They allow us to break up largeprograms into smaller, easier to understand, and easier to maintain programs. They alsoencourage software reuse.

Sleep function using time.After:

A buffered channel allows send operations to succeed regardless of whether or not thereis a receiver on the other end by storing the sent message in a buffer. To create abuffered channel, specify a buffer size as an argument to make: make(chan int, 20).

### Index

Caleb Doxsey is a New York City–based developer who enjoys helping new programmers learn Go. He works as a software engineer at DataDog, building monitoring software for the cloud.
