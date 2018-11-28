--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Use Webhooks to Start a GitHub Pipeline<a name="pipelines-webhooks"></a>

A webhook is an HTTP notification that detects events in another tool, such as a GitHub repository, and connects those external events to a pipeline\.

When you use the console to create or edit your GitHub pipeline, AWS CodePipeline creates a webhook\. AWS CodePipeline deletes your webhook when you delete your pipeline\. You do not need to manage it in GitHub\. If you use the AWS CLI or AWS CloudFormation to create or edit a GitHub pipeline, you must use the information in these sections to manage webhooks yourself\.

**Topics**
+ [Create a Webhook for Your GitHub Pipeline](pipelines-webhooks-create.md)
+ [List Webhooks in Your Account](pipelines-webhooks-view.md)
+ [Edit the Webhook for Your GitHub Pipeline](pipelines-webhooks-update.title.md)
+ [Delete the Webhook for Your GitHub Pipeline](pipelines-webhooks-delete.md)
+ [Create a CloudWatch Events Rule That Starts Your GitHub Pipeline \(AWS CloudFormation Template\)](pipelines-webhooks-create-cfn.md)
+ [Configure Your GitHub Pipelines to Use Webhooks for Change Detection](pipelines-webhooks-migration.md)