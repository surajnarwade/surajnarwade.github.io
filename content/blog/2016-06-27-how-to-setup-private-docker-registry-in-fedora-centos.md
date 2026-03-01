+++
title = "How to Setup Private Docker Registry in Fedora/CentOS ?"
slug = "how-to-setup-private-docker-registry-in-fedora-centos"
date = "2016-06-27"
category = "Open Source"
author = "Suraj Narwade"

+++
When you request container from image,docker usually it looks for container image locally on a system, if its not there, it will download it to your system from somewhere, i.e. [Docker Hub Regitry](https://hub.docker.com/)

**What is Private Docker Registry ?**

*If you want to keep your images private to yourself only, instead of going them over internet, you want to save to time by pushing and pulling them locally, at that point, Private Docker Registry comes into picture.*

<img src="/private.png" width="500" height="300" />

**NOTE :- Docker is not required for Docker registry Server.**

Following example shows how to deploy Docker registry on fedora(22 or later):

**Setting Up a Docker Registry in Fedora**

- Install docker registry package:
  

```
$ dnf install docker-registry
```
- You may need to open TCP port 5000 to allow access to Docker registry service, assuming that you are using firewalld service.
  

```
$ firewall-cmd --permanent --add-port=5000/tcp
$ firewall-cmd --reload
```
- Start and Enable service and begin using it.
  

```
$ systemctl start docker-registry
$ systemctl enable docker-registry
```
- Check status of service by following command, if its active and running, then it means that docker registry is properly up and running.
  

```
$ systemctl status docker-registry
```

<img src="/registry.png" width="600" height="400" />


**Allow access to registry for Docker Client**

- Allow docker client to access registry by making changes in */etc/sysconfig/docker* file as following lines (As We have not used any SSL security certificate here, hence we are adding it to insecure registry field)
  

```
 ADD_REGISTRY='--add-registry localhost:5000'
 INSECURE_REGISTRY='--insecure-registry localhost:5000'
```
- Now, restart docker service:
  

```
$ systemctl restart docker
```
- Tag your private image, so that you can push it to local/private docker registry(For ex.image name is private-image)
  

```
$ docker tag private-image localhost:5000/private-image:latest
```
- Push the private image into local docker registry by typing following commands:
  

```
$ docker push localhost:5000/private-image:latest
```
- To make sure you can properly pull image from private registry, remove current image from system.
  

```
$ docker rmi private-image localhost:5000/private-image:latest
```
- Now, Try to pull image from private registry.
  

```
$ docker pull localhost:5000/private-image:latest
```
- Check Image is downloaded or not by following command & it should show output as below:
  

```
 $ docker images
 REPOSITORY              TAG    IMAGE ID     CREATED     VIRTUAL SIZE
 localhost:5000/private-image latest 91c95931e552 5 weeks ago 910 B
```
Thank you !!!
