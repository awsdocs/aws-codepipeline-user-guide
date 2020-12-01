# Grant Amazon SNS permissions to a CodePipeline service role<a name="approvals-service-role-permissions"></a>

If you plan to use Amazon SNS to publish notifications to topics when approval actions require review, the service role you use in your CodePipeline operations must be granted permission to access the Amazon SNS resources\. You can use the IAM console to add this permission to your service role\.

****

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.
**Important**  
Make sure you are signed in to the AWS Management Console with the same account information you used in [Getting started with CodePipeline](getting-started-codepipeline.md)\.

1. In the IAM console, in the navigation pane, choose **Roles**\.

1. Choose the name of the service role you use in your CodePipeline operations\.

1. Choose the **Permissions** tab\. Choose **Attach policies**\.

1. Choose **Create policy**\.

1. On the **Create policy** page, choose the **JSON** tab\.

   Paste the following policy: 

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

   Choose **Review policy**\.

1. On the **Review policy** page, in the **Name** field, type a name to identify this policy, such as `SNSPublish`\. Choose **Create policy**\.

1. Go back to your service role and choose the **Permissions** tab\. Choose **Attach policies**\.

   Find your `SNSPublish` policy and choose the box next to it\. Choose **Attach policy**\.