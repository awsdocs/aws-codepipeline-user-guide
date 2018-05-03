# Start an Amazon S3 Pipeline Automatically Using a CloudWatch Events Rule<a name="create-cloudtrail-S3-source"></a>

You should use Amazon CloudWatch Events to detect source code changes and trigger your pipeline to start automatically\. If your pipeline has an Amazon S3 source, you must create a corresponding AWS CloudTrail trail to log write events to objects in your Amazon S3 source bucket\.

AWS CloudTrail is a service that logs and filters events on your Amazon S3 source bucket\. The trail filters events on the bucket, and then sends the filtered source changes to the Amazon CloudWatch Events rule\. The Amazon CloudWatch Events rule detects the source change and then starts your pipeline\. 

**Note**  
For pipelines with an Amazon S3 source, an Amazon CloudWatch Events rule detects source changes and then starts your pipeline when changes occur\. When you use the console to create or change a pipeline, the rule and all associated resources are created for you\. If you create or change an Amazon S3 pipeline in the CLI or AWS CloudFormation, you must create the Amazon CloudWatch Events rule, IAM role, and AWS CloudTrail trail manually\.

**Requirements:**
+ If you are not creating a trail, you need to use an existing AWS CloudTrail trail for logging events in your Amazon S3 source bucket and sending filtered events to the Amazon CloudWatch Events rule\.
+ You need to create or use an existing S3 bucket where AWS CloudTrail can store its log files\. To deliver log files to an Amazon S3 bucket, AWS CloudTrail must have the required permissions, and it cannot be configured as a [Requester Pays](http://docs.aws.amazon.com/AmazonS3/latest/dev/latest/dev/RequesterPaysBuckets.html) bucket\. AWS CloudTrail automatically attaches the required permissions to a bucket when you create an Amazon S3 bucket as part of creating or updating a trail in the console\. For more information, see [Amazon S3 Bucket Policy for CloudTrail](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/create-s3-bucket-policy-for-cloudtrail.html)\.