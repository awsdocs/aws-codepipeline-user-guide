# Grant Amazon SNS permissions to a CodePipeline service role<a name="approvals-service-role-permissions"></a>

If you plan to use Amazon SNS to publish notifications to topics when approval actions require review, the service role you use in your CodePipeline operations must be granted permission to access the Amazon SNS resources\. You can use the IAM console to add this permission to your service role\.

****

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.
**Important**  
Make sure you are signed in to the AWS Management Console with the same account information you used in [Getting started with CodePipeline](getting-started-codepipeline.md)\.

1. In the IAM console, in the navigation pane, choose **Roles**\.

1. Choose the name of the service role you use in your CodePipeline operations\.

1. On the **Permissions** tab, in the **Inline Policies** area, choose **Create Role Policy**\.

   –or–

   If the **Create Role Policy** button is not available, expand the **Inline Policies** area, and then choose **click here**\.

1. On the **Set Permissions** page, choose **Custom Policy**, and then choose **Select**\.

1. On the **Review Policy** page, in the **Policy Name** field, type a name to identify this policy, such as `SNSPublish`\.

1. Paste the following into the **Policy Document** field: 

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "sns:Publish",
               "Resource": "*"
           }
       ]
   }
   ```

1. Choose **Apply Policy**\.