+++
title = "Basic Authentication in HTTP API requests in Golang"
slug = "basic-authentication-in-http-api-requests-in-golang"
description = "A practical post about basic authentication in http api requests in golang with lessons, examples, and useful takeaways."
author = "Suraj Narwade"
date = "2023-02-12T14:05:38.996Z"
category = "Blog"
series = ["HTTP using Golang"]
+++

In Golang, implementing basic authentication in an HTTP API request is relatively straightforward.


Once we construct the request, then we have to call the `SetBasicAuth()` method and pass `username` \& `password`



```
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
)

func main() {
    client := &http.Client{}
    req, err := http.NewRequest("GET", "http://google.com", nil)
    if err != nil {
        log.Fatal(err)
    }
    req.SetBasicAuth("admin", "password")
    resp, err := client.Do(req)
    if err != nil {
        log.Fatal(err)
    }
    defer resp.Body.Close()
    bodyText, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("%s\n", bodyText)
}

```

It is equivalent of,



```
curl -u username:password http://example.com

```

or



```
curl -H "Authorization: Basic <base64 encoded username:password >" localhost:8080

```

Let's understand what `SetBasicAuth` is the method done behind the scene?


Ref: [https://github.com/golang/go/blob/fd0c0db4a411eae0483d1cb141e801af401e43d3/src/net/http/request.go\#L988](https://github.com/golang/go/blob/fd0c0db4a411eae0483d1cb141e801af401e43d3/src/net/http/request.go#L988)



```
func (r *Request) SetBasicAuth(username, password string) {
    r.Header.Set("Authorization", "Basic "+basicAuth(username, password))
}

func basicAuth(username, password string) string {
    auth := username + ":" + password
    return base64.StdEncoding.EncodeToString([]byte(auth))
}

```

it is adding Auth headers in the request, where username and password and encode to base64


This is a simple example of implementing basic authentication in an HTTP API request in Golang. Keep in mind that in production, it's not recommended to hardcode the credentials in the code. And it's best practice to use HTTPS for added security.



> Note: Basic Authentication is not the only way to authenticate a user, and it's not recommended to use it in case of sensitive information or data.


I hope you learnt something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you!


