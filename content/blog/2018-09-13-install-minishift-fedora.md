+++
date = "2018-09-13T21:21:49+05:30"
title = "Installing Minishift on Fedora"
slug = "install-minishift-fedora"
category = "Blog"
author = "Suraj Narwade"

+++



Check for latest release on github:
-----------------------------------

* Check here: https://github.com/minishift/minishift/releases


Download latest binary
----------------------

```
wget https://github.com/minishift/minishift/releases/download/v1.24.0/minishift-1.24.0-linux-amd64.tgz

tar -xvf minishift-1.24.0-linux-amd64.tgz

cp minishift ~/.local/bin/minishift
```

NOTE: Create `~/.local/bin` if it's not present, it's already set in PATH variable.

Setting up Virtualization environment
-------------------------------------


```
sudo dnf install libvirt qemu-kvm -y
```


```
sudo usermod -a -G libvirt $USER
```

```
newgrp libvirt
```

Setting the KVM driver
----------------------

```
sudo curl -L https://github.com/dhiltgen/docker-machine-kvm/releases/download/v0.7.0/docker-machine-driver-kvm -o /usr/local/bin/docker-machine-driver-kvm
sudo chmod +x /usr/local/bin/docker-machine-driver-kvm
```


Reference:
----------

* https://docs.okd.io/latest/minishift/getting-started/installing.html
* https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html 


