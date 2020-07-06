# Delete the webhook for your GitHub source<a name="pipelines-webhooks-delete"></a>

To use the AWS CLI to delete a webhook:

1. You must deregister the webhook before you delete it\. Call the deregister\-webhook\-with\-third\-party command and include the `--webhook-name` parameter\.

   The following sample command deregisters the webhook named `"my-webhook"`\.

   ```
   aws codepipeline deregister-webhook-with-third-party --webhook-name my-webhook
   ```

1. Call the delete\-webhook command and include the `--name` parameter\.

   The following sample command deletes the webhook named `"my-webhook"`\.

   ```
   aws codepipeline delete-webhook --name my-webhook
   ```