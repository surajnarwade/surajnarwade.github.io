+++
date = "2017-03-21T21:21:49+05:30"
title = "What's new in Docker ?"
slug = "whats-new-in-docker"
description = "A quick look at what is new in docker  and why it matters."
category = "Blog"
author = "Suraj Narwade"

+++

* Recently, I installed latest docker on my machine. But I noticed lots of new features, commands and aliases to the existing commands and felt it was worth posting.

* Docker version that I installed is 17.03.0-ce, [Click here](https://docs.docker.com/engine/installation/linux/fedora/) to know how to install Docker

<img src="/docker.png" width="400" height="300" align="middle"/>

* As we can see in docker help, commands are grouped as management commands under names like container, network, image, etc as per their purpose. 

```
$ docker --help

Usage:	docker COMMAND

A self-sufficient runtime for containers
...
...
Management Commands:
  container   Manage containers
  image       Manage images
  network     Manage networks
  node        Manage Swarm nodes
  plugin      Manage plugins
  secret      Manage Docker secrets
  service     Manage services
  stack       Manage Docker stacks
  swarm       Manage Swarm
  system      Manage Docker
  volume      Manage volumes

Commands:
 ...
 ...
Run 'docker COMMAND --help' for more information on a command.
```

* For example, Under `docker container`, all commands related to managing to containers are classified.

```
$ docker container --help
Usage:	docker container COMMAND

Manage containers

Options:
      --help   Print usage

Commands:
  attach      Attach to a running container
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  ...
  ...
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker container COMMAND --help' for more information on a command.
```
* There is new feature here, which makes plugin management in docker easier, now we can easily install or delete or get information about plugins.

 ```
 $ docker plugin --help
 Usage:	docker plugin COMMAND
 
 Manage plugins
 
 Options:
       --help   Print usage
 
 Commands:
   create      Create a plugin from a rootfs and configuration. Plugin data directory must contain config.json and rootfs directory.
   disable     Disable a plugin
   enable      Enable a plugin
   inspect     Display detailed information on one or more plugins
   install     Install a plugin
   ls          List plugins
   push        Push a plugin to a registry
   rm          Remove one or more plugins
   set         Change settings for a plugin
   upgrade     Upgrade an existing plugin

 ```
 
New Aliases:
============

* `docker container ls` is same as `docker ps`

```
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                          PORTS               NAMES
66d17f3bc07e        hello-world         "/hello"            About a minute ago   Exited (0) About a minute ago                       ecstatic_lamarr 
```

* `docker image ls` is same as  `docker images`

```
# docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
fedora              latest              0047cca29c6f        5 days ago          230 MB
hello-world         latest              48b5124b2768        2 months ago        1.84 kB
```

New `docker system` command added:
==================================
* `docker system df` - *Show docker disk Usage*

```
# docker system df
TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              1                   0                   1.84 kB             1.84 kB (100%)
Containers          0                   0                   0 B                 0 B
Local Volumes       0                   0                   0 B                 0 B
```

* `docker system events` - *Get real time events (like oc get events in openshift)* 

* `docker system info` - *Displays System-wide information*

* `docker system prune` - *Removes unused data*

```
WARNING! This will remove:
	- all stopped containers
	- all volumes not used by at least one container
	- all networks not used by at least one container
	- all dangling images
Are you sure you want to continue? [y/N]
```


New `prune` feature added:
==========================

* `docker container prune` - *Remove all stopped containers*

* `docker image prune` - *Remove unused images*

* `docker network prune` - *Remove all unused networks*

* `docker volume prune` - *Remove all unused volumes*

**above all things can be achieved by using one command also,**

* **`docker system prune`** (WARNING!, be careful with this command.)

#Reference:

* [Click here](https://docs.docker.com/engine/reference/commandline/docker/) for more command line reference.

Thanks, Happy Hacking !!!
-------------------------