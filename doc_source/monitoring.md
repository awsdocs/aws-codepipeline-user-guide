# Monitoring Pipelines with AWS CodePipeline<a name="monitoring"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of AWS CodePipeline\. You should collect monitoring data from all parts of your AWS solution so that you can more easily debug a multi\-point failure, if one occurs\. Before you start monitoring, you should create a monitoring plan that answers the following questions:

+ What are your monitoring goals?

+ Which resources will you monitor?

+ How often will you monitor these resources?

+ Which monitoring tools are available for you to use?

+ Who will perform the monitoring tasks?

+ Who should be notified if something goes wrong?

You can use the following tools to monitor your AWS CodePipeline pipelines and their resources:

+ **Amazon CloudWatch Events** — Use Amazon CloudWatch Events to detect and react to pipeline execution state changes \(for example, send an Amazon SNS notification or invoke a Lambda function\)\.

+ **AWS CloudTrail** — Use CloudTrail to capture API calls made by or on behalf of AWS CodePipeline in your AWS account and deliver the log files to an Amazon S3 bucket\. You can choose to have CloudWatch publish Amazon SNS notifications when new log files are delivered so you can take quick action\.

+ **Console and CLI** — You can use the AWS CodePipeline console and CLI to view details about the status of a pipeline or a particular pipeline execution\.

