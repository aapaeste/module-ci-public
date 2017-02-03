**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/iam-policies/terraform-remote-state-s3/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# IAM Policy for Enabling Terraform Remote State with S3
 
This module creates an IAM Policy that contains the minimum permissions necessary to use Terraform with [Remote State 
in S3](https://www.terraform.io/docs/state/remote/s3.html).

### What practical actions does this IAM Policy enable?

When using Terraform with Remote State in S3, your AWS account needs certain permissions to read and write the S3
 Bucket. Note that these permissions are separate from any permissions required by [Terragrunt](https://github.com/gruntwork-io/terragrunt), 
 for which there is a separate [terragrunt module](../terragrunt)

## How do you use this module?

* See the [root README](/README.md) for instructions on using Terraform modules.
* See the [iam-policies example](/examples/iam-policies) folder for sample usage.
* See [vars.tf](./vars.tf) for all the variables you can set on this module.

The general idea is to:

1. Use Terraform to create this IAM Policy plus any others that you need.
2. Manually create the IAM User for your CI job and attach the newly created IAM Policies. 