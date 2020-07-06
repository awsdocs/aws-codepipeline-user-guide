# Tutorial: Create a pipeline that uses AWS AppConfig as a deployment provider<a name="tutorials-AppConfig"></a>

In this tutorial, you configure a pipeline that continuously delivers configuration files using AWS AppConfig as the deployment action provider in your deployment stage\.

**Topics**
+ [Prerequisites](#tutorials-AppConfig-prereq)
+ [Step 1: Create your AWS AppConfig resources](#tutorials-AppConfig-application)
+ [Step 2: Upload files to your S3 source bucket](#tutorials-AppConfig-bucket)
+ [Step 3: Create your pipeline](#tutorials-AppConfig-pipeline)
+ [Step 4: Make a change to any source file and verify deployment](#tutorials-AppConfig-verify)

## Prerequisites<a name="tutorials-AppConfig-prereq"></a>

Before you begin, you must complete the following:
+ Create or use an Amazon S3 bucket with versioning enabled\. Follow the instructions in [Step 1: Create an S3 bucket for your application](tutorials-simple-s3.md#s3-create-s3-bucket) to create an S3 bucket\.

## Step 1: Create your AWS AppConfig resources<a name="tutorials-AppConfig-application"></a>

In this section, you create the following resources:
+ An *application* in AWS AppConfig is a logical unit of code that provides capabilities for your customers\.
+ An *environment* in AWS AppConfig is a logical deployment group of AppConfig targets, such as applications in a beta or production environment\.
+ A *configuration profile* is a collection of settings that influence the behavior of your application\. The configuration profile enables AWS AppConfig to access your configuration in its stored location\.
+ \(Optional\) A *deployment strategy* in AWS AppConfig defines the behavior of a configuration deployment, such as what percentage of clients should receive the new deployed config at any given time during a deployment\.

**To create an application, environment, configuration profile, and deployment strategy**

1. Sign in to the AWS Management Console\.

1. Use the steps in the following topics to create your resources in AWS AppConfig\.
   + [Create an application](https://docs.aws.amazon.com/systems-manager/latest/userguide/appconfig-creating-application.html)\.
   + [Create an environment](https://docs.aws.amazon.com/systems-manager/latest/userguide/appconfig-creating-environment.html)\.
   + [Create an AWS CodePipeline configuration profile](https://docs.aws.amazon.com/systems-manager/latest/userguide/appconfig-creating-configuration-and-profile.html)\.
   + \(Optional\) [Choose a predefined deployment strategy or create your own](https://docs.aws.amazon.com/systems-manager/latest/userguide/appconfig-creating-deployment-strategy.html)\.

## Step 2: Upload files to your S3 source bucket<a name="tutorials-AppConfig-bucket"></a>

In this section, zip and push your source files to the bucket that the pipeline uses for your source stage\.

**To create configuration files**

1. Create a `configuration.json` file for each configuration in each Region\. Include the following contents:

   ```
   Hello World!
   ```

1. In the Amazon S3 console for your bucket, choose **Upload**, and follow the instructions to upload your \.zip file\.

**To zip and upload source files**

1. Create a \.zip file with your files and name the \.zip file `configuration-files.zip`\. As an example, your \.zip file can use the following structure:

   ```
   .
   └── appconfig-configurations
       └── MyConfigurations
           ├── us-east-1
           │   └── configuration.json
           └── us-west-2
               └── configuration.json
   ```

1. In the Amazon S3 console for your bucket, choose **Upload**, and follow the instructions to upload your \.zip file\.

## Step 3: Create your pipeline<a name="tutorials-AppConfig-pipeline"></a>

In this section, you create a pipeline with the following actions:
+ A source stage with a CodeCommit action where the source artifacts are the files for your website\.
+ A deployment stage with an Amazon S3 deployment action\.

**To create a pipeline with the wizard**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Welcome** page, **Getting started** page, or **Pipelines** page, choose **Create pipeline**\.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyS3DeployPipeline**\.

1. In **Service role**, choose **New service role** to allow CodePipeline to create a service role in IAM\.

1. Leave the settings under **Advanced settings** at their defaults, and then choose **Next**\.

1. In **Step 2: Add source stage**, in **Source provider**, choose **Amazon S3**\. In **Bucket**, choose the name of your S3 source bucket\. 

   In **S3 object key**, enter the name of your \.zip file: `configuration-files.zip`\.

   Choose **Next**\.

1. In **Step 3: Add build stage**, choose **Skip build stage**, and then accept the warning message by choosing **Skip** again\. 

   Choose **Next**\.

1. In **Step 4: Add deploy stage**:

   1. In **Deploy provider**, choose **AWS AppConfig**\. 

   1. In **Application**, choose the name of the application you created in AWS AppConfig\. The field shows the ID for your application\.

   1. In **Environment**, choose the name of the environment you created in AWS AppConfig\. The field shows the ID for your environment\.

   1. In **Configuration profile**, choose the name of the configuration profile you created in AWS AppConfig\. The field shows the ID for your configuration profile\.

   1. In **Deployment strategy**, choose the name of your deployment strategy\. This can be either a deployment strategy you created in AppConfig or one you have chosen from predefined deployment strategies in AppConfig\. The field shows the ID for your deployment strategy\.

   1. In **Input artifact configuration path**, enter the file path\. Make sure that your input artifact configuration path matches the directory structure in your S3 bucket\.  
![\[The action page for an AWS AppConfig action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/appconfig-deploy-action.png)![\[The action page for an AWS AppConfig action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The action page for an AWS AppConfig action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   1. Choose **Next**\.

1. In **Step 5: Review**, review the information, and then choose **Create pipeline**\.  
![\[The completed pipeline for an AWS AppConfig action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/appconfig-pipeline.png)![\[The completed pipeline for an AWS AppConfig action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The completed pipeline for an AWS AppConfig action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## Step 4: Make a change to any source file and verify deployment<a name="tutorials-AppConfig-verify"></a>

Make a change to your source files and upload the change to your bucket\. This triggers your pipeline to run\. Verify that your configuration is available by viewing the version\.