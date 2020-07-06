# Using tags to control access to CodePipeline resources<a name="tag-based-access-control"></a>

Conditions in IAM user policy statements are part of the syntax that you use to specify permissions to resources required by CodePipeline actions\. Using tags in conditions is one way to control access to resources and requests\. For information about tagging CodePipeline resources, see [Tagging resources](tag-resources.md)\. This topic discusses tag\-based access control\.

When you design IAM policies, you might be setting granular permissions by granting access to specific resources\. As the number of resources that you manage grows, this task becomes more difficult\. Tagging resources and using tags in policy statement conditions can make this task easier\. You grant access in bulk to any resource with a certain tag\. Then you repeatedly apply this tag to relevant resources, during creation or later\.

Tags can be attached to the resource or passed in the request to services that support tagging\. In CodePipeline, resources can have tags, and some actions can include tags\. When you create an IAM policy, you can use tag condition keys to control:
+ Which users can perform actions on a pipeline resource, based on tags that it already has\.
+ Which tags can be passed in an action's request\.
+ Whether specific tag keys can be used in a request\.

For the complete syntax and semantics of tag condition keys, see [Controlling Access Using Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) in the *IAM User Guide*\.

The following examples demonstrate how to specify tag conditions in policies for CodePipeline users\.

**Example 1: Limit actions based on tags in the request**  
The `CodePipelineFullAccess` managed user policy gives users unlimited permission to perform any CodePipeline action on any resource\.  
The following policy limits this power and denies unauthorized users permission to create pipelines for specific projects\. To do that, it denies the `CreatePipeline` action if the request specifies a tag named `Project` with one of the values `ProjectA` or `ProjectB`\. \(The `aws:RequestTag` condition key is used to control which tags can be passed in an IAM request\.\) In addition, the policy prevents these unauthorized users from tampering with the resources by using the `aws:TagKeys` condition key to not allow tag modification actions to include these same tag values or to completely remove the `Project` tag\. A customer's administrator must attach this IAM policy to unauthorized IAM users, in addition to the managed user policy\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "codepipeline:CreatePipeline",
        "codepipeline:TagResource"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:RequestTag/Project": ["ProjectA", "ProjectB"]
        }
      }
    },
    {
      "Effect": "Deny",
      "Action": [
        "codepipeline:UntagResource"
      ],
      "Resource": "*",
      "Condition": {
        "ForAllValues:StringEquals": {
          "aws:TagKeys": ["Project"]
        }
      }
    }
  ]
}
```

**Example 2: Limit actions based on resource tags**  
The `CodePipelineFullAccess` managed user policy gives users unlimited permission to perform any CodePipeline action on any resource\.  
The following policy limits this power and denies unauthorized users permission to perform actions on specified project pipelines\. To do that, it denies some actions if the resource has a tag named `Project` with one of the values `ProjectA` or `ProjectB`\. \(The `aws:ResourceTag` condition key is used to control access to the resources based on the tags on those resources\.\) A customer's administrator must attach this IAM policy to unauthorized IAM users, in addition to the managed user policy\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "codepipeline:TagResource",
        "codepipeline:UntagResource",
        "codepipeline:UpdatePipeline",
        "codepipeline:DeletePipeline",
        "codepipeline:ListTagsForResource"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/Project": ["ProjectA", "ProjectB"]
        }
      }
    }
  ]
}
```

**Example 3: Allow actions based on tags in the request**  
The following policy grants users permission to create development pipelines in CodePipeline\.  
To do that, it allows the `CreatePipeline` and `TagResource` actions if the request specifies a tag named `Project` with the value `ProjectA`\. \(The `aws:RequestTag` condition key is used to control which tags can be passed in an IAM request\.\) The `aws:TagKeys` condition ensures tag key case sensitivity\. This policy is useful for IAM users who don't have the `CodePipelineFullAccess` managed user policy attached\. The managed policy gives users unlimited permission to perform any CodePipeline action on any resource\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "codepipeline:CreatePipeline",
        "codepipeline:TagResource"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:RequestTag/Project": "ProjectA"
        },
        "ForAllValues:StringEquals": {
          "aws:TagKeys": ["Project"]
        }
      }
    }
  ]
}
```

**Example 4: Allow actions based on resource tags**  
The following policy grants users permission to perform actions on, and get information about, project pipelines in CodePipeline\.  
To do that, it allows specific actions if the pipeline has a tag named `Project` with the value `ProjectA`\. \(The `aws:RequestTag` condition key is used to control which tags can be passed in an IAM request\.\) The `aws:TagKeys` condition ensures tag key case sensitivity\. This policy is useful for IAM users who don't have the `CodePipelineFullAccess` managed user policy attached\. The managed policy gives users unlimited permission to perform any CodePipeline action on any resource\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "codepipeline:UpdatePipeline",
        "codepipeline:DeletePipeline",
        "codepipeline:ListPipelines"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/Project": "ProjectA"
        },
        "ForAllValues:StringEquals": {
          "aws:TagKeys": ["Project"]
        }
      }
    }
  ]
}
```