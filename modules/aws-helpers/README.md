**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/aws-helpers/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# AWS Helpers

This module contains helper scripts that automate common AWS tasks:

* `publish-ami`: This script copies the given AMI to the specified AWS regions and makes it public.

## Installing the helpers

You can install the helpers using the [Gruntwork Installer](https://github.com/gruntwork-io/gruntwork-installer):

```bash
gruntwork-install --module-name "aws-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "v0.0.1"
```

We recommend running this command in the `dependencies` section of `circle.yml`:

```yaml
dependencies:
  override:
    # Install the Gruntwork Installer
    - curl -Ls https://raw.githubusercontent.com/gruntwork-io/gruntwork-installer/master/bootstrap-gruntwork-installer.sh | bash /dev/stdin --version v0.0.16

    # Use the Gruntwork Installer to install the gruntwork-module-circleci-helpers module
    - gruntwork-install --module-name "aws-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "v0.0.1"
```