# Working with pipelines in CodePipeline<a name="pipelines"></a>

To define an automated release process in AWS CodePipeline, you create a pipeline, which is a workflow construct that describes how software changes go through a release process\. A pipeline is composed of stages and actions that you configure\. 

**Note**  
When you add Build, Deploy, Test, or Invoke stages, in addition to the default options provided with CodePipeline, you can choose custom actions that you have already created for use with your pipelines\. Custom actions can be used for tasks such as running an internally developed build process or a test suite\. Version identifiers are included to help you distinguish among different versions of a custom action in the provider lists\. For more information, see [Create and add a custom action in CodePipeline](actions-create-custom-action.md)\.

Before you can create a pipeline, you must first complete the steps in [Getting started with CodePipeline](getting-started-codepipeline.md)\.

For more information about pipelines, see [CodePipeline concepts](concepts.md), [CodePipeline tutorials](tutorials.md), and, if you want to use the AWS CLI to create a pipeline, [CodePipeline pipeline structure reference](reference-pipeline-structure.md)\. To view a list of pipelines, see [View pipeline details and history in CodePipeline](pipelines-view.md)\.

**Topics**
+ [Start a pipeline execution](pipelines-about-starting.md)
+ [Stop a pipeline execution](pipelines-stop.md)
+ [Create a pipeline](pipelines-create.md)
+ [Edit a pipeline](pipelines-edit.md)
+ [View pipeline details and history](pipelines-view.md)
+ [Delete a pipeline](pipelines-delete.md)
+ [Create a pipeline that uses resources from another account](pipelines-create-cross-account.md)
+ [Edit a pipeline for push events](update-change-detection.md)
+ [Create the CodePipeline service role](pipelines-create-service-role.md)
+ [Tag a pipeline](pipelines-tag.md)
+ [Create a connection to Bitbucket](connections-pipelines.md)
+ [Create a notification rule](notification-rule-create.md)