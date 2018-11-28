--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Create a CloudWatch Events Rule That Schedules Your Pipeline to Start \(Console\)<a name="pipelines-trigger-source-schedule-console"></a>

**To create a CloudWatch Events rule with a schedule as the event source**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Create rule**, and then under **Event Source**, choose **Schedule**\.  
![\[S3 source state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/test-schedule-cptarget.png)![\[S3 source state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[S3 source state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Set up the schedule using a fixed rate or expression\. For information, see [Schedule Expression for Rules](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html)\.

1. In **Targets**, choose **CodePipeline**\.

1. Enter the pipeline ARN for the pipeline execution that starts when triggered by this schedule\.
**Note**  
You can find the pipeline ARN in the metadata output after you run the get\-pipeline command\.

1. Choose one of the following to create or specify an IAM service role that gives Amazon CloudWatch Events permissions to invoke the target associated with your Amazon CloudWatch Events rule \(in this case, the target is AWS CodePipeline\)\. 
   + Choose **Create a new role for this specific resource** to create a service role that grants Amazon CloudWatch Events permissions to your start your pipeline executions when triggered\.
   + Choose **Use existing role** to enter a service role that grants Amazon CloudWatch Events permissions to your start your pipeline executions when triggered\.

1. Choose **Configure details**\.

1. On the **Configure rule details** page, enter a name and description for the rule, and then choose **State** to enable the rule\.

1. If you're satisfied with the rule, choose **Create rule**\.