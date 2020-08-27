# Create a connection to Bitbucket<a name="connections-pipelines"></a>

You can use the AWS CodePipeline console to create a connection to a third\-party code repository\. As an example, this topic shows you how to create a connection to a Bitbucket repository\. Before you begin:
+ You must have created an account with the provider of the third\-party repository, such as Bitbucket\.
+ You must have already created a third\-party code repository, such as a Bitbucket repository\.

**Note**  
Bitbucket connections only provide access to repositories owned by the Bitbucket account that was used to create the connection\.

**Topics**
+ [Create a connection \(console\)](#connections-pipelines-console)
+ [Create a connection \(CLI\)](#connections-pipelines-cli)

## Create a connection \(console\)<a name="connections-pipelines-console"></a>

You use the console to create a connection by using one of the following methods:
+ Use the **Create Pipeline** wizard or the **Edit action** page in the CodePipeline console\. Note that when you configure a CodePipeline action, you can choose existing connections or create a new connection\.
+ Associate your Bitbucket repository to your feedback and analysis tools in [Amazon CodeGuru Reviewer](Amazon CodeGuru Reviewer User Guidewelcome.html)\.

This example shows the flow to create connections as part of the **Create Pipeline** wizard in CodePipeline\. To create a connection to a third\-party source provider in the console, you must provide the source file location and information about the repository\.

**Step 1: Create your pipeline and connection**

1. Sign in to the CodePipeline console\.

1. On the **Welcome** page, choose **Create pipeline**\. 

   If this is your first time using CodePipeline, choose **Get Started**\.

1. On the **Step 1: Choose pipeline settings** page, in **Pipeline name**, enter the name for your pipeline\.

   In a single AWS account, each pipeline you create in an AWS Region must have a unique name\. Names can be reused for pipelines in different Regions\.
**Note**  
After you create a pipeline, you cannot change its name\. For information about other limitations, see [Quotas in AWS CodePipeline](limits.md)\.

1. In **Service role**, do one of the following:
   + To allow CodePipeline to create a service role in AWS Identity and Access Management \(IAM\), choose **New service role**\. In **Role name**, the role and policy name both default to this format: `AWSCodePipelineServiceRole-region-pipeline_name`\. For example, this is the service role for a pipeline named `MyPipeline`: `AWSCodePipelineServiceRole-us-west-2-MyPipeline`\.
   + To use a service role that is already created in IAM, choose **Existing service role**\. In **Role ARN**, choose your service role Amazon Resource Name \(ARN\) from the list\.
**Note**  
Depending on when your service role was created, you might need to update its permissions to support AWS CodeStar connections\. For instructions for the CodePipeline service role, see [Update the CodePipeline Service Role](https://docs.aws.amazon.com/codepipeline/latest/userguide/security-iam.html#how-to-update-role-new-services)\. 

1. \(Optional\) Expand **Advanced settings**\.

1. In **Artifact store**, do one of the following: 

   1. To use the default artifact store, such as the Amazon Simple Storage Service \(Amazon S3\) artifact bucket designated as the default, for your pipeline in the AWS Region you selected for your pipeline, choose **Default location**\.

   1. If you already have an artifact store, such as an S3 artifact bucket, in the same Region as your pipeline, choose **Custom location**\. In **Bucket**, choose the bucket name\.

1. In **Encryption key**, do one of the following: 

   1. To use the CodePipeline default AWS managed AWS KMS customer master key \(CMK\) to encrypt the data in the pipeline artifact store \(S3 bucket\), choose **Default AWS Managed Key**\.

   1. To use your CMK to encrypt the data in the pipeline artifact store \(S3 bucket\), choose **Customer Managed Key**\. In **KMS customer master key**, choose the key ID, key ARN, or alias ARN\.

1.  Choose **Next**\.

**Step 2: Create a source stage**

1. On the **Add source stage** page, in **Source provider**, choose **Bitbucket**\.  
![\[Console screenshot showing the Add source stage dialog box with Bitbucket chosen as the source provider.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/bitbucket-add-source.png)![\[Console screenshot showing the Add source stage dialog box with Bitbucket chosen as the source provider.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the Add source stage dialog box with Bitbucket chosen as the source provider.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Under **Connection**, choose an existing connection, or choose **Connect to Bitbucket** to create one\. 

1. On the **Connect to Bitbucket** page, in **Connection name**, enter the name for the connection that you want to create\. The name helps you identify this connection later\.

   Under **Bitbucket apps**, choose an app installation or choose **Install a new app** to create one\.   
![\[Console screenshot showing the Connect to Bitbucket dialog box, with the install new app button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/newreview-source-wizard-bitbucket.png)![\[Console screenshot showing the Connect to Bitbucket dialog box, with the install new app button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the Connect to Bitbucket dialog box, with the install new app button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. On the app installation page, a message shows that the AWS CodeStar app is trying to connect to your Bitbucket account\. Choose **Grant access**\.  
![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/bitbucket-access-popup.png)![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. The connection ID for your new installation is displayed\. Choose **Complete connection**\. 

1. In **Repository name**, choose the name of your third\-party repository\. In **Branch name**, choose the branch where you want your pipeline to detect source changes\.

1. In **Output artifact format**, you must choose the format for your artifacts\. 
   + To store output artifacts from the Bitbucket action using the default method, choose **CodePipeline default**\. The action accesses the files from the Bitbucket repository and stores the artifacts in a ZIP file in the pipeline artifact store\.
   + To store a JSON file that contains a URL reference to the repository so that downstream actions can perform Git commands directly, choose **Full clone**\. This option can only be used by CodeBuild downstream actions\.

1. Choose **Next**\.

1. Finish creating your next stage with the wizard\.

**To review the pipeline**

1. On the **Step 5: Review** page, review your pipeline configuration\. Choose **Create pipeline** to create the pipeline, or choose **Previous** to go back and edit your choices\.

1.  To exit the wizard without creating a pipeline, choose **Cancel**\.

## Create a connection \(CLI\)<a name="connections-pipelines-cli"></a>

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