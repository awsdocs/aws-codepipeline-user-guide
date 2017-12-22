# Create a Pipeline in AWS CodePipeline<a name="pipelines-create"></a>

You can create a pipeline by using the AWS CodePipeline console or by using the AWS CLI\.

You can also create pipelines that build and deploy container\-based applications by using Amazon ECS as the deployment provider\. Before you create a pipeline that deploys container\-based applications with Amazon ECS, you must prepare an image definitions file\.


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

Before you create your pipeline, use the steps below to set up the image definitions file\.

1. As part of planning the container\-based application deployment for your pipeline, plan the source stage and the build stage, if applicable\.

1. Choose one of the following:

   1.  If your pipeline has no build stage, you must manually create the JSON file and upload it to your source repository so the source action can provide the artifact\. Create the file using a text editor, and name the file or use the default `imagedefinitions.json` filename\. Push the image definitions file to your source repository\.
**Note**  
Remember to zip the JSON file if your source repository is an Amazon S3 bucket\.

   1. If your pipeline has a build stage, add a command to your build spec file that outputs the image definitions file in your source repository during the build phase\. The example below uses the printf command to create an imagedefinitions\.json file\. List this command in the `post_build` section of the buildspec\.yml file:

      ```
      printf '[{"name":"container_name","imageUri":"image_URI"}]' >
      imagedefinitions.json
      ```

      You must include the image definitions file as an output artifact in the buildspec\.yml file\.

1. When you create your pipeline in the console, you must enter the exact image definitions filename into the **Image Filename **field on the **Deploy** screen of the Create Pipeline wizard\.

For a step\-by\-step tutorial for creating a pipeline that uses Amazon ECS as the deployment provider, see [Tutorial: Continuous Deployment with AWS CodePipeline](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cd-pipeline.html)\.


+ [Create Image Definitions File for Deploying Container\-based Applications](#pipelines-create-image-definitions)
+ [Create a Pipeline \(Console\)](#pipelines-create-console)
+ [Create a Pipeline \(CLI\)](#pipelines-create-cli)

## Create a Pipeline \(Console\)<a name="pipelines-create-console"></a>

To create a pipeline in the console, you'll need to provide the source file location and information about the providers you will use for your actions\.

When you use the console to create a pipeline, you must include a Source stage and one or both of the following: 

+ A Build stage\.

+ A Staging \(deployment\) stage\. 

When you use the pipeline wizard, AWS CodePipeline creates the names of stages \(Source, Build, Staging\)\. These names cannot be changed\. You can give more specific names \(for example, BuildToGamma or DeployToProd\) to stages you add later\.

AWS CodePipeline uses Amazon CloudWatch Events to detect changes in your AWS CodeCommit source repository and branch\. Using Amazon CloudWatch Events to automatically start your pipeline when changes occur is the default for this source type\. 

**Note**  
When you use the console to create a pipeline that has an AWS CodeCommit source repository, the rule is created for you\. If you use the AWS CLI to create the pipeline, you must create the Amazon CloudWatch Events rule and IAM role manually\. For more information, see [Start a Pipeline Automatically Using Amazon CloudWatch Events](triggering.md)\.

**To create a pipeline**

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codepipeline](http://console.aws.amazon.com/codepipeline)\.

1. On the **Welcome** page, choose **Create pipeline**\. 

   If this is your first time using AWS CodePipeline, an introductory page appears instead of **Welcome**\. Choose **Get Started Now**\.

1. On the **Step 1: Name** page, in the **Pipeline name** box, type the name for your pipeline, and then choose **Next step**\.

   Within a single AWS account, each pipeline you create in a region must have a unique name\. Names can be reused for pipelines in different regions\.
**Note**  
After you create a pipeline, you cannot change its name\. For information about other limitations, see [Limits in AWS CodePipeline](limits.md)\.

1. On the **Step 2: Source** page, in the **Source provider** drop\-down list, choose the type of repository where your source code is stored and specify its required options:

   + **GitHub**: Choose **Connect to GitHub**\. If prompted to sign in, provide your GitHub credentials\. 
**Important**  
Do not provide your AWS credentials\.

     If this is your first time connecting to GitHub from AWS CodePipeline, you will be asked to authorize application access to your account\. Review the permissions required for integration, and then, if you want to continue, choose **Authorize application**\. For more information about how AWS CodePipeline integrates with GitHub, see [Source Action Integrations](integrations-action-type.md#integrations-source)\.
**Note**  
There is a limit to the number of OAuth tokens you can use in GitHub for a particular application, such as AWS CodePipeline\. Within a single AWS account, AWS CodePipeline will automatically update existing equivalent OAuth tokens to in an attempt to avoid exceeding this limit\. If you exceed this limit as a result of connecting many AWS accounts with the same GitHub user account, you can use personal tokens\. For more information, see [To configure a pipeline to use a personal access token from GitHub](troubleshooting.md#troubleshooting-gs2)\.

     On the **Source** page in the **Create Pipeline** wizard, from the **Repository** drop\-down list, choose the GitHub repository you want to use as the source location for your pipeline\. In **Branch**, from the drop\-down list, choose the branch you want to use, and then choose **Next step**\.

   + **Amazon S3**: In **Amazon S3 location**, provide the Amazon S3 bucket name and path to the object in a bucket with versioning enabled, and then choose **Next step**\. The format of the bucket name and path will look something like this:

     ```
      s3://bucketName/folderName/objectName
     ```

   + **AWS CodeCommit**: In **Repository name**, choose the name of the AWS CodeCommit repository you want to use as the source location for your pipeline\. In **Branch name**, from the drop\-down list, choose the branch you want to use\.

     After you choose the AWS CodeCommit repository name and branch, a message is displayed in **Change detection options** showing the Amazon CloudWatch Events rule that will be created for this pipeline\. Accept the defaults under **Change detection options**\. This allows AWS CodePipeline to use Amazon CloudWatch Events to detect changes for your new pipeline\. Choose **Next step**\.
**Note**  
The object and file type must be compatible with the deployment system you plan to use, for example Elastic Beanstalk or AWS CodeDeploy\. Example supported file types might include \.zip, \.tar, and \.tgz files\. For more information about the supported container types for Elastic Beanstalk, see [Customizing and Configuring Elastic Beanstalk Environments](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-containers.html) and [Supported Platforms](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.platforms.html)\. For more information about deploying revisions with AWS CodeDeploy, see [Uploading Your Application Revision](http://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-steps.html#deployment-steps-uploading-your-app) and [Prepare a Revision](http://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-prepare-revision.html)\.

1. On the **Step 3: Build** page, do one of the following, and then choose **Next step**:

   + Choose **No Build** to skip the configuration of a build stage\. 

   + Choose a custom action provider of build services that you want to use, and provide the configuration details for that provider\.
**Note**  
The steps for adding a build provider vary by provider\. For an example of how to add Jenkins as a build provider, see [Tutorial: Create a Four\-Stage Pipeline](tutorials-four-stage-pipeline.md)\.

   + Choose AWS CodeBuild, and then either choose **Select an existing build project **or **Create a new build project**\. 

     + For an existing build project, in **Project name**, choose the name of the build project, and then choose **Save build project**\.

     + For a new build project, make sure you’ve completed the steps in [Plan a Build](http://docs.aws.amazon.com/codebuild/latest/userguide/planning.html), and then follow the instructions in [Create a Pipeline that Uses AWS CodeBuild](http://docs.aws.amazon.com/codebuild/latest/userguide/how-to-create-pipeline.html#pipelines-create-console) in *[AWS CodeBuild User Guide](http://docs.aws.amazon.com/codebuild/latest/userguide/)*\.
**Note**  
Make sure that the service role for AWS CodePipeline has appropriate permissions for AWS CodeBuild\. For more information, see [Add Permissions for Other AWS Services](how-to-custom-role.md#how-to-update-role-new-services)\.

1. On the **Step 4: Deploy** page, do one of the following, and then choose **Next step**:

   + Choose **No Deployment** to skip the configuration of a deployment stage\.
**Note**  
You can skip adding a deployment provider now only if you chose a build provider in the previous step\.

   + Choose a custom action that you have created for a deployment provider\.

   + Choose one of the following default providers from the **Deployment provider** drop\-down list:

     + **AWS CodeDeploy**: Type or choose the name of an existing AWS CodeDeploy application in **Application name** and the name of a deployment group for that application in **Deployment group**, and then choose **Next step**\. Alternatively, you can create an application, deployment group, or both by choosing those links in AWS CodeDeploy\.

     + **AWS Elastic Beanstalk**: Type or choose the name of an existing Elastic Beanstalk application in **Application name** and an environment for that application in **Environment name**, and then choose **Next step**\. Alternatively, you can create an application, environment, or both by choosing those links in Elastic Beanstalk\.

     + **AWS OpsWorks Stacks**: Type or choose the name of the stack you want to use in **Stack** and the layer that your target instances belong to in **Layer**\. In **App**, choose the application that you want to update and deploy\. If you need to create an app, choose **create a new one in AWS OpsWorks**\. 

       For information about adding an application to a stack and layer in AWS OpsWorks, see [Adding Apps](http://docs.aws.amazon.com/opsworks/latest/userguide/workingapps-creating.html) in the *AWS OpsWorks User Guide*\. 

       For an end\-to\-end example of how to use a simple pipeline in AWS CodePipeline as the source for code that you run on AWS OpsWorks layers, see [Using AWS CodePipeline with AWS OpsWorks Stacks](http://docs.aws.amazon.com/opsworks/latest/userguide/other-services-cp.html)\.

     + **AWS CloudFormation**: Do one of the following:

       + In **Action mode**, choose **Create or update a stack**, enter a stack name and template file name, and then choose the name of a role for AWS CloudFormation to assume\. Optionally, enter the name of a configuration file and choose an IAM capability option\.

       + In **Action mode**, choose **Create or replace a change set**, enter a stack name and change set name, and then choose the name of a role for AWS CloudFormation to assume\. Optionally, enter the name of a configuration file and choose an IAM capability option\.

       For detailed information about integrating AWS CloudFormation capabilities into a pipeline in AWS CodePipeline, see [Continuous Delivery with AWS CodePipeline](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline.html) in *AWS CloudFormation User Guide*\.

     + **Amazon ECS**: In **Cluster name**, type or choose the name of an existing Amazon ECS cluster\. In **Service name**, type or choose the name of the service running on the cluster\. Alternatively, you can create a new cluster and service\. In **Image filename**, type the name of the image definitions file that describes your service's container and image\. Choose **Next step**\.
**Note**  
Make sure your Amazon ECS cluster is configured with two or more instances\. Amazon ECS clusters must contain at least two instances, in order to maintain one **Primary** instance and another instance to accommodate new deployments\.

       For a tutorial about deploying container\-based applications with your pipeline, see [Tutorial: Continuous Deployment with AWS CodePipeline](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cd-pipeline.html)\.
**Note**  
After a pipeline is created successfully, you can edit it to add more deployment stages or actions to a deployment stage\.

1. On the **Step 5: Service Role** page, do one of the following, and then choose **Next step**:

   + In the **Service Role**, drop\-down list, choose an IAM service role you have set up for AWS CodePipeline\.

   + If you do not have a service role, choose **Create role**, and then on the IAM console page that describes the role that will be created for you, choose **Allow**\. On the **Step 5: Service Role** page, *AWS\-CodePipeline\-Service* will appear in the drop\-down box\.
**Note**  
Depending on when your service role was created, you may need to update its permissions to support additional AWS services\. For information, see [Add Permissions for Other AWS Services](how-to-custom-role.md#how-to-update-role-new-services)\. 

   For more information about the service role and its policy statement, see [Manage the AWS CodePipeline Service Role](how-to-custom-role.md)\.

1. On the **Step 6: Review** page, review your pipeline configuration, and then choose **Create pipeline** to create the pipeline or **Previous** to go back and edit your choices\. To exit the wizard without creating a pipeline, choose **Cancel**\.

Now that you've created your pipeline, you can view it in the console\. The pipeline will start to run automatically after you create it\. For more information, see [View Pipeline Details and History in AWS CodePipeline](pipelines-view.md)\. You can also make changes to the pipeline\. For more information, see [Edit a Pipeline in AWS CodePipeline](pipelines-edit.md)\.

## Create a Pipeline \(CLI\)<a name="pipelines-create-cli"></a>

To use the AWS CLI to create a pipeline, you create a JSON file to define the pipeline structure, and then run the create\-pipeline command with the `--cli-input-json` parameter\. 

The simplest way to create the JSON file is to start with an existing pipeline\. You can use the get\-pipeline command to get a copy of the JSON structure of that pipeline, and then modify that structure in a plain\-text editor\. For more information about pipeline structure, see [AWS CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md) and [create\-pipeline](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/create-pipeline.html) in the AWS CodePipeline [API Reference](http://docs.aws.amazon.com/cli/latest/reference/codepipeline)\.

**Note**  
If you don’t already have a pipeline, you can use the wizard in the AWS CodePipeline console to create a pipeline, and then use that JSON structure as the basis for your pipeline\.

You will need the ARN of the service role you created for AWS CodePipeline in [Getting Started with AWS CodePipeline](getting-started-codepipeline.md) and the name of an Amazon S3 bucket where artifacts for the pipeline will be stored\. This bucket must be in the same region as the pipeline\. You will use both when you run the create\-pipeline command\. Unlike the console, the create\-pipeline command in the AWS CLI cannot create an Amazon S3 bucket for storing artifacts\. The bucket must already exist\.

**Important**  
You cannot use the AWS CLI to create a pipeline that includes partner actions\. To create a pipeline that includes partner actions, use the AWS CodePipeline console\.


+ [Create the JSON file](#pipelines-create-cli-json)
+ [Run the create\-pipeline Command](#pipelines-create-cli-command)

### Create the JSON file<a name="pipelines-create-cli-json"></a>

 To create a JSON file, use the get\-pipeline command to copy the structure of an existing pipeline to a file, edit it, and then call that file when running the create\-pipeline command\.

**To create the JSON file**

1. At a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), run the get\-pipeline command on a pipeline, and then copy the output of the command to a JSON file\. For example, for a pipeline named MyFirstPipeline, you would type something similar to the following:

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   The output of the command is sent to the *pipeline\.json* file\.

1. Open the file in a plain\-text editor and edit the values to reflect the structure you want to create\. At a minimum, you must change the name of the pipeline\. You should also consider whether you want to change the Amazon S3 bucket where artifacts for this pipeline are stored; the source location for your code; the deployment provider; how you want your code deployed; and other details\.

   The following two\-stage sample pipeline structure highlights the values you should consider changing for your pipeline\. Your pipeline will likely contain more than two stages:

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
                               "S3ObjectKey": "ExampleCodePipelineSampleBundle.zip"
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

   AWS CodePipeline uses Amazon CloudWatch Events to detect changes in your AWS CodeCommit source repository and branch\. Using Amazon CloudWatch Events to automatically start your pipeline when changes occur is recommended and is accomplished using these steps in the CLI: 

   1. Open the JSON file in a plain\-text editor and modify the file by setting the `PollForSourceChanges` parameter to false\. The flag is located in the source stage of the pipeline structure\.

      The following example shows how the parameter should look after the file is modified to disable periodic checks;

      ```
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
                      "provider": "CodeCommit"
                  },
                  "outputArtifacts": [
                      {
                          "name": "MyApp"
                      }
                  ],
                  "configuration": {
                      "PollForSourceChanges": "false",
                      "BranchName": "master",
                      "RepositoryName": "MyTestRepo"
                  },
                  "runOrder": 1
              }
          ]
      },
      ```

   1. After you create your pipeline, you must manually create the CloudWatch Events rule for change detection\. For more information on using the CLI to create the rule, see [Start a Pipeline Automatically Using Amazon CloudWatch Events](triggering.md)\.

1. When you are satisfied with its structure, save your file as with a name like **pipeline\.json**\.

### Run the create\-pipeline Command<a name="pipelines-create-cli-command"></a>

After you have a JSON file that contains the structure of your pipeline, call that file when you run the create\-pipeline command\.

**To create a pipeline**

1. Run the create\-pipeline command and use the `--cli-input-json` parameter to specify the JSON file you created previously\.

   To create a pipeline named *MySecondPipeline* with a JSON file named pipeline\.json that includes the name "*MySecondPipeline*" as the value for `name` in the JSON, your command would look similar to the following:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline create-pipeline --cli-input-json file://pipeline.json
   ```

   This command returns the structure of the entire pipeline you created\.

1. To view the pipeline you just created, either open the AWS CodePipeline console and choose it from the list of pipelines, or use the get\-pipeline\-state command\. For more information, see [View Pipeline Details and History in AWS CodePipeline](pipelines-view.md)\. 