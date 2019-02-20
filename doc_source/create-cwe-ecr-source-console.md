# Create a CloudWatch Events Rule for an Amazon ECR Source \(Console\)<a name="create-cwe-ecr-source-console"></a>

**To create a CloudWatch Events rule for use in CodePipeline operations \(Amazon ECR source\)**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Create rule**, and then under **Event source**, from **Service Name**, choose **EC2 Container Registry**\.

1. In **Event Source**, choose **Event Pattern**\.

   Choose **Edit**, and then paste the following event pattern in the **Event Source** window for a `my-image-repo` repository with a tagged image version of `latest`:

   ```
   {
     "source": [
       "aws.ecr"
     ],
     "detail": {
       "eventName": [
         "PutImage"
       ],
       "requestParameters": {
         "repositoryName": [
           "my-image-repo"
         ],
         "imageTag": [
           "latest"
         ]
       }
     }
   }
   ```  
![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/ecr-repoevent-cptarget.png)![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Create repository state change rule\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

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