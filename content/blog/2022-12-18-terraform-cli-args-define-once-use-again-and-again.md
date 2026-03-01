+++
title = "Terraform CLI Args, define once, use again and again"
slug = "terraform-cli-args-define-once-use-again-and-again"
author = "Suraj Narwade"
date = "2022-12-18T15:32:10.384Z"
category = "Blog"
+++

I regularly use `*terraform plan -no-color*` command to check the plan in a more readable way. I wrote a blog post regarding the same [here](https://surajincloud.com/blog/read-terraform-plan-the-better-way/). Give it a read if you haven’t already.


Running this command every time is quite cumbersome. Hence I was looking for options and came across these terraform environment variables features.


I can export `TF_CLI_ARGS` as shown below with the arg.



```
export TF_CLI_ARGS="-no-color"

```

Now, If I run `terraform plan` it will be equivalent of `terraform plan -no-color`


but If I run `terraform apply` it starts to fail as `-no-color` is not a valid arg for `terraform apply`.


To solve this problem, we can also mention command\-specific args, such as `TF_CLI_ARGS_plan` or `TF_CLI_ARGS_apply` , In my case, as shown below,



```
export TF_CLI_ARGS_plan="-no-color"

```

I added the above statement in my bashrc, and life is a little easier with terraform now.


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


