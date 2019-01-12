--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Tutorial: Create a Pipeline That Builds and Tests Your iOS App After a Change in Your Amazon S3 Bucket<a name="tutorials-codebuild-devicefarm-S3"></a>

 You can use AWS CodePipeline to easily configure a continuous integration flow in which your app is tested each time the source bucket changes\. This tutorial shows you how to create and configure a pipeline to test your built iOS app from an Amazon S3 bucket\. The pipeline detects the arrival of a saved change through Amazon CloudWatch Events, and then uses [Device Farm](https://docs.aws.amazon.com/devicefarm/latest/developerguide/welcome.html) to test the built application\. 

**Important**  
Make sure that you create all of the AWS resources for this procedure in the same AWS Region where you create your pipeline\. For example, if you create your pipeline in the US East \(Ohio\) Region, your Amazon S3 bucket or AWS CodeCommit repository, AWS CodeDeploy resources, and Amazon EC2 instance key pair must also be in the US East \(Ohio\) Region\.  
Later, you can use the AWS CLI to add a cross\-region build, test, or deploy action\.

You can try this out using your existing iOS app, or you can use the [sample iOS app](samples/s3-ios-test-1.zip)\.

![\[The Step 2: Source page in the AWS CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-push-build-test-S3.png)![\[The Step 2: Source page in the AWS CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 2: Source page in the AWS CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)


****  

|  |  |  |  |  | 
| --- |--- |--- |--- |--- |
| Configure | Add definitions | Upload | Test | Report | 
| Configure pipeline resources | Add test definitions to your package | Upload \.zip to your bucket | Test output artifact kicked off automatically | View test results | 

## Configure AWS CodePipeline to Use Your Device Farm Tests \(Amazon S3 Example\)<a name="codepipeline-configure-tests-S3"></a>

1. Create or use an Amazon S3 bucket with versioning enabled\. Follow the instructions in [Step 1: Create an Amazon S3 Bucket for Your Application](tutorials-simple-s3.md#s3-create-s3-bucket) to create an Amazon S3 bucket\.

1. In the Amazon S3 console for your bucket, choose **Upload**, and follow the instructions to upload your \.zip file\.

   Your sample application must be packaged in a \.zip file\.

1. To create your pipeline and add a source stage, do the following:

   1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   1. Choose **Create pipeline**\. On the **Step 1: Choose pipeline settings** page, in **Pipeline name**, enter the name for your pipeline\.

   1. In **Service role**, leave **New service role** selected, and leave **Role name** unchanged\. You can also choose to use an existing service role, if you have one\.
**Note**  
If you use an AWS CodePipeline service role that was created before July 2018, you must add permissions for Device Farm\. To do this, open the IAM console, find the role, and then add the following permissions to the role's policy\. For more information, see [Add Permissions for Other AWS Services](how-to-custom-role.md#how-to-update-role-new-services)\.  

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

   1. On the **Step 2: Add source stage** page, in **Source provider**, choose **Amazon S3**\.

   1. In **Amazon S3 location**, enter the bucket and object key for your \.zip file\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-add-source-S3.png)

   1. Choose **Next**\.

1. In **Build**, create a placeholder build stage for your pipeline\. This allows you to create the pipeline in the wizard\. After you use the wizard to create your two\-stage pipeline, you no longer need this placeholder build stage\. After the pipeline is completed, this second stage is deleted and the new test stage is added in step 5\.

   1. In **Build provider**, choose **Add Jenkins**\. This build selection is a placeholder\. It is not used\.

   1. In **Provider name**, enter a name\. The name is a placeholder\. It is not used\.

   1. In **Server URL**, enter text\. The text is a placeholder\. It is not used\.

   1. In **Project name**, enter a name\. The name is a placeholder\. It is not used\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-ios-define-build-S3.png)

   1. Choose **Next**\.

   1. On the **Step 4: Add deploy stage** page, choose **Skip deploy stage**, and then accept the warning message by choosing **Skip** again\. Choose **Next**\.

   1. On **Step 5: Review**, choose **Create pipeline**\. You should see a diagram that shows the source and build stages\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-view-pipeline-S3.png)

1. Add a Device Farm test action to your pipeline as follows:

   1. In the upper left, choose **Edit**\. 

   1. Choose the **X** icon to delete the build stage\. This deletes the placeholder stage now that you no longer need it for pipeline creation\.

   1. At the bottom of the diagram, choose **\+ Add stage**\.

   1. Enter a stage name, and then choose **\+ Add action group**\.

   1. In **Action name**, enter a name\. 

   1. In **Test provider**, choose **AWS Device Farm**\.

   1. In **Action Name**, enter a name and from **Test provider**, choose **AWS Device Farm**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-add-action.png)

   1. In **Project name**, choose your existing Device Farm project or choose **Create a new project**\. 

   1. In **Device pool**, choose your existing device pool or choose **Create a new device pool**\. If you create a device pool, you must select a set of test devices\.

   1. In **App type**, choose **iOS**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-choose-test-provider-S3.png)

   1. In **App file path**, enter the path of the compiled app package\. The path is relative to the root of the input artifact for the test stage\. Typically, this path is similar to `ios-test.ipa`\.

   1. In **Test type**, do one of the following:
      + If you're using one of the built\-in Device Farm tests, choose the type of test configured in your Device Farm project\.
      + If you aren't using one of the built\-in Device Farm tests, choose your type of test, and then in **Test file path**, enter the path of the test definition file\. The path is relative to the root of the input artifact for your test\.   
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-test-type.png)

   1. In the remaining fields, provide the configuration that is appropriate for your test and application type\.

   1. \(Optional\) In **Advanced**, provide configuration information for your test run\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-advanced.png)

   1. In **Input artifacts**, choose **MyApp**\.

      In the AWS CodePipeline console, you can find the name of the output artifact for each stage by hovering over the information icon in the pipeline diagram\. Because your pipeline tests your app directly from the **Source** stage, choose **MyApp**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-output-artifact-S3.png)

   1. At the bottom of the panel, choose **Add Action**\.

   1. In the AWS CodePipeline pane, choose **Save pipeline change**, and then choose **Save change**\. View your updated pipeline\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-ios-final-view-pipeline.png)

   1. To submit your changes and start a pipeline build, choose **Release change**, and then choose **Release**\.