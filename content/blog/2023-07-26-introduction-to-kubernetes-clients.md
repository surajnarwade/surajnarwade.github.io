+++
title = "Introduction to Kubernetes Clients"
slug = "introduction-to-kubernetes-clients"
author = "Suraj Narwade"
date = "2023-07-26T09:57:14.643Z"
category = "Blog"
+++

In this post, we will see different Kubernetes clients, the client is something that you can use to talk to the Kubernetes cluster.


Mainly, there are 3 ways to talk to a cluster:


* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [Console/Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)
* programmatically using clients


The Kubernetes community maintains clients in various languages in this [repo](https://github.com/kubernetes-client).


Since Kubernetes is in Golang, a client library known as [client\-go](https://github.com/kubernetes/client-go) is widely used. But there are clients available in other interesting languages :)


* [Python](https://github.com/kubernetes-client/python)
* [Ruby](https://github.com/kubernetes-client/ruby)
* [C Sharp](https://github.com/kubernetes-client/csharp)
* [Perl](https://github.com/kubernetes-client/perl)
* [Haskell](https://github.com/kubernetes-client/haskell)
* [Javascript](https://github.com/kubernetes-client/javascript)
* [Java](https://github.com/kubernetes-client/java)


for Java, there's one more client known as [fabric8 kubernetes client](https://github.com/fabric8io/kubernetes-client) which is also widely used and famous too :) (it works with OpenShift too)


In the upcoming blog post, we will see how we can leverage `client-go` effectively to talk to the cluster and create some tools using it.


