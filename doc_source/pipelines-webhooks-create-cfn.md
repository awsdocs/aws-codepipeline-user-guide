# Create a webhook for a GitHub source \(AWS CloudFormation template\)<a name="pipelines-webhooks-create-cfn"></a>

To use AWS CloudFormation to create a webhook, update your template as described here\.<a name="proc-cfn-webhook-github"></a>

**To add parameters and create a webhook in your template**

We strongly recommend that you use AWS Secrets Manager to store your credentials\. If you use Secrets Manager, you must have already configured and stored your secret parameters in Secrets Manager\. This example uses dynamic references to AWS Secrets Manager for the GitHub credentials for your webhook\. For more information, see [ Using Dynamic References to Specify Template Values](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/dynamic-references.html#dynamic-references-secretsmanager)\. 
**Important**  
When passing secret parameters, do not enter the value directly into the template\. The value is rendered as plaintext and is therefore readable\. For security reasons, do not use plaintext in your AWS CloudFormation template to store your credentials\.

When you use the CLI or AWS CloudFormation to create a pipeline and add a webhook, you must disable periodic checks\.
**Note**  
To disable periodic checks, you must explicitly add the `PollForSourceChanges` parameter and set it to false, as detailed in the final procedure below\. Otherwise, the default for a CLI or AWS CloudFormation pipeline is that `PollForSourceChanges` defaults to true and does not display in the pipeline structure output\. For more information about PollForSourceChanges defaults, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.

1. In the template, under `Resources`, add your parameters:

------
#### [ YAML ]

   ```
   Parameters:
           GitHubOwner:
             Type: String
   
   ...
   ```

------
#### [ JSON ]

   ```
   {
     "Parameters": {
       "BranchName": {
         "Description": "GitHub branch name",
           "Type": "String",
             "Default": "master"
       },
       "GitHubOwner": {
         "Type": "String"
       },
   
   
   ...
   ```

------

1. Use the `AWS::CodePipeline::Webhook` AWS CloudFormation resource to add a webhook\.
**Note**  
The `TargetAction` you specify must match the `Name` property of the source action defined in the pipeline\.

   If `RegisterWithThirdParty` is set to `true`, make sure the user associated to the `OAuthToken` can set the required scopes in GitHub\. The token and webhook require the following GitHub scopes: 
   + `repo` \- used for full control to read and pull artifacts from public and private repositories into a pipeline\. 
   + `admin:repo_hook` \- used for full control of repository hooks\.

   Otherwise, GitHub returns a 404\. For more information about the 404 returned, see [https://help.github.com/articles/about-webhooks](https://help.github.com/articles/about-webhooks)\.

------
#### [ YAML ]

   ```
     AppPipelineWebhook:
       Type: AWS::CodePipeline::Webhook
       Properties:
         Authentication: GITHUB_HMAC
         AuthenticationConfiguration:
           SecretToken: {{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}
         Filters:
           - 
             JsonPath: "$.ref"
             MatchEquals: refs/heads/{Branch}
         TargetPipeline: !Ref AppPipeline
         TargetAction: SourceAction
         Name: AppPipelineWebhook
         TargetPipelineVersion: !GetAtt AppPipeline.Version
         RegisterWithThirdParty: true
   
   
   ...
   ```

------
#### [ JSON ]

   ```
     "AppPipelineWebhook": {
       "Type": "AWS::CodePipeline::Webhook",
       "Properties": {
         "Authentication": "GITHUB_HMAC",
         "AuthenticationConfiguration": {
           "SecretToken": "{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}"
         },
         "Filters": [
           {
             "JsonPath": "$.ref",
             "MatchEquals": "refs/heads/{Branch}"
           }
         ],
         "TargetPipeline": {
           "Ref": "AppPipeline"
         },
         "TargetAction": "SourceAction",
         "Name": "AppPipelineWebhook",
         "TargetPipelineVersion": {
           "Fn::GetAtt": [
             "AppPipeline",
             "Version"
           ]
         },
         "RegisterWithThirdParty": true
       }
     },
   
   ...
   ```

------

1. Save the updated template to your local computer, and then open the AWS CloudFormation console\. 

1. Choose your stack, and then choose **Create Change Set for Current Stack**\.

1. Upload the template, and then view the changes listed in AWS CloudFormation\. These are the changes to be made to the stack\. You should see your new resources in the list\. 

1. Choose **Execute**\.<a name="proc-cfn-flag-github"></a>

**To edit your pipeline's PollForSourceChanges parameter**
**Important**  
When you create a pipeline with this method, the `PollForSourceChanges` parameter defaults to true if it is not explicitly set to false\. When you add event\-based change detection, you must add the parameter to your output and set it to false to disable polling\. Otherwise, your pipeline starts twice for a single source change\. For details, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.
+ In the template, change `PollForSourceChanges` to `false`\. If you did not include `PollForSourceChanges` in your pipeline definition, add it and set it to false\.

  **Why am I making this change? ** Changing this parameter to `false` turns off periodic checks so you can use event\-based change detection only\.

------
#### [ YAML ]

  ```
            Name: Source
            Actions: 
              - 
                Name: SourceAction
                ActionTypeId: 
                  Category: Source
                  Owner: ThirdParty
                  Version: 1
                  Provider: GitHub
                OutputArtifacts: 
                  - Name: SourceOutput
                Configuration: 
                  Owner: !Ref GitHubOwner
                  Repo: !Ref RepositoryName
                  Branch: !Ref BranchName
                  OAuthToken: {{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}
                  PollForSourceChanges: false
                RunOrder: 1
  ```

------
#### [ JSON ]

  ```
   {
      "Name": "Source",
      "Actions": [
        {
          "Name": "SourceAction",
          "ActionTypeId": {
            "Category": "Source",
            "Owner": "ThirdParty",
            "Version": 1,
            "Provider": "GitHub"
          },
          "OutputArtifacts": [
            {
              "Name": "SourceOutput"
            }
          ],
          "Configuration": {
            "Owner": {
              "Ref": "GitHubOwner"
            },
            "Repo": {
              "Ref": "RepositoryName"
            },
            "Branch": {
              "Ref": "BranchName"
            },
            "OAuthToken": "{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}",
            "PollForSourceChanges": false
          },
          "RunOrder": 1
        }
  ```

------