+++
title = "How to share process namespace among containers in a Kubernetes Pod"
slug = "share-process-namespace-among-containers-in-a-kubernetes-pod"
author = "Suraj Narwade"
date = "2022-12-17T10:48:31.266Z"
category = "Blog"
+++

A few days ago, I was working on an application which consisted of a multi\-container pod where I had to send a signal from container A to a process from container B within the same Pod.


We know containers inside the pod already share the network namespace by default which means they technically have the same IP address.


Apart from this, most things, such as other namespaces, are isolated. For example, process namespace. Containers within the same pod run their process namespace and don’t share any information with another container.


And I wanted precisely the opposite of that to run my application.


For this use case, I crawled through Kubernetes documentation and came across this field called. `shareProcessName: true`.



> This feature was added to Kubernetes 1\.17


### Let’s try it out


Here’s the example where two containers are in a given pod, but the process namespace is not shared.



```
apiVersion: v1
kind: Pod
metadata:
  name: pod
spec:
  containers:
  - name: container1
    image: envoyproxy/envoy:v1.19-latest
  - name: container2
    image: busybox
    args:
    - /bin/sh
    - -c
    - echo hello;sleep 3600

```

Now, let’s exec into the pod and see what process we see:



```
# container 1
$ kubectl exec -it pod -c container1 -- bash
root@pod:/# ps aux
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
envoy 1 0.5 0.5 2284636 47188 ? Ssl 21:39 0:00 envoy -c /etc/envoy/envoy.yaml
root 82 0.0 0.0 3796 3072 pts/0 Ss 21:42 0:00 bash
root 92 0.0 0.0 5296 2404 pts/0 R+ 21:42 0:00 ps aux
# container 2
$ kubectl exec -it pod -c container2 -- sh
/ # ps aux
PID USER TIME COMMAND
1 root 0:00 sleep 3600
27 root 0:00 sh
33 root 0:00 ps aux

```

Process namespace is entirely isolated.


Let’s share the process namespace in the pod.



```
apiVersion: v1
kind: Pod
metadata:
  name: pod
spec:
  shareProcessNamespace: true
  containers:
  - name: container1
    image: envoyproxy/envoy:v1.19-latest
  - name: container2
    image: busybox
    args:
    - /bin/sh
    - -c
    - echo hello;sleep 3600

```

Now, if we exec into the second container of our pod, we will now see processes from container 1 as well.



```
$ kubectl exec -it pod -c container2 -- sh
/ # ps aux
PID USER TIME COMMAND
1 65535 0:00 /pause
7 101 0:00 envoy -c /etc/envoy/envoy.yaml
29 root 0:00 sleep 3600
35 root 0:00 sh
41 root 0:00 ps aux

```

Have you noticed that container processes are no longer PID 1, though


### Remember this


* **Once you share the process namespace, Container’s process will not run as PID 1**.


This may not work for some applications that need to run with PID 1, such as systemd.


* **Processes are visible among all the containers in a given pod**.


This means all the information from /proc is visible, which may contain sensitive information.


Let’s explore `/proc`.



```
# ls /proc/7
attr coredump_filter gid_map mountinfo oom_score_adj sessionid status wchan
autogroup cpuset io mounts pagemap setgroups syscall
auxv cwd limits mountstats personality smaps task
cgroup environ loginuid net projid_map smaps_rollup timens_offsets
clear_refs exe map_files ns root stack timers
cmdline fd maps oom_adj sched stat timerslack_ns
comm fdinfo mem oom_score schedstat statm uid_map

```

One thing to note here is that even if `/proc` is shared; content is protected by filesystem permissions. As you can see, if we try to access content for process 7, which is our envoy process, we are getting access denied, but if there are no proper filesystem permissions in place, you’ll be able to see the content for the process as well.



```
$ ls /proc/7/root
ls: /proc/7/root: Permission denied

```

### Where can I use it?


* Send signals to the process to restart or kill the process
* Separate container with a set of tools either for troubleshooting or as a sidecar


### References


* [https://kubernetes.io/docs/tasks/configure\-pod\-container/share\-process\-namespace/](https://kubernetes.io/docs/tasks/configure-pod-container/share-process-namespace/)


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


