# Access Control with IAM Policies<a name="access-control"></a>

You can have valid credentials to authenticate your requests, but unless you have permissions you cannot create or access CodePipeline resources\. For example, you must have permissions to create, view, or delete pipelines; to retrieve information about applications and deployment groups in CodeDeploy, and about stacks, apps and layers in AWS OpsWorks Stacks; to add or retrive artifacts from Amazon Simple Storage Service buckets; and so on\.

The following sections describe how to manage permissions for CodePipeline\. We recommend that you read the overview first\.
+ [Overview of Managing Access Permissions to Your CodePipeline Resources](iam-access-control-identity-based.md)
+ [Using Identity\-Based Policies \(IAM Policies\) for CodePipeline](iam-identity-based-access-control.md)
+ [CodePipeline Permissions Reference](permissions-reference.md)

## Viewing Resources in the Console<a name="console-resources"></a>

The CodePipeline console requires the `ListRepositories` permission to display a list of repositories for your AWS account in the AWS Region where you are signed in\. The console also includes a **Go to resource** function to quickly perform a case insensitive search for resources\. This search is performed in your AWS account in the AWS Region where you are signed in\. The following resources are displayed across the following services:
+ AWS CodeBuild: Build projects
+ AWS CodeCommit: Repositories
+ AWS CodeDeploy: Applications
+ AWS CodePipeline: Pipelines

To perform this search across resources in all services, you must have the following permissions:
+ AWS CodeBuild: `ListProjects`
+ CodeCommit: `ListRepositories`
+ CodeDeploy: `ListApplications`
+ CodePipeline: `ListPipelines`

Results are not returned for a service's resources if you do not have permissions for that service\. Even if you have permissions for viewing resources, specific resources will not be returned if there is an explicit `Deny` to view those resources\.