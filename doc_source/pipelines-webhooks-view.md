# List Webhooks in Your Account<a name="pipelines-webhooks-view"></a>

You can use the AWS CLI to list webhooks in your account\.

1. To list your webhooks, call the list\-webhooks command and include the \-\-endpoint\-url and \-\-region parameters\.

   Use the following syntax:

   ```
   aws codepipeline list-webhooks
   --endpoint-url "endpoint_URL" 
   --region region
   ```

   Examples:

   The following sample command lists webhooks for the "eu\-central\-1" endpoint URL\.

   ```
   aws codepipeline list-webhooks --endpoint-url "https://codepipeline.eu-central-1.amazonaws.com" --region "eu-central-1"
   ```

1. Webhooks are listed, including the name and ARN for each webhook\.

   ```
   {
       "webhooks": [
           {
               "url": "https://webhooks.domain.com/trigger111111111EXAMPLE11111111111111111": {
                   "authenticationConfiguration": {
                       "SecretToken": "Secret"
                   },
                   "name": "my-webhook",
                   "authentication": "GITHUB_HMAC",
                   "targetPipeline": "my-Pipeline",
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
       ]
   }
   ```

1. In GitHub, select your repository\.

1. Choose **Settings**\.

1. Choose **Webhooks**\. View the webhook information for your repository\.