## Let's Go5
> Alex Edwards

### 1. Introduction

In this book we’ll be building a web application called Snippetbox, which lets people paste and share snippets of text — a bit like Pastebin or GitHub’s Gists. 

how to structure a project, routing requests, working with a database, processing forms and displaying dynamic data safely.

Then later in the book we’ll add user accounts, and restrict the application so that only registered users can create snippets.

This will take us through more advanced topics like configuring a HTTPS server, session management, user authentication and middleware.


This book is designed for people who are new to Go, but you’ll probably find it more enjoyable if you have a general understanding of Go’s syntax first.

... // Indicates that some existing code has been omitted.

Terminal (command line) instructions are shown with a black background and start with a dollar symbol. 

Some chapters in this book end with an additional information section. These sections contain information that isn’t relevant to our application build, but is still important (or sometimes, just interesting) to know about.

Hey, I’m Alex Edwards, a full-stack web developer. I began working with Go 7 years ago in 2013, and have been teaching people and writing about the language for nearly as long.

Let’s Go: Learn to build professional web applications with Go. Copyright © 2020 Alex Edwards.
Last updated 2021-03-03 16:51:27 UTC. Version 1.5.2.


### 2. Foundations

You’ll learn how to:
•  Setup a project directory which follows the Go conventions.
•  Start a web server and listen for incoming HTTP requests.
•  Route requests to different handlers based on the request path.

•  Fetch and validate untrusted user input from URL query string parameters.
•  Structure your project in a sensible and scalable way.

### 2.1. Installing Go

 I recommend installing this

Detailed instructions for different operating systems can be found here:
•  Removing an old version of Go
•  Installing Go on Mac OS X

### 2.2. Project Setup and Enabling Modules

I’m going to locate my project directory under $HOME/code, but you can choose a different location if you wish.
$ mkdir -p $HOME/code/snippetbox


To avoid potential import conflicts with other people’s packages or the standard library in the future, you want to pick a module path that is globally unique and unlikely to be used by anything else.

run the go mod init command — passing in your module path as a parameter like so:
$ cd $HOME/code/snippetbox
$ go mod init alexedwards.net/snippetbox

File: go.mod
module alexedwards.net/snippetbox

In order for Go to use modules, this must have the value On, Auto or the empty string "". If it is set to Off, please change this environment variable before continuing.

### 2.3. Web Application Basics

Now that everything is set up correctly let’s make the first iteration of our web application. We’ll begin with the three absolute essentials:

The first thing we need is a handler. 

They’re responsible for executing your application logic and for writing HTTP response headers and bodies.


The second component is a router (or servemux in Go terminology). This stores a mapping between the URL patterns for your application and the corresponding handlers. 

/ Define a home handler function which writes a byte slice containing

// "Hello from Snippetbox" as the response body.

func home(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("Hello from Snippetbox"))
}


    log.Println("Starting server on :4000")
    err := http.ListenAndServe(":4000", mux)
    log.Fatal(err)


Note: The home handler function is just a regular Go function with two parameters. The http.ResponseWriter parameter provides methods for assembling a HTTP response and sending it to the user, and the *http.Request parameter is a pointer to a struct which holds information about the current request (like the HTTP method and the URL being requested).

the servemux will check the URL path and dispatch the request to the matching handler.

While the server is running, open a web browser and try visiting http://localhost:4000. 

The TCP network address that you pass to http.ListenAndServe() should be in the format "host:port". If you omit the host (like we did with ":4000") then the server will listen on all your computer’s available network interfaces.

If you use a named port then Go will attempt to look up the relevant port number from your /etc/services file when starting the server, or will return an error if a match can’t be found.

During development the go run command is a convenient way to try out your code. 

a space-separated list of .go files

$ go run main.go
$ go run .
$ go run alexedwards.net/snippetbox

### 2.4. Routing Requests

mux := http.NewServeMux()
    mux.HandleFunc("/", home)
    mux.HandleFunc("/snippet", showSnippet)

Go’s servemux supports two different types of URL patterns: fixed paths and subtree paths. Fixed paths don’t end with a trailing slash, whereas subtree paths do end with a trailing slash.

Our two new patterns — "/snippet" and "/snippet/create" — are both examples of fixed paths. In Go’s servemux, fixed path patterns like these are only matched (and the corresponding handler called) when the request URL path exactly matches the fixed path.

In contrast, our pattern "/" is an example of a subtree path (because it ends in a trailing slash). Another example would be something like "/static/". Subtree path patterns are matched (and the corresponding handler called) whenever the start of a request URL path matches the subtree path. 

you can think of subtree paths as acting a bit like they have a wildcard at the end, like "/**" or "/static/**".

This helps explain why the "/" pattern is acting like a catch-all. The pattern essentially means match a single slash, followed by anything (or nothing at all).


So what if you don’t want the "/" pattern to act like a catch-all?

It’s not possible to change the behavior of Go’s servemux to do this, but you can include a simple check in the home hander which ultimately has the same effect:

/ Check if the current request URL path exactly matches "/". If it doesn't, use

    // the http.NotFound() function to send a 404 response to the client.

// Importantly, we then return from the handler. If we don't return the handler

    // would keep executing and also write the "Hello from SnippetBox" message.

if r.URL.Path != "/" {
        http.NotFound(w, r)
        return
    }

The DefaultServeMux
If you’ve been working with Go for a while you might have come across the http.Handle() and http.HandleFunc() functions. These allow you to register routes without declaring a servemux, like this:


func main() {
    http.HandleFunc("/", home)
    http.HandleFunc("/snippet", showSnippet)
    http.HandleFunc("/snippet/create", createSnippet)

    log.Println("Starting server on :4000")
    err := http.ListenAndServe(":4000", nil)
    log.Fatal(err)
}

Here’s the relevant line from the Go source code:
var DefaultServeMux = NewServeMux()
Although this approach can make your code slightly shorter, I don’t recommend it for production applications.

Because DefaultServeMux is a global variable, any package can access it and register a route — including any third-party packages that your application imports. If one of those third-party packages is compromised, they could use DefaultServeMux to expose a malicious handler to the web.

So, for the sake of security, it’s generally a good idea to avoid DefaultServeMux and the corresponding helper functions. Use your own locally-scoped servemux instead, like we have been doing in this project so far.

•  In Go’s servemux, longer URL patterns always take precedence over shorter ones. 

This has the nice side-effect that you can register patterns in any order and it won’t change how the servemux behaves.


Request URL paths are automatically sanitized. If the request path contains any . or .. elements or repeated slashes, the user will automatically be redirected to an equivalent clean URL.

For example, if you have registered the subtree path /foo/, then any request to /foo will be redirected to /foo/.


It’s possible to include host names in your URL patterns. This can be useful when you want to redirect all HTTP requests to a canonical URL, or if your application is acting as the back end for multiple sites or services. For example:

What About RESTful Routing?
It’s important to acknowledge that the routing functionality provided by Go’s servemux is pretty lightweight. It doesn’t support routing based on the request method, it doesn’t support semantic URLs with variables in them, and it doesn’t support regexp-based patterns. 

For the times that you need more, there’s a huge choice of third-party routers that you can use instead of Go’s servemux. We’ll look at some of the popular options later in the book.

### 2.5. Customizing HTTP Headers

  // Use r.Method to check whether the request is using POST or not. Note that

    // http.MethodPost is a constant equal to the string "POST".

  if r.Method != http.MethodPost {
        // If it's not, use the w.WriteHeader() method to send a 405 status

        // code and the w.Write() method to write a "Method Not Allowed"

        // response body. We then return from the function so that the

        // subsequent code is not executed.

        w.WriteHeader(405)
        w.Write([]byte("Method Not Allowed"))
        return
    }

It’s only possible to call w.WriteHeader() once per response, and after the status code has been written it can’t be changed. If you try to call w.WriteHeader() a second time Go will log a warning message.


If you don’t call w.WriteHeader() explicitly, then the first call to w.Write() will automatically send a 200 OK status code to the user. So, if you want to send a non-200 status code, you must call w.WriteHeader() before any call to w.Write().

 you should now get response with a 405 Method Not Allowed status code.

HTTP/1.1 405 Method Not Allowed


The first parameter is the header name, and

        // the second parameter is the header value.

        w.Header().Set("Allow", http.MethodPost)
        w.WriteHeader(405)
        w.Write([]byte("Method Not Allowed"))
        return

Changing the response header map after a call to w.WriteHeader() or w.Write() will have no effect on the headers that the user receives.

Notice how the response now includes a Allow: POST header?


it’s a good opportunity to use the http.Error() shortcut. This is a lightweight helper function which takes a given message and status code, then calls the w.WriteHeader() and w.Write() methods behind-the-scenes for us.


Let’s update the code to use this instead.

 w.Header().Set("Allow", http.MethodPost)
        // Use the http.Error() function to send a 405 status code and "Method Not

        // Allowed" string as the response body.

        http.Error(w, "Method Not Allowed", 405)

The biggest difference is that we’re now passing our http.ResponseWriter to another function, which sends a response to the user for us.


But there’s also Add(), Del() and Get() methods that you can use to read and manipulate the header map too.

/ Set a new cache-control header. If an existing "Cache-Control" header exists

// it will be overwritten.

w.Header().Set("Cache-Control", "public, max-age=31536000")

/ In contrast, the Add() method appends a new "Cache-Control" header and can

// be called multiple times.

w.Header().Add("Cache-Control", "public")
w.Header().Add("Cache-Control", "max-age=31536000")

// Retrieve the first value for the "Cache-Control" header.

w.Header().Get("Cache-Control")

If this function can’t guess the content type, Go will fall back to setting the header Content-Type: application/octet-stream instead.

The http.DetectContentType() function generally works quite well, but a common gotcha for web developers new to Go is that it can’t distinguish JSON from plain text.

So, by default, JSON responses will be sent with a Content-Type: text/plain; charset=utf-8 header. You can prevent this from happening by setting the correct header manually like so:
w.Header().Set("Content-Type", "application/json")
w.Write([]byte(`{"name":"Alex"}`))

Note: If a HTTP/2 connection is being used, Go will always automatically convert the header names and values to lowercase for you as per the HTTP/2 specifications.

If you want to suppress the Date header, for example, you need to write:
w.Header()["Date"] = nil

### 2.6. URL Query Strings

1.  It needs to retrieve the value of the id parameter from the URL query string, which we can do using the r.URL.Query().Get() method. This will always return a string value for a parameter, or the empty string "" if no matching parameter exists.

2.  Because the id parameter is untrusted user input, we should validate it to make sure it’s sane and sensible. 

we want to check that it contains a positive integer value. We can do this by trying to convert the string value to an integer with the strconv.Atoi() function, and then checking the value is greater than zero.

/ Extract the value of the id parameter from the query string and try to

    // convert it to an integer using the strconv.Atoi() function. If it can't

    // be converted to an integer, or the value is less than 1, we return a 404 page

    // not found response.

    id, err := strconv.Atoi(r.URL.Query().Get("id"))
    if err != nil || id < 1 {
        http.NotFound(w, r)
        return
    }

    // Use the fmt.Fprintf() function to interpolate the id value with our response

    // and write it to the http.ResponseWriter.

    fmt.Fprintf(w, "Display a specific snippet with ID %!d(MISSING)...", id)

Let’s try this out.


If you take a look at the documentation for the fmt.Fprintf() function you’ll notice that it takes an io.Writer as the first parameter…

…but we passed it our http.ResponseWriter object instead — and it worked fine.
We’re able to do this because the io.Writer type is an interface, and the http.ResponseWriter object satisfies the interface because it has a w.Write() method.

### 2.7. Project Structure and Organization

it’s a good time to think how to organize and structure this project.


For this project we’ll implement an outline structure which follows a popular and tried-and-tested approach. It’s a solid starting point, and you should be able to reuse thegeneral structure in a wide variety of projects.

$ mkdir -p cmd/web pkg ui/html ui/static
$ touch cmd/web/main.go
$ touch cmd/web/handlers.go
The structure of your project repository should now look like this:


The cmd directory will contain the application-specific code for the executable applications in the project. For now we’ll have just one executable application — the web application — which will live under the cmd/web directory.

•  The pkg directory will contain the ancillary non-application-specific code used in the project. We’ll use it to hold potentially reusable code like validation helpers and the SQL database models for the project.

The ui directory will contain the user-interface assets used by the web application. Specifically, the ui/html directory will contain HTML templates, and the ui/static directory will contain static files (like CSS and images).

1.  It gives a clean separation between Go and non-Go assets. All the Go code we write will live exclusively under the cmd and pkg directories, leaving the project root free to hold non-Go assets like UI files, makefiles and module definitions (including our go.mod file). This can make things easier to manage when it comes to building and deploying your application in the future.

For example, you might want to add a CLI (Command Line Interface) to automate some administrative tasks in the future. With this structure, you could create this CLI application under cmd/cli and it will be able to import and reuse all the code you’ve written under the pkg directory.


Refactoring Your Existing Code

File: cmd/web/handlers.go
package main

So now our web application consists of multiple Go source code files under the cmd/web directory. To run these, we can use the go run command like so:
$ cd $HOME/code/snippetbox
$ go run ./cmd/web

### 2.8. HTML Templating and Inheritance

Throughout this book we’ll use the naming convention <name>.<role>.tmpl for template files, where <role> is either page, partial or layout. Being able to determine the role of the template from the filename will help us when it comes to creating a cache of templates later in the book.

For this we need to import Go’s html/template package, which provides a family of functions for safely parsing and rendering HTML templates. We can use the functions in this package to parse the template file and then execute the template.

// Use the template.ParseFiles() function to read the template file into a

    // template set. If there's an error, we log the detailed error message and use

    // the http.Error() function to send a generic 500 Internal Server Error

    // response to the user.

    ts, err := template.ParseFiles("./ui/html/home.page.tmpl")
    if err != nil {
        log.Println(err.Error())
        http.Error(w, "Internal Server Error", 500)
        return
    }

// We then use the Execute() method on the template set to write the template

    // content as the response body. The last parameter to Execute() represents any

    // dynamic data that we want to pass in, which for now we'll leave as nil.

    err = ts.Execute(w, nil)

like the header, navigation and metadata inside the <head> HTML element.
To save us typing and prevent duplication, it’s a good idea to create a layout (or master) template which contains this shared content, which we can then compose with the page-specific markup for the individual pages.

Go ahead and create a new ui/html/base.layout.tmpl file

{{define "base"}}

Here we’re using the {{define "base"}}...{{end}} action to define a distinct named template called base, which contains the content we want to appear on every page.

Inside this we use the {{template "title" .}} and {{template "main" .}} actions to denote that we want to invoke other named templates (called title and main) at a particular point in the HTML.


If you’re wondering, the dot at the end of the {{template "title" .}} action represents any dynamic data that you want to pass to the invoked template. We’ll talk more about this later in the book.


 File: ui/html/home.page.tmpl
{{template "base" .}}

{{define "title"}}Home{{end}}

{{define "main"}}
<h2
>Latest Snippets</h2>
<p
>There's nothing to see here yet!</p>
{{end}}

the {{template "base" .}} action. This informs Go that when the home.page.tmpl file is executed, that we want to invoke the named template base.

// Initialize a slice containing the paths to the two files. Note that the

    // home.page.tmpl file must be the *first* file in the slice.

    files := []string{
        "./ui/html/home.page.tmpl",
        "./ui/html/base.layout.tmpl",
    }

// Use the template.ParseFiles() function to read the files and store the

    // templates in a template set. Notice that we can pass the slice of file paths

    // as a variadic parameter?

    ts, err := template.ParseFiles(files...)

So now, instead of containing HTML directly, our template set contains 3 named templates (base, title and main) and an instruction to invoke the base template (which in turn invokes the title and main templates).

Feel free to restart the server and give this a try. You should find that it renders the same output as before (although there will be some extra whitespace in the HTML source where the actions are).


The big benefit of using this pattern to compose templates is that you’re able to cleanly define the page-specific content in individual files on disk

This is particularly helpful for larger applications, where different pages of your application may need to use different layouts.


Create a new ui/html/footer.partial.tmpl file and add a named template called footer like so:
$ touch ui/html/footer.partial.tmpl
  File: ui/html/footer.partial.tmpl
{{define "footer"}}
<footer
>Powered by <a href='https://golang.org/'
>Go</a></footer>
{{end}}

Then update the base template so that it invokes the footer using the {{template "footer" .}} action:

        <!-- Invoke the footer template -->
        {{template "footer" .}}

Finally, we need update the home handler to include the new ui/html/footer.partial.tmpl file when parsing the template files:

In the code above we’ve used the {{template}} action to invoke one template from another. But Go also provides a {{block}}...{{end}} action which you can use instead. This acts like the {{template}} action, except it allows you to specify some default content if the template being invoked doesn’t exist in the current template set.

you don’t need to include any default content between the {{block}} and {{end}} actions. In that case, the invoked template acts like it’s ‘optional’. If the template exists in the template set, then it will be rendered. But if it doesn’t, then nothing will be displayed.

### 2.9. Serving Static Files

[插图]

Go’s net/http package ships with a built-in http.FileServer handler which you can use to serve files over HTTP from a specific directory. 

 so that all requests which begin with "/static/" are handled using this, like so:

Remember: The pattern "/static/" is a subtree path pattern, so it acts a bit like there is a wildcard at the end.

fileServer := http.FileServer(http.Dir("./ui/static/"))

When this handler receives a request, it will remove the leading slash from the URL path and then search the ./ui/static directory for the corresponding file to send to the user.

So, for this to work correctly, we must strip the leading "/static" from the URL path before passing it to http.FileServer. Otherwise it will be looking for a file which doesn’t exist and the user will receive a 404 page not found response. Fortunately Go includes a http.StripPrefix() helper specifically for this task.

    // Note that the path given to the http.Dir function is relative to the project

    // directory root.

    fileServer := http.FileServer(http.Dir("./ui/static/"))

    // Use the mux.Handle() function to register the file server as the handler for

    // all URL paths that start with "/static/". For matching paths, we strip the

    // "/static" prefix before the request reaches the file server.

    mux.Handle("/static/", http.StripPrefix("/static", fileServer))

Once that’s complete, restart the application and open http://localhost:4000/static/ in your browser. 

  <!-- Link to the CSS stylesheet and favicon -->
        <link rel='stylesheet' href='/static/css/main.css'
>

<!-- And include the JavaScript file -->
        <script src="/static/js/main.js" type="text/javascript"
></script>

Make sure you save the changes, then visit http://localhost:4000. Your home page should now look like this:

Go’s file server has a few really nice features that are worth mentioning:

•  It sanitizes all request paths by running them through the path.Clean() function before searching for a file. This removes any . and .. elements from the URL path, which helps to stop directory traversal attacks.

curl -i -H "Range: bytes=100-199" --output - http://localhost:4000/static/img/logo.png
HTTP/1.1 206 Partial Content
Accept-Ranges: bytes
Content-Length: 100
Content-Range: bytes 100-199/1075
Content-Type: image/png

The Last-Modified and If-Modified-Since headers are transparently supported. If a file hasn’t changed since the user last requested it, then http.FileServer will send a 304 Not Modified status code instead of the file itself. This helps reduce latency and processing overhead for both the client and server.

•  The Content-Type is automatically set from the file extension using the mime.TypeByExtension() function. You can add your own custom extensions and content types using the mime.AddExtensionType() function if necessary.

Both Windows and Unix-based operating systems cache recently-used files in RAM, so (for frequently-served files at least) it’s likely that http.FileServer will be serving them from RAM rather than making the relatively slow round-trip to your hard disk.

func downloadHandler(w http.ResponseWriter, r *http.Request) {
    http.ServeFile(w, r, "./ui/static/file.zip")
}

Warning: http.ServeFile() does not automatically sanitize the file path. If you’re constructing a file path from untrusted user input, to avoid directory traversal attacks you must sanitize the input with filepath.Clean() before using it.


If you want to disable directory listings there are a few different approaches you can take. The simplest way? Add a blank index.html file to the specific directory that you want to disable listings for. This will then be served instead of the directory listing, and the user will get a 200 OK response with no body. 

find ./ui/static -type d -exec touch {}/index.html \;

### 2.10. The http.Handler Interface

Strictly speaking, what we mean by handler is an object which satisfies the http.Handler interface:
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}

In simple terms, this basically means that to be a handler an object must have a ServeHTTP() method with the exact signature:
ServeHTTP(http.ResponseWriter, *http.Request)

So in its simplest form a handler might look something like this:
type home struct {}

func (h *home) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("This is my home page"))
}

mux.Handle("/", &home{})

Now, creating an object just so we can implement a ServeHTTP() method on it is long-winded and a bit confusing.

Which is why in practice it’s far more common to write your handlers as a normal function (like we have been so far). For example:
func home(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("This is my home page"))
}

Instead we need to transform it into a handler using the http.HandlerFunc() adapter, like so:
mux := http.NewServeMux()
mux.Handle("/", http.HandlerFunc(home))

The http.HandlerFunc() adapter works by automatically adding a ServeHTTP() method to the home function. When executed, this ServeHTTP() method then simply calls the content of the original home function. It’s a roundabout but convenient way of coercing a normal function into satisfying the http.Handler interface.


 This is just some syntactic sugar that transforms a function to a handler and registers it in one step, instead of having to do it manually. The code above is functionality equivalent to this:
mux := http.NewServeMux()
mux.HandleFunc("/", home)

The http.ListenAndServe() function takes a http.Handler object as the second parameter…
func ListenAndServe(addr string, handler Handler) error
… but we’ve been passing in a servemux.


We were able to do this because the servemux also has a ServeHTTP() method, meaning that it too satisfies the http.Handler interface.
For me it simplifies things to think of the servemux as just being a special kind of handler, which instead of providing a response itself passes the request on to a second handler. 

In fact, what exactly is happening is this: When our server receives a new HTTP request, it calls the servemux’s ServeHTTP() method. This looks up the relevant handler based on the request URL path, and in turn calls that handler’s ServeHTTP() method. You can think of a Go web application as a chain of ServeHTTP() methods being called one after another.


Requests Are Handled Concurrently

There is one more thing that’s really important to point out: all incoming HTTP requests are served in their own goroutine. For busy servers, this means it’s very likely that the code in or called by your handlers will be running concurrently. 

the downside is that you need to be aware of (and protect against) race conditions when accessing shared resources from your handlers.


### 3. Configuration and Error Handling

focus on making improvements that’ll make it easier to manage as it grows.

•  Set configuration settings for your application at runtime in an easy and idiomatic way using command-line flags.
•  Improve your application log messages to include more information, and manage them differently depending on the type (or level) of log message.

•  Centralize error handling so that you don’t need to repeat yourself when writing code.

### 3.1. Managing Configuration Settings

contains a couple of hard-coded configuration settings:

In this chapter we’ll start to improve that, and make the network address for our server configurable at runtime.

The easiest way to accept and parse a command-line flag from your application is with a line of code like this:
addr := flag.String("addr", ":4000", "HTTP network address")
This essentially defines a new command-line flag with the name addr, a default value of ":4000" and some short help text explaining what the flag controls. The value of the flag will be stored in the addr variable at runtime.

// Importantly, we use the flag.Parse() function to parse the command-line flag.

    // This reads in the command-line flag value and assigns it to the addr

    // variable. You need to call this *before* you use the addr variable

    // otherwise it will always contain the default value of ":4000". If any errors are

    // encountered during parsing the application will be terminated.

    flag.Parse()


    // The value returned from the flag.String() function is a pointer to the flag

    // value, not the value itself. So we need to dereference the pointer (i.e.

    // prefix it with the * symbol) before using it. Note that we're using the

    // log.Printf() function to interpolate the address with the log message.

    log.Printf("Starting server on %!s(MISSING)", *addr)
    err := http.ListenAndServe(*addr, mux)

Save this file and try using the -addr flag when you start the application. 

Note: Ports 0-1023 are restricted and (typically) can only be used by services which have root privileges. If you try to use one of these ports you should get a bind: permission denied error message on start-up.


Command-line flags are completely optional. For instance, if you run the application with no -addr flag the server will fall back to listening on address :4000 (which is the default value we specified).

we’ve used the flag.String() function to define the command-line flag. This has the benefit of converting whatever value the user provides at runtime to a string type.

Automated Help
Another great feature is that you can use the -help flag to list all the available command-line flags for an application and their accompanying help text. Give it a try:


you’re probably thinking what about environment variables? Surely it’s good-practice to store configuration settings there

If you want, you can store your configuration settings in environment variables and access them directly from your application by using the os.Getenv() function like so:
addr := os.Getenv("SNIPPETBOX_ADDR")

But this has some drawbacks compared to using command-line flags. You can’t specify a default setting (the return value from os.Getenv() is the empty string if the environment variable doesn’t exist), you don’t get the -help functionality

and the return value from os.Getenv() is always a string. You don’t get automatic type conversions like you do with flag.Int() and the other command line flag functions.

Instead, you can get the best of both worlds by passing the environment variable as a command-line flag when starting the application. For example:
$ export SNIPPETBOX_ADDR=":9999"
$ go run ./cmd/web -addr=$SNIPPETBOX_ADDR

You must explicitly use -flag=false if you want to set a boolean flag value to false.

Pre-Existing Variables
It’s possible to parse command-line flag values into the memory addresses of pre-existing variables, using the flag.StringVar(), flag.IntVar(), flag.BoolVar() and other functions. This can be useful if you want to store all your configuration settings in a single struct.

type Config struct {
    Addr      string
    StaticDir string
}

...

cfg := new(Config)
flag.StringVar(&cfg.Addr, "addr", ":4000", "HTTP network address")
flag.StringVar(&cfg.StaticDir, "static-dir", "./ui/static", "Path to static assets")
flag.Parse()

### 3.2. Leveled Logging

At the moment in our main.go file we’re outputting log messages using the log.Printf() and log.Fatal() functions.


The log.Fatal() function will also call os.Exit(1) after writing the message, causing the application to immediately exit.

log.Printf("Starting server on %!s(MISSING)", *addr) // Information message

err := http.ListenAndServe(*addr, mux)
log.Fatal(err) // Error message

Let’s improve our application by adding some leveled logging capability, so that information and error messages are managed slightly differently. Specifically:
•  We will prefix informational messages with "INFO" and output the message to standard out (stdout).

along with the relevant file name and line number that called the logger (to help with debugging).


There are a couple of different ways to do this, but a simple and clean approach is to use the log.New() function to create two new custom loggers.


    // Use log.New() to create a logger for writing information messages. This takes

    // three parameters: the destination to write the logs to (os.Stdout), a string

    // prefix for message (INFO followed by a tab), and flags to indicate what

    // additional information to include (local date and time). Note that the flags

    // are joined using the bitwise OR operator |.

    infoLog := log.New(os.Stdout, "INFO\t", log.Ldate|log.Ltime)

// Create a logger for writing error messages in the same way, but use stderr as

    // the destination and use the log.Lshortfile flag to include the relevant

    // file name and line number.

    errorLog := log.New(os.Stderr, "ERROR\t", log.Ldate|log.Ltime|log.Lshortfile)


    // Write messages using the two new loggers, instead of the standard logger.

    infoLog.Printf("Starting server on %!s(MISSING)", *addr)
    err := http.ListenAndServe(*addr, mux)
    errorLog.Fatal(err)

and our error message also includes the file name and line number (main.go:30) that called the logge

Tip: If you want to include the full file path in your log output, instead of just the file name, you can use the log.Llongfile flag instead of log.Lshortfile when creating your custom logger. You can also force your logger to use UTC datetimes (instead of local ones) by adding the log.LUTC flag.

Decoupled Logging
A big benefit of logging your messages to the standard streams (stdout and stderr) like we are is that your application and logging are decoupled. Your application itself isn’t concerned with the routing or storage of the logs, and that can make it easier to manage the logs differently depending on the environment.

In staging or production environments, you can redirect the streams to a final destination for viewing and archival. This destination could be on-disk files, or a logging service such as Splunk. Either way, the final destination of the logs can be managed by your execution environment independently of the application.

$ go run ./cmd/web >>/tmp/info.log 2>>/tmp/error.log
Note: Using the double arrow >> will append to an existing file, instead of truncating it when starting the application.

There is one more change we need to make to our application. By default, if Go’s HTTP server encounters an error it will log it using the standard logger. For consistency it’d be better to use our new errorLog logger instead.

To make this happen we need to initialize a new http.Server struct containing the configuration settings for our server, instead of using the http.ListenAndServe() shortcut.



    // Initialize a new http.Server struct. We set the Addr and Handler fields so

    // that the server uses the same network address and routes as before, and set

    // the ErrorLog field so that the server now uses the custom errorLog logger in

    // the event of any problems.

    srv := &http.Server{
        Addr:     *addr,
        ErrorLog: errorLog,
        Handler:  mux,
    }

err := srv.ListenAndServe()

you should avoid using the Panic() and Fatal() variations outside of your main() function — it’s good practice to return errors instead, and only panic or exit directly from main().


Concurrent Logging
Custom loggers created by log.New() are concurrency-safe. You can share a single logger and use it across multiple goroutines and in your handlers without needing to worry about race conditions.

Logging to a File


you can always open a file in Go and use it as your log destination. As a rough example:
f, err := os.OpenFile("/tmp/info.log", os.O_RDWR|os.O_CREATE, 0666)
if err != nil {
    log.Fatal(err)
}
defer f.Close()

infoLog := log.New(f, "INFO\t", log.Ldate|log.Ltime)

### 3.3. Dependency Injection

This raises a good question: how can we make our new errorLog logger available to our home function from main()?


 What we really want to answer is: how can we make any dependency available to our handlers?
There are a few different ways to do this, the simplest being to just put the dependencies in global variables. But in general, it is good practice to inject dependencies into your handlers. It makes your code more explicit, less error-prone and easier to unit test than if you use global variables.

For applications where all your handlers are in the same package, like ours, a neat way to inject dependencies is to put them into a custom application struct, and then define your handler functions as methods against application.

// Define an application struct to hold the application-wide dependencies for the

// web application. For now we'll only include fields for the two custom loggers, but

// we'll add more to it as the build progresses.

type application struct {
    errorLog *log.Logger
    infoLog  *log.Logger
}

update your handler functions so that they become methods against the application struct…

/ Change the signature of the home handler so it is defined as a method against

// *application.

func (app *application) home(w http.ResponseWriter, r *http.Request) {

        // Because the home handler function is now a method against application

        // it can access its fields, including the error logger. We'll write the log

        // message to this instead of the standard logger.

        app.errorLog.Println(err.Error())

// Initialize a new instance of application containing the dependencies.

    app := &application{
        errorLog: errorLog,
        infoLog:  infoLog,
    }

I understand that this approach might feel a bit complicated and convoluted, especially when an alternative is to simply make the infoLog and errorLog loggers global variables. But stick with me. As the application grows, and our handlers start to need more dependencies, this pattern will begin to show its worth.

Closures for Dependency Injection
The pattern that we’re using to inject dependencies won’t work if your handlers are spread across multiple packages. In that case, an alternative approach is to create a config package exporting an Application struct and have your handler functions close over this to form a closure. 

    app := &config.Application{
        ErrorLog: log.New(os.Stderr, "ERROR\t", log.Ldate|log.Ltime|log.Lshortfile)
    }

You can find a complete and more concrete example of how to use the closure pattern in this Gist.

### 3.4. Centralized Error Handling

Let’s neaten up our application by moving some of the error handling code intohelper methods. This will help separate our concerns and stop us repeating codeas we progress through the build.

File: cmd/web/helpers.go
package main

// The serverError helper writes an error message and stack trace to the errorLog,

// then sends a generic 500 Internal Server Error response to the user.

func (app *application) serverError(w http.ResponseWriter, err error) {
    trace := fmt.Sprintf("%!s(MISSING)\n%!s(MISSING)", err.Error(), debug.Stack())
    app.errorLog.Println(trace)

    http.Error(w, http.StatusText(http.StatusInternalServerError), http.StatusInternalServerError)
}

/ The clientError helper sends a specific status code and corresponding description

// to the user. We'll use this later in the book to send responses like 400 "Bad

// Request" when there's a problem with the request that the user sent.

func (app *application) clientError(w http.ResponseWriter, status int) {
    http.Error(w, http.StatusText(status), status)
}

•  In the serverError() helper we use the debug.Stack() function to get a stack trace for the current goroutine and append it to the log message. Being able to see the execution path of the application via the stack trace can be helpful when you’re trying to debug errors.


In the clientError() helper we use the http.StatusText() function to automatically generate a human-friendly text representation of a given HTTP status code. For example, http.StatusText(400) will return the string "Bad Request".


We’ve started using the net/http package’s named constants for HTTP status codes, instead of integers. In the serverError() helper we’ve used the constant http.StatusInternalServerError instead of writing 500, and in the notFound() helper we’ve used the constant http.StatusNotFound instead of writing 404.


Using status constants is a nice touch which helps make your code clear and self-documenting — especially when dealing with less-commonly-used status codes. You can find the complete list of status code constants here.


       app.notFound(w) // Use the notFound() helper

What we want to report is the file name and line number one step back in the stack trace, which would give us a clearer idea of where the error actually originated from.
We can do this by changing the serverError() helper to use our logger’s Output() function and setting the frame depth to 2. Reopen your helpers.go file and update it like so:

trace := fmt.Sprintf("%!s(MISSING)\n%!s(MISSING)", err.Error(), debug.Stack())
    app.errorLog.Output(2, trace)

And if you try again now, you should find that the appropriate file name and line number (handlers.go:25) is being reported:

### 3.5. Isolating the Application Routes

Our main() function is beginning to get a bit crowded, so to keep it clear and focused I’d like to move the route declarations for the application into a standalone routes.go file

Handler:  app.routes(), // Call the new app.routes() method

This is quite a bit neater. The routes for our application are now isolated and encapsulated in the app.routes() method, and the responsibilities of our main() function are limited to:
•  Parsing the runtime configuration settings for the application;
•  Establishing the dependencies for the handlers; and
•  Running the HTTP server.


### 4. Database-Driven Responses

•  Connect to MySQL from your web application (specifically, you’ll learn how to establish a pool of reusable connections).
•  Create a standalone models package, so that your database logic is reusable and decoupled from your web application.


•  Prevent SQL injection attacks by correctly using placeholder parameters.

•  Use transactions, so that you can execute multiple SQL statements in one atomic action.

### 4.1. Setting Up MySQL

Copy and paste the following commands into the mysql prompt to create a new snippetbox database using UTF8 encoding.
-- Create a new UTF-8 `snippetbox` database.

CREATE DATABASE snippetbox CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Switch to using the `snippetbox` database.

USE snippetbox;

Then copy and paste the following SQL statement to create a new snippets table to hold the text snippets for our application:


id INTEGER NOT NULL PRIMARY KEY AUTO_INCREMENT,

-- Add an index on the created column.

CREATE INDEX idx_snippets_created ON snippets(created);

Each record in this table will have an integer id field which will act as the unique identifier for the text snippet.

We’ll also keep some metadata about the times that the snippet was created and when it expires.

Instead it’s better to create a database user with restricted permissions on the database.

So, while you’re still connected to the MySQL prompt run the following commands to create a new web user with SELECT and INSERT privileges only on the database.

CREATE USER 'web'@'localhost';
GRANT SELECT, INSERT, UPDATE ON snippetbox.* TO 'web'@'localhost';
-- Important: Make sure to swap 'pass' with a password of your own choosing.

ALTER USER 'web'@'localhost' IDENTIFIED BY 'pass';

If the permissions are working correctly you should find that you’re able to perform SELECT and INSERT operations on the database correctly, but other commands such as DROP TABLE and GRANT will fail.


mysql> DROP TABLE snippets;
ERROR 1142 (42000): DROP command denied to user 'web'@'localhost' for table 'snippets'

### 4.2. Installing a Database Driver

To use MySQL from our Go web application we need to install a database driver. This essentially acts as a middleman, translating commands between Go and the MySQL database itself.

To download it, go to your project directory and run the go get command like so:
$ cd $HOME/code/snippetbox
$ go get github.com/go-sql-driver/mysql@v1

Notice here that we’re postfixing the package path with @v1 to indicate that we want to download the latest available version of the package with the major release number 1.


Or if you want to download a specific version of a package, you can use the full version number. For example:
$ go get github.com/go-sql-driver/mysql@v1.0.3

Unlike the go.mod file, go.sum isn’t designed to be human-editable and generally you won’t need to open it. But it serves two useful functions:
•  If you run the go mod verify command from your terminal, this will verify that the checksums of the downloaded packages on your machine match the entries in go.sum, so you can be confident that they haven’t been altered.

•  If someone else needs to download all the dependencies for the project — which they can do by running go mod download — they will get an error if there is any mismatch between the dependencies they are downloading and the checksums in the file.

Upgrading Packages
Once a package has been downloaded and added to your go.mod file the package and version are ‘fixed’. But there’s many reasons why you might want to upgrade to use a newer version of a package in the future.
To upgrade to latest available minor or patch release of a package, you can simply run go get with the -u flag like so:


go mod tidy, which will automatically remove any unused packages from your go.mod and go.sum files.
$ go mod tidy -v


### 4.3. Creating a Database Connection Pool

// The sql.Open() function initializes a new sql.DB object, which is essentially a

// pool of database connections.

db, err := sql.Open("mysql", "web:pass@/snippetbox?parseTime=true")

There are a few things about this code to explain and emphasize:


•  The parseTime=true part of the DSN above is a driver-specific parameter which instructs our driver to convert SQL TIME and DATE fields to Go time.Time objects.

The sql.Open() function returns a sql.DB object. This isn’t a database connection — it’s a pool of many connections. This is an important difference to understand. Go manages these connections as needed, automatically opening and closing connections to the database via the driver.

The connection pool is safe for concurrent access, so you can use it from web application handlers safely.
•  The connection pool is intended to be long-lived. In a web application it’s normal to initialize the connection pool in your main() function and then pass the pool to your handlers. You shouldn’t call sql.Open() in a short-lived handler itself — it would be a waste of memory and network resources.

Let’s look at how to use sql.Open() in practice. Open up your main.go file and add the following code:

// To keep the main() function tidy I've put the code for creating a connection

    // pool into the separate openDB() function below. We pass openDB() the DSN

    // from the command-line flag.

    db, err := openDB(*dsn)
    if err != nil {
        errorLog.Fatal(err)
    }


    // We also defer a call to db.Close(), so that the connection pool is closed

    // before the main() function exits.

    defer db.Close()

  // Because the err variable is now already declared in the code above, we need

    // to use the assignment operator = here, instead of the := 'declare and assign'

    // operator.

    err = srv.ListenAndServe()
    errorLog.Fatal(err)

// The openDB() function wraps sql.Open() and returns a sql.DB connection pool

// for a given DSN.

func openDB(dsn string) (*sql.DB, error) {
    db, err := sql.Open("mysql", dsn)
    if err != nil {
        return nil, err
    }
    if err = db.Ping(); err != nil {
        return nil, err
    }
    return db, nil
}

•  The sql.Open() function doesn’t actually create any connections, all it does is initialize the pool for future use. Actual connections to the database are established lazily, as and when needed for the first time. So to verify that everything is set up correctly we need to use the db.Ping() method to create a connection and check for any errors.


At this moment in time, the call to defer db.Close() is a bit superfluous. Our application is only ever terminated by a signal interrupt (i.e. Ctrl+c) or by errorLog.Fatal(). In both of those cases, the program exits immediately and deferred functions are never run.

If the application fails to start and you get an "Access denied..." error message like below, then the problem probably lies with your DSN. Double-check that the username and password are correct, that your database users have the right permissions, and that your MySQL instance is using standard settings.

### 4.4. Designing a Database Model

Remember: The pkg directory is being used to hold ancillary non-application-specific code, which could potentially be reused. A database model which could be used by other applications in the future (like a command line interface application) fits the bill here.


We’ll start by using the pkg/models/models.go file to define the top-level data types that our database model will use and return.

var ErrNoRecord = errors.New("models: no matching record found")



type Snippet struct {
    ID      int
    Title   string
    Content string
    Created time.Time
    Expires time.Time
}

In this file we’re going to define a new SnippetModel type and implement some methods on it to access and manipulate the database. 

// Define a SnippetModel type which wraps a sql.DB connection pool.

type SnippetModel struct {
    DB *sql.DB
}

// This will return a specific snippet based on its id.

func (m *SnippetModel) Get(id int) (*models.Snippet, error) {
    return nil, nil
}

// This will return the 10 most recently created snippets.

func (m *SnippetModel) Latest() ([]*models.Snippet, error) {
    return nil, nil
}

To use this model in our handlers we need to establish a new SnippetModel struct in main() and then inject it as a dependency via the application struct — just like we have with our other dependencies.


    "alexedwards.net/snippetbox/pkg/models/mysql" // New import

// Add a snippets field to the application struct. This will allow us to

// make the SnippetModel object available to our handlers.

type application struct {
    errorLog *log.Logger
    infoLog  *log.Logger
    snippets *mysql.SnippetModel
}



    db, err := openDB(*dsn)
    if err != nil {
        errorLog.Fatal(err)
    }
    defer db.Close()


    // Initialize a mysql.SnippetModel instance and add it to the application

    // dependencies.

    app := &application{
        errorLog: errorLog,
        infoLog:  infoLog,
        snippets: &mysql.SnippetModel{DB: db},
    }

as our application continues to grow it should start to become clearer why we’re structuring things the way we are.
If you take a step back, you might be able to see a few benefits emerging:


There’s a clean separation of concerns. Our database logic isn’t tied to our handlers which means that handler responsibilities are limited to HTTP stuff (i.e. validating requests and writing responses). This will make it easier to write tight, focused, unit tests in the future.


By creating a custom SnippetModel type and implementing methods on it we’ve been able to make our model a single, neatly encapsulated object, which we can easily initialize and then pass to our handlers as a dependency. Again, this makes for easier to maintain, testable code.

Because the model actions are defined as methods on an object — in our case SnippetModel — there’s the opportunity to create an interface and mock it for unit testing purposes.

•  And finally, the directory structure scales nicely if your project has multiple back ends. For example, if some of your data is held in Redis you could put all the models for it in a pkg/models/redis package.


### 4.5. Executing SQL Statements

Because the data we’ll be using will ultimately be untrusted user input from a form, it’s good practice to use placeholder parameters instead of interpolating data in the SQL query.

Go provides three different methods for executing database queries:
•  DB.Query() is used for SELECT queries which return multiple rows.
•  DB.QueryRow() is used for SELECT queries which return a single row.
•  DB.Exec() is used for statements which don’t return rows (like INSERT and DELETE).


 // Write the SQL statement we want to execute. I've split it over two lines

    // for readability (which is why it's surrounded with backquotes instead

    // of normal double quotes).

    stmt := `INSERT INTO snippets (title, content, created, expires)
    VALUES(?, ?, UTC_TIMESTAMP(), DATE_ADD(UTC_TIMESTAMP(), INTERVAL ? DAY))`

    // Use the Exec() method on the embedded connection pool to execute the

    // statement. The first parameter is the SQL statement, followed by the

    // title, content and expiry values for the placeholder parameters. This

    // method returns a sql.Result object, which contains some basic

    // information about what happened when the statement was executed.

    result, err := m.DB.Exec(stmt, title, content, expires)
    if err != nil {
        return 0, err
    }


    // The ID returned has the type int64, so we convert it to an int type

    // before returning.

    return int(id), nil

Let’s quickly discuss the sql.Result interface returned by DB.Exec(). This provides two methods:
•  LastInsertId() — which returns the integer (an int64) generated by the database in response to a command. Typically this will be from an “auto increment” column when inserting a new row, which is exactly what’s happening in our case.
•  RowsAffected() — which returns the number of rows (as an int64) affected by the statement.

Important: Not all drivers and databases support the LastInsertId() and RowsAffected() methods. For example, LastInsertId() is not supported by PostgreSQL. So if you’re planning on using these methods it’s important to check the documentation for your particular driver first.

Also, it is perfectly acceptable (and common) to ignore the sql.Result return value if you don’t need it. Like so:
_, err := m.DB.Exec("INSERT INTO ...", ...)

   // Pass the data to the SnippetModel.Insert() method, receiving the

    // ID of the new record back.

    id, err := app.snippets.Insert(title, content, expires)

note that the -L flag instructscurl to automatically follow redirects

Placeholder Parameters
In the code above we constructed our SQL statement using placeholder parameters, where ? acted as a placeholder for the data we want to insert.
The reason for using placeholder parameters to construct our query (rather than string interpolation) is to help avoid SQL injection attacks from any untrusted user-provided input.

The database then executes the prepared statement using these parameters. Because the parameters are transmitted later, after the statement has been compiled, the database treats them as pure data. They can’t change the intent of the statement. So long as the original statement is not derived from an untrusted data, injection cannot occur.

3.  It then closes (or deallocates) the prepared statement on the database.


The placeholder parameter syntax differs depending on your database. MySQL, SQL Server and SQLite use the ? notation, but PostgreSQL uses the $N notation. For example, if you were using PostgreSQL instead you would write:
_, err := m.DB.Exec("INSERT INTO ... VALUES ($1, $2, $3)", ...)

### 4.6. Single-record SQL Queries

This returns a pointer to a sql.Row object which

    // holds the result from the database.

    row := m.DB.QueryRow(stmt, id)

    // Initialize a pointer to a new zeroed Snippet struct.

    s := &models.Snippet{}

    // Use row.Scan() to copy the values from each field in sql.Row to the

    // corresponding field in the Snippet struct. Notice that the arguments

    // to row.Scan are *pointers* to the place you want to copy the data into,

    // and the number of arguments must be exactly the same as the number of

    // columns returned by your statement.

    err := row.Scan(&s.ID, &s.Title, &s.Content, &s.Created, &s.Expires)


        if errors.Is(err, sql.ErrNoRows) {
            return nil, models.ErrNoRecord
        } else {
             return nil, err
        }

    // If everything went OK then return the Snippet object.

    return s, nil

Aside: You might be wondering why we’re returning the models.ErrNoRecord error instead of sql.ErrNoRows directly. The reason is to help encapsulate the model completely, so that our application isn’t concerned with the underlying datastore or reliant on datastore-specific errors for its behavior.

•  CHAR, VARCHAR and TEXT map to string.
•  BOOLEAN maps to bool.
•  INT maps to int; BIGINT maps to int64.
•  DECIMAL and NUMERIC map to float.
•  TIME, DATE and TIMESTAMP map to time.Time.

A quirk of our MySQL driver is that we need to use the parseTime=true parameter in our DSN to force it to convert TIME and DATE fields to time.Time. Otherwise it returns these as []byte objects. This is one of the many driver-specific parameters that it offers.

if errors.Is(err, models.ErrNoRecord) {
            app.notFound(w)


    // Write the snippet data as a plain-text HTTP response body.

    fmt.Fprintf(w, "%!v(MISSING)", s)

Let’s give this a try.

The first thing is that sql.ErrNoRows is an example of what is known as a sentinel error — which we can roughly define as an error object stored in an global variable. Typically you create them using the errors.New() function.

 couple of examples of sentinel errors from the standard library are io.ErrUnexpectedEOF and bytes.ErrTooLarge, and our own models.ErrNoRecord error that we just created is also an example of a sentinel error.

But from Go 1.13 onwards it is better to use the errors.Is() function instead, like so:
if errors.Is(err, sql.ErrNoRows) {
    // Do something

} else {
    // Do something else

}
The reason for this is that Go 1.13 introduced the ability to wrap errors to add additional information. And, if an sentinel error is wrapped, then the old style of checking for a match will cease to work because the wrapped error is not equal to the original sentinel error.

In contrast, the errors.Is() function works by unwrapping errors — if necessary — before checking for a match.
So basically, if you are running Go 1.13 or newer, prefer to use errors.Is(). It’s a sensible way to future-proof your code and prevent issues caused by you — or any packages that your code imports — wrapping errors in the future.

In practice, you can shorten the code (or at least, the number of lines!) by leveraging the fact that errors from DB.QueryRow() are deferred until Scan() is called. 

  s := &models.Snippet{}
    err := m.DB.QueryRow("SELECT ...", id).Scan(&s.ID, &s.Title, &s.Content, &s.Created, &s.Expires)

### 4.7. Multiple-record SQL Queries

updating the SnippetModel.Latest() method to return the most recently created ten snippets (so long as they haven’t expired) using the following SQL query:
SELECT id, title, content, created, expires FROM snippets
WHERE expires > UTC_TIMESTAMP() ORDER BY created DESC LIMIT 10

   // Use the Query() method on the connection pool to execute our

    // SQL statement. This returns a sql.Rows resultset containing the result of

    // our query.

    rows, err := m.DB.Query(stmt)

/ We defer rows.Close() to ensure the sql.Rows resultset is

    // always properly closed before the Latest() method returns. This defer

    // statement should come *after* you check for an error from the Query()

    // method. Otherwise, if Query() returns an error, you'll get a panic

    // trying to close a nil resultset.

    defer rows.Close()


    // Initialize an empty slice to hold the models.Snippets objects.

    snippets := []*models.Snippet{}


    for rows.Next() {
        // Create a pointer to a new zeroed Snippet struct.

        s := &models.Snippet{}
        // Use rows.Scan() to copy the values from each field in the row to the

        // new Snippet object that we created. Again, the arguments to row.Scan()

        // must be pointers to the place you want to copy the data into, and the

        // number of arguments must be exactly the same as the number of

        // columns returned by your statement.

        err = rows.Scan(&s.ID, &s.Title, &s.Content, &s.Created, &s.Expires)
        if err != nil {
            return nil, err
        }
        // Append it to the slice of snippets.

        snippets = append(snippets, s)

    // When the rows.Next() loop has finished we call rows.Err() to retrieve any

    // error that was encountered during the iteration. It's important to

    // call this - don't assume that a successful iteration was completed

    // over the whole resultset.

    if err = rows.Err(); err != nil {
        return nil, err
    }

    // If everything went OK then return the Snippets slice.

    return snippets, nil

Important: Closing a resultset with defer rows.Close() is critical here. As long as a resultset is open it will keep the underlying database connection open… so if something goes wrong in this method and the resultset isn’t closed, it can rapidly lead to all the connections in your pool being used up.

for _, snippet := range s {
        fmt.Fprintf(w, "%!v(MISSING)\n", snippet)
    }

### 4.8. Transactions and Other Details

If you’re coming from Ruby, Python or PHP, the code for querying SQL databases may feel a bit verbose, especially if you’re used to dealing with an abstraction layer or ORM.

But the upside of the verbosity is that our code is non-magical; we can understand and control exactly what is going on. And with a bit of time, you’ll find that the patterns for making SQL queries become familiar and you can copy-and-paste from previous work.


One thing that Go doesn’t do very well is managing NULL values in database records.

If we queried that row, then rows.Scan() would return an error because it can’t convert NULL into a string:

But, as a rule, the easiest thing to do is simply avoid NULL values altogether. Set NOT NULL constraints on all your database columns, like we have done in this book, along with sensible DEFAULT values as necessary.

To guarantee that the same connection is used you can wrap multiple statements in a transaction. Here’s the basic pattern:

    // Calling the Begin() method on the connection pool creates a new sql.Tx

    // object, which represents the in-progress database transaction.

    tx, err := m.DB.Begin()

_, err = tx.Exec("INSERT INTO ...")
    if err != nil {
        // If there is any error, we call the tx.Rollback() method on the

        // transaction. This will abort the transaction and no changes will be

        // made to the database.

        tx.Rollback()
        return err
    }


    // to the database with the tx.Commit() method. It's really important to ALWAYS

    // call either Rollback() or Commit() before your function returns. If you

    // don't the connection will stay open and not be returned to the connection

    // pool. This can lead to hitting your maximum connection limit/running out of

    // resources.

    err = tx.Commit()
    return err
}

Transactions are also super-useful if you want to execute multiple SQL statements as a single atomic action. So long as you use the tx.Rollback() method in the event of any errors, the transaction ensures that either:
•  All statements are executed successfully; or
•  No statements are executed and the database remains unchanged.

By default, there is no limit on the maximum number of open connections (idle + in-use) at one time, but the default maximum number of idle connections in the pool is 2. You can change these defaults with the SetMaxOpenConns() and SetMaxIdleConns() methods. 

For example, the default limit for MySQL is 151. So leaving SetMaxOpenConns() totally unlimited or setting it to greater than 151 may result in your database returning a "too many connections" error under high load. To prevent this error from happening, you’d need to set the maximum open connections value to comfortably below 151.


But in turn that creates another problem. When the SetMaxOpenConns() limit is reached, any new database tasks that your application needs to execute will be forced to wait until a connection becomes free.

but in a web application it’s arguably better to immediately log the "too many connections" error message and send a 500 Internal Server Error to the user, rather than having their HTTP request hang and potentially timeout while waiting for a free connection.

That’s why I haven’t used the SetMaxOpenConns() and SetMaxIdleConns() methods in our application, and left the behavior of sql.DB as the default settings.

As I mentioned earlier, the Exec(), Query() and QueryRow() methods all use prepared statements behind the scenes to help prevent SQL injection attacks. They set up a prepared statement on the database connection, run it with the parameters provided, and then close the prepared statement.


// We need somewhere to store the prepared statement for the lifetime of our

// web application. A neat way is to embed it alongside the connection pool.

type ExampleModel struct {
    DB         *sql.DB
    InsertStmt *sql.Stmt
}

// Create a constructor for the model, in which we set up the prepared

// statement.

func NewExampleModel(db *sql.DB) (*ExampleModel, error) {
    // Use the Prepare method to create a new prepared statement for the

    // current connection pool. This returns a sql.Stmt object which represents

    // the prepared statement.

    insertStmt, err := db.Prepare("INSERT INTO ...")
    if err != nil {
        return nil, err
    }

    // Store it in our ExampleModel object, alongside the connection pool.

    return &ExampleModel{db, insertStmt}, nil

// In the web application's main function we will need to initialize a new

// ExampleModel struct using the constructor function.

    // Create a new ExampleModel object, which includes the prepared statement.

    exampleModel, err := NewExampleModel(db)
    if err != nil {
       errorLog.Fatal(err)
    }

    // Defer a call to Close on the prepared statement to ensure that it is

    // properly closed before our main function terminates.

    defer exampleModel.InsertStmt.Close()

The sql.Stmt object then remembers which connection in the pool was used. The next time, the sql.Stmt object will attempt to use the same database connection again. If that connection is closed or in use (i.e. not idle) the statement will be re-prepared on another connection.

This can lead to statements being prepared and re-prepared more often than you would expect — or even running into server-side limits on the number of statements (in MySQL the default maximum is 16,382 prepared statements).

The code too is more complicated than not using prepared statements.
So, there is a trade-off to be made between performance and complexity. As with anything, you should measure the actual performance benefit of implementing your own prepared statements to determine if it’s worth doing. For most cases, I would suggest that using the regular Query(), QueryRow() and Exec() methods — without preparing statements yourself — is a reasonable starting point.

### 5. Dynamic HTML Templates

Use the various actions and functions in Go’s html/template package to control the display of dynamic data.
•  Create a template cache so that your templates aren’t being read from disk for each HTTP request.

### 5.1. Displaying Dynamic Data

Within your HTML templates, any dynamic data that you pass in is represented by the . character (referred to as dot).

the content is rendered correctly.

But in a real-world application there are often multiple pieces of dynamic datathat you want to display in the same page.A lightweight and type-safe way to achieve this is to wrap your dynamic data in astruct which acts like a single ‘holding structure’ for your data.

// Define a templateData type to act as the holding structure for

// any dynamic data that we want to pass to our HTML templates.

// At the moment it only contains one field, but we'll add more

// to it as the build progresses.

type templateData struct {
    Snippet *models.Snippet
}


    // Create an instance of a templateData struct holding the snippet data.

    data := &templateData{Snippet: s}

Escaping
The html/template package automatically escapes any data that is yielded between {{ }} tags. This behavior is hugely helpful in avoiding cross-site scripting (XSS) attacks, and is the reason that you should use the html/template package instead of the more generic text/template package that Go also provides.

As an example of escaping, if the dynamic data you wanted to yield was:
<span
>{{"<script
>alert('xss attack')</script>"}}</span>
It would be rendered harmlessly as:
<span
>&lt;script&gt;alert(&#39;xss attack&#39;)&lt;/script&gt;</span>


It’s really important to note that when you’re invoking one template from another template, dot needs to be explicitly passed or pipelined to the template being invoked. 

You can also pass parameters to methods. For example, you could use the AddDate() method to add six months to a time like so:
<span
>{{.Snippet.Created.AddDate 0 6 0}}</span>
Notice that this is different syntax to calling functions in Go — the parameters are not surrounded by parentheses and are separated by a single space character, not a comma.

### 5.2. Template Actions and Functions

So now, between {{with .Snippet}} and the corresponding {{end}} tag, the value of dot is set to .Snippet. Dot essentially becomes the models.Snippet struct instead of the parent templateData struct.

•  We want to use the {{if}} action to check whether the slice of snippets is empty or not. If it’s empty, we want to display a "There's nothing to see here yet! message. Otherwise, we want to render a table containing the snippet information.

•  We want to use the {{range}} action to iterate over all snippets in the slice, rendering the contents of each snippet in a table row.

{range .Snippets}}
        <tr
>
            <td
><a href='/snippet?id={{.ID}}'
>{{.Title}}</a></td>
            <td
>{{.Created}}</td>
            <td
>#{{.ID}}</td>
        </tr>
        {{end}}
    </table>
    {{else}}
        <p
>There's nothing to see here... yet!</p>
    {{end}}

### 5.3. Caching Templates

There are two main issues at the moment:

1.  Each and every time we render a web page, our application reads and parses the relevant template files using the template.ParseFiles() function. We could avoid this duplicated work by parsing the files once — when starting the application — and storing the parsed templates in an in-memory cache.
2.  There’s duplicated code in the home and showSnippet handlers, and we could reduce this duplication by creating a helper function.


Let’s tackle the first point first, and create an in-memory map with the type map[string]*template.Template to cache the parsed templates.

// Use the filepath.Glob function to get a slice of all filepaths with

    // the extension '.page.tmpl'. This essentially gives us a slice of all the

    // 'page' templates for the application.

    pages, err := filepath.Glob(filepath.Join(dir, "*.page.tmpl"))
    if err != nil {
        return nil, err
    }

        // Extract the file name (like 'home.page.tmpl') from the full file path

        // and assign it to the name variable.

        name := filepath.Base(page)


        // Use the ParseGlob method to add any 'layout' templates to the

        // template set (in our case, it's just the 'base' layout at the

        // moment).

        ts, err = ts.ParseGlob(filepath.Join(dir, "*.layout.tmpl"))
        if err != nil {
            return nil, err
        }

       // Add the template set to the cache, using the name of the page

        // (like 'home.page.tmpl') as the key.

        cache[name] = ts


    // Return the map.

    return cache, nil

The next step is to initialize this cache in the main() function and make it available to our handlers as a dependency via the application struct, like this:

// Add a templateCache field to the application struct.

type application struct {
    errorLog      *log.Logger
    infoLog       *log.Logger
    snippets      *mysql.SnippetModel
    templateCache map[string]*template.Template
}

// Initialize a new template cache...

    templateCache, err := newTemplateCache("./ui/html/")

So, at this point, we’ve got an in-memory cache of the relevant template set for each of our pages, and our handlers have access to this cache via the application struct.


create a helper method so that we can easily render the templates from the cache.

why the signature for the render() method includes a *http.Request parameter… even though it’s not used anywhere. This is simply to future-proof the method signature, because later in the book we will need access to this.


can dramatically simplify the code in our handlers:



    // Use the new render helper.

    app.render(w, r, "home.page.tmpl", &templateData{
        Snippets: s,
    })

### 5.4. Catching Runtime Errors

we’ve added the line {{len nil}}, which should generate an error at runtime because in Go the value nil does not have a length.

This is pretty bad. Our application has thrown an error, but the user has wrongly been sent a 200 OK response. And even worse, they’ve received a half-complete HTML page.

To fix this we need to make the template render a two-stage process. First, we should make a ‘trial’ render by writing the template into a buffer. If this fails, we can respond to the user with an error message. But if it works, we can then write the contents of the buffer to our http.ResponseWriter.



    // Initialize a new buffer.

    buf := new(bytes.Buffer)

    // Write the template to the buffer, instead of straight to the

    // http.ResponseWriter. If there's an error, call our serverError helper and then

    // return.

    err := ts.Execute(buf, td)
    if err != nil {
        app.serverError(w, err)
        return
    }

    // Write the contents of the buffer to the http.ResponseWriter. Again, this

    // is another time where we pass our http.ResponseWriter to a function that

    // takes an io.Writer.

    buf.WriteTo(w)


Great stuff. That’s looking much better.


### 5.5. Common Dynamic Data

For example, you might want to include the name and profile picture of the current user, or a CSRF token in all pages with forms.


let’s begin with something simple, and say that we want to includethe current year in the footer on every page.

To do this we’ll begin by adding a new CurrentYear field to the templateData struct

// Add a CurrentYear field to the templateData struct.

type templateData struct {
    CurrentYear int
    Snippet     *models.Snippet
    Snippets    []*models.Snippet
}

Then the next step is to create a new addDefaultData() helper method to our application, which will inject the current year into an instance of a templateData struct.

// Create an addDefaultData helper. This takes a pointer to a templateData

// struct, adds the current year to the CurrentYear field, and then returns

// the pointer. Again, we're not using the *http.Request parameter at the

// moment, but we will do later in the book.

func (app *application) addDefaultData(td *templateData, r *http.Request) *templateData {
    if td == nil {
        td = &templateData{}
    }
    td.CurrentYear = time.Now().Year()
    return td
}

### 5.6. Custom Template Functions

how to create your own custom functions to use in Go templates.


There are two main steps to doing this:
1.  We need to create a template.FuncMap object containing the custom humanDate() function.
2.  We need to use the template.Funcs() method to register this before parsing the templates.


        // The template.FuncMap must be registered with the template set before you

        // call the ParseFiles() method. This means we have to use template.New() to

        // create an empty template set, use the Funcs() method to register the

        // template.FuncMap, and then parse the file as normal.

        ts, err := template.New(name).Funcs(functions).ParseFiles(page)

<!-- Use the new template function here -->
            <td
>{{humanDate .Created}}</td>

In the code above, we called our custom template function like this:
<time
>Created: {{humanDate .Created}}</time>
An alternative approach is to use the | character to pipeline values to a function. This works a bit like pipelining outputs from one command to another in Unix terminals. We could re-write the above as:
<time
>Created: {{.Created | humanDate}}</time>

<time
>{{.Created | humanDate | printf "Created: %!s(MISSING)"}}</time>

### 6. Middleware

there’s probably some shared functionality that you want to use for many (or even all) HTTP requests. For example, you might want to log every request, compress every response, or check a cache before passing the request to your handlers.

A common way of organizing this shared functionality is to set it up as middleware. This is essentially some self-contained code which independently acts on a request before or after your normal application handlers.

•  How to create middleware which sets useful security headers on every HTTP response.
•  How to create middleware which logs the requests received by your application.
•  How to create middleware which recovers panics so that they are gracefully handled by your application.

•  How to create and use composable middleware chains to help manage and organize your middleware.

### 6.1. How Middleware Works

“You can think of a Go web application as a chain of ServeHTTP() methods being called one after another.”

Currently, in our application, when our server receives a new HTTP request itcalls the servemux’s ServeHTTP() method. This looks up the relevant handlerbased on the request URL path, and in turn calls that handler’s ServeHTTP()method.

The basic idea of middleware is to insert another handler into this chain. The middleware handler executes some logic, like logging a request, and then calls the ServeHTTP() method of the next handler in the chain.

Regardless of what you do with a closure it will always be able to access the variables that are local to the scope it was created in — which in this case means that fn will always have access to the next variable.
•  We then convert this closure to a http.Handler and return it using the http.HandlerFunc() adapter.


If this feels confusing, you can think of it more simply: myMiddleware is a function that accepts the next handler in a chain as a parameter. It returns a handler which executes some logic and then calls the next handler.


Simplifying the Middleware

If you position your middleware before the servemux in the chain then it will act on every request that your application receives.

 good example of where this would be useful is middleware to log requests —as that’s typically something you would want to do for all requests.Alternatively, you can position the middleware after the servemux in the chain —by wrapping a specific application handler. This would cause your middleware toonly be executed for specific routes.

An example of this would be something like authorization middleware, which you may only want to run on specific routes.
We’ll demonstrate how to do both of these things in practice as we progress through the book.

### 6.2. Setting Security Headers

make our own middleware which automatically adds the following two HTTP headers to every response:
X-Frame-Options: deny
X-XSS-Protection: 1; mode=block
If you’re not familiar with these headers, they essentially instruct the user’s web browser to implement some additional security measures to help prevent XSS and Clickjacking attacks. It’s good practice to include them unless you have a specific reason for not doing so.


        w.Header().Set("X-XSS-Protection", "1; mode=block")
        w.Header().Set("X-Frame-Options", "deny")

        next.ServeHTTP(w, r)

Because we want this middleware to act on every request that is received, weneed it to be executed before a request hits our servemux. We want the flow ofcontrol through our application to look like:[插图]

   // Pass the servemux as the 'next' parameter to the secureHeaders middleware.

    // Because secureHeaders is just a function, and the function returns a

    // http.Handler we don't need to do anything else.

    return secureHeaders(mux)

You should see that the two security headers are now included in every response.

In any middleware handler, code which comes before next.ServeHTTP() will beexecuted on the way down the chain, and any code after next.ServeHTTP() — orin a deferred function — will be executed on the way back up.

// If the user isn't authorized send a 403 Forbidden status and

        // return to stop executing the chain.

        if !isAuthorized(r) {
            w.WriteHeader(http.StatusForbidden)
            return
        }

### 6.3. Request Logging

add some middleware to log HTTP requests.

Notice that this time we’re implementing the middleware as a method on application?
This is perfectly valid to do. Our middleware method has the same signature as before, but because it is a method against application it also has access to the handler dependencies including the information logger.


    // Wrap the existing chain with the logRequest middleware.

    return app.logRequest(secureHeaders(mux))

### 6.4. Panic Recovery

Go’s HTTP server assumes that the effect of any panic is isolated to the goroutine serving the active HTTP request (remember, every request is handled in it’s own goroutine).

But it won’t terminate the application,so importantly, any panic in your handlers won’t bring down your server.

But if a panic does happen in one of our handlers, what will the user see?

$ curl -i http://localhost:4000
curl: (52) Empty reply from server
Unfortunately, all we get is an empty response due to Go closing the underlying HTTP connection following the panic.

This isn’t a great experience for the user. It would be more appropriate and meaningful to send them a proper HTTP response with a 500 Internal Server Error status instead.

A neat way of doing this is to create some middleware which recovers the panic andcalls our app.serverError() helper method.

To do this, we can leverage the fact that deferred functions are always called when the stack is being unwound following a panic.

        // Create a deferred function (which will always be run in the event

        // of a panic as Go unwinds the stack).

        defer func() {
            // Use the builtin recover function to check if there has been a

            // panic or not. If there has...

            if err := recover(); err != nil {
                // Set a "Connection: close" header on the response.

                w.Header().Set("Connection", "close")
                // Call the app.serverError helper method to return a 500

                // Internal Server response.

                app.serverError(w, fmt.Errorf("%!s(MISSING)", err))
            }

•  Setting the Connection: Close header on the response acts as a trigger to make Go’s HTTP server automatically close the current connection after a response has been sent. It also informs the user that the connection will be closed. Note: If the protocol being used is HTTP/2, Go will automatically strip the Connection: Close header from the response (so it is not malformed) and send a GOAWAY frame.

•  The value returned by the builtin recover() function has the type interface{}, and its underlying type could be string, error, or something else — whatever the parameter passed to panic() was.

Let’s now put this to use in the routes.go file, so that it is the first thing in our chain to be executed (so that it covers panics in all subsequent middleware and handlers).

// Wrap the existing chain with the recoverPanic middleware.

    return app.recoverPanic(app.logRequest(secureHeaders(mux)))

// Spin up a new goroutine to do some background processing.

    go func() {
        defer func() {
            if err := recover(); err != nil {
                log.Println(fmt.Errorf("%!s(MISSING)\n%!s(MISSING)", err, debug.Stack()))
            }
        }()

### 6.5. Composable Middleware Chains

because it makes it easy to create composable, reusable, middleware chains — and that can be a real help as your application grows and your routes become more complex. 

But the real power lies in the fact that you can use it to create middleware chains that can be assigned to variables, appended to, and reused.


    standardMiddleware := alice.New(app.recoverPanic, app.logRequest, secureHeaders)

    // Return the 'standard' middleware chain followed by the servemux.

    return standardMiddleware.Then(mux)

Because we have modules enabled for our project, Go is clever enough to recognize that our code is importing a new third-party package and has automatically downloaded justinas/alice for us.

### 7. RESTful Routing

requests to /snippet/create are handled differently based on the request method. Specifically:
•  For GET /snippet/create requests we want to show the user the HTML form for adding a new snippet.
•  For POST /snippet/create requests we want to process this form data and then insert a new snippet record into our database.

### 7.1. Installing a Router

Both have good documentation

A possible drawback is that the package isn’t really maintained anymore.


gorilla/mux is much more full-featured. In addition to method-based routing and support for semantic URLs, you can use it to route based on scheme, host and headers. Regular expression patterns in URLs are also supported. 

So, out of the two packages, we’ll opt for Pat.

### 7.2. Implementing RESTful Routes

mux := pat.New()
mux.Get("/snippet/:id", http.HandlerFunc(app.showSnippet))

The "/snippet/:id" pattern includes a named capture :id. The named capture acts like a wildcard, whereas the rest of the pattern matches literally. Pat will add the contents of the named capture to the URL query string at runtime behind the scenes.

Pat doesn’t allow us to register handler functions directly, so you need to convert themusing the http.HandlerFunc() adapter.

So to ensure that the exact match takes preference, we need to register the exact match routes before any wildcard routes.

    // Because Pat matches the "/" path exactly, we can now remove the manual check

    // of r.URL.Path != "/" from this handler.

// Pat doesn't strip the colon from the named capture key, so we need to

    // get the value of ":id" from the query string instead of "id".

    id, err := strconv.Atoi(r.URL.Query().Get(":id"))

Finally, we need to update the table in our home.page.tmpl file so that the links in the HTML also use the new semantic URL style of /snippet/:id.

### 8. Processing Forms

1.  The user is shown the blank form when they make a GET request to /snippet/create.
2.  The user completes the form and it’s submitted to the server via a POST request to /snippet/create.

3.  The form data will be validated by our createSnippet handler. If there are any validation failures the form will be re-displayed with the appropriate form fields highlighted. If it passes our validation checks, the data for the new snippet will be added to the database and then we’ll redirect the user to "/snippet/:id".

•  Some techniques for performing common validation checks on the form data.
•  A user-friendly pattern for alerting the user to validation failures and re-populating form fields with previously submitted data.

### 8.2. Parsing Form Data

2.  We can then get to the form data contained in r.PostForm by using the r.PostForm.Get() method. For example, we can retrieve the value of the title field with r.PostForm.Get("title"). 

If there is no matching field name in the form this will return the empty string "", similar to the way that query string parameters worked earlier in the book.


    // First we call r.ParseForm() which adds any data in POST request bodies

    // to the r.PostForm map. This also works in the same way for PUT and PATCH

    // requests. If there are any errors, we use our app.ClientError helper to send

    // a 400 Bad Request response to the user.

    err := r.ParseForm()


    // Use the r.PostForm.Get() method to retrieve the relevant data fields

    // from the r.PostForm map.

    title := r.PostForm.Get("title")
    content := r.PostForm.Get("content")
    expires := r.PostForm.Get("expires")

And then submit the form. If everything has worked, you should be redirected to a pagedisplaying your new snippet like so:

But an alternative approach is to use the (subtly different) r.Form map.


The r.PostForm map is populated only for POST, PATCH and PUT requests, and contains the form data from the request body.

In contrast, the r.Form map is populated for all requests (irrespective of their HTTP method), and contains the form data from any request body and any query string parameters. So, if our form was submitted to /snippet/create?foo=bar, we could also get the value of the foo parameter by calling r.Form.Get("foo"). Note that in the event of a conflict, the request body value will take precedent over the query string parameter.

Using the r.Form map can be useful if your application sends data in a HTML form and in the URL

I recommend avoiding these shortcuts because they silently ignore any errors returned by r.ParseForm(). That’s not ideal — it means our application could be encountering errors and failing for users, but there’s no feedback mechanism to let them know.


The underlying type of the r.PostForm map is url.Values, which in turn has the underlying type map[string][]string. 

If you want to change this limit you can use the http.MaxBytesReader() function like so:

// Limit the request body size to 4096 bytes

r.Body = http.MaxBytesReader(w, r.Body, 4096)

### 8.3. Data Validation

We should do this to ensure that the form data is present, of the correct type and meets any business rules that we have.


•  Check that the title, content and expires fields are not empty.
•  Check that the title field is not more than 100 characters long.
•  Check that the expires value matches one of our permitted values (1, 7 or 365 days).



    // Initialize a map to hold any validation errors.

    errors := make(map[string]string)


    // map using the field name as the key.

    if strings.TrimSpace(title) == "" {
        errors["title"] = "This field cannot be blank"
    } else if utf8.RuneCountInString(title) > 100 {
        errors["title"] = "This field is too long (maximum is 100 characters)"
    }

    // If there are any errors, dump them in a plain text HTTP response and return

    // from the handler.

    if len(errors) > 0 {
        fmt.Fprint(w, errors)
        return
    }

Note: When we check the length of the title field, we’re using the utf8.RuneCountInString() function — not Go’s len() function. This is because we want to count the number of characters in the title rather than the number of bytes. To illustrate the difference, the string "Zoë" has 3 characters but a length of 4 bytes because of the umlauted ë character.

If there are any validation errors we want to re-display the form, highlighting the fields which failed validation and automatically re-populating any previously submitted data.
To do this, let’s begin by adding two new fields to our templateData struct: FormErrors to hold any validation errors and FormData to hold any previously submitted data

// Add FormData and FormErrors fields to the templateData struct.

type templateData struct {
    CurrentYear int
    FormData    url.Values
    FormErrors  map[string]string
    Snippet     *models.Snippet
    Snippets    []*models.Snippet
}

Note: In this struct the FormData field has the type url.Values, which is the same underlying type as the r.PostForm map that held the data sent in the request body.



    // If there are any validation errors, re-display the create.page.tmpl

    // template passing in the validation errors and previously submitted

    // r.PostForm data.

    if len(errors) > 0 {
        app.render(w, r, "create.page.tmpl", &templateData{
            FormErrors: errors,
            FormData:   r.PostForm,
        })
        return
    }



So how will we render these errors in the template?
The underlying type of the FormErrors field is a map[string]string (keyed by form field name).

For example, to render any error message for the title field we can use the tag {{.FormErrors.title}} in our template. It’s important to mention that — unlike struct fields — map key names don’t have to be capitalized to access them from a template.


The underlying type of the FormData is url.Values and we can use its Get() method to retrieve the value for a field, just like we did in our createSnippet handler. For example, to render the previously submitted value for the title field we can use the tag {{.FormData.Get "title"}} in our template.

    {{with .FormErrors.content}}
            <label class='error'
>{{.}}</label>
        {{end}}


Before we continue, feel free to spend some time playing around with the form and validation rules until you’re confident that everything is working as you expect it to.

### 8.4. Scaling Data Validation

if your application has many forms then you can end up with quite a lot of repetition in your code and validation rules. 

Let’s address this by creating a forms package to abstract some of this behavior and reduce the boilerplate code in our handler. We won’t actually change how the application works for the user at all; it’s just a refactoring of our codebase.

// Define a new errors type, which we will use to hold the validation error

// messages for forms. The name of the form field will be used as the key in

// this map.

type errors map[string][]string

// Implement an Add() method to add error messages for a given field to the map.

func (e errors) Add(field, message string) {
    e[field] = append(e[field], message)
}



// Create a custom Form struct, which anonymously embeds a url.Values object

// (to hold the form data) and an Errors field to hold any validation errors

// for the form data.

type Form struct {
    url.Values
    Errors errors
}

// Define a New function to initialize a custom Form struct. Notice that

// this takes the form data as the parameter?

func New(data url.Values) *Form {
    return &Form{
        data,
        errors(map[string][]string{}),
    }
}

// Implement a Required method to check that specific fields in the form

// data are present and not blank. If any fields fail this check, add the

// appropriate message to the form errors.

func (f *Form) Required(fields ...string) {
    for _, field := range fields {
        value := f.Get(field)
        if strings.TrimSpace(value) == "" {
            f.Errors.Add(field, "This field cannot be blank")
        }
    }
}

// Implement a Valid method which returns true if there are no errors.

func (f *Form) Valid() bool {
    return len(f.Errors) == 0
}

// Update the templateData fields, removing the individual FormData and

// FormErrors fields and replacing them with a single Form field.

type templateData struct {
    CurrentYear int
    Form        *forms.Form

    // Create a new forms.Form struct containing the POSTed data from the

    // form, then use the validation methods to check the content.

    form := forms.New(r.PostForm)
    form.Required("title", "content", "expires")
    form.MaxLength("title", 100)
    form.PermittedValues("expires", "365", "7", "1")

    // If the form isn't valid, redisplay the template passing in the

    // form.Form object as the data.

    if !form.Valid() {
        app.render(w, r, "create.page.tmpl", &templateData{Form: form})
        return
    }

 Both form data and errors are neatly encapsulated ina single forms.Form object — which we can easily pass to our templates — and itprovides a simple and consistent API for retrieving and displaying both the form data andany error messages.Go ahead and re-run the application now. All being well, you should find that the formand validation rules are working correctly in the same manner as before.

### 9. Stateful HTTP

display a one-time confirmation message

A confirmation message like this should only show up for the user once (immediately after creating the snippet) and no other users should ever see the message. 

To make this work, we need to start sharing data (or state) between HTTP requests for the same user. The most common way to do that is to implement a session for the user.

In this section you’ll learn:
•  What session managers are available to help us implement sessions in Go.
•  How you can customize session behavior (including timeouts and cookie settings) based on your application’s needs.
•  How to use sessions to safely and securely share data between requests for a particular user.


### 9.1. Installing a Session Manager

it’s a good idea to use an existing, well-tested, third-party package.


•  gorilla/sessions is the most established and well-known package. It has a simple and easy-to-use API and supports a huge range of third-party session stores including MySQL, PostgreSQL and Redis. However, importantly (and unfortunately) there are problems with memory leaks and it doesn’t provide a mechanism to renew session tokens 

 It doesn’t suffer from the same security and memory leak issues as Gorilla Sessions, supports automatic loading and saving of session data via middleware, and has a nice interface for type-safe manipulation of data.


golangcollege/sessions provides a cookie-based session store using encrypted andauthenticated cookies. It’s lightweight and focused, with a simple API, and supportsautomatic loading and saving of session data via middleware. Because it uses cookiesto store session data it’s very performant and easy to setup, but there’s a couple ofimportant downsides: the amount of information you can store is limited (to 4KB) andyou can’t revoke sessions in the same way that you can using a server-side store.

In general, if you’re happy to use cookie-based sessions then I recommend using the golangcollege/sessions package, which is exactly what we will do in the rest of this section of the book.

### 9.2. Setting Up the Session Manager

but if you’re going to use it in a production application I recommend reading the documentation and API reference to familiarize yourself with the full range of features.

The session manager holds the configuration settings for our sessions, and also provides some middleware and helper methods to handle the loading and saving of session data.

The other thing that we’ll need is a 32-byte long secret key for encrypting and authenticating the session cookies. 

// Use the sessions.New() function to initialize a new session manager,

    // passing in the secret key as the parameter. Then we configure it so

    // sessions always expires after 12 hours.

    session := sessions.New([]byte(*secret))
    session.Lifetime = 12 * time.Hour

Note: The sessions.New() function returns a Session struct which holds the configuration settings for the session. In the code above we’ve set the Lifetime field of this struct so that sessions expire after 12 hours, but there’s a range of other fields that you can and should configure depending on your application’s needs.

It’s important to note that we don’t need this middleware to act on all our application routes. Specifically, we don’t need it on the /static/ route, because all this does is serve static files and there is no need for any stateful behavior.

    dynamicMiddleware := alice.New(app.session.Enable)

### 9.3. Working with Session Data

let’s put the session functionality to work and use it to persist the confirmation flash message between HTTP requests

Or alternatively, we can use the *Session.GetString() method which takes care of the type conversion for us.

However, because we want our confirmation message to be displayed once — and only once — we’ll also need to remove the message from the session data after retrieving it.
We could use the *Session.Remove() method to do this, but a better option is the *Session.PopString() method, which retrieves a string value for a given key and then deletes it from the session data in one step.



    // Use the Put() method to add a string value ("Your snippet was saved

    // successfully!") and the corresponding key ("flash") to the session

    // data. Note that if there's no existing session for the current user

    // (or their session has expired) then a new, empty, session for them

    // will automatically be created by the session middleware.

    app.session.Put(r, "flash", "Snippet successfully created!")

{{with .Flash}}
            <div class='flash'
>{{.}}</div>
            {{end}}

Remember, the {{with .Flash}} block will only be evaluated if the value of .Flash is not the empty string. So, if there’s no "flash" key in the current user’s session, the result is that the chunk of new markup simply won’t be displayed.

And after redirection you should see the flash message now being displayed:

If you try refreshing the page, you can confirm that the flash message is no longer shown — it was a one-off message for the current user immediately after they created the snippet.

A little improvement we can make (which will save us some work later in the build) is to automate the display of flash messages, so that any message is automatically included the next time any page is rendered.

We can do this by adding any flash message to the template data via the addDefaultData() helper method that we made earlier, like so:

// Add the flash message to the template data, if one exists.

    td.Flash = app.session.PopString(r, "flash")
    return td

the code can be reverted to look like this:

Feel free to try running the application again and creating another snippet. You should find that the flash message functionality still works as expected.

### 10. Security Improvements

we’re going to make some improvements to our application sothat our data is kept secure during transit and our server is better able to deal with somecommon types of Denial-of-Service attacks.

•  How to quickly and easily create a self-signed TLS certificate, using only Go.


•  How to set connection timeouts on our server to mitigate Slowloris and other slow-client attacks.

### 10.1. Generating a Self-Signed TLS Certificate

SSL now has been officially deprecated due to security concerns, but the name still lives on in the public consciousness and is often used interoperably with TLS. For clarity and accuracy, we’ll stick with the term TLS throughout this book.

and is fine for development and testing purposes.

Handily, the crypto/tls package in Go’s standard library includes a generate_cert.go tool that we can use to easily create our own self-signed certificate.

Once you know where it is located, you can then run the generate_cert.go tool like so:
$ go run /usr/local/go/src/crypto/tls/generate_cert.go --rsa-bits=2048 --host=localhost
2018/10/16 11:50:14 wrote cert.pem
2018/10/16 11:50:14 wrote key.pem

And that’s it! We’ve now got a self-signed TLS certificate (and corresponding private key)that we can use during development.

### 10.2. Running a HTTPS Server

Now we have a self-signed TLS certificate and corresponding private key, starting aHTTPS web server is lovely and simple

    // Use the ListenAndServeTLS() method to start the HTTPS server. We

    // pass in the paths to the TLS certificate and corresponding private key as

    // the two parameters.

    err = srv.ListenAndServeTLS("./tls/cert.pem", "./tls/key.pem")

 the only difference is that it will now be talking HTTPS instead of HTTP.


you will probably get a browser warning similar to the screenshot below.


If you’re using Firefox like me, click “Advanced” then “Add Exception”, and in the dialog box that appears click “Confirm Security Exception”.

although it will still carry a warning in the URL bar because the TLS certificate is self-signed

I recommend pressing Ctrl+i to inspect the Page Info for your homepage:

The ‘Technical Details’ section here confirms that our connection is encrypted and working as expected.

It’s important to note that our HTTPS server only supports HTTPS. If you try making a regular HTTP request to it, it won’t work. But exactly what happens depends on the version of Go that you’re running.

In Go version 1.12 and newer, the server will send the user a 400 Bad Request status and the message "Client sent an HTTP request to an HTTPS server". Nothing will be logged.

A big plus of using HTTPS is that — if a client supports HTTP/2 connections — Go’s HTTPS server will automatically upgrade the connection to use HTTP/2.

This is good because it means that, ultimately, our pages will load faster for users. If you’re not familiar with HTTP/2 you can get a run-down of the basics

 if you look at the headers for the homepage you should see that the protocol being used is HTTP/2.

It’s important to note that the user that you’re using to run your Go application must have read permissions for both the cert.pem and key.pem files, otherwise ListenAndServeTLS() will return a permission denied error.

By default, the generate_cert.go tool grants read permission to all users for the cert.pem file but read permission only to the owner of the key.pem file. In my case the permissions look like this:


### 10.3. Configuring HTTPS Settings

Configuring HTTPS Settings
Go has pretty good default settings for its HTTPS server, but there are a couple of improvements and optimizations that we can make.

To change the default TLS settings we need to do two things:
•  First, we need to create a tls.Config struct which contains the non-default TLS settings that we want to use.
•  Second, we need to add this to our http.Server struct before we start the server.


    // Initialize a tls.Config struct to hold the non-default TLS settings we want

    // the server to use.

    tlsConfig := &tls.Config{
        PreferServerCipherSuites: true,
        CurvePreferences:         []tls.CurveID{tls.X25519, tls.CurveP256},
    }


    // Set the server's TLSConfig field to use the tlsConfig variable we just

    // created.

    srv := &http.Server{
        Addr:     *addr,
        ErrorLog: errorLog,
        Handler:  app.routes(),
        TLSConfig: tlsConfig,
    }

Mozilla’s recommended configurations for modern, intermediate and old browsers may assist you in making a decision here.

Go’s HTTPS server supports TLS versions 1.0 to 1.3.


### 10.4. Connection Timeouts

Let’s take a moment to improve the resiliency of our server by adding some timeout settings

// Add Idle, Read and Write timeouts to the server.

        IdleTimeout:  time.Minute,
        ReadTimeout:  5 * time.Second,
        WriteTimeout: 10 * time.Second,

All three of these timeouts — IdleTimeout, ReadTimeout and WriteTimeout — are server-wide settings which act on the underlying connection and apply to all requests irrespective of their handler or URL.


By default, Go enables keep-alives on all accepted connections. This helps reduce latency (especially for HTTPS connections) because a client can reuse the same connection for multiple requests without having to repeat the handshake.

Also by default, Go will automatically close keep-alive connections after 3 minutes of inactivity. This helps to clear-up connections where the user has unexpectedly disappeared (e.g. due to a power cut client-side).

but you can reduce it via the IdleTimeout setting. In our case, we’ve set it to 1 minute, which means that all keep-alive connections will be automatically closed after 1 minute of inactivity.


In our code we’ve also set the ReadTimeout setting to 5 seconds. This means that if therequest headers or body are still being read 5 seconds after the request is first accepted,

then Go will close the underlying connection. Because this is a ‘hard’ closure on theconnection, the user won’t receive any HTTP(S) response.

Setting a short ReadTimeout period helps to mitigate the risk from slow-client attacks — such as Slowloris — which could otherwise keep a connection open indefinitely by sending partial, incomplete, HTTP(S) requests.


Important: If you set ReadTimeout but don’t set IdleTimeout, then IdleTimeout will default to using the same setting as ReadTimeout. For instance, if you set ReadTimeout to 3 seconds, then there is the side-effect that all keep-alive connections will also be closed after 3 seconds of inactivity. Generally, my recommendation is to avoid any ambiguity and always set an explicit IdleTimeout value for your server.

The WriteTimeout setting will close the underlying connection if our server attempts towrite to the connection after a given period (in our code, 10 seconds). But this behavesslightly differently depending on the protocol being used.

Therefore, the idea of WriteTimeout is generally not to prevent long-running handlers, but to prevent the data that the handler returns from taking too long to write.

The http.Server object also provides a MaxHeaderBytes field, which you can use to control the maximum number of bytes the server will read when parsing request headers. By default, Go allows a maximum header length of 1MB.

If MaxHeaderBytes is exceeded then the user will automatically be sent a 431 Request Header Fields Too Large response.

### 11. User Authentication

we’re going to add some user authentication functionality to our application, so that only registered, logged-in users can create new snippets. 

sign up for an account

the process will work like this:
1.  A user will register by visiting a form at /user/signup and entering their name, email address and password. We’ll store this information in a new users database table (which we’ll create in a moment).

2.  A user will log in by visiting a form at /user/login and entering their email address and password.

If there’s a match, the user has authenticated successfully and we add the relevant id value for the user to their session data, using the key "authenticatedUserID".

4.  When we receive any subsequent requests, we can check the user’s session data for a "authenticatedUserID" value. If it exists, we know that the user has already successfully logged in. We can keep checking this until the session expires, when the user will need to log in again. If there’s no "authenticatedUserID" in the session, we know that the user is not logged in.

How to implement basic signup, login and logout functionality for users.
•  A secure approach to encrypting and storing user passwords securely in your database using Bcrypt.

•  A solid and straightforward approach to verifying that a user is logged in using middleware and sessions.
•  How to prevent Cross-Site Request Forgery (CSRF) attacks.

### 11.1. Routes Setup

                <a href='/user/signup'
>Signup</a>
                <a href='/user/login'
>Login</a>
                <form action='/user/logout' method='POST'
>
                    <button
>Logout</button>
                </form>

they should respond with the relevant placeholder plain-text response. 

### 11.2. Creating a Users Model

Now that the routes are set up, we need to create a new users database table and a database model to access it.

•  I’ve set the type of the hashed_password field to CHAR(60). This is because we’ll be storing hashes of the user passwords in the database — not the passwords themselves — and the hashed versions will always be exactly 60 characters long.

•  I’ve also added a UNIQUE constraint on the email column and named it users_uc_email. This constraint ensures that we won’t end up with two users who have the same email address.

If we try to insert a record in this table with a duplicate email, MySQL will throw an ERROR 1062: ER_DUP_ENTRY error.


•  There’s also an active column which we will use to contain the status of the user account. When this is TRUE, the user will be able to log in and use the application as normal. When this is FALSE, the user will be considered deactivated and they won’t be able to log in.

add a new User struct to hold the data for each user, plus a couple of new error types:

    ErrNoRecord = errors.New("models: no matching record found")
    // Add a new ErrInvalidCredentials error. We'll use this later if a user

    // tries to login with an incorrect email address or password.

    ErrInvalidCredentials = errors.New("models: invalid credentials")
    // Add a new ErrDuplicateEmail error. We'll use this later if a user

    // tries to signup with an email address that's already in use.

    ErrDuplicateEmail = errors.New("models: duplicate email")

// Define a new User type. Notice how the field names and types align

// with the columns in the database `users` table?

type User struct {
    ID             int
    Name           string
    Email          string
    HashedPassword []byte
    Created        time.Time
    Active         bool
}

// We'll use the Authenticate method to verify whether a user exists with

// the provided email address and password. This will return the relevant

// user ID if they do.

func (m *UserModel) Authenticate(email, password string) (int, error) {
    return 0, nil
}

The final stage is to add a new field to our application struct so that we can make this model available to our handlers. Update the main.go file as follows:

// Initialize a mysql.UserModel instance and add it to the application

    // dependencies.

    app := &application{
        errorLog:      errorLog,
        infoLog:       infoLog,
        session:       session,
        snippets:      &mysql.SnippetModel{DB: db},
        templateCache: templateCache,
        users:         &mysql.UserModel{DB: db},
    }

At this stage you should find that it compiles correctly without anyproblems.

### 11.3. User Signup and Password Encryption

        <div
>
            <label
>Name:</label>
            {{with .Errors.Get "name"}}
                <label class='error'
>{{.}}</label>
            {{end}}
            <input type='text' name='name' value='{{.Get "name"}}'
>
        </div>

// Use the regexp.MustCompile() function to parse a pattern and compile a

// regular expression for sanity checking the format of an email address.

// This returns a *regexp.Regexp object, or panics in the event of an error.

// Doing this once at runtime, and storing the compiled regular expression

// object in a variable, is more performant than re-compiling the pattern with

// every request.

var EmailRX = regexp.MustCompile("^[a-zA-Z0-9.!#$%!&(MISSING)'*+\\/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$")

// Implement a MatchesPattern method to check that a specific field in the form

// matches a regular expression. If the check fails then add the

// appropriate message to the form errors.

func (f *Form) MatchesPattern(field string, pattern *regexp.Regexp) {
    value := f.Get(field)
    if value == "" {
        return
    }
    if !pattern.MatchString(value) {
        f.Errors.Add(field, "This field is invalid")
    }
}

we need to double-escape special characters in the regexp with \\ for it to work correctly (we can’t use a raw string literal because the pattern contains a back quote character).


    // Validate the form contents using the form helper we made earlier.

    form := forms.New(r.PostForm)
    form.Required("name", "email", "password")
    form.MaxLength("name", 255)
	form.MaxLength("email", 255)
    form.MatchesPattern("email", forms.EmailRX)

    // If there are any errors, redisplay the signup form.

    if !form.Valid() {
        app.render(w, r, "signup.page.tmpl", &templateData{Form: form})
        return
    }



, it’s hugely important that it doesn’t contain the plain-text versions of your users’ passwords.


This function will return a 60-character long hash which looks a bit like this: $2a$12$NuTjWXm3KKntReFwyBVHyuf/to.HEwTy.eS206TNfkGfr6HzGJSWG

the bcrypt.GenerateFromPassword() function also adds a random salt to the password to help avoid rainbow-table attacks.


we can check that a plain-text password matches a particular hash using the bcrypt.CompareHashAndPassword() function like so:

All errors returned by MySQL have a particular code, which we can use to triage what has caused the error

In the case of a duplicate email, the error code used will be 1062 (ER_DUP_ENTRY).


     // If this returns an error, we use the errors.As() function to check

        // whether the error has the type *mysql.MySQLError. If it does, the

        // error will be assigned to the mySQLError variable. We can then check

        // whether or not the error relates to our users_uc_email key by

        // checking the contents of the message string. If it does, we return

        // an ErrDuplicateEmail error.

        var mySQLError *mysql.MySQLError
        if errors.As(err, &mySQLError) {
            if mySQLError.Number == 1062 && strings.Contains(mySQLError.Message, "users_uc_email") {
                return models.ErrDuplicateEmail
            }
        }

/ Try to create a new user record in the database. If the email already exists

    // add an error message to the form and re-display it.

    err = app.users.Insert(form.Get("name"), form.Get("email"), form.Get("password"))
    if err != nil {
        if errors.Is(err, models.ErrDuplicateEmail) {
            form.Errors.Add("email", "Address is already in use")
            app.render(w, r, "signup.page.tmpl", &templateData{Form: form})
        } else {
            app.serverError(w, err)
        }
        return

// Otherwise add a confirmation flash message to the session confirming that

    // their signup worked and asking them to log in.

    app.session.Put(r, "flash", "Your signup was successful. Please log in.")

    // And redirect the user to the login page.

    http.Redirect(w, r, "/user/login", http.StatusSeeOther)

You should get a validation failure

But it’s probably a good idea to avoid using these for two reasons:

Unless you’re very careful, sending a plain-text password to your database risks the password being accidentally recorded in one of your database logs. A couple of high-profile examples of this happening were the Github and Twitter incidents in 2018.

### 11.4. User Login

The process for creating the user login page follows the same general pattern as the user signup.

        {{with .Errors.Get "generic"}}
            <div class='error'
>{{.}}</div>
        {{end}}


Notice how we’ve included a {{with .Errors.Get "generic"}} action at the top of the form, instead of displaying of error messages for the individual fields? We’ll use this to present the user with a generic “your email address or password is wrong” message if their login fails, instead of explicitly indicating which of the fields is wrong.

Again, for security, we’re not re-displaying the user’s password if the login fails.


how do we verify that the email and password submitted by a user are correct?

1.  First it should retrieve the hashed password associated with the email address from our MySQL users table. If the email doesn’t exist in the database, or it’s for a user that has been deactivated, we will return the ErrInvalidCredentials error that we made earlier.


// Otherwise, the password is correct. Return the user ID.

    return id, nil

If the login details are valid, we then want to add the user’s id to their session data so that — for future requests — we know that they have authenticated successfully and which user they are.


    // Check whether the credentials are valid. If they're not, add a generic error

    // message to the form failures map and re-display the login page.

    form := forms.New(r.PostForm)
    id, err := app.users.Authenticate(form.Get("email"), form.Get("password"))
    if err != nil {
        if errors.Is(err, models.ErrInvalidCredentials) {
            form.Errors.Add("generic", "Email or Password is incorrect")


    // Add the ID of the current user to the session, so that they are now 'logged

    // in'.

    app.session.Put(r, "authenticatedUserID", id)

So, let’s give this a try. Restart the application and try submitting some invalid usercredentials…

But when you input some correct credentials (use the email address and password forthe user that you created in the previous chapter), the application should log you inand redirect you to the create snippet page, like so:

Session Fixation Attacks

However, if you were using a server-side data store for sessions (with a session ID stored in a cookie) then to mitigate the risk of session fixation attacks it’s important that you change the value of the session ID before making any changes to privilege levels (e.g. login and logout operations).


If you’re using Gorilla Sessions to manage server-side sessions there’s unfortunately no way to refresh the session ID while retaining the session data, so you’ll need to either accept the risk of a session fixation attack or create a new session and copy the data across manually.

### 11.5. User Logout

Implementing the user logout isstraightforward in comparison to the signup and login — all we need to do is removethe "authenticatedUserID" value from their session.

Let’s update the logoutUser handler to do exactly that.

// Remove the authenticatedUserID from the session data so that the user is

    // 'logged out'.

    app.session.Remove(r, "authenticatedUserID")
    // Add a flash message to the session to confirm to the user that they've been

    // logged out.

    app.session.Put(r, "flash", "You've been logged out successfully!")
    http.Redirect(w, r, "/", http.StatusSeeOther)

If you now click the ‘Logout’ link in thenavigation bar you should be logged out and redirected to the homepage like so:

### 11.6. User Authorization

2.  The contents of the navigation bar changes depending on whether a user is authenticated (logged in) or not. Authenticated users should see links to ‘Home’, ‘Create snippet’ and ‘Logout’. Unauthenticated users should see links to ‘Home’, ‘Signup’ and ‘Login’.

we can check whether a request is being made by an authenticated user or not by checking for the existence of an "authenticatedUserID" value in their session data.


Open the cmd/web/helpers.go file and add an isAuthenticated() helper function to return the authentication status like so:


// Return true if the current request is from authenticated user, otherwise return false.

func (app *application) isAuthenticated(r *http.Request) bool {
    return app.session.Exists(r, "authenticatedUserID")
}

The next step for us is to find a way to pass this information to our HTML templates, so that we can toggle the contents of the navigation bar appropriately.
There’s two parts to this. First, we’ll need to add a new IsAuthenticated field to our templateData struct:

And the second step is to update our addDefaultData() helper so that this information is automatically added to the templateData struct every time we render a template. Like so:


    // Add the authentication status to the template data.

    td.IsAuthenticated = app.isAuthenticated(r)
    return td

Once that’s done, we can update the ui/html/base.layout.tmpl file to toggle the navigation links using the {{if .IsAuthenticated}} action like so:

                <!-- Toggle the navigation link -->
                {{if .IsAuthenticated}}
                    <a href='/snippet/create'
>Create snippet</a>
                {{end}}

Remember: The {{if ...}} action considers empty values (false, 0, any nil pointer or interface value, and any array, slice, map, or string of length zero) to be false.

Otherwise — if you are logged in — your homepage should look like this:

Restricting Access
As it stands, we’re hiding the ‘Create snippet’ navigation link for any user that isn’t logged in. But an unauthenticated user could still create a new snippet by visiting the https://localhost:4000/snippet/create page directly.

Let’s fix that, so that if an unauthenticated user tries to visit any routes with the URL path /snippet/create they are redirected to /user/login instead.


The simplest way to do this is via some middleware. Open the cmd/web/middleware.go file and create a new requireAuthentication() middleware function, following the same pattern that we used earlier in the book:

        // If the user is not authenticated, redirect them to the login page and

        // return from the middleware chain so that no subsequent handlers in

        // the chain are executed.

        if !app.isAuthenticated(r) {
            http.Redirect(w, r, "/user/login", http.StatusSeeOther)
            return
        }


        // Otherwise set the "Cache-Control: no-store" header so that pages

        // require authentication are not stored in the users browser cache (or

        // other intermediary cache).

        w.Header().Add("Cache-Control", "no-store")

        // And call the next handler in the chain.

        next.ServeHTTP(w, r)


    // Add the requireAuthentication middleware to the chain.

    mux.Get("/snippet/create", dynamicMiddleware.Append(app.requireAuthentication).ThenFunc(app.createSnippetForm))

Save the files, restart the application and make sure that you’re logged out.

Then try visiting https://localhost:4000/snippet/create directly in your browser. Youshould find that you get immediately redirected to the login form instead.

you can manually wrap your handlers like this:


### 11.7. CSRF Protection

we’ll look at how to protect our application from Cross-Site Request Forgery (CSRF) attacks.


If you’re not familiar with the principles of CSRF, it’s a form of cross-domain attack where a malicious third-party website sends state-changing HTTP requests to your website. A great explanation of the basic CSRF attack can be found here.


•  A user logs into our application. Our session cookie is set to persist for 12 hours, so they will remain logged in even if they navigate away from the application.

•  The user then goes to a malicious website which contains some code that sends a request to POST /snippets/create to add a new snippet to our database.

Since the user is still logged in to our application, the request is processed with their privileges. Completely unknown to them, a new snippet will be added to our database.

One mitigation that we can take to prevent CSRF attacks is to make sure that the SameSite attribute is set on our session cookie.


By default the golangcollege/sessions package that we’re using always sets SameSite=Lax on the session cookie. This means that the session cookie won’t be sent by the user’s browser for cross-site usage

the SameSite attribute is only fully supported by 85%!o(MISSING)f browsers worldwide. So, although it’s something than we can (and should) use as a defensive measure, we can’t rely on it for all users.

To mitigate the risk of CSRF for all users we’ll also need to implement some form of token check. Like session management and password hashing, when it comes to this there’s a lot that you can get wrong so it’s probably safer to use a tried-and-tested third-party package instead of rolling your own implementation.


They both do roughly the same thing, using the Double Submit Cookie pattern to prevent attacks. In this pattern a random CSRF token is generated and sent to the user in a CSRF cookie. This CSRF token is then added to a hidden field in each form that’s vulnerable to CSRF. When the form is submitted, both packages use some middleware to check that the hidden field value and cookie value match.

// Create a NoSurf middleware function which uses a customized CSRF cookie with

// the Secure, Path and HttpOnly flags set.

func noSurf(next http.Handler) http.Handler {
    csrfHandler := nosurf.New(next)
    csrfHandler.SetBaseCookie(http.Cookie{
        HttpOnly: true,
        Path:     "/",
        Secure:   true,
    })

    return csrfHandler
}

    // Use the nosurf middleware on all our 'dynamic' routes.

    dynamicMiddleware := alice.New(app.session.Enable, noSurf)

And because the logout form can potentially appear on every page, it makes sense to add the CSRF token to the template data via our addDefaultData() helper. This will mean it’s automatically available to our templates each time we render a page. 

	// Add the CSRF token to the templateData struct.

	td.CSRFToken = nosurf.Token(r)

                   <form action='/user/logout' method='POST'
>
                        <!-- Include the CSRF token -->
                        <input type='hidden' name='csrf_token' value='{{.CSRFToken}}'
>
                        <button
>Logout</button>
                    </form>

And if you try submitting the forms it should now work correctly again.

### 12. Using Request Context

We could make this more robust by checking our users database table to make sure that the "authenticatedUserID" value is valid, and that the user account it relates to is still active

So, if we check the database from the isAuthenticated() helper directly, we would end up making duplicated round-trips to the database during every request. Not very efficient.


A better approach would be to carry out this check in some middleware to determine whether the current request is from an authenticated-and-active user or not, and then pass this information down to all subsequent handlers in the chain.

•  What request context is, how to use it, and when it is appropriate to use it.
•  How to use request context in practice to pass information about the current user between your handlers.

### 12.1. How Request Context Works

Every http.Request that our handlers process has a context.Context object embedded in it, which we can use to store information during the lifetime of the request.

•  First, we use the r.Context() method to retrieve the existing context from a request and assign it to the ctx variable.
•  Then we use the context.WithValue() method to create a new copy of the existing context, containing the key "isAuthenticated" and a value of true.
•  Then finally we use the r.WithContext() method to create a copy of the request containing our new context.

What we’re doing is creating a new copy of the http.Request object with our new context in it.

The important thing to explain here is that, behind the scenes, request context values are stored with the type interface{}. And that means that, after retrieving them from the context, you’ll need to assert them to their original type before you use them.

To retrieve a value we need to use the r.Context().Value() method, like so:


isAuthenticated, ok := r.Context().Value("isAuthenticated").(bool)

But this isn’t recommended, because there’s a risk that other third-party packages used by your application will also want to store data using the key "isAuthenticated". And that would cause a naming collision and bugginess.

To avoid this, it’s good practice to create your own custom type which you can use for your context keys. Extending our sample code, it’s much better to do something like this:
type contextKey string

const contextKeyIsAuthenticated = contextKey("isAuthenticated")

isAuthenticated, ok := r.Context().Value(contextKeyIsAuthenticated).(bool)
if !ok {
    return errors.New("could not convert value to bool")
}

### 12.2. Request Context for Authentication/Authorization

We’ll begin by heading back to our pkg/models/mysql/users.go file and updating theUserModel.Get() method, so that it retrieves the details of a specific user from thedatabase like so:

func (m *UserModel) Get(id int) (*models.User, error) {
    u := &models.User{}

    stmt := `SELECT id, name, email, created, active FROM users WHERE id = ?`
    err := m.DB.QueryRow(stmt, id).Scan(&u.ID, &u.Name, &u.Email, &u.Created, &u.Active)
    if err != nil {
        if errors.Is(err, sql.ErrNoRows) {
            return nil, models.ErrNoRecord
        } else {
            return nil, err
        }

we have a unique key we can use to store and retrieve the user details from request context.


type contextKey string

const contextKeyIsAuthenticated = contextKey("isAuthenticated")

Let’s create a new authenticate() middleware which fetches the user’s ID from their session data, checks the database to see if the ID is valid and for an active user, and then updates the request context to include this information.



        // Otherwise, we know that the request is coming from a active, authenticated,

        // user. We create a new copy of the request, with a true boolean value

        // added to the request context to indicate this, and call the next handler

        // in the chain *using this new copy of the request*.

		ctx := context.WithValue(r.Context(), contextKeyIsAuthenticated, true)
		next.ServeHTTP(w, r.WithContext(ctx))

When we do have an authenticated-and-active user, we create a copy of the requestwith a contextKeyIsAuthenticated key and true value stored in the request context.We then pass this copy of the *http.Request to the next handler in the chain.

The last thing that we need to do is updated our isAuthenticated() helper, so thatinstead of checking the session data it now checks the request context to determine if auser is authenticated or not.

func (app *application) isAuthenticated(r *http.Request) bool {
	isAuthenticated, ok := r.Context().Value(contextKeyIsAuthenticated).(bool)
	if !ok {
		return false
	}
	return isAuthenticated

the application is now smart enough to recognize that the user has been deactivated

request context should only be used to store information relevant to the lifetime of a specific request. The Go documentation for context.Context warns:
Use context Values only for request-scoped data that transits processes and APIs.

### 13. Testing

But there are some conventions, patterns and good-practices that you can follow.

•  How to create and run table-driven unit tests and sub-tests in Go.
•  How to unit test your HTTP handlers and middleware.
•  How to perform ‘end-to-end’ testing of your web application routes, middleware and handlers.


•  How to use a test instance of MySQL to perform integration tests.
•  How to easily calculate and profile code coverage for your tests.

### 13.1. Unit Testing and Sub-Tests

we’ll create a unit test to make sure that our humanDate() function (which we made back in the Custom Template Functions chapter) is outputting time.Time values in the exact format that we want.

func humanDate(t time.Time) string {
    return t.UTC().Format("02 Jan 2006 at 15:04")
}

The reason that I want to start by testing this is because it’s a simple function. We can explore the basic syntax and patterns for writing tests without getting too caught-up in the functionality that we’re testing.

•  The test is just regular Go code, which calls the humanDate function and checks that the result matches what we expect.

•  Your unit tests are contained in a normal Go function with the signature func(*testing.T).

•  To be a valid unit test the name of this function must begin with the word Test. Typically this is then followed by the name of the function, method or type that you’re testing to help make it obvious at a glance what is being tested.

•  You can use the t.Errorf() function to mark a test as failed and log a descriptive message about the failure.

Let’s try this out. Save the file, then use the go test command to run all the tests in our cmd/web package like so:
$ go test ./cmd/web


If you want more detail, you can see exactly which tests are being run by using the -v flag to get the verbose output:
$ go test -v ./cmd/web

In Go, an idiomatic way to run multiple test cases is to use table-driven tests.
Essentially, the idea behind table-driven tests is to create a table of test cases containing the inputs and expected outputs, and to then loop over these, running each test case in a sub-test. 

    // Create a slice of anonymous structs containing the test case name,

    // input to our humanDate() function (the tm field), and expected output

    // (the want field).

    // Loop over the test cases.

    for _, tt := range tests {
        // Use the t.Run() function to run a sub-test for each test case. The

        // first parameter to this is the name of the test (which is used to

        // identify the sub-test in any log output) and the second parameter is

        // and anonymous function containing the actual test for each case.

        t.Run(tt.name, func(t *testing.T) {
            hd := humanDate(tt.tm)

            if hd != tt.want {
                t.Errorf("want %!q(MISSING); got %!q(MISSING)", tt.want, hd)
            }
        })
    }

OK, let’s run this and see what happens:

It also worth pointing out that when we use the t.Errorf() function to mark a test as failed, it doesn’t cause go test to immediately exit. All the other tests and sub-tests will continue to be run after a failure.

As a side note, you can use the -failfast flag to stop the tests running after the first failure, if you want, like so:
$ go test -failfast -v ./cmd/web

    // Convert the time to UTC before formatting it.

    return t.UTC().Format("02 Jan 2006 at 15:04")

And when you re-run the tests everything should now pass.


Running All Tests
To run all the tests for a project — instead of just those in a specific package — you can use the ./... wildcard pattern like so:
$ go test ./...

### 13.2. Testing HTTP Handlers

All the handlers that we’ve written for our Snippetbox project so far are a bit complex to test, and to introduce things I’d prefer to start off with something a bit simpler.

 It’s the type of handler that you might want to implement for status-checking or uptime monitoring of your server.

func ping(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("OK"))
}

•  Checks that the response body written by the ping handler is "OK".

To assist in testing your HTTP handlers Go provides the net/http/httptest package, which contains a suite of useful tools.


One of these tools is the httptest.ResponseRecorder type. This is essentially an implementation of http.ResponseWriter which records the response status code, headers and body instead of actually writing them to a HTTP connection.


    // Initialize a new httptest.ResponseRecorder.

    rr := httptest.NewRecorder()

    // Initialize a new dummy http.Request.

    r, err := http.NewRequest(http.MethodGet, "/", nil)

    // Call the Result() method on the http.ResponseRecorder to get the

    // http.Response generated by the ping handler.

    rs := rr.Result()

    // We can then examine the http.Response to check that the status code

    // written by the ping handler was 200.

    if rs.StatusCode != http.StatusOK {
        t.Errorf("want %!d(MISSING); got %!d(MISSING)", http.StatusOK, rs.StatusCode)
    }


    // And we can check that the response body written by the ping handler

    // equals "OK".

    defer rs.Body.Close()
    body, err := ioutil.ReadAll(rs.Body)
    if err != nil {
        t.Fatal(err)
    }

    if string(body) != "OK" {
        t.Errorf("want body to equal %!q(MISSING)", "OK")
    }

When called, t.Fatal() will mark the test as failed, log the error, and then completely stop execution of any further tests.

•  The middleware correctly calls the next handler in the chain.

If you run the tests now, everything should pass without any issues.


It’s possible to only run specific tests by using the -run flag. This allows you to pass in a regular expression — and only tests with a name that matches the regular expression will be run.

By default, the go test command executes all tests in a serial manner, one after another. When you have a small number of tests (like we do) and the runtime is very fast, this is absolutely fine.

You can indicate that it’s OK for a test to be run in concurrently alongside other tests by calling the t.Parallel() function at the start of the test. For example:
func TestPing(t *testing.T) {
    t.Parallel()

    ...
}

Enabling the Race Detector
The go test command includes a -race flag which enables Go’s race detector when running tests.


### 13.3. End-To-End Testing

how to run end-to-end tests on your web application that encompass your routing, middleware and handlers. 

Arguably, end-to-end testing should give you more confidence that your application is working correctly than unit testing in isolation.


The key to end-to-end testing our application is the httptest.NewTLSServer() function, which spins up a httptest.Server instance that we can make HTTPS requests to.

There are a few things about this code to point out and discuss.

The httptest.NewTLSServer() function accepts a http.Handler as the parameter,and this handler gets called each time the test server receives a HTTPS request.

Doing this gives us a test server that exactly mimics our application routes,middleware and handlers, and is a big upside of the work that we did earlier in thebook to isolate all our application routing in the app.routes() method.

Our TestPing function is now working nicely. But there’s a good opportunity to breakout some of this code into some helper functions, which we can reuse as we addmore end-to-end tests to our project.

In our case the helpers will only be used in our cmd/web package and we’ll put them in a new cmd/web/testutils_test.go file.

/ application struct containing mocked dependencies.

func newTestApplication(t *testing.T) *application {
    return &application{
        errorLog: log.New(ioutil.Discard, "", 0),
        infoLog:  log.New(ioutil.Discard, "", 0),
    }
}

// Define a custom testServer type which anonymously embeds a httptest.Server

// instance.

type testServer struct {
    *httptest.Server
}

We now have a neat pattern in place for spinning up a test server and making requests to it

We’ve also broken apart some of the code into helpers, which will make writing future tests quicker and easier.

•  We don’t want the client to automatically follow redirects. Instead we want it to return the first HTTPS response sent by our server so that we can test the response for that specific request.


    // Initialize a new cookie jar.

    jar, err := cookiejar.New(nil)
    if err != nil {
        t.Fatal(err)
    }


    // Add the cookie jar to the client, so that response cookies are stored

    // and then sent with subsequent requests.

    ts.Client().Jar = jar

### 13.4. Mocking Dependencies

But first, let’s talk about dependencies.
Throughout this project we’ve injected dependencies into our handlers via the application struct, which currently looks like this:


When testing, it sometimes makes sense to mock these dependencies instead of using exactly the same ones that you do in your production application.


For example, in the previous chapter we mocked the errorLog and infoLog dependencies with loggers that write messages to ioutil.Discard, instead of the os.Stdout and os.Stderr streams like we do in our production application:


The reason for mocking these and writing to ioutil.Discard is to avoid clogging up our test output with unnecessary log messages when we run go test -v.

By creating mocks of these it’s possible for us to test the behavior of our handlers without needing to setup an entire test instance of the MySQL database.

To do this, we’re going to create a simple struct which implements the same methods as our production mysql.SnippetModel, but have the methods return some fixed dummy data instead.

If you go ahead and try to run the tests now, it will fail with the following messages:

This is happening because our application struct is expecting pointers tomysql.SnippetModel and mysql.UserModel objects, but we are trying to use pointersto mock.SnippetModel and mock.UserModel objects instead.

The idiomatic fix for this is to change our application struct so that it uses interfaces instead, which are satisfied by both our mock and production database models.

Tip: If you’re not familiar with the idea of interfaces in Go, then there is an introduction in this blog post which I recommend reading.

A nice pattern, which helps to keep your code simple, is to define the interfaces inline in the application struct, like so:


snippets interface {
		Insert(string, string, string) (int, error)
		Get(int) (*models.Snippet, error)
		Latest() ([]*models.Snippet, error)
	}

And if you try running the tests again now, everything should work correctly.

We’ve updated the application struct so that instead of the snippets and users fields having the concrete types *mysql.SnippetModel and *mysql.UserModel they are interfaces instead.

we can use them in our application struct. Both our ‘real’ database models (under the pkg/models/mysql package) and mock database models (under the pkg/models/mock package) satisfy the interfaces, so we can now use them interchangeably.

    // Set up some table-driven tests to check the responses sent by our

    // application for different URLs.

### 13.5. Testing HTML Forms

Testing this route is made a bit more complicated by the anti-CSRF check that our application does. Any request that we make to POST /user/signup will always receive a 400 Bad Request response unless the request contains a valid CSRF token and cookie. To get around this we need to emulate the workflow of a real-life user as part of our test, like so:


1.  Make a GET /user/signup request. This will return a response which contains a CSRF cookie in the response headers and a CSRF token in the HTML response body.
2.  Extract the CSRF token from the HTML response body.


// Define a regular expression which captures the CSRF token value from the

// HTML for our user signup page.

var csrfTokenRX = regexp.MustCompile(`<input type='hidden' name='csrf_token' value='(.+)'>`)

Because the CSRF token is a base64 encoded string it potentially includes the + character, and this will be escaped to &#43;. So after extracting the token from the HTML we need to run it through html.UnescapeString() to get the original token value.

because the test results have been cached. 

If you want, you can force the test to run again by using the -count=1 flag like so:

Alternatively, you can clear cached results for all tests with the go clean command:
$ go clean -testcache

### 13.6. Integration Testing

Running end-to-end tests with mocked dependencies is a good thing to do, but we could improve confidence in ourapplication even more if we also verify that our real MySQL database models are working as expected.

Test Database Setup and TeardownThe first step is to create the test version of our MySQL database.

A setup script to create the database tables (so that they mimic our production database) and insert a known set oftest data than we can work with in our tests.A teardown script which drops the database tables and any data.

Note: The Go tool ignores any directories called testdata, so these scripts will be ignored when compiling yourapplication. Fun fact: The Go tool also ignores any directories or files which have names that begin with an _ or .character.


    // setup and teardown scripts contains multiple SQL statements, we need

    // to use the `multiStatements=true` parameter in our DSN. This instructs

    // our MySQL database driver to support executing multiple SQL statements

    // in one db.Exec() call.

    db, err := sql.Open("mysql", "test_web:pass@/test_snippetbox?parseTime=true&multiStatements=true")
    if err != nil {
        t.Fatal(err)
    }

    // Read the setup SQL script from file and execute the statements.

    script, err := ioutil.ReadFile("./testdata/setup.sql")
    if err != nil {
        t.Fatal(err)
    }
    _, err = db.Exec(string(script))
    if err != nil {
        t.Fatal(err)
    }

    // Return the connection pool and an anonymous function which reads and

    // executes the teardown script, and closes the connection pool. We can

    // assign this anonymous function and call it later once our test has

    // completed.

    return db, func() {
        script, err := ioutil.ReadFile("./testdata/teardown.sql")
        if err != nil {
            t.Fatal(err)
        }
        _, err = db.Exec(string(script))
        if err != nil {
            t.Fatal(err)
        }

        db.Close()
    }
}

	// Skip the test if the `-short` flag is provided when running the test.

	// We'll talk more about this in a moment.

	if testing.Short() {
		t.Skip("mysql: skipping integration test")
	}



// Initialize a connection pool to our test database, and defer a

			// call to the teardown function, so it is always run immediately

			// before this sub-test returns.

			db, teardown := newTestDB(t)
			defer teardown()

Tip: Using the reflect.DeepEqual() function, like we are in the code above, is an effective way to check forequality between arbitrarily complex custom types.

If you’re running hundreds of integration tests against your database, you might end up waiting minutes, rather than seconds, for your tests to finish.


When your tests take a long time, you might decide that you want to skip specific long-running tests under certaincircumstances.

A common and idiomatic way to skip long-running tests is to use the testing.Short() function to check for the presence of a -short flag, like we have in the code above.


Otherwise, if you don’t use the -short flag, then the test will be executed as normal.


### 13.7. Profiling Test Coverage

A great feature of the go test tool is the metrics and visualizations that it provides for test coverage.

From the results here we can see that 49.3%!o(MISSING)f the statements in our cmd/web package are executed during our tests,

We can get a more detailed breakdown of test coverage by method and function byusing the -coverprofile flag like so:[插图]

You can then view the coverage profile by using the go tool cover command like so:

An alternative and more visual way to view the coverage profile is to use the -html flag instead of -func.
$ go tool cover -html=/tmp/profile.out
This will open a browser window containing a navigable and highlighted representation of your code, similar to this:

It’s easy to see exactly which statements get executed during your tests (colored green) and which are not (highlighted red).


Instead of just highlighting the statements in green and red, using -covermode=countmakes the coverage profile record the exact number of times that each statement isexecuted during the tests.

### 14. Conclusion

Over the course of this book we’ve explicitly covered a lot of topics, including routing, templating, working with a database, authentication/authorization, using HTTPS, using Go’s testing package and more.


Over the course of this book we’ve explicitly covered a lot of topics, including routing, templating, working with a database, authentication/authorization, using HTTPS, using Go’s testing package and more.

the way that our project code is organized and links together — is something that you should be able to take and apply in your future work.

Importantly, I also wanted the book to convey that you don’t need a framework to build web applications in Go. Go’s standard library contains almost all the tools that you need… even for a moderately complex application. 

there are lightweight and focused third-party packages that you can reach for.

I recommend taking a bit of time to review the code that you’ve written so far. As you go through it, make sure that you’re clear about what each part of the codebase does, and how it fits in with the project as a whole.

And finally, a small request. Sales and marketing really isn’t my strong suit, so if youenjoyed this book and would recommend it to other people, I’d appreciate your help inspreading the word!

### 15. Appendices

Appendices
•  How HTTPS Works
•  Further Reading and Useful Links

### 15.1. How HTTPS Works

How HTTPS Works
You can think of a TLS connection happening in two stages. The first stage is the handshake, in which the client verifies that the server is trusted and generates some TLS session keys. The second stage is the actual transmission of the data, in which the data is encrypted and signed using the TLS session keys generated during the handshake.

1.  A TCP connection is established and the TLS handshake begins. The client sends the web server a list of the TLS versions and cipher suites that it supports 

2.  The web server sends the client confirmation of the TLS version and cipher suite it has chosen.

The web server sends its TLS certificate (which contains its RSA public key). The client then verifies that the TLS certificate is not expired and is trusted. Web browsers come installed with the public keys of all the major certificate authorities, which they can use to verify that the web server’s certificate was indeed signed by the trusted certificate authority. 

The client also confirms that the host named in the TLS certificate is the one to which it has an open connection.


4.  The client generates the secret session keys. It encrypts these using the server’s RSA public key (from the TLS certificate) and sends them to the web server which decrypts them using their RSA private key. This is known as the key exchange. Both the client and web server now have access to the same session keys, and no other parties should know them. This is the end of the TLS handshake.

The data is broken up into records (generally up to 16KB in size).

6.  The encrypted, signed record is sent to the server. Then, using the session keys, the server can unencrypt the record and verify that the signature is correct.

In this process two different types of encryption are used. The TLS handshake portion of the process uses asymmetric encryption (RSA) to securely share the session keys, whereas the actual transfer of the data uses symmetric encryption/decryption (AES) which is much faster.


enerally, it’s good practice to prefer ECDHE cipher suite variants unless you have an explicit need for another device to read your traffic (e.g. a network monitor).

### 15.2. Further Reading and Useful Links

Further Reading and Useful Links
Coding Style Guidelines
•  Effective Go — Tips for writing clear, idiomatic Go code.
•  Clear is better than clever [Video] — Presentation by Dave Cheney from GopherCon Singapore 2019
•  Go Code Review Comments — Style guidelines plus common mistakes to avoid.
•  Practical Go — Real world advice for writing maintainable Go programs.
•  What’s in a name? — Guidelines for naming things in Go.

Recommended Tutorials
•  A Step-by-Step Guide to Go Internationalization & Localization
•  An Overview of Go’s Tooling
•  Data Races vs Race Conditions
•  Error Value FAQ
•  Generating Secure Random Numbers
•  Go 1.11 Modules
•  How to write benchmarks in Go
•  HTTP/2 Server Push
•  Interfaces Explained
•  Learning Go’s Concurrency Through Illustrations
•  Traps, Gotchas, and Common Mistakes for New Golang Devs
•  Understanding Mutexes
•  Using user defined flag types


Third-party Package Lists
•  Awesome Go — Large listing of popular third-party Go packages.
•  Go Projects — Another listing of third-party Go packages.


### 16. Guided Exercises

is a good opportunity to test your understanding of what you’ve learned, and practice putting it into use yourself.

if you get stuck

If your code looks slightly different, no worries. There’s more than one way to achieve the same goal.


### 16.1. Add an About Page to the Application

Your goal for this exercise is to add a new ‘About’ page to the application. It should be mapped to the GET /about route, available to both authenticated and unauthenticated users, and look similar to this:


Suggested Code for Step 1

### 16.2. Test the CreateSnippetForm Handler

 Specifically, you want to test that:Unauthenticated users are redirected to the login form.Authenticated users are shown the form to create a new snippet.

### 16.3. Add a User Profile Page to the Application

Your goal for this exercise is to add a new ‘User Profile’ page to the application.

### 16.5. Redirect User Appropriately after Login

Your goal in this exercise is to update the application so that users are redirected tothe page they were originally trying to visit after logging in.

### 16.6. Add a Debug Mode

be familiar with the idea of a ‘debug’ mode where detailed errors are displayed to the user in a HTTP response instead of a generic "Internal Server Error" message.

You goal in this exercise is to set up a similar ‘debug mode’ for our application, which can be enabled by using the -debug flag like so:
$ go run ./cmd/web -debug

When running in debug mode, any detailed errors and stack traces should be displayed in the browser similar to this:
 

    // Create a new debug flag with the default value of false.

	debug := flag.Bool("debug", false, "Enable debug mode")

debug:         *debug, // Add the debug flag value to the application struct.

trace := fmt.Sprintf("%!s(MISSING)\n%!s(MISSING)", err.Error(), debug.Stack())
	app.errorLog.Output(2, trace)

	if app.debug {
		http.Error(w, trace, http.StatusInternalServerError)
		return
	}