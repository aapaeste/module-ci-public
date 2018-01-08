**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/precommit-hooks/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# Pre-commit hooks

This module defines Git pre-commit hooks intended for use with [pre-commit](http://pre-commit.com/). The currently 
supported hooks are:

* **terraform-fmt**: Automatically run `terraform fmt` on all Terraform code (`*.tf` files).
* **shellcheck**: Run [`shellcheck`](https://www.shellcheck.net/) to lint files that contain a bash [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix))
* **gofmt**: Automatically run `gofmt` on all Goland code (`*.go` files).
* **golint**: Automatically run `golint` on all Golang code (`*.go` files)

## General Usage

In each of your repos that has Terraform code, add a file called `.pre-commit-config.yml` with the following contents:

```yaml
repos:
  - repo: git@github.com:gruntwork-io/module-ci.git
    sha: <VERSION> # Get the latest from: https://github.com/gruntwork-io/module-ci/releases
    hooks:
      - id: terraform-fmt
      - id: shellcheck
      - id: gofmt
      - id: golint
```

Next, have every developer: 

1. Install [pre-commit](http://pre-commit.com/). E.g. `brew install pre-commit`.
1. Run `pre-commit install` in the repo.

That’s it! Now every time you commit a Terraform change (`.tf` file), `terraform fmt` will be executed on it 
automatically! 


## Running Against All Files At Once

#### Example: Formatting all files

If you'd like to format all of your code at once (rather than one file at a time), you can run:

```bash
pre-commit run terraform-fmt --all-files
```



#### Example: Enforcing in CI

If you'd like to enforce all your hooks, you can configure your CI build to fail if the code isn't doesn't pass checks by adding the 
following to your build scripts:

```bash
pip install pre-commit
pre-commit install
pre-commit run --all-files
```

If all the files are formatted, the last command will exit with an exit code of 0. If any of the files are not 
formatted, the last command will exit with a code of 1, causing the build to fail.

Check out https://github.com/gruntwork-io/module-ecs/pull/45 for an example PR that adds the pre-commit hook and 
configures CircleCi to fail the build if the code isn't formatted.
