# Manage approval actions in CodePipeline<a name="approvals"></a>

In AWS CodePipeline, you can add an approval action to a stage in a pipeline at the point where you want the pipeline execution to stop so that someone with the required AWS Identity and Access Management permissions can approve or reject the action\. 

If the action is approved, the pipeline execution resumes\. If the action is rejected—or if no one approves or rejects the action within seven days of the pipeline reaching the action and stopping—the result is the same as an action failing, and the pipeline execution does not continue\.

You might use manual approvals for these reasons:
+ You want someone to perform a code review or change management review before a revision is allowed into the next stage of a pipeline\.
+ You want someone to perform manual quality assurance testing on the latest version of an application, or to confirm the integrity of a build artifact, before it is released\.
+ You want someone to review new or updated text before it is published to a company website\.

## Configuration options for manual approval actions in CodePipeline<a name="approvals-configuration-options"></a>

CodePipeline provides three configuration options you can use to tell approvers about the approval action\. 

**Publish Approval Notifications** You can configure an approval action to publish a message to an Amazon Simple Notification Service topic when the pipeline stops at the action\. Amazon SNS delivers the message to every endpoint subscribed to the topic\. You must use a topic created in the same AWS Region as the pipeline that will include the approval action\. When you create a topic, we recommend you give it a name that will identify its purpose, in formats such as `MyFirstPipeline-us-east-2-approval`\. 

When you publish approval notifications to Amazon SNS topics, you can choose from formats such as email or SMS recipients, SQS queues, HTTP/HTTPS endpoints, or AWS Lambda functions you invoke using Amazon SNS\. For information about Amazon SNS topic notifications, see the following topics:
+ [What Is Amazon Simple Notification Service?](https://docs.aws.amazon.com/sns/latest/dg/welcome.html)
+ [Create a Topic in Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/CreateTopic.html)
+ [Sending Amazon SNS Messages to Amazon SQS Queues](https://docs.aws.amazon.com/sns/latest/dg/SendMessageToSQS.html)
+ [Subscribing a Queue to an Amazon SNS Topic](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqssubscribe.html)
+ [Sending Amazon SNS Messages to HTTP/HTTPS Endpoints](https://docs.aws.amazon.com/sns/latest/dg/SendMessageToHttp.html)
+ [Invoking Lambda Functions Using Amazon SNS Notifications](https://docs.aws.amazon.com/sns/latest/dg/sns-lambda.html)

For the structure of the JSON data generated for an approval action notification, see [JSON data format for manual approval notifications in CodePipeline](approvals-json-format.md)\.

**Specify a URL for Review** As part of the configuration of the approval action, you can specify a URL to be reviewed\. The URL might be a link to a web application you want approvers to test or a page with more information about your approval request\. The URL is included in the notification that is published to the Amazon SNS topic\. Approvers can use the console or CLI to view it\. 

**Enter Comments for Approvers** When you create an approval action, you can also add comments that are displayed to those who receive the notifications or those who view the action in the console or CLI response\.

**No Configuration Options** You can also choose not to configure any of these three options\. You might not need them if, for example, you can notify someone directly that the action is ready for their review, or you simply want the pipeline to stop until you decide to approve the action yourself\. 

## Setup and workflow overview for approval actions in CodePipeline<a name="approvals-overview"></a>

The following is an overview for setting up and using manual approvals\. 

1. You grant the IAM permissions required for approving or rejecting approval actions to one or more IAM users in your organization\. 

1. \(Optional\) If you are using Amazon SNS notifications, you ensure that the service role you use in your CodePipeline operations has permission to access Amazon SNS resources\. 

1. \(Optional\) If you are using Amazon SNS notifications, you create an Amazon SNS topic and add one or more subscribers or endpoints to it\. 

1. When you use the AWS CLI to create the pipeline or after you have used the CLI or console to create the pipeline, you add an approval action to a stage in the pipeline\. 

   If you are using notifications, you include the Amazon Resource Name \(ARN\) of the Amazon SNS topic in the configuration of the action\. \(An ARN is a unique identifier for an Amazon resource\. ARNs for Amazon SNS topics are structured like *arn:aws:sns:us\-east\-2:80398EXAMPLE:MyApprovalTopic*\. For more information, see [Amazon Resource Names \(ARNs\) and AWS Service Namespaces](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *Amazon Web Services General Reference*\.\)

1. The pipeline stops when it reaches the approval action\. If an Amazon SNS topic ARN was included in the configuration of the action, a notification is published to the Amazon SNS topic, and a message is delivered to any subscribers to the topic or subscribed endpoints, with a link to review the approval action in the console\.

1. An approver examines the target URL and reviews comments, if any\.

1. Using the console, CLI, or SDK, the approver provides a summary comment and submits a response:
   + Approved: The pipeline execution resumes\.
   + Rejected: The stage status is changed to "Failed" and the pipeline execution does not resume\. 

   If no response is submitted within seven days, the action is marked as "Failed\."