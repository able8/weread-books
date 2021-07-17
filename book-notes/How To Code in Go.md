## How To Code in Go
> Mark Bates, Cory LaNou, Tim Raymond

### About DigitalOcean

DigitalOcean is a cloud services platform delivering the simplicity developers love and businesses trust to run production applications at scale. It provides highly available, secure and scalable compute, storage and networking solutions that help developers build great software faster.

### Preface — Getting Started with this Book

One of the benefits of Go is that most of the code that you write will compile and run on Linux, Windows, and macOS, so you can choose a development environment that suits you.

If you are unfamiliar with Go, or do not have a development environment set up, Chapter 1 of this book goes into detail about how to install Go for development on a local Ubuntu 18.04 system, Chapter 2 explains how to set up Go on macOS, and Chapter 3 covers how to get started with Go using Windows 10.

### Introduction

This book explores various Go topics with clear, self-contained example code.

This book starts with chapters on how to set up and configure a local Go development environment. It ends with chapters that explain how to build your Go programs with conditional imports to target specific operating systems, and a final chapter on parsing command line flags to help you build your own utilities.

### How To Install Go and Set Up a Local Programming Environment on Ubuntu 18.04

In this step, you’ll install Go by downloading the current release from the official Go downloads page.

Before downloading Go, make sure that you are in the home (~) directory:
cd ~
Use curl to retrieve the tarball URL that you copied from the official Go downloads page:
curl -O https://dl.google.com/go/go1.12.1.linux-amd64.tar.gz
Next, use sha256sum to verify the tarball:
sha256sum go1.12.1.linux-amd64.tar.gz

Issue the following command to create the directory structure for your Go workspace:
mkdir -p $HOME/go/{bin,src}
The -p option tells mkdir to create all parents in the directory, even if they don’t currently exist. Using {bin,src} creates a set of arguments to mkdir and tells it to create both the bin directory and the src directory.


### How To Install Go and Set Up a Local Programming Environment on macOS

Step 2 — Installing Xcode
Xcode is an integrated development environment (IDE) that is comprised of software development tools for macOS. You can check if Xcode is already installed by typing the following in the Terminal window:
xcode-select -p
The following output means that Xcode is installed:
Output
/Library/Developer/CommandLineTools

If you received an error, then in your web browser install [Xcode from the App Store] (https://itunes.apple.com/us/app/xcode/id497799835?mt=12&ign-mpt=uo%!D(MISSING)2) and accept the default options.

A package manager is a collection of software tools that work to automate installation processes that include initial software installation, upgrading and configuring of software, and removing software as needed. 

Homebrew provides macOS with a free and open source software package managing system that simplifies the installation of software on macOS.

### How To Write Your First Program in Go

import is a Go keyword that tells the Go compiler which other packages you want to use in this file. Here you import the fmt package that comes with the standard library. The fmt package provides formatting and printing functions that can be useful when developing.


Instead of modifying your existing program, create a new program called greeting.go with the nano editor:
nano greeting.go

  fmt.Println("Please enter your name.")
  var name string
  fmt.Scanln(&name)
}
The fmt.Scanln method tells the computer to wait for input from the keyboard ending with a new line or (\n), character. This pauses the program, allowing the user to enter any text they want. The program will continue when the user presses the ENTER key on their keyboard. All of the keystrokes, including the ENTER keystroke, are then captured and converted to a string of characters.


name = strings.TrimSpace(name)
This uses the TrimSpace function, from Go’s standard library strings package, on the string that you captured with fmt.Scanln. The strings.TrimSpace function removes any space characters, including new lines, from the start and end of a string. In this case, it removes the newline character at the end of the string created when you pressed ENTER.

To use the strings package you need to import it at the top of the program.
Locate these lines in your program:
greeting.go

import (

    "fmt"

)

### Understanding the GOPATH

A Go Workspace is how Go manages our source files, compiled binaries, and cached objects used for faster compilation later. It is typical, and also advised, to have only one Go Workspace, though it is possible to have multiple spaces. The GOPATH acts as the root folder of a workspace.

Introduced in Go 1.11, Go Modules aim to replace Go Workspaces and the GOPATH. While it is recommended to start using modules, some environments, such as corporate environments, may not be ready to use modules.


### How To Write Comments in Go

Comment Syntax
Comments in Go begin with a set of forward slashes (//) and continue to the end of the line. It is idiomatic to have a white space after the set of forward slashes.
Generally, comments will look something like this:

// This is a comment

For documenting code, it is considered idiomatic to always use // syntax. You would only use the /* ... */ syntax for debugging, which we will cover later in this article.

/*

Everything here

will be considered

a block comment

*/

Note: Commenting out code should only be done for testing purposes. Do not leave snippets of commented out code in your final program.

Commenting your code properly in Go will also allow for you to use the Godoc tool. Godoc is a tool that will extract comments from your code and generate documentation for your Go program.

### Understanding Data Types in Go

There are also a couple of alias number types, which assign useful names to specific data types:
byte        alias for uint8
rune        alias for int32
The purpose of the byte alias is to make it clear when your program is using bytes as a common computing measurement in character string elements, as opposed to small integers unrelated to the byte data measurement. Even though byte and uint8 are identical once the program is compiled, byte is often used to represent character data in numeric form, whereas uint8 is intended to be a number in your program.

The rune alias is a bit different. Where byte and uint8 are exactly the same data, a rune can be a single byte or four bytes, a range determined by int32. A rune is used to represent a Unicode character, whereas only ASCII characters can be represented solely by an int32 data type.

Understanding the boundaries of your data will help you avoid potential bugs in your program in the future.

Strings
A string is a sequence of one or more characters (letters, numbers, symbols) that can be either a constant or a variable. Strings exist within either back quotes ` or double quotes " in Go and have different characteristics depending on which quotes you use.

If you use the back quotes, you are creating a raw string literal. If you use the double quotes, you are creating an interpreted string literal.


Raw String Literals
Raw string literals are character sequences between back quotes, often called back ticks. Within the quotes, any character will appear just as it is displayed between the back quotes, except for the back quote character itself.


Raw string literals may also be used to create multiline strings:

a := `This string is on 

multiple lines

within a single back 

quote on either side.`

fmt.Println(a)

Arrays
An array is an ordered sequence of elements. The capacity of an array is defined at creation time. Once an array has allocated its size, the size can no longer be changed. Because the size of an array is static, it means that it only allocates memory once. 

This makes arrays somewhat rigid to work with, but increases performance of your program. Because of this, arrays are typically used when optimizing programs.

Slices
A slice is an ordered sequence of elements that can change in length. Slices can increase their size dynamically. When you add new items to a slice, if the slice does not have enough memory to store the new items, it will request more memory from the system as needed. Because a slice can be expanded to add more elements when needed, they are more commonly used than arrays.

A slice of strings looks like this:

[]string{"shark", "cuttlefish", "squid", "mantis shrimp"}

We can print them out by calling the variable:

fmt.Println(seaCreatures)
The output will look exactly like the list that we created:
Output
[shark cuttlefish squid mantis shrimp]

Maps
The map is Go’s built-in hash or dictionary type. Maps use keys and values as a pair to store data. This is useful in programming to quickly look up values by an index, or in this case, a key. For instance, you may want to keep a map of users, indexed by their user ID. The key would be the user ID, and the user object would be the value.

### An Introduction to Working with Strings in Go

Here’s an example of a raw string literal:
`Say "hello" to Go!`
Backslashes have no special meaning inside of raw string literals. For instance, \n will appear as the actual characters, backslash \ and letter n. Unlike interpreted string literals, in which \n would insert an actual new line.

The + operator can not be used between two different data types. As an example, you can’t concatenate strings and integers together. If you were to try to write the following:

fmt.Println("Sammy" + 27)
You will receive the following errors:
Output
cannot convert "Sammy" (type untyped string) to type int
invalid operation: "Sammy" + 27 (mismatched types string and int)

### How To Format Strings in Go

Multiple Lines
Printing strings on multiple lines can make text more readable to humans. With multiple lines, strings can be grouped into clean and orderly text, formatted as a letter, or used to maintain the linebreaks of a poem or song lyrics.


### An Introduction to the Strings Package in Go

To convert the string "Sammy Shark" to be all uppercase, you would use the strings.ToUpper function:

ss := "Sammy Shark"

fmt.Println(strings.ToUpper(ss))

The strings package has a number of functions that help determine if a string contains a specific sequence of characters.

The strings.HasPrefix and strings.HasSuffix allow you to check to see if a string starts or ends with a specific set of characters.

For example, to check to see if the string "Sammy Shark" starts with Sammy and ends with Shark:

ss := "Sammy Shark"

fmt.Println(strings.HasPrefix(ss, "Sammy"))

Finally, to see how many times the letter S appears in the phrase Sammy Shark:

fmt.Println(strings.Count(ss, "S"))
Output
2

The built-in function len() returns the number of characters in a string. 

The strings.Join, strings.Split, and strings.ReplaceAll functions are a few additional ways to manipulate strings in Go.
The strings.Join function is useful for combining a slice of strings into a new single string.
To create a comma-separated string from a slice of strings, we would use this function as per the following:

fmt.Println(strings.Join([]string{"sharks", "crustaceans", "plankton"}, ","))
Output
sharks,crustaceans,plankton

Another useful function in addition to strings.Split is strings.Fields. The difference is that strings.Fields will ignore all whitespace, and will only split out the actual fields in a string:

data := "  username password     email  date"

fields := strings.Fields(data)

fmt.Printf("%!q(MISSING)", fields)
Output
["username" "password" "email" "date"]

The strings.ReplaceAll function can take an original string and return an updated string with some replacement.

### How To Use Variables and Constants in Go

While variables are case sensitive, the case of the first letter of a variable has special meaning in Go. If a variable starts with an uppercase letter, then that variable is accessible outside the package it was declared in (or exported). If a variable starts with a lowercase letter, then it is only available within the package it is declared in.

Multiple Assignment
Go also allows us to assign several values to several variables within the same line. Each of these values can be of a different data type:

j, k, l := "shark", 2.05, 15

### How To Convert Data Types in Go

For example, Go sometimes automatically generates numeric values as int, which may not match your input value. If your input value were int64, you would not be able to use the int and the int64 numbers in the same mathematical expression until you converted their data types to match.

Assume that you have an int8 and you need to convert it to an int32. You can do this by wrapping it in the int32() type conversion:

var index int8 = 15



var bigIndex int32



bigIndex = int32(index)

To verify your data types, you could use the fmt.Printf statement and the %!T(MISSING) verb with the following syntax:

fmt.Printf("index data type:    %!T(MISSING)\n", index)

fmt.Printf("bigIndex data type: %!T(MISSING)\n", bigIndex)
Output
index data type:    int8
bigIndex data type: int32

Keep in mind that when converting integers you could potentially exceed the maximum value of the data type and wraparound:

var big int64 = 129

var little = int8(big)

fmt.Println(little)
Output
-127

Strings are a common form of data in computer programs, and you may need to convert strings to numbers or numbers to strings fairly often, especially when you are taking in user-generated data.

You also used the %!q(MISSING) verb in the fmt.Printf function, which tells the function to quote the string provided.

### Understanding Maps in Go

Again, the keys are not sorted. If you want to sort them, you use the sort.Strings function from the sort package:

sort.Strings(keys)

Even though the key sammy was not in the map, Go still returned the value of 0. This is because the value data type is an int, and because Go has a zero value for all variables, it returns the zero value of 0.

count, ok := counts["sammy"]
If the key sammy exists in the counts map, then ok will be true. Otherwise ok will be false.

In Go, you can combine variable declaration and conditional checking with an if/else block. This allows you to use a single statement for this check:

if count, ok := counts["sammy"]; ok {

    fmt.Printf("Sammy has a count of %!d(MISSING)\n", count)

} else {

    fmt.Println("Sammy was not found")

}

If you would like to clear a map of all of its values, you can do so by setting it equal to an empty map of the same type. This will create a new empty map to use, and the old map will be cleared from memory by the garbage collector.
Let’s remove all the items within the permissions map:

permissions = map[int]string{}

fmt.Println(permissions)
The output shows that you now have an empty map devoid of key-value pairs:
Output
map[]

Because maps are mutable data types, they can be added to, modified, and have items removed and cleared.

### Understanding Arrays and Slices in Go

append() is a built-in method in Go that adds elements to a collection data type. However, this method will not work when used with an array. As mentioned before, the primary way in which arrays are different from slices is that the size of an array cannot be modified. This means that while you can change the values of elements in an array, you can’t make the array larger or smaller after it has been defined.


A slice is a data type in Go that is a mutable, or changeable, ordered sequence of elements. Since the size of a slice is variable, there is a lot more flexibility when using them; when working with data collections that may need to expand or contract in the future, using a slice will ensure that your code does not run into errors when trying to manipulate the length of the collection.

### Handling Errors in Go

Error handling is the process of identifying when your program is in an unexpected state, and taking steps to record diagnostic information for later debugging.

The fmt.Errorf function allows you to dynamically build an error message. Its first argument is a string containing your error message with placeholder values such as %!s(MISSING) for a string and %!d(MISSING) for an integer. fmt.Errorf interpolates the arguments that follow this formatting string into those placeholders in order:


    err := fmt.Errorf("error occurred at: %!v(MISSING)", time.Now())

We used the fmt.Errorf function to build an error message that would include the current time. 

You can discard any unwanted values that functions return using the special _ variable name.

### Handling Panics in Go

calling function has completed execution. Deferred functions run even in the presence of a panic, and are used as a safety mechanism to guard against the chaotic nature of panics. 

### Understanding Package Visibility in Go

replace github.com/gopherguides/logging => ../logging

The second line tells the compiler that the package github.com/gopherguides/logging can be found locally on disk in the ../logging directory.

### How To Write Switch Statements in Go


        switch {

        case guess > target:

            fmt.Println("Too high!")

        case guess < target:

            fmt.Println("Too low!")

        default:

            fmt.Println("You win!")

            return

        }

### How To Construct For Loops in Go

for range sharks {
        sharks = append(sharks, "shark")
    }

Notice that we didn’t have to use the blank identifier _ to ignore any of the return values from the range operator. Go allows us to leave out the entire declaration portion of the range statement if we don’t need to use either of the return values.

### How To Define and Call Functions in Go

Returning a Value
You can pass a parameter value into a function, and a function can also produce a value.
A function can produce a value with the return statement, which will exit a function and optionally pass an expression back to the caller. The return data type must be specified as well.

Note: It is considered best practice to only return two or three values. Additionally, you should always return all errors as the last return value from a function.


Functions are code blocks of instructions that perform actions within a program, helping to make our code reusable and modular.

### Understanding defer in Go

One of the primary uses of a defer statement is for cleaning up resources, such as open files, network connections, and database handles. When your program is finished with these resources, it’s important to close them to avoid exhausting the program’s limits and to allow other programs access to those resources. defer makes our code cleaner and less error prone by keeping the calls to close the file/resource in proximity to the open call.


we will learn how to properly use the defer statement for cleaning up resources as well as several common mistakes that are made when using defer.


Because the calls are placed on a stack, they are called in last-in-first-out order.

func main() {

    defer fmt.Println("Bye")

    fmt.Println("Hi")

}
In the main function, we have two statements. The first statement starts with the defer keyword, followed by a print statement that prints out Bye. The next line prints out Hi.

If we run the program, we will see the following output:

The key to understanding defer is that when the defer statement is executed, the arguments to the deferred function are evaluated immediately. When a defer executes, it places the statement following it on a list to be invoked prior to the function returning.

Using defer to clean up resources is very common in Go.

Although this code works, there is a subtle bug. If the call to io.WriteString fails, the function will return without closing the file and releasing the resource back to the system.

func write(fileName string, text string) error {
    file, err := os.Create(fileName)
    if err != nil {
        return err
    }
    defer file.Close()
    _, err = io.WriteString(file, text)
    if err != nil {
        return err
    }
    return nil
}

However, we have introduced yet another bug by adding the defer. We are no longer checking the potential error that can be returned from the Close method. This is because when we use defer, there is no way to communicate any return value back to our function.

Let’s look at how we can both defer the call to Close, and still report on an error if we encounter one.

The only change in this program is the last line in which we return file.Close(). If the call to Close results in an error, this will now be returned as expected to the calling function. Keep in mind that our defer file.Close() statement is also going to run after the return statement. This means that file.Close() is potentially called twice. While this isn’t ideal, it is an acceptable practice as it should not create any side effects to your program.


Multiple defer Statements
It is normal to have more than one defer statement in a function. 

Notice that the order is the opposite in which we called the defer statements. This is because each deferred statement that is called is stacked on top of the previous one, and then called in reverse when the function exits scope (Last In, First Out).


file, err := os.Create(fileName)

    if err != nil {

        return err

    }

    defer file.Close()

    _, err = io.WriteString(file, text)

    if err != nil {

        return err

    }



    return file.Close()


    n, err := io.Copy(dst, src)

    if err != nil {

        return err

    }

    fmt.Printf("Copied %!d(MISSING) bytes from %!s(MISSING) to %!s(MISSING)\n", n, source, destination)



    if err := src.Close(); err != nil {

        return err

    }

We check to see if we received an error opening the file. If so, we return the error and exit the function. Otherwise, we defer the closing of the source file we just opened.

Notice that we explicitly call Close() for each file, even though the defer will also call Close(). This is to ensure that if there is an error closing a file, we report the error. It also ensures that if for any reason the function exits early with an error, for instance if we failed to copy between the two files, that each file will still try to close properly from the deferred calls.

### Customizing Go Binaries with Build Tags

In Go, a build tag, or a build constraint, is an identifier added to a piece of code that determines when the file should be included in a package during the build process. This allows you to build different versions of your Go application from the same source code and to toggle between them in a fast and organized manner. 

Build tags are also used for integration testing, allowing you to quickly switch between the integrated code and the code with a mock service or stub, and for differing levels of feature sets within an application.

Let’s start by examining what a build tag looks like:

// +build tag_name
By putting this line of code as the first line of your package and replacing tag_name with the name of your build tag, you will tag this package as code that can be selectively included in the final binary. 

// +build pro

package main

func init() {
  features = append(features,
    "Pro Feature #1",
    "Pro Feature #2",
  )
}

At the top of the pro.go file, we added // +build pro followed by a blank newline. This trailing newline is required, otherwise Go interprets this as a comment. Build tag declarations must also be at the very top of a .go file. Nothing, not even comments, can be above build tags.
The +build declaration tells the go build command that this isn’t a comment, but instead is a build tag. The second part is the pro tag. By adding this tag at the top of the pro.go file, the go build command will now only include the pro.go file with the pro tag is present.

When running the go build command, we can use the -tags flag to conditionally include code in the compiled source by adding the tag itself as an argument. Let’s do this for the pro tag:
go build -tags pro
This will output the following:

Instead of putting both tags on the same line, if we put them on separate lines, then go build will interpret those tags using AND logic.


### Understanding Pointers in Go

 data type called a pointer holds the memory address of the data, but not the data itself. The memory address tells the function where to find the data, but not the value of the data.

You can pass the pointer to the function instead of the data, and the function can then alter the original variable in place. This is called passing by reference, because the value of the variable isn’t passed to the function, just its location.


In this article, you will create and use pointers to share access to the memory space for a variable.

Let’s take a look at a pointer to a string. The following code declares both a value of a string, and a pointer to a string:
main.go

package main



import "fmt"



func main() {

    var creature string = "shark"

    var pointer *string = &creature

In practice, you’ll probably never output a memory address to look at it. We’re showing you for illustrative purposes. Because each program is created in its own memory space when it is run, the value of the pointer will be different each time you run it, and will be different than the output shown here:
Output
creature = shark
pointer = 0xc0000721e0

Function Pointer Receivers
When you write a function, you can define arguments to be passed ether by value, or by reference. Passing by value means that a copy of that value is sent to the function, and any changes to that argument within that function only effect that variable within that function, and not where it was passed from. However, if you pass by reference, meaning you pass a pointer to that argument, you can change the value from within the function, and also change the value of the original variable that was passed in.

All variables in Go have a zero value. This is true even for a pointer. If you declare a pointer to a type, but assign no value, the zero value will be nil. nil is a way to say that “nothing has been initialized” for the variable.


It is common in Go that if you are receiving an argument as a pointer, you check to see if it was nil or not before performing any operations on it to prevent the program from panicking.
This is a common approach for checking for nil:

if someVariable == nil {

    // print an error or return from the method or fuction

}

If you do, you’ll likely just want to return, or return an error to show that an invalid argument was passed to the function or method. The following code demonstrates checking for nil:



func changeCreature(creature *Creature) {

    if creature == nil {

        fmt.Println("creature is nil")

        return

    }



    creature.Species = "jellyfish"

    fmt.Printf("2) %!v(MISSING)\n", creature)

}

The big difference is that if you define a method with a value receiver, you are not able to make changes to the instance of that type that the method was defined on.

### Defining Structs in Go

Inline Structs
In addition to defining a new type to represent a struct, you can also define an inline struct. These on-the-fly struct definitions are useful in situations where inventing new names for struct types would be wasted effort. For example, tests often use a struct to define all the parameters that make up a particular test case. 

### Defining Methods in Go

Functions allow you to organize logic into repeatable procedures that can use different arguments each time they run. In the course of defining functions, you’ll often find that multiple functions might operate on the same piece of data each time. Go recognizes this pattern and allows you to define special functions, called methods, whose purpose is to operate on instances of some specific type, called a receiver. Adding methods to types allows you to communicate not only what the data is, but also how that data should be used.

Defining a Method
The syntax for defining a method is similar to the syntax for defining a function. The only difference is the addition of an extra parameter after the func keyword for specifying the receiver of the method. The receiver is a declaration of the type that you wish to define the method on. The following example defines a method on a struct type:

Pointer Receivers
The syntax for defining methods on the pointer receiver is nearly identical to defining methods on the value receiver. The difference is prefixing the name of the type in the receiver declaration with an asterisk (*).

### How To Build and Install Go Programs

In Go, the process of translating source code into a binary executable is called building. Once this executable is built, it will contain not only your application, but also all the support code needed to execute the binary on the target platform. This means that a Go binary does not need system dependencies such as Go tooling to run on a new system, unlike other languages like Ruby, Python, or Node.js. 

To test the program, use the go run command, as you’ve done in previous tutorials:
go run main.go
You’ll receive the following output:
Output
Hello, World!

Now that you know how to generate an executable, the next step is to identify how Go chooses a name for the binary and to customize this name for your project.

Now run the install command:
go install
This will build your binary and place it in $GOPATH/bin. To test this, run the following:
ls $GOPATH/bin
This will list the contents of $GOPATH/bin:
Output
greeter
Note: The go install command does not support the -o flag, so it will use one of the default names described earlier to name the executable.


### How To Use Struct Tags in Go

Struct tags are small pieces of metadata attached to fields of a struct that provide instructions to other Go code that works with the struct.


Go struct tags are annotations that appear after the type in a Go struct declaration. Each tag is composed of short strings associated with some corresponding value.
A struct tag looks like this, with the tag offset with backtick ` characters:

type User struct {

    Name string `example:"name"`

}

Try this example to see what struct tags look like, and that without code from another package, they will have no effect.


To use struct tags to accomplish something, other Go code must be written to examine structs at runtime. The standard library has packages that use struct tags as part of their operation. The most popular of these is the encoding/json package.

The JSON encoder in the standard library makes use of struct tags as annotations indicating to the encoder how you would like to name your fields in the JSON output. These JSON encoding and decoding mechanisms can be found in the encoding/json package.


Within the value part of any json struct tag, you can suffix the desired name of your field with ,omitempty to tell the JSON encoder to suppress the output of this field when the field is set to the zero value. The following example fixes the previous examples to no longer output empty fields:

PreferredFish []string  `json:"preferredFish,omitempty"`

These features of the encoding/json package, ,omitempty and "-", are not standards. What a package decides to do with values of a struct tag depends on its implementation. Because the encoding/json package is part of the standard library, other packages have also implemented these features in the same way as a matter of convention. However, it’s important to read the documentation for any third-party package that uses struct tags to learn what is supported and what is not.

### How To Use Interfaces in Go

Notice that we not only accept both arguments as the type Sizer, but we also return the result as a Sizer as well. This means that we no longer return a Square or a Circle, but the interface of Sizer.

### Building Go Applications for Different Operating Systems and Architectures

To find this list of possible platforms, run the following:
go tool dist list
You will receive an output similar to the following:

This is a good example program because the operation of the program depends on which OS it is running on. On Windows, the path separator is a backslash, \, while Unix-based systems use a forward slash, /.

// +build aix darwin dragonfly freebsd js,wasm linux nacl netbsd openbsd solaris



package os



const (

  PathSeparator     = '/' // OS-specific path separator

  PathListSeparator = ':' // OS-specific path list separator

)

package os



const (

        PathSeparator     = '\\' // OS-specific path separator

        PathListSeparator = ';'  // OS-specific path list separator

)

. . .
Although the value of PathSeparator is \\ here, the code will render the single backslash (\) needed for Windows filepaths, since the first backslash is only needed as an escape character.

Notice that, unlike the Unix file, there are no build tags at the top. This is because GOOS and GOARCH can also be passed to go build by adding an underscore (_) and the environment variable value as a suffix to the filename, something we will go into more in the section Using GOOS and GOARCH File Name Suffixes. Here, the _windows part of path_windows.go makes the file act as if it had the build tag // +build windows at the top of the file. Because of this, when your program is run on Windows, it will use the constants of PathSeparator and PathListSeparator from the path_windows.go code snippet.

### Using ldflags to Set Version Information for Go Applications

When deploying applications into a production environment, building binaries with version information and other metadata will improve your monitoring, logging, and debugging processes by adding identifying information to help track your builds over time. This version information can often include highly dynamic data, such as build time, the machine or user building the binary, the Version Control System (VCS) commit ID it was built against, and more.

ldflags, then, stands for linker flags. It is called this because it passes a flag to the underlying Go toolchain linker, cmd/link, that allows you to change the values of imported packages at build time from the command line.

go build -ldflags="-X 'package_path.variable_name=new_value'"
Inside the quotes, there is now the -X option and a key-value pair that represents the variable to be changed and its new value. The . character separates the package path and the variable name, and single quotes are used to avoid breaking characters in the key-value pair.

To replace the Version variable in your example application, use the syntax in the last command block to pass in a new value and build the new binary:
go build -ldflags="-X 'main.Version=v1.0.0'"
In this command, main is the package path of the Version variable, since this variable is in the main.go file. Version is the variable that you are writing to, and v1.0.0 is the new value.


### How To Use the Flag Package in Go

useColor := flag.Bool("color", false, "display colorized output")

    flag.Parse()



    if *useColor {

        colorize(ColorBlue, "Hello, DigitalOcean!")

        return

    }

The value returned from this function is a pointer to a bool. The flag.Parse function on the next line uses this pointer to set the bool variable based on the flags passed in by the user. We are then able to check the value of this bool pointer by dereferencing the pointer. More information about pointer variables can be found in the tutorial on pointers.


    var count int

    flag.IntVar(&count, "n", 5, "number of lines to read from the file")

    flag.Parse()

Using FlagSet to Implement Sub-commands
Modern command-line applications often implement “sub-commands” to bundle a suite of tools under a single command. The most well-known tool that uses this pattern is git. When examining a command like git init, git is the command and init is the sub-command of git. One notable feature of sub-commands is that each sub-command can have its own collection of flags.

To learn more about the Go programming language, check out our full How To Code in Go series.