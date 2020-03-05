# Start a Pipeline Execution in CodePipeline<a name="pipelines-about-starting"></a>

When a pipeline execution starts, it runs a revision through every stage and action in the pipeline\.

There are two ways to start a pipeline execution in AWS CodePipeline:
+ **Automatically**: Using change detection methods that you specify, you can make your pipeline start when a change is made to a repository\. You can also make your pipeline start on a schedule\. The following are the automatic change detection methods:
  + When you use the console to create a pipeline that has a CodeCommit source repository or S3 source bucket, CodePipeline creates an Amazon CloudWatch Events rule that starts your pipeline when the source changes\. This is the recommended change detection method\. If you use the AWS CLI to create the pipeline, the change detection method defaults to starting the pipeline by periodically checking the source \(CodeCommit, Amazon S3, and GitHub source providers only\)\. We recommend that you disable periodic checks and create the rule manually\. For more information, see [ Use CloudWatch Events to Start a Pipeline \(CodeCommit Source\)](triggering.md)\.
  + When you use the console to create a pipeline that has a GitHub repository, CodePipeline creates a webhook that starts your pipeline when the source changes\. This is the recommended change detection method\. If you use the AWS CLI to create the pipeline, the change detection method defaults to starting the pipeline by periodically checking the source\. We recommend that you disable periodic checks and create the webhook\. For more information, see [Use Webhooks to Start a Pipeline \(GitHub Source\)](pipelines-webhooks.md)\.
  + Most source actions in CodePipeline, such as GitHub, require either a configured change detection resource \(such as a webhook or CloudWatch Events rule\) or use the option to poll the repository for source changes\. For pipelines with a Bitbucket Cloud source action, you do not have to set up a webhook or default to polling\. The connections action manages your source change detection for you\. 
+ **Manually**: You can use the console or the AWS CLI to start a pipeline manually\. For information, see [Start a Pipeline Manually in AWS CodePipeline](pipelines-rerun-manually.md)\.

By default, pipelines are configured to start automatically using change detection methods\.

**Note**  
Your pipeline runs only when something changes in the source repository and branch that you have defined\.

**Topics**
+ [Change Detection Methods to Start Pipelines](#change-detection-methods)
+ [Use CloudWatch Events to Start a Pipeline \(CodeCommit Source\)](triggering.md)
+ [Use CloudWatch Events to Start a Pipeline \(Amazon S3 Source\)](create-cloudtrail-S3-source.md)
+ [Use Webhooks to Start a Pipeline \(GitHub Source\)](pipelines-webhooks.md)
+ [Use CloudWatch Events to Start a Pipeline \(Amazon ECR Source\)](create-cwe-ecr-source.md)
+ [Use Periodic Checks to Start a Pipeline](run-automatically-polling.md)
+ [Start a Pipeline Manually in AWS CodePipeline](pipelines-rerun-manually.md)
+ [Use Amazon CloudWatch Events to Start a Pipeline on a Schedule](pipelines-trigger-source-schedule.md)

## Change Detection Methods to Start Pipelines<a name="change-detection-methods"></a>

When you create or update a pipeline, you specify the method to use to react to source repository changes\. Your choice determines how your pipeline is started\.


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-about-starting.html)

If you have a pipeline that uses periodic checks to react to source changes, you can update it to use the recommended detection method \(webhooks for pipelines with a GitHub source and Amazon CloudWatch Events for pipelines with a CodeCommit or Amazon S3 source\)\.


| How to Migrate Pipelines to the Recommended Change Detection Method | Source | Recommended Detection Method | Console | CLI | AWS CloudFormation | 
| --- | --- | --- | --- | --- | --- | 
| Amazon S3 | Amazon CloudWatch Events \(recommended\) and an AWS CloudTrail trail\.  | See [Update Pipelines for Push Events \(CodeCommit or Amazon S3 Source\) \(Console\)](update-change-detection.md#update-change-detection-console-codecommit-S3)\. | See [Update Pipelines for Push Events \(Amazon S3 Source\) \(CLI\)](update-change-detection.md#update-change-detection-cli-S3)\. | See [Update Pipelines for Push Events \(Amazon S3 Source\) \(AWS CloudFormation Template\)](update-change-detection.md#update-change-detection-cfn-s3)\. | 
| AWS CodeCommit | Amazon CloudWatch Events \(recommended\)\. | See [Update Pipelines for Push Events \(CodeCommit or Amazon S3 Source\) \(Console\)](update-change-detection.md#update-change-detection-console-codecommit-S3)\. | See [Update Pipelines for Push Events \(CodeCommit Source\) \(CLI\)](update-change-detection.md#update-change-detection-cli-codecommit)\. | See [Update Pipelines for Push Events \(CodeCommit Source\) \(AWS CloudFormation Template\)](update-change-detection.md#update-change-detection-cfn-codecommit)\. | 
| GitHub | Webhooks \(recommended\)\.[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-about-starting.html) | See [Update Pipelines for Push Events \(GitHub Source\) \(Console\)](update-change-detection.md#update-change-detection-console-github)\. | See [Update Pipelines for Push Events \(GitHub Source\) \(CLI\)](update-change-detection.md#update-change-detection-cli-github)\. | See [Update Pipelines for Push Events \(GitHub Source\) \(AWS CloudFormation Template\)](update-change-detection.md#update-change-detection-cfn-github)\. | 