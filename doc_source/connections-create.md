# Connect to Bitbucket<a name="connections-create"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

You can use the AWS CodePipeline console to create a connection to a third\-party code repository\. As an example, this topic shows you how to create a connection to a Bitbucket repository\. Before you begin:
+ You must have created an account with the provider of the third\-party repository, such as Bitbucket\.
+ You must have already created a third\-party code repository, such as a Bitbucket repository\.

When you configure a CodePipeline action, you can choose existing connections created in any AWS Region\.

CodePipeline uses webhooks to manage change detection for your pipeline\.

To create a connection to a third\-party source provider in the console, you must provide the source file location and information about the repository\.

**Step 1: Create your pipeline and choose your connection**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Welcome** page, choose **Create pipeline**\. 

   If this is your first time using CodePipeline, choose **Get Started**\.

1. On the **Step 1: Choose pipeline settings** page, in **Pipeline name**, enter the name for your pipeline\.

   In a single AWS account, each pipeline you create in an AWS Region must have a unique name\. Names can be reused for pipelines in different Regions\.
**Note**  
After you create a pipeline, you cannot change its name\. For information about other limitations, see [Quotas in AWS CodePipeline](limits.md)\.

1. In **Service role**, do one of the following:
   + Choose **New service role** to allow CodePipeline to create a service role in IAM\. In **Role name**, the role and policy name both default to this format: `AWSCodePipelineServiceRole-region-pipeline_name`\. For example, this is the service role for a pipeline named `MyPipeline`: `AWSCodePipelineServiceRole-eu-west-2-MyPipeline`\.
   + Choose **Existing service role** to use a service role already created in IAM\. In **Role name**, choose your service role from the list\.
**Note**  
Depending on when your service role was created, you might need to update its permissions to support other AWS services\. For information, see [Add Permissions to the CodePipeline Service Role](security-iam.md#how-to-update-role-new-services)\. 

   For more information about the service role and its policy statement, see [Manage the CodePipeline Service Role](security-iam.md#how-to-custom-role)\.

1. In **Artifact store**, do one of the following: 

   1. Choose **Default location** to use the default artifact store in the AWS Region you have selected for your pipeline\. This can be the S3 artifact bucket designated as the default\.

   1. Choose **Custom location** if you already have an artifact store, such as an S3 artifact bucket, in the same Region as your pipeline\.
**Note**  
This is not the source bucket for your source code\. This is the artifact store for your pipeline\. A separate artifact store, such as an S3 bucket, is required for each pipeline\. When you create or edit a pipeline, you must have an artifact bucket in the pipeline Region and one artifact bucket per AWS Region where you are running an action\.  
For more information, see [Input and Output Artifacts](welcome-introducing-artifacts.md) and [CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\.

1.  Choose **Next**\.

**Step 2: Create a source stage**

1. On the **Step 2: Add source stage** page, in **Source provider**, choose **Bitbucket Cloud**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/bitbucket-add-source.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Under **Connection**, choose an existing connection or choose **Connect to Bitbucket Cloud** to create one\. 

1. On the **Create connection to Bitbucket Cloud** page, in **Connection name**, enter the name for the connection you want to create\. The name helps you identify this connection later\.

   Under **Bitbucket Cloud apps**, choose an app installation or choose **Install a new app** to create one\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/newreview-source-wizard-bitbucket.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. On the app installation page, a message shows that the AWS CodeStar app is trying to connect to your Bitbucket account\. Choose **Grant access**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/bitbucket-access-popup.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. The connection ID for your new installation is displayed\. Choose **Complete connection**\. 

1. In **Repository name**, choose the name of your third\-party repository\. In **Branch name**, choose the branch where you want your pipeline to detect source changes\.

1. In **Output artifact format**, you must choose the format for your artifacts\. 
   + To store output artifacts from the Bitbucket action using the default method, choose **CodePipeline default**\. The action accesses the files from the Bitbucket repository and stores the artifacts in a ZIP file in the pipeline artifact store\.
   + To store a JSON file that contains a URL reference to the repository so that downstream actions can perform Git commands directly, choose **Full clone**\. This option can only be used by CodeBuild downstream actions\.

1. Choose **Next**\.

1. Finish creating your next stage with the wizard\.

**Review the pipeline**

1. On the **Step 5: Review** page, review your pipeline configuration, and then choose **Create pipeline** to create the pipeline or **Previous** to go back and edit your choices\.

1.  To exit the wizard without creating a pipeline, choose **Cancel**\.