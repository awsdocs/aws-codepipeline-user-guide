# Start a Pipeline Execution in AWS CodePipeline<a name="pipelines-about-starting"></a>

When a pipeline execution starts, it runs a revision through every stage and action in the pipeline\.

There are two ways to start a pipeline execution:
+ **Automatically**: Using change\-detection methods that you specify, you can make your pipeline start automatically when a change is made to a repository\. You can also make your pipeline start automatically on a schedule\. The following are the automatic change\-detection methods:
  + When you use the console to create a pipeline that has an AWS CodeCommit source repository or Amazon S3 source bucket, AWS CodePipeline creates an Amazon CloudWatch Events rule that starts your pipeline when the source changes\. This is the recommended change\-detection method\. If you use the AWS CLI to create the pipeline, the change\-detection method defaults to automatically starting the pipeline by checking the source periodically\. It is recommended to disable periodic checks and create the additional resources manually\. For more information, see [ Start an AWS CodeCommit Pipeline Automatically Using a CloudWatch Events Rule](triggering.md)\.
  + When you use the console to create a pipeline that has a GitHub repository, AWS CodePipeline creates a webhook that starts your pipeline when the source changes\. This is the recommended change\-detection method\. If you use the AWS CLI to create the pipeline, the change\-detection method defaults to automatically starting the pipeline by checking the source periodically\. It is recommended to disable periodic checks and create the additional resources manually\. For more information, see [Start a GitHub Pipeline Automatically Using Webhooks](pipelines-webhooks.md)\.
+ **Manually**: You can use the console or the AWS CLI to manually start a pipeline, as described in [Start a Pipeline Manually in AWS CodePipeline](pipelines-rerun-manually.md)\.

By default, pipelines are configured to start automatically using change\-detection methods\.

**Note**  
Your pipeline runs only when something changes in the source repository and branch that you have defined\.

**Topics**
+ [Change\-Detection Methods Used to Start Pipelines Automatically](#change-detection-methods)
+ [Start an AWS CodeCommit Pipeline Automatically Using a CloudWatch Events Rule](triggering.md)
+ [Start an Amazon S3 Pipeline Automatically Using a CloudWatch Events Rule](create-cloudtrail-S3-source.md)
+ [Start a GitHub Pipeline Automatically Using Webhooks](pipelines-webhooks.md)
+ [Start a Pipeline Automatically Using Periodic Checks](run-automatically-polling.md)
+ [Start a Pipeline Manually in AWS CodePipeline](pipelines-rerun-manually.md)
+ [Start a Pipeline Automatically on a Schedule with Amazon CloudWatch Events](pipelines-trigger-source-schedule.md)

## Change\-Detection Methods Used to Start Pipelines Automatically<a name="change-detection-methods"></a>

When you create or update a pipeline, you specify the change\-detection method to use to react to source repository changes\. Your choice determines how your pipeline is started\.


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-about-starting.html)