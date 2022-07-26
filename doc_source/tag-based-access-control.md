# Using tags to control access to CodePipeline resources<a name="tag-based-access-control"></a>

Conditions in IAM user policy statements are part of the syntax that you use to specify permissions to resources required by CodePipeline actions\. Using tags in conditions is one way to control access to resources and requests\. For information about tagging CodePipeline resources, see [Tagging resources](tag-resources.md)\. This topic discusses tag\-based access control\.

When you design IAM policies, you might be setting granular permissions by granting access to specific resources\. As the number of resources that you manage grows, this task becomes more difficult\. Tagging resources and using tags in policy statement conditions can make this task easier\. You grant access in bulk to any resource with a certain tag\. Then you repeatedly apply this tag to relevant resources, during creation or later\.

Tags can be attached to the resource or passed in the request to services that support tagging\. In CodePipeline, resources can have tags, and some actions can include tags\. When you create an IAM policy, you can use tag condition keys to control:
+ Which users can perform actions on a pipeline resource, based on tags that it already has\.
+ Which tags can be passed in an action's request\.
+ Whether specific tag keys can be used in a request\.

String condition operators let you construct `Condition` elements that restrict access based on comparing a key to a string value\. You can add `IfExists` to the end of any condition operator name except the Null condition\. You do this to say "If the policy key is present in the context of the request, process the key as specified in the policy\. If the key is not present, evaluate the condition element as true\." For example, you can use `StringEqualsIfExists` to restrict by condition keys that might not be present on other types of resources\. 

For the complete syntax and semantics of tag condition keys, see [Controlling Access Using Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) in the *IAM User Guide*\. For additional information about condition keys, see the following resources\. The CodePipeline policy examples in this section align with the following information about condition keys and expand on it with examples of nuances for CodePipeline such as nesting of resources\.
+ [String condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_String)
+ [AWS services that work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html)
+ [SCP syntax](https://docs.aws.amazon.com/IAM/latest/UserGuide/orgs_manage_policies_scps_syntax.html)
+ [IAM JSON policy elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html)
+ [aws:RequestTag/tag\-key](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-requesttag)
+ [Condition keys for CodePipeline](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awscodepipeline.html#awscodepipeline-policy-keys)

The following examples demonstrate how to specify tag conditions in policies for CodePipeline users\.

**Example 1: Limit actions based on tags in the request**  
The `AWSCodePipeline_FullAccess` managed user policy gives users unlimited permission to perform any CodePipeline action on any resource\.  
The following policy limits this power and denies unauthorized users permission to create pipelines where specific tags are listed in the request\. To do that, it denies the `CreatePipeline` action if the request specifies a tag named `Project` with one of the values `ProjectA` or `ProjectB`\. \(The `aws:RequestTag` condition key is used to control which tags can be passed in an IAM request\.\)   
In the following example, the intent of the policy is to deny unauthorized users permission to create a pipeline with the tag values specified\. However, creating a pipeline requires accessing resources in addition to the pipeline itself \(for example, pipeline actions and stages\)\. Because the `'Resource'` specified in the policy is `'*'`, the policy is evaluated against every resource that has an ARN and is created when the pipeline is being created\. These additional resources do not have the tag condition key, so the `StringEquals` check fails, and the user is not granted the ability to create any pipeline\. To address this, use the `StringEqualsIfExists` condition operator instead\. This way, the test only happens if the condition key exists\.   
You could read the following as: "If the resource being checked has a tag `"RequestTag/Project"` condition key, then allow the action only if the key value begins with `projectA`\. If the resource being checked does not have that condition key, then don't worry about it\."   
In addition, the policy prevents these unauthorized users from tampering with the resources by using the `aws:TagKeys` condition key to not allow tag modification actions to include these same tag values\. A customer's administrator must attach this IAM policy to unauthorized IAM users, in addition to the managed user policy\.  

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
        "StringEqualsIfExists": {
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

**Example 2: Limit tagging actions based on resource tags**  
The `AWSCodePipeline_FullAccess` managed user policy gives users unlimited permission to perform any CodePipeline action on any resource\.  
The following policy limits this power and denies unauthorized users permission to perform actions on specified project pipelines\. To do that, it denies some actions if the resource has a tag named `Project` with one of the values `ProjectA` or `ProjectB`\. \(The `aws:ResourceTag` condition key is used to control access to the resources based on the tags on those resources\.\) A customer's administrator must attach this IAM policy to unauthorized IAM users, in addition to the managed user policy\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "codepipeline:TagResource"
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
To do that, it allows the `CreatePipeline` and `TagResource` actions if the request specifies a tag named `Project` with the value `ProjectA`\. In other words, the only tag key which can be specified is `Project`, and its value must be `ProjectA`\.  
The `aws:RequestTag` condition key is used to control which tags can be passed in an IAM request\. The `aws:TagKeys` condition ensures tag key case sensitivity\. This policy is useful for IAM users who don't have the `AWSCodePipeline_FullAccess` managed user policy attached\. The managed policy gives users unlimited permission to perform any CodePipeline action on any resource\.  

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

**Example 4: Limit untagging actions based on resource tags**  
The `AWSCodePipeline_FullAccess` managed user policy gives users unlimited permission to perform any CodePipeline action on any resource\.  
The following policy limits this power and denies unauthorized users permission to perform actions on specified project pipelines\. To do that, it denies some actions if the resource has a tag named `Project` with one of the values `ProjectA` or `ProjectB`\.   
Also, the policy prevents these unauthorized users from tampering with the resources by using the `aws:TagKeys` condition key to not allow tag modification actions to completely remove the `Project` tag\. A customer's administrator must attach this IAM policy to unauthorized IAM users, in addition to the managed user policy\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
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