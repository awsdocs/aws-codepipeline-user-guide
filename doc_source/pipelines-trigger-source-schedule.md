# Use Amazon CloudWatch Events to start a pipeline on a schedule<a name="pipelines-trigger-source-schedule"></a>

You can set up a rule in Amazon CloudWatch Events to start a pipeline on a schedule\. 

## Create a CloudWatch Events rule that schedules your pipeline to start \(console\)<a name="pipelines-trigger-source-schedule-console"></a>

**To create a CloudWatch Events rule with a schedule as the event source**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Create rule**, and then under **Event Source**, choose **Schedule**\.

1. Set up the schedule using a fixed rate or expression\. For information, see [Schedule Expression for Rules](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html)\.

1. In **Targets**, choose **CodePipeline**\.

1. Enter the pipeline ARN for the pipeline execution that starts when triggered by this schedule\.
**Note**  
You can find the pipeline ARN under **Settings** in the console\. See [View the pipeline ARN and service role ARN \(console\)](pipelines-view-console.md#pipelines-settings-console)\.

1. Choose one of the following to create or specify an IAM service role that gives Amazon CloudWatch Events permissions to invoke the target associated with your Amazon CloudWatch Events rule \(in this case, the target is CodePipeline\)\. 
   + Choose **Create a new role for this specific resource** to create a service role that grants Amazon CloudWatch Events permissions to start your pipeline executions when triggered\.
   + Choose **Use existing role** to enter a service role that grants Amazon CloudWatch Events permissions to start your pipeline executions when triggered\.

1. Choose **Configure details**\.

1. On the **Configure rule details** page, enter a name and description for the rule, and then choose **State** to enable the rule\.

1. If you're satisfied with the rule, choose **Create rule**\.

## Create a CloudWatch Events rule that schedules your pipeline to start \(CLI\)<a name="pipelines-trigger-source-schedule-cli"></a>

To use the AWS CLI to create a rule, call the put\-rule command, specifying:
+ A name that uniquely identifies the rule you are creating\. This name must be unique across all of the pipelines you create with CodePipeline associated with your AWS account\.
+ The schedule expression for the rule\.

**To create a CloudWatch Events rule with a schedule as the event source**

1. Call the put\-rule command and include the `--name `and `--schedule-expression` parameters\.

   Examples:

   The following sample command uses \-\-schedule\-expression to create a rule called `MyRule2` that filters CloudWatch Events on a schedule\.

   ```
   aws events put-rule --schedule-expression 'cron(15 10 ? * 6L 2002-2005)' --name MyRule2
   ```

1. Grant permissions for Amazon CloudWatch Events to use CodePipeline to invoke the rule\. For more information, see [Using Resource\-Based Policies for Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/resource-based-policies-cwe.html)\.

   1. Use the following sample to create the trust policy to allow Amazon CloudWatch Events to assume the service role\. Name it `trustpolicyforCWE.json`\.

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

   1. Use the following command to create the `Role-for-MyRule` role and attach the trust policy\.

      ```
      aws iam create-role --role-name Role-for-MyRule --assume-role-policy-document file://trustpolicyforCWE.json
      ```

   1. Create the permissions policy JSON as shown in this sample for the pipeline named `MyFirstPipeline`\. Name the permissions policy `permissionspolicyforCWE.json`\.

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

   1. Use the following command to attach the new `CodePipeline-Permissions-Policy-for-CWE` permissions policy to the `Role-for-MyRule` role you created\.

      ```
      aws iam put-role-policy --role-name Role-for-MyRule --policy-name CodePipeline-Permissions-Policy-For-CWE --policy-document file://permissionspolicyforCWE.json
      ```