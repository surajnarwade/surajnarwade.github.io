+++
date = "2018-08-13T21:21:49+05:30"
title = "Play with OpenShift Internal Docker Registry and imagestreams"
slug = "access-minishift-registry"
category = "Blog"
author = "Suraj Narwade"

+++


* Configure your shell to use docker daemon of Minishift

```
eval $(minishift docker-env)
```

* export user token registry URL 

```
export TOKEN=$(oc whoami -t)
export REGISTRY_URL=$(minishift openshift registry)
```

* Logging in to Registry

```
docker login -u developer -p $TOKEN $REGISTRY_URL
```

OR

* we can use directly as,

```
docker login -u developer -p $(oc whoam -t) $(minishift openshift registry)
```

Now you can tag images, push it to respective project to create imagestream

```
docker tag mynodejs $(minishift openshift registry)/myproject/nodejs
```

* Push the docker image to registry so that it will create imagestream automatically,

```
docker push $(minishift openshift registry)/myproject/nodejs
```

* Now you can create application using the new imagestream,

```
oc new-app --image-stream=nodejs --name app
```