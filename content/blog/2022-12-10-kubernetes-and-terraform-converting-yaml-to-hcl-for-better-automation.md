+++
title = "Kubernetes and Terraform: Converting YAML to HCL for Better Automation"
slug = "kubernetes-and-terraform-converting-yaml-to-hcl-for-better-automation"
description = "A practical post about kubernetes and terraform: converting yaml to hcl for better automation with lessons, examples, and useful takeaways."
author = "Suraj Narwade"
date = "2022-12-10T22:54:09.874Z"
category = "Blog"
+++

Kubernetes and Terraform are both powerful tools for managing cloud infrastructure, but they use different configuration languages. Kubernetes uses YAML, while Terraform uses HashiCorp Configuration Language (HCL). This can make it difficult to use the two tools together, as you may need to convert YAML files to HCL in order to use them with Terraform.


Sometimes when you are spinning up managed Kubernetes such as GKE, EKS and AKS, you may need to deploy Kubernetes resources via terraform.


Now, if you already have YAML but you don’t have time to rewrite terraform for it, what should you do?


Fortunately, there’s an amazing open\-source tool available to help you convert Kubernetes YAML to Terraform HCL. In this blog post, we’ll walk you through the steps to do this so that you can take advantage of the best features of this tool.


[**GitHub \- sl1pm4t/k2tf: Kubernetes YAML to Terraform HCL converter**  
\*A tool for converting Kubernetes API Objects (in YAML format) into HashiCorp's Terraform configuration language. The…\*github.com](https://github.com/sl1pm4t/k2tf)


#### **Installation**


* on Mac



```
brew install k2tf

```

* on Linux \& Windows  
 \- Grab the latest release binaries from the Github [release page](https://github.com/sl1pm4t/k2tf/releases/)


Let’s take a pod definition



```
---
apiVersion: v1
kind: Pod
metadata:
 name: nginx
spec:
 containers:
 - name: nginx
   image: nginx:1.14.2
   ports:
   - containerPort: 80

```

Let’s convert it to HCL



```
$ k2tf -f pod.yaml
resource "kubernetes_pod" "nginx" {
  metadata {
    name = "nginx"
  }

  spec {
    container {
      name = "nginx"
      image = "nginx:1.14.2"
      port {
      container_port = 80
      }
    }
  }
}

```

you can save the terraform HCL output into the file using the following command,



```
k2tf -f pod.yaml -o pod.tf

```

you can pass a file consisting of multiple resources as input to `k2tf` tool.


for example,



```
$ cat manifest.yaml
---
apiVersion: v1
kind: Pod
metadata:
    name: nginx
spec:
    containers:
    - name: nginx
      image: nginx:1.14.2
      ports:
      - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9376

```

we can run the following command to convert the above file:



```
k2tf -f manifest.yaml -o manifests.tf

```

one of the interesting features in `k2tf` is, you can fetch the resource definition from the cluster and then convert it to HCL



```
kubectl get deployments -o yaml | k2tf -o resources.tf

```


> default value for `-f` flag of k2tf is `—` which means read from the STDIN


If you find the tool useful, make sure you ⭐️ the repo and show your love for the project :)


I’ve also made a video for the same:



I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


