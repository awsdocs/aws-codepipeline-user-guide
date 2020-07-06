# Edit a pipeline in CodePipeline<a name="pipelines-edit"></a>

A pipeline describes the release process that you want AWS CodePipeline to follow, including stages and actions that must be completed\. You can edit a pipeline to add or remove these elements\. However, when you edit a pipeline, values such as the pipeline name or pipeline metadata cannot be changed\.

Unlike creating a pipeline, editing a pipeline does not rerun the most recent revision through the pipeline\. If you want to run the most recent revision through a pipeline you've just edited, you must manually rerun it\. Otherwise, the edited pipeline runs the next time you make a change to a source location configured in the source stage\. For information, see [Start a pipeline manually in AWS CodePipeline](pipelines-rerun-manually.md)\.

You can add actions to your pipeline that are in an AWS Region different from your pipeline\. When an AWS service is the provider for an action, and this action type/provider type are in a different AWS Region from your pipeline, this is a cross\-Region action\. For more information about cross\-Region actions, see [Add a cross\-Region action in CodePipeline](actions-create-cross-region.md)\.

CodePipeline uses change detection methods to start your pipeline when a source code change is pushed\. These detection methods are based on source type: 
+ CodePipeline uses Amazon CloudWatch Events to detect changes in your CodeCommit source repository or your Amazon S3 source bucket\.
+ CodePipeline uses webhooks to detect changes in your GitHub source repository and branch\.

**Note**  
Change detection resources are created automatically when you use the console\. When you use the console to create or edit a pipeline, the additional resources are created for you\. If you use the AWS CLI to create the pipeline, you must create the additional resources yourself\. For more information about creating or updating a CodeCommit pipeline, see [Create a CloudWatch Events rule for a CodeCommit source \(CLI\)](pipelines-trigger-source-repo-changes-cli.md)\. For more information about using the CLI to create or update an Amazon S3 pipeline, see [Create a CloudWatch Events rule for an Amazon S3 source \(CLI\)](create-cloudtrail-S3-source-cli.md)\. For more information about creating or updating a GitHub pipeline, see [Use webhooks to start a pipeline \(GitHub source\)](pipelines-webhooks.md)\.

**Topics**
+ [Edit a pipeline \(console\)](#pipelines-edit-console)
+ [Edit a pipeline \(AWS CLI\)](#pipelines-edit-cli)

## Edit a pipeline \(console\)<a name="pipelines-edit-console"></a>

You can use the CodePipeline console to add, edit, or remove stages in a pipeline and to add, edit, or remove actions in a stage\.

**To edit a pipeline**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account are displayed\.

1. In **Name**, choose the name of the pipeline you want to edit\. This opens a detailed view of the pipeline, including the state of each of the actions in each stage of the pipeline\.

1. On the pipeline details page, choose **Edit**\. 

1. On the **Edit** page, do one of the following:
   + To edit a stage, choose **Edit stage**\. You can add actions in serial and parallel with existing actions:

     You can also edit actions in this view by choosing the edit icon for those actions\. To delete an action, choose the delete icon on that action\.
   + To edit an action, choose the edit icon for that action, and then on **Edit action**, change the values\. Items marked with an asterisk \(**\***\) are required\.
     + For a CodeCommit repository name and branch, a message appears showing the Amazon CloudWatch Events rule to be created for this pipeline\. If you remove the CodeCommit source, a message appears showing the Amazon CloudWatch Events rule to be deleted\.
     + For an Amazon S3 source bucket, a message appears showing the Amazon CloudWatch Events rule and AWS CloudTrail trail to be created for this pipeline\. If you remove the Amazon S3 source, a message appears showing the Amazon CloudWatch Events rule and AWS CloudTrail trail to be deleted\. If the AWS CloudTrail trail is in use by other pipelines, the trail is not removed and the data event is deleted\.
     + For a GitHub source, the following are added for the pipeline:
       + CodePipeline uses an OAuth token to create an authorized application that is managed by CodePipeline\.
**Note**  
In GitHub, there is a limit to the number of OAuth tokens you can use for an application, such as CodePipeline\. If you exceed this limit, retry the connection to allow CodePipeline to reconnect by reusing existing tokens\. For more information, see [Pipeline error: I receive a pipeline error that says: "Could not access the GitHub repository" or "Unable to connect to the GitHub repository"](troubleshooting.md#troubleshooting-gs2)\.
       + CodePipeline creates a webhook in GitHub to detect source changes and then start your pipeline when a change occurs\. CodePipeline creates the following along with the webhook:
         + A secret is randomly generated and used to authorize the connection to GitHub\.
         + The webhook URL is generated using the public endpoint for the Region\.
         + The webhook is registered with GitHub\. This subscribes the URL to receive repository events\.

     If you delete a GitHub source action, the webhook is deregistered and deleted for you\.
   + To add a stage, choose **\+ Add stage** at the point in the pipeline where you want to add a stage\. Provide a name for the stage, and then add at least one action to it\. Items marked with an asterisk \(**\***\) are required\.
   + To delete a stage, choose the delete icon on that stage\. The stage and all of its actions are deleted\.

   For example, if you wanted to add a serial action to a stage in a pipeline:

   1. In the stage where you want to add your action, choose **Edit stage**, and then choose **\+ Add action group**\. 

   1. In **Edit action**, in **Action name**, enter the name of your action\. The **Action provider** list displays provider options by category\. Look for the category \(for example, **Deploy**\)\. Under the category, choose the provider \(for example, **AWS CodeDeploy**\)\. In **Region**, choose the AWS Region where the resource is created or where you plan to create it\. The **Region** field designates where the AWS resources are created for this action type and provider type\. This field only displays for actions where the action provider is an AWS service\. The **Region** field defaults to the same AWS Region as your pipeline\.

      For more information about the requirements for actions in CodePipeline, including names for input and output artifacts and how they are used, see [Action structure requirements in CodePipeline](reference-pipeline-structure.md#action-requirements)\. For examples of adding action providers and using the default fields for each provider, see [Create a pipeline \(console\)](pipelines-create.md#pipelines-create-console)\. 

      To add CodeBuild as a build action or test action to a stage, see [Use CodePipeline with CodeBuild to Test Code and Run Builds](https://docs.aws.amazon.com/codebuild/latest/userguide/how-to-create-pipeline.html) in the *CodeBuild User Guide*\.
**Note**  
Some action providers, such as GitHub, require you to connect to the provider's website before you can complete the configuration of the action\. When you connect to a provider's website, make sure you use the credentials for that website\. Do not use your AWS credentials\. 

   1. When you have finished configuring your action, choose **Save**\.
**Note**  
You cannot rename a stage in the console view\. You can add a stage with the name you want to change, and then delete the old one\. Make sure you have added all the actions you want in that stage before you delete the old one\.

1. When you have finished editing your pipeline, choose **Save** to return to the summary page\.
**Important**  
After you save your changes, you cannot undo them\. You must edit the pipeline again\. If a revision is running through your pipeline when you save your changes, the run is not completed\. If you want a specific commit or change to run through the edited pipeline, you must manually run it through the pipeline\. Otherwise, the next commit or change runs automatically through the pipeline\.

1. To test your action, choose **Release change** to process that commit through the pipeline and commit a change to the source specified in the source stage of the pipeline\. Or follow the steps in [Start a pipeline manually in AWS CodePipeline](pipelines-rerun-manually.md) to use the AWS CLI to manually release a change\.

## Edit a pipeline \(AWS CLI\)<a name="pipelines-edit-cli"></a>

You can use the update\-pipeline command to edit a pipeline\.

**Important**  
Although you can use the AWS CLI to edit pipelines that include partner actions, you must not manually edit the JSON of a partner action\. If you do so, the partner action fails after you update the pipeline\.

**To edit a pipeline**

1. Open a terminal session \(Linux, macOS, or Unix\) or command prompt \(Windows\) and run the get\-pipeline command to copy the pipeline structure into a JSON file\. For example, for a pipeline named **MyFirstPipeline**, enter the following command: 

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   This command returns nothing, but the file you created should appear in the directory where you ran the command\.

1. Open the JSON file in any plain\-text editor and modify the structure of the file to reflect the changes you want to make to the pipeline\. For example, you can add or remove stages, or add another action to an existing stage\.

   The following example shows how you would add another deployment stage in the pipeline\.json file\. This stage runs after the first deployment stage named *Staging*\. 
**Note**  
This is just a portion of the file, not the entire structure\. For more information, see [CodePipeline pipeline structure reference](reference-pipeline-structure.md)\.

   ```
   ,      
           {
               "name": "Staging",
               "actions": [
                       {
                           "inputArtifacts": [
                               {
                                   "name": "MyApp"
                               }
                           ],
                           "name": "Deploy-CodeDeploy-Application",
                           "actionTypeId": {
                               "category": "Deploy",
                               "owner": "AWS",
                               "version": "1",
                               "provider": "CodeDeploy"
                           },
                           "outputArtifacts": [],
                           "configuration": {
                               "ApplicationName": "CodePipelineDemoApplication",
                               "DeploymentGroupName": "CodePipelineDemoFleet"
                           },
                           "runOrder": 1
                       }
                   ]
                },      
           {
               "name": "Production",
               "actions":  [
                       {
                           "inputArtifacts": [
                               {
                                   "name": "MyApp"
                               }
                           ],
                           "name": "Deploy-Second-Deployment",
                           "actionTypeId": {
                               "category": "Deploy",
                               "owner": "AWS",
                               "version": "1",
                               "provider": "CodeDeploy"
                           },
                           "outputArtifacts": [],
                           "configuration": {
                           "ApplicationName": "CodePipelineDemoApplication",
                           "DeploymentGroupName": "CodePipelineProductionFleet"
                           },
                           "runOrder": 1
                       }
                   ]
           }
       ]
   }
   ```

   The following example shows how you would add a source stage that uses a GitHub repository as its source action\. For more information about how CodePipeline integrates with GitHub, see [Source action integrations](integrations-action-type.md#integrations-source)\. 
**Note**  
This is just a portion of the file, not the entire structure\. For more information, see [CodePipeline pipeline structure reference](reference-pipeline-structure.md)\.

   ```
   {
                   "name": "Source",
                   "actions": [
                       {
                           "inputArtifacts": [],
                           "name": "Source",
                           "actionTypeId": {
                               "category": "Source",
                               "owner": "ThirdParty",
                               "provider": "GitHub",
                               "version": "1"
                           },
                           "outputArtifacts": [
                               {
                                   "name": "MyApp"
                               }
                           ],
                           "configuration": {
                               "Owner": "MyGitHubAccountName",
                               "Repo": "MyGitHubRepositoryName",
                               "PollForSourceChanges": "false",
                               "Branch": "master",
                               "OAuthToken": "****"
                           },
                           "runOrder": 1
                       }
                   ]
               },
   ```

   The value for `OAuthToken` remains masked because CodePipeline uses it to access the GitHub repository\. You can use a personal access token for this value\. To create a personal access token, see [Configure your pipeline to use a personal access token \(GitHub and CLI\)](GitHub-create-personal-token-CLI.md)\.
**Note**  
Some edits, such as moving an action from one stage to another stage, delete the last known state history for the action\. If a pipeline contains one or more secret parameters, such as an OAuth token for an action, that token is masked by a series of asterisks \(\*\*\*\*\)\. These secret parameters are left unchanged unless you edit that portion of the pipeline \(for example, if you change the name of the action that includes the OAuth token or the name of the stage that contains an action that uses an OAuth token\)\. If you make a change that affects an action that includes an OAuth token, you must include the value of the token in the edited JSON\. For more information, see [Configure your pipeline to use a personal access token \(GitHub and CLI\)](GitHub-create-personal-token-CLI.md)\. It is a security best practice to rotate your personal access token on a regular basis\. For more information, see [Use GitHub and the CodePipeline CLI to create and rotate your GitHub personal access token on a regular basis](GitHub-authentication.md#GitHub-rotate-personal-token-CLI)\.

   For information about using the CLI to add an approval action to a pipeline, see [Add a manual approval action to a pipeline in CodePipeline ](approvals-action-add.md)\.

   Make sure the `PollForSourceChanges` parameter in your JSON file is set as follows: 

   ```
                   "PollForSourceChanges": "false",
   ```

   CodePipeline uses Amazon CloudWatch Events to detect changes in your CodeCommit source repository and branch or your Amazon S3 source bucket\. CodePipeline uses webhooks to detect changes in your GitHub source repository and branch\. The next step includes instructions for creating these resources manually\. Setting the flag to `false` disables periodic checks, which are not required when you use the recommended change detection methods\. 

1. To add a build, test, or deploy action in a Region different from your pipeline, you must add the following to your pipeline structure\. For detailed instructions, see [Add a cross\-Region action in CodePipeline](actions-create-cross-region.md)\.
   + Add the `Region` parameter to your action's pipeline structure\.
   + Use the `artifactStores` parameter to specify an artifact bucket for each Region where you have an action\.

1. If you are working with the pipeline structure retrieved using the get\-pipeline command, you must modify the structure in the JSON file\. You must remove the `metadata` lines from the file so the update\-pipeline command can use it\. Remove the section from the pipeline structure in the JSON file \(the `"metadata": { }` lines and the `"created"`, `"pipelineARN"`, and `"updated"` fields\)\.

   For example, remove the following lines from the structure: 

   ```
   "metadata": {  
     "pipelineArn": "arn:aws:codepipeline:region:account-ID:pipeline-name",
     "created": "date",
     "updated": "date"
     }
   ```

   Save the file\.

1. If you use the CLI to edit a pipeline, you must manually manage the recommended change detection resources for your pipeline:
   + For a CodeCommit repository, you must create the CloudWatch Events rule, as described in [Create a CloudWatch Events rule for a CodeCommit source \(CLI\)](pipelines-trigger-source-repo-changes-cli.md)\.
   + For an Amazon S3 source, you must create the CloudWatch Events rule and AWS CloudTrail trail, as described in [Use CloudWatch Events to start a pipeline \(Amazon S3 source\)](create-cloudtrail-S3-source.md)\.
   + For a GitHub source, you must create the webhook, as described in [Use webhooks to start a pipeline \(GitHub source\)](pipelines-webhooks.md)\.

1. To apply your changes, run the update\-pipeline command, specifying the pipeline JSON file:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline update-pipeline --cli-input-json file://pipeline.json
   ```

   This command returns the entire structure of the edited pipeline\.
**Note**  
The update\-pipeline command stops the pipeline\. If a revision is being run through the pipeline when you run the update\-pipeline command, that run is stopped\. You must start the pipeline manually to run that revision through the updated pipeline\.

1. Open the CodePipeline console and choose the pipeline you just edited\.

   The pipeline shows your changes\. The next time you make a change to the source location, the pipeline runs that revision through the revised structure of the pipeline\.

1. To manually run the last revision through the revised structure of the pipeline, run the start\-pipeline\-execution command\. For more information, see [Start a pipeline manually in AWS CodePipeline](pipelines-rerun-manually.md)\.

For more information about the structure of a pipeline and expected values, see [CodePipeline pipeline structure reference](reference-pipeline-structure.md) and [AWS CodePipeline API Reference](http://docs.aws.amazon.com/codepipeline/latest/APIReference)\.