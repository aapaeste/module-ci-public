**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/scheduled-lambda-job/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# Scheduled Lambda Job Module

This module makes it easy to run an [AWS Lambda Job](https://aws.amazon.com/lambda/) on a scheduled basis. This is
useful for periodic background jobs, such as taking a daily snapshot of your servers.

## How do you use this module?

* See the [root README](/README.md) for instructions on using Terraform modules.
* See the [scheduled-lambda-job example](/examples/scheduled-lambda-job) folder for sample usage.
* See [vars.tf](./vars.tf) for all the variables you can set on this module.

The general idea is to:

1. Create the Lambda function code in JavaScript (Node.js), Java, or Python (see [Lambda programming
   model](https://docs.aws.amazon.com/lambda/latest/dg/programming-model-v2.html)).
1. Put the lambda function code in a zip file (see [Creating a deployment
   package](https://docs.aws.amazon.com/lambda/latest/dg/deployment-package-v2.html)).
1. Use the scheduled-lambda-job module in Terraform, providing it with the path to your zip file and a rate or cron
   expression to specify when it should be executed (see [schedule expression
   syntax](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html)).

## What is AWS Lambda?

[AWS Lambda](https://aws.amazon.com/lambda/) lets you run code without provisioning or managing servers. You define a
function in JavaScript, Java, or Python, upload the code to Lambda, specify how that function can be triggered (e.g.
via a scheduled event, or an HTTP endpoint, or via an API call), and then AWS Lambda takes care of all the details of
deploying and scaling the infrastructure to run that code.

## How do you add additional security group rules to the scheduled-lambda-job?

By default, the scheduled-lambda-job module configures your lambda function with a security group that allows all
outbound connections, but no inbound connections. The ID of the security group is exported as the output
`security_group_id`, so you can add custom rules using the `aws_security_group_rule` resource. For example, to allow
incoming HTTP requests on port 80:

```hcl
module "my_lambda_job" {
  source = "git::git@github.com:gruntwork-io/module-ci.git//modules/scheduled-lambda-job?ref=v1.0.8"
  # (params omitted)
}

resource "aws_security_group_rule" "allow_all_http_inbound" {
  type = "ingress"
  from_port = 80
  to_port = 80
  protocol = "tcp"
  cidr_blocks = ["0.0.0.0/0"]
  security_group_id = "${module.my_lambda_job.security_group_id}"
}
```

## How do you add additional IAM policies?

By default, the scheduled-lambda-job module configures your lambda function with an IAM role that allows it to create
Network Interfaces, write logs to CloudWatch Logs, and be triggered by CloudWatch Events. The ID of the IAM role is
exported as the output `iam_role_id` and the ID of the lambda function is exported as the output
`scheduled_lambda_job_arn`, so you can add custom rules using either the `iam_policy_attachment` or
`aws_lambda_permission` resources, respectively. For example, to allow your lambda job to be triggered by SNS:

```hcl
module "my_lambda_job" {
  source = "git::git@github.com:gruntwork-io/module-ci.git//modules/scheduled-lambda-job?ref=v1.0.8"
  # (params omitted)
}

resource "aws_lambda_permission" "with_sns" {
  statement_id = "AllowExecutionFromSNS"
  action = "lambda:InvokeFunction"
  function_name = "${module.my_lambda_job.scheduled_lambda_job_arn}"
  principal = "sns.amazonaws.com"
  source_arn = "${aws_sns_topic.default.arn}"
}
```