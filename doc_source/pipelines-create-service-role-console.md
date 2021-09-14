# Create the CodePipeline service role \(console\)<a name="pipelines-create-service-role-console"></a>

When you use the console to create a pipeline, you create the CodePipeline service role with the pipeline creation wizard\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   Choose **Create pipeline** and complete the **Step 1: Choose pipeline settings** page in the pipeline creation wizard\.
**Note**  
After you create a pipeline, you cannot change its name\. For information about other limitations, see [Quotas in AWS CodePipeline](limits.md)\.

1. In **Service role**, choose **New service role** to allow CodePipeline to create a new service role in IAM\.

1. Complete the pipeline creation\. Your pipeline service role is available to view in your list of IAM roles, and you can view the service role ARN associated to a pipeline by running the `get-pipeline` command with the AWS CLI\.