# Configure GitHub authentication<a name="GitHub-authentication"></a>

CodePipeline uses GitHub OAuth tokens and personal access tokens to access your GitHub repositories and retrieve the latest changes\. There are two ways to configure authentication in GitHub:
+ AWS creates a default AWS managed OAuth token when you use the console to create or update pipelines\. 
+ You can create and manage your own customer\-generated personal access tokens\. You need personal access tokens when you use the CLI, SDK, or AWS CloudFormation to create or update your pipeline\. 

**Topics**
+ [View your authorized OAuth apps](#GitHub-view-oauth-token)
+ [Configure your pipeline to use a personal access token \(GitHub and CLI\)](GitHub-create-personal-token-CLI.md)
+ [Use GitHub and the CodePipeline CLI to create and rotate your GitHub personal access token on a regular basis](#GitHub-rotate-personal-token-CLI)

### View your authorized OAuth apps<a name="GitHub-view-oauth-token"></a>

CodePipeline uses OAuth tokens to integrate with GitHub\. GitHub tracks the permissions of the OAuth token for CodePipeline\.

**To view your authorized integrations in GitHub**

1. In GitHub, from the drop\-down option on your profile photo, choose **Settings**\.

1. Choose **Applications**, and then choose **Authorized OAuth Apps**\.

1. Review your authorized apps\.  
![\[Review authorized apps in GitHub.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/oauth-apps.png)![\[Review authorized apps in GitHub.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Review authorized apps in GitHub.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

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
![\[Edit the personal access token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/personal-token1.png)![\[Edit the personal access token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Edit the personal access token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Choose **Regenerate token**\.  
![\[Edit the personal access token by regenerating it.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/personal-token2.png)![\[Edit the personal access token by regenerating it.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Edit the personal access token by regenerating it.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Next to the regenerated token, choose the copy icon\.  
![\[Copy the rotated token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/personal-token3.png)![\[Copy the rotated token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Copy the rotated token.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

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
When you use an AWS CloudFormation template to update the pipeline, you must first store the token as a secret in AWS Secrets Manager\. You include the value for this field as a dynamic reference to the stored secret in Secrets Manager\. For an example, see [GitHub](action-reference-GitHub.md)\.

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

For more information, see [Pipeline error: I receive a pipeline error that says: "Could not access the GitHub repository" or "Unable to connect to the GitHub repository"](troubleshooting.md#troubleshooting-gs2)\.