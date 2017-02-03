**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/iam-policies/terragrunt/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# IAM Policy for Enabling Terragrunt
 
This module creates an IAM Policy that contains the minimum permissions necessary to work with [Terragrunt](https://github.com/gruntwork-io/terragrunt).

### What practical actions does this IAM Policy enable?

Terragrunt is a thin wrapper for Terraform, and some of its features use unique AWS permissions. For example, Terragrunt
provides locking using DynamoDB, which requires DynamoDB permissions. Terragrunt also requests the identity of the 
current IAM User, which requires another permission. This IAM Policy covers all the permissions needed by Terragrunt.

## How do you use this module?

* See the [root README](/README.md) for instructions on using Terraform modules.
* See the [iam-policies example](/examples/iam-policies) folder for sample usage.
* See [vars.tf](./vars.tf) for all the variables you can set on this module.

The general idea is to:

1. Use Terraform to create this IAM Policy plus any others that you need.
2. Manually create the IAM User for your CI job and attach the newly created IAM Policies. 