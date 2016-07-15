# Gruntwork Module CircleCI Helpers

This module contains helper scripts used in CircleCI jobs of Gruntwork Modules, including:

* `configure-environment-for-gruntwork-module`: This script is meant to be run in a Circle CI job to configure the
  build environment for a typical Gruntwork module, including creating symlinks so the GOPATH works in CircleCI,
  installing Terraform, Packer, and Glide, and using Glide to install Go dependencies.
* `run-go-tests`: This script is meant to be run in a CircleCI job to run automated tests written in Go with the proper
  settings for the test path (so it works with GOPATH), parallelism, and timeouts.
* `upload-github-release-assets`: This script can be used to automatically upload assets to a [GitHub
  Release](https://help.github.com/articles/about-releases/). For example, if you need to build new binaries of some
  app each time you release, you can use this script to upload those binaries to GitHub.
* `build-go-binaries`: This script is meant to be run in a CircleCI job to build binaries for an app written in Go. It
  uses gox under the hood and provides reasonable defaults for parallelism, build flags, file naming, and
  os/architecture options.
* `git-add-commit-push`: This script is meant to be run in a CI job to add, commit, and push a given set of files to
  Git, handling common tasks like configuring Git in a CI job, checking for common error cases, and ensuring the commit
  doesn't trigger another CI job.

## Installing the helpers

You can install the helpers using the [Gruntwork Installer](https://github.com/gruntwork-io/gruntwork-installer):

```bash
gruntwork-install --module-name "gruntwork-module-circleci-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "0.0.1"
```

We recommend running this command in the `dependencies` section of `circle.yml`:

```yaml
dependencies:
  override:
    # Install the Gruntwork Installer
    - curl -Ls https://raw.githubusercontent.com/gruntwork-io/gruntwork-installer/master/bootstrap-gruntwork-installer.sh | bash /dev/stdin --version 0.0.9

    # Use the Gruntwork Installer to install the gruntwork-module-circleci-helpers module
    - gruntwork-install --module-name "gruntwork-module-circleci-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "0.0.5"
```

## Using the configure-environment-for-gruntwork-module helper

The `configure-environment-for-gruntwork-module` script will do the following:

1. Create symlinks so GOPATH works correctly, as explained in [this
   post](https://robots.thoughtbot.com/configure-circleci-for-go).
1. Install Terraform, Packer, and Glide. If you wish to customize the version of each app that gets installed, you can
   use the `--terraform-version`, `--packer-version`, and `--glide-version` flags, respectively.
1. Run `glide install` in folders you specify via the `--go-src-path` parameter.

We recommend running this helper in the `dependencies` section of `circle.yml`:

```yaml
machine:
  # Add the folders where configure-environment-for-gruntwork-module installs binaries to the PATH
  environment:
    PATH: $PATH:$HOME/terraform:$HOME/packer:$HOME/glide/linux-amd64

dependencies:
  override:
    # Install the Gruntwork Installer
    - curl -Ls https://raw.githubusercontent.com/gruntwork-io/gruntwork-installer/master/bootstrap-gruntwork-installer.sh | bash /dev/stdin --version 0.0.9

    # Use the Gruntwork Installer to install the gruntwork-module-circleci-helpers module
    - gruntwork-install --module-name "gruntwork-module-circleci-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "0.0.1"

    # Configure the CircleCI environment using the gruntwork-module-circleci-helpers module
    - configure-environment-for-gruntwork-module --go-src-path test --go-src-path modules/my-go-app

  # Cache the folders where configure-environment-for-gruntwork-module installs binaries
  cache_directories:
    - ~/terraform
    - ~/packer
    - ~/glide
```

Three key things to note:

1. While the `configure-environment-for-gruntwork-module` script can install Terraform, Packer, and Glide, it cannot
   automatically add them to your `PATH`. Therefore, you have to do it yourself in the `machine` section of
   `circle.yml` as shown above.
1. Use the `--go-src-path` option to tell `configure-environment-for-gruntwork-module` where your Go source files are
   located. You can specify the flag more than once if you have more than one Go app, as shown in the example above.
   The `configure-environment-for-gruntwork-module` script will run `glide install` in each of those folders to
   install their dependencies.
1. To avoid reinstalling Terraform, Packer, and Glide over and over again, use `cache_directories` as shown above.

## Using the run-go-tests helper

The `run-go-tests` can run automated tests written in Go with the proper settings for the test path (so it works with
GOPATH), parallelism, and timeouts.

We recommend running this helper in the `test` section of `circle.yml`:

```yaml

test:
  override:
    - run-go-tests --path test
```

A few things to note:

1. Use the `--path` parameter to tell `run-go-tests` where your tests are located. The path should be relative to your
   project root. The default is the present working directory, which in Circle CI, is the root of your Git checkout.
1. Use the `--packages` parameter to specify which packages to test. The default is the output of running
   `glide novendor` in `--path`.
1. The `run-go-tests` script will add a `-short` parameter to the test command when not running on the master branch.
   Your tests can read in the value of this flag [as shown here](https://golang.org/pkg/testing/) to determine if a
   long-running test should be executed or not.

## Using the upload-github-release-assets helper

To use this helper, you must first create a [GitHub access
token](https://help.github.com/articles/creating-an-access-token-for-command-line-use/) and set it as the environment
variable `GITHUB_OAUTH_TOKEN`.

After that, in CircleCI, you can run the `upload-github-release-assets` script as follows:

```
upload-github-release-assets /assets-to-upload/file1 /assets-to-upload/file2 /assets-to-upload/file3
```

You can also use bash parameter expansion:

```
upload-github-release-assets /assets-to-upload/*
```

That is, you just pass in a list of assets to upload, and the script will automatically figure out the proper GitHub
repo and tag to use based on [CircleCI environment variables](https://circleci.com/docs/environment-variables/). If
you're not using CircleCI or want to customize some of the settings, you may use the following options:

* `--github-repo-owner`: The user or organization that owns the repo (e.g. gruntwork-io).
* `--github-repo-name`: The name of the repo (e.g. module-ci).
* `--git-tag`: The git tag for which assets should be pushed (e.g. v0.0.3).

## Using the build-go-binaries helper

This script accepts the following options:

* `--src-path`: The path, relative to the project root, where Go source files can be found. Default: current working
  directory.
* `--dest-path`: The path, relative to the project root, in which the binaries should be created.
* `--app-name`: What to name the Go binaries.
* `--os`: The OS's to build binaries for.
* `--arch`: The architectures to build binaries for.

Example:

```
build-go-binaries --app-name myapp --src-path foo/bar --dest-path foo/bin
```

This will create 32 bit and 64 bit binaries for Linux, OS X, and Windows in the `foo/bin` folder with the name
`myapp_<os>_<arch>` (e.g. `myapp_linux_amd64`, `myapp_windows_386.exe`, etc).

If you run `build-go-binaries` with no options, it will build the source code in the current working directory into a
`bin` folder and pick reasonable defaults for all the other values using [CircleCI environment
variables](https://circleci.com/docs/environment-variables/).

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

The `git-add-commit-push` script relies on SSH auth, so make sure the SSH key you use in your CI job has push
privileges for your repo.