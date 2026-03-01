+++
title = "Read Terraform's plan the better way"
slug = "read-terraforms-plan-the-better-way"
author = "Suraj Narwade"
date = "2022-12-19T07:09:47.610Z"
category = "Blog"
+++

Last year, I started exploring and studying terraform for work. While studying, I mostly relied on Terraform plan command to check the output plan.


As terraform module I was writing got bigger, the plan got bigger too, and I needed to store the plan in a file and then read \& analyse the file later.


For example, I want to read the plan for the following resource \& store it in a file,



```
resource "null_resource" "hello_world"{
  provisioner "local-exec" {
    command = "echo \"hello world\""
  }
}

```

Like any Linux user, I ran the following command,



```
terraform plan > planfile

```

and then


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672753821836/dRQnxW-rp.gif)


because look at this file,



```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
[32m+[0m create
[0m
Terraform will perform the following actions:
[1m # null_resource.hello_world[0m will be created[0m[0m
[0m [32m+[0m[0m resource "null_resource" "hello_world" {
[32m+[0m [0m[1m[0mid[0m[0m = (known after apply)
}

[0m[1mPlan:[0m 1 to add, 0 to change, 0 to destroy.[0m

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.

```

After a lot of googling, I came across this life\-changing flag,



```
terraform plan -no-color > planfile

```

and my life became easy and simple as it was before:


![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672753824307/8DL9qWRzp.gif)



```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:
# null_resource.hello_world will be created
  + resource "null_resource" "hello_world" {
      + id = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.

```

I hope this helps and makes your life a bit easy.


Happy Terraforming!!!


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


