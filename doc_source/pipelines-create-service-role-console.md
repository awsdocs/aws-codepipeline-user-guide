# Create the CodePipeline service role \(console\)<a name="pipelines-create-service-role-console"></a>

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   Choose **Create pipeline** and complete the **Step 1: Choose pipeline settings** page in the pipeline creation wizard\.
**Note**  
After you create a pipeline, you cannot change its name\. For information about other limitations, see [Quotas in AWS CodePipeline](limits.md)\.

1. In **Service role**, do one of the following:
   + Choose **New service role** to allow CodePipeline to create a new service role in IAM\. In **Role name**, the role and policy name both default to this format: AWSCodePipelineServiceRole\-*region*\-*pipeline\_name*\. For example, this is the service role created for this tutorial: AWSCodePipelineServiceRole\-eu\-west\-2\-MyFirstPipeline\.
   + Choose **Existing service role** to use a service role already created in IAM\. In **Role name**, choose your service role from the list\.
**Note**  
In the console, service roles created before September 2018 are created with the name "oneClick\_AWS\-CodePipeline\-Service\_*ID\-Number*"\.  
Service roles created after September 2018 use the service role name format "AWSCodePipelineServiceRole\-*Region*\-*Pipeline\_Name*"\. For example, for a pipeline named MyFirstPipeline created in the console in eu\-west\-2, the service role named "AWSCodePipelineServiceRole\-eu\-west\-2\-MyFirstPipeline" is created\. The policy name format is the same as the role name format\.

1. Complete the pipeline creation\. Your pipeline service role is available to view in your list of IAM roles, and you can view the service role ARN associated to a pipeline by running the `get-pipeline` command with the AWS CLI\.