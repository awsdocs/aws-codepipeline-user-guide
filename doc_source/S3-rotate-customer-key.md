# Configure Server\-Side Encryption for S3 Buckets When Using AWS CloudFormation or the CLI<a name="S3-rotate-customer-key"></a>

When you create a pipeline using AWS CloudFormation or the CLI, you must configure server\-side encryption manually\. Use the sample bucket policy above, and then create your own customer\-managed SSE\-KMS encryption keys\. You can also choose to use your own keys instead of the default Amazon S3 key in general\. Some reasons for doing so include:
+ You want to rotate the key on a schedule to meet business or security requirements for your organization\.
+ You want to create a pipeline that uses resources associated with another AWS account\. This requires the use of a customer\-managed key\. For more information, see [Create a Pipeline in CodePipeline That Uses Resources from Another AWS Account](pipelines-create-cross-account.md)\. 

Cryptographic best practices discourage extensive reuse of encryption keys\. As a best practice, rotate your key on a regular basis\. To create new cryptographic material for your AWS Key Management Service \(AWS KMS\) customer master keys \(CMKs\), you can create new CMKs, and then change your applications or aliases to use the new CMKs\. Or, you can enable automatic key rotation for an existing CMK\. 

To rotate your SSE\-KMS customer master key, see [Rotating Customer Master Keys](https://docs.aws.amazon.com/kms/latest/developerguide/rotate-keys.html)\. 