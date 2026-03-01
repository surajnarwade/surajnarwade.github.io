+++
title = "Understanding HTTP Server in Go #3 - Handlers"
slug = "understanding-http-server-in-go-handlers"
author = "Suraj Narwade"
date = "2023-08-30T23:00:14.837Z"
category = "Blog"
series = ["HTTP using Golang"]
+++

Welcome to the exciting third instalment of our journey into creating web servers using Golang. In our previous articles, we covered the basics of creating a web server and explored the concept of custom servers using the `http.Server` struct.


***Check out the previous blog posts:  
\\* Part 1:\*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-basic***](https://surajincloud.com/understanding-http-server-in-go-basic)


***\\* part 2:\*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-using\-httpserver\-struct***](https://surajincloud.com/understanding-http-server-in-go-using-httpserver-struct)


However, as you might have noticed, our server was, until now, simply returning a 404 error. Fear not, because in this article, we're about to breathe life into our server by diving into the realm of handlers.


### **The Missing Piece: Handlers**


So far, our web server setup has been impressive, but it's been missing a crucial element \- a way to handle incoming requests effectively. This is where handlers come into play. In GoLang, a handler is an object that implements the `ServeHTTP` method with the following signature:



```
ServeHTTP(http.ResponseWriter, *http.Request)

```

Let's write our first handler,



```
package main

import (
    "fmt"
    "net/http"
)

type handler struct{}

func (h *handler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello World")
}
func main() {
    h := handler{}
    s := http.Server{
        Addr:    ":8080",
        Handler: &h,
    }
    s.ListenAndServe()
}

```

Inside the method, we're using the `fmt.Fprintf` function to write "Hello World" to the response writer, effectively sending this text as the response.


Now if you run this program and try to access it,



```
$ curl localhost:8080
Hello World

```

But here's where the real fun begins \- the power of routing! If you try accessing `/hi` or `/bye`, you'll notice that you still receive the "Hello World" response. This is because we haven't implemented specific routes yet.


For example,



```
$ curl localhost:8080/hello
Hello World

```

But we didn't define the hello endpoint, ideally, it should have been `404` isn't it, What could be the reason for this, it makes our webserver again useless.


This is because we haven't implemented specific routes yet and effectively we are not using any mux here.


In the next part of our series, we will see how we can have multiple handlers on our webserver. till then,


Happy Coding !!!


