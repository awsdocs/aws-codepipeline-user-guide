# Edit a Pipeline in AWS CodePipeline<a name="pipelines-edit"></a>

A pipeline describes the release process you want AWS CodePipeline to follow, including the stages and actions that must be completed\. You can edit a pipeline to add or remove these elements\. However, when you edit or update a pipeline, values such as the pipeline name or pipeline metadata cannot be changed\.

Unlike creating a pipeline, editing a pipeline does not rerun the most recent revision through the pipeline\. If you want to run the most recent revision through a pipeline you've just edited, you must manually rerun it\. Otherwise, the edited pipeline will run the next time you make a change to a source location configured in the source stage of the pipeline\. For information, see [Start a Pipeline Manually in AWS CodePipeline](pipelines-rerun-manually.md)\.



## Edit a Pipeline \(Console\)<a name="pipelines-edit-console"></a>

You can use the AWS CodePipeline console to add, edit, or remove stages in a pipeline, as well as to add, edit, or remove actions in a stage\.

AWS CodePipeline uses Amazon CloudWatch Events to detect changes in your AWS CodeCommit source repository and branch\. Using Amazon CloudWatch Events to automatically start your pipeline when changes occur is the default behavior for this source type\. 

**Note**  
When you use the console to edit a pipeline that has an AWS CodeCommit source repository, the rule and IAM role are created for you\. If you use the AWS CLI to edit the pipeline, you must create the Amazon CloudWatch Events rule and IAM role manually\. For more information, see [Start a Pipeline Automatically Using Amazon CloudWatch Events](triggering.md)\.

**To edit a pipeline in the AWS CodePipeline console**

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codepipeline](http://console.aws.amazon.com/codepipeline)\.

   The names of all pipelines associated with your AWS account are displayed\.

1. In **Name**, choose the name of the pipeline you want to edit\. This opens a detailed view of the pipeline, including the state of each of the actions in each stage of the pipeline\.

1. On the pipeline details page, choose **Edit**\. This opens the editing page for the pipeline\.

1. On the **Edit** page, do one of the following:

   + To edit a stage, choose the edit icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/edit_icon.png)\) on that stage\. You can add actions \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/add_action.png)\) in serial and parallel with existing actions:

     You can also edit actions in this view by choosing the edit icon for those actions\. To delete an action, choose the delete icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/delete_icon.png)\) on that action\.

     To add AWS CodeBuild as a build action or test action to a stage, see [Use AWS CodePipeline with AWS CodeBuild to Test Code and Run Builds](http://docs.aws.amazon.com/codebuild/latest/userguide/how-to-create-pipeline.html) in *[AWS CodeBuild User Guide](http://docs.aws.amazon.com/codebuild/latest/userguide/)*\.

   + To edit an action, choose the edit icon for that action, and then on **Edit action**, change the values\. Items marked with an asterisk \(**\***\) are required\.
**Note**  
If you edit a source action by choosing an updated AWS CodeCommit repository name and branch, a message is displayed under **Change detection options** showing the Amazon CloudWatch Events rule that will be created for this pipeline\. This allows AWS CodePipeline to use Amazon CloudWatch Events to detect changes for your new pipeline\.

   + To add a stage, choose **\+ Stage** at the point in the pipeline where you want to add a stage\. Provide a name for the stage, and then add at least one action to it\. Items marked with an asterisk \(**\***\) are required\.

   + To delete a stage, choose the delete icon on that stage\. The stage and all of its actions will be deleted\.

   For example, if you wanted to add an action to an existing stage in a pipeline:

   1. In the stage where you want to add your action, choose the edit icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/edit_icon.png)\), and then add your action by choosing **\+ Action**\. 
**Note**  
To add an action that runs in parallel with another action, choose the add action icon next to that action\. To run your action before another action, choose the add action icon above that action\. To run your action after another action in that stage, choose the add action icon under that action\.

   1. The **Add action** panel opens\. In **Action category**, choose the category of your action, such as build or deploy\. In the system drop\-down list, choose the system, and then provide all the required details for the action configuration, such as a name for the action, a provider, and the input or output artifact, depending on the action type\. Items marked with an asterisk \(**\***\) are required\. For more information about the requirements for actions in AWS CodePipeline, including names for input and output artifacts and how they are used, see [Action Structure Requirements in AWS CodePipeline](reference-pipeline-structure.md#action-requirements)\. 
**Note**  
Some action providers, such as GitHub, require you to connect to the provider's website before you can complete the configuration of the action\. When connecting to a provider's website, make sure you use the credentials for that website\. Do not use your AWS credentials\. 

   1. When you have finished configuring your action, choose **Add action**\.
**Note**  
You cannot rename an action or a stage in the console view\. You can add a new stage or action with the name you want to change, and then delete the old one\. Make sure you have added all the actions you want in that stage before you delete the old one\.

1. When you have finished editing your pipeline, choose **Save pipeline changes** to return to the summary page\.
**Important**  
After you save your changes, you cannot undo them\. You must re\-edit the pipeline\. In addition, if a revision is running through your pipeline when you save your changes, it will not complete the run\. If you want a specific commit or change to run through the edited pipeline, you must manually rerun the change through the pipeline after saving your changes\. Otherwise, the next commit or change will run automatically through the pipeline\.

1. To test your action, choose **Release change** to process that commit through the pipeline, commit a change to the source specified in the source stage of the pipeline, or follow the steps in [Start a Pipeline Manually in AWS CodePipeline](pipelines-rerun-manually.md) to manually release a change using the AWS CLI\.

## Edit a Pipeline \(AWS CLI\)<a name="pipelines-edit-cli"></a>

You can use the update\-pipeline command to edit a pipeline\.

**Important**  
While you can edit pipelines that include partner actions using the AWS CLI, you must not manually edit the JSON of a partner action itself\. If you do so, the partner action will likely fail after you update the pipeline\.

**To edit a pipeline in the AWS CLI**

1. Open a terminal session \(Linux, macOS, or Unix\) or command prompt \(Windows\) and run the get\-pipeline command to copy the pipeline structure into a JSON file\. For example, for a pipeline named **MyFirstPipeline**, you would type the following command: 

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   This command returns nothing, but the file you created should appear in the directory where you ran the command\.

1. Open the JSON file in any plain\-text editor and modify the structure of the file to reflect the changes you want to make to the pipeline\. For example, you can add or remove stages, or add another action to an existing stage\.
**Note**  
Some edits, such as moving an action from one stage to another stage, delete the last known state history for the action\. In addition, if a pipeline contains one or more secret parameters, such as an OAuth token for an action, that token is masked by a series of asterisks \(\*\*\*\*\)\. These secret parameters are left unchanged unless you edit that portion of the pipeline \(for example, if you change the name of the action that includes the OAuth token or the name of the stage that contains an action that uses an OAuth token\)\. If you make a change that affects an action that includes an OAuth token, you must include the value of the token in the edited JSON\. For more information, see [AWS CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\. It is a security best practice to rotate your personal access token on a regular basis\. For more information, see [Use GitHub and the AWS CodePipeline CLI to Create and Rotate Your GitHub Personal Access Token on a Regular Basis](GitHub-rotate-personal-token-CLI.md)\.

   The following example shows how you would add another deployment stage in the pipeline\.json file\. This stage will run after the first deployment stage named *Staging*\. 
**Note**  
This is just a portion of the file, not the entire structure\. For more information, see [AWS CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\.

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

   The following example shows how you would add a source stage that uses a GitHub repository as its source action\. For more information about how AWS CodePipeline integrates with GitHub, see [Source Action Integrations](integrations-action-type.md#integrations-source)\. 
**Note**  
This is just a portion of the file, not the entire structure\. For more information, see [AWS CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\.

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
                               "version": "1",
                               "provider": "GitHub"
                           },
                           "outputArtifacts": [
                               {
                                   "name": "MyApp"
                               }
                           ],
                           "configuration": {
                               "Owner": "MyGitHubAccountName",
                               "Repo": "MyGitHubRepositoryName",
                               "Branch": "master",
                               "OAuthToken": "****"
                           },
                           "runOrder": 1
                       }
                   ]
               },
   ```

   You must supply a value for `OAuthToken` to access the GitHub repository\. You can use a personal access token for this value\. To create a personal access token, see [Pipeline Error: I receive a pipeline error that includes the following message: "PermissionError: Could not access the GitHub repository"](troubleshooting.md#troubleshooting-gs2)\.

   For information about using the CLI to add an approval action to a pipeline, see [Add a Manual Approval Action to a Pipeline in AWS CodePipeline ](approvals-action-add.md)\.

   AWS CodePipeline uses Amazon CloudWatch Events to detect changes in your AWS CodeCommit source repository and branch\. Using Amazon CloudWatch Events to automatically start your pipeline when changes occur is recommended and is accomplished using these steps in the CLI: 

   1. Open the JSON file in a plain\-text editor and modify the file by setting the `PollForSourceChanges` parameter to `false`\. The parameter is located in the source stage of the pipeline structure\.

      The following example shows how the parameter should look after the file is modified to disable periodic checks:

      ```
      {
          "name": "Source",
          "actions": [
              {
                  "inputArtifacts": [],
                  "name": "Source",
                  "actionTypeId": {
                      "category": "Source",
                      "owner": "AWS",
                      "version": "1",
                      "provider": "CodeCommit"
                  },
                  "outputArtifacts": [
                      {
                          "name": "MyApp"
                      }
                  ],
                  "configuration": {
                      "PollForSourceChanges": "false",
                      "BranchName": "master",
                      "RepositoryName": "MyTestRepo"
                  },
                  "runOrder": 1
              }
          ]
      },
      ```

   1. After you edit your pipeline, you must manually create the CloudWatch Events rule for change detection\. For more information on using the CLI to create the rule, see [Start a Pipeline Automatically Using Amazon CloudWatch Events](triggering.md)\.

1. To apply your changes, run the update\-pipeline command, specifying the pipeline JSON file, similar to the following:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline update-pipeline --cli-input-json file://pipeline.json
   ```

   This command returns the entire structure of the edited pipeline\.
**Note**  
The update\-pipeline command stops the pipeline\. If a revision is being run through the pipeline when you run the update\-pipeline command, that run will be stopped\. You must manually start the pipeline to run that revision through the updated pipeline\.

1. Open the AWS CodePipeline console and choose the pipeline you just edited from the list\.

   The pipeline shows your changes\. The next time you make a change to the source location, the pipeline will run that revision through the revised structure of the pipeline\.

1. To manually run the last revision through the revised structure of the pipeline, run the start\-pipeline\-execution command\. For more information, see [Start a Pipeline Manually in AWS CodePipeline](pipelines-rerun-manually.md)\.

For more information about the structure of a pipeline and expected values, see [AWS CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md) and [AWS CodePipeline API Reference](http://docs.aws.amazon.com/codepipeline/latest/APIReference)\.