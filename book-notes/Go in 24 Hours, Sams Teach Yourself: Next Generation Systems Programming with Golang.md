## Go in 24 Hours, Sams Teach Yourself: Next Generation Systems Programming with Golang
> George Ornbo

### Title Page

Go in 24 Hours Sams Teach YourselfNext Generation Systems Programming with Golang

### About the Author

George Ornbo is a software engineer, blogger, and author with 14 years of experience delivering software to startups and enterprise clients. He has experience with a broad range of programming languages, UNIX, and the underlying protocols of the web. He is currently working at a Blockchain startup in London.

### Introduction

If you are interested in creating systems or network-based applications, Go is a great choice. As a relatively new language, it has been designed by experienced and respected computer scientists to respond to the challenges of creating concurrent, networked programs at scale. 

The book starts with the basics of Go, including setting up your environment and running your first Go program. You are then introduced to some of the fundamentals of the language, including strings, functions, structs, and methods. You will understand how to use Goroutines and Channels, a unique feature of Go that abstracts much of the difficulty of concurrent programming.


You will be introduced to how to debug and test Go code, and will be introduced to some techniques that will help you to understand how to write idiomatic Go code.

You will then understand how to write basic command-line programs, HTTP servers, and HTTP clients; how to work with JSON; and how to work with files.

You can download the code examples at https://github.com/shapeshed/golang-book-examples/

if you already have Go installed, by running go get github.com/shapeshed/golang-book-examples

### Hour 1. Getting Started

The announcement describes the high-level goals of the language as combining “the development speed of working in a dynamic language like Python with the performance and safety of a compiled language like C or C++.”

During the compilation of code, a compiler can check for errors, make performance optimizations, and output binaries that can run on different platforms.

To complete the installation, the directory structure for Go projects needs to be created. 

Go offers a shorthand for compiling and running a file because this is a common step duringdevelopment. The following code compiles a program and runs it in one step.go run main.go

Q. Why was Go created when there are so many other languages?
A. Go is a reaction to traditional languages, like Java and C++, which can be cumbersome, slow, and heavy. Taking some inspiration from dynamically typed languages, like Python, the language designers wanted to create a language that was easy to work with, but could scale to large teams working on high traffic production system

A. The compiler binary file needs to include everything it needs to execute the program. The downside of this is that it results in a large file in relation to the source code. The upside is that there are no dependencies to install when you want to run the program.


Q. Should I use go build or go run?
A. When developing it is recommended to use go run. When you are ready to share your program, use go build.

1. Aside from a computer, all you really need is a terminal and a text editor.

### Hour 2. Understanding Types

Languages are said to be “strongly typed” if a compiler throws an error when types are usedincorrectly. Languages are said to be “dynamically typed” (also known as “loosely typed”and “weakly typed”) if a runtime converts types from one type to another in order to executethe program, or if the compiler does not enforce the type system. 

Some advantages of using a statically typed language include:[插图] Better performance than dynamically typed languages.[插图] Bugs can often be caught by a compiler.[插图] Code editors can offer code completion and other tooling.[插图] Better data integrity.

Some advantages of using a dynamically typed language include:[插图] It is often faster to write software using a dynamically typed language.[插图] There is no need to wait for a compiler to execute code.[插图] Dynamically typed languages are generally less rigid, and some argue that they are moreopen to change.[插图] Some argue there is a shorter learning curve.

A Boolean value can be declared as follows.var b boolIf the variable is not assigned a value, it will be false by default.

Booleans may be reassigned after they have been created (see Listing 2.6), and they are anextremely useful programming construct.

Bits are just a series of Boolean values and are either 1 or 0. One bit is either a 1 or a 0.

For signed integers, one of the bits may be used as a sign to represent its value. 

Using the int type is a signed integer, and as such positive and negative numbers are supported. Depending on the underlying architecture of a machine, int can either be a signed 32-bit integer or a signed 64-bit integer. Unless you are working with really big numbers, or have particular performance concerns, you can just use int without really worrying about what the compiler does.

When initializing an array, both the length and type of an array must be specified.
var beatles [4]string

Using the reflect package (as shown in Listing 2.7) can be useful for debugging or if there is a requirement to verify the underlying type.

LISTING 2.7 Type Checking with the reflect Package


12:      fmt.Println(reflect.TypeOf(s))

A common programming task is to take data in one type and convert it to another. 

Suppose that the variable s exists as a type string with the value true. To complete Boolean comparisons, the string must be converted into a Boolean type.
var s string = "true"
b, err := strconv.ParseBool(s)
The variable b is now a Boolean type. Similarly, a Boolean value can be converted to a string.
s := strconv.FormatBool(true)
fmt.Println(s)

2. An unsigned integer has no “sign” to take up bytes. In a 4-bit integer, an unsigned integer has 16 possible numbers and can only be positive. A 4-bit signed integer still has 16 possible numbers, but they can be either negative or positive.

### Hour 3. Understanding Variables

Go supports several shorthand ways of declaring variables. Variables of the same type may be declared and assigned values on the same line, as shown in Listing 3.4.

 8:      var s, t string = "foo", "bar"

After a variable is declared with a type, it is not possible to declare it again. Although reassigning the value is allowed, declaring a variable again is not permitted and results in a compile time error.


You may have noticed that Go offers several ways to declare a variable. For completeness, here are the ways that a variable can be declared:
var s string = "Hello World"
var s = "Hello World"
var t string
t = "Hello World"
u := "Hello World"

It is not possible to use short variable declaration outside a function, but otherwise, anything goes.


If you look at the Go source code, you see that short variable declaration is the most common way of declaring a variable.


In plain English, this can be explained as:
￼ Brace brackets {} in Go denote a block.
￼ A variable declared within brace brackets {} may be accessed anywhere within the block.
￼ Brace brackets within brace brackets denote a new block, known as an inner block.
￼ Inner blocks can access variables within outer blocks.
￼ Outer blocks cannot access variables within inner blocks.

In short, every inner level (or block) can access its outer levels, but outer levels cannot access inner levels.


This is because Go also considers a file a block, so anything outside of the first level of brace brackets in a file is available within all blocks.

The sequence of numbers is in base 16 format. Do not worry if you do not understand what base 16 is. It is enough to understand that each variable has a different memory address where the value of the variable is stored. 

Constants represent values that do not change during the life of a program. They can be useful for declaring variables that never change. Once a variable has been initialized, it can be referenced but never changed.

1. A short variable declaration may not be used outside of a function.
2. A block in Go is denoted by brace brackets {}. For completeness, there is also a file level block, package level block, and a universe block.


3. A constant is a value that should not change while the program is running.

### Hour 4. Using Functions in Go

You will see how Go can return multiple results, accept a varyingnumber of arguments, and how it displays some features that make it look somewhat like afunctional programming language.

At a high level, a function takes an input and returns an output. As data flows through afunction, it is transformed.

In this trivial example,the two numbers are said to be the inputs and the result of the sum is said to be the output.

The first line of a functionprovides this information and is known as the function signature. 

[插图] The function does one thing and does it well. 

[插图] Maintenance. If you are working in a team, do you think the function is easy to read andunderstand? If it is not, it may be overly complex or require some documentation. Rememberthat it may be you that revisits the function in the middle of the night in twelve months’ time!

The return statement returns multiple values, separated by acomma.func getPrize() (int, string) {    i := 2    s := "goldfish"    return i, s

func sumNumbers(numbers . . .int) int {The function will accept one or more integers. It may be used to sum any number of integersand will return a single integer. Within the function, the numbers variable contains a slice ofthe argument. 

func sumNumbers(numbers . . .int) int {    total := 0    for _, number := range numbers {        total += number    }    return total}

Named return values allow a function to assign values to named variables before they arereturned. This can be useful to make a function more explicit or readable. To use namedreturn values, the function signature declares the variables as part of the return values.func sayHi() (x, y string) {

 If you use named return values, there is no need to explicitly return thevariables. This is known as a naked return statement.

Recursive functions are functions that can call themselves indefinitely or, more usually, untila particular condition is met. To use recursive functions, a function calls itself as the resultvalue of a terminating statement.

 In essence, Go treats functions as a type like any other, so it ispossible to assign functions to a value and call them at a later date. 

 The function is called using () after the variable name.

Remember that functions are a type in Go, so they may be passed to another function. 

A. No. Once a function is declared, the compiler takes the function signature and uses it toverify that the number of arguments and types are correctly used.

A.Named return functions can be used for short functions but can make code more difficultto read; and in some cases, they are more prone to bugs. 

### Hour 5. Using Control Flow

 the switch statement offers more readable codeand better performance.

10:      switch i {11:      case 2:12:          fmt.Println("Two")13:      case 3:14:          fmt.Println("Three")15:      case 4:16:          fmt.Println("Four")17:      }

This can be triggered by a return statement or if a function has reachedthe end of the function body. The defer statement is typically used for cleanup operationsor to ensure that operations like network calls have completed before running anotherfunction.

A. Both else if and switch are valid ways to create control flow. Many programmers finda switch statement easier to read than an else if chain. As such, using switch isrecommended.

### Hour 6. Working with Arrays, Slices, and Maps

[插图]Working with arrays[插图]Working with slices[插图] Adding and removing elements from a slice

An array is a data structure that is a collection of other pieces of data. An array is commonlyused in programming to logically group pieces of data. Arrays are frequently used to store anumerically indexed group of data and represent a fundamental building block ofprogramming.

To create an array in Go, the length and data type must be declared when initializing anarray variable:var cheeses [2]string

The index starts at zero rather than one, so to access the first element of anarray the index is “0”. To access the second element of an array, the index is “1”, and so on.

To print the values of an array, the variable and the index value can be used:fmt.Println(cheeses[0])fmt.Println(cheeses[1])Furthermore, to print every element of an array, the variable name on its own can be used.fmt.Println(cheeses)

A slice is defined as a “contiguous segment of an underlying array and provides access to anumbered sequence of elements from that array.” As such, slices provide access to parts ofan array in sequential order. Why then do slices exist? Why not just use arrays?

Using arrays in Go has some limitations. You saw in the cheeses array that it was notpossible to add additional elements to the array. Slices offer more flexibility than arrays inthat it is possible to add, remove, and copy elements from a slice. Slices can be considereda lightweight wrapper around arrays that preserves the integrity of the array type but makesarrays easier to work with.

To declare an empty slice with a length of 2, the syntax isvar cheeses = make([]string, 2)

Go offers the builtin function append as a way to extend the length of a slice.

 Thismeans that it is possible to append many values to a slice by using the append function.Click here to view code imagecheeses := append(cheeses, "Camembert", "Reblochon", "Picodon")

Deleting Elements from a Slice
To delete an element from a slice, the append builtin may also be used. In the following example, the element at index 2 is to be deleted:
Click here to view code image
cheeses = append(cheeses[:2], cheeses[2+1:]...)

To copy all or some of the elements of a slice, the builtin function copy may be used. Tocopy elements from one slice to another, a slice must be initialized with the same type.

. Maps can be very efficient in looking up pieces of information, as the data can beretrieved directly through the key. In simple terms, maps can be considered a collection ofkeys and values.

An empty map can be initialized in a single line of code.Click here to view code imagevar players = make(map[string]int)

To delete elements from a map, the delete builtin function can be used.delete(players, "cook")

### Hour 7. Using Structs and Pointers

As its name suggests, a struct is a structure of data elements that is a useful programming construct. 

Finally, you will learn the subtle difference between using pointers and values in relation to underlying memory. By the end of this hour, you will have a good understanding of structs and how to create and use them.

Structs provide a way to reference a series of grouped values through a single variable name. This allows many different data fields of possibly different types to be stored and accessed under a single variable.

Structs can improve modularity and allow complex data structures to be created and passed around. Structs can also be thought of as a template for creating a data record, like an employee record or an airline booking.


 7:  type Movie struct {
 8:      Name   string
 9:      Rating float32
11:  }

￼ Within the curly brackets, a series of data fields are specified with a name and a type. Note that no values are assigned at this point. Think of it like a template.

￼ In the main function, a variable m is initialized through shorthand variable assignment, and values are assigned to the data fields according to their types.

￼ The data fields are accessed using dot notation and printed to the console.

var m Movie
This creates an instance of the struct with default values assigned to the specified data types. A useful tip for debugging and viewing the values of structs is to use the fmt package to print out the field names and values for a struct. This can be achieved using the %!v(MISSING) verb and passing a struct.
fmt.Printf("%!v(MISSING)\n", m)

Go will assign zero values for each type if a struct is initialized without values.


Values of struct fields are mutable, meaning they can be changed dynamically. If, for example, you want to change the name of the movie, this is allowed. It is not possible, however, to change the types in a struct after it has been declared or an instance has been created. This will result in a compile time error.

An instance of a struct can also be created with the new keyword, as shown in Listing 7.3. The new keyword allocates memory storage for a variable of a type Movie and assigns it to m. It is then possible to assign data values to the data fields as before, using dot notation.
m := new(Movie)
m.Name = "Metropolis"
m.Rating = 0.99

c := Movie{Name: "Citizen Kane", Rating: 10}
It is possible to omit the field names and assign values in the order they are declared, although for maintainability this is not recommended.
Click here to view code image
c := Movie{"Citizen Kane", 10}

As field lists grow, it can also help maintenance and readability to put fields on a new line. Note that if you choose to do this, the line of the last data field must end in a comma.
c := Movie {
    Name: "Citizen Kane",
    Rating: 10,
}

Using short variable assignment is the most common—and recommended—way of creating a struct (see Listing 7.4).



LISTING 7.4 Initializing a Struct Using Short Variable Assignment

Structs are commonly used in C and C++ and, as a C-family language, Golang also has structs. 

If a struct element is initialized without a value, the relevant default value from Table 7.1 is assigned. 

Instead of creating a struct directly, the NewAlarm function can be used to create an Alarm struct with a custom default value for the Sound field. 

By wrapping the creation of the struct in a constructor function, the value of Klaxon is assigned. 

TRY IT YOURSELF: Setting Default Values Using a Constructor Function


It is not possible to compare structs of different types, and attempting to do so will result in a compile time error. As such, it can be necessary to check that structs are of the same type before attempting a comparison. Go’s reflect package supports this and allows the underlying type of a struct to be interrogated. Listing 7.8 shows the reflect package being used to show the underlying type of a struct being shown.


22:      fmt.Printf(reflect.TypeOf(a))
23:      fmt.Printf(reflect.TypeOf(b))

Understanding Public and Private Values
If you are familiar with other programming languages, you may understand the idea of public and private values. This is where a value is public if it is exported and is available outside of a function, method, or package. If a value is private, it is only available within its context. 

This is so that identifiers that start with an uppercase letter are exported, and those without are not.

For the struct and elements within it to be exported, the struct name and the elements within it must begin with an uppercase letter.

When working with structs, it is important to understand the difference between pointer and value references. This is also relevant in Hour 8, where you add methods to a struct.

. A pointer holds the memory address of a value, meaning that when it is referenced, it is possible to read or write the value that has been stored. When a struct is initialized, memory is allocated for the data elements and their default values in the struct. A pointer to this memory is returned, and this is assigned to a variable name. Using shorthand variable assignment, memory is allocated, and default values are assigned.
a := Drink{}

￼ Using fmt.Printf, the underlying memory addresses of a and b are printed to the terminal. These show that each variable has a separate memory address.

4. You should see the following text printed to the terminal:
{Name:Lemonade Ice:false}
{Name:Lemonade Ice:true}
0xc42000a1e0
0xc42000a200

To be able to modify values contained in the original instantiation of a struct, a pointer should be used. A pointer is a reference to the memory address, so rather than operating on a copy of the struct, modifications will update any variable referencing the underlying memory. 

18:      b := &a
19:      b.Ice = false

The difference between pointers and values is a subtle one, but choosing whether to use a pointer or a value is simple. If you need to modify (or mutate) the original initialization of a struct, use a pointer. If you need to operate on a struct, but do not want to modify the original initialization of a struct, use a value.

You saw how to define a custom value on creation through a constructor function. You learned about exported values and the difference between pointers and values in relation to structs. 

Q. I have seen three different ways of creating a struct. Which one should I use?
A. The recommended way of creating an instance of a struct is to use the shorthand variable assignment method :=. Using new and a variable is also valid, but less commonly used.

 How many levels can I nest structs?
A. There is no limit as to how many levels of structs can be nested. Modeling data in deeply nested structures is probably a sign, though, that another data structure would be better suited.


Q. Can I use any data type in a struct?
A. Yes, any data type may be used in a struct, including any custom types.

The workshop contains quiz questions and exercises to help you solidify your understanding of the material covered. Try to answer all questions before looking at the “Answers” section that follows.

### Hour 8. Creating Methods and Interfaces

A method is similar to a function with one simple addition. A method augments a function by adding an extra parameter section immediately after the func keyword that accepts a single argument. 

Notice that the method takes an additional parameter immediately after the func keyword that is called a receiver. Technically, a method receiver is a type, and in this case it is a pointer to the Movie struct. Next comes the method name and any arguments for the method, followed by the return value types.

Why then use a method instead of simply using a function? For example, the following uses a function and is equivalent to the method declaration.


The summary function depends on the Movie struct and vice versa, and there is a direct relationship between the two. 

Where there is a strong relationship between a function and a struct, it makes sense to use a method.

The benefit of using a method is that this only needs to be written once and can then be used on any instance of a struct.


A method set is a set of methods that are available to a data type. Any data type in Go can have a method set associated with it. This allows a data type to have a relationship with methods, as you saw in the Movie struct example. There is no limit to the number of methods in a method set, and it can become a useful way to encapsulate functionality and to create library code.


The benefit of using a method set over just using a function is that the SurfaceArea and Volume methods only need to be written once. If, for example, a bug was found in one of these methods, it would only need to be changed in one place.

 You have seen how methods are a function with a special argument known as a receiver. The receiver can either be a pointer or a value, and the difference here is quite subtle.

Suppose that a struct exists to hold data for a triangle.
type Triangle struct {
    width  float64
    height float64
}

Note that the receiver expects a Triangle struct, and the asterisk specifies that it should be a pointer.
Click here to view code image
func (t *Triangle) area() float64 {
    return 0.5 * (t.width * t.height)
}

What do you think the value of t.base is after running this program?

Methods that take a pointer receiver are able to modify data elements declared within the original declaration of a struct. This is because, rather than operating on a copy of the struct, methods that take a pointer receiver have a reference to the same memory location as the original definition.

The difference between pointers and values is a subtle one, but choosing whether to use a pointer or a value is simple. If you need to modify (or mutate) the original initialization of a struct, use a pointer. If you need to operate on a struct, but do not want to modify the original initialization of a struct, use a value.

You can think of interfaces as a blueprint for a method set, in that an interface describes all the methods in a set but does not implement them. Interfaces are powerful since they act as a specification for a method set, meaning that implementations can be swapped, providing they conform to the expectations of an interface.

An interface describes all the methods of a method set and provides the function signatures for each method. 

Interfaces provide a way to promote code reuse across entities that share the same behavior. 

type Robot interface {
    PowerOn() err
}
The Robot interface contains a single method, PowerOn. The interface also describes the function signature for the PowerOn methods that takes no arguments and returns an error type. From a 
high level, interfaces can also help to reason about code design. It is easy to see without being concerned about an implementation what the surface layer of the design looks like.

So, how is an interface used? As interfaces act as a blueprint for method sets, they must be implemented before being used. Code that satisfies an interface is said to implement it. The Robot interface can be implemented by declaring a method set that satisfies the interface.

conforms to the method set definition of requiring a PowerOn method. It also conforms to the function signature. Note that the R2D2 struct that the method set is attached to has a different set of data fields to the T850 implementation. The PowerOn method is also completely different, although it conforms to the function signature. For the interface, all that matters is that it conforms with implementing the method set with the correct function signatures.


As an interface is a type, it can be passed to a function as an argument. This allows functions to be written that can be reused for multiple implementations of an interface.

For the Robot interface, a function can be written to boot any Robot.
func Boot(r Robot) error {
    return r.PowerOn()
}

This function accepts any implementation of the Robot interface as an argument and returns the result of calling the PowerOn method. This function can be used to boot any Robot, regardless of how the code inside the PowerOn method is actually implemented. Both the T850 and R2D2 Robots can make use of this method.

44:      err := Boot(&r)

Object-oriented programming refers to a programming paradigm where data is modeling in terms of objects that behave in certain ways. Typically, object-oriented languages offer the ability to inherit one object from another.

By defining a database interface, the implementation of the interface becomes more important than the database used. If the implementation satisfies the interface, theoretically any database could be used and can easily be swapped out. The idea that a database interface can include one or more implementations introduces the idea of polymorphism.


During this hour, you learned about methods and interfaces. You should now understand how to declare a method and how methods can promote code reuse and consistency over simply using functions.

. What is the difference between a function and a method?
A. Technically, the only difference between a method and a function is that a method has an additional argument to specify a receiver. This allows methods to be called on a type, promoting code reuse and modularization.


Q. When should I use a pointer reference, and when should I use a value reference?
A. If you need to modify data within the original declaration of a struct, use a pointer. If you want to operate on a copy of the original data, use a value reference.

1. What are some of the benefits of using a method?

1. Methods mean that code only needs to be written once. Methods can enhance usability, consistency, and reliability.

2. Yes. Method sets can be associated with any type in Go.


### Hour 9. Working with Strings

You will learn how to manipulate strings and explore encoding systems behind strings.


Characters preceded by a backslash \ are interpreted as they are in rune literals

Raw string literals are created between back quotes (or backticks), as in ‘hello’. Unlike interpreted strings, backslashes have no special meaning, and Go will interpret the string as it is presented. 

To concatenate (or combine) strings in Go, the + operator may be used to combine variables with a string type. It does not matter whether the string was created using an interpreted or raw string literal. 

Only variables of the string type may be concatenated. Attempting to concatenate an int with a string results in a compiler error, as shown in Listing 9.6.


The error says that the int type cannot be combined with a string. So how do we combine these values? Go offers the strconv package in the standard library and the Itoa method to accomplish this: it converts an int to a string, as shown in Listing 9.7.


using a buffer of empty bytes is a more efficient way to build a string. In Listing 9.8, a string is created with a loop that runs 500 times. Although this could be created with the += operator, using a buffer is much faster.

 9:      var buffer bytes.Buffer
10:
11:      for i := 0; i < 500; i++ {
12:          buffer.WriteString("z")
13:      }
14:
15:      fmt.Println(buffer.String())

1. An empty bytes buffer is initialized and assigned to the variable buffer.
2. A loop runs 500 times and writes the string "z" into the buffer.
3. Once the loop has completed the value, the String() function is called on the buffer to output the result as a string.

One of the most important encoding standards was ASCII, or the American Standard Code for Information Interchange. This standardized the numbers to be used to represent characters within the English alphabet.

The ASCII encoding standard defines how 128 characters should be represented through 7-bit integers,

Do not worry if you do not understand everything. It is enough to understand that numbers map to characters.

In short, it was possible to say “hello” in English, but not ￼ in Japanese.
To respond to this, the Unicode encoding scheme was created in 1987, and it offered support for most character sets on the planet.

To understand strings in more detail and know how to manipulate them, it is important to understand that a string in Go is in effect a read-only slice of bytes. Using Go’s len built-in method, it is possible to see the number of bytes in a string:

Western characters (e.g., a, b, c) generally map to a single byte. The word “hello” has 5 bytes, and because Go strings are a slice of bytes, it is possible to output byte values at a specific position in a string. In the following example, the first byte of the string is returned:
s := "hello"
fmt.Printf(s[0])
//outputs 104

You might think that the character “h” should be the result. But because indexing the string accesses the bytes rather than the character, the byte value is shown in base 10 (or decimal) format. 

Go can format a base 10 value back to a character literal and show the underlining binary format:
s := "hello"
fmt.Println("%!q(MISSING)", s[0])
// outputs 'h;
fmt.Println("%!b(MISSING)", s[0])
// outputs 1101000

Note that strings are really just a slice of bytes, and that this means they can be manipulated as you would any other slice of bytes in Go.

Searching for a Substring in a String
Another common task when working with strings is to search for a substring within a string. The INDEX method provides this, and takes a second argument of a string to search for. If a match is found, the index number of the first match is returned. If no match is found, the value –1 is returned. Remember that the index starts from 0!


The strings package provides many methods for trimming parts of a string. A common task when dealing with input from users or data sources is to ensure there are no leading or trailing spaces. The TrimSpace method provides this

you will understand how to trim leading and trailing white space from a string.

Q. Can I change a string after it is created?
A. A string is immutable in Go, meaning you cannot change it after it has been assigned. If you try to redeclare a variable, a compilation error will occur. You are permitted to concatenate strings using the += assignment operator.

4. UTF-8 was created to provide a standard way to represent almost every character set on the planet.

### Hour 10. Handling Errors

Error handling is an important part of programming robust, reliable, and maintainable code. 

 9:      file, err := ioutil.ReadFile("foo.txt")
10:      if err != nil {
11:          fmt.Println(err)
12:          return
13:      }
14:      fmt.Println("%!s(MISSING)", file)


￼ If an error is returned, this will mean the error type is not nil.
￼ The error is printed, and the program exists.
￼ If there is no error, the contents of the file are printed.

Here is the function definition:
Click here to view code image
func ReadFile(filename string) ([]byte, error)

This means calling the ReadFile function will always return an error value that can be checked. In the first line of the main function in the example, the return values of the ReadFile method are read into two variables, file and err. 

file, err := ioutil.ReadFile("foo.txt")
The := syntax is a short assignment statement in Go that may be used within a function. The Go compiler will infer the types of the variables automatically without needing to explicitly declare them. So in this case, there is no need to tell the compiler that file is a byte slice and err is an error type. This is a handy convenience, and the same code is equivalent.
Click here to view code image
var file []byte
var err error
file, err = ioutil.Readfile("foo.txt")

The convention in Go is that the error type will be nil if there is no error in execution. Thisallows programmers to check whether the execution is completed as expected when they calla method or function.if err != nil {    // something went wrong}

You have seen how to work with errors, but what if you want to create and return errors?The standard library errors package supports creating and manipulating errors.

  err := errors.New("Something went wrong") 9:      if err != nil {10:          fmt.Println(err)11:      }

[插图] If the value is found to not be nil, it is printed.

Formatting ErrorsIn addition to the errors package, the fmt standard library package offers a Errorfmethod that supports formatting the string returned (see Listing 10.3). This allows multiplevalues to be combined into a more meaningful error string and for dynamic error strings tobe created.

 8:      name, role := "Richard Jupp", "Drummer" 9:      err := fmt.Errorf("The %!v(MISSING) %!v(MISSING) quit", role, name)10:      if err != nil {11:          fmt.Println(err)12:      }

With an understanding of how errors arecreated and returned, Listing 10.4 shows an example.

 7:  func Half(numberToHalf int) (int, error) { 8:      if numberToHalf%! (MISSING)!= 0 { 9:          return -1, fmt.Errorf("Cannot half %!v(MISSING)", numberToHalf)10:      }11:      return numberToHalf / 2, nil12:  }

The example shows a function returning an error and the caller handling the error. The codemay be explained as follows:

No config file found. Please create one at ~/.foorc.This is a better error message, because:[插图] It is specific about the problem.[插图] It offers a resolution to the problem.[插图] It is respectful to the user.

panic is a built-in function in Go that stops the ordinary flow of control and beginspanicking, halting the execution of the program.

Execution is halted after panic is called, so the line “This is not executed” is never reached.

The following example is often overused in Go code, and is really saying, “this is probablythe end, my friend, crash the program.” There are times when this is appropriate, but ingeneral avoid doing this:if err != nil {    panic(err)}

There are some scenarios where using panic may be the correct thing to do, however:[插图] The program reaches a state that is unrecoverable. This may mean that the state is lost orthat continuing to execute the program will cause even more problems. In this scenario, thebest thing to do is to crash the program.[插图] A scenario where the error cannot be handled.

3. Note that the program panics and halts execution.

 the caller is responsible for handling errors.

Q. I see the if err != nil pattern everywhere in Go code. This seems like a lot of repetition.Is this the best way?A. Although this may seem like a lot of repetition, being able to check errors is actually afeature of Go, and offers a lot of power to programmers. Although there are techniques andthird-party packages that can reduce repetition, most Go programmers learn to enjoy beingable to handle errors as they want to.

1. Go functions support multiple return values, and the convention is to return errors as thelast return value. This allows errors to be customized and returned to the caller. After that, itis up to the caller what happens with the error. Other languages take a view that all errorsshould crash the application immediately. Go is more flexible, and leaves it to the caller of amethod or function to decide what to do with an error.

3.Panic should be used in situations where a program cannot recover from an error. Itshould be a last resort, and used if a program has reached a state where crashing is themost responsible thing to do.

### Hour 11. Working with Goroutines

You willunderstand the difference between sequential execution and concurrent execution. You willsee how Goroutines can be one way to deal with network latency. You will be introduced tothe difference between concurrency and parallelism and see how Goroutines make programsrun faster.

If a programmer were given the task to encode this process, it would seem reasonable toconsider that one task cannot be completed without the previous one finishing. This processmaps well to the idea of code executing in the order that it appears in a script, where onething happens after another.

 If a program is executed entirely in the sequence, the entire program can halttemporarily if a line takes a long time to complete. This may cause an end user to wait along time for an event to happen.

how long it will take for a network call to complete, or how long it willtake for a file to be read from a disk.

Some code is written to complete this request and handle the response from theweb server. Once the request leaves the application, many things could affect how quicklythe response returns, such as:[插图] Speed of the DNS lookup to find the address of the weather service

[插图] Speed of the Internet connection between the application and the weather service servers[插图] Speed of establishing a connection with the weather service’s servers[插图] Speed that the weather service application responds

Given that all these factors are beyond the control of the initiating application, it is entirelyreasonable to assume that the speed of the response cannot be predicted. Furthermore, it islikely that each request will take a different amount of time to respond. 

[插图] How quickly can the order be taken?[插图] How quickly can the chef take the order?[插图] How quickly can the chef cook the food?

Instead, servers complete tasksconcurrently. This means they can take other orders while a chef cooks the food, and theycan deliver food while other customers choose their meals.

 Rob Pike,one of the creators of Go, came up with an excellent statement for the difference betweenthe two: “Concurrency is about dealing with lots of things at once. Parallelism is about doinglots of things at once.”

If you prefer to use another browser and do not knowhow to open the Developer Tools, enter ‘Developer Tools’ into Google along with the nameof your preferred browser.

 A slow function call can be simulated using time.Sleep, which pauses theexecution of a program for a particular duration. In reality, this might be a slow network callor a long-running function.

[插图] The time.Sleep method pauses the execution for two seconds.[插图] Although the execution is paused, nothing else happens, so the second line in the mainfunction is not executed.

[插图] After two seconds, the slowFunc function completes and prints a line to that effect.

This code can be said to be blocking, as no other execution can occur while the programwaits for slowFunc() to return.

Go provides Goroutines as a way to handle operations concurrently.

The slowFunc function willstill execute, but it will no longer block the execution of other lines in the program.

Using Goroutines is incredibly simple, and is just a case of putting the go keyword beforeany function or method that should be executed by a Goroutine. 

2. Read the code and try to understand what it is doing.

Dealing with network latency is a good example of where Goroutines can be useful—especially when working with third-party websites or services, which can be unpredictable.

The slice can then be iterated over to make a request to the website and print the responsetime.for _, u := range urls {    responseTime(u)}

func responseTime(url string) {    start := time.Now()    res, err := http.Get(url)    if err != nil {        log.Fatal(err)    }    defer res.Body.Close()    elapsed := time.Since(start).Seconds()    fmt.Printf("%!s(MISSING) took %!v(MISSING) seconds \n", url, elapsed)}

Running the example from a server in North America gives very different results.

The example also demonstrates how network latency introduces a problem. The order ofexecution is decided by the order that the URLs are declared in the script. Note thatresponses always come in the same order. This is because requests are executed insequence, so requests can only be completed one at a time.

This is where Goroutines can be used, and Go makes itincredibly easy to make code concurrent. All that needs to be changed in the previousexample is to add the go keyword before the call to the responseTime function.

This means requests to the websites are madeat the same time rather than one after another. When each request completes, it prints theresponse time to the console. Using Goroutines has many advantages for this scenario:

[插图] The time it takes to execute the three requests is shorter, as they are executedconcurrently. There is no need to make the requests one after the other.

[插图] As soon as a response is received, it can be used immediately. There is no need for aprevious request to complete.

Although Goroutines do not remove network latency, they provide a way to makeprogramming more efficient through a single keyword.

 Go offers a single keyword to programmersto deliver this, and it is one of the language’s most powerful and elegant features.

Node.js, for example, uses an event loop tomanage concurrency, while Java uses threads. Web servers such as Apache and Nginx alsohave different approaches to concurrency, with Apache favoring threads and processes, andNginx using an event loop. Do not worry if you do not understand all these terms. The pointis that there are many approaches to solving concurrency, and they use the resources on acomputer in different ways and make it easier or harder to write reliable software.

The creation of a Goroutineuses only a few kilobytes of memory, so many thousands of Goroutines can be createdwithout running out of memory. Goroutines can also be created and destroyed veryefficiently.

Q. Why does a Goroutine return immediately?A. Goroutines return immediately because this is in line with the idea of not blockingexecution. During Hour 12, you will learn about Channels, a way to connect and manageconcurrent Goroutines.

Q. What can I use Goroutines for?A. In any scenario where the order of events is unknown, Goroutines can be a good option.This includes network calls, reading files from a disk, and creating event-driven programssuch as chat applications and games.

1. What are some of the benefits of concurrent execution over sequential execution?

1. Executing code concurrently means that a program may be able to complete more quicklyand return data when it is ready rather than waiting for other parts of a program tocomplete.

3. Some other scenarios where concurrent programming is useful include reading or writingdata from a file on disk, reading or writing data from a network, and reading or writing datafrom a database.

1. If you have time, watch “Concurrency Is Not Parallelism” by Rob Pike, one of the creatorsof Golang (https://www.youtube.com/watch?v=cN_DpYBzKso). The talk is about 30 minutesin duration, but is an excellent introduction to concurrency and how Go approaches it.

### Hour 12. Introducing Channels

Hour 11 introduced you to Goroutines as a way to handle concurrent operations. You sawhow it is possible to program tasks to run concurrently and handle responses when theyreturn. 

Duringthis hour, you will be introduced to Channels, a way to manage communication betweenGoroutines. ~When combined, Channels and Goroutines offer a carefully curated environmentin which to develop concurrent software.

 Channels allow data to flow in and out of Goroutinesand facilitate communication between Goroutines themselves. Go’s approach to concurrencyis neatly encapsulated in a slogan defined in Effective Go.Don’t communicate by sharing memory; share memory by communicating.

In other programming languages, concurrent programming is oftenachieved by using a shared piece of memory between multiple processes or threads. Theshared memory allows programs to synchronize and ensure that execution happens in alogical way. 

Managing shared memory and locks is not an easy task, and many programming languagesexpect an in-depth knowledge of memory and memory management.

Although using shared memory certainly has its place, Go proposes Channels as a way toavoid using shared memory, instead favoring using messages between Goroutines. 

 Messaging allows a pushapproach to orchestrating concurrent events. When an event happens, a message can betriggered and pushed to a receiver. In a shared memory approach, a program must check theshared memory (pulling). For highly dynamic concurrent programming environments, manyfeel that using messaging is a better way to model communication flows.

On line 9 of Listing 12.1, a timer is used to prevent the program from exiting before theGoroutine has returned. While this is fine for an example to introduce Goroutines, usingtimers in more complex concurrent programs is not a good option. In Channels, Golangoffers a way to manage Goroutines and concurrency in general. 

Initializing a Channel is as follows:c := make(chan string)This can be explained as follows:[插图] A variable c is initialized using shorthand variable assignment and is assigned the value tothe right of :=.[插图] The builtin function make is used to initialize a Channel that is denoted by the chankeyword.[插图] After the declaration of chan comes string. This denotes that the Channel will hold dataof the string type. This also means that only string type values can be sent and received onthe Channel.

To send a message to a Channel is as follows:c <- "Hello World"Note the <- syntax. This denotes that the string to the right should be sent to the Channel tothe left. If a Channel is initialized to expect strings, only strings may be sent as messages tothe Channel. Trying to send a message of another type will result in an error.

Receiving a message on a Channel is as follows:msg := <-c

[插图] A Channel c is initialized to take data of the string type.[插图] As in Listing 12.1, a Goroutine is started to execute the slowFunc function.[插图] The slowFunc method takes the Channel as an argument.[插图] The single argument to the slowFunc function specifies a Channel and the data type thatis a string.

[插图] A variable msg is initialized to receive a message from the c Channel. This blocks theprocess until a message has been received, preventing the process from exiting.

[插图] The message is received and printed.[插图] As there is no more execution to complete, the program exits.

TRY IT YOURSELF: Using Channels to Communicate with Goroutines

Using Buffered ChannelsOften, a receiver will be available to receive messages once they are sent. But sometimes, noreceiver is available to receive a message. In this scenario, a buffered Channel can be used.Buffering means that data is held in the Channel until a receiver is ready. Once a receiver isavailable, the message will be delivered. To create a buffered rather than an unbufferedChannel, the builtin make can take an additional argument of a buffer length.

messages := make(chan string, 2)This creates a buffered Channel that can buffer up to two messages. Now two messages canbe added to the Channel—even if there is no receiver. Note that a buffered Channel can onlyhold as many messages as specified; sending more will result in an error.

Messages will be held in the Channel until a receiver is available. Listing 12.3 shows anexample of a buffered Channel receiving two messages and the messages being receivedonce a receiver is available.

Something new in this example is the usage of close. This denotes that the messageChannel is closed and no more messages can be sent to it.close(messages)

[插图] The Channel is passed as an argument to a function called receiver.[插图] The receiver function iterates over the Channel using range, and prints the bufferedmessages in the Channel to the console.

Althoughblocking operations are generally to be avoided in concurrent programming, there are timeswhen code should block.

Setting up a receiver on a Channel is a blocking operation in that it will prevent a functionfrom returning until a message has been received.

If you thought that this program will print a single ping message and exit, you are correct.Once a single message has been received, the blocking operation returns and the programexits.

 t := time.NewTicker(1 * time.Second)10:      for {11:          c <- "ping"12:          <-t.C13:      }

Preventing a Process from Exiting

If a process should receive a certain number of messages before exiting, a for statementincluding an iterator can be used. Once the iterations are complete, the process will exit.for i := 0; i < 5; i++ {    msg := <-messages    fmt.Println(msg)}

You saw how Channels can be passed to functions as arguments and messages sent to theChannels within the function. To further specify how Channels can be used within a function,it is possible to specify whether a Channel should be read-only, write-only, or read-writewhen passing it to a function. The syntax for specifying whether a Channel is read-only,write-only, or read-write is subtly different.

func channelReader(messages <-chan string) {    msg := <-messages    fmt.Println(msg)}

func channelWriter(messages chan<- string) {    messages <- "Hello world"}

If no <- exists, the Channel will be read-write.

Employing the select StatementSuppose there is a scenario where multiple Goroutines and a program should only operate onthe one that returns first. For this scenario, the select statement can be employed.

A select statement creates a series of receivers for Channels and executes whichever onereceives a message first. A select statement looks similar to a switch statement.Click here to view code imagechannel1 := make(chan string)channel2 := make(chan string)select {    case msg1 := <-channel1:        fmt.Println("received", msg1)    case msg2 := <-channel2:        fmt.Println("received", msg2)}

If a message is received on channel1, the first case will be executed. If a message isreceived on channel2, the second case will be executed. The execution is determined by thetiming of the message, with the first message determining which case is executed.

Generically, any other messages received after that will be discarded. Once a message hasbeen received, select stops blocking.

[插图] Two Channels are created that will hold string type data.

[插图] Two functions are created to send messages to these Channels. To simulate the speed ofthe function, the first sleeps for one second and the second for two seconds.

 a timeout case can be added that will be executed if noother cases are executed within half a second.

select {    case msg1 := <-channel1:         fmt.Println("received", msg1)    case msg2 := <-channel2:        fmt.Println("received", msg2)    case <-time.After(500 * time.Millisecond):        fmt.Println("no messages received. giving up.")}

By adding a quit Channel to a select block, a messagecan be sent to a quit Channel to terminate the statement and end blocking. Think of a quitChannel as a kill switch for blocking select statements.

By sending a message to the stop Channel, the select statement can betriggered to stop blocking, return from the loop, and continue execution.

messages := make(chan string)stop := make(chan bool)for {    select {    case <-stop:        return    case msg := <-messages:        fmt.Println(msg)    }}

A select statement within a for loop allows messages to be printed as they are received.As this is a blocking operation, these messages will continue to be printed until the processis manually terminated.for {    select {    case msg := <-messages:        fmt.Println(msg)    }}

In this example, there is no way to exit this program without killing the process, and it willrun indefinitely. A quit Channel allows execution to return from the blocking operation and forthe program to terminate. By creating a stop Channel, the program can send a message tothe stop Channel and break out of the for loop.stop := make(chan bool)for {    select {    case <- stop:        return    case msg := <-messages:        fmt.Println(msg)    }}

Listing 12.7 shows a full example of using a quit Channel. For the purposes of the example, amessage is sent to the quit Channel after a certain amount of time. In reality, this might bedetermined by an unknown event happening elsewhere in an application.

Concurrentprogramming is a large and complex topic, but if you have grasped how Channels supportcommunication with Goroutines, you have achieved a lot!

Q. Can Channels have more than one data type?A. No. Channels can only have one data type. Channels can be created with any type, so it ispossible to use a struct to hold more complex data structures.

Q. What happens if two Channels within a select statement receive a message at the sametime?A. One Channel will be chosen at random and executed. Only the selected one is executed.

Q. If I close a Channel, are buffered messages lost?A. Closing a buffer means that no more messages may be sent to a Channel. Bufferedmessages are retained so they can be read by a receiver.

1. Channels complement Goroutines and offer the ability to communicate between them. 

3. To receive ten messages from a Channel and then exit, a select statement can be usedinside a for loop that iterates ten times.

### Hour 13. Using Packages for Code Reuse

Hour 13. Using Packages for Code Reuse

A Go package is used to group code so it may be imported and used in a Go program. During this hour, you will be introduced to packages and how they can be used to create Go programs. You will also be introduced to dependency management in Go before creating and sharing your own package.

The only requirement for a main package is that it must declare a function main that takes no arguments and returns no value. In short, the main package is the root of your program.

The math package, for example, provides access to a Pi constant (see Listing 13.2).


LISTING 13.2 Accessing Pi Through the Math Package

fmt.Println(math.Pi)

The pattern of importing a package and using the exported identifiers is fundamental to how code reuse works in the standard library and with third-party code. Although it is a simple pattern, it is worth understanding, as it supports a great deal of flexibility and code reuse.


Go package names are short, concise, and evocative. The strings package contains functions for working with strings; the bytes package contains functions for working with bytes. The more you work with Go, the more you will memorize which package to use for a specific task; if you cannot remember, there is intuitive, well-organized documentation available.

Suppose a particular program needs to manipulate some strings. By reading through the list of standard library packages at https://golang.org/pkg/, it is possible to see that the strings package exists. Great! This can be imported and used to build the program. 

The strings package documentation is available at https://golang.org/pkg/strings/.

It also documents that the function takes a string and returns a string:
func ToLower(s string) string
There is also a short description describing what the function does and some example code demonstrating usage. The example can be executed in the browser. If you are interested in understanding the function in more detail, it is possible to jump to the source code for the package.

Before long, a program will reach the limit of the standard library. At this point, a programmer is faced with two choices:
￼ Write some code to solve the problem.
￼ Find a package (or library code) that solves the problem.

By nature, programmers are lazy, and will most likely opt for the second option. However, adding additional dependencies into a program should be considered carefully, as it has implications for the stability and maintainability of a program

There are a few questions to ask when considering using a third-party library:
￼ Do I understand what the code does?
￼ Is the code trustworthy?
￼ How well maintained is the code?
￼ Do I need this library?

￼ It is important to understand what a package does. Good third-party packages have excellent documentation, and often follow the Go Documentation conventions of offering documentation for exported identifiers. Reading the documentation can establish whether a package offers the required functionality.

Remember that packages imported into a program have access to an underlying operating system. Depending on the level of paranoia, trust may be established by looking at the number of other people using the package, through a recommendation from a colleague, or by reading the source code.

￼ The nature of software means that third-party packages will have bugs. Picking an actively maintained third-party package over one that has seen no updates for years means the package is likely to be more stable over time.

[插图] Importing a third-party package increases complexity. Often, an entire package will beimported for a single function. In this case, it is acceptable to copy the function rather thanuse a third-party package.

This takes a string, reverses it, and returns a string.To use a third-party package, it must first be installed using the go get command. This isinstalled by default with Go, and it takes a path to a remote server and installs it locally:Click here to view code imagego get github.com/golang/example/stringutil

Often, a third-party package will depend on other third-party packages. The go getcommand is smart enough to also download these dependencies, so there is no need to dothis manually for each package.

there are numerous considerations:[插图] How do you update a package when a bug fix is available?[插图] How do you specify a version of a package?[插图] How do you share a dependency manifest with other developers?[插图] How do you install dependencies on a build server?The go get command supports updating individual or all packages on a filesystem. Toupdate dependencies for a project, the following may be run from within a project folder:go get -u

The behavior of go get is to pull source code from a remote branch that matches the localbranch. For example, if the local branch is master, it will pull the latest from master.

As a response to this from version 1.5 of Go, the vendor folder was introduced. Thissupports adding third-party modules to a vendor folder within the project root and movingall package files to this folder. Rather than installing a package globally across a machine, itis installed directly into the project. The stringutil package installed earlier may be movedinto a vendor folder. As such, the directory structure to use the stringutil package fromgithub.com/golang/example/stringutil is as follows:

Note that the stringutil package will now only be available within this project, rather thanacross a machine. This approach has some advantages:[插图] Packages may be locked to a specific version by simply copying a version into a projectdirectory.[插图] There is no requirement for build servers to download dependencies, since they are in theproject.

If you are used to package managers, you may see some disadvantages:[插图] Dependencies must be included in a repository.[插图] It is not immediately apparent which version of a package is being used.[插图] Dependencies within a package are not handled.[插图] It is not possible to specify the exact commit or branch in a manifest.

The package will deal with temperatures and provide functions forconverting one temperature format to another.

Remember that any identifier starting with an uppercase letter will be available whenimporting the package. To create private identifiers (variables, functions, etc.), start thename with a lowercase letter.

To test the package, a test file can be created as temperature_test.go, as shown in Listing13.6. You will learn about testing in Hour 15. For now, it is enough to know that this file addstests for the package.

 7:  type temperatureTest struct { 8:      i  float64 9:      expected Temperature10:  }11:12:  var CtoFTests = []temperatureTest{13:      {4.1, 39.38},14:      {10, 50},15:      {–10, 14},16:  }

24:  func TestCtoF(t *testing.T) {25:      for _, tt := range CtoFTests {26:          actual := CtoF(tt.i)27:          if actual != tt.expected {28:              t.Errorf("expected %!v(MISSING), actual %!v(MISSING)", tt.expected, actual)29:          }30:      }31:  }

It is important to think about the users of your package if you are going to make it availableon the Internet. As such, include these three recommended files with your package:[插图] A LICENSE file describing how people may use the code[插图] A README file containing information on the package[插图] A Changelog file detailing changes made to the package

As the creator of the code, it is your choice how you license it. There are many open sourcelicenses available, with some being permissive and others restricted. A list of Open SourceLicenses is available for review at https://opensource.org/licenses.

The README file should contain information on the package, how to install it, and how touse it. You may wish to include information on how to submit contributions to the project. Ifyou are publishing your package to Github, consider writing in Markdown format. Githubautomatically formats markdown files.

The Changelog file should list changes to the package. This might include the addition offeatures or the removal of an API. Often, git tags are used to denote releases so that usersof a library can easily check out a specific version.

The example package from this hour is available at https://github.com/shapeshed/temperature.

This hour introduced you to packages as a way to encapsulate, reuse, and share Go code.You learned how to install packages and use them within Go programs. You saw how to usethe vendor folder to manage dependencies. You understood how to write your ownpackages. Finally, you understood how you can share the packages you create on theInternet.

Q. When should I write code, and when should I use a third-party package?A. This is a difficult question! Especially when you are starting out, it is tempting and oftenefficient to use a third-party package. Over time, as you become more proficient, you mayfind you rely on third-party libraries less. For particular packages such as database drivers, itmakes sense to use a third-party package. For other scenarios, it may make sense to writecode or simply copy parts of third-party packages into your code.

A. Introducing any third-party code into your project introduces a security risk. Go does notoffer package signing, so it is quite possible for a Github account to be compromised andfor an attacker to replace a popular package with some malicious code.

Go does not provide an official tool to move a third-partypackage’s dependencies into the vendor folder. This needs to be done manually or via athird-party dependency management tool.

3. How does the vendor folder work?

1. Identifiers beginning with an uppercase letter are exported, meaning they are availablewhen importing a package

In short, identifiers starting withan uppercase letter are public, and identifiers with a lowercase letter are private.

Packages are installed into your $GOPATH and are availableto programs within your $GOPATH.

3. The vendor folder allows Go to link any packages that are in the vendor folder to aprogram rather than using a globally installed version. This means an exact version of apackage may be used. Several third-party tools add further functionality.

Try to identify exported identifiers. Do not worry if you do not understand all ofthe code, just try to understand how a package works.

From the project page, areyou able to understand what the package does and how to use it? Install the package andcreate a basic example to show that you understand it.

3. Extend the temperature package created in this hour to offer a function to convertdegrees Celsius to kelvins. Add a table test to test a range of values.

### Hour 14. Naming Conventions in Go

This hour explores Go conventions and tools that can help promote writing IdiomaticGo. In reality, Idiomatic Go means following the standard conventions. After reading thishour, you will understand many of the conventions used within the Go community.

Go does notenforce specific conventions around code formatting, but does have a de facto standard thatis widely used and adopted in the community. 

Code formatting styles are a topic of frequent debate among programmers and, for themajority of the time, these debates detract from the code behind the formatting.

Furthermore, maintaining a code base where developers use different styles can lead toconfusing code, arguments around pull requests, and, at worst, errors. JavaScript, forexample, offers a rich and permissive syntax and is frequently the source of debate aroundhow code is written. 

Although there is some standardization within the Node.js community, it is common to see allthree styles used in projects. Frequently, code contributions to Open Source projects need tobe reformatted, as they do not conform with the prevailing convention. It may be difficult tobelieve, but this can be a source of friction on projects without even getting down to thecode.

The compiler does not enforce that code is formatted per the gofmt command, but almostall the Go community use gofmt and expect code to be formatted in this way.

Although some programmers are initially unhappy with the default choices around formatting,the fact that a choice has been made and that it is encapsulated in official code is liberating.

It is strongly recommended that you format your codeaccording to the Go conventions.

Go provides the gofmt tool to help ensure that Go code is formatted in line with expectedconventions. 

 Running gofmt on a file prints the results to standard outputwithout modifying the original file.

Notice that the file has been reformatted and is now more readable, with consistent spacing.To see the difference between a file and the expected conventions, the -d option may beused to show a diff. This shows the difference between the current file and what gofmtexpects.

If you are happy for the gofmt tool to rewrite the file, the -w flag can be used. Thisoverwrites the current file with formatting applied.gofmt -w example01.go

When writing Go code, many text editors have third-party Go plugins that automatically rungofmt when a file is saved and correct the formatting before a file is saved. Many of thesetools also ensure that your text editor is correctly configured to use tabs for indentationrather than spaces, as the Go convention expects.

Using a plugin in your text editor can bean excellent way of using tooling like gofmt without even thinking about it. Most text editorshave a plugin available for Go.

Many of these plugins also go beyond simple formatting; they allow developers to build, test,and run code from within the text editor. Although these plugins are not required to developGo code, they improve productivity, so it’s recommended that you review which Go pluginsare available for your text editor.

It is said that the two hardest things in computer science are caching and naming things.

The convention in Go is to use Camel Case orPascal Case for variable names where two words need to be compounded. Depending onwhether a value should be exported or not, Camel or Pascal case is chosen.var fileName // Camel Casevar FileName // Pascal Case

It is common in Go programs to see short variable names that reference a data type. This isfavored to allow a programmer to focus on logic rather than the variable. In this case, irepresents an Integer data type, s a String data type, and so on. Initially, it may seem thatvariables get lost in logic, but using the ubiquitous use of this convention quickly means thatyou become accustomed to it.

var i int = 3var s string = "hello"var b bool = true

Good naming can also help code become more readable. During Hour 4 you learned aboutfunction signatures, and later in Hour 8 you learned about methods. Once you are familiarwith how function signatures work, a good approach to naming can become self-documenting code for functions and methods. Consider the two following functionsignatures:Click here to view code imagefunc a(f float64) float64func (t *Triangle) Area() float64

The first example favors brevity in the function and variable name, but without looking at thefunction signature in context, it is difficult to understand what this function does. The secondexample is a method that uses both a good name for a receiver argument. It is clear that themethod is working with a triangle! The function name is also clear in what it is going to do.Given the receiver and function name, it is immediately clear from the function signature thatit computes the area of a triangle.

 Typically, in the Go source code interfaces arenamed with verbs and an “er” suffix to form a noun. The “er” suffix generally signifies anaction, so the naming indicates an action. Some examples are Reader, Writer, andByteWriter. Sometimes, names created this way are not even in the English language. Searchthe Go source code and you will find interface names like “Twoer”.

For exported functions, the convention is to respect the fact that when a package isimported, it will be accessed through the package name and then the exported function. 

Naming will always be a somewhat subjective exercise, but it is worth spending some timeconsidering how you will name things. Some things to consider when naming variables,functions, and interfaces include:[插图] Who will be using this code? Is it just me or a wider team?[插图] Are there any conventions established in the project?[插图] Could someone who is new to the code read it and understand roughly what it is doing?

Consider the context in which you are writing the code, theother people, and the dynamics of a team. For the majority of cases, it should be possible tosatisfy your own conventions and the context in which the code is being used.

golint is provided as part of the official tooling around Go. Whereas gofmt formats codeinto expected conventions, the golint command looks for style mistakes in terms of theconventions of the Go project itself. The golint executable is not installed by default butcan be installed as follows:Click here to view code imagego get -u github.com/golang/lint/golint

To check that the tool was installed correctly, type golint --help at the terminal. Youshould see some help text printed to the terminal. The golint tool provides useful hints onstyle and can also help with learning the accepted conventions of the Go ecosystem. 

This code compiles and passes gofmt. According to the compiler the code is correct, andaccording to gofmt it is correctly formatted. But, as you are probably aware, there are somestyle problems with this code. You may notice that a variable name uses an underscore, forexample. These types of style errors can be found using the golint tool. Running the codeagainst golint gives some advice on how the code style could be improved.

example02.go:9:2: don't use underscores in Go names; var a_string should be aString

Theserecommendations are not mandatory, as the code will compile, but given that the Go projectitself uses this tool, it is a good idea to try and fix the warnings and learn the conventions.Using the golint tool can also be a great way to learn to learn how to write Idiomatic Go.

The tool covers many conventions, including naming, styles, and general conventions. It isrecommended to use golint in your Go projects. Many of the text editor plugins provide away to run golint in a project on save, and you may want to consider using this when yourun tests on your project or just to run this periodically to improve your learning.

go lint example02.goexample02.go:5:7: exported const Foo should have comment or be unexportedexample02.go:9:2: don't use underscores in Go names; var a_string should be aString

As you develop more complex programs, documenting code becomes an important part ofwriting good software. Even if you are working on your own, comments in code can quicklyhelp to understand what a piece of code is doing. Go has excellent support for writingdocumentation, and the authors of Golang have thought carefully about makingdocumentation as easy as possible. The godoc tool is an official tool that can parse Gosource code and produce documentation from both the code and comments within it. As thecode itself creates the documentation, it is unlikely that documentation will get out ofsynchronization, a common problem for software projects.

go get golang.org/x/tools/cmd/godocTo check that it installed correctly, type godoc --help at the terminal. You should seesome help text for the tool.

Java has javadoc for writing comments, andListing 14.4 shows javadoc being used to document some code.

In this Java code, some fields require a @ before them so that javadoc knows where to lookfor specific pieces of data like the author and version. The godoc tool has no suchrequirements, and commenting code is just a case of using standard comments and adheringto a few simple conventions. To add a comment about a section of code, simply begin theline with the name of the element that you are commenting on. 

 8:  // Animal specifies an animal 9:  type Animal struct {10:      Name string // Name holds the name of a thing.11:12:      // Age holds the name of a thing.13:      Age int14:  }

Using godoc conventions means that code has a standard approach todocumentation and that producing and maintaining documentation for code becomessomething that happens by default.

Once you have installed godoc, the documentation for any of the standardlibraries is available from the terminal. To view documentation for the strings package, forexample, the following command can be run.godoc stringsThis outputs documentation for the strings standard library to standard output. If you areon a UNIX type system, you can use this to quickly find the function signature on a methodthat you want to use by combining it with grep.

Furthermore, you can even launch a web server to review standard library documentation.This can be useful if you are offline or have limited connectivity.godoc -http=":6060"After running this command, documentation for standard libraries is available via a browserat http://localhost:6060/pkg/.

If you are offline, you can also use the godoc tool to view documentation on athird-party project. 

Although developers are free to use their own conventions, godoc is a de facto standard,and it provides a standardized, low-friction way for writing and reading documentation.

Automating Workflow

During this hour, you learned about using gofmt, golint, and godoc. These are all excellenttools, but remembering to run them can be a problem. Even if you know the tools and how touse them, you sometimes might forget to use them. As such, it is recommended that wherepossible, you automate tooling to check code.

A Makefile is a tool to simplify compiling, testing, and linting of source code. Makefilesare useful in that they can combine a number of commands in sequence. Furthermore, theycan be used in Continuous Integration environments to automatically run tests, lint code, andcompile code.

Listing 14.6 shows a Makefile that runs gofmt on all files in a folder. If any files are foundto not be formatted correctly, the path is printed to the terminal.

To run this script, simply type make. If any of the files in the directory are incorrectlyformatted, these will be printed to the terminal. 

MakeThe following files need to be formatted:example04.gomake: *** [Makefile:4: check-gofmt] Error 1

Try It Yourself: Using a Makefile to Automate Workflow

This hour introduced you to Go conventions and official Go tooling. Although these are notnecessary for writing Go, they provide an excellent environment for writing and sharing code.It is recommended that you adhere to the conventions expressed in official tooling. Thisincludes the gofmt tool, a tool for formatting Go files, the golint tool that offers advice onstyle and conventions, and godoc, a tool for creating and reading documentation. Byadhering to Go conventions, you do not need to worry about style and different opinions, andyou just get on with the important bit—writing code.

A. Although you may not like some of the choices that have been made, the benefits of theconventions mean that everyone agrees. In time, you will forget about it.

Q. I am used to using all uppercase letters for a constant variable (e.g., CONSTANT).Should I use this in Go?A. Constants in Go are treated the same way as any other data element, so all-uppercaseletters should not be used.

1. If code is not formatted according to gofmt, will it run?

2. Documentation can help with readability and maintainability of code. It allows a singledeveloper to revisit code and quickly understand it, and for other team members to get up tospeed more quickly.

The idiomatic way of naming interfaces is to use a verb with “er” on the end. Thisdescribes what the interface does. Examples include Parser and Authorizer.

Find a popular Open Source Go project on the Internet and download the source code.Run the tools that you have learned in this hour against the code.

### Hour 15. Testing and Performance

In this hour, you will be introduced to the importance of testing in Go. You will write tests for Go programs, see why testing is important, and learn the different types of tests that you can run. You will also learn several useful patterns in Go testing to benchmark code. By the end of this hour, you will be able to test and benchmark Go code.


Furthermore, a developer can run tests each time a code change occurs, and have confidence that bugs and regressions have not been introduced. Testing software also allows a software engineer to declare the expected way a program should work.

Often, software tests are derived from a User Story or Specification that outlines how a feature should work.

 Some projects also mandate that new code has an accompanying test.


Well-written tests can act as documentation. Since a test describes the expected way a program should run, developers joining a project can often read tests as a way to understand how a program operates.


Several types of tests are commonly used.
￼ Unit Tests
￼ Functional Tests
￼ Integration Tests

Unit tests cover small parts of a code base and test these in isolation. Often, this might be a single function, and the inputs and outputs of a function are tested. A typical unit test might say, “If I give function x these values, I should expect this value to be returned.”

A regression is a bug or fault that has been introduced as a result of a change. Regressions mean that code was working before the change, but not after it. Unit tests can often catch regressions as they test the smallest parts of a program.

Integration tests typically test how the various parts of an application work together. If unit tests verify the smallest parts of a program, integration tests look at how the components of an application work together. Integration tests also test things like network calls and database connections, verifying that the system as a whole works as expected.

Typically, integration tests are more complex and difficult to construct than unit tests, as the tests need to assess dependent parts of an application.


Functional tests are often known as end-to-end tests and outside-in tests. These testsverify that software works as expected from an end-user perspective. They assess how aprogram works from the outside without being concerned with how the software worksinternally. 

[插图] Running outside-in tests against an API to check response codes and headers.

Many developers advocate using test-driven development (TDD). This is the practice ofthinking about a new feature in terms of a test. Before any code is written, a test is createdthat describes the expected functionality of a piece of code. This has many benefits.

[插图] It can help to inform code design; thinking clearly about how a piece of code works canoften improve the design.[插图] It can help to provide definition about how a feature should work.[插图] There is a test that will verify there are no regressions in the future.[插图] There is a test to verify that the code is correctly implemented.

Using TDD software, engineers can improve code design and confirm that the code isfunctional by ensuring that the test passes.

They arenot placed in a separate test directory. Instead, they are placed in the same directory as thecode that should be tested. Test files have the same name as the file they are testing, withthe addition of a _test suffix. So, a file to test the strings.go package is calledstrings_test.go, and is placed in the same directory as the strings.go file.

The second convention is that tests are functions that begin with the word “Test”.

[插图] The T type is passed to the function, which includes many functions to test code.[插图] An if statement evaluates whether true is not equal to true.[插图] The if statement evaluates to true, so the test failure is ignored.

The go test gives some helpful information about the test failure. The name of the test, thefile name, and the line number where the failure occurred are all shown. This can help withrecovering from test failures, as it is immediately clear where to look.

Another convention used in testing a Go package is to set up two variables: got and want.These represent the value to be tested and the expected value. 

   got := Greeting("George") 7:      want := "Hello George" 8:      if got != want { 9:          t.Fatalf("Expected %!q(MISSING), got %!q(MISSING)", want, got)10:      }

 The got variable is assigned the return value from the Greeting function, and representsthe value to test.[插图] A second variable want expresses the expected output.[插图] An if statement evaluates that got and want are the same. If they are not, an error isthrown.

Using the got want pattern is useful, as it is quickly evident why the test has failed.Furthermore, adding helpful error messages when a failure occurs can speed the ability to fixa failure.

For this scenario, Go has a pattern of table tests that allow many conditions to be tested.Listing 15.5 shows this example reworked to use a table test.

 5:  type GreetingTest struct { 6:      name   string 7:      locale string 8:      want   string 9:  }10:11:  var greetingTests = []GreetingTest{12:      {"George", "en-US", "Hello George"},13:      {"Chloé", "fr-FR", "Bonjour Chloé"},14:      {"Giuseppe", "it-IT", "Ciao Guiseppe"},15:  }

18:      for _, test := range greetingTests {19:          got := Greeting(test.name, test.locale)20:          if got != test.want {21:          t.Errorf("Greeting(%!s(MISSING),%!s(MISSING)) = %!v(MISSING); want %!v(MISSING)", test.name, test.locale,    actual, test.want)22:      }

[插图] A struct is created to hold the necessary data for constructing a test. This includes theinputs and the expected output.[插图] A slice of structs is created to hold all of the scenarios to test, including the expectedoutputs.[插图] Within the test, a range loops over all of the structs within the slice and tests each forequality.[插图] If any of the tests fail, a message is printed to the console.

The advice given in Hour9 was that the most performant method is to use buffers. Can this be proven to be true?

Do not be overly concerned about thefunctions, as the performance aspect is this hour’s focus.

24:  func StringFromBuffer(j int) string {25:      var buffer bytes.Buffer26:      for i := 0; i < j; i++ {27:          buffer.WriteString("a")28:      }29:      return buffer.String()30:  }

The testing package includes a powerful benchmarking framework that allows a function tobe run repeatedly and a benchmark to be applied. The number of times a function is run isnot set, but is adjusted by the benchmarking framework to have a reliable data set. At theend of the benchmark, a report is given on the number of operations that were complete pernanosecond.

Benchmark tests start with the Benchmark keyword and take the B type as an argument thatprovides functions to benchmark code. Listing 15.7 shows benchmark tests for the threedifferent techniques.

 5:  func BenchmarkStringFromAssignment(b *testing.B) { 6:      for n := 0; n < b.N; n++ { 7:          StringFromAssignment(100) 8:      } 9:  }

To run these tests, the -bench flag must be passed to the go testcommand.Click here to view code imagego test -bench=.BenchmarkStringFromAssignment-4           200000              5330 ns/op

 To help with this scenario, the go test tool provides a-cover flag that outputs a test coverage percentage.Click here to view code imagego test –cover example06.goPASScoverage: 50.0%!o(MISSING)f statements

You learned about the importance oftesting code and the difference between unit, functional, and integration tests. You wereintroduced to TDD, a technique that involves writing a failing test before you write code. 

You learned howto write table tests to support testing multiple inputs. You then understood how tobenchmark code before understanding how to see test coverage on a codebase. You haveunderstood how to test Go code, an important part of being an effective Go programmer

Although it mightseem like an overhead, writing tests has many benefits. It helps to reason about code,ensures that code is correctly implemented, and identifies regressions.

Around 80 percent is a good level, depending on the complexity of a project. 

2. Test-driven development is the practice of writing a failing test to describe the expectedfunctionality before writing any code. Many developers argue that this helps with codedesign and ensures that test coverage is an integral part of the development process.

Using benchmarks means that a repeatable test can be used to show that one function isfaster than another, or that performance has increased or decreased as a result of a codechange.

### Hour 16. Debugging

. As you begin to develop more complex programs in Go, debugging will becomepart of your daily workflow.

Many commonapplications provide logging capabilities. These logs can be useful to monitor the health ofan application, track down issues, and identify problems.

Web servers like Nginx (https://www.nginx.com/)write logs to files as they run, recording both an access log and an error log. Serveradministrators are able to use these logs to debug issues or just to check that everything isrunning smoothly. As these files are text files, it can be easy to find and understand an eventor an error.

If an access log shows many requests from a single IPaddress in a short period of time, there is probably something untoward going on. Action canthen be taken to ban requests from the offending IP address from using the server.

The Nginx web server also writes to a separate file when it considers that an error hasoccurred.

The log shows that the request wasforbidden by a rule on the server, so in fact everything is ok.

Without any logging, it would be difficult to debug an issue. Furthermore,logging can help with the daily administration of an application by ensuring that a program isrunning as expected.

Note that the date and time is added to the log message, which can be useful for reviewinglogs at a later time. The log package can also log errors in scenarios where fatal errorsoccur.

Writinglogs to a file is as simple as instructing the log package to write logs to a file.

err := os.OpenFile("example03.log", os.O_APPEND|os.O_CREATE|os.O_RDWR, 0666)if err != nil {        log.Fatal(err)}defer f.Close()log.SetOutput(f)

The final line directs the log package to use the example03.log file to record the logs of anapplication. 

Understanding how to Write Logs to a File

2. Try to understand what the code is doing.3. From the terminal, run go run example03.go.4. In the same folder, you will find a new file example03.log.

Running this program in the normal way outputs five messages to the terminal. Using shellredirection, however, the output can be written to a file. This works for both Linux andWindows.Click here to view code imagego run example04.go > example04.log 2>&1

Typically, it is better to use an operating system to redirect logs to a file rather than redirectto a file through Go code. This is more flexible and allows other tools to consume logs ifdesired.

11:      reader := bufio.NewReader(os.Stdin)12:      fmt.Print("Guess the name of my pet to win a prize: ")13:      text, _ := reader.ReadString('\n')14:      text = strings.Replace(text, "\n", "", -1)

Note at the end of the string is the \n character. This is used to denote a new line, as bydefault the Printf function does not add a carriage return. The %!v(MISSING) verb is the defaultformat of a type.

the v verb can be used with an additional + to print the field nameswithin a struct. This is useful, as there is no need to look up field names or remember theorder in which field names occur in a struct.

fmt.Printf("%!v(MISSING)\n", someStruct)Listing 16.8 shows a struct being printed to the console, both with and without the fieldnames.

 Delve (https://github.com/derekparker/delve) is one such project, and provides a richdebugging environment for Go projects.

you will understand how to debug Go code using Delve.

If you are on a UNIX type system (MacOS or Linux), the GNU Debugger is available to debugGolang programs. The GNU Debugger is widely available and almost always available toinstall via package managers. The GNU Debugger operates on binary files, so a Go binaryneeds to be compiled.

The GNU Debugger offers a rich debugging environment, although it is not specific to Go. Itis possible to be extremely granular and even step through a program line by line.

Although this hour introduced you to someuseful tooling for debugging, the best way to learn is to have a real bug and to debug it!With the tools that you have learned in this hour, you should have everything you need to getstarted.

For quick debugging, oftenusing fmt to print the value of a variable can resolve a bug. As programs become morecomplex, however, a more fully featured debugger may be necessary.

If a bug is found, it is a good idea towrite a failing test for it. As you debug your code, you can run the test to see if it is fixed.This has the added benefit that if the program code changes in the future and reintroducesthe bug, there is a test to cover it.

3. What are the advantages and disadvantages or using logging over a tool like Delve?

1. Logging can provide useful information when a bug is found in a program. As a programwrites logs throughout the execution of a program, this can help with debugging before abug is known because a log file will contain the state of a program when the bug happened.Occasionally, logs can provide an answer immediately.

 but for simple debugging, using logging or printing values to theterminal may be sufficient.

### Hour 17. Using Command-Line Programs

A command-line program (sometimes also called a command-line utility or tool) is aprogram designed to be run from a terminal.

Today, command-line utilities continue to be a popular and practical way forprogrammers and systems administrators to interact with an underlying operating system. Aprogrammer might want to create a command-line program to:[插图] Create a script that can be run automatically on a regular basis.[插图] Create a script to interact with files on an operating system.[插图] Create a script to perform system maintenance tasks.[插图] Avoid the unnecessary overhead of designing a graphical interface.

Some examples of scripts that can beautomatically run by an operating system include a program that is run:[插图] every minute to fetch data from a web service,[插图] every hour to remove temporary files,[插图] every day to back up a database,[插图] every month to complete system maintenance tasks,[插图] every year to remind you it is your birthday.

Standard Input is data given to a command-line program. This can be a file, or just a stringof text given to a program. 

Go offers great support for creating command-line programs. It conforms to the idea oftaking inputs and sending outputs, and often Go will automatically ensure that output is sentto the correct output stream. Data passed to a command-line program on the command lineis known as arguments. 

 8:  func main() { 9:      for i, arg := range os.Args {10:          fmt.Println("argument", i, "is", arg)11:      }12:  }

The Args method returns a slice of strings containing the name of the program and anyarguments passed to it. In the example, range is used to iterate over the arguments andprint them to the terminal. 

[插图] The ability to specify the type of values passed as arguments[插图] The ability to set default values for a flag[插图] Automatically generated help text

 9:      s := flag.String("s", "Hello world", "String help text")10:      flag.Parse()11:      fmt.Println("value of s:", *s)

[插图]flag.Parse is called so that the program may pass arguments once they have beendeclared.[插图] Finally, the value of s is printed. Note that the return value of flag.String is a pointer, sothe * operator is used to dereference it and show the underlying value.

The flag package creates some help text automatically that will be returned by eitherpassing any of the following:[插图]-h[插图]--h[插图]-help[插图]--helpThe help text is formatted and provides the end user with the expected type of argumentvalues.

If a user of the command-line program passes a value that does not map to the requiredtype, an error will be shown. In the following example, a string value “String” is passed to the-i flag. As this expects an integer value, an error is thrown. The default behavior of theflag package is to print the error followed by help text.

$ ./example03 -i Stringinvalid value "String" for flag -i: strconv.ParseInt: parsing "String": invalid syntax  Usage of ./example03:    -b    Bool help text

Although the flags package generates help text automatically, it is quite possible to overridethe default help format and provide custom help text. The Usage variable expects a functionand is called whenever an error occurs while parsing flags. A simple implementation is asfollows:Click here to view code imageflag.Usage = func() {    fmt.Fprintln(os.Stderr, "hello world")}

Note that the os standard library package is used to print the message to Standard Error, asthis message is shown when there is a parsing error, but the output is completelycustomizable. 

By using a raw string literal (anything between ``), the formatting will be preserved. Buildingand running the program now shows the custom help text.

Many command-line programs support subcommands. A good example of this is the gitcommand. This has a top-level command git and then multiple sub-level commands thateach have their own options and help text. Some examples of subcommands in git are asfollows:git clonegit branch

Running these commands with --help, it is possible to see that each subcommand has adifferent and independent option. The flag package is able to offer support forsubcommands through FlagSets. This supports creating independent sets of flags thatmay, among other things, be used to create subcommands. Flagsets and flags assigned tothem are as follows:Click here to view code imagecloneCmd := flag.NewFlagSet("clone", flag.ExitOnError)

The first argument is the name and defines the command name. The second argumentdefines the error handline behavior.[插图]flag.ContinueOnError—if there is a parsing error, continue execution.[插图]flag.ExitOnError—if there is a parsing error, exit with a status code of 2.[插图]flag.PanicOnError—if there is a parsing error, panic.

switch os.Args[1] {    case "clone":    // handle clone subcommand here    case "branch":    // handle branch subcommand here    default:    // handle everything else here}

[插图] A switch statement reads in the first argument to the command.

Now if users simply type the command with no arguments, they will get some useful help textto start using the program. 

LISTING 17.6Using Subcommands in a Command-Line Program

24:      uppercaseCmd := flag.NewFlagSet("uppercase", flag.ExitOnError)25:      lowercaseCmd := flag.NewFlagSet("lowercase", flag.ExitOnError)

The advantage of following Go conventions is that you may now push your code to Githuband let others easily install it with:Click here to view code imagego get github.com/[your github username]/helloworld

 You learned how toaccess raw arguments using the os package before understanding how to parse command-line arguments using the flag package. You learned how to customize help text, and thenlearned how to create subcommands within a command-line tool. Finally, you learned how toinstall and share a command-line tool.

Q. Why does Go treat-option and --option as the same?A. Although single dash and double dash options are generally different, Go authors decidedto make them the same.

Q. Is it safe to install a command-line program from someone else using go install?A. Check the contents of a package before installing and running it. A Go program has a lotof access to your operating system, so be careful. Imagine that you are installing maliciouscode and try to do some research to make certain that it is safe. Do you understand what itdoes? Is the program widely used? Do you know other people that use it?

1. Command-line scripts that want to write an error should use Standard Error. Sending textto standard error using the fmt and os packages may be achieved as follows:Click here to view code imagefmt.Fprintln(os.Stderr, "Something went wrong")

3.go install is used to install local packages on your machine. These might be files thatyou have written or files you have gathered from the Internet or a fileserver. go installfetches files from a remote server (like Github) and installs them, like go install does. Thecommands are roughly equivalent, apart from the fact that go get downloads files as well.

### Hour 18. Creating HTTP Servers

Responding with different content types[插图]Responding to different types of requests

Go offers strong support for creating web servers that can serve web pages, web services,and files. During this hour, you will learn how to create web servers that can respond todifferent routes, different types of requests, and different content types.

 7:  func helloWorld(w http.ResponseWriter, r *http.Request) { 8:      w.Write([]byte("Hello World\n")) 9:  }

[插图] Within the main function, a route / is created using the HandleFunc method. This takes apattern describing a path, followed by a function that defines how to respond to a request tothat path.

[插图] The helloWorld function takes a http.ResponseWriter and a pointer to the request.This means that within the function, the request can be examined or manipulated beforereturning a response to the client. In this case, the Write method is used to write theresponse. This writes the HTTP response including status, headers, and the body. The usageof []byte initializes a byte slice and converts the string value into bytes. This means it canbe used by the Write method, which expects a slice of bytes.

 HandleFunc sets up a routing table that allows the HTTP server to respond correctly.Click here to view code imagehttp.HandleFunc("/", helloWorld)http.HandleFunc("/users/", usersHandler)

In this example, whenever a request is made to /, the helloWorld function will be called.Whenever a request is made to /users/, the usersHandler function will be called, and soon.

[插图] The default router directs any request that it does not have a handler for to /.[插图] The route must match exactly; for example, a request to /users will go to /, as it ismissing a trailing slash.

Handler functions have access to the Request and the Response, so a common pattern isto complete everything needed for the request before writing the response back to the client.

As the response has already been written, this will haveno effect, but the code will compile. The key point: Write the response last.

   w.Write([]byte("Hello World\n"))     // This has no effect, as the response is already written     w.Header().Set("X-My-Header", "I am setting a header!")

 8:     if r.URL.Path != "/" { 9:              http.NotFound(w, r)10:              return11:     }12:     w.Write([]byte("Hello World\n"))

[插图] If it is not, the NotFound method from the http package is called, passing the responseand request. This writes a 404 response to the client.

Through the ResponseWriter, a handler function can add a header as follows:Click here to view code imagew.Header().Set("Content-Type", "application/json; charset=utf-8")Provided this is before the response is written to the client, the header will be added to theresponse. 

    w.Header().Set("Content-Type", "application/json; charset=utf-8")13:      w.Write([]byte(`{"hello": "world"}`))

HTTP servers typically respond to clients with multiple content types. Some content types incommon usage include text/plain, text/html, application/json, andapplication/xml. If a server supports multiple content types, a client may request acontent type using an Accept header. This means the same URL can serve a browser withHTML or an API client with JSON. 

12:      switch r.Header.Get("Accept") {13:      case "application/json":14:              w.Header().Set("Content-Type", "application/json; charset=utf-8")15:              w.Write([]byte(`{"message": "Hello World"}`))16:     case "application/xml":17:              w.Header().Set("Content-Type", "application/xml; charset=utf-8")18:              w.Write([]byte(`<?xml version="1.0" encoding="utf-    8"?><Message>Hello World</Message>`)19:     default:20:              w.Header().Set("Content-Type", "text/plain; charset=utf-8")21:              w.Write([]byte("Hello World\n"))22:      }

[插图] Depending on the contents of the Accept header, a switch statement is used to set theresponse accordingly.[插图] If no header is found, the server defaults to sending a plain text response.

curl -si -H 'Accept: application/json' http://localhost:8000

12:      switch r.Method {13:      case "GET":14:              w.Write([]byte("Received a GET request\n"))15:      case "POST":16:              w.Write([]byte("Received a POST request\n"))17:      default:18:              w.WriteHeader(http.StatusNotImplemented)19:              w.Write([]byte(http.StatusText(http.StatusNotImplemented)) + "\n")20:      }

[插图] If the method is not a GET or a POST, it falls through to the default. This sends a 501 NotImplemented HTTP response. The 501 code means the server does not understand or doesnot support the HTTP method sent by the client.

3. Using curl, make a request to the web server using a GET request. Note that the -Xoption is used to set the type of request:Click here to view code imagecurl -si -X GET http://localhost:8000

curl -si -X POST http://localhost:8000

In Go, the query stringparameters for a request are available as a map of strings, and these can be iterated overusing a range clause.

func queryParams(w http.ResponseWriter, r *http.Request) {    for k, v := range r.URL.Query() {        fmt.Printf("%!s(MISSING): %!s(MISSING)\n", k, v)    }}

For a POST request, data is usually sent as the body of a request. This data may be read andused as follows:Click here to view code imagefunc queryParams(w http.ResponseWriter, r *http.Request) {    reqBody, err := ioutil.ReadAll(r.Body)    if err != nil {        log.Fatal(err)    }    fmt.Printf("%!s(MISSING)", reqBody)}

Warning: Do Not Trust User InputAn aside on security for a moment: Data received on a server should be considereduntrusted. An attacker may send a request that attempts to steal information, gain access toa server, or delete a database. All data coming into a server should be considered unsafeand should be filtered before use.

    fmt.Printf("%!s(MISSING)\n", reqBody)28:              w.Write([]byte("Received a POST request\n"))

A. The http package by default uses ServeMux to handle routing, and neither variables norregular expressions are supported. Some popular community-created routers offer variablesand other features like request and content types. Generally, they integrate with the httppackage.

3. No. You should never trust data submitted by a client to a server. Data should be sanitizedbefore use.

### Hour 19. Creating HTTP Clients with Go

you learn how to create HTTP clients using Go. You willlearn how to make different types of requests and how to debug your program duringdevelopment.

A good way to understand the structure of an HTTP request is to use curl. You used curlin Hour 18 to work with HTTP servers. It is also a useful client for working with HTTP clients!

The output of the curl command in verbose mode describes an HTTP request and responsecycle between a server and a client. 

If you do not have access to curl, the nature of HTTP requests may also be examinedthrough Google Chrome Developer Tools. 

For simple GET requests, Go provides a shorthand method in the net/http package to make GET requests. Using this method means there is no need to be concerned about configuring the HTTP client or setting request headers. The default configuration is good, and if the requirement is to fetch some data from a remote server, it works well.


 If there is an error from this request (e.g., no network), the error is logged, and the scriptexits.

[插图] If there is an error in reading the response body, the error is logged, and the script exits.[插图] The response body is printed.

Executing this program will make a request to the ifconfig.co service and return the IPaddress that the request was initiated from.

In Listing 19.2, the client will be making a POST request to https://httpbin.org/post.The httpbin service is a tool for testing HTTP clients, and the /post endpoint returns datathat was posted to it along with information on the client.

12:      postData := strings.NewReader(`{ "some": "json" }`)13:      response, err := http.Post("https://httpbin.org/post", "application/json",    postData)14:      if err != nil {15:          log.Fatal(err)16:      }

[插图] A postData variable is initialized, and a JSON string is assigned as the value. This usesthe strings standard library to format it as an io.Reader ready for transmission.

[插图] A POST request is made using the Post method. The first argument is the URL to post to,the second is the Content-Type of the data, and the third is the data.[插图] If there is an error from this request (e.g., no network), the error is logged, and the scriptexits.

￼ If there is an error in reading the response body, the error is logged, and the script exits.

To have further control over an HTTP request, a custom HTTP client should be used. Thedefault HTTP client from the net/http package may be used, and the default settings areautomatically applied unless they are overridden.

11:      client := &http.Client{}12:      request, err := http.NewRequest("GET", "https://ifconfig.co", nil)13:      if err != nil {14:          log.Fatal(err)15:      }16:17:      response, err := client.Do(request)

[插图] The NewRequest method is used to issue a GET request to https://ifconfig.co.[插图] The Do method is used to send the request and handle the response.

Using a custom HTTP client means that custom headers, basic authentication, and cookiesmay be set on a request. Given that the difference between the code needed to make arequest with the shorthand method and a custom HTTP client is trivial, it is recommendedthat for anything other than trivial requirements, a custom HTTP client is used.

the net/http/httputil alsoprovides methods that make debugging HTTP clients and servers easy. TheDumpRequestOut and DumpResponse methods from the net/http/httputil packageprovide a way to view requests and responses.

  debug := os.Getenv("DEBUG")

20:       if debug == "1" {21:           debugRequest, err := httputil.DumpRequestOut(request, true)22:           if err != nil {23:               log.Fatal(err)24:           }25:           fmt.Printf("%!s(MISSING)", debugRequest)26:      }

To request JSON data, the client may be amended to set a header to request JSON. This canbe added to the request as follows.Click here to view code imagerequest.Header.Add("Accept", "application/json")

At alow level, a number of variables affect the speed of the response:[插图] Speed of a DNS lookup.[插图] Speed of opening a TCP socket to the server IP address.[插图] Speed of establishing a TCP connection.[插图] Speed of a TLS handshake if the connection is over TLS.[插图] Speed of sending data to the server.[插图] Speed of any redirects.

[插图] Speed of the web server in returning the response.[插图] Speed of the data being returned to the client.

server does not respond, a request will wait or hang indefinitely. It is recommended that onany request, a timeout is set. If a request is not completed before a timeout, an error isreturned.client := &http.Client{    Timeout: 1 * time.Second,}

This configuration allows the client one second to complete the request. To demonstratethis, working with the previous example, a timeout setting can be applied to the client. In thiscase, a short timeout is applied so the timeout may be seen.

client := &http.Client{    Timeout: 50 * time.Millisecond,}

Go also exposes individual parts of an HTTP transaction via creating a transport ifrequired.Click here to view code imagetr := &http.Transport{    DialContext: (&net.Dialer{        Timeout:   30 * time.Second,        KeepAlive: 30 * time.Second,    }).DialContext,    TLSHandshakeTimeout:   10 * time.Second,    IdleConnTimeout:       90 * time.Second,    ResponseHeaderTimeout: 10 * time.Second,    ExpectContinueTimeout: 1 * time.Second,}client := &http.Client{    Transport: tr,}

1. The Accept header tells a server what type or types of content the client is able toreceive. The Content-Type header is sent by the server to indicate what type of data isbeing sent to the server. If a client asks the server for application/json using the Acceptheader, and the server supports it, data should be returned with the Content-Type headerset to application/json.

2. If an HTTP client is created, a header may be set on the request as follows:Click here to view code imageclient := &http.Client{} request, err := http.NewRequest("GET", "http://www.example.com", nil) request.Header.Add("Connection", "close")

3. Depending on your level of experience with HTTP, the Get and Post methods provide aquick way of getting started. These methods can also be used with creating an HTTP clientand specifying a timeout, as follows:Click here to view code imageclient := &http.Client{           Timeout: 1 * time.Second,}resp, err := client.Get("http://example.com")If you need control over headers and other elements of the request, the NewRequest methodshould be used.

### Hour 20. Working with JSON

JSON was originally created as a subset of JavaScript, but thedata format is independent of the language; indeed, most languages support encoding anddecoding data as JSON. JSON has become a de facto standard for storing and exchangingdata on the Internet, and has largely taken over from XML (Extensible Markup Language).Although many modern data services still offer an XML format, the most common dataformat to be found on the Internet is JSON. If you are doing any programming that consumesor exposes services from the Internet, it is highly likely you will be using JSON.

Listing 20.1 consists entirely of key value pairs, but a key can also have an array of values.The following example shows an array in JSON.{   "name":"George",   "age":40,   "children":[ "Bea", "Fin"]}

Although an array exists in Go, slices are used more commonly to represent a group ofelements. Somewhat confusingly, arrays can also be known as lists in other programminglanguages, but they all share a common definition of a group of single elements.

JSON is typically more lightweight in terms of thenumber of bytes needed to represent the data. For sending data over a network like theInternet, this can mean that applications run slightly faster.

In recent years, many excellent JSON APIs have been created on the Internet. It is nowpossible to have access to a wealth of data on almost any subject using the Internet as adata exchange platform. Instead of connecting directly to a database, APIs allowprogrammers to request data in a range of formats and to use the data.

Application developers have used many of these APIs to create new and interesting productsand services.

The process of encoding means to convert data into an encoded form. For the purposes ofthis hour, this format is JSON. The encoding/json package provides the Marshal functionfor encoding Go data to JSON. 

To encodethis data to JSON, use the Marshal function. This expects an interface and returns a stringof bytes. As a struct may implement an interface, the struct can be passed directly to theMarshal function.Click here to view code imagejsonByteData, err = json.Marshall(p)

After checking for an error, the byte slice can be converted into a string, and the data isconverted to JSON text format.Click here to view code imagejsonStringData := string(jsonByteData)fmt.Println(jsonStringData)

Although the previous example successfully converted data to JSON format, there is oneproblem. The JSON key names all begin with uppercase letters. Although JSON has noofficial standard, the convention is to use CamelCase. Table 20.1 shows the differencebetween the Go variable names and the expected variable names in JSON.

Structs can declare tags on data fields, and if there is a data tag for JSON, the encoder willuse this value for the key. To map correctly to the CamelCase expectations of JSON issimply a case of tagging the fields of the struct correctly.Click here to view code imagetype Person struct {    Name    string   `json:"name"`    Age     int      `json:"age"`    Hobbies []string `json:"hobbies"`}Listing 20.4 shows a full example of using struct tags to create expected JSON keys.

Struct tags can also be used to omit empty fields in a struct from being encoded to JSON.By default, if a struct is initialized with empty values, the ensuing encoded JSON includesvalues that have been assigned according to Go’s zero values.

To omit zero values from encoded JSON, struct tags can be used to indicate that the fieldmay optionally be empty, and that it should not be included if it is empty. This appendsomitempty after the JSON key name.Click here to view code imagetype Person struct {    Name    string   `json:"name,omitempty"`    Age     int      `json:"age,omitempty"`    Hobbies []string `json:"hobbies,omitempty"`}

Decoding JSON is also a common task for network programming. Data may be received froma database, an API call, or from configuration files. As raw JSON is just data in text format, itcan be represented initially in Go as a string. The Unmarshal function expects a slice ofbytes and an interface to decode the data to. Depending on how data is received, it may bepossible to receive it as a slice of bytes. If this is not possible, it must be converted beforepassing it to the Unmarshal function.

var jsonStringData := `{"name":"George","age":40,"hobbies":["Cycling","Cheese","Techno"]}jsonByteData := []byte(jsonStringData)

Just like when encoding JSON, struct tags may be used to tell the decoder to map a key to afield.

 As JSON is a subsetof JavaScript, it follows the same approach of not needing to declare data types. This leadsto some tricky data transformation problems between loosely and strongly typed languages.

When creating structs to decode or encode JSON, be aware of these data types, as theencoding/json package will throw an error if data types are mismatched. 

Running this example results in an error, because the true value is actually a string in JSON,as it is surrounded by quote marks. The Go decoder tries to unmarshal this value into a GoBoolean value, but since the value is a string this is not possible, and a fatal error is thrown.

When retrieving JSON via an HTTP request in Go, data will be received as a stream ratherthan a string or a slice of bytes. 

As the data is a stream, the NewDecoder function from the encoding/json package can beused. This expects an io.Reader, which conveniently is the type returned by http.Get, andreturns a Decoder type. This type decodes the data into a struct. As before, the decoderexpects a struct, so an instance of the struct must be created and passed to the Decodemethod.

22:      err = json.NewDecoder(res.Body).Decode(&u)

A. Yes, you need to create a struct to encode or decode JSON. Although this may seemtedious, particularly for large JSON objects, like the Github example you saw, it does lead tomore robust and fault-tolerant code. If you can map JSON types on Go types, you get thebenefit of type safety as well as using JSON as a data exchange format. There are severalservices online that can automatically create Go structs from JSON data.

A. JSON has shown itself to be a flexible, easy to learn, and expressive data format. The riseof JavaScript as the language of the web means that JSON services are easy to consumewithin a browser. JSON is also smaller than other formats, meaning it can be transferred andstored efficiently.

1. A JavaScript number maps to a float64 in Go.

2. No. It is possible to define a struct to only include fields that you are interested in. Structtags can be used to map JSON fields to Go struct fields.

### Hour 21. Working with Files

[插图]Reading and writing files with the ioutil package

You will be introduced to thedifferences between using the ioutil convenience functions and the os package. Finally,you will see how to use files to manage configuration.

 But, in fact, files offera programmer configuration management, storing the state of a program, and even readingdata from the underlying operating system.

Because Unix exposes system data as a file, the cat command can be used toextract information on the underlying system. One of these virtual files is /proc/loadavg,which shows the current load on the system.cat /proc/loadavg0.31 0.25 0.26 1/227 15992

Working with files is a such a common requirement that the ioutil package is offered aspart of the standard library to provide a shorthand for many of the operations involved inreading and writing files. In fact, this package is mostly just a wrapper for the os module,providing shorter code and removing the need to handle cleanup operations. If any of thefollowing operations are needed and fine-grained control is not needed, the ioutilpackage is a good option:[插图] Reading a file[插图] Listing a directory[插图] Creating a temporary directory

One of the most common file operations is reading. The ioutil package provides theReadfile function to do this, and it expects a filename to be passed to it. The functionreturns the contents of the file as a slice of bytes. 291This means that if the file contents areto be used as a string, the slice of bytes needs to be converted to a string type.

10:      fileBytes, err := ioutil.ReadFile("example01.txt")11:      if err != nil {12:          log.Fatal(err)13:      }14:15:      fmt.Println(fileBytes)16:17:      fileString := string(fileBytes)18:      fmt.Println(fileString)

The ioutil package also provides a convenience function for creating files through theWriteFile function. Although the WriteFile function is designed to write data to a file, itcan also be used to create a file. 

Generally, the default mode for files on Unix type systems is 0644,meaning the owner can read and write, but everyone else can only read. If you are creatingfiles on a filesystem, some thought should be given to the permissions used to create a file.If you are unsure, following the default Unix permissions of 0644 is a good default.

10:      b := make([]byte, 0)11:      err := ioutil.WriteFile("example02.txt", b, 0644)

Passing an empty slice of bytes to the WriteFile method is somewhat of a trick to takeadvantage of using the shorthand functions available in the ioutil package. Because thebehavior of the WriteFile function is to create the file if it does not exist, it can also beused to create an empty file.

To write a string to afile, this must first be converted to a slice of bytes. Listing 21.3 shows a string of text beingwritten to a file. If the file does not exist, it will be created.

10:      s := "Hello World"11:      err := ioutil.WriteFile("example03.txt", []byte(s), 0644)

The ioutil package also provides a convenience function for this in theReadDir, expects to be given a directory as a string, and returns a list of directories sortedby filename. The filenames are a FileInfo type and offer the following pieces ofinformation:[插图]Name: the name of the file[插图]Size: the size in bytes of the file[插图]Mode: a representation of permissions in bits[插图]ModTime: when the file was last modified[插图]IsDir: whether the file is a directory[插图]Sys: underlying data source

10:      files, err := ioutil.ReadDir(".")11:      if err != nil {12:          log.Fatal(err)13:      }14:15:      for _, file := range files {16:          fmt.Println(file.Mode(), file.Name())17:      }

10:      from, err := os.Open("./example05.txt")11:      if err != nil {12:          log.Fatal(err)13:      }14:      defer from.Close()15:16:      to, err := os.OpenFile("./example05.copy.txt", os.O_RDWR|os.O_CREATE, 0666)17:      if err != nil {18:          log.Fatal(err)19:      }20:      defer to.Close()21:22:      _, err = io.Copy(to, from)23:      if err != nil {24:          log.Fatal(err)25:      }

[插图] The Open function from the os module is used to read a file from the disk.[插图] A defer statement is used to close the file once the script has finished all otherexecutions.

[插图] The OpenFile function is used to open a file. The first argument is the name of the file tobe opened or created if it does not exist. The second argument represents the flags to beused on the file. In this case, it is read and write and should be created if it does not exist.Finally, the permissions on the file are set.

 Be warned: there is no warning or ability to recover thefile when using this, so be careful.

Using Files to Manage ConfigurationA common use of files in programming is to manage configuration. As code may be used indifferent contexts, a file can often be used to set different configuration parameters that canbe used to start a program.

In a development process, as an application moves throughdevelopment, staging, and production environments, using a file can be an effective way tomanage the differences between environments. Example of this include:[插图] The URL of a web service[插图] Access keys[插图] Port numbers[插图] Environment variables

By storing JSON in a file, a Go program can read this file and use it as configuration data.Listing 21.7 shows a JSON file being read and decoded into a configuration struct.

17:      f, err := ioutil.ReadFile("config.json")18:      if err != nil {19:          log.Fatal(err)20:      }21:      c := Config{}22:      err = json.Unmarshal(f, &c)21:      if err != nil {22:          log.Fatal(err)23:      }24:      fmt.Printf("%!v(MISSING)\n", c)

TOML (Tom’s Obvious, Minimal Language, https://github.com/toml-lang/toml) is a fileformat that was specifically designed for configuration files and is popular in the Gocommunity. It supports a more expressive format than JSON and maps more easily to Gotypes than JSON. While JSON is designed for serializing data, TOML is designed specificallyfor configuration files. As such, TOML has some advantages over JSON in that it is easier toread and has some features, like comments, that JSON does not have. At a basic level, thesyntax is straightforward and can be used to specify keys and values in the same way thatJSON does.Name = "George"Awake = trueHungry = false

go get github.com/BurntSushi/tomlThe package provides a DecodeFile function that expects a filename and a struct todecode the TOML into.

 If you prefer the expressiveness of TOML, adding awell-maintained dependency to your project is not a bad thing. If, however, you can expressyour configuration in a JSON file, you have one less dependency to manage.

Q. Does Go check whether a file exists before operating on it?A. No. As a programmer, you are required to check that a file exists before using it. If a filedoes not exist, an error will be thrown.

1. This is a tricky question! A Go program is executed in the context of the user that you arelogged in as. A file with 0700 permissions is limited to the owner of the file. If the file owneris the same as the user executing the Go process, it will be readable. If it is not the same, itwill not be accessible.

### Hour 22. Introducing Regular Expressions

[插图]Defining Regular Expressions[插图]Getting familiar with regular expression syntax[插图]Using Regular Expressions for validation[插图]Using Regular Expressions to transform data[插图]Parsing data with Regular Expressions

A Regular Expression describes a search pattern that can be used to interact with data.Although Regular Expressions are difficult to learn, they can be extremely powerful. RegularExpressions support tasks like data validation, searching for data, and manipulating largebodies of text. 

Regular Expressions in Go are provided by the regex package, which implements RegularExpression search and pattern matching. The package implements the RE2 syntax and is thesame general syntax used by Perl and Python. It can operate on strings or bytes.

To look for a needle in a haystack, the MatchString function is provided. This takes aRegular Expression pattern and a string and returns true or false depending on whether thereis a match.

To make the search case insensitive, the Regular Expression must be updated to match acase insensitive search for the word. This uses some special syntax to indicate the searchshould be case insensitive.needle := "(?i)chocolate"The special syntax at the start of the string instructs the Regular Expression engine to ignorecase sensitivity. Running this example returns true, as the string is found.

To help explore Regular Expression syntax, suppose that a username needs to bevalidated before it is inserted into a database. Before writing a Regular Expression, it is agood idea to write down the conditions for which it must test. 

Think about trying to write some code to test this without using a Regular Expression. Itwould be possible, but it would take a lot of code. By using Regular Expressions, this can beachieved in a single line:^[a-zA-Z0-9]{5-12}$

[插图] The ^ character indicates that the match should start from the beginning of the string.[插图] The character set within the square brackets [] represents any character that should bematched.[插图] The numbers within the curly brackets {} represent that the match should occur at least5 times and not more than 12 times.

Regular Expressions can be used as a way to validate input data to a program, and theyrepresent an efficient way to parse and understand data. To assign a Regular Expression toa variable, it must first be parsed. Two functions are provided for this:[插图]Compile returns an error if the Regular Expression fails to compile.[插图]MustCompile panics if it cannot compile the Regular Expression.

but generally MustCompile isfavored.

 9:      re := regexp.MustCompile("^[a-zA-Z0-9]{5,12}")10:      fmt.Println(re.MatchString("slimshady99"))

 Another common programming task is to clean data so that it cansafely be used. Regular Expressions can also be used for this task to match a pattern andperform a substitution. 

16:      re := regexp.MustCompile("^[a-zA-Z0-9]{5,12}")17:      an := regexp.MustCompile(":^alnum:")18:

  if len(username) > 12 {21:              username = username[:12]22:              fmt.Printf("trimmed username to %!v(MISSING)\n", username)23:          }

24:          if !re.MatchString(username) {25:              username = an.ReplaceAllString(username, "x")26:              fmt.Printf("rewrote username to %!v(MISSING)\n", username)27:          }

Once you become familiar with the idea that Regular Expressions operate on strings orbytes, there are many opportunities to use Regular Expressions to extract and manipulatedata. 

Nonetheless, there are times when scraping HTML can be the only option. Because HTML isa text document, a Regular Expression can be used to extract relevant pieces of information.

The FindAllString function is used to look for all occurrences of a Regular Expressionwithin a string, and these occurrences are then printed to the terminal. 

   rHTML := regexp.MustCompile("<[^>]*>")

         cleanTitle := rHTML.ReplaceAllString(title, "")

In reality, for scraping HTML, it is recommended that Go’s html package is used, because itis likely to be more performant than Regular Expressions, but working with HTML is a goodway to showcase some features of Go’s Regular Expressions.

. For most mere mortals, constructingcomplex Regular Expressions is often a case of referring to documentation and testingpatterns.

3. Which Regular Expression character signifies the end of a line?

1. The Compile method returns an error if the Regular Expression cannot be parsed. If theMustCompile function fails to parse the Regular Expression, it will cause a panic.

3. The $ signifies the end of a line.

### Hour 23. Programming Time in Go

￼ Putting your program to sleep
￼ Setting a timeout
￼ Using a ticker
￼ Representing time in a string format
￼ Working with Time structs
￼ Adding and subtracting time
￼ Comparing different Time structs

By the end of this hour, you will have a good understanding of how to work with time in Go.

To print the current time on a machine using Go, the Now function can be used. 

In fact, it comes from the underlying operating system. Depending on how accurate the time on your underlying operating system is, this can be useful or not. On most operating systems, the user can set the time.


The Network Time Protocol (NTP) is a networking protocol for synchronizing across a network. 

In computing, the difference between time zones can be negated by referring to Coordinated Universal Time (UTC). UTC is a time standard rather than a time zone and allows computers anywhere in the work to have a common reference without having to work out relative time zones.


This uses the time package to send a message to channel after a certain amount of time. The After function can be used to execute something after a certain amount of time.

Listing 23.3 shows an example of using the After function to trigger a timeout after a certain amount time.


Using a Ticker
A ticker allows code to be repeatedly executed after a certain amount of time. This can be useful for scenarios where jobs need to be run at regular intervals on a long-running process.

 9:      c := time.Tick(5 * time.Second)
10:      for t := range c {
11:          fmt.Printf("The time is now %!v(MISSING)\n", t)
12:      }

10:      s := "2006-01-02T15:04:05+07:00"
11:      t, err := time.Parse(time.RFC3339, s)

Once a time string has been parsed into a Time struct, there a number of useful methods for working with a Time struct. Listing 23.6 shows some of the methods that are available on a Time struct.

20:      fmt.Printf("UNIX time is %!v(MISSING)\n", t.Unix())
21:      fmt.Printf("The day of the week is %!v(MISSING)\n", t.Weekday())

As such, you should be beware that time can easily be wrong. Computers might be set to the wrong time zone or may have drifted far away from the actual time. If you control a server, ensure that something like the Network Time Protocol is installed.

The ISO 8601 format is used in UTC and is well implemented across the web. RFC3339 is an extension to ISO 8601. The two are both good to use. If in doubt, the ISO 8601 standard is widely supported.

### Hour 24. Deploying Go Code

You will then learn how to ship a binary in a Dockercontainer and understand the security considerations of offering binaries as downloads.

The build for a 32-bit Linux is as follows:Click here to view code imageGOOS=linux GOARCH=386 go build example01.go

To build for a 64-bit Windows machine, run the following:Click here to view code imageGOOS=windows GOARCH=amd64 go build example01.goYou do not need to be on a 32-bit Linux machine or a 64-bit Windows machine to compilebinaries for these platforms. For example, compiling the Windows binary results in a .exefile that can be run on Windows.

Using a compiled language like Go greatlysimplifies this process.

The file size is 1.5MB for a program that simply prints a line to the console. Why is this? Thereason is that Go has to include everything it needs to execute the program, including the Gorun-time. Using a language like Ruby or Node.js, the run-time is installed on the server, soonly the files that need to be executed need to be shipped. Although the binary size seemsrelatively large, the advantages are that it will run anywhere on the architecture it wascompiled for with no dependencies.

Some compile flags can reduce the size of compiled binaries. These omit the symbol table,debug information, and the DWARF symbol table. It is not necessary to understand whatthese are. It is enough to know that these are not needed once the binary is ready forrelease.Click here to view code imageGOOS=linux go build -ldflags="-s -w" example01.go

. These devices typically have limited storage. In this scenario,a tool called upx (https://upx.github.io/) can be used. The upx tool is generally available inLinux package managers. It works by compressing the binary.

Using upx, the Hello World program has been reduced to just 374KB. Not bad from the1.5MB that it started at!

 The Go team continues to work onreducing the binary size, and each major release sees binary sizes decreasing.

Docker is a modern alternative to running applications within virtual machines and offers alightweight way to ensure applications run in the same environment, regardless of theoperating system environment they are executed in. Docker uses containers to allowapplications to have a sandboxed environment within an operating system without theoverhead of a full virtual machine.

 Most cloud providers now offer acontainer-based service where Docker containers can be hosted and run. 

You may have heard of the term “DevOps.” This is reallyabout helping software teams to release and ship code quickly, safely, and smoothly. Alongwith other tools, Docker can help in this process. Using Docker, deployment pipelines can becreated that mean developers can just push some code into a code repository, and a fewminutes later it will automatically and magically be released into production.

But the tooling that has emerged around Docker to “ship” containers warrants an argumentto use Docker to package and release Go code.

 1:  FROM golang:1.9 2:  COPY example02.go / 3:  RUN go build -o /example02 /example02.go 4:  EXPOSE 8000 5:  ENTRYPOINT ["/example02"]

docker run -p 8000:8000 hello-go:latestThis command binds port 8000 from the container to the host machine, meaning it can beaccessed. Opening a browser at http://localhost:8000/ shows the applicationresponding with the “Hello World” text.

Because a Go binary has everything it needs to run on a platform, it is not uncommon to seeGo projects distributed as downloadable files from the Internet.

 This is a lightweightand easy way to share a program, since it is very easy to upload and share files on theInternet.[插图]

The secondcommon approach is to provide a checksum of the file. This acts as a fingerprint on the file.

For macOS, the command is shasum;

. Users can therefore download the binary fileand check it against the list of published checksums.

The go get command may also beused to install command line tools too, and even official tooling uses this approach. This lighttouch approach is extremely effective. The source for a tool can be shared on a site likeGithub and can then be installed with a single command. For example, installing dep, thedependency management tool, is as follows:Click here to view code imagego get -u github.com/golang/dep/cmd/dep

Some popular package managers and their platformsinclude:[插图] Homebrew (https://brew.sh/) - macOS[插图] Chocolatey (https://chocolatey.org/) - Windows[插图] Apt (https://wiki.debian.org/Apt) - Debian, Ubuntu[插图] Yum (http://yum.baseurl.org/) - Fedora, Red Hat, Centos

A. There is no easy answer. If you are deploying a web application, using Docker is a goodoption. If you are sharing, a command line tool offering the file as a download or through goget are good options. If you are writing firmware, you will probably need an entirely differentprocess. 

A. If you are creating command line applications and system tools with Go, then learningDocker is probably not needed. If you are creating servers or anything that is deployed in thecontext of other applications, Docker is definitely worth understanding.

3. By default, you should not trust binary files downloaded from the Internet. If you haveconfidence in the identity of the site, then trust can, and should, be established beforeexecuting the file.

### Hour 25. Creating a RESTful JSON API

Managing dependencies can quickly become complex, so using a tool like dep is a good idea.

The httprouter package is a third-party package that builds on top of the net/http package to offer a simple way to create routes and access request parameters. 

Although adding dependencies into a project should be considered carefully, httprouter is a small package that is well maintained and widely used by the community.


When you submit a search in Google, for example, the URL after the search contains lots of data in the GET parameters. 

Another difference between httprouter and net/http is the ability to use dynamic routes. It is possible to extend net/http to accept a regular expression to serve dynamic routes, but this is a good example of where using well-tested library code can save time.

The small extensions of dynamic routing and providing a convenient way to access request parameters makes the httprouter package an excellent choice when creating HTTP servers. Moreover, the author of the package has conducted extensive performance benchmarking against a range of other packages to prove that the library is highly performant.


Hour 18 introduced you to REST, a de facto standard for interacting with data resources over HTTP. REST uses HTTP verbs to manage data resources on a remote server.

Although you could manually test the HTTP server each time you make an update, this would quickly become tedious. It would be better to add some tests that can be run each time an update is made

Using the testing package along with net/http/httptest tests can be created to check that routes respond correctly. 

10:  func TestListTasksHandler(t *testing.T) {
11:      router := httprouter.New()
12:      router.GET("/", ListTasks)
13:      r, _ := http.NewRequest("GET", "/", nil)
14:      w := httptest.NewRecorder()
15:      router.ServeHTTP(w, r)
16:
17:      expectedContentTypeHeader := "application/json; charset=utf-8"
18:      if w.Header().Get("Content-Type") != expectedContentTypeHeader {

19:          t.Errorf("handler returned unexpected Content-Type header: got %!v(MISSING) want %!v(MISSING)",
20:              w.Header().Get("Content-Type"), expectedContentTypeHeader)
21:      }
22:
23:      expectedCode := http.StatusOK
24:      if w.Code != expectedCode {
25:          t.Errorf("handler returned unexpected status code: got %!v(MISSING) want %!v(MISSING)",
26:              w.Code, expectedCode)
27:      }

To work with data applications, store data in memory, meaning that it will be lost when the server is stopped, or in a database, meaning that it will persist between server restarts. 

for simplicity BoltDB, an embedded database, will be used. An embedded database means it is embedded in the Go runtime, so no dependencies other than the library are needed. This is convenient, as there is no need to go through a lengthy installation process.


BoltDB is a key value store, meaning data is stored with a key that can be used to retrieve it at a later date. 

Listing 25.8 shows the contents of store.go that will support reading and writing from the BoltDB data store.

 9:  type Store struct {
10:      // db is the underlying handle to the db.
11:      db *bolt.DB
12:  }

14:  // NewStore sets up BoltDB
15:  func NewStore() (*Store, error) {

16:      handle, err := bolt.Open("tasks.db", 0600, nil)
17:
18:      if err != nil {
19:          return nil, err
20:      }
21:
22:      return &Store{db: handle}, nil
23:  }

25:  // Initialize sets up all the buckets
26:  func (s *Store) Initialize() error {
27:      return s.db.Update(func(tx *bolt.Tx) error {
28:          _, err := tx.CreateBucketIfNotExists([]byte("tasks"))
29:          if err != nil {
30:              return err
31:          }

A few modifications need to be made to main.go in order to integrate BoltDB and store.go. First, in order that the data store can be accessible anywhere within main.go, a variable is declared to reference the Store struct.
var store *Store

The server can now be started. Remember that if you are using go run you will need to pass all files to the command. This is not the case if you choose to build the binary first using go build.
Click here to view code image
go run main.go task.go store.go
Making a request for all tasks shows an empty array, since no tasks have been created.

You were introduced to the dep tool for managing dependencies and two third-party libraries: httprouter and boltdb. You learned how httprouter makes it easy to work with parameters and to create dynamic routes. You also saw how boltdb works as an embedded database. You saw how to create routes for a RESTful web service and then learned how to set headers correctly. You progressed to adding a persistence layer and were able to create and retrieve data. Based on the knowledge you have gained in this hour, you have learned enough to create a basic JSON web service.

Q. Why is REST designed around the HTTP verbs?
A. REST was originally defined by Ray Fielding, one of the principal authors of the HTTP specification. He set out how resources could be managed on a server using HTTP. If you want to read the full paper, it is available at https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm.

3. If a resource is not found, a 404 response code should be returned.

### Hour 26. Creating a TCP Chat Server

This hour introduces you to TCP, one of the most important Internet protocols. You learn how to connect to TCP servers using telnet, and you will see some interesting services that are available over telnet. You are introduced to the net package, a low-level library for creating TCP servers. You will then build up an example TCP chat server and use telnet to connect clients to it. 

Transmission Control Protocol (TCP) is one of the main low-level protocols of the Internet. Combined with the Internet Protocol (IP), it is the backbone of Internet communications. TCP was first declared in May 1974 

In fact, HTTP builds on top of TCP/IP, with HTTP being defined as being in the Application Layer, and TCP/IP being in the Transport Layer. TCP/IP is the Transport Layer for many protocols including HTTP, HTTPS, FTP, IMAP, and many more.

Because HTTP is built on top of TCP/IP, it is possible to request a web page.
Click here to view code image
GET / HTTP/1.1
HOST: shapeshed.com

[HIT ENTER TWICE HERE]

This creates a TCP server listening on port 8080. To be able to do anything useful with the server, it must listen for incoming connections and respond to them. A very simple example is a server that listens for connections, responds with a message, then closes a connection.


so to prevent the server from exiting, a for statement can be used.

    conn.Write([]byte("Hello World!\n"))

The Write method expects a slice of bytes, so this is created accordingly. 

Chat services have been around for a long time on the web. Typically, they are rooms or places where people from around the world can connect and talk to each other.

￼ Must maintain connections with clients.
￼ Must broadcast messages to clients as soon as they are received.
￼ Must understand when a client disconnects from the service.
￼ May have to manage many rooms with clients able to be in one or more.
In fact, chat is an excellent example of where concurrency is an important factor in the software. Suppose that a simple service has 50 clients connected to a single chat room. Users are chatting regularly and are frequently sending messages into the server. The server must process these ­messages promptly and in the right order. 

With a few small modifications, the server can be made to respond concurrently to ­client requests, as shown in Listing 26.2.

16:      for {
17:          conn, err := server.Accept()
18:          if err != nil {
19:              log.Fatal(err)
20:          }
21:
22:          go handleRequest(conn)
23:
24:      }
25:  }
26:

27:  func handleRequest(conn net.Conn) {
28:      io.Copy(conn, conn)
29:  }

￼ When a connection is received, it’s passed to the handleRequest function. (This is run in a Goroutine.)

￼ The handleRequest function uses io.Copy. This transparently copies a writer to a reader so it echoes whatever was sent into the server back to the client.

Running a telnet client against this server shows that messages are echoed back to the client.

 Messages are looped back to the client that sends them, but not to other clients. Although the server has been made concurrent, there is still no shared data between clients.


Initially, the server will need to maintain a list of active connections so that it knows to whom it should send messages. The obvious way to 
do this is to initialize a slice of connections that can be managed as clients connect and disconnect from the service.
var connections []net.Conn

it is possible to add to a slice as follows:
Click here to view code image
connections = append(connections, conn)
Removing from a slice is also straightforward.
Click here to view code image
connections = append(connections[:i], connections[i+1:]...)

The connections variable represents a good example of some shared memory that will be accessed by different Goroutines. To prevent race conditions, this is a good use case for using channels. Instead of modifying the connections variable directly in a Goroutine, a message can be sent to a channel. To support this, two channels are created; one for adding a client and another for removing a client.
Click here to view code image
var addClient = make(chan net.Conn)
var removeClient = make(chan net.Conn)

To handle the adding and removing of a client, channel receivers are created in a startChannels() function.

 addClient <- conn

        go handleRequest(conn)

How though, does the server know that a client has disconnected? The server reads from a client and will receive an End Of File error on a disconnection, indicating that the other side of the TCP socket has closed the connection. This error can be used to remove a client from the connection pool and to close the connection properly from the server side.

Furthermore, a removeConn function can be added to remove the connection from the connection pool on the server.

func handleRequest(conn net.Conn) {
    for {
        message := make([]byte, 1024)

        _, err := conn.Read(message)
        if err != nil {
            if err == io.EOF {
                removeClient <- conn
                conn.Close()
                return
            }
            log.Fatal(err)
        }
    }
}


func removeConn(conn net.Conn) {
    var i int
    for i = range connections {
        if connections[i] == conn {
            break
        }

    }

To demonstrate clients joining and leaving the server, the number of connections is printed each time a client leaves or joins.
LISTING 26.3 Managing Client Connections

When a message isreceived on the channel, it will call the broadcastMessage function.

            case message := <-messages:                broadcastMessage(message)            case newClient := <-addClient:                connections = append(connections, newClient)                fmt.Println(len(connections))            case deadClient := <-removeClient:                removeConn(deadClient)                fmt.Println(len(connections))

message := make([]byte, 1024)

To demonstrate the changes that have been made, the server can be started, and two clientscan be connected to the server.

This final piece offunctionality to add is the ability for the server to omit broadcasting the message to thesender of the message. To achieve this, a struct can be used to contain both the messageand the sender.

In order to identify the sender of amessage, the struct can hold the connection of the sender.type Message struct {    conn    net.Conn    message []byte}

Now when a new message is received, a Message struct can be created that contains boththe message and the connection that it was received from.m := Message{    conn:    conn,    message: message,}

To allow the Message struct to be sent to the “messages” channel, the channel needs to bechanged to accept Message structs rather than byte slices.Click here to view code imagevar messages = make(chan Message)

If the first telnet client sends a message, it will no longer receive the message back toitself.

It is now possible to send and receive messages between clients, and the server functions asa chat server. In less than 100 lines of code and just using the standard library package, Gohas shown off some of its strengths! Not only does Go have strong support for programminglow-level protocols like TCP, but it also offers Goroutines as a way to manage concurrencyand Channels as a way to share data between Goroutines. Granted, the server is not readyfor production (it has no chat rooms, and error handling is basic), and demonstrates thebasic functionality of the net package.

Youlearned about telnet as a way to interact with TCP servers. You learned that HTTP sits ontop of TCP and saw that it is possible to use a TCP client to request data from an HTTPserver. 

Q.Telnetlooks like it should be in a museum! Do people really still use it?A. Yes! Although telnet is old and it shows, it is still a useful tool for interacting withnetwork services. It can be used to perform low-level debugging on web servers, mailservers, and FTP servers.

A. HTTP is designed to operate on top of TCP and targets communication between webclients. Where servers are communicating directly in a trusted network, TCP is a morelightweight way for servers to communicate.

A. Go is certainly an excellent choice for highly concurrent services like chat. If you areseriously considering creating a chat service or a real-time system, you may also want tolook at Erlang (https://www.erlang.org/)

1. The TCP/IP stack offers the fundamental backbone to the Internet. HTTP builds on top ofthis layer to support web-based communication between clients and servers.

Furthermore, the server must maintain somecomplex state as clients frequently join or leave the server.

1. Perform some research on WebSockets and see how this relates to TCP.