+++
title = "Let's SSM, not SSH, on EC2 instances"
author = "Suraj Narwade"
date = "2023-02-20T18:40:39.193Z"
category = "Blog"
+++

To access EC2 instances, SSH has been the preferred way for many years. But this way has its downsides, such as managing the keypairs, etc.


Recently, I came across a new safe and secure way to access instances, even from a Web browser and from the terminal, which is SSM. Let's learn more about SSM and how to set it up.


**What is SSM?**


SSM stands for Systems Manager, a set of tools such as Session manager, State manager, patch manager, etc.


Among all of them, we are going to focus on Session Manager as a replacement for SSH.


**What is SSM Session Manager?**


Session Manager is a feature of AWS Systems Manager (SSM) that allows you to establish secure, bi\-directional connectivity between your local computer and an Amazon Elastic Compute Cloud (EC2\) instance using a web\-based shell or the AWS CLI.


**Key features of SSM Session Manager**


Some of the key features of Session Manager are as follows:


1. **Secure communication** using IAM and SSL.
2. **Fine\-grained access control**: With the IAM policy's help, you can control who has access to start sessions and require MFA for added security.
3. **Audit**: It logs all session activity, providing a complete audit trail of who accessed an instance and what actions were performed.


**Supported Operating Systems:**


* Amazon Linux 2
* Amazon Linux
* Ubuntu Server
* Red Hat Enterprise Linux (RHEL)
* SUSE Linux Enterprise Server (SLES)


**How does it work?**


You will need to install the SSM agent on your EC2 machine. Amazon Linux or EKS Optimised AMIs comes with pre\-installed SSM agents.


Refer to this documentation to install SSM Agent: [https://docs.aws.amazon.com/systems\-manager/latest/userguide/sysman\-manual\-agent\-install.html](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-manual-agent-install.html).


Verify that the agent is installed,



```
sudo systemctl status amazon-ssm-agent

```

Now that agent is installed, we will need to give appropriate IAM permissions to the EC2 instance.


We will need to attach the default IAM Policy `AmazonSSMManagedInstanceCore` to the IAM role of the EC2 instance.


If you cannot use default policies, follow this documentation and manually create the policy: [https://docs.aws.amazon.com/systems\-manager/latest/userguide/getting\-started\-add\-permissions\-to\-existing\-profile.html](https://docs.aws.amazon.com/systems-manager/latest/userguide/getting-started-add-permissions-to-existing-profile.html).


### **Accessing the instance via Browser**


* To access the instance using SSM, head over to the EC2 console.
* Select the EC2 instance you wish to access.
* Click on Connect and now select `Session Manager` and then click on `Connect`
* It will open a new tab in your browser with shell access to your instance.


### **Accessing the instance via AWS CLI**


* To access the instance using AWS CLI, you will need a session manager plugin for AWS CLI
* Follow this documentation to install the plugin: [https://docs.aws.amazon.com/systems\-manager/latest/userguide/session\-manager\-working\-with\-install\-plugin.html](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html)
* Verify that the plugin is installed by running the following command,
* ```
  $ session-manager-plugin
  The Session Manager plugin is installed successfully. Use the AWS CLI to start a session.

```
* Now you can run the following command to connect to the instance



```
  aws ssm start-session --target <instance-id>

```

 In This way, you can completely ditch SSH and start using SSM to access your EC2 instances.


 I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.


