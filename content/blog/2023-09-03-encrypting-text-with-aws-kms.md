+++
title = "Encrypting text with AWS KMS"
slug = "encrypting-text-with-aws-kms"
author = "Suraj Narwade"
date = "2023-09-03T23:00:09.442Z"
category = "Blog"
+++

Encrypting and decrypting strings using AWS Key Management Service (KMS) and the AWS Command Line Interface (CLI) is a crucial part of securing sensitive data in your AWS environment. In this blog post, we'll walk through the process of encrypting and decrypting a string using KMS and the AWS CLI. This is a fundamental skill for protecting your data in AWS services like S3, RDS, and Lambda.


### Requirement


* You will need AWS CLI, You can follow this doc to install it, [https://docs.aws.amazon.com/cli/latest/userguide/getting\-started\-install.html](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
* You can follow this guide to create the KMS key, [https://docs.aws.amazon.com/kms/latest/developerguide/create\-keys.html](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html)


### Encrypting String


* Let's create a simple plaintext string for encryption purposes.



```
echo "Hello World" > hello.txt

```

* Let's run the following command to encrypt our string. If you notice we are outputting it to `encrypted.base64` because AWS CLI will encrypt it and then encode it into base64\.



```
aws kms encrypt --key-id <KMS_ID> \
  --plaintext fileb://hello.txt \
  --output text --query CiphertextBlob > encrypted.bas64

```

Now this string is safe to store as only the KMS key can decrypt this.


### **Decrypting String**


* For decrypting the string, let's first decode the base64 string. If you check the content of `encrypted`, it will be a binary blob.



```
cat encrypted.base64 | base64 --decode > encrypted

```

* Let's AWS CLI to decrypt the base64 decoded `encrypted`. You will get another base64 string



```
aws kms decrypt --ciphertext-blob fileb://encrypted --output text --query Plaintext --key-id <KMS_ID> --region eu-west-2
SGVsbG8gV29ybGQK

```

* Now we just need to decode the string to get our data string.



```
echo "SGVsbG8gV29ybGQK" | base64 --decode
Hello World

```

* You can combine the above 2 commands in one command and output the text into a file.



```
aws kms decrypt --ciphertext-blob fileb://encrypted --output text --query Plaintext --key-id <KMS_ID> --region eu-west-2 | base64 --decode > decrypted.tx

```

In this blog post, we've learned how to encrypt and decrypt a string using AWS KMS and the AWS CLI.


Remember to manage your KMS keys carefully, control access with IAM policies, and follow AWS security best practices to maintain a robust security posture in your AWS environment.


