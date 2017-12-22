# Permissions Required to Use the AWS CodePipeline Console<a name="console-permissions"></a>

For a user to work with AWS CodePipeline in the AWS CodePipeline console, that user must have a minimum set of permissions that allows the user to describe other AWS resources for their AWS account\. In order to use AWS CodePipeline in the AWS CodePipeline console, you must have permissions from the following services:

+ AWS Identity and Access Management

+ Amazon Simple Storage Service

Depending on the other services you incorporate into your pipelines, you may need permissions from one or more of the following:

+ AWS CodeCommit

+ AWS CodeBuild

+ AWS CloudFormation

+ AWS CodeDeploy

+ AWS Elastic Beanstalk

+ AWS Lambda

+ AWS OpsWorks

If you create an IAM policy that is more restrictive than the minimum required permissions, the console won't function as intended for users with that IAM policy\. To ensure that those users can still use the AWS CodePipeline console, also attach the `AWSCodePipelineReadOnlyAccess` managed policy to the user, as described in [AWS Managed \(Predefined\) Policies for AWS CodePipeline](managed-policies.md)\.

You don't need to allow minimum console permissions for users that are making calls only to the AWS CLI or the AWS CodePipeline API\.