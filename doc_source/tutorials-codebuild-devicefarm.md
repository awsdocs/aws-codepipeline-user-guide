--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Tutorial: Create a Pipeline That Builds and Tests Your Android App When a Commit Is Pushed to Your GitHub Repository<a name="tutorials-codebuild-devicefarm"></a>

 You can use AWS CodePipeline to configure a continuous integration flow in which your app is built and tested each time a commit is pushed to its repository\. This tutorial shows how to create and configure a pipeline to build and test your Android app with source code in a GitHub repository\. The pipeline detects the arrival of a new commit through [webhooks that AWS CodePipeline configures for your GitHub repository](https://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-webhooks-migration.html), and then uses [AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html) to build the app and [Device Farm](https://docs.aws.amazon.com/devicefarm/latest/developerguide/welcome.html) to test it\. 

**Important**  
Make sure that you create all of the AWS resources for this procedure in the same AWS Region where you create your pipeline\. For example, if you create your pipeline in the US East \(Ohio\) Region, your Amazon S3 bucket or AWS CodeCommit repository, AWS CodeDeploy resources, and Amazon EC2 instance key pair must also be in the US East \(Ohio\) Region\.  
Later, you can use the AWS CLI to add a cross\-region build, test, or deploy action\.

You can try this out using your existing Android app and test definitions, or you can use the [sample app and test definitions provided by Device Farm](https://github.com/aws-samples/aws-device-farm-sample-app-for-android)\.

![\[The Step 2: Source page in the AWS CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-push-build-test-GitHub.png)![\[The Step 2: Source page in the AWS CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 2: Source page in the AWS CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)


****  

|  |  |  |  |  | 
| --- |--- |--- |--- |--- |
| Configure | Add definitions | Push | Build and Test | Report | 
| Configure pipeline resources | Add build and test definitions to your package | Push a package to your repository | App build and test build output artifact kicked off automatically | View test results | 

## Configure AWS CodePipeline to Use Your Device Farm Tests<a name="codepipeline-configure-tests"></a>

1. Add and commit a file called [https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html) in the root of your app code, and push it to your repository\. AWS CodeBuild uses this file to perform commands and access artifacts required to build your app\.

   ```
   version: 0.2
   
   phases:
     build:
       commands:
         - chmod +x ./gradlew
         - ./gradlew assembleDebug
   artifacts:
     files:
        - './android/app/build/outputs/**/*.apk'
     discard-paths: yes
   ```

1. \(Optional\) If you [use Calabash or Appium to test your app](https://docs.aws.amazon.com/devicefarm/latest/developerguide/test-types-intro.html), add the test definition file to your repository\. In a later step, you can configure Device Farm to use the definitions to carry out your test suite\. 

   If you use Device Farm built\-in tests, you can skip this step\.

1. To create your build project in AWS CodeBuild, do the following:

   1. Sign in to the AWS Management Console and open the AWS CodeBuild console\.

   1. If a welcome page is displayed, choose **Get started**\.

      If a welcome page is not displayed, on the navigation pane, choose **Build projects**, and then choose **Create build project**\.

   1. In **Project name**, enter a name for this build project\.

   1. In **Source provider**, choose **AWS CodePipeline**\.

   1. In **Environment**, choose **Managed image**\. For **Operating system**, choose **Ubuntu**\.

   1. For **Runtime**, choose **Android**\. For **Version**, choose **aws/codebuild/android\-java\-8:26\.1\.1**\. AWS CodeBuild uses this OS image, which has Android Studio installed, to build your app\.

   1. For **Service role**, choose your AWS CodeBuild service role\.

   1. For **Build specifications**, choose **Use the buildspec\.yml in the source code root directory**\.

   1. Choose **Create build project**\. This creates an AWS CodeBuild project that uses the `buildspec.yml` in your repository for configuration\. The build project uses a service role to manage AWS service permissions\. This step might take a couple of minutes\.

1. To create your pipeline and add a source stage, do the following:

   1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   1. Choose **Create pipeline**\. On the **Step 1: Choose pipeline settings** page, in **Pipeline name**, enter the name for your pipeline\.

   1. In **Service role**, leave **New service role** selected, and leave **Role name** unchanged\. You can also choose to use an existing service role, if you have one\.
**Note**  
If you use an AWS CodePipeline service role that was created before July 2018, you need to add permissions for Device Farm\. To do this, open the IAM console, find the role, and then add the following permissions to the role's policy\. For more information, see [Add Permissions for Other AWS Services](how-to-custom-role.md#how-to-update-role-new-services)\.  

      ```
      {
           "Effect": "Allow",
           "Action": [
              "devicefarm:ListProjects",
              "devicefarm:ListDevicePools",
              "devicefarm:GetRun",
              "devicefarm:GetUpload",
              "devicefarm:CreateUpload",
              "devicefarm:ScheduleRun"
           ],
           "Resource": "*"
      }
      ```

   1. In **Artifact location**, do one of the following: 

      1. Choose **Default location** to use the default artifact store, such as the Amazon S3 artifact bucket designated as the default, for your pipeline in the region you have selected for your pipeline\.

      1. Choose **Custom location** if you already have an existing artifact store you have created, such as an Amazon S3 artifact bucket, in the same region as your pipeline\.
**Note**  
This is not the source bucket for your pipeline's source code\. This is the artifact store for your pipeline\. A separate artifact store, such as an Amazon S3 bucket, is required for each pipeline, in the same region as the pipeline\.

   1. Choose **Next**\.

   1. On the **Step 2: Add source stage** page, in **Source provider**, choose **GitHub**, and then choose **Connect to GitHub**\.

   1. In the browser window, choose **Authorize aws\-codesuite**\. This allows your pipeline to make your repository a source, and to use webhooks that detect when new code is pushed to the repository\.

   1. In **Repository**, choose the source repository\.

   1. In **Branch**, choose the branch that you want to use\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-add-source.png)

   1. Choose **Next**\.

1. In **Build**, add a build stage:

   1. In **Build provider**, choose **AWS CodeBuild**\.

   1. In **AWS CodeBuild**, choose **Project name**, and then enter a name for your project\.

   1. Choose **Next**\.

   1. In **Deploy provider**, choose **Skip**, accept the message by choosing **Skip**, and then choose **Next**\.

   1. On **Step 5: Review**, choose **Create pipeline**\. You should see a diagram that shows the source and build stages\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-view-pipeline.png)

1. Add a Device Farm test action to your pipeline:

   1. In the upper left, choose **Edit**\.

   1. At the bottom of the diagram, choose **\+ Add stage**\.

   1. Enter a stage name, and then choose **\+ Add action group**\.

   1. In **Action name**, enter a name\. 

   1. In **Action provider**, choose **AWS Device Farm**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-add-action-pol.png)

   1. In **ProjectId**, choose your existing Device Farm project or choose **Create a new project**\. 

   1. In **DevicePoolArn**, choose your existing device pool\. If you create a device pool, you must select a set of test devices\.

   1. In **App type**, choose **Android**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-choose-test-provider.png)

   1. In **App file path**, enter the path of the compiled app package\. The path is relative to the root of the input artifact for the test stage\. Typically, this path is similar to `app-release.apk`\.

   1. In **Test type**, do one of the following:
      + If you're using one of the built\-in Device Farm tests, choose the type of test configured in your Device Farm project\.
      + If you aren't using one of the built\-in Device Farm tests, choose your type of test, and then in **Test file path**, enter the path of the test definition file\. The path is relative to the root of the input artifact for your test\.   
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-test-type.png)

   1. In the remaining fields, provide the configuration that is appropriate for your test and application type\.

   1. \(Optional\) In **Advanced**, provide configuration information for your test run\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-advanced.png)

   1. In **Input artifacts**, choose the input artifact that matches the output artifact of the stage that comes before the test stage\.   
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-input-artifact.png)

      In the AWS CodePipeline console, you can find the name of the output artifact for each stage by hovering over the information icon in the pipeline diagram\. If your pipeline tests your app directly from the **Source** stage, choose **MyApp**\. If the pipeline includes a **Build** stage, choose **MyAppBuild**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-output-artifact.png)

   1. At the bottom of the panel, choose **Add Action**\.

   1. In the AWS CodePipeline pane, choose **Save pipeline change**, and then choose **Save change**\.

   1. To submit your changes and start a pipeline build, choose **Release change**, and then choose **Release**\.