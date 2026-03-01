+++
title = "fmt in Golang - formatting I/O"
slug = "fmt-in-golang"
author = "Suraj Narwade"
date = "2023-07-30T19:29:54.197Z"
category = "Blog"
+++

`fmt` is one of the essential packages in Golang. `fmt` stands for "format." This package allows us to format strings, print output, and interact with the standard input/output streams.


In this blog post, we will explore the key functionalities of the `fmt` package.


you can import the package as shown:



```
import "fmt"

```

Printing to standard output
---------------------------


There are various functions available for printing such as `fmt.Println` , `fmt.Print` and `fmt.Print`


### Println


This will print a line with a new line at the end



```
fmt.Println("Hello World")

```

**output:**



```
Hello, World!

```

### Print


This will print but no newline at the end


**output:**



```
fmt.Print("Hello World")

```

### Printf


This will format as per the format specifier and print without a newline at the end



```
fmt.Printf("My name is %s.\\n", name)

```

Here are some examples of format specifiers,



```
%s: string
%d: integer
%f: float
%t: boolean
%v: any value based on its type

```

Note: One of the noticeable differences between print and println is that print adds Spaces between operands and println doesn't.


for example,



```
fmt.Println("hello", "world")
fmt.Print("hello", "world")

```

the output will be:



```
hello world
helloworld

```

Formatting without printing
---------------------------


using `Sprint` you can format the string and store it in a variable without printing to standard output, some of the available options are:


### Sprint


This will store the string in the given variable



```
value := fmt.Sprint("Hello World")

```

### Sprintf


This will format the string as per the format specifier and store it in the given variable



```
value := fmt.Sprintf("My name is %s", name)

```

### Sprintln


This will format the string as per the format specifier, add a new line at the end and store it in the given variable



```
value := fmt.Sprintln("My name is %s", name)

```

Reading input
-------------


For reading user input from the standard input or console, we can use `Scanf` , `Scan` , Scanln from the fmt package


### Scan


this reads space\-separated values until a whitespace is encountered



```
fmt.Print("Enter your name: ")
fmt.Scan(&name)

```

### Scanln


this reads a line of text until a newline is encountered



```
fmt.Print("Enter your age: ")
fmt.Scanln(&age)

```

### Scanf


this reads formatted input based on the format specifier



```
fmt.Print("Enter your age: ")
fmt.Scanf("%d", &age)

```

Writing to the specific output stream
-------------------------------------


using Fprint, we can format and write data to a specific output stream, such as a file or an `io.Writer` interface (for example, write formatted data to the response writer (`http.ResponseWriter`) when handling HTTP requests)


### Fprint



```
fmt.Fprint(os.Stdout, "Hello world")

```

### Fprintln



```
fmt.Fprintln(os.Stdout, "Hello world")

```

### Fprintf



```
func testHandler(w http.ResponseWriter, r *http.Request) {
    message := "Hello World"
    fmt.Fprintf(w, "%s", message)
}

```

There is much more interesting stuff in fmt, but we will see that in upcoming blogs. Till then,


Happy Coding :)


