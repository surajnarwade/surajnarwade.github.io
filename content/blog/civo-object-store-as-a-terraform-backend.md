+++
title = "Civo Object Store as a Terraform Backend"
author = "Suraj Narwade"
date = "2023-02-12T12:38:12.062Z"
category = "Blog"
+++

Recently, Civo Cloud launched an object store that is object Storage and S3\-compatible.


Read more about it here: [https://www.civo.com/learn/using\-civo\-object\-stores](https://www.civo.com/learn/using-civo-object-stores)


In my [Cloud Heist \- Civo series](https://www.youtube.com/playlist?list=PLbxFcvwuGy_qFFr0GttRTYJCvy4jNAP55) on youtube, one of the viewers asked how we can store terraform state in the bucket, similar to how we do in AWS space.


Here's the solution for the same,


* Let's create the object store



```
  resource "civo_object_store" "statefile" {
      name = "state"
      max_size_gb = 500
      region = "LON1"
  }

```
* Now apply this config. It will create the bucket and local state file.



```
  $ terraform apply

```
* now let's verify from the console that the bucket is created (make sure you are in the correct region)
* we will use civo CLI to get the information about the object store.
* ```
    # state is the name of the bucket/objectstore
    $ civo objectstore show state 

                         ID : 9177e5e5-f04f-4a9b-bc25-6e028ca54187
                       Name : state
                       Size : 500
      Object Store Endpoint : objectstore.lon1.civo.com
                     Region : LON1
                 Access Key : <ACCESS_KEY>
                     Status : ready
      To access the secret key run: civo objectstore credential secret --access-key=<ACCESS_KEY>

```

 \* note down the access key from the final output and run the following command to get the required environment variables. since we are using `s3` backend, which is mainly used for AWS, we will have to use environment variables with the prefix `AWS`
* ```
    $ civo objectstore credential export --access-key=<ACCESS_KEY>
      # Tip: You can redirect output with (>> ~/.zshrc) to add these to Zsh's startup automatically
      export AWS_ACCESS_KEY_ID=<ACCESS_KEY>
      export AWS_SECRET_ACCESS_KEY=<SECRET_ACCESS_KEY>
      export AWS_DEFAULT_REGION=LON1
      export AWS_HOST=https://objectstore.lon1.civo.com

```
* Now export those variables or store them in the bashrc or zshrc.
* Let's define the terraform backend. As mentioned earlier, we are using the S3 backend, which is used for AWS. We will need to skip the region checks and metadata API checks.



```
terraform {
  backend "s3" {
    bucket                      = "state"
    key                         = "terraform.tfstate"
    region                      = "LON1"
    endpoint                    = "https://objectstore.lon1.civo.com"
    skip_region_validation      = true
    skip_credentials_validation = true
    skip_metadata_api_check     = true
    force_path_style            = true
  }
}

```

Now Run terraform init.



```
$ terraform init

```

After this existing state is copied to the new backend, the state for whatever resources we create will be stored in the new state backend.



> Note: As there is no dynamoDB equivalent in the civo cloud, we could not implement State locking mechanism, Hence, If you are using approach mentioned in this blog, be careful.
> 
> 
> also follow the issue: <https://github.com/hashicorp/terraform/issues/27070>


I hope you learnt something new from this blog post. Click [**here**](https://surajincloud.com/about) to learn about me and how you can support my work, Thank you!


