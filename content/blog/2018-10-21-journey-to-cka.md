+++
date = "2018-10-21T21:21:49+05:30"
title = "Journey to the CKA"
slug = "journey-to-cka"
description = "A personal account of the journey to the CKA and the lessons learned along the way."
category = "Blog"
author = "Suraj Narwade"

+++

 Hello Folks,
 
 I appeared for the exam on 19th October 2018 and  yesterday(20th Oct 2018), I got the result & I scored **92%**, Yay :)

I got so many request about How to prepare for the exam, Here's my experience with preparation for the exam.

A bit about Exam:
=================
<br/>

* Duration: 3 hours (which is fair time if you do lot of practice, I completed in 2 hours 15 mins)
* Questions: 24
* Clusters: 6

After clearing 9 certs to become RHCA (Red Hat Certified Architect), exam environment was kind of familiar to me.

to be specific, it's browser based exam which is monitored by a proctor, where right half of the screen is your terminal and left half is your question pane.

Like Red Hat Exams, CKA is also hands-on exam which test your practical, troubleshooting knowledge with kubernetes.



Beginning:
==========
<br/>

* Register for the exam [here](https://www.cncf.io/certification/cka/).
* Read [Candidate Handbook](https://www.cncf.io/certification/candidate-handbook) and [exam tips](https://www.cncf.io/certification/tips) carefully.

Preparing for the Exam: Phase 1 (Kubernetes Knowledge):
========================================================
<br/>

* [Walid Shaari](https://twitter.com/walidshaari)'s [github repo](https://github.com/walidshaari/Kubernetes-Certified-Administrator) is extremely useful entrypoint for getting starting around syllabus and many useful links and guide :)

* Create mind map of [Kubernetes docs](https://kubernetes.io/docs/home), it will help you to find something quickly since you can use docs for the reference.
  
* Get your hands dirty with [`Kubernetes tasks`](https://kubernetes.io/docs/tasks/).

* Go through [`Kubernetes Concepts`](https://kubernetes.io/docs/concepts/) and [`Kubernetes Reference`](https://kubernetes.io/docs/reference/) at least twice.
  
* To utilise time wisely, familiar yourself with kubectl command the imperative way, that means, instead of writing yamls, try to generate it,
Refer [`Kubectl Cheatsheet`](https://kubernetes.io/docs/reference/kubectl/cheatsheet/) and [Managing Kubernetes Objects Using Imperative Commands](https://kubernetes.io/docs/concepts/overview/object-management-kubectl/imperative-command/) for the same. You might also like to check out [blog](https://suraj.pro/post/kubectl/) post about `kubectl` which I wrote last year.  


For example, 

```
To generate pod defination, I will do,

kubectl run mypod --image=busybox --generator=run-pod/v1
```

  
<br/>

* Kubernetes Cluster:
---------------------

  * [`Kubernetes the hard way`](https://github.com/kelseyhightower/kubernetes-the-hard-way/) by [Kelsey Hightower](https://twitter.com/kelseyhightower), 
  Please do these at least 5 times. This tutorial is performed on GCP, but you can do it locally as well.

  * [Kinvolk's](https://twitter.com/kinvolkio) [`Kubernetes The Hard way`](https://github.com/kinvolk/kubernetes-the-hard-way-vagrant) on vagrant is also useful to study.
  
  * After learning the hard way, for practicing other topics, you can use same cluster or you can use:
      * [minikube](https://github.com/kubernetes/minikube/)
      * [katacoda kubernetes playground](https://www.katacoda.com/courses/kubernetes/playground)
      * set up with [kubeadm](https://kubernetes.io/docs/setup/independent/install-kubeadm/) (it's so simple)

* Books:
  ------

  * Specifically for the exam, I didn't refer any books, but if you are newbie, it will be awesome if you go through following books:
      * `Kubernetes: Up and Running: Dive into the Future of Infrastructure`
      * `Kubernetes in Action` 

Preparing for the Exam: Phase 2 (Supporting Knowledge): 
========================================================
<br/>

* Learn `tmux`. Since, exam provides only one terminal window, `tmux` will help to split screen into panes and more windows, here's my [`cheatsheet`](https://gist.github.com/surajnarwade/2e398402960f6b0c1ed2daed681b934d) for tmux.

* Learn how to work with systemd files,

```
# reloading the systemd daemon

systemctl daemon-reload

# starting and enabling the service

systemctl restart <service>
systemctl enable <service>
```

* Debugging systemd services:

```
systemctl status <service>

journalctl -u <service>
```

Before the Exam:
================
<br/>

* Since you can give exam on chrome only, you have to be careful about `Ctrl+w` which closes the current tab in chrome. To avoid this,
* My friend Suraj Deshmukh (who also cleared CKA on the same time and his blog on  experience with CKA is [here](https://suraj.io/post/road-to-cka/) ) wrote a blog about disabling `Ctrl+w` which you can find [here](https://suraj.io/post/disable-ctrl-w/). 

First Minute of Exam:
=====================
<br/>

* Make alias of few commands as per your convenience and put it in bashrc file,

mine were,

```
k = 'kubectl'
kgp = 'kubectl get pods'
kgs = 'kubectl get svc'
kgc = 'kubectl get componentstatus'
```

* kubectl autocompletion, it really helps :)

```
source <(kubectl completion bash)
```

Finally...
==========
<br/>

* Thanks to [@kubernauts](https://twitter.com/kubernauts) community for all the guidance and help :)
If you are preparing for CKA or if you deal with kubernetes, please join [Kubernauts community slack](https://kubernauts-slack-join.herokuapp.com/), there are really lovely kubernauts who are ready to help you out :)

* If you have any further query, feel free to reach out to me via [@red_suraj](https://twitter.com/red_suraj) on twitter or [Suraj Narwade](https://www.linkedin.com/in/surajnarwade/) on linkedin.

* Here's my certificate:
  

<br/>

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Successfully completed the <a href="https://twitter.com/CloudNativeFdn?ref_src=twsrc%5Etfw">@CloudNativeFdn</a> <a href="https://twitter.com/hashtag/CKA?src=hash&amp;ref_src=twsrc%5Etfw">#CKA</a> :)<br>Thanks <a href="https://twitter.com/kubernauts?ref_src=twsrc%5Etfw">@kubernauts</a> for all support :) <a href="https://t.co/mmazHFBUSt">pic.twitter.com/mmazHFBUSt</a></p>&mdash; Suraj Narwade 🇮🇳 (@red_suraj) <a href="https://twitter.com/red_suraj/status/1053706874917289984?ref_src=twsrc%5Etfw">October 20, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<br/>

All the Best :)
---------------