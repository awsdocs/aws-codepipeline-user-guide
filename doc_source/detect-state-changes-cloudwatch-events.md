# Monitoring CodePipeline events<a name="detect-state-changes-cloudwatch-events"></a>



You can monitor CodePipeline events in EventBridge, which delivers a stream of real\-time data from your own applications, software\-as\-a\-service \(SaaS\) applications, and AWS services\. EventBridge routes that data to targets such as AWS Lambda and Amazon Simple Notification Service\. These events are the same as those that appear in Amazon CloudWatch Events, which delivers a near real\-time stream of system events that describe changes in AWS resources\. For more information, see [What Is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/) in the *Amazon EventBridge User Guide*\.

**Note**  
Amazon EventBridge is the preferred way to manage your events\. Amazon CloudWatch Events and EventBridge are the same underlying service and API, but EventBridge provides more features\. Changes you make in either CloudWatch Events or EventBridge will appear in each console\.

Events are composed of rules\. A rule is configured by choosing the following:
+ **Event Pattern\.** Each rule is expressed as an event pattern with the source and type of events to monitor, and event targets\. To monitor events, you create a rule with the service you are monitoring as the event source, such as CodePipeline\. For example, you can create a rule with an event pattern that that uses CodePipeline as an event source to trigger the rule when there are changes in the state of a pipeline, stage, or action\.
+ **Targets\.** The new rule receives a selected service as the event target\. You might want to set up a target service to send notifications, capture state information, take corrective action, initiate events, or take other actions\. When you add your target, you must also grant permissions to Amazon CloudWatch Events to allow it to invoke the selected target service\.

Each type of execution state change event emits notifications with specific message content, where:
+ The initial `version` entry shows the version number for the event\.
+ The `version` entry under pipeline `detail` shows the pipeline structure version number\.
+ The `execution-id` entry under pipeline `detail` shows the execution ID for the pipeline execution that caused the state change\. Refer to the GetPipelineExecution API call in the [AWS CodePipeline API Reference](https://docs.aws.amazon.com/codepipeline/latest/APIReference/)\.

CodePipeline reports an event to Amazon CloudWatch Events whenever the state of a resource in your AWS account changes\. Events are emitted on a guaranteed, at\-least\-once basis for the following resources:
+ Pipeline executions
+ Stage executions
+ Action executions

The following examples show events for CodePipeline\.

**Topics**
+ [Detail types](#detect-state-events-types)
+ [Pipeline\-level events](#detect-state-events-pipeline)
+ [Stage\-level events](#detect-state-events-stage)
+ [Action\-level events](#detect-state-events-action)
+ [Create a Rule That Sends a Notification on a Pipeline Event](#create-cloudwatch-notifications)

## Detail types<a name="detect-state-events-types"></a>

When you set up events to monitor, you can choose the detail type for the event\.

You can configure notifications to be sent when the state changes for:
+ Specified pipelines or all your pipelines\. You control this by using `"detail-type":` `"CodePipeline Pipeline Execution State Change"`\.
+ Specified stages or all your stages, within a specified pipeline or all your pipelines\. You control this by using `"detail-type":` `"CodePipeline Stage Execution State Change"`\.
+ Specified actions or all actions, within a specified stage or all stages, within a specified pipeline or all your pipelines\. You control this by using `"detail-type":` `"CodePipeline Action Execution State Change"`\.


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/detect-state-changes-cloudwatch-events.html)

## Pipeline\-level events<a name="detect-state-events-pipeline"></a>

Pipeline\-level events are emitted when there is a state change for a pipeline execution\.

**Topics**
+ [Pipeline STARTED event](#detect-state-events-pipeline-started)
+ [Pipeline STOPPING event](#detect-state-events-pipeline-stopping)
+ [Pipeline SUCCEEDED event](#detect-state-events-pipeline-succeeded)
+ [Pipeline FAILED event](#detect-state-events-pipeline-failed)

### Pipeline STARTED event<a name="detect-state-events-pipeline-started"></a>

 When a pipeline execution starts, it emits an event that sends notifications with the following content\. This example is for the pipeline named `"myPipeline"` in the `us-east-1` Region\. The `id` field represents the event ID, and the `account` field represents the account ID where the pipeline is created\.

```
{
    "version": "0",
    "id": "01234567-EXAMPLE",
    "detailType": "CodePipeline Pipeline Execution State Change",
    "source": "aws.codepipeline",
    "account": "123456789012",
    "time": "2020-01-24T22:03:07Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:codepipeline:us-east-1:123456789012:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "execution-id": "12345678-1234-5678-abcd-12345678abcd",
        "execution-trigger": {
            "trigger-type": "StartPipelineExecution",
            "trigger-detail": "arn:aws:sts::123456789012:assumed-role/Admin/my-user"
        },
        "state": "STARTED",
        "version": 1
    }
}
```

### Pipeline STOPPING event<a name="detect-state-events-pipeline-stopping"></a>

When a pipeline execution is stopping, it emits an event that sends notifications with the following content\. This example is for the pipeline named `myPipeline` in the `us-east-1` Region\.

```
{
    "version": "0",
    "id": "01234567-EXAMPLE",
    "detailType": "CodePipeline Pipeline Execution State Change",
    "source": "aws.codepipeline",
    "account": "123456789012",
    "time": "2020-01-24T22:02:20Z",
    "region": "us-west-2",
    "resources": [
        "arn:aws:codepipeline:us-west-2:123456789012:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "execution-id": "12345678-1234-5678-abcd-12345678abcd",
        "state": "STOPPING",
        "version": 3,
        "stop-execution-comments": "Stopping the pipeline for an update"
    }
}
```

### Pipeline SUCCEEDED event<a name="detect-state-events-pipeline-succeeded"></a>

 When a pipeline execution succeeds, it emits an event that sends notifications with the following content\. This example is for the pipeline named `myPipeline` in the `us-west-2` Region\.

```
{
    "version": "0",
    "id": "01234567-EXAMPLE",
    "detailType": "CodePipeline Pipeline Execution State Change",
    "source": "aws.codepipeline",
    "account": "123456789012",
    "time": "2020-01-24T22:03:44Z",
    "region": "us-west-2",
    "resources": [
        "arn:aws:codepipeline:us-west-2:123456789012:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "execution-id": "12345678-1234-5678-abcd-12345678abcd",
        "state": "SUCCEEDED",
        "version": 3
    }
}
```

### Pipeline FAILED event<a name="detect-state-events-pipeline-failed"></a>

When a pipeline execution fails, it emits an event that sends notifications with the following content\. This example is for the pipeline named `"myPipeline"` in the `us-west-2` Region\.

```
{
    "version": "0",
    "id": "01234567-EXAMPLE",
    "detailType": "CodePipeline Pipeline Execution State Change",
    "source": "aws.codepipeline",
    "account": "123456789012",
    "time": "2020-01-31T18:55:43Z",
    "region": "us-west-2",
    "resources": [
        "arn:aws:codepipeline:us-west-2:123456789012:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "execution-id": "12345678-1234-5678-abcd-12345678abcd",
        "state": "FAILED",
        "version": 4
    }
}
```

## Stage\-level events<a name="detect-state-events-stage"></a>

Stage\-level events are emitted when there is a state change for a stage execution\.

**Topics**
+ [Stage STARTED event](#detect-state-events-stage-started)
+ [Stage STOPPING event](#detect-state-events-stage-stopping)
+ [Stage STOPPED event](#detect-state-events-stage-stopped)

### Stage STARTED event<a name="detect-state-events-stage-started"></a>

When a stage execution starts, it emits an event that sends notifications with the following content\. This example is for the pipeline named `"myPipeline"` in the `us-east-1` Region, for the stage `Prod`\.

```
{
    "version": "0",
    "id": 01234567-EXAMPLE,
    "detailType": "CodePipeline Stage Execution State Change",
    "source": "aws.codepipeline",
    "account": 123456789012,
    "time": "2020-01-24T22:03:07Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:codepipeline:us-east-1:123456789012:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "version": "1",
        "execution-id": 12345678-1234-5678-abcd-12345678abcd,
        "stage": "Prod",
        "state": "STARTED"
    }
}
```

### Stage STOPPING event<a name="detect-state-events-stage-stopping"></a>

When a stage execution is stopping, it emits an event that sends notifications with the following content\. This example is for the pipeline named `myPipeline` in the `us-west-2` Region, for the stage `Deploy`\.

```
{
    "version": "0",
    "id": "01234567-EXAMPLE",
    "detailType": "CodePipeline Stage Execution State Change",
    "source": "aws.codepipeline",
    "account": "123456789012",
    "time": "2020-01-24T22:02:20Z",
    "region": "us-west-2",
    "resources": [
        "arn:aws:codepipeline:us-west-2:123456789012:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "execution-id": "12345678-1234-5678-abcd-12345678abcd",
        "stage": "Deploy",
        "state": "STOPPING",
        "version": 3
    }
}
```

### Stage STOPPED event<a name="detect-state-events-stage-stopped"></a>

When a stage execution is stopped, it emits an event that sends notifications with the following content\. This example is for the pipeline named `myPipeline` in the `us-west-2` Region, for the stage `Deploy`\.

```
{
    "version": "0",
    "id": "01234567-EXAMPLE",
    "detailType": "CodePipeline Stage Execution State Change",
    "source": "aws.codepipeline",
    "account": "123456789012",
    "time": "2020-01-31T18:21:39Z",
    "region": "us-west-2",
    "resources": [
        "arn:aws:codepipeline:us-west-2:123456789012:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "execution-id": "12345678-1234-5678-abcd-12345678abcd",
        "stage": "Deploy",
        "state": "STOPPED",
        "version": 3
    }
}
```

## Action\-level events<a name="detect-state-events-action"></a>

Action\-level events are emitted when there is a state change for an action execution\.

**Topics**
+ [Action STARTED event](#detect-state-events-action-started)
+ [Action SUCCEEDED event](#detect-state-events-action-succeeded)
+ [Action FAILED event](#detect-state-events-action-failed)
+ [Action ABANDONED event](#detect-state-events-action-abandoned)

### Action STARTED event<a name="detect-state-events-action-started"></a>

When an action execution starts, it emits an event that sends notifications with the following content\. This example is for the pipeline named `myPipeline` in the `us-east-1` Region, for the deployment action `myAction`\.

```
{
    "version": "0",
    "id": 01234567-EXAMPLE,
    "detailType": "CodePipeline Action Execution State Change",
    "source": "aws.codepipeline",
    "account": 123456789012,
    "time": "2020-01-24T22:03:07Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:codepipeline:us-east-1:123456789012:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "execution-id": 12345678-1234-5678-abcd-12345678abcd,
        "stage": "Prod",
        "action": "myAction",
        "state": "STARTED",
        "type": {
            "owner": "AWS",
            "category": "Deploy",
            "provider": "CodeDeploy",
            "version": 1
        },
        "version": "1",
    }
}
```

### Action SUCCEEDED event<a name="detect-state-events-action-succeeded"></a>

When an action execution succeeds, it emits an event that sends notifications with the following content\. This example is for the pipeline named `"myPipeline"` in the `us-west-2` Region, for the source action `"Source"`\. For this event type, there are two different `region` fields\. The event `region` field specifies the Region for the pipeline event\. The `region` field under the `detail` section specifies the Region for the action\.

```
{
    "version": "0",
    "id": "01234567-EXAMPLE",
    "detailType": "CodePipeline Action Execution State Change",
    "source": "aws.codepipeline",
    "account": "123456789012",
    "time": "2020-01-24T22:03:11Z",
    "region": "us-west-2",
    "resources": [
        "arn:aws:codepipeline:us-west-2:123456789012:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "execution-id": "12345678-1234-5678-abcd-12345678abcd",
        "stage": "Source",
        "action": "Source",
        "state": "SUCCEEDED",
        "region": "us-west-2",
        "type": {
            "owner": "AWS",
            "provider": "CodeCommit",
            "category": "Source",
            "version": "1"
        },
        "version": 3
    }
}
```

### Action FAILED event<a name="detect-state-events-action-failed"></a>

When an action execution fails, it emits an event that sends notifications with the following content\. This example is for the pipeline named `"myPipeline"` in the `us-west-2` Region, for the action `"Deploy"`\.

```
{
    "version": "0",
    "id": "01234567-EXAMPLE",
    "detailType": "CodePipeline Action Execution State Change",
    "source": "aws.codepipeline",
    "account": "123456789012",
    "time": "2020-01-31T18:55:43Z",
    "region": "us-west-2",
    "resources": [
        "arn:aws:codepipeline:us-west-2:123456789012:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "execution-id": "12345678-1234-5678-abcd-12345678abcd",
        "stage": "Deploy",
        "action": "Deploy",
        "state": "FAILED",
        "region": "us-west-2",
        "type": {
            "owner": "AWS",
            "provider": "CodeDeploy",
            "category": "Deploy",
            "version": "1"
        },
        "version": 4
    }
}
```

### Action ABANDONED event<a name="detect-state-events-action-abandoned"></a>

When an action execution is abandoned, it emits an event that sends notifications with the following content\. This example is for the pipeline named `"myPipeline"` in the `us-west-2` Region, for the action `"Deploy"`\.

```
{
    "version": "0",
    "id": "01234567-EXAMPLE",
    "detailType": "CodePipeline Action Execution State Change",
    "source": "aws.codepipeline",
    "account": "123456789012",
    "time": "2020-01-31T18:21:39Z",
    "region": "us-west-2",
    "resources": [
        "arn:aws:codepipeline:us-west-2:123456789012:myPipeline"
    ],
    "detail": {
        "pipeline": "myPipeline",
        "execution-id": "12345678-1234-5678-abcd-12345678abcd",
        "stage": "Deploy",
        "action": "Deploy",
        "state": "ABANDONED",
        "region": "us-west-2",
        "type": {
            "owner": "AWS",
            "provider": "CodeDeploy",
            "category": "Deploy",
            "version": "1"
        },
        "version": 3
    }
}
```

## Create a Rule That Sends a Notification on a Pipeline Event<a name="create-cloudwatch-notifications"></a>

A rule watches for certain events and then routes them to AWS targets that you choose\. You can create a rule that performs an AWS action automatically when another AWS action happens, or a rule that performs an AWS action regularly on a set schedule\.

**Topics**
+ [Send a Notification When Pipeline State Changes \(Console\)](#monitoring-cloudwatch-events-console)
+ [Send a Notification When Pipeline State Changes \(CLI\)](#monitoring-cloudwatch-events-cli)

### Send a Notification When Pipeline State Changes \(Console\)<a name="monitoring-cloudwatch-events-console"></a>

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

### Send a Notification When Pipeline State Changes \(CLI\)<a name="monitoring-cloudwatch-events-cli"></a>

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

1. Call the put\-targets command and include the following parameters:
   + The `--rule` parameter is used with the `rule_name` you created by using put\-rule\. 
   + The `--targets` parameter is used with the list `Id` of the target in the list of targets and the `ARN` of the Amazon SNS topic\.

   The following sample command specifies that for the rule called `MyPipelineStateChanges`, the target `Id` is composed of the number one, indicating that in a list of targets for the rule, this is target 1\. The sample command also specifies an example `ARN` for the Amazon SNS topic\.

   ```
   aws events put-targets --rule MyPipelineStateChanges --targets Id=1,Arn=arn:aws:sns:us-west-2:11111EXAMPLE:MyNotificationTopic
   ```

1. Add permissions for Amazon CloudWatch Events to use the designated target service to invoke the notification\. For more information, see [Using Resource\-Based Policies for Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/resource-based-policies-cwe.html)\.
