+++
title = "Why close the HTTP API response body in Golang? What if you don't..."
slug = "why-close-the-http-api-response-body-in-golang-what-if-you-dont"
author = "Suraj Narwade"
date = "2023-01-14T18:06:45.314Z"
category = "Blog"
series = ["HTTP using Golang"]
+++

In the [previous article](https://surajincloud.com/how-to-make-an-http-get-request-in-golang), we saw how to make the HTTP GET API request in Golang. While going through the example, we discussed closing the response body.


In this article, Let's discuss why it is essential to close the response body.


### **What is the response body?**


The response body is a stream of data that is read from the server.


### **Why should I close it?**


* Ensure all data has been read and the resources associated with it are freed up.


### **What happens if I don't close it?**


* It can lead to resource leaks.
* It can cause the program to consume more memory and resources than necessary, leading to performance issues.
* It can also cause issues with the underlying connection as transport may not reuse HTTP/1\.x “keep\-alive” TCP connections if the Body is not read to completion and closed.


From the official docs,


Link: [https://pkg.go.dev/net/http\#Client](https://pkg.go.dev/net/http#Client)



```
    // The http Client and Transport guarantee that Body is always
    // non-nil, even on responses without a body or responses with
    // a zero-length body. It is the caller's responsibility to
    // close Body. The default HTTP client's Transport may not
    // reuse HTTP/1.x "keep-alive" TCP connections if the Body is
    // not read to completion and closed.

```

### **What should I do?**


* Always use `defer` statement and write down the statement immediately. This will ensure that the response body gets closed even in case of runtime error during the reading and parsing of the response.


Here's the complete example,



```
    resp, err := http.Get("http://localhost:8080")
    if err != nil {
        log.Fatal(err)
    }
    defer resp.Body.Close()

```

Thanks for reading :)


This [blog post](https://andrii-kushch.medium.com/is-it-necessary-to-close-the-body-in-the-http-response-object-in-golang-171c44c9394d#:~:text=According%20to%20the%20documentation%20of,TCP%20connection%20to%20the%20server.) has a more detailed explanation for the same.


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


