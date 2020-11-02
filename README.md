# iam-policies

## Example of an complete use case
The following code will provide you're AWS environment with an S3 Bucket ("**my-awesome-bucket-name**"), containing the following folder structure:

    |_ foo
    	|_ one
    	|_ two
    	|_ three
    	|_ four
    	|_ five
    	|_ six
    |_ bar
    	|_ one
    	|_ two
    	|_ three
    	|_ four
    	|_ five
    	|_ six
    |_ baz
    	|_ one
    	|_ two
    	|_ three
    	|_ four
    	|_ five
    	|_ six

A IAM Policy will be generated, providing read-only (download) access to this Bucket ("**my-awesome-bucket-name**").
To achieve this, please take a look in the following code:

### Example main.tf

    provider "aws" {
      profile         = var.profile
      region          = var.region
      version         = "3.12.0"
    }

    module "s3" {
      source          = "git@github.com:terraform-cloud-aws-modules/s3.git"
      bucketname      = "my-awesome-bucket-name"
      rootlevelfolder = ["foo", "bar", "baz"]
      sublevelfolder  = ["one", "two", "three", "four", "five", "six"]
      tags            = var.tags
    }

    module "s3_read_policy" {
      source          = "git@github.com:terraform-cloud-aws-modules/iam-policies.git"
      description     = "Provides read-only (download) access for s3 bucket"
      name            = "s3-ro-access"
      path            = "/"
      statements      = [
        {
          sid         = "S3AccessRO"
          actions     = [
            "s3:GetObject",
            "s3:GetObjectVersion"
          ]
          effect      = "Allow"
          resources   = flatten([
          module.s3.this_s3_bucket_arn,
          ])
        }
      ]
    }

### Example variables.tf

    variable "profile" {
      type           = string
      description    = "provider profile"
    }
    
    variable "region" {
      type           = string
      description.   = "provider region"
    }
    
    variable "tags" {
        type         = map
        description  = "Tags used for AWS resources"
    }

### Example outputs.tf

    output "bucket_arn" {
        value        = module.s3.this_s3_bucket_arn
    }
### Example common.tfvars

    tags = {
      environment    = "testing"
      team           = "terraformers"
      project        = "aws1"
    }

