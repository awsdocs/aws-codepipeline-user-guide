# Use Webhooks to Start a Pipeline \(GitHub Source\)<a name="pipelines-webhooks"></a>

A webhook is an HTTP notification that detects events in another tool, such as a GitHub repository, and connects those external events to a pipeline\.

When you use the console to create or edit a pipeline that has a GitHub source, CodePipeline creates a webhook\. CodePipeline deletes your webhook when you delete your pipeline\. You do not need to manage it in GitHub\. If you use the AWS CLI or AWS CloudFormation to create or edit a pipeline that has a GitHub source, you must use the information in these sections to manage webhooks yourself\.

**Topics**
+ [Create a Webhook for a GitHub Source](pipelines-webhooks-create.md)
+ [List Webhooks in Your Account](pipelines-webhooks-view.md)
+ [Edit the Webhook for Your GitHub Source](pipelines-webhooks-update.title.md)
+ [Delete the Webhook for Your GitHub Source](pipelines-webhooks-delete.md)
+ [Create a Webhook for a GitHub Source \(AWS CloudFormation Template\)](pipelines-webhooks-create-cfn.md)
+ [Configure Your Pipelines to Use Webhooks for Change Detection \(GitHub Source\)](pipelines-webhooks-migration.md)