+++
date = "2019-01-17T21:21:49+05:30"
title = "Story of Noob Developer on OpenShift"
slug = "warlock-odo"
description = "A practical post about story of noob developer on openshift with lessons, examples, and useful takeaways."
category = "Blog"
author = "Suraj Narwade"

+++



A NodeJS developer named Warlock, recently joined his dream company as an Enterprise Developer.

On day 1, his boss gave him a Code Repository & his OpenShift Platform Credentials to work his magic.

Warlock got his code,

```
git clone https://github.com/sclorg/nodejs-ex
```

But was unclear about how to proceed  as he had no idea about OpenShift.
He then referred to the OpenShift docs but got confused and overwhelmed by all the new terminologies, DeploymentConfigs, Pods, Services, and Routes mentioned in it. He could barely login into the cluster using `oc` and thus got frustrated.

Then, one of his friend suggested that he take a look at [odo](https://openshiftdo.org), Warlock instantly took to it, as it had simple, easy to understand concepts like Application, Components, and URLs.

He found it interesting, read the docs further, and started using it.

* He logged into the cluster with the credentials given by his boss,

```
odo login -u warlock@dream.company -p xxxxxxx
```

* Warlock read the concept of an application in odo & created an application:

```
odo app create myapp
```

* He referred the odo catalog to check on the availability of NodeJS,

```
odo catalog list components
NAME        PROJECT       TAGS
dotnet      openshift     2.0,latest
httpd       openshift     2.4,latest
nginx       openshift     1.10,1.12,1.8,latest
nodejs      openshift     0.10,10,4,6,8,8-RHOAR,latest
perl        openshift     5.16,5.20,5.24,5.26,latest
php         openshift     5.5,5.6,7.0,7.1,latest
python      openshift     2.7,3.3,3.4,3.5,3.6,latest
ruby        openshift     2.0,2.2,2.3,2.4,2.5,latest
wildfly     openshift     10.0,10.1,11.0,12.0,13.0,8.1,9.0,latest
```

Woo, Warlock was happy because NodeJS was available in the cluster.


Since he had only one component of the type nodejs, he created the application,

```
odo create nodejs
```


* Now what? Warlock needed an URL to access his application, he tried:

```
odo url create
```

* There was nothing on the URL, warlock got confused, then he realized, he hadn’t pushed the code. So he pushed his code:


```
odo push
```


Tada, Now he could access his application, everytime he made some changes he did `odo push` and woah :)


* After a while, he got bored of pushing again and again. On further exploration of the hallowed odo docs he found the watch feature, and performed `odo watch` in his repo and wooooot !!!!

```
odo watch
```

Now everytime he changed his code, the changes were automatically reflected in the URL, he did not need to manually push each time.

In this way, Warlock the newbie developed his NodeJS application on OpenShift Platform.


Now, Warlock always uses odo, Warlock is Happy, Be like Warlock :D
===========================================================







