+++
title = "Building awsctl using Golang #1"
author = "Suraj Narwade"
date = "2023-02-17T08:36:32.639Z"
category = "Blog"
+++

A few days back, I came up with the idea of awsctl CLI which will be kubectl style and will be easy to generate information about aws resources.


I decided to live stream the development of the project so that it will help beginners to understand the process and lifecycle of the OpenSource project and will help the audience to learn how to write the CLI tool.


here's the summary of first(16th Feb 2023\) stream:


* I created the repo for awsctl: <https://github.com/surajincloud/awsctl>
* we selected `Apache` Open Source License for the project using <https://choosealicense.com/>
* we then Installed the [cobra\-cli](https://github.com/spf13/cobra#usage) tool to initialize the commands.
* initialized the skeleton of the CLI and implemented `version` command :D
* we also added `ec2` \& `s3` commands but we didn't add any logic for the same.


Check out the stream recording for more:


[https://www.youtube.com/live/W9AmkQrNLqw?feature\=share](https://www.youtube.com/live/W9AmkQrNLqw?feature=share)
 


In the next stream, we will define, AWS Client and run `awsctl get ec2` command.


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


