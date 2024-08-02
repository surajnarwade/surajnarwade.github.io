+++
title = "Building which (Linux) command in Go"
author = "Suraj Narwade"
date = "2023-09-09T10:48:33.097Z"
category = "Blog"
+++

One of the best ways to learn a language is via building equivalent Linux commands using the language of your choice.


`which` is my all\-time favourite command to see if the binary is present on the server or not. You can see which usage as shown below,



```
$ which kubectl-eks
/Users/surajnarwade/.krew/bin/kubectl-eks

```

We simply pass the binary name to which command and it will give us the absolute path to the binary.


**\| *Here's the man page for the which command:*** [***https://linux.die.net/man/1/which***](https://linux.die.net/man/1/which)


Now let's try to write it in Golang,



```
package main

import (
    "fmt"
    "log"
    "os"
    "os/exec"
)

func main() {
    path, err := exec.LookPath(os.Args[1])
    if err != nil {
        log.Fatalf("Error: %s", err)
    }
    fmt.Printf("%s is available at %s\n", os.Args[1], path)
}

```

Let's run the program,



```
$ go run main.go kubectl-eks
kubectl-eks is available at /Users/surajnarwade/.krew/bin/kubectl-eks

```

Let's understand the program step by step,


* In the following step, we are passing an argument to the program and we are using it as an input to `exec.Lookpath` the function, Here's the [doc link](https://pkg.go.dev/os/exec#LookPath) for the function.



```
  path, err := exec.LookPath(os.Args[1])

```
* then we simply handle the error and print the `path` variable.


Now, in a similar way, you can try other commands using standard Golang libraries. Let me know in the comments if you tried any.


Happy Coding!!!


