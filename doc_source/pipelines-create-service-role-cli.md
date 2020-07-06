# Create the CodePipeline service role \(CLI\)<a name="pipelines-create-service-role-cli"></a>

Before you create a pipeline with the AWS CLI or AWS CloudFormation, create a CodePipeline service role for your pipeline\.

1. Use the IAM console or the AWS CLI to create a role and attach the service role policy\. To view the default service role policy, use the procedure in [Create the CodePipeline service role \(console\)](pipelines-create-service-role-console.md) to create the service role and view the default policy in the IAM console\. The policy name format is normally the same as the role name format\.

1. Use the service role ARN when you create your pipeline with the AWS CLI or AWS CloudFormation\. 

1. After you create it, your pipeline service role is available to view in your list of IAM roles, and you can view the service role ARN associated to a pipeline by running the `get-pipeline` command with the AWS CLI\.