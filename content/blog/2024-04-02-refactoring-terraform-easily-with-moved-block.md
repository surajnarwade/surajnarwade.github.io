+++
title = "Refactoring Terraform easily with moved block"
slug = "refactoring-terraform-easily-with-moved-block"
author = "Suraj Narwade"
date = "2024-04-02T10:20:49.779Z"
category = "Blog"
+++

As Terraform codebase grows, there are chances where you may need to refactor or rename resources in your codebase which can result into deletion and recreation of resources.


This happens because terraform thinks that you want to delete and create new resource and it allocates new id to the resource.


Instead, you should let terraform know that you want to simply move the resource instead of replacing it.


To mimic this we will take simple example where we will have AWS instance with name `a` and we want to change it to `b`


To avoid that, Terraform already has functionality where you can perform terrraform commands to rename and move the resource inside the state.


For example,



```
terraform state mv aws_instance.a aws_instance.b

```

But Running terraform commands sometimes can be daunting or may not be available because of security requirement of the organisations.


### Enter moved block


The "moved" configuration block facilitates tracking of resource relocations directly within your configuration. This feature allows you to plan, preview, and confirm resource relocations, ensuring a secure and efficient reorganization of your configuration.


For example,



```
moved {
  from = aws_instance.a
  to   = aws_instance.b
}

```

moved block only has two fields:


* `from` : id of the resource
* `to` : intended id of the resource


once you add this configuration, you can actually plan the changes and safely apply the changes.


Reference
---------


* [https://developer.hashicorp.com/terraform/tutorials/configuration\-language/move\-config](https://developer.hashicorp.com/terraform/tutorials/configuration-language/move-config)
* <https://developer.hashicorp.com/terraform/cli/state/move>
* <https://developer.hashicorp.com/terraform/language/modules/develop/refactoring>


