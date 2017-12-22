# Using Identity\-Based Policies \(IAM Policies\) for AWS CodePipeline<a name="iam-identity-based-access-control"></a>

This topic provides examples of identity\-based policies that demonstrate how an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\)\.

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available to manage access to your AWS CodePipeline resources\. For more information, see [Overview of Managing Access Permissions to Your AWS CodePipeline Resources](iam-access-control-identity-based.md)

The following sections provide instructions for working with IAM policies specific to AWS CodePipeline\. 

The following shows an example of a permissions policy that allows a user to enable and disable all stage transitions in the pipeline named **MyFirstPipeline** in the us\-west\-2 region:

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