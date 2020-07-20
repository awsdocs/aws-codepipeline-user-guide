# Detect and react to changes in pipeline state with Amazon CloudWatch Events<a name="detect-state-changes-cloudwatch-events"></a>

Amazon CloudWatch Events is a web service that monitors your AWS resources and the applications you run on AWS\. You can use Amazon CloudWatch Events to detect and react to changes in the state of a pipeline, stage, or action\. Then, based on rules you create, CloudWatch Events invokes one or more target actions when a pipeline, stage, or action enters the state you specify in a rule\. Depending on the type of state change, you might want to send notifications, capture state information, take corrective action, initiate events, or take other actions\.

Amazon CloudWatch Events are composed of:
+ **Rules\.** An event in Amazon CloudWatch Events is configured by first creating a rule with a selected service as the event source\. 
+ **Targets\.** The new rule receives a selected service as the event target\. For a list of services available as Amazon CloudWatch Events targets, see [What Is Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)\.

Examples of Amazon CloudWatch Events rules and targets:
+ A rule that sends a notification when the instance state changes, where an EC2 instance is the event source and Amazon SNS is the event target\.
+ A rule that sends a notification when the build phase changes, where a CodeBuild configuration is the event source and Amazon SNS is the event target\.
+ A rule that detects pipeline changes and invokes an AWS Lambda function\.

To configure AWS CodePipeline as an event source:

1. Create an Amazon CloudWatch Events rule that uses CodePipeline as an event source\.

1. Create a target for your rule that uses one of the services available as targets in Amazon CloudWatch Events, such as AWS Lambda or Amazon SNS\.

1. Grant permissions to Amazon CloudWatch Events to allow it to invoke the selected target service\. 

## Understand how a pipeline execution state change rule works<a name="create-cloudwatch-notifications"></a>

You build rules for detecting and reacting to pipeline state changes using the **Events** window in Amazon CloudWatch\. As you build your rule, the **Event Pattern Preview** box in the console \(or the `--event-pattern` output in the CLI\) displays the event fields, in JSON format\. 

You can configure notifications to be sent when the state changes for:
+ Specified pipelines or all your pipelines\. You control this by using `"detail-type":` `"CodePipeline Pipeline Execution State Change"`\.
+ Specified stages or all your stages, within a specified pipeline or all your pipelines\. You control this by using `"detail-type":` `"CodePipeline Stage Execution State Change"`\.
+ Specified actions or all actions, within a specified stage or all stages, within a specified pipeline or all your pipelines\. You control this by using `"detail-type":` `"CodePipeline Action Execution State Change"`\.

Each type of execution state change event emits notifications with specific message content, where:
+ The initial `version` entry shows the version number for the CloudWatch event\.
+ The `version` entry under pipeline `detail` shows the pipeline structure version number\.
+ The `execution-id` entry under pipeline `detail` shows the execution ID for the pipeline execution that caused the state change\. Refer to the GetPipelineExecution API call in the [AWS CodePipeline API Reference](https://docs.aws.amazon.com/codepipeline/latest/APIReference/)\.

**Pipeline execution state change message content: ** When a pipeline execution starts, it emits an event that sends notifications with the following content\. This example is for the pipeline named `"myPipeline"` in the `us-east-1` region\.

```
{
    "version": "0",
    "id": event_Id,
    "detail-type": "CodePipeline Pipeline Execution State Change",
    "source": "aws.codepipeline",
    "account": Pipeline_Account,
    "time": TimeStamp,
    "region": "us-east-1",
    "resources": [
        "arn:aws:codepipeline:us-east-1:account_ID:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "version": "1",
        "state": "STARTED",
        "execution-id": execution_Id
    }
}
```

**Stage execution state change message content: **When a stage execution starts, it emits an event that sends notifications with the following content\. This example is for the pipeline named `"myPipeline"` in the `us-east-1` region, for the stage `"Prod"`\.

```
{
    "version": "0",
    "id": event_Id,
    "detail-type": "CodePipeline Stage Execution State Change",
    "source": "aws.codepipeline",
    "account": Pipeline_Account,
    "time": TimeStamp,
    "region": "us-east-1",
    "resources": [
        "arn:aws:codepipeline:us-east-1:account_ID:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "version": "1",
        "execution-id": execution_Id,
        "stage": "Prod",
        "state": "STARTED"
    }
}
```

**Action execution state change message content: ** When an action execution starts, it emits an event that sends notifications with the following content\. This example is for the pipeline named `"myPipeline"` in the `us-east-1` region, for the action `"myAction"`\.

```
{
    "version": "0",
    "id": event_Id,
    "detail-type": "CodePipeline Action Execution State Change",
    "source": "aws.codepipeline",
    "account": Pipeline_Account,
    "time": TimeStamp,
    "region": "us-east-1",
    "resources": [
        "arn:aws:codepipeline:us-east-1:account_ID:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "version": "1",
        "execution-id": execution_Id,
        "stage": "Prod",
        "action": "myAction",
        "state": "STARTED",
        "type": {
            "owner": "AWS",
            "category": "Deploy",
            "provider": "CodeDeploy",
            "version": 1
        }
    }
}
```

Valid state values:


**Pipeline\-level states in events**  

| Pipeline state | Description | 
| --- | --- | 
| STARTED | The pipeline execution is currently running\. | 
| SUCCEEDED | The pipeline execution was completed successfully\. | 
| RESUMED | A failed pipeline execution has been retried in response to the RetryStageExecution API call\. | 
| FAILED | The pipeline execution was not completed successfully\. | 
| CANCELED | The pipeline execution was canceled because the pipeline structure was updated\. | 
| SUPERSEDED |  While this pipeline execution was waiting for the next stage to be completed, a newer pipeline execution advanced and continued through the pipeline instead\.  | 


**Stage\-level states in events**  

| Stage state | Description | 
| --- | --- | 
| STARTED | The stage is currently running\. | 
| SUCCEEDED | The stage was completed successfully\. | 
| RESUMED | A failed stage has been retried in response to the RetryStageExecution API call\. | 
| FAILED | The stage was not completed successfully\. | 
| CANCELED | The stage was canceled because the pipeline structure was updated\. | 


**Action\-level states in events**  

| Action state | Description | 
| --- | --- | 
| STARTED | The action is currently running\. | 
| SUCCEEDED | The action was completed successfully\. | 
| FAILED | For Approval actions, the FAILED state means the action was either rejected by the reviewer or failed due to an incorrect action configuration\. | 
| CANCELED | The action was canceled because the pipeline structure was updated\. | 

### Prerequisites<a name="cloudwatch-notifications-prerequisites"></a>

Before you create event rules for use in your CodePipeline operations, you should do the following:
+ Complete the CloudWatch Events prerequisites\. For this information, see [Regional Endpoints](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/GettingSetup_cwe.html#CWE_Prerequisites)\.
+ Familiarize yourself with events, rules, and targets in CloudWatch Events\. For more information, see [What Is Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)\.
+ Create the target or targets you will use in your event rules, such as an Amazon SNS notification topic\.

### Send a notification whenever pipeline state changes \(console\)<a name="monitoring-cloudwatch-events-about"></a>

These steps show how to use the CloudWatch console to create a rule to send notifications of changes in CodePipeline\. 

**To create a CloudWatch Events rule with CodePipeline as the event source**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Create rule**\. Under **Event source**, from the **Service Name** drop\-down list, choose **CodePipeline**\.

1. From the **Event Type** drop\-down list, choose the level of state change for the notification\.
   + For a rule that applies to pipeline\-level events, choose **CodePipeline Pipeline Execution State Change**\.
   + For a rule that applies to stage\-level events, choose **CodePipeline Stage Execution State Change**\.
   + For a rule that applies to action\-level events, choose **CodePipeline Action Execution State Change**\.

1. Specify the state changes the rule applies to:
   + For a rule that applies to all state changes, choose **Any state**\.
   + For a rule that applies to some state changes only, choose **Specific state\(s\)**, and then choose one or more state values from the list\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/test-notifications-in-cwe.png)

1. For event patterns that are more detailed than the selectors allow, you can also use the **Edit** option in the **Event Pattern Preview** window to designate an event pattern in JSON format\. The following example shows the JSON structure edited manually to specify a pipeline named "myPipeline\."  
![\[A pipeline name can be edited manually in the JSON structure for the rule.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/cloudwatch-rule-event-pattern.png)![\[A pipeline name can be edited manually in the JSON structure for the rule.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A pipeline name can be edited manually in the JSON structure for the rule.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
**Note**  
If not otherwise specified, then the event pattern is created for all pipelines/stages/actions and states\.

   For more detailed event patterns, you can copy and paste the following example event patterns into the **Edit** window\.
   +   
**Example**  

     Use this sample event pattern to capture failed deploy and build actions across all the pipelines\.

     ```
     {
     "source": [
         "aws.codepipeline"
       ],
       "detail-type": [
         "CodePipeline Action Execution State Change"
       ],
       "detail": {
         "state": [
           "FAILED"
         ],
         "type": {
           "category": ["Deploy", "Build"]
         }
       }
     }
     ```
   +   
**Example**  

     Use this sample event pattern to capture all rejected or failed approval actions across all the pipelines\.

     ```
     {
      "source": [
         "aws.codepipeline"
       ],
       "detail-type": [
         "CodePipeline Action Execution State Change"
       ],
       "detail": {
         "state": [
           "FAILED"
         ],
         "type": {
           "category": ["Approval"]
         }
       }
     }
     ```
   +   
**Example**  

     Use this sample event pattern to capture all the events from the specified pipelines\.

     ```
     {
     "source": [
         "aws.codepipeline"
       ],
       "detail-type": [
         "CodePipeline Pipeline Execution State Change",
         "CodePipeline Action Execution State Change",
         "CodePipeline Stage Execution State Change"
       ],
       "detail": {
         "pipeline": ["myPipeline", "my2ndPipeline"]
       }
     }
     ```

1. In the **Targets** area, choose **Add target\***\.

1. In the **Select target type** list, choose the type of target for this rule, and then configure options required by that type\. 

1. Choose **Configure details**\.

1. On the **Configure rule details** page, type a name and description for the rule, and then select the **State** box to enable to rule now\.

1. Choose **Create rule**\.

### Send a notification whenever pipeline state changes \(CLI\)<a name="monitoring-cloudwatch-events-console"></a>

These steps show how to use the CLI to create a CloudWatch Events rule to send notifications of changes in CodePipeline\. 

To use the AWS CLI to create a rule, call the put\-rule command, specifying:
+ A name that uniquely identifies the rule you are creating\. This name must be unique across all of the pipelines you create with CodePipeline associated with your AWS account\.
+ The event pattern for the source and detail fields used by the rule\. For more information, see [Amazon CloudWatch Events and Event Patterns](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/CloudWatchEventsandEventPatterns.html)\.

**To create a CloudWatch Events rule with CodePipeline as the event source**

1. Call the put\-rule command to create a rule specifying the event pattern\. \(See the preceding tables for valid states\.\)

   The following sample command uses \-\-event\-pattern to create a rule called `“MyPipelineStateChanges”` that emits the CloudWatch event when a pipeline execution fails for the pipeline named "myPipeline\."

   ```
   aws events put-rule --name "MyPipelineStateChanges" --event-pattern "{\"source\":[\"aws.codepipeline\"],\"detail-type\":[\"CodePipeline Pipeline Execution State Change\"],\"detail\":{\"pipeline\":[\"myPipeline\"],\"state\":[\"FAILED\"]}}"
   ```

1. Call the put\-targets command to add a target to your new rule, as shown in this example for an Amazon SNS topic:

   ```
   aws events put-targets --rule MyPipelineStateChanges --targets Id=1,Arn=arn:aws:sns:us-west-2:11111EXAMPLE:MyNotificationTopic
   ```

1. Add permissions for Amazon CloudWatch Events to use the designated target service to invoke the notification\. For more information, see [Using Resource\-Based Policies for Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/resource-based-policies-cwe.html)\.