**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/kubernetes-circleci-helpers/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# Kubernetes CircleCI Helpers

This module contains helper scripts that can setup Kubernetes tools for use in a CircleCI job, including:

* `setup-minikube`: This script will install and setup [`minikube`](https://kubernetes.io/docs/setup/minikube/) in a way
  that is compatible with CircleCI. In addition, this script will install
  [`kubectl`](https://kubernetes.io/docs/tasks/tools/install-kubectl/), setup to talk to the `minikube` cluster. Note
  that this MUST be run on a job that is using the machine executor with the ubuntu 16.04 image
  (`ubuntu-1604:201903-01`).


## Installing the helpers

You can install the helpers using the [Gruntwork Installer](https://github.com/gruntwork-io/gruntwork-installer):

```bash
gruntwork-install --module-name "kubernetes-circleci-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "0.0.1"
```

We recommend running this command in one of the jobs for CircleCI:

```yaml
job:
  setup:
    # Note: Since minikube uses Docker, we need to run on the machine executor to avoid complications with
    # Docker-in-Docker. Also, minikube requires systemd which is only introduced starting 16.04
    machine:
      enabled: true
      image: "ubuntu-1604:201903-01"
    steps:
      - run:
        command: |
          # Install the Gruntwork Installer
          curl -Ls https://raw.githubusercontent.com/gruntwork-io/gruntwork-installer/master/bootstrap-gruntwork-installer.sh | bash /dev/stdin --version 0.0.22

          # Use the Gruntwork Installer to install the kubernetes-circleci-helpers module
          gruntwork-install --module-name "kubernetes-circleci-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "0.13.12"
```


## Using the setup-minikube helper

The `setup-minikube` script will do the following:

1. Mark the root directory as rshared so that it is available to `minikube` (see
   https://github.com/kubernetes/kubernetes/issues/61058 for more info).
1. Install `kubectl` into `/usr/local/bin`. You can use the `--k8s-version` flag to customize the version that is
   installed. Note that the installed Kubernetes version will also reflect this, so that both `kubectl` and the
   available Kubernetes APIs are in sync.
1. Install `minikube` into `/usr/local/bin`. You can use the `--minikube-version` flag to customize the version that is
   installed.
1. Start `minikube` and wait for it to come up. You can use the `--k8s-version`
   flag to configure which version of Kubernetes to run.
1. Disable the `dashboard` addon, while enabling the `ingress` addon so that it is available, and ensure the `ingress`
   addon gets deployed correctly.

A few things to note about the way `minikube` is installed:

- Disable VM drivers so that it runs directly.
- RBAC is turned on.
- The nginx ingress controller is turned on with SSL disabled.
- The default `ServiceAccount` in the `kube-system` namespace inherits cluster admin status.

You can call this as a step in your job:

```yaml
job:
  setup:
    steps:
      - run:
        command: |
          # Install the Gruntwork Installer
          curl -Ls https://raw.githubusercontent.com/gruntwork-io/gruntwork-installer/master/bootstrap-gruntwork-installer.sh | bash /dev/stdin --version 0.0.9

          # Use the Gruntwork Installer to install the kubernetes-circleci-helpers module
          gruntwork-install --module-name "kubernetes-circleci-helpers" --repo "https://github.com/gruntwork-io/module-ci" --tag "0.0.5"

      - run:
        command: setup-minikube
```
