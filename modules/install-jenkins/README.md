**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/install-jenkins/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# Install Jenkins Module

This module contains two scripts for working with [Jenkins CI server](https://jenkins.io):
 
1. `install.sh`: This script will install Jenkins on a Linux server. Currently, Ubuntu and CentOS are supported.
   This script also installs the `run-jenkins` script.
1. `run-jenkins`: This script can be used to configure and run Jenkins. You will typically run this script while your
   server is booting.




## Example code

- Check out the [jenkins example](/examples/jenkins) for working sample code.
- See [install.sh](install.sh) and [run-jenkins.sh](run-jenkins) for all options you can pass to these scripts.




## Install Jenkins

The easiest way to install and run these scripts is to use the [Gruntwork 
Installer](https://github.com/gruntwork-io/gruntwork-installer) (make sure to replace `VERSION` below with the latest
version from the [releases page](https://github.com/gruntwork-io/module-ci-public/releases)):

```bash
gruntwork-install \
  --module-name 'install-jenkins' \
  --repo 'https://github.com/gruntwork-io/module-ci' \
  --tag '<VERSION>' \
  --version 2.164.3
```

The command above will copy `install.sh` to your server, run it, install Jenkins 2.164.3, and copy the `run-jenkins`
script into `/usr/local/bin`. We recommend running this command in a [Packer template](https://www.packer.io/) so you
can create an AMI with Jenkins installed. Check out the [jenkins example](/examples/jenkins) for an example of such a
Packer template.




## Run Jenkins 

Once you have an AMI with Jenkins installed, you need to deploy it on an EC2 Instance in AWS. The easiest way to do 
this is with the [jenkins-server module](/modules/jenkins-server). When the EC2 Instance is booting, you should 
typically do two things in [User Data](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html):

1. Mount an EBS volume for the Jenkins home directory. You want to use an EBS volume so that your Jenkins data is 
   persisted even if the EC2 Instance is replaced (e.g., after a crash or upgrade). The `mount-ebs-volume` script in the 
   [persistent-ebs-volume module](https://github.com/gruntwork-io/module-server-public/tree/master/modules/persistent-ebs-volume)
   makes it easy to attach and mount a volume.

1. Execute the `run-jenkins` script to start Jenkins, set its home directory to the mount point for the EBS volume,
   and configure it to use a certain amount of memory: 

    ```bash
    run-jenkins \
      --memory "1g" \
      --jenkins-home "/jenkins"
    ```
    
Check out the [jenkins example](/examples/jenkins) for an example of such a User Data script.    
