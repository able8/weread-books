## Let's Go Further
> Alex Edwards

### 1. Introduction

a JSON API for retrieving and managing information about movies.

And at the end of the book, we’ll deploy the finished API to a Linux server running on Digital Ocean.


I’ve used Go to build a variety of production applications, from simple websites to high-frequency trading systems. I also maintain several open-source Go packages, including the popular session management system SCS.

Let’s Go Further. Copyright © 2021 Alex Edwards.

Any use of this information is at your own risk.

### 1.1. Prerequisites

This book is written as a follow up to Let’s Go, and we’ll leverage a lot of the information and code patterns from that book again here.

If you haven’t, then I highly recommend starting with Let’s Go first — especially if you’re a newcomer to Go.

If you need to upgrade your version of Go, then please go ahead and do that now. The instructions for your operating system can be found here.

•  The curl tool for working with HTTP requests and responses from your terminal. 

•  The hey tool for carrying out some basic load tests. If you have Go 1.16 on your computer, you can install hey with the go install command:
$ go install github.com/rakyll/hey@latest

•  The git version control system. Installation instructions for all operating systems can be found here.

### 2. Getting Started

we’re going to set up a project directory

•  Create a skeleton directory structure for the project and explain at a high-level how our Go code and other assets will be organized.
•  Establish a HTTP server to listen for incoming HTTP requests.
•  Introduce a sensible pattern for managing configuration settings (via command-line flags) and using dependency injection to make dependencies available to our handlers.

•  Use the httprouter package to help implement a standard RESTful structure for the API endpoints.


### 2.1. Project Setup and Skeleton Structure

Let’s kick things off by creating a greenlight directory to act as the top-level ‘home’ for this project. I’m going to create my project directory at $HOME/Projects/greenlight, but feel free to choose a different location if you wish.
$ mkdir -p $HOME/Projects/greenlight


Then change into this directory and use the go mod init command to enable modules for the project.


•  When there is a valid go.mod file in the root of your project directory, your project is a module.

•  When you’re working inside your project directory and download a dependency with go get, then the exact version of the dependency will be recorded in the go.mod file. Because the exact version is known, this makes it much easier to ensure reproducible builds across different machines and environments.

•  When you run or build the code in your project, Go will use the exact dependencies listed in the go.mod file. If the necessary dependencies aren’t already on your local machine, then Go will automatically download them for you — along with any recursive dependencies too.

The go.mod file also defines the module path (which is greenlight.alexedwards.net in my case). This is essentially the identifier that will be used as the root import path for the packages in your project.

•  It’s good practice to make the module path unique to you and your project. A common convention in the Go community is to base it on a URL that you own.

Let’s take a moment to talk through these files and folders and explain the purpose that they’ll serve in our finished project.
•  The bin directory will contain our compiled application binaries, ready for deployment to a production server.


•  The cmd/api directory will contain the application-specific code for our Greenlight API application. This will include the code for running the server, reading and writing HTTP requests, and managing authentication.

•  The internal directory will contain various ancillary packages used by our API. It will contain the code for interacting with our database, doing data validation, sending emails and so on. Basically, any code which isn’t application-specific and can potentially be reused will live in here. Our Go code under cmd/api will import the packages in the internal directory (but never the other way around).

•  The migrations directory will contain the SQL migration files for our database.
•  The remote directory will contain the configuration files and setup scripts for our production server.
•  The go.mod file will declare our project dependencies, versions and module path.
•  The Makefile will contain recipes for automating common administrative tasks — like auditing our Go code, building binaries, and executing database migrations.

It’s important to point out that the directory name internal carries a special meaning and behavior in Go: any packages which live under this directory can only be imported by code inside the parent of the internal directory. In our case, this means that any packages which live in internal can only be imported by code inside our greenlight project directory.


this means that any packages under internal cannot be imported by code outside of our project.

Before we continue, let’s quickly check that everything is setup correctly. Open the cmd/api/main.go file in your text editor and add the following code:

Save this file, then use the go run command in your terminal to compile and execute the code in the cmd/api package. All being well, you will see the following output:
$ go run ./cmd/api
Hello world!

### 2.2. A Basic HTTP Server

let’s focus our attentionon getting a HTTP server up and running.

To start with, we’ll configure our server to have just one endpoint: /v1/healthcheck. This endpoint will return some basic information about our API, including its current version number and operating environment (development, staging, production, etc.).

// Declare a string containing the application version number. Later in the book we'll 

// generate this automatically at build time, but for now we'll just store the version

// number as a hard-coded global constant.

const version = "1.0.0"

// Define a config struct to hold all the configuration settings for our application.

// For now, the only configuration settings will be the network port that we want the 

// server to listen on, and the name of the current operating environment for the

// application (development, staging, production, etc.). We will read in these  

// configuration settings from command-line flags when the application starts.

type config struct {
    port int
    env  string
}



/ Define an application struct to hold the dependencies for our HTTP handlers, helpers,

// and middleware. At the moment this only contains a copy of the config struct and a 

// logger, but it will grow to include a lot more as our build progresses.

type application struct {
    config config
    logger *log.Logger
}

// Declare an instance of the config struct.

    var cfg config

    // Read the value of the port and env command-line flags into the config struct. We

    // default to using the port number 4000 and the environment "development" if no

    // corresponding flags are provided.

    flag.IntVar(&cfg.port, "port", 4000, "API server port")
    flag.StringVar(&cfg.env, "env", "development", "Environment (development|staging|production)")
    flag.Parse()


    // Initialize a new logger which writes messages to the standard out stream, 

    // prefixed with the current date and time.

    logger := log.New(os.Stdout, "", log.Ldate | log.Ltime)


    // Declare an instance of the application struct, containing the config struct and 

    // the logger.

    app := &application{
        config: cfg,
        logger: logger,
    }

Important: If any of the terminology or patterns in the code above are unfamiliar to you, then I strongly recommend reading the first Let’s Go book which explains them in detail.

Creating the healthcheck handler
The next thing we need to do is create the healthcheckHandler method for responding to HTTP requests. For now, we’ll keep the logic in this handler really simple and have it return a plain-text response containing three pieces of information:

•  A fixed "status: available" string.
•  The API version from the hard-coded version constant.
•  The operating environment name from the env command-line flag.

Go ahead and create a new cmd/api/healthcheck.go file:
$ touch cmd/api/healthcheck.go
And then add the following code:
 File: cmd/api/healthcheck.go


// Declare a handler which writes a plain-text response with information about the 

// application status, operating environment and version.

func (app *application) healthcheckHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintln(w, "status: available")
    fmt.Fprintf(w, "environment: %!s(MISSING)\n", app.config.env)
    fmt.Fprintf(w, "version: %!s(MISSING)\n", version)
}

The important thing to point out here is that healthcheckHandler is implemented as a method on our application struct.


This is an effective and idiomatic way to make dependencies available to our handlers without resorting to global variables or closures — any dependency that the healthcheckHandler needs can simply be included as a field in the application struct when we initialize it in main().


We can see this pattern already being used in the code above, where the operating environment name is retrieved from the application struct by calling app.config.env.

should see a log message confirming that the HTTP server is running, similar to this:
$ go run ./cmd/api
2021/04/05 19:42:50 starting development server on :4000

you can use curl to make the request from your terminal:

Note: The -i flag in the command above instructs curl to display the HTTP response headers as well as the response body.

When you do this, you should see the contents of the log message change accordingly. For example:
$ go run ./cmd/api -port=3030 -env=production
2021/04/05 19:48:34 starting production server on :3030

So, to avoid problems and confusion for clients, it’s a good idea to always implement some form of API versioning.

There are two common approaches to doing this:
1.  By prefixing all URLs with your API version, like /v1/healthcheck or /v2/healthcheck.
2.  By using custom Accept and Content-Type headers on requests and responses to convey the API version, like Accept: application/vnd.greenlight-v1.


It makes it possible for developers to see which version of the API is being used at a glance, and it also means that the API can still be explored using a regular web browser (which is harder if custom headers are required).

Throughout this book we’ll version our API by prefixing all the URL paths with/v1/ — just like we did with the /v1/healthcheck endpoint in this chapter.

### 2.3. API Endpoints and RESTful Routing

For both security and semantic correctness, it’s important that we use the appropriate HTTP method for the action that the handler is performing.


So — for example — to retrieve the details of a specific movie a client will make a request like GET /v1/movies/1, instead of appending the movie ID in a query string parameter like GET /v1/movies?id=1.

http.ServeMux — the router in the Go standard library — is quite limited in terms of its functionality. In particular it doesn’t allow you to route requests to different handlers based on the request method (GET, POST, etc.), nor does it provide support for clean URLs with interpolated parameters.


Most importantly, httprouter is stable, well-tested and provides the functionality we need — and as a bonus it’s also extremely fast thanks to its use of a radix tree for URL matching. If you’re building a REST API for public consumption, then httprouter is a solid choice.

go get github.com/julienschmidt/httprouter@v1.3.0

To demonstrate how httprouter works, we’ll start by adding the two endpoints for creating a new movie and showing the details of a specific movie to our codebase. By the end of this chapter, our API endpoints will look like this:

To prevent our main() function from becoming cluttered as the API grows, let’s encapsulate all the routing rules in a new cmd/api/routes.go file.


 // Initialize a new httprouter router instance.

    router := httprouter.New()


    // Register the relevant methods, URL patterns and handler functions for our

    // endpoints using the HandlerFunc() method. Note that http.MethodGet and 

    // http.MethodPost are constants which equate to the strings "GET" and "POST" 

    // respectively.

    router.HandlerFunc(http.MethodGet, "/v1/healthcheck", app.healthcheckHandler)

// Return the httprouter instance.

    return router

There are a couple of benefits to encapsulating our routing rules in this way. The first benefit is that it keeps our main() function clean and ensures all our routes are defined in one single place.

The other big benefit, which we demonstrated in the first Let’s Go book, is that we can now easily access the router in any test code by initializing an application instance and calling the routes() method on it.

Now that the routing rules are set up, we need to make the createMovieHandler andshowMovieHandler methods for the new endpoints. 


    // When httprouter is parsing a request, any interpolated URL parameters will be

    // stored in the request context. We can use the ParamsFromContext() function to

    // retrieve a slice containing these parameter names and values.

    params := httprouter.ParamsFromContext(r.Context())


    // We can then use the ByName() method to get the value of the "id" parameter from 

    // the slice. In our project all movies will have a unique positive integer ID, but 

    // the value returned by ByName() is always a string. So we try to convert it to a 

    // base 10 integer (with a bit size of 64). If the parameter couldn't be converted, 

    // or is less than 1, we know the ID is invalid so we use the http.NotFound() 

    // function to return a 404 Not Found response.

    id, err := strconv.ParseInt(params.ByName("id"), 10, 64)
    if err != nil || id < 1 {
        http.NotFound(w, r)
        return
    }

If everything is set up correctly, you will see some responses which look similar to this:

That’s looking really good. The httprouter package has automatically sent a 405 Method Not Allowed response for us, including an Allow header which lists the HTTP methods that are supported for the endpoint.

Likewise, you can make an OPTIONS request to a specific URL and httprouter will send back a response with an Allow header detailing the supported HTTP methods. Like so:

This should result in a 404 Not Found response

The code to extract an id parameter from a URL like /v1/movies/:id is something thatwe’ll need repeatedly in our application, so let’s abstract the logic for this into a smallreuseable helper method.

Note: The readIDParam() method doesn’t use any dependencies from our application struct so it could just be a regular function, rather than a method on application. But in general, I suggest setting up all your application-specific handlers and helpers so that they are methods on application. It helps maintain consistency in your code structure, and also future-proofs your code for when those handlers and helpers change later and they do need access to a dependency.

With this helper method in place, the code in our showMovieHandler can now be made a lot simpler:

id, err := app.readIDParam(r)

Because conflicting routes aren’t allowed, there are no routing-priority rules that you need to worry about, and it reduces the risk of bugs and unintended behavior in your application.

More information about the available settings can be found here.

### 3. Sending JSON Responses

In this section of the book, we’re going to update our API handlers so that they return JSON responses instead of just plain text.

JSON (which is an acronym for JavaScript Object Notation) is a human-readable textformat which can be used to represent structured data.

As an example, in our project a movie could be represented with the following JSON:
{

•  The parentheses {} define an JSON object, which is made up of comma-separated key/value pairs.


•  The keys must be strings, and the values can be either strings, numbers, booleans, other JSON objects, or an array of any of those things (enclosed by []).

•  Strings must be surrounded by double-quotes " (rather than single quotes ') and boolean values are either true or false.

•  Outside of a string, whitespace characters in JSON do not carry any significance — it would be perfectly valid for us to represent the same movie using the following JSON instead:

•  How to create a reusable helper for sending JSON responses, which will ensure that all your API responses have a sensible and consistent structure.

### 3.1. Fixed-Format JSON

So that means you can write a JSON response from your Go handlers in the same waythat you would write any other text response: using w.Write(), io.WriteString() or oneof the fmt.Fprint functions.

In fact, the only special thing we need to do is set a Content-Type: application/json header on the response, so that the client knows it’s receiving JSON and can interpret it accordingly.


    // Create a fixed-format JSON response from a string. Notice how we're using a raw

    // string literal (enclosed with backticks) so that we can include double-quote 

    // characters in the JSON without needing to escape them? We also use the %!q(MISSING) verb to 

    // wrap the interpolated values in double-quotes.

    js := `{"status": "available", "environment": %!q(MISSING), "version": %!q(MISSING)}`
    js = fmt.Sprintf(js, app.config.env, version)

    // Set the "Content-Type: application/json" header on the response. If you forget to

    // this, Go will default to sending a "Content-Type: text/plain; charset=utf-8"

    // header instead.

    w.Header().Set("Content-Type", "application/json")

    // Write the JSON as the HTTP response body.

    w.Write([]byte(js))

If you’re running one of the newer versions of Firefox, you’ll find that it knows the response contains JSON (due to the Content-Type header) and it will present the response using the inbuilt JSON viewer. Like so:

If you click on the Raw Data tab you should see the original unformatted JSON response:
 

It can be useful for API endpoints that always return the same static JSON, or as a quick and easy way to generate small dynamic responses like we have here.

The important word here is “must”. Because our API will be a public-facing application, itmeans that our JSON responses must always be UTF-8 encoded.

And it also means that it’s safe for the client to assume that the responses it gets are always UTF-8 encoded. Because of this, including a charset=utf-8 parameter is redundant.

The RFC also explicitly notes that the application/json media type does not have a charset parameter defined, which means that it is technically incorrect to include one too.

### 3.2. JSON Encoding

or the purpose of sending JSON in a HTTP response — using json.Marshal() is generally the better choice. So let’s start with that.

The way that json.Marshal() works is conceptually quite simple — you pass a native Go object to it as a parameter, and it returns a JSON representation of that object in a []byte slice. The function signature looks like this:
func Marshal(v interface{}) ([]byte, error)

Note: The v parameter in the above method has the type interface{} (known as the empty interface). This effectively means that we’re able to pass any Go type to Marshal() as the v parameter.


// Pass the map to the json.Marshal() function. This returns a []byte slice 

    // containing the encoded JSON. If there was an error, we log it and send the client

    // a generic error message.

    js, err := json.Marshal(data)

    // Append a newline to the JSON. This is just a small nicety to make it easier to 

    // view in terminal applications.

    js = append(js, '\n')

    // At this point we know that encoding the data worked without any problems, so we

    // can safely set any necessary HTTP headers for a successful response.

    w.Header().Set("Content-Type", "application/json")

    // Use w.Write() to send the []byte slice containing the JSON as the response body.

    w.Write(js)
}

As our API grows we’re going to be sending a lot of JSON responses, so it makes senseto move some of this logic into a reusable writeJSON() helper method.


    // Note that it's OK if the provided header map is nil. Go doesn't throw an error

    // if you try to range over (or generally, read from) a nil map.

    for key, value := range headers {
        w.Header()[key] = value
    }

    // Add the "Content-Type: application/json" header, then write the status code and

    // JSON response.

    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(status)
    w.Write(js)



Now that the writeJSON() helper is in place, we can significantly simplify the code in healthcheckHandler

•  Go time.Time values (which are actually a struct behind the scenes) will be encoded as a JSON string in RFC 3339 format like "2020-11-08T06:27:59+01:00", rather than as a JSON object.

•  A []byte slice will be encoded as a base64-encoded JSON string, rather than as a JSON array. So, for example, a byte slice of []byte{'h','e','l','l','o'} would appear as "aGVsbG8=" in the JSON output. The base64 encoding uses padding and the standard character set.

•  Encoding of nested objects is supported. So, for example, if you have a slice of structs in Go that will encode to an array of objects in JSON.

•  Channels, functions and complex number types cannot be encoded. If you try to do so, you’ll get a json.UnsupportedTypeError error at runtime.


•  Any pointer values will encode as the value pointed to. Likewise, interface{} values will encode as the value contained in the interface.

it’s also possible to use Go’s json.Encoder type to perform the encoding. This allows you to encode an object to JSON and write that JSON to an output stream in a single step.

This pattern works, and it’s very neat and elegant, but if you consider it carefully you might notice a slight problem…

When we call json.NewEncoder(w).Encode(data) the JSON is created and written to the http.ResponseWriter in a single step, which means there’s no opportunity to set HTTP response headers conditionally based on whether the Encode() method returns an error or not.


Implementing that cleanly while using the json.Encoder pattern is quite difficult.

Another option is to write the JSON to an interim bytes.Buffer instead of directly to the http.ResponseWriter. You can then check for any errors, before setting the Cache-Control header and copying the JSON from the bytes.Buffer to http.ResponseWriter. But once you start doing that, it’s simpler and cleaner (as well as slightly faster) to use the alternative json.Marshal() approach instead.


Talking of speed, you might be wondering if there’s any performance difference between using json.Encoder and json.Marshal(). The short answer to that is yes… but the difference is small and in most cases you should not worry about it.

### 3.3. Encoding Structs

Instead of encoding a map to create this JSON object (like we did in the previous chapter), this time we’re going to encode a custom Movie struct.

Important: It’s crucial to point out here that all the fields in our Movie struct are exported (i.e. start with a capital letter), which is necessary for them to be visible to Go’s encoding/json package. Any fields which aren’t exported won’t be included when encoding a struct to JSON.

It’s quite simple in practice:


    // Create a new instance of the Movie struct, containing the ID we extracted from 

    // the URL and some dummy data. Also notice that we deliberately haven't set a

    // value for the Year field.

    movie := data.Movie{
        ID:        id,
        CreatedAt: time.Now(),
        Title:     "Casablanca",
        Runtime:   102,
        Genres:    []string{"drama", "romance", "war"},
        Version:   1,
    }

    // Encode the struct to JSON and send it as the HTTP response.

    err = app.writeJSON(w, http.StatusOK, movie, nil)

•  By default, the keys in the JSON object are equal to the field names in the struct (ID, CreatedAt, Title and so on). We’ll talk about how to customize these shortly.

•  If a struct field doesn’t have an explicit value set, then the JSON-encoding of the zero value for the field will appear in the output. We can see an example of this in the response above — we didn’t set a value for the Year field in our Go code, but it still appears in the JSON output with the value 0.

Changing keys in the JSON object
One of the nice things about encoding structs in Go is that you can customize the JSON by annotating the fields with struct tags.

Probably the most common use of struct tags is to change the key names that appear in the JSON object. This can be useful when your struct field names aren’t appropriate for public-facing responses, or you want to use an alternative casing style in your JSON output.

// Annotate the Movie struct with struct tags to control how the keys appear in the 

// JSON-encoded output.

type Movie struct {
    ID        int64     `json:"id"`
    CreatedAt time.Time `json:"created_at"`
    Title     string    `json:"title"`
    Year      int32     `json:"year"`
    Runtime   int32     `json:"runtime"`
    Genres    []string  `json:"genres"`
    Version   int32     `json:"version"`
}

Hiding struct fields in the JSON object
It’s also possible to control the visibility of individual struct fields in the JSON by using the omitempty and - struct tag directives.

In contrast the omitempty directive hides a field in the JSON output if and only if the struct field value is empty, where empty is defined as being:
•  Equal to false, 0, or ""
•  An empty array, slice or map
•  A nil pointer or a nil interface value

let’s make a couple more changes to our Movie struct. The CreatedAt field isn’t relevant to our end users, so let’s always hide this in the output using the - directive. And we’ll also use the omitempty directive to hide the Year, Runtime and Genres fields in the output if and only if they are empty.

    CreatedAt time.Time `json:"-"` // Use the - directive

    Title     string    `json:"title"`
    Year      int32     `json:"year,omitempty"`    // Add the omitempty directive

    Runtime   int32     `json:"runtime,omitempty"` // Add the omitempty directive

Hint: If you want to use omitempty and not change the key name then you can leave it blank in the struct tag — like this: json:",omitempty". Notice that the leading comma is still required.

We can see here that the CreatedAt struct field no longer appears in the JSON at all, and the Year field (which had the value 0) doesn’t appear either thanks to the omitempty directive. The other fields that we used omitempty on (Runtime and Genres) are unaffected.

But using the json:"-" struct tag is generally a betterchoice: it’s an explicit indication to both Go and any future readers of your code thatyou don’t want the field included in the JSON, and it helps prevents problems ifsomeone changes the field to be exported in the future without realizing theconsequences.

The string struct tag directive
A final, less-frequently-used, struct tag directive is string. You can use this on individual struct fields to force the data to be represented as a string in the JSON output.

For example, if we wanted the value of our Runtime field to be represented as a JSON string (instead of a number) we could use the string directive like this:

    Runtime   int32     `json:"runtime,omitempty,string"` // Add the string directive

For any other type of struct field it will have no effect.

### 3.4. Formatting and Enveloping Responses

We can make these easier to read in terminals by using the json.MarshalIndent() function to encode our response data, instead of the regular json.Marshal() function. This automatically adds whitespace to the JSON output, putting each element on a separate line and prefixing each line with optional prefix and indent characters.


    // Use the json.MarshalIndent() function so that whitespace is added to the encoded 

    // JSON. Here we use no line prefix ("") and tab indents ("\t") for each element.

    js, err := json.MarshalIndent(data, "", "\t")

If you restart the API and try making the same requests from your terminal again, you will now receive some nicely-whitespaced JSON responses similar to these:

it unfortunately doesn’t come for free. As well as the fact that the responses are now slightly larger in terms of total bytes, the extra work that Go does to add the whitespace has a notable impact on performance.

But if your API is operating in a very resource-constrained environment, or needs to manage extremely high levels of traffic, then this is worth being aware of and you may prefer to stick with using json.Marshal() instead.

through the standalone json.Indent() function to add the whitespace. There’s also a reverse function available, json.Compact(), which you can use to remove whitespace from JSON.

Notice how the movie data is nested under the key "movie" here, rather than being thetop-level JSON object itself?

Enveloping response data like this isn’t strictly necessary, and whether you choose to doso is partly a matter of style and taste. But there are a few tangible benefits:

1.  Including a key name (like "movie") at the top-level of the JSON helps make the response more self-documenting. For any humans who see the response out of context, it is a bit easier to understand what the data relates to.


2.  It reduces the risk of errors on the client side, because it’s harder to accidentally process one response thinking that it is something different. To get at the data, a client must explicitly reference it via the "movie" key.

Let’s start by updating the cmd/api/helpers.go file as follows:

// Define an envelope type.

type envelope map[string]interface{}


    // Create an envelope{"movie": movie} instance and pass it to writeJSON(), instead

    // of passing the plain movie struct.

    err = app.writeJSON(w, http.StatusOK, envelope{"movie": movie}, nil)

### 3.5. Advanced JSON Customization

By using struct tags, adding whitespace and enveloping the data, we’ve been able to add quite a lot of customization to our JSON responses already.

When Go is encoding a particular type to JSON, it looks to see if the type has a MarshalJSON() method implemented on it. If it has, then Go will call this method to determine how to encode it.

If the type does satisfy the interface, then Go will call its MarshalJSON() method and use the []byte slice that it returns as the encoded JSON value.
If the type doesn’t have a MarshalJSON() method, then Go will fall back to trying to encode it to JSON based on its own internal set of rules.

Hint: You can see this in action if you look at the source code for Go’s time.Time type. Behind the scenes time.Time is actually a struct, but it has a MarshalJSON() method which outputs a RFC 3339 format representation of itself. This is what gets called whenever a time.Time value is encoded to JSON.

To help illustrate this, let’s look at a concrete example in our application.

There are a few ways we could achieve this, but a clean and simple approach is to create a custom type specifically for the Runtime field, and implement a MarshalJSON() method on this custom type.

// Declare a custom Runtime type, which has the underlying type int32 (the same as our

// Movie struct field).

type Runtime int32

// Implement a MarshalJSON() method on the Runtime type so that it satisfies the 

// json.Marshaler interface. This should return the JSON-encoded value for the movie 

// runtime (in our case, it will return a string in the format "<runtime> mins").

func (r Runtime) MarshalJSON() ([]byte, error) {
    // Generate a string containing the movie runtime in the required format.

    jsonValue := fmt.Sprintf("%!d(MISSING) mins", r)

    // Use the strconv.Quote() function on the string to wrap it in double quotes. It 

    // needs to be surrounded by double quotes in order to be a valid *JSON string*.

    quotedJSONValue := strconv.Quote(jsonValue)

    // Convert the quoted string value to a byte slice and return it.

    return []byte(quotedJSONValue), nil
}

•  If your MarshalJSON() method returns a JSON string value, like ours does, then you must wrap the string in double quotes before returning it. Otherwise it won’t be interpreted as a JSON string and you’ll receive a runtime error similar to this:
json: error calling MarshalJSON for type data.Runtime: invalid character 'm' after top-level value

•  We’re deliberately using a value receiver for our MarshalJSON() method rather than a pointer receiver like func (r *Runtime) MarshalJSON(). This gives us more flexibility because it means that our custom JSON encoding will work on both Runtime values and pointers to Runtime values. As Effective Go mentions:
The rule about pointers vs. values for receivers is that value methods can be invoked on pointers and values, but pointer methods can only be invoked on pointers.

    // Use the Runtime type instead of int32. Note that the omitempty directive will

    // still work on this: if the Runtime field has the underlying value 0, then it will

    // be considered empty and omitted -- and the MarshalJSON() method we just made 

    // won't be called at all.

    Runtime Runtime  `json:"runtime,omitempty"`

You should now see a response containing the custom runtime value in the format "<runtime> mins", similar to this:


All in all, this is a pretty nice way to generate custom JSON. Our code is succinct and clear, and we’ve got a custom Runtime type that we can use wherever and whenever we need it.

But there is a downside. It’s important to be aware that using custom types can sometimes be awkward when integrating your code with other packages, and you may need to perform type conversions to change your custom type to and from a value that the other packages understand and accept.

Instead of creating a custom Runtime type, we could have implemented a MarshalJSON() method on our Movie struct and customized the whole thing. Like this:


The downside of the approach above is that the code feels quite verbose and repetitive. You might be wondering: is there a better way?

### 3.6. Sending Error Messages

At this point our API is sending nicely formatted JSON responses for successfulrequests, but if a client makes a bad request — or something goes wrong in ourapplication — we’re still sending them a plain-text error message from the http.Error()and http.NotFound() functions.In this chapter we’ll fix that by creating some additional helpers to manage errors andsend the appropriate JSON responses to our clients.

// The logError() method is a generic helper for logging an error message. Later in the

// book we'll upgrade this to use structured logging, and record additional information

// about the request including the HTTP method and URL.

func (app *application) logError(r *http.Request, err error) {
    app.logger.Println(err)
}

// The errorResponse() method is a generic helper for sending JSON-formatted error

// messages to the client with a given status code. Note that we're using an interface{}

// type for the message parameter, rather than just a string type, as this gives us

// more flexibility over the values that we can include in the response.

func (app *application) errorResponse(w http.ResponseWriter, r *http.Request, status int, message interface{}) {
    env := envelope{"error": message}

/ Write the response using the writeJSON() helper. If this happens to return an

    // error then log it, and fall back to sending the client an empty response with a

    // 500 Internal Server Error status code.

    err := app.writeJSON(w, status, env, nil)
    if err != nil {
        app.logError(r, err)
        w.WriteHeader(500)
    }
}

// The serverErrorResponse() method will be used when our application encounters an

// unexpected problem at runtime. It logs the detailed error message, then uses the

// errorResponse() helper to send a 500 Internal Server Error status code and JSON

// response (containing a generic error message) to the client.

func (app *application) serverErrorResponse(w http.ResponseWriter, r *http.Request, err error) {
    app.logError(r, err)

    message := "the server encountered a problem and could not process your request"
    app.errorResponse(w, r, http.StatusInternalServerError, message)
}

let’s update our API handlers to use these new helpers instead of the http.Error() and http.NotFound() functions.

But what about the error messages that httprouter automatically sends when it can’tfind a matching route? By default, these will still be the same plain-text (non-JSON)responses that we saw earlier in the book.

Fortunately, httprouter allows us to set our own custom error handlers when we initialize the router. These custom handlers must satisfy the http.Handler interface, which is good news for us because it means we can easily re-use the notFoundResponse() and methodNotAllowedResponse() helpers that we just made.

// Convert the notFoundResponse() helper to a http.Handler using the 

    // http.HandlerFunc() adapter, and then set it as the custom error handler for 404

    // Not Found responses.

    router.NotFound = http.HandlerFunc(app.notFoundResponse)

•  The HTTP request specifies an unsupported HTTP protocol version.
•  The HTTP request contains a missing or invalid Host header, or multiple Host headers.

•  The HTTP request contains an invalid header name or value.

•  The size of the HTTP request headers exceeds the server’s MaxHeaderBytes setting.
•  The client makes a HTTP request to a HTTPS server.

Unfortunately, these responses are hard-coded into the Go standard library, and there’s nothing we can do to customize them to use JSON instead.

we shouldn’t be overly concerned ifbad clients are sometimes sent a plain-text response instead of JSON.

### 4. Parsing JSON Requests

When a client calls this endpoint, we will expect them to provide a JSON request body containing data for the movie they want to create in our system.

For now, we’ll just focus on the reading, parsing and validation aspects of dealing with this JSON request body. 

•  How to read a request body and decode it to a native Go object using the encoding/json package.
•  How to deal with bad requests from clients and invalid JSON, and return clear, actionable, error messages.
•  How to create a reusable helper package for validating data to ensure it meets your business rules.

### 4.1. JSON Decoding

Both approaches have their pros and cons, but for the purpose of decoding JSON from a HTTP request body, using json.Decoder is generally the best choice. It’s more efficient than json.Unmarshal(), requires less code, and offers some helpful settings that you can use to tweak its behavior.

   // Declare an anonymous struct to hold the information that we expect to be in the 

    // HTTP request body (note that the field names and types in the struct are a subset

    // of the Movie struct that we created earlier). This struct will be our *target 

    // decode destination*.

    var input struct {
        Title   string   `json:"title"`
        Year    int32    `json:"year"`
        Runtime int32    `json:"runtime"`
        Genres  []string `json:"genres"`
    }

    // Initialize a new json.Decoder instance which reads from the request body, and 

    // then use the Decode() method to decode the body contents into the input struct. 

    // Importantly, notice that when we call Decode() we pass a *pointer* to the input 

    // struct as the target decode destination. If there was an error during decoding,

    // we also use our generic errorResponse() helper to send the client a 400 Bad 

    // Request response containing the error message.

    err := json.NewDecoder(r.Body).Decode(&input)
    if err != nil {
        app.errorResponse(w, r, http.StatusBadRequest, err.Error())
        return
    }


    // Dump the contents of the input struct in a HTTP response.

    fmt.Fprintf(w, "%!v(MISSING)\n", input)

There are few important and interesting things about this code to point out:

•  When calling Decode() you must pass a non-nil pointer as the target decode destination. If you don’t use a pointer, it will return a json.InvalidUnmarshalError error at runtime.

•  When decoding a JSON object into a struct, the key/value pairs in the JSON are mapped to the struct fields based on the struct tag names. If there is no matching struct tag, Go will attempt to decode the value into a field that matches the key name (exact matches are preferred, but it will fall back to a case-insensitive match).

•  There is no need to close r.Body after it has been read. This will be done automatically by Go’s http.Server, so you don’t have too.


# Create a BODY variable containing the JSON data that we want to send.
$ BODY='{"title":"Moana","year":2016,"runtime":107, "genres":["animation","adventure"]}'

# Use the -d flag to send the contents of the BODY variable as the HTTP request body.
# Note that curl will default to sending a POST request when the -d flag is used.
$ curl -i -d "$BODY" localhost:4000/v1/movies

Zero values
Let’s take a quick look at what happens if we omit a particular key/value pair in our JSON request body. For example, let’s make a request with no year in the JSON, like so:


For example, if you have the JSON string "foo" it can be decoded into a Go string, but trying to decode it into a Go int or bool will result in an error at runtime 

As we mentioned at the start of this chapter, it’s also possible to use the json.Unmarshal() function to decode a HTTP request body.



    // Use io.ReadAll() to read the entire request body into a []byte slice.

    body, err := io.ReadAll(r.Body)
    if err != nil {
        app.serverErrorResponse(w, r, err)
        return
    }

// Use the json.Unmarshal() function to decode the JSON in the []byte slice to the

    // input struct. Again, notice that we are using a *pointer* to the input

    // struct as the decode destination.

    err = json.Unmarshal(body, &input)
    if err != nil {
        app.errorResponse(w, r, http.StatusBadRequest, err.Error())
        return
    }

Using this approach is fine — the code works, and it’s clear and simple. But it doesn’t offer any benefits over and above the json.Decoder approach that we’re already taking.

Not only is the code marginally more verbose, but it’s also less efficient. If we benchmark the relative performance for this particular use case, we can see that using json.Unmarshal() requires about 80%!m(MISSING)ore memory (B/op) than json.Decoder, as well as being a tiny bit slower (ns/op).

### 4.2. Managing Bad Requests

•  What if the client sends something that isn’t JSON, like XML or some random bytes?

•  What if the JSON types don’t match the types we are trying to decode into?
•  What if the request doesn’t even contain a body?

For a private API which won’t be used by members of the public, then this behavior is probably fine and you needn’t do anything else.

To improve this, we’re going to explain how to triage the errors returned by Decode() and replace them with clearer, easy-to-action, error messages to help the client debug exactly what is wrong with their JSON.

 In this helper we’ll decode the JSON from the request body as normal, then triage the errors and replace them with our own custom messages as necessary.

    // If there is an error during decoding, start the triage...

        var syntaxError *json.SyntaxError
        var unmarshalTypeError *json.UnmarshalTypeError
        var invalidUnmarshalError *json.InvalidUnmarshalError


        switch {
        // Use the errors.As() function to check whether the error has the type 

        // *json.SyntaxError. If it does, then return a plain-english error message 

        // which includes the location of the problem.

        case errors.As(err, &syntaxError):
            return fmt.Errorf("body contains badly-formed JSON (at character %!d(MISSING))", syntaxError.Offset)


        // An io.EOF error will be returned by Decode() if the request body is empty. We

        // check for this with errors.Is() and return a plain-english error message 

        // instead.

        case errors.Is(err, io.EOF):
            return errors.New("body must not be empty")


    // Use the new readJSON() helper to decode the request body into the input struct. 

    // If this returns an error we send the client the error message along with a 400

    // Bad Request status code, just like before.

    err := app.readJSON(w, r, &input)
    if err != nil {
        app.errorResponse(w, r, http.StatusBadRequest, err.Error())
        return
    }

Let’s quickly replace this with a specialist app.badRequestResponse() helperfunction instead:

func (app *application) badRequestResponse(w http.ResponseWriter, r *http.Request, err error) {
    app.errorResponse(w, r, http.StatusBadRequest, err.Error())
}


    err := app.readJSON(w, r, &input)
    if err != nil {
        // Use the new badRequestResponse() helper.

        app.badRequestResponse(w, r, err) 
        return
    }

This is a small change, but a useful one. As our application gradually gets more complex, using specialist helpers like this to manage different kinds of errors will help ensure that our error responses remain consistent across all our endpoints.

it’s generally considered best practice in Go to return your errors and handle them gracefully.

It’s helpful here to distinguish between the two classes of error that your application might encounter.

The first class of errors are expected errors that may occur during normal operation. Some examples of expected errors are those caused by a database query timeout, a network resource being unavailable, or bad user input. These errors don’t necessarily mean there is a problem with your program itself — in fact they’re often caused by things outside the control of your program. Almost all of the time it’s good practice to return these kinds of errors and handle them gracefully.

The other class of errors are unexpected errors. These are errors which should not happen during normal operation, and if they do it is probably the result of a developer mistake or a logical error in your codebase. 

In fact, the Go standard library frequently does this when you make a logical error or try to use the language features in an unintended way — such as when trying to access an out-of-bounds index in a slice, or trying to close an already-closed channel.

But even then, I’d recommend trying to return and gracefully handle unexpected errors in most cases. The exception to this is when returning the error adds an unacceptable amount of error handling to the rest of your codebase.


The Go By Example page on panics summarizes all of this quite nicely:
A panic typically means something went unexpectedly wrong. Mostly we use it to fail fast on errors that shouldn’t occur during normal operation and that we aren’t prepared to handle gracefully.

### 4.3. Restricting Inputs

One such thing is dealing with unknown fields. For example, you can try sending a request containing the unknown field

Notice how this request works without any problems — there’s no error to inform the client that the rating field is not recognized by our application. In certain scenarios, silently ignoring unknown fields may be exactly the behavior you want, but in our case it would be better if we could alert the client to the issue.


Fortunately, Go’s json.Decoder provides a DisallowUnknownFields() setting that we can use to generate an error when this happens.

# Body contains garbage content after the first JSON value
$ curl -i -d '{"title": "Moana"} :~()' localhost:4000/v1/movies

To ensure that there are no additional JSON values (or any other content) in the request body, we will need to call Decode() a second time in our readJSON() helper and check that it returns an io.EOF (end of file) error.

We can address this by using the http.MaxBytesReader() function to limit the maximum size of the request body.

    // Use http.MaxBytesReader() to limit the size of the request body to 1MB.

    maxBytes := 1_048_576
    r.Body = http.MaxBytesReader(w, r.Body, int64(maxBytes))


    // Initialize the json.Decoder, and call the DisallowUnknownFields() method on it

    // before decoding. This means that if the JSON from the client now includes any

    // field which cannot be mapped to the target destination, the decoder will return

    // an error instead of just ignoring the field.

    dec := json.NewDecoder(r.Body)
    dec.DisallowUnknownFields()

        // If the request body exceeds 1MB in size the decode will now fail with the

        // error "http: request body too large". There is an open issue about turning

        // this into a distinct error type at https://github.com/golang/go/issues/30715.

        case err.Error() == "http: request body too large":
            return fmt.Errorf("body must not be larger than %!d(MISSING) bytes", maxBytes)


    // Call Decode() again, using a pointer to an empty anonymous struct as the

    // destination. If the request body only contained a single JSON value this will

    // return an io.EOF error. So if we get anything else, we know that there is

    // additional data in the request body and we return our own custom error message.

    err = dec.Decode(&struct{}{})
    if err != io.EOF {
        return errors.New("body must only contain a single JSON value")
    }

    return nil

Once you’ve made those changes, let’s try out the requests from earlier in the chapter again:

Those are working much better now — processing of the request is terminated and the client receives a clear error message explaining exactly what the problem is.

Lastly, let’s try making a request with a very large JSON body.

But now it’s written, it’s done. You don’t need to touch it again, and it’s something that you can copy-and-paste into other projects easily.

### 4.4. Custom JSON Decoding

In this chapter, we’re going to look at this from the other side and update our application so that the createMovieHandler accepts runtime information in this format.

If you try sending a request with the movie runtime in this format right now, you’ll get a 400 Bad Request response (since it’s not possible to decode a JSON string into an int32 type). 

To make this work, what we need to do is intercept the decoding process and manually convert the "<runtime> mins" JSON string into an int32 instead.
So how can we do that?

The key thing here is knowing about Go’s json.Unmarshaler interface, which looks like this:
type Unmarshaler interface {
    UnmarshalJSON([]byte) error
}
When Go is decoding some JSON, it will check to see if the destination type satisfies the json.Unmarshaler interface. If it does satisfy the interface, then Go will call it’s UnmarshalJSON() method to determine how to decode the provided JSON into the target type.

Let’s take a look at how to use this in practice.

        Runtime data.Runtime `json:"runtime"` // Make this field a data.Runtime type.

Then let’s head to the internal/data/runtime.go file and add a UnmarshalJSON() method to our Runtime type. In this method we need to parse the JSON string in the format "<runtime> mins", convert the runtime number to an int32, and then assign this to the Runtime value itself.


// Define an error that our UnmarshalJSON() method can return if we're unable to parse

// or convert the JSON string successfully.

var ErrInvalidRuntimeFormat = errors.New("invalid runtime format")

type Runtime int32

    // We expect that the incoming JSON value will be a string in the format 

    // "<runtime> mins", and the first thing we need to do is remove the surrounding 

    // double-quotes from this string. If we can't unquote it, then we return the 

    // ErrInvalidRuntimeFormat error.

    unquotedJSONValue, err := strconv.Unquote(string(jsonValue))
    if err != nil {
        return ErrInvalidRuntimeFormat
    }

    // Split the string to isolate the part containing the number. 

    parts := strings.Split(unquotedJSONValue, " ")

    // Sanity check the parts of the string to make sure it was in the expected format. 

    // If it isn't, we return the ErrInvalidRuntimeFormat error again.

    if len(parts) != 2 || parts[1] != "mins" {
        return ErrInvalidRuntimeFormat
    }

    // Convert the int32 to a Runtime type and assign this to the receiver. Note that we use

    // use the * operator to deference the receiver (which is a pointer to a Runtime 

    // type) in order to set the underlying value of the pointer.

    *r = Runtime(i)


then make a request using the new format runtime value in the JSON. You should see that the request completes successfully, and the number is extracted from the string and assigned the Runtime field of our input struct. 

Whereas if you make the request using a JSON number, or any other format, you should now get an error response containing the message from the ErrInvalidRuntimeFormat variable, similar to this:

### 4.5. Validating JSON Input

In many cases, you’ll want to perform additional validation checks on the data from a client to make sure it meets your specific business rules before processing it. 

•  The movie runtime is not empty and is a positive integer.

If any of those checks fail, we want to send the client a 422 Unprocessable Entity response along with error messages which clearly describe the validation failures.

// Define a new Validator type which contains a map of validation errors.

type Validator struct {
    Errors map[string]string
}

// New is a helper which creates a new Validator instance with an empty errors map.

func New() *Validator {
    return &Validator{Errors: make(map[string]string)}
}

/ Valid returns true if the errors map doesn't contain any entries.

func (v *Validator) Valid() bool {
    return len(v.Errors) == 0
}



// AddError adds an error message to the map (so long as no entry already exists for

// the given key).

func (v *Validator) AddError(key, message string) {
    if _, exists := v.Errors[key]; !exists {
        v.Errors[key] = message
    }
}

// Check adds an error message to the map only if a validation check is not 'ok'.

func (v *Validator) Check(ok bool, key, message string) {
    if !ok {
        v.AddError(key, message)
    }
}

// In returns true if a specific value is in a list of strings.

func In(value string, list ...string) bool {
    for i := range list {
        if value == list[i] {
            return true
        }
    }
    return false
}

// Unique returns true if all string values in a slice are unique.

func Unique(values []string) bool {
    uniqueValues := make(map[string]bool)

    for _, value := range values {
        uniqueValues[value] = true
    }

    return len(values) == len(uniqueValues)
}

Alright, let’s start putting the Validator type to use!



    // Initialize a new Validator instance.

    v := validator.New()




    v.Check(input.Title != "", "title", "must be provided")
    v.Check(len(input.Title) <= 500, "title", "must not be more than 500 bytes long")

    v.Check(input.Year != 0, "year", "must be provided")
    v.Check(input.Year >= 1888, "year", "must be greater than 1888")
    v.Check(input.Year <= int32(time.Now().Year()), "year", "must not be in the future")


    // Use the Valid() method to see if any of the checks failed. If they did, then use

    // the failedValidationResponse() helper to send a response to the client, passing 

    // in the v.Errors map.

    if !v.Valid() {
        app.failedValidationResponse(w, r, v.Errors)
        return
    }

With that done, we should be good to try this out.

That’s looking great. Our validation checks are working and preventing the request from being executed successfully — and even better — the client is getting a nicely formed JSON response with clear, informative, error messages for each problem.

In large projects it’s likely that you’ll want to reuse some of the same validation checks in multiple places. In our case — for example — we’ll want to use many of these same checks later when a client edits the movie data.

To prevent duplication, we can collect the validation checks for a movie into a standalone ValidateMovie() function. In theory this function could live almost anywhere in our codebase 

But personally, I like to keep the validation checks close to the relevant domain type in the internal/data package.


Important: Notice that the validation checks are now being performed on a Movie struct — not on the input struct in our handlers.



    // Copy the values from the input struct to a new Movie struct.

    movie := &data.Movie{
        Title:   input.Title,
        Year:    input.Year,
        Runtime: input.Runtime,
        Genres:  input.Genres,
    }

    // Call the ValidateMovie() function and return a response containing the errors if 

    // any of the checks fail.

    if data.ValidateMovie(v, movie); !v.Valid() {
        app.failedValidationResponse(w, r, v.Errors)
        return
    }

Firstly, you might be wondering why we’re initializing the Validator instance in ourhandler and passing it to the ValidateMovie() function — rather than initializing it inValidateMovie() and passing it back as a return value.

You might also be wondering why we’re decoding the JSON request into the input struct and then copying the data across, rather than just decoding into the Movie struct directly.


The problem with decoding directly into a Movie struct is that a client could provide the keys id and version in their JSON request, and the corresponding values would be decoded without any error into the ID and Version fields of the Movie struct — even though we don’t want them to be. 

decoding into an intermediary struct (like we are in our handler) is a cleaner, simpler, and more robust approach — albeit a little bit verbose

If you make an invalid request, you should get a response containing the error messages similar to this:

Feel free to play around with this, and try sending different values in the JSON until you’re happy that all the validation checks are working as expected.

### 5. Database Setup and Configuration

Unlike the first Let’s Go book (where we used MySQL as our database), this time we’re going to use PostgreSQL. It’s open-source, very reliable and has some helpful modern features — including support for array and JSON data types, full-text search, and geospatial queries. 

•  How to install and set up PostgreSQL on your local machine.
•  How to use the psql interactive tool to create databases, PostgreSQL extensions and user accounts.
•  How to initialize a database connection pool in Go and configure its settings to improve performance and stability.

### 5.1. Setting up PostgreSQL

The official PostgreSQL documentation contains comprehensive download and installation instructions for all types of operating systems, but if you’re using macOS you should be able to install it with:

Or if you’re using a Linux distribution you should be able to install it via your package manager. For example, if your OS supports the apt package manager (like Debian and Ubuntu does) you can install it with:
$ sudo apt install postgresql


You can check that this is available by running the psql --version command from your terminal like so:
$ psql --version


If you’re not already familiar with PostgreSQL, the process for connecting to it for the first time using psql can be a bit unintuitive. So let’s take a moment to walk through it.


During installation, an operating system user named postgres should also have been created on your machine. On Unix-based systems you can check your /etc/passwd file to confirm this, like so:
$ cat /etc/passwd | grep 'postgres'


This is important because, by default, PostgreSQL uses an authentication scheme called peer authentication for any connections from the local machine. Peer authentication means that if the current operating system user’s username matches a valid PostgreSQL user username, they can log in to PostgreSQL as that user with no further authentication. There are no passwords involved.


So it follows that if we switch to our operating system user called postgres, we should be able to connect to PostgreSQL using psql without needing any further authentication. In fact, you can do both these things in one step with the following command:
$ sudo -u postgres psql

So, just to confirm, what we’ve done here is use the sudo command (superuser do) to run the psql command as the operating system user called postgres. That opens a session in the interactive terminal-based front-end, where we are authenticated as the PostgreSQL superuser called postgres.


If you want, you can confirm this by running a "SELECT current_user" query to see which PostgreSQL user you currently are:
postgres=# SELECT current_user;


While we’re connected as the postgres superuser, let’s create a new database for our project called greenlight, and then connect to it using the \c command like so:
postgres=# CREATE DATABASE greenlight;
CREATE DATABASE
postgres=# \c greenlight


Hint: In PostgreSQL the \ character indicates a meta command. Some other useful meta commands are \l to list all databases, \dt to list tables, and \du to list users. You can also run \? to see the full list of available meta commands.

there are a couple of tasks we need to complete.

We want to set up this new user to use password-based authentication, instead of peer authentication.

It’s important to note that extensions can only be added by superusers, to a specific database.


Once that’s successfully done, you can type exit or \q to close the terminal-based front-end and revert to being your normal operating system user.


When prompted, enter the password that you set in the step above.
$ psql --host=localhost --dbname=greenlight --username=greenlight


Great! That confirms that our database and the new greenlight user with password credentials are working correctly, and that we’re able to execute SQL statements as that user without any issues.


Optimizing PostgreSQL settings
The default settings that PostgreSQL ships with are quite conservative, and you can often improve the performance of your database by tweaking the values in your postgresql.conf file.


You can check where your postgresql.conf file lives with the following SQL query:
$ sudo -u postgres psql -c 'SHOW config_file;'


If you’re interested in optimizing PostgreSQL, I recommend giving this a read.


Alternatively, you can use this web-based tool to generate suggested values based on your available system hardware. A nice feature of this tool is that it also outputs ALTER SYSTEM SQL statements, which you can run against your database to change the settings instead of altering your postgresql.conf file manually.


### 5.2. Connecting to PostgreSQL

 You can find a list of available drivers for PostgreSQL in the Go wiki, but for our project we’ll opt for the popular, reliable, and well-established pq 

To connect to the database we’ll also need a data source name (DSN), which is basically a string that contains the necessary connection parameters. 

[插图]

At a high-level:
•  We want the DSN to be configurable at runtime, so we will pass it to the application using a command-line flag rather than hard-coding it. For simplicity during development, we’ll use the DSN above as the default value for the flag.

because connections to the database are established lazily as and when needed for the first time — we will also need to use the db.PingContext() method to actually create a connection and verify that everything is set up correctly.


    // Import the pq driver so that it can register itself with the database/sql 

    // package. Note that we alias this import to the blank identifier, to stop the Go 

    // compiler complaining that the package isn't being used.

    _ "github.com/lib/pq"

// Add a db struct field to hold the configuration settings for our database connection

// pool. For now this only holds the DSN, which we will read in from a command-line flag.

type config struct {
    port int
    env  string
    db   struct {
        dsn string
    }
}




    // Read the DSN value from the db-dsn command-line flag into the config struct. We

    // default to using our development DSN if no flag is provided.

    flag.StringVar(&cfg.db.dsn, "db-dsn", "postgres://greenlight:pa55word@localhost/greenlight", "PostgreSQL DSN")


    // Call the openDB() helper function (see below) to create the connection pool,

    // passing in the config struct. If this returns an error, we log it and exit the

    // application immediately.

    db, err := openDB(cfg)
    if err != nil {
        logger.Fatal(err)
    }


    // Defer a call to db.Close() so that the connection pool is closed before the

    // main() function exits.

    defer db.Close()

// Also log a message to say that the connection pool has been successfully 

    // established.

    logger.Printf("database connection pool established")

// The openDB() function returns a sql.DB connection pool.

func openDB(cfg config) (*sql.DB, error) {
    // Use sql.Open() to create an empty connection pool, using the DSN from the config

    // struct.

    db, err := sql.Open("postgres", cfg.db.dsn)
    if err != nil {
        return nil, err
    }


    // Create a context with a 5-second timeout deadline.

    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
    defer cancel()


    // Use PingContext() to establish a new connection to the database, passing in the

    // context we created above as a parameter. If the connection couldn't be

    // established successfully within the 5 second deadline, then this will return an

    // error.

    err = db.PingContext(ctx)
    if err != nil {
        return nil, err
    }

    // Return the sql.DB connection pool.

    return db, nil

Important: We’ll talk about using context to manage timeouts properly later in the book, so don’t worry about this too much at the moment. For now, it’s sufficient to know that if the PingContext() call could not complete successfully in 5 seconds, then it will return an error.

You should now see a log message on startup confirming that the connection pool has been successfully established.

So let’s take some steps to decouple the DSN from our project code and instead store it as an environment variable on your local machine.

If you’re following along, create a new GREENLIGHT_DB_DSN environment variable by adding the following line to either your $HOME/.profile or $HOME/.bashrc files:
 File: $HOME/.profile
...

export GREENLIGHT_DB_DSN='postgres://greenlight:pa55word@localhost/greenlight'

Note: Running source will affect the current terminal window only. So, if you switch to a different terminal window, you’ll need to run it again for it to take effect.


Now let’s update our cmd/api/main.go file to access the environment variable using the os.Getenv() function, and set this as the default value for our DSN command-line flag.

It’s fairly straightforward in practice:



    // Use the value of the GREENLIGHT_DB_DSN environment variable as the default value

    // for our db-dsn command-line flag.

    flag.StringVar(&cfg.db.dsn, "db-dsn", os.Getenv("GREENLIGHT_DB_DSN"), "PostgreSQL DSN")

If you restart the application again now, you should find that it compiles correctly and works just like before.

A nice side effect of storing the DSN in an environment variable is that you can use it to easily connect to the greenlight database as the greenlight user, rather than specifying all the connection options manually when running psql. Like so:
$ psql $GREENLIGHT_DB_DSN 

### 5.3. Configuring the Database Connection Pool

explaining how the connection pool works behind the scenes, and exploring the settings we can use to change and optimize its behavior.


Note: This chapter contains quite a lot of theory, and although it’s interesting, it’s not critical to the application build. If you find it too heavy-going, feel free to skim over it for now and then circle back to it later.

The most important thing to understand is that a sql.DB pool contains two types of connections — ‘in-use’ connections and ‘idle’ connections. A connection is marked as in-use when you are using it to perform a database task, such as executing a SQL statement or querying rows, and when the task is complete the connection is then marked as idle.


When you instruct Go to perform a database task, it will first check if any idle connections are available in the pool. If one is available, then Go will reuse this existing connection and mark it as in-use for the duration of the task. If there are no idle connections in the pool when you need one, then Go will create a new additional connection.

When Go reuses an idle connection from the pool, any problems with the connection are handled gracefully. Bad connections will automatically be re-tried twice before giving up, at which point Go will remove the bad connection from the pool and create a new one to carry out the task.

The SetMaxOpenConns() method allows you to set an upper MaxOpenConns limit on the number of ‘open’ connections (in-use + idle connections) in the pool. By default, the number of open connections is unlimited.

Important: Note that ‘open’ connections is equal to ‘in-use’ plus ‘idle’ connections — it is not just ‘in-use’ connections.

But leaving it unlimited isn’t necessarily the best thing to do. By default PostgreSQL has a hard limit of 100 open connections and, if this hard limit is hit under heavy load, it will cause our pq driver to return a "sorry, too many clients already" error.


Note: The hard limit on open connections can be changed in your postgresql.conf file using the max_connections setting.

To avoid this error, it makes sense limit the number of open connections in our pool to comfortably below 100 — leaving enough headroom for any other applications or sessions that also need to use PostgreSQL.

The other benefit of setting a MaxOpenConns limit is that it acts as a very rudimentary throttle, and prevents the database from being swamped by a huge number of tasks all at once.

But setting a limit comes with an important caveat. If the MaxOpenConns limit is reached, and all connections are in-use, then any further database tasks will be forced to wait until a connection becomes free and marked as idle. 

So to mitigate this, it’s important to always set a timeout on database tasks using a context.Context object. We’ll explain how to do that later in the book.

The SetMaxIdleConns() method sets an upper MaxIdleConns limit on the number of idle connections in the pool. By default, the maximum number of idle connections is 2.

In theory, allowing a higher number of idle connections in the pool will improve performance because it makes it less likely that a new connection needs to be established from scratch — therefore helping to save resources.


But it’s also important to realize that keeping an idle connection alive comes at a cost. It takes up memory which can otherwise be used for your application and database, and it’s also possible that if a connection is idle for too long then it may become unusable. For example, by default MySQL will automatically close any connections which haven’t been used for 8 hours.


So, potentially, setting MaxIdleConns too high may result in more connections becoming unusable and more resources being used than if you had a smaller idle connection pool — with fewer connections that are used more frequently. 

The SetConnMaxLifetime() method sets the ConnMaxLifetime limit — the maximum length of time that a connection can be reused for. By default, there’s no maximum lifetime and connections will be reused forever.

If we set ConnMaxLifetime to one hour, for example, it means that all connections will be marked as ‘expired’ one hour after they were first created, and cannot be reused after they’ve expired. But note:

•  This isn’t an idle timeout. The connection will expire one hour after it was first created — not one hour after it last became idle.
•  Once every second Go runs a background cleanup operation to remove expired connections from the pool.

In theory, leaving ConnMaxLifetime unlimited (or setting a long lifetime) will help performance because it makes it less likely that new connections will need to be created from scratch. But in certain situations, it can be useful to enforce a shorter lifetime. 

If we set ConnMaxIdleTime to 1 hour, for example, any connections that have sat idle in the pool for 1 hour since last being used will be marked as expired and removed by the background cleanup operation.

This setting is really useful because it means that we can set a relatively high limit on the number of idle connections in the pool, but periodically free-up resources by removing any idle connections that we know aren’t really being used anymore.


Putting it into practice


1.  As a rule of thumb, you should explicitly set a MaxOpenConns value. This should be comfortably below any hard limits on the number of connections imposed by your database and infrastructure, and you may also want to consider keeping it fairly low to act as a rudimentary throttle.

but ideally you should tweak this value for your hardware depending on the results of benchmarking and load-testing.

2.  In general, higher MaxOpenConns and MaxIdleConns values will lead to better performance. But the returns are diminishing, and you should be aware that having a too-large idle connection pool (with connections that are not frequently re-used) can actually lead to reduced performance and unnecessary resource consumption.

3.  To mitigate the risk from point 2 above, you should set generally set a ConnMaxIdleTime value to remove idle connections that haven’t been used for a long time. In this project we’ll set a ConnMaxIdleTime duration of 15 minutes.

Rather than hard-coding these settings, lets update the cmd/api/main.go file to accept them as command line flags.


1.  We could use an integer to represent the number of seconds (or minutes) and convert this to a time.Duration.

2.  We could use a string representing the duration — like "5s" (5 seconds) or "10m" (10 minutes) — and then parse it using the time.ParseDuration() function.

Both approaches would work fine, but we’ll go with option 2 in this project.

// Add maxOpenConns, maxIdleConns and maxIdleTime fields to hold the configuration

// settings for the connection pool.

type config struct {
    port int
    env  string
    db   struct {
        dsn          string
        maxOpenConns int
        maxIdleConns int
        maxIdleTime  string
    }
}


    flag.StringVar(&cfg.db.maxIdleTime, "db-max-idle-time", "15m", "PostgreSQL max connection idle time")



    // Set the maximum number of open (in-use + idle) connections in the pool. Note that

    // passing a value less than or equal to 0 will mean there is no limit.

    db.SetMaxOpenConns(cfg.db.maxOpenConns)


    // Use the time.ParseDuration() function to convert the idle timeout duration string

    // to a time.Duration type.

    duration, err := time.ParseDuration(cfg.db.maxIdleTime)
    if err != nil {
        return nil, err
    }

    // Set the maximum idle timeout.

    db.SetConnMaxIdleTime(duration)

But later in the book we’ll do a bit of load testing, and explain how to monitor the state of the connection pool in real-time using the db.Stats() method. At that point, you’ll be able to see some of the behavior that we’ve talked about in this chapter in action.

### 6. SQL Migrations

But instead, we’re going to explore how to use SQL migrations to create the table (and more generally, manage database schema changes throughout the project).

•  The high-level principles behind SQL migrations and why they are useful.
•  How to use the command-line migrate tool to programmatically manage changes to your database schema.


### 6.1. An Overview of SQL Migrations

In case you’re not familiar with the idea of SQL migrations, at a very high-level the concept works like this:


1.  For every change that you want to make to your database schema (like creating a table, adding a column, or removing an unused index) you create a pair of migration files. One file is the ‘up’ migration which contains the SQL statements necessary to implement the change, and the other is a ‘down’ migration which contains the SQL statements to reverse (or roll-back) the change.

2.  Each pair of migration files is numbered sequentially, usually 0001, 0002, 0003... or with a Unix timestamp, to indicate the order in which migrations should be applied to a database.

The tool keeps track of which migrations have already been applied, so that only the necessary SQL statements are actually executed.

Using migrations to manage your database schema, rather than manually executing the SQL statements yourself, has a few benefits:

•  The database schema (along with its evolution and changes) is completely described by the ‘up’ and ‘down’ SQL migration files. And because these are just regular files containing some SQL statements, they can be included and tracked alongside the rest of your code in a version control system.

•  It’s possible to replicate the current database schema precisely on another machine by running the necessary ‘up’ migrations. 

This is a big help when you need to manage and synchronize database schemas in different environments (development, testing, production, etc.).

•  It’s possible to roll-back database schema changes if necessary by applying the appropriate ‘down’ migrations.

To manage SQL migrations in this project we’re going to use the migrate command-line tool (which itself is written in Go).

Detailed installation instructions for different operating systems can be found here, but on macOS you should be able to install it with the command:
$ brew install golang-migrate

$ curl -L https://github.com/golang-migrate/migrate/releases/download/v4.14.1/migrate.linux-amd64.tar.gz | tar xvz
$ mv migrate.linux-amd64 $GOPATH/bin/migrate


Before you continue, please check that it’s available and working on your machine by trying to execute the migrate binary with the -version flag. It should output the current version number similar to this:
$ migrate -version
4.14.1

### 6.2. Working with SQL Migrations

The first thing we need to do is generate a pair of migration files using the migrate create command. 

•  The -seq flag indicates that we want to use sequential numbering like 0001, 0002, ... for the migration files (instead of a Unix timestamp, which is the default).

•  The -ext flag indicates that we want to give the migration files the extension .sql.
•  The -dir flag indicates that we want to store the migration files in the ./migrations directory (which will be created automatically if it doesn’t already exist).

•  The name create_movies_table is a descriptive label that we give the migration files to signify their contents.

At the moment these two new files are completely empty. Let’s edit the ‘up’ migration file to contain the necessary CREATE TABLE statement for our movies table, like so:

version integer NOT NULL DEFAULT 1

This is important because it means we’ll be able to easily map the data in each row of our movies table to a single Movie struct in our Go code.

If you’re not familiar with the different PostgreSQL data types in the SQL statement above, the official documentation provides a comprehensive overview. But the most important things to point out are that:


•  The id column has the type bigserial, which is a 64-bit auto-incrementing integer starting at 1. This will be the primary key for the table.


•  The genres column has the type text[] which is an array of zero-or-more text values. It’s important to note that arrays in PostgreSQL are themselves queryable and indexable

•  For storing strings we’re using the text type, instead of the alternative varchar or varchar(n) types. 

explains why text is generally the best character type to use in PostgreSQL.

The DROP TABLE command in PostgreSQL always removes any indexes and constraints that exist for the target table, so this single statement is sufficient to reverse the ‘up’.
Great, that’s our first pair of migration files ready to go!

While we are at it, let’s also create a second pair of migration files containing CHECK constraints to enforce some of our business rules at the database-level.

ALTER TABLE movies ADD CONSTRAINT movies_runtime_check CHECK (runtime >= 0);

ALTER TABLE movies ADD CONSTRAINT movies_year_check CHECK (year BETWEEN 1888 AND date_part('year', now()));

ALTER TABLE movies DROP CONSTRAINT IF EXISTS movies_runtime_check;

When we insert or update data in our movies table, if any of these checks fail our database driver will return an error similar to this:
pq: new row for relation "movies" violates check constraint "movies_year_check"

If everything is set up correctly, you should see some output confirming that the migrations have been executed successfully.

$ migrate -path=./migrations -database=$GREENLIGHT_DB_DSN up

At this point, it’s worth opening a connection to your database and listing the tables with the \dt meta command

The schema_migrations table is automatically generated by the migrate tool and used to keep track of which migrations have been applied. Let’s take a quick look inside it:
greenlight=> SELECT * FROM schema_migrations;

if you want to see which migration version your database is currently on you can run the migrate tool’s version command, like so:
$ migrate -path=./migrations -database=$EXAMPLE_DSN version

You can also migrate up or down to a specific version by using the goto command:
$ migrate -path=./migrations -database=$EXAMPLE_DSN goto 1

You can use the down command to roll-back by a specific number of migrations. For example, to rollback the most recent migration you would run:
$ migrate -path=./migrations -database =$EXAMPLE_DSN down 1


It’s important to talk about what happens when you make a syntax error in your SQL migration files, because the behavior of the migrate tool can be a bit confusing to start with.

If the migration file which failed contained multiple SQL statements, then it’s possible that the migration file was partially applied before the error was encountered. In turn, this means that the database is in an unknown state as far as the migrate tool is concerned.

What you need to do is investigate the original error and figure out if the migration file which failed was partially applied. If it was, then you need to manually roll-back the partially applied migration.

Once that’s done, then you must also ‘force’ the version number in the schema_migrations table to the correct value. For example, to force the database version number to 1 you should use the force command like so:
$ migrate -path=./migrations -database=$EXAMPLE_DSN force 1

Once you force the version, the database is considered ‘clean’ and you should be able to run migrations again without any problem.

The migrate tool also supports reading migration files from remote sources including Amazon S3 and GitHub repositories. For example:


More information about this functionality and a full list of the supported remote resources can be found here.

Running migrations on application startup

If you want, it is also possible to use the golang-migrate/migrate Go package (not the command-line tool) to automatically execute your database migrations on application start up.

tightly coupling the execution of migrations with your application source code can potentially be limiting and problematic in the longer term.

The article decoupling database migrations from server startup provides a good discussion on this, and I recommend reading it if this is a topic that you’re interested in. It’s Python-focused, but don’t let that put you off — the same principles apply in Go applications too.


### 7. CRUD Operations

we’re going to focus on building up the functionality for creating, reading, updating and deleting movies in our system.


How to create a database model which isolates all the logic for executingSQL queries against your database.

•  How to implement the four basic CRUD (create, read, update and delete) operations on a specific resource in the context of an API.

### 7.1. Setting up the Movie Model

If you don’t like the term model then you might want to think of this as your data access or storage layer instead. But whatever you prefer to call it, the principle is the same — it will encapsulate all the code for reading and writing movie data to and from our PostgreSQL database.


// Define a MovieModel struct type which wraps a sql.DB connection pool.

type MovieModel struct {
    DB *sql.DB
}

// Add a placeholder method for inserting a new record in the movies table.

func (m MovieModel) Insert(movie *Movie) error {
    return nil
}



As an additional step, we’re going to wrap our MovieModel in a parent Models struct. Doing this is totally optional, but it has the benefit of giving you a convenient single ‘container’ which can hold and represent all your database models as your application grows.


// Create a Models struct which wraps the MovieModel. We'll add other models to this,

// like a UserModel and PermissionModel, as our build progresses.

type Models struct {
    Movies MovieModel
}

// For ease of use, we also add a New() method which returns a Models struct containing

// the initialized MovieModel.

func NewModels(db *sql.DB) Models {
    return Models{
        Movies: MovieModel{DB: db},
    }
}

and then passed to our handlers as a dependency

// Add a models field to hold our new Models struct.

type application struct {
    config config
    logger *log.Logger
    models data.Models
}



    // Use the data.NewModels() function to initialize a Models struct, passing in the 

    // connection pool as a parameter.

    app := &application{
        config: cfg,
        logger: logger,
        models: data.NewModels(db),
    }

One of the nice things about this pattern is that the code to execute actions on our movies table will be very clear and readable from the perspective of our API handlers. For example, we’ll be able to execute the Insert() method by simply writing:
app.models.Movies.Insert(...)

The general structure is also easy to extend. When we create more database models in the future, all we have to do is include them in the Models struct and they will automatically be available to our API handlers.

With a small tweak to this pattern, it’s also possible to support mocking of your database models for the purpose of unit testing. 

But as a quick example, you could create a MockMovieModel struct similar to this:


type MockMovieModel struct{}

func (m MockMovieModel) Insert(movie *Movie) error {
    // Mock the action...

}

type Models struct {
    // Set the Movies field to be an interface containing the methods that both the 

    // 'real' model and mock model need to support.

    Movies interface {
        Insert(movie *Movie) error
        Get(id int64) (*Movie, error)
        Update(movie *Movie) error
        Delete(id int64) error
    }
}

// Create a helper function which returns a Models instance containing the mock models

// only.

func NewMockModels() Models {
    return Models{
        Movies: MockMovieModel{},
    }
}

### 7.2. Creating a New Movie

Specifically, we want it to execute the following SQL query:
INSERT INTO movies (title, year, runtime, genres) 
VALUES ($1, $2, $3, $4)
RETURNING id, created_at, version

•  It uses $N notation to represent placeholder parameters for the data that we want to insert in the movies table. 

it’s important to use placeholder parameters to help prevent SQL injection attacks, unless you have a very specific reason for not using them.

The remaining columns in the movies table will be filled with system-generated values at the moment of insertion — the id will be an auto-incrementing integer, and the created_at and version values will default to the current time and 1 respectively.

In this query we’re using it to return the system-generated id, created_at and version values.


Throughout this project we’ll stick with using Go’s database/sql package to execute our database queries, rather than using a third-party ORM or other tool.

// The Insert() method accepts a pointer to a movie struct, which should contain the 

// data for the new record.

func (m MovieModel) Insert(movie *Movie) error 


    // Create an args slice containing the values for the placeholder parameters from 

    // the movie struct. Declaring this slice immediately next to our SQL query helps to

    // make it nice and clear *what values are being used where* in the query.

    args := []interface{}{movie.Title, movie.Year, movie.Runtime, pq.Array(movie.Genres)}


    // Use the QueryRow() method to execute the SQL query on our connection pool,

    // passing in the args slice as a variadic parameter and scanning the system-

    // generated id, created_at and version values into the movie struct.

    return m.DB.QueryRow(query, args...).Scan(&movie.ID, &movie.CreatedAt, &movie.Version)
}



Because the Insert() method signature takes a *Movie pointer as the parameter, when we call Scan() to read in the system-generated data we’re updating the values at the location the parameter points to. Essentially, our Insert() method mutates the Movie struct that we pass to it and adds the system-generated values to it.

The next thing to talk about is the placeholder parameter inputs, which we declare in an args slice like this:
args := []interface{}{movie.Title, movie.Year, movie.Runtime, pq.Array(movie.Genres)}

Storing the inputs in a slice isn’t strictly necessary, but as mentioned in the code comments above it’s a nice pattern that can help the clarity of your code. Personally, I usually do this for SQL queries with more than three placeholder parameters.

Also, notice the final value in the slice? In order to store our movie.Genres value (which is a []string slice) in the database, we need to pass it through the pq.Array() adapter function before executing the SQL query.

Behind the scenes, the pq.Array() adapter takes our []string slice and converts it to a pq.StringArray type.

 Let’s hook up the Insert() method to our createMovieHandler so that our POST /v1/movies endpoint works in full

// Note that the movie variable contains a *pointer* to a Movie struct.

    movie := &data.Movie{
        Title:   input.Title,
        Year:    input.Year,
        Runtime: input.Runtime,
        Genres:  input.Genres,
    }

// Call the Insert() method on our movies model, passing in a pointer to the 

    // validated movie struct. This will create a record in the database and update the 

    // movie struct with the system-generated information.

    err = app.models.Movies.Insert(movie)
    if err != nil {
        app.serverErrorResponse(w, r, err)
        return
    }



We make an  

    // empty http.Header map and then use the Set() method to add a new Location header,

    // interpolating the system-generated ID for our new movie in the URL.

    headers := make(http.Header)
    headers.Set("Location", fmt.Sprintf("/v1/movies/%!d(MISSING)", movie.ID))


    // Write a JSON response with a 201 Created status code, the movie data in the 

    // response body, and the Location header.

    err = app.writeJSON(w, http.StatusCreated, envelope{"movie": movie}, headers)

OK, let’s try this out!

Location: /v1/movies/1

That’s looking perfect. We can see that the JSON response contains all the information for the new movie, including the system-generated ID and version numbers. And the response also includes the Location: /v1/movies/1 header, pointing to the URL which will later represent the movie in our system.

If you’re coding-along, please run the following commands to create three more movie records in the database:
$ BODY='{"title":"Black Panther","year":2018,"runtime":"134 mins","genres":["action","adventure"]}'
$ curl -d "$BODY" localhost:4000/v1/movies


At this point you might also want to take a look in PostgreSQL to confirm that the records have been created properly. You should see that the contents of the movies table now looks similar to this (including the appropriate movie genres in an array)

A nice feature of the PostgreSQL placeholder parameter $N notation is that you can use the same parameter value in multiple places in your SQL statement. 

Executing multiple statements
Occasionally you might find yourself in the position where you want to execute more than one SQL statement in the same database call, like this:
stmt := `
    UPDATE foo SET bar = true; 
    UPDATE foo SET baz = false;`

err := db.Exec(stmt)

Having multiple statements in the same call is supported by the pq driver, so long as the statements do not contain any placeholder parameters. If they do contain placeholder parameters, then you’ll receive the following error message at runtime:
pq: cannot insert multiple commands into a prepared statement

To work around this, you will need to either split out the statements into separate database calls, or if that’s not possible, you can create a custom function in PostgreSQL which acts as a wrapper around the multiple SQL statements that you want to run.


### 7.3. Fetching a Movie

To avoid making an unnecessary database call, we take a shortcut

    // and return an ErrRecordNotFound error straight away.

    if id < 1 {
        return nil, ErrRecordNotFound
    }


    // Declare a Movie struct to hold the data returned by the query.

    var movie Movie

    // Execute the query using the QueryRow() method, passing in the provided id value  

    // as a placeholder parameter, and scan the response data into the fields of the 

    // Movie struct. Importantly, notice that we need to convert the scan target for the 

    // genres column using the pq.Array() adapter function again.

    err := m.DB.QueryRow(query, id).Scan(
        &movie.ID,
        &movie.CreatedAt,
        &movie.Title,
        &movie.Year,
        &movie.Runtime,
        pq.Array(&movie.Genres),
        &movie.Version,
    )

Why not use an unsigned integer for the movie ID?

This might have led you to wonder: if the movie ID is never negative, why aren’t we using an unsigned uint64 type to store the ID in our Go code, instead of an int64?

There are two reasons for this.
The first reason is because PostgreSQL doesn’t have unsigned integers. It’s generally sensible to align your Go and database integer types to avoid overflows or other compatibility problems, so because PostgreSQL doesn’t have unsigned integers, this means that we should avoid using uint* types in our Go code for any values that we’re reading/writing to PostgreSQL too.

By sticking with an int64 in our Go code, we eliminate the risk of ever encountering this error.

### 7.4. Updating a Movie

6.  Call the Update() method to store the updated movie record in our database.
7.  Write the updated movie data in a JSON response using the app.writeJSON() helper.


    // Declare an input struct to hold the expected data from the client.

    var input struct {
        Title   string       `json:"title"`
        Year    int32        `json:"year"`
        Runtime data.Runtime `json:"runtime"`
        Genres  []string     `json:"genres"`
    }

    // Read the JSON request body data into the input struct.

    err = app.readJSON(w, r, &input)
    if err != nil {
        app.badRequestResponse(w, r, err)
        return
    }


    // Pass the updated movie record to our new Update() method.

    err = app.models.Movies.Update(movie)
    if err != nil {
        app.serverErrorResponse(w, r, err)
        return
    }

    // Write the updated movie record in a JSON response.

    err = app.writeJSON(w, http.StatusOK, envelope{"movie": movie}, nil)
    if err != nil {
        app.serverErrorResponse(w, r, err)
    }

You should also be able to verify that the change has been persisted by making a GET /v1/movies/2 request again

### 7.5. Deleting a Movie

•  If a movie with the id provided in the URL exists in the database, we want to delete the corresponding record and return a success message to the client.

•  If the movie id doesn’t exist, we want to return a 404 Not Found response to the client.

•  If the number of rows affected is 1, then we know that the movie existed in the table and has now been deleted… so we can send the client a success message.

•  Conversely, if the number of rows affected is 0 we know that no movie with that id existed at the point we tried to delete it, and we can send the client a 404 Not Found response.

func (m MovieModel) Delete(id int64) error {
    // Return an ErrRecordNotFound error if the movie ID is less than 1.

    if id < 1 {
        return ErrRecordNotFound
    }


    // Execute the SQL query using the Exec() method, passing in the id variable as

    // the value for the placeholder parameter. The Exec() method returns a sql.Result

    // object.

    result, err := m.DB.Exec(query, id)
    if err != nil {
        return err
    }


    // If no rows were affected, we know that the movies table didn't contain a record

    // with the provided ID at the moment we tried to delete it. In that case we 

    // return an ErrRecordNotFound error.

    if rowsAffected == 0 {
        return ErrRecordNotFound
    }



if they are machines, then a 204 No Content response is probably sufficient.


The delete operation should work without any problems, and you should receive the confirmation message like so:
$ curl -X DELETE localhost:4000/v1/movies/3
{
    "message": "movie successfully deleted"
}

If you repeat the same request to delete the already-deleted movie, you should now get a 404 Not Found response and error message.

### 8. Advanced CRUD Operations

•  How to support partial updates to a resource (so that the client only needs to send the data that they want to change).
•  How to use optimistic concurrency control to avoid race conditions when two clients try to update the same resource at the same time.


•  How to use context timeouts to terminate long-running database queries and prevent unnecessary resource use.


### 8.1. Handling Partial Updates

it supports partial updates of the movie records. Conceptually this is a little more complicated than making a complete replacement, which is why we laid the groundwork with that approach first.

The key thing to notice here is that pointers have the zero-value nil.

So — in theory — we could change the fields in our input struct to be pointers. Then to see if a client has provided a particular key/value pair in the JSON, we can simply check whether the corresponding field in the input struct equals nil or not.


    Genres  []string      `json:"genres"`  // We don't need to change this because slices already have the zero-value nil.


    // Use pointers for the Title, Year and Runtime fields.

    var input struct {
        Title   *string       `json:"title"`
        Year    *int32        `json:"year"`
        Runtime *data.Runtime `json:"runtime"`
        Genres  []string      `json:"genres"`
    }


    // If the input.Title value is nil then we know that no corresponding "title" key/

    // value pair was provided in the JSON request body. So we move on and leave the 

    // movie record unchanged.

Importantly, because input.Title is a now a pointer to a string, we need 

    // to dereference the pointer using the * operator to get the underlying value

    // before assigning it to our movie record.

    if input.Title != nil {
        movie.Title = *input.Title
    }

    // We also do the same for the other fields in the input struct.

    if input.Year != nil {
        movie.Year = *input.Year
    }

To summarize this: we’ve changed our input struct so that all the fields now have the zero-value nil. After parsing the JSON request, we then go through the input struct fields and only update the movie record if the new value is not nil.

In addition to this, for API endpoints which perform partial updates on a resource, it’s appropriate to the use the HTTP method PATCH rather than PUT (which is intended for replacing a resource in full).


    // Require a PATCH request, rather than PUT.

    router.HandlerFunc(http.MethodPatch, "/v1/movies/:id", app.updateMovieHandler)

We can see that the year value has been correctly updated, and the version number has been incremented, but none of the other data fields have been changed.

One special-case to be aware of is when the client explicitly supplies a field in the JSON request with the value null. In this case, our handler will ignore the field and treat it like it hasn’t been supplied.

For example, the following request would result in no changes to the movie record (apart from the version number being incremented):

“JSON items with null values will be ignored and will remain unchanged”.

### 8.2. Optimistic Concurrency Control

there is a race condition if two clients try to update the same movie record at exactly the same time.


Now imagine that Alice and Bob send these two update requests at exactly the same time. As we explained in Let’s Go, Go’s http.Serverhandles each HTTP request in its own goroutine, so when this happens the code in our updateMovieHandler will be running concurrently in two different goroutines.

Alice’s update to the movie runtime will be lost when Bob’s update overwrites it with the old runtime value. And this happens silently — there’s nothing to inform either Alice or Bob of the problem.

Note: This specific type of race condition is known as a data race. Data races can occur when two or more goroutines try to use a piece of shared data (in this example the movie record) at the same time, but the result of their operations is dependent on the exact order that the scheduler executes their instructions.

Now we understand that the data race exists and why it’s happening, how can we prevent it?


There are a couple of options, but the simplest and cleanest approach in this case is to use a form of optimistic locking based on the version number in our movie record.


But the update is only executed if the version number in the database is still N. If it has changed, then we don’t execute the update and send the client an error message instead.

This means that the first update request that reaches our database will succeed, and whoever is making the second update will receive an error message instead of having their change applied.

To make this work, we’ll need to change the SQL statement for updating a movie so that it looks like this:

OK, that’s enough theory… let’s put this into practice!

var (
    ErrRecordNotFound = errors.New("record not found")
    ErrEditConflict   = errors.New("edit conflict")
)



    // Add the 'AND version = $6' clause to the SQL query.

    query := `
        UPDATE movies 
        SET title = $1, year = $2, runtime = $3, genres = $4, version = version + 1
        WHERE id = $5 AND version = $6
        RETURNING version`

func (app *application) editConflictResponse(w http.ResponseWriter, r *http.Request) {
    message := "unable to update the record due to an edit conflict, please try again"
    app.errorResponse(w, r, http.StatusConflict, message)
}


    // Intercept any ErrEditConflict error and call the new editConflictResponse()

    // helper.

    err = app.models.Movies.Update(movie)
    if err != nil {
        switch {
        case errors.Is(err, data.ErrEditConflict):
            app.editConflictResponse(w, r)
        default:
            app.serverErrorResponse(w, r, err)
        }
        return
    }

    err = app.writeJSON(w, http.StatusOK, envelope{"movie": movie}, nil)
    if err != nil {
        app.serverErrorResponse(w, r, err)
    }

If two goroutines are executing the code at the same time, the first update will succeed, and the second will fail because the version number in the database no longer matches the expected value.

We can see that the second update hasn’t been applied, the data race has been avoided, and the client has received a clear error response.

You can press Ctrl+C in your terminal to get back to your regular terminal prompt:

But in other applications this exact class of race condition can have much more serious consequences — such as when updating the stock level for a product in an online store, or updating the balance of an account.


Using an incrementing integer version number as the basis for an optimistic lock is safe and computationally cheap. I’d recommend using this approach unless you have a specific reason not to.

As an alternative, you could use a last_updated timestamp as the basis for the lock.But this is less safe — there’s the theoretical possibility that two clients could updatea record at exactly the same time, and using a timestamp also introduces the risk offurther problems if your server’s clock is wrong or becomes wrong over time.

If it’s important to you that the version identifier isn’t guessable, then a good option isto use a high-entropy random string such as a UUID in the version field.PostgreSQL has a UUID type and the uuid-ossp extension which you could use forthis purpose like so:

### 8.3. Managing SQL Query Timeouts

This feature can be useful when you have a SQL query that is taking longer to run than expected. When this happens, it suggests a problem — either with that particular query or your database or application more generally — and you probably want to cancel the query (in order to free up resources), log an error for further investigation, and return a 500 Internal Server Error response to the client.

Specifically, we’ll update our SQL query to return a pg_sleep(10) value, which will make PostgreSQL sleep for 10 seconds before returning its result.

the request hangs for 10 seconds before you finally get a successful response displaying the movie information.

Note: In the curl command above we’re using the -w flag to annotate the HTTP response with the total time taken for the command to complete. For more details about the timing information available in curl please see this excellent blog post.

Now that we’ve got some code that mimics a long-running query, let’s enforce a timeout so that the SQL query is automatically canceled if it doesn’t complete within 3 seconds.

To do this we need to:
1.  Use the context.WithTimeout() function to create a context.Context instance with a 3-second timeout deadline.
2.  Execute the SQL query using the QueryRowContext() method, passing the context.Context instance as a parameter.

Without it, the resources won’t bereleased until either the 3-second timeout is hit or the parent context (which in thisspecific example is context.Background()) is canceled.

The timeout countdown begins from the moment that the context is created usingcontext.WithTimeout(). Any time spent executing code between creating thecontext and calling QueryRowContext() will count towards the timeout.

OK, let’s try this out.

Inthat light it makes sense: our application is the user, and we’re purposefullycanceling the query after 3 seconds.

After 3 seconds, the context timeout is reached and our pq database driver sends a cancellation signal to PostgreSQL†. PostgreSQL then terminates the running query, the corresponding resources are freed-up, and it returns the error message that we see above. The client is then sent a 500 Internal Server Error response, and the error is logged so that we know something has gone wrong.

More precisely, our context (the one with the 3-second timeout) has a Done channel, and when the timeout is reached the Done channel will be closed. While the SQL query is running, our database driver pq is also running a background goroutine which listens on this Done channel. If the channel gets closed, then pq sends a cancellation signal to PostgreSQL.

There’s another important thing to point out here: it’s possible that the timeout deadline will be hit before the PostgreSQL query even starts.

In this scenario — or any other which causes a delay — it’s possible that the timeout deadline will be hit before a free database connection even becomes available. If this happens then QueryRowContext() will return a context.DeadlineExceeded error.

whereas the context deadline exceeded message relates to the queued SQL query being canceled before a free database connection even became available.

Let’s quickly update our database model to use a 3-second timeout deadline for all our operations. 

### 9. Filtering, Sorting, and Pagination

and then gradually making it more useful and usable by adding filtering, sorting, and pagination functionality.

•  Return the details of multiple resources in a single JSON response.
•  Accept and apply optional filter parameters to narrow down the returned data set.
•  Implement full-text search on your database fields using PostgreSQL’s inbuilt functionality.


•  Develop a pragmatic, reusable, pattern to support pagination on large data sets, and return pagination metadata in your JSON responses.

### 9.1. Parsing Query String Parameters

Over the next few chapters, we’re going configure the GET /v1/movies endpoint sothat a client can control which movie records are returned via query stringparameters. 

Note: In the sort parameter we will use the - character to denote descending sort order. So, for example, the parameter sort=title implies an ascending alphabetical sort on movie title, whereas sort=-title implies a descending sort.

how to parse these query string parameters in our Go code.

•  The page and page_size parameters will contain numbers, and we will want to convert these query string values into Go int types.


•  There are some validation checks that we’ll want to apply to the query string values, like making sure that page and page_size are not negative numbers

•  We want our application to set some sensible default values in case parameters like page, page_size and sort aren’t provided by the client.


// The readString() helper returns a string value from the query string, or the provided

// default value if no matching key could be found.

func (app *application) readString(qs url.Values, key string, defaultValue string) string {
    // Extract the value for a given key from the query string. If no key exists this

    // will return the empty string "". 

    s := qs.Get(key)

    // If no key exists (or the value is empty) then return the default value.

    if s == "" {
        return defaultValue
    }

    // Otherwise return the string.

    return s
}


    // Otherwise parse the value into a []string slice and return it.

    return strings.Split(csv, ",")


    // Try to convert the value to an int. If this fails, add an error message to the 

    // validator instance and return the default value.

    i, err := strconv.Atoi(s)
    if err != nil {
        v.AddError(key, "must be an integer value")
        return defaultValue
    }

    // Otherwise, return the converted integer value.

    return i


    // Call r.URL.Query() to get the url.Values map containing the query string data.

    qs := r.URL.Query()

    // Use our helpers to extract the title and genres query string values, falling back

    // to defaults of an empty string and an empty slice respectively if they are not 

    // provided by the client.

    input.Title = app.readString(qs, "title", "")
    input.Genres = app.readCSV(qs, "genres", []string{})


    // Get the page and page_size query string values as integers. Notice that we set 

    // the default page value to 1 and default page_size to 20, and that we pass the 

    // validator instance as the final argument here. 

    input.Page = app.readInt(qs, "page", 1, v)
    input.PageSize = app.readInt(qs, "page_size", 20, v)

Important: When using curl to send a request containing more than one query string parameter, you must wrap the URL in quotes for it to work correctly.

That’s looking good — we can see that the values provided in our query string have all been parsed correctly and are included in the input struct.


The page, page_size and sort query string parameters are things that you’ll potentially want to use on other endpoints in your API too. So, to help make this easier, let’s quickly split them out into a reusable Filters struct.


type Filters struct {
    Page     int
    PageSize int
    Sort     string
}

    // Embed the new Filters struct.

    var input struct {
        Title  string
        Genres []string
        data.Filters
    }


    // Read the page and page_size query string values into the embedded struct.

    input.Filters.Page = app.readInt(qs, "page", 1, v)
    input.Filters.PageSize = app.readInt(qs, "page_size", 20, v)

### 9.2. Validating Query String Parameters

But we still need to perform some additional sanity checks on the query string valuesprovided by the client. In particular, we want to check that:

To fix this, let’s open up the internal/data/filters.go file and create a newValidateFilters() function which conducts these checks on the values.

func ValidateFilters(v *validator.Validator, f Filters) {
    // Check that the page and page_size parameters contain sensible values.

    v.Check(f.Page > 0, "page", "must be greater than zero")
    v.Check(f.Page <= 10_000_000, "page", "must be a maximum of 10 million")
    v.Check(f.PageSize > 0, "page_size", "must be greater than zero")
    v.Check(f.PageSize <= 100, "page_size", "must be a maximum of 100")

    // Check that the sort parameter matches a value in the safelist.

    v.Check(validator.In(f.Sort, f.SortSafelist...), "sort", "invalid sort value")
}

    // Add the supported sort values for this endpoint to the sort safelist.

    input.Filters.SortSafelist = []string{"id", "title", "year", "runtime", "-id", "-title", "-year", "-runtime"}

Feel free to test this out more if you like, and try sending different query string values until you’re confident that the validation checks are all working correctly.

### 9.3. Listing Data

OK, let’s move on and get our GET /v1/movies endpoint returning some real data.

Our aim in this chapter will be to get the endpoint to return a JSON responsecontaining an array of all movies, similar to this:

To retrieve this data from our PostgreSQL database, let’s create a new GetAll()method on our database model which executes the following SQL query:


    // Importantly, defer a call to rows.Close() to ensure that the resultset is closed

    // before GetAll() returns.

    defer rows.Close()

    // Initialize an empty slice to hold the movie data.

    movies := []*Movie{}


        // Add the Movie struct to the slice.

        movies = append(movies, &movie)
    }

    // When the rows.Next() loop has finished, call rows.Err() to retrieve any error 

    // that was encountered during the iteration.

    if err = rows.Err(); err != nil {
        return nil, err
    }

    // If everything went OK, then return the slice of movies.

    return movies, nil

And now we should be ready to try this out.

### 9.4. Filtering Lists

In this chapter we’re going to start putting our query string parameters to use, so that clients can search for movies with a specific title or genres.

// List movies where the title is a case-insensitive exact match for 'black panther'.

/v1/movies?title=black+panther

// List movies where the genres includes 'adventure'.

/v1/movies?genres=adventure

Note: The + symbol in the query strings above is a URL-encoded space character. Alternatively you could use %! (MISSING)instead… either will work in the context of a query string.

The hardest part of building a dynamic filtering feature like this is the SQL query to retrieve the data — we need it to work with no filters, filters on both title and genres, or a filter on only one of them.

So this filter condition will essentially be ‘skipped’ when movie title being searched for is the empty string "".


The (genres @> $2 OR $2 = '{}') condition works in the same way. The @> symbol is the ‘contains’ operator for PostgreSQL arrays, and this condition will return true if all values in the placeholder parameter $2 are contained in the database genres field or the placeholder parameter contains an empty array.

Note: PostgreSQL also provides a range of other useful array operators andfunctions, including the && ‘overlap’ operator, the <@ ‘contained by’ operator,and the array_length() function. A complete list can be found here.

Our API endpoint is now returning the appropriately filtered movie records, and we have a pattern that we can easily extend to include other filtering rules in the future (such as a filter on the movie year or runtime) if we want to.

### 9.5. Full-Text Search

In this chapter we’re going to make our movie title filter easier to use by adapting it to support partial matches, rather than requiring a match on the full title. 

to leverage PostgreSQL’s full-text search functionality, which allows you to perform ‘natural language’ searches on text fields in your database.

PostgreSQL full-text search is a powerful and highly-configurable tool, and explaining how it works and the available options in full could easily take up a whole book in itself.

To implement a basic full-text search on our title field, we’re going to update our SQLquery to look like this:

That looks pretty complicated at first glance, so let’s break it down and explain what’s going on.

The to_tsvector('simple', title) function takes a movie title and splits it into lexemes. We specify the simple configuration, which means that the lexemes are just lowercase versions of the words in the title†. For example, the movie title "The Breakfast Club" would be split into the lexemes 'breakfast' 'club' 'the'.


As an example, the search value"The Club" would result in the query term 'the' & 'club'.

Adding indexes
To keep our SQL query performing quickly as the dataset grows, it’s sensible to use indexes to help avoid full table scans and avoid generating the lexemes for the title field every time the query is run.

Note: If you are not familiar with the concept of indexes in SQL databases, the official PostgreSQL documentation provides a good introduction and a summary of different index types. I recommend reading these quickly to give you an overview before continuing.

CREATE INDEX IF NOT EXISTS movies_title_idx ON movies USING GIN (to_tsvector('simple', title));
CREATE INDEX IF NOT EXISTS movies_genres_idx ON movies USING GIN (genres);

DROP INDEX IF EXISTS movies_title_idx;

You can retrieve a list of all available configurations by running the \dF meta-command in PostgreSQL:
postgres=# \dF
              List of text search configurations

Another option is the ILIKE operator, which allows you to find rows which match a specific (case-insensitive) pattern. 

### 9.6. Sorting Lists

Let’s now update the logic for our GET /v1/movies endpoint so that the client can controlhow the movies are sorted in the JSON response.

we want to let the client control the sort order via a querystring parameter in the format sort={-}{field_name}, where the optional - character isused to indicate a descending sort order. 

// Sort the movies on the title field in ascending alphabetical order.

/v1/movies?sort=title

// Sort the movies on the year field in descending numerical order.

/v1/movies?sort=-year

The difficulty here is that the values for the ORDER BY clause will need to be generated at runtime based on the query string values from the client.

If sorting is not chosen, the rows will be returned in an unspecified order. The actual order in that case will depend on the scan and join plan types and the order on disk, but it must not be relied on. A particular output ordering can only be guaranteed if the sort step is explicitly chosen.

We need to make sure that the order of movies is perfectly consistent between requests to prevent items in the list ‘jumping’ between the pages.

So, in our case, we can apply a secondary sort on the id column to ensure an always-consistent order. 

ORDER BY year DESC, id ASC


To get the dynamic sorting working, let’s begin by updating our Filters struct to includesome sortColumn() and sortDirection() helpers that transform a query string value(like -year) into values we can use in our SQL query.

// Return the sort direction ("ASC" or "DESC") depending on the prefix character of the

// Sort field.

func (f Filters) sortDirection() string {
    if strings.HasPrefix(f.Sort, "-") {
        return "DESC"
    }

    return "ASC"
}



Notice that the sortColumn() function is constructed in such a way that it will panic if theclient-provided Sort value doesn’t match one of the entries in our safelist. In theory thisshouldn’t happen — the Sort value should have already been checked by calling theValidateFilters() function — but this is a sensible failsafe to help stop a SQL injectionattack occurring.

Now let’s update our internal/data/movies.go file to call those methods, andinterpolate the return values into our SQL query’s ORDER BY clause. Like so:

### 9.7. Paginating Lists

you might want to implement some form of pagination on the endpoint — so that it only returns a subset of the records in a single HTTP response.

// Return the 5 records on page 1 (records 1-5 in the dataset)

/v1/movies?page=1&page_size=5

// Return the next 5 records on page 2 (records 6-10 in the dataset)

/v1/movies?page=2&page_size=5

Behind the scenes, the simplest way to support this style of pagination is by adding LIMIT and OFFSET clauses to our SQL query.

Let’s start by adding some helper methods to our Filters struct for calculating theappropriate LIMIT and OFFSET values.

func (f Filters) limit() int {
    return f.PageSize
}

func (f Filters) offset() int {
    return (f.Page - 1) * f.PageSize
}

Note: In the offset() method there is the theoretical risk of an integer overflow as we are multiplying two int values together. However, this is mitigated by the validation rules we created in our ValidateFilters() function, where we enforced maximum values of page_size=100 and page=10000000 (10 million). This means that the value returned by offset() should never come close to overflowing.

As the final stage in this process, we need to update our database model’s GetAll() method to add the appropriate LIMIT and OFFSET clauses to the SQL query.


    // As our SQL query now has quite a few placeholder parameters, let's collect the

    // values for the placeholders in a slice. Notice here how we call the limit() and

    // offset() methods on the Filters struct to get the appropriate values for the

    // LIMIT and OFFSET clauses.

    args := []interface{}{title, pq.Array(genres), filters.limit(), filters.offset()}



If you try to request the third page, you should get an empty JSON array in the response like so:

### 9.8. Returning Pagination Metadata

At this point the pagination on our GET /v1/movies endpoint is working nicely, but it would be even better if we could include some additional metadata along with the response. Information like the current and last page numbers, and the total number of available records would help to give the client context about the response and make navigating through the pages easier.

we’ll improve the response so that it includes additional pagination metadata, similar to this:
{
    "metadata": {
        "current_page": 1,
        "page_size": 20,
        "first_page": 1,
        "last_page": 42,
        "total_records": 832
    },

The challenging part of doing this is generating the total_records figure. We want this to reflect the total number of available records given the title and genres filters that are applied — not the absolute total of records in the movies table.

The inclusion of the count(*) OVER() expression at the start of the query will result in the filtered record count being included as the first value in each row.

2.  The window function count(*) OVER() is applied, which counts all the qualifying rows

3.  The ORDER BY rules are applied and the qualifying rows are sorted.
4.  The LIMIT and OFFSET rules are applied and the appropriate sub-set of sorted qualifying rows is returned.

 define a new Metadata struct to hold thepagination metadata, along with a helper to calculate the values. Like so:

// Define a new Metadata struct for holding the pagination metadata.

type Metadata struct {
    CurrentPage  int `json:"current_page,omitempty"`
    PageSize     int `json:"page_size,omitempty"`
    FirstPage    int `json:"first_page,omitempty"`
    LastPage     int `json:"last_page,omitempty"`
    TotalRecords int `json:"total_records,omitempty"`
}

return Metadata{
        CurrentPage:  page,
        PageSize:     pageSize,
        FirstPage:    1,
        LastPage:     int(math.Ceil(float64(totalRecords) / float64(pageSize))),
        TotalRecords: totalRecords,
    }

            &totalRecords, // Scan the count from the window function into totalRecords.

Finally, we need to update our listMoviesHandler handler to receive the Metadata struct returned by GetAll() and include the information in the JSON response for the client. Like so:

Lastly, if you make a request with a too-high page value, you should get a response with an empty metadata object and movies array, like this:

But the end result is really powerful. The client now has a lot of control over what their response contains, with filtering, pagination and sorting all supported.


### 10. Structured Logging and Error Handling

focus on making some improvements to the way that we handle exceptions and errors.

•  Create a custom logger for writing structured, levelled, log messages in JSON format.

•  Integrate the custom logger with your handlers and Go’s http.Server, so that all log messages are written to one location in a consistent format.

•  How to recover and gracefully handle panics in your application middleware and handlers.


### 10.1. Structured JSON Log Entries

For many applications this simple style of logging will be good enough, and there’s no need to introduce additional complexity to your project.


how to adapt our application so that log entries are written in a structured JSON format

Essentially, each log entry will be a single JSON object containing the following key/value pairs:

To implement this in our API, we’re going to create our own custom Logger type which writes structured log entries in JSON format.


We’ll implement some convenience methods on Logger, such as PrintInfo() and PrintError(), in order to make it easy to write log entries with a specific level from our application in a single command.

// Define a Level type to represent the severity level for a log entry.

type Level int8

// Initialize constants which represent a specific severity level. We use the iota

// keyword as a shortcut to assign successive integer values to the constants.

const (
    LevelInfo  Level = iota // Has the value 0.

    LevelError              // Has the value 1.

    LevelFatal              // Has the value 2.

    LevelOff                // Has the value 3.

)

// Return a human-friendly string for the severity level.

func (l Level) String() string {
    switch l {
    case LevelInfo:
        return "INFO"
    case LevelError:
        return "ERROR"
    case LevelFatal:
        return "FATAL"
    default:
        return ""
    }
}



// Define a custom Logger type. This holds the output destination that the log entries

// will be written to, the minimum severity level that log entries will be written for,

// plus a mutex for coordinating the writes.

type Logger struct {
    out      io.Writer
    minLevel Level
    mu       sync.Mutex
}

/ Return a new Logger instance which writes log entries at or above a minimum severity

// level to a specific output destination.

func New(out io.Writer, minLevel Level) *Logger {
    return &Logger{
        out:      out,
        minLevel: minLevel,
    }
}



// Declare some helper methods for writing log entries at the different levels. Notice

// that these all accept a map as the second parameter which can contain any arbitrary

// 'properties' that you want to appear in the log entry.

func (l *Logger) PrintInfo(message string, properties map[string]string) {
    l.print(LevelInfo, message, properties)
}

func (l *Logger) PrintFatal(err error, properties map[string]string) {
    l.print(LevelFatal, err.Error(), properties)
    os.Exit(1) // For entries at the FATAL level, we also terminate the application.

}

// Print is an internal method for writing the log entry.

func (l *Logger) print(level Level, message string, properties map[string]string) (int, error) {
    // If the severity level of the log entry is below the minimum severity for the

    // logger, then return with no further action.

    if level < l.minLevel {
        return 0, nil
    }


    // Include a stack trace for entries at the ERROR and FATAL levels.

    if level >= LevelError {
        aux.Trace = string(debug.Stack())
    }

    // Declare a line variable for holding the actual log entry text.

    var line []byte

    // Marshal the anonymous struct to JSON and store it in the line variable. If there

    // was a problem creating the JSON, set the contents of the log entry to be that

    // plain-text error message instead.

    line, err := json.Marshal(aux)
    if err != nil {
        line = []byte(LevelError.String() + ": unable to marshal log message:" + err.Error())
    }


    // Lock the mutex so that no two writes to the output destination cannot happen

    // concurrently. If we don't do this, it's possible that the text for two or more

    // log entries will be intermingled in the output.

    l.mu.Lock()
    defer l.mu.Unlock()

    // Write the log entry followed by a newline.

    return l.out.Write(append(line, '\n'))

// We also implement a Write() method on our Logger type so that it satisfies the 

// io.Writer interface. This writes a log entry at the ERROR level with no additional 

// properties.

func (l *Logger) Write(message []byte) (n int, err error) {
    return l.print(LevelError, string(message), nil)
}

The two most interesting things to point out in this code are our use of the iota enumerator to create the log levels and a sync.Mutex to coordinate the writes.

You can use iota to easily assign successive integer values so a set of integer constants. It starts at zero, and increments by 1 for every constant declaration, resetting to 0 when the word const appears in the code again. The way that we’ve used this means that each of our severity levels has an integer value, with the more severe levels having a higher value.

We’re also using a sync.Mutex (a mutual exclusion lock) to prevent our Logger instance making multiple writes concurrently. Without this mutex lock, it’s possible that the content of multiple log entries would be written at exactly the same time and be mixed up in the output, rather than each entry being written in full on its own line.


Important: How mutexes work, and how to use them, can be quite confusing if you haven’t encountered them before and it’s impossible to fully explain in a few short sentences. I’ve written a much more detailed article — Understanding Mutexes — which provides a proper explanation, and if you’re not already confident with mutexes I highly recommend reading this before you continue.

let’s update our cmd/api/main.go file to create a new Logger instance and then use it in our code. We’ll also add it to our application struct, so that it’s available as a dependency to all our handlers and helpers.

// Change the logger field to have the type *jsonlog.Logger, instead of

// *log.Logger.

type application struct {
    config config
    logger *jsonlog.Logger
    models data.Models
}




    // Initialize a new jsonlog.Logger which writes any messages *at or above* the INFO

    // severity level to the standard out stream.

    logger := jsonlog.New(os.Stdout, jsonlog.LevelInfo)

    // Likewise use the PrintInfo() method to write a message at the INFO level.

    logger.PrintInfo("database connection pool established", nil)




    // Again, we use the PrintInfo() method to write a "starting server" message at the

    // INFO level. But this time we pass a map containing additional properties (the

    // operating environment and server address) as the final parameter.

    logger.PrintInfo("starting server", map[string]string{
        "addr": srv.Addr,
        "env":  cfg.env,
    })

One of the nice things here is that it’s simple to include additional information as properties when logging the error.

    // Use the PrintError() method to log the error message, and include the current 

    // request method and URL as properties in the log entry.

    app.logger.PrintError(err, map[string]string{
        "request_method": r.Method,
        "request_url":    r.URL.String(),
    })

At this point, if you restart the application you should now see two INFO level log entries in JSON format like so:

Notice how in the second log entry the additional properties have been included in a nested JSON object?


This should result in a FATAL log entry and stack trace being printed, followed by the application immediately exiting. Similar to this:


Integration with the http.Server error log
It’s important to be aware that Go’s http.Server may also write its own log messages relating to things like unrecovered panics, or problems accepting and writing to HTTP connections.

By default, it writes these messages to the standard logger — which means they will be written to the standard error stream (instead of standard out like our other log messages), and they won’t be in our nice new JSON format.

Unfortunately, you can’t set http.Server to use our new Logger type directly. Instead, you will need to leverage the fact that our Logger satisfies the io.Writer interface (thanks to the Write() method that we added to it), and set http.Server to use a regular log.Logger instance from the standard library which writes to our own Logger as the target destination.


    // Initialize the custom logger.

    logger := jsonlog.New(os.Stdout, jsonlog.LevelInfo)


    srv := &http.Server{
        Addr:         fmt.Sprintf(":%!d(MISSING)", cfg.port),
        Handler:      app.routes(),
        // Create a new Go log.Logger instance with the log.New() function, passing in 

        // our custom Logger as the first parameter. The "" and 0 indicate that the

        // log.Logger instance should not use a prefix or any flags.

        ErrorLog:     log.New(logger, "", 0),
        IdleTimeout:  time.Minute,
        ReadTimeout:  10 * time.Second,
        WriteTimeout: 30 * time.Second,
    }

With that set up, any log messages that http.Server writes will be passed to ourLogger.Write() method, which in turn will output a log entry in JSON format at theERROR level.

Third-party logging packagesIf you don’t want to implement your own custom logger, like we have in this chapter, thenthere is a plethora of third-party packages available which implement structured loggingin JSON (and other) formats.

If I had to recommend one, it would be zerolog. It has a nice interface, and a good range of customization options. 

then it may be a sensible choice.

### 10.2. Panic Recovery

At the moment any panics in our API handlers will be recovered automatically by Go’s http.Server. This will unwind the stack for the affected goroutine (calling any deferred functions along the way), close the underlying HTTP connection, and log an error message and stack trace.

This behavior is OK, but it would be better for the client if we could also send a 500 Internal Server Error response to explain that something has gone wrong — rather than just closing the HTTP connection with no context.



        // Create a deferred function (which will always be run in the event of a panic

        // as Go unwinds the stack).

        defer func() {
            // Use the builtin recover function to check if there has been a panic or

            // not.

            if err := recover(); err != nil {
                // If there was a panic, set a "Connection: close" header on the 

                // response. This acts as a trigger to make Go's HTTP server 

                // automatically close the current connection after a response has been 

                // sent.

                w.Header().Set("Connection", "close")
                // The value returned by recover() has the type interface{}, so we use

                // fmt.Errorf() to normalize it into an error and call our 

                // serverErrorResponse() helper. In turn, this will log the error using

                // our custom Logger type at the ERROR level and send the client a 500

                // Internal Server Error response.

                app.serverErrorResponse(w, r, fmt.Errorf("%!s(MISSING)", err))
            }
        }()

        next.ServeHTTP(w, r)
    })

Once that’s done, we need to update our cmd/api/routes.go file so that the recoverPanic() middleware wraps our router. This will ensure that the middleware runs for every one of our API endpoints.



    // Wrap the router with the panic recovery middleware.

    return app.recoverPanic(router)

Now that’s in place, if there is a panic in one of our API handlers the recoverPanic() middleware will recover it and call our regular app.serverErrorResponse() helper. In turn, that will log the error using our custom Logger and send the client a nice 500 Internal Server Error response with a JSON body.

It’s really important to realize that our middleware will only recover panics that happen inthe same goroutine that executed the recoverPanic() middleware.

If, for example, you have a handler which spins up another goroutine (e.g. to do some background processing), then any panics that happen in the background goroutine will not be recovered — not by the recoverPanic() middleware… and not by the panic recovery built into http.Server. These panics will cause your application to exit and bring down the server.

So, if you are spinning up additional goroutines from within your handlers and there is any chance of a panic, you must make sure that you recover any panics from within those goroutines too.

### 11. Rate Limiting

If you’re building an API for public use, then it’s quite likely that you’ll want to implement some form of rate limiting to prevent clients from making too many requests too quickly, and putting excessive strain on your server.


Essentially, we want this middleware to check how many requests have been received in the last ‘N’ seconds and — if there have been too many — then it should send the client a 429 Too Many Requests response. We’ll position this middleware before our main application handlers, so that it carries out this check before we do any expensive processing like decoding a JSON request body or querying our database.

•  How to create middleware to rate-limit requests to your API endpoints, first by making a single rate global limiter, then extending it to support per-client limiting based on IP address.

•  How to make rate limiter behavior configurable at runtime, including disabling the rate limiter altogether for testing purposes.


### 11.1. Global Rate Limiting

Let’s build things up slowly and start by creating a single global rate limiter for our application. This will consider all the requests that our API receives (rather than having separate rate limiters for every individual client).

Instead of writing our own rate-limiting logic from scratch, which would be quite complex and time-consuming, we can leverage the x/time/rate package to help us here. This provides a tried-and-tested implementation of a token bucket rate limiter.

A Limiter controls how frequently events are allowed to happen. It implements a “token bucket” of size b, initially full and refilled at rate r tokens per second.

•  We will have a bucket that starts with b tokens in it.
•  Each time we receive a HTTP request, we will remove one token from the bucket.
•  Every 1/r seconds, a token is added back to the bucket — up to a maximum of b total tokens.
•  If we receive a HTTP request and the bucket is empty, then we should return a 429 Too Many Requests response.

In practice this means that our application would allow a maximum ‘burst’ of b HTTP requests in quick succession, but over time it would allow an average of r requests per second.

// Note that the Limit type is an 'alias' for float64.

func NewLimiter(r Limit, b int) *Limiter
So if we want to create a rate limiter which allows an average of 2 requests per second, with a maximum of 4 requests in a single ‘burst’, we could do so with the following code:
// Allow 2 requests per second, with a maximum of 4 requests in a burst. 

limiter := rate.NewLimiter(2, 4)

Once of the nice things about the middleware pattern that we are using is that it is straightforward to include ‘initialization’ code which only runs once when we wrap something with the middleware, rather than running on every request that the middleware handles.
func (app *application) exampleMiddleware(next http.Handler) http.Handler {
    
    // Any code here will run only once, when we wrap something with the middleware. 


    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        
        // Any code here will run for every request that the middleware handles.


        next.ServeHTTP(w, r)
    })


    limiter := rate.NewLimiter(2, 4)

    // The function we are returning is a closure, which 'closes over' the limiter 

    // variable.

    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        // Call limiter.Allow() to see if the request is permitted, and if it's not, 

        // then we call the rateLimitExceededResponse() helper to return a 429 Too Many

        // Requests response (we will create this helper in a minute).

        if !limiter.Allow() {
            app.rateLimitExceededResponse(w, r)
            return
        }

In this code, whenever we call the Allow() method on the rate limiter exactly one token will be consumed from the bucket. If there are no tokens left in the bucket, then Allow() will return false and that acts as the trigger for us send the client a 429 Too Many Requests response.

It’s also important to note that the code behind the Allow() method is protected by a mutex and is safe for concurrent use.

func (app *application) rateLimitExceededResponse(w http.ResponseWriter, r *http.Request) {
    message := "rate limit exceeded"
    app.errorResponse(w, r, http.StatusTooManyRequests, message)
}

 This should come after our panic recovery middleware (so thatany panics in rateLimit() are recovered), but otherwise we want it to be used as earlyas possible to prevent unnecessary work for our server.


    // Wrap the router with the rateLimit() middleware.

    return app.recoverPanic(app.rateLimit(router))

But once those 4 requests were used up, the tokens in the bucket ran out and our API began to return the "rate limit exceeded" error response instead.

### 11.2. IP-based Rate Limiting

Using a global rate limiter can be useful when you want to enforce a strict limit on the total rate of requests to your API, and you don’t care where the requests are coming from. But it’s generally more common to want a separate rate limiter for each client, so that one bad client making too many requests doesn’t affect all the others.

A conceptually straightforward way to implement this is to create an in-memory map of rate limiters, using the IP address for each client as the map key.

Each time a new client makes a request to our API, we will initialize a new rate limiter and add it to the map. For any subsequent requests, we will retrieve the client’s rate limiter from the map and check whether the request is permitted by calling its Allow() method, just like we did before.


Because we’ll potentially have multiple goroutines accessing the map concurrently, we’ll need to protect access to the map by using a mutex to prevent race conditions.


    // Declare a mutex and a map to hold the clients' IP addresses and rate limiters.

    var (
        mu      sync.Mutex
        clients = make(map[string]*rate.Limiter)
    )

        // Extract the client's IP address from the request.

        ip, _, err := net.SplitHostPort(r.RemoteAddr)


        // Lock the mutex to prevent this code from being executed concurrently.

        mu.Lock()

        // Check to see if the IP address already exists in the map. If it doesn't, then

        // initialize a new rate limiter and add the IP address and limiter to the map.

        if _, found := clients[ip]; !found {
            clients[ip] = rate.NewLimiter(2, 4)
        }


        // Call the Allow() method on the rate limiter for the current IP address. If

        // the request isn't allowed, unlock the mutex and send a 429 Too Many Requests

        // response, just like before.

        if !clients[ip].Allow() {
            mu.Unlock()
            app.rateLimitExceededResponse(w, r)
            return
        }


        // Very importantly, unlock the mutex before calling the next handler in the

        // chain. Notice that we DON'T use defer to unlock the mutex, as that would mean

        // that the mutex isn't unlocked until all the handlers downstream of this 

        // middleware have also returned.

        mu.Unlock()

        next.ServeHTTP(w, r)

The code above will work, but there’s a slight problem — the clients map will grow indefinitely, taking up more and more resources with every new IP address and rate limiter that we add.

To prevent this, let’s update our code so that we also record the last seen time for each client. We can then run a background goroutine in which we periodically delete any clients that we haven’t been seen recently from the clients map.


To make this work, we’ll need to create a custom client struct which holds both the rate limiter and last seen time for each client, and launch the background cleanup goroutine when initializing the middleware.

    // Define a client struct to hold the rate limiter and last seen time for each

    // client.

    type client struct {
        limiter  *rate.Limiter
        lastSeen time.Time
    }


    // Launch a background goroutine which removes old entries from the clients map once

    // every minute.

    go func() {
        for {
            time.Sleep(time.Minute)

            // Lock the mutex to prevent any rate limiter checks from happening while

            // the cleanup is taking place.

            mu.Lock()

            // Loop through all clients. If they haven't been seen within the last three

            // minutes, delete the corresponding entry from the map.

            for ip, client := range clients {
                if time.Since(client.lastSeen) > 3*time.Minute {
                    delete(clients, ip)
                }
            }

            // Importantly, unlock the mutex when the cleanup is complete.

            mu.Unlock()
        }
    }()


        // Update the last seen time for the client.

        clients[ip].lastSeen = time.Now()



Distributed applications
Using this pattern for rate-limiting will only work if your API application is running on a single-machine. If your infrastructure is distributed, with your application running on multiple servers behind a load balancer, then you’ll need to use an alternative approach.

If you’re using HAProxy or Nginx as a load balancer or reverse proxy, both of these have built-in functionality for rate limiting that it would probably be sensible to use. Alternatively, you could use a fast database like Redis to maintain a request count for clients, running on a server which all your application servers can communicate with.


### 11.3. Configuring the Rate Limiters

At the moment our requests-per-second and burst values are hard-coded into therateLimit() middleware.

This is OK, but it would be more flexible if they were configurable at runtime instead. Likewise, it would be useful to have an easy way to turn off rate limiting altogether (which is useful when you want to run benchmarks or carry out load testing, when all requests might be coming from a small number of IP addresses).

To make these things configurable, let’s head back to our cmd/api/main.go file and update the config struct and command-line flags like so:


    // Add a new limiter struct containing fields for the requests-per-second and burst

    // values, and a boolean field which we can use to enable/disable rate limiting

    // altogether.

    limiter struct {
        rps     float64
        burst   int
        enabled bool
    }


    // Create command line flags to read the setting values into the config struct. 

    // Notice that we use true as the default for the 'enabled' setting?

    flag.Float64Var(&cfg.limiter.rps, "limiter-rps", 2, "Rate limiter maximum requests per second")
    flag.IntVar(&cfg.limiter.burst, "limiter-burst", 4, "Rate limiter maximum burst")
    flag.BoolVar(&cfg.limiter.enabled, "limiter-enabled", true, "Enable rate limiter")

And then let’s update our rateLimit() middleware to use these settings, like so:

       // Only carry out the check if rate limiting is enabled.

        if app.config.limiter.enabled {

// Use the requests-per-second and burst values from the config

                    // struct.

                    limiter: rate.NewLimiter(rate.Limit(app.config.limiter.rps), app.config.limiter.burst),

If you issue a batch of six requests in quick succession again, you should now find that only the first two succeed:
$ for i in {1..6}; do curl  http://localhost:4000/v1/healthcheck; done

And you should find that all requests now complete successfully, no matter how many you make.

### 12. Graceful Shutdown

we’re going to talk about an important but often overlooked topic: how to safely stop your running application.
At the moment, when we stop our API application (usually by pressing Ctrl+C) it is terminated immediately with no opportunity for in-flight HTTP requests to complete.

This isn’t ideal for two reasons:
•  It means that clients won’t receive responses to their in-flight requests — all they will experience is a hard closure of the HTTP connection.
•  Any work being carried out by our handlers may be left in an incomplete state.

We’re going to mitigate these problems by adding graceful shutdown functionality to our application, so that in-flight HTTP requests have the opportunity to finish being processed before the application is terminated.


You’ll learn about:
•  Shutdown signals — what they are, how to send them, and how to listen for them in your API application.
•  How to use these signals to trigger a graceful shutdown of the HTTP server using Go’s Shutdown() method.

### 12.1. Sending Shutdown Signals

When our application is running, we can terminate it at any time by sending it a specific signal. A common way to do this, which you’ve probably been using, is by pressing Ctrl+C on your keyboard to send an interrupt signal — also known as a SIGINT.

But this isn’t the only type of signal which can stop our application. Some of the other common ones are:
￼

Catachable signals can be intercepted by our application and either ignored, or used to trigger a certain action (such as a graceful shutdown). Other signals, like SIGKILL, are not catchable and cannot be intercepted.

Let’s take a quick look at these signals in action. 

Doing this should start a process with the name api on your machine. You can use the pgrep command to verify that this process exists, like so:
$ pgrep -l api
4414 api

In my case, I can see that the api process is running and has the process ID 4414 (your process ID will most likely be different).

Feel free to repeat the same process, but sending a SIGTERM signal instead:
$ pkill -SIGTERM api


This time you should see the line signal: terminated at the end of the output, like so:

You can also try sending a SIGQUIT signal — either by pressing Ctrl+\ on your keyboard or running pkill -SIGQUIT api. This will cause the application to exit with a stack dump, similar to this:

We can see that these signals are effective in terminating our application — but the problem we have is that they all cause our application to exit immediately.

Fortunately, Go provides tools in the os/signals package that we can use to intercept catchable signals and trigger a graceful shutdown of our application. We’ll look at how to do that in the next chapter.

### 12.2. Intercepting Shutdown Signals

let’s move the code related to our http.Server out of the main() function and into a separate file. This will give us a cleaner, clearer, starting point from which we can build up the graceful shutdown functionality.

And then add a new app.serve() method which initializes and starts our http.Server, like so:

With that in place, we can simplify our main() function to use this new app.serve() method like so:

// Call app.serve() to start the server.

    err = app.serve()

The next thing that we want to do is update our application so that it ‘catches’ any SIGINT and SIGTERM signals. As we mentioned above, SIGKILL signals are not catchable (and will always cause the application to terminate immediately), and we’ll leave SIGQUIT with its default behavior (as it’s handy if you want to execute a non-graceful shutdown via a keyboard shortcut).

To catch the signals, we’ll need to spin up a background goroutine which runs for the lifetime of our application. In this background goroutine, we can use the signal.Notify() function to listen for specific signals and relay them to a channel for further processing.



    // Start a background goroutine.

    go func() {
        // Create a quit channel which carries os.Signal values.

        quit := make(chan os.Signal, 1)

        // Use signal.Notify() to listen for incoming SIGINT and SIGTERM signals and 

        // relay them to the quit channel. Any other signals will not be caught by

        // signal.Notify() and will retain their default behavior.

        signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)


        // Read the signal from the quit channel. This code will block until a signal is

        // received.

        s := <-quit

        // Log a message to say that the signal has been caught. Notice that we also

        // call the String() method on the signal to get the signal name and include it

        // in the log entry properties.

        app.logger.PrintInfo("caught signal", map[string]string{
            "signal": s.String(),
        })

        // Exit the application with a 0 (success) status code.

        os.Exit(0)
    }()

after intercepting the signal, all we do is log a message and then exit our application. But the important thing is that it demonstrates the pattern of how to catch specific signals and handle them in your code.

our quit channel is a buffered channel with size 1.
We need to use a buffered channel here because signal.Notify() does not wait for a receiver to be available when sending a signal to the quit channel. If we had used a regular (non-buffered) channel here instead, a signal could be ‘missed’ if our quit channel is not ready to receive at the exact moment that the signal is sent. By using a buffered channel, we avoid this problem and ensure that we never miss a signal.

OK, let’s try this out.


First, run the application and then press Ctrl+C on your keyboard to send a SIGINT signal. You should see a "caught signal" log entry with "signal":"interrupt" in the properties, similar to this:

You can also restart the application and try sending a SIGTERM signal. This time the log entry properties should contain "signal":"terminated"

In contrast, sending a SIGKILL or SIGQUIT signal will continue to cause the application to exit immediately without the signal being caught, so you shouldn’t see a "caught signal" message in the logs. For example, if you restart the application and issue a SIGKILL…
$ pkill -SIGKILL api

The application should be terminated immediately, and the logs will look like this:

### 12.3. Executing the Shutdown

Intercepting the signals is all well and good, but it’s not very useful until we do something with them! In this chapter, we’re going to update our application so that the SIGINT and SIGTERM signals we intercept trigger a graceful shutdown of our API.


Specifically, after receiving one of these signals we will call the Shutdown() method on our HTTP server.

The official documentation describes this as follows:
Shutdown gracefully shuts down the server without interrupting any active connections. Shutdown works by first closing all open listeners, then closing all idle connections, and then waiting indefinitely for connections to return to idle and then shut down.


    // Create a shutdownError channel. We will use this to receive any errors returned

    // by the graceful Shutdown() function.

    shutdownError := make(chan error)


        // Update the log entry to say "shutting down server" instead of "caught signal".

        app.logger.PrintInfo("shutting down server", map[string]string{
            "signal": s.String(),
        })


        // Create a context with a 5-second timeout.

        ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
        defer cancel()


        // Call Shutdown() on our server, passing in the context we just made.

        // Shutdown() will return nil if the graceful shutdown was successful, or an

        // error (which may happen because of a problem closing the listeners, or 

        // because the shutdown didn't complete before the 5-second context deadline is

        // hit). We relay this return value to the shutdownError channel.

         shutdownError <- srv.Shutdown(ctx)

    // Calling Shutdown() on our server will cause ListenAndServe() to immediately 

    // return a http.ErrServerClosed error. So if we see this error, it is actually a

    // good thing and an indication that the graceful shutdown has started. So we check 

    // specifically for this, only returning the error if it is NOT http.ErrServerClosed. 

    err := srv.ListenAndServe()
    if !errors.Is(err, http.ErrServerClosed) {
        return err
    }


    // Otherwise, we wait to receive the return value from Shutdown() on the  

    // shutdownError channel. If return value is an error, we know that there was a

    // problem with the graceful shutdown and we return the error.

    err = <-shutdownError
    if err != nil {
        return err
    }


    // At this point we know that the graceful shutdown completed successfully and we 

    // log a "stopped server" message.

    app.logger.PrintInfo("stopped server", map[string]string{
        "addr": srv.Addr,
    })

At first glance this code might seem a bit complex, but at a high-level what it’s doing can be summarized very simply: when we receive a SIGINT or SIGTERM signal, we instruct our server to stop accepting any new HTTP requests, and give any in-flight requests a ‘grace period’ of 5 seconds to complete before the application is terminated.

•  The Shutdown() method does not wait for any background tasks to complete, nor does it close hijacked long-lived connections like WebSockets. Instead, you will need to implement your own logic to coordinate a graceful shutdown of these things. We’ll look at some techniques for doing this later in the book.

•  There is an open bug which effectively limits the grace period to 5 seconds for HTTP/2 connections. HTTP/2 is automatically supported for HTTPS connections in Go, so if you’re planning to handle HTTPS connections from your Go application then (for now) it is sensible to use a sub 5-second grace period for your context timeout. If it wasn’t for this bug, I would suggest using a longer grace period in this chapter (around 20 seconds).

you can add a 4 second sleep delay to the healthcheckHandler method


    // Add a 4 second delay.

    time.Sleep(4 * time.Second)

Then start the API, and in another terminal window issue a request to the healthcheck endpoint followed by a SIGTERM signal.

In the logs for the server, you should immediately see a "shutting down server" message following the SIGTERM signal, similar to this:


Then after a 4 second delay for the in-flight request to complete, our healthcheckHandler should return the JSON response as normal and you should see that our API has logged a final "stopped server" message before exiting cleanly:


So this is working really well now. Any time that we want to gracefully shutdown our application, we can do so by sending a SIGINT (Ctrl+C) or SIGTERM signal. So long as no in-flight requests take more than 5 seconds to complete, our handlers will have time to complete their work and our clients will receive a proper HTTP response. 

And if we ever want to exit immediately, without a graceful shutdown, we can still do so by sending a SIGQUIT (Ctrl+\) or SIGKILL signal instead.

### 13. User Model Setup and Registration

we’re going to shift our focus towards users: registering them, activating them, authenticating them, and restricting access to our API endpoints depending on the permissions that they have.

•  Create a new users table in PostgreSQL for storing our user data.
•  Create a UserModel which contains the code for interacting with our users table, validating user data, and hashing user passwords.

•  Develop a POST /v1/users endpoint which can be used to register new users in our application.

### 13.1. Setting up the Users Database Table

Let’s begin by creating a new users table in our database. If you’re following along, use the migrate tool to generate a new pair of SQL migration files:

1.  The email column has the type citext (case-insensitive text). This type stores text data exactly as it is inputted — without changing the case in any way — but comparisons against the data are always case-insensitive… including lookups on associated indexes.

2.  We’ve also got a UNIQUE constraint on the email column. Combined with the citext type, this means that no two rows in the database can have the same email value — even if they have different cases. This essentially enforces a database-level business rule that no two users should exist with the same email address.

type bytea (binary string). In this column we’ll store a one-way hash of the user’s password generated using bcrypt — not the plaintext password itself.

4.  The activated column stores a boolean value to denote whether a user account is ‘active’ or not. We will set this to false by default when creating a new user, and require the user to confirm their email address before we set it to true.

5.  We’ve also included a version number column, which we will increment each time a user record is updated. This will allow us to use optimistic locking to prevent race conditions when updating user records, in the same way that we did with movies earlier in the book.

One important thing to point out here: the UNIQUE constraint on our email column has automatically been assigned the name users_email_key. This will become relevant in the next chapter, when we need to handle any errors caused by a user registering twice with the same email address.

### 13.2. Setting up the Users Model

Now that our database table is set up, we’re going to update our internal/data package to contain a new User struct (to represent the data for an individual user), and create a UserModel type (which we will use to perform various SQL queries against our users table).


Let’s start by defining the User struct, along with some helper methods for setting and verifying the password for a user.

As we mentioned earlier, in this project we will use bcrypt to hash user passwords before storing them in the database.

install the golang.org/x/crypto/bcrypt package, which provides an easy-to-use Go implementation of the bcrypt algorithm.

// Define a User struct to represent an individual user. Importantly, notice how we are 

// using the json:"-" struct tag to prevent the Password and Version fields appearing in

// any output when we encode it to JSON. Also notice that the Password field uses the

// custom password type defined below.

type User struct {
    ID        int64     `json:"id"`
    CreatedAt time.Time `json:"created_at"`
    Name      string    `json:"name"`
    Email     string    `json:"email"`
    Password  password  `json:"-"`
    Activated bool      `json:"activated"`
    Version   int       `json:"-"`
}

// Create a custom password type which is a struct containing the plaintext and hashed 

// versions of the password for a user. The plaintext field is a *pointer* to a string,

// so that we're able to distinguish between a plaintext password not being present in 

// the struct at all, versus a plaintext password which is the empty string "".

type password struct {
    plaintext *string
    hash      []byte
}

/ The Set() method calculates the bcrypt hash of a plaintext password, and stores both 

// the hash and the plaintext versions in the struct.

func (p *password) Set(plaintextPassword string) error {
    hash, err := bcrypt.GenerateFromPassword([]byte(plaintextPassword), 12)
    if err != nil {
        return err
    }

    p.plaintext = &plaintextPassword
    p.hash = hash

    return nil
}

// The Matches() method checks whether the provided plaintext password matches the 

// hashed password stored in the struct, returning true if it matches and false 

// otherwise.

func (p *password) Matches(plaintextPassword string) (bool, error) {
    err := bcrypt.CompareHashAndPassword(p.hash, []byte(plaintextPassword))

•  The bcrypt.GenerateFromPassword() function generates a bcrypt hash of a password using a specific cost parameter (in the code above, we use a cost of 12). The higher the cost, the slower and more computationally expensive it is to generate the hash.

This function returns a hash string in the format:
$2b$[cost]$[22-character salt][31-character hash]


•  The bcrypt.CompareHashAndPassword() function works by re-hashing the provided password using the same salt and cost parameter that is in the hash string that we’re comparing against. 

Let’s move on and create some validation checks for our User struct. Specifically, we want to:


•  Check that the Name field is not the empty string, and the value is less than 500 bytes long.

•  Check that the Email field is not the empty string, and that it matches the regular expression for email addresses that we added in our validator package earlier in the book.

•  If the Password.plaintext field is not nil, then check that the value is not the empty string and is between 8 and 72 bytes long.
•  Check that the Password.hash field is never nil.


Note: When creating a bcrypt hash the input is truncated to a maximum of 72 bytes. So, if someone uses a very long password, it means that any bytes after that would effectively be ignored when creating the hash. To avoid any confusion for users, we’ll simply enforce a hard maximum length of 72 bytes on the password in our validation checks. 

func ValidateUser(v *validator.Validator, user *User) {
    v.Check(user.Name != "", "name", "must be provided")
    v.Check(len(user.Name) <= 500, "name", "must not be more than 500 bytes long")

    // Call the standalone ValidateEmail() helper.

    ValidateEmail(v, user.Email)

    // If the plaintext password is not nil, call the standalone 

    // ValidatePasswordPlaintext() helper.

    if user.Password.plaintext != nil {
        ValidatePasswordPlaintext(v, *user.Password.plaintext)
    }

The next step in this process is setting up a UserModel type which isolates the database interactions with our PostgreSQL users table.

•  GetByEmail() to retrieve the data for a user with a specific email address.
•  Update() to change the data for a specific user.

// Define a custom ErrDuplicateEmail error.

var (
    ErrDuplicateEmail = errors.New("duplicate email")
)



// Create a UserModel struct which wraps the connection pool.

type UserModel struct {
    DB *sql.DB
}

        case err.Error() == `pq: duplicate key value violates unique constraint "users_email_key"`:
            return ErrDuplicateEmail

The only difference is that in some of the methods we’re specifically checking for any errors due to a violation of our unique users_email_key constraint. As we’ll see in the next chapter, by treating this as a special case we’ll be able to respond to clients with a message to say that “this email address is already in use”, rather than sending them a 500 Internal Server Error response like we normally would.


To finish all this off, the final thing we need to do is update our internal/data/models.go file to include the new UserModel in our parent Models struct. Like so:
 File: internal/data/models.go
package data

...

type Models struct {
    Movies MovieModel
    Users  UserModel // Add a new Users field.

}

func NewModels(db *sql.DB) Models {
    return Models{
        Movies: MovieModel{DB: db},
        Users:  UserModel{DB: db}, // Initialize a new UserModel instance.

    }
}

### 13.3. Registering a User

manage the process of registering (or signing up) a new user.

When a client calls this new POST /v1/users endpoint, we will expect them to provide the following details for the new user in a JSON request body. Similar to this:

When we receive this, the registerUserHandler should create a new User struct containing these details, validate it with the ValidateUser() helper, and then pass it to our UserModel.Insert() method to create a new database record.


    // Parse the request body into the anonymous struct.

    err := app.readJSON(w, r, &input)


    // Copy the data from the request body into a new User struct. Notice also that we

    // set the Activated field to false, which isn't strictly necessary because the 

    // Activated field will have the zero-value of false by default. But setting this 

    // explicitly helps to make our intentions clear to anyone reading the code.

    user := &data.User{
        Name:      input.Name,
        Email:     input.Email,
        Activated: false,
    }


    v := validator.New()

    // Validate the user struct and return the error messages to the client if any of 

    // the checks fail.

    if data.ValidateUser(v, user); !v.Valid() {
        app.failedValidationResponse(w, r, v.Errors)
        return
    }

Hint: If you’re following along, remember the password you used in the request above — you’ll need it later!

If you take a look at your PostgreSQL database, you should also see the new record in the users table. Similar to this:


greenlight=> SELECT * FROM users;

If you want, you can run the following query to append the regular string version to the table too: SELECT *, encode(password_hash, 'escape') FROM users.

OK, let’s try making another request to our API but with some invalid user details. This time our validation checks will kick in and the client should receive the relevant error messages.

This time you should get a validation error containing an “a user with this email address already exists” message

Because the email column in our database has the type citext, these alternative versions will be successfully identified as duplicates too.

•  Thanks to the specifications in RFC 2821, the domain part of an email address (username@domain) is case-insensitive. This means we can be confident that the real-life user behind alice@example.com is the same person as alice@EXAMPLE.COM.


•  The username part of an email address may or may not be case-sensitive — it depends on the email provider. Almost every major email provider treats the username as case-insensitive, but it is not absolutely guaranteed. All we can say here is that the real-life user behind the address alice@example.com is very probably (but not definitely) the same as ALICE@example.com.


However, because alice@example.com and ALICE@example.com are very probably the same user, we should generally treat email addresses as case-insensitive for comparison purposes.

In our registration workflow, using a case-insensitive comparison prevents users from accidentally (or intentionally) registering multiple accounts by just using different casing. 

It’s important to be aware that our registration endpoint is vulnerable to user enumeration. For example, if an attacker wants to know whether alice@example.com has an account with us, all they need to do is send a request like this:

The first, most obvious, risk relates to user privacy. For services that are sensitive or confidential you probably don’t want to make it obvious who has an account. The second risk is that it makes it easier for an attacker to compromise a user’s account.

Once they know a user’s email address, they can potentially:
•  Target the user with social engineering or another type of tailored attack.
•  Search for the email address in leaked password tables, and try those same passwords on our service.

1.  Making sure that the response sent to the client is always exactly the same, irrespective of whether a user exists or not. Generally, this means changing your response wording to be ambiguous, and notifying the user of any problems in a side-channel (such as sending them an email to inform them that they already have an account).

For all your regular users who are not attackers, they’re a negative from a UX point of view. You have to ask: is it worth the trade-off?


There are a few things to think about when answering this question. How important is user privacy in your application? 

### 14. Sending Emails

we’re going to inject some interactivity into our API, and adapt our registerUserHandler so that it sends the user a welcome email after they successfully register.


In the process of doing this we’re going to cover a few interesting topics.

•  How to use the Mailtrap SMTP service to send and monitor test emails during development.

•  How to use the html/template package and the new Go 1.16 embedded files functionality to create dynamic and easy-to-manage templates for your email content.

•  How to create a reusable internal/mailer package for sending emails from your application.
•  How to implement a pattern for sending emails in background goroutines, and how to wait for these to complete during a graceful shutdown.

### 14.1. SMTP Server Setup

In order to develop our email sending functionality, we’ll need access to a SMTP (Simple Mail Transfer Protocol) server that we can safely use for testing purposes.

There are a huge number of SMTP service providers (such as Postmark, Sendgrid or Amazon SES) that we could use to send our emails — or you can even install and run your own SMTP server. But in this book we’re going to use Mailtrap.


The reason for using Mailtrap is because it’s a specialist service for sending emails during development and testing. Essentially, it delivers all emails to an inbox that you can access, instead of sending them to the actual recipient.


I’ve got no affiliation with the company — I just find that the service works well and is simple to use. They also offer a ‘free forever’ plan, which should be sufficient for anyone who is coding-along with this book.


Note: If you already have your own SMTP server, or there is an alternative SMTP provider that you want to use instead, that’s absolutely fine. Feel free to skip ahead to the next chapter.

To setup a Mailtrap account, head to the signup page where you can register using either your email address or your Google or GitHub accounts (if you have them).

Once you’re registered and logged in, you should see a page listing your available inboxes, similar to the screenshot below.

You can change the name if you want by clicking the pencil icon under Actions.

If you go ahead and click through to that inbox, you should see that it’s currently empty and contains no emails, similar to this:

make a note of the credentials displayed on your screen (or just keep the browser tab open) — you’ll need them in the next chapter.

Note: The credentials in the screenshot above have been reset and are no longer valid, so please don’t try to use them!

### 14.2. Creating Email Templates

To start with, we’ll keep the content of the welcome email really simple, with a short message to let the user know that their registration was successful and confirmation of their ID number. Similar to this:

it’s a simple way for us to demonstrate how to include dynamic data in emails — rather than just static content.

There are several different approaches we could take to define and manage the content for this email, but a convenient and flexible way is to use Go’s templating functionality from the html/template package.

define three named templates to use as part of our welcome email:
•  A "subject" template containing the subject line for the email.
•  A "plainBody" template containing the plain-text variant of the email message body.
•  A "htmlBody" template containing the HTML variant of the email message body.

{define "subject"}}Welcome to Greenlight!{{end}}

•  We’ve defined the three named templates using the {{define "..."}}...{{end}} tags.

•  You can render dynamic data in these templates via the . character (referred to as dot). In the next chapter we’ll pass a User struct to the templates as dynamic data, which means that we can then render the user’s ID using the tag {{.ID}} in the templates.

Note: If you need to frequently change the text of emails or require them to be user-editable, then it might be appropriate to store these templates as strings in your database instead. But I’ve found that storing them in a file, like we are here, is a less complicated approach and a good starting point for most projects.


### 14.3. Sending a Welcome Email

To send emails we could use Go’s net/smtp package from the standard library. But unfortunately it’s been frozen for a few years, and doesn’t support some of the features that you might need in more advanced use-cases, such as the ability to add attachments.

So instead, I recommend using the third-party go-mail/mail package to help send email. It’s well tested, has good documentation, and a very clear and useable API.

Rather than writing all the code for sending the welcome email in our registerUserHandler, in this chapter we’re going to create a new internal/mailer package which wraps up the logic for parsing our email templates and sending emails.

we’re also going to use the new Go 1.16 embedded files functionality, so that the email template files will be built into our binary when we create it later. This is really nice because it means we won’t have to deploy these template files separately to our production server.


It’s probably easiest to demonstrate how this works by getting straight into the code and talking through the details as we go.

// Below we declare a new variable with the type embed.FS (embedded file system) to hold 

// our email templates. This has a comment directive in the format `//go:embed <path>`

// IMMEDIATELY ABOVE it, which indicates to Go that we want to store the contents of the

// ./templates directory in the templateFS embedded file system variable.

// ↓↓↓


//go:embed "templates"

var templateFS embed.FS

/ Define a Mailer struct which contains a mail.Dialer instance (used to connect to a

// SMTP server) and the sender information for your emails (the name and address you

// want the email to be from, such as "Alice Smith <alice@example.com>").

type Mailer struct {
    dialer *mail.Dialer
    sender string
}

    // Initialize a new mail.Dialer instance with the given SMTP server settings. We 

    // also configure this to use a 5-second timeout whenever we send an email.

    dialer := mail.NewDialer(host, port, username, password)
    dialer.Timeout = 5 * time.Second

    // Return a Mailer instance containing the dialer and sender information.

    return Mailer{
        dialer: dialer,
        sender: sender,
    }

// Use the ParseFS() method to parse the required template file from the embedded 

    // file system.

    tmpl, err := template.New("email").ParseFS(templateFS, "templates/"+templateFile)
    if err != nil {
        return err
    }


    // Follow the same pattern to execute the "plainBody" template and store the result

    // in the plainBody variable.

    plainBody := new(bytes.Buffer)
    err = tmpl.ExecuteTemplate(plainBody, "plainBody", data)
    if err != nil {
        return err
    }




    // Call the DialAndSend() method on the dialer, passing in the message to send. This

    // opens a connection to the SMTP server, sends the message, then closes the

    // connection. If there is a timeout, it will return a "dial tcp: i/o timeout"

    // error.

    err = m.dialer.DialAndSend(msg)
    if err != nil {
        return err
    }

Before we continue, let’s take a quick moment to discuss embedded file systems in more detail, because there are a couple of things that can be confusing when you first encounter them.


•  You can only use the //go:embed directive on global variables at package level, not within functions or methods. If you try to use it in a function or method, you’ll get the error "go:embed cannot apply to var inside func" at compile time.

•  When you use the directive //go:embed "<path>" to create an embedded file system, the path should be relative to the source code file containing the directive. 

•  If the path is to a directory, then all files in the directory are recursively embedded, except for files with names that begin with . or _. If you want to include these files you should use the * wildcard character in the path, like //go:embed "templates/*"

•  You can specify multiple directories and files in one directive. For example: //go:embed "images" "styles/css" "favicon.ico".

Now that our email helper package is in place, we need to hook it up to the rest of our code in the cmd/api/main.go file. 

•  Adapt our code to accept the configuration settings for the SMTP server as command-line flags.

// Update the config struct to hold the SMTP server settings.

type config struct {
    port int
    env  string
    db   struct {
        dsn          string
        maxOpenConns int
        maxIdleConns int
        maxIdleTime  string
    }
    limiter struct {
        enabled bool
        rps     float64
        burst   int
    }
    smtp struct {
        host     string
        port     int
        username string
        password string
        sender   string
    }
}

// Update the application struct to hold a new Mailer instance.

type application struct {
    config config
    logger *jsonlog.Logger
    models data.Models
    mailer mailer.Mailer
}


    // Read the SMTP server configuration settings into the config struct, using the

    // Mailtrap settings as the default values. IMPORTANT: If you're following along,

    // make sure to replace the default values for smtp-username and smtp-password

    // with your own Mailtrap credentials.

    flag.StringVar(&cfg.smtp.host, "smtp-host", "smtp.mailtrap.io", "SMTP host")
    flag.IntVar(&cfg.smtp.port, "smtp-port", 25, "SMTP port")
    flag.StringVar(&cfg.smtp.username, "smtp-username", "0abf276416b183", "SMTP username")


    // Initialize a new Mailer instance using the settings from the command line

    // flags, and add it to the application struct.

    app := &application{
        config: cfg,
        logger: logger,
        models: data.NewModels(db),
        mailer: mailer.New(cfg.smtp.host, cfg.smtp.port, cfg.smtp.username, cfg.smtp.password, cfg.smtp.sender),
    }

And then the final thing we need to do is update our registerUserHandler to actually send the email

If everything is set up correctly you should get a 201 Created response containing the new user details, similar to the response above.


That’s quite a long time for a HTTP request to complete, and we’ll speed it up in the next chapter by sending the welcome email in a background goroutine.

If you want, you can also click on the Text tab to see the plain-text version of the email, and the Raw tab to see the complete email including headers.

Retrying email send attempts

If you want, you can make the email sending process a bit more robust by adding some basic ‘retry’ functionality to the Mailer.Send() method. For example:

    // Try sending the email up to three times before aborting and returning the final 

    // error. We sleep for 500 milliseconds between each attempt.

    for i := 1; i <= 3; i++ {
        err = m.dialer.DialAndSend(msg)
        // If everything worked, return nil.

        if nil == err {
            return nil
        }

        // If it didn't work, sleep for a short time and retry.

        time.Sleep(500 * time.Millisecond)
    }

Hint: In the code above we’re using the clause if nil == err to check if the send was successful, rather than if err == nil . They’re functionally equivalent, but having nil as the first item in the clause makes it a bit visually jarring and less likely to be confused with the far more common if err != nil clause.

This retry functionality is a relatively simple addition to our code, but it helps toincrease the probability that emails are successfully sent in the event of transientnetwork issues. 

you might want to make the sleep duration even longer here, as itwon’t materially impact the client and gives more time for transient issues to resolve.

### 14.4. Sending Background Emails

adds quite a lot of latency to the total request/response round-trip for the client.
One way we could reduce this latency is by sending the email in a background goroutine. This would effectively ‘decouple’ the task of sending an email from the rest of the code in our registerUseHandler, and means that we could return a HTTP response to the client without waiting for the email sending to complete.

At its very simplest, we could adapt our handler to execute the email send in a background goroutine like this:


    // Launch a goroutine which runs an anonymous function that sends the welcome email.

    go func() {
        err = app.mailer.Send(user.Email, "user_welcome.tmpl", user)
        if err != nil {
            // Importantly, if there is an error sending the email then we use the 

            // app.logger.PrintError() helper to manage it, instead of the 

            // app.serverErrorResponse() helper like before.

            app.logger.PrintError(err, nil)
        }
    }()


    // Note that we also change this to send the client a 202 Accepted status code.

    // This status code indicates that the request has been accepted for processing, but 

    // the processing has not been completed.

    err = app.writeJSON(w, http.StatusAccepted, envelope{"user": user}, nil)

The code in this background goroutine will be executedconcurrently with the subsequent code in our registerUserHandler, which means weare no longer waiting for the email to be sent before we return a JSON response tothe client. Most likely, the background goroutine will still be executing its code longafter the registerUserHandler has returned.

•  The code running in the background goroutine forms a closure over the user and app variables. It’s important to be aware that these ‘closed over’ variables are not scoped to the background goroutine, which means that any changes you make to them will be reflected in the rest of your codebase.

In our case we aren’t changing the value of these variables in any way, so this behavior won’t cause us any issues. But it is important to keep in mind.

This time, you should see that the time taken to return the response is much faster — in my case 0.27 seconds compared to the previous 2.33 seconds.


It’s important to bear in mind that any panic which happens in this background goroutine will not be automatically recovered by our recoverPanic() middleware or Go’s http.Server, and will cause our whole application to terminate.

 So we need to make sure that any panic in this background goroutine is manually recovered, using a similar pattern to the one in our recoverPanic() middleware.



    // Launch a background goroutine to send the welcome email.

    go func() {
        // Run a deferred function which uses recover() to catch any panic, and log an

        // error message instead of terminating the application.

        defer func() {
            if err := recover(); err != nil {
                app.logger.PrintError(fmt.Errorf("%!s(MISSING)", err), nil)
            }
        }()

        // Send the welcome email.

        err = app.mailer.Send(user.Email, "user_welcome.tmpl", user)
        if err != nil {
            app.logger.PrintError(err, nil)
        }
    }()

If you need to execute a lot of background tasks in your application, it can get tedious to keep repeating the same panic recovery code — and there’s a risk that you might forget to include it altogether.
To help take care of this, it’s possible to create a simple helper function which wraps the panic recovery logic.

// The background() helper accepts an arbitrary function as a parameter.

func (app *application) background(fn func()) {
    // Launch a background goroutine.

    go func() {
        // Recover any panic.

        defer func() {
            if err := recover(); err != nil {
                app.logger.PrintError(fmt.Errorf("%!s(MISSING)", err), nil)
            }
        }()

        // Execute the arbitrary function that we passed as the parameter.

        fn()
    }()

This background() helper leverages the fact that Go has first-class functions, which means that functions can be assigned to variables and passed as parameters to other functions.

Let’s double-check that this is still working. 

If everything is set up correctly, you’ll now see the corresponding email appear in your Mailtrap inbox again.

### 14.5. Graceful Shutdown of Background Tasks

Sending our welcome email in the background is working well, but there’s still an issue we need to address.

When we initiate a graceful shutdown of our application, it won’t wait for any background goroutines that we’ve launched to complete. So — if we happen to shutdown our server at an unlucky moment — it’s possible that a new client will be created on our system but they will never be sent their welcome email.

Fortunately, we can prevent this by using Go’s sync.WaitGroup functionality to coordinate the graceful shutdown and our background goroutines.

When you want to wait for a collection of goroutines to finish their work, the principal tool to help with this is the sync.WaitGroup type.
The way that it works is conceptually a bit like a ‘counter’. Each time you launch a background goroutine you can increment the counter by 1, and when each goroutine finishes, you then decrement the counter by 1. You can then monitor the counter, and when it equals zero you know that all your background goroutines have finished.

Let’s take a quick look at a standalone example of how sync.WaitGroup works in practice.

// Defer a call to wg.Done() to indicate that the background goroutine has 

            // completed when this function returns. Behind the scenes this decrements 

            // the WaitGroup counter by 1 and is the same as writing wg.Add(-1).

            defer wg.Done()



// Wait() blocks until the WaitGroup counter is zero --- essentially blocking until all

    // goroutines have completed.

    wg.Wait()

If we called wg.Add(1) in the background goroutine itself, there is a race condition because wg.Wait() could potentially be called before the counter is even incremented.

Let’s update our application to incorporate a sync.WaitGroup that coordinates our graceful shutdown and background goroutines.

// Include a sync.WaitGroup in the application struct. The zero-value for a

// sync.WaitGroup type is a valid, useable, sync.WaitGroup with a 'counter' value of 0,

// so we don't need to do anything else to initialize it before we can use it.

type application struct {
    config config
    logger *jsonlog.Logger
    models data.Models
    mailer mailer.Mailer
    wg     sync.WaitGroup
}

Next let’s head to the cmd/api/helpers.go file and update the app.background()helper so that the sync.WaitGroup counter is incremented each time before we launcha background goroutine, and then decremented when it completes.

Then the final thing we need to do is update our graceful shutdown functionality so that it uses our new sync.WaitGroup to wait for any background goroutines before terminating the application. We can do that by adapting our app.serve() method like so:


        // Call Wait() to block until our WaitGroup counter is zero --- essentially

        // blocking until the background goroutines have finished. Then we return nil on

        // the shutdownError channel, to indicate that the shutdown completed without

        // any issues.

        app.wg.Wait()
        shutdownError <- nil

To try this out, go ahead and restart the API and then send a request to thePOST /v1/users endpoint immediately followed by a SIGTERM signal. For example:

Notice how the "completing background tasks" message is written, then there is apause of a couple of seconds while the background email sending completes,followed finally by the "stopped server" message?

This nicely illustrates how the graceful shutdown process waited for the welcome email to be sent (which took about two seconds in my case) before finally terminating the application.

### 15. User Activation

we’re going to build up the functionality to confirm that a user used their own, real, email address by including ‘account activation’ instructions in their welcome email.


helps prevent abuse by people who register with a fake email address or one that doesn’t belong to them.

To give you an overview upfront, the account activation process will work like this:
1.  As part of the registration process for a new user we will create a cryptographically-secure random activation token that is impossible to guess.

2.  We will then store a hash of this activation token in a new tokens table, alongside the new user’s ID and an expiry time for the token.


5.  If the hash of the token exists in the tokens table and hasn’t expired, then we’ll update the activated status for the relevant user to true.
6.  Lastly, we’ll delete the activation token from our tokens table so that it can’t be used again.

•  Implement a secure ‘account activation’ workflow which verifies a new user’s email address.

Generate fast hashes of data using the crypto/sha256 package.

•  Implement patterns for working with cross-table relationships in your database, including setting up foreign keys and retrieving related data via SQL JOIN queries.

### 15.1. Setting up the Tokens Database Table

Let’s begin by creating a new tokens table in our database to store the activationtokens for our users.

user_id bigint NOT NULL REFERENCES users ON DELETE CASCADE,

•  The hash column will contain a SHA-256 hash of the activation token. It’s important to emphasize that we will only store a hash of the activation token in our database — not the activation token itself.

We want to hash the token before storing it for the same reason that we bcrypt a user’s password — it provides an extra layer of protection if the database is ever compromised or leaked. 

it is sufficient to use a fast algorithm like SHA-256 to create the hash, instead of a slow algorithm like bcrypt.


•  The user_id column will contain the ID of the user associated with the token. We use the REFERENCES user syntax to create a foreign key constraint against the primary key of our users table, which ensures that any value in the user_id column has a corresponding id entry in our users table.


We also use the ON DELETE CASCADE syntax to instruct PostgreSQL to automatically delete all records for a user in our tokens table when the parent record in the users table is deleted.

If you use ON DELETE RESTRICT, you would need to manually delete any tokens for the user before you delete the user record itself.


•  The expiry column will contain the time that we consider a token to be ‘expired’ and no longer valid. Setting a short expiry time is good from a security point-of-view because it helps reduce the window of possibility for a successful brute-force attack against the token. And it also helps in the scenario where the user is sent a token but doesn’t use it, and their email account is compromised at a later time. By setting a short time limit, it reduces the time window that the compromised token could be used.

•  Lastly, the scope column will denote what purpose the token can be used for. Later in the book we’ll also need to create and store authentication tokens, and most of the code and storage requirements for these is exactly the same as for our activation tokens. So instead of creating separate tables (and the code to interact with them), we’ll store them in one table with a value in the scope column to restrict the purpose that the token can be used for.

### 15.2. Creating Secure Activation Tokens

In our case, we’ll create our activation tokens using Go’s crypto/rand package and 128-bits (16 bytes) of entropy.

Then in this file let’s define a Token struct (to represent the data for an individualtoken), and a generateToken() function that we can use to create a new token.

// Define constants for the token scope. For now we just define the scope "activation"

// but we'll add additional scopes later in the book.

const (
    ScopeActivation = "activation"
)

// Define a Token struct to hold the data for an individual token. This includes the 

// plaintext and hashed versions of the token, associated user ID, expiry time and 

// scope.

type Token struct {
    Plaintext string
    Hash      []byte
    UserID    int64
    Expiry    time.Time
    Scope     string
}

// Create a Token instance containing the user ID, expiry, and scope information.  

    // Notice that we add the provided ttl (time-to-live) duration parameter to the 

    // current time to get the expiry time?

    token := &Token{
        UserID: userID,
        Expiry: time.Now().Add(ttl),
        Scope:  scope,
    }

    // Initialize a zero-valued byte slice with a length of 16 bytes.

    randomBytes := make([]byte, 16)

    // Use the Read() function from the crypto/rand package to fill the byte slice with 

    // random bytes from your operating system's CSPRNG. This will return an error if 

    // the CSPRNG fails to function correctly.

    _, err := rand.Read(randomBytes)
    if err != nil {
        return nil, err
    }

// Encode the byte slice to a base-32-encoded string and assign it to the token 

    // Plaintext field. This will be the token string that we send to the user in their

    // welcome email. They will look similar to this:

    //

    // Y3QMGX3PJ3WLRL2YRTQGQ6KRHU

    // 

    // Note that by default base-32 strings may be padded at the end with the = 

    // character. We don't need this padding character for the purpose of our tokens, so 

    // we use the WithPadding(base32.NoPadding) method in the line below to omit them.

    token.Plaintext = base32.StdEncoding.WithPadding(base32.NoPadding).EncodeToString(randomBytes)


    // Generate a SHA-256 hash of the plaintext token string. This will be the value 

    // that we store in the `hash` field of our database table. Note that the 

    // sha256.Sum256() function returns an *array* of length 32, so to make it easier to  

    // work with we convert it to a slice using the [:] operator before storing it.

    hash := sha256.Sum256([]byte(token.Plaintext))
    token.Hash = hash[:]

The length of the plaintext token string itself depends on how those 16 random bytes are encoded to create a string. In our case we encode the random bytes to a base-32 string, which results in a string with 26 characters.

New() will be a shortcut method which creates a new token using thegenerateToken() function and then calls Insert() to store the data.

DeleteAllForUser() to delete all tokens with a specific scope for a specific user.

// The New() method is a shortcut which creates a new Token struct and then inserts the

// data in the tokens table.

func (m TokenModel) New(userID int64, ttl time.Duration, scope string) (*Token, error) {
    token, err := generateToken(userID, ttl, scope)
    if err != nil {
        return nil, err
    }

    err = m.Insert(token)
    return token, err
}

And finally, we need to update the internal/data/models.go file so that the new TokenModel is included in our parent Models struct. Like so:
 File: internal/data/models.go
package data

...

type Models struct {
    Movies MovieModel
    Tokens TokenModel // Add a new Tokens field.

    Users  UserModel
}

It’s important that you never use the math/rand package for any purpose where cryptographic security is required, such as generating tokens or secrets like we are here.

In fact, it’s arguably best to use crypto/rand as standard practice. Only opt for using math/rand in specific scenarios where you are certain that a deterministic PRNG is acceptable, and you actively need the faster performance of math/rand.

### 15.3. Sending Activation Tokens

The most important thing about this email is that we’re instructing the user to activate by issuing a PUT request to our API — not by clicking a link which contains the token as part of the URL path or query string.

Having a user click a link to activate via a GET request (which is used by default when clicking a link) would certainly be more convenient, but in the case of our API it has some big drawbacks. In particular:
•  It would violate the HTTP principle that the GET method should only be used for ‘safe’ requests which retrieve resources — not for requests that modify something (like a user’s activation status).

That could result in a scenario where a malicious actor (Eve) wants to make an account using someone else’s email (Alice). Eve signs up, and Alice received an email. Alice opens the email because she is curious about an account she didn’t request. Her browser (or antivirus) requests the URL in the background, inadvertently activating the account.


All-in-all, you should make sure that any actions which change the state of your application (including activating a user) are only ever executed via POST, PUT, PATCH or DELETE requests — not by GET requests.

Note: If your API is the back-end for a website, then you could adapt this email so that it asks the user to click a link which takes them to a page on your website. They can then click a button on the page to ‘confirm their activation’, which performs the PUT request to your API that actually activates the user. We’ll look at this pattern in more detail in the next chapter.

// As there are now multiple pieces of data that we want to pass to our email

        // templates, we create a map to act as a 'holding structure' for the data. This

        // contains the plaintext version of the activation token for the user, along 

        // with their ID.

        data := map[string]interface{}{
            "activationToken": token.Plaintext,
            "userID":          user.ID,
        }

        // Send the welcome email, passing in the map above as dynamic data.

        err = app.mailer.Send(user.Email, "user_welcome.tmpl", data)
        if err != nil {
            app.logger.PrintError(err, nil)
        }
    })

And if you open your Mailtrap inbox again, you should now see the new welcome email containing the activation token for faith@example.com, like so:
 

As we mentioned earlier, psql always displays values in bytea columns as a hex-encoded string. So what we’re seeing here is a hexadecimal encoding of the SHA-256 hash of the plaintext token P4B3URJZJ2NW5UPZC2OHN4H2NM that we sent in the welcome email.

Note: If you want, you can verify that this is correct by entering a plaintext activation token from the welcome email into this online SHA-256 hash generator. You should see that the result matches the hex-encoded value inside your PostgreSQL database.

Notice too that the user_id value of 7 is correct for our faith@example.com user, the expiry time has been correctly set to three days from now, and the token has the scope value activation?


### 15.4. Activating a User

What we have is known in relational database terms as a one-to-many relationship — where one user may have many tokens, but a token can only belong to one user.

•  Retrieve the user associated with a token.
•  Retrieve all tokens associated with a user.
To implement these queries in your code, a clean and clear approach is to update your database models to include some additional methods like this:
UserModel.GetForToken(token)   → Retrieve the user associated with a token
TokenModel.GetAllForUser(user) → Retrieve all tokens associated with a user


The nice thing about this approach is that the entities being returned align with the main responsibility of the models: the UserModel method is returning a user, and the TokenModel method is returning tokens.


And the workflow will look like this:
1.  The user submits the plaintext activation token (which they just received in their email) to the PUT /v1/users/activated endpoint.
2.  We validate the plaintext token to check that it matches the expected format, sending the client an error message if necessary.
3.  We then call the UserModel.GetForToken() method to retrieve the details of the user associated with the provided token. If there is no matching token found, or it has expired, we send the client an error message.

We activate the associated user by setting activated = true on the user record andupdate it in our database.

5.  We delete all activation tokens for the user from the tokens table. We can do this using the TokenModel.DeleteAllForUser() method that we made earlier.

  // Parse the plaintext activation token from the request body.

    var input struct {
        TokenPlaintext string `json:"token"`
    }

    err := app.readJSON(w, r, &input)


    // Validate the plaintext token provided by the client.

    v := validator.New()

    if data.ValidateTokenPlaintext(v, input.TokenPlaintext); !v.Valid() {
        app.failedValidationResponse(w, r, v.Errors)
        return
    }


    // Update the user's activation status.

    user.Activated = true

    // Save the updated user record in our database, checking for any edit conflicts in

    // the same way that we did for our movie records.

    err = app.models.Users.Update(user)
    if err != nil {
        switch {
        case errors.Is(err, data.ErrEditConflict):
            app.editConflictResponse(w, r)
        default:
            app.serverErrorResponse(w, r, err)
        }
        return
    }


    // If everything went successfully, then we delete all activation tokens for the

    // user.

    err = app.models.Tokens.DeleteAllForUser(data.ScopeActivation, user.ID)

If there is no matching token found, or it has expired, we want this to return a ErrRecordNotFound error instead.

This is more complicated than most of the SQL queries we’ve used so far, so let’s take a moment to explain what it is doing.
In this query we are using INNER JOIN to join together information from the users and tokens tables. Specifically, we’re using the ON users.id = tokens.user_id clause to indicate that we want to join records where the user id value equals the token user_id.

Behind the scenes, you can think of INNER JOIN as creating an ‘interim’ table containing the joined data from both tables. Then, in our SQL query, we use the WHERE clause to filter this interim table to leave only rows where the token hash and token scope match specific placeholder parameter values, and the token expiry is after a specific time.

// Calculate the SHA-256 hash of the plaintext token provided by the client.

    // Remember that this returns a byte *array* with length 32, not a slice.

    tokenHash := sha256.Sum256([]byte(tokenPlaintext))


    // Create a slice containing the query arguments. Notice how we use the [:] operator

    // to get a slice containing the token hash, rather than passing in the array (which

    // is not supported by the pq driver), and that we pass the current time as the

    // value to check against the token expiry.

    args := []interface{}{tokenHash[:], tokenScope, time.Now()}


    // Execute the query, scanning the return values into a User struct. If no matching

    // record is found we return an ErrRecordNotFound error.

    err := m.DB.QueryRowContext(ctx, query, args...).Scan(
        &user.ID,
        &user.CreatedAt,
        &user.Name,
        &user.Email,
        &user.Password.hash,
        &user.Activated,
        &user.Version,
    )
    if err != nil {
        switch {
        case errors.Is(err, sql.ErrNoRows):
            return nil, ErrRecordNotFound
        default:
            return nil, err
        }
    }

    // Return the matching user.

    return &user, nil

I should quickly explain that the reason we’re using PUT rather than POST for this endpoint is because it’s idempotent.
If a client sends the same PUT /v1/users/activated request multiple times, the first will succeed (assuming the token is valid) and then any subsequent requests will result in an error being sent to the client (because the token has been used and deleted from the database). But the important thing is that nothing in our application state (i.e. database) changes after that first request.

Basically, there are no application state side-effects from the client sending the same request multiple times, which means that the endpoint is idempotent and using PUT is more appropriate than POST.

And if you try repeating the request again with the same token, you should now get an "invalid or expired activation token" error due to the fact we have deleted all activation tokens for faith@example.com.


Important: In production, with activation tokens for real accounts, you must make sure that the tokens are only ever accepted over an encrypted HTTPS connection — not via regular HTTP like we are using here.

Lastly, let’s take a quick look in our database to see the state of our users table.

If your API is the backend to a website, rather than a completely standalone service, you can tweak the activation workflow to make it simpler and more intuitive for users while still being secure.


There are two main options here. The first, and most robust, option is to ask the user to copy-and-paste the token into a form on your website which then performs the PUT /v1/users/activate request for them using some JavaScript.

To activate your Greenlight account please visit h͟t͟t͟p͟s͟:͟/͟/͟e͟x͟a͟m͟p͟l͟e͟.͟c͟o͟m͟/͟u͟s͟e͟r͟s͟/͟a͟c͟t͟i͟v͟a͟t͟e͟  and 
enter the following code:

--------------------------
Y3QMGX3PJ3WLRL2YRTQGQ6KRHU
--------------------------

This approach is fundamentally simple and secure — effectively your website just provides a form that performs the PUT request for the user, rather than them needing to do it manually using curl or another tool.


The URL domain should be either be hard-coded or passed in as a command-line flag when starting the application.


Alternatively, if you don’t want the user to copy-and-paste a token, you could ask them to click a link containing the token which takes them to a page on your website. 

To activate your Greenlight account please click the following link:

h͟t͟t͟p͟s͟:͟/͟/͟e͟x͟a͟m͟p͟l͟e͟.͟c͟o͟m͟/͟u͟s͟e͟r͟s͟/͟a͟c͟t͟i͟v͟a͟t͟e͟?͟t͟o͟k͟e͟n͟=͟Y͟3͟Q͟M͟G͟X͟3͟P͟J͟3͟W͟L͟R͟L͟2͟Y͟R͟T͟Q͟G͟Q͟6͟K͟R͟H͟U͟

Please note that this link will expire in 3 days and can only be used once.


This page should then display a button that says something like ‘Confirm your account activation’, and some JavaScript on the webpage can extract the token from the URL and submit it to your PUT /v1/users/activate API endpoint when the user clicks the button.

If you go with this second option, you also need to take steps to avoid thetoken being leaked in a referrer header if the user navigates to a different site.You can use the Referrer-Policy: Origin header or<meta name="referrer" content="origin"> HTML tag to mitigate this,although you should be aware that it’s not supported by absolutely all webbrowsers (support is currently at ~96%!)(MISSING).

It’s worth pointing out that the SQL query we’re using in UserModel.GetForToken() is theoretically vulnerable to a timing attack, because PostgreSQL’s evaluation of the tokens.hash = $1 condition is not performed in constant-time.

in theory an attacker could issuethousands of requests to our PUT /v1/users/activated endpoint and analyze tinydiscrepancies in the average response time to build up a picture of a hashed activationtoken value in the database.

But, in our case, even if a timing attack was successful it would only leak the hashed token value from the database — not the plaintext token value that the user actually needs to submit to activate their account.

So the attacker would still need to use brute-force to find a 26-character string which happens to have the same SHA-256 hash that they discovered from the timing attack. This is incredibly difficult to do, and simply not viable with current technology.

### 16. Authentication

we’re going to look at how to authenticate requests to our API, so that we know exactly which user a particular request is coming from.

Remember: Authentication is about confirming who a user is, whereas authorization is about checking whether that user is permitted to do something.

•  Lay out the possible approaches to API authentication that we could use, and talk through their relative pro and cons.


•  Implement a stateful token-based authentication pattern, which allows clients to exchange their user credentials for a time-limited authentication token identifying who they are.


### 16.1. Authentication Options

let’s talk about how we’re going to authenticate requests to our API and find out which user a request is coming from.

 there are many different options, and it’s not always immediately clear which one is the right fit for your project. So in this chapter we’ll discuss some of the most common approaches at a high level, talk through their relative pros and cons, and finish with some general guidelines about when they’re appropriate to use.

Specifically, the five approaches that we’ll compare are:
•  Basic authentication
•  Stateful token authentication
•  Stateless token authentication
•  API key authentication
•  OAuth 2.0 / OpenID Connect


Important: For all authentication methods that we’re describing in this chapter, it’s assumed that your API only communicates with clients over HTTPS.

Perhaps the simplest way to determine who is making a request to your API is to use HTTP basic authentication.
With this method, the client includes an Authorization header with every request containing their credentials. The credentials need to be in the format username:password and base-64 encoded.

In your API, you can then extract the credentials from this header using Go’s Request.BasicAuth() method, and verify that they’re correct before continuing to process the request.
A big plus of HTTP basic authentication is how simple it is for clients. They can just send the same header with every request — and HTTP basic authentication is supported out-of-the-box by most programming languages, web browsers, and tools such as curl and wget.

It’s often useful in the scenario where your API doesn’t have ‘real’ user accounts, but you want a quick and easy way to restrict access to it or protect it from prying eyes.

For APIs with ‘real’ user accounts and — in particular — hashed passwords, it’s not such a great fit. Comparing the password provided by a client against a (slow) hashed password is a deliberately costly operation, and when using HTTP basic authentication you need to do that check for every request. That will create a lot of extra work for your API server and add significant latency to responses.

But even then, basic authentication can still be a good choice if traffic to your API is very low and response speed is not important to you.


The high-level idea behind token authentication (also sometimes known as bearer token authentication) works like this:
1.  The client sends a request to your API containing their credentials (typically username or email address, and password).
2.  The API verifies that the credentials are correct, generates a bearer token which represents the user, and sends it back to the user. The token expires after a set period of time, after which the user will need to resubmit their credentials again to get a new token.
3.  For subsequent requests to the API, the client includes the token in an Authorization header like this:
Authorization: Bearer <token>


4.  When your API receives this request, it checks that the token hasn’t expired and examines the token value to determine who the user is.

For APIs where user passwords are hashed (like ours), this approach is better than basic authentication because it means that the slow password check only has to be done periodically — either when creating a token for the first time or after a token has expired.

The downside is that managing tokens can be complicated for clients — they will need to implement the necessary logic for caching tokens, monitoring and managing token expiry, and periodically generating new tokens.


In a stateful token approach, the value of the token is a high-entropy cryptographically-secure random string. This token — or a fast hash of it — is stored server-side in a database, alongside the user ID and an expiry time for the token.

When the client sends back the token in subsequent requests, your API can look up the token in the database, check that it hasn’t expired, and retrieve the corresponding user ID to find out who the request is coming from.

The big advantage of this is that your API maintains control over the tokens —it’s straightforward to revoke tokens on a per-token or per-user basis bydeleting them from the database or marking them as expired.

Conceptually it’s also simple and robust — the security is provided by the token being an ‘unguessable’, which is why it’s important to use a high-entropy cryptographically-secure random value for the token.


So, what are the downsides?

Beyond the complexity for clients that is inherent with token authentication generally, it’s difficult to find much to criticize about this approach.

Perhaps the fact that it requires a database lookup is a negative — but in most cases you will need to make a database lookup to check the user’s activation status or retrieve additional information about them anyway.

In contrast, stateless tokens encode the user ID and expiry time in the token itself. The token is cryptographically signed to prevent tampering and (in some cases) encrypted to prevent the contents being read.

There are a few different technologies that you can use to create stateless tokens. Encoding the information in a JWT (JSON Web Token) is probably the most well-known approach, but PASETO, Branca and nacl/secretbox are viable alternatives too. 

The main selling point of using stateless tokens for authentication is that the work to encode and decode the token can be done in memory, and all the information required to identify the user is contained within the token itself. There’s no need to perform a database lookup to find out who a request is coming from.

The primary downside of stateless tokens is that they can’t easily be revoked once they are issued.

In an emergency, you could effectively revoke all tokens by changing the secret used for signing your tokens (forcing all users to re-authenticate), or another workaround is to maintain a blocklist of revoked tokens in a database (although that defeats the ‘stateless’ aspect of having stateless tokens).

Note: You should generally avoid storing additional information in a stateless token, such as a user’s activation status or permissions, and using that as the basis for authorization checks. During the lifetime of the token, the information encoded into it will potentially become stale and out-of-sync with the real data in your system — and relying on stale data for authorization checks can easily lead to unexpected behavior for users and various security issues.

Finally, with JWTs in particular, the fact that they’re highly configurable means that there are lots of things you can get wrong. The Critical vulnerabilities in JSON Web Token libraries and JWT Security Best Practices articles provide a good introduction to the type of things you need to be careful of here.

Because of these downsides, stateless tokens — and JWTs in particular — are generally not the best choice for managing authentication in most API applications.


But they can be very useful in a scenario where you need delegated authentication — where the application creating the authentication token is different to the application consuming it, and those applications don’t share any state (which means that using stateful tokens isn’t an option).

On receiving it, your API can regenerate the fast hash of the key and use it tolookup the corresponding user ID from your database.

 the main difference is that the keys are permanent keys, rather than temporary tokens.

On one hand, this is nice for the client as they can use the same key for every request and they don’t need to write code to manage tokens or expiry. On the other hand, the user now has two long-lived secrets to manage which can potentially compromise their account: their password, and their API key.

Another option is to leverage OAuth 2.0 for authentication. With this approach, information about your users (and their passwords) is stored by a third-party identity provider like Google or Facebook rather than yourself.
The first thing to mention here is that OAuth 2.0 is not an authentication protocol, and you shouldn’t really use it for authenticating users. The oauth.net website has a great article explaining this, and I highly recommend reading it.

If you want to implement authentication checks against a third-party identity provider, you should use OpenID Connect (which is built directly on top of OAuth 2.0).


•  When you want to authenticate a request, you redirect the user to an ‘authentication and consent’ form hosted by the identity provider.
•  If the user consents, then the identity provider sends your API an authorization code.

•  Your API then sends the authorization code to another endpoint provided by the identity provider. They verify the authorization code, and if it’s valid they will send you a JSON response containing an ID token.
•  This ID token is itself a JWT. You need to validate and decode this JWT to get the actual user information, which includes things like their email address, name, birth date, timezone etc.
•  Now that you know who the user is, you can then implement a stateful or stateless authentication token pattern so that you don’t have to go through the whole process for every subsequent request.


there are pros and cons to using OpenID Connect. The big plus is that you don’t need to persistently store user information or passwords yourself. The big downside is that it’s quite complex — although there are some helper packages like coreos/go-oidc which do a good job of masking that complexity and providing a simple interface for the OpenID Connect workflow that you can hook in to.


It’s also important to point out that using OpenID Connect requires all your users to have an account with the identity provider, and the ‘authentication and consent’ step requires human interaction via a web browser — which is probably fine if your API is the back-end for a website, but not ideal if it is a ‘standalone’ API with other computer programs as clients.


•  If your API doesn’t have ‘real’ user accounts with slow password hashes, then HTTP basic authentication can be a good — and often overlooked — fit.

•  If you don’t want to store user passwords yourself, all your users have accounts with a third-party identity provider that supports OpenID Connect, and your API is the back-end for a website… then use OpenID Connect.

•  If you require delegated authentication, such as when your API has a microservice architecture with different services for performing authentication and performing other tasks, then use stateless authentication tokens.

◦  Stateful authentication tokens are a nice fit for APIs that act as the back-end for a website or single-page application, as there is a natural moment when the user logs-in where they can be exchanged for user credentials.

◦  In contrast, API keys can be better for more ‘general purpose’ APIs because they’re permanent and simpler for developers to use in their applications and scripts.

Note: Although I really don’t recommend using JWTs unless you need some form of delegated authentication, I’m aware that they hold a lot of mind-share in the developer community and are often used more widely than that.


### 16.2. Generating Authentication Tokens

we’re going to focus on building up the code for a new POST/v1/tokens/authentication endpoint, which will allow a client to exchange their credentials (email address and password) for a stateful authentication token.

At a high level, the process for exchanging a user’s credentials for an authentication token will work like this:
1.  The client sends a JSON request to a new POST/v1/tokens/authentication endpoint containing their credentials (email and password).
2.  We look up the user record based on the email, and check if the password provided is the correct one for the user. If it’s not, then we send an error response.
3.  If the password is correct, we use our app.models.Tokens.New() method to generate a token with an expiry time of 24 hours and the scope "authentication".
4.  We send this authentication token back to the client in a JSON response body.

// Add struct tags to control how the struct appears when encoded to JSON.

type Token struct {
    Plaintext string    `json:"token"`
    Hash      []byte    `json:"-"`
    UserID    int64     `json:"-"`
    Expiry    time.Time `json:"expiry"`
    Scope     string    `json:"-"`
}

These new struct tags mean that only the Plaintext and Expiry fields will be included when encoding a Token struct — all the other fields will be omitted. We also rename the Plaintext field to "token", just because it’s a more meaningful name for clients than ‘plaintext’ is.

Altogether, this means that when we encode a Token struct to JSON the result will look similar to this:
{
    "token": "X3ASTT2CDAN66BACKSCI4SU7SI",
    "expiry": "2021-01-18T13:00:25.648511827+01:00"
}


    // Lookup the user record based on the email address. If no matching user was

    // found, then we call the app.invalidCredentialsResponse() helper to send a 401

    // Unauthorized response to the client (we will create this helper in a moment).

    user, err := app.models.Users.GetByEmail(input.Email)

// Otherwise, if the password is correct, we generate a new token with a 24-hour 

    // expiry time and the scope 'authentication'.

    token, err := app.models.Tokens.New(user.ID, 24*time.Hour, data.ScopeAuthentication)


    // Encode the token to JSON and send it in the response along with a 201 Created

    // status code.

    err = app.writeJSON(w, http.StatusCreated, envelope{"authentication_token": token}, nil)

func (app *application) invalidCredentialsResponse(w http.ResponseWriter, r *http.Request) {
    message := "invalid authentication credentials"
    app.errorResponse(w, r, http.StatusUnauthorized, message)
}

You should get a 201 Created response and a JSON body containing an authentication token, similar to this:

In contrast, if you try making a request with a well-formed but unknown email address, or an incorrect password, you should get an error response.

You can do that, and in most cases it will probably work fine. But it’s important to be conscious that you making a willful violation of the HTTP specifications: Authorization is a request header, not a response header.

### 16.3. Authenticating Requests

let’s look at how we can use that token to authenticatethem, so we know exactly which user a request is coming from.

Essentially, once a client has an authentication token we will expect them to include it with all subsequent requests in an Authorization header, like so:
Authorization: Bearer IEYZQUBEMPPAKPOAWTPV6YJ6RM
When we receive these requests, we’ll use a new authenticate() middleware method to execute the following logic:


•  If the authentication token is not valid, then we will send the client a 401 Unauthorized response and an error message to let them know that their token is malformed or invalid.

•  If the authentication token is valid, we will look up the user details and add their details to the request context.

•  If no Authorization header was provided at all, then we will add the details for an anonymous user to the request context instead.

// Declare a new AnonymousUser variable.

var AnonymousUser = &User{}

// Check if a User instance is the AnonymousUser.

func (u *User) IsAnonymous() bool {
    return u == AnonymousUser
}



So here we’ve created a new AnonymousUser variable, which holds a pointer to a User struct representing an inactivated user with no ID, name, email or password.

relates to storing the user details in the request context.


•  Every http.Request that our application processes has a context.Context embedded in it, which we can use to store key/value pairs containing arbitrary data during the lifetime of the request. In this case we want to store a User struct containing the current user’s information.

•  Any values stored in the request context have the type interface{}. This means that after retrieving a value from the request context you need to assert it back to its original type before using it.

•  It’s good practice to use your own custom type for the request context keys. This helps prevent naming collisions between your code and any third-party packages which are also using the request context to store information.

// Define a custom contextKey type, with the underlying type string.

type contextKey string

// Convert the string "user" to a contextKey type and assign it to the userContextKey

// constant. We'll use this constant as the key for getting and setting user information

// in the request context.

const userContextKey = contextKey("user")

/ Add the "Vary: Authorization" header to the response. This indicates to any

        // caches that the response may vary based on the value of the Authorization

        // header in the request.

        w.Header().Add("Vary", "Authorization")

// Retrieve the value of the Authorization header from the request. This will

        // return the empty string "" if there is no such header found.

        authorizationHeader := r.Header.Get("Authorization")

        // If there is no Authorization header found, use the contextSetUser() helper

        // that we just made to add the AnonymousUser to the request context. Then we 

        // call the next handler in the chain and return without executing any of the

        // code below.

        if authorizationHeader == "" {
            r = app.contextSetUser(r, data.AnonymousUser)
            next.ServeHTTP(w, r)
            return


        // Otherwise, we expect the value of the Authorization header to be in the format

        // "Bearer <token>". We try to split this into its constituent parts, and if the

        // header isn't in the expected format we return a 401 Unauthorized response

        // using the invalidAuthenticationTokenResponse() helper (which we will create 

        // in a moment).

        headerParts := strings.Split(authorizationHeader, " ")
        if len(headerParts) != 2 || headerParts[0] != "Bearer" {
            app.invalidAuthenticationTokenResponse(w, r)
            return
        }


        // Extract the actual authentication token from the header parts.

        token := headerParts[1]

        // Validate the token to make sure it is in a sensible format.

        v := validator.New()

        // If the token isn't valid, use the invalidAuthenticationTokenResponse() 

        // helper to send a response, rather than the failedValidationResponse() helper 

        // that we'd normally use.

        if data.ValidateTokenPlaintext(v, token); !v.Valid() {
            app.invalidAuthenticationTokenResponse(w, r)
            return
        }


        // Call the contextSetUser() helper to add the user information to the request

        // context.

        r = app.contextSetUser(r, user)

•  If a valid authentication token is provided in the Authorization header, then a User struct containing the corresponding user details will be stored in the request context.
•  If no Authorization header is provided at all, our AnonymousUser struct will be stored in the request context.

Note: We’re including a WWW-Authenticate: Bearer header here to help inform or remind the client that we expect them to authenticate using a bearer token.


    // Use the authenticate() middleware on all requests.

    return app.recoverPanic(app.rateLimit(app.authenticate(router)))

Then let’s try the same thing but with a valid authentication token in the Authorization header. This time, the relevant user details should be added to the request context, and we should again get a successful response

Hint: If you get an error response here, make sure that you’re using the correct authentication token from the first request in the second request.


### 17. Permission-based Authorization

By the time a request leaves our authenticate() middleware, there are now two possible states for the request context. Either:
•  The request context contains a User struct (representing a valid, authenticated, user).
•  Or the request context contains an AnonymousUser struct.


and look at how to perform different authorization checks to restrict access to our API endpoints. Specifically, you’ll learn how to:
•  Add checks so that only activated users are able to access the various /v1/movies** endpoints.
•  Implement a permission-based authorization pattern, which provides fine-grained control over exactly which users can access which endpoints.

### 17.1. Requiring User Activation

the first thing we’re going to do in terms of authorization is restrict access to our /v1/movies** endpoints — so that they can only be accessed by users who are authenticated (not anonymous), and who have activated their account.


•  If the user is anonymous we should send a 401 Unauthorized response and an error message saying “you must be authenticated to access this resource”.


•  If the user is not anonymous (i.e. they have authenticated successfully and we know who they are), but they are not activated we should send a 403 Forbidden response and an error message saying “your user account must be activated to access this resource”.


Remember: A 401 Unauthorized response should be used when you have missing or bad authentication, and a 403 Forbidden response should be used afterwards, when the user is authenticated but isn’t allowed to perform the requested operation.


        // Use the contextGetUser() helper that we made earlier to retrieve the user 

        // information from the request context.

        user := app.contextGetUser(r)

        // If the user is anonymous, then call the authenticationRequiredResponse() to 

        // inform the client that they should authenticate before trying again.

        if user.IsAnonymous() {
            app.authenticationRequiredResponse(w, r)
            return
        }

Instead of accepting and returning a http.Handler, it accepts and returns a http.HandlerFunc.
This is a small change, but it makes it possible to wrap our /v1/movie** handler functions directly with this middleware, without needing to make any further conversions.

    router.NotFound = http.HandlerFunc(app.notFoundResponse)
    router.MethodNotAllowed = http.HandlerFunc(app.methodNotAllowedResponse)

// Use the requireActivatedUser() middleware on our five /v1/movies** endpoints.

    router.HandlerFunc(http.MethodGet, "/v1/movies", app.requireActivatedUser(app.listMoviesHandler))

quickly connect to your PostgreSQL database and double-check which users are already activated.


Splitting up the middleware
At the moment we have one piece of middleware doing two checks: first it checks that the user is authenticated (not anonymous), and second it checks that they are activated.

However, there would be some overlap between these two middlewares, as they would both be checking whether a user is authenticated not. A neat way to avoid this duplication is to have your requireActivatedUser() middleware automatically call the requireAuthenticatedUser() middleware.

// Create a new requireAuthenticatedUser() middleware to check that a user is not 

// anonymous.

func (app *application) requireAuthenticatedUser(next http.HandlerFunc) http.HandlerFunc {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        user := app.contextGetUser(r)

        if user.IsAnonymous() {
            app.authenticationRequiredResponse(w, r)
            return
        }

        next.ServeHTTP(w, r)
    })
}


    // Wrap fn with the requireAuthenticatedUser() middleware before returning it.

    return app.requireAuthenticatedUser(fn)

In our context this makes a lot of sense — we shouldn’t be checking if a user is activated unless we know exactly who they are!

If you only have a couple of endpoints where you want to perform authorization checks,then rather than using middleware it can often be easier to do the checks inside therelevant handlers instead. For example:

### 17.2. Setting up the Permissions Database Table

Restricting our API so that movie data can only be accessed and edited by activated users is useful, but sometimes you might need a more granular level of control. For example, in our case we might be happy for ‘regular’ users of our API to read the movie data (so long as they are activated), but we want to restrict write access to a smaller subset of trusted users.

we’re going to introduce the concept of permissions to our application, so that only users who have a specific permission can perform specific operations. In our case, we’re going to create two permissions: a movies:read permission which will allow a user to fetch and filter movies, and a movies:write permission which will allow users to create, edit and delete movies.

The relationship between permissions and users is a great example of a many-to-many relationship. One user may have many permissions, and the same permission may belong to many users.

The classic way to manage a many-to-many relationship in a relational database like PostgreSQL is to create a joining table between the two entities.

•  The PRIMARY KEY (user_id, permission_id) line sets a composite primary key on our users_permissions table, where the primary key is made up of both the users_id and permission_id columns. Setting this as the primary key essentially means that the same user/permission combination can only appear once in the table and cannot be duplicated.


And likewise, we use the REFERENCES permissions syntax to ensure that the permission_id column has a corresponding entry in the permissions table.


### 17.3. Setting up the Permissions Model

Next let’s head to our internal/data package and add a PermissionModel to manage the interactions with our new tables. For now, the only thing we want to include in this model is a GetAllForUser() method to return all permission codes for a specific user. The idea is that we’ll be able to use this in our handlers and middleware like so:
// Return a slice of the permission codes for the user with ID = 1. This would return

// something like []string{"movies:read", "movies:write"}.

app.models.Permissions.GetAllForUser(1)

In this query we are using the INNER JOIN clause to join our permissions table to our users_permissions table, and then using it again to join that to the users table. Then we use the WHERE clause to filter the result, leaving only rows which relate to a specific user ID.

// Define a Permissions slice, which we will use to will hold the permission codes (like

// "movies:read" and "movies:write") for a single user.

type Permissions []string

// Add a helper method to check whether the Permissions slice contains a specific 

// permission code.

func (p Permissions) Include(code string) bool {
    for i := range p {
        if code == p[i] {
            return true
        }
    }
    return false
}



/ The GetAllForUser() method returns all permission codes for a specific user in a 

// Permissions slice. The code in this method should feel very familiar --- it uses the

// standard pattern that we've already seen before for retrieving multiple data rows in 

// an SQL query.

func (m PermissionModel) GetAllForUser(userID int64) (Permissions, error) {

### 17.4. Checking Permissions

Now that our PermissionModel is set up, let’s look at how we can use it to restrict access to our API endpoints.


•  We’ll make a new requirePermission() middleware which accepts a specific permission code like "movies:read" as an argument.
•  In this middleware we’ll retrieve the current user from the request context, and call the app.models.Permissions.GetAllForUser() method (which we just made) to get a slice of their permissions.
•  Then we can then check to see if the slice contains the specific permission code needed. If it doesn’t, we should send the client a 403 Forbidden response.

To put this into practice, let’s first make a new notPermittedResponse() helper function for sending the 403 Forbidden response. 

func (app *application) notPermittedResponse(w http.ResponseWriter, r *http.Request) {
    message := "your user account doesn't have the necessary permissions to access this resource"
    app.errorResponse(w, r, http.StatusForbidden, message)
}

We’re going to set this up so that the requirePermission() middleware automatically wraps our existing requireActivatedUser() middleware, which in turn — don’t forget — wraps our requireAuthenticatedUser() middleware.

This is important — it means that when we use the requirePermission() middleware we’ll actually be carrying out three checks which together ensure that the request is from an authenticated (non-anonymous), activated user, who has a specific permission.


// Note that the first parameter for the middleware function is the permission code that

// we require the user to have.

func (app *application) requirePermission(code string, next http.HandlerFunc) http.HandlerFunc {
    fn := func(w http.ResponseWriter, r *http.Request) {
        // Retrieve the user from the request context.

        user := app.contextGetUser(r)

        // Get the slice of permissions for the user.

        permissions, err := app.models.Permissions.GetAllForUser(user.ID)
        if err != nil {
            app.serverErrorResponse(w, r, err)
            return
        }

        // Check if the slice includes the required permission. If it doesn't, then 

        // return a 403 Forbidden response.

        if !permissions.Include(code) {
            app.notPermittedResponse(w, r)
            return


        // Otherwise they have the required permission so we call the next handler in

        // the chain.

        next.ServeHTTP(w, r)
    }

// Wrap this with the requireActivatedUser() middleware before returning it.

    return app.requireActivatedUser(fn)


    // Use the requirePermission() middleware on each of the /v1/movies** endpoints,

    // passing in the required permission code as the first parameter.

    router.HandlerFunc(http.MethodGet, "/v1/movies", app.requirePermission("movies:read", app.listMoviesHandler))
    router.HandlerFunc(http.MethodPost, "/v1/movies", app.requirePermission("movies:write", app.createMovieHandler))

-- Set the activated field for alice@example.com to true.

UPDATE users SET activated = true WHERE email = 'alice@example.com';

-- Give all users the 'movies:read' permission

INSERT INTO users_permissions
SELECT id, (SELECT id FROM permissions WHERE code = 'movies:read') FROM users;

Once that completes, you should see a list of the currently activated users and their permissions, similar to this:
       email       |        permissions         
-------------------+----------------------------
 alice@example.com | {movies:read}
 faith@example.com | {movies:read,movies:write}


### 17.5. Granting Permissions

Our permissions model and authorization middleware are now functioning well. But — at the moment — when a new user registers an account they don’t have any permissions. In this chapter we’re going to change that so that new users are automatically granted the "movies:read" permission by default.


In order to grant permissions to a user we’ll need to update our PermissionModel to include an AddForUser() method, which adds one or more permission codes for a specific user to our database. The idea is that we will be able to use it in our handlers like this:
// Add the "movies:read" and "movies:write" permissions for the user with ID = 2.

app.models.Permissions.AddForUser(2, "movies:read", "movies:write")

// Add the provided permission codes for a specific user. Notice that we're using a 

// variadic parameter for the codes so that we can assign multiple permissions in a 

// single call.

func (m PermissionModel) AddForUser(userID int64, codes ...string) error {
    query := `
        INSERT INTO users_permissions
        SELECT $1, permissions.id FROM permissions WHERE permissions.code = ANY($2)`


    // Add the "movies:read" permission for the new user.

    err = app.models.Permissions.AddForUser(user.ID, "movies:read")
    if err != nil {
        app.serverErrorResponse(w, r, err)
        return
    }

Let’s check that this is working correctly by registering a brand-new user with the email address grace@example.com:

### 18. Cross Origin Requests

we’re going to switch to a completely new topic and update our application so that it supports cross-origin requests (CORS) from JavaScript.

•  What cross-origin requests are, and why web browsers prevent them by default.
•  The difference between simple and preflight cross-origin requests.
•  How to use Access-Control headers to allow or disallow specific cross-origin requests.
•  About the security considerations you need to be aware of when CORS configuring settings in your application.

### 18.1. An Overview of CORS

Basically, if two URLs have the same scheme, host and port (if specified) they are said to share the same origin. 

Understanding what origins are is important because all web browsers implement a security mechanism known as the same-origin policy. 

•  A webpage on one origin can embed certain types of resources from another origin in their HTML — including images, CSS, and JavaScript files. For example, doing this is in your webpage is OK:


•  A webpage on one origin can send data to a different origin. For example, it’s OK for a HTML form in a webpage to submit data to a different origin.
•  But a webpage on one origin is not allowed to receive data from a different origin.

the same-origin policy prevents a (potentially malicious) website on another origin from reading (possibly confidential) information from your website.


It’s important to emphasize that cross-origin sending of data is not prevented by the same-origin policy, despite also being dangerous. In fact, this is why CSRF attacks are possible and why we need to take additional steps to prevent them — like using SameSite cookies and CSRF tokens.

If this JavaScript tries to make an HTTP request to https://bar.com/data.json (a different origin), then the request will be sent and processed by the bar.com server, but the user’s web browser will block the response so that the JavaScript code from https://foo.com cannot see it.

Generally speaking, the same-origin policy is an extremely useful security safeguard. But while it’s good in the general case, in certain circumstances you might want to relax it.

For example, if you have an API at api.example.com and a trusted JavaScript front-end application running on www.example.com, then you’ll probably want to allow cross-origin requests from the trusted www.example.com domain to your API.

Or perhaps you have a completely open public API, and you want to allow cross-origin requests from anywhere so it’s easy for other developers to integrate with their own websites.

Fortunately, most modern web browsers allow you to allow or disallow specific cross-origin requests to your API by setting Access-Control headers on your API responses. We’ll explain exactly how to do that and how these headers work over the next few chapters of this book.


### 18.2. Demonstrating the Same-Origin Policy

To demonstrate how the same-origin policy works and how to relax it for requests to our API, we need to simulate a request to our API from a different origin.

<script>
        document.addEventListener('DOMContentLoaded', function() {
            fetch("http://localhost:4000/v1/healthcheck").then(
                function (response) {
                    response.text().then(function (text) {
                        document.getElementById("output").innerHTML = text;
                    });
                }, 
                function(err) {
                    document.getElementById("output").innerHTML = err;
                }
            );
        });
    </script>

    // Make the server address configurable at runtime via a command-line flag.

    addr := flag.String("addr", ":9000", "Server address")
    flag.Parse()

    log.Printf("starting server on %!s(MISSING)", *addr)

    // Start a HTTP server listening on the given address, which responds to all

    // requests with the webpage HTML above.

    err := http.ListenAndServe(*addr, http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte(html))
    }))
    log.Fatal(err)


•  The fetch() method works asynchronously and returns a promise. We use the then() method on the promise to set up two callback functions: the first callback is executed if fetch() is successful, and the second is executed if there is a failure.

•  This logic is all wrapped up in document.addEventListener(‘DOMContentLoaded’, function(){…}), which essentially means that fetch() won’t be called until the user’s web browser has completely loaded the HTML document.


So, when you visit http://localhost:9000 in your web browser, the fetch() action to http://localhost:4000/v1/healthcheck should be forbidden by the same-origin policy. Specifically, our API should receive and process the request, but your web browser should block the response from being read by the JavaScript code.

Note: The error message you see here is defined by your web browser, so yours might be slightly different if you’re not using Firefox.

It’s also helpful here to open your browser’s developer tools, refresh the page, and look at your console log. You should see a message stating that the response from our GET /v1/healthcheck endpoint was blocked from being read due to the same-origin policy, similar to this:
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:4000/v1/healthcheck.

You may also want to open the network activity tab in your developer tools and examine the HTTP headers associated with the blocked request.
 

The first thing is that the headers demonstrate that the request was sent to our API, which processed the request and returned a successful 200 OK response to the web browser containing all our standard response headers. To re-iterate: the request itself was not prevented by the same-origin policy — it’s just that the browser won’t let JavaScript see the response.


Finally, it’s important to emphasize that the same-origin policy is a web browser thing only.
Outside of a web browser, anyone can make a request to our API from anywhere, using curl, wget or any other means and read the response. That’s completely unaffected and unchanged by the same-origin policy.

### 18.3. Simple CORS Requests

Let’s now make some changes to our API which relax the same-origin policy, so that JavaScript can read the responses from our API endpoints.
To start with, the simplest way to achieve this is by setting the following header on all our API responses:
Access-Control-Allow-Origin: *

The Access-Control-Allow-Origin response header is used to indicate to a browser that it’s OK to share a response with a different origin. In this case, the header value is the wildcard * character, which means that it’s OK to share the response with any other origin.


create a small enableCORS() middleware function in our API application which sets this header:
 File: cmd/api/middleware.go
package main

...

func (app *application) enableCORS(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Access-Control-Allow-Origin", "*")

        next.ServeHTTP(w, r)
    })
}


    // Add the enableCORS() middleware.

    return app.recoverPanic(app.enableCORS(app.rateLimit(app.authenticate(router))))

If we positioned it after our rate limiter, for example, any cross-origin requests that exceed the rate limit would not have the Access-Control-Allow-Origin header set. This means that they would be blocked by the client’s web browser due to the same-origin policy, rather than the client receiving a 429 Too Many Requests response like they should.


But more often you’ll probably want to restrict CORS to a much smaller set of trusted origins.
To do this, you need to explicitly include the trusted origins in the Access-Control-Allow-Origin header instead of using a wildcard. For example, if you want to only allow CORS from the origin https://www.example.com you could send the following header in your responses:
Access-Control-Allow-Origin: https://www.example.com

But if you need to support multiple trusted origins, or you want the value to be configurable at runtime, then things get a bit more complex.

To work around this limitation, you’ll need to update your enableCORS() middleware to check if the value of the Origin header matches one of your trusted origins. If it does, then you can reflect (or echo) that value back in the Access-Control-Allow-Origin response header.


Note: The web origin specification does permit multiple space-separated values in the Access-Control-Allow-Origin header but, unfortunately, no web browsers actually support this.

Let’s update our API so that cross-origin requests are restricted to a list of trusted origins, configurable at runtime.

$ go run ./cmd/api -cors-trusted-origins="https://www.example.com https://staging.example.com"
In order to process this command-line flag, we can combine the new Go 1.16 flags.Func() and strings.Fields() functions to split the origin values into a []string slice ready for use.


    // Use the flag.Func() function to process the -cors-trusted-origins command line

    // flag. In this we use the strings.Fields() function to split the flag value into a

    // slice based on whitespace characters and assign it to our config struct.

    // Importantly, if the -cors-trusted-origins flag is not present, contains the empty

    // string, or contains only whitespace, then strings.Fields() will return an empty

    // []string slice.

    flag.Func("cors-trusted-origins", "Trusted CORS origins (space separated)", func(val string) error {
        cfg.cors.trustedOrigins = strings.Fields(val)
        return nil
    })

A side effect of this is that the response will be different depending on the origin that the request is coming from. Specifically, the value of the Access-Control-Allow-Origin header may be different in the response, or it may not even be included at all.

So because of this we should make sure to always set a Vary: Origin response header to warn any caches that the response may be different. This is actually really important, and it can be the cause of subtle bugs like this one if you forget to do it. As a rule of thumb:


If your code makes a decision about what to return based on the content of a request header, you should include that header name in your Vary response header — even if the request didn’t include that header.



        // Add the "Vary: Origin" header.

        w.Header().Add("Vary", "Origin")

        // Get the value of the request's Origin header.

        origin := r.Header.Get("Origin")

        // Only run this if there's an Origin request header present AND at least one

        // trusted origin is configured.

        if origin != "" && len(app.config.cors.trustedOrigins) != 0 {
            // Loop through the list of trusted origins, checking to see if the request

            // origin exactly matches one of them.

            for i := range app.config.cors.trustedOrigins {
                if origin == app.config.cors.trustedOrigins[i] {
                    // If there is a match, then set a "Access-Control-Allow-Origin"

                    // response header with the request origin as the value.

                    w.Header().Set("Access-Control-Allow-Origin", origin)
                }
            }
        }

And with those changes complete, we’re now ready to try this out again.

If you have a lot of trusted origins that you want to support, then you might be tempted to check for a partial match on the origin to see if it ‘starts with’ or ‘ends with’ a specific value, or matches a regular expression.

If your API endpoint requires credentials (cookies or HTTP basic authentication) you should also set an Access-Control-Allow-Credentials: true header in your responses. If you don’t set this header, then the web browser will prevent any cross-origin responses with credentials from being read by JavaScript.


Importantly, you must never use the wildcard Access-Control-Allow-Origin: * header in conjunction with Access-Control-Allow-Credentials: true, as this would allow any website to make a credentialed cross-origin request to your API.

Also, importantly, if you want credentials to be sent with a cross-origin request then you’ll need to explicitly specify this in your JavaScript. For example, with fetch() you should set the credentials value of the request to 'include'. Like so:
fetch("https://api.example.com", {credentials: 'include'}).then( ... );

Or if using XMLHTTPRequest you should set the withCredentials property to true. For example:
var xhr = new XMLHttpRequest();
xhr.open('GET', 'https://api.example.com');
xhr.withCredentials = true;
xhr.send(null);

### 18.4. Preflight CORS Requests

When a cross-origin request doesn’t meet these conditions, then the web browser will trigger an initial ‘preflight’ request before the real request. The purpose of this preflight request is to determine whether the real cross-origin request will be permitted or not.

And because the header Content-Type: application/json isn’t allowed in a ‘simple’ cross-origin request, this should trigger a preflight request to our API.

            fetch("http://localhost:4000/v1/tokens/authentication", {
                method: "POST",
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    email: 'alice@example.com',
                    password: 'pa55word'
                })
            }).then(

. If you look at the console log in your developer tools, you should see a message similar to this:
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:4000/v1/tokens/authentication. (Reason: header ‘content-type’ is not allowed according to header ‘Access-Control-Allow-Headers’ from CORS preflight response).

We can see that there are two requests here marked as ‘blocked’ by the browser:
•  An OPTIONS /v1/tokens/authentication request (this is the preflight request).
•  A POST /v1/tokens/authentication request (this is the ‘real’ request).

Access-Control-Request-Headers: content-type
Access-Control-Request-Method: POST

•  Origin — As we saw previously, this lets our API know what origin the preflight request is coming from.

•  Access-Control-Request-Headers — This lets our API know what HTTP headers will be sent with the real request (in this case we can see that the real request will include a content-type header).

In order to respond to a preflight request, the first thing we need to do is identify that it is a preflight request — rather than just a regular (possibly even cross-origin) OPTIONS request.

To do that, we can leverage the fact that preflight requests always have three components: the HTTP method OPTIONS, an Origin header, and an Access-Control-Request-Method header. If any one of these pieces is missing, we know that it is not a preflight request.

Once we identify that it is a preflight request, we need to send a 200 OK response with some special headers to let the browser know whether or not it’s OK for the real request to proceed. These are:
•  An Access-Control-Allow-Origin response header, which reflects the value of the preflight request’s Origin header (just like in the previous chapter).
•  An Access-Control-Allow-Methods header listing the HTTP methods that can be used in real cross-origin requests to the URL.


In our case, we could set the following response headers to allow cross-origin requests for all our endpoints:
Access-Control-Allow-Origin: <reflected trusted origin>
Access-Control-Allow-Methods: OPTIONS, PUT, PATCH, DELETE 
Access-Control-Allow-Headers: Authorization, Content-Type


Important: When responding to a preflight request it’s not necessary to include the CORS-safe methods HEAD, GET or POST in the Access-Control-Allow-Methods header. Likewise, it’s not necessary to include forbidden or CORS-safe headers in Access-Control-Allow-Headers.


1.  Set a Vary: Access-Control-Request-Method header on all responses, as the response will be different depending on whether or not this header exists in the request.

                  // Set the necessary preflight response headers, as discussed

                        // previously.

                        w.Header().Set("Access-Control-Allow-Methods", "OPTIONS, PUT, PATCH, DELETE")
                        w.Header().Set("Access-Control-Allow-Headers", "Authorization, Content-Type")

                        // Write the headers along with a 200 OK status and return from

                        // the middleware with no further action.

                        w.WriteHeader(http.StatusOK)

•  When we respond to a preflight request we deliberately send the HTTP status 200 OK rather than 204 No Content — even though there is no response body. This is because certain browser versions may not support 204 No Content responses and subsequently block the real request.

Note: If you look at the details for the preflight request, you should see that our new CORS headers have been set on the preflight response, shown by the red arrow in the screenshot above.

If you want, you can also add an Access-Control-Max-Age header to your preflight responses. This indicates the number of seconds that the information provided by the Access-Control-Allow-Methods and Access-Control-Allow-Headers headers can be cached by the browser.
For example, to allow the values to be cached for 60 seconds you can set the following header on your preflight response:
Access-Control-Max-Age: 60

If you don’t set an Access-Control-Max-Age header, current versions of Chrome/Chromium and Firefox will default to caching these preflight response values for 5 seconds. Older versions or other browsers may have different defaults, or not cache the values at all.

Setting a long Access-Control-Max-Age duration might seem like an appealing way to reduce requests to your API — and it is! But you also need to be careful. Not all browsers provide a way to clear the preflight cache, so if you send back the wrong headers the user will be stuck with them until the cache expires.
If you want to disable caching altogether, you can set the value to -1:
Access-Control-Max-Age: -1

It’s also important to be aware that browsers may impose a hard maximum on how long the headers can be cached for. The MDN documentation says:
•  Firefox caps this at 24 hours (86400 seconds).
•  Chromium (prior to v76) caps at 10 minutes (600 seconds).

•  Wildcards in these headers are currently only supported by 74%!o(MISSING)f browsers. Any browsers which don’t support them will block the preflight request.
•  The Authorization header cannot be wildcarded. Instead, you will need to include this explicitly in the header like Access-Control-Allow-Headers: Authorization, *.
•  Wildcards are not supported for credentialed requests (those with cookies or HTTP basic authentication). For these, the character * will be treated as the literal string "*", rather than as a wildcard.

### 19. Metrics

When your application is running in production with real-life traffic — or if you’re carrying out targeted load testing — you might want some up-close insight into how it’s performing and what resources it is using.

•  How much memory is my application using? How is this changing over time?
•  How many goroutines are currently in use? How is this changing over time?
•  How many database connections are in use and how many are idle? Do I need to change the connection pool settings?
•  What is the ratio of successful HTTP responses to both client and server errors? Are error rates elevated above normal?

Having insight into these things can help inform your hardware and configuration setting choices, and act as an early warning sign of potential problems (such as memory leaks).

To assist with this, Go’s standard library includes the expvar package which makes it easy to collate and view different application metrics at runtime.


•  How to use the expvar package to view application metrics in JSON format via a HTTP handler.
•  What default application metrics are available, and how to create your own custom application metrics for monitoring the number of active goroutines and the database connection pool.

•  How to use middleware to monitor request-level application metrics, including the counts of different HTTP status codes.


### 19.1. Exposing Metrics with Expvar

Viewing metrics for our API is made easy by the fact that the expvar package provides an expvar.Handler() function which returns a HTTP handler exposing your application metrics.
By default this handler displays information about memory usage, along with a reminder of what command-line flags you used when starting the application, all outputted in JSON format.


    // Register a new GET /debug/vars endpoint pointing to the expvar handler.

    router.Handler(http.MethodGet, "/debug/vars", expvar.Handler())

Note: Using the endpoint GET /debug/vars for the expvar handler is conventional but certainly not necessary. If you prefer, it’s perfectly fine to register it at an alternative endpoint like GET /v1/metrics instead.

should see a JSON response containing information about your running application.


This is essentially a JSON representation of the os.Args variable, and it’s useful if you want to see exactly what non-default settings were used when starting the application.


The "memstats" item contains a ‘moment-in-time’ snapshot of memory usage, as returned by the runtime.MemStats() function. Documentation and descriptions for all of the values can be found here, but the most important ones are:
•  TotalAlloc — Cumulative bytes allocated on the heap (will not decrease).
•  HeapAlloc — Current number of bytes on the heap.
•  HeapObjects — Current number of objects on the heap.
•  Sys — Total bytes of memory obtained from the OS (i.e. total memory reserved by the Go runtime for the heap, stacks, and other internal data structures).

Hint: If any of the terms above are unfamiliar to you, then I strongly recommend reading the Understanding Allocations in Go blog post which provides a great introduction to how Go allocates memory and the concepts of the heap and the stack.

### 19.2. Creating Custom Metrics

The default information exposed by the expvar handler is a good start, but we can make it even more useful by exposing some additional custom metrics in the JSON response.

The code to do this breaks down into two basic steps: first we need to register a custom variable with the expvar package, and then we need to set the value for the variable itself. In one line, the code looks roughly like this:
expvar.NewString("version").Set(version)

The first part of this — expvar.NewString("version") — creates a new expvar.String type, then publishes it so it appears in the expvar handler’s JSON response with the name "version", and then returns a pointer to it. Then we use the Set() method on it to assign an actual value to the pointer.

•  The expvar.String type is safe for concurrent use. So — if you want to — it’s OK to manipulate this value at runtime from your application handlers.
•  If you try to register two expvar variables with the same name, you’ll get a runtime panic when the duplicate variable is registered.

// Publish a new "version" variable in the expvar handler containing our application

    // version number (currently the constant "1.0.0").

    expvar.NewString("version").Set(version)

Dynamic metrics
Occasionally you might want to publish metrics which require you to call other code — or do some kind of pre-processing — to generate the necessary information. To help with this there is the expvar.Publish() function, which allows you to publish the result of a function in the JSON output.

For example, if you want to publish the number of currently active goroutines from Go’s runtime.NumGoroutine() function, you could write the following code:
expvar.Publish("goroutines", expvar.Func(func() interface{} {
    return runtime.NumGoroutine()
}))

It’s important to point out here that the interface{} value returned from this function must encode to JSON without any errors. If it can’t be encoded to JSON, then it will be omitted from the expvar output

In the case of the code snippet above, runtime.NumGoroutine() returns a regular int type — which will encode to a JSON number. So there’s no problem with that here.


    // Publish the number of active goroutines.

    expvar.Publish("goroutines", expvar.Func(func() interface{} {
        return runtime.NumGoroutine()
    }))

    // Publish the database connection pool statistics.

    expvar.Publish("database", expvar.Func(func() interface{} {
        return db.Stats()
    }))

    // Publish the current Unix timestamp.

    expvar.Publish("timestamp", expvar.Func(func() interface{} {
        return time.Now().Unix()
    }))

For example, you can send a batch of requests to the POST /v1/tokens/authentication endpoint (which is slow and costly because it checks a bcrypt-hashed password) like so:
$ BODY='{"email": "alice@example.com", "password": "pa55word"}'
$ hey -d "$BODY" -m "POST" http://localhost:4000/v1/tokens/authentication


At the moment I took this screenshot, we can see that my API application had 118 active goroutines, with 11 database connections in use and 14 connections sat idle.

The database WaitCount figure of 25 is the total number of times that our application had to wait for a database connection to become available in our sql.DB pool (because all connections were in-use).

Also, the MaxIdleTimeClosed figure is the total count of the number of connections that have been closed because they reached their ConnMaxIdleTime limit (which in our case is set to 15 minutes by default).

It’s important to be aware that these metrics provide very useful information to anyone who wants to perform a denial-of-service attack against your application, and that the "cmdline" values may also expose potentially sensitive information (like a database DSN).

So you should make sure to restrict access to the GET /debug/vars endpoint when running in a production environment.


There are a few different approaches you could take to do this.
One option is to leverage our existing authentication process and create a metrics:view permission so that only certain trusted users can access the endpoint. Another option would be to use HTTP Basic Authentication to restrict access to the endpoint.

As part of our Caddy set up, we’ll restrict access to the GET /debug/vars endpoint so that it can only be accessed via connections from the local machine, rather than being exposed on the internet.

hopefully it will become possible to omit these in a future version of Go.

### 19.3. Request-level Metrics

In this chapter we’re going to create some new middleware to record custom request-level metrics for our application. We’ll start by recording the following three things:
•  The total number of requests received.
•  The total number of responses sent.
•  The total (cumulative) time taken to process all requests in microseconds.


create a new metrics() middleware method, which initializes the necessary expvar variables and then updates them each time we process a request.

    // Initialize the new expvar variables when the middleware chain is first built.

    totalRequestsReceived := expvar.NewInt("total_requests_received")
    totalResponsesSent := expvar.NewInt("total_responses_sent")
    totalProcessingTimeMicroseconds := expvar.NewInt("total_processing_time_μs")

// The following code will be run for every request...

    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        // Record the time that we started to process the request.

        start := time.Now()

        // Use the Add() method to increment the number of requests received by 1.

        totalRequestsReceived.Add(1)

        // Call the next handler in the chain.

        next.ServeHTTP(w, r)

        // On the way back up the middleware chain, increment the number of responses

        // sent by 1.

        totalResponsesSent.Add(1)

        // Calculate the number of microseconds since we began to process the request,

        // then increment the total processing time by this amount.

        duration := time.Now().Sub(start).Microseconds()
        totalProcessingTimeMicroseconds.Add(duration)
    })

// Use the new metrics() middleware at the start of the chain.

    return app.metrics(app.recoverPanic(app.enableCORS(app.rateLimit(app.authenticate(router)))))

Based on the total_processing_time_μs and total_responses_sent values, we can calculate that the average processing time per request was approximately 2.4 seconds 

•  The number of ‘active’ in-flight requests:
total_requests_received - total_responses_sent

### 19.4. Recording HTTP Status Codes

Unfortunately Go doesn’t make this easy — there is nobuilt-in way to examine a http.ResponseWriter to see what status code is going tobe sent to a client.

I highly recommend using the third-party httpsnoop package. It’s small and focused, with no additional dependencies, and it makes it very easy to record the HTTP status code and size of each response, along with the total processing time for each request.

Let’s dig in and adapt our metrics() middleware to use the httpsnoop package, and record the HTTP status codes for our responses.


Then, for each request, we’ll use the httpsnopp.CaptureMetrics() function to get the HTTP status code, and increment the value in the map accordingly.

    // Declare a new expvar map to hold the count of responses for each HTTP status

    // code.

    totalResponsesSentByStatus := expvar.NewMap("total_responses_sent_by_status")

        // Increment the requests received count, like before.

        totalRequestsReceived.Add(1)

        // Call the httpsnoop.CaptureMetrics() function, passing in the next handler in

        // the chain along with the existing http.ResponseWriter and http.Request. This

        // returns the metrics struct that we saw above.

        metrics := httpsnoop.CaptureMetrics(next, w, r)


        // Use the Add() method to increment the count for the given status code by 1.

        // Note that the expvar map is string-keyed, so we need to use the strconv.Itoa()

        // function to convert the status code (which is an integer) to a string.

        totalResponsesSentByStatus.Add(strconv.Itoa(metrics.Code), 1)

And if you take a look at the metrics in your browser, you should now see the same corresponding counts for each status code under the total_responses_sent_by_status item, similar to this:


Now that we have some good application-level metrics being recorded, there is the whole question of what should you do with them?
The answer to this will be different from project-to-project.

In other projects, you might want to write a script to periodically fetch the JSON data from the GET /debug/vars endpoint and carry out further analysis. This might include functionality to alert you if something appears to be abnormal.


, you might want to use a tool like Prometheus to fetch and visualize the data from the endpoint, and display graphs of the metrics in real-time.

There are a lot of different options, and the right thing to do really depends on the needs of your project and business. But in all cases, using the expvar package to collect and publish the metrics gives you a great platform from which you can integrate any external monitoring, alerting or visualization tools.

### 20. Building, Versioning and Quality Control

we’re going shift our focus from writing code to managing and maintaining our project, and take steps to help automate common tasks and prepare our API for deployment.


•  Use a makefile to automate common tasks in your project, such as creating and executing migrations.
•  Carry out quality control checks of your code using the go vet and staticcheck tools.
•  Vendor third-party packages, in case they ever become unavailable in the future.

•  Build and run executable binaries for your applications, reduce their size, and cross-compile binaries for different platforms.
•  Burn-in a version number and build time to your application when building the binary.
•  Leverage Git to generate automated version numbers as part of your build process.

### 20.1. Creating and Using Makefiles

we’re going to look at how to use the GNU make utility and makefiles to help automate common tasks in your project.


On Windows machines you can install make using the Chocolatey package manager with the command:
> choco install make

A makefile is essentially a text file which contains one or more rules that the make utility can run. Each rule has a target and contains a sequence of sequential commands which are executed when the rule is run. 

Important: Please note that each command in a makefile rule must start with a tab character, not spaces. If you happen to be reading this in PDF format, the tab will appear as a single space character in the snippet above, but please make sure it is a tab character in your own code.

Make sure that the Makefile is saved, and then you can execute a specific rule by running $ make <target> from your terminal.

Great, that’s worked well. When we type make run, the make utility looks for a file called Makefile or makefile in the current directory and then executes the commands associated with the run target.


If you want, it’s possible to suppress commands from being echoed by prefixing them with the @ character.

@echo 'Running up migrations...'
	migrate -path ./migrations -database ${GREENLIGHT_DB_DSN} up
Notice here how we’ve used the @ character in the up rule to prevent the echo command from being echoed itself when running?


Likewise, if you want, you can also try running make psql to connect to the greenlight database with psql.

this rule we’ll pass the name of the migration files as an argument, similar to this:
$ make migration name=create_example_table
The syntax to access the value of named arguments is exactly the same as for accessing environment variables. So, in the example above, we could access the migration file name via ${name} in our makefile.

The make documentation suggests using lower case letters for variable names that only serve an internal purpose in the makefile, and using upper case variable names otherwise.

As your makefile continues to grow, you might want to start namespacing your target names to provide some differentiation between rules and help organize the file. For example, in a large makefile rather than having the target name up it would be clearer to give it the name db/migrations/up instead.
I recommend using the / character as a namespace separator, rather a period, hyphen or the : character. In fact, the : character should be strictly avoided in target names as it can cause problems when using target prerequisites (something that we’ll cover in a moment).


A nice feature of using the / character as the namespace separator is that you get tab completion in the terminal when typing target names. For example, if you type make db/migrations/ and then hit tab on your keyboard the remaining targets under the namespace will be listed.

Let’s leverage this functionality to ask the user for confirmation to continue before executing our db/migrations/up rule.

# Create the new confirm target.
confirm:
	@echo -n 'Are you sure? [y/N] ' && read ans && [ $${ans:-N} = y ]

Let’s try this out and see what happens when we enter y:


Another small thing that we can do to make our makefile more user-friendly is to include some comments and help functionality. Specifically, we’ll prefix each rule in our makefile with a comment in the following format:
## <example target call>: <help text>

Then we’ll create a new help rule which parses the makefile itself, extracts the help text from the comments using sed, formats them into a table and then displays them to the user.

## help: print this help message
help:
	@echo 'Usage:'
	@sed -n 's/^##//p' ${MAKEFILE_LIST} | column -t -s ':' |  sed -e 's/^/ /'


Note: MAKEFILE_LIST is a special variable which contains the name of the makefile being parsed by make.


And if you now execute the help target, you should get a response which lists all the available targets and the corresponding help text. Similar to this:


I should also point out that positioning the help rule as the first thing in the Makefile is a deliberate move. If you run make without specifying a target then it will default to executing the first rule in the file.

So this means that if you try to run make without a target you’ll now be presentedwith the help information, like so:

If you’re using make primarily to execute actions, like we are, then this can cause aproblem if there is a file in your project directory with the same path as a targetname.

$ make run/api 
make: 'run/api' is up to date.
Because we already have a file on disk at ./run/api, the make tool considers this rule to have already been executed and so returns the message that we see above without taking any further action.

To work around this, we can declare our makefile targets to be phony targets:
A phony target is one that is not really the name of a file; rather it is just a name for a rule to be executed.

.PHONY: target
target: prerequisite-target-1 prerequisite-target-2 ...
	command

Let’s go ahead and update out Makefile so that all our rules have phony targets, like so:
 File: Makefile
## help: print this help message
.PHONY: help
help:
	@echo 'Usage:'
	@sed -n 's/^##//p' ${MAKEFILE_LIST} | column -t -s ':' |  sed -e 's/^/ /'


but in practice not declaring a target as phony when it actually is can lead to bugs or confusing behavior. For example, imagine if in the future someone unknowingly creates a file called confirm in the root of the project directory. This would mean that our confirm rule is never executed, which in turn would lead to dangerous or destructive rules being executed without confirmation.


To avoid this kind of bug, if you have a makefile rule which carries out an action (rather than creating a file) then it’s best to get into the habit of declaring it as phony.

All in all, this is shaping up nicely. Our makefile is starting to contain some helpful functionality, and we’ll continue to add more to it over the next few chapters of this book.

creating a makefile in the root of a project directory is normally one of the very first things I do when starting a project. I find that using a makefile for common tasks helps save both typing and mental overhead during development, and — in the longer term — it acts as a useful entry point and a reminder of how things work when you come back to a project after a long break.


### 20.2. Managing Environment Variables

Instead, we can update our makefile so that the DSN value from the GREENLIGHT_DB_DSN environment variable is passed in as part of the rule.

## run/api: run the cmd/api application
.PHONY: run/api
run/api:
	go run ./cmd/api -db-dsn=${GREENLIGHT_DB_DSN}

This is a small change but a really nice one, because it means that the default configuration values for our application no longer change depending on the operating environment. 

Hint: If you’re not comfortable with the DSN value (which contains a password) being displayed on your screen when you type make run/api, remember that you can use the @ character in your makefile to suppress that command from being echoed.


Using a .envrc file
If you like, you could also remove the GREENLIGHT_DB_DSN environment variable from your $HOME/.profile or $HOME/.bashrc files, and store it in a .envrc file in the root of your project directory instead.

File: .envrc
export GREENLIGHT_DB_DSN=postgres://greenlight:pa55word@localhost/greenlight
You can then use a tool like direnv to automatically load the variables from the .envrc file into your current shell, or alternatively, you can add an include command at the top of your Makefile to load them instead. Like so:
 File: Makefile
# Include variables from the .envrc file
include .envrc

This approach is particularly convenient in projects where you need to make frequent changes to your environment variables, because it means that you can just edit the .envrc file without needing to reboot your computer or run source after each change.

### 20.3. Quality Controlling Code

we’re going to focus on adding an audit rule to our Makefile to check, test and tidy up our codebase automatically.

•  Use the go mod tidy command to prune any unused dependencies from the go.mod and go.sum files, and add any missing dependencies.

•  Use the go mod verify command to check that the dependencies on your computer (located in your module cache located at $GOPATH/pkg/mod) haven’t been changed since they were downloaded and that they match the cryptographic hashes in your go.sum file. Running this helps ensure that the dependencies being used are the exact ones that you expect.

•  Use the go fmt ./... command to format all .go files in the project directory, according to the Go standard. This will reformat files ‘in place’ and output the names of any changed files.


•  Use the go vet ./... command to check all .go files in the project directory. The go vet tool runs a variety of analyzers which carry out static analysis of your code and warn you about things which might be wrong but won’t be picked up by the compiler — such as unreachable code, unnecessary assignments, and badly-formed build tags.


•  Use the third-party staticcheck tool to carry out some additional static analysis checks.

go: downloading golang.org/x/mod v0.3.0
$ which staticcheck
/home/alex/go/bin/staticcheck
Note: If nothing is found by the which command, check that your $GOPATH/bin directory is on your system path. If you’re not sure where your $GOPATH directory is, you can find out by running go env GOPATH from your terminal.


let’s also add some comment blocks to help organize and clarify the purpose of our different makefile rules

audit:
	@echo 'Tidying and verifying module dependencies...'
	go mod tidy
	go mod verify
	@echo 'Formatting code...'
	go fmt ./...
	@echo 'Vetting code...'
	go vet ./...
	staticcheck ./...
	@echo 'Running tests...'
	go test -race -vet=off ./...


Now that’s done, all you need to do is type make audit to run these checks before you commit any code changes into your version control system or build any binaries.

•  Creating an end-to-end test for the GET /v1/healthcheck endpoint to verify that the headers and response body are what you expect.

### 20.4. Module Proxies and Vendoring

One of the risks of using third-party packages in your Go code is that the package repository may cease to be available. 

Fortunately, Go provides two ways in which we can mitigate this risk: module proxies and vendoring.

In version 1.13, Go began to support module proxies (also known as module mirrors) by default. These are services which mirror source code from the original, authoritative, repositories 

Go ahead and run the go env command on your machine to print out the settings for your Go operating environment. Your output should look similar to this:


The important thing to look at here is the GOPROXY setting, which contains a comma-separated list of module mirrors. By default it has the following value:
GOPROXY="https://proxy.golang.org,direct"

Whenever you fetch a package using the go command — either with go get or one of the go mod * commands — it will first attempt to retrieve the source code from this mirror.


If the mirror already has a stored copy of the source code for the required package and version number, then it will return this code immediately in a zip file. Otherwise, if it’s not already stored, then the mirror will attempt to fetch the code from the authoritative repository, proxy it onwards to you, and store it for future use.

If the mirror can’t fetch the code at all, then it will return an error response and the go tool will fall back to fetching a copy directly from the authoritative repository (thanks to the direct directive in the GOPROXY setting).

•  The https://proxy.golang.org module mirror typically stores packages long-term, thereby providing a degree of protection in case the original repository disappears from the internet.

•  It’s not possible to override or delete a package once it’s stored in the https://proxy.golang.org module mirror. This can help prevent any bugs or problems which might arise if a package author (or an attacker) releases an edited version of the package with the same version number.


•  Fetching modules from the https://proxy.golang.org mirror can be much faster than getting them from the authoritative repositories.

But if you don’t want to use the module mirror provided by Google, or you’re behind a firewall that blocks it, there are other alternatives like https://goproxy.io and the Microsoft-provided https://athens.azurefd.net that you can try instead. Or you can even host your own module mirror using the open-source Athens and goproxy projects.


For example, if you wanted to switch to using https://goproxy.io as the primary mirror, then fall back to using https://proxy.golang.org as a secondary mirror, then fall back to a direct fetch, you could update your GOPROXY setting like so:
$ export GOPROXY=https://goproxy.io,https://proxy.golang.org,direct

Go’s module mirror functionality is great, and I recommend using it. But it isn’t a silver bullet for all developers and all projects.


Or maybe you need to routinely work in an environment without network access. In those scenarios you probably still want to mitigate the risk of a disappearing dependency, but using a module mirror isn’t possible or appealing.

proxy.golang.org does not save all modules forever. There are a number of reasons for this, but one reason is if proxy.golang.org is not able to detect a suitable license. In this case, only a temporarily cached copy of the module will be made available, and may become unavailable if it is removed from the original source and becomes outdated.

So, for these reasons, it can still be sensible to vendor your project dependencies using the go mod vendor command. Vendoring dependencies in this way basically stores a complete copy of the source code for third-party packages in a vendor folder in your project.


•  We’ve added the vendor rule as a prerequisite to audit, which means it will automatically be run each time we execute the audit rule.

•  The go mod tidy command will make sure the go.mod and go.sum files list all the necessary dependencies for our project (and no unnecessary ones).

•  The go mod verify command will verify that the dependencies stored in your module cache (located on your machine at $GOPATH/pkg/mod) match the cryptographic hashes in the go.sum file.


•  The go mod vendor command will then copy the necessary source code from your module cache into a new vendor directory in your project root.


Once that’s completed, you should see that a new vendor directory has been created containing copies of all the source code along with a modules.txt file. The directory structure in your vendor folder should look similar to this:
$ tree -L 3 ./vendor/
./vendor/
├── github.com
│   ├── felixge
│   │   └── httpsnoop
│   ├── go-mail
│   │   └── mail

Now, when you run a command such as go run, go test or go build, the go tool will recognize the presence of a vendor folder and the dependency code in the vendor folder will be used — rather than the code in the module cache on your local machine.


Note: If you want to confirm that it’s really the vendored dependencies being used, you can run go clean -modcache to remove everything from your local module cache. When you run the API again, you should find that it still starts up correctly without needing to re-fetch the dependencies from the Go module mirror.

Because all the dependency source code is now stored in your project repository itself, it’s easy to check it into Git (or an alternative version control system) alongside the rest of your code. This is reassuring because it gives you complete ownership of all the code used to build and run your applications, kept under version control.


The downside of this, of course, is that it adds size and bloat to your project repository. This is of particular concern in projects that have a lot of dependencies and the repository will be cloned a lot, such as projects where a CI/CD system clones the repository with each new commit.

Let’s also take a quick look in the vendor/modules.txt file that was created.

# github.com/felixge/httpsnoop v1.0.1
## explicit
github.com/felixge/httpsnoop
# github.com/go-mail/mail/v2 v2.3.0
## explicit

his vendor/modules.txt file is essentially a manifest of the vendored packages and their version numbers. When vendoring is being used, the go tool will check that the module version numbers in modules.txt are consistent with the version numbers in the go.mod file. If there’s any inconsistency, then the go tool will report an error.


Note: It’s important to point out that there’s no easy way to verify that the checksums of the vendored dependencies match the checksums in the go.sum file. 

To mitigate that, it’s a good idea to run both go mod verify and go mod vendor regularly. Using go mod verify will verify that the dependencies in your module cache match the go.sum file, and go mod vendor will copy those same dependencies from the module cache into your vendor folder. This is one of the reasons why our make vendor rule is setup to run both commands, and why we’ve also included it as a prerequisite to the make audit rule.

Lastly, you should avoid making any changes to the code in the vendor directory. Doing so can potentially cause confusion (because the code would no longer be consistent with the original version of the source code) and — besides — running go mod vendor will overwrite any changes you make each time you run it. If you need to change the code for a dependency, it’s far better to fork it and import the forked version instead.

with Caddy as a reverse-proxy in-front of it. This means that, as far as our API is concerned, all the requests it receives will be coming from a single IP address (the one running the Caddy instance). In turn, that will cause problems for our rate limiter middleware which limits access based on IP address.

Fortunately, like most other reverse proxies, Caddy adds an X-Forwarded-For header to each request. This header will contain the real IP address for the client.
Although we could write the logic to check for the presence of an X-Forwarded-For header and handle it ourselves, I recommend using the realip package to help with this. This package retrieves the client IP address from any X-Forwarded-For or X-Real-IP headers, falling back to use r.RemoteAddr if neither of them are present.

            // Use the realip.FromRequest() function to get the client's real IP address.

            ip := realip.FromRequest(r)

If you try to run the API application again now, you should receive an error message similar to this:
$ make run/api 
go: inconsistent vendoring in /home/alex/Projects/greenlight:
        github.com/tomasen/realip@v0.0.0-20180522021738-f0c99a92ddce: is explicitly 
            required in go.mod, but not marked as explicit in vendor/modules.txt


Essentially what’s happening here is that Go is looking for thegithub.com/tomasen/realip package in our vendor directory, but at the moment thatpackage doesn’t exist in there.

To solve this, you’ll need to manually run either the make vendor or make audit commands,

Once that’s done, everything should work correctly again:

Most of the go tools support the ./... wildcard pattern, like go fmt ./..., go vet ./... and go test ./.... This pattern matches the current directory and all sub-directories, excluding the vendor directory.


Generally speaking, this is useful because it means that we’re not formatting, vettingm or testing the code in our vendor directory unnecessarily — and our make audit rule won’t fail due to any problems that might exist within those vendored packages.

### 20.5. Building Binaries

we’re going to focus on explaining how to build an executable binary that you can distribute and run on other machines without needing the Go toolchain installed.


To build a binary we need to use the go build command. As a simple example, usage looks like this:
$ go build -o=./bin/api ./cmd/api
When we run this command, go build will compile the cmd/api package (and any dependent packages) into files containing machine code, and then link these together to form executable binary. In the command above, the executable binary will be output to ./bin/api.

And you should be able to run this executable to start your API application, passing in any command-line flag values as necessary. For example:
$ ./bin/api -port=4040 -db-dsn=postgres://greenlight:pa55word@localhost/greenlight

Reducing binary size
If you take a closer look at the executable binary you’ll see that it weighs in at 10470419 bytes (about 10.5MB).

Note: If you’re following along, your binary size may be slightly different. It depends on your operating system, and the exact version of Go and the third-party dependencies that you’re using.

It’s possible to reduce the binary size by around 25%!b(MISSING)y instructing the Go linker to strip the DWARF debugging information and symbol table from the binary. We can do this as part of the go build command by using the linker flag -ldflags="-s" as follows:

It’s important to be aware that stripping the DWARF information and symbol table will make it harder to debug an executable using a tool like Delve or gdb. But, generally, it’s not often that you’ll need to do this — and there’s even an open proposal from Rob Pike to make omitting DWARF information the default behavior of the linker in the future.


 This is particularly useful if you’re developing on one operating system and deploying on another.

To see a list of all the operating system/architecture combinations that Go supports, you can run the go tool dist list command like so:
$ go tool dist list

And you can specify the operating system and architecture that you want to create the binary for by setting GOOS and GOARCH environment variables when running go build. For example:
$ GOOS=linux GOARCH=amd64 go build {args}

So let’s update our make build/api rule so that it creates two binaries — one for use on your local machine, and another for deploying to the Ubuntu Linux server.

## build/api: build the cmd/api application
.PHONY: build/api
build/api:
	@echo 'Building cmd/api...'
	go build -ldflags='-s' -o=./bin/api ./cmd/api
	GOOS=linux GOARCH=amd64 go build -ldflags='-s' -o=./bin/linux_amd64/api ./cmd/api


As a general rule, you probably don’t want to commit your Go binaries into version control alongside your source code as they will significantly inflate the size of your repository.

let’s quickly add an additional rule to the .gitignore file which instructs Git to ignore the contents of the bin directory.

$ echo 'bin/' >> .gitignore
$ cat .gitignore
.envrc
bin/

It’s important to note that the go build command caches build output in the Go build cache. This cached output will be reused again in future builds where appropriate, which can significantly speed up the overall build time for your application.
If you’re not sure where your build cache is, you can check by running the go env GOCACHE command:
$ go env GOCACHE
/home/alex/.cache/go-build


You should also be aware that the build cache does not automatically detect any changes to C libraries that your code imports with cgo. So, if you’ve changed a C library since the last build, you’ll need to use the -a flag to force all packages to be rebuilt when running go build. Alternatively, you could use go clean to purge the cache:
$ go build -a -o=/bin/foo ./cmd/foo        # Force all packages to be rebuilt


Note: If you ever run go build on a non-main package, the build output will be stored in the build cache so it can be reused, but no executable will be produced.

### 20.6. Managing and Automating Version Numbers

we’re going take steps to make it easier to view and manage this version number, and also explain how you can generate version numbers automatically based on Git commits and integrate them into your application.

Let’s start by updating our application so that we can easily check the version number by running the binary with a -version command-line flag, similar to this:
$ ./bin/api -version
Version:        1.0.0


    // If the version flag value is true, then print out the version number and

    // immediately exit.

    if *displayVersion {
        fmt.Printf("Version:\t%!s(MISSING)\n", version)
        os.Exit(0)
    }

You should find that it prints out the version number and then exits, similar to this:


Remember: Boolean command-line flags without a value are interpreted as having the value true. So running our application with -version is the same as running it with -version=true.


Let’s extend this further, so that the -version flag forces our application to print out the version number and the exact time that the executable binary was built. Similar to this:
$ ./bin/api -version
Version:        1.0.0
Build time:     2021-02-15T13:54:14+01:00


This is a really interesting change, because the build time is a completely dynamic value and outside the control of our application code. We don’t know the build time in advance of calling go build, and therefore it’s not something that we can define in our source code in the normal way.


// Create a buildTime variable to hold the executable binary build time. Note that this

// must be a string type, as the -X linker flag will only work with string variables.

var buildTime string

And then we need to make a couple of changes to our Makefile. Specifically, we need to:
1.  Use the unix date command to generate the current time and store it in a current_time variable.
2.  Update the build/api rule to ‘burn in’ the current time to our main.buildTime variable using the -X linker flag.

current_time = $(shell date --iso-8601=seconds)

## build/api: build the cmd/api application
.PHONY: build/api
build/api:
	@echo 'Building cmd/api...'
	go build -ldflags='-s -X main.buildTime=${current_time}' -o=./bin/api ./cmd/api

current_time = $(shell date --iso-8601=seconds)
linker_flags = '-s -X main.buildTime=${current_time}'

create automated version numbers for your application based on your Git commits.

In this case, we can see that git describe returns an abbreviated version of the commit hash. We’re also using the --dirty flag, which means that the descriptor will be suffixed with "-dirty" if there are any uncommitted changes in the repository. For example 0f8573a-dirty.

// Remove the hardcoded version number and make version a variable instead of a constant.

var (
	buildTime string
	version   string
)

git_description = $(shell git describe --always --dirty)
linker_flags = '-s -X main.buildTime=${current_time} -X main.version=${git_description}'

$ ./bin/api -version
Version:        76c976a
Build time:     2021-04-18T18:43:32+02:00
We can see that our binary is now reporting that it was been built from a clean version of the repository with the commit hash 76c976a. Let’s cross check this against the git log output for the project:


That’s looking good — the commit hash in our Git history aligns perfectly with our application version number. And that means it’s now easy for us to identify exactly what code a particular binary contains — all we need to do is run the binary with the -version flag and then cross-reference it against the Git repository history.

In some projects you may want to annotate certain Git commits with tags, often to denote a formal release number.
To illustrate this, let’s add the v1.0.0 tag to our latest commit like so:
$ git tag v1.0.0
$ git log
commit 76c976a07d0fce3d813f3a4748574f81b1e24226 (HEAD -> master, tag: v1.0.0)


This will force the output to be in the following format:
{tag}-{number of additional commits}-g{abbreviated commit hash}
Let’s take a quick look at what this currently looks like for our project:
$ git describe --always --dirty --tags --long
v1.0.0-0-g76c976a
So, in this case, we can see from the version number that the repository is based on v1.0.0, has 0 additional commits on top of that, and the latest commit hash is 76c976a. The g character which prefixes the commit hash stands for ‘git’, and exists to help distinguish the hash from any hashes generated by other version-control systems.

The version number has now been changed to v1.0.0-1-gf27fd0f, indicating that the binary was built using the repository code from commit f27fd0f, which is 1 commit ahead of the v1.0.0 tag.

### 21. Deployment and Hosting

we’re going to look at how to deploy our API application to a production server and expose it on the internet.


using standard Linux tooling to manage server configuration and deployment.

We’ll also be automating the server configuration and deployment process as much as possible, so that it’s easy to make continuous deployments and possible to replicate the server again in the future if you need to.

we’ll be using Digital Ocean as the hosting provider in this book. 

In terms of infrastructure and architecture, we’ll run everything on a single Ubuntu Linux server. Our stack will consist of a PostgreSQL database and the executable binary for our Greenlight API, operating in much the same way that we have seen so far in this book.

run Caddy as a reverse proxy in front of the Greenlight API.


Using Caddy has a couple of benefits. It will automatically handle and terminate HTTPS connections for us — including automatically generating and managing TLS certificates via Let’s Encrypt — and we can also use Caddy to easily restrict internet access to our metrics endpoint.


•  Automate the configuration of the server — including creating user accounts, configuring the firewall and installing necessary software.
•  Automate the process of updating your application and deploying changes to the server.

•  How to run your application as a background service using systemd, as a non-root user.
•  Use Caddy as a reverse proxy in front of your application to automatically manage TLS certificates and handle HTTPS connections.


### 21.1. Creating a Digital Ocean Droplet

The first thing that we need to do is commission a server on Digital Ocean to host our application.

Click the “Skip this” link at the bottom of the screen to go straight to your account control panel, which should look like this:


In order to log in to droplets in your Digital Ocean account you’ll need a SSH keypair.

Suggestion: If you’re unfamiliar with SSH, SSH keys, or public-key cryptography generally, then I recommend reading through the first half of this guide to get an overview before continuing.

$ ssh-keygen -t rsa -b 4096 -C "greenlight@greenlight.alexedwards.net" -f $HOME/.ssh/id_rsa_greenlight

And if you run the ssh-add -l command, you should see your new SSH key listed in the output, similar to this:

If you don’t see your key listed, then please add it to your SSH agent like so:
$ ssh-add $HOME/.ssh/id_rsa_greenlight

give it a memorable name, and submit the form, similar to the screenshot below.

There are a couple of ways that you can do this. It’s possible to do so programmatically via the Digital Ocean API or using the official command-line tool, and if you need to create or manage a lot of servers then I recommend using these.

Or alternatively, it’s possible to create a droplet manually via your control panel on the Digital Ocean website.

Under the Authentication section, make sure that SSH keys is selected as the authentication method and that the SSH key that you just uploaded is checked.

Then we get to the final configuration options.

You can go ahead and type exit to terminate the SSH connection and return to the terminal on your local machine, like so:


### 21.2. Server Configuration and Installing Software

Note: In this chapter we’ll use a regular bash script to perform the setup tasks, but if you’re happy using a configuration management tool such as Ansible, then please feel free to implement the same setup process using that instead.

•  Update all packages on the server, including applying security updates.


Set the server timezone (in my case I’ll set it to America/New_York) and installsupport for all locales.

•  Create a greenlight user on the server, which we can use for day-to-day maintenance and for running our API application (rather than using the all-powerful root user account). We should also add the greenlight user to the sudo group, so that it can perform actions as root if necessary.


•  Copy the root user’s $HOME/.ssh directory into the greenlight users home directory. This will enable us to authenticate as the greenlight user using the same SSH key pair that we used to authenticate as the root user. We should also force the greenlight user to set a new password the first time they log in.

•  Configure firewall settings to only permit traffic on ports 22 (SSH), 80 (HTTP) and 443 (HTTPS). We’ll also install fail2ban to automatically temporarily ban an IP address if it makes too many failed SSH login attempts.

# Update all software packages. Using the --force-confnew flag means that configuration 
# files will be replaced if newer ones are available.
apt update
apt --yes -o Dpkg::Options::="--force-confnew" upgrade

# Set the system timezone and install all locales.
timedatectl set-timezone ${TIMEZONE}
apt --yes install locales-all

# Add the new user (and give them sudo privileges).
useradd --create-home --shell "/bin/bash" --groups sudo "${USERNAME}"

# Force a password to be set for the new user the first time they log in.
passwd --delete "${USERNAME}"
chage --lastday 0 "${USERNAME}"

rsync --archive --chown=${USERNAME}:${USERNAME} /root/.ssh /home/${USERNAME}

# Install the migrate CLI tool.
curl -L https://github.com/golang-migrate/migrate/releases/download/v4.14.1/migrate.linux-amd64.tar.gz | tar xvz
mv migrate.linux-amd64 /usr/local/bin/migrate

# Install PostgreSQL.
apt --yes install postgresql

# Set up the greenlight DB and create a user account with the password entered earlier.
sudo -i -u postgres psql -c "CREATE DATABASE greenlight"
sudo -i -u postgres psql -d greenlight -c "CREATE EXTENSION IF NOT EXISTS citext"
sudo -i -u postgres psql -d greenlight -c "CREATE ROLE greenlight WITH LOGIN PASSWORD '${DB_PASSWORD}'"

 rsync the contents of the /remote/setup folder to the root user’s home directory on the droplet. Remember to replace the IP address with your own!


$ rsync -rP --delete ./remote/setup root@45.55.49.87:/root
sending incremental file list
setup/
setup/01.sh

Note: In this rsync command the -r flag indicates that we want to copy the contents of ./remote/setup recursively, the -P flag indicates that we want to display progress of the transfer, and the --delete flag indicates that we want to delete any extraneous files from destination directory on the droplet.

We’ll use the -t flag to force pseudo-terminal allocation, which is useful when executing screen-based programs on a remote machine.


To make connecting to the droplet a bit easier, and so we don’t have to remember the IP address, let’s quickly add a makefile rule for initializing a SSH connection to the droplet as the greenlight user. 

production/connect:
	ssh greenlight@${production_host_ip}
Once that’s in place, you can then connect to your droplet whenever you need to by simply typing make production/connect:


If you need to make further changes to your droplet configuration or settings, you can create an additional remote/setup/02.sh script and then execute it in the following way:
$ rsync -rP --delete ./remote/setup greenlight@45.55.49.87:~
$ ssh -t greenlight@45.55.49.87 "sudo bash /home/greenlight/setup/02.sh"


### 21.3. Deployment and Executing Migrations

At a very high-level, our deployment process will consist of three actions:
1.  Copying the application binary and SQL migration files to the droplet.
2.  Executing the migrations against the PostgreSQL database on the droplet.
3.  Starting the application binary as a background service.


First, it uses the rsync command to copy the ./bin/linux_amd64/api executable binary (the one specifically built for Linux) and the ./migrations folder into the home directory for the greenlight user on the droplet.


Because the $ character has a special meaning in makefiles, we are escaping it in the command above by prefixing it with an additional dollar character like $$. 

Let’s quickly connect to our droplet and verify that everything has worked.

 You should see it gracefully shutdown like so:

### 21.4. Running the API as a Background Service

the next step is to configure it to run as a background service, including starting up automatically when the droplet is rebooted.
There are a few different tools we could use to do this, but in this book we will use systemd — a collection of tools for managing services that ships with Ubuntu (and many other Linux distributions).

In order to run our API application as a background service, the first thing we need to do is make a unit file, which informs systemd how and when to run the service.

And then add the following markup:
 File: remote/production/api.service
[Unit]
# Description is a human-readable name for the service.
Description=Greenlight API service

# Wait until PostgreSQL is running and the network is "up" before starting the service.
After=postgresql.service
After=network-online.target
Wants=network-online.target

# Configure service start rate limiting. If the service is (re)started more than 5 times 
# in 600 seconds then don't permit it to start anymore.
StartLimitIntervalSec=600
StartLimitBurst=5

[Service]
# Execute the API binary as the greenlight user, loading the environment variables from
# /etc/environment and using the working directory /home/greenlight.
Type=exec
User=greenlight
Group=greenlight
EnvironmentFile=/etc/environment
WorkingDirectory=/home/greenlight
ExecStart=/home/greenlight/api -port=4000 -db-dsn=${GREENLIGHT_DB_DSN} -env=production

# Automatically restart the service after a 5-second wait if it exits with a non-zero 
# exit code. If it restarts more than 5 times in 600 seconds, then the rate limit we
# configured above will be hit and it won't be restarted anymore.
Restart=on-failure
RestartSec=5

[Install]
# Start the service automatically at boot time (the 'multi-user.target' describes a boot
# state when the system will accept logins).
WantedBy=multi-user.target

1.  To ‘install’ the file, we need to copy it into the /etc/systemd/system/ folder on our droplet.
2.  Then we need to run the systemctl enable api command on our droplet to make systemd aware of the new unit file and automatically enable the service when the droplet is rebooted.
3.  Finally, we need to run systemctl restart api to start the service.

sudo mv ~/api.service /etc/systemd/system/ \
		&& sudo systemctl enable api \
		&& sudo systemctl restart api \

Great! This confirms that our api service is running successfully in the background and, in my case, that it has the PID (process ID) 6997.

Restarting after reboot
By running the systemctl enable api command in our makefile after we copied across the systemd unit file, our API service should be started up automatically after the droplet is rebooted.


If you’re not planning to run your application behind a reverse proxy, and want to listen for requests directly on port 80 or 443, you’ll need to set up your unit file so that the service has the CAP_NET_BIND_SERVICE capability (which will allow it to bind to a restricted port).

It’s possible to view the logs for your background service using the journalctl command, like so:
$ sudo journalctl -u api
-- Logs begin at Sun 2021-04-18 14:55:45 EDT, end at Mon 2021-04-19 03:39:55 EDT. --
Apr 19 03:24:35 greenlight-production systemd[1]: Starting Greenlight API service...


The journalctl command is really powerful and offers a wide variety of parameters that you can use to filter your log messages and customize the formatting. This article provides a great introduction to the details of journalctl and is well worth a read.

### 21.5. Using Caddy as a Reverse Proxy

So the next step in setting up our production environment is to configure Caddy to act as a reverse proxy and forward any HTTP requests that it receives onward to our API.


The simplest way to configure Caddy is to create a Caddyfile — which contains a series of rules describing what we want Caddy to do. 

Blocking access to application metrics


As we mentioned earlier, it’s really not a good idea for this sensitive information to be publicly accessible.
Fortunately, it’s very easy to block access to this by adding a new respond directive to our Caddyfile like so:
 File: remote/production/Caddyfile
http://45.55.49.87 {
  respond /debug/* "Not Permitted" 403
  reverse_proxy localhost:4000
}

With this new directive we’re instructing Caddy to send a 403 Forbidden response for all requests which have a URL path beginning /debug/.


Although the metrics are no longer publicly accessible, you can still access them by connecting to your droplet via SSH and making a request to http://localhost:4000/debug/vars.

Or alternatively, you can open a SSH tunnel to the droplet and view them using a web browser on your local machine. For example, you could open an SSH tunnel between port 4000 on the droplet and port 9999 on your local machine by running the following command (make sure to replace both IP addresses with your own droplet IP):
$ ssh -L :9999:45.55.49.87:4000 greenlight@45.55.49.87
While that tunnel is active, you should be able to visit http://localhost:9999/debug/vars in your web browser and see your application metrics, like so:


Note: If you’re not sure how to alter your DNS records, your domain name registrar should provide guidance and documentation.


Now that we have a domain name set up we can utilize one of Caddy’s headline features: automatic HTTPS.
Caddy will automatically handle provisioning and renewing TLS certificates for your domain via Let’s Encrypt, as well as redirecting all HTTP requests to HTTPS. It’s simple to set up, very robust, and saves you the overhead of needing to keep track of certificate renewals manually.

# Set the email address that should be used to contact you if there is a problem with 
# your TLS certificates.

For the final time, deploy this Caddyfile update to your droplet…

And then when you refresh the page in your web browser, you should find that it is automatically redirected to a HTTPS version of the page.


Sometimes it can take Caddy a moment to complete the certificate set up for the very first time.


Lastly, if you want, you can use curl to try making a HTTP request to the application. You should see that this issues a 308 Permanent Redirect to the HTTPS version of the application

### 22.2. Creating Additional Activation Tokens

The token will be sent to them in an email, and they can then submit the token to the PUT /v1/users/activated endpoint to activate, in exactly the same way as if they received the token in their welcome email.


### 22.3. Authentication with JSON Web Tokens

The JWT approach is more complex, ultimately requires the same number of database lookups, and we lose the ability to revoke tokens. For those reasons, it doesn't make much sense to use them here. But I still want to explain the pattern for two reasons:


•  It’s worth understanding how they work in case you need to implement APIs that require delegated authentication, which is a scenario that they’re useful in.


JWTs are a type of stateless token. They contain a set of claims which are signed (using either a symmetric or asymmetric signing algorithm) and then encoded using base-64.

There are a few different packages available which make working with JWTs relatively simple in Go. In most cases the pascaldekloe/jwt package is a good choice — it has a clear and simple API, and is designed to avoid a couple of the major JWT security vulnerabilities by default.


Whereas if your JWT is going to be consumed by the same application that created it, then the appropriate choice is a (simpler and faster) symmetric-key algorithm like HMAC-SHA256 with a random secret key. This is what we’ll use for our API.


    // Create a JWT claims struct containing the user ID as the subject, with an issued

    // time of now and validity window of the next 24 hours. We also set the issuer and

    // audience to a unique identifier for our application.

    var claims jwt.Claims
    claims.Subject = strconv.FormatInt(user.ID, 10)
    claims.Issued = jwt.NewNumericTime(time.Now())
    claims.NotBefore = jwt.NewNumericTime(time.Now())
    claims.Expires = jwt.NewNumericTime(time.Now().Add(24 * time.Hour))
    claims.Issuer = "greenlight.alexedwards.net"
    claims.Audiences = []string{"greenlight.alexedwards.net"}

    // Sign the JWT claims using the HMAC-SHA256 algorithm and the secret key from the

    // application config. This returns a []byte slice containing the JWT as a base64-

    // encoded string.

    jwtBytes, err := claims.HMACSign(jwt.HS256, []byte(app.config.jwt.secret))


    // Convert the []byte slice to a string and return it in a JSON response.

    err = app.writeJSON(w, http.StatusCreated, envelope{"authentication_token": string(jwtBytes)}, 

If you’re curious, you can decode the base64-encoded JWT data. You should see that the content of the claims matches the information that you would expect, similar to this:
{"alg":"HS256"}{"iss":"greenlight.alexedwards.net","sub":"7","aud":["greenlight.alexedwards.net"],
"exp":1618938264.819205,"nbf":1618851864.819205,"iat":1618851864.8192048}...


Next you’ll need to update the authenticate() middleware to accept JWTs in an Authorization: Bearer <jwt> header, verify the JWT, and extract the user ID from the subject claim.

•  Check that the signature of the JWT matches the contents of the JWT, given our secret key. This will confirm that the token hasn’t been altered by the client.
•  Check that the current time is between the “not before” and “expires” times for the JWT.
•  Check that the JWT “issuer” is "greenlight.alexedwards.net".
•  Check that "greenlight.alexedwards.net" is in the JWT “audiences”.


        // Parse the JWT and extract the claims. This will return an error if the JWT 

        // contents doesn't match the signature (i.e. the token has been tampered with)

        // or the algorithm isn't valid.

        claims, err := jwt.HMACCheck([]byte(token), []byte(app.config.jwt.secret))
        if err != nil {
            app.invalidAuthenticationTokenResponse(w, r)
            return
        }


        // Check if the JWT is still valid at this moment in time.

        if !claims.Valid(time.Now()) {
            app.invalidAuthenticationTokenResponse(w, r)
            return
        }


        // Check that the issuer is our application.

        if claims.Issuer != "greenlight.alexedwards.net" {
            app.invalidAuthenticationTokenResponse(w, r)
            return
        }

If you’re planning to put a system into production which uses JWTs, I also recommend spending some time to read and fully understand the following two articles:
•  JWT Security Best Practices
•  Critical vulnerabilities in JSON Web Token libraries

### 22.4. JSON Encoding Nuances

Nil slices in Go will be encoded to the null JSON value, whereas an empty (but not nil) slice will be encoded to an empty JSON array

The omitempty struct tag directive never considers a struct to be empty — even if all the struct fields have their zero value and you use omitempty on those fields too. It will always appear as an object in the encoded JSON. For example, the following struct:
s := struct {
    Foo struct {
        Bar string `json:",omitempty"`
    } `json:",omitempty"`
}{}

Using omitempty on a zero-value time.Time field won’t hide it in the JSON object. This is because the time.Time type is a struct behind the scenes and, as mentioned above, omitempty never considers structs to be empty.

In addition, map keys that implement the encoding.TextMarshaler interface are also supported. This means that you can also use time.Time and net.IP values as map keys out-of-the-box. 

### 22.5. JSON Decoding Nuances

When you’re decoding a JSON array into a Go array (not a slice) there are a couple of important behaviors to be aware of:
•  If the Go array is smaller than the JSON array, then the additional JSON array elements are silently discarded.
•  If the Go array is larger than the JSON array, then the additional Go array elements are set to their zero values.

Using the struct tag json:"-" on a struct field will cause it to be ignored when decoding JSON, even if the JSON input contains a corresponding key/value pair. For example:

### 22.6. Request Context Timeouts

    // Create a context.Context with a one-second timeout deadline and which has the 

    // request context as the 'parent'.

    ctx, cancel := context.WithTimeout(r.Context(), time.Second)
    defer cancel()

    // Pass the context on to the Get() method.

    example, err := app.models.Example.Get(ctx, id)

The key thing to be aware of is that the request context will be canceled if the client closes their HTTP connection. From the net/http docs:
For incoming server requests, the [request] context is canceled when the client’s connection closes, the request is canceled (with HTTP/2), or when the ServeHTTP method returns.


On the other hand, we will receive the same error message in two very different scenarios:
•  When a database query takes too long to complete, in which case we would want to log it as an error.
•  When a client closes the connection — which can happen for many innocuous reasons, such as a user closing a browser tab or terminating a process. It’s not really an error from our application’s point of view, 

For incoming server requests, the [request] context is canceled … when the ServeHTTP method returns.

So, to summarize, using the request context as the parent context for database timeouts adds quite a lot of behavioral complexity and introduces nuances that you and anyone else working on the codebase needs to be aware of. You have to ask: is it worth it?