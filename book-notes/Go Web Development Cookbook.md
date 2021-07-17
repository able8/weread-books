## Go Web Development Cookbook
> Arpit Aggarwal

### Title Page

Go Web Development Cookbook
 
 
Build full-stack web applications with Go

### Who this book is for

Who this book is for
This book is intended for developers who want to use Go to write large concurrent web applications. Readers with some familiarity with Go will find this book the most beneficial.

### What this book covers

Chapter 1, Creating Your First Server in Go, explains how to write and interact with HTTP and TCP servers, optimize server responses with GZIP compression, and implement routing and logging in a Go web application.

### Download the example code files

The code bundle for the book is also hosted on GitHub at https://github.com/PacktPublishing/Go-Web-Development-Cookbook

### Creating Your First Server in Go

•  Implementing basic authentication on a simple HTTP server
•  Optimizing HTTP server responses with GZIP compression


Logging HTTP requests

### Introduction

Unlike most other programming languages, Go provides the net/http package, which is sufficient when creating HTTP clients and servers. This chapter will cover the creation of HTTP and TCP servers in Go.


where we implement basic authentication, optimize server responses, define multiple routes, and log HTTP requests. 

### How to do it…

In this recipe, we are going to create a simple HTTP server that will render Hello World! 

Perform the following steps:
1.  Create http-server.go and copy the following content:


  http.HandleFunc("/", helloWorld)
  err := http.ListenAndServe(CONN_HOST+":"+CONN_PORT, nil)

2.  Run the program with the following command:
$ go run http-server.go

### How it works…

•  package main: This defines the package name of the program.

ListenAndServe accepts two parameters—server address and handler. Here, we are passing the server address as localhost:8080 and handler as nil, which means we are asking the server to use DefaultServeMux as a handler.

### How to do it…

we are going to update the HTTP server we created in the previous recipe by adding a BasicAuth function and modifying the HandleFunc to call it. Perform the following steps:


func BasicAuth(handler http.HandlerFunc, realm string) http.HandlerFunc {
  return func(w http.ResponseWriter, r *http.Request) 
  {
    user, pass, ok := r.BasicAuth()
    if !ok || subtle.ConstantTimeCompare([]byte(user),
    []byte(ADMIN_USER)) != 1||subtle.ConstantTimeCompare([]byte(pass), 
    []byte(ADMIN_PASSWORD)) != 1 

### How it works…

The import function adds an additional package, crypto/subtle, which we will use to compare the username and password from the user's entered credentials.

otherwise it sets WWW-Authenticate along with a status code of 401 and writes You are Unauthorized to access the application on an HTTP response stream.

### Optimizing HTTP server responses with GZIP compression

By sending a compressed response we save network bandwidth and download time eventually rendering the page faster. What happens in GZIP compression is the browser sends a request header telling the server it accepts compressed content (.gzip and .deflate) and if the server has the capability to send the response in compressed form then sends it. 

If the server supports compression then it sets Content-Encoding: gzip as a response header, otherwise it sends a plain response back to the client

### How to do it…

use a Gorilla CompressHandler to send all the responses back to the client in the .gzip format.

1.  To use Gorilla handlers, first we need to install the package using the go get command or copy it manually to $GOPATH/src or $GOPATH, as follows:
$ go get github.com/gorilla/handlers

io.WriteString(w, "Hello World!")

mux := http.NewServeMux()
  mux.HandleFunc("/", helloWorld)
  err := http.ListenAndServe(CONN_HOST+":"+CONN_PORT,
  handlers.CompressHandler(mux))

### How it works…

•  mux := http.NewServeMux(): This allocates and returns a new HTTP request multiplexer (ServeMux), which matches the URL of each incoming request against a list of registered patterns and calls the handler for the pattern that most closely matches the URL.

Here, we are calling http.ListenAndServe to serve HTTP requests that handle each incoming connection in a separate Goroutine for us. ListenAndServe accepts two parameters—server address and handler. Here, we are passing the server address as localhost:8080 and handler as CompressHandler, which wraps our server with a .gzip handler to compress all responses in a .gzip format.

### Creating a simple TCP server

Go supports and provides a convenient way of writing TCP servers using a net package

### How to do it…

listener, err := net.Listen(CONN_TYPE, CONN_HOST+":"+CONN_PORT)

for 
  {
    conn, err := listener.Accept()
    if err != nil 
    {
      log.Fatal("Error accepting: ", err.Error())
    }
    log.Println(conn)
  }

### How it works…

•  listener, err := net.Listen(CONN_TYPE, CONN_HOST+":"+CONN_PORT): This creates a TCP server running on localhost at port 8080.

•  if err != nil { log.Fatal("Error starting tcp server: ", err) }: Here, we check if there is any problem in starting the TCP server. If there is, then log the error and exit with a status code of 1.

•  defer listener.Close(): This defer statement closes a TCP socket listener when the application closes.

if there are any errors in accepting the request, then we log it and exit;

### Reading data from a TCP connection

TCP is one of the most widely used protocols for this interaction.

Go provides a convenient way to read incoming connection data through bufio implementing buffered Input/Output

### How to do it…

print data on the server console

conn, err := listener.Accept()

go handleRequest(conn)

message, err := bufio.NewReader(conn).ReadString('\n')
  if err != nil 
  {
    fmt.Println("Error reading:", err.Error())
  }
  fmt.Print("Message Received from the client: ", string(message))
  conn.Close()

### How it works…

echo -n "Hello to TCP server\n" | nc localhost 8080

Let’s understand the change we introduced in this recipe:

which reads an incoming connection into the buffer until the first occurrence of \n and prints the message on the console. If there are any errors in reading the message then it prints the error message along with the error object and finally closes the connection, as follows:

### Writing data to a TCP connection

Another common, as well as important, scenario in any web application is to send the data back to the client or responding to the client. 

### How to do it…

  defer listener.Close()

func handleRequest(conn net.Conn) 

conn.Write([]byte(message + "\n"))
  conn.Close()

### How it works…

writes data as a byte array to the connection

### Implementing HTTP request routing

Most of the time, you have to define more than one URL route in a web application, which involves mapping the URL path to the handlers or resources. 

### How to do it…

http.HandleFunc("/", helloWorld)
  http.HandleFunc("/login", login)
  http.HandleFunc("/logout", logout)

### Implementing HTTP request routing using Gorilla Mux

One thing it doesn’t do very well is dynamic URL routing. Fortunately, we can achieve this with the gorilla/mux package

### How to do it…

var GetRequestHandler = http.HandlerFunc
(
  func(w http.ResponseWriter, r *http.Request) 
  {
    w.Write([]byte("Hello World!"))
  }
)

### How it works…

router := mux.NewRouter()
  router.Handle("/", GetRequestHandler).Methods("GET")
  router.Handle("/post", PostCallHandler).Methods("POST")
  router.Handle("/hello/{name}", PathVariableHandler).

### Logging HTTP requests

Logging HTTP requests is always useful when troubleshooting a web application, so it’s a good idea to log a request/response with a proper message and logging level. 

Go provides the log package, which can help us to implement logging in an application. 

### How to do it…

router.Handle("/", handlers.LoggingHandler(os.Stdout,
  http.HandlerFunc(GetRequestHandler))).Methods("GET")
  logFile, err := os.OpenFile("server.log",
  os.O_WRONLY|os.O_CREATE|os.O_APPEND, 0666)

### How it works…

This will log the request details in the server.log in the Apache Combined Log Format, as shown in the following screenshot:


we wrapped GetRequestHandler with a Gorilla logging handler, and passed a standard output stream as a writer to it, which means we are simply asking to log every request with the URL path / on the console in Apache Common Log Format.

Next, we create a new file named server.log in write-only mode, or we open it, if it already exists.

logFile, err := os.OpenFile("server.log", os.O_WRONLY|os.O_CREATE|os.O_APPEND, 0666)

Using router.Handle("/post", handlers.LoggingHandler(logFile, PostRequestHandler)).Methods("POST"), we wrapped GetRequestHandler with a Gorilla logging handler and passed the file as a writer to it, which means we are simply asking to log every request with the URL path /post in a file named /hello/{name} in Apache Common Log Format.


### Working with Templates, Static Files, and HTML Forms

we will cover the following recipes:
•  Creating your first template
•  Serving static files over HTTP


•  Validating your first HTML form
•  Uploading your first file

### Introduction

Quite often, we would like to create HTML forms to get the information from a client in a specified format, upload files or folders to the server, and generate generic HTML templates, rather than repeating the same static text.

and eventually create, read, and validate HTML forms and upload a file to the server.

### How to do it…

1.  Create first-template.html inside the templates directory by executing the following Unix command:

Copy the following content to first-template.html:

The preceding template has two placeholders, {{.Name}} and {{.Id}}, whose values will be substituted or injected by the template engine at runtime.

Create first-template.go, where we will populate the values for the placeholders, generate an HTML as an output, and write it to the client, as follows:


parsedTemplate, _ := template.ParseFiles("templates/
  first-template.html")
  err := parsedTemplate.Execute(w, person)

With everything in place, the directory structure should look like the following:


### How it works…

err := parsedTemplate.Execute(w, person): Here we are calling an Execute handler on a parsed template, which injects person data into the template, generates an HTML output, and writes it onto an HTTP response stream.

### Serving static files over HTTP

Moreover, it helps to boost application performance, as all the requests for the static files will be served from external sources and therefore reduce the load on the server.

### How to do it…

  fileServer := http.FileServer(http.Dir("static"))
  http.Handle("/static/", http.StripPrefix("/static/", fileServer))

### How it works…

•  http.StripPrefix("/static/", fileServer): This returns a handler that serves HTTP requests by removing /static from the request URL's path and invokes the file server. StripPrefix handles a request for a path that doesn't begin with a prefix by replying with an HTTP 404.

### How it works…

Once we enter the username and password and click on the Login button, we will see Hello followed by the username as the response from the server, as shown in the following screenshot:

func readForm(r *http.Request) *User {
 r.ParseForm()
 user := new(User)
 decoder := schema.NewDecoder()
 decodeErr := decoder.Decode(user, r.PostForm)

•  r.ParseForm(): Here we parse the request body as a form and put the results into both r.PostForm and r.Form.

•  decoder := schema.NewDecoder(): Here we are creating a decoder, which we will be using to fill a user struct with Form values.
•  decodeErr := decoder.Decode(user, r.PostForm): Here we decode parsed form data from POST body parameters to a user struct. 


  if r.Method == "GET" 
  {
    parsedTemplate, _ := template.ParseFiles("templates/
    login-form.html")
    parsedTemplate.Execute(w, nil)
  } 
  else 
  {
    user := readForm(r)
    fmt.Fprintf(w, "Hello "+user.Username+"!")
  }

### Validating your first HTML form

Most of the time, we have to validate a client's input before processing it,

we will be working with the most famous and commonly used validator,   github.com/asaskevich/govalidator, to validate our HTML form.

### How to do it…

type User struct 
{
  Username string `valid:"alpha,required"`
  Password string `valid:"alpha,required"`
}

### How it works…

2.  Next, we updated the User struct type to include a string literal tag with the key as valid and value as alpha, required, as follows:
type User struct 
{
  Username string `valid:"alpha,required"`

 If there are errors, then we write an error message to an HTTP response stream and return it.

### Uploading your first file

For example, if we are developing a job portal, then we may have to provide an option where the applicant can upload their profile/resume

### How to do it…

create an HTML form with a field of type file, which lets the user pick one or more files to upload to a server via a form submission

 <form action="/upload" method="post" enctype="multipart/
    form-data">
      <label for="file">File:</label>
      <input type="file" name="file" id="file">
      <input type="submit" name="submit" value="Submit">

In the preceding template, we defined a field of type file along with a Submit button.
On clicking the Submit button, the client encodes the data that forms the body of the request and makes a POST call to the form action, which is /upload in our case. 

 file, header, err := r.FormFile("file")

  _, copyFileError := io.Copy(out, file)
  if copyFileError != nil 
  {
    log.Printf("error occurred while file copy : ", copyFileError)
  }
  fmt.Fprintf(w, "File uploaded successfully : "+header.Filename)

http.HandleFunc("/upload", fileHandler)

### How it works…

Pressing the Submit button after choosing a file will result in the creation of a file on the server with the name as uploadedFile inside the /tmp directory. You can see this by executing the following commands:

Also, the successful upload will display the message on the browser, as shown in the following screenshot:

Let's understand the Go program we have written:
We defined the fileHandler() handler, which gets the file from the request, reads its content, and eventually writes it onto a file on a server. As this handler does a lot of things, let’s go through it in detail:


out, pathError := os.Create("/tmp/uploadedFile"): Here we are creating a file named uploadedFile inside a /tmp directory with mode 666, which means the client can read and write but cannot execute the file.


•  _, copyFileError := io.Copy(out, file): Here we copy content from the file we received to the file we created inside the /tmp directory.


fmt.Fprintf(w, "File uploaded successfully : "+header.Filename): Here we write a message along with a filename to an HTTP response stream.

### Working with Sessions, Error Handling, and Caching in Go

•  Creating your first HTTP session
•  Managing your HTTP session using Redis
•  Creating your first HTTP cookie
•  Implementing caching in Go
•  Implementing HTTP error handling in Go
•  Implementing login and logout in a web application

### Introduction

Sometimes, we would like to persist information such as user data at an application level rather than persisting it in a database, which can be easily achieved using sessions and cookies. 

The difference between the two is that sessions are stored on the server side, whereas cookies are stored on the client side. We may also need to cache static data to avoid unnecessary calls to a database or a web service, and implement error handling while developing a web application.

### Creating your first HTTP session

HTTP is a stateless protocol, which means each time a client retrieves a web page, the client opens a separate connection to the server and the server responds to it without keeping any record of the previous client request.

When we are working with sessions, clients just need to send an ID and the data is loaded from the server for the corresponding ID.

### How to do it…

Install the github.com/gorilla/sessions package using the go get command, as follows:
$ go get github.com/gorilla/sessions

var store *sessions.CookieStore
func init() 
{
  store = sessions.NewCookieStore([]byte("secret-key"))
}

func home(w http.ResponseWriter, r *http.Request) 
{
  session, _ := store.Get(r, "session-name")
  var authenticated interface{} = session.Values["authenticated"]
  if authenticated != nil 

  session, _ := store.Get(r, "session-name")
  session.Values["authenticated"] = true
  session.Save(r, w)
  fmt.Fprintln(w, "You have successfully logged in.")

### How it works…

This will result in an unauthorized access message from the server as shown in the following screenshot:
￼
This is because we first have to log in to an application, which will create a session ID that the server will validate before providing access to any web page. So, let's log in to the application:

Next, we will use this provided Cookie to access /home, as follows:
$ curl --cookie "session-name=MTUyMzEwMTI3NXxEdi1CQkFFQ180SUFBUkFCRUFBQUpmLUNBQUVHYzNSeWFXNW5EQThBRFdGMWRHaGxiblJwWTJGMFpXUUVZbTl2YkFJQ0FBRT18ou7Zxn3qSbqHHiajubn23Eiv8a348AhPl8RN3uTRM4M=;" http://localhost:8080/home

•  Using var store *sessions.CookieStore, we declared a private cookie store to store sessions using secure cookies.


•  Using func init() { store = sessions.NewCookieStore([]byte("secret-key")) }, we defined an init() function that runs before main() to create a new cookie store and assign it to the store.

init() is always called, regardless of whether there's a main function or not, so if you import a package that has an init function, it will be executed.

otherwise, we write a You are unauthorized to view the page. message along with a 403 HTTP code.

### Managing your HTTP session using Redis

While working with the distributed applications, we probably have to implement stateless load balancing for frontend users. This is so we can persist session information in a database or a filesystem so that we can identify the user and retrieve their information if a server gets shut down or restarted.


using Redis as the persistent store to save a session.

### Getting ready…

we will just extend this recipe to save session information in Redis rather than maintaining it on the server.

This recipe assumes you have Redis and Redis Browser installed and running locally on ports 6379 and 4567 respectively.

### How to do it…

  "github.com/gorilla/sessions"
  redisStore "gopkg.in/boj/redistore.v1"

store, err = redisStore.NewRediStore(10, "tcp", ":6379", "",
  []byte("secret-key"))

### How it works…

Executing the previous command will give us the Cookie, which has to be set as a request header to access any web page:


Once the previous command is executed, a Cookie will be created and saved in Redis, which you can see by executing the command from redis-cli or in the Redis Browser, as shown in the following screenshot:

1.  Using var store *redisStore.RediStore, we declared a private RediStore to store sessions in Redis.

2.  Next, we updated the init() function to create NewRediStore with a size and maximum number of idle connections as 10, and assigned it to the store. If there is an error while creating a store, then we log the error and exit with a status code of 1.

3.  Finally, we updated main() to introduce the defer store.Close() statement, which closes the Redis store once we return from the function.


### Creating your first HTTP cookie

Cookies play an important role when storing information on the client side and we can use their values to identify a user. Basically, cookies were invented to solve the problem of remembering information about the user or persistent-login authentication, which refers to websites being able to remember the identity of a principal between sessions.

Your device stores the text files locally, allowing your browser to access the cookie and pass data back to the original website, and are saved in name-value pairs.

### How to do it…

we will create a Gorilla secure cookie to store and retrieve cookies, as follows:

var cookieHandler *securecookie.SecureCookie
func init() 
{
  cookieHandler = securecookie.New(securecookie.
  GenerateRandomKey(64),
  securecookie.GenerateRandomKey(32))
}

cookie := &http.Cookie
    {
      Name: "first-cookie",
      Value: base64Encoded,
      Path: "/",
    }
    http.SetCookie(w, cookie)

log.Printf("Reading Cookie..")
  cookie, err := r.Cookie("first-cookie")
  if cookie != nil && err == nil 
  {
    value := make(map[string]string)
    if err = cookieHandler.Decode("key", cookie.Value, &value); 

### How it works…

Next, we will access http://localhost:8080/create, which will create a cookie with the name first-cookie

Using var cookieHandler *securecookie.SecureCookie, we declared a private secure cookie.

Next, we updated the init() function to create SecureCookie passing a 64-byte hash key, which is used to authenticate values using HMAC and a 32-byte block key, which is used to encrypt values.

Next, we defined a readCookie handler, where we retrieve a cookie from the request, which is first-cookie in our code, get a value for it, and write it to an HTTP response.

### Implementing caching in Go

Caching data in a web application is sometimes necessary to avoid requesting static data from a database or external service again and again.

### How to do it…

var newCache *cache.Cache
func init() 
{
  newCache = cache.New(5*time.Minute, 10*time.Minute)
  newCache.Set("foo", "bar", 

func getFromCache(w http.ResponseWriter, r *http.Request) 
{
  foo, found := newCache.Get("foo")

### How it works…

So, accessing the same URL again after five minutes will return Key Not Found in the Cache from the server, as follows:

### Implementing HTTP error handling in Go

Error handling means whenever an error occurs in an application, it should be logged somewhere, either in a file or in a database with the proper error message, along with the stack trace.

### How to do it…

  router := mux.NewRouter()
  router.Handle("/employee/get/{name}",
  WrapperHandler(getName)).Methods("GET")

### How it works…

1.  We defined a NameNotFoundError struct with two fields—Code of type int and Err of type error, which represents an error with an associated HTTP status code, as follows:

2.  Then, we allowed NameNotFoundError to satisfy the error interface, as follows:
func (nameNotFoundError NameNotFoundError) Error() string 
{
  return nameNotFoundError.Err.Error()
}


  switch e := err.(type) 
  {
    case NameNotFoundError:
    log.Printf("HTTP %!s(MISSING) - %!d(MISSING)", e.Err, e.Code)
    http.Error(w, e.Err.Error(), e.Code)

### Implementing login and logout in web application

we have to implement a mechanism that asks for the user's credentials before allowing them to view any web pages

### Getting ready…

we will just update it to implement login and logout mechanisms using the gorilla/securecookie package.

### How to do it…

var cookieHandler = securecookie.New
(
  securecookie.GenerateRandomKey(64),
  securecookie.GenerateRandomKey(32)
)

cookieValue := make(map[string]string)
    err = cookieHandler.Decode("session", cookie.Value,
    &cookieValue)
    if err == nil 
    {
      userName = cookieValue["username"]
    }

func clearSession(response http.ResponseWriter) 
{
  cookie := &http.Cookie
  {
    Name: "session",
    Value: "",
    Path: "/",
    MaxAge: -1,
  }
  http.SetCookie(response, cookie)
}

 var router = mux.NewRouter()
  router.HandleFunc("/", loginPage)
  router.HandleFunc("/home", homePage)
  router.HandleFunc("/login", login).Methods("POST")
  router.HandleFunc("/logout", logout).Methods("POST")
  http.Handle("/", router)

### How it works…

The hash key is used to authenticate values using HMAC and the block key is used to encrypt values.

3.  Next, we defined a setSession handler, where we create and initialize a map with the key and value as username, serialize it, sign it with a message authentication code, encode it using a cookieHandler.Encode handler, create a new HTTP cookie, and write it to an HTTP response stream.

check if both are not empty, then call a setSession handler and redirect to /home, otherwise, redirect to the root URL /.

•  var router = mux.NewRouter(): Here, we create a new router instance.

•  router.HandleFunc("/", loginPage): Here, we are registering the loginPageHandler handler with the / URL pattern using HandleFunc of the gorilla/mux package, which means the loginPage handler gets executed by passing (http.ResponseWriter, *http.Request) as parameters to it whenever we access the HTTP URL with the / pattern.

•  http.Handle("/", router): Here, we are registering the router for the / URL pattern using HandleFunc of the net/http package, which means all requests with the / URL pattern are handled by the router handler.


### Writing and Consuming RESTful Web Services in Go

•  Creating your first HTTP DELETE method
•  Versioning your REST API
•  Creating your first REST client

### Introduction

we will often also write and consume web services. This is because they expose functionality over a network, which is accessible through the HTTP protocol, making an application a single source of truth.

### How to do it…

The former writes the static array of employees and the latter writes employee details for the provided ID to an HTTP response stream, as follows:


type Employee struct 
{
  Id string `json:"id"`
  FirstName string `json:"firstName"`
  LastName string `json:"lastName"`
}
type Employees []Employee
var employees []Employee

func getEmployees(w http.ResponseWriter, r *http.Request) 
{
  json.NewEncoder(w).Encode(employees)
}

func getEmployee(w http.ResponseWriter, r *http.Request) 
{
  vars := mux.Vars(r)
  id := vars["id"]
  for _, employee := range employees 
  {
    if employee.Id == id 
    {
      if err := json.NewEncoder(w).Encode(employee);

muxRouter := mux.NewRouter().StrictSlash(true)
  router := AddRoutes(muxRouter)

### How it works…

4.  Next, we defined a static Employees array, as follows:
func init() 
{
  employees = Employees 
  {
    Employee{Id: "1", FirstName: "Foo", LastName: "Bar"},
    Employee{Id: "2", FirstName: "Baz", LastName: "Qux"},
  }
}

the former just marshals a static array of employees and writes it to an HTTP response stream

### How to do it…

  employee := Employee{}
  err := json.NewDecoder(r.Body).Decode(&employee)
  if err != nil 
  {
    log.Print("error occurred while decoding employee 
    data :: ", err)
    return
  }

### How to do it…

the HTTP PUT method and a handler that either updates the employee details for the provided ID or adds an employee to the initial static array of employees; if the ID does not exist, marshal it to the JSON, and write it to an HTTP response stream, as follows:

### How to do it…

index := GetIndex(employee.Id)
  employees = append(employees[:index], employees[index+1:]...)

func GetIndex(id string) int 
{
  for i := 0; i < len(employees); i++ 
  {
    if employees[i].Id == id 
    {
      return i
    }
  }
  return -1
}

### Versioning your REST API

However, in a case where you have a public API or an API where you do not have control over every client using it, versioning of your API may be required, as businesses need to evolve

### How to do it…

2.  Create http-rest-versioning.go where we will define two versions of the same URL path that support the HTTP GET method, with one having v1 as a prefix and the other one with v2 as a prefix in the route, as follows:

if strings.HasPrefix(r.URL.Path, "/v1") 
  {
    json.NewEncoder(w).Encode(employeesV1)
  } 
  else if strings.HasPrefix(r.URL.Path, "/v2") 
  {
    json.NewEncoder(w).Encode(employeesV2)
  } 
  else 
  {
    json.NewEncoder(w).Encode(employees)
  }

  muxRouter := mux.NewRouter().StrictSlash(true)
  router := AddRoutes(muxRouter)
  // v1
  AddRoutes(muxRouter.PathPrefix("/v1").Subrouter())
  // v2
  AddRoutes(muxRouter.PathPrefix("/v2").Subrouter())

### How it works…

Next, executing a GET request with the path prefix as /v1 from the command line as follows, will give you a list of one set of employees:
$ curl -X GET http://localhost:8080/v1/employees

1.  First, we defined a single route with the name getEmployees, which executes a getEmployees handler for every GET request for the URL pattern /employees.

3.  Next, we have defined a getEmployees handler where we check for the prefix in the URL path and perform an action based on it.

### Creating your first REST client

 we will write a REST client using the https://gopkg.in/resty.v1 package, which itself is inspired bythe Ruby rest client to consume the RESTful services.

### How to do it…

.  Create http-rest-client.go where we will define handlers that call resty handlers, such as GET, POST, PUT, and DELETE, get the response from the REST service, and write it to an HTTP response stream,

### How it works…

3.  Next, we defined a getEmployees handler where we create a new resty request object calling its R() handler, call the Get method, which performs the HTTP GET request, gets the response, and writes it to an HTTP response.

### Creating your first ReactJS client

ReactJS is a declarative JavaScript library that helps in building user interfaces efficiently. Because it works onthe concept of virtual DOM it improves application performance, since JavaScript virtual DOM is faster than theregular DOM.

### Getting ready…

 this recipe assumes you have npm installed on your machine and you have basic knowledge of npm andwebpack, which is a JavaScript Module bundler.

### How to do it…

Create another directory with the name assets where all our frontend code files, such as .html, .js, .css,and images will be kept, as follows:

### How it works…

Once we run the program, the HTTP server will start locally listening on port 8080.  

Browsing to http://localhost:8080 will show us the VueJS client page

### Working with SQL and NoSQL Databases

•  Integrating MySQL and Go
•  Creating your first record in MySQL
•  Reading records from MySQL


### Introduction

we will integrate a Go web application with the most famous open source databases—MySQL and MongoDB and learn to perform CRUD operations on them. 

I assume both of the databases are installed and running on your local machine.

### Getting ready…

Also, log into the MySQL database and create a mydb database, executing the commands as shown in the following screenshot:


### How to do it…

func init() 
{
  db, connectionError = sql.Open(DRIVER_NAME, DATA_SOURCE_NAME)
  if connectionError != nil 
  {
    log.Fatal("error connecting to database :: ", connectionError)
  }
}

  rows, err := db.Query("SELECT DATABASE() as db")

for rows.Next() 
  {
    rows.Scan(&db)
  }
  fmt.Fprintf(w, "Current Database is :: %!s(MISSING)", db)

### How it works…

we imported github.com/go-sql-driver/mysql for its side effects or initialization, using the underscore in front of an import statement explicitly.

### Getting ready…

Before creating a record, we have to create a table in the MySQL database,

### How to do it…

vals := r.URL.Query()
  name, ok := vals["name"]
  if ok 

stmt, err := db.Prepare("INSERT employee SET name=?")
    if err != nil 
    {
      log.Print("error preparing query :: ", err)
      return
    }
    result, err := stmt.Exec(name[0])

### How it works…

curl -X POST http://localhost:8080/employee/create?name=foo

### How to do it…

Create read-record-mysql.go where we connect to the MySQL database, perform a SELECT query to get all the employees from the database, iterate over the records, copy its value into the struct, add all of them to a list, and write it to an HTTP response stream

  log.Print("reading records from database")
  rows, err := db.Query("SELECT * FROM employee")

employees := []Employee{}
  for rows.Next() 
  {
    var uid int
    var name string
    err = rows.Scan(&uid, &name)
    employee := Employee{Id: uid, Name: name}
    employees = append(employees, employee)
  }
  json.NewEncoder(w).Encode(employees)

### How it works…

2.  Next, we declared the Go data structure Person, which has Id and Name fields.
Do remember that the field name should begin with a capital letter in the type definition or there could be errors. 

marshals the object list to JSON, and writes it to an HTTP response stream.

### How to do it…

stmt, err := db.Prepare("UPDATE employee SET name=? 
    where uid=?")

result, err := stmt.Exec(name[0], id)

rowsAffected, err := result.RowsAffected()
    fmt.Fprintf(w, "Number of rows updated in database 
    are :: %!d(MISSING)",rowsAffected)

### How it works…

which will be replaced dynamically, executes the statement, gets the number of rows updated as a result of its execution, and writes it to an HTTP response stream.

and closed the database using the defer db.Close() statement once we return from the main() function.

### Deleting your first record from MySQL

Consider a scenario where an employee has left the organization and you want to revoke their details from the database. 

### How to do it…

vals := r.URL.Query()
  name, ok := vals["name"]
  if ok 
  {
    log.Print("going to delete record in database for 
    name :: ", name[0])
    stmt, err := db.Prepare("DELETE from employee where name=?")


  else 
  {
    fmt.Fprintf(w, "Error occurred while deleting record in 
    database for name %!s(MISSING)", name[0])
  }

### Getting ready…

mongo
This should return the following response:

### How to do it…

Install the gopkg.in/mgo.v package, using the go get command, as follows:

var session *mgo.Session
var connectionError error
func init() 
{
  session, connectionError = mgo.Dial(MONGO_DB_URL)
  if connectionError != nil 
  {
    log.Fatal("error connecting to database :: ", connectionError)
  }
  session.SetMode(mgo.Monotonic, true)
}

fmt.Fprintf(w, "Databases names are :: %!s(MISSING)", strings.Join
  (db, ", "))

### How it works…

you have to pass both the host and port separated by a colon

### Creating your first document in MongoDB

we will learn how we can create a BSON document (a binary-encoded serialization of JSON-like documents) in a database, using a MongoDB driver for Go (gopkg.in/mgo.v2). 

### How to do it…

type Employee struct 
{
  Id int `json:"uid"`
  Name string `json:"name"`
}

log.Print("going to insert document in database for name 
    :: ", name[0])
    collection := session.DB("mydb").C("employee")
    err = collection.Insert(&Employee{employeeId, name[0]})

### How it works…

curl -X POST http://localhost:8080/employee/create?name=foo\&id=1

we converted the variable ID of string type to int type. 

### How to do it…

err := collection.Find(bson.M{}).All(&employees)

### Updating your first document in MongoDB

Once a BSON document is created we may need to update some of its fields. In that case, we have to execute update/upsert queries on the MongoDB collection

### How to do it…

collection := session.DB("mydb").C("employee")
    var changeInfo *mgo.ChangeInfo
    changeInfo, err = collection.Upsert(bson.M{"id": employeeId},
    &Employee{employeeId, name[0]})

fmt.Fprintf(w, "Number of documents updated in database 
    are :: %!d(MISSING)", changeInfo.Updated)

### How it works…

and call the collection.Upsert handler to insert if not present, or update an employee document with a new name for the supplied ID.

### How to do it…

removeErr := collection.Remove(bson.M{"name": name[0]})

### Writing Microservices in Go Using Micro – a Microservice Toolkit

Writing Microservices in Go Using Micro – a Microservice Toolkit


•  Creating your first protocol buffer
•  Spinning up a microservice discovery client 
•  Creating your first microservice

•  Interacting with microservices using a command-line interface and web UI

### Introduction

With organizations now moving toward DevOps, microservices have started gaining popularity as well. As these services are independent in nature and can be developed in any language it allows organizations to focus on their development.

we will be able to write microservices using Go Micro in a fairly easy way.


### Creating your first protocol buffer

Protocol buffers are a flexible, efficient, and automated mechanism for encoding and serializing structured data supported by Go. In this recipe, we will learn how to write our first protocol buffer.

### Getting ready…

1.  Verify whether protoc is installed by executing the following command:
$ protoc --version

### How to do it…

1.  Create hello.proto inside the proto directory and define a service interface with the name Say, which has two datatypes—Request and Response, as follows:

syntax = "proto3";
service Say 
{
  rpc Hello(Request) returns (Response) {}
}
message Request 
{
  string name = 1;
}
message Response 
{
  string msg = 1;
}

Compile hello.proto with the following command:
$ protoc --go_out=plugins=micro:. hello.proto

### How it works…

Once the command has executed successfully, hello.pb.go will be created inside the proto directory, which will look like as shown in the following screenshot:

Let’s understand the .proto file we have written:
•  syntax = "proto3";: Here we specify that we are using proto3 syntax, 

If we don't specify the syntax explicitly then the compiler assumes we are using proto2. 

•  service Say { rpc Hello(Request) returns (Response) {} }: Here we defined an RPC service with the name Say and a Hello method that takes Request and returns a Response.

•  message Request { string name = 1; }: Here we defined the Request data type that has a name field.


### Spinning up a microservice discovery client

In a microservices architecture where multiple services are deployed, the service discovery client helps the application to find out the services they are dependent on, which can be either through DNS or HTTP.

### How it works…

Moreover, browsing to http://localhost:8500/ui/ will display the Consul web UI where we can view all the services and nodes

### Getting ready…

Start consul agent by executing the following command:
$ consul agent -dev

Install and run micro by executing the following commands:
$ go get github.com/micro/micro
$ micro api
 2018/02/06 00:03:36 Registering RPC Handler at /rpc
 2018/02/06 00:03:36 Registering API Default Handler at /

### How to do it…

type Say struct{}
func (s *Say) Hello(ctx context.Context, req *hello.Request, 
rsp *hello.Response) error 
{
  log.Print("Received Say.Hello request - first greeting service")
  rsp.Msg = "Hello " + req.Name
  return nil
}

service := micro.NewService
  (
    micro.Name("go.micro.service.greeter"),
    micro.RegisterTTL(time.Second*30),
    micro.RegisterInterval(time.Second*10),
  )
  service.Init()
  hello.RegisterSayHandler(service.Server(), new(Say))
  if err := service.Run(); err != nil 

### How it works…

Next, execute a POST request from the command line as follows:
$ curl -X POST -H 'Content-Type: application/json' -d '{"service": "go.micro.service.greeter", "method": "Say.Hello", "request": {"name": "Arpit Aggarwal"}}' http://localhost:8080/rpc

we create a new service with the name go.micro.service.greeter using micro.NewService(), initialize it, register the handler with it, and finally start it.

### Creating your second microservice

demonstrates the concept of client-side load balancing between the two different instances of a service with the same name.

### Creating your Micro API

we will learn how we can access the services using Go Micro API, which implements an API gateway pattern to provide a single entry point to the microservices. The advantage of using Go Micro API is that it serves over HTTP and dynamically routes to the appropriate backend service using HTTP handlers.


### How to do it…

3.  Move to the api directory and run the program with the following command:
$ go run greeting-api.go

### Interacting with microservices using a command-line interface and web UI

Interacting with microservices using a command-line interface and web UI

### How to do it…

github.com/micro/micro

### How it works…

Querying the same greeter service using a CLI command, query go.micro.service.greeter Say.Hello {"name" : "Arpit Aggarwal"} will render you the response, {"msg": "Hello Arpit Aggarwal"}:


### Working with WebSocket in Go

•  Creating your first WebSocket server
•  Creating your first WebSocket client
•  Debugging your first local WebSocket server

### Introduction

WebSocket provides a bidirectional, single-socket, full-duplex connection between the server and the client, making real-time communication much more efficient than other ways such as long polling and server-sent events.


With WebSocket, the client and the server can talk independently, each able to send and receive information at the same time after the initial handshake, reusing the same connection from the client to the server and the server to the client, which eventually reduces the delay and server load greatly, allowing web applications to perform modern tasks in the most effective way.

So there are no compatibility issues.


we will learn how to create a WebSocket server and client, writing unit tests and debugging the server running either locally or remotely.

### Creating your first WebSocket server

 write a WebSocket server, which is a TCP application listening on port 8080 that allows connected clients to send messages to each other.

### How to do it…

.  Create websocket-server.go where we will upgrade an HTTP request to WebSocket, read the JSON message from the client, and broadcast it to all of the connected clients

var clients = make(map[*websocket.Conn]bool)
var broadcast = make(chan Message) 
var upgrader = websocket.Upgrader{}

websocket, err := upgrader.Upgrade(w, r, nil)
  if err != nil 
  {
    log.Fatal("error upgrading GET request to a 
    websocket :: ", err)
  }
  defer websocket.Close()
  clients[websocket] = true

err := websocket.ReadJSON(&message)
    if err != nil 
    {
      log.Printf("error occurred while reading 
      message : %!v(MISSING)", err)
      delete(clients, websocket)
      break
    }
    broadcast <- message

message := <-broadcast
    for client := range clients 
    {
      err := client.WriteJSON(message)
      if err != nil 
      {
        log.Printf("error occurred while writing 
        message to client: %!v(MISSING)", err)
        client.Close()
        delete(clients, client)
      }

### How it works…

4.  Next, we defined a HandleClients handler, which upon receiving the HTTP GET request, upgrades it to WebSocket, registers the client with the socket server, reads the requested JSON messages, and writes it to the broadcast channel.

### Creating your first WebSocket client

create a simple client to start the WebSocket handshake process.

The client will send a pretty standard HTTP GET request to the WebSocket server and the server upgrades it through an Upgrade header in the response.

### How to do it…

1.  Create index.html where we will open a connection to a non-secure WebSocket server on page load, as follows:

var socket = new WebSocket("ws://" + window.
    location.host + "/echo");
    socket.onopen = function () 
    {
      output.innerHTML += "Status: Connected\n";
    };

function send() 
    {
      socket.send
      (
        JSON.stringify
        (
          {
            message: input.value
          }
        )
      );
      input.value = "";
    }

### Debugging your first local WebSocket server

Debugging a web application is one of the most important skills for a developer to learn, as it helps in identifying a problem, isolating the source of the problem, and then either correcting the problem or determining a way to work around it. 

### How to do it…

rename the configuration to WebSocket Local Debug, change Run kind to Directory, and click on Apply and OK as shown in the following screenshot:

### Debugging your first remote WebSocket server

we will learn how to debug it if it is running on another or a remote machine.

### Unit testing your first WebSocket server

Unit testing or test-driven development helps the developer to design loosely-coupled code with the focus on code reusability. It also helps us to realize when to stop coding and make changes quickly.


### How to do it…

  "net/http"
  "net/http/httptest"

server := httptest.NewServer(http.HandlerFunc
  (HandleClients))
  defer server.Close()
  u := "ws" + strings.TrimPrefix(server.URL, "http")
  socket, _, err := websocket.DefaultDialer.Dial(u, nil)

  m := Message{Message: "hello"}
  if err := socket.WriteJSON(&m); err != nil 

  assert.Equal(t, "hello", message.Message, "they 

### How it works…

It will give us the response ok, which means the test compiled and executed successfully.
Let’s see how it looks when a Go test fails. 

It will give us the failure response along with the error trace 

### Working with the Go Web Application Framework – Beego

Working with the Go Web Application Framework – Beego
In this chapter, we will cover the following recipes:
•  Creating your first project using Beego
•  Creating your first controller and router
•  Creating your first view

•  Handling HTTP errors in Beego
•  Implementing caching in Beego
•  Monitoring the Beego application
•  Deploying the Beego application on a local machine
•  Deploying the Beego application with Nginx

### Introduction

A web application framework is a must whenever we are developing an application because it significantly speeds up and simplifies our work by eliminating the need to write a lot of repetitive code and providing features such as models, APIs, and other elements. 

we will learn about Beego, which is one of the most popular and commonly used web MVC frameworks. We will start with creating the project and then move on to creating controllers, views, and  filters. 

### How to do it…

github.com/beego/bee

2.  Open a terminal to your $GOPATH/src directory and create a project using the bee new command, as follows:
$ cd $GOPATH/src
$ bee new my-first-beego-project

3.  Go to the path of the newly created project and enter bee run to compile and run the project, as follows:

### Creating your first controller and router

One of the main components of a web application is the controller, which acts as a coordinator between the view and the model and handles the user's requests, which could be a button click, or a menu selection, or HTTP GET and POST requests.

### How to do it…

func (this *FirstController) GetEmployees() 
{
  this.Ctx.ResponseWriter.WriteHeader(200)
  this.Data["json"] = employees
  this.ServeJSON()
}


  beego.Router("/", &controllers.MainController{})
  beego.Router("/employees", &controllers.FirstController{},
  "get:GetEmployees")

### How it works…

•  type FirstController struct { beego.Controller }: Here, we defined the FirstController struct type, which contains an anonymous struct field of type beego.Controller because of which FirstController automatically acquires all the methods of beego.Controller.


### Creating your first view

A view is a visual representation of a model. It accesses data through the model and specifies how that data should be presented. It  maintains consistency in its presentation when the model changes, which can be either through a push model, where the view registers itself with the model for change notifications, or a pull model, where the view is responsible for calling the model when it needs to retrieve the most current data.

### How to do it…

<table border= "1" style="width:100%!;(MISSING)">
      {{range .employees}}
      <tr>
        <td>{{.Id}}</td>
        <td>{{.FirstName}}</td>
        <td>{{.LastName}}</td>
      </tr>
      {{end}}
    </table>

func (this *FirstController) Dashbaord() 
{
  this.Data["employees"] = employees
  this.TplName = "dashboard.tpl"
}

### How to do it…

create sessioncontroller.go, where we will define handlers which make sure that only authenticated users can view the home page, as follows:

func (this *SessionController) Home() 
{
  isAuthenticated := this.GetSession("authenticated")
  if isAuthenticated == nil || isAuthenticated == false 
  {
    this.Ctx.WriteString("You are unauthorized to 
    view the page.")
    return
  }
  this.Ctx.ResponseWriter.WriteHeader(200)
  this.Ctx.WriteString("Home Page")

func (this *SessionController) Login() 
{
  this.SetSession("authenticated", true)
  this.Ctx.ResponseWriter.WriteHeader(200)
  this.Ctx.WriteString("You have successfully logged in.")
}

import github.com/astaxie/beego/session/redis

### How it works…

Next, we will execute a couple of commands to see how the session works. Firstly, we will access /home by executing the following command: 
$ curl -X GET http://localhost:8080/home

This will give us an unauthorized access message as a response from the server:

Apparently, we can't access it because we have to login into the application first

Now we will use the cookie beegosessionID created as part of the /login request to access /home, as follows:
$ curl --cookie "beegosessionID=6e1c6f60141811f1371d7ea044f1c194" http://localhost:8080/home



### Creating your first filter

Sometimes, we may want to perform logic either before an action method is called or after an action method runs. In that case, we use filters

### How to do it…

var LogManager = func(ctx *context.Context) 
{ 
  fmt.Println("IP :: " + ctx.Request.RemoteAddr + ", 
  Time :: " + time.Now().Format(time.RFC850))
}


  beego.Router("/", &controllers.MainController{})
  ...
  beego.InsertFilter("/*", beego.BeforeRouter, 
  filters.LogManager)

### How it works…

Using beego.InsertFilter("/*", beego.BeforeRouter, filters.LogManager) , we inserted a filter in an application which executes for the URL pattern /* before finding a router and that is handled by LogManager. 

### Handling HTTP errors in Beego

Error handling is one of the most important aspects in a web application design because it helps in two ways. 

Firstly, it lets the application user know in a relatively friendly manner that something has gone wrong and they should contact the technical support department or someone from tech support should be notified.

### How to do it…

type ErrorController struct 
{
  beego.Controller
}
func (c *ErrorController) Error404() 
{
  c.Data["content"] = "Page Not Found"
  c.TplName = "404.tpl"
}

### Implementing caching in Beego

Caching data in a web application is sometimes necessary to avoid requesting the static data from a database or external service again and again. 

Beego supports four cache providers: file, Memcache, memory, and Redis.

### How to do it…

var beegoCache cache.Cache
var err error
func init() 
{
  beegoCache, err = cache.NewCache("memory",
  `{"interval":60}`)
  beegoCache.Put("foo", "bar", 100000*time.Second)
}

func (this *CacheController) GetFromCache() 
{
  foo := beegoCache.Get("foo")
  this.Ctx.WriteString("Hello " + fmt.Sprintf("%!v(MISSING)", foo))
}

### Monitoring the Beego application

Monitoring the Beego application
Once the Beego application is up and running, we can easily monitor application request statistics, performance, health checks, tasks, and the configuration status through its admin dashboard. We will learn how to do this in this recipe.

### How to do it…

1.  Enable the application live monitor by adding EnableAdmin = true in $GOPATH/src/my-first-beego-project/conf/app.conf, as follows:

### How it works…

Browsing http://localhost:8088/qps will show us the request statistics of an application, as shown in the following screenshot:


### Deploying the Beego application on a local machine

Deploying the Beego application on a local machine
Once the application development is over, we have to deploy it to make it available for use by the end users, which can be done either locally or remotely. 

### How to do it…

Because the application created by bee is in the development mode by default and it’s always a best practice to run an application in the production mode on the public facing servers, we have to change the RunMode as prod in $GOPATH/src/my-first-beego-project/conf/app.conf, as follows:
beego.RunMode = "prod"

2.  Include static files, configuration files, and templates as part of the Beego application bytecode file in a separate directory by executing the following commands:

3.  Move to $GOPATH/my-first-beego-app-deployment and use the nohup command to run an application as a backend process, as follows:
$ cd $GOPATH/my-first-beego-app-deployment
$ nohup ./my-first-beego-project &

### How to do it…

1.  Open the Nginx configuration file at /Users/ArpitAggarwal/nginx/conf/nginx.conf and replace the location block under server with the following content:
location / 
{
  # root html;
  # index index.html index.htm;
  proxy_pass http://localhost:8080/;
}

### Working with Go and Docker

Working with Go and Docker

•  Building a Go web application Docker image
•  Running a web application Docker container linked with a MySQL Docker container on a user-defined bridge network


### Introduction

With organizations moving towards DevOps, Docker has started to gain popularity as well. Docker allows for packaging an application with all of its dependencies into a standardized unit for software development. And if that unit runs on your local machine, we can guarantee that it will run exactly the same way, anywhere from QA, to staging, and to production environments. 

we will learn how to create a Docker image and Docker containers to deploy a simple Go web application, following which we will be looking at how we can save the container to an image and push it to the Docker registry, along with some basic concepts of Docker networking.

### Building your first Go Docker image

There are two ways by which a Docker image can be created, which is either from scratch or from a parent image.

### How to do it…

 you are building an image behind a corporate proxy, you will probably have to provide the proxy settings. You can do this by adding environment variables using the ENV statement in the Dockerfile, which we often call as a runtime customization, as follows:
FROM golang:1.9.2
....
ENV http_proxy "http://proxy.corp.com:80"
ENV https_proxy "http://proxy.corp.com:80"
...

We can also pass the proxy settings at build time to the builder using the --build-arg <varname>=<value> flag, which is called as a build time customization, as follows:

$ docker build --no-cache=true --build-arg http_proxy="http://proxy.corp.com:80" -t golang-image.

### Running your first Go Docker container

A Docker container includes an application and all of its dependencies. It shares the kernel with other containers and runs as an isolated process in the user space on the host operating system. 

we have to create and run the containers from an image

### How to do it…

Execute the docker run command to create and run a Docker container from the golang-image, assigning the container name as golang-container using the -name flag

The -d flag specified in the docker run command starts the container in a daemon mode and the hash string at the end represents the ID of the golang-container.

### How it works…

Verify whether the Docker container has been created and is running successfully by executing the following command:
$ docker ps

Once the preceding command has executed successfully, it will give us the running Docker container details, as shown in the following screenshot:

To list all the Docker containers, whether they are running or not, we have to pass an additional flag, -a, as docker ps -a.

### Pushing your Docker image to the Docker Registry

You can save an image either on a local machine or in an artifactory or to any of the public or private Docker Registries, such as Docker Hub

### How to do it…

4.  Verify whether the image has been tagged successfully by executing the docker images command:


### How it works…

To verify whether an image has been pushed successfully to the Docker Hub, browse https://hub.docker.com/, sign in using your credentials, and, once logged in, you will see the tagged image, as shown in the following screenshot:

If you performed any changes to the Docker container and want to persist them as well as part of an image, then first you have to commit the changes to a new image or to the same image using the docker commit command before tagging and pushing it to the Docker Hub, as follows:
$ docker commit <container-id> golang-image-new
$ docker tag golang-image-new arpitaggarwal/golang-image


### Creating your first user-defined bridge network

Whenever we want to connect one Docker container to another Docker container by the container name, then first we have to create a user-defined network. This is because Docker does not support automatic service discovery on the default bridge network. In this recipe, we will learn how to create our own bridge network.


### How to do it…

Execute the docker network command to create a bridge network with the name as my-bridge-network, as follows:
$ docker network create my-bridge-network

### How it works…

docker network ls

To see detailed information about my-bridge-network, run the docker network inspect command followed by the network name, as follows:

### Running a MySQL Docker image on a user-defined bridge network

we will run a MySQL image on the user-defined bridge network that we created in the previous recipe, passing the --net flag value as my-bridge-network.

### How to do it…

The --net flag specified in the docker run command connects mysql-container to my-bridge-network. The -p flag specified in the docker run command publishes the container's 3306 port to the host 3306 port. The -e flag specified in the docker run command sets the MYSQL_ROOT_PASSWORD value as my-pass, which is an environment variable of the mysql:latest image.

The -d flag specified in the docker run command starts the container in a daemon mode

### How it works…

Inspecting the my-bridge-network again will show us the mysql-container details in the Containers section, as follows:


### How to do it…

func getDBInfo(w http.ResponseWriter, r *http.Request) 
{
  rows, err := db.Query("SELECT SUBSTRING_INDEX(USER(), 
  '@', -1) AS ip, @@hostname as hostname, @@port as port,
  DATABASE() as current_database;")

var buffer bytes.Buffer

for rows.Next() 
  {
    var ip string
    var hostname string
    var port string
    var current_database string
    err = rows.Scan(&ip, &hostname, &port, &current_database)
    buffer.WriteString("IP :: " + ip + " | HostName :: " + 
    hostname + " | Port :: " + port + " | Current 
    Database :: " + current_database)
  }
  fmt.Fprintf(w, buffer.String())

### Running a web application Docker container linked with a MySQL Docker container on a user-defined bridge network

As we know Docker does not support automatic service discovery on the default bridge network, we will be using the user-defined network that we created in one of our previous recipes to run a Go web application Docker image.

### How it works…

Moreover, inspecting my-bridge-network again will show us the mysql-container and web-application-container details in the Containers section, as follows:

### Securing a Go Web Application

•  Creating a private key and SSL certificate using OpenSSL
•  Moving an HTTP server to HTTPS


•  Creating a JSON web token 
•  Securing a RESTful service using a JSON web token
•  Preventing cross-site request forgery in Go web applications


### Introduction

Application security is a very wide topic and can be implemented in various ways that are beyond the scope of this chapter.


securing Go web application REST endpoints using JSON web tokens (JWTs), and protecting our application from cross-site request forgery (CSRF) attacks.

### Creating a private key and SSL certificate using OpenSSL

To move a server running on HTTP to HTTPS, the first thing we have to do is to get the SSL certificate, which may be either self-signed or a certificate signed by a trusted certificate authority such as Comodo, Symantec, or GoDaddy. 

To get the SSL certificate signed by a trusted certificate authority, we have to provide them with a Certificate Signing Request (CSR), which mainly consists of the public key of a key pair and some additional information, whereas a self-signed certificate is a certificate that you can issue to yourself, signed with its own private key.

Self-signed certificates can be used to encrypt data as well as CA-signed certificates, but the users will be displayed with a warning that says that the certificate is not trusted by their computer or browser. Therefore, you should not use them for the production or public servers.

we will learn how to create a private key, a certificate-signing request, and a self-signed certificate.

### How to do it…

1.  Generate a private key and certificate signing request using openssl by executing the following command:
$ openssl req -newkey rsa:2048 -nodes -keyout domain.key -out domain.csr -subj "/C=IN/ST=Mumbai/L=Andheri East/O=Packt/CN=packtpub.com"

2.  Generate a certificate and sign it with the private key we just created by executing the following command:
$ openssl req -key domain.key -new -x509 -days 365 -out domain.crt -subj "/C=IN/ST=Mumbai/L=Andheri East/O=Packt/CN=packtpub.com"

### How it works…

, where domain.key is a 2,048-bit RSA private key that is used to sign the SSL certificate, and domain.crt and domain.csr are certificate-signing requests that consist of the public key of a key pair with some additional information, which is inserted into the certificate when it is signed.


•  The -newkey rsa:2048 option creates a new certificate request and a new private key that should be 2,048-bit, generated using the RSA algorithm.


•  The -nodes option specifies that the private key created will not be encrypted with a passphrase.

•  The -keyout domain.key option specifies the filename to write the newly created private key to.

•  The -subj option replaces a subject field of the input request with specified data and outputs a modified request. If we do not specify this option, then we have to answer the CSR information prompt by OpenSSL to complete the process.

The -x509 option outputs a self-signed certificate instead of a certificate request. The -days 365 option specifies the number of days to certify the certificate for. The default is 30 days.

### Moving an HTTP server to HTTPS

Moving an HTTP server to HTTPS
Once the web application development is over, it's likely that we will deploy it to the servers. While deploying, it is always recommended to run the web application on an HTTPS protocol rather than HTTP, especially for the servers that are publicly exposed.

### How to do it…

err := http.ListenAndServeTLS(CONN_HOST+":"+CONN_PORT,
  HTTPS_CERTIFICATE, DOMAIN_PRIVATE_KEY, nil)

### How it works…

Moreover, executing a GET request from the command line passing the --insecure flag with curl will skip the certificate validation, as we are using a self-signed certificate:
$ curl -X GET https://localhost:8443/ --insecure

•  err := http.ListenAndServeTLS(CONN_HOST+":"+CONN_PORT, HTTPS_CERTIFICATE, DOMAIN_PRIVATE_KEY, nil): Here, we are calling http.ListenAndServeTLS to serve HTTPS requests that handle each incoming connection in a separate Goroutine. 

ListenAndServeTLS accepts four parameters—server address, SSL certificate, private key, and a handler. Here, we are passing the server address as localhost:8443, our self-signed certificate, private key, and handler as nil, which means we are asking the server to use DefaultServeMux as a handler.

### Defining REST APIs and routes

While writing RESTful APIs, it's very common to authenticate the user before allowing them to access it. A prerequisite to authenticating the user is to create the API routes, which we will be covering in this recipe.


### How to do it…

func getStatus(w http.ResponseWriter, r *http.Request) 
{
  w.Write([]byte("API is up and running"))
}
func getEmployees(w http.ResponseWriter, r *http.Request) 
{
  json.NewEncoder(w).Encode(employees)
}
func getToken(w http.ResponseWriter, r *http.Request) 
{ 
  w.Write([]byte("Not Implemented"))
}

### How it works…

•  func getStatus(w http.ResponseWriter, r *http.Request) { w.Write([]byte("API is up and running"))}: This is a handler that just writes API is up and running to an HTTP response stream.


•  func getEmployees(w http.ResponseWriter, r *http.Request) { json.NewEncoder(w).Encode(employees)}: This is a handler that writes a static array of employees to an HTTP response stream.


### Creating a JSON web token

Creating a JSON web token
To secure your REST API or a service endpoint, you have to write a handler in Go that generates a JSON web token, or JWT.
In this recipe, we will be using https://github.com/dgrijalva/jwt-go to generate JWT

### How to do it…

2.  Create create-jwt.go, where we will define the getToken handler that generates JWT

var signature = []byte("secret")
func getToken(w http.ResponseWriter, r *http.Request) 
{
  claims := &jwt.StandardClaims
  {
    ExpiresAt: time.Now().Add(time.Hour *
    CLAIM_EXPIRY_IN_HOURS).Unix(),
    Issuer: CLAIM_ISSUER,
  }
  token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
  tokenString, _ := token.SignedString(signature)
  w.Write([]byte(tokenString))

### How it works…

, let's attempt to get the access token of the REST API through the command line:
$ curl -X GET http://localhost:8080/get-token
It will give us the JWT token generated:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1MTM1MDY4ODEsImlzcyI6IlBhY2t0In0.95vuiR7lpWt4AIBDasBzOffL_Xv78_J9rcrKkeqSW08

Next, browse to https://jwt.io/ and paste the token generated in the Encoded section to see it's decoded value, as shown in the following screenshot:

•  var signature = []byte("secret"): This is the signature held by the server. Using this, the server will be able to verify existing tokens and sign new ones.

using the JWT StandardClaims handler, which then generates a JWT token using the jwt NewWithClaims handler, and, finally, signs it with the server signature and writes it to an HTTP response stream. 


### Securing a RESTful service using a JSON web token

Securing a RESTful service using a JSON web token
Once we have a REST API endpoint and a JWT token generator handler in hand, we can easily secure our endpoints with the JWT, which we will be covering in this recipe.


### How to do it…

$ go get github.com/auth0/go-jwt-middleware
$ go get github.com/dgrijalva/jwt-go
$ go get github.com/gorilla/handlers
$ go get github.com/gorilla/mux
2.  Create http-rest-api-secured.go, where we will define the JWT middleware to check for JWTs on HTTP requests, and wrap the /employees route with it, as follows:


var signature = []byte("secret")
var jwtMiddleware = jwtmiddleware.New
(
  jwtmiddleware.Options
  {
    ValidationKeyGetter: func(token *jwt.Token) (interface{}, error) 
    {
      return signature, nil
    },
    SigningMethod: jwt.SigningMethodHS256,
  }
)

muxRouter.HandleFunc("/get-token", getToken).Methods("GET")
  muxRouter.Handle("/employees", jwtMiddleware.Handler
  (http.HandlerFunc(getEmployees))).Methods("GET")

### How it works…

Required authorization token not found
It will display us the message that the JWT was not found in the request. So, to get the list of all the employees, we have to get the access token of the API, which we can get by executing the following  command:
$ curl -X GET http://localhost:8080/get-token

Now, calling the employee API, again passing the JWT as the HTTP Authorization request header as:
$ curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1MTM1MTI2NTksImlzcyI6IlBhY2t0In0.2r_q_82erdOmt862ofluiMGr3O5x5_c0_sMyW7Pi5XE" http://localhost:8080/employees

we imported an additional package, github.com/auth0/go-jwt-middleware, with the alias as jwtmiddleware, which checks for JWTs on HTTP requests.


2.  Then, we constructed a new secure instance of jwtmiddleware, passing SigningMethod as HS256 and the ValidationKeyGetter option as a Go function that returns the key to validate the JWT. Here, a server signature is used as a key to validate the JWT.

3.  Finally, we wrapped the /employees route with a jwtmiddleware handler in main(), which means for each request with the URL pattern /employees , we check and validate the JWT before serving the response.


### Preventing cross-site request forgery in Go web applications

Implementing cross-site request forgery in Go is fairly easy using the Gorilla CSRF package

### How to do it…

2.  Create sign-up.html with name and email input text fields and an action that gets called whenever an HTML form is submitted, as follows:

<form method="POST" action="/post" accept-charset="UTF-8">
      <input type="text" name="name">
      <input type="text" name="email">
      {{ .csrfField }}
      <input type="submit" value="Sign up!">
    </form>

http.ListenAndServeTLS(CONN_HOST+":"+CONN_PORT, 
  HTTPS_CERTIFICATE, DOMAIN_PRIVATE_KEY, csrf.Protect
  (AUTH_KEY)(muxRouter))

### How it works…

Next, execute a POST request from the command line as:
$ curl -X POST --data "name=Foo&email=aggarwalarpit.89@gmail.com" https://localhost:8443/post --insecure
It will give you the Forbidden - CSRF token invalid message as a response from the server and forbids you to submit an HTML form because the server does not find a valid CSRF token as part of the request:

So, to submit a form, firstly we have to sign up, which generates a valid CSRF token by executing the following command:
$ curl -i -X GET https://localhost:8443/signup --insecure
This will give you an HTTP X-CSRF-Token , as shown in the following screenshot:

And now you have to pass it as an HTTP X-CSRF-Token request header along with an HTTP cookie to submit an HTML form, as follows:
$ curl -X POST --data "name=Foo&email=aggarwalarpit.89@gmail.com" -H "X-CSRF-Token: M9gqV7rRcXERvSJVRSYprcMzwtFmjEHKXRm6C8cDC4EjTLIt4OiNzVrHfYNB12nEx280rrKs8fqOgvfcJgQiFA==" --cookie 

•  var AUTH_KEY = []byte("authentication-key"): This is the authentication key which is used to generate the CSRF token.

signUp: This is a handler that parses sign-up.html and provides an <input> field populated with a CSRF token replacing {{ .csrfField }} in the form.

and called the http.ListenAndServeTLS passing handler as csrf.Protect(AUTH_KEY)(muxRouter), which makes sure all POST requests without a valid token will return HTTP 403 Forbidden.

### Deploying a Go Web App and Docker Containers to AWS

•  Setting up an EC2 instance to run a Docker container
•  Pulling a Docker image on an AWS EC2 instance from Docker Hub
•  Running your Go Docker container on an EC2 instance


### Introduction

Nowadays, every organization is moving toward DevOps and everyone is talking about continuous integration and continuous deployment, often termed as CI and CD, which have become must-have skills for developers to learn. When we refer to CI/CD, at a very high level, we talk about the deployment of containers to public/private clouds through continuous integration tools, such as Jenkins and Bamboo. 

As we are going to work with Docker and AWS, I will assume you possess basic knowledge of Docker and AWS.

### Creating your first EC2 instance to run a Go web application

In this recipe, we will create an EC2 instance, provision it, and run a simple Go web application.


### How to do it…

1.  Login into AWS, move to the EC2 Management Console, and click on Launch Instance in the Create Instance section

4.  Enable Auto-assign Public IP in the Configure Instance Details section, as shown in the following screenshot:

5.  Do not make any changes to the Add Storage and Add Tags section.

7.  Select Create a new key pair from the drop-down menu, give a name to the key pair, and click on the Download Key Pair button. Save the my-first-ec2-instance.pem file and click on Launch Instance

### How it works…

Once you click on Launch Instance, it will create and boot up a Linux machine on AWS, assigning the instance an ID, public DNS, and public IP through which we can access it.


### Interacting with your first EC2 instance

To deploy an application on an EC2 instance, we first have to login into it and install the necessary packages/software, which can be easily done through an SSH client

### How to do it…

1.  Set the permissions of the private key file—my-first-ec2-instance.pem—to 400, which means the user/owner can read, can't write, and can't execute, whereas the group and others can't read, can't write, and can't execute it, by executing the chmod command, as follows:
$ chmod 400 my-first-ec2-instance.pem

2.  Get the public DNS of the EC2 instance and connect to it using a private key file as an ec2-user by executing the ssh command, as follows:
$ ssh -i my-first-ec2-instance.pem ec2-user@ec2-172-31-34-99.compute-1.amazonaws.com

Once the command has executed successfully, we will be logged in to the EC2 instance and the output will look like the following:

3.  Switch to the root user from ec2-user by executing the sudo command:
[ec2-user@ip-172-31-34-99 ~]$ sudo su

### Creating, copying, and running a Go web application on your first EC2 instance

Once we have an EC2 instance ready with the required libraries installed, we can simply copy the application using the secure copy protocol and then run it using the go run command, which we will be covering in this recipe.

### How to do it…

4.  Run http-server.go in the background, executing the no hang-up or nohup command, as follows:
[ec2-user@ip-172-31-34-99 ~] $ nohup go run http-server.go &

### How to do it…

1.  Switch to the root user from the ec2-user user by executing the following command:
[ec2-user@ip-172-31-34-99 ~]$ sudo su
[root@ip-172-31-34-99 ec2-user]#
2.  Install Docker and update an EC2 instance by executing the following commands:
[root@ip-172-31-34-99 ec2-user] yum install -y docker
[root@ip-172-31-34-99 ec2-user] yum update -y

3.  Start Docker as a service on an EC2 instance by executing the following command:
[root@ip-172-31-34-99 ec2-user] service docker start

4.  Add ec2-user to the docker group so that you can execute Docker commands without using sudo, as follows:
[root@ip-172-31-34-99 ec2-user] usermod -a -G docker ec2-user

5.  Log out of the EC2 instance by executing the following commands:
[root@ip-172-31-34-99 ec2-user]# exit

6.  Log in again to pick up the new Docker group permissions by executing the following command:

This will give us the output on the console, as shown in the following screenshot:


### How it works…

This will display system-wide information regarding the Docker installation

### How to do it…

The -p flag specified in the docker run command publishes a container's port(s) to the host. As we have an HTTP server running on port 8080 inside a Docker container and we opened port 80 for inbound traffic of our E2C instance, we mapped it as 80:8080.

### How it works…

Login into an EC2 instance and verify whether the Docker container has been created and is running successfully by executing the following command:
$ docker ps

Once the preceding command has executed successfully, it will give us the running Docker container details, as shown in the following screenshot:

### Other Books You May Enjoy

Cloud Native programming with Golang

•  Build secure microservices that can effectively communicate with other services
•  Get to know about event-driven architectures by diving into message queues such as Kafka, Rabbitmq, and AWS SQS.

•  Leverage the power of containers
•  Explore Amazon cloud services fundamentals


Distributed Computing with Go

•  Learn industry best practices with technologies such as REST, OpenAPI, Docker, and so on
•  Design and build a distributed search engine
