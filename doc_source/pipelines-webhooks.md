# Start a GitHub Pipeline Automatically Using Webhooks<a name="pipelines-webhooks"></a>

A webhook is an HTTP notification that detects events in another tool, such as a GitHub repository, and connects those external events to a pipeline\.

AWS CodePipeline creates a webhook when you use the console to create or edit your GitHub pipeline and deletes your webhook when you delete your pipeline\. You do not need to manually manage it in GitHub\. If you use the AWS CLI or AWS CloudFormation to create or edit a GitHub pipeline, you must use the information in these sections to manage webhooks yourself\.

**Topics**
+ [Create a Webhook for Your GitHub Pipeline](pipelines-webhooks-create.md)
+ [List Webhooks in Your Account](pipelines-webhooks-view.md)
+ [Update the Webhook for Your GitHub Pipeline](pipelines-webhooks-update.title.md)
+ [Delete the Webhook for Your GitHub Pipeline](pipelines-webhooks-delete.md)
+ [Configure Your GitHub Pipelines to Use Webhooks for Change Detection](pipelines-webhooks-migration.md)