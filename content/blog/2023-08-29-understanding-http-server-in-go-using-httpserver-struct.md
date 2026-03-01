+++
title = "Understanding HTTP Server in Go #2 - using http.Server struct"
slug = "understanding-http-server-in-go-using-httpserver-struct"
author = "Suraj Narwade"
date = "2023-08-29T23:00:12.435Z"
category = "Blog"
series = ["HTTP using Golang"]
+++

Welcome back to the second part of our exploration into building web servers using GoLang! In the [previous blog post](https://surajincloud.com/understanding-http-server-in-go-basic), we discussed creating a basic web server using the `http.ListenAndServe` function.


However, this does not give us more control over the server config and to gain more control and flexibility over the server's behaviour, you're in for a treat. In this article, we'll delve into the world of custom servers by utilizing the `http.Server` struct.


The code is very straightforward,



```
package main

import "net/http"

func main() {
    // Creating an instance of http.Server
    s := http.Server{
        Addr:    ":8080",
        Handler: nil, // You can replace this with your own handler logic
    }

    // Starting the server
    s.ListenAndServe()
}

```

You can find the server struct here: [https://pkg.go.dev/net/http\#Server](https://pkg.go.dev/net/http#Server)


and the `ListenAndServe` method on the server struct can be found here: [https://pkg.go.dev/net/http\#Server.ListenAndServe](https://pkg.go.dev/net/http#Server.ListenAndServe)


This is the exact same method that we saw in the previous blog post.


Basically instead of passing `Addr` and `Handler` as an input and then implicitly creating Server struct, we are defining the struct beforehand.


Let's take a look at this server struct,



```
type Server struct {
    Addr                          string
    Handler                       Handler
    DisableGeneralOptionsHandler  bool
    TLSConfig                     *tls.Config
    ReadTimeout                   time.Duration
    ReadHeaderTimeout             time.Duration
    WriteTimeout                  time.Duration
    IdleTimeout                   time.Duration
    MaxHeaderBytes                int
    TLSNextProto                  map[string]func(*Server, *tls.Conn, Handler)
    ConnState                     func(net.Conn, ConnState)
    ErrorLog                      *log.Logger
    BaseContext                   func(net.Listener) context.Context
    ConnContext                   func(ctx context.Context, c net.Conn) context.Context
    inShutdown                    atomic.Bool
    disableKeepAlives             atomic.Bool
    nextProtoOnce                 sync.Once
    nextProtoErr                  error
    mu                            sync.Mutex
    listeners                     map[*net.Listener]struct{}
    activeConn                    map[*conn]struct{}
    onShutdown                    []func()
    listenerGroup                 sync.WaitGroup
}

```

Along with `Addr` and `Handler` You can now set various timeouts, context, logger, etc


Similar to a [previous blog post](https://surajincloud.com/understanding-http-server-creation-in-go-with-nethttp), as you run this code, you will still get `404` As we have not defined any handler as such this server is technically useless but a good example to understand the internals.


In the upcoming blog post, we will see how to define a handler and make this server more usable.


Till then, Happy Coding!!!


