# iam-policies

## Example main.tf

    provider "aws" {
      profile = var.profile
      region  = var.region
      version = "3.12.0"
    }

    module "s3" {
    	source     = "git@github.com:terraform-cloud-aws-modules/s3.git"
    	bucketname = "my-awesome-bucket-name"
    	tags = var.tags
    	rootlevelfolder = ["foo", "bar", "baz"]
    	sublevelfolder = ["one", "two", "three", "four", "five", "six"]
    }

    module "s3_read_policy" {
      source      = "git@github.com:terraform-cloud-aws-modules/iam-policies.git"
      description = "Provides read-only (download) access for s3 bucket"
      name        = "s3-ro-access"
      path        = "/"
      statements = [
        {
          sid = "S3AccessRO"
          actions = [
            "s3:GetObject",
            "s3:GetObjectVersion"
          ]
          effect    = "Allow"
          resources = flatten([
          module.s3.this_s3_bucket_arn,
          ])
        }
      ]
    }

