# Tutorial: Create a pipeline that publishes your serverless application to the AWS Serverless Application Repository<a name="tutorials-serverlessrepo-auto-publish"></a>

You can use AWS CodePipeline to continuously deliver your AWS SAM serverless application to the AWS Serverless Application Repository\.

This tutorial shows how to create and configure a pipeline to build your serverless application that is hosted in GitHub and publish it to the AWS Serverless Application Repository automatically\. The pipeline uses GitHub as the source provider and CodeBuild as the build provider\. To publish your serverless application to the AWS Serverless Application Repository, you deploy an [application](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:077246666028:applications~aws-serverless-codepipeline-serverlessrepo-publish ) \(from the AWS Serverless Application Repository\) and associate the Lambda function created by that application as an Invoke action provider in your pipeline\. Then you can continuously deliver application updates to the AWS Serverless Application Repository, without writing any code\.

**Important**  
Many of the actions you add to your pipeline in this procedure involve AWS resources that you need to create before you create the pipeline\. AWS resources for your source actions must always be created in the same AWS Region where you create your pipeline\. For example, if you create your pipeline in the US East \(Ohio\) Region, your CodeCommit repository must be in the US East \(Ohio\) Region\.   
You can add cross\-region actions when you create your pipeline\. AWS resources for cross\-region actions must be in the same AWS Region where you plan to execute the action\. For more information, see [Add a cross\-Region action in CodePipeline](actions-create-cross-region.md)\.

## Before you begin<a name="tutorials-serverlessrepo-auto-publish-prereq"></a>

In this tutorial, we assume the following\. 
+ You are familiar with [AWS Serverless Application Model \(AWS SAM\)](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/) and the [AWS Serverless Application Repository](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/)\.
+ You have a serverless application hosted in GitHub that you have published to the AWS Serverless Application Repository using the AWS SAM CLI\. To publish an example application to the AWS Serverless Application Repository, see [Quick Start: Publishing Applications](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/serverlessrepo-quick-start.html) in the *AWS Serverless Application Repository Developer Guide*\. To publish your own application to the AWS Serverless Application Repository, see [Publishing Applications Using the AWS SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-template-publishing-applications.html) in the *AWS Serverless Application Model Developer Guide*\.

## Step 1: Create a buildspec\.yml file<a name="serverlessrepo-auto-publish-create-buildspec"></a>

Create a `buildspec.yml` file with the following contents, and add it to your serverless application's GitHub repository\. Replace *template\.yml* with your application's AWS SAM template and *bucketname* with the S3 bucket where your packaged application is stored\.

```
version: 0.2
phases:
  install:
    runtime-versions:
        python: 3.8
  build:
    commands:
      - pip install --upgrade pip
      - pip install pipenv --user
      - pipenv install awscli aws-sam-cli
      - pipenv run sam package --template-file template.yml --s3-bucket bucketname --output-template-file packaged-template.yml
artifacts:
  files:
    - packaged-template.yml
```

## Step 2: Create and configure your pipeline<a name="serverlessrepo-auto-publish-create-pipeline"></a>

Follow these steps to create your pipeline in the AWS Region where you want to publish your serverless application\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

1. If necessary, switch to the AWS Region where you want to publish your serverless application\.

1. Choose **Create pipeline**\. On the **Choose pipeline settings** page, in **Pipeline name**, enter the name for your pipeline\.

1. In **Service role**, choose **New service role** to allow CodePipeline to create a service role in IAM\.

1. Leave the settings under **Advanced settings** at their defaults, and then choose **Next**\.

1. On the **Add source stage** page, in **Source provider**, choose **GitHub**, and then choose **Connect to GitHub**\.

1. In the browser window, choose **Authorize aws\-codesuite**\. This allows your pipeline to make your repository a source, and to use webhooks that detect when new code is pushed to the repository\.

1. In **Repository**, choose your GitHub source repository\.

1. In **Branch**, choose your GitHub branch\.

1. Choose **Next**\.

1. On the **Add build stage** page, add a build stage:

   1. In **Build provider**, choose **AWS CodeBuild**\. For **Region**, use the pipeline Region\.

   1. Choose **Create project**\.

   1. In **Project name**, enter a name for this build project\.

   1. In **Environment image**, choose **Managed image**\. For **Operating system**, choose **Ubuntu**\.

   1. For **Runtime** and **Runtime version**, choose the runtime and version required for your serverless application\.

   1. For **Service role**, choose **New service role**\.

   1. For **Build specifications**, choose **Use a buildspec file**\.

   1. Choose **Continue to CodePipeline**\. This opens the CodePipeline console and creates a CodeBuild project that uses the `buildspec.yml` in your repository for configuration\. The build project uses a service role to manage AWS service permissions\. This step might take a couple of minutes\.

   1. Choose **Next**\.

1. On the **Add deploy stage** page, choose **Skip deploy stage**, and then accept the warning message by choosing **Skip** again\. Choose **Next**\.

1. Choose **Create pipeline**\. You should see a diagram that shows the source and build stages\.

1. Grant the CodeBuild service role permission to access the S3 bucket where your packaged application is stored\.

   1. In the **Build** stage of your new pipeline, choose **CodeBuild**\.

   1. Choose the **Build details** tab\.

   1. In **Environment**, choose the CodeBuild service role to open the IAM console\.

   1. Expand the selection for `CodeBuildBasePolicy`, and choose **Edit policy**\.

   1. Choose **JSON**\.

   1. Add a new policy statement with the following contents\. The statement allows CodeBuild to put objects into the S3 bucket where your packaged application is stored\. Replace *bucketname* with the name of your S3 bucket\.

      ```
              {
                  "Effect": "Allow",
                  "Resource": [
                      "arn:aws:s3:::bucketname/*"
                  ],
                  "Action": [
                      "s3:PutObject"
                  ]
              }
      ```

   1. Choose **Review policy**\.

   1. Choose **Save changes**\.

## Step 3: Deploy the publish application<a name="serverlessrepo-auto-publish-deploy-app"></a>

Follow these steps to deploy the application that contains the Lambda function that performs the publish to the AWS Serverless Application Repository\. This application is **aws\-serverless\-codepipeline\-serverlessrepo\-publish**\.

**Note**  
You must deploy the application to the same AWS Region as your pipeline\.

1. Go to the [application](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:077246666028:applications~aws-serverless-codepipeline-serverlessrepo-publish ) page, and choose **Deploy**\.

1. Select **I acknowledge that this app creates custom IAM roles**\. 

1. Choose **Deploy**\.

1. Choose **View AWS CloudFormation Stack** to open the AWS CloudFormation console\.

1. Expand the **Resources** section\. You see **ServerlessRepoPublish**, which is of the type **AWS::Lambda::Function**\. Make a note of the physical ID of this resource for the next step\. You use this physical ID when you create the new publish action in CodePipeline\.

## Step 4: Create the publish action<a name="serverlessrepo-auto-publish-create-action"></a>

Follow these steps to create the publish action in your pipeline\.

1. Open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

1. In the left navigation section, choose the pipeline that you want to edit\.

1. Choose **Edit**\.

1. After the last stage of your current pipeline, choose **\+ Add stage**\. In **Stage name** enter a name, such as **Publish**, and choose **Add stage**\.

1. In the new stage, choose **\+ Add action group**\.

1. Enter an action name\. From **Action provider**, in **Invoke**, choose **AWS Lambda**\.

1. From **Input artifacts**, choose **BuildArtifact**\.

1. From **Function name**, choose the physical ID of the Lambda function that you noted in the previous step\.

1. Choose **Save** for the action\.

1. Choose **Done** for the stage\.

1. In the upper right, choose **Save**\.

1. To verify your pipeline, make a change to your application in GitHub\. For example, change the application's description in the `Metadata` section of your AWS SAM template file\. Commit the change and push it to your GitHub branch\. This triggers your pipeline to run\. When the pipeline is complete, check that your application has been updated with your change in the [AWS Serverless Application Repository](https://console.aws.amazon.com/serverlessrepo/home)\.