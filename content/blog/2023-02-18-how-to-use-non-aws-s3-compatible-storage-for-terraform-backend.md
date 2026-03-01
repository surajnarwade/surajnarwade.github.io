+++
title = "How to use non-AWS S3 Compatible storage for Terraform Backend?"
slug = "how-to-use-non-aws-s3-compatible-storage-for-terraform-backend"
description = "Learn how to use non-aws s3 compatible storage for terraform backend with practical steps and examples."
author = "Suraj Narwade"
date = "2023-02-18T15:30:39.163Z"
category = "Blog"
+++

As we know, Terraform supports S3 as a backend to store the state in AWS. In GCS and Azure, there are equivalent solutions for object storage available.


What if we want to store terraform state in our environment or on any other cloud provider?


The good news is that we can do that, as all we need is S3\-compliant storage.


An example can be:


* [Minio](https://min.io/): minio is S3 compatible Opensource Object storage
* [Civo Object store](https://www.civo.com/compute): I have already shared a blog post for the same [here](https://surajincloud.com/civo-object-store-as-a-terraform-backend)


For this example, we will use minio:


* Let's run minio in a docker container locally

```bash
mkdir -p ${HOME}/minio/data

docker run \
   -p 9000:9000 \
   -p 9090:9090 \
   --user $(id -u):$(id -g) \
   --name minio1 \
   -e "MINIO_ROOT_USER=ADMIN" \
   -e "MINIO_ROOT_PASSWORD=PASSWORD" \
   -v ${HOME}/minio/data:/data \
   quay.io/minio/minio server /data --console-address ":9090"
```
* Once it is running, go to [http://127\.0\.0\.1:9090](http://127.0.0.1:9090/access-keys) and log in using the credentials
* create access key [http://127\.0\.0\.1:9090/access\-keys](http://127.0.0.1:9090/access-keys)
* Once you have the access keys, you can now define the backend as shown below:



```hcl
terraform {
  backend "s3" {
    bucket                      = "state"
    key                         = "terraform.tfstate"
    region                      = "myregion"
    endpoint                    = "http://127.0.0.1:9090"
    skip_region_validation      = true
    skip_credentials_validation = true
    skip_metadata_api_check     = true
    force_path_style            = true
  }
}

```

What's happening here?


* Setting `skip_credentials_validation` to true will disables validation of the credentials when communicating with the S3 service. This is needed as we are using non\-AWS S3\-compatible storage.
* Setting `skip_metadata_api_check` to true will disable the metadata API check (specific to AWS) when communicating with the S3 service.


* With `force_path_style` we are telling terraform to use a path style for the URL
* Setting `skip_region_validation` to true will disable region validation as it is again specific to AWS.


You can also export credentials in the environment variables as shown instead of keeping them in the code



```bash
export AWS_S3_ENDPOINT=<minio-url>
export AWS_ACCESS_KEY_ID=<access-key>
export AWS_SECRET_ACCESS_KEY=<secret-key>

```

That's it. You are now ready to use non\-AWS S3 compliant Storage for Terraform backend and do,



```bash
terraform init

```

I hope you learned something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you.

