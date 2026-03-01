+++
date = "2018-12-05T21:21:49+05:30"
title = "Local Development of golang app with minikube & ko"
slug = "develop-go-minikube"
description = "A practical post about local development of golang app with minikube & ko with lessons, examples, and useful takeaways."
category = "Blog"
author = "Suraj Narwade"

+++

<br/>

While exploring, I came across `ko` tool by google & found interesting since it buids and deploy golang applications to kubernetes easily.
This post is for minikube only since I am focussing on local development.

Pre-Flight Checks
=================

<br/>

* Installing `ko`

```
go get github.com/google/go-containerregistry/cmd/ko
```

That's it :)

* Verify your installation by `which ko`

* We can mention any docker registry (local or remote) using `KO_DOCKER_REPO` env variable, but as we are focussing on local development, we will publish images to minikube's docker daemon

```
eval $(minikube docker-env)
```

Let's do it
===========

<br/>

* take sample go web application:

```
package main

import (
	"fmt"
	"log"
	"net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hi there")
}

func main() {
	http.HandleFunc("/", handler)
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

* Now we need to write small `Deployment` (config.yml) file, but here's the magical part, instead of `image` name, we will be mentioning import path of go code. Cick [here](https://github.com/google/go-containerregistry/blob/master/cmd/ko/README.md#determining-supported-import-paths) for allowed paths.

```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: hello-world
spec:
  selector:
    matchLabels:
      foo: bar
  replicas: 1
  template:
    metadata:
      labels:
        foo: bar
    spec:
      containers:
      - name: hello-world
        # This is the import path for the Go binary to build and run.
        image: github.com/surajnarwade/webapp
        ports:
        - containerPort: 8080
```

* we also need to expose this deployment to access it

```
kubectl expose deployment hello-world --type=NodePort
```


* all set, now magic will begin,

```
ko apply -L -f config.yml
```

`-L` indicates publishing images locally

* check `minikube service hello-world`, you will see the output `Hi there`, isn't it cool ?

* Now you can make changes to code & again do `ko apply -L -f config.yml`, you will see changes reflected :)

* In this way, we can do local development of golang code with minikube, this looks cool but there's scope of more improvement too :P


What happens behind the scene ?
===============================

<br/>


* `ko` takes the import path of go code from the deployment, it builds the go binary.
* then, it creates new docker image with `distroless` as a base image and copies binary into it.
* it updates the deployment with this new image :)

<br/>


Reference:
----------

<br/>

* https://github.com/google/go-containerregistry/blob/master/cmd/ko/README.md

<br/>

**Happy Hacking :)**