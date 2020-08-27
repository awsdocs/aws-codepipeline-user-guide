# How AWS CodePipeline works with IAM<a name="security_iam_service-with-iam"></a>

Before you use IAM to manage access to CodePipeline, you should understand what IAM features are available to use with CodePipeline\. To get a high\-level view of how CodePipeline and other AWS services work with IAM, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) in the *IAM User Guide*\.

**Topics**
+ [CodePipeline identity\-based policies](#security_iam_service-with-iam-id-based-policies)
+ [CodePipeline resource\-based policies](#security_iam_service-with-iam-resource-based-policies)
+ [Authorization based on CodePipeline tags](#security_iam_service-with-iam-tags)
+ [CodePipeline IAM roles](#security_iam_service-with-iam-roles)

## CodePipeline identity\-based policies<a name="security_iam_service-with-iam-id-based-policies"></a>

With IAM identity\-based policies, you can specify allowed or denied actions and resources as well as the conditions under which actions are allowed or denied\. CodePipeline supports specific actions, resources, and condition keys\. To learn about all of the elements that you use in a JSON policy, see [IAM JSON Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) in the *IAM User Guide*\.

### Actions<a name="security_iam_service-with-iam-id-based-policies-actions"></a>

The `Action` element of an IAM identity\-based policy describes the specific action or actions that will be allowed or denied by the policy\. Policy actions usually have the same name as the associated AWS API operation\. The action is used in a policy to grant permissions to perform the associated operation\. 

Policy actions in CodePipeline use the following prefix before the action: `codepipeline:`\. 

For example, to grant someone permission to view the existing pipelines in the account, you include the `codepipeline:GetPipeline` action in their policy\. Policy statements must include either an `Action` or `NotAction` element\. CodePipeline defines its own set of actions that describe tasks that you can perform with this service\.

To specify multiple actions in a single statement, separate them with commas as follows:

```
"Action": [
      "codepipeline:action1",
      "codepipeline:action2"
```

You can specify multiple actions using wildcards \(\*\)\. For example, to specify all actions that begin with the word `Get`, include the following action:

```
"Action": "codepipeline:Get*"
```



For a list of CodePipeline actions, see [Actions Defined by AWS CodePipeline](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awscodepipeline.html#awscodepipeline-actions-as-permissions) in the *IAM User Guide*\.

### Resources<a name="security_iam_service-with-iam-id-based-policies-resources"></a>

The `Resource` element specifies the object or objects to which the action applies\. Statements must include either a `Resource` or a `NotResource` element\. You specify a resource using an ARN or using the wildcard \(\*\) to indicate that the statement applies to all resources\.



#### CodePipeline resources and operations<a name="ACP_ARN_Format"></a>

In CodePipeline, the primary resource is a pipeline\. In a policy, you use an Amazon Resource Name \(ARN\) to identify the resource that the policy applies to\. CodePipeline supports other resources that can be used with the primary resource, such as stages, actions, and custom actions\. These are referred to as subresources\. These resources and subresources have unique Amazon Resource Names \(ARNs\) associated with them\. For more information about ARNs, see [Amazon Resource Names \(ARN\) and AWS Service Namespaces](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *Amazon Web Services General Reference*\. To get the pipeline ARN associated with your pipeline, you can find the pipeline ARN under **Settings** in the console\. For more information, see [View the pipeline ARN and service role ARN \(console\)](pipelines-view-console.md#pipelines-settings-console)\.


| Resource Type | ARN Format | 
| --- | --- | 
|  Pipeline  |  arn:aws:codepipeline:*region*:*account*:*pipeline\-name*  | 
| Stage |  arn:aws:codepipeline:*region*:*account*:*pipeline\-name*/*stage\-name*  | 
| Action |  arn:aws:codepipeline:*region*:*account*:*pipeline\-name*/*stage\-name*/*action\-name*  | 
| Custom action | arn:aws:codepipeline:region:account:actionType:owner/category/provider/version | 
|  All CodePipeline resources  |  arn:aws:codepipeline:\*  | 
|  All CodePipeline resources owned by the specified account in the specified Region  |  arn:aws:codepipeline:*region*:*account*:\*  | 

**Note**  
Most services in AWS treat a colon \(:\) or a forward slash \(/\) as the same character in ARNs\. However, CodePipeline uses an exact match in event patterns and rules\. Be sure to use the correct ARN characters when creating event patterns so that they match the ARN syntax in the pipeline you want to match\.

In CodePipeline, there are API calls that support resource\-level permissions\. Resource\-level permissions indicate whether an API call can specify a resource ARN, or whether the API call can only specify all resources using the wildcard\. See [CodePipeline permissions reference](permissions-reference.md) for a detailed description of resource\-level permissions and a listing of the CodePipeline API calls that support resource\-level permissions\.

For example, you can indicate a specific pipeline \(*myPipeline*\) in your statement using its ARN as follows:

```
"Resource": "arn:aws:codepipeline:us-east-2:111222333444:myPipeline"
```

You can also specify all pipelines that belong to a specific account by using the \(\*\) wildcard character as follows:

```
"Resource": "arn:aws:codepipeline:us-east-2:111222333444:*"
```

To specify all resources, or if a specific API action does not support ARNs, use the \(\*\) wildcard character in the `Resource` element as follows:

```
"Resource": "*"
```

**Note**  
When you create IAM policies, follow the standard security advice of granting least privilege—that is, granting only the permissions required to perform a task\. If an API call supports ARNs, then it supports resource\-level permissions, and you do not need to use the \(\*\) wildcard character\.

Some CodePipeline API calls accept multiple resources \(for example, `GetPipeline`\)\. To specify multiple resources in a single statement, separate their ARNs with commas, as follows:

```
"Resource": ["arn1", "arn2"]
```

CodePipeline provides a set of operations to work with the CodePipeline resources\. For a list of available operations, see [CodePipeline permissions reference](permissions-reference.md)\.

### Condition keys<a name="security_iam_service-with-iam-id-based-policies-conditionkeys"></a>

The `Condition` element \(or `Condition` *block*\) lets you specify conditions in which a statement is in effect\. The `Condition` element is optional\. You can create conditional expressions that use [condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html), such as equals or less than, to match the condition in the policy with values in the request\. 

If you specify multiple `Condition` elements in a statement, or multiple keys in a single `Condition` element, AWS evaluates them using a logical `AND` operation\. If you specify multiple values for a single condition key, AWS evaluates the condition using a logical `OR` operation\. All of the conditions must be met before the statement's permissions are granted\.

 You can also use placeholder variables when you specify conditions\. For example, you can grant an IAM user permission to access a resource only if it is tagged with their IAM user name\. For more information, see [IAM Policy Elements: Variables and Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html) in the *IAM User Guide*\. 

CodePipeline defines its own set of condition keys and also supports using some global condition keys\. To see all AWS global condition keys, see [AWS Global Condition Context Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.



 All Amazon EC2 actions support the `aws:RequestedRegion` and `ec2:Region` condition keys\. For more information, see [Example: Restricting Access to a Specific Region](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExamplePolicies_EC2.html#iam-example-region)\. 

To see a list of CodePipeline condition keys, see [Condition Keys for AWS CodePipeline](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awscodepipeline.html#awscodepipeline-policy-keys) in the *IAM User Guide*\. To learn with which actions and resources you can use a condition key, see [Actions Defined by AWS CodePipeline](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awscodepipeline.html#awscodepipeline-actions-as-permissions)\.

### Examples<a name="security_iam_service-with-iam-id-based-policies-examples"></a>



To view examples of CodePipeline identity\-based policies, see [AWS CodePipeline identity\-based policy examples](security_iam_id-based-policy-examples.md)\.

## CodePipeline resource\-based policies<a name="security_iam_service-with-iam-resource-based-policies"></a>

CodePipeline does not support resource\-based policies\. However, a resource\-based policy example for the S3 service related to CodePipeline is provided\.

### Examples<a name="security_iam_service-with-iam-resource-based-policies-examples"></a>



To view examples of CodePipeline resource\-based policies, see [AWS CodePipeline resource\-based policy examples](security_iam_resource-based-policy-examples.md),

## Authorization based on CodePipeline tags<a name="security_iam_service-with-iam-tags"></a>

You can attach tags to CodePipeline resources or pass tags in a request to CodePipeline\. To control access based on tags, you provide tag information in the [condition element](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) of a policy using the `codepipeline:ResourceTag/key-name`, `aws:RequestTag/key-name`, or `aws:TagKeys` condition keys\. For more information about tagging CodePipeline resources, see [Tagging resources](tag-resources.md)\.

To view an example identity\-based policy for limiting access to a resource based on the tags on that resource, see [Using tags to control access to CodePipeline resources](tag-based-access-control.md)\.

## CodePipeline IAM roles<a name="security_iam_service-with-iam-roles"></a>

An [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is an entity in your AWS account that has specific permissions\.

### Using temporary credentials with CodePipeline<a name="security_iam_service-with-iam-roles-tempcreds"></a>

You can use temporary credentials to sign in with federation, assume an IAM role, or to assume a cross\-account role\. You obtain temporary security credentials by calling AWS STS API operations such as [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html) or [GetFederationToken](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetFederationToken.html)\. 

CodePipeline supports the use of temporary credentials\. 

### Service roles<a name="security_iam_service-with-iam-roles-service"></a>

CodePipeline allows a service to assume a [service role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-role) on your behalf\. This role allows the service to access resources in other services to complete an action on your behalf\. Service roles appear in your IAM account and are owned by the account\. This means that an IAM administrator can change the permissions for this role\. However, doing so might break the functionality of the service\.

CodePipeline supports service roles\. 