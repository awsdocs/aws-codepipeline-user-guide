# Working with actions in CodePipeline<a name="actions"></a>

In AWS CodePipeline, an action is part of the sequence in a stage of a pipeline\. It is a task performed on the artifact in that stage\. Pipeline actions occur in a specified order, in sequence or in parallel, as determined in the configuration of the stage\.

CodePipeline provides support for six types of actions:
+ Source 
+ Build 
+ Test 
+ Deploy 
+ Approval 
+ Invoke 

For information about the AWS services and partner products and services you can integrate into your pipeline based on action type, see [Integrations with CodePipeline action types](integrations-action-type.md)\.

**Topics**
+ [Create a custom action for a pipeline](actions-create-custom-action.md)
+ [Tag a custom action in CodePipeline](customactions-tag.md)
+ [Invoke a Lambda function in a pipeline](actions-invoke-lambda-function.md)
+ [Retry a failed action](actions-retry.md)
+ [Manage approval actions in pipelines](approvals.md)
+ [Add a cross\-Region action to a pipeline](actions-create-cross-region.md)
+ [Working with variables](actions-variables.md)