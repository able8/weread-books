## Table of Contents
> Unknown

### Introduction

This book is hosted on github-pages.

### Architecture

You can't build a system without some idea of what you want to build. And you can't build it if you don't know theenvironment in which it will work.

### Protocol Layers

Distributed systems are hard. There are multiple computers involved, which have to be connected in some way. Programs have to be written to run on each computer in the system and they all have to co-operate to get a distributed task done.

The common way to deal with complexity is to break it down into smaller and simpler parts. These parts have their own structure, but they also have defined means of communicating with other related parts. In distributed systems, the parts are called protocol layers and they have clearly defined functions. 

### Component Distribution

A simple but effective way of decomposing many applications is to consider them as made up of three parts:
•  Presentation component
•  Application logic
•  Data access

The presentation component is responsible for interactions with the user, both displaying data and gathering input. It may be a modern GUI interface with buttons, lists, menus, etc., or an older command-line style interface, asking questions and getting answers. The details are not important at this level.

The application logic is responsible for interpreting the users' responses, for applying business rules, for preparing queries and managing responses from the their component.
The data access component is responsible for storing and retrieving data. This will often be through a database, but not necessarily.

### Points of Failure

Distributed applications run in a complex environment. This makes them much more prone to failure than standalone applications on a single computer. The points of failure include
•  The client side of the application could crash

•  The client's network card could fail
•  Network contention could cause timeouts
•  There may be network address conflicts
•  Network elements such as routers could fail
•  Transmission errors may lose messages
•  The client and server versions may be incompatible
•  The server's network card could fail

### Overview of Go language

I don't feel like writing a chapter introducing Go right now, as there are other materials already available. There are several tutorials on the Go web site:
•  Getting started
•  A Tutorial for the Go Programming Language
•  Effective Go

### Socket-level Programming

It deals with host and service addressing, and then considers TCP and UDP. It shows how to build both servers and clients using the TCP and UDP Go APIs. It also looks at raw sockets, in case you need to implement your own protocol above IP.

### IP address type

The package "net" defines many types, functions and methods of use in Go network programming. The type IP is defined as byte slices
type IP []byte

func main() {
    if len(os.Args) != 2 {
        fmt.Fprintf(os.Stderr, "Usage: %!s(MISSING) ip-addr\n", os.Args[0])
        os.Exit(1)
    }
    name := os.Args[1]

    addr := net.ParseIP(name)
    if addr == nil {
        fmt.Println("Invalid address")
    } else {
        fmt.Println("The address is ", addr.String())
    }
    os.Exit(0)
}

Many of the other functions and methods in the net package return a pointer to an IPAddr. This is simply a structure containing an IP.
type IPAddr {
    IP IP
}

A primary use of this type is to perform DNS lookups on IP host names.
func ResolveIPAddr(net, addr string) (*IPAddr, os.Error)

where net is one of "ip", "ip4" or "ip6". This is shown in the program

    addr, err := net.ResolveIPAddr("ip", name)
    if err != nil {
        fmt.Println("Resolution error", err.Error())
        os.Exit(1)
    }
    fmt.Println("Resolved address is ", addr.String())
    os.Exit(0)

The function ResolveIPAddr will perform a DNS lookup on a hostname, and return a single IP address. However, hosts may have multiple IP addresses, usually from multiple network interface cards. They may also have multiple host names, acting as aliases.
func LookupHost(name string) (addrs []string, err os.Error)

### Services

Services run on host machines. They are typically long lived and are designed to wait for requests and respond to them. There are many types of services, and there are many ways in which they can offer their services to clients. 

The type TCPAddr is a structure containing an IP and a port:
type TCPAddr struct {
    IP   IP
    Port int
}



### TCP Sockets

    service := os.Args[1]

    tcpAddr, err := net.ResolveTCPAddr("tcp4", service)
    checkError(err)

    conn, err := net.DialTCP("tcp", nil, tcpAddr)
    checkError(err)

    _, err = conn.Write([]byte("HEAD / HTTP/1.0\r\n\r\n"))
    checkError(err)

    result, err := ioutil.ReadAll(conn)
    checkError(err)

    fmt.Println(string(result))

func checkError(err error) {
    if err != nil {
        fmt.Fprintf(os.Stderr, "Fatal error: %!s(MISSING)", err.Error())
        os.Exit(1)
    }
}

The first point to note is the almost excessive amount of error checking that is going on. This is normal for networking programs: the opportunities for failure are substantially greater than for standalone programs. Hardware may fail on the client, the server, or on any of the routers and switches in the middle; communication may be blocked by a firewall; timeouts may occur due to network load; the server may crash while the client is talking to it.

The daytime service is very simple and just writes the current time to the client, closes the connection, and resumes waiting for the next client.



    for {
        conn, err := listener.Accept()
        if err != nil {
            continue
        }

        daytime := time.Now().String()
        conn.Write([]byte(daytime)) // don't care about return value
        conn.Close()                // we're finished with this client
    }

While it works, there is a significant issue with this server: it is single-threaded. While a client has a connection open to it, no other client can connect. Other clients are blocked, and will probably time out. Fortunately this is easily fixed by making the client handler a go-routine. 

// run as a goroutine
        go handleClient(conn)

// close connection on exit
    defer conn.Close()

### Controlling TCP connections

The server may wish to timeout a client if it does not respond quickly enough i.e. does not write a request to the server in time.

Both do this by
func (c *TCPConn) SetTimeout(nsec int64) os.Error

before any reads or writes on the socket.

Staying alive
A client may wish to stay connected to a server even if it has nothing to send. It can use
func (c *TCPConn) SetKeepAlive(keepalive bool) os.Error

### UDP Datagrams

In a connectionless protocol each message contains information about its origin and destination. There is no "session" established using a long-lived socket. UDP clients and servers make use of datagrams, which are individual messages containing source and destination information. There is no state maintained by these messages, unless the client or server does so. The messages are not guaranteed to arrive, or may arrive out of order.


### Server listening on multiple sockets

In Go, accomplish the same by using a different goroutine for each port. A thread will become runnable when the lower-level select() discovers that I/O is ready for this thread.


### The types Conn, PacketConn and Listener

Instead of separate dial functions for TCP and UDP, you can use a single function
func Dial(net, laddr, raddr string) (c Conn, err os.Error)

Note that this function takes a string rather than address as raddr argument, so that programs using this can avoid working out the address type first.


### Raw sockets and the type IPConn

Go allows you to build so-called raw sockets, to enable you to communicate using one of these other protocols, or even to build your own. But it gives minimal support: it will connect hosts, and write and read packets between the hosts. 

how to send a ping message to a host. Ping uses the "echo" command from the ICMP protocol. This is a byte-oriented protocol, in which the client sends a stream of bytes to another host, and the host replies. the format is:
•  The first byte is 8, standing for the echo message
•  The second byte is zero
•  The third and fourth bytes are a checksum on the entire message

    var msg [512]byte
    msg[0] = 8  // echo
    msg[1] = 0  // code 0
    msg[2] = 0  // checksum, fix later
    msg[3] = 0  // checksum, fix later
    msg[4] = 0  // identifier[0]
    msg[5] = 13 //identifier[1]
    msg[6] = 0  // sequence[0]
    msg[7] = 37 // sequence[1]
    len := 8

    check := checkSum(msg[0:len])
    msg[2] = byte(check >> 8)
    msg[3] = byte(check & 255)

### Data Serialisation

Communication between a client and a service requires the exchange of data. This data may be highly structured, but has to be serialised for transport. This chapter looks at the basics of serialisation and then considers several techniques supported by Go APIs.


Messages are sent across the network as a sequence of bytes, which has no structure except for a linear stream of bytes. We shall address the various possibilities for messages and the protocols that define them in the next chapter. In this chapter we concentrate on a component of messages - the data that is transferred.

### JSON

JSON stands for JavaScript Object Notation. It was designed to be a lightweight means of passing data between JavaScript systems. It uses a text-based format and is sufficiently general that it has become used as a general purpose serialisation method for many programming languages.

They are delimited by square brackets [ ... ]. Objects are represented by a list of "field: value" pairs enclosed in curly braces { ... }.

JSON is a very simple language, but nevertheless can be quite useful. Its text-based format makes it easy for people to use, even though it has the overheads of string handling.

•  String values encode as JSON strings, with each invalid UTF-8 sequence replaced by the encoding of the Unicode replacement character U+FFFD.
•  Array and slice values encode as JSON arrays, except that []byte encodes as a base64-encoded string.

•  Struct values encode as JSON objects. Each struct field becomes a member of the object. By default the object's key name is the struct field name converted to lower case. If the struct field has a tag, that tag will be used as the name instead.

•  Map values encode as JSON objects. The map's key type must be string; the object keys are used directly as map keys.
•  Pointer values encode as the value pointed to. (Note: this allows trees, but not graphs!). A nil pointer encodes as the null JSON object.

•  Interface values encode as the value contained in the interface. A nil interface value encodes as the null JSON object.


func saveJSON(fileName string, key interface{}) {
    outFile, err := os.Create(fileName)
    checkError(err)
    encoder := json.NewEncoder(outFile)
    err = encoder.Encode(key)
    checkError(err)
    outFile.Close()
}

func loadJSON(fileName string, key interface{}) {
    inFile, err := os.Open(fileName)
    checkError(err)
    decoder := json.NewDecoder(inFile)
    err = decoder.Decode(key)
    checkError(err)
    inFile.Close()

### The gob package

Gob is a serialisation technique specific to Go. It is designed to encode Go data types specifically and does not at present have support for or by any other languages.

It supports all Go data types except for channels and functions. It supports integers of all types and sizes, strings and booleans, structs, arrays and slices.

To use Gob to marshall a data value, you first need to create an Encoder. This takes a Writer as parameter and marshalling will be done to this write stream. The encoder has a method Encode which marshalls the value to the stream. This method can be called multiple times on multiple pieces of data. Type information for each data type is only written once, though.

### Encoding binary data as strings

Binary data transmitted in HTTP responses and requests is often translated into an ASCII form. This makes it easy to inspect the HTTP messages with a simple text reader without worrying about what strange 8-bit bytes might do to your display!

One common format is Base64. Go has support for many binary-to-text formats, including Base64.

    eightBitData := []byte{1, 2, 3, 4, 5, 6, 7, 8}
    bb := &bytes.Buffer{}
    encoder := base64.NewEncoder(base64.StdEncoding, bb)
    encoder.Write(eightBitData)
    encoder.Close()
    fmt.Println(bb)

    dbuf := make([]byte, 12)
    decoder := base64.NewDecoder(base64.StdEncoding, bb)
    decoder.Read(dbuf)
    for _, ch := range dbuf {
        fmt.Print(ch)
    }

### Protocol Design

•  Is it to be broadcast or point to point? Broadcast must be UDP, local multicast or the more experimental MBONE. Point to point could be either TCP or UDP.

### Version control

The ability to talk earlier version formats may be lost if the protocol changes too much. In this case, you need to be able to ensure that no copies of the earlier version still exist - and that is generally impossible.

### Data Format

There are two main format choices for messages: byte encoded or character encoded.


In the byte format
•  the first part of the message is typically a byte to distinguish between message types.
•  The message handler would examine this first byte to distinguish message type and then perform a switch to select the appropriate handler for that type.
•  Further bytes in the message would contain message content according to a pre-defined format (as discussed in the previous chapter).

The advantages are compactness and hence speed. The disadvantages are caused by the opaqueness of the data: it may be harder to spot errors, harder to debug, require special purpose decoding functions. There are many examples of byte-encoded formats, including major protocols such as DNS and NFS

Character formats are easier to setup and easier to debug. For example, you can use telnet to connect to a server on any port, and send client requests to that server. It isn't so easy the other way, but you can use tools like tcpdump to snoop on TCP traffic and see immediately what clients are sending to servers.

The principal complication at this level is the varying status of "newline" across different operating systems. Unix uses the single character "\n". Windows and others (more correctly) use the pair "\r\n". On the internet, the pair "\r\n" is most common - Unix systems just need to take care that they don't assume "\n".

### Simple Example

func pwd(conn net.Conn) {
    s, err := os.Getwd()
    if err != nil {
        conn.Write([]byte(""))
        return
    }
    conn.Write([]byte(s))
}

### Managing character sets and encodings

If you build an application that can be run in different countries then users will demand that it uses their own language. In a distributed system, different components of the system may be used by users expecting different languages and characters.

### Definitions

For example, 7-bit ASCII code points can be encoded as themselves into 8-bit bytes (an octet). So ASCII 'A' (with codepoint 65) is encoded as the 8-bit octet 01000001. 

### Unicode

Neither ASCII nor ISO 8859 cover the languages based on hieroglyphs. Chinese is estimated to have about 20,000 separate characters, with about 5,000 in common use. These need more than a byte, and typically two bytes has been used. There have been many of these two-byte character sets: Big5, EUC-TW, GB2312 and GBK/GBX for Chinese, JIS X 0208 for Japanese, and so on. These encodings are generally not mutually compatable.

Unicode is an embracing standard character set intended to cover all major character sets in use. It includes European, Asian, Indian and many more. It is now up to version 5.2 and has over 107,000 characters. The number of code points now exceeds 65,536, that is. more than 2^16. This has implications for character encodings.


•  UTF-32 is a 4-byte encoding, but is not commonly used, and HTML 5 warns explicitly against using it
•  UTF-16 encodes the most common characters into 2 bytes with a further 2 bytes for the "overflow", with ASCII and ISO 8859-1 having the usual values
•  UTF-8 uses between 1 and 4 bytes per character, with ASCII having the usual values (but not ISO 8859-1)

### UTF-8 Go and runes

Go uses UTF-8 encoded characters in its strings. Each character is of type rune. This is a alias for int32 as a Unicode character can be 1, 2 or 4 bytes in UTF-8 encoding. In terms of characters, a string is an array of runes.

A string is also an array of bytes, but you have to be careful: only for the ASCII subset is a byte equal to a character. All other characters occupy two, three or four bytes. This means that the length of a string in characters (runes) is generally not the same as the length of its byte array. They are only equal when the string consists of ASCII characters only.


If we take a UTF-8 string and test its length, you get the length of the underlying byte array. But if you cast the string to an array of runes []rune then you get an array of the Unicode code points which is generally the number of characters:
str := "百度一下，你就知道"

println("String length", len([]rune(str)))
println("Byte length", len(str))



 The underlying data type for a UTF-8 string in Go is a byte array, and as we saw just above, Go looks after encoding the string into 1, 2, 3 or 4 bytes as needed. The length of the string is the length of the byte array, so you write any UTF-8 string by writing the byte array.

Similarly to read a string, you just read into a byte array and then cast the array to a string using string([]byte). If Go cannot properly decode bytes into Unicode characters, then it gives the Unicode Replacement Character \uFFFD. The length of the resulting byte array is the length of the legal portion of the string.

### UTF-16 and Go

Unfortunately, there is a little devil lurking behind UTF-16. It is basically an encoding of characters into 16-bit short integers. The big question is: for each short, how is it written as two bytes? The top one first, or the top one second? Either way is fine, as long as the receiver uses the same convention as the sender.

But its value 0xfffe is chosen so that you can tell the byte-order:
•  In a big-endian system it is FF FE
•  In a little-endian system it is FE FF
Text will sometimes place the BOM as the first character in the text. The reader can then examine these two bytes to determine what endian-ness has been used.

### Other character sets and Go

But if you want your applications to be usable by the rest of the world, then you need to pay attention to these complexities.


### ISO security architecture

•  Authentication - proof of identity
•  Data integrity - data is not tampered with
•  Confidentiality - data is not exposed to others
•  Notarization/signature
•  Access control
•  Assurance/availability

### Public key encryption

Public key encryption and decryption requires two keys: one to encrypt and a second one to decrypt. The encryption key is usually made public in some way so that anyone can encrypt messages to you. The decryption key must stay private, otherwise everyone would be able to decrypt those messages! Public key systems are asymmetric, with different keys for different uses.

There are many public key encryption systems supported by Go. A typical one is the RSA scheme.
A program generating RSA private and public keys is


    saveGobKey("private.key", key)
    saveGobKey("public.key", publicKey)



func saveGobKey(fileName string, key interface{}) {
    outFile, err := os.Create(fileName)
    checkError(err)
    encoder := gob.NewEncoder(outFile)
    err = encoder.Encode(key)
    checkError(err)
    outFile.Close()
}

    var key rsa.PrivateKey
    loadKey("private.key", &key)

func loadKey(fileName string, key interface{}) {
    inFile, err := os.Open(fileName)
    checkError(err)
    decoder := gob.NewDecoder(inFile)
    err = decoder.Decode(key)
    checkError(err)
    inFile.Close()
}

### X.509 certificates

A Public Key Infrastructure (PKI) is a framework for a collection of public keys, along with additional information such as owner name and location, and links between them giving some sort of approval mechanism.

### Overview of HTTP

URLs and resources
URLs specify the location of a resource. A resource is often a static file, such as an HTML document, an image, or a sound file. But increasingly, it may be a dynamically generated object, perhaps based on information stored in a database.

HTTP 1.1 fixes many problems with HTTP 1.0, but is more complex because of it. This version is done by extending or refining the options available to HTTP 1.0. e.g.

### Simple user-agents

User agents such as browsers make requests and get responses. The response type is

type Response struct {
    Status     string // e.g. "200 OK"
    StatusCode int    // e.g. 200
    Proto      string // e.g. "HTTP/1.0"
    ProtoMajor int    // e.g. 1
    ProtoMinor int    // e.g. 0

    RequestMethod string // e.g. "HEAD", "CONNECT", "GET", etc.

    Header map[string]string

    Body io.ReadCloser

    ContentLength int64

    TransferEncoding []string

    Close bool

    Trailer map[string]string
}



The simplest request is from a user agent is "HEAD" which asks for information about a resource and its HTTP server. The function
func Head(url string) (r *Response, err os.Error)

can be used to make this query.


### Configuring HTTP requests

The data type used to build requests is the type Request. This is a complex type, and is given in the Go documentation as

type Request struct {
    Method     string // GET, POST, PUT, etc.
    RawURL     string // The raw URL given in the request.
    URL        *URL   // Parsed URL.
    Proto      string // "HTTP/1.0"
    ProtoMajor int    // 1
    ProtoMinor int    // 0

There is a lot of information that can be stored in a request. You do not need to fill in all fields, only those of interest. The simplest way to create a request with default values is by for example
request, err := http.NewRequest("GET", url.String(), nil)

Once a request has been created, you can modify fields. For example, to specify that you only wish to receive UTF-8, add an "Accept-Charset" field to a request by
request.Header.Add("Accept-Charset", "UTF-8;q=1, ISO-8859-1;q=0")

If appropriate the media type should state the charset, such as text/html; charset=UTF-8. If there is no charset specification, then according to the HTTP specification it should be treated as the default ISO8859-1 charset. 

### The Client object

To send a request to a server and get a reply, the convenience object Client is the easiest way. This object can manage multiple requests and will look after issues such as whether the server keeps the TCP connection alive, and so on.



    client := &http.Client{}

    request, err := http.NewRequest("GET", url.String(), nil)
    // only accept UTF-8
    request.Header.Add("Accept-Charset", "UTF-8;q=1, ISO-8859-1;q=0")
    checkError(err)

    response, err := client.Do(request)
    if response.Status != "200 OK" {
        fmt.Println(response.Status)
        os.Exit(2)
    }

### Proxy handling

But it can find proxy information if it is set in operating system environment variables such as HTTP_PROXY or http_proxy using the function
func ProxyFromEnvironment(req *Request) (*url.URL, error)

If your programs are running in such an environment you can use this function instead of having to explicitly know the proxy parameters.

Some proxies will require authentication, by a user name and password in order to pass requests. A common scheme is "basic authentication" in which the user name and password are concatenated into a string "user:password" and then BASE64 encoded. This is then given to the proxy by the HTTP request header "Proxy-Authorisation" with the flag that it is the basic authentication


    // encode the auth
    basic := "Basic " + base64.StdEncoding.EncodeToString([]byte(auth))

    transport := &http.Transport{Proxy: http.ProxyURL(proxyURL)}
    client := &http.Client{Transport: transport}

    request, err := http.NewRequest("GET", url.String(), nil)

    request.Header.Add("Proxy-Authorization", basic)
    dump, _ := httputil.DumpRequest(request, false)
    fmt.Println(string(dump))

    // send the request
    response, err := client.Do(request)

### HTTPS connections by clients

For secure, encrypted connections, HTTP uses TLS which is described in the chapter on security. The protocol of HTTP+TLS is called HTTPS and uses https:// urls instead of http:// urls.


Servers are required to return valid X.509 certificates before a client will accept data from them. If the certificate is valid, then Go handles everything under the hood and the clients given previously run okay with https URLs.

### Servers

fileServer := http.FileServer(http.Dir("/home/httpd/html/"))

    // register the handler and deliver requests to it
    err := http.ListenAndServe(":8000", fileServer)
    checkError(err)
    // That's it!

This server even delivers "404 not found" messages for requests for file resources that don't exist!

// file handler for most files
    fileServer := http.FileServer(http.Dir("/var/www"))
    http.Handle("/", fileServer)

    // function handler for /cgi-bin/printenv
    http.HandleFunc("/cgi-bin/printenv", printEnv)

func printEnv(writer http.ResponseWriter, req *http.Request) {
    env := os.Environ()
    writer.Write([]byte("<h1>Environment</h1>\n<pre>"))
    for _, v := range env {
        writer.Write([]byte(v + "\n"))
    }
    writer.Write([]byte("</pre>"))
}

You can define your own handlers. These can either be registered with the default multiplexer by calling http.HandleFunc which takes a pattern and a function. The functions such as ListenAndServe then take a nil handler function. This was done in the last example.

If you want to take over the multiplexer role then you can give a non-zero function as the handler function. This function will then be totally responsible for managing the requests and responses.

The following example is trivial, but illustrates the use of this:

Go has extensive support for HTTP. This is not surprising, since Go was partly invented to fill a need by Google for their own servers.


### Templates

Many languages have mechanisms to convert strings from one to another. Go has a template mechanism to convert strings based on the content of an object supplied as an argument. 

### Inserting object values

 template is applied to a Go object. Fields from that Go object can be inserted into the template, and you can 'dig' into the object to find subfields, etc. The current object is represented as '.', so that to insert the value of the current object as a string, you use {{.}}. The package uses the fmt package by default to work out the string used as inserted values.

We can loop over the elements of an array or other list using the range command. So to access the contents of the Emails array we do
{{range .Emails}}
        ...
{{end}}

An alternative is to switch the current object to the Jobs field. This is done using the {{with ...}} ... {{end}} construction, where now {{.}} is the Jobs field, which is an array:
{{with .Jobs}}
    {{range .}}
        An employer is {{.Employer}}
        and the role is {{.Role}}
    {{end}}
{{end}}

Once we have a template, we can apply it to an object to generate a new string, using the object to fill in the template values. This is a two-step process which involves parsing the template and then applying it to an object. The result is output to a Writer

Note that there is plenty of whitespace as newlines in this printout. This is due to the whitespace we have in our template. If we wish to reduce this, eliminate newlines in the template as in
{{range .Emails}} An email is {{.}} {{end}}

### Pipelines

To take the value of the current object '.' and apply HTML escapes to it, you write a "pipeline" in the template
{{. | html}}

and similarly for other functions.

### Variables

To access the email strings, we use a range statement such as
{{range .Emails}}
    {{.}}
{{end}}

But at that point we cannot access the Name field as '.' is now traversing the array elements and the Name is outside of this scope. The solution is to save the value of the Name field in a variable that can be accessed anywhere in its scope. Variables in templates are prefixed by '$'. So we write
{{$name := .Name}}
{{range .Emails}}
    Name is {{$name}}, email is {{.}}
{{end}}

### A Complete Web Server

I am learning Chinese. Rather, after many years of trying I am still attempting to learn Chinese. 

### The Chinese Dictionary

Chinese is a complex language (aren't they all :-( ). The written form is hieroglyphic, that is "pictograms" instead of using an alphabet. But this written form has evolved over time, and even recently split into two forms: "traditional" Chinese as used in Taiwan and Hong Kong, and "simplified" Chinese as used in mainland China. While most of the characters are the same, about 1,000 are different. Thus a Chinese dictionary will often have two written forms of the same character.

Most Westerners like me can't understand these characters. So there is a "Latinised" form called Pinyin which writes the characters in a phonetic alphabet based on the Latin alphabet. It isn't quite the Latin alphabet, because Chinese is a tonal language, and the Pinyin form has to show the tones (much like acccents in French and other European languages). So a typical dictionary has to show four things: the traditional form, the simplified form, the Pinyin and the English. For example,

### HTML

... in progress


### Remote Procedure Call

1.  The client calls the local stub procedure. The stub packages up the parameters into a network message. This is called marshalling.
2.  Networking functions in the O/S kernel are called by the stub to send the message.
3.  The kernel sends the message(s) to the remote system. This may be connection-oriented or connectionless.

4.  A server stub unmarshalls the arguments from the network message.
5.  The server stub executes a local procedure call.
6.  The procedure completes, returning execution to the server stub.
7.  The server stub marshals the return values into a network message.
8.  The return messages are sent back.

9.  The client stub reads the messages using the network functions.
10.  The message is unmarshalled. and the return values are set on the stack for the local process.

There are two common styles for implementing RPC. The first is typified by Sun's RPC/ONC and by CORBA.

### Go RPC

Go's RPC is so far unique to Go. It is different to the other RPC systems, so a Go client will only talk to a Go server. It uses the Gob serialisation system discussed in "Chapter 4 Data serialisation - The gob package", which defines the data types which can be used.

The client needs to set up an HTTP connection to the RPC server. It needs to prepare a structure with the values to be sent, and the address of a variable to store the results in. Then it can make a Call with arguments:
•  The name of the remote function to execute
•  The values to be sent
•  The address of a variable to store the result in

### JSON

It just uses a different "wire" format for the data, JSON instead of gob. As such, clients or servers could be written in other languasge that understand sockets and JSON.


### Handling Timeouts

Because these channels use the network, there is alwasy the possibility of network errors leading to timeouts. Andrew Gerrand points out a solution using timeouts: "[Set up a timeout channel.] We can then use a select statement to receive from either ch or timeout. If nothing arrives on ch after one second, the timeout case is selected and the attempt to read from ch is abandoned."

timeout := make(chan bool, 1)
go func() {
    time.Sleep(1e9) // one second
    timeout <- true
}()

select {
case <- ch:
    // a read from ch has occurred
case <- timeout:
    // the read from ch has timed out
}

### Web socket server

A web socket server starts off by being an HTTP server, accepting TCP conections and handling the HTTP requests on the TCP connection. When a request comes in that switches that connection to a being a web socket connection, the protocol handler is changed from an HTTP handler to a WebSocket handler. So it is only that TCP connection that gets its role changed: the server continues to be an HTTP server for other requests, while the TCP socket underlying that one connection is used as a web socket.


An HTTP server that is only expecting to be used for web sockets might run by
func main() {
    http.Handle("/", websocket.Handler(WSHandler))
    err := http.ListenAndServe(":12345", nil)
    checkError(err)
}



A more complex server might handle both HTTP and web socket requests simply by adding in more handlers.

### The Message object

msgToSend := "Hello"
err := websocket.Message.Send(ws, msgToSend)



conn, err := websocket.Dial(service, "", "http://localhost")
    checkError(err)

### Web sockets over TLS


    http.Handle("/", websocket.Handler(Echo))
    err := http.ListenAndServeTLS(":12345", "jan.newmarch.name.pem",
        "private.pem", nil)
    checkError(err)

The client is the same echo client as before. All that changes is the url, which uses the "wss" scheme instead of the "ws" scheme:
EchoClient wss://localhost:12345/

