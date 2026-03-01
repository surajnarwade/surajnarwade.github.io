+++
date = "2018-11-30T21:21:49+05:30"
title = "Exploring Kubernetes: Client-Go - part-2"
slug = "exploring-k8s-part2"
description = "Explore kubernetes: client-go - part-2 and learn the core ideas step by step."
category = "Blog"
author = "Suraj Narwade"

+++


If you have kubeconfig file already in place,

```
kubeconfig := clientcmd.NewNonInteractiveDeferredLoadingClientConfig(clientcmd.NewDefaultClientConfigLoadingRules(), &clientcmd.ConfigOverrides{})
```

There is one more function `clientcmd.NewInteractiveDeferredLoadingClientConfig` but as per code, we can use it only when password is allowed


* Namespace given by current context,

```
namespace, _, err := kubeconfig.Namespace()
```

* REST config needed to operations

```
restconfig, err := kubeconfig.ClientConfig()
```

* if you want kubeconfig as a struct


```
clientcmdapi.Config, err := kubeconfig.RawConfig()
```

Here, `kubeconfig` is interface of following type:

```
// ClientConfig is used to make it easy to get an api server client
type ClientConfig interface {
	// RawConfig returns the merged result of all overrides
	RawConfig() (clientcmdapi.Config, error)
	// ClientConfig returns a complete client config
	ClientConfig() (*restclient.Config, error)
	// Namespace returns the namespace resulting from the merged
	// result of all overrides and a boolean indicating if it was
	// overridden
	Namespace() (string, bool, error)
	// ConfigAccess returns the rules for loading/persisting the config.
	ConfigAccess() ConfigAccess
}
```

structs `NewNonInteractiveDeferredLoadingClientConfig` & `inClusterClientConfig` implements above interface and can be used as per requirements whether inside of cluster or outside.
