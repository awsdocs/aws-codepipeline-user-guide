# Configure server\-side encryption for artifacts stored in Amazon S3 for CodePipeline<a name="S3-artifact-encryption"></a>

There are two ways to configure server\-side encryption for Amazon S3 artifacts:
+ CodePipeline creates an S3 artifact bucket and default AWS managed CMK when you create a pipeline using the Create Pipeline wizard\. The AWS managed CMK is encrypted along with object data and managed by AWS\.
+ You can create and manage your own customer managed CMK\.

**Important**  
CodePipeline only supports symmetric customer master keys \(CMKs\)\. Do not use an asymmetric CMK to encrypt the data in your S3 bucket\.

If you are using the default S3 key, you cannot change or delete this AWS managed CMK\. If you are using a customer managed CMK in AWS KMS to encrypt or decrypt artifacts in the S3 bucket, you can change or rotate this customer managed CMK as necessary\.

Amazon S3 supports bucket policies that you can use if you require server\-side encryption for all objects that are stored in your bucket\. For example, the following bucket policy denies upload object \(`s3:PutObject`\) permission to everyone if the request does not include the `x-amz-server-side-encryption` header requesting server\-side encryption with SSE\-KMS\.

```
{
    "Version": "2012-10-17",
    "Id": "SSEAndSSLPolicy",
    "Statement": [
        {
            "Sid": "DenyUnEncryptedObjectUploads",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::codepipeline-us-west-2-89050EXAMPLE/*",
            "Condition": {
                "StringNotEquals": {
                    "s3:x-amz-server-side-encryption": "aws:kms"
                }
            }
        },
        {
            "Sid": "DenyInsecureConnections",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:*",
            "Resource": "arn:aws:s3:::codepipeline-us-west-2-89050EXAMPLE/*",
            "Condition": {
                "Bool": {
                    "aws:SecureTransport": "false"
                }
            }
        }
    ]
}
```

For more information about server\-side encryption and AWS KMS, see [Protecting Data Using Server\-Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html) and [Protecting Data Using Server\-Side Encryption with CMKs Stored in AWS Key Management Service \(SSE\-KMS\)](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html)\.

For more information about AWS KMS, see the [AWS Key Management Service Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/)\.

**Topics**
+ [View your AWS managed CMK](#S3-view-default-keys)
+ [Configure server\-side encryption for S3 buckets using AWS CloudFormation or the AWS CLI](#S3-rotate-customer-key)

### View your AWS managed CMK<a name="S3-view-default-keys"></a>

When you use the **Create Pipeline** wizard to create your first pipeline, an S3 bucket is created for you in the same Region you created the pipeline\. The bucket is used to store pipeline artifacts\. When a pipeline runs, artifacts are put into and retrieved from the S3 bucket\. By default, CodePipeline uses server\-side encryption with AWS KMS using the AWS managed CMK for Amazon S3 \(the `aws/s3` key\)\. This AWS managed CMK is created and stored in your AWS account\. When artifacts are retrieved from the S3 bucket, CodePipeline uses the same SSE\-KMS process to decrypt the artifact\.

**To view information about your AWS managed CMK**

1. Sign in to the AWS Management Console and open the AWS KMS console\.

1. If a welcome page appears, choose **Get Started Now**\.

1. In the service navigation pane, choose **AWS managed keys**\. 

1. Choose the Region for your pipeline\. For example, if the pipeline was created in `us-east-2`, make sure that the filter is set to US East \(Ohio\)\.

   For more information about the Regions and endpoints available for CodePipeline, see [AWS CodePipeline endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/codepipeline.html)\.

1. In the list, choose the key with the alias used for your pipeline \(by default, **aws/s3**\)\. Basic information about the key is displayed\.



### Configure server\-side encryption for S3 buckets using AWS CloudFormation or the AWS CLI<a name="S3-rotate-customer-key"></a>

When you use AWS CloudFormation or the AWS CLI to create a pipeline, you must configure server\-side encryption manually\. Use the sample bucket policy above, and then create your own customer managed CMK\. You can also use your own keys instead of the AWS managed CMK\. Some reasons to choose your own key include:
+ You want to rotate the key on a schedule to meet business or security requirements for your organization\.
+ You want to create a pipeline that uses resources associated with another AWS account\. This requires the use of a customer managed CMK\. For more information, see [Create a pipeline in CodePipeline that uses resources from another AWS account](pipelines-create-cross-account.md)\. 

Cryptographic best practices discourage extensive reuse of encryption keys\. As a best practice, rotate your key on a regular basis\. To create new cryptographic material for your AWS KMS keys, you can create a customer managed CMK, and then change your applications or aliases to use the new customer managed CMK\. Or, you can enable automatic key rotation for an existing customer managed CMK\. 

To rotate your customer managed CMK, see [Rotating Customer Master Keys](https://docs.aws.amazon.com/kms/latest/developerguide/rotate-keys.html)\. 

**Important**  
CodePipeline only supports symmetric customer master keys \(CMKs\)\. Do not use an asymmetric CMK to encrypt the data in your S3 bucket\.