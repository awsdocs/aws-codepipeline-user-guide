# CodePipeline Tutorials<a name="tutorials"></a>

After you complete the steps in [Getting Started with CodePipeline](getting-started-codepipeline.md), you can try one of the AWS CodePipeline tutorials in this user guide: 


|  |  | 
| --- |--- |
| I want to create a two\-stage pipeline that uses CodeDeploy to deploy a sample application from an Amazon S3 bucket to Amazon EC2 instances running Amazon Linux\. After using the wizard to create my pipeline, I want to add a third stage\. | See [Tutorial: Create a Simple Pipeline \(Amazon S3 Bucket\)](tutorials-simple-s3.md)\. | 
| I want to create a two\-stage pipeline that uses CodeDeploy to deploy a sample application from a CodeCommit repository to an Amazon EC2 instance running Amazon Linux\. | See [Tutorial: Create a Simple Pipeline \(CodeCommit Repository\)](tutorials-simple-codecommit.md)\. | 
| I want to add a build stage to the three\-stage pipeline I created in the first tutorial\. The new stage uses Jenkins to build my application\. | See [Tutorial: Create a Four\-Stage Pipeline](tutorials-four-stage-pipeline.md)\. | 
| I want to set up a CloudWatch Events rule that sends notifications whenever there are changes to the execution state of my pipeline, stage, or action\. | See [Tutorial: Set Up a CloudWatch Events Rule to Receive Email Notifications for Pipeline State Changes](tutorials-cloudwatch-sns-notifications.md)\. | 
| I want to create a pipeline with a GitHub source that builds and tests an Android app with CodeBuild and AWS Device Farm\. | See [Tutorial: Create a Pipeline That Builds and Tests Your Android App When a Commit Is Pushed to Your GitHub Repository](tutorials-codebuild-devicefarm.md)\. | 
| I want to create a pipeline with an Amazon S3 source that tests an iOS app with AWS Device Farm\. | See [Tutorial: Create a Pipeline That Tests Your iOS App After a Change in Your S3 Bucket](tutorials-codebuild-devicefarm-S3.md)\. | 
| I want to create a pipeline that deploys my product template to AWS Service Catalog\. | See [Tutorial: Create a Pipeline That Deploys to AWS Service Catalog](tutorials-S3-servicecatalog.md)\. | 
| I want to use sample templates to create a simple pipeline \(with an Amazon S3, CodeCommit, or GitHub source\) using the AWS CloudFormation console\. | See [Tutorial: Create a Pipeline with AWS CloudFormation](tutorials-cloudformation.md)\. | 
| I want to create a two\-stage pipeline that uses CodeDeploy and Amazon ECS for blue green deployment of an image from an Amazon ECR repository to an Amazon ECS cluster and service\. | See [Tutorial: Create a Pipeline with an Amazon ECR Source and ECS\-to\-CodeDeploy Deployment](tutorials-ecs-ecr-codedeploy.md)\. | 
| I want to create a pipeline that continuously publishes my serverless application to the AWS Serverless Application Repository\. | See [Tutorial: Create a Pipeline That Publishes Your Serverless Application to the AWS Serverless Application Repository](tutorials-serverlessrepo-auto-publish.md)\. | 

**Note**  
[Tutorial: Create a Four\-Stage Pipeline](tutorials-four-stage-pipeline.md) shows how to create a pipeline that gets source code from a GitHub repository, uses Jenkins to build and test the source code, and then uses CodeDeploy to deploy the built and tested source code to Amazon EC2 instances running Amazon Linux or Microsoft Windows Server\. Because this tutorial builds on concepts covered in the walkthroughs, we recommend you complete at least one of them first\.

The following tutorials and walkthroughs in other user guides provide guidance for integrating other AWS services into your pipelines:
+ [Create a Pipeline that Uses CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/how-to-create-pipeline.html#pipelines-create-console) in *[AWS CodeBuild User Guide](https://docs.aws.amazon.com/codebuild/latest/userguide/)*
+  [Using CodePipeline with AWS OpsWorks Stacks](https://docs.aws.amazon.com/opsworks/latest/userguide/other-services-cp.html) in *[AWS OpsWorks User Guide](https://docs.aws.amazon.com/opsworks/latest/userguide/)*
+ [Continuous Delivery with CodePipeline](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline.html) in *[AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/)*
+ [Elastic Beanstalk Walkthrough](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/GettingStarted.Walkthrough.html) in *[AWS Elastic Beanstalk Developer Guide](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/)* 
+ [Set Up a Continuous Deployment Pipeline Using CodePipeline](https://aws.amazon.com/getting-started/tutorials/continuous-deployment-pipeline/)