+++
date = "2017-12-09T21:21:49+05:30"
title = "Mounting host folders in minikube"
slug = "hostmount-minikube"
category = "Blog"
author = "Suraj Narwade"

+++


supports PV of type `hostpath`.

minikube is configured to persist files stored under following directories.

* /data
* /var/lib/localkube
* /var/lib/docker
* /tmp/hostpath_pv
* /tmp/hostpath-provisioner

any other directory will not persist the data after reboot.

To mount host directory inside minikube,

```
$ minikube mount /host-mount-path:/vm-mount-path
```

for example,

```
$ minikube mount ~/mount-dir:/mountexample
Mounting /home/user/mount-dir/ into /mountexample on the minikubeVM
This daemon process needs to stay alive for the mount to still be accessible...
ufs starting
```

This process has to stay open, so open another terminal (if you want more than one mountpath, open one more terminal follow the above procedure)


Mounting inside container,

```
---
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    args:
    - bash
    stdin: true
    stdinOnce: true
    tty: true
    workingDir: "/mountexample"
    volumeMounts:
    - mountPath: "/mountexample"
      name: host-mount
  volumes:
  - name: host-mount
    hostPath:
      path: "/mountexample"
```

Another way is to create PV using following YAML and then you can map your host directory path to this directory path,

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0001
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 5Gi
  hostPath:
    path: /data/pv0001/
```

Run `kubectl create -f <above yaml file>` to create PV.

As of now, only `hostpath` and accessmode (read write once) are supported.

Reference:
----------

* https://kubernetes.io/docs/getting-started-guides/minikube/

