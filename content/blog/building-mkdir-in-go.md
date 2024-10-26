+++
title = "Building mkdir (linux) command in Go"
author = "Suraj Narwade"
date = "2024-10-25T14:20:49.779Z"
category = "Blog"
series = ["Linux commands using Golang"]
+++

One of the best way to learn language is to try recreating familiar linux command in it. In one of the previous post, we have seen how we can implement simple `which` command, you can check the [blog post here](https://surajnarwade.com/blog/building-which-linux-command-in-go/).

In this blog post, we will try to implement `mkdir` command, Let’s get started.

`mkdir` is one of the important commands in Linux which helps to create directories.

we will cover following in the blog post:

- mkdir command
- mkdir with -p flag
- mkdir with -m flag

**\| *Here's the man page for the mkdir command:*** [***https://linux.die.net/man/1/mkdir***](https://linux.die.net/man/1/mkdir)

### mkdir command

Let’s try running mkdir command and understand what it does,

```
mkdir mydirectory
```

let’s see what it has created,

```
drwxr-xr-x   2 suraj.narwade  staff    64 25 Oct 14:22 mydirectory
```

it has created the `mydirectory` directory with `755` permissions.

Let’s implement this in Go,

```
package main

import (
	"fmt"
	"os"
)

func main() {
	// Check if the user provided a directory name
	if len(os.Args) < 2 {
		fmt.Println("Usage: mkdir directory_name ...")
		return
	}

	dirName := os.Args[1]

	// Create the directory
	err := os.Mkdir(dirName, 0755)
	if err != nil {
		fmt.Printf("Error creating directory: %s\n", err)
		return
	}
}

```

### mkdir command with -p flag

when `-p` flag is specified, it will create all the intermediate directories provided as well, for example,

```
mkdir -p mydirectory/a/b/c
```

it will create all the directories, `mydirectory`, `a`, `b`, `c`

let’s implement this,

```
package main

import (
	"fmt"
	"os"
)

func main() {
	if len(os.Args) < 2 {
		fmt.Println("Usage: mkdir [-p] directory_name ...")
		return
	}

	// Check if the -p flag is passed
	var dirName string
	createParents := false

	if os.Args[1] == "-p" {
		createParents = true
		if len(os.Args) < 3 {
			fmt.Println("Usage: mkdir [-p] directory_name ...")
			return
		}
		dirName = os.Args[2]
	} else {
		dirName = os.Args[1]
	}

	// Create the directory (with or without parent directories)
	var err error
	if createParents {
		err = os.MkdirAll(dirName, 0755) // MkdirAll creates parents if necessary
	} else {
		err = os.Mkdir(dirName, 0755)
	}

	if err != nil {
		fmt.Printf("Error creating directory: %s\n", err)
		return
	}
}

```

### mkdir command with -m flag

Till now, default file permission were `755` but what if we want to set different permissions. we can do so with `-m` flag. Let’s try it out.

```
mkdir -m 700 mydirectory
```

it has created `mydirectory` with `700` permission.

```
drwx------  2 suraj.narwade  wheel   64 25 Oct 14:37 mydirectory
```

okay, now let’s `-m` flag and finalise our code for `mkdir` .

```
package main

import (
	"fmt"
	"os"
	"strconv"
)

func main() {
	if len(os.Args) < 2 {
		fmt.Println("Usage: mkdir [-p] [-m mode] directory_name ...")
		return
	}

	// Default mode and flags
	mode := os.FileMode(0755) // Default permissions
	createParents := false
	dirName := ""

	// Parsing arguments for flags
	for i := 1; i < len(os.Args); i++ {
		arg := os.Args[i]

		if arg == "-p" {
			createParents = true
		} else if arg == "-m" && i+1 < len(os.Args) {
			// Parse mode from next argument
			modeArg := os.Args[i+1]
			parsedMode, err := strconv.ParseUint(modeArg, 8, 32)
			if err != nil {
				fmt.Printf("Invalid mode: %s\n", modeArg)
				return
			}
			mode = os.FileMode(parsedMode)
			i++ // Skip next argument since it's the mode
		} else {
			dirName = arg
		}
	}

	// Ensure directory name is provided
	if dirName == "" {
		fmt.Println("Error: Directory name is required.")
		return
	}

	// Create the directory
	var err error
	if createParents {
		err = os.MkdirAll(dirName, mode) // MkdirAll with custom mode
	} else {
		err = os.Mkdir(dirName, mode)
	}

	if err != nil {
		fmt.Printf("Error creating directory: %s\n", err)
		return
	}
}
```

Hope you learnt something new, try it out and let me know if you have any suggestions or any comments.

Till then, happy coding 😊
