+++
title = "Understanding HTTP Server in Go #1 - Basic"
author = "Suraj Narwade"
date = "2023-08-29T20:40:46.064Z"
category = "Blog"
series = ["HTTP using Golang"]
+++

The `net/http` package in Go offers a powerful set of tools for constructing HTTP servers to handle incoming requests and send responses. In this article, we'll dive into setting up a basic HTTP server.


Before we delve into the specifics, let's familiarize ourselves with a couple of key terms:


* **Multiplexer (or Mux)**: This is an essential component of an HTTP server that routes incoming requests to the appropriate handlers based on the requested paths or URLs. we will see this in detail in upcoming posts.


With this foundation in mind, let's explore how the `net/http` package simplifies the process of creating an HTTP server.


**Starting a Basic HTTP Server**
--------------------------------


The `net/http` package makes it remarkably easy to create an HTTP server. The `http.ListenAndServe` function serves as a gateway to launching a server. Here's a simple example of its usage:



```
goCopy code
package main

import (
     "net/http"
)

func main() {
     http.ListenAndServe(":8080", nil)
}

```

In this example, we use `http.ListenAndServe` to create a server that listens on port 8080 for incoming requests. The second argument `nil` signifies that we are using the default multiplexer, known as `DefaultServeMux`, to handle the incoming requests.


(second argument is usually a handler but we will see how that works in upcoming blog posts)


**Demystifying ListenAndServe**
-------------------------------


The `ListenAndServe` function is a convenient way to initiate an HTTP server, but let's explore what's happening under the hood.


Here's a closer look at the `ListenAndServe` function:


Ref: [https://pkg.go.dev/net/http\#ListenAndServe](https://pkg.go.dev/net/http#ListenAndServe)



```
func ListenAndServe(addr string, handler Handler) error {
    server := &Server{Addr: addr, Handler: handler}
    return server.ListenAndServe()
}

```

This function constructs a `Server` struct with the provided address and handler. It then invokes the `ListenAndServe` method on this struct.


**Inside ListenAndServe**
-------------------------


The `ListenAndServe` method orchestrates the process of setting up a server:


Ref: [https://cs.opensource.google/go/go/\+/refs/tags/go1\.21\.0:src/net/http/server.go;l\=2973](https://cs.opensource.google/go/go/+/refs/tags/go1.21.0:src/net/http/server.go;l=2973)



```
func (srv *Server) ListenAndServe() error {
    if srv.shuttingDown() {
        return ErrServerClosed
    }
    addr := srv.Addr
    if addr == "" {
        addr = ":http"
    }
    ln, err := net.Listen("tcp", addr)
    if err != nil {
        return err
    }
    return srv.Serve(ln)
}

```

This method checks for a shutdown state and determines the address to listen on. If no address is provided, the default is set to port 80 (HTTP). The `net.Listen` function is then used to create a listener interface for incoming connections on all the network interfaces.


**The Serving Process**
-----------------------


The `Serve` method is pivotal in the server setup:


Ref: [https://cs.opensource.google/go/go/\+/refs/tags/go1\.21\.0:src/net/http/server.go;l\=3026](https://cs.opensource.google/go/go/+/refs/tags/go1.21.0:src/net/http/server.go;l=3026)



```
func (srv *Server) Serve(l net.Listener) error {
    // ... (error handling and graceful shutdown code omitted)
    for {
        rw, e := l.Accept()
        // ... (error handling code omitted)
        go srv.ServeHTTP(rw, req)
    }
}

```

The `Serve` method continuously accepts incoming connections. Each connection is assigned to a goroutine and the `ServeHTTP` method is invoked to handle the incoming request.


Now if you run the simple webserver, you will get `404` what makes this webserver pretty useless but a really good example to understand the internals.


In upcoming posts, we will learn further interesting stuff about net/http and running a web server in Golang and also make this webserver more usable.


Happy Coding !!!


