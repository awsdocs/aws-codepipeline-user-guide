# How pipeline executions work<a name="concepts-how-it-works"></a>

This section provides an overview of the way CodePipeline processes a set of changes\. CodePipeline tracks each pipeline execution that starts when a change is made to the source code\. CodePipeline also tracks the way each execution progresses through the pipeline, including whether it is superseded by another execution\.

## How pipeline executions are started<a name="concepts-how-it-works-starting"></a>

You can trigger an execution when you change your source code or manually start the pipeline\. You can also trigger an execution through an Amazon CloudWatch Events rule that you schedule\. For example, when a source code change is pushed to a repository configured as the pipeline's source action, the pipeline detects the change and starts an execution\.

**Note**  
If a pipeline contains multiple source actions, all of them run again, even if a change is detected for one source action only\.

## How pipeline executions are stopped<a name="concepts-how-it-works-stopping"></a>

To use the console to stop a pipeline execution, you can choose **Stop execution** on the pipeline visualization page, on the execution history page, or on the detailed history page\. To use the CLI to stop a pipeline execution, you use the `stop-pipeline-execution` command\. For more information, see [Stop a pipeline execution in CodePipeline](pipelines-stop.md)\.

There are two ways to stop a pipeline execution:
+ **Stop and wait:** All in\-progress action executions are allowed to complete, and subsequent actions are not started\. The pipeline execution does not continue to subsequent stages\. You cannot use this option on an execution that is already in a `Stopping` state\.
+ **Stop and abandon:** All in\-progress action executions are abandoned and do not complete, and subsequent actions are not started\. The pipeline execution does not continue to subsequent stages\. You can use this option on an execution that is already in a `Stopping` state\.
**Note**  
This option can lead to failed tasks or out of sequence tasks\.

Each option results in a different sequence of pipeline and action execution phases, as follows\.

**Option 1: Stop and wait**

When you choose to stop and wait, the selected execution continues until in\-progress actions are completed\. For example, the following pipeline execution was stopped while the build action was in progress\. 

1. In the pipeline view, the success message banner is displayed, and the build action continues until it is completed\. The pipeline execution status is **Stopping**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/stop-exec-wait-vis-1.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   In the history view, the status for in\-progress actions, such as the build action, is **In progress** until the build action is completed\. While actions are in progress, the pipeline execution status is **Stopping**\.

1. The execution stops when the stopping process is complete\. If the build action is completed successfully, its status is **Succeeded**, and the pipeline execution shows a status of **Stopped**\. Subsequent actions do not start\. The **Retry** button is enabled\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/stop-exec-wait-vis-2.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   In the history view, the execution status is **Stopped** after the in\-progress action is completed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/stop-exec-wait-hist-1.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

**Option 2: Stop and abandon**

When you choose to stop and abandon, the selected execution does not wait for in\-progress actions to complete\. The actions are abandoned\. For example, the following pipeline execution was stopped and abandoned while the build action was in progress\.

1. In the pipeline view, the success banner message is displayed, the build action shows a status of **In progress**, and the pipeline execution shows a status of **Stopping**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/stop-exec-abandon-vis-1.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. After the pipeline execution stops, the build action shows a status of **Abandoned**, and the pipeline execution shows a status of **Stopped**\. Subsequent actions do not start\. The **Retry** button is enabled\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/stop-exec-abandon-vis-2.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. In the history view, the execution status is **Stopped**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/stop-exec-abandon-hist-1.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

**Use cases for stopping a pipeline execution**

We recommend that you use the stop and wait option to stop a pipeline execution\. This option is safer because it avoids possible failed or out\-of\-sequence tasks in your pipeline\. When an action is abandoned in CodePipeline, the action provider continues any tasks related to the action\. In the case of an AWS CloudFormation action, the deployment action in the pipeline is abandoned, but the stack update might continue and result in a failed update\. 

As an example of abandoned actions that can result in out\-of\-sequence tasks, if you are deploying a large file \(1GB\) through an S3 deployment action, and you choose to stop and abandon the action while the deployment is already in progress, the action is abandoned in CodePipeline, but continues in Amazon S3\. Amazon S3 does not encounter any instruction to cancel the upload\. Next, if you start a new pipeline execution with a very small file, there are now two deployments in progress\. Because the file size of the new execution is small, the new deployment completes while the old deployment is still uploading\. When the old deployment completes, the new file is overwritten by the old file\.

You might want to use the stop and abandon option in the case where you have a custom action\. For example, you can abandon a custom action with work that does not need to finish before you stop the execution for a new execution with a bug fix\.

## How executions are processed in a pipeline<a name="concepts-how-it-works-executions"></a>

An execution consists of a set of changes picked up and processed by the execution\. Pipelines can process multiple executions at the same time\. Each execution is run through the pipeline separately\. The pipeline processes each execution in order and might supersede an earlier execution with a later one\. The following rules are used to process executions in a pipeline\.

**Rule 1: Stages are locked when an execution is being processed**

Because each stage can process only one execution at a time, the stage is locked while in progress\. When the execution completes a stage, it transitions to the next stage in the pipeline\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/Promotion.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

**Rule 2: Subsequent executions wait for the stage to be unlocked**

While a stage is locked, waiting executions are held in front of the locked stage\. All actions configured for a stage must be completed successfully before the stage is considered complete\. A failure releases the lock on the stage\. When an execution is stopped, the execution does not continue in a stage and the stage is unlocked\.

**Note**  
Before you stop an execution, we recommend that you disable the transition in front of the stage\. This way, when the stage is unlocked due to the stopped execution, the stage does not accept a subsequent pipeline execution\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/Waiting.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

**Rule 3: Waiting executions are superseded by more recent executions**

Executions are only superseded in between stages\. A locked stage holds one execution at the front of the stage awaiting the stage to complete\. A more recent execution overtakes a waiting execution and continues to the next stage as soon as the stage is unlocked\. The superseded execution does not continue\. In this example, Execution 2 has been superseded by Execution 3 while awaiting the locked stage\. Execution 3 enters the stage next\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/Batching.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## Managing pipeline executions<a name="concepts-how-it-works-transitions-approvals"></a>

The flow of pipeline executions can be controlled by:
+ A *transition*, which controls the flow of executions into the stage\. Transitions can be enabled or disabled\. After you enable the transition, any execution waiting to enter the stage moves into the stage and locks it\. Similar to executions awaiting a locked stage, when a transition is disabled, the execution waiting to enter the stage can still be superseded by a new execution\. When a disabled transition is re\-enabled, the latest execution, including any that superseded older executions while the transition was disabled, enters the stage\.
+ An *approval action*, which prevents a pipeline from transitioning to the next action until permission is granted \(for example, through manual approval from an authorized IAM user\)\. You might use an approval action when you want to control the time at which a pipeline transitions to a final **Production** stage, for example\.
**Note**  
A stage with an approval action is locked until the approval action is approved or rejected or has timed out\. A timed\-out approval action is processed in the same way as a failed action\.
+ A *failure*, when an action in a stage does not complete successfully\. The revision does not transition to the next action in the stage or the next stage in the pipeline\. The following can occur:
  + You manually retry the stage that contains the failed actions\. This resumes the execution \(it retries failed actions and, if they succeed, continues in the stage/pipeline\)\.
  + Another execution enters the failed stage and supersedes the failed execution\. At this point, the failed execution cannot be retried\.

### Recommended pipeline structure<a name="concepts-recommended-pipeline-method"></a>

When deciding how a code change should flow through your pipeline, it is best to group related actions within a stage so that, when the stage locks, the actions all process the same execution\. You might create a stage for each application environment, AWS Region, or Availability Zone, and so on\. A pipeline with too many stages \(that is, too granular\) can allow too many concurrent changes, while a pipeline with many actions in a large stage \(too coarse\) can take too long to release a change\.

As an example, a test action after a deployment action in the same stage is guaranteed to test the same change that was deployed\. In this example, a change is deployed to a Test environment and then tested, and then the latest change from the test environment is deployed to a Production environment\. In the recommended example, the Test environment and the Prod environment are separate stages\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/structure-example-recommended-notrecommended.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)