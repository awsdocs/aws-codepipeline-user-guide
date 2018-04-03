# Start a Pipeline Automatically Using a CloudWatch Events Rule<a name="triggering"></a>

You can use Amazon CloudWatch Events to trigger pipelines to start automatically when rule or schedule criteria are met\. For pipelines with an Amazon S3 or AWS CodeCommit source, an Amazon CloudWatch Events rule detects source changes and then starts your pipeline\. When you use the console to create or change a pipeline, the rule and all associated resources are created for you\. If you create or change an Amazon S3 or AWS CodeCommit pipeline in the CLI or AWS CloudFormation, you must use these steps to create the Amazon CloudWatch Events rule and all associated resources manually\.

In Amazon CloudWatch Events, you create a rule to detect and react to changes in the state of the pipeline's defined source\.

**To create the rule**

1. Create an Amazon CloudWatch Events rule that uses the pipeline's source repository as the event source\.

1. Add AWS CodePipeline as the target\. 

1. Grant Amazon CloudWatch Events permissions to start the pipeline\.

As you build your rule, the **Event Pattern Preview** pane in the console \(or the `--event-pattern` output in the AWS CLI\) displays the event fields, in JSON format\. The following sample AWS CodeCommit event pattern uses this JSON structure:

```
{
  "source": [ "aws.codecommit" ],
  "detail-type": [ "CodeCommit Repository State Change" ],
  "resources": [ "CodeCommitRepo_ARN" ],
  "detail": {
     "event": [
       "referenceCreated",
       "referenceUpdated"],
     "referenceType":["branch"],
     "referenceName": ["branch_name"]
  }
}
```

The following is a sample AWS CodeCommit event pattern in the **Event** window for a "MyTestRepo" repository with a branch named "master:"

```
{
  "source": [
    "aws.codecommit"
  ],
  "detail-type": [
    "CodeCommit Repository State Change"
  ],
  "resources": [
    "arn:aws:codecommit:us-west-2:80398EXAMPLE:MyTestRepo"
  ],
  "detail": {
    "referenceType": [
      "branch"
    ],
    "referenceName": [
      "master"
    ]
  }
}
```

The event pattern uses these fields:
+ `source:` should contain `aws.codecommit` as the event source\. 
+ `detail-type:` displays the available event type \(`CodeCommit Repository State Change`\)\.
+ `resources:` contains the repository ARN\.
+ `detail:` contains the repository branch information `referenceType` and `referenceName`\.

**Topics**
+ [Prerequisites](pipelines-trigger-prerequisites.md)
+ [Create a CloudWatch Events Rule That Starts Your AWS CodeCommit Pipeline](pipelines-trigger-source-repo-changes.md)
+ [Create a CloudWatch Events Rule That Starts Your Amazon S3 Pipeline](create-cloudtrail-S3-source.md)
+ [Create a CloudWatch Events Rule That Schedules Your Pipeline to Start](pipelines-trigger-source-schedule.md)
+ [Configure Your AWS CodeCommit Pipelines to Use Amazon CloudWatch Events for Change Detection](trigger-codecommit-migration-cwe.md)
+ [Configure Your Amazon S3 Pipelines to Use Amazon CloudWatch Events for Change Detection](trigger-S3-migration-cwe.md)