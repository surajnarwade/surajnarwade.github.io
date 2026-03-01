+++
title = "RDO BUG TRIAGE DAY - First step towards Contribution !!!"
slug = "rdo-bug-triage-day-first-step-towards-contribution"
date = "2016-05-20"  
category = "Open Source"
author = "Suraj Narwade"

+++

Hello, after long time, I am writing for a good reason :P
1. had participated in RDO bug triage day on 18th and 19th of May,
   For those who dont know what is bug triage,
   Have a look at it:
   

*https://www.rdoproject.org/community/rdo-bugtriage/*

(I also came to know it just day before yesterday :-))

As a newbie to RDO community, [ChandanKumar](https://twitter.com/ciypro) and [Matthias Runge](https://twitter.com/matrunge) guided me to what to do there.
So, I had created account on http://bugzilla.redhat.com, Then I joined *#rdo*, people out there also helped me in getting things done.

So I reviewed few bugs and then [ChandanKumar](https://twitter.com/ciypro) told me, how to triaged it. Mostly I picked  following bugs:

- https://bugzilla.redhat.com/show_bug.cgi?id=1205645
- https://bugzilla.redhat.com/show_bug.cgi?id=1195977
- https://bugzilla.redhat.com/show_bug.cgi?id=1213545
- https://bugzilla.redhat.com/show_bug.cgi?id=1212899
- https://bugzilla.redhat.com/show_bug.cgi?id=1301158
  

Almost one bug for each component like cinder, glance, keystone and heat.
1. found one missing dependency So I got to fix that bug. So, [ChandanKumar](https://twitter.com/ciypro)  helped me to setup DLRN which maintains yum repositories of Openstack upstream (RDO).
   And for one bug https://bugzilla.redhat.com/show_bug.cgi?id=1212899, I have sent patch which is under review(https://review.rdoproject.org/r/#/c/1172/) right now.
   Once it approves, this will be my first contribution to RDO project. :-)
2. have learnt alot in these two days as where is Openstack repositories is maintained and how dependencies are kept. Now, onwards, I will regularly participate in bug triage days.
   

My next blog will be on how to setup [DLRN](http://dlrn.readthedocs.io/en/latest/) to Contribute in RDO, Till then, Stay tuned. Be happy. Contribute to OPenSource. :P

