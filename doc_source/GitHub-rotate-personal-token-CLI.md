# Use GitHub and the CodePipeline CLI to Create and Rotate Your GitHub Personal Access Token on a Regular Basis<a name="GitHub-rotate-personal-token-CLI"></a>

The advantage of using tokens instead of passwords in a script is that tokens can be revoked or rotated\. You are also able to grant specific privileges and permissions to a personal access token\. Tokens should be stored securely and rotated or regenerated routinely\. Token rotation is recommended by [RFC\-6819 \(OAuth 2\.0 Threat Model and Security Considerations\), section 5\.1\.5\.3](https://tools.ietf.org/html/rfc6819#section-5.1.5.3)\.

For more information, see [Creating a personal access token for the command line](https://help.github.com/articles/creating-an-access-token-for-command-line-use/) on the GitHub website\. 

After you have regenerated a new personal access token, you can rotate it by using the CLI or API or by using AWS CloudFormation and calling `UpdatePipeline`\.

**Note**  
 You might have to update other applications if they are using the same personal access token\. As a security best practice, do not share a single token across multiple applications\. Create a new personal access token for each application\.

Use these steps to rotate your GitHub personal access token and then update the pipeline structure with the new token\.

**Note**  
After you rotate your personal access token, remember to update any CLI scripts or AWS CloudFormation templates containing the old token information\.

1. In GitHub, from the drop\-down option on your profile photo, choose **Settings**\. 

1. Choose **Developer settings** and then choose **Personal access tokens**\.

1. Next to your GitHub personal access token, choose **Edit**\.  
![\[Edit the personal access token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/personal-token1.png)![\[Edit the personal access token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Edit the personal access token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Choose **Regenerate token**\.  
![\[Edit the personal access token by regenerating it.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/personal-token2.png)![\[Edit the personal access token by regenerating it.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Edit the personal access token by regenerating it.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Next to the regenerated token, choose the copy icon\.  
![\[Copy the rotated token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/personal-token3.png)![\[Copy the rotated token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Copy the rotated token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. At a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), run the get\-pipeline command on the pipeline where you want to change the personal access token, and then copy the output of the command to a JSON file\. For example, for a pipeline named MyFirstPipeline, you would type something similar to the following:

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   The output of the command is sent to the *pipeline\.json* file\.

1. Open the file in a plain\-text editor and edit the value in the `OAuthTokenField` of your GitHub action\. Replace the asterisks \(\*\*\*\*\) with the token you copied from GitHub\. For example:

   ```
   {
       "configuration": {
           "Owner": "MyGitHubUserName",
           "Repo": "test-repo",
           "Branch": "master",
           "OAuthToken": "Replace the **** with your personal access token"
       },
   ```

1. If you are working with the pipeline structure retrieved using the `get-pipeline` command, you must modify the structure in the JSON file by removing the `metadata` lines from the file, or the `update-pipeline` command will not be able to use it\. Remove the section from the pipeline structure in the JSON file \(the "metadata": \{ \} lines and the "created," "pipelineARN," and "updated" fields within\)\.

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

For more information, see [Pipeline Error: I receive a pipeline error that includes the following message: "PermissionError: Could not access the GitHub repository"](troubleshooting.md#troubleshooting-gs2)\.