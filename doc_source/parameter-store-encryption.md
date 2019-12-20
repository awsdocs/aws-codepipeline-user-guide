# Use Parameter Store to Track Database Passwords or Third Party API Keys<a name="parameter-store-encryption"></a>

You can use Parameter Store to track and update configuration secrets such as database passwords\. This procedure shows you how to use Parameter Store to create a secret parameter\. You can also create automated scripts to use Parameter Store to securely set and manage your passwords and keys automatically\. For an example of build spec automation for CodeDeploy, see [Use Parameter Store to Securely Access Secrets and Config Data in AWS CodeDeploy](https://aws.amazon.com/blogs/mt/use-parameter-store-to-securely-access-secrets-and-config-data-in-aws-codedeploy/) on the AWS Management and Governance Blog\.

**To create a parameter in Parameter Store**

1. Sign in to your AWS account and go to the Amazon EC2 console\.

1. Under **Systems Manager Shared Resources**, choose **Parameter Store**\.

1. Choose **Get Started Now** or **Create Parameter** and enter the following information:

   1. In **Name**, enter a name for your parameter\.

   1. Under **Type**, choose **Secure String**\. Your default AWS KMS key will be used to encrypt sensitive data\.

   1. Paste the parameter in **Value**\.

1. Choose **Create Parameter**\. The Parameter Store console opens and you can see your newly created parameter\.