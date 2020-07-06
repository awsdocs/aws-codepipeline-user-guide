# CodePipeline permissions reference<a name="permissions-reference"></a>

Use the following table as a reference when you are setting up access control and writing permissions policies that you can attach to an IAM identity \(identity\-based policies\)\. The table lists each CodePipeline API operation and the corresponding actions for which you can grant permissions to perform the action\. For operations that support *resource\-level permissions*, the table lists the AWS resource for which you can grant the permissions\. You specify the actions in the policy's `Action` field\.

*Resource\-level permissions* are those that allow you to specify which resources users are allowed to perform actions on\. AWS CodePipeline provides partial support for resource\-level permissions\. This means that for some AWS CodePipeline API calls, you can control when users are allowed to use those actions based on conditions that must be met, or which resources users are allowed to use\. For example, you can grant users permission to list pipeline execution information, but only for a specific pipeline or pipelines\.

**Note**  
The **Resources** column lists the resource required for API calls that support resource\-level permissions\. For API calls that do not support resource\-level permissions, you can grant users permission to use it, but you have to specify a wildcard \(\*\) for the resource element of your policy statement\.


**CodePipeline API Operations and required permissions for actions**  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/permissions-reference.html)