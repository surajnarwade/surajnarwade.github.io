+++
title = "Installing Kubernetes Cluster with 3 minions on Fedora 23"
slug = "installing-kubernetes-cluster-with-3-minions-on-fedora-23"

description = "Follow a step-by-step guide to install kubernetes cluster with 3 minions on fedora 23 successfully."
date = "2016-05-27" 
category = "What I learn ?"
author = "Suraj Narwade"

+++

**Kubernetes**

Kubernetes is a system, developed by google, for managing containerized applications in a clustered environment. It provides basic mechanisms for deployment, maintenance and scaling of applications on public, private or hybrid setups. It also comes with self-healing features where containers can be auto provisioned, restarted or even replicated.
It aims to provide a "platform for automating deployment, scaling, and operations of application containers across clusters of hosts"

<img src="/kube7-logo.png" width="600" height="250" />

.

In this blog post, we’ll install a Kubernetes cluster with three minions on Fedora 23, we will also see example on how to manage pods.

**Prerequisites**

```bash
$ systemctl stop firewalld
$ systemctl disable firewalld
```
**Setting up the Kubernetes Master**

1.Install etcd and kubernetes using dnf

```bash
$ dnf -y install etcd kubernetes
```
2.Configure etcd to listen all IP addresses, Following lines are to be uncommented and assigned with respective values in  /etc/etcd/etcd.conf:

```ini
 ETCD_NAME=default
 ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
 ETCD_LISTEN_CLIENT_URLS="http://0.0.0.0:2379"
 ETCD_ADVERTISE_CLIENT_URLS="http://localhost:2379
```
3.Configure Kubernetes API server by editing /etc/kubernetes/apiserver as below:

```ini
 KUBE_API_ADDRESS="--address=0.0.0.0"
 KUBE_API_PORT="--port=8080"
 KUBELET_PORT="--kubelet_port=10250"
 KUBE_ETCD_SERVERS="--etcd_servers=http://127.0.0.1:2379"
 KUBE_SERVICE_ADDRESSES="--service-cluster-ip-range=10.254.0.0/16"
 KUBE_ADMISSION_CONTROL="--admission_control=NamespaceLifecycle,NamespaceExists,LimitRanger,SecurityContextDeny,ResourceQuota"
 KUBE_API_ARGS=""
```
4.We have to Start and enable these servies; etcd, kube-apiserver, kube-controller-manager and kube-scheduler:

```bash
   $ for SERVICES in etcd kube-apiserver kube-controller-manager kube-scheduler; do
    systemctl restart $SERVICES
    systemctl enable $SERVICES
    systemctl status $SERVICES
    done
```
5.We have to Define flannel network configuration in etcd. This configuration will be pulled by flannel service on minions, it is used for internetwork communication in containers:

```bash
$ etcdctl mk /atomic.io/network/config '{"Network":"172.17.0.0/16"}'
```
.

**Setting up Kubernetes Minions (Nodes)**

<img src="/minion.jpg" width="600" height="400" />

Now follow following steps to configure minion1, minion2 and minion3 .

1. Install flannel and Kubernetes using dnf:

```bash
$ dnf -y install flannel kubernetes
```
1. Update the following line in /etc/sysconfig/flanneld to connect to the respective master(via flannel service):

```ini
FLANNEL_ETCD="http://10.3.3.171:2379"
```
2. Update KUBE_MASTER in /etc/kubernetes/config to Kubernetes master API server for connection:

```ini
KUBE_MASTER="--master=http://10.3.13.171:8080"
```

4.Configure kubelet service by editing  /etc/kubernetes/kubelet on each minion as following:

*Minion1:*

```ini
 KUBELET_ADDRESS="--address=0.0.0.0"
 KUBELET_PORT="--port=10250"
 # change the hostname to this host’s IP address
 KUBELET_HOSTNAME="--hostname_override=10.3.3.172"
 KUBELET_API_SERVER="--api_servers=http://10.3.3.171:8080"
 KUBELET_ARGS=""
```
*Minion2:*

```ini
 KUBELET_ADDRESS="--address=0.0.0.0"
 KUBELET_PORT="--port=10250"
 # change the hostname to this host’s IP address
 KUBELET_HOSTNAME="--hostname_override=10.3.3.173"
 KUBELET_API_SERVER="--api_servers=http://10.3.3.171:8080"
 KUBELET_ARGS=""
```
*Minion3:*

```ini
 KUBELET_ADDRESS="--address=0.0.0.0"
 KUBELET_PORT="--port=10250"
 # change the hostname to this host’s IP address
 KUBELET_HOSTNAME="--hostname_override=10.3.3.174"
 KUBELET_API_SERVER="--api_servers=http://10.3.3.171:8080"
 KUBELET_ARGS=""
```
1. We have to Start and enable kube-proxy, kubelet, docker and flanneld services on each minion:

```bash
$ for SERVICES in kube-proxy kubelet docker flanneld; do
    systemctl restart $SERVICES
    systemctl enable $SERVICES
    systemctl status $SERVICES
done
```
2. On each minion, you can check that you will have two new interfaces added, docker0 and flannel0. You can check different range of IP addresses on flannel0 interface on each minion, you can check it by following command on each minion:

```bash
$ ip a | grep flannel | grep inet
```
if all the services are started correctly, Hence, Everything is set now, we can check minion status as following:

```text
 $ kubectl get nodes
 NAME          STATUS    AGE
 10.3.3.172   Ready     10m
 10.3.3.173   Ready     12m
 10.3.3.174   Ready     15m
```
We will create a simple pod definition like this below(pod is nothing but group of containers)

```yaml
 #pod-nginx.yaml
 apiVersion: v1
 kind: Pod
   metadata : 
  name: nginx
   spec : 
    containers:
    - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```
Create a pod containing an nginx server (pod-nginx.yaml):

```bash
$ kubectl create -f ./pod-nginx.yaml
```
We can check output at:

```bash
 $ curl http://$(kubectl get pod nginx -o go-template={{.status.podIP}})
```
To List all pods:

```text
 $ kubectl get pods
 NAME          READY     STATUS    RESTARTS   AGE
 nginx         1/1       Running   0          2m
```
To replicate the container, for example:

```yaml
 apiVersion: v1
 kind: ReplicationController
 metadata : 
  name: nginx
  spec : 
    replicas: 3
    selector:
    app: nginx
    template:
    metadata:
    labels:
    app: nginx
    spec:
    containers:
    - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```
Then pods will be listed as,(containers will be spinned on all minions in cluster way)

```text
 $ sudo kubectl get pods
 NAME          READY     STATUS    RESTARTS   AGE
 nginx-1z3lw   1/1       Running   0          3m
 nginx-343jk   1/1       Running   0          3m
 nginx-s9gt8   1/1       Running   0          3m
```
We can Delete the pod by name as following:

```bash
 $ kubectl delete pod nginx
```
We can check all logs(events) as following :

```bash
 $ kubectl get events
```
Thank You !!!
For any query or any suggestion, Comments are most welcome !!!
