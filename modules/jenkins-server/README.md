**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/jenkins-server/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# Jenkins server

This module can be used to deploy [Jenkins CI server](https://jenkins.io) in AWS. It creates the following resources:

- An ASG to run Jenkins and automatically redeploy it if it crashes
- An EBS volume for the Jenkins data directory that persists between redeploys
- An ALB to route traffic to Jenkins
- A Route 53 DNS A record for Jenkins pointing at the ALB

Under the hood, we are using the [server-group module](https://github.com/gruntwork-io/module-asg-public/tree/master/modules/server-group)
to run an ASG for Jenkins that can correctly reattach an EBS volume.



## Example code

- Check out the [jenkins example](/examples/jenkins) for working sample code.
- See [vars.tf](vars.tf) for all parameters you can configure on this module.



## Jenkins AMI

See the [install-jenkins module](/modules/install-jenkins) for a way to create an AMI with Jenkins installed and a
script you can run in User Data to start Jenkins while the server is booting.




## Backing up Jenkins

See the [ec2-backup module](/modules/ec2-backup) for an automatic way to take scheduled backups of Jenkins and its EBS 
volume.




## IAM permissions

This module assigns an IAM role to the Jenkins server and exports the ID of the IAM role. To give your Jenkins server
IAM permissions—e.g., so you can use the server to automatically deploy changes into your AWS account—you can attach 
permissions to the IAM role using the [aws_iam_role_policy 
resource](https://www.terraform.io/docs/providers/aws/r/iam_role_policy.html):

```hcl
module "jenkins" {
  source = "git::git@github.com:gruntwork-io/module-ci.git//modules/jenkins-server?ref=v1.0.8"
  
  # ... (params ommitted) ...
}

resource "aws_iam_role_policy" "example" {
  role   = "${module.jenkins.jenkins_iam_role_id}"
  policy = "${data.aws_iam_policy_document.example.json}"
}

data "aws_iam_policy_document" "example" {
  statement {
    effect    = "Allow"
    actions   = ["s3:*"]
    resources = "*"
  }
}
```