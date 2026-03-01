+++
title = "Encrypting EC2 Sessions with KMS and SSM Session Managers"
slug = "encrypting-ec2-sessions-with-kms-and-ssm-session-managers--deleted"
description = "A practical post about encrypting ec2 sessions with kms and ssm session managers with lessons, examples, and useful takeaways."
author = "Suraj Narwade"
date = "2023-02-20T15:54:58.870Z"
category = "Blog"
+++

In a previous blog post, we have seen how to enable SSM Session for EC2 instances to ditch SSH and enable safe and secure shell access.


In this blog post, we will go one step further towards security and encrypting our SSM sessions from EC2\-managed nodes and the local machines of users with the help of the KMS key.


### Creating the KMS key


* To encrypt the sessions in SSM, we will first need to have the KMS key.



```
aws kms create-key --description "SSM Session Manager Key" --origin AWS_KMS

```

This will create a KMS key for us.


Or you can use Terraform to create KMS keys.


### Adding appropriate permissions to the EC2 instance.


You must create an IAM policy with the following permissions and attach it to the IAM role attached to the EC2 instance.



```
kms:Encrypt

```

### Enable KMS encryption in SSM Session Manager.


* Go to the AWS Systems Manager console, [https://console.aws.amazon.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/).
* Select Session Manager from the navigation.
* Choose the **Preferences** tab, and then choose **Edit**.
* Select the check box next to **Enable KMS encryption**.
* Select the existing KMS key from the list or fill the box with KMS Key ARN (you can also click on create a key, which will take you to the KMS Console)
* Choose **Save**.


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


