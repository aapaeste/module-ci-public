**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/git-helpers/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# Git Helpers

This module contains helper scripts that automate common git tasks:

* `git-rebase`: This script can be used to merge git branch A into branch B using git rebase.
* `git-add-commit-push`: This script is meant to be run in a CI job to add, commit, and push a given set of files to
  Git, handling common tasks like configuring Git in a CI job, checking for common error cases, and ensuring the commit
  doesn't trigger another CI job.

## Installing the helpers

You can install the helpers using the [Gruntwork Installer](https://github.com/gruntwork-io/gruntwork-installer)
(make sure to replace `<VERSION>` below with the latest version from the [releases
page](https://github.com/gruntwork-io/module-ci-public/releases)):

```bash
gruntwork-install --module-name "git-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "<VERSION>"
```

We recommend running this command in the `dependencies` section of `circle.yml`:

```yaml
dependencies:
  override:
    # Install the Gruntwork Installer
    - curl -Ls https://raw.githubusercontent.com/gruntwork-io/gruntwork-installer/master/bootstrap-gruntwork-installer.sh | bash /dev/stdin --version 0.0.9

    # Use the Gruntwork Installer to install the gruntwork-module-circleci-helpers module
    - gruntwork-install --module-name "git-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "0.0.5"
```

## Using the git-add-commit-push helper

The most common use-case for this script is to automatically commit generated files (e.g. generated code, auto-filled
version number, aut-generated docs) to Git at the end of a CI job. Here is an example `circle.yml` file that shows the
usage:

```yaml
deployment:
  release:
    tag: /v.*/
    commands:
      # Generate a new file
      - auto-generate-some-code --output generated-file.txt
      # Commit the file to Git
      - git-add-commit-push --path generated-file.txt --message "Automatically regenerate generated-file.txt"
```

The main options to pass to `git-add-commit-push` are:

* `--path`: The path to add, commit, and push to Git. Required. May be specified more than once.
* `--message`: The commit message. Required.
