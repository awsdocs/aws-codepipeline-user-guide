# Grant Approval Permissions to an IAM User in AWS CodePipeline<a name="approvals-iam-permissions"></a>

Before IAM users in your organization can approve or reject approval actions, they must be granted permissions to access pipelines and to update the status of approval actions\. You can grant permission to access all pipelines and approval actions in your account by attaching the `AWSCodePipelineApproverAccess` managed policy to an IAM user, role, or group; or you can to grant limited permissions by specifying the individual resources that can be accessed by an IAM user, role, or group\.

**Note**  
The permissions described in this topic grant very limited access\. To enable a user, role, or group to do more than approve or reject approval actions, you can attach other managed policies\. For information about the managed policies available for AWS CodePipeline, see [AWS Managed \(Predefined\) Policies for AWS CodePipeline](managed-policies.md)\.

## Grant Approval Permission to All Pipelines and Approval Actions<a name="approvals-iam-permissions-all"></a>

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Groups**, **Roles**, or **Users**\.

1. Choose the group, role or IAM user to grant permissions to\.

1. Choose the **Permissions** tab\.

1. Choose **Add permissions**, and then choose **Attach existing policies directly **\.

1. Select the check box next to `AWSCodePipelineApproverAccess` managed policy, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

## Specify Approval Permission for Specific Pipelines and Approval Actions<a name="approvals-iam-permissions-limited"></a>

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.
**Important**  
Make sure you are signed in to the AWS Management Console with the same account information you used in [Getting Started with AWS CodePipeline](getting-started-codepipeline.md)\.

1. In the navigation pane, choose **Groups** or **Users**, as appropriate\.

1. Browse to and choose the user or group you want to change\. 

1. On the summary page, choose the **Permissions** tab, and expand the **Inline Policies** section\.

1. Do one of the following:
   + If you chose **Groups**, now choose **Create Group Policy**\. If you chose **Users**, now choose **Create User Policy**\.
   + If no inline policies have been created yet, choose **click here**\.

1. Choose **Custom Policy**, and then choose **Select**\. 

1. Type a name for this policy in **Policy Name**\.

1. Specify the individual resources an IAM user can access\. For example, the following policy grants users the authority to approve or reject only the action named `MyApprovalAction` in the `MyFirstPipeline` pipeline in the US East \(Ohio\) Region \(us\-east\-2\):

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Action": [
                   "codepipeline:ListPipelines"
               ],
               "Resource": [
                   "*"
               ],
               "Effect": "Allow"
           },
           {
               "Action": [
                   "codepipeline:GetPipeline",
                   "codepipeline:GetPipelineState",
                   "codepipeline:GetPipelineExecution"
               ],
               "Effect": "Allow",
               "Resource": "arn:aws:codepipeline:us-east-2:80398EXAMPLE:MyFirstPipeline"
           },
           {
               "Action": [
                   "codepipeline:PutApprovalResult"
               ],
               "Effect": "Allow",
               "Resource": "arn:aws:codepipeline:us-east-2:80398EXAMPLE:MyFirstPipeline/MyApprovalStage/MyApprovalAction"
           }
       ]
   }
   ```
**Note**  
The `codepipeline:ListPipelines` permission is required only if IAM users need to access the AWS CodePipeline dashboard to view this list of pipelines\. If console access is not required, you can omit `codepipeline:ListPipelines`\.

1. Choose **Validate Policy**\. Correct any errors displayed in a red box at the top of the page\. 

1. When you are satisfied with the policy, choose **Apply Policy**\.