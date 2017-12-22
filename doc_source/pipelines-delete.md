# Delete a Pipeline in AWS CodePipeline<a name="pipelines-delete"></a>

You can always edit a pipeline to change its functionality, but you might decide you want to delete it instead\. You can delete a pipeline by using the AWS CodePipeline console or by using the AWS CLI and delete\-pipeline command\.


+ [Delete a Pipeline \(Console\)](#pipelines-delete-console)
+ [Delete a Pipeline \(CLI\)](#pipelines-delete-cli)

## Delete a Pipeline \(Console\)<a name="pipelines-delete-console"></a>

**To delete a pipeline in the AWS CodePipeline console**

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codepipeline](http://console.aws.amazon.com/codepipeline)\.

1. The names of all pipelines associated with your AWS account are displayed, along with their status\. 

1. In **Name**, choose the name of the pipeline you want to delete\. This opens a detailed view of the pipeline\.

1. On the pipeline details page, choose **Edit**\. This opens the editing page for the pipeline\.

1. On the **Edit** page, choose **Delete**\.

1. Type the name of the pipeline, and then choose **Delete**\.
**Important**  
This action cannot be undone\.

## Delete a Pipeline \(CLI\)<a name="pipelines-delete-cli"></a>

To manually delete a pipeline by using the AWS CLI, use the [delete\-pipeline](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/delete-pipeline.html) command\. 

**Important**  
Deleting a pipeline is irreversible\. There is no confirmation dialog box\. After the command is run, the pipeline is deleted, but none of the resources used in the pipeline are deleted\. This makes it easier to create a new pipeline that uses those resources to automate the release of your software\.

**To delete a pipeline by using the AWS CLI**

1. Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\) and use the AWS CLI to run the delete\-pipeline command, specifying the name of the pipeline you want to delete\. For example, to delete a pipeline named *MyFirstPipeline*: 

   ```
   aws codepipeline delete-pipeline --name MyFirstPipeline
   ```

   This command returns nothing\.

1. Delete any resources no longer needed\.
**Note**  
Deleting a pipeline does not delete the resources used in the pipeline, such as the AWS CodeDeploy or Elastic Beanstalk application you used to deploy your code, or, if you created your pipeline from the AWS CodePipeline console, the Amazon S3 bucket AWS CodePipeline created to store the artifacts of your pipelines\. Make sure that you delete resources that are no longer required so that you will not be charged for them in the future\. For example, when you use the console to create a pipeline for the first time, AWS CodePipeline creates one Amazon S3 bucket to store all artifacts for all of your pipelines\. If you have deleted all of your pipelines, follow the steps in [Deleting a Bucket](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/DeletingaBucket.html)\. 