# Create the CodePipeline Service Role<a name="pipelines-create-service-role"></a>

When you create a pipeline, you create a service role or use an existing service role\.

You can use the CodePipeline console or the AWS CLI to create an CodePipeline service role\. A service role is required to create a pipeline, and the pipeline is always associated to that service role\.

The service role is not an AWS managed role but is created initially for pipeline creation, and then as new permissions are added to the service role policy, you may need to update the service role for your pipeline\. Once your pipeline is created with a service role, you cannot apply a different service role to that pipeline\. Attach the recommended policy to the service role as detailed in [Review the Default CodePipeline Service Role Policy](how-to-custom-role.md#view-default-service-role-policy)\.

For more information about the service role and its policy statement, see [Manage the CodePipeline Service Role](how-to-custom-role.md)\.