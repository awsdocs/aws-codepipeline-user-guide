# Use Parameter Store to Track Database Passwords or Third\-Party API Keys<a name="parameter-store-encryption"></a>

You can use Parameter Store to track and update configuration secrets, such as database passwords\. This procedure describes how to create a secret parameter in Parameter Store\. You can also create automated scripts to use Parameter Store to securely set and manage your passwords and keys automatically\. For an example of build spec automation for CodeDeploy, see this [ blog post](https://aws.amazon.com/blogs/mt/use-parameter-store-to-securely-access-secrets-and-config-data-in-aws-codedeploy/)\.

**Manually create a parameter in Parameter Store**

1. Sign in to your AWS account and go to the Amazon EC2 console\.

1. Under **Systems Manager Shared Resources**, choose **Parameter Store**\.

1. Choose **Get Started Now** or **Create Parameter**, and then enter the following information:

   1. In **Name**, type a name for your parameter\.

   1. Under **Type**, choose **Secure String**\. This encrypts sensitive data using your default AWS KMS key\.

   1. Paste the parameter into the **Value** field\.

1. Choose **Create Parameter**\. Your new parameter appears in the Parameter Store console\.