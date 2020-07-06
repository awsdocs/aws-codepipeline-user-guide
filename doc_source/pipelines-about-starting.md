# Start a pipeline execution in CodePipeline<a name="pipelines-about-starting"></a>

When a pipeline execution starts, it runs a revision through every stage and action in the pipeline\.

There are two ways to start a pipeline execution in AWS CodePipeline:
+ **Automatically**: Using change detection methods that you specify, you can make your pipeline start when a change is made to a repository\. You can also make your pipeline start on a schedule\. The following are the automatic change detection methods:
  + When you use the console to create a pipeline that has a CodeCommit source repository or S3 source bucket, CodePipeline creates an Amazon CloudWatch Events rule that starts your pipeline when the source changes\. This is the recommended change detection method\. If you use the AWS CLI to create the pipeline, the change detection method defaults to starting the pipeline by periodically checking the source \(CodeCommit, Amazon S3, and GitHub source providers only\)\. We recommend that you disable periodic checks and create the rule manually\. For more information, see [ Use CloudWatch Events to start a pipeline \(CodeCommit source\)](triggering.md)\.
  + When you use the console to create a pipeline that has a GitHub repository, CodePipeline creates a webhook that starts your pipeline when the source changes\. This is the recommended change detection method\. If you use the AWS CLI to create the pipeline, the change detection method defaults to starting the pipeline by periodically checking the source\. We recommend that you disable periodic checks and create the webhook\. For more information, see [Use webhooks to start a pipeline \(GitHub source\)](pipelines-webhooks.md)\.
  + Most source actions in CodePipeline, such as GitHub, require either a configured change detection resource \(such as a webhook or CloudWatch Events rule\) or use the option to poll the repository for source changes\. For pipelines with a Bitbucket Cloud source action, you do not have to set up a webhook or default to polling\. The connections action manages your source change detection for you\. 
+ **Manually**: You can use the console or the AWS CLI to start a pipeline manually\. For information, see [Start a pipeline manually in AWS CodePipeline](pipelines-rerun-manually.md)\.

By default, pipelines are configured to start automatically using change detection methods\.

**Note**  
Your pipeline runs only when something changes in the source repository and branch that you have defined\.

**Topics**
+ [Change detection methods to start pipelines](#change-detection-methods)
+ [Use CloudWatch Events to start a pipeline \(CodeCommit source\)](triggering.md)
+ [Use CloudWatch Events to start a pipeline \(Amazon S3 source\)](create-cloudtrail-S3-source.md)
+ [Use webhooks to start a pipeline \(GitHub source\)](pipelines-webhooks.md)
+ [Use CloudWatch Events to start a pipeline \(Amazon ECR source\)](create-cwe-ecr-source.md)
+ [Use periodic checks to start a pipeline](run-automatically-polling.md)
+ [Start a pipeline manually in AWS CodePipeline](pipelines-rerun-manually.md)
+ [Use Amazon CloudWatch Events to start a pipeline on a schedule](pipelines-trigger-source-schedule.md)

## Change detection methods to start pipelines<a name="change-detection-methods"></a>

When you create or update a pipeline, you specify the method to use to react to source repository changes\. Your choice determines how your pipeline is started\.


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-about-starting.html)

If you have a pipeline that uses polling, you can update it to use the recommended detection method\. For more information, see [Update polling pipelines to the recommended change detection method](trigger-S3-migration-cwe.md)\.