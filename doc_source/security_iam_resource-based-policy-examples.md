# AWS CodePipeline resource\-based policy examples<a name="security_iam_resource-based-policy-examples"></a>

**Topics**

Other services, such as Amazon S3, also support resource\-based permissions policies\. For example, you can attach a policy to an S3 bucket to manage access permissions to that bucket\. Although CodePipeline doesn't support resource\-based policies, it does store artifacts to be used in pipelines in versioned S3 buckets\. 

**Example To create a policy for an S3 bucket to use as the artifact store for CodePipeline**  
You can use any versioned S3 bucket as the artifact store for CodePipeline\. If you use the **Create Pipeline** wizard to create your first pipeline, this S3 bucket is created for you to ensure that all objects uploaded to the artifact store are encrypted and connections to the bucket are secure\. If you create your own S3 bucket, as a best practice, consider adding the following policy or its elements to the bucket\. In this policy, the ARN for the S3 bucket is `codepipeline-us-east-2-1234567890`\. Replace this ARN with the ARN for your S3 bucket:  

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
            "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*",
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
            "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*",
            "Condition": {
                "Bool": {
                    "aws:SecureTransport": false
                }
            }
        }
    ]
}
```