# Stop a pipeline execution in CodePipeline<a name="pipelines-stop"></a>

When a pipeline execution starts to run through a pipeline, it enters one stage at a time and locks the stage while all action executions in the stage are running\. These in\-progress actions must be handled in a way so that, when the pipeline execution is stopped, the actions are either allowed to complete or abandoned\. 

There are two ways to stop a pipeline execution:
+ **Stop and wait**: AWS CodePipeline waits to stop the execution until all in\-progress actions are completed \(that is, the actions have a `Succeeded` or `Failed` status\)\. This option preserves in\-progress actions\. The execution is in a `Stopping` state until the in\-progress actions are complete\. Then the execution is in a `Stopped` state\. The stage unlocks after the actions are complete\.

  If you choose to stop and wait, and you change your mind while your execution is still in a `Stopping` state, you can then choose to abandon\.
+ **Stop and abandon**: AWS CodePipeline stops the execution without waiting for in\-progress actions to complete\. The execution is in a `Stopping` state for a very short time while the in\-progress actions are abandoned\. After the execution is stopped, the action execution is in an `Abandoned` state while the pipeline execution is in a `Stopped` state\. The stage unlocks\.

  For a pipeline execution in a `Stopped` state, the actions in the stage where the execution stopped can be retried\.
**Warning**  
This option can lead to failed tasks or out of sequence tasks\.

**Topics**
+ [Stop a pipeline execution \(console\)](#pipelines-stop-console)
+ [Stop a pipeline execution \(CLI\)](#pipelines-stop-cli)

## Stop a pipeline execution \(console\)<a name="pipelines-stop-console"></a>

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. Do one of the following:
**Note**  
Before you stop an execution, we recommend that you disable the transition in front of the stage\. This way, when the stage unlocks due to the stopped execution, the stage does not accept a subsequent pipeline execution\.
   + In **Name**, choose the name of the pipeline with the execution you want to stop\. On the pipeline details page, choose **Stop execution**\.
   + Choose **View history**\. On the history page, choose **Stop execution**\.

1. On the **Stop execution** page, under **Select execution**, choose the execution you want to stop\.
**Note**  
The execution is displayed only if it is still in progress\. Executions that are already complete are not displayed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/stop-execution.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Under **Select an action to apply to execution**, choose one of the following:
   + To make sure the execution does not stop until all in\-progress actions are complete, choose **Stop and wait**\.
**Note**  
You cannot choose to stop and wait if the execution is already in a **Stopping** state, but you can choose to stop and abandon\.
   + To stop without waiting for in\-progress actions to complete, choose **Stop and abandon**\.
**Warning**  
This option can lead to failed tasks or out of sequence tasks\.

1. \(Optional\) Enter comments\. These comments, along with the execution status, are displayed on the history page for the execution\.

1. Choose **Stop**\.
**Important**  
This action cannot be undone\.

1. View the execution status in the pipeline visualization as follows:
   + If you chose to stop and wait, the selected execution continues until in\-progress actions are completed\.
     + The success banner message is displayed at the top of the console\.
     + In the current stage, in\-progress actions continue in an `InProgress` state\. While the actions are in progress, the pipeline execution is in a `Stopping` state\.

       After the actions complete \(that is, the action fails or succeeds\), the pipeline execution changes to a `Stopped` state and the action changes to a `Failed` or `Succeeded` state\. You can also view the action state on the execution details page\. You can view the execution status on the execution history page or the execution details page\.
     + The pipeline execution changes to a `Stopping` state briefly, and then it changes to a `Stopped` state\. You can view the execution status on the execution history page or the execution details page\.
   + If you chose to stop and abandon, the execution does not wait for in\-progress actions to complete\. 
     + The success banner message is displayed at the top of the console\.
     + In the current stage, in\-progress actions change to a status of `Abandoned`\. You can also view the action status on the execution details page\.
     + The pipeline execution changes to a `Stopping` state briefly, and then it changes to a `Stopped` state\. You can view the execution status on the execution history page or the execution details page\.

   You can view the pipeline execution status in the execution history view and the detailed history view\.

## Stop a pipeline execution \(CLI\)<a name="pipelines-stop-cli"></a>

To use the AWS CLI to manually stop a pipeline, use the stop\-pipeline\-execution command with the following parameters:
+ Execution ID \(required\)
+ Comments \(optional\)
+ Pipeline name \(required\)
+ Abandon flag \(optional, the default is false\)

Command format:

```
aws codepipeline stop-pipeline-execution --pipeline-name Pipeline_Name –-pipeline-execution-id Execution_ID [--abandon | --no-abandon] [--reason STOP_EXECUTION_REASON]
```

1. Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\)\.

1. To stop a pipeline execution, choose one of the following:
   + To make sure the execution does not stop until all in\-progress actions are complete, choose to stop and wait\. You can do this by including the no\-abandon parameter\. If you do not specify the parameter, the command defaults to stop and wait\. Use the AWS CLI to run the stop\-pipeline\-execution command, specifying the name of the pipeline and the execution ID\. For example, to stop a pipeline named *MyFirstPipeline* with the stop and wait option specified:

     ```
     aws codepipeline stop-pipeline-execution --pipeline-name MyFirstPipeline --pipeline-execution-id d-EXAMPLE --no-abandon
     ```

     For example, to stop a pipeline named *MyFirstPipeline*, defaulting to the stop and wait option, and choosing to include comments:

     ```
     aws codepipeline stop-pipeline-execution --pipeline-name MyFirstPipeline --pipeline-execution-id d-EXAMPLE --reason "Stopping execution after the build action is done"
     ```
**Note**  
You cannot choose to stop and wait if the execution is already in a **Stopping** state\. You can choose to stop and abandon an execution that is already in a **Stopping** state\.
   + To stop without waiting for in\-progress actions to complete, choose to stop and abandon\. Include the abandon parameter\. Use the AWS CLI to run the stop\-pipeline\-execution command, specifying the name of the pipeline and the execution ID\. 

     For example, to stop a pipeline named *MyFirstPipeline*, specifying the abandon option, and choosing to include comments:

     ```
     aws codepipeline stop-pipeline-execution --pipeline-name MyFirstPipeline --pipeline-execution-id d-EXAMPLE --abandon --reason "Stopping execution for a bug fix"
     ```