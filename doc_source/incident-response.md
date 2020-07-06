# Logging and monitoring in CodePipeline<a name="incident-response"></a>

You can use logging features in AWS to determine the actions users have taken in your account and the resources that were used\. The log files show:
+ The time and date of actions\.
+ The source IP address for an action\.
+ Which actions failed due to inadequate permissions\.

Logging features are available in the following AWS services:
+ AWS CloudTrail can be used to log AWS API calls and related events made by or on behalf of an AWS account\. For more information, see [Logging CodePipeline API calls with AWS CloudTrail](monitoring-cloudtrail-logs.md)\.
+ Amazon CloudWatch Events can be used to monitor your AWS Cloud resources and the applications you run on AWS\. You can create alerts in Amazon CloudWatch Events based on metrics that you define\. For more information, see [Detect and react to changes in pipeline state with Amazon CloudWatch Events](detect-state-changes-cloudwatch-events.md)\.