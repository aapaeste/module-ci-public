**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/iam-policies/ecr-docker-push/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# IAM Policy for Pushing a Docker Image to ECR
 
This module creates an IAM Policy that contains the minimum permissions necessary to check if a Docker Image already 
exists in an [Amazon ECR](http://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) repo, and to push a new
Docker Image to Amazon ECR. It does not permit creating an ECR Repo from scratch. 

### What practical actions does this IAM Policy enable?

This IAM Policy will enable its bearer to first check if it's necessary to build a Docker Image by seeing if that Docker
 Image Tag already exists in an Amazon ECR Repo. Second, it enables the Docker Image to be pushed to Amazon ECR.
 
The IAM Policy should be attached to any IAM User, IAM Group, or IAM Role to which you want to grant these permissions.

## How do you use this module?

* See the [root README](/README.md) for instructions on using Terraform modules.
* See the [iam-policies example](/examples/iam-policies) folder for sample usage.
* See [vars.tf](./vars.tf) for all the variables you can set on this module.

The general idea is to:

1. Use Terraform to create this IAM Policy plus any others that you need.
2. Manually create the IAM User for your CI job and attach the newly created IAM Policies. 