+++
date = "2019-04-13T21:21:49+05:30"
title = "Creating users on OpenShift 4"
category = "Blog"
author = "Suraj Narwade"

+++


It's always bit of confusing about OpenShift 4 :D
here's how we can add user in OpenShift 4.


* Create htpasswd file as below and users and their passwords,
  

```
$ htpasswd -cb users.htpasswd user1 user1pass
```

```
$ htpasswd -b users.htpasswd user2 user2pass
```

* Through console, login with `kube:admin` user, navigate to `Administration > Cluster Settings > Global Configuration`

* Click on `Edit YAML` in front of `Oauth`

* Click on `Overview`, under `Identity Providers` section, Click on `Add` and select `HTPasswd`

* put name as `htpasswd`, mapping method as `add` and select file which we created earlier using htpasswd.

* Check `Yaml` section and verify that block as following should appear under `spec`:

```
  identityProviders:
    - htpasswd:
        fileData:
          name: htpasswd-qbwc4
      mappingMethod: add
      name: htpasswd
      type: HTPasswd
```

* Verify users,

```
$ oc login -u system:admin

$ oc get users
NAME        UID                                    FULL NAME   IDENTITIES
user1       5699e114-5e0d-11e9-9a49-0a580a800045               htpasswd:avni
user2       8c932519-5e0d-11e9-9a49-0a580a800045               htpasswd:developer
```

* Now you can login with your new users,

for example, 

```
oc login -u user1 -p user1pass
```

* Happy Hacking :)