# Delete the Webhook for Your GitHub Pipeline<a name="pipelines-webhooks-delete"></a>

To use the AWS CLI to delete a webhook:

1. Before you delete the webhook, you must deregister it\. Call the deregister\-webhook\-with\-third\-party command and include the \-\-webhook\-name parameter\.

   Use the following syntax:

   ```
   aws codepipeline deregister-webhook-with-third-party
   --webhook-name webhook_name
   ```

   Examples:

   The following sample command deregisters the webhook named "my\-webhook\."

   ```
   aws codepipeline deregister-webhook-with-third-party --webhook-name my-webhook
   ```

1. Call the delete\-webhook command and include the \-\-name parameter\.

   Use the following syntax:

   ```
   aws codepipeline delete-webhook
   --name "webhook_name"
   ```

   Examples:

   The following sample command deletes the webhook named "my\-webhook\."

   ```
   aws codepipeline delete-webhook --name my-webhook
   ```