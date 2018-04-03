# How to Start a Pipeline Execution in AWS CodePipeline<a name="pipelines-about-starting"></a>

When a pipeline execution starts, it runs a revision through every stage and action in the pipeline\.

There are two ways to start a pipeline execution:
+ **Automatically**: Using change\-detection methods that you specify, you can make your pipeline start automatically when a change is made to a repository\. You can also make your pipeline start automatically on a schedule\. 
+ **Manually**: You can use the console or the AWS CLI to start a pipeline, as described in [Start a Pipeline Manually in AWS CodePipeline](pipelines-rerun-manually.md)\.

By default, pipelines are configured to start automatically using available change detection methods\.

**Note**  
Your pipeline runs only when something changes in the source repository and branch you have defined\.

**Topics**
+ [Change\-Detection Methods Used to Start Pipelines Automatically](#change-detection-methods)
+ [How to Start Pipelines Using Amazon CloudWatch Events](#starting-pipelines-with-cloudwatch)

## Change\-Detection Methods Used to Start Pipelines Automatically<a name="change-detection-methods"></a>

When you create or update a pipeline, you specify the change\-detection method used to react to source repository changes\. Your choice then determines how your pipeline is automatically started\.


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-about-starting.html)

## How to Start Pipelines Using Amazon CloudWatch Events<a name="starting-pipelines-with-cloudwatch"></a>

Amazon CloudWatch is a web service that monitors your AWS resources and the applications that you run on AWS\. 

In Amazon CloudWatch, an event indicates a change in your AWS environment\. AWS resources can generate events when their state changes\. Using simple rules that are set up in Amazon CloudWatch Events, you can match events and route them to one or more target functions or streams\.

Amazon CloudWatch Events can be set up to point to AWS CodePipeline as a target\. AWS CodePipeline uses Amazon CloudWatch Events to detect changes in the configured AWS CodeCommit or Amazon S3 source\. When a change occurs, Amazon CloudWatch Events automatically starts your pipeline\.

**To configure CloudWatch Events to start your pipeline automatically when something in your source repository changes**

1. Create a CloudWatch Events rule that uses one of the available services as an event source\.

1. Create a target for your rule that selects AWS CodePipeline as a target\. 

1. Grant permissions to CloudWatch Events to start the pipeline by creating or selecting an AWS CodePipeline service role that CloudWatch Events will assume\.

When you use the console to create a pipeline that has an AWS CodeCommit source repository or Amazon S3 source bucket, the additional resources are created for you\. If you use the AWS CLI to create the pipeline, you must create the additional resources manually\. For more information, see [ Start a Pipeline Automatically Using a CloudWatch Events Rule](triggering.md)\.