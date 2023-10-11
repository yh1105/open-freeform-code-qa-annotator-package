
# Post \#69633803 [Link](https://stackoverflow.com/questions/69633803/)

## Terraform Import of map resources

**Vote**: 2 (631/702) **Views**: 1344 (635/702) 

**Internal ID** \#1-3-297

Created at 2021-10-19 15:35:58

Tags: `amazon-web-services` `dictionary` `amazon-s3` `terraform` `terraform-provider-aws`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I've got a map variable that identifies existing s3 buckets:
```
resource "aws_s3_bucket" "bucket" {
  for_each = var.s3_replication
  bucket   = each.value.source
  #other configuration
}

variable "s3_replication" {
  description = "Map of buckets to replicate"
  type        = map
  default = {
    logs = {
      source = "logs_bucket",
      destination = "central_logs_bucket"
    },
    security = {
      source = "cloudtrail_bucket",
      destination = "central_security_bucket"
    }
  }
}
```

Since these buckets already exist, I am trying to import them and then apply the a configuration to them to update the resources. Unfortunately, I am not able to figure out how to do a terraform import on these. I've tried:
```
terraform import aws_s3_bucket.bucket["logs"] logs_bucket
terraform import aws_s3_bucket.bucket[logs] logs_bucket
terraform import aws_s3_bucket.bucket[0] logs_bucket
terraform import aws_s3_bucket.bucket[0].source logs_bucket
terraform import aws_s3_bucket.bucket[0[source]] logs_bucket
```

All failing with a different error. Any idea on how to import existing resources listed on a map?


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2021-10-19 15:45:34

------------

The `terraform import` subcommand relies on strings in the map key within resource namespaces that are first class expression, and this causes issues with shell interpreters where the resource is not a first class expression because they are not the Terraform DSL. You can work around this by casting the entire resource name as a literal string:
```
terraform import 'aws_s3_bucket.bucket["logs"]' logs_bucket
```

and this will resolve your issue.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-10-19 16:03:52

------------

Before doing an `import` I recommend doing a terraform `plan`. The output of the plan would look like this:
```
Terraform will perform the following actions:

  # aws_s3_bucket.bucket["logs"] will be created
  + resource "aws_s3_bucket" "bucket" {
      + acceleration_status         = (known after apply)
      + acl                         = "private"
      + arn                         = (known after apply)
      + bucket                      = "logs_bucket"
      + bucket_domain_name          = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags_all                    = (known after apply)
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)

      + versioning {
          + enabled    = (known after apply)
          + mfa_delete = (known after apply)
        }
    }

  # aws_s3_bucket.bucket["security"] will be created
  + resource "aws_s3_bucket" "bucket" {
      + acceleration_status         = (known after apply)
      + acl                         = "private"
      + arn                         = (known after apply)
      + bucket                      = "cloudtrail_bucket"
      + bucket_domain_name          = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags_all                    = (known after apply)
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)

      + versioning {
          + enabled    = (known after apply)
          + mfa_delete = (known after apply)
        }
    }

Plan: 2 to add, 0 to change, 0 to destroy.
```

Having this plan we can see that the resources which will be created can be references as `aws_s3_bucket.bucket["logs"]` and `aws_s3_bucket.bucket["security"]`. Instead of doing an `apply` we can import them as follows:
Bash:
```
terraform import 'aws_s3_bucket.bucket["security"]' cloudtrail-bucket
terraform import 'aws_s3_bucket.bucket["logs"]' logs-bucket
```

Windows CMD:
```
terraform import 'aws_s3_bucket.bucket[\"security\"]' cloudtrail-bucket
terraform import 'aws_s3_bucket.bucket[\"logs\"]' logs-bucket
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-10-19 17:01:58

------------

The syntax from the answers posted are correct. My code was just bugging out because it couldn't locate the varsfile. I needed to add -var-file={{ path to tfvars }}. So the final syntax looks like:
```
terraform import -var-file={{ path to tfvars}} 'aws_s3_bucket.bucket["logs"]' logs_bucket
```



------------
    
    