# Create the CodePipeline Service Role \(CLI\)<a name="pipelines-create-service-role-cli"></a>

Before you create a pipeline with the AWS CLI or AWS CloudFormation, create a CodePipeline service role for your pipeline\.

1. Use the IAM console or the AWS CLI to create a role with the policy detailed in [Review the Default CodePipeline Service Role Policy](how-to-custom-role.md#view-default-service-role-policy)\. The policy name format is normally the same as the role name format\.

1. Use the service role ARN when you create your pipeline with the AWS CLI or AWS CloudFormation\. 

1. After you create it, your pipeline service role is available to view in your list of IAM roles, and you can view the service role ARN associated to a pipeline by running the `get-pipeline` command with the AWS CLI\.