# Create a CloudWatch Events Rule That Starts Your AWS CodeCommit Pipeline \(Console\)<a name="pipelines-trigger-source-repo-changes-console"></a>

**To create a CloudWatch Events rule for use in AWS CodePipeline operations**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Create rule**, and then under **Event source**, choose **CodeCommit** from the **Service Name** drop\-down list\.

1. The service name specifies the service that owns the event resource\. For example, choose AWS CodeCommit to trigger a pipeline when there are changes to the AWS CodeCommit repository associated with a pipeline\.

1. From the **Event Type** drop\-down list, choose **CodeCommit Repository State Change**\.  
![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/test-repoevent-cptarget.png)![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. To make a rule that applies to all repositories, choose **Any resource**\.

   To make a rule that applies to one or more repositories, choose **Specific resource\(s\) by ARN**, and then enter the ARN\.
**Note**  
You can find an AWS CodeCommit repository ARN on the **Settings** page in the AWS CodeCommit console\.

   To specify the branch to associate with the repository, choose **Edit**, and type the resource type branch and the branch name\. Use the event pattern options for `detail`\. The preceding example shows the detail options for an AWS CodeCommit repository branch named "master\." Choose **Save**\.

   In the **Event Pattern Preview** pane, view the rule\.

1. In the **Targets** area, choose **CodePipeline**\.

1. Enter the pipeline ARN for the pipeline to be started when triggered by this rule\.
**Note**  
You can find the pipeline ARN in the metadata output after you run the `get-pipeline` command\. The pipeline ARN is constructed in this format:   
arn:aws:codepipeline:*region*:*account*:*pipeline\-name*  
Sample pipeline ARN:  
*arn:aws:codepipeline:us\-east\-2:80398EXAMPLE:MyFirstPipeline *

1. Create or specify an IAM service role that grants Amazon CloudWatch Events permissions to invoke the target associated with your Amazon CloudWatch Events rule \(in this case, the target is AWS CodePipeline\)\. 
   + Choose **Create a new role for this specific resource** to create a service role that gives Amazon CloudWatch Events permissions to your start your pipeline executions when triggered\.
   + Choose **Use existing role** to enter a service role that gives Amazon CloudWatch Events permissions to your start your pipeline executions when triggered\.

1. Review your rule setup to make sure it meets your requirements\.

1. Choose **Configure details**\.

1. On the **Configure rule details** page, type a name and description for the rule, and then choose **State** to enable the rule\.

1. If you're satisfied with the rule, choose **Create rule**\.