# Example 3: Create a GitHub Pipeline with AWS CloudFormation<a name="tutorials-cloudformation-github"></a>

This walkthrough shows you how to use the AWS CloudFormation console to create infrastructure that includes a pipeline connected to a GitHub source repository\. In this tutorial, you use the provided sample template file to create your resource stack, which includes your artifact store, pipeline, and change\-detection resource \(your webhook\)\. After you create your resource stack in AWS CloudFormation, you can view your pipeline in the AWS CodePipeline console\. The pipeline is a two\-stage pipeline with a GitHub source stage and a CodeDeploy deployment stage\.

**Prerequisites:**

You must have created the following resources to use with the AWS CloudFormation sample template:
+ A CodeDeploy application and deployment group\. You can use the CodeDeploy resources you created in [Tutorial: Create a Simple Pipeline \(CodeCommit Repository\)](tutorials-simple-codecommit.md)\.
+ Download the sample AWS CloudFormation template file for creating a pipeline\. You can download the sample template in [YAML](samples/codepipeline-github-events-yaml.zip) or [JSON](samples/codepipeline-github-events-json.zip) format\. Unzip the file and place it on your local computer\.
+ Download the [SampleApp\_Linux\.zip](samples/SampleApp_Linux.zip)\.
+ The GitHub repository and branch you want to use for your source\.
+ A personal access key for your GitHub repository\. This is used to provide an OAuth token for connection to your repository\.

1. Unzip the files from [SampleApp\_Linux\.zip](https://s3.amazonaws.com/aws-codedeploy-us-east-1/samples/latest/SampleApp_Linux.zip) and upload the files to your GitHub repository\. You must upload the unzipped files to the root directory of your repository\.

1. Open the AWS CloudFormation console and choose **Create Stack**\.

1. In **Choose a template**, choose **Upload a template to Amazon S3**\. Choose **Browse**, and then select the template file from your local computer\. Choose **Next**\.

1. In **Stack name**, enter a name for your pipeline\. Parameters specified by the sample template are displayed\. Enter the following parameters: 

   1. In **ApplicationName**, enter the name of your CodeDeploy application\.

   1. In **BetaFleet**, enter the name of your CodeDeploy deployment group\.

   1. In **BranchName**, enter the repository branch you want to use\.

   1. In **GitHubOAuthToken**, enter the personal access key for your GitHub repository\.

   1. In **GitHubOwner**, enter the GitHub user name for the owner of the repository\.

   1. In **GitHubSecret**, enter the secret you want to use for the webhook AWS CloudFormation creates\.

   1. In **RepositoryName**, enter the name of your GitHub source repository\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/create-stack-github-pipeline.png)

1. Choose **Next**\. Accept the defaults on the following page, and then choose **Next**\.

1. In **Capabilities**, select **I acknowledge that AWS CloudFormation might create IAM resources**, and then choose **Create**\.

1. After your stack creation is complete, view the event list to check for any errors\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   Under **Pipelines**, choose your pipeline, and then choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. In your source repository, commit and push a change\. Your change\-detection resources pick up the change and your pipeline starts\.