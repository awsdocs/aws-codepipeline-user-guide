# How Pipeline Executions Work<a name="concepts-how-it-works"></a>

This section provides an overview of the way CodePipeline processes a set of changes\. CodePipeline tracks each pipeline execution that starts when a change is made to the source code\. CodePipeline also tracks the way each execution progresses through the pipeline, including whether it is superseded by another execution\.

## How Pipeline Executions Are Started<a name="concepts-how-it-works-starting"></a>

You can trigger an execution when you change your source code or manually start the pipeline\. You can also trigger an execution through an Amazon CloudWatch Events rule that you schedule\. For example, when a source code change is pushed to a repository configured as the pipeline's source action, the pipeline detects the change and starts an execution\.

**Note**  
If a pipeline contains multiple source actions, all of them run again, even if a change is detected for one source action only\.

## How Executions Are Processed in a Pipeline<a name="concepts-how-it-works-executions"></a>

An execution consists of a set of changes picked up and processed by the execution\. Pipelines can process multiple executions at the same time\. Each execution is run through the pipeline separately\. The pipeline processes each execution in order and might supersede an earlier execution with a later one\. The following rules are used to process executions in a pipeline\.

**Rule 1: Stages are locked when processing an execution**

Because each stage can process only one execution at a time, the stage is locked while in progress\. When the execution completes a stage, it transitions to the next stage in the pipeline\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/Promotion.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

**Rule 2: Subsequent executions wait for the stage to be unlocked**

While a stage is locked, waiting executions are held in front of the locked stage\. All actions configured for a stage must be completed successfully before the stage is considered complete\. A failure releases the lock on the stage\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/Waiting.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

**Rule 3: Waiting executions are superseded by more recent executions**

Executions are only superseded in between stages\. A locked stage holds one execution at the front of the stage awaiting the stage to complete\. A more recent execution overtakes a waiting execution and continues to the next stage as soon as the stage is unlocked\. The superseded execution does not continue\. In this example, Execution 2 has been superseded by Execution 3 while awaiting the locked stage\. Execution 3 enters the stage next\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/Batching.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## Managing Pipeline Executions<a name="concepts-how-it-works-transitions-approvals"></a>

The flow of pipeline executions can be controlled by:
+ A *transition*, which controls the flow of executions into the stage\. Transitions can be enabled or disabled\. After you enable the transition, any execution waiting to enter the stage moves into the stage and locks it\. Similar to executions awaiting a locked stage, when a transition is disabled, the execution waiting to enter the stage can still be superseded by a new execution\. When a disabled transition is re\-enabled, the latest execution, including any that superseded older executions while the transition was disabled, enters the stage\.
+ An *approval action*, which prevents a pipeline from transitioning to the next action until permission is granted \(for example, through manual approval from an authorized IAM user\)\. You might use an approval action when you want to control the time at which a pipeline transitions to a final **Production** stage, for example\.
**Note**  
A stage with an approval action is locked until the approval action is approved or rejected or has timed out\. A timed\-out approval action is processed in the same way as a failed action\.
+ A *failure*, when an action in a stage does not complete successfully\. The revision does not transition to the next action in the stage or the next stage in the pipeline\. The following can occur:
  + You manually retry the stage that contains the failed actions\. This resumes the execution \(it retries failed actions and, if they succeed, continues in the stage/pipeline\)\.
  + Another execution enters the failed stage and supersedes the failed execution\. At this point, the failed execution cannot be retried\.

### Recommended Pipeline Structure<a name="concepts-recommended-pipeline-method"></a>

When deciding how a code change should flow through your pipeline, it is best to group related actions within a stage so that, when the stage locks, the actions all process the same execution\. You might create a stage for each application environment, AWS Region, or Availability Zone, and so on\. A pipeline with too many stages \(that is, too granular\) can allow too many concurrent changes, while a pipeline with many actions in a large stage \(too coarse\) can take too long to release a change\.

As an example, a build and test action followed by a deployment action in the same stage is guaranteed to deploy the same change that was tested\. In this example, a change is built, tested, and deployed to a Test environment, and then the latest change from the test environment is deployed to a Production environment\. In the recommended example, the Test environment and the Prod environment are separate stages\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/structure-example-recommended-notrecommended.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)