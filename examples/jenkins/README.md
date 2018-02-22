**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/examples/jenkins/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# Jenkins example

This folder shows an example of how to use the [jenkins-server](/modules/jenkins-server), 
[install-jenkins](/modules/install-jenkins), and [ec2-backup](/modules/ec2-backup) modules to deploy a [Jenkins CI 
server](https://jenkins.io) in AWS. The server will run on top of an Auto Scaling Group (for high availability), with
an ALB and domain name, an EBS volume to persist data between redeploys, and a Lambda function that will take automatic 
nightly snapshots of the EBS volume for backup.




## Quick start

To run this example, you need to:

1. Build a Jenkins AMI
1. Deploy the Jenkins AMI with Terraform
1. Login to Jenkins


### Build a Jenkins AMI

We create an AMI that has Jenkins and its dependencies installed using the [Packer](https://www.packer.io/) template
under `packer/jenkins.json`. To build the AMI:

1. Set up your [AWS credentials as environment variables](https://www.packer.io/docs/builders/amazon.html).
1. Set the `GITHUB_OAUTH_TOKEN` environment variable to a valid GitHub auth token with "repo" access. You can generate
   one here: https://github.com/settings/tokens
1. In `packer/jenkins.json`, configure the AWS region you want to use via the `aws_region` variable.   
1. To run Jenkins on Ubuntu: `packer build -only=ubuntu-ami packer/jenkins.json`. 
1. To run Jenkins on CentOS: `packer build -only=centos-ami packer/jenkins.json`. 

Note down the ID of the new AMI, as you'll need it in the next section.   


### Deploy the Jenkins AMI with Terraform

We have created [Terraform](https://www.terraform.io/) configurations to deploy the AMI from the previous step in AWS. 
To deploy the Terraform code:

1. Open `vars.tf`, set the environment variables specified at the top of the file, and fill in any other variables that
   don't have a default: 
    1. `ami_id`: Set this to the ID of the AMI you built in the previous section. 
    1. `hosted_zone_domain_name`: Set this to a domain name for which you have a [Public Hosted 
        Zone](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/AboutHZWorkingWith.html) in [Route 
        53](https://aws.amazon.com/route53/). You will also need to request a free wildcard TLS certificate for this
        domain name using [AWS Certificate Manager](https://aws.amazon.com/certificate-manager/): e.g., if the domain
        name is `foo.com`, you'll need to request a free certificate for `*.foo.com`. Getting a cert is a very easy
        process that takes only a couple minutes: [instructions](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request.html).
    1. `key_pair_name`: The [EC2 Key Pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) to
       associate with the Jenkins server. You'll need access to this EC2 Key Pair so you can SSH to the server later.         
1. Run `terraform init`.
1. Run `terraform plan`.
1. If the plan looks good, run `terraform apply`.

When `apply` finishes, it'll output the domain name of your server. Head over to that URL and you'll be prompted to 
login to Jenkins.


### Login to Jenkins

When you first load Jenkins, it will ask you to enter the initial admin password to unlock it. This password is located
on the Jenkins server itself and the initial login page will show you the path (typically, 
`/jenkins/secrets/initialAdminPassword`). You'll need to SSH to the server to get the password.




## Local testing

The Packer template in `packer/jenkins.json` can build not only an AMI, but also a Docker image with Jenkins installed.
You can run this Docker image on your local computer for faster ("unit") testing:

1. Set the `GITHUB_OAUTH_TOKEN` environment variable to a valid GitHub auth token with "repo" access. You can generate
   one here: https://github.com/settings/tokens
1. To run Jenkins on Ubuntu:   
    1. `packer build -only=ubuntu-docker packer/jenkins.json`
    1. `docker-compose run --service-ports jenkins_ubuntu`
1. To run Jenkins on CentOS:    
    1. `packer build -only=centos-docker packer/jenkins.json` 
    1. `docker-compose run --service-ports jenkins_centos`

The `docker-compose.yml` in this folder will run the scripts in the `mock` folder to mock out a few dependencies and 
boot up Jenkins locally on port 8080. After a few seconds, you should be able to test it by going to 
http://localhost:8080.