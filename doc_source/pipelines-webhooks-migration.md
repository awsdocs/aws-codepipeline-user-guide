# Configure Your Pipelines to Use Webhooks for Change Detection \(GitHub Source\)<a name="pipelines-webhooks-migration"></a>

You can now use webhooks as the change detection method for your pipelines with a GitHub repository\. You must update existing pipelines to use this feature\. When you use the console to create or edit a pipeline, CodePipeline creates the webhook for you\.

This table includes procedures for configuring pipelines with a GitHub source to use webhooks\.


****  

| Method | Instructions | Further action required? | 
| --- | --- | --- | 
| Configuring pipelines in the console |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-webhooks-migration.html) See [Edit a Pipeline \(Console\)](pipelines-edit.md#pipelines-edit-console)\.  |  The webhook is created for you and registered with GitHub\.  No further action is required\.  | 
| Configuring pipelines in the CLI |  Use the update\-pipeline command to set the `PollForSourceChanges` parameter to `false`\. See [Edit a Pipeline \(AWS CLI\)](pipelines-edit.md#pipelines-edit-cli)\.  |  You must create the webhook and register it with GitHub\. See [Create a Webhook for a GitHub Source](pipelines-webhooks-create.md)\.  | 
| Configuring pipelines in AWS CloudFormation |  Retain periodic checking for your GitHub source repository\.  |  For information about the webhook resource in AWS CloudFormation, see [AWS::CodePipeline::Webhook](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-codepipeline-webhook.html)\.  | 