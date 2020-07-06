# JSON data format for manual approval notifications in CodePipeline<a name="approvals-json-format"></a>

For approval actions that use Amazon SNS notifications, JSON data about the action is created and published to Amazon SNS when the pipeline stops\. You can use the JSON output to send messages to Amazon SQS queues or invoke functions in AWS Lambda\. 

**Note**  
This guide does not address how to configure notifications using JSON\. For information, see [Sending Amazon SNS Messages to Amazon SQS Queues](https://docs.aws.amazon.com/sns/latest/dg/SendMessageToSQS.html) and [Invoking Lambda Functions Using Amazon SNS Notifications](https://docs.aws.amazon.com/sns/latest/dg/sns-lambda.html) in the *Amazon SNS Developer Guide*\.

The following example shows the structure of the JSON output available with CodePipeline approvals\.

```
{
    "region": "us-east-2",
    "consoleLink": "https://console.aws.amazon.com/codepipeline/home?region=us-east-2#/view/MyFirstPipeline",
    "approval": {
        "pipelineName": "MyFirstPipeline",
        "stageName": "MyApprovalStage",
        "actionName": "MyApprovalAction",
        "token": "1a2b3c4d-573f-4ea7-a67E-XAMPLETOKEN",
        "expires": "2016-07-07T20:22Z",
        "externalEntityLink": "http://example.com",
        "approvalReviewLink": "https://console.aws.amazon.com/codepipeline/home?region=us-east-2#/view/MyFirstPipeline/MyApprovalStage/MyApprovalAction/approve/1a2b3c4d-573f-4ea7-a67E-XAMPLETOKEN",
        "customData": "Review the latest changes and approve or reject within seven days."
    }
}
```