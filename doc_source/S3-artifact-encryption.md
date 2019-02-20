# Configure Server\-Side Encryption for Artifacts Stored in Amazon S3 for CodePipeline<a name="S3-artifact-encryption"></a>

There are two ways to configure server\-side encryption for Amazon S3 artifacts:
+ CodePipeline creates an Amazon S3 artifact bucket and default AWS\-managed SSE\-KMS encryption keys when you create a pipeline using the Create Pipeline wizard\. The master key is encrypted along with object data and managed by AWS\.
+ You can create and manage your own customer\-managed SSE\-KMS keys\.

If you are using the default Amazon S3 key, you cannot change or delete this AWS\-managed key\. If you are using a customer\-managed key in AWS KMS to encrypt or decrypt artifacts in the Amazon S3 bucket, you can change or rotate this key as necessary\.

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
            "Resource": "arn:aws:s3:::codepipeline-us-west-2-890506445442/*",
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
            "Resource": "arn:aws:s3:::codepipeline-us-west-2-890506445442/*",
            "Condition": {
                "Bool": {
                    "aws:SecureTransport": "false"
                }
            }
        }
    ]
}
```

For more information about server\-side encryption and AWS KMS, see [Protecting Data Using Server\-Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html) and [\.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html)

For more information about AWS KMS, see the [AWS Key Management Service Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/) and [ and [https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html)](https://docs.aws.amazon.com/AmazonS3/latest/dev/bucket-encryption.html)

**Topics**
+ [View Your Default Amazon S3 SSE\-KMS Encryption Keys](S3-view-default-keys.md)
+ [Configure Server\-Side Encryption for S3 Buckets When Using AWS CloudFormation or the CLI](S3-rotate-customer-key.md)