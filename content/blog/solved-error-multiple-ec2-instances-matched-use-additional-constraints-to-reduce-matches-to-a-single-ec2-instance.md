+++
title = "[SOLVED] Error: multiple EC2 Instances matched; use additional constraints to reduce matches to a single EC2 Instance"
author = "Suraj Narwade"
date = "2023-02-12T09:58:31.986Z"
category = "Blog"
+++

When you are using data sources for `aws_instance` probably to fetch the IP address or something else,



```
data "aws_instance" "foo" {
  filter {
    name   = "tag:Name"
    values = ["instance-name"]
  }
}

```

You must have faced the following issues while using Terraform for EC2 instances.



```
│ Error: multiple EC2 Instances matched; use additional constraints to reduce matches to a single EC2 Instance

```

### Why this issue occurs?


There's a fair chance that the previous instance with the same name is destroyed, but there's still an entry with the terminated state.


### Resolution


In this case, you will need to add another filter that will only retrieve running instances, not the terminated ones.



```
data "aws_instance" "foo" {
  filter {
    name   = "tag:Name"
    values = ["instance-name"]
  }
  filter {
    name   = "instance-state-name"
    values = ["running"]
  } 
}

```

Note: [`instance_state`](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/instance#instance_state) field won't work in this case.


I hope you learnt something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you!


