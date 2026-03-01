+++
date = "2017-12-13T21:21:49+05:30"
title = "Mounting host folders in minishift"
slug = "hostmount-minishift"
description = "A practical post about mounting host folders in minishift with lessons, examples, and useful takeaways."
category = "Blog"
author = "Suraj Narwade"

+++


Make sure `sshd` service is running on your host.

```
$ sudo systemctl status sshd
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; disabled; vendor preset: disabled)
   Active: active (running) since Wed 2017-12-13 18:23:09 IST; 4min 14s ago
     Docs: man:sshd(8)
           man:sshd_config(5)
 Main PID: 11319 (sshd)
    Tasks: 1 (limit: 4915)
   Memory: 4.9M
      CPU: 333ms
   CGroup: /system.slice/sshd.service
           └─11319 /usr/sbin/sshd -D

Dec 13 18:23:09 localhost.localdomain systemd[1]: Starting OpenSSH server daemon...
Dec 13 18:23:09 localhost.localdomain sshd[11319]: Server listening on 0.0.0.0 port 22.
Dec 13 18:23:09 localhost.localdomain sshd[11319]: Server listening on :: port 22.
Dec 13 18:23:09 localhost.localdomain systemd[1]: Started OpenSSH server daemon.
```

Create 