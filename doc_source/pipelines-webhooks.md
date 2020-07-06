# Use webhooks to start a pipeline \(GitHub source\)<a name="pipelines-webhooks"></a>

A webhook is an HTTP notification that detects events in another tool, such as a GitHub repository, and connects those external events to a pipeline\.

When you use the console to create or edit a pipeline that has a GitHub source, CodePipeline creates a webhook\. CodePipeline deletes your webhook when you delete your pipeline\. You do not need to manage it in GitHub\. If you use the AWS CLI or AWS CloudFormation to create or edit a pipeline that has a GitHub source, you must use the information in these sections to manage webhooks yourself\.

**Topics**
+ [Create a webhook for a GitHub source](pipelines-webhooks-create.md)
+ [List webhooks in your account](pipelines-webhooks-view.md)
+ [Edit the webhook for your GitHub source](pipelines-webhooks-update.title.md)
+ [Delete the webhook for your GitHub source](pipelines-webhooks-delete.md)
+ [Tag a webhook in CodePipeline](tag-webhooks.md)
+ [Create a webhook for a GitHub source \(AWS CloudFormation template\)](pipelines-webhooks-create-cfn.md)