--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# View Your Default Amazon S3 SSE\-KMS Encryption Keys<a name="S3-view-default-keys"></a>

When you use the Create Pipeline wizard to create your first pipeline, an Amazon S3 bucket is created for you in the same region you created the pipeline\. The bucket is used to store pipeline artifacts\. When a pipeline runs, artifacts are put into and retrieved from the Amazon S3 bucket\. By default, AWS CodePipeline uses server\-side encryption with the AWS KMS\-managed keys \(SSE\-KMS\) using the default key for Amazon S3 \(the `aws/s3` key\)\. This key is created and stored in your AWS account\. When artifacts are retrieved from the Amazon S3 bucket, AWS CodePipeline uses the same SSE\-KMS process to decrypt the artifact\. 

To view information about your default AWS KMS key, do the following:

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the service navigation pane, choose **Encryption Keys**\. \(If a welcome page appears, choose **Get Started Now**\.\)

1. In **Filter**, choose the region for your pipeline\. For example, if the pipeline was created in `us-east-2`, make sure the filter is set to US East \(Ohio\)\.

   For more information about the regions and endpoints available for AWS CodePipeline, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#codepipeline_region)\.

1. In the list of encryption keys, choose the key with the alias used for your pipeline \(by default, **aws/s3**\)\. Basic information about the key will be displayed\.