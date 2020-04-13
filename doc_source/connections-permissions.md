# IAM Permissions Reference for Connections<a name="connections-permissions"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

CodePipeline integrates with AWS CodeStar connections, a feature that allows you to connect your pipelines to third\-party code repositories\. To create connections for your AWS resources, you must have the required IAM permissions\. For more information about connections, see [Working with Connections in CodePipeline](connections.md)\. For information about updating your CodePipeline service role in IAM to use connections, see [Add Permissions to the CodePipeline Service Role](security-iam.md#how-to-update-role-new-services)\.

## Permissions for Managing Connections<a name="connections-permissions-actions"></a>

The following IAM operations are supported in the AWS CLI and SDKs to create, update, and delete connections\.

```
codestar-connections:CreateConnection
codestar-connections:DeleteConnection
codestar-connections:GetConnection
codestar-connections:ListConnections
```

These operations support the following condition keys:


| Action | Condition keys | 
| --- | --- | 
|  `codestar-connections:CreateConnection`  |  `codestar-connections:ProviderType`  | 
|  codestar\-connections:DeleteConnection | N/A | 
| codestar\-connections:GetConnection | N/A | 
| codestar\-connections:ListConnections | codestar\-connections:ProviderTypeFilter | 

## Permissions for the Connection Handshake<a name="connections-permissions-actions-handshake"></a>

The following IAM operations are used by the console when performing a browser\-based handshake\.

```
codestar-connections:ListInstallationTargets
codestar-connections:GetInstallationUrl
codestar-connections:StartOAuthHandshake
codestar-connections:UpdateConnectionInstallation
codestar-connections:GetIndividualAccessToken
```

These operations support the following condition keys:


| Action | Condition keys | 
| --- | --- | 
|  `codestar-connections:ListInstallationTargets`  |  N/A  | 
|  codestar\-connections:GetInstallationUrl | codestar\-connections:ProviderType | 
|  codestar\-connections:StartOAuthHandshake | codestar\-connections:ProviderType | 
| codestar\-connections:UpdateConnectionInstallation | codestar\-connections:InstallationId | 
|  codestar\-connections:GetIndividualAccessToken | codestar\-connections:ProviderType | 

## Passing a Connection to a Service<a name="connections-passconnection"></a>

When a connection is passed to a service \(for example, when a connection ARN is provided in a pipeline definition to create or update a pipeline\) the user must have the `codestar-connections:PassConnection` permission\.

This operation also supports the following condition key:
+ `codestar-connections:PassedToService`


**Supported values for condition keys**  

| Key | Valid Action Providers | 
| --- | --- | 
|  `codestar-connections:PassedToService`  |  `codepipeline.amazonaws.com`  | 

## Using a Connection<a name="connections-use"></a>

When a service like CodePipeline uses a connection, the service role must have the `codestar-connections:UseConnection` permission for a given connection\.

This operation also supports the following condition keys:
+ `codestar-connections:BranchName`
+ `codestar-connections:FullRepositoryId`
+ `codestar-connections:OwnerId`
+ `codestar-connections:ProviderAction`
+ `codestar-connections:ProviderPermissionsRequired`
+ `codestar-connections:RepositoryName`


**Supported values for condition keys**  

| Key | Valid Action Providers | 
| --- | --- | 
|  `codestar-connections:FullRepositoryId`  |  The user name and repository name of a Bitbucket repository, such as `my-owner/my-repository`\. Supported only when the connection is being used to access a specific repository\.  | 
|  `codestar-connections:ProviderActionType`  |  read\_only or read\_write  | 
|  `codestar-connections:ProviderAction`  |  `GetBranch`, `ListRepositories`, `ListOwners`, `ListBranches`, `StartUploadArchiveToS3`, `GitPush`, `GitPull`, `GetUploadArchiveToS3Status` For information, see [Supported Values for ProviderAction](#connections-use-provider)\.  | 

The required condition keys for some functionality might change over time\. We recommend that you use `codestar-connections:UseConnection` to control access to a connection unless your access control requirements require different permissions\.

### Supported Values for ProviderAction<a name="connections-use-provider"></a>

When a connection is used by an AWS service, it results in API calls being made to your source code provider\. For example, a service might list repositories for a Bitbucket connection by calling the `https://api.bitbucket.org/2.0/repositories/username` API\.

The `ProviderAction` condition key allows you to restrict which APIs on a provider can be called\. Because the API path might be generated dynamically and the path vares from provider to provider, the `ProviderAction` value is mapped to an abstract action name rather than the URL of the API\. This allows you to write policies that have the same effect regardless of the provider type for the connection\.

The following are the access types that are granted for each of the supported `ProviderAction` values:
+ `GetBranch`: Accesses information about a branch, such as the latest commit for that branch
+ `ListRepositories`: Accesses a list of public and private repositories, including details about those repositories, that belong to an owner\. 
+ `ListOwners`: Accesses a list of owners that the connection has access to\.
+ `ListBranches`: Accesses the list of branches that exist on a given repository\.
+ `StartUploadArchiveToS3`: Reads source code and uploads it to Amazon S3\.
+ `GitPush`: Writes to a repository using Git\.
+ `GitPull`: Reads from a repository using Git\.
+ `GetUploadArchiveToS3Status`: Accesses the status of an upload, including any error messages, started by `StartUploadArchiveToS3`\.