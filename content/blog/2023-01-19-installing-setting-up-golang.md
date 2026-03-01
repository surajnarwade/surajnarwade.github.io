+++
title = "Installing & Setting up Golang"
slug = "installing-setting-up-golang"
author = "Suraj Narwade"
date = "2023-01-19T12:00:42.122Z"
category = "Blog"
+++

Golang, also known as Go, is a programming language developed by Google. Today, it is used by many organisations for their web applications and CLI tools. Many OpenSource projects in cloud\-native spaces, such as Kubernetes and Docker, were written using Go.


Golang is known for its simplicity, efficiency, and scalability. This blog post will walk you through installing and setting up Golang on your computer.


**Downloading the Go**


* Go to this link <https://go.dev/dl/> and select the build depending on your operating system, as shown in the following image.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673647935746/fefb98c8-8821-48d6-a4a2-51a8003aa0b1.png)


At the time of writing this blog, 1\.19\.5 was the latest stable version of Golang.


**Installing Golang**


Once the binary is downloaded, you can install it on your computer by following the instructions on the link <https://go.dev/doc/install>. The installation process is different for each operating system, so follow the instructions specific to your OS.


**Verify the Installation**


To verify that Golang has been successfully installed, open a terminal and run the following command:



```
$ go version

```

This will display the version of Golang that is currently installed on your computer.


In my case, it showed,



```
$ go version
go version go1.19.5 linux/amd64

```

**Let's write Code**


Once Golang is installed and set up, you can start writing code.



```
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}

```

You can run the program by using the following command,



```
$ go run main.go
Hello, World!

```

In conclusion, installing and setting up Golang is a straightforward process that can be completed in just a few steps. With Golang installed, you can start writing efficient, scalable, and simple code in no time.


I hope you learnt something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you!


