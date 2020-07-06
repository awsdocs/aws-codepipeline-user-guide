# Tutorial: Use an AWS Step Functions invoke action in a pipeline<a name="tutorials-step-functions"></a>

You can use AWS Step Functions to create and configure state machines\. This tutorial shows you how to add an invoke action to a pipeline that activates state machine executions from your pipeline\. 

In this tutorial, you do the following tasks:
+ Create a standard state machine in AWS Step Functions\.
+ Enter the state machine input JSON directly\. You can also upload the state machine input file to an Amazon Simple Storage Service \(Amazon S3\) bucket\.
+ Update your pipeline by adding the state machine action\.

**Topics**
+ [Prerequisite: Create or choose a simple pipeline](#tutorials-step-functions-prereq)
+ [Step 1: Create the sample state machine](#tutorials-step-functions-sample)
+ [Step 2: Add a Step Functions invoke action to your pipeline](#tutorials-step-functions-pipeline)

## Prerequisite: Create or choose a simple pipeline<a name="tutorials-step-functions-prereq"></a>

In this tutorial, you add an invoke action to an existing pipeline\. You can use the pipeline you created in [Tutorial: Create a simple pipeline \(S3 bucket\)](tutorials-simple-s3.md) or [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md)\.

You use an existing pipeline with a source action and at least a two\-stage structure, but you do not use source artifacts for this example\.

**Note**  
You might need to update the service role used by your pipeline with additional permissions required to run this action\. To do this, open the AWS Identity and Access Management \(IAM\) console, find the role, and then add the permissions to the role's policy\. For more information, see [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services)\.

## Step 1: Create the sample state machine<a name="tutorials-step-functions-sample"></a>

In the Step Functions console, create a state machine using the `HelloWorld` sample template\. For instructions, see [Create a State Machine](https://docs.aws.amazon.com/step-functions/latest/dg/getting-started.html#create-state-machine) in the *AWS Step Functions Developer Guide*\.

## Step 2: Add a Step Functions invoke action to your pipeline<a name="tutorials-step-functions-pipeline"></a>

Add a Step Functions invoke action to your pipeline as follows:

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account are displayed\.

1. In **Name**, choose the name of the pipeline you want to edit\. This opens a detailed view of the pipeline, including the state of each of the actions in each stage of the pipeline\.

1. On the pipeline details page, choose **Edit**\.

1. On the second stage of your simple pipeline, choose **Edit stage**\. Choose **Delete**\. This deletes the second stage now that you no longer need it\.

1. At the bottom of the diagram, choose **\+ Add stage**\.

1. In **Stage name**, enter a name for the stage, such as **Invoke**, and then choose **Add stage**\.

1. Choose **\+ Add action group**\.

1. In **Action name**, enter a name, such as **Invoke**\.

1. In **Action provider**, choose **AWS Step Functions**\. Allow **Region** to default to the pipeline Region\.

1. In **Input artifacts**, choose `SourceArtifact`\.

1. In **State machine ARN**, choose the Amazon Resource Name \(ARN\) for the state machine that you created earlier\.

1. \(Optional\) In **Execution name prefix**, enter a prefix to be added to the state machine execution ID\.

1. In **Input type**, choose **Literal**\.

1. In **Input**, enter the input JSON that the `HelloWorld` sample state machine expects\.
**Note**  
The input to the state machine execution is different from the term used in CodePipeline to describe input artifacts for actions\.

   For this example, enter the following JSON:

   ```
   {"IsHelloWorldExample": true}
   ```  
![\[Console screenshot showing details of adding Step Functions invoke action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/stepfunctions-edit-action.png)![\[Console screenshot showing details of adding Step Functions invoke action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing details of adding Step Functions invoke action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Choose **Done**\.

1. On the stage that you're editing, choose **Done**\. In the AWS CodePipeline pane, choose **Save**, and then choose **Save** on the warning message\.

1. To submit your changes and start a pipeline execution, choose **Release change**, and then choose **Release**\.  
![\[Console screenshot showing successfully completed pipeline with pipeline execution ID.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/stepfunctions-pipelines.png)![\[Console screenshot showing successfully completed pipeline with pipeline execution ID.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing successfully completed pipeline with pipeline execution ID.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. On your completed pipeline, choose **AWS Step Functions** in your invoke action\. In the AWS Step Functions console, view your state machine execution ID\. The ID shows your state machine name `HelloWorld` and the state machine execution ID with the prefix `my-prefix`\.

   ```
   arn:aws:states:us-west-2:account-ID:execution:HelloWorld:my-prefix-0d9a0900-3609-4ebc-925e-83d9618fcca1
   ```