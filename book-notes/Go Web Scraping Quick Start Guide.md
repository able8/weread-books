## Go Web Scraping Quick Start Guide
> Vincent Smith

### Title Page

Implement the power of Go to scrape and crawl data
from the web

### About the author

Vincent Smith has been a software engineer for 10 years, having worked in various fields from health and IT to machine learning, and large-scale web scrapers. 

### What this book covers

Chapter 2, The Request/Response Cycle, outlines the structure of HTTP requests and responses, and explains how to use Go to make and process them.

Chapter 8, Scraping at 100x, provides a blueprint for building a large-scale web scraper and provides some examples from the open source community.

### Download the example code files

The code bundle for the book is also hosted on GitHub at https://github.com/PacktPublishing/Go-Web-Scraping-Quick-Start-Guide

In case there's an update to the code, it will be updated on the existing GitHub repository.

### Reviews

Please leave a review. Once you have read and used this book, why not leave a review on the site that you purchased it from? Potential readers can then see and use your unbiased opinion to make purchase decisions

### Introducing Web Scraping and Go

Introducing Web Scraping and Go
Collecting, parsing, storing, and processing data are essential tasks that almost everybody will need to do in their software development career.

Here, you will find a guide for performing web scraping in Go. 

to building highly concurrent distributed systems.

•  What is web scraping?

•  How can you set up a Go development environment?

### What is web scraping?

for a specific purpose. 

### Why do you need a web scraper?

Because of the lack of this relationship, the one who needs the data has to rely on their own means to gather the information.

### Search engines

This book will not, however, cover indexing and ranking pages to provide relevant search results.

### Price comparison

Price comparison
Another known use case is to find specific products or services sold through various websites and track their prices. 

get notified if the price of an item drops. 


In later chapters, you will learn how to extract information from HTML pages in order to collect this information.

### What is Go?

 At the time of its creation, the goal was to build a language that was fast, safe, and simple.

Go powers many large-scale web infrastructure platforms and tools such as Docker, Kubernetes, and Terraform. These platforms enable companies to build production-scale products supporting Fortune 500 companies. 

### Why is Go a good fit for web scraping?

The architecture of the Go programming language, as well as its standard libraries, make it a great choice for building web scrapers that are fast, scalable, and maintainable. 

there are three main reasons why Go is a great fit for web scraping:

### Go is fast

This speed is typically coupled with a low resource footprint, as the runtime is very lightweight and does not use much RAM.

This reduces the cost of operating a web scraper at larger scales.

The speed and efficiency of Go allow you to do more with less.

### Go is safe

This makes the language ideal for building systems at a large scale and being confident in how your program will run in production. Also, since Go programs are built with a compiler instead of being run with an interpreter, it allows you to catch more bugs at compile time and greatly reduces the dreaded runtime errors.

This helps prevent memory leaks that might occur from mishandling objects in your code. 

### Go is simple

Go offers a built-in HTTP client in the net/http package that is fully-featured out of the box, but also allows for a lot of customization. Making an HTTP request is as simple, as follows:
http.Get("http://example.com")

The HTTP client in the net/http package is also very configurable, letting you tune special parameters and methods to suit your specific needs.

This simplicity will help eliminate some of the guesswork of writing code. You will not need to determine the best way to make an HTTP request; Go has already worked it out and provided you with the best tools you need to get the job done. 

### How to set up a Go development environment

Before you get started building a web scraper, you will need the proper tools. Setting up a development environment for writing Go code is relatively simple.

### Go language and tools

This is a screenshot from the installation page from the Go website, containing all of the instructions necessary for installing Go on your computer:

### The Request/Response Cycle

When you want to visit a website, your browser sends the website URL to a DNS server, the URL is translated into an IP address, and your browser then sends a request to the machine at that IP address. The machine, called a web server, receives and inspects the request, and makes a decision on what to send back to your browser.

### 400–499 range

400–499 range
When you encounter status codes in this range, you should be concerned. The 400 range indicates that there was something wrong with your request. There are many different issues that can trigger these responses, such as poor formatting, authentication issues, or unusual requests. 

### 500–599 range

The status codes 502 Bad Gateway and 503 Service Temporarily Unavailable indicate that the server was unable to produce the resource due to a problem within the server. 

### What do HTTP requests/responses look like in Go?

All of this information is contained in the r variable, which is a *http.Response that was returned from the http.Get() function.

Let's take a look at the definition of what an http.Response object looks like in Go. The following struct is defined in the Go standard library:


type Response struct {
    Status string
    StatusCode int
    Proto string
    ProtoMajor int
    ProtoMinor int
    Header Header
    Body io.ReadCloser
    ContentLength int64
    TransferEncoding []string
    Close bool
    Uncompressed bool
    Trailer Header
    Request *Request
    TLS *tls.ConnectionState
}

### A simple request example

  // Read the data from the server
  r.Body.Read(webPageContent)

// Open a writable file on your computer (create if it does not 
     exist)
  var out *os.File
  out, err = os.OpenFile("index.html", os.O_CREATE|os.O_WRONLY, 0664)

With these basics in mind, you should be on your way to creating a web scraper in Go that can create HTTP requests and read HTTP responses with just a few lines of code.

### Web Scraping Etiquette

•  What is a robots.txt file?
•  What is a User-Agent string?
•  How can you throttle your web scraper?

### What is a robots.txt file?

The pages that are related to logging in and signing up, which could be used for automated account creation, are also disallowed by Disallow: /m/.

### What is a User-Agent string?

You can see that this string identifies the family, name, and version of the web browser, as well as the operating system. This string will be sent with every request from this browser inside of a request header, such as the following:
GET /index.html HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:57.0) Gecko/20100101 Firefox/57.0

Not all User-Agent strings contain this much information. HTTP clients that are not web browsers are typically much smaller. Here are some examples:
•  cURL: curl/7.47.0
•  Go: Go-http-client/1.1
•  Java: Apache-HttpClient/4.5.2

### How to throttle your scraper

For smaller servers, this is especially true, as they have a much more limited pool of resources. 

### How to use caching

A website that supports caching, will give the client information on what it can store, and how long to store it. This is done through response headers such as Cache-Control, Etag, Date, Expires, and Vary.

### Cache-Control

The Cache-Control header is used to indicate whether this content is cacheable, and for how long. Some of the common values for this header are as follows:

In the response from the previous example, the server sends a Cache-Control header:
Cache-Control: max-age=604800
This indicates that you should cache this page for up to 604880 seconds (seven days).


### Caching content in Go

The library provides a module that can store and retrieve web pages from your local machine, as well as a plugin for the Go HTTP client to automatically handle all cache-related HTTP request and response headers. It also provides multiple storage backends where you can store the cached information, such as Redis, Memcached, and LevelDB.

The diskv project is used by httpcache to store the web page on your local machine.


  // Make the initial request
  println("Caching: http://www.example.com/index.html")
  resp, err := cachedClient.Get("http://www.example.com/index.html")
  if err != nil {
    panic(err)
  }

, it reads all of the cache-related headers to determine if it can store the page and includes the expiration date with the data. 

On the second request, httpcache checks if the content is expired and returns the cached data instead of making another HTTP request. It also adds an extra header, X-From-Cache, to indicate that this is being read from the cache. 

Using a client that is automatically set up to handle caching content will make your scraper run faster, as well as reducing the likelihood that your web scraper will be flagged as a bad citizen.

### Summary

Parsing HTML, we will look at how to extract information from HTML pages using various techniques.

### Parsing HTML

•  Searching using the strings package
•  Searching using the regexp package
•  Searching using XPath queries
•  Searching using Cascading Style Sheets selectors

### Structure

Each HTML document has a specific layout starting with the <!doctype> tag. This tag is used to define the version of the HTML specification used to validate this specific document.

In our case, the <!doctype html> refers to the HTML 5 specification.

Following the <!doctype> tag is the <html> tag, which holds the actual content of the web page.

### Searching using the regexp package

By using capture groups in regular expressions, you can extract data matching a query from the web page.

### Example – Finding links


  stringBody := string(data)

### Example – Finding prices

stringBody := string(data)

  re := regexp.MustCompile(`.*main-book-price.*\n.*(\$[0-9]*\.[0-9]{0,2})`)
  priceMatches := re.FindStringSubmatch(stringBody)

  fmt.Printf("Book Price: %!s(MISSING)\n", priceMatches[1])

### Searching using XPath queries

In particular, HTML documents are very similar to XML documents, although they are not fully XML-compliant. Because of this XML-like structure, we can search for content in the pages using XPath queries.


### Example – Daily deals

On the other hand, if you wanted to retrieve a block or multiple blocks of text where the content had no format, it would become more difficult to do so with basic string searches. XPath simplifies this by allowing you to retrieve all of the text content inside of a node.

However, the open source community has built various XPath libraries for Go. The one I would recommend is htmlquery by GitHub user antchfx.
You can obtain this library by using the following command:
go get github.com/antchfx/htmlquery

The following example demonstrates how you can scrape daily deals using an XPath query to discover some basic product information:


func main() {
  doc, err := htmlquery.LoadURL("https://www.packtpub.com/packt/offers/free-learning")

dealTextNodes := htmlquery.Find(doc, `//div[@class="dotd-main-book-summary float-left"]//text()`)

### Searching using Cascading Style Sheets selectors

CSS selectors offer a shorthand for class attributes by simply using a .. The same XPath query would look like div.some-class as a CSS query. Another common shorthand used here is searching elements with an id attribute, which is represented in CSS as a # symbol.

### Example – Collecting products

In this example, you can use CSS queries to return all of the text from all of the child elements as well. We use the :not() operator to exclude the countdown timer, and finally, to process the lines of text to ignore spaces and blank lines.

### Summary

CSS selectors are the simplest way to search for and extract data from HTML documents and provide many helpful features that are HTML-specific.

### Web Scraping Navigation

•  How to follow links
•  How to submit forms with POST requests
•  How to track your history to avoid loops

### Following links

If you find that the href attribute lacks the http:// or https:// prefix and the hostname, you must use the prefix and hostname of the current web page.


### Example – Daily deals

You could collect more information about each book by following each link to the book's main web page. 

title = bookPage.Find("div.book-top-block-info h1").Text()
      description = strings.TrimSpace(bookPage.Find("div.book-top-
      block-info div.book-top-block-info-one-liner").Text())

time.Sleep(1 * time.Second)

 At the end of each page, we sleep for 1 second so that our web scraper does not overburden the servers

### Example – Submitting searches

By right-clicking on the Search... box, you should be able to inspect the element using your browser's Developer tools. This reveals the HTML source code for the page, showing that this box is located in an HTML form. In Google Chrome, it looks similar to the following screenshot:


This form uses the HTTP GET method, and submits to the https://hub.packtpub.com/ endpoint.

 By submitting this query periodically, you could discover new articles as soon as they are released.

### Example – POST method

Instead of putting the values in the URL, you would need to build a request body instead. 

func main() {
  data := url.Values{}
  data.Set("s", "Golang")

  response, err := http.PostForm("https://hub.packtpub.com/", data)

In Go, you build a form submission using the url.Values struct. You can use this to set the inputs of the form—s=Golang in our case—and submit it using the http.Post() function. 

### Avoiding loops

Therefore, it is very important to build a tracking system into your scraper that records its history.


The simplest data structure for storing a unique collection of items would be a set. The Go standard library does not have a set data structure, but it can be emulated by using a map[string]interface{}{}.
An interface{} in Go is a generic object, similar to java.lang.Object.


visitedMap := map[string]interface{}{}
In this case, we would use the visited URL as the key, and anything you want as the value. We will just use nil, because as long as the key is present, we know we have visited the site.

When you try to retrieve a value from a map, using a given key, Go will return two values: the value for the key if it exists, and a Boolean, stating whether or not the key exists in the map. In our case, we are only concerned about the latter.

We would check for a site visit like the one demonstrated in the following code block:
_, ok := visitedMap["http://example.com/index.html"]

// ok == true, meaning the URL exists in the visitedMap
  // Skip this URL

### Breadth-first versus depth-first crawling

Now that you have the ability to navigate to different pages, as well as the ability to avoid getting stuck in a loop, you have one more important choice to make when crawling a website.

### Breadth-first

There is no right or wrong technique for how to navigate your scraper; it all depends on your specific needs. Depth-first crawling will reveal the origins of specific topics, whereas breadth-first crawling will complete a full cluster before discovering new content. 

You might even use a combination of techniques if this suits your requirements.

### Navigating with JavaScript

This is not always the case for more modern websites, which contain JavaScript code responsible for loading extra information after the initial page loads. I

background, make a second request to collect the actual results to display. In order to do this, custom code written in JavaScript is run by your web browser. Using the standard HTTP client would not be sufficient in this case and you would need to use an external browser that supports JavaScript execution.

In Go, there are many options for integrating scraper code with web browsers thanks to a few standard protocols. The WebDriver protocol is the original standard developed by Selenium and is supported by most major browsers.

This protocol allows programs to send a browser's commands, such as load a web page, wait for an element, click a button, and capture the HTML. These commands would be necessary to collect results from a web page where items are loaded via JavaScript. 

### Example – Book reviews

On the Packt Publishing website, book reviews are loaded via JavaScript and are not visible when the page is first loaded. This example demonstrates how to use the selenium package to scrape reviews from a book listing on the Packt Publishing site. 

### Summary

we covered the basics of how to navigate your web scraper through a website. 

### Protecting Your Web Scraper

there are a few things you should do to make sure it operates safely. A number of important measures should be taken to protect your web scraper. As you should be aware, nothing on the internet should be fully trusted if you do not have complete ownership of it.

### Virtual private servers

Virtual private servers
When you make an HTTP request for a website, you are making a direct connection between your computer and the targeted server. By doing this, you are providing them with your machine's public IP address, which can be used to determine your general location, and your Internet Service Provider (ISP).

With this in mind, it is preferable to not expose any of your personal assets to untrusted servers.

You can rent Virtual Private Server (VPS) instances from various providers on the web.
Some of the more notable companies include the following:
•  Amazon Web Services (AWS)
•  Microsoft Azure
•  Google Cloud
•  DigitalOcean
•  Linode

You will need to deploy your web scraping code onto these machines and run the program from within your VPS. 

You can spin up multiple VPS instances to run your scrapers in parallel.


### Proxies

The role of a proxy is to provide an additional layer of protection on top of your system. At its core, a proxy is a server that sits in between your web scraper and the target web server, and passes communication between the two. 

There are many types of proxy available, each with its own pros and cons

### Public and shared proxies

Speed is another concern for public proxies: the more traffic flows through a proxy, the less bandwidth will be available. On the other hand, these proxies are free to use and could be useful during your testing and debugging stages.

### Location

On occasion, the location of the proxy may be important to you.

so you should always consult local laws before pursuing this route

### Anonymity

Not all proxies completely hide the originating source of the request when they pass data to the target servers. 

Transparent proxies provide the target server with information about who you are, and should be avoided in most cases. These proxies pass HTTP headers to the target server, such as X-Forwarded-For: <your_ip_address>, to identify the originating source of the request, and Via: <proxy_server> to identify the proxy itself.

Anonymous proxies provide the same headers as transparent proxies, but they may provide false information in order to hide your true identity. In this case, the target server will be aware that the connection is being made through a proxy, but the true source of the request is unknown.


### Proxies in Go

The Go HTTP client contains an object called a transport. The transport is responsible for low-level communication with web servers, including opening and closing connections, sending and receiving data, and handling HTTP 1XX response codes.

### Virtual private networks

VPNs are not legal in all countries. Please comply with local laws.

Configuring your web scraper to use the VPN is different from proxies. VPNs usually require a specific client to connect your machine to their network, which is not done through code. 

Follow the instructions supplied by your VPN provider in order to connect to a VPN network.

### Whitelists

You could then verify that this matches by using the path.Match() function, shown as follows:
doesMatch, err := path.Match("hub.packtpub.com/*", site)

### What is concurrency

Concurrency should not be confused with parallelism, where two things or instructions can literally be executed at the same time.

### Race conditions

This situation is known as a race condition and is one of the most common issues you will run into as you build concurrent programs. In order to prevent race conditions from happening, there needs to be a mechanism where one thread can block access to the map for all other threads. 

In order to enter the room, you first check if the door is locked. If it is locked, you must wait for someone to unlock it before you can proceed. 

If there is already an existing lock being held, your thread will wait until the existing lock is released.

concurrent reads, but must protect the object when a write is in process, the sync package provides the sync.RWMutex struct. This works similarly to the sync.Mutex, except that it separates locking into two separate methods:

•  RLock() / RUnlock(): A lock used specifically when a read operation is in progress

The thread obtains a read lock before checking the map, to ensure that no writes are being made. 

### The Go concurrency model

Do not communicate by sharing memory; instead, share memory by communicating.

Instead of doing this, Go provides tools to help share memory by communicating.


### Goroutines

Goroutines are described as a:
Lightweight thread of execution.
Unlike traditional threads found in C and Java, goroutines are managed by the Go runtime and not the OS thread scheduler.

When multiple goroutines need to communicate with each other, Go provides a simple tool for them to use.

### Channels

for update := range updatesChan {
    for url, status := range update {
      siteStatus[url] = status
      numberCompleted++
    }

Closing a channel prevents the sending and receiving of any more data through that channel. As far as for-range operations are concerned, this marks the end of the stream, exiting to loop.

### Conditions

One common control that you will want to put into place is the number of active concurrent scraping threads. Before you launch a new goroutine, you will need to satisfy a certain condition, being that the number of active threads is less than some value. If this condition fails the check, your new goroutine will have to wait, until it is signaled that the condition passes. This scenario is solved using the sync.Cond object.

The sync.Cond object provides a mechanism to tell goroutines to wait until a signal is received, based on any defined condition. A sync.Cond struct wraps a sync.Locker (usually a sync.Mutex or sync.RWMutex) to control access to the condition itself.

how you might use a sync.Cond to control the number of active scraper threads:

const maxActiveThreads = 1

func scrapeSite(site string, condition *sync.Cond) {
  condition.L.Lock()
  if activeThreads >= maxActiveThreads {
    condition.Wait()
  }
  activeThreads++
  condition.L.Unlock()

the program first checks if it has reached the maximum number of concurrent threads. If this condition is true, it will wait. 

We use Signal() in our case to notify a single goroutine that the condition has passed, and that it can proceed.

Broadcast() was used here, it would release all of the goroutines which are currently waiting on the condition.

 good use case for that could be pausing the entire system to make some configuration change on the fly, then resuming all paused goroutines.

### Atomic counters

The sync package offers a subpackage called atomic that provides methods for updating objects without the need for locks. This is done by creating a new variable each time a change is made, while preventing multiple writes from occurring at the same time. 

  "sync/atomic"

### Scraping at 100x

Up to this point, you have learned how to collect information from the internet efficiently, safely, and respectfully.

accomplish your goals.

However, there may come a day when you need to upscale your application to handle large and production-sized projects. You may be lucky enough to make a living out of offering services, and, as that business grows, you will need an architecture that is robust and manageable.

•  Scraping JavaScript pages with chrome-protocol
•  Distributed scraping with dataflowkit

### Components of a web scraping system

we saw how defining a clear separation of roles between the worker goroutines and the main goroutine helped mitigate issues in the program.

### Queue

Queues can be set up in many different ways. In many of the previous examples, we used a []string or a map[string]string to hold the target URLs the scraper should pursue. This works for smaller scale web scrapers where the work is being pushed to the workers.

Queuing systems are not always a part of the main scraping application. There are many suitable solutions for external queues, such as databases, or streaming platforms, such as Redis and Kafka. These tools will support your queuing system to the limits of your own imagination.

### Cache

With a cache, we are able to avoid requesting content from a website if we know nothing has changed. In our previous examples, we used a local cache which saves the content into a folder on the local machine. In larger web scrapers with multiple machines, this causes problems, as each machine would need to maintain its own cache. Having a shared caching solution would solve this problem and increase the efficiency of your web scraper.

### Storage

These days, there are many database solutions available to satisfy different needs.

 If you have data that has more of anested structure, then you may want to look at NoSQL databases. 

The sql package was built to provide a common set of functions used to communicate with SQL databases suchas MySQL, PostgreSQL

The core of the sql package provides methods for opening and closingdatabase connections, querying the database, iterating through rows of results, and performing inserts andmodifications to the data. 

, Go allows you to swap out yourdatabase for another SQL database with less effort.

### Logs

This works well enough for a single node, but, as your scraper grows into a distributed architecture, itcauses problems. In order to check how things are running in your system an operator would need to log intoeach machine to look at the logs.

 A logging system built for distributed computing would beideal at this point.

One of the more popular choices is Graylog.Setting up a Graylog server is a simple process, requiring a MongoDB database and an Elasticsearch database tosupport it. 

 If you decide to changeyour logging server during the development of your scraper, you need only to change the formatter instead ofreplacing all of your log statements.

### Scraping HTML pages with colly

colly is one of the available projects on GitHub that covers most of the systems discussed earlier. This project isbuilt to run on a single machine, due to its reliance on a local cache and queuing system.

colly is built to only process HTML and XML files. It does not offer support for JavaScript execution. However,you would be surprised at how much information you can collect with pure HTML. 

 In this function, it performs a CSS query for <a> tags containing the href attribute. 

. This project is a great starting point for any web scraperthat only requires HTML pages. It does not need much to set up and have a flexible system for parsing HTMLpages.

### Scraping JavaScript pages with chrome-protocol

 Most importantly,the protocol allows the program to collect the HTML on demand. This way, if you werescraping a web page in which search results were loaded via JavaScript, you could waitfor the results to display, request the HTML page, and continue parsing the informationneeded.

The chrome-protocol project on GitHub, developed by the GitHub user 4ydx, providesaccess to use the DevTools Protocol to drive compatible web browsers.

There are many more actions that you can send to a browser, and, by building your owncustom script, you can navigate through JavaScript websites and collect the data thatyou need. 

### Example – Amazon Daily Deals

In the following example, we will use chrome-protocol and goquery to retrieve the Daily Deals from amazon.com.

This example is a bit complex

This block of code imports the necessary packages to run the rest of the program. Some new packages that wehave not seen before are:encoding/json: Go standard library for handling JSON datagithub.com/4ydx/chrome-protocol: Open source library for using the DevTools Protocol

### Distributed scraping with dataflowkit

The architecture of dataflowkit is separated into two distinct parts: fetching and parsing. Both Fetch and Parse phases of the system are built as separate binaries to be run on different machines. They communicate over HTTP via an API, as would you if you need to send or receive any information.

Once the page has been received, parsing the page often provides little overhead.

go get github.com/slotix/dataflowkit/...

### The Fetch service

The fetch.Request object is a convenient way of structuring our POST request data, and the json library makes it easy to attach as the request body.

    Actions: `[{"click":{"element":"a"}}]`,

### The Parse service

There are many options you can set when configuring the Parse service that will determine the backend it uses to cache the results: how to handle pagination, the location of the Fetch service, and so on. For now, we will use the standard defaults.

  "github.com/slotix/dataflowkit/fetch"
  "github.com/slotix/dataflowkit/scrape"

You are armed with enough knowledge and enough tools to build efficient and powerful systems, but I hope your learning does not stop here!

### Summary

We used colly to scrape HTML pages that did not require JavaScript. We used chrome-protocol to drive web browsers to scrape sites that do require JavaScript. 

 I hope you check out some other publications on building applications in Go and continue to hone your skills!

### Leave a review - let other readers know what you think

Leave a review - let other readers know what you think
Please share your thoughts on this book with others by leaving a review on the site that you bought it from. 

It will only take a few minutes of your time, but is valuable to other potential customers, our authors, and Packt. Thank you!