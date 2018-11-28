--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Use Parameter Store to Track Database Passwords or Third Party API Keys<a name="parameter-store-encryption"></a>

You can use Parameter Store to track and update configuration secrets such as database passwords\. This procedure describes using Parameter Store to manually create a secret parameter\. You can also create automated scripts to use Parameter Store to securely set and manage your passwords and keys automatically\. See an example of build spec automation for AWS CodeDeploy at this [ blog post](https://aws.amazon.com/blogs/mt/use-parameter-store-to-securely-access-secrets-and-config-data-in-aws-codedeploy/)\.

**Manually create a parameter in Parameter Store**

1. Sign in to your AWS account and go to the Amazon EC2 console\.

1. Under the **Systems Manager Shared Resources** section, click **Parameter Store**\.

1. Click **Get Started Now** or **Create Parameter** and enter the following information:

   1. Type a name for your parameter in the **Name** field\.

   1. Under **Type**, choose **Secure String**\. This encrypts sensitive data using your default AWS KMS key\.

   1. Paste the parameter into the **Value** field\.

1. Click **Create Parameter**, and it will bring you to the Parameter Store console where you can see your newly created parameter\.