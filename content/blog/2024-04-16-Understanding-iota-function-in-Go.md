+++
title = "Understanding iota function in Go"
slug = "Understanding-iota-function-in-Go"
description = "Understand iota function in go with clear explanations and practical examples."
author = "Suraj Narwade"
date = "2024-04-16T14:20:49.779Z"
category = "Blog"
+++

Iota is predefined identifier which is used in constant declaration in Go. It is used to simplify incrementing value of constants.

iota value always start at 0 and increments automatically by 1 for next constant.

here’s the syntax for the iota,

```
const (

a = iota // 0
b = iota // 1
c = iota // 2
)
```

or you can simply write it as,

```
const (

a = iota
b
c
)
```

Let’s look at simple example,

```
package main

import "fmt"

func main() {
    const (
        a  = iota // 0
        b         // 1
        c         // 2
    )
    
    fmt.Println(a, b, c)
}

```

output will be,

```
0 1 2
```

**Custom increment**

In some use cases, we might need different kind of increment or start at 1.

```
    const (
        A = iota * 2 // 0
        B            // 2
        C            // 4
    )

    const (
        X = iota + 1 // 1
        Y            // 2
        Z            // 3
```

If you want to skip the particular value, you can do so by putting `_` for example,

```
package main

import "fmt"

func main() {
    const (
        a  = iota // 0
        _  
        c         // 2
    )
    
    fmt.Println(a, c)
}

```

output will be,

```
0 2
```

## Use cases

### **Enumerated Constants**

In cases, where we want to assign integer value to constants, enumerations, it is used.

for example,

```
const (
    Sunday = iota // 0
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
)
```

### File permissions

```jsx
const (
    Read = 1 << iota  // 1 (001)
    Write             // 2 (010)
    Execute           // 4 (100)
)

permissions := Read | Execute
// You can check if the permission includes 'Write':
if permissions&Write != 0 {
    fmt.Println("Write permission granted")
}

```

### Log levels

```
const (
    Debug = iota  // 0
    Info          // 1
    Warn          // 2
    Error         // 3
)

logLevel := Warn
if logLevel >= Error {
    fmt.Println("Critical issue detected!")
}

```

### Units of Measurements

```
const (
    _   = iota // ignore first value by assigning to blank identifier
    KB = 1 << (10 * iota) // 1024
    MB                  // 1024 * 1024
    GB                  // 1024 * 1024 * 1024
)
```

### HTTP codes

```
const (
    OK       = iota + 200 // 200
    Created               // 201
    Accepted              // 202
)
```

Using `iota` makes your code cleaner, more maintainable, and less error-prone by eliminating the need to manually assign values in such scenarios.

If you know any other use cases which is not mentioned in the blog, do let me know in the comments.

thank you for reading :)
