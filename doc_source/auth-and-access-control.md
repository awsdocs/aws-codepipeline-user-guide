# Authentication, Access Control, and Security Best Practices for AWS CodePipeline<a name="auth-and-access-control"></a>

Access to AWS CodePipeline requires credentials\. Those credentials must have permissions to access AWS resources, such as retrieving artifacts from Amazon S3 buckets; accessing information about applications and deployment groups in AWS CodeDeploy; and granting permission to approve or reject a manual approval action\. The following sections provide details on how you can use [AWS Identity and Access Management \(IAM\)](http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) and AWS CodePipeline to help secure access to your resources:

+ [Authentication](authentication.md)

+ [Access Control with IAM Policies](access-control.md)

+ [Security Best Practices](best-practices-security.md)