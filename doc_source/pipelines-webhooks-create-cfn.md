--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Create a Webhook That Starts Your GitHub Pipeline \(AWS CloudFormation Template\)<a name="pipelines-webhooks-create-cfn"></a>

To use AWS CloudFormation to create a webhook, update your template as described here\.<a name="proc-cfn-webhook-github"></a>

**To add parameters and create a webhook in your template**

1. In the template, under `Resources`, add your parameters:

------
#### [ YAML ]

   ```
   Parameters:
           GitHubOwner:
             Type: String
           GitHubSecret:
             Type: String
             NoEcho: true
           GitHubOAuthToken:
             Type: String
             NoEcho: true
   
   
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
           "GitHubSecret": {
               "Type": "String",
               "NoEcho": true
           },
           "GitHubOAuthToken": {
               "Type": "String",
               "NoEcho": true
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
           SecretToken: !Ref GitHubSecret
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
                       "SecretToken": {
                           "Ref": "GitHubSecret"
                       }
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
                  OAuthToken: !Ref GitHubOAuthToken
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
                                      "OAuthToken": {
                                          "Ref": "GitHubOAuthToken"
                                      },
                                      "PollForSourceChanges": false
                                  },
                                  "RunOrder": 1
                              }
  ```

------