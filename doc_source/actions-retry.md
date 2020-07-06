# Retry a failed action in CodePipeline<a name="actions-retry"></a>

In AWS CodePipeline, an action is a task performed on an artifact in a stage\. A failure is an action in a stage that is not completed successfully\. You can use the CLI to manually retry the failed action before the stage completes \(while other actions are still in progress\)\. If a stage completes with one or more failed actions, the stage fails, and the pipeline execution does not transition to the next stage in the pipeline\.

You can retry the latest failed actions in a stage without having to run a pipeline again from the beginning\. You do this by retrying the stage that contains the actions\. You can retry a stage immediately after any of actions fail\. All actions that are still in progress continue their work, and failed actions are triggered once again\.

If you are using the console to view a pipeline, a **Retry** button appears on the stage where the failed actions can be retried\. 

![\[A stage that contains one or more failed actions displays a Retry button\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/actions-retry-button.png)![\[A stage that contains one or more failed actions displays a Retry button\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A stage that contains one or more failed actions displays a Retry button\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

If you are using the AWS CLI, you can use the get\-pipeline\-state command to determine whether any actions have failed\.

**Note**  
In the following cases, you may not be able to retry actions:  
The overall pipeline structure changed after an action failed\.
Another retry attempt in the stage is already in progress\.

**Topics**
+ [Retry failed actions \(console\)](#actions-retry-console)
+ [Retry failed actions \(CLI\)](#actions-retry-cli)

## Retry failed actions \(console\)<a name="actions-retry-console"></a>

****

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account are displayed\.

1.  In **Name**, choose the name of the pipeline\. 

1. Locate the stage with the failed action, and then choose **Retry**\.
**Note**  
To identify which actions in the stage can be retried, hover over the **Retry** button\.

   If all retried actions in the stage are completed successfully, the pipeline continues to run\.

## Retry failed actions \(CLI\)<a name="actions-retry-cli"></a>

**Option 1: Retry failed actions using parameters for the CLI command**

To use the AWS CLI to retry failed actions, you run the retry\-stage\-execution command with the following parameters: 

```
--pipeline-name <value>
--stage-name <value>
--pipeline-execution-id <value>
--retry-mode <value>
```
**Note**  
The only value you can use for `retry-mode` is `FAILED_ACTIONS`\.
+ At a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), run the [https://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-state.html](https://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-state.html) command, as shown in the following example for a pipeline named `MyPipeline`\.

  ```
  aws codepipeline retry-stage-execution --pipeline-name MyPipeline --stage-name Deploy --pipeline-execution-id b59babff-5f34-EXAMPLE --retry-mode FAILED_ACTIONS
  ```

  The output returns the execution ID:

  ```
  {
      "pipelineExecutionId": "b59babff-5f34-EXAMPLE"
  }
  ```

**Option 2: Retry failed actions using a JSON input file for the CLI command**

You can also run the command with a JSON input file\. You first create a JSON file that identifies the pipeline, the stage that contains the failed actions, and the latest pipeline execution in that stage\. You then run the retry\-stage\-execution command with the `--cli-input-json` parameter\. To retrieve the details you need for the JSON file, it's easiest to use the get\-pipeline\-state command\.

1. At a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), run the [https://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-state.html](https://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-state.html) command on a pipeline\. For example, for a pipeline named MyFirstPipeline, you would type something similar to the following:

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

1. Call the file you created when you run the [https://docs.aws.amazon.com/cli/latest/reference/codepipeline/retry-stage-execution.html](https://docs.aws.amazon.com/cli/latest/reference/codepipeline/retry-stage-execution.html) command\. For example:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline retry-stage-execution --cli-input-json file://retry-failed-actions.json
   ```

1. To view the results of the retry attempt, either open the CodePipeline console and choose the pipeline that contains the actions that failed, or use the get\-pipeline\-state command again\. For more information, see [View pipeline details and history in CodePipeline](pipelines-view.md)\. 