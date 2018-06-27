# Update the Webhook for Your GitHub Pipeline<a name="pipelines-webhooks-update.title"></a>

You can use the AWS CLI to edit the webhook for your repository\. Depending on the way you update a webhook, you use one of the following methods: 
+ If you use the console to edit the GitHub source action for your pipeline, then the webhook is updated for you \(and reregistered if appropriate\)\.
+ If you are not updating the webhook name, and you are not changing the GitHub repository, you can use the AWS CLI to update the webhook\. An example of this is provided below in a procedure to update a webhook secret\. See Example 1\.
+ If you are changing the webhook name or GitHub repository name, you must edit the source action in the console or delete and recreate the webhook in the CLI\. After you create the new webhook, you also register it\. See Example 2\.

**Example 1: To update a webhook secret using the CLI**

1. In a text editor, modify the JSON file for the webhook you want to update\. This example modifies the sample file that was used to create the webhook in [Create a Webhook for Your GitHub Pipeline](pipelines-webhooks-create.md)\. This sample is updating a webhook named "my\-webhook" to change the secret token\.

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

1. Call the put\-webhook command and include the \-\-cli\-input and \-\-region parameters\.

   Use the following syntax:

   ```
   aws codepipeline put-webhook
   --cli-input-json file:"file_name" 
   --region region
   ```

   Examples:

   The following sample command updates a webhook with the modified "webhook\_json" JSON file\.

   ```
   aws codepipeline put-webhook --cli-input-json file://webhook_json.json --region "eu-central-1"
   ```

1. The output returns the webhook details and shows the new secret\.
**Note**  
You can edit the GitHub source action in the console\. This allows AWS CodePipeline to manage webhooks for you\.

**Example 2: To update a webhook name or GitHub repository using the CLI**

1. Deregister and delete the existing webhook that is associated to the old webhook name or GitHub repository using the steps in [Delete the Webhook for Your GitHub Pipeline](pipelines-webhooks-delete.md)\.

1. Recreate the webhook by creating and registering a new webhook using the steps in [Create a Webhook for Your GitHub Pipeline](pipelines-webhooks-create.md)\.
**Note**  
You can edit the GitHub source action in the console\. This allows AWS CodePipeline to manage webhooks for you\.