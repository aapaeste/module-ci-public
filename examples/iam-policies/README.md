**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/examples/iam-policies/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# IAM Policies Example

This folder shows an example of how to create a set of IAM Policies for use with a CI system and attach them to an IAM
User. In a real-world setting, you may wish to use the `iam-policies` modules to create the IAM Policies, but manually 
create a new IAM User and manually attach the desired IAM Policies

## How do you run this example?

To apply the Terraform templates:

1. Install [Terraform](https://www.terraform.io/).
1. Open `vars.tf`, set the environment variables specified at the top of the file, and fill in any other variables that
   don't have a default.
1. Run `terraform get`.
1. Run `terraform plan`.
1. If the plan looks good, run `terraform apply`.
