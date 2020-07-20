# Tutorial: Create a pipeline that builds and tests your Android app when a commit is pushed to your GitHub repository<a name="tutorials-codebuild-devicefarm"></a>

 You can use AWS CodePipeline to configure a continuous integration flow in which your app is built and tested each time a commit is pushed to its repository\. This tutorial shows how to create and configure a pipeline to build and test your Android app with source code in a GitHub repository\. The pipeline detects the arrival of a new commit through [webhooks that CodePipeline configures for your GitHub repository](https://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-webhooks-migration.html), and then uses [CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html) to build the app and [Device Farm](https://docs.aws.amazon.com/devicefarm/latest/developerguide/welcome.html) to test it\. 

**Important**  
Many of the actions you add to your pipeline in this procedure involve AWS resources that you need to create before you create the pipeline\. AWS resources for your source actions must always be created in the same AWS Region where you create your pipeline\. For example, if you create your pipeline in the US East \(Ohio\) Region, your CodeCommit repository must be in the US East \(Ohio\) Region\.   
You can add cross\-region actions when you create your pipeline\. AWS resources for cross\-region actions must be in the same AWS Region where you plan to execute the action\. For more information, see [Add a cross\-Region action in CodePipeline](actions-create-cross-region.md)\.

You can try this out using your existing Android app and test definitions, or you can use the [sample app and test definitions provided by Device Farm](https://github.com/aws-samples/aws-device-farm-sample-app-for-android)\.

![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-push-build-test-GitHub.png)![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)


****  

|  |  |  |  |  | 
| --- |--- |--- |--- |--- |
| Configure | Add definitions | Push | Build and Test | Report | 
| Configure pipeline resources | Add build and test definitions to your package | Push a package to your repository | App build and test build output artifact kicked off automatically | View test results | 

**Before you begin**

1. Sign in to the AWS Device Farm console and choose **Create a new project**\.

1. Choose your project\. In the browser, copy the URL of your new project\. The URL contains the project ID\. 

1. Copy and retain this project ID\. You use it when you create your pipeline in CodePipeline\.

   Here is an example URL for a project\. To extract the project ID, copy the value after `projects/`\. In this example, the project ID is `eec4905f-98f8-40aa-9afc-4c1cfexample`\.

   ```
   https://<region-URL>/devicefarm/home?region=us-west-2#/projects/eec4905f-98f8-40aa-9afc-4c1cfexample/runs
   ```

## Configure CodePipeline to use your Device Farm tests<a name="codepipeline-configure-tests"></a>

1. Add and commit a file called [https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html) in the root of your app code, and push it to your repository\. CodeBuild uses this file to perform commands and access artifacts required to build your app\.

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

1. To create your pipeline and add a source stage, do the following:

   1. Sign in to the AWS Management Console and open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   1. Choose **Create pipeline**\. On the **Step 1: Choose pipeline settings** page, in **Pipeline name**, enter the name for your pipeline\.

   1. In **Service role**, leave **New service role** selected, and leave **Role name** unchanged\. You can also choose to use an existing service role, if you have one\.
**Note**  
If you use a CodePipeline service role that was created before July 2018, you need to add permissions for Device Farm\. To do this, open the IAM console, find the role, and then add the following permissions to the role's policy\. For more information, see [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services)\.  

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

   1. Leave the settings under **Advanced settings** at their defaults, and then choose **Next**\.

   1. On the **Step 2: Add source stage** page, in **Source provider**, choose **GitHub**, and then choose **Connect to GitHub**\.

   1. In the browser window, choose **Authorize aws\-codesuite**\. This allows your pipeline to make your repository a source, and to use webhooks that detect when new code is pushed to the repository\.

   1. In **Repository**, choose the source repository\.

   1. In **Branch**, choose the branch that you want to use\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-add-source.png)

   1. Choose **Next**\.

1. In **Add build stage**, add a build stage:

   1. In **Build provider**, choose **AWS CodeBuild**\. Allow **Region** to default to the pipeline Region\.

   1. Choose **Create project**\.

   1. In **Project name**, enter a name for this build project\.

   1. In **Environment image**, choose **Managed image**\. For **Operating system**, choose **Ubuntu**\.

   1. For **Runtime**, choose **Standard**\. For **Image**, choose **aws/codebuild/standard:1\.0**\.

      CodeBuild uses this OS image, which has Android Studio installed, to build your app\.

   1. For **Service role**, choose your existing CodeBuild service role or create a new one\.

   1. For **Build specifications**, choose **Use a buildspec file**\.

   1. Choose **Continue to CodePipeline**\. This returns to the CodePipeline console and creates a CodeBuild project that uses the `buildspec.yml` in your repository for configuration\. The build project uses a service role to manage AWS service permissions\. This step might take a couple of minutes\.

   1. Choose **Next**\.

1. On the **Step 4: Add deploy stage** page, choose **Skip deploy stage**, and then accept the warning message by choosing **Skip** again\. Choose **Next**\.

1. On **Step 5: Review**, choose **Create pipeline**\. You should see a diagram that shows the source and build stages\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-view-pipeline.png)

1. Add a Device Farm test action to your pipeline:

   1. In the upper right, choose **Edit**\.

   1. At the bottom of the diagram, choose **\+ Add stage**\. In **Stage name**, enter a name, such as **Test**\.

   1. Choose **\+ Add action group**\.

   1. In **Action name**, enter a name\. 

   1. In **Action provider**, choose **AWS Device Farm**\. Allow **Region** to default to the pipeline Region\.

   1. In **Input artifacts**, choose the input artifact that matches the output artifact of the stage that comes before the test stage, such as `BuildArtifact`\. 

      In the AWS CodePipeline console, you can find the name of the output artifact for each stage by hovering over the information icon in the pipeline diagram\. If your pipeline tests your app directly from the **Source** stage, choose **SourceArtifact**\. If the pipeline includes a **Build** stage, choose **BuildArtifact**\.

   1. In **ProjectId**, enter your Device Farm project ID\. Use the steps at the start of this tutorial to retrieve your project ID\.

   1. In **DevicePoolArn**, enter the ARN for the device pool\.

   1. In **AppType**, enter **Android**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-choose-test-provider.png)

      The following is a list of valid values for **AppType**:
      + **iOS**
      + **Android**
      + **Web**

   1. In **App**, enter the path of the compiled app package\. The path is relative to the root of the input artifact for the test stage\. Typically, this path is similar to `app-release.apk`\.

   1. In **TestType**, enter your type of test, and then in **Test**, enter the path of the test definition file\. The path is relative to the root of the input artifact for your test\.

      The following is a list of valid values for **TestType**:
      + **APPIUM\_JAVA\_JUNIT**
      + **APPIUM\_JAVA\_TESTNG**
      + **APPIUM\_PYTHON**
      + **APPIUM\_WEB\_JAVA\_JUNIT**
      + **APPIUM\_WEB\_JAVA\_TESTNG**
      + **APPIUM\_WEB\_PYTHON**
      + **BUILTIN\_EXPLORER**
      + **BUILTIN\_FUZZ**
      + **CALABASH**
      + **INSTRUMENTATION**
      + **UIAUTOMATION**
      + **UIAUTOMATOR**
      + **WEB\_PERFORMANCE\_PROFILE**
      + **XCTEST**
      + **XCTEST\_UI**
**Note**  
Custom environment nodes are not supported\.

   1. In the remaining fields, provide the configuration that is appropriate for your test and application type\.

   1. \(Optional\) In **Advanced**, provide configuration information for your test run\.

   1. Choose **Save**\.

   1. On the stage you are editing, choose **Done**\. In the AWS CodePipeline pane, choose **Save**, and then choose **Save** on the warning message\.

   1. To submit your changes and start a pipeline build, choose **Release change**, and then choose **Release**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-android-final-view-pipeline.png)