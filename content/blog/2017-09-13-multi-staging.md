+++
date = "2017-09-13T21:21:49+05:30"
title = "Multistage build in Docker"
slug = "multi-staging"
category = "Blog"
author = "Suraj Narwade"

+++



<br>

### Example Use Cases

When might you want to use a multi-stage build? It allows you to do an entire pipeline within a single build, rather than having to script the pipeline externally. Here’s a few examples…

* Java apps using WAR files 
	* First stage uses a container with Maven to compile, test, and build the war file
	* Second stage copies the built war file into an image with the app server (Wildfly, Tomcat, Jetty, etc.)

* Golang apps with standalone binaries
	* First stage uses a container with golang SDK to build the binary
	* Second stage copies the binary into an image

* Node.js app needing processed JavaScript for client
	* First stage uses a Node container, installs dev dependencies, and performs a build (maybe compiling Typescript, Webpack-ify, etc.)
	* Second stage also uses a Node container, installs only prod dependencies (like Express), and copies the distributable from stage one




<br>

Here, we will take simple `hello-world` golang program to illustrate single-stage build vs multi-stage build:


```
$ cat main.go 
package main

import "fmt"

func main() {
	fmt.Println("Hello World!")
}

```


### Single stage build

Usually for static type langauge as golang, derive Dockerfile from golang SDk, add source, do a build and push it to dockerhub, unfortuantely size was very huge. for example, basic `golang:alpine` image is of size 257 MB, it increases further more.

workaround was :
Derive from a Golang base image with the whole runtime/SDK (Dockerfile.build)
Add source code
Produce a statically-linked binary
Copy the static binary from the image to the host (docker create, docker cp)
Derive from SCRATCH or some other light-weight image such as alpine (Dockerfile)
Add the binary back in
Push a tiny image to the Docker Hub


* that means having two separate Dockerfiles and shell script to control it

* Dockerfile.singlestage

```
FROM golang:alpine
WORKDIR /app
ADD . /app
RUN cd /app && go build -o app
ENTRYPOINT ./app
```

* now build the dockerfile and run it using following commands,

```
$ docker build -t surajnarwade/go-singlestage-app .
$ docker run --rm surajnarwade/go-singlestage-app
```

* You can check docker image size which is only **259MB**:

```
$ docker images
REPOSITORY         		 TAG                 IMAGE ID            CREATED             SIZE
go-singlestage-app               latest              bb2594c6d2fd        3 days ago          259MB
```

### Multistage build

* It removes hassle of maintaining multiple files, so now we have to maintain only one Dockerfile

* syntax is very simple, we will give label to stages as

```
FROM <image> as <label>
``` 

* and then use it in another stage to copy artifacts as,

```
COPY --from=<label>
```

* whichever is last FROM statement, that is the final base image
* Dockerfile.multistage

```
# build stage
FROM golang:alpine AS build-env
ADD . /src
RUN cd /src && go build -o app

# final stage
FROM alpine
WORKDIR /app
COPY --from=build-env /src/app /app/
ENTRYPOINT ./app
```
now build the dockerfile and run it using following commands,

```
$ docker build -t surajnarwade/go-multistage-app .
$ docker run --rm surajnarwade/go-multistage-app
```

* you can see docker image size is reduced which is only approximately **6 MB**:

```
$ docker images
REPOSITORY         		 TAG                 IMAGE ID            CREATED             SIZE
go-multistage-app                latest              975ef40d39ee        3 days ago          5.52MB
```


#### Reference:

* https://blog.alexellis.io/mutli-stage-docker-builds/
* https://github.com/appleboy/docker-multi-stage-build
* https://blog.mikesir87.io/2017/03/introducing-docker-multi-stage-builds/
