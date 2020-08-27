# Create a CloudWatch Events rule for a CodeCommit source \(console\)<a name="pipelines-trigger-source-repo-changes-console"></a>

**Important**  
If you use the console to create or edit your pipeline, your CloudWatch Events rule is created for you\.

**To create a CloudWatch Events rule for use in CodePipeline operations**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Create rule**, and then under **Event source**, from **Service Name**, choose **CodeCommit**\.

   The service name that you choose owns the event resource\. For example, choose CodeCommit to trigger a pipeline when there are changes to the CodeCommit repository associated with a pipeline\.

1. From **Event Type**, choose **CodeCommit Repository State Change**\.  
![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/test-repoevent-cptarget.png)![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. To make a rule that applies to all repositories, choose **Any resource**\.

   To make a rule that applies to one or more repositories, choose **Specific resource\(s\) by ARN**, and then enter the ARN\.
**Note**  
You can find the ARN for a CodeCommit repository on the **Settings** page in the CodeCommit console\.

   To specify the branch to associate with the repository, choose **Edit**, and enter the resource type branch and branch name\. Use the event pattern options for `detail`\. The preceding example shows the detail options for a CodeCommit repository branch named `master`\.

   The following is a sample CodeCommit event pattern in the **Event** window for a `MyTestRepo` repository with a branch named `master`:

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

   Choose **Save**\.

   In the **Event Pattern Preview** pane, view the rule\.

1. In **Targets**, choose **CodePipeline**\.

1. Enter the pipeline ARN for the pipeline to be started when triggered by this rule\.
**Note**  
You can find the pipeline ARN under **Settings** in the console\. See [View the pipeline ARN and service role ARN \(console\)](pipelines-view-console.md#pipelines-settings-console)\.  
The pipeline ARN is constructed in this format:   
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