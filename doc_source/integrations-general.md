--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# General Integrations with AWS CodePipeline<a name="integrations-general"></a>

The following AWS service integrations are not based on AWS CodePipeline action types\.


|  |  | 
| --- |--- |
| AWS CloudTrail |  [CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/) captures AWS API calls and related events made by or on behalf of an AWS account and delivers log files to an Amazon S3 bucket that you specify\. You can configure CloudTrail to capture API calls from the AWS CodePipeline console, AWS CodePipeline commands from the AWS CLI, and from the AWS CodePipeline API\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-general.html)  | 
| Amazon CloudWatch |  [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/) monitors your AWS resources\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-general.html)  | 
| Amazon CloudWatch Events | [Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/) is a web service that detects changes in your AWS services based on rules that you define and invokes an action in one or more specified AWS services when a change occurs\.[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-general.html) | 
| AWS Key Management Service |  [AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/) is a managed service that makes it easy for you to create and control the encryption keys used to encrypt your data\. By default, AWS CodePipeline uses AWS KMS to encrypt artifacts for pipelines stored in Amazon S3 buckets\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-general.html)  | 