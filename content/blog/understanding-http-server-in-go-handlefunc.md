+++
title = "Understanding HTTP Server in Go #5 - HandleFunc"
author = "Suraj Narwade"
date = "2023-08-31T08:25:55.190Z"
category = "Blog"
+++

Welcome back to the fifth instalment of our in\-depth exploration of creating dynamic web servers using Golang! In this post, we're diving into the fascinating world of `HandlerFunc`. As you've come to expect, GoLang provides us with an elegant and powerful way to handle requests through this mechanism. Let's embark on this journey of discovery.


***Check out previous posts:***


***Part 1:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-basic***](https://surajincloud.com/understanding-http-server-in-go-basic)


***Part 2:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-using\-httpserver\-struct***](https://surajincloud.com/understanding-http-server-in-go-using-httpserver-struct)


***Part 3:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-handlers***](https://surajincloud.com/understanding-http-server-in-go-handlers)


***Part 4:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-multiple\-handlers***](https://surajincloud.com/understanding-http-server-in-go-multiple-handlers)


Let's dive into the code to uncover the magic behind `HandleFunc`:



```
func HandleFunc(pattern string, handler func(ResponseWriter, *Request)) {
    DefaultServeMux.HandleFunc(pattern, handler)
}

```

\| You can find the code here: [https://pkg.go.dev/net/http\#HandleFunc](https://pkg.go.dev/net/http#HandleFunc)


As you can see, `HandleFunc` takes in a URL pattern and a handler function. It then registers this handler function with the `DefaultServeMux`, allowing it to be invoked when the corresponding URL is accessed.


Taking it a step further, let's peek into the `HandleFunc` method itself:



```
func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request)) {
    if handler == nil {
        panic("http: nil handler")
    }
    mux.Handle(pattern, HandlerFunc(handler))
}

```

\| You can find the code here: [https://pkg.go.dev/net/http\#ServeMux.HandleFunc](https://pkg.go.dev/net/http#ServeMux.HandleFunc)


Here, `HandleFunc` within a custom `ServeMux` instance ensures that the provided handler function is not nil. Once validated, it invokes the underlying `Handle` method, associating the pattern with an `HandlerFunc` instance that wraps the provided handler function.


Let's understand this with the help of example,



```
package main

import (
    "fmt"
    "net/http"
)

func Handler1(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello World - 1")
}

func main() {
    s := http.Server{
        Addr: ":8080",
    }

    http.HandleFunc("/hello1", Handler1)

    s.ListenAndServe()
}

```

In this example, we define a `Handler1` function that writes "Hello World \- 1" to the response writer. We then use `http.HandleFunc` to associate this handler with the `/test` URL path.


Let's test the code,



```
$ curl localhost:8080/hello1
Hello World - 1

```

We can now assign multiple handlers to paths quite easily.



```
package main

import (
    "fmt"
    "net/http"
)

func Handler1(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello World - 1")
}

func Handler2(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello World - 2")
}

func main() {
    s := http.Server{
        Addr: ":8080",
    }

    http.HandleFunc("/hello1", Handler1)
    http.HandleFunc("/hello2", Handler2)

    s.ListenAndServe()
}

```

With `HandlerFunc`, handling multiple routes becomes a breeze. By leveraging the power of this mechanism, you can create cleaner, more organized, and easily maintainable code.


Stay tuned for the next part of our journey, where we'll unravel the intricacies of dynamic routing using `http.ServeMux`. The world of GoLang web servers is evolving before our eyes, and we're equipping you with the knowledge to master it.


Happy coding!


