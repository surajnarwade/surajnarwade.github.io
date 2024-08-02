+++
title = "Understanding HTTP Server in Go #6 - Mux"
author = "Suraj Narwade"
date = "2023-09-01T23:00:12.747Z"
category = "Blog"
+++

Up until now, we've been using the `DefaultServeMux`, but it's time to wield more control and precision by explicitly using the `http.ServeMux`. Plus, we'll look at the powerful frameworks available, like Gorilla Mux and Echo, that take web server functionality to new heights.


***Check out previous posts:***


* ***Part 1:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-basic***](https://surajincloud.com/understanding-http-server-in-go-basic)
* ***Part 2:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-using\-httpserver\-struct***](https://surajincloud.com/understanding-http-server-in-go-using-httpserver-struct)
* ***Part 3:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-handlers***](https://surajincloud.com/understanding-http-server-in-go-handlers)
* ***Part 4:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-multiple\-handlers***](https://surajincloud.com/understanding-http-server-in-go-multiple-handlers)
* ***Part 5:*** [***https://surajincloud.com/understanding\-http\-server\-in\-go\-handlefunc***](https://surajincloud.com/understanding-http-server-in-go-handlefunc)


### **Why do we need this?**


While the `DefaultServeMux` In Golang does a great job of handling basic routing, there will come a point where you'll want more control over your application's routes. This is where the `http.ServeMux` steps in. It empowers you to explicitly define how your application responds to different routes, giving you fine\-grained control over your server's behaviour.


Some of the benefits of using custom mux over the default one,


* **Explicit Control:** When you create your own `http.ServeMux`, you have complete control over how routes are defined and handled. This allows for more explicit and organized route management, making it easier to understand and maintain your code.
* **Isolation:** By using a custom `http.ServeMux`, you isolate your routes and handlers from other parts of your application. This can help prevent naming clashes and ensure that your route\-handling logic is contained within the relevant context.
* **Reusability:** Custom muxes enable you to define route handlers as separate functions that can be reused across different applications or parts of your codebase. This promotes modularity and code reuse, as you can easily swap or reuse handlers as needed.
* **Middleware Support:** While the default `http.ServeMux` lacks built\-in middleware support, using a custom mux allows you to implement middleware at the mux level. This can include authentication, logging, or any other pre\-processing or post\-processing logic you need.
* **Fine\-Grained Control:** With a custom mux, you can design advanced routing scenarios. You might want to conditionally route requests based on headers, query parameters, or other contextual information. This level of control isn't readily achievable with the default mux.
* **Framework Integration:** If you plan to use third\-party web frameworks like Gorilla Mux or Echo, understanding and working with custom muxes provides a solid foundation. These frameworks often require you to set up custom muxes for enhanced routing and middleware capabilities.


Let's dive into the code and see how we can use `http.ServeMux` to create explicit routes for our web server:



```
package main

import (
    "fmt"
    "net/http"
)

func homeHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello World!")
}

func aboutHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "About Us Page - Coming Soon")
}

func main() {
    mux := http.NewServeMux() // Creating a new ServeMux instance

    mux.HandleFunc("/", helloHandler)
    mux.HandleFunc("/about", aboutHandler)

    // Creating the server with our custom ServeMux
    s := http.Server{
        Addr:    ":8080",
        Handler: mux, // Using our custom ServeMux
    }

    s.ListenAndServe()
}

```

This modular approach not only makes the code cleaner and more readable, but it also allows for better organization and potential reuse of route handlers.


but there are multiple implementations that give extra capabilities. Some of the examples are:


* httprouter: <https://github.com/julienschmidt/httprouter>
* echo: <https://github.com/labstack/echo>
* gorilla mux: <https://github.com/gorilla/mux>


We will see more advanced concepts in an upcoming post in our quest to learn more about golang webservers.


Stay curious and keep coding!


