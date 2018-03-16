# AWS CodePipeline User Guide

-----
*****Copyright &copy; 2018 Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

-----
Amazon's trademarks and trade dress may not be used in 
     connection with any product or service that is not Amazon's, 
     in any manner that is likely to cause confusion among customers, 
     or in any manner that disparages or discredits Amazon. All other 
     trademarks not owned by Amazon are the property of their respective
     owners, who may or may not be affiliated with, connected to, or 
     sponsored by Amazon.

-----
## Contents
+ [What Is AWS CodePipeline?](welcome.md)
   + [AWS CodePipeline Concepts](concepts.md)
+ [Getting Started with AWS CodePipeline](getting-started-codepipeline.md)
+ [Product and Service Integrations with AWS CodePipeline](integrations.md)
   + [Integrations with AWS CodePipeline Action Types](integrations-action-type.md)
   + [General Integrations with AWS CodePipeline](integrations-general.md)
   + [Examples from the Community](integrations-community.md)
      + [Integration Examples: Blog Posts](integrations-community-blogposts.md)
      + [Integration Examples: Videos](integrations-community-videos.md)
+ [AWS CodePipeline Tutorials](tutorials.md)
   + [Tutorial: Create a Simple Pipeline (Amazon S3 Bucket)](tutorials-simple-s3.md)
   + [Tutorial: Create a Simple Pipeline (AWS CodeCommit Repository)](tutorials-simple-codecommit.md)
   + [Tutorial: Create a Four-Stage Pipeline](tutorials-four-stage-pipeline.md)
   + [Tutorial: Set Up a CloudWatch Events Rule to Receive Email Notifications for Pipeline State Changes](tutorials-cloudwatch-sns-notifications.md)
+ [AWS CodePipeline Best Practices and Use Cases](best-practices.md)
   + [Best Practices](best-practices-1.md)
   + [Continuous Delivery of AWS Lambda-Based Applications](use-cases-lambda.md)
   + [Continuous Delivery of AWS CloudFormation Templates](use-cases-cloudformation.md)
+ [Working with Pipelines in AWS CodePipeline](pipelines.md)
   + [How to Start a Pipeline Execution in AWS CodePipeline](pipelines-about-starting.md)
   + [Create a Pipeline in AWS CodePipeline](pipelines-create.md)
   + [Edit a Pipeline in AWS CodePipeline](pipelines-edit.md)
   + [Start a Pipeline Manually in AWS CodePipeline](pipelines-rerun-manually.md)
   + [Start a Pipeline Automatically Using Amazon CloudWatch Events](triggering.md)
   + [Start a Pipeline Automatically Using Periodic Checks](run-automatically-polling.md)
   + [View Pipeline Details and History in AWS CodePipeline](pipelines-view.md)
   + [Delete a Pipeline in AWS CodePipeline](pipelines-delete.md)
   + [Create a Pipeline in AWS CodePipeline That Uses Resources from Another AWS Account](pipelines-create-cross-account.md)
+ [Working with Actions in AWS CodePipeline](actions.md)
   + [Create and Add a Custom Action in AWS CodePipeline](actions-create-custom-action.md)
   + [Invoke an AWS Lambda Function in a Pipeline in AWS CodePipeline](actions-invoke-lambda-function.md)
   + [Retry a Failed Action in AWS CodePipeline](actions-retry.md)
   + [Manage Approval Actions in AWS CodePipeline](approvals.md)
      + [Grant Approval Permissions to an IAM User in AWS CodePipeline](approvals-iam-permissions.md)
      + [Grant Amazon SNS Permissions to an AWS CodePipeline Service Role](approvals-service-role-permissions.md)
      + [Add a Manual Approval Action to a Pipeline in AWS CodePipeline](approvals-action-add.md)
      + [Approve or Reject an Approval Action in AWS CodePipeline](approvals-approve-or-reject.md)
      + [JSON Data Format for Manual Approval Notifications in AWS CodePipeline](approvals-json-format.md)
+ [Working with Stage Transitions in AWS CodePipeline](transitions.md)
+ [Monitoring Pipelines with AWS CodePipeline](monitoring.md)
   + [Detect and React to Changes in Pipeline State with Amazon CloudWatch Events](detect-state-changes-cloudwatch-events.md)
   + [Log AWS CodePipeline API Calls with AWS CloudTrail](monitoring-cloudtrail-logs.md)
   + [View Current Source Revision Details in a Pipeline in AWS CodePipeline](monitoring-source-revisions-view.md)
+ [Troubleshooting AWS CodePipeline](troubleshooting.md)
+ [Authentication, Access Control, and Security Best Practices for AWS CodePipeline](auth-and-access-control.md)
   + [Authentication](authentication.md)
   + [Access Control with IAM Policies](access-control.md)
      + [Overview of Managing Access Permissions to Your AWS CodePipeline Resources](iam-access-control-identity-based.md)
      + [Using Identity-Based Policies (IAM Policies) for AWS CodePipeline](iam-identity-based-access-control.md)
      + [Permissions Required to Use the AWS CodePipeline Console](console-permissions.md)
      + [AWS Managed (Predefined) Policies for AWS CodePipeline](managed-policies.md)
      + [Manage the AWS CodePipeline Service Role](how-to-custom-role.md)
      + [Customer Managed Policy Examples](customer-managed-policies.md)
      + [AWS CodePipeline Permissions Reference](permissions-reference.md)
   + [Security Best Practices](best-practices-security.md)
      + [Configure Server-Side Encryption for Artifacts Stored in Amazon S3 for AWS CodePipeline](S3-artifact-encryption.md)
         + [View Your Default Amazon S3 SSE-KMS Encryption Keys](S3-view-default-keys.md)
         + [Configure Server-Side Encryption for S3 Buckets When Using AWS CloudFormation or the CLI](S3-rotate-customer-key.md)
      + [Configure GitHub Authentication](GitHub-authentication.md)
         + [View Your Authorized OAuth Apps](GitHub-view-oauth-token.md)
         + [Use GitHub and the AWS CodePipeline CLI to Create and Rotate Your GitHub Personal Access Token on a Regular Basis](GitHub-rotate-personal-token-CLI.md)
      + [Use Parameter Store to Track Database Passwords or Third Party API Keys](parameter-store-encryption.md)
+ [AWS CodePipeline Command Line Reference](reference-command-line.md)
+ [AWS CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)
+ [Limits in AWS CodePipeline](limits.md)
+ [AWS CodePipeline User Guide Document History](history.md)
+ [AWS Glossary](glossary.md)