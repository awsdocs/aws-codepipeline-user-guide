# Update a GitHub version 1 source action to a GitHub version 2 source action<a name="update-github-action-connections"></a>

In AWS CodePipeline, there are two supported versions of the GitHub source action:
+ **Recommended:** The GitHub version 2 action uses Github app\-based auth backed by a [CodeStarSourceConnection for Bitbucket, GitHub, and GitHub Enterprise Server actions](action-reference-CodestarConnectionSource.md) resource\. It installs an AWS CodeStar Connections application into your GitHub organization so that you can manage access in GitHub\.
+ **Not recommended:** The GitHub version 1 action uses OAuth tokens to authenticate with GitHub and uses a separate webhook to detect changes\. This is no longer the recommended method\.

**Note**  
This feature is not available in the Asia Pacific \(Hong Kong\) or Europe \(Milan\) Region\. To use other source actions available in that Region, see [Source action integrations](integrations-action-type.md#integrations-source)\.

There are some important advantages to using the GitHub version 2 action instead of the GitHub version 1 action:
+ With connections, CodePipeline no longer requires OAuth apps or personal access tokens to access your repository\. When you create a connection, you install a GitHub App that manages authentication to your GitHub repository and allows permissions at the organization level\. You must authorize OAuth tokens as a user to access the repository\. For more information about OAuth\-based GitHub access in contrast to App\-based GitHub access, see [https://docs.github.com/en/developers/apps/differences-between-github-apps-and-oauth-apps](https://docs.github.com/en/developers/apps/differences-between-github-apps-and-oauth-apps)\.
+ When you manage GitHub version 2 actions in the CLI or CloudFormation, you no longer have to store your personal access token as a secret in Secrets Manager\. You no longer have to dynamically reference the stored secret in your CodePipeline action configuration\. You instead add the connection ARN to your action configuration\. For an example action configuration, see [CodeStarSourceConnection for Bitbucket, GitHub, and GitHub Enterprise Server actions](action-reference-CodestarConnectionSource.md)\.
+ When you create a connection resource to use with your GitHub version 2 action in CodePipeline, you can use the same connection resource to associate other supported services, such as CodeGuru Reviewer, with your repository\.
+ In Github version 2, you can clone repositories to access git metadata in subsequent CodeBuild actions, while in Github version 1 you can only download the source\.
+ An administrator installs the app for your organization's repositories\. You no longer have to track OAuth tokens that depend on the individual who created the token\.

All apps installed to an organization have access to the same set of repositories\. To change who can access each repository, modify the IAM policy for each connection\. For an example, see [Example: A scoped\-down policy for using connections with a specified repository](https://docs.aws.amazon.com/dtconsole/latest/userguide/security_iam_id-based-policy-examples-connections.html#security_iam_id-based-policy-examples-connections-reposcope)\.

You can use the steps in this topic to delete your GitHub version 1 source action and add a GitHub version 2 source action from the CodePipeline console\.

**Topics**
+ [Step 1: Replace your version 1 GitHub action](#connections-pipelines-github-action)
+ [Step 2: Create a connection to GitHub](#connections-pipelines-github-create)
+ [Step 3: Save your GitHub source action](#connections-pipelines-github-save)

## Step 1: Replace your version 1 GitHub action<a name="connections-pipelines-github-action"></a>

Use the pipeline edit page to replace your version 1 GitHub action with a version 2 GitHub action\.

**To replace your version 1 GitHub action**

1. Sign in to the CodePipeline console\.

1. Choose your pipeline, and choose **Edit**\. Choose **Edit stage** on your source stage\. A message displays that recommends you update your action\.

1. In **Action provider**, choose **GitHub \(Version 2\)**\.

1. Do one of the following:
   + Under **Connection**, if you have not already created a connection to your provider, choose **Connect to GitHub**\. Proceed to Step 2: Create a connection to GitHub\.
   + Under **Connection**, if you have already created a connection to your provider, choose the connection\. Proceed to Step 3: Save the Source Action for Your Connection\.

## Step 2: Create a connection to GitHub<a name="connections-pipelines-github-create"></a>

After you choose to create the connection, the **Connect to GitHub** page is shown\.

**To create a connection to GitHub**

1. Under **GitHub connection settings**, your connection name is shown in **Connection name**\.

   Under **GitHub Apps**, choose an app installation or choose **Install a new app** to create one\.
**Note**  
You install one app for all of your connections to a particular provider\. If you have already installed the GitHub app, choose it and skip this step\.

1. If the authorization page for GitHub displays, log in with your credentials and then choose to continue\.

1. On the app installation page, a message shows that the AWS CodeStar app is trying to connect to your GitHub account\.
**Note**  
You only install the app once for each GitHub account\. If you previously installed the app, you can choose **Configure** to proceed to a modification page for your app installation, or you can use the back button to return to the console\.

1. On the **Install AWS CodeStar** page, choose **Install**\.

1. On the **Connect to GitHub** page, the connection ID for your new installation is displayed\. Choose **Connect**\.

## Step 3: Save your GitHub source action<a name="connections-pipelines-github-save"></a>

Complete your updates on the **Edit action** page to save your new source action\.

**To save your GitHub source action**

1. In **Repository**, enter the name of your third\-party repository\. In **Branch**, enter the branch where you want your pipeline to detect source changes\.
**Note**  
In **Repository**, type `owner-name/repository-name` as shown in this example:   

   ```
   my-account/my-repository
   ```

1. In **Output artifact format**, choose the format for your artifacts\. 
   + To store output artifacts from the GitHub action using the default method, choose **CodePipeline default**\. The action accesses the files from the GitHub repository and stores the artifacts in a ZIP file in the pipeline artifact store\.
   + To store a JSON file that contains a URL reference to the repository so that downstream actions can perform Git commands directly, choose **Full clone**\. This option can only be used by CodeBuild downstream actions\.

     If you choose this option, you will need to update the permissions for your CodeBuild project service role as shown in [Add CodeBuild GitClone permissions for connections to Bitbucket, GitHub, or GitHub Enterprise Server](troubleshooting.md#codebuild-role-connections)\. For a tutorial that shows you how to use the **Full clone** option, see [Tutorial: Use full clone with a GitHub pipeline source](tutorials-github-gitclone.md)\.

1. In **Output artifacts**, you can retain the name of the output artifact for this action, such as `SourceArtifact`\. Choose **Done** to close the **Edit action** page\.

1. Choose **Done** to close the stage editing page\. Choose **Save** to close the pipeline editing page\.