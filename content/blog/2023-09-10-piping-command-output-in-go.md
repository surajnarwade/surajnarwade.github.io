+++
title = "Piping Command Output in Go"
slug = "piping-command-output-in-go"
description = "A practical post about piping command output in go with lessons, examples, and useful takeaways."
author = "Suraj Narwade"
date = "2023-09-10T08:03:23.844Z"
category = "Blog"
+++

In a previous blog post, we learned how to create and run commands in Go. Now, let's explore how to recreate the following command:



```
ls -l | grep hello

```

Let's create the first command:



```
lsCmd := exec.Command("ls", "-l")

```

Next, create the second command:



```
grepCmd := exec.Command("grep", "hello")

```

To connect them, we need to understand what's happening in the Linux command. Essentially, we are taking the stdout of the first command `ls -l` and passing it to the stdin of the second command `grep hello`. To achieve this in Go, follow these steps:


1. Create a pipe for the stdout of the first command:



```
lsOutput, err := lsCmd.StdoutPipe()
if err != nil {
    fmt.Println("Error creating pipe for ls:", err)
    return
}

```

1. Start the first command:



```
if err := lsCmd.Start(); err != nil {
    fmt.Println("Error starting ls:", err)
    return
}

```

1. Set the stdin of the second command to the stdout of the first:



```
grepCmd.Stdin = lsOutput

```

1. Define the second command and start it:



```
grepOutput, err := grepCmd.StdoutPipe()
if err != nil {
    fmt.Println("Error creating pipe for grep:", err)
    return
}

if err := grepCmd.Start(); err != nil {
    fmt.Println("Error starting grep:", err)
    return
}

```

1. Add wait statements to ensure both commands are complete:



```
lsCmd.Wait()
grepCmd.Wait()

```

Now, let's read the output and print it.



```

grepResult, err := io.ReadAll(grepOutput)
if err != nil {
    fmt.Printf("Error reading command output: %v\n", err)
    return
}
fmt.Printf("%s\n", grepResult)

```

Now let's take a look at the complete program and run it,



```
package main

import (
    "fmt"
    "os/exec"
)

func main() {
    // Create the first command: ls -l
    lsCmd := exec.Command("ls", "-l")

    // Create the second command: grep hello
    grepCmd := exec.Command("grep", "hello")

    // Create a pipe for the stdout of the first command
    lsOutput, err := lsCmd.StdoutPipe()
    if err != nil {
        fmt.Println("Error creating pipe for ls:", err)
        return
    }

    // Start the first command
    if err := lsCmd.Start(); err != nil {
        fmt.Println("Error starting ls:", err)
        return
    }

    // Set the stdin of the second command to the stdout of the first
    grepCmd.Stdin = lsOutput

    // Create a pipe for the stdout of the second command
    grepOutput, err := grepCmd.StdoutPipe()
    if err != nil {
        fmt.Println("Error creating pipe for grep:", err)
        return
    }

    // Start the second command
    if err := grepCmd.Start(); err != nil {
        fmt.Println("Error starting grep:", err)
        return
    }

    // Wait for both commands to complete
    lsCmd.Wait()
    grepCmd.Wait()

    // Read and print the output of the second command (grep)
    grepResult, err := io.ReadAll(grepOutput)
    if err != nil {
        fmt.Printf("Error reading command output: %v\n", err)
        return
    }
    fmt.Println(string(grepResult))
}

```

the output will be:



```
go run hello.go 
-rw-r--r--  1 surajnarwade  staff     805 10 Sep 07:27 hello.go

```

Hope you learnt something interesting, Let me know in the comments :) 


Happy Coding!!!


