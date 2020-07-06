# Edit the webhook for your GitHub source<a name="pipelines-webhooks-update.title"></a>

You can use the AWS CLI to edit the webhook for your repository\.
+ If you use the console to edit the GitHub source action for your pipeline, the webhook is updated for you \(and re\-registered, if appropriate\)\.
+ If you are not updating the webhook name, and you are not changing the GitHub repository, you can use the AWS CLI to update the webhook\. See Example 1\.
+ If you are changing the webhook name or GitHub repository name, you must edit the source action in the console or delete and recreate the webhook in the CLI\. After you create the webhook, you also register it\. See Example 2\.

**Example 1: To update a webhook secret**

1. In a text editor, edit the JSON file for the webhook you want to update\. This example modifies the sample file that was used to create the webhook in [Create a webhook for a GitHub source](pipelines-webhooks-create.md)\. This sample changes the secret token of the webhook named `"my-webhook"`\.

   ```
   {"webhook": 
     {"name": "my-webhook",
      "targetPipeline": "pipeline_name",
      "targetAction": "source_action_name",
      "filters": [
       {
         "jsonPath": "$.ref", 
         "matchEquals": "refs/heads/{Branch}"
       }
      ],
      "authentication": "GITHUB_HMAC",
      "authenticationConfiguration": {"SecretToken":"new_secret"}
     }
   }
   ```

1. Call the put\-webhook command and include the `--cli-input` and `--region` parameters\.

   The following sample command updates a webhook with the modified `"webhook_json"` JSON file\.

   ```
   aws codepipeline put-webhook --cli-input-json file://webhook_json.json --region "eu-central-1"
   ```

1. The output returns the webhook details and the new secret\.
**Note**  
You can edit the GitHub source action in the console\. This allows CodePipeline to manage webhooks for you\.

**Example 2: To update a webhook name or GitHub repository**

1. Use the steps in [Delete the webhook for your GitHub source](pipelines-webhooks-delete.md) to deregister and delete the existing webhook that is associated with the old webhook name or GitHub repository\.

1. Use the steps in [Create a webhook for a GitHub source](pipelines-webhooks-create.md) to recreate the webhook\.
**Note**  
You can edit the GitHub source action in the console\. This allows CodePipeline to manage webhooks for you\.