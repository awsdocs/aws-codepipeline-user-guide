--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# AWS CodePipeline Tutorials<a name="tutorials"></a>

After you complete the steps in [Getting Started with AWS CodePipeline](getting-started-codepipeline.md), you can try one of the tutorials in this user guide: 


|  |  | 
| --- |--- |
| I want to create a pipeline that uses AWS CodeDeploy to deploy a sample application from an Amazon S3 bucket to Amazon EC2 instances running Amazon Linux\. | See [Tutorial: Create a Simple Pipeline \(Amazon S3 Bucket\)](tutorials-simple-s3.md)\. | 
| I want to create a pipeline that uses AWS CodeDeploy to deploy a sample application from an AWS CodeCommit repository to an Amazon EC2 instance running Amazon Linux\. | See [Tutorial: Create a Simple Pipeline \(AWS CodeCommit Repository\)](tutorials-simple-codecommit.md)\. | 

**Note**  
[Tutorial: Create a Four\-Stage Pipeline](tutorials-four-stage-pipeline.md) shows how to create a pipeline that gets source code from a GitHub repository, uses Jenkins to build and test the source code, and then uses AWS CodeDeploy to deploy the built and tested source code to Amazon EC2 instances running Amazon Linux or Microsoft Windows Server\. Because this tutorial builds on concepts covered in the walkthroughs, we recommend you complete at least one of them first\.  
If you want to try AWS CodePipeline with [AWS CodeDeploy](https://aws.amazon.com/codedeploy) without setting up all the required resources, try the [Explore Continuous Delivery in AWS with the Pipeline Starter Kit](https://aws.amazon.com/blogs/devops/explore-continuous-delivery-in-aws-with-the-pipeline-starter-kit/)\. The starter kit sets up a complete pipeline that builds and deploys a sample application in just a few steps, using an AWS CloudFormation template to create the pipeline and all of its resources in the US East \(N\. Virginia\) Region\.

The following tutorials and walkthroughs in other user guides provide guidance for integrating other AWS services into your pipelines:
+ [Create a Pipeline that Uses AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/how-to-create-pipeline.html#pipelines-create-console) in *[AWS CodeBuild User Guide](https://docs.aws.amazon.com/codebuild/latest/userguide/)*
+  [Using AWS CodePipeline with AWS OpsWorks Stacks](https://docs.aws.amazon.com/opsworks/latest/userguide/other-services-cp.html) in *[AWS OpsWorks User Guide](https://docs.aws.amazon.com/opsworks/latest/userguide/)*
+ [Continuous Delivery with AWS CodePipeline](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline.html) in *[AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/)*
+ [Elastic Beanstalk Walkthrough](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/GettingStarted.Walkthrough.html) in *[AWS Elastic Beanstalk Developer Guide](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/)* 
+ [AWS for DevOps Getting Started Guide](http://docs.aws.amazon.com/devops/latest/gsg/welcome.html) 
+ [Explore Continuous Delivery in AWS with the Pipeline Starter Kit](https://aws.amazon.com/blogs/devops/explore-continuous-delivery-in-aws-with-the-pipeline-starter-kit/)
+ [Set Up a Continuous Deployment Pipeline Using AWS CodePipeline](https://aws.amazon.com/getting-started/tutorials/continuous-deployment-pipeline/)