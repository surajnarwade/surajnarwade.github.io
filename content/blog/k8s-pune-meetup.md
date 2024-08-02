+++
date = "2017-07-10T21:21:49+05:30"
title = "Kubernetes Pune Meetup - July 2017"
category = "meetup"
author = "Suraj Narwade"

+++


It was beautiful Saturday morning and we were at yet another chapter of  [kubernetes Pune meetup](https://www.meetup.com/Kubernetes-Pune/events/240840502/).
Meetup had a good turnaround of about 30 people, even it was the weekend.

[Harshal Shah](https://twitter.com/harshal2623 ) started the  Session on "Lifecycle of a Pod", where he explained in detail about various states of pods, liveness and readiness probes as well as restart policies. That was really helpful as we get to know what actually happens to pod while deploying and undeploying the application.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Now Harshal is talking about &#39;Birth of a pod&#39;  <a href="https://twitter.com/kubernetesio">@kubernetesio</a> Pune meetup. <a href="https://t.co/t5tP7DvOtt">pic.twitter.com/t5tP7DvOtt</a></p>&mdash; Praveen Kumar (@kumar_pravin) <a href="https://twitter.com/kumar_pravin/status/883555317283381248">July 8, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

<br>

Next, in the queue, there was a talk on “Making Kubernetes simpler with Kompose” by [Pradeepto](https://twitter.com/pradeepto) and [Suraj](https://twitter.com/red_suraj) which was a mind blowing thing for some attendees. Pradeepto spoke about the problem statement that Kompose is trying to solve followed by  Kompose’s journey to where it is now. He mentioned how we at Red Hat Developer Engineering became a part of it. Then he elaborated the architecture of Kompose and gave the stage to Suraj.

<br>

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr"><a href="https://twitter.com/pradeepto">@pradeepto</a> passionately explaining docker-compose lovers K8S problem at <a href="https://twitter.com/hashtag/kubernetespune?src=hash">#kubernetespune</a> meetup with <a href="https://twitter.com/red_suraj">@red_suraj</a> <a href="https://t.co/LQWhC8xmNu">pic.twitter.com/LQWhC8xmNu</a></p>&mdash; Vishal Biyani (@vishal_biyani) <a href="https://twitter.com/vishal_biyani/status/883585280724291585">July 8, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

<br>

Following by a short discussion regarding Kompose and its features, [Suraj](https://twitter.com/red_suraj) demoed Kompose by deploying few applications and showed how easy it was to deploy microservices on Kubernetes using Kompose. He explained how Kompose converts artifacts for various providers. He also covered various scenarios and edge cases. Along with that, he explained the features of Kompose such as labels which are used for ingress(not supported in docker-compose) as well as build-push support. You can find his demo examples [here](https://github.com/surajnarwade/k8s_meetup_demo ).

<br>

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Kompose talk at <a href="https://twitter.com/kubernetesio">@kubernetesio</a> Meetup Pune by <a href="https://twitter.com/red_suraj">@red_suraj</a> His Kompose demo is dope! <a href="https://t.co/ekbs2a2QqW">pic.twitter.com/ekbs2a2QqW</a></p>&mdash; Pradeepto B. (@pradeepto) <a href="https://twitter.com/pradeepto/status/883568343591997440">July 8, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

<br>

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr"><a href="https://twitter.com/pradeepto">@pradeepto</a> and <a href="https://twitter.com/red_suraj">@red_suraj</a> with <a href="https://twitter.com/hashtag/kompose?src=hash">#kompose</a> session in .<a href="https://twitter.com/kubernetesio">@kubernetesio</a> pune meetup. <a href="https://t.co/usOdl03RTS">pic.twitter.com/usOdl03RTS</a></p>&mdash; Budhram (@budhrg) <a href="https://twitter.com/budhrg/status/883560902188048384">July 8, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

<br>

We got a lot of questions around Kompose and its features. After the demo, [Pradeepto](https://twitter.com/pradeepto) concluded the talk with future for the Kompose and quick introduction to [kedgeproject](https://github.com/kedgeproject/kedge ), also he urged attendees to try out both projects and give feedbacks or file issues. 

By the way, [Kedge](https://github.com/kedgeproject/kedge) is a simple and easy way to define and deploy applications to Kubernetes by writing very concise application definitions. Please give it a spin.

After a heavy discussion and demos, we had short snacks break over a tasty samosa and cup of tea. This break seems to be good opportunity to interact with people and networking.

Last but not the least, [Rahul Mahale](https://twitter.com/Rahul_Mahale)  shared his experience with deploying Rails app using kubernetes. He also discussed problems he faced and how he overcome it with the help of tools such as Prometheus, graphana, etc.

<br>

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr"><a href="https://twitter.com/hashtag/kubernetes?src=hash">#kubernetes</a> meetup Pune #7 <a href="https://twitter.com/Rahul_Mahale">@Rahul_Mahale</a> Rails apps on K8S, a big thanks to <a href="https://twitter.com/BigBinary">@BigBinary</a> <a href="https://twitter.com/_cha1tanya">@_cha1tanya</a> for hosting <a href="https://twitter.com/infracloudio">@infracloudio</a> <a href="https://t.co/EumiMMFpjA">pic.twitter.com/EumiMMFpjA</a></p>&mdash; Vishal Biyani (@vishal_biyani) <a href="https://twitter.com/vishal_biyani/status/883584956038955008">July 8, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

<br>

Thanks to all speakers for making this meetup as a great place to learn, [Prathmesh](https://twitter.com/_cha1tanya) and  [Bigbinary](https://twitter.com/BigBinary )  for the venue and constant support for hosting the [Kubernetes](https://twitter.com/kubernetesio) Meetup Pune. Please stay tuned to the meetup page, we will have our next meetup on August 5th possibly at [Red Hat](https://www.redhat.com/en) Pune office. See you there.





