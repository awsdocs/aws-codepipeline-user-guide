# Customer managed policy examples<a name="customer-managed-policies"></a>

In this section, you can find example user policies that grant permissions for various CodePipeline actions\. These policies work when you are using the CodePipeline API, AWS SDKs, or the AWS CLI\. When you are using the console, you must grant additional permissions specific to the console\. For more information, see [Permissions required to use the CodePipeline console](security-iam-permissions-console.md)\.

**Note**  
All examples use the US West \(Oregon\) Region \(`us-west-2`\) and contain fictitious account IDs\.

**Examples**
+ [Example 1: Grant permissions to get the state of a pipeline](#identity-based-policies-example-1)
+ [Example 2: Grant permissions to enable and disable transitions between stages](#identity-based-policies-example-2)
+ [Example 3: Grant permissions to get a list of all available action types](#identity-based-policies-example-3)
+ [Example 4: Grant permissions to approve or reject manual approval actions](#identity-based-policies-example-4)
+ [Example 5: Grant permissions to poll for jobs for a custom action](#identity-based-policies-example-5)
+ [Example 6: Attach or edit a policy for Jenkins integration with AWS CodePipeline](#identity-based-policies-example-6)
+ [Example 7: Configure cross\-account access to a pipeline](#identity-based-policies-example-7)
+ [Example 8: Use AWS resources associated with another account in a pipeline](#identity-based-policies-example-8)

## Example 1: Grant permissions to get the state of a pipeline<a name="identity-based-policies-example-1"></a>

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

## Example 2: Grant permissions to enable and disable transitions between stages<a name="identity-based-policies-example-2"></a>

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

## Example 3: Grant permissions to get a list of all available action types<a name="identity-based-policies-example-3"></a>

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

## Example 4: Grant permissions to approve or reject manual approval actions<a name="identity-based-policies-example-4"></a>

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

## Example 5: Grant permissions to poll for jobs for a custom action<a name="identity-based-policies-example-5"></a>

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

## Example 6: Attach or edit a policy for Jenkins integration with AWS CodePipeline<a name="identity-based-policies-example-6"></a>

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

## Example 7: Configure cross\-account access to a pipeline<a name="identity-based-policies-example-7"></a>

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

## Example 8: Use AWS resources associated with another account in a pipeline<a name="identity-based-policies-example-8"></a>

You can configure policies that allow a user to create a pipeline that uses resources in another AWS account\. This requires configuring policies and roles in both the account that creates the pipeline \(AccountA\) and the account that created the resources to be used in the pipeline \(AccountB\)\. You must also create a customer managed key in AWS Key Management Service to use for cross\-account access\. For more information and step\-by\-step examples, see [Create a pipeline in CodePipeline that uses resources from another AWS account](pipelines-create-cross-account.md) and [Data protection configuration](data-protection.md#security-configuration)\.

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

The following example shows a policy for AWS KMS where ***arn:aws:kms:us\-east\-1:012ID\_ACCOUNT\_A:key/2222222\-3333333\-4444\-556677EXAMPLE*** is the ARN of the customer managed key created in AccountA and configured to allow AccountB to use it:

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

For more information about how to edit a pipeline for cross\-account access to resources, see [Step 2: Edit the pipeline ](pipelines-create-cross-account.md#pipelines-create-cross-account-create)\.