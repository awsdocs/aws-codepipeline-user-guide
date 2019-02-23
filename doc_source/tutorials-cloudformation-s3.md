# Example 2: Create an Amazon S3 Pipeline with AWS CloudFormation<a name="tutorials-cloudformation-s3"></a>

You use a sample template file to create your resource stack, which includes your source bucket, artifact store, pipeline, and change\-detection resources, such as your Amazon CloudWatch Events rule\. The pipeline is a two\-stage pipeline with an Amazon S3 source stage and a CodeDeploy deployment stage\.

**Prerequisites:**

You must have created the following resources to use with the AWS CloudFormation sample template:
+ You must have created a CodeDeploy application and deployment group\. You can use the CodeDeploy resources you created in [Tutorial: Create a Simple Pipeline \(CodeCommit Repository\)](tutorials-simple-codecommit.md)\.
+ Download the sample AWS CloudFormation template files for creating a pipeline with an Amazon S3 source: 
  + Download the sample template for your pipeline in [YAML](samples/codepipeline-s3-events-yaml.zip) or [JSON](samples/codepipeline-s3-events-json.zip) format\.
  + Download the sample template for your CloudTrail bucket and trail in [YAML](samples/codepipeline-s3-cloudtrail-yaml.zip) and [JSON](samples/codepipeline-s3-cloudtrail-json.zip)\.
  + Unzip the files and place them on your local computer\.
+ Download the sample application based on the instances you created in your deployment group:
  + If you want to use CodeDeploy to deploy to Amazon Linux instances, use the sample availble from [https://github.com/awslabs/aws-codepipeline-s3-aws-codedeploy_linux](https://github.com/awslabs/aws-codepipeline-s3-aws-codedeploy_linux)\.
  + If you want to use CodeDeploy to deploy to Windows Server instances, use the sample available from : [https://github\.com/awslabs/AWSCodePipeline\-S3\-AWSCodeDeploy\_Windows](https://github.com/awslabs/AWSCodePipeline-S3-AWSCodeDeploy_Windows)\.

  Save the zip file on your local computer\. You upload the zip file after the stack is created\.

**Create your pipeline in AWS CloudFormation**

1. Open the AWS CloudFormation console, and choose **Create Stack**\.

1. In **Choose a template**, choose **Upload a template to Amazon S3**\. Choose **Browse**, and then select the template file from your local computer\. Choose **Next**\.

1. In **Stack name**, enter a name for your pipeline\. Parameters specified by the sample template are displayed\. Enter the following parameters: 

   1. In **ApplicationName**, enter the name of your CodeDeploy application\.

   1. In **BetaFleet**, enter the name of your CodeDeploy deployment group\.

   1. In **SourceObjectKey**, accept the default for the sample application's zip file\. You upload this file to your bucket as soon as the template creates it\.

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

1. In the Amazon S3 console for your bucket, choose **Upload**, and follow the instructions to upload your \.zip file\.

   Your sample application must remain packaged in a \.zip file\.

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