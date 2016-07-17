**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/terraform-helpers/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# Terraform Helpers

This folder contains several helper scripts for working with [Terraform](https://www.terraform.io/). The scripts are:

1. `terraform-update-variable`: This script can automatically update a variable in a `terraform.tfvars` file and
   commit and push the changes to Git. It's common practice to store the version number of the currently deployed app
   in a variable and this script comes in handy for updating those variables automatically, such as in a CI job after
   building a new AMI or Docker image.
2. `terraform-deploy`: This script automates all the steps it takes to deploy Terraform templates. This includes
   installing Terraform itself, configuring remote state storage, downloading Terraform modules, generating a plan,
   and running `apply`. If you combine this script with the `terraform-update-variable` script, you can easily
   deploy a new version of an app in a completely automated fashion, such as from a CI job.

## Using the helper scripts in your code

You can install these scripts using the [Gruntwork Installer](https://github.com/gruntwork-io/gruntwork-installer):

```bash
gruntwork-install --module-name "terraform-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "0.0.1"
```

See the examples in the next section for how to use them.

## Examples

The examples below should give you an idea of how to use the scripts. Run the scripts above with the `--help` flag to
see full documentation.

Imagine you have a set of Terraform templates in the `templates` folder to deploy an app called `rails-app`. You create
Amazon Machine Images (AMIs) for each new version of the app using [Packer](https://www.packer.io/) and the currently
deployed version of the app is defined in a variable called `rails_app_version`, set to `ami-12345` in
`templates/terraform.tfvars`.

If you just build a new version of the AMI, `ami-67890`, you could deploy it automatically, perhaps in a CI job,
as follows:

```bash
cd templates
terraform-update-variable --name rails_app_version --value ami-67890
terraform-deploy --remote-state-bucket my_s3_bucket --aws-region us-east-1
```

Running the commands above would:

1. Set the `rails_app_version` variable to the ID of the new AMI, `ami-67890` in `terraform.tfvars`.
1. `commit` and `push` the changes to `terraform.tfvars` to Git.
1. Deploy the changes using Terraform.


