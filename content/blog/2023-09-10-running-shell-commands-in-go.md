+++
title = "Running Shell Commands in Go using os/exec"
slug = "running-shell-commands-in-go"
author = "Suraj Narwade"
date = "2023-09-10T07:52:58.664Z"
category = "Blog"
+++

Sometimes, there are certain use cases where you may have to run shell commands through a Golang app. This is where the `os/exec` package comes in handy.


If you want to run a command like `sleep 1`, you can simply do the following:



```
cmd := exec.Command("sleep", "1")
err := cmd.Run()

```

This will run the command and wait until the execution finishes, but it doesn't capture the stdout.


If you intend to capture both stdout and stderr, you can use the following code:



```
cmd := exec.Command("ls", "-l")
stdoutStderr, err := cmd.CombinedOutput()
if err != nil {
    log.Fatal(err)
}

```

It's called "combined output" because it returns both stdout and stderr.


If you are interested in only the stdout, you can do the following:



```
out, err := exec.Command("ls", "-l").Output()
if err != nil {
    log.Fatal("Error:", err)
}
fmt.Println(string(out))

```

Additionally, you can print the command itself (useful for debugging purposes):



```
cmd := exec.Command("ls", "-l")
fmt.Println(cmd.String())

```

Happy coding!


