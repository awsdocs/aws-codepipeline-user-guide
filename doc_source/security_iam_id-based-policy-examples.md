# AWS CodePipeline Identity\-Based Policy Examples<a name="security_iam_id-based-policy-examples"></a>

By default, IAM users and roles don't have permission to create or modify CodePipeline resources\. They also can't perform tasks using the AWS Management Console, AWS CLI, or AWS API\. An IAM administrator must create IAM policies that grant users and roles permission to perform specific API operations on the specified resources they need\. The administrator must then attach those policies to the IAM users or groups that require those permissions\.

To learn how to create an IAM identity\-based policy using these example JSON policy documents, see [Creating Policies on the JSON Tab](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-json-editor) in the *IAM User Guide*\.

**Topics**
+ [Policy Best Practices](#security_iam_service-with-iam-policy-best-practices)
+ [Viewing Resources in the Console](#security-iam-resources-console)
+ [Allow Users to View Their Own Permissions](#security_iam_id-based-policy-examples-view-own-permissions)
+ [Identity\-Based Policies \(IAM\) Examples](#security-iam-id-policies-examples)
+ [Using Tags to Control Access to CodePipeline Resources](tag-based-access-control.md)
+ [Permissions Required to Use the CodePipeline Console](#security-iam-permissions-console)
+ [AWS Managed \(Predefined\) Policies for CodePipeline](#managed-policies)
+ [Customer Managed Policy Examples](#customer-managed-policies)

## Policy Best Practices<a name="security_iam_service-with-iam-policy-best-practices"></a>

Identity\-based policies are very powerful\. They determine whether someone can create, access, or delete CodePipeline resources in your account\. These actions can incur costs for your AWS account\. When you create or edit identity\-based policies, follow these guidelines and recommendations:
+ **Get Started Using AWS Managed Policies** – To start using CodePipeline quickly, use AWS managed policies to give your employees the permissions they need\. These policies are already available in your account and are maintained and updated by AWS\. For more information, see [Get Started Using Permissions With AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#bp-use-aws-defined-policies) in the *IAM User Guide*\.
+ **Grant Least Privilege** – When you create custom policies, grant only the permissions required to perform a task\. Start with a minimum set of permissions and grant additional permissions as necessary\. Doing so is more secure than starting with permissions that are too lenient and then trying to tighten them later\. For more information, see [Grant Least Privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege) in the *IAM User Guide*\.
+ **Enable MFA for Sensitive Operations** – For extra security, require IAM users to use multi\-factor authentication \(MFA\) to access sensitive resources or API operations\. For more information, see [Using Multi\-Factor Authentication \(MFA\) in AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) in the *IAM User Guide*\.
+ **Use Policy Conditions for Extra Security** – To the extent that it's practical, define the conditions under which your identity\-based policies allow access to a resource\. For example, you can write conditions to specify a range of allowable IP addresses that a request must come from\. You can also write conditions to allow requests only within a specified date or time range, or to require the use of SSL or MFA\. For more information, see [IAM JSON Policy Elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

## Viewing Resources in the Console<a name="security-iam-resources-console"></a>

The CodePipeline console requires the `ListRepositories` permission to display a list of repositories for your AWS account in the AWS Region where you are signed in\. The console also includes a **Go to resource** function to quickly perform a case insensitive search for resources\. This search is performed in your AWS account in the AWS Region where you are signed in\. The following resources are displayed across the following services:
+ AWS CodeBuild: Build projects
+ AWS CodeCommit: Repositories
+ AWS CodeDeploy: Applications
+ AWS CodePipeline: Pipelines

To perform this search across resources in all services, you must have the following permissions:
+ CodeBuild: `ListProjects`
+ CodeCommit: `ListRepositories`
+ CodeDeploy: `ListApplications`
+ CodePipeline: `ListPipelines`

Results are not returned for a service's resources if you do not have permissions for that service\. Even if you have permissions for viewing resources, some resources are not returned if there is an explicit `Deny` to view those resources\.

## Allow Users to View Their Own Permissions<a name="security_iam_id-based-policy-examples-view-own-permissions"></a>

This example shows how you might create a policy that allows IAM users to view the inline and managed policies that are attached to their user identity\. This policy includes permissions to complete this action on the console or programmatically using the AWS CLI or AWS API\.

```
{
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "ViewOwnUserInfo",
               "Effect": "Allow",
               "Action": [
                   "iam:GetUserPolicy",
                   "iam:ListGroupsForUser",
                   "iam:ListAttachedUserPolicies",
                   "iam:ListUserPolicies",
                   "iam:GetUser"
               ],
               "Resource": [
                   "arn:aws:iam::*:user/${aws:username}"
               ]
           },
           {
               "Sid": "NavigateInConsole",
               "Effect": "Allow",
               "Action": [
                   "iam:GetGroupPolicy",
                   "iam:GetPolicyVersion",
                   "iam:GetPolicy",
                   "iam:ListAttachedGroupPolicies",
                   "iam:ListGroupPolicies",
                   "iam:ListPolicyVersions",
                   "iam:ListPolicies",
                   "iam:ListUsers"
               ],
               "Resource": "*"
           }
       ]
   }
```

## Identity\-Based Policies \(IAM\) Examples<a name="security-iam-id-policies-examples"></a>

You can attach policies to IAM identities\. For example, you can do the following: 
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permissions to view pipelines in the CodePipeline console, you can attach a permissions policy to a user or group that the user belongs to\.
+ **Attach a permissions policy to a role \(grant cross\-account permissions\)** – You can attach an identity\-based permissions policy to an IAM role to grant cross\-account permissions\. For example, the administrator in Account A can create a role to grant cross\-account permissions to another AWS account \(for example, Account B\) or an AWS service as follows:

  1. Account A administrator creates an IAM role and attaches a permissions policy to the role that grants permissions on resources in Account A\.

  1. Account A administrator attaches a trust policy to the role identifying Account B as the principal who can assume the role\. 

  1. Account B administrator can then delegate permissions to assume the role to any users in Account B\. Doing this allows users in Account B to create or access resources in Account A\. The principal in the trust policy an also be an AWS service principal if you want to grant an AWS service permissions to assume the role\.

  For more information about using IAM to delegate permissions, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

The following shows an example of a permissions policy that allows a user to enable and disable all stage transitions in the pipeline named `MyFirstPipeline` in the `us-west-2 region`:

```
{
  "Version": "2012-10-17",
  "Statement" : [
    {
      "Effect" : "Allow",
      "Action" : [
        "codepipeline:EnableStageTransition",
        "codepipeline:DisableStageTransition"
      ],
      "Resource" : [
        "arn:aws:codepipeline:us-west-2:111222333444:MyFirstPipeline"
      ]
    }
  ]
}
```

The following example shows a policy in the 111222333444 account that allows users to view, but not change, the pipeline named `MyFirstPipeline` in the CodePipeline console\. This policy is based on the `AWSCodePipelineReadOnlyAccess` managed policy, but because it is specific to the `MyFirstPipeline` pipeline, it cannot use the managed policy directly\. If you do not want to restrict the policy to a specific pipeline, consider using one of the managed policies created and maintained by CodePipeline\. For more information, see [Working with Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html)\. You must attach this policy to an IAM role you create for access, for example, a role named `CrossAccountPipelineViewers`:

```
{
    "Statement": [
        {
            "Action": [
                "codepipeline:GetPipeline",
                "codepipeline:GetPipelineState",
                "codepipeline:GetPipelineExecution",
                "codepipeline:ListPipelineExecutions",
                "codepipeline:ListActionTypes",
                "codepipeline:ListPipelines",
                "iam:ListRoles",
                "s3:GetBucketPolicy",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket",
                "codecommit:ListBranches",
                "codecommit:ListRepositories",
                "codedeploy:GetApplication",
                "codedeploy:GetDeploymentGroup",
                "codedeploy:ListApplications",
                "codedeploy:ListDeploymentGroups",
                "elasticbeanstalk:DescribeApplications",
                "elasticbeanstalk:DescribeEnvironments",
                "lambda:GetFunctionConfiguration",
                "lambda:ListFunctions",
                "opsworks:DescribeApps",
                "opsworks:DescribeLayers",
                "opsworks:DescribeStacks"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:codepipeline:us-west-2:111222333444:MyFirstPipeline"
        }
    ],
    "Version": "2012-10-17"
}
```

After you create this policy, create the IAM role in the 111222333444 account and attach the policy to that role\. In the role's trust relationships, you must add the AWS account that will assume this role\. The following example shows a policy that allows users from the *111111111111* AWS account to assume roles defined in the 111222333444 account:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111111111111:root"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

The following example shows a policy created in the *111111111111* AWS account that allows users to assume the role named *CrossAccountPipelineViewers* in the 111222333444 account:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::111222333444:role/CrossAccountPipelineViewers"
        }
    ]
}
```

You can create IAM policies to restrict the calls and resources that users in your account have access to, and then attach those policies to IAM users\. For more information about how to create IAM roles and to explore example IAM policy statements for CodePipeline, see [Customer Managed Policy Examples](#customer-managed-policies)\. 

## Permissions Required to Use the CodePipeline Console<a name="security-iam-permissions-console"></a>

To use CodePipeline in the CodePipeline console, you must have must have a minimum set of permissions from the following services:
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

If you create an IAM policy that is more restrictive than the minimum required permissions, the console won't function as intended for users with that IAM policy\. To ensure that those users can still use the CodePipeline console, also attach the `AWSCodePipelineReadOnlyAccess` managed policy to the user, as described in [AWS Managed \(Predefined\) Policies for CodePipeline](#managed-policies)\.

You don't need to allow minimum console permissions for users who are making calls to the AWS CLI or the CodePipeline API\.

## AWS Managed \(Predefined\) Policies for CodePipeline<a name="managed-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. Managed policies grant necessary permissions for common use cases so you can avoid having to investigate which permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies, which you can attach to users in your account, are specific to CodePipeline:
+ `AWSCodePipelineFullAccess` – Grants full access to CodePipeline\.
+ `AWSCodePipelineCustomActionAccess` – Grants permission to an IAM user to create custom actions in CodePipeline or integrate Jenkins resources for build or test actions\.
+ `AWSCodePipelineReadOnlyAccess` – Grants read\-only access to CodePipeline\.
+ `AWSCodePipelineApproverAccess` – Grants permission to an IAM user to approve or reject a manual approval action\.

### CodePipeline Managed Policies for Notifications<a name="notifications-permissions"></a>

CodePipeline supports notifications to make users aware of important changes to pipelines\.  Managed policies for CodePipeline include policy statements for notification functionality\. For more information, see [What are notifications?](https://docs.aws.amazon.com/codestar-notifications/latest/userguide/welcome.html)

#### Permissions for Notifications in Full Access Managed Policies<a name="notifications-fullaccess"></a>

The `AWSCodePipelineFullAccess` managed policy includes the following statements to allow full access to notifications\. Users with this managed policy applied can also create and manage Amazon SNS topics for notifications, subscribe and unsubscribe users to topics, and list topics to choose as targets for notification rules\.

```
    {
        "Sid": "CodeStarNotificationsReadWriteAccess",
        "Effect": "Allow",
        "Action": [
            "codestar-notifications:CreateNotificationRule",
            "codestar-notifications:DescribeNotificationRule",
            "codestar-notifications:UpdateNotificationRule",
            "codestar-notifications:DeleteNotificationRule",
            "codestar-notifications:Subscribe",
            "codestar-notifications:Unsubscribe"
        ],
        "Resource": "*",
        "Condition" : {
            "StringLike" : {"codestar-notifications:NotificationsForResource" : "arn:aws:codepipeline:*"} 
        }
    },    
    {
        "Sid": "CodeStarNotificationsListAccess",
        "Effect": "Allow",
        "Action": [
            "codestar-notifications:ListNotificationRules",
            "codestar-notifications:ListTargets",
            "codestar-notifications:ListTagsforResource"
        ],
        "Resource": "*"
    },
    {
        "Sid": "CodeStarNotificationsSNSTopicCreateAccess",
        "Effect": "Allow",
        "Action": [
            "sns:CreateTopic",
            "sns:SetTopicAttributes"
        ],
        "Resource": "arn:aws:sns:*:*:codestar-notifications*"
    },
    {
        "Sid": "SNSTopicListAccess",
        "Effect": "Allow",
        "Action": [
            "sns:ListTopics"
        ],
        "Resource": "*"
    }
```

#### Permissions for Notifications in Read\-Only Managed Policies<a name="notifications-readonly"></a>

The `AWSCodePipelineReadOnlyAccess` managed policy includes the following statements to allow read\-only access to notifications\. Users with this managed policy applied can view notifications for resources, but cannot create, manage, or subscribe to them\. 

```
   {
        "Sid": "CodeStarNotificationsPowerUserAccess",
        "Effect": "Allow",
        "Action": [
            "codestar-notifications:DescribeNotificationRule"
        ],
        "Resource": "*",
        "Condition" : {
            "StringLike" : {"codestar-notifications:NotificationsForResource" : "arn:aws:codepipeline:*"} 
        }
    },    
    {
        "Sid": "CodeStarNotificationsListAccess",
        "Effect": "Allow",
        "Action": [
            "codestar-notifications:ListNotificationRules"
        ],
        "Resource": "*"
    }
```

For more information, see [Identity and Access Management for AWS CodeStar Notifications](https://docs.aws.amazon.com/codestar-notifications/latest/userguide/security-iam.html)\.

### IAM Permissions Reference for Connections<a name="connections-permissions"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

CodePipeline integrates with AWS CodeStar connections, a feature that allows you to connect your pipelines to third\-party code repositories\. To create connections for your AWS resources, you must have the required IAM permissions\. For more information about connections, see [Working with Connections in CodePipeline](connections.md)\.

#### Permissions for Managing Connections<a name="connections-permissions-actions"></a>

The following IAM operations are supported in the AWS CLI and SDKs to create, update, and delete connections\.

```
codestar-connections:CreateConnection
codestar-connections:DeleteConnection
codestar-connections:GetConnections
codestar-connections:ListConnections
```

#### Permissions for the Connection Handshake<a name="connections-permissions-actions-handshake"></a>

The following IAM operations are used by the console when performing a browser\-based handshake\.

```
codestar-connections:ListInstallationTargets
codestar-connections:GetInstallationUrl
codestar-connections:StartOAuthHandshake
codestar-connections:UpdateConnectionInstallation
codestar-connections:GetIndividualAccessToken
```

#### Passing a Connection to a Service<a name="connections-passconnection"></a>

When a connection is passed to a service \(for example, when a connection ARN is provided in a pipeline definition to create or update a pipeline\) the user must have the `codestar-connections:PassConnection` permission\.

This operation also supports the following condition key:
+ `codestar-connections:PassedToService`


**Supported values for condition keys**  

| Key | Valid Action Providers | 
| --- | --- | 
|  `codestar-connections:PassedToService`  |  `codepipeline.amazonaws.com`  | 

#### Using a Connection<a name="connections-use"></a>

When a service like CodePipeline uses a connection, the service role must have the `codestar-connections:UseConnection` permission for a given connection\.

This operation also supports the following condition keys:
+ `codestar-connections:FullRepositoryId`
+ `codestar-connections:ProviderActionType`
+ `codestar-connections:ProviderAction`


**Supported values for condition keys**  

| Key | Valid Action Providers | 
| --- | --- | 
|  `codestar-connections:FullRepositoryId`  |  The user name and repository name of a Bitbucket repository, such as `my-owner/my-repository`\. Supported only when the connection is being used to access a specific repository\.  | 
|  `codestar-connections:ProviderActionType`  |  read\_only or read\_write  | 
|  `codestar-connections:ProviderAction`  |  `GetBranch`, `ListRepositories`, `ListOwners`, `ListBranches`, `StartUploadArchiveToS3`, `GitPush`, `GitPull`, `GetUploadArchiveToS3Status` For information, see [Supported Values for ProviderAction](#connections-use-provider)\.  | 

The required condition keys for some functionality might change over time\. We recommend that you use `codestar-connections:UseConnection` to control access to a connection unless your access control requirements require different permissions\.

##### Supported Values for ProviderAction<a name="connections-use-provider"></a>

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

## Customer Managed Policy Examples<a name="customer-managed-policies"></a>

In this section, you can find example user policies that grant permissions for various CodePipeline actions\. These policies work when you are using the CodePipeline API, AWS SDKs, or the AWS CLI\. When you are using the console, you must grant additional permissions specific to the console\. For more information, see [Permissions Required to Use the CodePipeline Console](#security-iam-permissions-console)\.

**Note**  
All examples use the US West \(Oregon\) Region \(`us-west-2`\) and contain fictitious account IDs\.

**Examples**
+ [Example 1: Grant Permissions to Get the State of a Pipeline](#identity-based-policies-example-1)
+ [Example 2: Grant Permissions to Enable and Disable Transitions Between Stages](#identity-based-policies-example-2)
+ [Example 3: Grant Permissions to Get a List of All Available Action Types](#identity-based-policies-example-3)
+ [Example 4: Grant Permissions to Approve or Reject Manual Approval Actions](#identity-based-policies-example-4)
+ [Example 5: Grant Permissions to Poll for Jobs for a Custom Action](#identity-based-policies-example-5)
+ [Example 6: Attach or Edit a Policy for Jenkins Integration with AWS CodePipeline](#identity-based-policies-example-6)
+ [Example 7: Configure Cross\-Account Access to a Pipeline](#identity-based-policies-example-7)
+ [Example 8: Use AWS Resources Associated with Another Account in a Pipeline](#identity-based-policies-example-8)

### Example 1: Grant Permissions to Get the State of a Pipeline<a name="identity-based-policies-example-1"></a>

The following example grants permissions to get the state of the pipeline named `MyFirstPipeline`:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codepipeline:GetPipelineState"
            ],
            "Resource": "arn:aws:codepipeline:us-west-2:111222333444:MyFirstPipeline"
        }
    ]
}
```

### Example 2: Grant Permissions to Enable and Disable Transitions Between Stages<a name="identity-based-policies-example-2"></a>

The following example grants permissions to disable and enable transitions between all stages in the pipeline named `MyFirstPipeline`:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codepipeline:DisableStageTransition",
                "codepipeline:EnableStageTransition"
            ],
            "Resource": "arn:aws:codepipeline:us-west-2:111222333444:MyFirstPipeline/*"
        }
    ]
}
```

To allow the user to disable and enable transitions for a single stage in a pipeline, you must specify the stage\. For example, to allow the user to enable and disable transitions for a stage named `Staging` in a pipeline named `MyFirstPipeline`:

```
"Resource": "arn:aws:codepipeline:us-west-2:111222333444:MyFirstPipeline/Staging"
```

### Example 3: Grant Permissions to Get a List of All Available Action Types<a name="identity-based-policies-example-3"></a>

The following example grants permissions to get a list of all available action types available for pipelines in the `us-west-2` Region:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codepipeline:ListActionTypes"
            ],
            "Resource": "arn:aws:codepipeline:us-west-2:111222333444:actiontype:*"
        }
    ]
}
```

### Example 4: Grant Permissions to Approve or Reject Manual Approval Actions<a name="identity-based-policies-example-4"></a>

The following example grants permissions to approve or reject manual approval actions in a stage named `Staging` in a pipeline named `MyFirstPipeline`: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codepipeline:PutApprovalResult"
            ],
            "Resource": "arn:aws:codepipeline:us-west-2:111222333444:MyFirstPipeline/Staging/*"
        }
    ]
}
```

### Example 5: Grant Permissions to Poll for Jobs for a Custom Action<a name="identity-based-policies-example-5"></a>

The following example grants permissions to poll for jobs for the custom action named `TestProvider`, which is a `Test` action type in its first version, across all pipelines: 

**Note**  
The job worker for a custom action might be configured under a different AWS account or require a specific IAM role in order to function\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codepipeline:PollForJobs"
            ],
            "Resource": [
                "arn:aws:codepipeline:us-west-2:111222333444:actionType:Custom/Test/TestProvider/1"
            ]
        }
    ]
}
```

### Example 6: Attach or Edit a Policy for Jenkins Integration with AWS CodePipeline<a name="identity-based-policies-example-6"></a>

If you configure a pipeline to use Jenkins for build or test, create a separate IAM user for that integration and attach an IAM policy that has the minimum permissions required for integration between Jenkins and CodePipeline\. This policy is the same as the `AWSCodePipelineCustomActionAccess` managed policy\. The following example shows a policy to attach to an IAM user for Jenkins integration:

```
{
    "Statement": [
        {
            "Action": [
                "codepipeline:AcknowledgeJob",
                "codepipeline:GetJobDetails",
                "codepipeline:PollForJobs",
                "codepipeline:PutJobFailureResult",
                "codepipeline:PutJobSuccessResult"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ],
    "Version": "2012-10-17"
}
```

### Example 7: Configure Cross\-Account Access to a Pipeline<a name="identity-based-policies-example-7"></a>

You can configure access to pipelines for users and groups in another AWS account\. The recommended way is to create a role in the account where the pipeline was created\. The role should allow users from the other AWS account to assume that role and access the pipeline\. For more information, see [Walkthrough: Cross\-Account Access Using Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/walkthru_cross-account-with-roles.html)\.

The following example shows a policy in the 80398EXAMPLE account that allows users to view, but not change, the pipeline named `MyFirstPipeline` in the CodePipeline console\. This policy is based on the `AWSCodePipelineReadOnlyAccess` managed policy, but because it is specific to the `MyFirstPipeline` pipeline, it cannot use the managed policy directly\. If you do not want to restrict the policy to a specific pipeline, consider using one of the managed policies created and maintained by CodePipeline\. For more information, see [Working with Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html)\. You must attach this policy to an IAM role you create for access, for example, a role named `CrossAccountPipelineViewers`:

```
{
   "Statement": [
        {
            "Action": [
                "codepipeline:GetPipeline",
                "codepipeline:GetPipelineState",
                "codepipeline:ListActionTypes",
                "codepipeline:ListPipelines",
                "iam:ListRoles",
                "s3:GetBucketPolicy",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket",
                "codedeploy:GetApplication",
                "codedeploy:GetDeploymentGroup",
                "codedeploy:ListApplications",
                "codedeploy:ListDeploymentGroups",
                "elasticbeanstalk:DescribeApplications",
                "elasticbeanstalk:DescribeEnvironments",
                "lambda:GetFunctionConfiguration",
                "lambda:ListFunctions"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:codepipeline:us-east-2:80398EXAMPLE:MyFirstPipeline"
        }
    ],
    "Version": "2012-10-17"
}
```

After you create this policy, create the IAM role in the 80398EXAMPLE account and attach the policy to that role\. In the role's trust relationships, you must add the AWS account that assumes this role\. The following example shows a policy that allows users from the *111111111111* AWS account to assume roles defined in the 80398EXAMPLE account:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111111111111:root"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

The following example shows a policy created in the *111111111111* AWS account that allows users to assume the role named `CrossAccountPipelineViewers` in the 80398EXAMPLE account:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::80398EXAMPLE:role/CrossAccountPipelineViewers"
        }
    ]
}
```

### Example 8: Use AWS Resources Associated with Another Account in a Pipeline<a name="identity-based-policies-example-8"></a>

You can configure policies that allow a user to create a pipeline that uses resources in another AWS account\. This requires configuring policies and roles in both the account that creates the pipeline \(AccountA\) and the account that created the resources to be used in the pipeline \(AccountB\)\. You must also create a customer\-managed key in AWS Key Management Service to use for cross\-account access\. For more information and step\-by\-step examples, see [Create a Pipeline in CodePipeline That Uses Resources from Another AWS Account](pipelines-create-cross-account.md) and [Data Protection Configuration](data-protection.md#security-configuration)\.

The following example shows a policy configured by AccountA for an S3 bucket used to store pipeline artifacts\. The policy grants access to AccountB\. In the following example, the ARN for AccountB is `012ID_ACCOUNT_B`\. The ARN for the S3 bucket is `codepipeline-us-east-2-1234567890`\. Replace these ARNs with the ARNs for the S3 bucket and the account you want to allow access:

```
{
    "Version": "2012-10-17",
    "Id": "SSEAndSSLPolicy",
    "Statement": [
        {
            "Sid": "DenyUnEncryptedObjectUploads",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*",
            "Condition": {
                "StringNotEquals": {
                    "s3:x-amz-server-side-encryption": "aws:kms"
                }
            }
        },
        {
            "Sid": "DenyInsecureConnections",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:*",
            "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*",
            "Condition": {
                "Bool": {
                    "aws:SecureTransport": false
                }
            }
        },
        {
            "Sid": "",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::012ID_ACCOUNT_B:root"
            },
            "Action": [
                "s3:Get*",
                "s3:Put*"
            ],
            "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*"
        },
        {
            "Sid": "",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::012ID_ACCOUNT_B:root"
            },
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890"
        }
    ]
}
```

 The following example shows a policy configured by AccountA that allows AccountB to assume a role\. This policy must be applied to the service role for CodePipeline \(`AWS-CodePipeline-Service`\)\. For more information about how to apply policies to roles in IAM, see [Modifying a Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/roles-managingrole-editing.html)\. In the following example, `012ID_ACCOUNT_B` is the ARN for AccountB:

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "sts:AssumeRole",
        "Resource": [
            "arn:aws:iam::012ID_ACCOUNT_B:role/*"
        ]
    }
}
```

The following example shows a policy configured by AccountB and applied to the [EC2 instance role](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-iam-instance-profile.html) for CodeDeploy\. This policy grants access to the S3 bucket used by AccountA to store pipeline artifacts \(*codepipeline\-us\-east\-2\-1234567890*\):

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:Get*"
            ],
            "Resource": [
                "arn:aws:s3:::codepipeline-us-east-2-1234567890/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::codepipeline-us-east-2-1234567890"
            ]
        }
    ]
}
```

The following example shows a policy for AWS KMS where ***arn:aws:kms:us\-east\-1:012ID\_ACCOUNT\_A:key/2222222\-3333333\-4444\-556677EXAMPLE*** is the ARN of the customer\-managed key created in AccountA and configured to allow AccountB to use it:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kms:DescribeKey",
                "kms:GenerateDataKey*",
                "kms:Encrypt",
                "kms:ReEncrypt*",
                "kms:Decrypt"
            ],
            "Resource": [
                "arn:aws:kms:us-east-1:012ID_ACCOUNT_A:key/2222222-3333333-4444-556677EXAMPLE"
            ]
        }
    ]
}
```

The following example shows an inline policy for an IAM role \(`CrossAccount_Role`\) created by AccountB that allows access to CodeDeploy actions required by the pipeline in AccountA\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codedeploy:CreateDeployment",
                "codedeploy:GetDeployment",
                "codedeploy:GetDeploymentConfig",
                "codedeploy:GetApplicationRevision",
                "codedeploy:RegisterApplicationRevision"
            ],
            "Resource": "*"
        }
    ]
}
```

The following example shows an inline policy for an IAM role \(`CrossAccount_Role`\) created by AccountB that allows access to the S3 bucket to download input artifacts and upload output artifacts:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject*",
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": [
                "arn:aws:s3:::codepipeline-us-east-2-1234567890/*"
            ]
        }
    ]
}
```

For more information about how to edit a pipeline for cross\-account access to resources, see [Step 2: Edit the Pipeline ](pipelines-create-cross-account.md#pipelines-create-cross-account-create)\.