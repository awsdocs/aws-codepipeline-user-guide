# Working with Actions in AWS CodePipeline<a name="actions"></a>

In AWS CodePipeline, an action is part of the sequence in a stage of a pipeline\. It is a task performed on the artifact in that stage\. Pipeline actions occur in a specified order, in sequence or in parallel, as determined in the configuration of the stage\.

AWS CodePipeline provides support for six types of actions:

+ Source 

+ Build 

+ Test 

+ Deploy 

+ Approval 

+ Invoke 

For information about the AWS services and partner products and services you can integrate into your pipeline based on action type, see [Integrations with AWS CodePipeline Action Types](integrations-action-type.md)\.


+ [Create a Custom Action for a Pipeline](actions-create-custom-action.md)
+ [Invoke a Lambda Function in a Pipeline](actions-invoke-lambda-function.md)
+ [Retry a Failed Action](actions-retry.md)
+ [Manage Approval Actions in Pipelines](approvals.md)