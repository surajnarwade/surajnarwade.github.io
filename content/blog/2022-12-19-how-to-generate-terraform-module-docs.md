+++
title = "How to generate Terraform module docs"
slug = "how-to-generate-terraform-module-docs"
author = "Suraj Narwade"
date = "2022-12-19T07:08:15.289Z"
category = "Blog"
+++

If you are Terraform user, you are pretty sure you know what the module is and must have used at least one community Terraform module. For those who don’t know what Terraform module is, Read more [here](https://learn.hashicorp.com/tutorials/terraform/module)


If you are a pro user or part of the platform engineering or DevOps team, you probably must have written one. When you write the module for yourself or other groups, it’s essential to have documentation on how to use the module, input or output parameters, etc.


The good news is that you don’t need to write documentation. Instead, you can generate it in seconds with the help of [terraform\-docs](https://terraform-docs.io/).



> *Note: provided that you have added appropriate descriptions at respective parameters.*


### Installing terraform\-docs


* `terraform-docs` It is written in Golang. Hence it is easy to install on any Operating system you are using.
* Follow this [guide](https://terraform-docs.io/user-guide/installation/) to install the `terraform-docs` utility on your respective operating system.


### Verify the installation


Once you have installed the tool, let’s verify it by running the following command, it should show the version and the operating system along with the architecture.



```
$ terraform-docs version
terraform-docs version v0.16.0 darwin/arm64

```


> **Note**: your version may differ.


### Let’s try it out


We will be using the [terraform\-civo\-kubernetes](https://github.com/surajincloud/terraform-civo-kubernetes) module. For example, I wrote this module to manage my Kubernetes clusters on Civo Cloud. Clone this repo and inside the repo directory, run the following command.



```
terraform-docs markdown table .

```

It will now generate documentation in table format and print on stdout. You can either output this to a file or copy and paste it into the README file. It looks something like this when you visualise it in the markdown file.


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672753814377/zCPRKnn_m.png)


Now we know the basic functionality of `terraform-docs`, you can further explore [official documentation](https://terraform-docs.io/) for the project and use the project like a PRO


### References


* website: [terraform\-docs.io](https://terraform-docs.io/)
* GitHub: [github.com/terraform\-docs/terraform\-docs](https://github.com/terraform-docs/terraform-docs/)


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


