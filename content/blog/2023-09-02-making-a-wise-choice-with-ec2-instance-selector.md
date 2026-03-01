+++
title = "Making a wise choice with ec2-instance-selector"
slug = "making-a-wise-choice-with-ec2-instance-selector"
author = "Suraj Narwade"
date = "2023-09-02T23:00:13.092Z"
category = "Blog"
+++

AWS has more than 200 instance types under EC2\. It's very tricky to select appropriate ec2\-instance types and surely AWS docs can be daunting.


You can check the official doc here: [https://aws.amazon.com/ec2/instance\-types/](https://aws.amazon.com/ec2/instance-types/)


ec2\-instance\-selector is an open\-source project by AWS. It is a CLI tool to helps you select compatible instance types.


Check out the repo here,


[https://github.com/aws/amazon\-ec2\-instance\-selector](https://github.com/aws/amazon-ec2-instance-selector)
 


### **Features**


* You can filter AWS Instance types using criteria like vcpu, memory, network performance, etc.
* You can use this project as a Go library to embed this functionality into your Golang projects, I will surely embed this in [awsctl](https://github.com/surajincloud/awsctl).


Installing ec2\-instance\-selector
----------------------------------


* On Mac



```
brew tap aws/tap
brew install ec2-instance-selector

```

* On Linux \& Windows, you can download the binary from the [release page](https://github.com/aws/amazon-ec2-instance-selector/releases/tag/v2.4.1).
* verify installation



```
ec2-instance-selector --version
v2.4.1

```

Usage
-----


* Lookup all the instance type details interactively



```
ec2-instance-selector -o interactive

```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1693654129163/98a1e68c-9743-4519-af4b-0abe480d8484.gif)


* Not all regions have all the instance types, so you can pass the region of your choice via `--region` flag



```
ec2-instance-selector --region eu-west-2

```

* If you are looking for a specific vcpu or memory count, you can pass flags as shown below,



```
ec2-instance-selector --vcpus 4 --memory 16
g4ad.xlarge
g4dn.xlarge
g5.xlarge
im4gn.xlarge
...
...

```

* you can also print in tabular format,



```
➜ ec2-instance-selector --vcpus 4 --memory 16 -o table
Instance Type        VCPUs        Mem (GiB)
-------------        -----        ---------
g4ad.xlarge          4            16
g4dn.xlarge          4            16
g5.xlarge            4            16
im4gn.xlarge         4            16
...
...

➜ ec2-instance-selector --vcpus 4 --memory 16 -o table-wide
Instance Type  VCPUs   Mem (GiB)  Hypervisor  Current Gen  Hibernation Support  CPU Arch  Network Performance  ENIs    GPUs    GPU Mem (GiB)  GPU Info             On-Demand Price/Hr  Spot Price/Hr (30d avg)
-------------  -----   ---------  ----------  -----------  -------------------  --------  -------------------  ----    ----    -------------  --------             ------------------  -----------------------
g4ad.xlarge    4       16         nitro       true         false                x86_64    Up to 10 Gigabit     2       1       8              AMD Radeon Pro V520  $0.44271            $0.25325
g4dn.xlarge    4       16         nitro       true         false                x86_64    Up to 25 Gigabit     3       1       16             NVIDIA T4            $0.615              $0.21569
g5.xlarge      4       16         nitro       true         false                x86_64    Up to 10 Gigabit     4       1       24             NVIDIA A10G          $1.277              $0.3831
im4gn.xlarge   4       16         nitro       true         false                arm64     Up to 25 Gigabit     4       0       0              none                 $0.42209            $0.24382
m4.xlarge      4       16         xen         false        true                 x86_64    High                 4       0       0              none                 $0.232              $0.11175
...
...

```

These are just a few examples of `ec2-instance-selector` but you can do much more such as finding instances by prices, CPU architecture, EBS, etc.


You can find this information in the help section,



```
ec2-instance-selector --help

```

If you find any useful commands with `ec2-instance-selector` , do let me know in the comment section.


Have a lovely day :)


