--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# AWS CodePipeline Permissions Reference<a name="permissions-reference"></a>

When you are setting up [Access Control with IAM Policies](access-control.md) and writing permissions policies that you can attach to an IAM identity \(identity\-based policies\), you can use the following table as a reference\. The table lists each AWS CodePipeline API operation and the corresponding actions for which you can grant permissions to perform the action\. You specify the actions in the policy's `Action` field, and you specify a wildcard character \(\*\) as the resource value in the policy's `Resource` field\.

You can use AWS\-wide condition keys in your AWS CodePipeline policies to express conditions\. For a complete list of AWS\-wide keys, see [Available Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\.

**Note**  
To specify an action, use the `codepipeline:` prefix followed by the API operation name\. For example: `codepipeline:GetPipeline`, `codepipeline:CreatePipeline`, or `codepipeline:*` \(for all AWS CodePipeline actions\)\.

To specify multiple actions in a single statement, separate them with commas as follows:

```
"Action": ["codepipeline:action1", "codepipeline:action2"]
```

You can also specify multiple actions using wildcards\. For example, you can specify all actions whose name begins with the word "Get" as follows:

```
"Action": "codepipeline:Get*"
```

To specify all AWS CodePipeline API actions, use the \* wildcard as follows:

```
"Action": "codepipeline:*"
```

The actions you can specify in an IAM policy for use with AWS CodePipeline are listed below\.


**AWS CodePipeline API Operations and Required Permissions for Actions**  

| AWS CodePipeline API Operations | Required Permissions \(API Actions\) | 
| --- | --- | 
|  [AcknowledgeJob](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_AcknowledgeJob.html)  |  `codepipeline:AcknowledgeJob` Required to view information about a specified job and whether that job has been received by the job worker\. Only used for custom actions\.  | 
|  [AcknowledgeThirdPartyJob](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_AcknowledgeThirdPartyJob.html)  |  `codepipeline:AcknowledgeThirdPartyJob` Required to confirm a job worker has received the specified job\. Only used for partner actions\.  | 
|  [CreateCustomActionType](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_CreateCustomActionType.html)  |  `codepipeline:CreateCustomActionType` Required to create a new custom action that can be used in all pipelines associated with the AWS account\. Only used for custom actions\.  | 
|  [CreatePipeline](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_CreatePipeline.html)  |  `codepipeline:CreatePipeline` Required to create a pipeline\.  | 
|  [DeleteCustomActionType](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_DeleteCustomActionType.html)  |  `codepipeline:DeleteCustomActionType` Required to mark a custom action as deleted\. PollForJobs for the custom action will fail after the action is marked for deletion\. Only used for custom actions\.  | 
|  [DeletePipeline](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_DeletePipeline.html)  |  `codepipeline:DeletePipeline` Required to delete a pipeline\.  | 
| [DeleteWebhook](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_DeleteWebhook.html) |  `codepipeline:DeleteWebhook` Required to delete a webhook\.  | 
|  [DeregisterWebhookWithThirdParty](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_DeregisterWebhookWithThirdParty.html)  |  `codepipeline:DeregisterWebhookWithThirdParty` Before a webhook is deleted, required to remove the connection between the webhook that was created by CodePipeline and the external tool with events to be detected\. Currently only supported for webhooks that target an action type of GitHub\.  | 
|  [DisableStageTransition](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_DisableStageTransition.html)  |  `codepipeline:DisableStageTransition` Required to prevent artifacts in a pipeline from transitioning to the next stage in the pipeline\.  | 
|  [EnableStageTransition](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_EnableStageTransition.html)  |  `codepipeline:EnableStageTransition` Required to enable artifacts in a pipeline to transition to a stage in a pipeline\.  | 
|  [GetJobDetails](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_GetJobDetails.html)  |  `codepipeline:GetJobDetails` Required to retrieve information about a job\. Only used for custom actions\.  | 
|  [GetPipeline](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_GetPipeline.html)  |  `codepipeline:GetPipeline` Required to retrieve the structure, stages, actions, and metadata of a pipeline, including the pipeline ARN\.  | 
|  [GetPipelineExecution](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_GetPipelineExecution.html)  |  `codepipeline:GetPipelineExecution` Required to retrieve information about an execution of a pipeline, including details about artifacts, the pipeline execution ID, and the name, version, and status of the pipeline\.  | 
|  [GetPipelineState](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_GetPipelineState.html)  |  `codepipeline:GetPipelineState` Required to retrieve information about the state of a pipeline, including the stages and actions\.  | 
|  [GetThirdPartyJobDetails](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_GetThirdPartyJobDetails.html)  |  `codepipeline:GetThirdPartyJobDetails` Required to request the details of a job for a third party action\. Only used for partner actions\.  | 
|  [ListActionTypes](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_ListActionTypes.html)  |  `codepipeline:ListActionTypes` Required to generate a summary of all AWS CodePipeline action types associated with your account\.  | 
|  [ListPipelineExecutions](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_ListPipelineExecutions.html)  |  `codepipeline:ListPipelineExecutions` Required to generate a summary of the most recent executions for a pipeline\.  | 
|  [ListPipelines](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_ListPipelines.html)  |  `codepipeline:ListPipelines` Required to generate a summary of all of the pipelines associated with your account\.  | 
| [ListWebhooks](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_ListWebhooks.html) |  `codepipeline:ListWebhooks` Required to list all of the webhooks in the account for that region\.  | 
|  [PollForJobs](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PollForJobs.html)  |  `codepipeline:PollForJobs` Required to get a listing of all the webhooks in this region for this account\.  | 
|  [PollForThirdPartyJobs](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PollForThirdPartyJobs.html)  |  `codepipeline:PollForThirdPartyJobs` Required to determine whether there are any third party jobs for a job worker to act on\. Only used for partner actions\.  | 
|  [PutActionRevision](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PutActionRevision.html)  |  `codepipeline:PutActionRevision` Required to report information to AWS CodePipeline about new revisions to a source  | 
|  [PutApprovalResult](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PutApprovalResult.html)  |  `codepipeline:PutApprovalResult` Required to report the response to a manual approval request to AWS CodePipeline\. Valid responses include Approved and Rejected\.  | 
|  [PutJobFailureResult](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PutJobFailureResult.html)  |  `codepipeline:PutJobFailureResult` Required to report the failure of a job as returned to the pipeline by a job worker\. Only used for custom actions\.  | 
|  [PutJobSuccessResult](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PutJobSuccessResult.html)  |  `codepipeline:PutJobSuccessResult` Required to report the success of a job as returned to the pipeline by a job worker\. Only used for custom actions\.  | 
|  [PutThirdPartyJobFailureResult](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PutThirdPartyJobFailureResult.html)  |  `codepipeline:PutThirdPartyJobFailureResult` Required to report the failure of a third party job as returned to the pipeline by a job worker\. Only used for partner actions\.  | 
|  [PutThirdPartyJobSuccessResult](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PutThirdPartyJobSuccessResult.html)  |  `codepipeline:PutThirdPartyJobSuccessResult` Required to report the success of a third party job as returned to the pipeline by a job worker\. Only used for partner actions\.   | 
|  [PutWebhook](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PutWebhook.html)  |  `codepipeline:PutWebhook` Required to create a webhook\.  | 
|  [RegisterWebhookWithThirdParty](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_RegisterWebhookWithThirdParty.html)  |  `codepipeline:RegisterWebhookWithThirdParty` After a webhook is created, required to configure supported third parties to call the generated webhook URL\.  | 
|  |  | 
|  [RetryStageExecution](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_RetryStageExecution.html)  |  `codepipeline:RetryStageExecution` Required to resume the pipeline execution by retrying the last failed actions in a stage\.  | 
|  [StartPipelineExecution](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_StartPipelineExecution.html)  |  `codepipeline:StartPipelineExecution` Required to start the specified pipeline\. Specifically, it begins processing the latest commit to the source location specified as part of the pipeline\.  | 
|  [UpdatePipeline](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_UpdatePipeline.html)  |  `codepipeline:UpdatePipeline` Required to update a specified pipeline with edits or changes to its structure\.  | 