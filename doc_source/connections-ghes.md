# GitHub Enterprise Server connections<a name="connections-ghes"></a>

Connections allow you to authorize and establish configurations that associate your third\-party provider with your AWS resources\. To associate your third\-party repository as a source for your pipeline, you use a connection\. 

To add a GitHub Enterprise Server source action in CodePipeline, you can choose either to: 
+ Use the CodePipeline console **Create pipeline** wizard or **Edit action** page to choose the **GitHub Enterprise Server** provider option\. See [Create a connection to GitHub Enterprise Server \(console\)](#connections-ghes-console) to add the action\. The console helps you create a host resource and a connections resource\.
+ Use the CLI to add the action configuration for the `CreateSourceConnection` action with the `GitHubEnterpriseServer` provider and create your resources:
  + To create your connections resources, see [Create a host and connection to GitHub Enterprise Server \(CLI\)](#connections-ghes-cli) to create a host resource and a connections resource with the CLI\.
  + Use the `CreateSourceConnection` example action configuration in [CodeStarSourceConnection](action-reference-CodestarConnectionSource.md) to add your action as shown in [Create a pipeline \(CLI\)](pipelines-create.md#pipelines-create-cli)\.

**Note**  
You can also create a connection using the Developer Tools console under **Settings**\. See [Create a Connection](https://docs.aws.amazon.com/dtconsole/latest/userguide/connections-create.html)\.

Before you begin:
+ You must have created an account with GitHub Enterprise Server and installed the GitHub Enterprise Server instance on your infrastructure\.
+ You must have already created a code repository with GitHub Enterprise Server\.

**Important**  
AWS CodeStar Connections does not support GitHub Enterprise Server version 2\.22\.0\.

**Topics**
+ [Create a connection to GitHub Enterprise Server \(console\)](#connections-ghes-console)
+ [Create a host and connection to GitHub Enterprise Server \(CLI\)](#connections-ghes-cli)

## Create a connection to GitHub Enterprise Server \(console\)<a name="connections-ghes-console"></a>

Use these steps to use the CodePipeline console to add a connections action for your GitHub Enterprise Server repository\.

**Note**  
GitHub Enterprise Server connections only provide access to repositories owned by the GitHub Enterprise Server account that was used to create the connection\.

**Before you begin:**

For a host connection to GitHub Enterprise Server, you must have completed the steps to create a host resource for your connection\. See [Manage hosts for connections](https://docs.aws.amazon.com/dtconsole/latest/userguide/connections-hosts.html)\.

### Step 1: Create or edit your pipeline<a name="connections-ghes-console-action"></a>

**To create or edit your pipeline**

1. Sign in to the CodePipeline console\.

1. Choose one of the following\.
   + Choose to create a pipeline\. Follow the steps in *Create a Pipeline* to complete the first screen and choose **Next**\. On the **Source** page, under **Source provider**, choose **GitHub Enterprise Server**\.
   + Choose to edit an existing pipeline\. Choose **Edit**, and then choose **Edit stage**\. Choose to add or edit your source action\. On the **Edit action** page, under **Action name**, enter the name for your action\. In **Action provider**, choose **GitHub Enterprise Server**\.

1. Do one of the following:
   + Under **Connection**, if you have not already created a connection to your provider, choose **Connect to GitHub Enterprise Server**\. Proceed to Step 2: Create a Connection to GitHub Enterprise Server\.
   + Under **Connection**, if you have already created a connection to your provider, choose the connection\. Proceed to Step 3: Save the Source Action for Your Connection\.

### Create a connection to GitHub Enterprise Server<a name="connections-ghes-console-create"></a>

After you choose to create the connection, the **Connect to GitHub Enterprise Server** page is shown\.

**Important**  
AWS CodeStar Connections does not support GitHub Enterprise Server version 2\.22\.0\.

**To connect to GitHub Enterprise Server**

1. Under **GitHub Enterprise Server apps**, choose an app installation or choose **Install a new app** to create one\. 
**Note**  
You install one app for all of your connections to a particular provider\. If you have already installed the GitHub Enterprise Server app, choose it and skip this step\.

1. On the GitHub authorization page, choose **Authorize**\.

1. On the app installation page, a message shows that the AWS app is ready to be installed\. Choose **Install**\.

1. The connection ID for your new GitHub Enterprise Server installation is displayed\. Choose **Connect**\.

### Step 3: Save your GitHub Enterprise Server source action<a name="connections-ghes-console-save"></a>

Use these steps on the wizard or **Edit action** page to save your source action with your connection information\.

**To complete and save your source action with your connection**

1. In **Repository name**, choose the name of your third\-party repository\. In **Branch name**, choose the branch where you want your pipeline to detect source changes\.

1. In **Output artifact format**, you must choose the format for your artifacts\. 
   + To store output artifacts from the GitHub Enterprise Server action using the default method, choose **CodePipeline default**\. The action accesses the files from the GitHub Enterprise Server repository and stores the artifacts in a ZIP file in the pipeline artifact store\.
   + To store a JSON file that contains a URL reference to the repository so that downstream actions can perform Git commands directly, choose **Full clone**\. This option can only be used by CodeBuild downstream actions\.

1. Choose **Next** on the wizard or **Save** on the **Edit action** page\.

## Create a host and connection to GitHub Enterprise Server \(CLI\)<a name="connections-ghes-cli"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to create a connection\. 

To do this, use the create\-connection command\. 

**Important**  
A connection created through the AWS CLI or AWS CloudFormation is in `PENDING` status by default\. After you create a connection with the CLI or AWS CloudFormation, use the console to edit the connection to make its status `AVAILABLE`\.

You can use the AWS Command Line Interface \(AWS CLI\) to create a host for installed connections\. 

**Note**  
You only create a host once per GitHub Enterprise Server account\. All of your connections to a specific GitHub Enterprise Server account will use the same host\.

You use a host to represent the endpoint for the infrastructure where your third\-party provider is installed\. After you complete the host creation with the CLI, the host is in **Pending** status\. You then set up, or register, the host to move it to an **Available** status\. After the host is available, you complete the steps to create a connection\.

To do this, use the create\-host command\. 

**Important**  
A host created through the AWS CLI is in `Pending` status by default\. After you create a host with the CLI, use the console or the CLI to set up the host to make its status `Available`\.

**To create a host**

1. Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\)\. Use the AWS CLI to run the create\-host command, specifying the `--name`, `--provider-type`, and `--provider-endpoint` for your connection\. In this example, the third\-party provider name is `GitHubEnterpriseServer` and the endpoint is `my-instance.dev`\.

   ```
   aws codestar-connections create-host --name MyHost --provider-type GitHubEnterpriseServer --provider-endpoint "https://my-instance.dev"
   ```

   If successful, this command returns the host Amazon Resource Name \(ARN\) information similar to the following\.

   ```
   {
       "HostArn": "arn:aws:codestar-connections:us-west-2:account_id:host/My-Host-28aef605"
   }
   ```

   After this step, the host is in `PENDING` status\.

1. Use the console to complete the host setup and move the host to an `Available` status\.

**To create a connection to GitHub Enterprise Server**

1. Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\)\. Use the AWS CLI to run the create\-connection command, specifying the `--host-arn` and `--connection-name` for your connection\.

   ```
   aws codestar-connections create-connection --host-arn arn:aws:codestar-connections:us-west-2:account_id:host/MyHost-234EXAMPLE --connection-name MyConnection
   ```

   If successful, this command returns the connection ARN information similar to the following\.

   ```
   {
       "ConnectionArn": "arn:aws:codestar-connections:us-west-2:account_id:connection/aEXAMPLE-8aad"
   }
   ```

1. Use the console to set up the pending connection\.