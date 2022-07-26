# Tutorial: Create a pipeline with AWS CloudFormation StackSets deployment actions<a name="tutorials-stackset-deployment"></a>

In this tutorial, you use the AWS CodePipeline console to create a pipeline with deployment actions for creating a stack set and creating stack instances\. When the pipeline runs, the template creates a stack set and also creates and updates the instances where the stack set is deployed\.

There are two ways to manage permissions for a stack set: self\-managed and AWS\-managed IAM roles\. This tutorial provides examples with self\-managed permissions\.

To most effectively use Stacksets in CodePipeline, you should have a clear understanding of the concepts behind AWS CloudFormation StackSets and how they work\. See [StackSets concepts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html) in the *AWS CloudFormation User Guide*\.

**Note**  
This feature is not available in the Asia Pacific \(Hong Kong\) and Europe \(Milan\) Regions\. To reference other available actions, see [Product and service integrations with CodePipeline](integrations.md)\.

**Topics**
+ [Prerequisites](#tutorials-stackset-deployment-prereq)
+ [Step 1: Upload the sample AWS CloudFormation template and parameter file](#tutorials-stackset-deployment-upload)
+ [Step 2: Create your pipeline](#tutorials-cloudformation-action-pipeline)
+ [Step 3: View initial deployment](#tutorials-cloudformation-action-initial)
+ [Step 4: Add a CloudFormationStackInstances action](#tutorials-stacksets-instances)
+ [Step 5: View stack set resources for your deployment](#tutorials-stacksets-view)
+ [Step 6: Make an update to your stack set](#tutorials-stacksets-update)

## Prerequisites<a name="tutorials-stackset-deployment-prereq"></a>

For stack set operations, you use two different accounts: an administration account and a target account\. You create stack sets in the administrator account\. You create individual stacks that belong to a stack set in the target account\.

**To create an administrator role with your administrator account**
+ Follow the instructions in [Set up basic permissions for stack set operations](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-prereqs-self-managed.html#stacksets-prereqs-accountsetup)\. Your role must be named **`AWSCloudFormationStackSetAdministrationRole`**\.

**To create a service role in the target account**
+ Create a service role in the target account that trusts the administrator account\. Follow the instructions in [Set up basic permissions for stack set operations](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-prereqs-self-managed.html#stacksets-prereqs-accountsetup)\. Your role must be named **`AWSCloudFormationStackSetExecutionRole`**\. 

## Step 1: Upload the sample AWS CloudFormation template and parameter file<a name="tutorials-stackset-deployment-upload"></a>

Create a source bucket for your stack set template and parameters files\. Download the sample AWS CloudFormation template file, set up a parameters file, and then zip the files before upload to your S3 source bucket\.

**Note**  
Make sure to ZIP the source files before you upload to your S3 source bucket, even if the only source file is the template\.



**To create an S3 source bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\.

1. In **Bucket name**, enter a name for your bucket\.

   In **Region**, choose the Region where you want to create your pipeline\. Choose **Create bucket**\.

1. After the bucket is created, a success banner displays\. Choose **Go to bucket details**\.

1. On the **Properties** tab, choose **Versioning**\. Choose **Enable versioning**, and then choose **Save**\.

**To create the AWS CloudFormation template file**

1. Download the following sample template file for generating CloudTrail configuration for stack sets: [https://s3.amazonaws.com/cloudformation-stackset-sample-templates-us-east-1/EnableAWSCloudtrail.yml](https://s3.amazonaws.com/cloudformation-stackset-sample-templates-us-east-1/EnableAWSCloudtrail.yml)\.

1. Save the file as `template.yml`\.

**To create the parameters\.txt file**

1. Create a file with the parameters for your deployment\. Parameters are values that you want to update in your stack at runtime\. The following sample file updates the template parameters for your stack set to enable logging validation and global events\.

   ```
   [
     {
       "ParameterKey": "EnableLogFileValidation",
       "ParameterValue": "true"
     }, 
     {
       "ParameterKey": "IncludeGlobalEvents",
       "ParameterValue": "true"
     }
   ]
   ```

1. Save the file as `parameters.txt`\.

**To create the accounts\.txt file**

1. Create a file with the accounts where you want to create instances, as shown in the following sample file\.

   ```
   [
       "111111222222,333333444444"
   ]
   ```

1. Save the file as `accounts.txt`\.

**To create and upload source files**

1. Combine the files into a single ZIP file\. Your files should look like this in your ZIP file\.

   ```
   template.yml
   parameters.txt
   accounts.txt
   ```

1. Upload the ZIP file to your S3 bucket\. This file is the source artifact created by the **Create Pipeline** wizard for your deployment action in CodePipeline\.

## Step 2: Create your pipeline<a name="tutorials-cloudformation-action-pipeline"></a>

In this section, you create a pipeline with the following actions:
+ A source stage with an S3 source action where the source artifact is your template file and any supporting source files\.
+ A deployment stage with an AWS CloudFormation stack set deployment action that creates the stack set\.
+ A deployment stage with an AWS CloudFormation stack instances deployment action that creates the stacks and instances within the target accounts\.

**To create a pipeline with a CloudFormationStackSet action**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Welcome** page, **Getting started** page, or **Pipelines** page, choose **Create pipeline**\.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyStackSetsPipeline**\.

1. In **Service role**, choose **New service role** to allow CodePipeline to create a service role in IAM\.

1. In **Artifact store**, leave the defaults\.
**Note**  
This is not the source bucket for your source code\. This is the artifact store for your pipeline\. A separate artifact store, such as an S3 bucket, is required for each pipeline\. When you create or edit a pipeline, you must have an artifact bucket in the pipeline Region and one artifact bucket per AWS Region where you are running an action\.  
For more information, see [Input and output artifacts](welcome-introducing-artifacts.md) and [CodePipeline pipeline structure reference](reference-pipeline-structure.md)\.

   Choose **Next**\.

1. On the **Step 2: Add source stage** page, in **Source provider**, choose **Amazon S3**\.

1. In **Bucket**, enter the S3 source bucket you created for this tutorial, such as `BucketName`\. In **S3 object key**, enter the file path and file name for your ZIP file, such as `MyFiles.zip`\.

1. Choose **Next**\.

1. In **Step 3: Add build stage**, choose **Skip build stage**, and then accept the warning message by choosing **Skip** again\. 

   Choose **Next**\.

1. In **Step 4: Add deploy stage**:

   1. In **Deploy provider**, choose **AWS CloudFormation Stack Set**\.

   1. In **Stack set name**, enter a name for the stack set\. This is the name of the stack set that the template creates\.
**Note**  
Make a note of your stack set name\. You will use it when you add the second StackSets deployment action to your pipeline\.

   1. In **Template path**, enter the artifact name and file path where you uploaded your template file\. For example, enter the following using the default source artifact name `SourceArtifact`\.

      ```
      SourceArtifact::template.yml
      ```

   1. In **Deployment targets**, enter the artifact name and file path where you uploaded your accounts file\. For example, enter the following using the default source artifact name `SourceArtifact`\.

      ```
      SourceArtifact::accounts.txt
      ```

   1. In **Deployment target AWS Regions**, enter one Region for deployment of your initial stack instance, such as `us-east-1`\.

   1. Expand **Deployment options**\. In **Parameters**, enter the artifact name and file path where you uploaded your parameters file\. For example, enter the following using the default source artifact name `SourceArtifact`\.

      ```
      SourceArtifact::parameters.txt
      ```

      To enter the parameters as a literal input rather than a file path, enter the following:

      ```
      ParameterKey=EnableLogFileValidation,ParameterValue=true
      ParameterKey=IncludeGlobalEvents,ParameterValue=true
      ```

   1. In **Capabilities**, choose CAPABILITY\_IAM and CAPABILITY\_NAMED\_IAM\.

   1. In **Permission model**, choose SELF\_MANAGED\.

   1. In **Failure tolerance percentage**, enter `20`\.

   1. In **Max concurrent percentage**, enter `25`\.

   1. Choose **Next**\.

   1. Choose **Create pipeline**\. Your pipeline displays\. 

   1. Allow your pipeline to run\. 

## Step 3: View initial deployment<a name="tutorials-cloudformation-action-initial"></a>

View the resources and status for your initial deployment\. After verifying the deployment successfully created your stack set, you can add the second action to your **Deploy** stage\.

**To view the resources**

1. Open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

1. Under **Pipelines**, choose your pipeline and choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. Choose the AWS CloudFormation action on the **CloudFormationStackSet** action in your pipeline\. The template, resources, and events for your stack set are shown in the AWS CloudFormation console\.

1. In the left navigation panel, choose **StackSets**\. In the list, choose the new stack set\.

1. Choose the **Stack instances** tab\. Verify that one stack instance for each account you provided was created in the us\-east\-1 Region\. Verify that the status for each stack instance is `CURRENT`\.

## Step 4: Add a CloudFormationStackInstances action<a name="tutorials-stacksets-instances"></a>

Create a next action in your pipeline that will allow AWS CloudFormation StackSets to create the remainingstack instances\.

**To create a next action in your pipeline**

1. Open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   Under **Pipelines**, choose your pipeline and choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. Choose to edit the pipeline\. The pipeline displays in **Edit** mode\. 

1. On the **Deploy** stage, choose **Edit**\.

1. Under the **AWS CloudFormation Stack Set** deploy action, choose **Add action group**\.

1. On the **Edit action** page, add the action details:

   1. In **Action name**, enter a name for the action\.

   1. In **Action provider**, choose **AWS CloudFormation Stack Instances**\.

   1. Under **Input artifacts**, choose **SourceArtifact**\.

   1. In **Stack set name**, enter the name for the stack set\. This is the name of the stack set that you provided in the first action\.

   1. In **Deployment targets**, enter the artifact name and file path where you uploaded your accounts file\. For example, enter the following using the default source artifact name `SourceArtifact`\.

      ```
      SourceArtifact::accounts.txt
      ```

   1. In **Deployment target AWS Regions**, enter the Regions for deployment of your remaining stack instances, such as `us-east-2` and `eu-central-1` as follows:

      ```
      us-east2, eu-central-1
      ```

   1. In **Failure tolerance percentage**, enter `20`\.

   1. In **Max concurrent percentage**, enter `25`\.

   1. Choose **Save**\.

   1. \.Manually release a change\. Your updated pipeline displays with two actions in the Deploy stage\.

## Step 5: View stack set resources for your deployment<a name="tutorials-stacksets-view"></a>

You can view the resources and status for your stack set deployment\.

**To view the resources**

1. Open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

1. Under **Pipelines**, choose your pipeline and then choose **View**\. The diagram shows your pipeline source and deployment stages\.

1. Choose the AWS CloudFormation action on the **`AWS CloudFormation Stack Instances`** action in your pipeline\. The template, resources, and events for your stack set are shown in the AWS CloudFormation console\.

1. In the left navigation panel, choose **StackSets**\. In the list, choose your stack set\.

1. Choose the **Stack instances** tab\. Verify that all remaining stack instances for each account you provided were created or updated in the expected Regions\. Verify that the status for each stack instance is `CURRENT`\.

## Step 6: Make an update to your stack set<a name="tutorials-stacksets-update"></a>

Make an update to your stack set and deploy the update to instances\. In this example, you also make a change to the deployment targets you want to designate for update\. The instances that are not part of the update move to an outdated status\.

1. Open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

1. Under **Pipelines**, choose your pipeline and then choose **Edit**\. On the **Deploy** stage, choose **Edit**\.

1. Choose to edit the **AWS CloudFormation Stack Set** action in your pipeline\. In **Description**, write over the existing description with a new description for the stack set\.

1. Choose to edit the **AWS CloudFormation Stack Instances** action in your pipeline\. In **Deployment target AWS Regions**, delete the `us-east-2` value that was entered when the action was created\.

1. Save the changes\. Choose **Release change** to run your pipeline\.

1. Open your action in AWS CloudFormation\. Choose the **StackSet info** tab\. In **StackSet description**, verify that the new description is shown\.

1. Choose the **Stack instances** tab\. Under **Status**, verify that the status for the stack instances in us\-east\-2 is `OUTDATED`\.