# Create a CloudWatch Events rule for an Amazon ECR source \(console\)<a name="create-cwe-ecr-source-console"></a>

**To create a CloudWatch Events rule for use in CodePipeline operations \(Amazon ECR source\)**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Create rule**, and then under **Event source**, from **Service Name**, choose **EC2 Container Registry**\.

1. In **Event Source**, choose **Event Pattern**\.

   Choose **Edit**, and then paste the following example event pattern in the **Event Source** window for a `cwe-test` repository with an image tag of `cli-testing`:

   ```
   {
       "detail-type": [
           "ECR Image Action"
       ],
       "source": [
           "aws.ecr"
       ],
       "detail": {
           "action-type": [
               "PUSH"
           ],
           "image-tag": [
               "latest"
           ],
           "repository-name": [
               "cwe-test"
           ],
           "result": [
               "SUCCESS"
           ]
       }
   }
   ```
**Note**  
To view the full event pattern supported for Amazon ECR events, see [Amazon ECR Events and EventBridge](https://docs.aws.amazon.com/AmazonECR/latest/userguide/ecr-eventbridge.html) or [Amazon Elastic Container Registry Events](https://docs.aws.amazon.com/eventbridge/latest/userguide/event-types.html#ecr-event-types)\.

1. Choose **Save**\.

   In the **Event Pattern Preview** pane, view the rule\.

1. In **Targets**, choose **CodePipeline**\.

1. Enter the pipeline ARN for the pipeline to be started when triggered by this rule\.
**Note**  
You can find the pipeline ARN in the metadata output after you run the get\-pipeline command\. The pipeline ARN is constructed in this format:   
arn:aws:codepipeline:*region*:*account*:*pipeline\-name*  
Sample pipeline ARN:  
`arn:aws:codepipeline:us-east-2:80398EXAMPLE:MyFirstPipeline`

1. Create or specify an IAM service role that grants Amazon CloudWatch Events permissions to invoke the target associated with your Amazon CloudWatch Events rule \(in this case, the target is CodePipeline\)\. 
   + Choose **Create a new role for this specific resource** to create a service role that gives Amazon CloudWatch Events permissions to your start your pipeline executions when triggered\.
   + Choose **Use existing role** to enter a service role that gives Amazon CloudWatch Events permissions to your start your pipeline executions when triggered\.

1. Review your rule setup to make sure it meets your requirements\.

1. Choose **Configure details**\.

1. On the **Configure rule details** page, enter a name and description for the rule, and then choose **State** to enable the rule\.

1. If you're satisfied with the rule, choose **Create rule**\.