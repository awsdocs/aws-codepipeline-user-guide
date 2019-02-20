# Overview of Managing Access Permissions to Your CodePipeline Resources<a name="iam-access-control-identity-based"></a>

Every AWS resource is owned by an AWS account, and permissions to create or access a resource are governed by permissions policies\. An account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\), and some services \(such as AWS Lambda\) also support attaching permissions policies to resources\. 

**Note**  
An *account administrator* \(or administrator IAM user\) is a user with administrator privileges\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

When granting permissions, you decide who is getting the permissions, the resources they get permissions for, and the specific actions that you want to allow on those resources\.

**Topics**
+ [CodePipeline Resources and Operations](#ACP_ARN_Format)
+ [Supported Resource\-Level Permissions for CodePipeline API Calls](#iam-actions-resource-level)
+ [Understanding Resource Ownership](#understanding-resource-ownership)
+ [Managing Access to Resources](#managing-access-resources)
+ [Specifying Policy Elements: Actions, Effects, and Principals](#actions-effects-principals)
+ [Specifying Conditions in a Policy](#policy-conditions)

## CodePipeline Resources and Operations<a name="ACP_ARN_Format"></a>

In CodePipeline, the primary resource is a pipeline\. In a policy, you use an Amazon Resource Name \(ARN\) to identify the resource that the policy applies to\. CodePipeline supports other resources that can be used with the primary resource, such as stages, actions, and custom actions\. These are referred to as subresources\. These resources and subresources have unique Amazon Resource Names \(ARNs\) associated with them\. For more information about ARNs, see [Amazon Resource Names \(ARN\) and AWS Service Namespaces](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *Amazon Web Services General Reference*\. To get the pipeline ARN associated with your pipeline, use the CLI to run the get\-pipeline command\. For more information, see [GetPipeline](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_GetPipeline.html) in the [CodePipeline API Reference](https://docs.aws.amazon.com/codepipeline/latest/APIReference/)\.


| Resource Type | ARN Format | 
| --- | --- | 
|  Pipeline  |  arn:aws:codepipeline:*region*:*account*:*pipeline\-name*  | 
| Stage |  arn:aws:codepipeline:*region*:*account*:*pipeline\-name*/*stage\-name*  | 
| Action |  arn:aws:codepipeline:*region*:*account*:*pipeline\-name*/*stage\-name*/*action\-name*  | 
| Custom action | arn:aws:codepipeline:region:account:actionType:owner/category/provider/version | 
|  All CodePipeline resources  |  arn:aws:codepipeline:\*  | 
|  All CodePipeline resources owned by the specified account in the specified region  |  arn:aws:codepipeline:*region*:*account*:\*  | 

**Note**  
Most services in AWS treat a colon \(:\) or a forward slash \(/\) as the same character in ARNs\. However, CodePipeline uses an exact match in event patterns and rules\. Be sure to use the correct ARN characters when creating event patterns so that they match the ARN syntax in the pipeline you want to match\.

In CodePipeline, there are API calls that support resource\-level permissions\. Resource\-level permissions indicate whether an API call can specify a resource ARN, or whether the API call can only specify all resources using the wildcard\. See [Supported Resource\-Level Permissions for CodePipeline API Calls](#iam-actions-resource-level) for a detailed description of resource\-level permissions and a listing of the CodePipeline API calls that support resource\-level permissions\.

For example, you can indicate a specific pipeline \(*myPipeline*\) in your statement using its ARN as follows:

```
"Resource": "arn:aws:codepipeline:us-east-2:111222333444:myPipeline"
```

You can also specify all pipelines that belong to a specific account by using the \(\*\) wildcard character as follows:

```
"Resource": "arn:aws:codepipeline:us-east-2:111222333444:*"
```

To specify all resources, or if a specific API action does not support ARNs, use the \(\*\) wildcard character in the Resource element as follows:

```
"Resource": "*"
```

**Note**  
When you create IAM policies, follow the standard security advice of granting least privilege—that is, granting only the permissions required to perform a task\. If an API call supports ARNs, then it supports resource\-level permissions, and you do not need to use the \(\*\) wildcard character\.

Some CodePipeline API calls accept multiple resources \(for example, GetPipeline\)\. To specify multiple resources in a single statement, separate their ARNs with commas, as follows:

```
"Resource": ["arn1", "arn2"]
```

CodePipeline provides a set of operations to work with the CodePipeline resources\. For a list of available operations, see [CodePipeline Permissions Reference](permissions-reference.md)\.

## Supported Resource\-Level Permissions for CodePipeline API Calls<a name="iam-actions-resource-level"></a>

*Resource\-level permissions* are those that allow you to specify which resources users are allowed to perform actions on\. AWS CodePipeline provides partial support for resource\-level permissions\. This means that for some AWS CodePipeline API calls, you can control when users are allowed to use those actions based on conditions that must be met, or which resources users are allowed to use\. For example, you can grant users permission to list pipeline execution information, but only for a specific pipeline or pipelines\.

The following table describes the AWS CodePipeline API calls that currently support resource\-level permissions, as well as the supported resources, resource ARNs, and condition keys for each action\. 

**Note**  
AWS CodePipeline API calls that are not listed in this table do not support resource\-level permissions\. If an AWS CodePipeline API call does not support resource\-level permissions, you can grant users permission to use it, but you have to specify a \* for the resource element of your policy statement\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/iam-access-control-identity-based.html)

## Understanding Resource Ownership<a name="understanding-resource-ownership"></a>

The AWS account owns the resources that are created in the account, regardless of who created the resources\. Specifically, the resource owner is the AWS account of the [principal entity](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html) \(that is, the root account, an IAM user, or an IAM role\) that authenticates the resource creation request\. The following examples illustrate how this works:
+ If you use the root account credentials of your AWS account to create a rule, your AWS account is the owner of the CodePipeline resource\.
+ If you create an IAM user in your AWS account and grant permissions to create CodePipeline resources to that user, the user can create CodePipeline resources\. However, your AWS account, to which the user belongs, owns the CodePipeline resources\.
+ If you create an IAM role in your AWS account with permissions to create CodePipeline resources, anyone who can assume the role can create CodePipeline resources\. Your AWS account, to which the role belongs, owns the CodePipeline resources\.

## Managing Access to Resources<a name="managing-access-resources"></a>

A *permissions policy* describes who has access to what\. The following section explains the available options for creating permissions policies\.

**Note**  
This section discusses using IAM in the context of CodePipeline\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see [What Is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*\. For information about IAM policy syntax and descriptions, see [IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Policies attached to an IAM identity are referred to as identity\-based policies \(IAM policies\) and policies attached to a resource are referred to as resource\-based policies\. CodePipeline supports only identity\-based \(IAM policies\)\.

**Topics**
+ [Identity\-Based Policies \(IAM Policies\)](#identity-based-policies)
+ [Resource\-Based Policies](#resource-based-policies)

### Identity\-Based Policies \(IAM Policies\)<a name="identity-based-policies"></a>

You can attach policies to IAM identities\. For example, you can do the following: 
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permissions to view pipelines in the CodePipeline console, you can attach a permissions policy to a user or group that the user belongs to\.
+ **Attach a permissions policy to a role \(grant cross\-account permissions\)** – You can attach an identity\-based permissions policy to an IAM role to grant cross\-account permissions\. For example, the administrator in Account A can create a role to grant cross\-account permissions to another AWS account \(for example, Account B\) or an AWS service as follows:

  1. Account A administrator creates an IAM role and attaches a permissions policy to the role that grants permissions on resources in Account A\.

  1. Account A administrator attaches a trust policy to the role identifying Account B as the principal who can assume the role\. 

  1. Account B administrator can then delegate permissions to assume the role to any users in Account B\. Doing this allows users in Account B to create or access resources in Account A\. The principal in the trust policy an also be an AWS service principal if you want to grant an AWS service permissions to assume the role\.

  For more information about using IAM to delegate permissions, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

The following example shows a policy in the 111222333444 account that allows users to view, but not change, the pipeline named *MyFirstPipeline* in the CodePipeline console\. This policy is based on the **AWSCodePipelineReadOnlyAccess** managed policy, but because it is specific to the *MyFirstPipeline* pipeline, it cannot use the managed policy directly\. If you do not want to restrict the policy to a specific pipeline, strongly consider using one of the managed policies created and maintained by CodePipeline\. For more information, see [Working with Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html)\. You must attach this policy to an IAM role you create for access, for example a role named *CrossAccountPipelineViewers*:

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
            "Resource": "*"
        }
    ],
    "Version": "2012-10-17"
}
```

Once you create this policy, create the IAM role in the 111222333444 account and attach the policy to that role\. In the role's trust relationships, you must add the AWS account that will assume this role\. The following example shows a policy that allows users from the *111111111111* AWS account to assume roles defined in the 111222333444 account:

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

You can create specific IAM policies to restrict the calls and resources that users in your account have access to, and then attach those policies to IAM users\. For more information about how to create IAM roles and to explore example IAM policy statements for CodePipeline, see [Overview of Managing Access Permissions to Your CodePipeline Resources](#iam-access-control-identity-based)\. 

### Resource\-Based Policies<a name="resource-based-policies"></a>

Other services, such as Amazon S3, also support resource\-based permissions policies\. For example, you can attach a policy to an S3 bucket to manage access permissions to that bucket\. Although CodePipeline doesn't support resource\-based policies, it does store artifacts to be used in pipelines in versioned S3 buckets\. 

**Example To create a policy for an Amazon S3 bucket to use as the artifact store for CodePipeline**  
You can use any versioned Amazon S3 bucket as the artifact store for CodePipeline\. If you use the **Create Pipeline** wizard to create your first pipeline, this Amazon S3 bucket is created for you automatically to ensure that all objects uploaded to the artifact store are encrypted and connections to the bucket are secure\. As a best practice, if you create your own Amazon S3 bucket, consider adding the following policy or its elements to the bucket\. In this policy, the ARN for the Amazon S3 bucket is *codepipeline\-us\-east\-2\-1234567890*\. Replace this ARN with the ARN for your Amazon S3 bucket:  

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
        }
    ]
}
```

## Specifying Policy Elements: Actions, Effects, and Principals<a name="actions-effects-principals"></a>

For each CodePipeline resource, the service defines a set of API operations\. To grant permissions for these API operations, CodePipeline defines a set of actions that you can specify in a policy\. Some API operations can require permissions for more than one action in order to perform the API operation\. For more information about resources and API operations, see [CodePipeline Resources and Operations](#ACP_ARN_Format) and [CodePipeline Permissions Reference](permissions-reference.md)\.

The following are the basic policy elements:
+ **Resource** – You use an Amazon Resource Name \(ARN\) to identify the resource that the policy applies to\. For more information, see [CodePipeline Resources and Operations](#ACP_ARN_Format)\.
+ **Action** – You use action keywords to identify resource operations that you want to allow or deny\. For example, the `codepipeline:GetPipeline` permission allows the user permissions to perform the `GetPipeline` operation\.
+ **Effect** – You specify the effect, either allow or deny, when the user requests the specific action\. If you don't explicitly grant access to \(allow\) a resource, access is implicitly denied\. You can also explicitly deny access to a resource, which you might do to make sure that a user cannot access it, even if a different policy grants access\.
+ **Principal** – In identity\-based policies \(IAM policies\), the user that the policy is attached to is the implicit principal\. For resource\-based policies, you specify the user, account, service, or other entity that you want to receive permissions \(applies to resource\-based policies only\)\.

To learn more about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

For a table showing all of the CodePipeline API actions and the resources that they apply to, see [CodePipeline Permissions Reference](permissions-reference.md)\.

## Specifying Conditions in a Policy<a name="policy-conditions"></a>

When you grant permissions, you can use the access policy language to specify the conditions when a policy should take effect\. For example, you might want a policy to be applied only after a specific date\. For more information about specifying conditions in a policy language, see [Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

To express conditions, you use predefined condition keys\. There are no condition keys specific to CodePipeline\. However, there are AWS\-wide condition keys that you can use as appropriate\. For a complete list of AWS\-wide keys, see [Available Keys for Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. 