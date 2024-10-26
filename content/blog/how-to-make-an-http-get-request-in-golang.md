+++
title = "How to make an HTTP GET request in Golang?"
author = "Suraj Narwade"
date = "2023-01-13T16:33:28.575Z"
category = "Blog"
series = ["HTTP using Golang"]
+++

Golang is one of the widely used languages for designing API Clients. While you design the client, one of the important aspects of the client is fetching the data from the API in some format using a **GET** request.


Here's the `curl` command example, which mimics the GET request which we will write code for in Golang,



```
$ curl -XGET localhost:8080
{"message":"hello world !!!"}

```


> **Note**: I already have a basic webserver running, Hence I can get the output on localhost.


Now we know the curl command, Let's write the Go code for the same,



```
package main

import (
    "io"
    "net/http"
)

func main() {
    resp, err := http.Get("http://localhost:8080")
    if err != nil {
        log.Fatal(err)
    }
    defer resp.Body.Close()
    body, err := io.ReadAll(resp.Body)
    fmt.Println(string(body))
}

```

Let's run this program,



```
$ go run main.go
{"message":"hello world !!!"}

```

we get a similar output, which means we achieved what we wanted.


But let's understand what's happening here line by line,


* We are importing `net/http` the package consists of HTTP Client and Server implementations and many other HTTP\-related functionalities, which we will discuss in upcoming blogs.



```
import "net/http"

```

* Now we will use `http.Get()` the function to make the API call. This function takes `url` as input.



```
resp, err := http.Get("http://localhost:8080")

```

This `http.Get` returns the Response struct and an error.


* We should `defer` the response body closure. It can cause issues if we don't close it, so this is an important step.



```
defer resp.Body.Close()

```

* Once we have the response, we can use the `response.Body` field to read the response data. The `response.Body` field is an `io.ReadCloser`, which means it implements the `io.Reader` interface.



```
body, err := io.ReadAll(resp.Body)

```

Now, the `body` is of type `[]byte` so we will convert it to a string and then print the response.



```
fmt.Println(string(data))

```

This is a basic example of making a GET request in Golang. Many other options are available in the package, such as setting headers, specifying timeouts, and more, which we will see in upcoming blog posts.


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


