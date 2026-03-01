+++
title = "View Kubernetes Secrets easily"
slug = "view-kubernetes-secrets-easily"
description = "Learn how to view kubernetes secrets easily more easily with a practical approach."
author = "Suraj Narwade"
date = "2022-12-13T16:34:15.025Z"
category = "Blog"
+++

Often we find it difficult to visualize the Kubernetes secrets as they are base64 encoded. You will need to manually copy the encoded data and then decode it or write small custom shell scripts for the same.


Fortunately, there’s a kubectl plugin to simplify this process. `view-secret` The plugin allows users to view the contents of a secret without having to decode it manually.


Check out the project on GitHub:


[https://github.com/elsesiy/kubectl\-view\-secret](https://github.com/elsesiy/kubectl-view-secret)
 


If you find the tool useful, make sure you ⭐️ the repo and show your love for the project :)


To use the `kubectl view-secret` plugin, you first need to install it on your system. You can do this by running the following command:



```
$ kubectl krew install view-secret

```

Note: above command will only work if you have [Krew](https://krew.sigs.k8s.io/) installed.


* You can also install the binary from the [release page](https://github.com/elsesiy/kubectl-view-secret/releases).


Once the plugin is installed, you can use it by running the following command:



```
$ kubectl view-secret [SECRET_NAME]

```

This will display the contents of the secret in plain text. For example, if you have a secret named. `mysecret`,



```
$ kubectl get secret mysecret -o yaml
apiVersion: v1
data:
  foo: YmFy
kind: Secret
metadata:
  name: my-secret
  namespace: default
type: Opaque

```

Ideally, you would have to decode the value manually and view the secret. For example,



```
$ echo "YmFy" | base64 -d
bar

```

with `view-secret` the plugin, you can view its contents by running the following command:



```
$ kubectl view-secret mysecret
Choosing key: foo
bar

```

This will print the contents of the secret in plain text, allowing you to easily view and manage the secret without having to decode it manually.


another interesting use case can be if a secret has more than one key value in it,



```
$ kubectl get secret mysecret -o yaml
apiVersion: v1
data:
  bar: Zm9v
  foo: YmFy
kind: Secret
metadata:
  name: mysecret
  namespace: default
type: Opaque

```

you can now explore secret using the plugin as follow,



```
$ kubectl view-secret mysecret
Multiple sub keys found. Specify another argument, one of:
-> bar
-> foo
$ kubectl view-secret mysecret foo
bar
$ kubectl view-secret mysecret bar
foo

```

In conclusion, the `kubectl view-secret` a plugin is a useful tool for viewing secrets in Kubernetes clusters. It allows users to view the contents of a secret in plain text.




---


[https://surajincloud.gumroad.com/l/own\-kubectl\-command?layout\=profile](https://surajincloud.gumroad.com/l/own-kubectl-command?layout=profile)
 




---


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


Check out the video format of this blog.


