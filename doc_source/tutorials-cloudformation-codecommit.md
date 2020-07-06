# Example 1: Create an AWS CodeCommit pipeline with AWS CloudFormation<a name="tutorials-cloudformation-codecommit"></a>

This walkthrough shows you how to use the AWS CloudFormation console to create infrastructure that includes a pipeline connected to a CodeCommit source repository\. In this tutorial, you use the provided sample template file to create your resource stack, which includes your artifact store, pipeline, and change\-detection resources, such as your Amazon CloudWatch Events rule\. After you create your resource stack in AWS CloudFormation, you can view your pipeline in the AWS CodePipeline console\. The pipeline is a two\-stage pipeline with a CodeCommit source stage and a CodeDeploy deployment stage\.

**Prerequisites:**

You must have created the following resources to use with the AWS CloudFormation sample template:
+ You must have created a source repository\. You can use the AWS CodeCommit repository you created in [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md)\.
+ You must have created a CodeDeploy application and deployment group\. You can use the CodeDeploy resources you created in [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md)\.
+ Choose one of these links to download the sample AWS CloudFormation template file for creating a pipeline: [YAML](samples/codepipeline-codecommit-events-yaml.zip) \| [JSON](samples/codepipeline-codecommit-events-json.zip)

  Unzip the file and place it on your local computer\.
+ Download the [SampleApp\_Linux\.zip](samples/SampleApp_Linux.zip) sample application file\.

1. Unzip the files from [SampleApp\_Linux\.zip](samples/SampleApp_Linux.zip) and upload the files to your AWS CodeCommit repository\. You must upload the unzipped files to the root directory of your repository\. You can follow the instructions in [Step 2: Add sample code to your CodeCommit repository](tutorials-simple-codecommit.md#codecommit-add-code) to push the files to your repository\.

1. Open the AWS CloudFormation console and choose **Create Stack**\.

1. In **Choose a template**, choose **Upload a template to Amazon S3**\. Choose **Browse** and then select the template file from your local computer\. Choose **Next**\.

1. In **Stack name**, enter a name for your pipeline\. Parameters specified by the sample template are displayed\. Enter the following parameters: 

   1. In **ApplicationName**, enter the name of your CodeDeploy application\.

   1. In **BetaFleet**, enter the name of your CodeDeploy deployment group\.

   1. In **BranchName**, enter the repository branch you want to use\.

   1. In **RepositoryName**, enter the name of your CodeCommit source repository\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/create-stack-codecommit-pipeline.png)

1. Choose **Next**\. Accept the defaults on the following page, and then choose **Next**\.

1. In **Capabilities**, select **I acknowledge that AWS CloudFormation might create IAM resources**, and then choose **Create**\.

1. After your stack creation is complete, view the event list to check for any errors\.

   **Troubleshooting**

   The IAM user who is creating the pipeline in AWS CloudFormation might require additional permissions to create resources for the pipeline\. The following permissions are required in the IAM user's policy to allow AWS CloudFormation to create the required Amazon CloudWatch Events resources for the CodeCommit pipeline:

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

1. Sign in to the AWS Management Console and open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   Under **Pipelines**, choose your pipeline and choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. In your source repository, commit and push a change\. Your change\-detection resources pick up the change, and your pipeline starts\.