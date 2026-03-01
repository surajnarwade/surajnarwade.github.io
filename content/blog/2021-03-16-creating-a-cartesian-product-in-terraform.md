+++
title = "Creating a Cartesian Product in Terraform"
slug = "creating-a-cartesian-product-in-terraform"
description = "Learn how to create a cartesian product in terraform with a practical example."
author = "Suraj Narwade"
date = "2021-03-16T16:24:04.027Z"
category = "Blog"
+++

For one of my terraform tasks, I needed all the possible combinations of elements from all given sets. For example, there are two sets.



```
set1 = ["a","b","c"]
set2 = [1,2]

```

The expected result is,



```
[["a",1], ["a",2],["b",1],["b",2],["c",1],["c",2]]

```

After searching about this, I learned that such a concept is called the [Cartesian Product](https://en.wikipedia.org/wiki/Cartesian_product). From the Wikipedia definition,



> the **Cartesian product** of two [sets](https://en.wikipedia.org/wiki/Set_%28mathematics%29) *A* and *B*, denoted *A* × *B*, is the set of all [ordered pairs](https://en.wikipedia.org/wiki/Ordered_pair) (*a*, *b*) where *a* is in *A* and *b* is in *B*.


In my mind, I thought there had to be a nested for looping \& tried something and failed miserably.


#### **But we think from the Programmer's perspective, but Terraform is not a Programming Language.**


Hence, After searching a lot, I came across this amazing function called [setproduct](https://www.terraform.io/docs/language/functions/setproduct.html), which basically provides Cartesian product out of the box,



```
locals{
b = ["a", "b", "c"]
a = [1, 2]
}

output "example" {
value = setproduct(local.b, local.a)}

```

and the output is amazing and lifesaving,



```
[
 [
  "a",
  1,
 ],
 [
  "a",
  2,
 ],
 [
  "b",
  1,
 ],
[
  "b",
  2,
 ],
 [
  "c",
  1,
 ],
 [
  "c",
  2,
 ],
 ]

```

If you have successfully created a cartesian product without using `setproduct()`, do comment here :)


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


