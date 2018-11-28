--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Authentication, Access Control, and Security Configuration for AWS CodePipeline<a name="auth-and-access-control"></a>

Access to AWS CodePipeline requires credentials\. Those credentials must have permissions to access AWS resources, such as retrieving artifacts from Amazon S3 buckets; accessing information about applications and deployment groups in AWS CodeDeploy; and granting permission to approve or reject a manual approval action\. The following sections provide details on how you can use [AWS Identity and Access Management \(IAM\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) and AWS CodePipeline to help secure access to your resources:
+ [Authentication](authentication.md)
+ [Access Control with IAM Policies](access-control.md)
+ [Security Configuration](security-configuration.md)