--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# View Pipeline Details and History in AWS CodePipeline<a name="pipelines-view"></a>

You can use the AWS CodePipeline console or the AWS CLI to view details about pipelines associated with your AWS account\. 

**Topics**
+ [View Pipeline Details and History \(Console\)](#pipelines-view-console)
+ [View Pipeline Details and History \(CLI\)](#pipelines-view-cli)

## View Pipeline Details and History \(Console\)<a name="pipelines-view-console"></a>

You can use the AWS CodePipeline console to view a list of all of the pipelines in your account\. You can also view details for each pipeline, including when actions last ran in the pipeline, whether a transition between stages is enabled or disabled, whether any actions have failed, and other information\. You can also view a history page that shows details for all pipeline executions for which history has been recorded\. Execution history is limited to the past 12 months\. 

**Note**  
After an hour, the detailed view of a pipeline stops refreshing automatically in your browser\. To view current information, refresh the page\.

**To view a pipeline**

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names and creation date of all pipelines associated with your AWS account are displayed, along with links to view execution history\.

1. To see details for a single pipeline, in **Name**, choose the pipeline\. You can also select the pipeline, and then choose **View pipeline**\. A detailed view of the pipeline, including the state of each action in each stage and the state of the transitions, is displayed\.  
![\[The console view of the pipeline indicates the state of each action in each stage and the state of the transitions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-view-pipeline-pol.png)![\[The console view of the pipeline indicates the state of each action in each stage and the state of the transitions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The console view of the pipeline indicates the state of each action in each stage and the state of the transitions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   The graphical view displays the following information for each stage:
   + The stage name\.
   + Every action configured for the stage\.
   + The state of transitions between stages \(enabled or disabled\), as indicated by the state of the arrow between stages\. An enabled transition is indicated by an arrow with a **Disable transition** button next to it\. A disabled transition is indicated by an arrow with a strikeout under it and an **Enable transition** button next to it\.
   + A color bar to indicate the status of the stage:
     + Gray: No executions yet
     + Blue: In progress
     + Green: Succeeded
     + Red: Failed

   The graphical view also displays the following information about actions in each stage:
   + The name of the action\.
   + The provider of the action, such as AWS CodeDeploy\.
   + When the action was last run\.
   + Whether the action succeeded or failed\.
   + Links to other details about the last run of the action, where available\.
   + Details about the source revisions that are running through the latest pipeline execution in the stage or, for AWS CodeDeploy deployments, the latest source revisions that were deployed to target instances\.

1. To see the configuration details for an action in a stage of a pipeline, choose the information icon next to the action\.

1. To view the details of the provider of the action, choose the provider\. For example, in the preceding example pipeline, if you choose AWS CodeDeploy in either the Staging or Production stages the AWS CodeDeploy console page for the deployment group configured for that stage is displayed\.

1. To see the progress details for an action in a stage, choose **Details** when it is displayed next to an action in progress \(indicated by an **In Progress** message\)\. If the action is in progress, you see the incremental progress and the steps or actions as they occur\. 
**Note**  
Details are available for source actions that retrieve content from GitHub repositories, but not those that retrieve content from Amazon S3 buckets or AWS CodeCommit repositories\.

1. To approve or reject actions that have been configured for manual approval, choose **Review**\.

1. To retry actions in a stage that were not completed successfully, choose **Retry**\.

1. To get more information about errors or failures for a completed action in a stage, choose **Details**\. Details from the last time the action ran, including the results of that action \(**Succeeded** or **Failed**\) are displayed\.

1. To view details about source artifacts \(output artifact that originated in the first stage of a pipeline\) that are used the latest pipeline execution for a stage, click in the details information area at the bottom of the stage\. You can view details about identifiers, such as commit IDs, check\-in comments, and the time since the artifact was created or updated\. For more information, see [View Current Source Revision Details in a Pipeline in AWS CodePipeline](monitoring-source-revisions-view.md)\.

1. To view details about the most recent executions for the pipeline, choose **View history**\. For past executions, you can view revision details associated with source artifacts, such as execution IDs, status, start and end times, duration, and commit IDs and messages\.

## View Pipeline Details and History \(CLI\)<a name="pipelines-view-cli"></a>

You can run the following commands to view details about your pipelines and pipeline executions:
+  list\-pipelines command to view a summary of all of the pipelines associated with your AWS account\.
+ get\-pipeline command to review details of a single pipeline\.
+ list\-pipeline\-executions to view summaries of the most recent executions for a pipeline\.
+ get\-pipeline\-execution to view information about an execution of a pipeline, including details about artifacts, the pipeline execution ID, and the name, version, and status of the pipeline\. 

For information about using the CLI to view details about the source revisions used in the latest pipeline execution for a stage, see [View Current Source Revision Details in a Pipeline \(CLI\)](monitoring-source-revisions-view.md#monitoring-source-revisions-view-cli)\.

**To view a pipeline**

1. Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\) and use the AWS CLI to run the [list\-pipelines](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/list-pipelines.html) command:

   ```
   aws codepipeline list-pipelines
   ```

   This command returns a list of all of the pipelines associated with your AWS account\.

1. To view details about a pipeline, run the [get\-pipeline](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline.html) command, specifying the unique name of the pipeline\. For example, to view details about a pipeline named *MyFirstPipeline*, enter the following:

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline
   ```

   This command returns the structure of the pipeline\.

1. To view details about the current state of a pipeline, run the [get\-pipeline\-state](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-state.html) command, specifying the unique name of the pipeline\. For example, to view details about the current state of a pipeline named *MyFirstPipeline*, enter the following:

   ```
   aws codepipeline get-pipeline-state --name MyFirstPipeline
   ```

   This command returns the current status of all stages of the pipeline and the status of the actions in those stages\.

   The following example shows the returned data for a three\-stage pipeline named *MyFirstPipeline*, where the first two stages and actions show success, the third shows failure, and the transition between the second and third stages is disabled:

   ```
   {
       "updated": 1427245911.525,
       "created": 1427245911.525,
       "pipelineVersion": 1,
       "pipelineName": "MyFirstPipeline",
       "stageStates": [
           {
               "actionStates": [
                   {
                       "actionName": "Source",
                       "entityUrl": "https://console.aws.amazon.com/s3/home?#",
                       "latestExecution": {
                           "status": "Succeeded",
                           "lastStatusChange": 1427298837.768
                       }
                   }
               ],
               "stageName": "Source"
           },
           {
               "actionStates": [
                   {
                       "actionName": "Deploy-CodeDeploy-Application",
                       "entityUrl": "https://console.aws.amazon.com/codedeploy/home?#",
                       "latestExecution": {
                           "status": "Succeeded",
                           "lastStatusChange": 1427298939.456,
                           "externalExecutionUrl": "https://console.aws.amazon.com/?#",
                           "externalExecutionId": ""c53dbd42-This-Is-An-Example"",
                           "summary": "Deployment Succeeded"
                       }
                   }
               ],
               "inboundTransitionState": {
                   "enabled": true
               },
               "stageName": "Staging"
           },
           {
               "actionStates": [
                   {
                       "actionName": "Deploy-Second-Deployment",
                       "entityUrl": "https://console.aws.amazon.com/codedeploy/home?#",
                       "latestExecution": {
                           "status": "Failed",
                           "errorDetails": {
                               "message": "Deployment Group is already deploying deployment ...",
                               "code": "JobFailed"
                           },
                           "lastStatusChange": 1427246155.648
                       }
                   }
               ],
               "inboundTransitionState": {
                   "disabledReason": "Disabled while I investigate the failure",
                   "enabled": false,
                   "lastChangedAt": 1427246517.847,
                   "lastChangedBy": "arn:aws:iam::80398EXAMPLE:user/CodePipelineUser"
               },
               "stageName": "Production"
           }
       ]
   }
   ```

1. To view details about past executions of a pipeline, run the [list\-pipeline\-executions](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/list-pipeline-executions.html) command, specifying the unique name of the pipeline\. For example, to view details about the current state of a pipeline named *MyFirstPipeline*, enter the following:

   ```
   aws codepipeline list-pipeline-executions --pipeline-name MyFirstPipeline
   ```

   This command returns summary information about all pipeline executions for which history has been recorded over the past 12 months\. The summary includes start and end times, duration, and status\.

   The following example shows the returned data for a pipeline named *MyFirstPipeline* that has had three executions:

   ```
   {
       "pipelineExecutionSummaries": [
           {
               "lastUpdateTime": 1496380678.648,
               "pipelineExecutionId": "7cf7f7cb-3137-539g-j458-d7eu3EXAMPLE",
               "startTime": 1496380258.243,
               "status": "Succeeded"
           },
           {
               "lastUpdateTime": 1496591045.634,
               "pipelineExecutionId": "3137f7cb-8d494hj4-039j-d84l-d7eu3EXAMPLE",
               "startTime": 1496590401.222,
               "status": "Succeeded"
           },
           {
               "lastUpdateTime": 1496946071.6456,
               "pipelineExecutionId": "4992f7jf-7cf7-913k-k334-d7eu3EXAMPLE",
               "startTime": 1496945471.5645,
               "status": "Succeeded"
           }
       ]
   }
   ```

   To view more details about a pipeline execution, run the [get\-pipeline\-execution](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-execution.html), specifying the unique ID of the pipeline execution\. For example, to view more details about the first execution in the previous example, enter the following: 

   ```
   aws codepipeline get-pipeline-execution --pipeline-name MyFirstPipeline --pipeline-execution-id 7cf7f7cb-3137-539g-j458-d7eu3EXAMPLE
   ```

   This command returns summary information about an execution of a pipeline, including details about artifacts, the pipeline execution ID, and the name, version, and status of the pipeline\. 

   The following example shows the returned data for a pipeline named *MyFirstPipeline*:

   ```
   {
       "pipelineExecution": {
           "pipelineExecutionId": "3137f7cb-7cf7-039j-s83l-d7eu3EXAMPLE",
           "pipelineVersion": 2,
           "pipelineName": "MyFirstPipeline",
           "status": "Succeeded",
           "artifactRevisions": [
               {
                   "created": 1496380678.648,
                   "revisionChangeIdentifier": "1496380258.243",
                   "revisionId": "7636d59f3c461cEXAMPLE8417dbc6371",
                   "name": "MyApp",
                   "revisionSummary": "Updating the application for feature 12-4820"
               }
           ]
       }
   }
   ```