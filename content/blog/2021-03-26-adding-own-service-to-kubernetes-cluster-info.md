+++
title = "Adding own service to Kubernetes Cluster Info"
slug = "adding-own-service-to-kubernetes-cluster-info"
description = "A practical post about adding own service to kubernetes cluster info with lessons, examples, and useful takeaways."
author = "Suraj Narwade"
date = "2021-03-26T17:39:01.865Z"
category = "Blog"
+++

Last week, while going through some of the Kubernetes manifest files, I stumbled upon this label:



```
kubernetes.io/cluster-service: “true”

```

I searched about this in Documentation but could not find much information, so I thought of writing a small blog post about it.


[https://surajincloud.gumroad.com/l/own\-kubectl\-command](https://surajincloud.gumroad.com/l/own-kubectl-command)
 


When you add this label to any resource of type `Kind: Service` , it means it is part of cluster service, and the user needs to know when they are looking for cluster information. Hence it appears in the following command:



```
kubectl cluster-info

```

By default, this command shows:



```
$ kubectl cluster-info
Kubernetes master is running at https://kubernetes.docker.internal:6443
KubeDNS is running at https://kubernetes.docker.internal:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxyTo further debug and diagnose cluster problems, use ‘kubectl cluster-info dump’.

```

Now, we will deploy a sample Nginx deployment and will try to add this to cluster info:



```
kubectl run nginx --image=nginx -n kube-system
kubectl expose deployment nginx --port 80 -n kube-system \
--labels kubernetes.io/cluster-service=true

```

Now you can see nginx service in `kubectl cluster-info` output,



```
$ kubectl cluster-info
Kubernetes master is running at https://kubernetes.docker.internal:6443
KubeDNS is running at https://kubernetes.docker.internal:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
nginx is running at https://kubernetes.docker.internal:6443/api/v1/namespaces/kube-system/services/nginx/proxy

```


> ***Note: This will work only for services in*** `***kube-system***` ***namespace.***


You can check out the video tutorial for the same here:



I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


