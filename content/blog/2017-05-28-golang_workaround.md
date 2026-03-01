+++
date = "2017-05-28T21:21:49+05:30"
title = "Golang workaround for cannot assign to struct field in map"
slug = "golang_workaround"
description = "A practical post about golang workaround for cannot assign to struct field in map with lessons, examples, and useful takeaways."
category = "Blog"
author = "Suraj Narwade"

+++

Yesterday, I was working one of the [Kompose](https://github.com/kubernetes-incubator/kompose) issue, and I was working on map of string to struct, while iterating over a map I wanted to change elements of struct, so I tried similar to this,

```
package main

import "fmt"

type Animal struct {
	count int
}

func main() {
	m := map[string]Animal{"cat": Animal{2}, "dog": Animal{3}, "mouse": Animal{5}}
        fmt.Println(m)
	m["dog"].count = 4
	
	fmt.Println(m)

}
```
so I got this error,

```
tmp/sandbox728133053/main.go:12: cannot assign to struct field m["dog"].count in map
```

After googling for some time, I found this solution and I tried & it worked as below:

```
package main

import "fmt"

type Animal struct {
	count int
}

func main() {
	m := map[string]Animal{"cat": Animal{2}, "dog": Animal{3}, "mouse": Animal{5}}

	fmt.Println(m)

	var x = m["dog"]
	x.count = 4
	m["dog"] = x

	fmt.Println(m)


}
```

I found one more way to do this by storing pointers as shown below:

```
package main

import "fmt"

type Animal struct {
	count int
}

func main() {
	m := map[string]*Animal{"cat": &Animal{2}, "dog": &Animal{3}, "mouse": &Animal{5}}
	fmt.Printf("%#v\n",m["dog"])
	
	m["dog"].count = 4

	fmt.Printf("%#v", m["dog"])

}
```

``Happy Hacking !!!``

#### Reference:
 
 * https://github.com/golang/go/issues/3117 

