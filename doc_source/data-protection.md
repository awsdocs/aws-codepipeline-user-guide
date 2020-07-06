# Data protection in AWS CodePipeline<a name="data-protection"></a>

AWS CodePipeline conforms to the AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/), which includes regulations and guidelines for data protection\. AWS is responsible for protecting the global infrastructure that runs all of the AWS services\. AWS maintains control over data hosted on this infrastructure, including the security configuration controls for handling customer content and personal data\. AWS customers and APN partners, acting either as data controllers or data processors, are responsible for any personal data that they put in the AWS Cloud\. 

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\) so that each user is given only the permissions required to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls in AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.

We strongly recommend that you never put sensitive identifying information, such as your customers' account numbers, into free\-form fields such as a **Name** field\. This includes when you work with CodePipeline or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into CodePipeline or other services might get picked up for inclusion in diagnostic logs\. When you provide a URL to an external server, don't include credentials information in the URL to validate your request to that server\.

For more information about data protection, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

## Internetwork traffic privacy<a name="inter-network-traffic-privacy"></a>

 Amazon VPC is an AWS service that you can use to launch AWS resources in a virtual network \(*virtual private cloud*\) that you define\. CodePipeline supports Amazon VPC endpoints powered by AWS PrivateLink, an AWS technology that facilitates private communication between AWS services using an elastic network interface with private IP addresses\. This means you can connect directly to CodePipeline through a private endpoint in your VPC, keeping all traffic inside your VPC and the AWS network\. Previously, applications running inside a VPC required internet access to connect to CodePipeline\. With a VPC, you have control over your network settings, such as:
+ IP address range\.
+ Subnets\.
+ Route tables\.
+ Network gateways\.

To connect your VPC to CodePipeline, you define an interface VPC endpoint for CodePipeline\. This type of endpoint makes it possible for you to connect your VPC to AWS services\. The endpoint provides reliable, scalable connectivity to CodePipeline without requiring an internet gateway, network address translation \(NAT\) instance, or VPN connection\. For information about setting up a VPC, see the [VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\.

## Encryption at rest<a name="encryption-at-rest"></a>

Data in CodePipeline is encrypted at rest using service\-owned KMS keys\. Code artifacts are stored in a customer\-owned S3 bucket and encrypted with either the default AWS managed SSE\-KMS encryption key or a customer managed SSE\-KMS key\. For more information, see [Configure server\-side encryption for artifacts stored in Amazon S3 for CodePipeline](S3-artifact-encryption.md)\.

## Encryption in transit<a name="encryption-in-transit"></a>

All service to service communication is encrypted in transit using SSL/TLS\. 

## Encryption key management<a name="key-management"></a>

If the customer chooses the default option for encrypting code artifacts, CodePipeline uses the default AWS managed SSE\-KMS encryption key\. Customers cannot change or delete this AWS managed key\. If customers are using a customer managed key \(CMK\) in AWS KMS to encrypt or decrypt artifacts in the S3 bucket, they can change or rotate this key as necessary\.

**Important**  
CodePipeline only supports symmetric customer master keys \(CMKs\)\. Do not use an asymmetric CMK to encrypt the data in your S3 bucket\.

## Data protection configuration<a name="security-configuration"></a>

This section describes data protection configuration for the following:
+ S3 artifacts server\-side encryption \(SSE\)\.
+ GitHub personal access tokens\.
+ Secret parameter tracking in AWS Secrets Manager\.

**Topics**
+ [Configure server\-side encryption for artifacts stored in Amazon S3 for CodePipeline](S3-artifact-encryption.md)
+ [Configure GitHub authentication](GitHub-authentication.md)
+ [Use AWS Secrets Manager to track database passwords or third\-party API keys](parameter-store-encryption.md)