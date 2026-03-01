+++
date = "2018-11-30T21:21:49+05:30"
title = "Exploring Kubernetes: Client-Go - part-3"
slug = "exploring-k8s-part3"
description = "Explore kubernetes: client-go - part-3 and learn the core ideas step by step."
category = "Blog"
author = "Suraj Narwade"

+++


we got `restConfig` and `namespace`, connection is set up Yay

From this restconfig, Now we need to create respective clients for further operations

we will need following package:

```
import k8s.io/client-go/kubernetes
```

* create basic kubernetes client:

```
kubeClient, err := kubernetes.NewForConfig(restConfig)
```

it returns clients for APIgroups like:
* `Apps`
* `CoreV1`
* `Batch`
* `StorageV1`

If you have some custom controller, generated code will have `NewForConfig` function which you have call separately,

For example, In case of service catalog,

```
serviceCatalogClient, err := servicecatalogclienset.NewForConfig(restConfig)
```

Let's take an example for pod:

kubeClient.CoreV1().Pods(namespace).List


* kubeClient is struct consist of all other apigroup clients with all structs unexported
* so we will call method `CoreV1()` which returns interface which has struct `c.coreV1` and interface