# Appendix A: GitHub version 1 source actions<a name="appendix-github-oauth"></a>

This appendix provides information about version 1 of the GitHub action in CodePipeline\.

**Note**  
The GitHub version 1 action is not recommended and will soon no longer be supported\. For a pipeline with a GitHub version 1 action, CodePipeline uses OAuth\-based tokens to connect to your GitHub repository\. By contrast, the GitHub action \(version 2\) uses a connection resource to associate AWS resources to your GitHub repository\. The connection resource uses app\-based tokens to connect\. For more information about updating your pipeline to the recommended GitHub action that uses a connection, see [Update a GitHub version 1 source action to a GitHub version 2 source action](update-github-action-connections.md)\. For more information about OAuth\-based GitHub access in contrast to app\-based GitHub access, see [https://docs.github.com/en/developers/apps/differences-between-github-apps-and-oauth-apps](https://docs.github.com/en/developers/apps/differences-between-github-apps-and-oauth-apps)\.

To integrate with GitHub, CodePipeline uses a GitHub OAuth application for your pipeline\. CodePipeline uses webhooks to manage change detection for your pipeline with the GitHub version 1 source action\.

This reference contains the following sections for the GitHub version 1 action:
+ For information about how to add a GitHub version 1 source action and webhook to a pipeline, see [Adding a GitHub version 1 source action](#appendix-github-methods)\.
+ For information about the configuration parameters and example YAML/JSON snippets for a GitHub version 1 source action, see [GitHub version 1 source action structure reference](#action-reference-GitHub)\.
+ For information about how to manage webhook for a GitHub version 1 source action, see [Use webhooks to start a pipeline \(GitHub version 1 source actions\)](#pipelines-webhooks)\. The GitHub version 1 source action uses webhooks for source change detection to trigger pipelines\. This appendix provides details for managing your GitHub version 1 source action webhooks as follows:
  + Create and manage webhooks
  + Manage webhooks in your CLI and AWS CloudFormation pipelines
  + Migrate GitHub pipelines from polling to webhooks
+ For information about how to manage the OAuth app or personal access token for a GitHub version 1 source action, see [Configure authentication \(GitHub version 1 source actions\)](#GitHub-authentication)\. The GitHub GitHub version 1 source action uses OAuth\-based apps for authorization between CodePipeline and your repository\. This appendix provides details for managing your OAuth apps as follows:
  + Manage OAuth apps
  + Manage your Personal Access Token
  + Manage your token with the CLI or AWS CloudFormation
+ For a sample AWS CloudFormation template to create a resource stack for a pipeline with a GitHub GitHub version 1 source action and webhook, see [Tutorial: Create a pipeline with AWS CloudFormation \(GitHub version 1 source actions\)](#tutorials-cloudformation-github)\.
+ For troubleshooting procedures related to the GitHub version 1 source action, see [Troubleshooting \(GitHub version 1 source actions\)](#troubleshooting-gs)\.

**Topics**
+ [Adding a GitHub version 1 source action](#appendix-github-methods)
+ [GitHub version 1 source action structure reference](#action-reference-GitHub)
+ [Use webhooks to start a pipeline \(GitHub version 1 source actions\)](#pipelines-webhooks)
+ [Update a polling pipeline for a GitHub version 1 source action](#update-change-detection-for-github)
+ [Configure authentication \(GitHub version 1 source actions\)](#GitHub-authentication)
+ [Tutorial: Create a pipeline with AWS CloudFormation \(GitHub version 1 source actions\)](#tutorials-cloudformation-github)
+ [Troubleshooting \(GitHub version 1 source actions\)](#troubleshooting-gs)

## Adding a GitHub version 1 source action<a name="appendix-github-methods"></a>

You add GitHub version 1 source actions to CodePipeline by: 
+ Using the CodePipeline console **Create pipeline** wizard \([Create a pipeline \(console\)](pipelines-create.md#pipelines-create-console)\) or **Edit action** page to choose the **GitHub** provider option\. The console creates a webhook that starts your pipeline when the source changes\.
+ Using the CLI to add the action configuration for the `GitHub` action and creating additional resources as follows:
  + Using the `GitHub` example action configuration in [GitHub version 1 source action structure reference](#action-reference-GitHub) to create the action as shown in [Create a pipeline \(CLI\)](pipelines-create.md#pipelines-create-cli)\.
  + Disabling periodic checks and creating the change detection manually, because the change detection method defaults to starting the pipeline by polling the source\. You can use one of the following methods: [Create a webhook for a GitHub source](#pipelines-webhooks-create) or [Create a webhook for a GitHub source \(AWS CloudFormation template\) ](#pipelines-webhooks-create-cfn)\.

## GitHub version 1 source action structure reference<a name="action-reference-GitHub"></a>

**Note**  
The GitHub version 1 source action is not recommended and will soon no longer be supported\. For a pipeline with a GitHub GitHub version 1 source action, CodePipeline uses OAuth\-based tokens to connect to your GitHub repository\. By contrast, the new GitHub action \(version 2\) uses a connection resource to associate AWS resources to your GitHub repository\. The connection resource uses app\-based tokens to connect\. For more information about updating your pipeline to the recommended GitHub action that uses a connection, see [Update a GitHub version 1 source action to a GitHub version 2 source action](update-github-action-connections.md)\.

Triggers the pipeline when a new commit is made on the configured GitHub repository and branch\.

To integrate with GitHub, CodePipeline uses an OAuth application or a personal access token for your pipeline\. If you use the console to create or edit your pipeline, CodePipeline creates a GitHub webhook that starts your pipeline when a change occurs in the repository\.

You must have already created a GitHub account and repository before you connect the pipeline through a GitHub action\.

If you want to limit the access CodePipeline has to repositories, create a GitHub account and grant the account access only to those repositories you want to integrate with CodePipeline\. Use that account when you configure CodePipeline to use GitHub repositories for source stages in pipelines\.

For more information, see the [GitHub developer documentation](https://developer.github.com) on the GitHub website\.

**Topics**
+ [Action type](#action-reference-GitHub-type)
+ [Configuration parameters](#action-reference-GitHub-config)
+ [Input artifacts](#action-reference-GitHub-input)
+ [Output artifacts](#action-reference-GitHub-output)
+ [Output variables](#action-reference-GitHub-variables)
+ [Action declaration \(GitHub example\)](#action-reference-GitHub-example)
+ [Connecting to GitHub \(OAuth\)](#action-reference-GitHub-auth)
+ [See also](#action-reference-GitHub-links)

### Action type<a name="action-reference-GitHub-type"></a>
+ Category: `Source`
+ Owner: `ThirdParty`
+ Provider: `GitHub`
+ Version: `1`

### Configuration parameters<a name="action-reference-GitHub-config"></a>

**Owner**  
Required: Yes  
The name of the GitHub user or organization who owns the GitHub repository\.

**Repo**  
Required: Yes  
The name of the repository where source changes are to be detected\.

**Branch**  
Required: Yes  
The name of the branch where source changes are to be detected\.

**OAuthToken**  
Required: Yes  
Represents the GitHub authentication token that allows CodePipeline to perform operations on your GitHub repository\. The entry is always displayed as a mask of four asterisks\. It represents one of the following values:  
+ When you use the console to create the pipeline, CodePipeline uses an OAuth token to register the GitHub connection\.
+ When you use the AWS CLI to create the pipeline, you can pass your GitHub personal access token in this field\. Replace the asterisks \(\*\*\*\*\) with your personal access token copied from GitHub\. When you run `get-pipeline` to view the action configuration, the four\-asterisk mask is displayed for this value\.
+ When you use an AWS CloudFormation template to create the pipeline, you must first store the token as a secret in AWS Secrets Manager\. You include the value for this field as a dynamic reference to the stored secret in Secrets Manager, such as `{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}`\.
For more information about GitHub scopes, see the [GitHub Developer API Reference](https://developer.github.com/v3/oauth/#scopes) on the GitHub website\.

**PollForSourceChanges**  
Required: No  
`PollForSourceChanges` controls whether CodePipeline polls the GitHub repository for source changes\. We recommend that you use webhooks to detect source changes instead\. For more information about configuring webhooks, see [Update pipelines for push events \(GitHub version 1 source actions\) \(CLI\)](#update-change-detection-cli-github) or [Update pipelines for push events \(GitHub version 1 source actions\) \(AWS CloudFormation template\)](#update-change-detection-cfn-github)\.  
If you intend to configure webhooks, you must set `PollForSourceChanges` to `false` to avoid duplicate pipeline executions\.
Valid values for this parameter:  
+ `True`: If set, CodePipeline polls your repository for source changes\.
**Note**  
If you omit `PollForSourceChanges`, CodePipeline defaults to polling your repository for source changes\. This behavior is the same as if `PollForSourceChanges` is set to `true`\.
+ `False`: If set, CodePipeline does not poll your repository for source changes\. Use this setting if you intend to configure a webhook to detect source changes\.

### Input artifacts<a name="action-reference-GitHub-input"></a>
+ **Number of Artifacts:** `0`
+ **Description:** Input artifacts do not apply for this action type\.

### Output artifacts<a name="action-reference-GitHub-output"></a>
+ **Number of Artifacts:** `1` 
+ **Description:** The output artifact of this action is a ZIP file that contains the contents of the configured repository and branch at the commit specified as the source revision for the pipeline execution\. The artifacts generated from the repository are the output artifacts for the GitHub action\. The source code commit ID is displayed in CodePipeline as the source revision for the triggered pipeline execution\.

### Output variables<a name="action-reference-GitHub-variables"></a>

When configured, this action produces variables that can be referenced by the action configuration of a downstream action in the pipeline\. This action produces variables which can be viewed as output variables, even if the action doesn't have a namespace\. You configure an action with a namespace to make those variables available to the configuration of downstream actions\.

For more information about variables in CodePipeline, see [Variables](reference-variables.md)\.

**CommitId**  
The GitHub commit ID that triggered the pipeline execution\. Commit IDs are the full SHA of the commit\.

**CommitMessage**  
The description message, if any, associated with the commit that triggered the pipeline execution\.

**CommitUrl**  
The URL address for the commit that triggered the pipeline\.

**RepositoryName**  
The name of the GitHub repository where the commit that triggered the pipeline was made\.

**BranchName**  
The name of the branch for the GitHub repository where the source change was made\.

**AuthorDate**  
The date when the commit was authored, in timestamp format\.  
For more information about the difference between an author and a committer in Git, see [Viewing the Commit History](http://git-scm.com/book/ch2-3.html) in Pro Git by Scott Chacon and Ben Straub\.

**CommitterDate**  
The date when the commit was committed, in timestamp format\.  
For more information about the difference between an author and a committer in Git, see [Viewing the Commit History](http://git-scm.com/book/ch2-3.html) in Pro Git by Scott Chacon and Ben Straub\.

### Action declaration \(GitHub example\)<a name="action-reference-GitHub-example"></a>

------
#### [ YAML ]

```
Name: Source
Actions:
  - InputArtifacts: []
    ActionTypeId:
      Version: '1'
      Owner: ThirdParty
      Category: Source
      Provider: GitHub
    OutputArtifacts:
      - Name: SourceArtifact
    RunOrder: 1
    Configuration:
      Owner: MyGitHubAccountName
      Repo: MyGitHubRepositoryName
      PollForSourceChanges: 'false'
      Branch: master
      OAuthToken: '{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}'
    Name: ApplicationSource
```

------
#### [ JSON ]

```
{
    "Name": "Source",
    "Actions": [
        {
            "InputArtifacts": [],
            "ActionTypeId": {
                "Version": "1",
                "Owner": "ThirdParty",
                "Category": "Source",
                "Provider": "GitHub"
            },
            "OutputArtifacts": [
                {
                    "Name": "SourceArtifact"
                }
            ],
            "RunOrder": 1,
            "Configuration": {
                "Owner": "MyGitHubAccountName",
                "Repo": "MyGitHubRepositoryName",
                "PollForSourceChanges": "false",
                "Branch": "master",
                "OAuthToken": "{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}"
            },
            "Name": "ApplicationSource"
        }
    ]
},
```

------

### Connecting to GitHub \(OAuth\)<a name="action-reference-GitHub-auth"></a>

The first time you use the console to add a GitHub repository to a pipeline, you are asked to authorize CodePipeline access to your repositories\. The token requires the following GitHub scopes:
+ The `repo` scope, which is used for full control to read and pull artifacts from public and private repositories into a pipeline\.
+ The `admin:repo_hook` scope, which is used for full control of repository hooks\.

When you use the CLI or an AWS CloudFormation template, you must provide the value for a personal access token that you have already created in GitHub\.

To view the CodePipeline OAuth applications for your pipeline, see [View your authorized OAuth apps](#GitHub-view-oauth-token)\.

To create and manage GitHub personal access tokens, see [Configure your pipeline to use a personal access token \(GitHub and CLI\)](#GitHub-create-personal-token-CLI)\.

### See also<a name="action-reference-GitHub-links"></a>

The following related resources can help you as you work with this action\.
+ Resource reference for the [AWS CloudFormation User Guide AWS::CodePipeline::Webhook](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-codepipeline-webhook.html) – This includes field definitions, examples, and snippets for the resource in AWS CloudFormation\.
+ Resource reference for the [AWS CloudFormation User Guide AWS::CodeStar::GitHubRepository](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-codestar-githubrepository.html) – This includes field definitions, examples, and snippets for the resource in AWS CloudFormation\.
+ [Tutorial: Create a pipeline that builds and tests your Android app with AWS Device Farm](tutorials-codebuild-devicefarm.md) – This tutorial provides a sample build spec file and sample application to create a pipeline with a GitHub source\. It builds and tests an Android app with CodeBuild and AWS Device Farm\.

## Use webhooks to start a pipeline \(GitHub version 1 source actions\)<a name="pipelines-webhooks"></a>

A webhook is an HTTP notification that detects events in another tool, such as a GitHub repository, and connects those external events to a pipeline\.

When you use the console to create or edit a pipeline that has a GitHub source, CodePipeline creates a webhook\. CodePipeline deletes your webhook when you delete your pipeline\. You do not need to manage it in GitHub\. If you use the AWS CLI or AWS CloudFormation to create or edit a pipeline that has a GitHub source, you must use the information in these sections to manage webhooks yourself\.

**Topics**
+ [Create a webhook for a GitHub source](#pipelines-webhooks-create)
+ [List webhooks in your account](#pipelines-webhooks-view)
+ [Edit the webhook for your GitHub source](#pipelines-webhooks-update.title)
+ [Delete the webhook for your GitHub source](#pipelines-webhooks-delete)
+ [Tag a webhook in CodePipeline](#tag-webhooks)
+ [Create a webhook for a GitHub source \(AWS CloudFormation template\)](#pipelines-webhooks-create-cfn)

### Create a webhook for a GitHub source<a name="pipelines-webhooks-create"></a>

After you use the AWS CLI to manually create a webhook, you must register the webhook in GitHub\. A designated AWS endpoint is used for the webhook and is supplied by the put\-webhook command\.

**Important**  
If you use the console to create or edit your pipeline, your webhook is created for you\.

To use the AWS CLI to create a webhook, call the put\-webhook command and supply the following:
+ A name that uniquely identifies the webhook\. This name must be unique within the region of the account for the pipeline\.
+ A secret in the JSON file to be used for GitHub authorization\.<a name="proc-cli-gh-webhook"></a>

**To create and register your webhook**
**Note**  
When you use the CLI or AWS CloudFormation to create a pipeline and add a webhook, you must disable periodic checks\. To disable periodic checks, you must explicitly add the `PollForSourceChanges` parameter and set it to false, as detailed in the final procedure below\. Otherwise, the default for a CLI or AWS CloudFormation pipeline is that `PollForSourceChanges` defaults to true and does not display in the pipeline structure output\. For more information about PollForSourceChanges defaults, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.

1. In a text editor, create and save a JSON file for the webhook you want to create\. Use this sample file for a webhook named `my-webhook`:

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

1. Call the put\-webhook command and include the `--cli-input` and `--region` parameters\.

   The following sample command creates a webhook with the `webhook_json` JSON file\.

   ```
   aws codepipeline put-webhook --cli-input-json file://webhook_json.json --region "eu-central-1"
   ```

1. In the output shown in this example, the URL and ARN are returned for a webhook named `my-webhook`\.

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
       },
       "tags": [{
         "key": "Project",
         "value": "ProjectA"
       }]
   }
   ```

   This example adds tagging to the webhook by including the `Project` tag key and `ProjectA` value on the webhook\. For more information about tagging resources in CodePipeline, see [Tagging resources](tag-resources.md)\.

1. Call the register\-webhook\-with\-third\-party command and include the `--webhook-name` parameter\.

   The following sample command registers a webhook named `my-webhook`\.

   ```
   aws codepipeline register-webhook-with-third-party --webhook-name my-webhook
   ```

If you are updating a pipeline to use webhooks, you must also use the following procedure to turn off periodic checks\.<a name="proc-cli-flag-github"></a>

**To edit your pipeline's PollForSourceChanges parameter**
**Important**  
When you create a pipeline with this method, the `PollForSourceChanges` parameter defaults to true if it is not explicitly set to false\. When you add event\-based change detection, you must add the parameter to your output and set it to false to disable polling\. Otherwise, your pipeline starts twice for a single source change\. For details, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.

1. Run the get\-pipeline command to copy the pipeline structure into a JSON file\. For example, for a pipeline named `MyFirstPipeline`, you would type the following command: 

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   This command returns nothing, but the file you created should appear in the directory where you ran the command\.

1. Open the JSON file in any plain\-text editor and edit the source stage by changing or adding the `PollForSourceChanges` parameter\. In this example, for a repository named `UserGitHubRepo`, the parameter is set to `false` \.

   **Why am I making this change? ** Changing this parameter turns off periodic checks so you can use event\-based change detection only\.

   ```
   "configuration": {
     "Owner": "darlaker",
     "Repo": "UserGitHubRepo",
     "PollForSourceChanges": "false",
     "Branch": "master",
     "OAuthToken": "****"
     },
   ```

1. If you are working with the pipeline structure retrieved using the get\-pipeline command, you must edit the structure in the JSON file by removing the `metadata` lines from the file\. Otherwise, the update\-pipeline command cannot use it\. Remove the `"metadata"` section from the pipeline structure in the JSON file, including the : `{ }` and the `"created"`, `"pipelineARN"`, and `"updated"` fields\.

   For example, remove the following lines from the structure: 

   ```
   "metadata": {  
     "pipelineArn": "arn:aws:codepipeline:region:account-ID:pipeline-name",
     "created": "date",
     "updated": "date"
     }
   ```

   Save the file\.

1. To apply your changes, run the update\-pipeline command, specifying the pipeline JSON file, similar to the following:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline update-pipeline --cli-input-json file://pipeline.json
   ```

   This command returns the entire structure of the edited pipeline\.
**Note**  
The update\-pipeline command stops the pipeline\. If a revision is being run through the pipeline when you run the update\-pipeline command, that run is stopped\. You must manually start the pipeline to run that revision through the updated pipeline\. Use the start\-pipeline\-execution command to manually start your pipeline\.

### List webhooks in your account<a name="pipelines-webhooks-view"></a>

You can use the AWS CLI to list webhooks in your account\.

**To list webhooks in your account**

1. To list your webhooks, call the list\-webhooks command and include the `--endpoint-url` and `--region` parameters\.

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

1. In GitHub, choose your repository\.

1. Choose **Settings**, and then choose **Webhooks**\.

   View the webhook information for your repository\.

### Edit the webhook for your GitHub source<a name="pipelines-webhooks-update.title"></a>

You can use the AWS CLI to edit the webhook for your repository\.
+ If you use the console to edit the GitHub source action for your pipeline, the webhook is updated for you \(and re\-registered, if appropriate\)\.
+ If you are not updating the webhook name, and you are not changing the GitHub repository, you can use the AWS CLI to update the webhook\. See Example 1\.
+ If you are changing the webhook name or GitHub repository name, you must edit the source action in the console or delete and recreate the webhook in the CLI\. After you create the webhook, you also register it\. See Example 2\.

**Example 1: To update a webhook secret**

1. In a text editor, edit the JSON file for the webhook you want to update\. This example modifies the sample file that was used to create the webhook in [Create a webhook for a GitHub source](#pipelines-webhooks-create)\. This sample changes the secret token of the webhook named `"my-webhook"`\.

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

1. Use the steps in [Delete the webhook for your GitHub source](#pipelines-webhooks-delete) to deregister and delete the existing webhook that is associated with the old webhook name or GitHub repository\.

1. Use the steps in [Create a webhook for a GitHub source](#pipelines-webhooks-create) to recreate the webhook\.
**Note**  
You can edit the GitHub source action in the console\. This allows CodePipeline to manage webhooks for you\.

### Delete the webhook for your GitHub source<a name="pipelines-webhooks-delete"></a>

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

### Tag a webhook in CodePipeline<a name="tag-webhooks"></a>

You can apply tags to your webhooks in CodePipeline\. Tags are key\-value pairs associated with AWS resources\. For information about CodePipeline resource tagging, use cases, tag key and value constraints, and supported resource types, see [Tagging resources](tag-resources.md)\.

You can specify tags when you create a webhook\. You can add, remove, and update the values of tags in a webhook\. You can add up to 50 tags to each webhook\.

**Topics**
+ [Add tags to an existing webhook](#tag-webhooks-add)
+ [View tags for a webhook](#tag-webhooks-list)
+ [Edit tags for a webhook](#tag-webhooks-update)
+ [Remove tags for a webhook](#tag-webhooks-delete)

#### Add tags to an existing webhook<a name="tag-webhooks-add"></a>

Follow these steps to use the AWS CLI to add a tag to a webhook\. To add a tag to a webhook when you create it, see [Create a webhook for a GitHub source](#pipelines-webhooks-create)\.

In these steps, we assume that you have already installed a recent version of the AWS CLI or updated to the current version\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

At the terminal or command line, run the tag\-resource command, specifying the Amazon Resource Name \(ARN\) of the webhook where you want to add tags and the key and value of the tag you want to add\. You can add more than one tag to a webhook\. For example, to tag a webhook named *MyWebhook* with two tags, a tag key named *Project* with the tag value of *NewProject*, and a tag key named *ApplicationName* with the tag value of *MyApplication*:

```
aws codepipeline tag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:webhook:MyWebhook --tags key=Project,value=NewProject key=ApplicationName,value=MyApplication
```

If successful, this command returns nothing\.

#### View tags for a webhook<a name="tag-webhooks-list"></a>

Follow these steps to use the AWS CLI to view the AWS tags for a webhook\. If no tags have been added, the returned list is empty\.

At the terminal or command line, run the list\-tags\-for\-resource command\. For example, to view a list of tag keys and tag values for a webhook named *MyWebhook* with the ARN `arn:aws:codepipeline:us-west-2:account-id:webhook:MyWebhook`:

```
aws codepipeline list-tags-for-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:webhook:MyWebhook
```

If successful, this command returns information similar to the following:

```
{
    "tags": {
        "Project": "NewProject",
        "ApplicationName": "MyApplication"
    }
}
```

#### Edit tags for a webhook<a name="tag-webhooks-update"></a>

Follow these steps to use the AWS CLI to update a tag for a webhook\. You can change the value for an existing key or add another key\. You can also remove tags from a webhook, as shown in the next section\.

At the terminal or command line, run the tag\-resource command, specifying the ARN of the webhook where you want to update a tag and specify the tag key and tag value:

```
aws codepipeline tag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:webhook:MyWebhook --tags key=Project,value=UpdatedProject
```

#### Remove tags for a webhook<a name="tag-webhooks-delete"></a>

Follow these steps to use the AWS CLI to remove a tag from a webhook\. When you remove tags from the associated resource, the tags are deleted\.

**Note**  
If you delete a webhook, all tag associations are removed from the webhook\. You do not have to remove tags before you delete a webhook\.

At the terminal or command line, run the untag\-resource command, specifying the ARN of the webhook where you want to remove tags and the tag key of the tag you want to remove\. For example, to remove a tag on a webhook named *MyWebhook* with the tag key *Project*:

```
aws codepipeline untag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:webhook:MyWebhook --tag-keys Project
```

If successful, this command returns nothing\. To verify the tags associated with the webhook, run the list\-tags\-for\-resource command\.

### Create a webhook for a GitHub source \(AWS CloudFormation template\)<a name="pipelines-webhooks-create-cfn"></a>

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

## Update a polling pipeline for a GitHub version 1 source action<a name="update-change-detection-for-github"></a>

Update a polling pipeline for a GitHub version 1 source action

### Update pipelines for push events \(GitHub version 1 source actions\) \(console\)<a name="update-change-detection-console-github"></a>

You can use the CodePipeline console to update your pipeline to use webhooks to detect changes in your CodeCommit source repository\.

Follow these steps to edit a pipeline that is using polling \(periodic checks\) to use Amazon CloudWatch Events instead\. If you want to create a pipeline, see [Create a pipeline in CodePipeline](pipelines-create.md)\.

When you use the console, the `PollForSourceChanges` parameter for your pipelined is changed for you\. The GitHub webhook is created and registered for you\.

**To edit the pipeline source stage**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account are displayed\.

1. In **Name**, choose the name of the pipeline you want to edit\. This opens a detailed view of the pipeline, including the state of each of the actions in each stage of the pipeline\.

1. On the pipeline details page, choose **Edit**\.

1. In **Edit stage**, choose the edit icon on the source action\.

1. Expand **Change detection options** and choose **Use Amazon CloudWatch Events to automatically start my pipeline when a change occurs \(recommended\)**\.

   A message is displayed to advise that CodePipeline creates a webhook in GitHub to detect source changes: AWS CodePipeline will create a webhook for you\. You can opt\-out in the options below\. Choose **Update**\. In addition to the webhook, CodePipeline creates the following:
   + A secret, randomly generated and used to authorize the connection to GitHub\.
   + The webhook URL, generated using the public endpoint for the Region\.

   CodePipeline registers the webhook with GitHub\. This subscribes the URL to receive repository events\.

1. When you have finished editing your pipeline, choose **Save pipeline changes** to return to the summary page\.

   A message displays the name of the webhook to be created for your pipeline\. Choose **Save and continue**\.

1. To test your action, release a change by using the AWS CLI to commit a change to the source specified in the source stage of the pipeline\.

### Update pipelines for push events \(GitHub version 1 source actions\) \(CLI\)<a name="update-change-detection-cli-github"></a>

Follow these steps to edit a pipeline that is using periodic checks to use a webhook instead\. If you want to create a pipeline, see [Create a pipeline in CodePipeline](pipelines-create.md)\.

To build an event\-driven pipeline, you edit the `PollForSourceChanges` parameter of your pipeline and then create the following resources manually:
+ GitHub webhook and authorization parameters<a name="proc-cli-gh-webhook"></a>

**To create and register your webhook**
**Note**  
When you use the CLI or AWS CloudFormation to create a pipeline and add a webhook, you must disable periodic checks\. To disable periodic checks, you must explicitly add the `PollForSourceChanges` parameter and set it to false, as detailed in the final procedure below\. Otherwise, the default for a CLI or AWS CloudFormation pipeline is that `PollForSourceChanges` defaults to true and does not display in the pipeline structure output\. For more information about PollForSourceChanges defaults, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.

1. In a text editor, create and save a JSON file for the webhook you want to create\. Use this sample file for a webhook named `my-webhook`:

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

1. Call the put\-webhook command and include the `--cli-input` and `--region` parameters\.

   The following sample command creates a webhook with the `webhook_json` JSON file\.

   ```
   aws codepipeline put-webhook --cli-input-json file://webhook_json.json --region "eu-central-1"
   ```

1. In the output shown in this example, the URL and ARN are returned for a webhook named `my-webhook`\.

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
       },
       "tags": [{
         "key": "Project",
         "value": "ProjectA"
       }]
   }
   ```

   This example adds tagging to the webhook by including the `Project` tag key and `ProjectA` value on the webhook\. For more information about tagging resources in CodePipeline, see [Tagging resources](tag-resources.md)\.

1. Call the register\-webhook\-with\-third\-party command and include the `--webhook-name` parameter\.

   The following sample command registers a webhook named `my-webhook`\.

   ```
   aws codepipeline register-webhook-with-third-party --webhook-name my-webhook
   ```<a name="proc-cli-flag-github"></a>

**To edit your pipeline's PollForSourceChanges parameter**
**Important**  
When you create a pipeline with this method, the `PollForSourceChanges` parameter defaults to true if it is not explicitly set to false\. When you add event\-based change detection, you must add the parameter to your output and set it to false to disable polling\. Otherwise, your pipeline starts twice for a single source change\. For details, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.

1. Run the get\-pipeline command to copy the pipeline structure into a JSON file\. For example, for a pipeline named `MyFirstPipeline`, you would type the following command: 

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   This command returns nothing, but the file you created should appear in the directory where you ran the command\.

1. Open the JSON file in any plain\-text editor and edit the source stage by changing or adding the `PollForSourceChanges` parameter\. In this example, for a repository named `UserGitHubRepo`, the parameter is set to `false` \.

   **Why am I making this change? ** Changing this parameter turns off periodic checks so you can use event\-based change detection only\.

   ```
   "configuration": {
     "Owner": "darlaker",
     "Repo": "UserGitHubRepo",
     "PollForSourceChanges": "false",
     "Branch": "master",
     "OAuthToken": "****"
     },
   ```

1. If you are working with the pipeline structure retrieved using the get\-pipeline command, you must edit the structure in the JSON file by removing the `metadata` lines from the file\. Otherwise, the update\-pipeline command cannot use it\. Remove the `"metadata"` section from the pipeline structure in the JSON file, including the : `{ }` and the `"created"`, `"pipelineARN"`, and `"updated"` fields\.

   For example, remove the following lines from the structure: 

   ```
   "metadata": {  
     "pipelineArn": "arn:aws:codepipeline:region:account-ID:pipeline-name",
     "created": "date",
     "updated": "date"
     }
   ```

   Save the file\.

1. To apply your changes, run the update\-pipeline command, specifying the pipeline JSON file, similar to the following:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline update-pipeline --cli-input-json file://pipeline.json
   ```

   This command returns the entire structure of the edited pipeline\.
**Note**  
The update\-pipeline command stops the pipeline\. If a revision is being run through the pipeline when you run the update\-pipeline command, that run is stopped\. You must manually start the pipeline to run that revision through the updated pipeline\. Use the start\-pipeline\-execution command to manually start your pipeline\.

### Update pipelines for push events \(GitHub version 1 source actions\) \(AWS CloudFormation template\)<a name="update-change-detection-cfn-github"></a>

Follow these steps to update your pipeline \(with a GitHub source\) from periodic checks \(polling\) to event\-based change detection using webhooks\.

To build an event\-driven pipeline with AWS CodeCommit, you edit the `PollForSourceChanges` parameter of your pipeline and then add a GitHub webhook resource to your template\.

If you use AWS CloudFormation to create and manage your pipelines, your template has content like the following\.

**Note**  
Note the `PollForSourceChanges` configuration property in the source stage\. If your template doesn't include that property, then `PollForSourceChanges` is set to `true` by default\.

------
#### [ YAML ]

```
Resources:
  AppPipeline: 
    Type: AWS::CodePipeline::Pipeline
    Properties: 
      Name: github-polling-pipeline
      RoleArn: 
        !GetAtt CodePipelineServiceRole.Arn
      Stages: 
        - 
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
                OAuthToken: {{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}                PollForSourceChanges: true
              RunOrder: 1


...
```

------
#### [ JSON ]

```
        "AppPipeline": {
            "Type": "AWS::CodePipeline::Pipeline",
            "Properties": {
                "Name": "github-polling-pipeline",
                "RoleArn": {
                    "Fn::GetAtt": [
                        "CodePipelineServiceRole",
                        "Arn"
                    ]
                },
                "Stages": [
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
                                    "PollForSourceChanges": true
                                },
                                "RunOrder": 1
                            }
                        ]
                    },


...
```

------<a name="proc-cfn-webhook-github"></a>

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

**Example**  
When you create these resources with AWS CloudFormation, the webhook defined is created in the specified GitHub repository\. Your pipeline is triggered on commit\.   

```
Parameters:
  GitHubOwner:
    Type: String
    
Resources:
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
  AppPipeline: 
    Type: AWS::CodePipeline::Pipeline
    Properties: 
      Name: github-events-pipeline
      RoleArn: 
        !GetAtt CodePipelineServiceRole.Arn
      Stages: 
        - 
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

...
```

```
{
    "Parameters": {
        "BranchName": {
            "Description": "GitHub branch name",
            "Type": "String",
            "Default": "master"
        },
        "RepositoryName": {
            "Description": "GitHub repository name",
            "Type": "String",
            "Default": "test"
        },
        "GitHubOwner": {
            "Type": "String"
        },
        "ApplicationName": {
            "Description": "CodeDeploy application name",
            "Type": "String",
            "Default": "DemoApplication"
        },
        "BetaFleet": {
            "Description": "Fleet configured in CodeDeploy",
            "Type": "String",
            "Default": "DemoFleet"
        }
    },
    "Resources": {

...

        },
        "AppPipelineWebhook": {
            "Type": "AWS::CodePipeline::Webhook",
            "Properties": {
                "Authentication": "GITHUB_HMAC",
                "AuthenticationConfiguration": {
                    "SecretToken": {
                        "{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}"
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
        "AppPipeline": {
            "Type": "AWS::CodePipeline::Pipeline",
            "Properties": {
                "Name": "github-events-pipeline",
                "RoleArn": {
                    "Fn::GetAtt": [
                        "CodePipelineServiceRole",
                        "Arn"
                    ]
                },
                "Stages": [
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

...
```

## Configure authentication \(GitHub version 1 source actions\)<a name="GitHub-authentication"></a>

CodePipeline uses GitHub OAuth tokens and personal access tokens to access your GitHub repositories and retrieve the latest changes\. There are two ways to configure authentication in GitHub:
+ AWS creates a default AWS managed OAuth token when you use the console to create or update pipelines\. 
+ You can create and manage your own customer\-generated personal access tokens\. You need personal access tokens when you use the CLI, SDK, or AWS CloudFormation to create or update your pipeline\. 

**Topics**
+ [View your authorized OAuth apps](#GitHub-view-oauth-token)
+ [Configure your pipeline to use a personal access token \(GitHub and CLI\)](#GitHub-create-personal-token-CLI)
+ [Use GitHub and the CodePipeline CLI to create and rotate your GitHub personal access token on a regular basis](#GitHub-rotate-personal-token-CLI)

### View your authorized OAuth apps<a name="GitHub-view-oauth-token"></a>

CodePipeline uses OAuth tokens to integrate with GitHub\. GitHub tracks the permissions of the OAuth token for CodePipeline\.

**To view your authorized integrations in GitHub**

1. In GitHub, from the drop\-down option on your profile photo, choose **Settings**\.

1. Choose **Applications**, and then choose **Authorized OAuth Apps**\.

1. Review your authorized apps\.

### Configure your pipeline to use a personal access token \(GitHub and CLI\)<a name="GitHub-create-personal-token-CLI"></a>

You can configure your pipeline to use a personal access token to connect to GitHub\. The advantage of using tokens instead of passwords in a script is that tokens can be revoked or rotated\. You can also grant specific privileges and permissions to a personal access token\. Each personal access token is associated at the pipeline, rather than account, level\.

**Note**  
You might have to update other applications if they are using the same personal access token\. As a security best practice, do not share a single token across multiple applications\. Create a personal access token for each application\. For more information, see [Creating a personal access token for the command line](https://help.github.com/articles/creating-an-access-token-for-command-line-use/) on the GitHub website\.

**To create a GitHub personal access token and then update the pipeline structure with the new token**

1. In GitHub, from the drop\-down option on your profile photo, choose **Settings**\.

1. Choose **Developer settings**, and then choose **Personal access tokens**\.

1. Choose **Generate new token**\.

   The **New personal access token** page appears\.

1. Under **Select scopes**, select **admin:repo\_hook** and **repo**\.

1. Choose **Generate token**\.

1. Next to the generated token, choose the copy icon\.
**Note**  
Make sure you copy your generated token now\. You cannot view the token after you close this page\.

1. At a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), run the get\-pipeline command on the pipeline where you want to change the OAuth token, and then copy the output of the command to a JSON file\. For example, for a pipeline named MyFirstPipeline, you would type something similar to the following:

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   The output of the command is sent to the `pipeline.json` file\.

1. Open the file in a plain\-text editor and edit the value in the `OAuthTokenField` of your GitHub action\.

   When you use the AWS CLI to create the pipeline, you can pass your GitHub personal access token in this field\. Replace the asterisks \(\*\*\*\*\) with the token you copied from GitHub\. When you run `get-pipeline` to view the action configuration, the four\-asterisk mask is displayed for this value\. For example, for a personal access token with the value `111222333444555666777888EXAMPLE`:

   ```
   "configuration": {
           "Owner": "MyGitHubUserName",
           "Repo": "test-repo",
           "Branch": "master",
           "OAuthToken": "111222333444555666777888EXAMPLE"
       }
   ```
**Note**  
When you use an AWS CloudFormation template to create the pipeline, you must first store the token as a secret in AWS Secrets Manager\. You include the value for this field as a dynamic reference to the stored secret in Secrets Manager\. For an example, see [GitHub version 1 source action structure reference](#action-reference-GitHub)\.

1. If you are working with the pipeline structure retrieved using the `get-pipeline` command, you must modify the structure in the JSON file by removing the `metadata` lines from the file\. Otherwise, the `update-pipeline` command cannot use it\. Remove the section from the pipeline structure in the JSON file \(the `"metadata": { }` lines and the `"created"`, `"pipelineARN"`, and `"updated"` fields\)\.

   For example, remove the following lines from the structure: 

   ```
   "metadata": {  
     "pipelineArn": "arn:aws:codepipeline:region:account-ID:pipeline-name",
     "created": "date",
     "updated": "date"
     }
   ```

1. Save the file, and then run the update\-pipeline with the `--cli-input-json` parameter to specify the JSON file you just edited\. 

   For example, to update a pipeline named MyFirstPipeline, you would type something similar to the following:

   ```
   aws codepipeline update-pipeline --cli-input-json file://pipeline.json
   ```
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

1. Repeat steps 6 through 8 for every pipeline that contains a GitHub action\.

1. When you are finished, delete the JSON files used to update those pipelines\.

### Use GitHub and the CodePipeline CLI to create and rotate your GitHub personal access token on a regular basis<a name="GitHub-rotate-personal-token-CLI"></a>

The advantage of using tokens instead of passwords in a script is that tokens can be revoked or rotated\. You can also grant specific privileges and permissions to a personal access token\. Tokens should be stored securely and rotated or regenerated routinely\. Token rotation is recommended by [RFC\-6819 \(OAuth 2\.0 Threat Model and Security Considerations\), section 5\.1\.5\.3](https://tools.ietf.org/html/rfc6819#section-5.1.5.3)\.

For more information, see [Creating a personal access token for the command line](https://help.github.com/articles/creating-an-access-token-for-command-line-use/) on the GitHub website\. 

After you have regenerated a new personal access token, you can rotate it by using the AWS CLI or API or by using AWS CloudFormation and calling `UpdatePipeline`\.

**Note**  
 You might have to update other applications if they are using the same personal access token\. As a security best practice, do not share a single token across multiple applications\. Create a new personal access token for each application\.

Use these steps to rotate your GitHub personal access token and then update the pipeline structure with the new token\.

**Note**  
After you rotate your personal access token, remember to update any AWS CLI scripts or AWS CloudFormation templates that contain the old token information\.

1. In GitHub, from the drop\-down option on your profile photo, choose **Settings**\. 

1. Choose **Developer settings**, and then choose **Personal access tokens**\.

1. Next to your GitHub personal access token, choose **Edit**\.

1. Choose **Regenerate token**\.

1. Next to the regenerated token, choose the copy icon\.

1. At a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), run the get\-pipeline command on the pipeline where you want to change the personal access token, and then copy the output of the command to a JSON file\. For example, for a pipeline named MyFirstPipeline, you would type something similar to the following:

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   The output of the command is sent to the `pipeline.json` file\.

1. Open the file in a plain\-text editor and edit the value in the `OAuthTokenField` of your GitHub action\.

   When you use the AWS CLI to create the pipeline, you can pass your GitHub personal access token in this field\. Replace the asterisks \(\*\*\*\*\) with the token you copied from GitHub\. When you run `get-pipeline` to view the action configuration, the four\-asterisk mask is displayed for this value\. For example, for a personal access token with the value `111222333444555666777888EXAMPLE`:

   ```
   "configuration": {
           "Owner": "MyGitHubUserName",
           "Repo": "test-repo",
           "Branch": "master",
           "OAuthToken": "111222333444555666777888EXAMPLE"
       }
   ```
**Note**  
When you use an AWS CloudFormation template to update the pipeline, you must first store the token as a secret in AWS Secrets Manager\. You include the value for this field as a dynamic reference to the stored secret in Secrets Manager\. For an example, see [GitHub version 1 source action structure reference](#action-reference-GitHub)\.

1. If you are working with the pipeline structure retrieved using the `get-pipeline` command, you must modify the structure in the JSON file by removing the `metadata` lines from the file\. Otherwise, the `update-pipeline` command cannot use it\. Remove the section from the pipeline structure in the JSON file \(the `"metadata": { }` lines and the `"created"`, `"pipelineARN"`, and `"updated"` fields\)\.

   For example, remove the following lines from the structure: 

   ```
   "metadata": {  
     "pipelineArn": "arn:aws:codepipeline:region:account-ID:pipeline-name",
     "created": "date",
     "updated": "date"
     }
   ```

1. Save the file, and then run update\-pipeline with the `--cli-input-json` parameter to specify the JSON file you just edited\. For example, to update a pipeline named MyFirstPipeline, you would type something similar to the following:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline update-pipeline --cli-input-json file://pipeline.json
   ```

1. When you have finished updating your pipelines, delete the JSON files\.

For more information, see [Pipeline error: "Could not access the GitHub repository" or "Unable to connect to the GitHub repository" \(GitHub version 1 source actions\)](#troubleshooting-gs2)\.

## Tutorial: Create a pipeline with AWS CloudFormation \(GitHub version 1 source actions\)<a name="tutorials-cloudformation-github"></a>

**Note**  
The GitHub version 1 action is not recommended\. For a pipeline with a GitHub version 1 action, CodePipeline uses OAuth\-based tokens to connect to your GitHub repository\. By contrast, the GitHub action \(version 2\) uses a connection resource to associate AWS resources to your GitHub repository\. The connection resource uses app\-based tokens to connect\. For more information about updating your pipeline to the recommended GitHub action that uses a connection, see [Update a GitHub version 1 source action to a GitHub version 2 source action](update-github-action-connections.md)\.

This tutorial shows you how to use the AWS CloudFormation console to create infrastructure that includes a pipeline connected to a GitHub source repository\. In this tutorial, you use the provided sample template file to create your resource stack, which includes your artifact store, pipeline, and change\-detection resource \(your webhook\)\. After you create your resource stack in AWS CloudFormation, you can view your pipeline in the AWS CodePipeline console\. The pipeline is a two\-stage pipeline with a GitHub source stage and a CodeDeploy deployment stage\.

We strongly recommend that you use AWS Secrets Manager to store your credentials\. If you use Secrets Manager, you must have already configured and stored your secret parameters in Secrets Manager\. This example uses dynamic references to AWS Secrets Manager for the GitHub credentials for your webhook\. For more information, see [ Using Dynamic References to Specify Template Values](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/dynamic-references.html#dynamic-references-secretsmanager)\. 

**Important**  
When passing secret parameters, do not enter the value directly into the template\. The value is rendered as plaintext and is therefore readable\. For security reasons, do not use plaintext in your AWS CloudFormation template to store your credentials\.

**Prerequisites:**

You must have created the following resources to use with the AWS CloudFormation sample template:
+ A CodeDeploy application and deployment group\. You can use the CodeDeploy resources you created in [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md)\.
+ Choose one of these links to download the sample AWS CloudFormation template file for creating a pipeline: [YAML](samples/codepipeline-github-events-yaml.zip) \| [JSON](samples/codepipeline-github-events-json.zip)

  Unzip the file and place it on your local computer\.
+ The sample templates in the bullet above are configured to use a GitHub secret token with this dynamic reference to the stored token in AWS Secrets Manager: `{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}`\. You must create a GitHub token and store it in Secrets Manager in order to use the dynamic reference in the template for your `OAuthToken` and `SecretToken` fields\.
+ Download the [SampleApp\_Linux\.zip](samples/SampleApp_Linux.zip)\.
+ The GitHub repository and branch you want to use for your source\.
+ A personal access key for your GitHub repository\. This is used to provide an OAuth token for connection to your repository\.

**To create your stack in AWS CloudFormation**

1. Unzip the files from [SampleApp\_Linux\.zip](samples/SampleApp_Linux.zip) and upload the files to your GitHub repository\. You must upload the unzipped files to the root directory of your repository\.

1. Open the AWS CloudFormation console and choose **Create Stack**\.

1. In **Choose a template**, choose **Upload a template to Amazon S3**\. Choose **Browse**, and then select the template file from your local computer\. Choose **Next**\.

1. In **Stack name**, enter a name for your pipeline\. Parameters specified by the sample template are displayed\. Enter the following parameters: 

   1. In **ApplicationName**, enter the name of your CodeDeploy application\.

   1. In **BetaFleet**, enter the name of your CodeDeploy deployment group\.

   1. In **BranchName**, enter the repository branch you want to use\.

   1. In **GitHubOAuthToken**, enter the personal access key for your GitHub repository\.

   1. In **GitHubOwner**, enter the GitHub user name for the owner of the repository\.

   1. In **GitHubSecret**, enter the secret you want to use for the webhook AWS CloudFormation creates\.

   1. In **RepositoryName**, enter the name of your GitHub source repository\.

1. Choose **Next**\. Accept the defaults on the following page, and then choose **Next**\.

1. In **Capabilities**, select **I acknowledge that AWS CloudFormation might create IAM resources**, and then choose **Create**\.

1. After your stack creation is complete, view the event list to check for any errors\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   Under **Pipelines**, choose your pipeline, and then choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. In your source repository, commit and push a change\. Your change\-detection resources pick up the change and your pipeline starts\.

## Troubleshooting \(GitHub version 1 source actions\)<a name="troubleshooting-gs"></a>

### Pipeline error: My GitHub source stage contains Git submodules, but CodePipeline doesn't initialize them \(GitHub version 1 source actions\)<a name="troubleshooting-gs1"></a>

**Problem:** CodePipeline does not support git submodules\. CodePipeline relies on the archive link API from GitHub, which does not support submodules\.

**Possible fixes:** Consider cloning the GitHub repository directly as part of a separate script\. For example, you could include a clone action in a Jenkins script\.

### Pipeline error: "Could not access the GitHub repository" or "Unable to connect to the GitHub repository" \(GitHub version 1 source actions\)<a name="troubleshooting-gs2"></a>

**Problem:** CodePipeline uses OAuth tokens to integrate with GitHub\. When you create a pipeline with a GitHub source provider, CodePipeline manages your GitHub credentials by creating a default OAuth token\. When your pipeline connects to the repository, it uses GitHub credentials to connect to GitHub\. The OAuth token credentials are managed by CodePipeline\. You do not view or manage the token in any way\. The other type of credentials you can use to connect to GitHub are personal access tokens, which are created by you instead of by OAuth apps\. Personal access tokens are managed by you and not by CodePipeline\.

If these permissions have been revoked or otherwise disabled, then the pipeline fails when it cannot use the GitHub token to connect to the repository\.

It is a security best practice to rotate your personal access token on a regular basis\. For more information, see [Use GitHub and the CodePipeline CLI to create and rotate your GitHub personal access token on a regular basis](#GitHub-rotate-personal-token-CLI)\.

**Possible fixes:** 

 If CodePipeline is unable to connect to the GitHub repository, there are two troubleshooting options: 
+ You might simply need to reconnect your pipeline to the repository manually\. You might have revoked the permissions of the OAuth token for CodePipeline and they just need to be restored\. To do this, see the steps below\.
+ You might need to change your default OAuth token to a personal access token\. The number of OAuth tokens is limited\. For more information, see [the GitHub documentation](https://developer.github.com/v3/oauth/)\. If CodePipeline reaches that limit, older tokens stop working, and actions in pipelines that rely on that token fail\.

1. Check to see if the permissions for CodePipeline have been revoked\. For the steps to check the **Authorized OAuth Apps** list in GitHub, see [View your authorized OAuth apps](#GitHub-view-oauth-token)\. If you do not see CodePipeline in the list, you must use the console to reconnect your pipeline to GitHub\.

   1. Open your pipeline in the console and choose **Edit**\. On the source stage that contains your GitHub source action, choose **Edit stage**\.

   1. On the GitHub source action, choose the edit icon\.

   1. On the **Edit action** page, choose **Connect to GitHub** to restore the authorization\. 

      If prompted, you might need to re\-enter the **Repository** and **Branch** for your action\. Choose **Done**\. Choose **Done** on the stage editing page, and then choose **Save** on the pipeline editing page\. Run the pipeline\.

1. If this does not correct the error but you can see CodePipeline in the **Authorized OAuth Apps** list in GitHub, you might have exceeded the allowed number of tokens\. To fix this issue, you can manually configure one OAuth token as a personal access token, and then configure all pipelines in your AWS account to use that token\. For more information, see [Configure your pipeline to use a personal access token \(GitHub and CLI\)](#GitHub-create-personal-token-CLI)\.