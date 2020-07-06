# CodePipeline tutorials<a name="tutorials"></a>

After you complete the steps in [Getting started with CodePipeline](getting-started-codepipeline.md), you can try one of the AWS CodePipeline tutorials in this user guide: 


|  |  | 
| --- |--- |
| I want to use the wizard to create a pipeline that uses CodeDeploy to deploy a sample application from an Amazon S3 bucket to Amazon EC2 instances running Amazon Linux\. After using the wizard to create my two\-stage pipeline, I want to add a third stage\. | See [Tutorial: Create a simple pipeline \(S3 bucket\)](tutorials-simple-s3.md)\. | 
| I want to create a two\-stage pipeline that uses CodeDeploy to deploy a sample application from a CodeCommit repository to an Amazon EC2 instance running Amazon Linux\. | See [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md)\. | 
| I want to add a build stage to the three\-stage pipeline I created in the first tutorial\. The new stage uses Jenkins to build my application\. | See [Tutorial: Create a four\-stage pipeline](tutorials-four-stage-pipeline.md)\. | 
| I want to set up a CloudWatch Events rule that sends notifications whenever there are changes to the execution state of my pipeline, stage, or action\. | See [Tutorial: Set up a CloudWatch Events rule to receive email notifications for pipeline state changes](tutorials-cloudwatch-sns-notifications.md)\. | 
| I want to create a pipeline with a GitHub source that builds and tests an Android app with CodeBuild and AWS Device Farm\. | See [Tutorial: Create a pipeline that builds and tests your Android app when a commit is pushed to your GitHub repository](tutorials-codebuild-devicefarm.md)\. | 
| I want to create a pipeline with an Amazon S3 source that tests an iOS app with AWS Device Farm\. | See [Tutorial: Create a pipeline that tests your iOS app after a change in your S3 bucket](tutorials-codebuild-devicefarm-S3.md)\. | 
| I want to create a pipeline that deploys my product template to AWS Service Catalog\. | See [Tutorial: Create a pipeline that deploys to AWS Service Catalog](tutorials-S3-servicecatalog.md)\. | 
| I want to use sample templates to create a simple pipeline \(with an Amazon S3, CodeCommit, or GitHub source\) using the AWS CloudFormation console\. | See [Tutorial: Create a pipeline with AWS CloudFormation](tutorials-cloudformation.md)\. | 
| I want to create a two\-stage pipeline that uses CodeDeploy and Amazon ECS for blue/green deployment of an image from an Amazon ECR repository to an Amazon ECS cluster and service\. | See [Tutorial: Create a pipeline with an Amazon ECR source and ECS\-to\-CodeDeploy deployment](tutorials-ecs-ecr-codedeploy.md)\. | 
| I want to create a pipeline that continuously publishes my serverless application to the AWS Serverless Application Repository\. | See [Tutorial: Create a pipeline that publishes your serverless application to the AWS Serverless Application Repository](tutorials-serverlessrepo-auto-publish.md)\. | 

The following tutorials in other user guides provide guidance for integrating other AWS services into your pipelines:
+ [Create a pipeline that uses CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/how-to-create-pipeline.html#pipelines-create-console) in *[AWS CodeBuild User Guide](https://docs.aws.amazon.com/codebuild/latest/userguide/)*
+  [Using CodePipeline with AWS OpsWorks Stacks](https://docs.aws.amazon.com/opsworks/latest/userguide/other-services-cp.html) in *[AWS OpsWorks User Guide](https://docs.aws.amazon.com/opsworks/latest/userguide/)*
+ [Continuous Delivery with CodePipeline](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline.html) in *[AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/)*
+ [Getting started using Elastic Beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/GettingStarted.html) in *[AWS Elastic Beanstalk Developer Guide](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/)* 
+ [Set Up a Continuous Deployment Pipeline Using CodePipeline](https://aws.amazon.com/getting-started/tutorials/continuous-deployment-pipeline/)