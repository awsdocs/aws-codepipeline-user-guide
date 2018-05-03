# Configure Your Amazon S3 Pipelines to Use Amazon CloudWatch Events for Change Detection<a name="trigger-S3-migration-cwe"></a>

You can now use event\-based Amazon CloudWatch Events rules as the change detection method for your pipelines with an Amazon S3 source\. Amazon CloudWatch Events detects changes in real time and then starts your pipeline\. You must update existing pipelines to use this feature\. When you create or edit a pipeline in the console, AWS CodePipeline creates the Amazon CloudWatch Events event\-based rule and an AWS CloudTrail trail\.

This table includes procedures for Amazon S3 pipelines\.


****  

| Method | Instructions | Further action required? | 
| --- | --- | --- | 
| Configuring pipelines in the console |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/trigger-S3-migration-cwe.html) See [Edit a Pipeline \(Console\)](pipelines-edit.md#pipelines-edit-console)\.  |  The Amazon CloudWatch Events rule and AWS CloudTrail trail are created for you\.  No further action is required\.  | 
| Configuring pipelines in the CLI |  Use the `update-pipeline` command to set the `PollForSourceChanges` parameter to `false`\. See [Edit a Pipeline \(AWS CLI\)](pipelines-edit.md#pipelines-edit-cli)\.  |  You must create: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/trigger-S3-migration-cwe.html) See [Start an Amazon S3 Pipeline Automatically Using a CloudWatch Events Rule](create-cloudtrail-S3-source.md)\.  | 
| Configuring pipelines in AWS CloudFormation |  Update the AWS CloudFormation resource stack\. See [What Is Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.  |  You must create: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/trigger-S3-migration-cwe.html) See [Start an Amazon S3 Pipeline Automatically Using a CloudWatch Events Rule](create-cloudtrail-S3-source.md)\.  | 