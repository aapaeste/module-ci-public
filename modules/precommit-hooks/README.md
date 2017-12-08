**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/precommit-hooks/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# Pre-commit hooks

This module defines Git pre-commit hooks intended for use with [pre-commit](http://pre-commit.com/). The currently 
supported hooks are:

* **terraform-fmt**: Automatically run `terraform fmt` on all Terraform code (`*.tf` files).




## Usage

Add the following to `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: git@github.com:gruntwork-io/module-ci.git
    sha: <VERSION> # fill in the latest version from the releases page here
    hooks:
      - id: terraform-fmt
```

Run `pre-commit install`. Now, every time you commit a Terraform change (`.tf` file), it'll automatically be formatted.

Note: the very first time around, you may want to run `pre-commit run terraform-fmt` to format all of your code.  

 