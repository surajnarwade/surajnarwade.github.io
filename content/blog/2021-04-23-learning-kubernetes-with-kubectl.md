+++
title = "Learning Kubernetes with Kubectl"
slug = "learning-kubernetes-with-kubectl"
description = "A practical post about learning kubernetes with kubectl with lessons, examples, and useful takeaways."
author = "Suraj Narwade"
date = "2021-04-23T06:53:40.035Z"
category = "Blog"
+++

When we work around Kubernetes, we often have to reference the documentation for a few things to save time from the journey from terminal to browser and back. Kubectl offers a great set of help in itself.


Learning about the resources


The command shows all fields from the pod resource along with its type and information about it. You can also look for a subfield.


for example,



```
$ kubectl explain pod
$ kubectl explain pod.spec.containers

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672864952768/3cc29994-61ab-4f41-a6bc-f8d7472a6995.gif)


Sometimes what happens is you know exactly what you want in resource definition, but you forget the field or misspell it, `recursive` the flag comes in handy when it shows just the fields along with their type, whether it's a string, map, list, etc.



```
$ kubectl explain pod --recursive

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672753857536/uH94cfRyv.gif)


while `kubectl explain`is pretty useful to know all fields and their type and usage; when you are building some cool stuff like some CLI tool, plugin or maybe operator, you might have to explore and learn about APIs and JSON structures. You can do that with `kubectl`right from the terminal, `raw` flag for `kubectl get` comes in handy for it.




---


[https://surajincloud.gumroad.com/l/own\-kubectl\-command](https://surajincloud.gumroad.com/l/own-kubectl-command)
 




---


**Learning about APIs**



```
kubectl get --raw /

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672753860869/xSqvXWPGt.gif)


We can also explore the subpath, as shown in the above gif.


* There’s one more command which is quite handy to see what resources are registered in a given kubernetes cluster. It also shows whether a given resource is a namespace or not, and it’s the short names.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672753863222/s79KIshPSE.png)


I covered this blog post in my keynote talk at Kubernetes Community Days Bangalore keynote as well. For more, watch my talk here:



I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


