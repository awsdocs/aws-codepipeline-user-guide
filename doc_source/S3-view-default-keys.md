# View Your Default Amazon S3 SSE\-KMS Encryption Keys<a name="S3-view-default-keys"></a>

When you use the Create Pipeline wizard to create your first pipeline, an Amazon S3 bucket is created for you in the same region you created the pipeline\. The bucket is used to store pipeline artifacts\. When a pipeline runs, artifacts are put into and retrieved from the Amazon S3 bucket\. By default, CodePipeline uses server\-side encryption with the AWS KMS\-managed keys \(SSE\-KMS\) using the default key for Amazon S3 \(the `aws/s3` key\)\. This key is created and stored in your AWS account\. When artifacts are retrieved from the Amazon S3 bucket, CodePipeline uses the same SSE\-KMS process to decrypt the artifact\. 

To view information about your default AWS KMS key, do the following:

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. To view the keys in your account that AWS creates and manages for you, in the navigation pane, choose **AWS managed keys**\. \(If a welcome page appears, choose **Create a key**, and then choose **AWS managed keys**\.\)

1. In the list of encryption keys, choose the key with the alias used for your pipeline \(by default, **aws/s3**\)\. Basic information about the key will be displayed\.