+++
title = "Run Commands for All the clusters in your Kubeconfig in parallel"
author = "Suraj Narwade"
date = "2022-12-13T07:34:04.317Z"
category = "Blog"
+++

Have you ever wondered about running `kubectl` commands in parallel for some of your clusters? You must have written custom shell scripts or Golang scripts to do so.


Fortunately, a tool to run commands in parallel now exists, Thanks to [Ahmet Alp Balkan](https://github.com/ahmetb) 🙏


Check out the GitHub repo, and don’t forget to ⭐️ the repo :)


[https://github.com/ahmetb/kubectl\-foreach](https://github.com/ahmetb/kubectl-foreach)
 


`kubectl-foreach` Is a tool that allows users to run a `kubectl` command in one or more contexts (clusters) in parallel. This can be very useful when you need to perform an action on all of the resources in given contexts/clusters, such as gathering information, scaling them up or down, rolling out an update, or applying a configuration change.


#### Installing `kubectl-foreach`


* Download the binary from the [release pages of the project](https://github.com/ahmetb/kubectl-foreach/releases) based on your Operating system and architecture.
* Install using the Krew kubectl plugin manager



```
kubectl krew install foreach

```

* Build from scratch



```
go install github.com/ahmetb/kubectl-foreach@latest

```

### Let's try it out


* Check versions on all the clusters at once



```
  kubectl foreach -- version

```



> Basically, whatever you write `--` will be your ARGS for `kubectl` commands


It will now show you the following prompt,



```
Will run command in context(s):
  - civo-1
  - civo-2
  - docker-desktop
Continue? [Y/n]:

```

Once you type `Y`you will see the equivalent kubectl version output for all the clusters,



```
Continue? [Y/n]: Y
docker-desktop | WARNING: This version information is deprecated and will be replaced with the output from kubectl version --short. Use --output=yaml|json to get the full version.
docker-desktop | Client Version: version.Info{Major:"1", Minor:"25", GitVersion:"v1.25.2", GitCommit:"5835544ca568b757a8ecae5c153f317e5736700e", GitTreeState:"clean", BuildDate:"2022-09-21T14:33:49Z", GoVersion:"go1.19.1", Compiler:"gc", Platform:"darwin/arm64"}
docker-desktop | Kustomize Version: v4.5.7
docker-desktop | Server Version: version.Info{Major:"1", Minor:"24", GitVersion:"v1.24.0", GitCommit:"4ce5a8954017644c5420bae81d72b09b735c21f0", GitTreeState:"clean", BuildDate:"2022-05-03T13:38:19Z", GoVersion:"go1.18.1", Compiler:"gc", Platform:"linux/arm64"}
civo-2 | Client Version: version.Info{Major:"1", Minor:"25", GitVersion:"v1.25.2", GitCommit:"5835544ca568b757a8ecae5c153f317e5736700e", GitTreeState:"clean", BuildDate:"2022-09-21T14:33:49Z", GoVersion:"go1.19.1", Compiler:"gc", Platform:"darwin/arm64"}
civo-2 | Kustomize Version: v4.5.7
civo-2 | Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.6+k3s1", GitCommit:"418c3fa858b69b12b9cefbcff0526f666a6236b9", GitTreeState:"clean", BuildDate:"2022-04-28T22:16:18Z", GoVersion:"go1.17.5", Compiler:"gc", Platform:"linux/amd64"}
civo-2 | WARNING: This version information is deprecated and will be replaced with the output from kubectl version --short. Use --output=yaml|json to get the full version.
civo-1 | Client Version: version.Info{Major:"1", Minor:"25", GitVersion:"v1.25.2", GitCommit:"5835544ca568b757a8ecae5c153f317e5736700e", GitTreeState:"clean", BuildDate:"2022-09-21T14:33:49Z", GoVersion:"go1.19.1", Compiler:"gc", Platform:"darwin/arm64"}
civo-1 | Kustomize Version: v4.5.7
civo-1 | Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.6+k3s1", GitCommit:"418c3fa858b69b12b9cefbcff0526f666a6236b9", GitTreeState:"clean", BuildDate:"2022-04-28T22:16:18Z", GoVersion: civo-2 | WARNING: version difference between client (1.25) and server (1.23) exceeds the supported minor version skew of +/-1
"go1.17.5", Compiler:"gc", Platform:"linux/amd64"}
civo-1 | WARNING: This version information is deprecated and will be replaced with the output from kubectl version --short. Use --output=yaml|json to get the full version.
civo-1 | WARNING: version difference between client (1.25) and server (1.23) exceeds the supported minor version skew of +/-1

```


> Note: you can disable the prompt by passing the **\-q** flag, you can also set the environment variable KUBECTL\_FOREACH\_DISABLE\_PROMPTS\=true to true so that the command won't prompt again


* the previous command showed output from `docker-desktop` ,`civo-1` , `civo-2` What if I only want to see the outputs of civo clusters?


For that, you can supply a regex of your choice, for example,



```
$ kubectl foreach /civo/ -- get ns
Will run command in context(s):
  - civo-1
  - civo-2
civo-1 | NAME STATUS AGE
civo-1 | default Active 44m
civo-1 | kube-system Active 44m
civo-1 | kube-public Active 44m
civo-1 | kube-node-lease Active 44m
civo-2 | NAME STATUS AGE
civo-2 | kube-system Active 34m
civo-2 | default Active 34m
civo-2 | kube-public Active 34m
civo-2 | kube-node-lease Active 34m

```

I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


