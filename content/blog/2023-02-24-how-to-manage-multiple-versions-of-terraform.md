+++
title = "How to manage multiple versions of Terraform?"
slug = "how-to-manage-multiple-versions-of-terraform"
description = "Learn how to manage multiple versions of terraform with practical steps and examples."
author = "Suraj Narwade"
date = "2023-02-24T16:00:39.160Z"
category = "Blog"
+++

Terraform is a popular tool for Infrastructure as a Code practice to manage Cloud Infrastructure using declarative ways.


As Infrastructure grows, the number of projects grows, and there's a fair chance that you may not have the same terraform versions among your projects.


There can be various reasons for it. As a result, you may need multiple terraform versions installed on your machine, which can get a little tricky.


Worry not. There's an open\-source project to solve this problem [tfenv](https://github.com/tfutils/tfenv).


### What is tfenv?


Tfenv is a simple command\-line tool that helps you manage multiple versions of Terraform. It allows you to easily switch between different versions of Terraform, depending on your requirements.


### Installing tfenv


**On Mac**


* Install using brew



```
brew install tfenv

```

**Manual Installation**


You can use following



```
$ git clone --depth=1 https://github.com/tfutils/tfenv.git ~/.tfenv
$ echo 'export PATH=$PATH:$HOME/.tfenv/bin' >> ~/.bashrc

```

Restart the Terminal, and you are set to use `tfenv`


### Verify the Installation


* Run the following command to verify the installation. It should also show the output as shown below.



```
$ tfenv
tfenv 3.0.0
Usage: tfenv <command> [<options>]

Commands:
   install       Install a specific version of Terraform
   use           Switch a version to use
   uninstall     Uninstall a specific version of Terraform
   list          List all installed versions
   list-remote   List all installable versions
   version-name  Print current version
   init          Update environment to use tfenv correctly.
   pin           Write the current active version to ./.terraform-version

```

### Usage


Once `tfenv` is installed, you are all set to manage multiple terraform versions.


* Installing specific versions of Terraform



```
  $ tfenv install 1.3.6
  Installing Terraform v1.3.6
  Downloading release tarball from https://releases.hashicorp.com/terraform/1.3.6/terraform_1.3.6_linux_amd64.zip
  ####################################################################################################################### 100.0%
  Downloading SHA hash file from https://releases.hashicorp.com/terraform/1.3.6/terraform_1.3.6_SHA256SUMS
  Not instructed to use Local PGP (/home/suraj/.tfenv/use-{gpgv,gnupg}) & No keybase install found, skipping OpenPGP signature verification
  terraform_1.3.6_linux_amd64.zip: OK
  Archive:  /tmp/tfenv_download.Xvaw0x/terraform_1.3.6_linux_amd64.zip
    inflating: /home/suraj/.tfenv/versions/1.3.6/terraform  
  Installation of terraform v1.3.6 successful. To make this your default version, run 'tfenv use 1.3.6'

```


* List all the available installed terraform versions



```
  $ tfenv list
    1.3.9
    1.3.8
  * 1.3.7 (set by /home/suraj/.tfenv/version)

```

 It will show you all the available versions and `*` will show the current terraform version.
* You can also print the current terraform version using the following command,



```
  $ tfenv version-name
  1.3.7

```
* If you want to switch to another version, you can do as shown below,



```
  $ tfenv use 1.3.8
  Switching default version to v1.3.8
  Default version (when not overridden by .terraform-version or TFENV_TERRAFORM_VERSION) is now: 1.3.8

```
* uninstalling specific versions of terraform



```
  $ tfenv uninstall 1.3.6
  Uninstall Terraform v1.3.6
  Terraform v1.3.6 is successfully uninstalled

```


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


