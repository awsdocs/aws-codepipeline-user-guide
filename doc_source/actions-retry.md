# Retry a Failed Action in AWS CodePipeline<a name="actions-retry"></a>

In AWS CodePipeline, an action is a task performed on an artifact in a stage\. If an action or a set of parallel actions is not completed successfully, the pipeline stops running\. 

You can retry the latest failed actions in a stage without having to run a pipeline again from the beginning\. If you are using the console to view a pipeline, a **Retry** button will appear on the stage where the failed actions can be retried\. 

![\[A stage that contains one or more failed actions displays a Retry button\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/actions-retry-button.png)![\[A stage that contains one or more failed actions displays a Retry button\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A stage that contains one or more failed actions displays a Retry button\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

If you are using the AWS CLI, you can use the get\-pipeline\-state command to determine whether any actions have failed\.

**Note**  
In the following cases, you may not be able to retry actions:  
The overall pipeline structure changed after an action failed\.
One or more actions in the stage are still in progress\.
Another retry attempt in the stage is already in progress\.


+ [Retry Failed Actions \(Console\)](#actions-retry-console)
+ [Retry Failed Actions \(CLI\)](#actions-retry-cli)

## Retry Failed Actions \(Console\)<a name="actions-retry-console"></a>

****

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codepipeline](http://console.aws.amazon.com/codepipeline)\.

   The names of all pipelines associated with your AWS account are displayed\.

1.  In **Name**, choose the name of the pipeline\. 

1. Locate the stage with the failed action, and then choose **Retry**\.
**Note**  
To identify which actions in the stage can be retried, hover over the **Retry** button\.

   If all retried actions in the stage are completed successfully, the pipeline continues to run\.

## Retry Failed Actions \(CLI\)<a name="actions-retry-cli"></a>

****

To use the AWS CLI to retry failed actions, you first create a JSON file that identifies the pipeline, the stage that contains the failed actions, and the latest pipeline execution in that stage\. You then run the retry\-stage\-execution command with the `--cli-input-json` parameter\. To retrieve the details you need for the JSON file, it's easiest to use the get\-pipeline\-state command\.

1. At a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), run the [http://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-state.html](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-state.html) command on a pipeline\. For example, for a pipeline named MyFirstPipeline, you would type something similar to the following:

   ```
   aws codepipeline get-pipeline-state --name MyFirstPipeline
   ```

   The response to the command includes pipeline state information for each stage\. In the following example, the response indicates that one or more actions failed in the Staging stage:

   ```
   {
       "updated": 1427245911.525,
       "created": 1427245911.525,
       "pipelineVersion": 1,
       "pipelineName": "MyFirstPipeline",
       "stageStates": [
           {
               "actionStates": [...],
               "stageName": "Source",
               "latestExecution": {
                   "pipelineExecutionId": "9811f7cb-7cf7-SUCCESS",
                   "status": "Succeeded"
               }
           },
           {
               "actionStates": [...],
               "stageName": "Staging",
               "latestExecution": {
                   "pipelineExecutionId": "3137f7cb-7cf7-EXAMPLE",
                   "status": "Failed"
               }
           }
       ]
   }
   ```

1. In a plain\-text editor, create a file where you will record the following, in JSON format:

   + The name of the pipeline that contains the failed actions

   + The name of the stage that contains the failed actions

   + The ID of the latest pipeline execution in the stage

   + The retry mode\. \(Currently, the only supported value is FAILED\_ACTIONS\)

   For the preceding MyFirstPipeline example, your file would look something like this:

   ```
   {
       "pipelineName": "MyFirstPipeline",
       "stageName": "Staging",
       "pipelineExecutionId": "3137f7cb-7cf7-EXAMPLE",
       "retryMode": "FAILED_ACTIONS"
   }
   ```

1. Save the file with a name like **retry\-failed\-actions\.json**\.

1. Call the file you created when you run the [http://docs.aws.amazon.com/cli/latest/reference/codepipeline/retry-stage-execution.html](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/retry-stage-execution.html) command\. For example:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline retry-stage-execution --cli-input-json file://retry-failed-actions.json
   ```

1. To view the results of the retry attempt, either open the AWS CodePipeline console and choose the pipeline that contains the actions that failed, or use the get\-pipeline\-state command again\. For more information, see [View Pipeline Details and History in AWS CodePipeline](pipelines-view.md)\. 