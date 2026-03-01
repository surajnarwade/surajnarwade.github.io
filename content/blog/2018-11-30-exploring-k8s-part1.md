+++
date = "2018-11-30T21:21:49+05:30"
title = "Exploring Kubernetes: Client-Go - part-1"
slug = "exploring-k8s-part1"
category = "Blog"
author = "Suraj Narwade"

+++

<br/>
Nowadays, I am learning advance `golang`, along with that knowledge, I am trying to understand kubernetes in more depth. I found `client-go` as goto thing to explore & learn things. I am also putting respective client-go code links wherever needed.

What's client-go ?
------------------
<br/>
Simple, golang client library for talking to kubernetes cluster

Where's client-go ?
-------------------
<br/>

* you can find it in `kubernetes/kubernetes/staging` directory and published by bot to `k8s.io/client-go`, kubernetes also uses client-go. it's interesting if you see `client-go` from kubernetes vendor, it  has symlinks to `kubernetes/kubernetes/staging`

<br/>
<br/>

**In this Adventure, very first step is to see how to connect to API Server :)**
--------------------------------------------------------------------------------

<br/>

* *Connecting to API Server*
<br/>
	* There are two ways to talk to cluster using any go program:
		* **outside cluster**: if your program is standalone and if you have either kubeconfig file or master URL.

		* **inside cluster**: if your program is supposed to run on kubernetes (for example, controller)

<br/>

* When your program runs on kubernetes, it uses service account token inside the pods `/var/run/secrets/kubernetes.io/serviceaccount` for auth and tls stuff.


Connecting to cluster using client-go
-------------------------------------

<br/>

* clientcmd holds the required code to configuring connection to the cluster using kubeconfig as well as about modification to kubeconfig. Code reference [here](https://github.com/kubernetes/client-go/blob/master/tools/clientcmd/client_config.go#L546).

```
import "k8s.io/client-go/tools/clientcmd"
```

* if you have master URL and kubeconfig both:

```
config, err := clientcmd.BuildConfigFromFlags(masterURL, kubeconfig)
```

* if you have only have kubeconfig file:

```
config, err := clientcmd.BuildConfigFromFlags("", kubeconfig)
```

* If you are running program on kubernetes, you don't need to provided parameters, it will automatically fetch details using serviceaccount tokens:

```
config, err := clientcmd.BuildConfigFromFlags("", "")
```

* If you don't provide any parameters, it fallbacks to `InClusterConfig`, even if it fails, it fallbacks to default configs which is either `KUBERNETES_MASTER` environment variable or simply `http://localhost:8080`, code reference [here.](https://github.com/kubernetes/client-go/blob/master/tools/clientcmd/client_config.go#L48)

* Directly use this (even above way internally uses this), Code Ref [here](https://github.com/kubernetes/client-go/blob/master/rest/config.go#L315).

```
import "k8s.io/client-go/rest"
...
config,err := rest.InClusterConfig()
```

<br/>

This method basically gets `KUBERNETES_SERVICE_HOST` and `KUBERNETES_SERVICE_PORT` environment variables (which are injected into every pod) and service token to generate the config.

<br/>


* `config` has all necessary information to talk to the cluster from anywhere :) If you check struct defination [here](https://github.com/kubernetes/client-go/blob/master/rest/config.go#L51)

it has fields like `Host, APIPath`, `Username, Password, BearerToken`, etc which are needed to talk to cluster

* If you explore more, you will see `clientcmd/api` has `Config` (`Kind: Config`) struct which is API resource for kubeconfig, code is [here](https://github.com/kubernetes/client-go/blob/master/tools/clientcmd/api/types.go#L29)

*TGIF, Enough for Today :)  Happy Weekend :) Stay Tuned :)*
-----------------------------------------------------------


