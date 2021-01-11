# Bitbucket connections<a name="connections-bitbucket"></a>

Connections allow you to authorize and establish configurations that associate your third\-party provider with your AWS resources\. To associate your third\-party repository as a source for your pipeline, you use a connection\. 

**Note**  
This feature is not available in the Asia Pacific \(Hong Kong\) Region\. To use other source actions available in that Region, see [Source action integrations](integrations-action-type.md#integrations-source)\.

To add a Bitbucket source action in CodePipeline, you can choose either to: 
+ Use the CodePipeline console **Create pipeline** wizard or **Edit action** page to choose the **Bitbucket** provider option\. See [Create a connection to Bitbucket \(console\)](#connections-bitbucket-console) to add the action\. The console helps you create a connections resource\.
+ Use the CLI to add the action configuration for the `CreateSourceConnection` action with the `Bitbucket` provider as follows:
  + To create your connections resources, see [Create a connection to Bitbucket \(CLI\)](#connections-bitbucket-cli) to create a connections resource with the CLI\.
  + Use the `CreateSourceConnection` example action configuration in [CodeStarSourceConnection](action-reference-CodestarConnectionSource.md) to add your action as shown in [Create a pipeline \(CLI\)](pipelines-create.md#pipelines-create-cli)\.

**Note**  
You can also create a connection using the Developer Tools console under **Settings**\. See [Create a Connection](https://docs.aws.amazon.com/dtconsole/latest/userguide/connections-create.html)\.

Before you begin:
+ You must have created an account with the provider of the third\-party repository, such as Bitbucket\.
+ You must have already created a third\-party code repository, such as a Bitbucket repository\.

**Topics**
+ [Create a connection to Bitbucket \(console\)](#connections-bitbucket-console)
+ [Create a connection to Bitbucket \(CLI\)](#connections-bitbucket-cli)

## Create a connection to Bitbucket \(console\)<a name="connections-bitbucket-console"></a>

Use these steps to use the CodePipeline console to add a connections action for your Bitbucket repository\.

**Note**  
Bitbucket connections only provide access to repositories owned by the Bitbucket account that was used to create the connection\.

### Step 1: Create or edit your pipeline<a name="connections-bitbucket-console-action"></a>

**To create or edit your pipeline**

1. Sign in to the CodePipeline console\.

1. Choose one of the following\.
   + Choose to create a pipeline\. Follow the steps in *Create a Pipeline* to complete the first screen and choose **Next**\. On the **Source** page, under **Source Provider**, choose **Bitbucket**\.  
![\[Console screenshot showing the Add source stage dialog box with Bitbucket chosen as the source provider.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/bitbucket-add-source.png)![\[Console screenshot showing the Add source stage dialog box with Bitbucket chosen as the source provider.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the Add source stage dialog box with Bitbucket chosen as the source provider.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)  
![\[Console screenshot showing the Add source stage dialog box with Bitbucket Cloud chosen as the source provider.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/bitbucket-add-source-edit.png)![\[Console screenshot showing the Add source stage dialog box with Bitbucket Cloud chosen as the source provider.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the Add source stage dialog box with Bitbucket Cloud chosen as the source provider.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
   + Choose to edit an existing pipeline\. Choose **Edit**, and then choose **Edit stage**\. Choose to add or edit your source action\. On the **Edit action** page, under **Action name**, enter the name for your action\. In **Action provider**, choose **Bitbucket**\.

1. Do one of the following:
   + Under **Connection**, if you have not already created a connection to your provider, choose **Connect to Bitbucket**\. Proceed to Step 2: Create a Connection to Bitbucket\.
   + Under **Connection**, if you have already created a connection to your provider, choose the connection\. Proceed to Step 3: Save the Source Action for Your Connection\.

### Step 2: Create a connection to Bitbucket<a name="connections-bitbucket-console-create"></a>

**To create a connection to Bitbucket**

1. On the **Connect to Bitbucket** settings page, enter your connection name and choose **Connect to Bitbucket**\.  
![\[Console screenshot showing the Connect to Bitbucket button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/create-connection-bitbucket.png)![\[Console screenshot showing the Connect to Bitbucket button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the Connect to Bitbucket button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   The **Bitbucket apps** field appears\.

1. Under **Bitbucket apps**, choose an app installation or choose **Install a new app** to create one\.
**Note**  
You install one app for all of your connections to a particular provider\. If you have already installed the Bitbucket app, choose it and move to step 4\.  
![\[Console screenshot showing the Connect to Bitbucket dialog box, with the install new app button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/newreview-source-wizard-bitbucket.png)![\[Console screenshot showing the Connect to Bitbucket dialog box, with the install new app button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the Connect to Bitbucket dialog box, with the install new app button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. If the login page for Bitbucket displays, log in with your credentials and then choose to continue\.

1. On the app installation page, a message shows that the AWS CodeStar app is trying to connect to your Bitbucket account\. Choose **Grant access**\.  
![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/bitbucket-access-popup.png)![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. In **Bitbucket apps**, the connection ID for your new installation is displayed\. Choose **Connect**\. The created connection displays in the connections list\.  
![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/create-connection-bitbucket-app-ID.png)![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

### Step 3: Save your Bitbucket source action<a name="connections-bitbucket-console-save"></a>

Use these steps on the wizard or **Edit action** page to save your source action with your connection information\.

**To complete and save your source action with your connection**

1. In **Repository name**, choose the name of your third\-party repository\. In **Branch name**, choose the branch where you want your pipeline to detect source changes\.

1. In **Output artifact format**, you must choose the format for your artifacts\. 
   + To store output artifacts from the Bitbucket action using the default method, choose **CodePipeline default**\. The action accesses the files from the Bitbucket repository and stores the artifacts in a ZIP file in the pipeline artifact store\.
   + To store a JSON file that contains a URL reference to the repository so that downstream actions can perform Git commands directly, choose **Full clone**\. This option can only be used by CodeBuild downstream actions\.

     If you choose this option, you will need to update the permissions for your CodeBuild project service role as shown in [Add CodeBuild GitClone permissions for connections to Bitbucket, GitHub, or GitHub Enterprise Server](troubleshooting.md#codebuild-role-connections)\.

1. Choose **Next** on the wizard or **Save** on the **Edit action** page\.

## Create a connection to Bitbucket \(CLI\)<a name="connections-bitbucket-cli"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to create a connection\. 

To do this, use the create\-connection command\. 

**Important**  
A connection created through the AWS CLI or AWS CloudFormation is in `PENDING` status by default\. After you create a connection with the CLI or AWS CloudFormation, use the console to edit the connection to make its status `AVAILABLE`\.

**To create a connection**

1. Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\)\. Use the AWS CLI to run the create\-connection command, specifying the `--provider-type` and `--connection-name` for your connection\. In this example, the third\-party provider name is `Bitbucket` and the specified connection name is `MyConnection`\.

   ```
   aws codestar-connections create-connection --provider-type Bitbucket --connection-name MyConnection
   ```

   If successful, this command returns the connection ARN information similar to the following\.

   ```
   {
       "ConnectionArn": "arn:aws:codestar-connections:us-west-2:account_id:connection/aEXAMPLE-8aad-4d5d-8878-dfcab0bc441f"
   }
   ```

1. Use the console to complete the connection\. For more information, see [Update a pending connection](https://docs.aws.amazon.com/dtconsole/latest/userguide/connections-update.html)\.