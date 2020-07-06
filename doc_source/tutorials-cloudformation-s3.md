# Example 2: Create an Amazon S3 pipeline with AWS CloudFormation<a name="tutorials-cloudformation-s3"></a>

This walkthrough shows you how to use the AWS CloudFormation console to create infrastructure that includes a pipeline connected to an Amazon S3 source bucket\. In this tutorial, you use the provided sample template file to create your resource stack, which includes your source bucket, artifact store, pipeline, and change\-detection resources, such as your Amazon CloudWatch Events rule and CloudTrail trail\. After you create your resource stack in AWS CloudFormation, you can view your pipeline in the AWS CodePipeline console\. The pipeline is a two\-stage pipeline with an Amazon S3 source stage and a CodeDeploy deployment stage\.

**Prerequisites:**

You must have the following resources to use with the AWS CloudFormation sample template:
+ You must have created the Amazon EC2 instances, where you installed the CodeDeploy agent on the instances\. You must have created a CodeDeploy application and deployment group\. Use the Amazon EC2 and CodeDeploy resources you created in [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md)\.
+ Choose the following links to download the sample AWS CloudFormation template files for creating a pipeline with an Amazon S3 source: 
  + Download the sample template for your pipeline: [YAML](samples/codepipeline-s3-events-yaml.zip) \| [JSON](samples/codepipeline-s3-events-json.zip)
  + Download the sample template for your CloudTrail bucket and trail: [YAML](samples/codepipeline-s3-cloudtrail-yaml.zip) \| [JSON](samples/codepipeline-s3-cloudtrail-json.zip)
  + Unzip the files and place them on your local computer\.
+ Download the sample application from [SampleApp\_Linux\.zip](samples/SampleApp_Linux.zip)\.

  Save the \.zip file on your local computer\. You upload the \.zip file after the stack is created\.

**Create your pipeline in AWS CloudFormation**

1. Open the AWS CloudFormation console, and choose **Create Stack**\.

1. In **Choose a template**, choose **Upload a template to Amazon S3**\. Choose **Browse**, and then select the template file from your local computer\. Choose **Next**\.

1. In **Stack name**, enter a name for your pipeline\. Parameters specified by the sample template are displayed\. Enter the following parameters: 

   1. In **ApplicationName**, enter the name of your CodeDeploy application\. You can replace the `DemoApplication` default name\.

   1. In **BetaFleet**, enter the name of your CodeDeploy deployment group\. You can replace the `DemoFleet` default name\.

   1. In **SourceObjectKey**, enter `SampleApp_Linux.zip`\. You upload this file to your bucket after the template creates the bucket and pipeline\.

1. Choose **Next**\. Accept the defaults on the following page, and then choose **Next**\.

1. In **Capabilities**, select **I acknowledge that AWS CloudFormation might create IAM resources**, and then choose **Create**\.

1. After your stack creation is complete, view the event list to check for any errors\.

   **Troubleshooting**

   The IAM user who is creating the pipeline in AWS CloudFormation might require additional permissions to create resources for the pipeline\. The following permissions are required in the IAM user's policy to allow AWS CloudFormation to create the required Amazon CloudWatch Events resources for the Amazon S3 pipeline:

   ```
   {
        "Effect": "Allow",
        "Action": [
           "events:PutRule",
           "events:PutEvents",
           "events:PutTargets",
           "events:DeleteRule",
           "events:RemoveTargets",
           "events:DescribeRule"
        ],
        "Resource": "*"
   }
   ```

1. In AWS CloudFormation, in the **Resources** tab for your stack, view the resources that were created for your stack\. 

   Choose the S3 bucket with a `sourcebucket` label in the name, such as `s3-cfn-codepipeline-sourcebucket-y04EXAMPLE.` Do not choose the pipeline artifact bucket\.

   The source bucket is empty because the resource is newly created by AWS CloudFormation\. Open the Amazon S3 console and locate your `sourcebucket` bucket\. Choose **Upload**, and follow the instructions to upload your `SampleApp_Linux.zip` \.zip file\.
**Note**  
When Amazon S3 is the source provider for your pipeline, you must upload to your bucket all source files packaged as a single \.zip file\. Otherwise, the source action fails\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   Under **Pipelines**, choose your pipeline, and then choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. Complete the steps in the following procedure to create your AWS CloudTrail resources\.

**Create your AWS CloudTrail resources in AWS CloudFormation**

1. Open the AWS CloudFormation console, and choose **Create Stack**\.

1. In **Choose a template**, choose **Upload a template to Amazon S3**\. Choose **Browse**, and then select the template file for the AWS CloudTrail resources from your local computer\. Choose **Next**\.

1. In **Stack name**, enter a name for your resource stack\. Parameters specified by the sample template are displayed\. Enter the following parameters: 

   1. In **SourceObjectKey**, accept the default for the sample application's zip file\.

1. Choose **Next**\. Accept the defaults on the following page, and then choose **Next**\.

1. In **Capabilities**, select **I acknowledge that AWS CloudFormation might create IAM resources**, and then choose **Create**\.

1. After your stack creation is complete, view the event list to check for any errors\.

   The following permissions are required in the IAM user's policy to allow AWS CloudFormation to create the required CloudTrail resources for the Amazon S3 pipeline:

   ```
   {
        "Effect": "Allow",
        "Action": [
           "cloudtrail:CreateTrail",
           "cloudtrail:DeleteTrail",
           "cloudtrail:StartLogging",
           "cloudtrail:StopLogging",
           "cloudtrail:PutEventSelectors"
        ],
        "Resource": "*"
   }
   ```

1. Sign in to the AWS Management Console and open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   Under **Pipelines**, choose your pipeline, and then choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. In your source bucket, commit and push a change\. Your change\-detection resources pick up the change and your pipeline starts\.