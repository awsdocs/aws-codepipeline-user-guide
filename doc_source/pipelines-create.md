# Create a Pipeline in CodePipeline<a name="pipelines-create"></a>

You can use the AWS CodePipeline console or the AWS CLI to create a pipeline\. Pipelines must have at least two stages\. The first stage of a pipeline is required to be a source stage, and the pipeline is required to additionally have at least one other stage that is a build or deployment stage\.

You can add actions to your pipeline that are in an AWS Region different from your pipeline\. When an AWS service is the provider for an action, and this action type/provider type are in a different AWS Region from your pipeline, this is a cross\-region action\. For more information about cross\-region actions, see [Add a Cross\-Region Action in CodePipeline](actions-create-cross-region.md)\.

You can also create pipelines that build and deploy container\-based applications by using Amazon ECS as the deployment provider\. Before you create a pipeline that deploys container\-based applications with Amazon ECS, you must create an image definitions file\.

CodePipeline uses change detection methods to start your pipeline when a source code change is pushed\. These detection methods are based on source type:
+ CodePipeline uses Amazon CloudWatch Events to detect changes in your CodeCommit source repository and branch or your Amazon S3 source bucket\.
+ CodePipeline uses webhooks to detect changes in your GitHub source repository and branch\. A webhook is an HTTP notification that detects events in an external tool and connects those external events to a pipeline\.

**Note**  
When you use the console to create or edit a pipeline, the change detection resources are created for you\. If you use the AWS CLI to create the pipeline, you must create the additional resources yourself\. For more information, see [ Use CloudWatch Events to Start a Pipeline \(CodeCommit Source\)](triggering.md)\.

**Topics**
+ [Create an Image Definitions File for Deploying Container\-Based Applications](#pipelines-create-image-definitions)
+ [Create a Pipeline \(Console\)](#pipelines-create-console)
+ [Create a Pipeline \(CLI\)](#pipelines-create-cli)

## Create an Image Definitions File for Deploying Container\-Based Applications<a name="pipelines-create-image-definitions"></a>

An image definitions document is a JSON file that describes your Amazon ECS container name and the image and tag\. If you are deploying container\-based applications, you must generate an image definitions file to provide the CodePipeline job worker with the Amazon ECS container and image identification to use for your pipeline’s deployment stage\.
+ The maximum file size limit for the image definitions file is 100 KB\.
+ You must generate the image definitions file so that it is ingested as an input artifact for the deploy action\.

The image definitions file provides the container name and image URI\. It must be constructed with the following set of key\-value pairs\.


**Create the image definitions file as a source or build artifact for container\-based deployments**  

| Key | Value | 
| --- | --- | 
| name | container\_name | 
| imageURI | image\_URI | 

Here is the JSON structure, where the container name is `sample-app`, the image URI is `ecs-repo`, and the tag is `latest`:

```
[
    {
        "name": "sample-app",
        "imageUri": "11111EXAMPLE.dkr.ecr.us-west-2.amazonaws.com/ecs-repo:latest"
    }
]
```

You can also construct the image definitions file to list multiple container\-image pairs\. 

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

   1.  If your pipeline has skipped the build stage, you must manually create the JSON file and upload it to your source repository so the source action can provide the artifact\. Create the file using a text editor, and name the file or use the default `imagedefinitions.json` file name\. Push the image definitions file to your source repository\.
**Note**  
If your source repository is an Amazon S3 bucket, remember to zip the JSON file\.

   1. If your pipeline has a build stage, add a command to your build spec file that outputs the image definitions file in your source repository during the build phase\. The following example uses the printf command to create an imagedefinitions\.json file\. List this command in the `post_build` section of the buildspec\.yml file:

      ```
      printf '[{"name":"container_name","imageUri":"image_URI"}]' >
      imagedefinitions.json
      ```

      You must include the image definitions file as an output artifact in the buildspec\.yml file\.

1. When you create your pipeline in the console, you must enter the image definitions file name into the **Image Filename** field on the **Deploy** page of the **Create Pipeline** wizard\.

For a step\-by\-step tutorial for creating a pipeline that uses Amazon ECS as the deployment provider, see [Tutorial: Continuous Deployment with CodePipeline](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cd-pipeline.html)\.

**Topics**
+ [Create an Image Definitions File for Deploying Container\-Based Applications](#pipelines-create-image-definitions)
+ [Create a Pipeline \(Console\)](#pipelines-create-console)
+ [Create a Pipeline \(CLI\)](#pipelines-create-cli)

## Create a Pipeline \(Console\)<a name="pipelines-create-console"></a>

To create a pipeline in the console, you must provide the source file location and information about the providers you will use for your actions\.

When you use the console to create a pipeline, you must include a source stage and one or both of the following: 
+ A build stage\.
+ A deployment stage\. 

When you use the pipeline wizard, CodePipeline creates the names of stages \(source, build, staging\)\. These names cannot be changed\. You can use more specific names \(for example, BuildToGamma or DeployToProd\) to stages you add later\.

**Step 1: Create and name your pipeline**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Welcome** page, choose **Create pipeline**\. 

   If this is your first time using CodePipeline, choose **Get Started**\.

1. On the **Step 1: Choose pipeline settings** page, in **Pipeline name**, enter the name for your pipeline\.

   In a single AWS account, each pipeline you create in an AWS Region must have a unique name\. Names can be reused for pipelines in different Regions\.
**Note**  
After you create a pipeline, you cannot change its name\. For information about other limitations, see [Limits in AWS CodePipeline](limits.md)\.

1. In **Service role**, do one of the following:
   + Choose **New service role** to allow CodePipeline to create a new service role in IAM\. In **Role name**, the role and policy name both default to this format: AWSCodePipelineServiceRole\-*region*\-*pipeline\_name*\. For example, this is the service role created for a pipeline named MyPipeline: AWSCodePipelineServiceRole\-eu\-west\-2\-MyPipeline\.
   + Choose **Existing service role** to use a service role already created in IAM\. In **Role name**, choose your service role from the list\.
**Note**  
Depending on when your service role was created, you might need to update its permissions to support additional AWS services\. For information, see [Add Permissions for Other AWS Services](how-to-custom-role.md#how-to-update-role-new-services)\. 

   For more information about the service role and its policy statement, see [Manage the CodePipeline Service Role](how-to-custom-role.md)\.

1. In **Artifact store**, do one of the following: 

   1. Choose **Default location** to use the default artifact store, such as the Amazon S3 artifact bucket designated as the default, for your pipeline in the AWS Region you have selected for your pipeline\.

   1. Choose **Custom location** if you already have an artifact store, such as an Amazon S3 artifact bucket, in the same Region as your pipeline\.
**Note**  
This is not the source bucket for your pipeline's source code\. This is the artifact store for your pipeline\. A separate artifact store, such as an Amazon S3 bucket, is required for each pipeline\. The artifact store you create or choose must be in the same AWS Region as your pipeline\. If you add cross\-region actions with the console, CodePipeline uses an additional artifact store in each Region where you have actions\. For more information, see [A Quick Look at Input and Output Artifacts](welcome.md#welcome-introducing-artifacts) and [CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\.

1.  Choose **Next**\.

**Step 2: Create a source stage**
+ On the **Step 2: Add source stage** page, in **Source provider**, choose the type of repository where your source code is stored, specify its required options, and then choose **Next step**\.
  + For **GitHub**: 

    1. Choose **Connect to GitHub**\. If you are prompted to sign in, provide your GitHub credentials\. 
**Important**  
Do not provide your AWS credentials\.

    1. If this is your first time connecting to GitHub from CodePipeline for this Region, you are asked to authorize application access to your account\. Review the permissions required for integration, and then, if you want to continue, choose **Authorize application**\. When you connect to GitHub in the console, the following resources are created for you:
       + CodePipeline uses an OAuth token to create an authorized application that is managed by CodePipeline\.
**Note**  
In GitHub, there is a limit to the number of OAuth tokens you can use for an application, such as CodePipeline\. If you exceed this limit, retry the connection to allow CodePipeline to reconnect by reusing existing tokens\. For more information, see [To configure a pipeline to use a personal access token from GitHub](troubleshooting.md#troubleshooting-gs2)\.
       + CodePipeline creates a webhook in GitHub to detect source changes and then start your pipeline when a change occurs\. In addition to the webhook, CodePipeline:
         + Randomly generates a secret and uses it to authorize the connection to GitHub\.
         + Generates the webhook URL using the public endpoint for the Region and registers it with GitHub\. This subscribes the URL to receive repository events\.

    1. Choose the GitHub repository you want to use as the source location for your pipeline\. In **Branch**, from the drop\-down list, choose the branch you want to use\.
  + For **Amazon S3**: 

    1. In **Amazon S3 location**, provide the Amazon S3 bucket name and path to the object in a bucket with versioning enabled\. The format of the bucket name and path looks like this:

       ```
        s3://bucketName/folderName/objectName
       ```

    1. After you choose the Amazon S3 source bucket, CodePipeline creates the Amazon CloudWatch Events rule and the AWS CloudTrail trail to be created for this pipeline\. Accept the defaults under **Change detection options**\. This allows CodePipeline to use Amazon CloudWatch Events and AWS CloudTrail to detect changes for your new pipeline\. Choose **Next**\.
  + For **AWS CodeCommit**:
    + In **Repository name**, choose the name of the CodeCommit repository you want to use as the source location for your pipeline\. In **Branch name**, from the drop\-down list, choose the branch you want to use\.
    + After you choose the CodeCommit repository name and branch, a message is displayed in **Change detection options** showing the Amazon CloudWatch Events rule to be created for this pipeline\. Accept the defaults under **Change detection options**\. This allows CodePipeline to use Amazon CloudWatch Events to detect changes for your new pipeline\.
  + For **Amazon ECR**:
    + In **Repository name**, choose the name of your Amazon ECR repository\.
    + In **Image tag**, specify the image name and version, if different from LATEST\.
    + In **Output artifacts**, choose the output artifact default, such as MyApp, that contains the image name and repository URI information you want the next stage to use\.

      For a tutorial about creating a pipeline for Amazon ECS blue\-green deployments with an Amazon ECR source stage, see [Tutorial: Create a Pipeline with an Amazon ECR Source and ECS\-to\-CodeDeploy Deployment](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/tutorials-ecs-ecr-codedeploy.html)\.
**Note**  
The object and file type must be compatible with the deployment system you plan to use \(for example, Elastic Beanstalk or CodeDeploy\)\. Supported file types might include \.zip, \.tar, and \.tgz files\. For more information about the supported container types for Elastic Beanstalk, see [Customizing and Configuring Elastic Beanstalk Environments](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-containers.html) and [Supported Platforms](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.platforms.html)\. For more information about deploying revisions with CodeDeploy, see [Uploading Your Application Revision](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-steps.html#deployment-steps-uploading-your-app) and [Prepare a Revision](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-prepare-revision.html)\.

**Step 3: Create a build stage**

This step is optional if you plan to create a deployment stage\.
+ On the **Step 3: Add build stage** page, do one of the following, and then choose **Next**: 
  + Choose **Skip build stage** if you plan to create a deployment stage\.
  + From **Build provider**, choose a custom action provider of build services that you want to use, and provide the configuration details for that provider\. For an example of how to add Jenkins as a build provider, see [Tutorial: Create a Four\-Stage Pipeline](tutorials-four-stage-pipeline.md)\.
  + From **Build provider**, choose **AWS CodeBuild**\. 

    In **Region**, choose the AWS Region where the resource is created or where you plan to create it\. The **Region** field designates where the AWS resources are created for this action type and provider type\. This field only displays for actions where the action provider is an AWS service\. The **Region** field defaults to the same AWS Region as your pipeline\.

    In **Project name**, choose your build project\. If you have already created a build project in CodeBuild, choose it\. Or you can create a build project in CodeBuild and then return to this task\. Follow the instructions in [Create a Pipeline That Uses CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/how-to-create-pipeline.html#pipelines-create-console) in the *CodeBuild User Guide*\. 

**Step 4: Create a deployment stage**

This step is optional if you have already created a build stage\.
+ On the **Step 4: Add deploy stage** page, do one of the following, and then choose **Next**:
  + Choose **Skip deploy stage** if you created a build stage in the previous step\.
**Note**  
This option does not appear if you have already skipped the build stage\. 
  + In **Deploy provider**, choose a custom action that you have created for a deployment provider\. 

    In **Region**, for cross\-region actions only, choose the AWS Region where the resource is created\. The **Region** field designates where the AWS resources are created for this action type and provider type\. This field only displays for actions where the action provider is an AWS service\. The **Region** field defaults to the same AWS Region as your pipeline\.
  + In **Deploy provider**, fields are available for default providers as follows:
    + **CodeDeploy**

      In **Application name**, enter or choose the name of an existing CodeDeploy application\. In **Deployment group**, enter the name of a deployment group for the application\. Choose **Next**\. You can also create an application, deployment group, or both in the CodeDeploy console\.
    + **AWS Elastic Beanstalk**

       In **Application name**, enter or choose the name of an existing Elastic Beanstalk application\. In **Environment name**, enter an environment for the application\. Choose **Next**\. You can also create an application, environment, or both in the Elastic Beanstalk console\.
    + **AWS OpsWorks Stacks**

      In **Stack**, enter or choose the name of the stack you want to use\. In **Layer**, choose the layer that your target instances belong to\. In **App**, choose the application that you want to update and deploy\. If you need to create an app, choose **Create a new one in AWS OpsWorks**\. 

      For information about adding an application to a stack and layer in AWS OpsWorks, see [Adding Apps](https://docs.aws.amazon.com/opsworks/latest/userguide/workingapps-creating.html) in the *AWS OpsWorks User Guide*\. 

      For an end\-to\-end example of how to use a simple pipeline in CodePipeline as the source for code that you run on AWS OpsWorks layers, see [Using CodePipeline with AWS OpsWorks Stacks](https://docs.aws.amazon.com/opsworks/latest/userguide/other-services-cp.html)\.
    + **AWS CloudFormation**

       Do one of the following:
      + In **Action mode**, choose **Create or update a stack**, enter a stack name and template file name, and then choose the name of a role for AWS CloudFormation to assume\. Optionally, enter the name of a configuration file and choose an IAM capability option\.
      + In **Action mode**, choose **Create or replace a change set**, enter a stack name and change set name, and then choose the name of a role for AWS CloudFormation to assume\. Optionally, enter the name of a configuration file and choose an IAM capability option\.

      For information about integrating AWS CloudFormation capabilities into a pipeline in CodePipeline, see [Continuous Delivery with CodePipeline](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline.html) in the *AWS CloudFormation User Guide*\.
    + **Amazon ECS**

       In **Cluster name**, enter or choose the name of an existing Amazon ECS cluster\. In **Service name**, enter or choose the name of the service running on the cluster\. You can also create a cluster and service\. In **Image filename**, enter the name of the image definitions file that describes your service's container and image\. Choose **Next**\.
**Note**  
Make sure your Amazon ECS cluster is configured with two or more instances\. Amazon ECS clusters must contain at least two instances so that one is maintained as the primary instance and another is used to accommodate new deployments\.

      For a tutorial about deploying container\-based applications with your pipeline, see [Tutorial: Continuous Deployment with CodePipeline](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cd-pipeline.html)\.
    + **Amazon ECS \(Blue/Green\)**

      Enter the CodeDeploy application and deployment group, Amazon ECS task definition, and AppSpec file information, and then choose **Next**\.

      For a tutorial about creating a pipeline for blue\-green deployments to an Amazon ECS cluster with CodeDeploy, see [Tutorial: Create a Pipeline with an Amazon ECR Source and ECS\-to\-CodeDeploy Deployment](tutorials-ecs-ecr-codedeploy.md)\.
    + **AWS Service Catalog**

      Choose **Enter deployment configuration** if you want to use fields in the console to specify your configuration, or choose **Configuration file** if you have a separate configuration file\. Enter product and configuration information, and then choose **Next**\.

      For a tutorial about deploying product changes to AWS Service Catalog with your pipeline, see [Tutorial: Create a Pipeline That Deploys to AWS Service Catalog](tutorials-S3-servicecatalog.md)\.
    + **Alexa Skills Kit**

      In **Alexa Skill ID**, enter the skill ID for your Alexa skill\. In **Client ID** and **Client secret**, enter the credentials generated using a Login with Amazon \(LWA\) security profile\. In **Refresh token**, enter the refresh token you generated using the ASK CLI command for retrieving a refresh token\. Choose **Next**\.

      For a tutorial about deploying Alexa skills with your pipeline and generating the LWA credentials, see [Tutorial: Create a Pipeline That Deploys an Amazon Alexa Skill](tutorials-alexa-skills-kit.md)\.
    + **Amazon S3**

      In **Bucket**, enter the name of the Amazon S3 bucket you want to use\. Choose **Extract file before deploy** if the input artifact to your deploy stage is a ZIP file\. If **Extract file before deploy** is selected, you may optionally enter a value for **Deployment path** to which your ZIP file will be unzipped\. If it is not selected, you are required to to enter a value in **S3 object key**\.
**Note**  
Most source and build stage output artifacts will be zipped\. All pipeline source providers except Amazon S3 will zip your source files before providing them as the input artifact to the next action\.

      Choose **Next**\.

      For a tutorial about creating a pipeline with an Amazon S3 deployment action provider, see [Tutorial: Create a Pipeline That Uses Amazon S3 as a Deployment Provider](tutorials-s3deploy.md)\.

**Step 5: Review the pipeline**
+ On the **Step 5: Review** page, review your pipeline configuration, and then choose **Create pipeline** to create the pipeline or **Previous** to go back and edit your choices\. To exit the wizard without creating a pipeline, choose **Cancel**\.

Now that you've created your pipeline, you can view it in the console\. The pipeline starts to run after you create it\. For more information, see [View Pipeline Details and History in CodePipeline](pipelines-view.md)\. For more information about making changes to your pipeline, see [Edit a Pipeline in CodePipeline](pipelines-edit.md)\.

## Create a Pipeline \(CLI\)<a name="pipelines-create-cli"></a>

To use the AWS CLI to create a pipeline, you create a JSON file to define the pipeline structure, and then run the create\-pipeline command with the `--cli-input-json` parameter\. 

**Important**  
You cannot use the AWS CLI to create a pipeline that includes partner actions\. You must use the CodePipeline console instead\.

For more information about pipeline structure, see [CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md) and [create\-pipeline](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/create-pipeline.html) in the CodePipeline [API Reference](http://docs.aws.amazon.com/cli/latest/reference/codepipeline)\.

To create a JSON file, use the sample pipeline JSON file, edit it, and then call that file when you run the create\-pipeline command\.

**Prerequisites:**

You need the ARN of the service role you created for CodePipeline in [Getting Started with CodePipeline](getting-started-codepipeline.md)\. You use the CodePipeline service role ARN in the pipeline JSON file when you run the create\-pipeline command\. For more information about creating a service role, see [Create the CodePipeline Service Role](pipelines-create-service-role.md)\. Unlike the console, running the create\-pipeline command in the AWS CLI does not have the option to create the CodePipeline service role for you\. The service role must already exist\.

You need the name of an Amazon S3 bucket where artifacts for the pipeline are stored\. This bucket must be in the same Region as the pipeline\. You use the bucket name in the pipeline JSON file when you run the create\-pipeline command\. Unlike the console, running the create\-pipeline command in the AWS CLI does not create an Amazon S3 bucket for storing artifacts\. The bucket must already exist\.

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

   CodePipeline uses Amazon CloudWatch Events to detect changes in your CodeCommit source repository and branch or your Amazon S3 source bucket\. CodePipeline uses webhooks to detect changes in your GitHub source repository and branch\. The next step includes instructions to manually create these resources for your pipeline\. Setting the flag to `false` disables periodic checks, which are not necessary when you are using the recommended change detection methods\. 

1. To create a build, test, or deploy action in a Region different from your pipeline, you must add the following to your pipeline structure\. For instructions, see [Add a Cross\-Region Action in CodePipeline](actions-create-cross-region.md)\.
   + Add the `Region` parameter to your action's pipeline structure\.
   + Use the `artifactStores` parameter to specify an artifact bucket for each AWS Region where you have an action\.

1. When you are satisfied with its structure, save your file with a name like **pipeline\.json**\.

**To create a pipeline**

1. Run the create\-pipeline command and use the `--cli-input-json` parameter to specify the JSON file you created previously\.

   To create a pipeline named *MySecondPipeline* with a JSON file named pipeline\.json that includes the name "*MySecondPipeline*" as the value for `name` in the JSON, your command would look like the following:

   ```
   aws codepipeline create-pipeline --cli-input-json file://pipeline.json
   ```
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   This command returns the structure of the entire pipeline you created\.

1. To view the pipeline, either open the CodePipeline console and choose it from the list of pipelines, or use the get\-pipeline\-state command\. For more information, see [View Pipeline Details and History in CodePipeline](pipelines-view.md)\. 

1. If you use the CLI to create a pipeline, you must manually create the recommended change detection resources for your pipeline:
   + For a pipeline with a CodeCommit repository, you must manually create the CloudWatch Events rule, as described in [Create a CloudWatch Events Rule for a CodeCommit Source \(CLI\)](pipelines-trigger-source-repo-changes-cli.md)\.
   + For a pipeline with an Amazon S3 source, you must manually create the CloudWatch Events rule and AWS CloudTrail trail, as described in [Use CloudWatch Events to Start a Pipeline \(Amazon S3 Source\)](create-cloudtrail-S3-source.md)\.
   + For a pipeline with a GitHub source, you must manually create the webhook, as described in [Use Webhooks to Start a Pipeline \(GitHub Source\)](pipelines-webhooks.md)\.