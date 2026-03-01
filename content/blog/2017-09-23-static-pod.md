+++
date = "2017-09-23T21:21:49+05:30"
title = "Running static pod using standalone kubelet on fedora"
slug = "static-pod"
description = "A practical post about running static pod using standalone kubelet on fedora with lessons, examples, and useful takeaways."
category = "Blog"
author = "Suraj Narwade"

+++

Static Pods are managed by kubelet on specific minion. As they are not associated with any controller, APIServer has no control over it.

One of the use case for static pod might be for storage like gluster on each minion or maybe for
os level debugging on OS like atomic host. 

For testing it, I referred [kelseyhightower's](https://twitter.com/kelseyhightower) standalone kubelet guide.


To install kubelet, we can either download `kubelet` binary and set it up manually or we can get it from `kubernetes-node` package,

```
sudo dnf install -y kubernetes-node
```

Now, we need to add Path to to the directory containing pod manifest files to run, or the path to a single pod manifest file.


Open `/etc/kubernetes/kubelet` and add `--pod-manifest-path=<manifest_path>`, In my case, it's `/home/vagrant`

so it will look like,

```
KUBELET_ARGS="--cgroup-driver=systemd --pod-manifest-path=/home/vagrant/"
```

You can mention `--manifest-url=<URL>` as well, so kubelet will download that manifest file from URL and will apply the changes.

Also, set `--allow-privileged` flag to true in `/etc/kubernetes/config`

```
KUBE_ALLOW_PRIV="--allow-privileged=true"
```

Reload the daemon

```
systemctl daemon-reload
```

Start the service

```
sudo systemctl restart kubelet
```

Enable the service

```
sudo systemctl enable kubelet
```

Check whether kubelet is running or not,

```
sudo systemctl status kubelet
```

### Running a static pod

I am taking example from official documentation,

```
apiVersion: v1 
kind: Pod 
metadata: 
 name: static-web 
 labels:
   role: myrole 
spec: 
 containers: 
 - name: web 
   image: nginx 
 ports: 
  - name: web 
    containerPort: 80 
    protocol: TCP
```

As soon as, we put this manifests in /home/vagrant, kubelet will automatically detect it and will start the containers.

You can check running containers by `docker ps`,

```
$ docker ps
CONTAINER ID        IMAGE                                                                                     COMMAND                  CREATED             STATUS              PORTS               NAMES
bd4e42a51f4c        docker.io/nginx@sha256:aa1c5b5f864508ef5ad472c45c8d3b6ba34e5c0fb34aaea24acf4b0cee33187e   "nginx -g 'daemon ..."   4 minutes ago       Up 4 minutes                            k8s_web_static-web-127.0.0.1_default_2bdef00423eeb60e6edec38d373da61d_0
c0c6c1106515        gcr.io/google_containers/pause-amd64:3.0                                                  "/pause"                 5 minutes ago       Up 5 minutes                            k8s_POD_static-web-127.0.0.1_default_2bdef00423eeb60e6edec38d373da61d_0
```

For more, you can follow kelsey hightower's [tutorial](https://github.com/kelseyhightower/standalone-kubelet-tutorial).

### Reference:

* [Static Pods](https://kubernetes.io/docs/tasks/administer-cluster/static-pod/)
* [Standalone Kubelet Tutorial by kelsey hightower](https://github.com/kelseyhightower/standalone-kubelet-tutorial)
* [issue 10093 on kubernetes](https://github.com/kubernetes/kubernetes/pull/10093)
