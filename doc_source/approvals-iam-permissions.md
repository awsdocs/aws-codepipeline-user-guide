# Grant approval permissions to an IAM user in CodePipeline<a name="approvals-iam-permissions"></a>

Before IAM users in your organization can approve or reject approval actions, they must be granted permissions to access pipelines and to update the status of approval actions\. You can grant permission to access all pipelines and approval actions in your account by attaching the `AWSCodePipelineApproverAccess` managed policy to an IAM user, role, or group; or you can to grant limited permissions by specifying the individual resources that can be accessed by an IAM user, role, or group\.

**Note**  
The permissions described in this topic grant very limited access\. To enable a user, role, or group to do more than approve or reject approval actions, you can attach other managed policies\. For information about the managed policies available for CodePipeline, see [AWS managed \(predefined\) policies for CodePipeline](managed-policies.md)\.

## Grant approval permission to all pipelines and approval actions<a name="approvals-iam-permissions-all"></a>

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Groups**, **Roles**, or **Users**\.

1. Choose the group, role or IAM user to grant permissions to\.

1. Choose the **Permissions** tab\.

1. Choose **Add permissions**, and then choose **Attach existing policies directly **\.

1. Select the check box next to `AWSCodePipelineApproverAccess` managed policy, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

## Specify approval permission for specific pipelines and approval actions<a name="approvals-iam-permissions-limited"></a>

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.
**Important**  
Make sure you are signed in to the AWS Management Console with the same account information you used in [Getting started with CodePipeline](getting-started-codepipeline.md)\.

1. In the navigation pane, choose **Groups** or **Users**, as appropriate\.

1. Browse to and choose the user or group you want to change\. 

1. Do one of the following:
   + If you chose **Groups**, choose the **Permissions** tab, and expand **Inline Policies**\. If no inline policies have been created yet, choose **click here**\.

     Choose **Custom Policy**, and then choose **Select**\. 

     In **Policy Name**, enter a name for this policy\. Continue to the next step to paste the policy in the **Policy Document** box\.
   + If you chose **Users**, choose the **Permissions** tab, and choose **Add inline policy**\. Choose the **JSON** tab\. Continue to the next step to paste the policy\.

1. Paste the policy into the policy box\. Specify the individual resources an IAM user can access\. For example, the following policy grants users the authority to approve or reject only the action named `MyApprovalAction` in the `MyFirstPipeline` pipeline in the US East \(Ohio\) Region \(us\-east\-2\):

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
The `codepipeline:ListPipelines` permission is required only if IAM users need to access the CodePipeline dashboard to view this list of pipelines\. If console access is not required, you can omit `codepipeline:ListPipelines`\.

1. Do one of the following:
   + If you chose **Groups**, choose **Validate Policy**\. Correct any errors displayed in a red box at the top of the page\. Choose **Apply Policy**\.
   + If you chose **Users**, choose **Review policy**\.

     In **Name**, enter a name for this policy\. Choose **Create policy**\.