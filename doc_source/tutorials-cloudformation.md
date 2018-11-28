--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Tutorial: Create a Pipeline with AWS CloudFormation<a name="tutorials-cloudformation"></a>

The examples use sample templates that allow you to use AWS CloudFormation to create a pipeline that deploys your application to your instances each time the source code changes\. The pipeline detects the arrival of a saved change through Amazon CloudWatch Events\.

## Example 1: Create an AWS CodeCommit Pipeline with AWS CloudFormation<a name="tutorials-cloudformation-codecommit"></a>

You use a sample template file to create your resource stack, which includes your artifact store, pipeline, and change\-detection resources, such as your Amazon CloudWatch Events rule\. The pipeline is a two\-stage pipeline with an AWS CodeCommit source stage and an AWS CodeDeploy deployment stage\.

**Prerequisites:**

You must have created the following resources to use with the AWS CloudFormation sample template:
+ You must have created a source repository\. You can use the AWS CodeCommit repository you created in [Tutorial: Create a Simple Pipeline \(AWS CodeCommit Repository\)](tutorials-simple-codecommit.md)\.
+ You must have created an AWS CodeDeploy application and deployment group\. You can use the AWS CodeDeploy resources you created in [Tutorial: Create a Simple Pipeline \(AWS CodeCommit Repository\)](tutorials-simple-codecommit.md)\.
+ Download the sample AWS CloudFormation template file for creating a pipeline\. You can download the sample template in [YAML](samples/codepipeline-codecommit-events-yaml.zip) or [JSON](samples/codepipeline-codecommit-events-json.zip) format\. Unzip the file and place it on your local computer\.
+ Download the [SampleApp\_Linux\.zip](samples/SampleApp_Linux.zip) sample application file\.

1. Unzip the files from [SampleApp\_Linux\.zip](https://s3.amazonaws.com/aws-codedeploy-us-east-1/samples/latest/SampleApp_Linux.zip) and upload the files into to your AWS CodeCommit repository\. You must upload the unzipped files to the root directory of your repository\. You can follow the instructions in [Step 2: Add Sample Code to Your AWS CodeCommit Repository](tutorials-simple-codecommit.md#codecommit-add-code) to push the files to your repository\.

1. Open the AWS CloudFormation console and choose **Create Stack**\.

1. In **Choose a template**, choose **Upload a template to Amazon S3**\. Choose **Browse** and then select the template file from your local computer\. Choose **Next**\.

1. In **Stack name**, enter a name for your pipeline\. Parameters specified by the sample template are displayed\. Enter the following parameters: 

   1. In **ApplicationName**, enter the name of your AWS CodeDeploy application\.

   1. In **BetaFleet**, enter the name of your AWS CodeDeploy deployment group\.

   1. In **BranchName**, enter the repository branch you want to use\.

   1. In **RepositoryName**, enter the name of your AWS CodeCommit source repository\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/create-stack-codecommit-pipeline.png)

1. Choose **Next**\. Accept the defaults on the following page, and then choose **Next**\.

1. In **Capabilities**, select **I acknowledge that AWS CloudFormation might create IAM resources**, and then choose **Create**\.

1. After your stack creation is complete, view the event list to check for any errors\.

   **Troubleshooting**

   The IAM user who is creating the pipeline in AWS CloudFormation might require additional permissions to create resources for the pipeline\. The following permissions are required in the IAM user's policy to allow AWS CloudFormation to create the required Amazon CloudWatch Events resources for the AWS CodeCommit pipeline:

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

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   Under **Pipelines**, choose your pipeline and choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. In your source repository, commit and push a change\. Your change\-detection resources pick up the change, and your pipeline starts\.

## Example 2: Create an Amazon S3 Pipeline with AWS CloudFormation<a name="tutorials-cloudformation-s3"></a>

You use a sample template file to create your resource stack, which includes your source bucket, artifact store, pipeline, and change\-detection resources, such as your Amazon CloudWatch Events rule\. The pipeline is a two\-stage pipeline with an Amazon S3 source stage and an AWS CodeDeploy deployment stage\.

**Prerequisites:**

You must have created the following resources to use with the AWS CloudFormation sample template:
+ You must have created an AWS CodeDeploy application and deployment group\. You can use the AWS CodeDeploy resources you created in [Tutorial: Create a Simple Pipeline \(AWS CodeCommit Repository\)](tutorials-simple-codecommit.md)\.
+ Download the sample AWS CloudFormation template files for creating a pipeline with an Amazon S3 source: 
  + Download the sample template for your pipeline in [YAML](samples/codepipeline-s3-events-yaml.zip) or [JSON](samples/codepipeline-s3-events-json.zip) format\.
  + Download the sample template for your CloudTrail bucket and trail in [YAML](samples/codepipeline-s3-cloudtrail-yaml.zip) and [JSON](samples/codepipeline-s3-cloudtrail-json.zip)\.
  + Unzip the files and place them on your local computer\.
+ Download the sample application based on the instances you created in your deployment group:
  + If you want to use AWS CodeDeploy to deploy to Amazon Linux instances, use the sample availble from [https://github.com/awslabs/aws-codepipeline-s3-aws-codedeploy_linux](https://github.com/awslabs/aws-codepipeline-s3-aws-codedeploy_linux)\.
  + If you want to use AWS CodeDeploy to deploy to Windows Server instances, use the sample available from : [https://github\.com/awslabs/AWSCodePipeline\-S3\-AWSCodeDeploy\_Windows](https://github.com/awslabs/AWSCodePipeline-S3-AWSCodeDeploy_Windows)\.

  Save the zip file on your local computer\. You upload the zip file after the stack is created\.

**Create your pipeline in AWS CloudFormation**

1. Open the AWS CloudFormation console, and choose **Create Stack**\.

1. In **Choose a template**, choose **Upload a template to Amazon S3**\. Choose **Browse**, and then select the template file from your local computer\. Choose **Next**\.

1. In **Stack name**, enter a name for your pipeline\. Parameters specified by the sample template are displayed\. Enter the following parameters: 

   1. In **ApplicationName**, enter the name of your AWS CodeDeploy application\.

   1. In **BetaFleet**, enter the name of your AWS CodeDeploy deployment group\.

   1. In **SourceObjectKey**, accept the default for the sample application's zip file\. You upload this file to your bucket as soon as the template creates it\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/create-stack-s3-pipeline.png)

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

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   Under **Pipelines**, choose your pipeline, and then choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. Complete the steps in the following procedure to create your AWS CloudTrail resources\.

**Create your AWS CloudTrail resources in AWS CloudFormation**

1. Open the AWS CloudFormation console, and choose **Create Stack**\.

1. In **Choose a template**, choose **Upload a template to Amazon S3**\. Choose **Browse**, and then select the template file for the AWS CloudTrail resources from your local computer\. Choose **Next**\.

1. In **Stack name**, enter a name for your resource stack\. Parameters specified by the sample template are displayed\. Enter the following parameters: 

   1. In **SourceObjectKey**, accept the default for the sample application's zip file\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/create-stack-s3-cloudtrail.png)

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

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   Under **Pipelines**, choose your pipeline, and then choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. In your source bucket, commit and push a change\. Your change\-detection resources pick up the change and your pipeline starts\.

## Example 3: Create a GitHub Pipeline with AWS CloudFormation<a name="tutorials-cloudformation-github"></a>

You use a sample template file to create your resource stack, which includes your artifact store, pipeline, and change\-detection resource \(your webhook\)\. The pipeline is a two\-stage pipeline with a GitHub source stage and an AWS CodeDeploy deployment stage\.

**Prerequisites:**

You must have created the following resources to use with the AWS CloudFormation sample template:
+ An AWS CodeDeploy application and deployment group\. You can use the AWS CodeDeploy resources you created in [Tutorial: Create a Simple Pipeline \(AWS CodeCommit Repository\)](tutorials-simple-codecommit.md)\.
+ Download the sample AWS CloudFormation template file for creating a pipeline\. You can download the sample template in [YAML](samples/codepipeline-github-events-yaml.zip) or [JSON](samples/codepipeline-github-events-json.zip) format\. Unzip the file and place it on your local computer\.
+ Download the [SampleApp\_Linux\.zip](samples/SampleApp_Linux.zip)\.
+ The GitHub repository and branch you want to use for your source\.
+ A personal access key for your GitHub repository\. This is used to provide an OAuth token for connection to your repository\.

1. Unzip the files from [SampleApp\_Linux\.zip](https://s3.amazonaws.com/aws-codedeploy-us-east-1/samples/latest/SampleApp_Linux.zip) and upload the files to your GitHub repository\. You must upload the unzipped files to the root directory of your repository\.

1. Open the AWS CloudFormation console and choose **Create Stack**\.

1. In **Choose a template**, choose **Upload a template to Amazon S3**\. Choose **Browse**, and then select the template file from your local computer\. Choose **Next**\.

1. In **Stack name**, enter a name for your pipeline\. Parameters specified by the sample template are displayed\. Enter the following parameters: 

   1. In **ApplicationName**, enter the name of your AWS CodeDeploy application\.

   1. In **BetaFleet**, enter the name of your AWS CodeDeploy deployment group\.

   1. In **BranchName**, enter the repository branch you want to use\.

   1. In **GitHubOAuthToken**, enter the personal access key for your GitHub repository\.

   1. In **GitHubOwner**, enter the GitHub user name for the owner of the repository\.

   1. In **GitHubSecret**, enter the secret you want to use for the webhook AWS CloudFormation creates\.

   1. In **RepositoryName**, enter the name of your GitHub source repository\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/create-stack-github-pipeline.png)

1. Choose **Next**\. Accept the defaults on the following page, and then choose **Next**\.

1. In **Capabilities**, select **I acknowledge that AWS CloudFormation might create IAM resources**, and then choose **Create**\.

1. After your stack creation is complete, view the event list to check for any errors\.

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   Under **Pipelines**, choose your pipeline, and then choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. In your source repository, commit and push a change\. Your change\-detection resources pick up the change and your pipeline starts\.