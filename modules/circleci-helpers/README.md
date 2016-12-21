**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/circleci-helpers/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# CircleCI Helpers

This module contains helper scripts specially for CircleCI jobs, including:

* `place-repo-in-gopath`: Places the git repo that triggered the CircleCI build in the GOPATH. This is useful because
   some tools (e.g. vendoring tools) require that they be executed in a repo located within the GOPATH.
* `install-go-version`: Installs the given version of Golang.

## Installing the helpers

You can install the helpers using the [Gruntwork Installer](https://github.com/gruntwork-io/gruntwork-installer):

```bash
gruntwork-install --module-name "circleci-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "0.0.1"
```

We recommend running this command in the `dependencies` section of `circle.yml`:

```yaml
dependencies:
  override:
    # Install the Gruntwork Installer
    - curl -Ls https://raw.githubusercontent.com/gruntwork-io/gruntwork-installer/master/bootstrap-gruntwork-installer.sh | bash /dev/stdin --version 0.0.9

    # Use the Gruntwork Installer to install the gruntwork-module-circleci-helpers module
    - gruntwork-install --module-name "circleci-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "0.0.5"
```

## Using the place-repo-in-gopath script

The `place-repo-in-gopath` script will create symlinks so GOPATH works correctly, as explained in [this
   post](https://robots.thoughtbot.com/configure-circleci-for-go).

We recommend running this helper in the `dependencies` section of `circle.yml`:

```yaml
dependencies:
  override:
    # Install the Gruntwork Installer
    - curl -Ls https://raw.githubusercontent.com/gruntwork-io/gruntwork-installer/master/bootstrap-gruntwork-installer.sh | bash /dev/stdin --version 0.0.9

    # Use the Gruntwork Installer to install the gruntwork-module-circleci-helpers module
    - gruntwork-install --module-name "circleci-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "0.0.1"

    # Place the repo in the GOPATH
    - place-repo-in-gopath
```