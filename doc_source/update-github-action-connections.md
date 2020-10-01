# Update a GitHub version 1 source action to a GitHub version 2 source action<a name="update-github-action-connections"></a>

Version 1 of the GitHub action uses OAuth apps to authenticate with GitHub and uses a separate webhook to detect changes\. This is no longer the recommended method\. The new GitHub action \(version 2\) uses a connection \(the `CodeStarSourceConnection` action in the CLI or the **GitHub** provider selection in the console\)\. Connections use app\-based authorization to connect your repository to CodePipeline\.

You can use the steps in this topic to delete your GitHub version 1 source action and add a GitHub version 2 source action from the CodePipeline console\.

**Topics**
+ [Step 1: Replace your version 1 GitHub action](#connections-pipelines-github-action)
+ [Step 2: Create a connection to GitHub](#connections-pipelines-github-create)
+ [Step 3: Save your GitHub source action](#connections-pipelines-github-save)

## Step 1: Replace your version 1 GitHub action<a name="connections-pipelines-github-action"></a>

Use the pipeline edit page to make a note of your existing action details\. You then delete your version 1 GitHub action and add a version 2 GitHub action\.

**To replace your version 1 GitHub action**

1. Sign in to the CodePipeline console\.

1. Choose your pipeline, and choose **Edit**\. Choose **Edit stage** on your source stage\. A message displays that recommends you update your action\.

   This GitHub action for authorizing CodePipeline to access your repository is no longer the recommended method\. Update your GitHub action by creating a connection that CodePipeline uses to access your repository\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/change-github-source-action.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. ew your existing action, and make a note of any details you want to remember\. For example, in **Output artifacts**, note the name of your output artifact, such as `SourceArtifact`\. You might want to use the same name in your new action\.

1. Close the **Edit action** page, and choose the action deletion \(X\) icon to delete the version 1 GitHub action from yourstage\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/change-github-source-action-delete.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. On your source stage, choose to add a new action\. 

   On the **Edit action** page, under **Action name**, enter the name for your action\. In **Action provider**, choose **GitHub**\.

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

1. In **Repository name**, choose the name of your third\-party repository\. In **Branch name**, choose the branch where you want your pipeline to detect source changes\.

1. In **Output artifact format**, you must choose the format for your artifacts\. 
   + To store output artifacts from the GitHub action using the default method, choose **CodePipeline default**\. The action accesses the files from the GitHub repository and stores the artifacts in a ZIP file in the pipeline artifact store\.
   + To store a JSON file that contains a URL reference to the repository so that downstream actions can perform Git commands directly, choose **Full clone**\. This option can only be used by CodeBuild downstream actions\. 

     If you choose this option, you will need to update the permissions for your CodeBuild project service role as shown in [Add GitClone permissions for connections](troubleshooting.md#codebuild-role-connections)\. For a tutorial that shows you how to use the **Full clone** option, see [Tutorial: Use full clone with a GitHub pipeline source](tutorials-github-gitclone.md)\.

1. In **Output artifacts**, enter the name of the output artifact for this action, such as `SourceArtifact`\. You might want to use the same name in your new action that was used in the previous action\.

1. Choose **Save** on the **Edit action** page\.