**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/iam-policies/ecs-service-deployment/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# IAM Policy for ECS Service Deployment
 
This module creates an IAM Policy that contains the minimum permissions necessary to deploy a new Docker image to an [ECS
Service](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html). It does not permit creating an 
ECS Service from scratch, only deploying a new Docker image. 

### What practical actions does this IAM Policy enable?

This IAM Policy will enable its bearer to deploy a new [ECS Task Definition](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_defintions.html) 
to an _existing_ ECS Service. This is equivalent to deploying a new Docker Image to an ECS Service. 

The IAM Policy should be attached to any IAM User, IAM Group, or IAM Role to which you want to grant these permissions.

## How do you use this module?

* See the [root README](/README.md) for instructions on using Terraform modules.
* See the [iam-policies example](/examples/iam-policies) folder for sample usage.
* See [vars.tf](./vars.tf) for all the variables you can set on this module.

The general idea is to:

1. Use Terraform to create this IAM Policy plus any others that you need.
2. Manually create the IAM User for your CI job and attach the newly created IAM Policies. 