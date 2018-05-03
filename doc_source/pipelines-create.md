# Create a Pipeline in AWS CodePipeline<a name="pipelines-create"></a>

You can use the AWS CodePipeline console or the AWS CLI to create a pipeline\.

You can also create pipelines that build and deploy container\-based applications by using Amazon ECS as the deployment provider\. Before you create a pipeline that deploys container\-based applications with Amazon ECS, you must prepare an image definitions file\.

AWS CodePipeline uses change\-detection methods to start your pipeline when a source code change is pushed\. These detection methods are based on source type: 
+ AWS CodePipeline uses Amazon CloudWatch Events to detect changes in your AWS CodeCommit source repository and branch or your Amazon S3 source bucket\.
+ AWS CodePipeline uses webhooks to detect changes in your GitHub source repository and branch\. A webhook is an HTTP notification that detects events in an external tool and connects those external events to a pipeline\.

**Note**  
When you use the console to create or edit a pipeline, the change\-detection resources are created for you\. If you use the AWS CLI to create the pipeline, you must create the additional resources yourself\. For more information, see [ Start an AWS CodeCommit Pipeline Automatically Using a CloudWatch Events Rule](triggering.md)\.

**Topics**
+ [Create Image Definitions File for Deploying Container\-based Applications](#pipelines-create-image-definitions)
+ [Create a Pipeline \(Console\)](#pipelines-create-console)
+ [Create a Pipeline \(CLI\)](#pipelines-create-cli)

## Create Image Definitions File for Deploying Container\-based Applications<a name="pipelines-create-image-definitions"></a>

An image definitions document is a JSON file that describes your Amazon ECS service's container name and the image and tag\. If you are deploying container\-based applications, you must generate an image definitions file to provide the AWS CodePipeline job worker with the Amazon ECS container and image identification to use for your pipeline’s deployment stage\.
+ The maximum file size limit for the image definitions file is 100 KB\.
+ You must generate the image definitions file so that it is ingested as an input artifact for the deploy action\.

The image definitions file provides the container name and image URI and must be constructed with the following set of key\-value pairs\.


**Create the image definitions file as a source or build artifact for container\-based deployments**  

| Key | Value | 
| --- | --- | 
| name | container\_name | 
| imageURI | image\_URI | 

JSON structure, where the container name is `sample-app`, and the image URI is `ecs-repo` and the tag is `latest`:

```
[
    {
        "name": "sample-app",
        "imageUri": "11111EXAMPLE.dkr.ecr.us-west-2.amazonaws.com/ecs-repo:latest"
    }
]
```

You can also construct the image definitions file to list multiple container/image pairs\. 

JSON structure:

```
[
    {
        "name": "simple-app",
        "imageUri": "httpd:2.4"
    },
    {
        "name": "simple-app-1",
        "imageUri": "mysql"
    },
    {
        "name": "simple-app-2",
        "imageUri": "java1.8"
    }
]
```

Before you create your pipeline, use the following steps to set up the image definitions file\.

1. As part of planning the container\-based application deployment for your pipeline, plan the source stage and the build stage, if applicable\.

1. Choose one of the following:

   1.  If your pipeline has no build stage, you must manually create the JSON file and upload it to your source repository so the source action can provide the artifact\. Create the file using a text editor, and name the file or use the default `imagedefinitions.json` file name\. Push the image definitions file to your source repository\.
**Note**  
Remember to zip the JSON file if your source repository is an Amazon S3 bucket\.

   1. If your pipeline has a build stage, add a command to your build spec file that outputs the image definitions file in your source repository during the build phase\. The following example uses the printf command to create an imagedefinitions\.json file\. List this command in the `post_build` section of the buildspec\.yml file:

      ```
      printf '[{"name":"container_name","imageUri":"image_URI"}]' >
      imagedefinitions.json
      ```

      You must include the image definitions file as an output artifact in the buildspec\.yml file\.

1. When you create your pipeline in the console, you must enter the exact image definitions file name into the **Image Filename** field on the **Deploy** page of the **Create Pipeline** wizard\.

For a step\-by\-step tutorial for creating a pipeline that uses Amazon ECS as the deployment provider, see [Tutorial: Continuous Deployment with AWS CodePipeline](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cd-pipeline.html)\.

**Topics**
+ [Create Image Definitions File for Deploying Container\-based Applications](#pipelines-create-image-definitions)
+ [Create a Pipeline \(Console\)](#pipelines-create-console)
+ [Create a Pipeline \(CLI\)](#pipelines-create-cli)

## Create a Pipeline \(Console\)<a name="pipelines-create-console"></a>

To create a pipeline in the console, you'll need to provide the source file location and information about the providers you will use for your actions\.

When you use the console to create a pipeline, you must include a source stage and one or both of the following: 
+ A build stage\.
+ A deployment stage\. 

When you use the pipeline wizard, AWS CodePipeline creates the names of stages \(source, build, staging\)\. These names cannot be changed\. You can use more specific names \(for example, BuildToGamma or DeployToProd\) to stages you add later\.

**Step 1: Create and name your pipeline**

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codepipeline](http://console.aws.amazon.com/codepipeline)\.

1. On the **Welcome** page, choose **Create pipeline**\. 

   If this is your first time using AWS CodePipeline, choose **Get Started Now**\.

1. On the **Step 1: Name** page, in **Pipeline name**, type the name for your pipeline, and then choose **Next step**\.

   Within a single AWS account, each pipeline you create in a region must have a unique name\. Names can be reused for pipelines in different regions\.
**Note**  
After you create a pipeline, you cannot change its name\. For information about other limitations, see [Limits in AWS CodePipeline](limits.md)\.

**Step 2: Create a source stage**
+ On the **Step 2: Source** page, in the **Source provider** drop\-down list, choose the type of repository where your source code is stored, specify its required options, and then choose **Next step**\.
  + For **GitHub**: 

    1. Choose **Connect to GitHub**\. If you are prompted to sign in, provide your GitHub credentials\. 
**Important**  
Do not provide your AWS credentials\.

    1. If this is your first time you are connecting to GitHub from AWS CodePipeline for this region, you are asked to authorize application access to your account\. Review the permissions required for integration, and then, if you want to continue, choose **Authorize application**\. When you connect to GitHub in the console, the following resources are created for you:
       + AWS CodePipeline uses an OAuth token to create an authorized application that is managed by AWS CodePipeline\.
**Note**  
In GitHub, there is a limit to the number of OAuth tokens you can use for an application, such as AWS CodePipeline\. If you exceed this limit, retry the connection to allow AWS CodePipeline to reconnect by reusing existing tokens\. For more information, see [To configure a pipeline to use a personal access token from GitHub](troubleshooting.md#troubleshooting-gs2)\.
       + AWS CodePipeline creates a webhook in GitHub to detect source changes and then start your pipeline when a change occurs\. In addition to the webhook,  AWS CodePipeline:
         + Randomly generates a secret and uses it to authorize the connection to GitHub\.
         + Generates the webhook URL using the public endpoint for the region and registers it with GitHub\. This subscribes the URL to receive repository events\.

    1. Choose the GitHub repository you want to use as the source location for your pipeline\. In **Branch**, from the drop\-down list, choose the branch you want to use\.
  + For **Amazon S3**: 

    1. In **Amazon S3 location**, provide the Amazon S3 bucket name and path to the object in a bucket with versioning enabled\. The format of the bucket name and path looks like this:

       ```
        s3://bucketName/folderName/objectName
       ```

    1. After you choose the Amazon S3 source bucket, a message appears in **Change detection options** that shows the Amazon CloudWatch Events rule and the AWS CloudTrail trail to be created for this pipeline\. The message also displays the data event to be added to your AWS CloudTrail trail in the following format: `my-bucket/path/to/object.zip`\. Accept the defaults under **Change detection options**\. This allows AWS CodePipeline to use Amazon CloudWatch Events and AWS CloudTrail to detect changes for your new pipeline\. Choose **Next step**\.
  + For **AWS CodeCommit**:
    + In **Repository name**, choose the name of the AWS CodeCommit repository you want to use as the source location for your pipeline\. In **Branch name**, from the drop\-down list, choose the branch you want to use\.
    + After you choose the AWS CodeCommit repository name and branch, a message is displayed in **Change detection options** showing the Amazon CloudWatch Events rule that will be created for this pipeline\. Accept the defaults under **Change detection options**\. This allows AWS CodePipeline to use Amazon CloudWatch Events to detect changes for your new pipeline\.
**Note**  
The object and file type must be compatible with the deployment system you plan to use \(for example, Elastic Beanstalk or AWS CodeDeploy\)\. Supported file types might include \.zip, \.tar, and \.tgz files\. For more information about the supported container types for Elastic Beanstalk, see [Customizing and Configuring Elastic Beanstalk Environments](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-containers.html) and [Supported Platforms](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.platforms.html)\. For more information about deploying revisions with AWS CodeDeploy, see [Uploading Your Application Revision](http://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-steps.html#deployment-steps-uploading-your-app) and [Prepare a Revision](http://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-prepare-revision.html)\.

**Step 3: Create a build stage**

On the **Step 3: Build** page, do one of the following, and then choose **Next step**:
+ 
  + Choose **No Build** to skip the configuration of a build stage\.
  + Choose a custom action provider of build services that you want to use, and provide the configuration details for that provider\.
**Note**  
The steps for adding a build provider vary by provider\. For an example of how to add Jenkins as a build provider, see [Tutorial: Create a Four\-Stage Pipeline](tutorials-four-stage-pipeline.md)\.
  + Choose AWS CodeBuild, and then choose your build options\. You can follow the instructions in [Create a Pipeline that Uses AWS CodeBuild](http://docs.aws.amazon.com/codebuild/latest/userguide/how-to-create-pipeline.html#pipelines-create-console) in *[AWS CodeBuild User Guide](http://docs.aws.amazon.com/codebuild/latest/userguide/)*\. 

    1. For **Project**, choose one of the following:
       + If you have already created the project in AWS CodeBuild, choose **Select an existing build project**\. In **Project name**, choose the name of the build project, and then choose **Save build project**\.
       + To use the AWS CodePipeline wizard to create your AWS CodeBuild project, choose **Create a new build project**\. Make sure you have completed the steps in [Plan a Build](http://docs.aws.amazon.com/codebuild/latest/userguide/planning.html)\. In **Project name**, type a name for your build project\.

    1. Choose the image and operating system for your build project\.

    1. Choose one of the following build specification options:
       + If you have already included a build spec file in your pipeline's source location, choose **Use the buildspec\.yml in the source code root directory**\.
       + To specify build commands and output file names in the wizard, choose **Insert build commands**\.

    1. In **Cache**, do one of the following:
       + If you do not want to use a cache for build dependencies, choose **No cache**\.
       + If you want to use a cache for build dependencies, choose **Amazon S3**, and then type the location of your Amazon S3 cache bucket\. Using a cache saves considerable build time because some reusable pieces of the build environment are stored in the cache and used across builds\.

         A valid cache bucket location must include the bucket name and prefix \(folder name\), as shown in this example:

         ```
         s3://mybucket/cacheBucket
         ```
**Note**  
Make sure your bucket is in the same region as your pipeline\.  
Make sure that the service role for AWS CodePipeline has appropriate permissions for AWS CodeBuild\. For more information, see [Add Permissions for Other AWS Services](how-to-custom-role.md#how-to-update-role-new-services)\.

**Step 4: Create a deployment stage**

On the **Step 4: Deploy** page, do one of the following, and then choose **Next step**:
+ Choose one of the following:
  + To skip the configuration of a deployment stage, choose **No Deployment**\.
**Note**  
You can skip adding a deployment provider now only if you chose a build provider in the previous step\.
  + Choose a custom action that you have created for a deployment provider\.
  + From the **Deployment provider** drop\-down list, choose one of the following default providers:
    + **AWS CodeDeploy**

      In **Application name**, type or choose the name of an existing AWS CodeDeploy application\. In **Deployment group**, type the name of a deployment group for the application\. Choose **Next step**\. You can also create an application, deployment group, or both in the AWS CodeDeploy console\.
    + **AWS Elastic Beanstalk**

       In **Application name**, type or choose the name of an existing Elastic Beanstalk application\. In **Environment name**, type an environment for the application\. Choose **Next step**\. You can also create an application, environment, or both in the Elastic Beanstalk console\.
    + **AWS OpsWorks Stacks**

      In **Stack**, type or choose the name of the stack you want to use\. In **Layer**, choose the layer that your target instances belong to\. In **App**, choose the application that you want to update and deploy\. If you need to create an app, choose **create a new one in AWS OpsWorks**\. 

      For information about adding an application to a stack and layer in AWS OpsWorks, see [Adding Apps](http://docs.aws.amazon.com/opsworks/latest/userguide/workingapps-creating.html) in the *AWS OpsWorks User Guide*\. 

      For an end\-to\-end example of how to use a simple pipeline in AWS CodePipeline as the source for code that you run on AWS OpsWorks layers, see [Using AWS CodePipeline with AWS OpsWorks Stacks](http://docs.aws.amazon.com/opsworks/latest/userguide/other-services-cp.html)\.
    + **AWS CloudFormation**

       Do one of the following:
      + In **Action mode**, choose **Create or update a stack**, type a stack name and template file name, and then choose the name of a role for AWS CloudFormation to assume\. Optionally, type the name of a configuration file and choose an IAM capability option\.
      + In **Action mode**, choose **Create or replace a change set**, type a stack name and change set name, and then choose the name of a role for AWS CloudFormation to assume\. Optionally, type the name of a configuration file and choose an IAM capability option\.

      For information about integrating AWS CloudFormation capabilities into a pipeline in AWS CodePipeline, see [Continuous Delivery with AWS CodePipeline](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline.html) in the *AWS CloudFormation User Guide*\.
    + **Amazon ECS**

       In **Cluster name**, type or choose the name of an existing Amazon ECS cluster\. In **Service name**, type or choose the name of the service running on the cluster\. You can also create a cluster and service\. In **Image filename**, type the name of the image definitions file that describes your service's container and image\. Choose **Next step**\.
**Note**  
Make sure your Amazon ECS cluster is configured with two or more instances\. Amazon ECS clusters must contain at least two instances so that one is maintained as the primary instance and another is used to accommodate new deployments\.

      For a tutorial about deploying container\-based applications with your pipeline, see [Tutorial: Continuous Deployment with AWS CodePipeline](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cd-pipeline.html)\.

**Step 5: Create a service role and review the pipeline**

1. On the **Step 5: Service Role** page, do one of the following, and then choose **Next step**:
   + In the **Service Role** drop\-down list, choose an IAM service role that you have set up for AWS CodePipeline\.
   + If you do not have a service role, choose **Create role**\. On the IAM console page that describes the role to be created for you, choose **Allow**\. On the **Step 5: Service Role** page, *AWS\-CodePipeline\-Service* appears in the drop\-down box\.
**Note**  
Depending on when your service role was created, you might need to update its permissions to support additional AWS services\. For information, see [Add Permissions for Other AWS Services](how-to-custom-role.md#how-to-update-role-new-services)\. 

   For more information about the service role and its policy statement, see [Manage the AWS CodePipeline Service Role](how-to-custom-role.md)\.

1. On the **Step 6: Review** page, review your pipeline configuration, and then choose **Create pipeline** to create the pipeline or **Previous** to go back and edit your choices\. To exit the wizard without creating a pipeline, choose **Cancel**\.

Now that you've created your pipeline, you can view it in the console\. The pipeline starts to run automatically after you create it\. For more information, see [View Pipeline Details and History in AWS CodePipeline](pipelines-view.md)\. For more information about making changes to your pipeline, see [Edit a Pipeline in AWS CodePipeline](pipelines-edit.md)\.

## Create a Pipeline \(CLI\)<a name="pipelines-create-cli"></a>

To use the AWS CLI to create a pipeline, you create a JSON file to define the pipeline structure, and then run the create\-pipeline command with the `--cli-input-json` parameter\. 

**Important**  
You cannot use the AWS CLI to create a pipeline that includes partner actions\. You must use the AWS CodePipeline console instead\.

For more information about pipeline structure, see [AWS CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md) and [create\-pipeline](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/create-pipeline.html) in the AWS CodePipeline [API Reference](http://docs.aws.amazon.com/cli/latest/reference/codepipeline)\.

To create a JSON file, use the sample pipeline JSON file, edit it, and then call that file when you run the create\-pipeline command\.

You need the ARN of the service role you created for AWS CodePipeline in [Getting Started with AWS CodePipeline](getting-started-codepipeline.md) and the name of an Amazon S3 bucket where artifacts for the pipeline are stored\. This bucket must be in the same region as the pipeline\. You use the ARN and bucket name when you run the create\-pipeline command\. Unlike the console, running the create\-pipeline command in the AWS CLI does not create an Amazon S3 bucket for storing artifacts\. The bucket must already exist\.

**Note**  
You can also use the get\-pipeline command to get a copy of the JSON structure of that pipeline, and then modify that structure in a plain\-text editor\.

**Topics**

**To create the JSON file**

1. At a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), create a new text file in a local directory\.

1. Open the file in a plain\-text editor and edit the values to reflect the structure you want to create\. At a minimum, you must change the name of the pipeline\. You should also consider whether you want to change:
   + The Amazon S3 bucket where artifacts for this pipeline are stored\.
   + The source location for your code\.
   + The deployment provider\.
   + How you want your code deployed\.

   The following two\-stage sample pipeline structure highlights the values you should consider changing for your pipeline\. Your pipeline likely contains more than two stages:

   ```
   {
       "pipeline": {
           "roleArn": "arn:aws:iam::80398EXAMPLE::role/AWS-CodePipeline-Service",  
           "stages": [
               {
                   "name": "Source",
                   "actions": [
                       {
                           "inputArtifacts": [],
                           "name": "Source",
                           "actionTypeId": {
                               "category": "Source",
                               "owner": "AWS",
                               "version": "1",
                               "provider": "S3"
                           },
                           "outputArtifacts": [
                               {
                                   "name": "MyApp"
                               }
                           ],
                           "configuration": {
                               "S3Bucket": "awscodepipeline-demobucket-example-date",
                               "S3ObjectKey": "ExampleCodePipelineSampleBundle.zip",
                               "PollForSourceChanges": "false"
                           },
                           "runOrder": 1                       
                       }
                   ]
               },
               {
                   "name": "Staging",
                   "actions": [
                       {
                           "inputArtifacts": [
                               {
                                   "name": "MyApp"
                               }
                           ],
                           "name": "Deploy-CodeDeploy-Application",
                           "actionTypeId": {
                               "category": "Deploy",
                               "owner": "AWS",
                               "version": "1",
                               "provider": "CodeDeploy"
                           },
                           "outputArtifacts": [],
                           "configuration": {
                               "ApplicationName": "CodePipelineDemoApplication",
                               "DeploymentGroupName": "CodePipelineDemoFleet"
                           },
                           "runOrder": 1
                       }
                   ]
               }
           ],
           "artifactStore": {
               "type": "S3",
               "location": "codepipeline-us-east-2-250656481468"
           },
           "name": "MyFirstPipeline",
           "version": 1
       },
       "metadata": {
           "pipelineArn": "arn:aws:codepipeline:us-east-2:80398EXAMPLE:MyFirstPipeline",
           "updated": 1501626591.112,
           "created": 1501626591.112
       }
   }
   ```

   Make sure the `PollForSourceChanges` parameter in your JSON file is set as follows: 

   ```
                   "PollForSourceChanges": "false",
   ```

   AWS CodePipeline uses Amazon CloudWatch Events to detect changes in your AWS CodeCommit source repository and branch or your Amazon S3 source bucket\. AWS CodePipeline uses webhooks to detect changes in your GitHub source repository and branch\. The next step includes instructions to create these resources manually for your pipeline\. Setting the flag to false disables periodic checks, which are not necessary when using the recommended change detection methods\. 

1. When you are satisfied with its structure, save your file with a name like **pipeline\.json**\.

**To create a pipeline**

1. Run the create\-pipeline command and use the `--cli-input-json` parameter to specify the JSON file you created previously\.

   To create a pipeline named *MySecondPipeline* with a JSON file named pipeline\.json that includes the name "*MySecondPipeline*" as the value for `name` in the JSON, your command would look similar to the following:

   ```
   aws codepipeline create-pipeline --cli-input-json file://pipeline.json
   ```
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   This command returns the structure of the entire pipeline you created\.

1. To view the pipeline you just created, either open the AWS CodePipeline console and choose it from the list of pipelines, or use the get\-pipeline\-state command\. For more information, see [View Pipeline Details and History in AWS CodePipeline](pipelines-view.md)\. 

1. If you use the CLI to create a pipeline, you must manually create the recommended change\-detection resources for your pipeline:
   + For a pipeline with an AWS CodeCommit repository, you must manually create the CloudWatch Events rule, as described in [Create a CloudWatch Events Rule That Starts Your AWS CodeCommit Pipeline \(CLI\)](pipelines-trigger-source-repo-changes-cli.md)\.
   + For a pipeline with an Amazon S3 source, you must manually create the CloudWatch Events rule and AWS CloudTrail trail, as described in [Start an Amazon S3 Pipeline Automatically Using a CloudWatch Events Rule](create-cloudtrail-S3-source.md)\.
   + For a pipeline with a GitHub source, you must manually create the webhook, as described in [Start a GitHub Pipeline Automatically Using Webhooks](pipelines-webhooks.md)\.