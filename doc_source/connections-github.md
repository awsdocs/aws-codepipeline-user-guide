# GitHub connections<a name="connections-github"></a>

You use connections to authorize and establish configurations that associate your third\-party provider with your AWS resources\.

**Note**  
This feature is not available in the Asia Pacific \(Hong Kong\) or Europe \(Milan\) Region\. To use other source actions available in that Region, see [Source action integrations](integrations-action-type.md#integrations-source)\.

To add a source action for your GitHub or GitHub Enterprise Cloud repository in CodePipeline, you can choose either to:
+ Use the CodePipeline console **Create pipeline** wizard or **Edit action** page to choose the **GitHub \(Version 2\)** provider option\. See [Create a connection to GitHub Enterprise Server \(console\)](connections-ghes.md#connections-ghes-console) to add the action\. The console helps you create a connections resource\.
**Note**  
For a tutorial that walks you through how to add a GitHub connection and use the **Full clone** option in your pipeline, see [Tutorial: Use full clone with a GitHub pipeline source](tutorials-github-gitclone.md)\.
+ Use the CLI to add the action configuration for the `CodeStarSourceConnection` action with the `GitHub` provider with the CLI steps shown in [Create a pipeline \(CLI\)](pipelines-create.md#pipelines-create-cli)\.

**Note**  
You can also create a connection using the Developer Tools console under **Settings**\. See [Create a Connection](https://docs.aws.amazon.com/dtconsole/latest/userguide/connections-create.html)\.

Before you begin:
+ You must have created an account with GitHub\.
+ You must have already created a GitHub code repository\.
+ If your CodePipeline service role was created before December 18, 2019, you might need to update its permissions to use `codestar-connections:UseConnection` for AWS CodeStar connections\. For instructions, see [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services)\.

**Topics**
+ [Create a connection to GitHub \(console\)](#connections-github-console)
+ [Create a connection to GitHub \(CLI\)](#connections-github-cli)

## Create a connection to GitHub \(console\)<a name="connections-github-console"></a>

Use these steps to use the CodePipeline console to add a connections action for your GitHub or GitHub Enterprise Cloud repository\.

**Note**  
Connections for GitHub only provide access to repositories owned by the GitHub account that was used to create the connection\.

### Step 1: Create or edit your pipeline<a name="connections-github-console-action"></a>

1. Sign in to the CodePipeline console\.

1. Choose one of the following\.
   + Choose to create a pipeline\. Follow the steps in *Create a Pipeline* to complete the first screen and choose **Next**\. On the **Source** page, under **Source Provider**, choose **GitHub \(Version 2\)**\.
   + Choose to edit an existing pipeline\. Choose **Edit**, and then choose **Edit stage**\. Choose to add or edit your source action\. On the **Edit action** page, under **Action name**, enter the name for your action\. In **Action provider**, choose **GitHub \(Version 2\)**\.

1. Do one of the following:
   + Under **Connection**, if you have not already created a connection to your provider, choose **Connect to GitHub**\. Proceed to Step 2: Create a Connection to GitHub\.
   + Under **Connection**, if you have already created a connection to your provider, choose the connection\. Proceed to Step 3: Save the source action for your connection\.

### Step 2: Create a connection to GitHub<a name="connections-github-console-create"></a>

After you choose to create the connection, the **Connect to GitHub** page appears\.

![\[Console screenshot showing the initial GitHub connection page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/github-conn.png)![\[Console screenshot showing the initial GitHub connection page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the initial GitHub connection page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

**To create a connection to GitHub**

1. Under **GitHub connection settings**, your connection name appears in **Connection name**\. Choose **Connect to GitHub**\. The access request page appears\.  
![\[Console screenshot showing the GitHub account access page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/github-conn-access.png)![\[Console screenshot showing the GitHub account access page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the GitHub account access page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Choose **Authorize AWS Connector for GitHub**\. The connection page displays and shows the **GitHub Apps** field\.  
![\[Console screenshot showing the initial GitHub connection page with the GitHub Apps field.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/github-conn-access-app.png)![\[Console screenshot showing the initial GitHub connection page with the GitHub Apps field.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the initial GitHub connection page with the GitHub Apps field.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Under **GitHub Apps**, choose an app installation or choose **Install a new app** to create one\.
**Note**  
You install one app for all of your connections to a particular provider\. If you have already installed the AWS Connector for GitHub app, choose it and skip this step\.

1. On the **Install AWS Connector for GitHub** page, choose the account where you want to install the app\.  
![\[Console screenshot showing the Connector for GitHub installation page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/github-conn-access-app-install1.png)![\[Console screenshot showing the Connector for GitHub installation page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the Connector for GitHub installation page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
**Note**  
You only install the app once for each GitHub account\. If you previously installed the app, you can choose **Configure** to proceed to a modification page for your app installation, or you can use the back button to return to the console\.

1. On the **Install AWS Connector for GitHub** page, leave the defaults, and choose **Install**\.  
![\[Console screenshot showing the second Connector for GitHub installation page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/github-conn-access-app-install2.png)![\[Console screenshot showing the second Connector for GitHub installation page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the second Connector for GitHub installation page.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   

1. On the **Connect to GitHub** page, the connection ID for your new installation appears in **GitHub Apps**\. Choose **Connect**\.

### Step 3: Save your GitHub source action<a name="connections-github-console-save"></a>

Use these steps on the **Edit action** page to save your source action with your connection information\.

**To save your GitHub source action**

1. In **Repository**, enter the name of your repository\. In **Branch name**, choose the branch where you want your pipeline to detect source changes\.
**Note**  
In **Repository**, type `owner-name/repository-name` as shown in this example:   

   ```
   my-account/my-repository
   ```

1. In **Output artifact format**, you must choose the format for your artifacts\. 
   + To store output artifacts from the GitHub action using the default method, choose **CodePipeline default**\. The action accesses the files from the GitHub repository and stores the artifacts in a ZIP file in the pipeline artifact store\.
   + To store a JSON file that contains a URL reference to the repository so that downstream actions can perform Git commands directly, choose **Full clone**\. This option can only be used by CodeBuild downstream actions\.

     If you choose this option, you will need to update the permissions for your CodeBuild project service role as shown in [Add CodeBuild GitClone permissions for connections to Bitbucket, GitHub, or GitHub Enterprise Server](troubleshooting.md#codebuild-role-connections)\. For a tutorial that shows you how to use the **Full clone** option, see [Tutorial: Use full clone with a GitHub pipeline source](tutorials-github-gitclone.md)\.

1. Choose **Next** on the wizard or **Save** on the **Edit action** page\.

## Create a connection to GitHub \(CLI\)<a name="connections-github-cli"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to create a connection\. 

To do this, use the create\-connection command\. 

**Important**  
A connection created through the AWS CLI or AWS CloudFormation is in `PENDING` status by default\. After you create a connection with the CLI or AWS CloudFormation, use the console to edit the connection to make its status `AVAILABLE`\.

**To create a connection**

1. Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\)\. Use the AWS CLI to run the create\-connection command, specifying the `--provider-type` and `--connection-name` for your connection\. In this example, the third\-party provider name is `GitHub` and the specified connection name is `MyConnection`\.

   ```
   aws codestar-connections create-connection --provider-type GitHub --connection-name MyConnection
   ```

   If successful, this command returns the connection ARN information similar to the following\.

   ```
   {
       "ConnectionArn": "arn:aws:codestar-connections:us-west-2:account_id:connection/aEXAMPLE-8aad-4d5d-8878-dfcab0bc441f"
   }
   ```

1. Use the console to complete the connection\. For more information, see [Update a pending connection](https://docs.aws.amazon.com/dtconsole/latest/userguide/connections-update.html)\. 