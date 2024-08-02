+++
title = "Difference between Setting & Adding the Headers in HTTP API in Golang"
author = "Suraj Narwade"
date = "2023-01-18T07:30:42.274Z"
category = "Blog"
+++

While working with APIs, headers are an essential aspect. Sometimes we set them, or sometimes we consume them and make decisions.


When I was exploring headers in Golang, I came across two methods. `Headers.Set()` \& `Headers.Add()` Initially, I thought they were the same. But then I wondered if they are the same, then why two methods?


Let's understand.


I will be using a proxy which will use `Add` \& `Set` on headers, as shown below,



```
func (p Proxy) handle(w http.ResponseWriter, r *http.Request) {
    r.Header.Add("Header1", "head1")
    r.Header.Set("Header2", "head2")
    p.proxy.ServeHTTP(w, r)
}

```

Let's try to curl the server along with Headers,



```
$ curl -i http://localhost:8080 -H 'header1: foo' -H 'header2: bar'
HTTP/1.1 200 OK
Content-Length: 34
Content-Type: text/plain; charset=utf-8
Date: Tue, 17 Jan 2023 20:46:34 GMT

header1: [head1 foo], header2: [bar]

```

**What happened here?**


* For `header1`, we were using `Add` method. When we tried passing value to the same header, it returned an array. So basically, it is appended to the existing value.


 *Official documentation reference:* [https://pkg.go.dev/net/http\#Header.Add](https://pkg.go.dev/net/http#Header.Add)
* For `header2`, we were using `Set` method. When we tried passing value to the same header, it didn't return as Array with old and newly appended values. Instead, it replaced the old value.


 *Official documentation reference:* [https://pkg.go.dev/net/http\#Header.Set](https://pkg.go.dev/net/http#Header.Set)


### Reference


* [https://tachingchen.com/blog/pitfall\-of\-golang\-header\-operation/](https://tachingchen.com/blog/pitfall-of-golang-header-operation/)


I hope you learnt something new from this blog post. Click [here](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you!


