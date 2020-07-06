# AWS managed \(predefined\) policies for CodePipeline<a name="managed-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. Managed policies grant necessary permissions for common use cases so you can avoid having to investigate which permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies, which you can attach to users in your account, are specific to CodePipeline:
+ `AWSCodePipelineFullAccess` – Grants full access to CodePipeline\.
+ `AWSCodePipelineCustomActionAccess` – Grants permission to an IAM user to create custom actions in CodePipeline or integrate Jenkins resources for build or test actions\.
+ `AWSCodePipelineReadOnlyAccess` – Grants read\-only access to CodePipeline\.
+ `AWSCodePipelineApproverAccess` – Grants permission to an IAM user to approve or reject a manual approval action\.