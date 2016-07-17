**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/docs-generator/README.md>
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# Docs Generator

This folder contains a Go app that can be used to generate public documentation from a private GitHub repo. It loops
over all the files in a given input folder (typically a private GitHub repo), and copies each documentation file (e.g.
markdown files such as this Readme) mostly unchanged to an output folder (typically a public GitHub repo). It also
copies each non-documentation file (e.g. source files) to the output folder, but it replaces their contents with
placeholder text.

For an example of the output of this process, check out the public GitHub repo
[module-ci-public](https://github.com/gruntwork-io/module-ci-public).

## Motivation

At Gruntwork, we have developed a number of infrastructure "packages" or "modules" that are thoroughly tested and
documented. Each of these modules is in a private GitHub repo with a name of the format module-XXX, such as
[module-vpc](https://github.com/gruntwork-io/module-vpc-public), [module-ecs](https://github.com/gruntwork-io/module-ecs-public), and
[module-aws-monitoring](https://github.com/gruntwork-io/module-aws-monitoring-public). We want to make the documentation from
these repos available online publicly so that:

1. The documentation gets indexed by Google.
1. Existing customers find the documentation while searching online.
1. Potential new customers find the documentation while searching online.
1. We can link to the documentation from [gruntwork.io](http://www.gruntwork.io/) to make it clearer to potential
   customers exactly what we're offering.

The question is, how can you publish the documentation publicly, but not the actual source code?

This Docs Generator project is the answer. It allows us to add a CI job to each module-XXX repo that extracts the
documentation from the repo and puts it into a module-XXX-public repo.

## Install

The Docs Generator can be installed using the [Gruntwork
Installer](https://github.com/gruntwork-io/gruntwork-installer):

```
gruntwork-install --binary-name 'docs-generator' --repo 'https://github.com/gruntwork-io/module-ci' --tag '0.0.1'
```

Alternatively, you can download the Docs Generator binary for your OS from the [Releases
Page](https://github.com/gruntwork-io/module-ci-public/releases).

## Usage

#### Command-line options

The `docs-generator` binary supports the following options:

* `input-path PATH` (required): Generate documentation from the files and folders in PATH.
* `output-path PATH` (required): Write the output to PATH.
* `github-org NAME` (required): The name of the GitHub organization. E.g. The 'foo' in github.com/foo/bar. Used to
  update URLs.
* `github-repo-name NAME` (required): The name of the GitHub repo. E.g. The 'bar' in github.com/foo/bar. Used to update
  URLs.
* `doc-pattern PATTERN` (optional): Copy files that match PATTERN to the output path, unchanged. May be specified more
  than once.
* `exclude-pattern PATTERN` (optional): A PATTERN to exclude while copying to the output path. May be specified more
  than once.
* `help,` (optional): Show the help text and exit.
* `version,` (optional): Print the version and exit.

Run `docs-generator --help` to see the full docs, including all teh default values.

#### Examples

Generate documentation from the folder `/home/ubuntu/module-vpc` into the folder `/home/ubuntu/module-vpc-public` for
the GitHub repo [module-vpc](https://github.com/gruntwork-io/module-vpc-public):

```
docs-generator --input-path /home/ubuntu/module-vpc --output-path /home/ubuntu-module-vpc-public --github-org gruntwork-io --github-repo-name module-vpc
```

Generate documentation from the folder `/home/ubuntu/module-vpc` into the folder `/home/ubuntu/module-vpc-public` for
the GitHub repo [module-vpc](https://github.com/gruntwork-io/module-vpc-public), including only Markdown files (`*.md` as
documentation) and excluding the `vendor` folder:

```
docs-generator --input-path /home/ubuntu/module-vpc --output-path /home/ubuntu-module-vpc-public --github-org gruntwork-io --github-repo-name module-vpc --doc-pattern "*.md" --exclude-pattern "vendor" --exclude-pattern "vendor/*"
```

Note that you need to quote the `--doc-pattern` and `--exclude-pattern` parameters so that bash does not expand them
immediately.

## Developing

To run the Docs Generator locally:

```
go run `ls *.go | grep -v _test.go`
```

To run the Docs Generator tests:

```
go test -v -parallel 128
```