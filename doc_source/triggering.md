# Start a Pipeline Automatically Using Amazon CloudWatch Events<a name="triggering"></a>

You can use Amazon CloudWatch Events to trigger pipelines to start automatically when Amazon CloudWatch Events rule or schedule criteria are met\.

You can use criteria you specify in an Amazon CloudWatch Events rule to detect and react to changes in the state of the pipeline's defined source\. Then, based on rules you create, CloudWatch Events starts your pipeline automatically when an event you specify occurs in a repository, pipeline, or other resource\.

**Note**  
This section provides instructions to manually set up the Amazon CloudWatch Events rule that you can use to automatically start your pipeline\. If you used the console to save a pipeline, AWS CodePipeline uses Amazon CloudWatch Events to automatically start your pipeline when changes occur\. This is the default behavior for the AWS CodeCommit source type\. When you use the console to create or edit a pipeline, the rule and IAM role are created automatically\.

To create the rule:

1. Create an Amazon CloudWatch Events rule that uses the pipeline's source repository as the event source\.

1. Add AWS CodePipeline as the target\. 

1. Give Amazon CloudWatch Events permissions to start the pipeline\.

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

The following is a sample AWS CodeCommit event pattern in the **Event** window for a "MyTestRepo" repository with a branch named "master":

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


+ [Prerequisites](#pipelines-trigger-prerequisites)
+ [Start a Pipeline Whenever a Source Repository Changes](#pipelines-trigger-source-repo-changes)
+ [Start a Pipeline on a Schedule](#pipelines-trigger-source-schedule)
+ [Migrate to Amazon CloudWatch Events Change Detection for Pipelines with an AWS CodeCommit Repository](#trigger-codecommit-migration-cwe)

## Prerequisites<a name="pipelines-trigger-prerequisites"></a>

Before you create rules for use in your AWS CodePipeline operations, you should do the following:

+ Complete the CloudWatch Events prerequisites\. For information, see [Amazon CloudWatch Events Prerequisites](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/CWE_Prerequisites.html)\.

+ Familiarize yourself with events, rules, and targets in CloudWatch Events\. For more information, see [What Is Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)\.

## Start a Pipeline Whenever a Source Repository Changes<a name="pipelines-trigger-source-repo-changes"></a>

You can set up a rule in CloudWatch Events to start a pipeline as soon as the source repository changes\.

**Note**  
Your pipeline runs only when something changes in the source repository and branch you have defined\.  
Supported services can also be configured as CloudWatch event sources for Lambda functions\. For more information, see [Supported Event Sources](http://docs.aws.amazon.com/lambda/latest/dg/invoking-lambda-function.html) in the AWS Lambda Developer Guide\. 

### Start a Pipeline Whenever a Source Repository Changes \(Console\)<a name="pipelines-trigger-source-repo-changes-console"></a>

**To create a CloudWatch Events rule that targets AWS CodePipeline**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Create rule**, and then under **Event source**, choose **CodeCommit** from the **Service Name** drop\-down list\.

1. The service name specifies the service that owns the event resource\. For example, choose AWS CodeCommit to trigger a pipeline when there are changes to the CodeCommit repository associated with a pipeline\.

1. From the **Event Type** drop\-down list, choose **CodeCommit Repository State Change**\.  
![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/test-repoevent-cptarget.png)![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. To make a rule that applies to all repositories, choose **Any resource**\.

   To make a rule that applies to one or more repositories, choose **Specific resource\(s\) by ARN**, and then enter the ARN\.
**Note**  
You can find a CodeCommit repository ARN on the **Settings** page in the CodeCommit console\.

   To specify the branch to associate with the repository, select Edit above the **Event Pattern Preview** box, and type the resource type branch and the branch name\. Use the event pattern options for `detail`\. This preceding example shows the detail options for an AWS CodeCommit repository branch named "master\." Choose **Save**\.

   In the **Event Pattern Preview** pane, view the rule\.

1. In the **Targets** area, choose **CodePipeline**\.

1. Enter the pipeline ARN for the pipeline that will start when triggered by this rule\.
**Note**  
You can find the pipeline ARN in the metadata output after using the CLI to run the `get-pipeline` command\. The pipeline ARN is constructed in this format:   
arn:aws:codepipeline:*region*:*account*:*pipeline\-name*  
Sample pipeline ARN:  
*arn:aws:codepipeline:us\-east\-2:80398EXAMPLE:MyFirstPipeline *

1. Choose one of the following to create or specify an IAM service role that will give Amazon CloudWatch Events permissions to invoke the target associated with your Amazon CloudWatch Events rule \(in this case, the target is AWS CodePipeline\)\. 

   + Select **Create a new role for this specific resource** to create a service role that gives Amazon CloudWatch Events permissions to your start your pipeline executions when triggered\.

   + Select **Use existing role** to enter a service role that gives Amazon CloudWatch Events permissions to your start your pipeline executions when triggered\.

1. Review your rule setup to make sure it meets your requirements\.

1. Choose **Configure details**\.

1. On the **Configure rule details** page, type a name and description for the rule, and then choose **State** to enable the rule\.

1. If you're satisfied with the rule, choose **Create rule**\.

### Start a Pipeline Whenever a Source Repository Changes \(CLI\)<a name="pipelines-trigger-source-repo-changes-cli"></a>

To use the AWS CLI to create a rule, call the put\-rule command, specifying:

+ A name that uniquely identifies the rule you are creating\. This name must be unique across all of the pipelines you create with AWS CodePipeline associated with your AWS account\.

+ The event pattern for the source and detail fields used by the rule\. For more information, see [Amazon CloudWatch Events and Event Patterns](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/CloudWatchEventsandEventPatterns.html)\.

**To create a CloudWatch Events rule with AWS CodeCommit as the event source and AWS CodePipeline as the target**

1. Call the put\-rule command and include the \-\-name and \-\-event\-pattern parameters\.

   Use the following syntax:

   ```
   aws events put-rule 
   --name "rule_name" 
   --event-pattern "{"source":["aws.service_name"], "detail-type":["event_type"], "resources":"repository_ARN"]}"
   ```

   Examples:

   The following sample command uses \-\-event\-pattern to create a rule called MyCodeCommitRepoRule\.

   ```
   aws events put-rule --name "MyCodeCommitRepoRule" --event-pattern "{\"source\":[\"aws.codecommit\"],\"detail-type\":[\"CodeCommit Repository State Change\"],\"detail\":{\"referenceType\":[\"branch\"],\"referenceName \":[\"master\"]}}"
   ```

1. To add AWS CodePipeline as a target, call the put\-targets command and include the \-\-rule and \-\-Id parameters\.

   Use the following syntax:

   ```
   aws events put-targets 
   --rule rule_name 
   --targets Id,ARN
   ```

   Examples:

   The following sample command specifies that for the rule called MyCodeCommitRepoRule, the target is the ID and ARN for the pipeline that starts when something changes in the repository\.

   ```
   aws events put-targets --rule MyCodeCommitRepoRule --targets Id=1,Arn=arn:aws:codepipeline:us-west-2:80398EXAMPLE:TestPipeline
   ```

1. Add permissions for Amazon CloudWatch Events to use AWS CodePipeline to invoke the rule\. For more information, see [Using Resource\-Based Policies for Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/resource-based-policies-cwe.html)\.

   1. Create the trust policy to allow CloudWatch Events to assume the service role\. Use the following sample trust policy and name it "trustpolicyforCWE\.json\."

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Principal": {
                      "Service": "events.amazonaws.com"
                  },
                  "Action": "sts:AssumeRole"
              }
          ]
      }
      ```

   1. Use the following command to create the "Role\-for\-MyRule" role and attach the trust policy:

      ```
      aws iam create-role --role-name Role-for-MyRule --assume-role-policy-document file://trustpolicyforCWE.json
      ```

   1. Create the permissions policy JSON as shown in this sample for the pipeline named “MyFirstPipeline\.” Name the permissions policy “permissionspolicyforCWE\.json”:

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "codepipeline:StartPipelineExecution"
                  ],
                  "Resource": [
                      "arn:aws:codepipeline:us-west-2:80398EXAMPLE:MyFirstPipeline"
                  ]
              }
          ]
      }
      ```

   1. Use the following command to attach the new “CodePipeline\-Permissions\-Policy\-for\-CWE” permissions policy to the “Role\-for\-MyRule” role you created:

      ```
      aws iam put-role-policy --role-name Role-for-MyRule --policy-name CodePipeline-Permissions-Policy-For-CWE --policy-document file://permissionspolicyforCWE.json
      ```

## Start a Pipeline on a Schedule<a name="pipelines-trigger-source-schedule"></a>

You can set up a rule in Amazon CloudWatch Events to start a pipeline on a schedule\.

### Start a Pipeline on a Schedule \(Console\)<a name="pipelines-trigger-source-schedule-console"></a>

**To create a CloudWatch Events schedule that uses AWS CodePipeline as a target**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Create rule**, and then under **Event Source**, choose **Schedule**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/test-schedule-cptarget.png)

1. Set up the schedule using a fixed rate or expression\. For information, see [Schedule Expression for Rules](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html)\.

1. In the **Targets** area, choose **CodePipeline**\.

1. Enter the pipeline ARN for the pipeline execution that starts when triggered by this schedule\.
**Note**  
You can find the pipeline ARN in the metadata output after using the CLI to run the `get-pipeline` command\.

1. Choose one of the following to create or specify an IAM service role that gives Amazon CloudWatch Events permissions to invoke the target associated with your Amazon CloudWatch Events rule \(in this case, the target is AWS CodePipeline\)\. 

   + Select **Create a new role for this specific resource** to create a service role that gives Amazon CloudWatch Events permissions to your start your pipeline executions when triggered\.

   + Select **Use existing role** to enter a service role that gives Amazon CloudWatch Events permissions to your start your pipeline executions when triggered\.

1. Choose **Configure details**\.

1. On the **Configure rule details** page, type a name and description for the rule, and then choose **State** to enable the rule\.

1. If you're satisfied with the rule, choose **Create rule**\.

### Start a Pipeline on a Schedule \(CLI\)<a name="pipelines-trigger-source-schedule-cli"></a>

To use the AWS CLI to create a rule, call the put\-rule command, specifying:

+ A name that uniquely identifies the rule you are creating\. This name must be unique across all of the pipelines you create with AWS CodePipeline associated with your AWS account\.

+ The schedule expression for the rule\.

**To create a CloudWatch Events rule with a schedule as the event source**

1. Call the put\-rule command and include the \-\-name and \-\-schedule\-expression parameters\.

   Examples:

   The following sample command uses \-\-schedule\-expression to create a rule called MyRule2 that filters CloudWatch Events on a schedule\.

   ```
   aws events put-rule --schedule-expression 'cron(15 10 ? * 6L 2002-2005)' --name MyRule2
   ```

1. Add permissions for Amazon CloudWatch Events to use AWS CodePipeline to invoke the rule\. For more information, see [Using Resource\-Based Policies for Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/resource-based-policies-cwe.html)\.

   1. Create the trust policy to allow CloudWatch Events to assume the service role\. Use the following sample trust policy and name it "trustpolicyforCWE\.json\."

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Principal": {
                      "Service": "events.amazonaws.com"
                  },
                  "Action": "sts:AssumeRole"
              }
          ]
      }
      ```

   1. Use the following command to create the "Role\-for\-MyRule" role and attach the trust policy:

      ```
      aws iam create-role --role-name Role-for-MyRule --assume-role-policy-document file://trustpolicyforCWE.json
      ```

   1. Create the permissions policy JSON as shown in this sample for the pipeline named “MyFirstPipeline\.” Name the permissions policy “permissionspolicyforCWE\.json”:

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "codepipeline:StartPipelineExecution"
                  ],
                  "Resource": [
                      "arn:aws:codepipeline:us-west-2:80398EXAMPLE:MyFirstPipeline"
                  ]
              }
          ]
      }
      ```

   1. Use the following command to attach the new “CodePipeline\-Permissions\-Policy\-for\-CWE” permissions policy to the “Role\-for\-MyRule” role you created:

      ```
      aws iam put-role-policy --role-name Role-for-MyRule --policy-name CodePipeline-Permissions-Policy-For-CWE --policy-document file://permissionspolicyforCWE.json
      ```

## Migrate to Amazon CloudWatch Events Change Detection for Pipelines with an AWS CodeCommit Repository<a name="trigger-codecommit-migration-cwe"></a>

You can now use event\-based Amazon CloudWatch Events rules as the change detection method for your pipelines with a AWS CodeCommit repository\. Amazon CloudWatch Events will detect changes in real time and then start your pipeline\. AWS CodePipeline now creates the Amazon CloudWatch Events event\-based rule when you create or edit a pipeline in the console\.

This section includes migration procedures based on the type of pipeline\.

### Automatic Migration for Existing Pipelines<a name="trigger-codecommit-migration-cwe-console"></a>

Existing pipelines are migrated automatically, as soon as the pipeline is edited in the console\. AWS CodePipeline updates the pipeline and creates the rule when you choose **Update** on the **Edit** action page\.

1. In the AWS CodePipeline console, open your pipeline and choose **Edit**\.

1. On the **Edit** page, choose the pencil icon next to the source action\. 

1. On the **Edit action** page, choose the AWS CodeCommit repository\. AWS CodePipeline automatically moves this source action to using Amazon CloudWatch Events for change detection\.

1. Choose **Update**\.

1. Choose **Save pipeline changes**\.

For more information, see [Edit a Pipeline \(Console\)](pipelines-edit.md#pipelines-edit-console)\.

### Reverting to Previous Detection Option<a name="trigger-codecommit-migration-cwe-revert"></a>

If your pipeline has been migrated to use Amazon CloudWatch Events for change detection, but you want to use the previous detection option, go to the AWS CodePipeline console\. On the **Edit action** page, under **Change detection options**, clear the option to use Amazon CloudWatch Events\.

1. In the AWS CodePipeline console, open your pipeline and choose **Edit**\.

1. On the **Edit** page, choose the pencil icon next to the source action\.

1. On the **Edit action** page, under **Change detection options**, choose the option to check periodically\. \(This option is not recommended\.\)

1. Choose **Update**\.

1. Choose **Save pipeline changes**\.

For more information, see [Edit a Pipeline \(Console\)](pipelines-edit.md#pipelines-edit-console)\.

### Manual Migration for Pipelines Configured Using AWS CloudFormation Templates<a name="trigger-codecommit-migration-cwe-templates"></a>

If you own a pipeline with an AWS CodeCommit repository that you manage using an AWS CloudFormation template, you can modify the template and then follow the procedure to manually create the Amazon CloudWatch Events rule, as described in [Start a Pipeline Automatically Using Amazon CloudWatch Events](#triggering)\.

### Manual Migration for Pipelines Edited in the CLI<a name="trigger-codecommit-migration-cwe-cli"></a>

Existing pipelines are automatically migrated as soon as the pipeline is edited in the console\. Migration is not performed for pipelines edited in the CLI\.

1. Use the CLI to run the update\-pipeline command and set the `PollForSourceChanges` parameter to `false`\.

1. Create the Amazon CloudWatch Events rule as described in [Start a Pipeline Automatically Using Amazon CloudWatch Events](#triggering)\.

For more information, see [Edit a Pipeline \(AWS CLI\)](pipelines-edit.md#pipelines-edit-cli)\.