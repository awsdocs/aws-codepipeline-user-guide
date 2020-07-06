# Use CloudWatch Events to start a pipeline \(Amazon S3 source\)<a name="create-cloudtrail-S3-source"></a>

You should use Amazon CloudWatch Events to detect source code changes and trigger the start of your pipeline\. If your pipeline has an Amazon S3 source, you must create an AWS CloudTrail trail to log write events to objects in your Amazon S3 source bucket\.

AWS CloudTrail is a service that logs and filters events on your Amazon S3 source bucket\. The trail sends the filtered source changes to the Amazon CloudWatch Events rule\. The Amazon CloudWatch Events rule detects the source change and then starts your pipeline\. 

**Note**  
For pipelines with an Amazon S3 source, an Amazon CloudWatch Events rule detects source changes and then starts your pipeline when changes occur\. When you use the console to create or change a pipeline, the rule and all associated resources are created for you\. If you create or change a pipeline with an Amazon S3 source in the CLI or AWS CloudFormation, you must create the Amazon CloudWatch Events rule, IAM role, and AWS CloudTrail trail manually\.

**Requirements:**
+ If you are not creating a trail, use an existing AWS CloudTrail trail for logging events in your Amazon S3 source bucket and sending filtered events to the Amazon CloudWatch Events rule\.
+ Create or use an existing S3 bucket where AWS CloudTrail can store its log files\. AWS CloudTrail must have the permissions required to deliver log files to an Amazon S3 bucket\. The bucket cannot be configured as a [Requester Pays](https://docs.aws.amazon.com/AmazonS3/latest/dev/RequesterPaysBuckets.html) bucket\. When you create an Amazon S3 bucket as part of creating or updating a trail in the console, AWS CloudTrail attaches the required permissions to a bucket for you\. For more information, see [Amazon S3 Bucket Policy for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/create-s3-bucket-policy-for-cloudtrail.html)\.