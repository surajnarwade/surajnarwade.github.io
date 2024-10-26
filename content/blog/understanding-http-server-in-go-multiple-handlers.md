+++
title = "Understanding HTTP Server in Go #4 - multiple handlers"
author = "Suraj Narwade"
date = "2023-08-31T07:53:51.667Z"
category = "Blog"
series = ["HTTP using Golang"]
+++

In the previous blog post, we made our webserver useful for the first time where we printed Hello World instead of 404\.


***Check out previous blog posts:***


***Part 1:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-basic***](https://surajincloud.com/understanding-http-server-in-go-basic)


***Part 2:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-using\-httpserver\-struct***](https://surajincloud.com/understanding-http-server-in-go-using-httpserver-struct)


***Part 3:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-handlers***](https://surajincloud.com/understanding-http-server-in-go-handlers)


It was still useless as it was printing the same response on all the paths.


Now in real real\-world scenario, we won’t have just one URL right, We will need multiple paths and handlers and we still don’t have mux so now in this case we can still have multiple handlers


We will need to define multiple structs along with their multiple serveHTTP methods


Here instead of passing one handler in the server struct, we will use the standard function called `http.Handle`


Let’s look at `http.Handle` function



```
func Handle(pattern string, handler Handler) { DefaultServeMux.Handle(pattern, handler) }

```

\| You can find the code for this function here: [https://pkg.go.dev/net/http\#Handle](https://pkg.go.dev/net/http#Handle)


`http.Handle` function will then attach a handler to `DefaultServeMux`



```
package main

import (
    "fmt"
    "net/http"
)

type THandler struct{}

func (t *THandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello World")
}

func main() {
    handler := THandler{}
    s := http.Server{
        Addr: ":8080",
    }
    http.Handle("/hello", &handler)
    s.ListenAndServe()
}

```

and now you can add more handlers,



```
package main

import (
    "fmt"
    "net/http"
)

type Handler1 struct{}

func (h *Handler1) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello World - 1")
}

type Handler2 struct{}

func (h *Handler2) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello World - 2")
}

func main() {
    handler1 := Handler1{}
    handler2 := Handler2{}
    s := http.Server{
        Addr: ":8080",
    }
    http.Handle("/hello1", &handler1)
    http.Handle("/hello2", &handler2)

    s.ListenAndServe()
}

```

Now let's run the server and check the output,



```
$ curl localhost:8080/hello1
Hello World - 1

$ curl localhost:8080/hello2
Hello World - 2

```

and if we try any other paths, we will receive 404 now,



```
$ curl localhost:8080/hello3
404 page not found

```

Now our server is kind of useful and working as intended, but we can further optimise it and make it more advanced, we will see that in upcoming blog posts.


Till then, Happy Coding !!!


