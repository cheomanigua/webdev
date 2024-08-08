---
title: 'Go'
date: 2019-02-11T19:27:37+10:00
weight: 3
---
## Installation
[go.dev/doc/install](https://go.dev/doc/install)

## Simplest web server

```go
package main

import (
    "net/http"
)

func main() {
    http.ListenAndServe(":8080", http.FileServer(http.Dir("./static")))
}
```
`http.ListenAndServe` creates the server.
`:8080` tells the server to listen for requests on port 8080.
`http.FileServer` servers the files to the client.
`http.Dir("./static")` instructs `http.FileServer` to look at the host's `static`directory for the files to serve.

By default, `net/http` will use `index.html` as the first file to look at. This is the structure of the project:

```
server/
    |_ go.mod
    |_ server.go
    |_ static/
        |_ index.html
        |_ about.html
        |_ contact.html
```

## Simplest web server: Effective Go version 

Let's make use of Effective Go writing style: clear and idiomatic Go code:

```go
package main

import (
    "log"
    "net/http"
)

func main() {
    fs := http.FileServer(http.Dir("./static"))
    http.Handle("/", fs)

    log.Print("Listening on :8080...")
    err := http.ListenAndServe(":8080", nil)
    if err != nil {
        log.Fatal(err)
    }
}
```

We can see now in the code above the `http.Handle`. What is it? It is a built in function that registers the handler for the given pattern in DefaultServerMux. You can check those patterns [here](https://pkg.go.dev/net/http#hdr-Patterns). Basically the first parameter declares the pattern to match, and the second paramenter instructs what the handler actually does when the match occurs.

## Handle vs HandleFunc (functions)

### Handle

`http.Handle` is a function that registers the handler for the given pattern in `[DefaultServerMux]`. The documentation for `[ServerMux]` explains how patterns are matched.

```go
func(pattern string, handler http.Handler)
```

### HandleFunc

`http.HandleFunc` is a function that registers the handler function for the the given pattern in `[DefaultServerMux]`. The documentation for `[ServerMux]` explains how patterns are matched.

```go
func(pattern string, handler func(http.ResponseWriter, *http.Request))
```

Example:

```go
package main

import (
	"io"
	"net/http"
)

func h1(w http.ResponseWriter, _ *http.Request) {
	io.WriteString(w, "Hello from a HandleFunc #1!\n")
}

func h2(w http.ResponseWriter, _ *http.Request) {
	io.WriteString(w, "Hello from a HandleFunc #2!\n")
}

type HelloHandler struct{}

func (h *HelloHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "Hello!")
}

func main() {
	http.HandleFunc("/", h1)
	http.HandleFunc("/endpoint", h2)

	hello := HelloHandler{}
	http.Handle("/hello", &hello)

	http.ListenAndServe(":8080", nil)
}
```

## Handler and HandlerFunc

### Handler (type)

`http.Handler` is an interface that responds to an HTTP request. [http.Handler.ServeHTTP] should write reply headers and data to the [`http.ResponseWriter`](https://pkg.go.dev/net/http#ResponseWriter) or read from the [Request.Body] after or concurrently with the completion of the ServeHTTP call.

```go
type Handler interface {
	ServeHTTP(ResponseWriter, *Request)
}
```

Types that implement the `ServeHTTP(w http.ResponseWriter, r *http.Request)` method satisfy the [`http.Handler`](https://pkg.go.dev/net/http/#Handler) interface and therefore instances of those types can, for example, be used as the second argument to the [`http.Handle`](https://pkg.go.dev/net/http/#Handle) function or the equivalent [`http.ServeMux.Handle`](https://pkg.go.dev/net/http#ServeMux.Handle) method.

An example might make this more clear:

```go
type myHandler struct {
    // ...
}

func (h myHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte(`hello world`))
}

func main() {
    http.Handle("/", myHandler{})
    http.ListenAndServe(":8080", nil)
}
```

#### func FileServer - a Handler type
```go
func FileServer(root FileSystem) Handler
```
FileServer returns a handler that serves HTTP requests with the contents of the file system rooted at root.

As a special case, the returned file server redirects any request ending in "/index.html" to the same path, without the final "index.html".

To use the operating system's file system implementation, use [`http.Dir`](https://pkg.go.dev/net/http#Dir):

```go
http.Handle("/", http.FileServer(http.Dir("/tmp")))
```

Example of FileServer Handler type:

```go
package main

import (
	"log"
	"net/http"
)

func main() {
	// Simple static webserver:
	log.Fatal(http.ListenAndServe(":8080", http.FileServer(http.Dir("/usr/share/doc"))))
}
```


### HandlerFunc (type)
`http.HandlerFunc` type is an adapter class to allow the use of ordinary functions as HTTP handlers. If '**f**' is a function with the appropiate signature, HandlerFunc(f) is a [`http.Handler`](https://pkg.go.dev/net/http#Handler) that calls '**f**'.

```go
type HandlerFunc func(ResponseWriter, *Request)
```

Functions with the signature `func(w http.ResponseWriter, r *http.Request)` are http handler funcs that can be converted to an `http.Handler` using the [`http.HandlerFunc`](https://pkg.go.dev/net/http#HandlerFunc) type. Notice that the signature is the same as the signature of the `http.Handler`'s `ServeHTTP` method.

For example:

```go
func myHandlerFunc(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte(`hello world`))
}

func main() {
    http.Handle("/", http.HandlerFunc(myHandlerFunc))
    http.ListenAndServe(":8080", nil)
}
```

The expression `http.HandlerFunc(myHandlerFunc)` converts the `myHandlerFunc` function to the type `http.HandlerFunc` which implements the `ServeHTTP` method so the resulting value of that expression is a valid `http.Handler` and therefore it can be passed to the `http.Handle("/", ...)` function call as the second argument.

Using plain http handler funcs instead of http handler types that implement the `ServeHTTP` method is common enough that the standard library provides the alternatives [`http.HandleFunc`](https://pkg.go.dev/net/http#HandleFunc) and [`http.ServeMux.HandleFunc`](https://pkg.go.dev/net/http#ServeMux.HandleFunc). All `HandleFunc` does is what we do in the above example, it converts the passed in function to `http.HandlerFunc` and calls http.Handle with the result.

#### func (HandlerFunc) ServeHTTP - a HandlerFunc type
ServeHTTP calls f(w,r)

```go
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request)
```



The `http.HandlerFunc()` converts a function to the `http.Handler` type so that it can be used as a normal handler in the `http.Handle()` method.

In simple words, when you pass a function to the `http.HandlerFunc()`, it implements the `http.Handler` type automatically by adding `ServeHTTP()` method.

You don't have to write `ServeHTTP()` method manually for your function. Therefore, your normal function becomes a handler.

```go
package main

import (
	"fmt"
	"net/http"
)

// MyHandler is a simple HTTP handler function.
func MyHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprint(w, "Hello, this is my handler!")
}

func main() {
    // Convert MyHandler function to a HandlerFunc
    handlerFunc := http.HandlerFunc(MyHandler)

    // Use handlerFunc as an HTTP handler
    http.Handle("/myhandler", handlerFunc)

    // Start the web server
    http.ListenAndServe(":8000", nil)
}
```

## HandleFunc vs HandlerFunc

`HandleFunc` registers the handler function for the given pattern in the server mux (router).

You can pass define an anonymous function, as we have seen in the basic *Hello World* example:

```go
http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintln(w, "Hello, world!")
}
```

But we can also pass a `HandlerFunc` type. In other words, we can pass any function that respects the following signature:

```go
func FunctionName(w http.ResponseWriter, req *http.Request)
```

We can rewrite the previous example passing the reference to a previously defined `HandlerFunc`. Here's the full example:

```go
package main

import (
    "fmt"
    "net/http"
)

// A HandlerFunc function
// Notice the signature of the function
func RootHandler(w http.ResponseWriter, req *http.Request) {
    fmt.Fprintln(w, "Hello, world!")
}

func main() {
    // Here we pass the reference to the `RootHandler` handler function
    http.HandleFunc("/", RootHandler)
    panic(http.ListenAndServe(":8080", nil))
}
```

## Tips

* `http.Handle` takes a value of any type that implements the `http.Handler` interface, `http.HandlerFunc` is one of those types that implements that interface, but it's not the only one.
* `http.HandleFunc` takes in a function of type `http.HandlerFunc`. Note that `http.HandleFunc` is not the same as `http.Handle`.
* also note that for the argument to `http.HandleFunc` the method `ServeHTTP(...` is not important, what's important is that the signature, ie type, of the function that you pass in is the same as defined by the argument, ie. `func(http.ResponseWriter, *http.Request)`. The ServeHTTP method is only important for the `http.Handle` or anything that depends on the `http.Handler` interface which declares that method as one of its members.
