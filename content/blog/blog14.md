+++
title = "Jenkins Docker Integration along with Fedora Contribution"
date = "2016-07-11"
category = "Open Source"
author = "Suraj Narwade"

+++

Hello Everyone,
From Past few days, I was studying  Jenkins,
1. have read VMs or cloud instances can be provisioned as the slave for Jenkins, I thought it would be awesome to have more generic setup.
   So I dig more into the web and found Jenkins Docker Plugin. so that we can provision Jenkins slave on Docker host. and this Docker host can be anywhere on any machine or any cloud instance.I have experimented it and I managed to contributr Jenkins fedora slave image to Fedora Cloud, https://github.com/fedora-cloud/Fedora-Dockerfiles/tree/master/jenkins-slave
   

Few Reasons for Container based slaves:

- Each image provides a clean environment in which a build can be run.
- They are relatively lightweight (compared to a full VM
- They can be saved/tagged to provide a record of the resulting build output
  

What Jenkins docker plugin does ?
it will able to use docker host so that it will dynamically
provision slave
run a single build
tear down the slave

<img src="/docker-jenkins.png" width="600" height="400" />
.

*Setting up the Docker host:*

- Make sure that Firewall is disabled
  

```
 sudo systemctl stop firewalld
 sudo systemctl disable firewalld
```
- Install docker engine
  

```
 sudo dnf update -y
 sudo dnf install docker -y
```
- Replace following lines with "ExecStart" under [Service] section in */usr/lib/systemd/system/docker.service*
  

```
 ExecStart=
 ExecStart=/usr/bin/docker daemon -H fd:// -H tcp://0.0.0.0:2376
```
- Start Docker daemon
  

<dl>
  <dt> :  : </dt>
  <dd>
```
    sudo systemctl daemon-reload
    sudo systemctl start docker
    sudo systemctl enable docker
```
  </dd>
</dl>
Creating Docker image:

- Recently, I have submitted docker image in dockerhub, so you can pull that image using following command,
  

```
 docker pull surajnarwade/jenkins-fedora-slave
```
- **Same image(Dockerfile) I requested to merge in Fedora-cloud, and the community happily accepted it, Hence my first Fedora Contribution :)**
  

- [Click Here](https://github.com/fedora-cloud/Fedora-Dockerfiles/tree/master/jenkins-slave) to access my contributed Dockerfile on fedora cloud.
- Download [Jenkins Docker plugin](https://wiki.jenkins-ci.org/display/JENKINS/Docker+Plugin) in Jenkins, goto "Manage Jenkins"->"Configure System", Scroll Down to bottom of the page, you should find an option to "Add a new cloud", Click on it and select Docker option.
- Docker URL should point to IP address of Docker host.
- Put name of Docker image in ID field.
- Put 'label' Labels field, so that Jenkins projects identify this as docker cloud provider.
- If everything sets up correctly, the "Test Connection" button should return the current version of Docker running on the host.
  

<img src="/screenshot.png" width="600" height="400" />
.

- Put label of Docker Cloud in Label Expression field in Project configurations, so now Jenkins should start up a new Docker container, run the build, and then shut down the container.
  

*Conclusion*

The integration between Jenkins and Docker is still at development stage, so it may take some time and efforts to get them working nicely together. Capability to start new build from clean image is very important thing when it comes to point of reproducible builds.

