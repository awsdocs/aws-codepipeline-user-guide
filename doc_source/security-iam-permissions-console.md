# Permissions required to use the CodePipeline console<a name="security-iam-permissions-console"></a>

To use CodePipeline in the CodePipeline console, you must have a minimum set of permissions from the following services:
+ AWS Identity and Access Management
+ Amazon Simple Storage Service

These permissions allow you to describe other AWS resources for your AWS account\.

Depending on the other services you incorporate into your pipelines, you might need permissions from one or more of the following:
+ AWS CodeCommit
+ CodeBuild
+ AWS CloudFormation
+ AWS CodeDeploy
+ AWS Elastic Beanstalk
+ AWS Lambda
+ AWS OpsWorks

If you create an IAM policy that is more restrictive than the minimum required permissions, the console won't function as intended for users with that IAM policy\. To ensure that those users can still use the CodePipeline console, also attach the `AWSCodePipelineReadOnlyAccess` managed policy to the user, as described in [AWS managed \(predefined\) policies for CodePipeline](managed-policies.md)\.

You don't need to allow minimum console permissions for users who are making calls to the AWS CLI or the CodePipeline API\.