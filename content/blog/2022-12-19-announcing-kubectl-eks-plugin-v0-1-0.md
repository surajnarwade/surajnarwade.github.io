+++
title = "Announcing Kubectl EKS Plugin v0.1.0"
slug = "announcing-kubectl-eks-plugin-v0-1-0"
description = "An announcement of Kubectl EKS Plugin v0.1.0 with the key details and what to expect."
author = "Suraj Narwade"
date = "2022-12-19T07:07:20.566Z"
category = "Blog"
+++

Hello everyone, It’s my pleasure to announce the v0\.1\.0 release of the [Kubectl EKS Plugin](https://github.com/surajincloud/kubectl-eks). I had this of the kubectl plugin for EKS from the day I started using Amazon EKS. But I finally made it into reality and cut the first release.


The mission of the kubectl EKS plugin is to simplify operations and provide easy access to cluster\-related information.


The first release of [kubectl\-eks](https://github.com/surajincloud/kubectl-eks) has few but convenient functionalities. I am excited about this project and hope to see more features in the upcoming months.


### Installing the plugin


* Go to the release page [here](https://github.com/surajincloud/kubectl-eks/releases) and download the respective binary. For example, On Linux,



```
$ wget https://github.com/surajincloud/kubectl-eks/releases/download/v0.1.0/kubectl-eks_0.1.0_linux_amd64.tar.gz
$ tar -xvf kubectl-eks_0.1.0_linux_amd64.tar.gz
$ mv kubectl-eks ~/.local/bin # you can move to any $PATH of your choice

```

* Build it from the source



```
$ git clone https://github.com/surajincloud/kubectl-eks
$ cd kubectl-eks
$ make
$ mv kubectl-eks ~/.local/bin # you can move to any $PATH of your choice

```



---


[https://surajincloud.gumroad.com/l/own\-kubectl\-command](https://surajincloud.gumroad.com/l/own-kubectl-command)
 




---


### Let’s Explore


* List EKS nodes but differently, and it shows more information.



```
$ kubectl eks nodes
NAME ARCH INSTANCE-TYPE OS CAPACITY-TYPE REGION AGE
ip-1-5-1-4.eu-west-1.compute.internal amd64 r5n.2xlarge linux SPOT eu-west-1a 14h
ip-1-5-1-9.eu-west-1.compute.internal amd64 r5.2xlarge linux ON_DEMAND eu-west-1a 2d5h

```

* List all IRSA. It shows the list of all serviceaccount with their IAM Role and Token expiration time.



```
$ kubectl eks irsa -n cloud
NAMESPACE SERVICEACCOUNT IAM-ROLE TOKEN-EXPIRATION
cloud test-1 arn:aws:iam::111111111111:role/test-1 86400
cloud test-2 arn:aws:iam::222222222222:role/test-2 86400

```

**Note**: If you don’t pass the flag, it will list from all namespaces.


* This feature is most exciting for me as I no longer need to install the AWS SSM plugin or search for instance id. I can use the following command for SSM access to EKS Node.



```
$ kubectl eks ssm ip-1-5-1-4.eu-west-1.compute.internal
SSM into node i-022357dcxxxxx
sh-4.2$

```

We will see more exciting stuff in this kubectl eks plugin in upcoming releases.


Check out the GitHub repo [here](https://github.com/surajincloud/kubectl-eks).


Please give it a go, and I hope you find it helpful. If you like the project, consider Starring it.


If you have any feature requests, Feel free to fill out a GitHub issue and contribute.


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


