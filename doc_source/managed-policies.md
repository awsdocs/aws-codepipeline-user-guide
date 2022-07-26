# AWS managed policies for CodePipeline<a name="managed-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. Managed policies grant necessary permissions for common use cases so you can avoid having to investigate which permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies, which you can attach to users in your account, are specific to CodePipeline:
+ `AWSCodePipeline_FullAccess` – Grants full access to CodePipeline\. To view the JSON policy document in the IAM console, see [AWSCodePipeline\_FullAccess](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AWSCodePipeline_FullAccess)\.
+ `AWSCodePipeline_ReadOnlyAccess` – Grants read\-only access to CodePipeline\. To view the JSON policy document in the IAM console, see [AWSCodePipeline\_ReadOnlyAccess](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AWSCodePipeline_ReadOnlyAccess)\.
+ `AWSCodePipelineApproverAccess` – Grants permission to an IAM user to approve or reject a manual approval action\. To view the JSON policy document in the IAM console, see [AWSCodePipelineApproverAccess](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AWSCodePipelineApproverAccess)\.
+ `AWSCodePipelineCustomActionAccess` – Grants permission to an IAM user to create custom actions in CodePipeline or integrate Jenkins resources for build or test actions\. To view the JSON policy document in the IAM console, see [AWSCodePipelineCustomActionAccess](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AWSCodePipelineApproverAccess)\.

**Important**  
The AWS managed policies `AWSCodePipelineFullAccess` and `AWSCodePipelineReadOnlyAccess` have been replaced\. Use the `AWSCodePipeline_FullAccess` and `AWSCodePipeline_ReadOnlyAccess` policies\.

## AWS CodePipeline updates to AWS managed policies<a name="security-iam-awsmanpol-updates"></a>



View details about updates to AWS managed policies for CodePipeline since this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the CodePipeline [ Document history](https://docs.aws.amazon.com/codepipeline/latest/userguide/history.html) page\.




| Change | Description | Date | 
| --- | --- | --- | 
|  CodePipeline started tracking changes  |  CodePipeline started tracking changes for its AWS managed policies\.  | March 12, 2021 | 