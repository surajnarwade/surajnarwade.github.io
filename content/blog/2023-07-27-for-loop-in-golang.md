+++
title = "For loop in Golang"
slug = "for-loop-in-golang"
author = "Suraj Narwade"
date = "2023-07-27T07:01:09.351Z"
category = "Blog"
+++

Unlike other Languages, Golang does not have multiple looping constructs. It has only one `for`.


But, I think that's enough to cover all the use cases, let's see different variations of loops.


Basic Loop
----------


In basic for loop, there are 3 elements,


* initialization: `i := 1`
* condition: `i<=5`
* post: `i++`


loop will continue to run until the postcondition is true.



```
    for i := 1; i <= 5; i++ {
        fmt.Println(i)
    }

```

output:



```
1
2
3
4
5

```

While loop
----------


a loop will continue to run until the condition (in this case `n<5` ) is true



```
n := 1
for n < 5 {
    n = n + 1
}
fmt.Println(n)

```

output:



```
5

```

Infinite loop
-------------



```
n := 0
for {
    n = n + 1 
}
fmt.Println("hello",n)

```

there will be no output as the loop will never end.


Loop over Slice or Array
------------------------


when looping over a slice or array, `n` is an index (starting from 0\) of the element `i`



```
a := []string{"hello", "world"}
for n, i := range a {
    fmt.Println(i, s)
}

```

output:



```
0 hello
1 world

```

in some use cases, you may not need the index, then you can simply ignore it as shown,



```
a := []string{"hello", "world"}
for _, i := range a {
    fmt.Println(s)
}

```

output:



```
hello
world

```

Loop over Maps
--------------



```
m := map[string]int{
    "london":  1,
    "newyork": 2,
    "dublin":  3,
}
for k, v := range m {
    fmt.Println(k, v)
}

```

output:



```
london 1
newyork 2
dublin 3

```

Breaking the loop
-----------------


In all the for loops we have seen above (especially infinite loop) sometimes there can be use cases where we want to break out of the loop if a certain condition is met.


we can do this with the help of `break` a statement. Let's take the same infinite loop example to understand this, but you can use this in any for loop variations.



```
n := 0
for {
    n = n + 1 
    if n == 5 {
        break
     }
}
fmt.Println("hello",n)

```

now there will be output,



```
hello 5

```

Skipping the iteration
----------------------


sometimes, you may want to skip the given iteration based on some condition, you can do that with the help of `continue` statement, for example,



```
    for i := 1; i <= 5; i++ {
        if i == 4 {
            continue
        }
        fmt.Println(i)
    }

```

output:



```
1
2
3
5

```

**Conclusion**
--------------


'For' loops are an essential component of any programming language, including Go. In this blog post, we covered the basic syntax of 'for' loops, infinite loops, iteration over arrays and slices, using 'continue' to skip iterations, and utilizing 'break' to exit a loop prematurely. With this knowledge, you will be able to confidently use 'for' loops to solve various use cases in Go.


Happy coding!


