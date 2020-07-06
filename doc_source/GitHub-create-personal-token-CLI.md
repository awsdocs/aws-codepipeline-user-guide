# Configure your pipeline to use a personal access token \(GitHub and CLI\)<a name="GitHub-create-personal-token-CLI"></a>

You can configure your pipeline to use a personal access token to connect to GitHub\. The advantage of using tokens instead of passwords in a script is that tokens can be revoked or rotated\. You can also grant specific privileges and permissions to a personal access token\. Each personal access token is associated at the pipeline, rather than account, level\.

**Note**  
You might have to update other applications if they are using the same personal access token\. As a security best practice, do not share a single token across multiple applications\. Create a personal access token for each application\. For more information, see [Creating a personal access token for the command line](https://help.github.com/articles/creating-an-access-token-for-command-line-use/) on the GitHub website\.

**To create a GitHub personal access token and then update the pipeline structure with the new token**

1. In GitHub, from the drop\-down option on your profile photo, choose **Settings**\.

1. Choose **Developer settings**, and then choose **Personal access tokens**\.

1. Choose **Generate new token**\.

1. Under **Select scopes**, select **admin:repo\_hook** and **repo**\.  
![\[Configure the personal access token scopes.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/personal-token-checkboxes.png)![\[Configure the personal access token scopes.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Configure the personal access token scopes.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

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
When you use an AWS CloudFormation template to create the pipeline, you must first store the token as a secret in AWS Secrets Manager\. You include the value for this field as a dynamic reference to the stored secret in Secrets Manager\. For an example, see [GitHub](action-reference-GitHub.md)\.

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