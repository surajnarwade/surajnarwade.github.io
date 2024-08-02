+++
title = "[SOLVED] MFA Entity already exists Error in AWS IAM"
author = "Suraj Narwade"
date = "2022-12-18T15:45:03.524Z"
category = "Blog"
+++

Sometimes, When IAM user tries to register a new Virtual MFA device using Google Authenticator or similar tools, they notice the following error,



```
Entity Already exists
This entity already exists. MFADevice entity at the same path and name already exists. Before adding a new virtual MFA device, ask your administrator to delete the existing device using the CLI or API.

```

This happens because the MFA device was created but not enabled for the users. It may happen because of various reasons. If you are a system administrator or platform engineer, this can be little pain to figure out the issue.


Luckily, the solution is straightforward, as shown below,


* List all Virtual MFA devices and find out the MFA serial number,



```
aws iam list-virtual-mfa-devices

```

For example, if your username is suraj, you can try the following command,



```
$ aws iam list-virtual-mfa-devices | grep suraj
arn:aws:iam::0123456789:mfa/suraj

```

Once you know the ARN of MFA, now we have to delete this dangling MFA using the following command,



```
$ aws iam delete-virtual-mfa-device --serial-number arn:aws:iam::0123456789:mfa/suraj

```

Now users should be able to register new MFA devices with their IAM users.


I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


