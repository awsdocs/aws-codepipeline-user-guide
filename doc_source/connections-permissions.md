# IAM Permissions Reference for Connections<a name="connections-permissions"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

CodePipeline integrates with AWS CodeStar connections, a feature that allows you to connect your pipelines to third\-party code repositories\. To create connections for your AWS resources, you must have the required IAM permissions\. For more information about connections, see [Working with Connections in CodePipeline](connections.md)\. For information about updating your CodePipeline service role in IAM to use connections, see [Add Permissions to the CodePipeline Service Role](security-iam.md#how-to-update-role-new-services)\.

## Permissions for Managing Connections<a name="connections-permissions-actions"></a>

A role or user designated to use the AWS CLI or SDK to create, update, and delete connections should have permissions limited to the following\.

**Note**  
You cannot complete or use a connection in the console with only the following permissions\. You need to add the permissions in the next section, along with the `UseConnection` permission\.

See the example policy in [Example: A Policy for Creating AWS CodeStar Connections with the CLI and Viewing with the Console](#iam-policy-examples-connections-clisdk)\.

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

A role or user designated to manage connections in the console should have the permissions required to complete a connection in the console and create an installation, which includes authorizing the handshake to the provider and creating installations for connections to use\. Use the following permissions in addition to the permissions above\.

The following IAM operations are used by the console when performing a browser\-based handshake\. These are IAM policy permissions\. They are not API actions\.

```
codestar-connections:GetIndividualAccessToken
codestar-connections:GetInstallationUrl
codestar-connections:ListInstallationTargets
codestar-connections:StartOAuthHandshake
codestar-connections:UpdateConnectionInstallation
```

These operations support the following condition keys:


| Action | Condition keys | 
| --- | --- | 
|  `codestar-connections:ListInstallationTargets`  |  N/A  | 
|  codestar\-connections:GetInstallationUrl | codestar\-connections:ProviderType | 
|  codestar\-connections:StartOAuthHandshake | codestar\-connections:ProviderType | 
| codestar\-connections:UpdateConnectionInstallation | codestar\-connections:InstallationId | 
|  codestar\-connections:GetIndividualAccessToken | codestar\-connections:ProviderType | 

Based on this, the following permissions are needed to use, create, update, or delete a connection in the console: 

```
codestar-connections:CreateConnection
codestar-connections:DeleteConnection
codestar-connections:GetConnection
codestar-connections:ListConnections
codestar-connections:UseConnection
codestar-connections:ListInstallationTargets
codestar-connections:GetInstallationUrl
codestar-connections:StartOAuthHandshake
codestar-connections:UpdateConnectionInstallation
codestar-connections:GetIndividualAccessToken
```

See the example policy in [Example: A Policy for Creating AWS CodeStar Connections with the Console](#iam-policy-examples-connections-console)\.

## Permissions for Tagging Connection Resources<a name="connections-permissions-tags"></a>

The following IAM operations are used when tagging connection resources\.

```
codestar-connections:ListTagsForResource
codestar-connections:TagResource
codestar-connections:UntagResource
```

## Passing a Connection to a Service<a name="connections-passconnection"></a>

When a connection is passed to a service \(for example, when a connection ARN is provided in a pipeline definition to create or update a pipeline\) the user must have the `codestar-connections:PassConnection` permission\. `PassConnection` is an IAM policy permission\. It is not an API action\.

This operation also supports the following condition key:
+ `codestar-connections:PassedToService`


**Supported values for condition keys**  

| Key | Valid Action Providers | 
| --- | --- | 
|  `codestar-connections:PassedToService`  |   [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/connections-permissions.html)    | 

## Using a Connection<a name="connections-use"></a>

When a service like CodePipeline uses a connection, the service role must have the `codestar-connections:UseConnection` permission for a given connection\. `UseConnection` is an IAM policy permission\. It is not an API action\.

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
|  `codestar-connections:ProviderPermissionsRequired`  |  read\_only or read\_write  | 
|  `codestar-connections:ProviderAction`  |  `GetBranch`, `ListRepositories`, `ListOwners`, `ListBranches`, `StartUploadArchiveToS3`, `GitPush`, `GitPull`, `GetUploadArchiveToS3Status` For information, see [Supported Values for ProviderAction](#connections-use-provider)\.  | 

The required condition keys for some functionality might change over time\. We recommend that you use `codestar-connections:UseConnection` to control access to a connection unless your access control requirements require different permissions\.

## Supported Values for ProviderAction<a name="connections-use-provider"></a>

When a connection is used by an AWS service, it results in API calls being made to your source code provider\. For example, a service might list repositories for a Bitbucket connection by calling the `https://api.bitbucket.org/2.0/repositories/username` API\.

The `ProviderAction` condition key allows you to restrict which APIs on a provider can be called\. Because the API path might be generated dynamically and the path vares from provider to provider, the `ProviderAction` value is mapped to an abstract action name rather than the URL of the API\. This allows you to write policies that have the same effect regardless of the provider type for the connection\.

The following are the access types that are granted for each of the supported `ProviderAction` values:
+ `CreatePullRequestDiffComment`: Required to access comments on a pull request\.
+ `GetBranch`: Accesses information about a branch, such as the latest commit for that branch\.
+ `GetPullRequest`: Required to view pull requests for a repository\.
+ `ListBranchCommits`: Required to view a list of commits for a repository branch\.
+ `ListCommitFiles`: Required to view a list of files for a commit\.
+ `ListPullRequestComments`: Required to view a list of comments for a pull request\.
+ `ListPullRequestCommits`: Required to view a list of commits for a pull request\.
+ `ListRepositories`: Accesses a list of public and private repositories, including details about those repositories, that belong to an owner\.
+ `ListOwners`: Accesses a list of owners that the connection has access to\.
+ `ListBranches`: Accesses the list of branches that exist on a given repository\.
+ `StartUploadArchiveToS3`: Reads source code and uploads it to Amazon S3\.
+ `GitPush`: Writes to a repository using Git\.
+ `GitPull`: Reads from a repository using Git\.
+ `GetUploadArchiveToS3Status`: Accesses the status of an upload, including any error messages, started by `StartUploadArchiveToS3`\.

## Example: A Policy for Creating AWS CodeStar Connections with the CLI and Viewing with the Console<a name="iam-policy-examples-connections-clisdk"></a>

A role or user designated to use the AWS CLI or SDK to view, create, tag, or delete connections should have permissions limited to the following\.

**Note**  
You cannot complete a connection in the console with only the following permissions\. You need to add the permissions in the next section\.

To use the console to view a list of available connections, view tags, and use a connection, use the following policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
            "codestar-connections:CreateConnection",
            "codestar-connections:DeleteConnection",
            "codestar-connections:UseConnection",
            "codestar-connections:GetConnection",
            "codestar-connections:ListConnections",
            "codestar-connections:TagResource",
            "codestar-connections:ListTagsForResource",
            "codestar-connections:UntagResource"
        ],
        "Resource": "*"
     }
   ]
}
```

## Example: A Policy for Creating AWS CodeStar Connections with the Console<a name="iam-policy-examples-connections-console"></a>

A role or user designated to manage connections in the console should have the permissions required to complete a connection in the console and create an installation, which includes authorizing the handshake to the provider and creating installations for connections to use\. `UseConnection` should also be added to use the connection in the console\. Use the following policy to view, use, create, or delete a connection in the console\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codestar-connections:CreateConnection",
                "codestar-connections:DeleteConnection",
                "codestar-connections:GetConnection",
                "codestar-connections:ListConnections",
                "codestar-connections:GetInstallationUrl",
                "codestar-connections:GetIndividualAccessToken",
                "codestar-connections:ListInstallationTargets",
                "codestar-connections:StartOAuthHandshake",
                "codestar-connections:UpdateConnectionInstallation",
                "codestar-connections:UseConnection"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```