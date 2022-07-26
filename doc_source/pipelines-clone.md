# Clone a pipeline in CodePipeline<a name="pipelines-clone"></a>

You can use the CodePipeline console to clone an existing pipeline\.

CodePipeline uses the console to generate a new pipeline that is cloned from the original pipeline\. The following considerations apply when cloning a pipeline:
+ Each pipeline must have a unique name\. When you clone your pipeline, you will give it a new name\.
+ You can only clone a pipeline that does not contain any secrets\. In the pipeline JSON, in the action configuration fields for pipeline actions, secrets are shown as masked values \(\*\*\*\*\)\. Per security requirements, CodePipeline does not allow retrieval of the values for any secrets in the original pipeline\.
+ CodePipeline generates new Amazon CloudWatch Events resources for the cloned pipeline that detect changes in your CodeCommit source repository and branch, your Amazon ECR source repository, or your Amazon S3 source bucket\.

**Note**  
Because you use the console to create or edit a pipeline, the change detection resources are created for you\. The cloning feature is only available in the console\. You cannot use the AWS CLI to clone the pipeline\.

****

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. Choose **Pipelines**, and then choose the pipeline you want to clone\.

1. Choose **Clone pipeline**\.

1. In **Pipeline name**, enter a name for your new pipeline\.
**Note**  
After you create a pipeline, you cannot change its name\. For information about other limitations, see [Quotas in AWS CodePipeline](limits.md)\.

1. In **Service role**, do one of the following:
   + Choose **New service role** to allow CodePipeline to create a new service role in IAM\.
   + Choose **Existing service role** to use a service role already created in IAM\. In **Role ARN**, choose your service role ARN from the list\.
**Note**  
Depending on when your service role was created, you might need to update its permissions to support additional AWS services\. For information, see [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services)\. 

   For more information about the service role and its policy statement, see [Manage the CodePipeline service role](security-iam.md#how-to-custom-role)\.

1. In **Artifact store**, do one of the following: 

   1. Choose **Default location** to use the default artifact store, such as the S3 artifact bucket designated as the default, for your pipeline in the AWS Region you have selected for your pipeline\.

   1. Choose **Custom location** if you already have an artifact store, such as an S3 artifact bucket, in the same Region as your pipeline\. In **Bucket**, choose the bucket name\.
**Note**  
This is not the source bucket for your source code\. This is the artifact store for your pipeline\. A separate artifact store, such as an S3 bucket, is required for each pipeline\. When you create or edit a pipeline, you must have an artifact bucket in the pipeline Region and one artifact bucket per AWS Region where you are running an action\.  
For more information, see [Input and output artifacts](welcome-introducing-artifacts.md) and [CodePipeline pipeline structure reference](reference-pipeline-structure.md)\.

1. In **Encryption key**, do one of the following: 

   1. To use the CodePipeline default AWS KMS key to encrypt the data in the pipeline artifact store \(S3 bucket\), choose **Default AWS Managed Key**\.

   1. To use your customer managed key to encrypt the data in the pipeline artifact store \(S3 bucket\), choose **Customer Managed Key**\. Choose the key ID, key ARN, or alias ARN\.

1.  Choose **Clone**\.