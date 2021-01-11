# Monitoring pipelines<a name="monitoring"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of AWS CodePipeline\. You should collect monitoring data from all parts of your AWS solution so that you can more easily debug a multi\-point failure, if one occurs\. Before you start monitoring, you should create a monitoring plan that answers the following questions:
+ What are your monitoring goals?
+ Which resources will you monitor?
+ How often will you monitor these resources?
+ Which monitoring tools are available for you to use?
+ Who will perform the monitoring tasks?
+ Who should be notified if something goes wrong?

You can use the following tools to monitor your CodePipeline pipelines and their resources:
+ **EventBridge event bus events** — You can monitor CodePipeline events in EventBridge, which detects changes in your pipeline, stage, or action execution status\. EventBridge routes that data to targets such as AWS Lambda and Amazon Simple Notification Service\. EventBridge events are the same as those that appear in Amazon CloudWatch Events\.
+ **AWS CloudTrail** — Use CloudTrail to capture API calls made by or on behalf of CodePipeline in your AWS account and deliver the log files to an Amazon S3 bucket\. You can choose to have CloudWatch publish Amazon SNS notifications when new log files are delivered so you can take quick action\.
+ **Console and CLI** — You can use the CodePipeline console and CLI to view details about the status of a pipeline or a particular pipeline execution\.

**Topics**
+ [Monitoring CodePipeline events](detect-state-changes-cloudwatch-events.md)
+ [Events placeholder bucket reference](reference-ct-placeholder-buckets.md)
+ [Logging API calls with AWS CloudTrail](monitoring-cloudtrail-logs.md)