# Create a Webhook for Your GitHub Pipeline<a name="pipelines-webhooks-create"></a>

You can use the AWS CLI to manually create a webhook\. Next, you must register the webhook in GitHub\. A designated AWS endpoint is used for the webhook and is supplied by the put\-webhook command\.

To use the AWS CLI to create a webhook, call the put\-webhook command and supply the following:
+ A name that uniquely identifies the webhook\. This name must be unique within the region of the account for the pipeline\.
+ A secret in the JSON file to be used for GitHub authorization\.

1. In a text editor, create and save a JSON file for the webhook you want to create\. Use this sample file for a webhook named "my\-webhook:"

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
      "authenticationConfiguration": {"SecretToken":"secret"}
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

   The following sample command creates a webhook with the "webhook\_json" JSON file\.

   ```
   aws codepipeline put-webhook --cli-input-json file://webhook_json.json --region "eu-central-1"
   ```

1. In the output shown in this example for a webhook named "my\-webhook," the URL and ARN are returned\.

   ```
   {
       "webhook": {
           "url": "https://webhooks.domain.com/trigger111111111EXAMPLE11111111111111111",
           "definition": {
               "authenticationConfiguration": {
                   "SecretToken": "secret"
               },
               "name": "my-webhook",
               "authentication": "GITHUB_HMAC",
               "targetPipeline": "pipeline_name",
               "targetAction": "Source",
               "filters": [
                   {
                       "jsonPath": "$.ref",
                       "matchEquals": "refs/heads/{Branch}"
                   }
               ]
           },
           "arn": "arn:aws:codepipeline:eu-central-1:ACCOUNT_ID:webhook:my-webhook"
       }
   }
   ```

1. Call the register\-webhook\-with\-third\-party command and include the \-\-webhook\-name parameter\.

   Use the following syntax:

   ```
   aws codepipeline register-webhook-with-third-party
   --webhook-name webhook_name
   ```

   Examples:

   The following sample command registers a webhook with the name "my\-webhook\."

   ```
   aws codepipeline register-webhook-with-third-party --webhook-name my-webhook
   ```