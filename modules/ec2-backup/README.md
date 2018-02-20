**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-ci>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-ci/blob/master/modules/ec2-backup/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# EC2 Backup Module

This module can be used to make scheduled backups of an EC2 Instance and its EBS Volumes. Under the hood, this module
uses [package-lambda](https://github.com/gruntwork-io/package-lambda) to deploy a Lambda function that is triggered on 
a scheduled basis by [Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)
and runs [ec2-snapper](https://github.com/josh-padnick/ec2-snapper) to take a snapshot of the EC2 Instance.




## Example code

- Check out the [jenkins example](/examples/jenkins) for working sample code.
- See [vars.tf](vars.tf) for all parameters you can configure on this module.




## Specifying an instance

To specify the instance to backup, you simply provide the instance's name via the `instance_name` parameter. This 
should correspond to a [tag](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html) on your EC2 Instance
with the name `Name`. 




## Configuring the schedule

You can specify how often this lambda function runs using the `backup_job_schedule_expression` parameter. This can
be either a rate expression such as `rate(1 day)` or a cron expression such as `cron(0 20 * * ? *)`. See [Schedule
Expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html) for more information and
examples.



## Triggering alarms if backup fails

Every time the function runs successfully, it will increment a [CloudWatch 
Metric](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html). We've configured a
[CloudWatch alarm](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) to go off
if the metric is not updated on the expected schedule, as that implies the backup has failed to run!

You can specify the metric namespace and name using the `cloudwatch_metric_namespace` and `cloudwatch_metric_name` 
parameters, respectively. You can specify the SNS topic to notify when the alarm goes off using the 
`alarm_sns_topic_arns` parameter. 



## Cleaning up old snapshots

To prevent the number of snapshots from growing infinitely and costing you a lot of money, `ec2-snapper` will 
automatically delete older snapshots. You can specify two parameters to control how many snapshots are kept around:

* `delete_older_than`: Delete all snapshots older than this duration. For example, if you set this parameter to `30d`,
  then snapshots that are more than 30 days old will be deleted. See [Delete AMIs older 
  than](https://github.com/josh-padnick/ec2-snapper#delete-amis-older-than-x-days--y-hours--z-minutes) for more info.

* `require_at_least`: Always keep around at least this many snapshots. This helps avoid deleting too much if you have,
  for example, a misconfiguration of the `delete_older_than` parameter.  
    