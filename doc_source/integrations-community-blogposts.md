# Integration examples: Blog posts<a name="integrations-community-blogposts"></a>
+ [How to deploy from GitHub to Amazon EC2 with CodePipeline](https://seanjziegler.com/deploying-code-from-github-to-aws-ec2-with-codepipeline/)

  Learn how to set up CodePipeline from scratch to deploy dev, test, and prod branches to separate deployment groups\. Learn how to use and configure IAM roles, the CodeDeploy agent, and CodeDeploy, along with CodePipeline\.

  *Published April 2020*
+ [Implementing DevSecOps Using CodePipeline](https://aws.amazon.com/blogs/devops/implementing-devsecops-using-aws-codepipeline/)

  Learn how to use a CI/CD pipeline in CodePipeline to automate preventive and detective security controls\. This post covers how to use a pipeline to create a simple security group and perform security checks during the source, test, and production stages to improve the security posture of your AWS accounts\.

  *Published March 2017*
+ [Continuous Deployment to Amazon ECS Using CodePipeline, CodeBuild, Amazon ECR, and AWS CloudFormation](https://aws.amazon.com/blogs/compute/continuous-deployment-to-amazon-ecs-using-aws-codepipeline-aws-codebuild-amazon-ecr-and-aws-cloudformation/)

  Learn how to create a continuous deployment pipeline to Amazon Elastic Container Service \(Amazon ECS\)\. Applications are delivered as Docker containers using CodePipeline, CodeBuild, Amazon ECR, and AWS CloudFormation\. 
  + Download a sample AWS CloudFormation template and instructions for using it to create your own continuous deployment pipeline from the [ECS Reference Architecture: Continuous Deployment](https://github.com/awslabs/ecs-refarch-continuous-deployment) repo on GitHub\.

  *Published January 2017*
+ [Continuous Deployment for Serverless Applications](https://aws.amazon.com/blogs/compute/continuous-deployment-for-serverless-applications/)

  Learn how to use a collection of AWS services to create a continuous deployment pipeline for your serverless applications\. You'll use the Serverless Application Model \(SAM\) to define the application and its resources and CodePipeline to orchestrate your application deployment\. 
  + [View a sample application](https://gist.github.com/SAPessi/246b278b6b7502b157a9fbaf3981d103) written in Go with the Gin framework and an API Gateway proxy shim\.

  *Published December 2016*
+ [Integrating Git with CodePipeline](https://aws.amazon.com/blogs/devops/integrating-git-with-aws-codepipeline/)

  Learn how to integrate CodePipeline with Git servers that support webhooks functionality, such as GitHub Enterprise, Bitbucket, and GitLab\.

  *Published November 2016*
+ [Scaling DevOps Deployments with CodePipeline and Dynatrace](https://www.dynatrace.com/blog/scaling-devops-deployments-with-aws-codepipeline-dynatrace/)

  Learn how use Dynatrace monitoring solutions to scale pipelines in CodePipeline, automatically analyze test executions before code is committed, and maintain optimal lead times\.

  *Published November 2016*
+ [Create a Pipeline for AWS Elastic Beanstalk in CodePipeline Using AWS CloudFormation and CodeCommit](http://www.stelligent.com/automation/create-a-pipeline-for-elastic-beanstalk-in-codepipeline-using-cloudformation-and-codecommit/)

  Learn how to implement continuous delivery in a CodePipeline pipeline for an application in AWS Elastic Beanstalk\. All AWS resources are provisioned automatically through the use of an AWS CloudFormation template\. This walkthrough also incorporates CodeCommit and AWS Identity and Access Management \(IAM\)\.

  *Published May 2016*
+ [Automate CodeCommit and CodePipeline in AWS CloudFormation](http://www.stelligent.com/automation/automate-codecommit-and-codepipeline-in-aws-cloudformation/)

  Use AWS CloudFormation to automate the provisioning of AWS resources for a continuous delivery pipeline that uses CodeCommit, CodePipeline, CodeDeploy, and AWS Identity and Access Management\.

  *Published April 2016*
+ [Create a Cross\-Account Pipeline in AWS CodePipeline](http://www.stelligent.com/automation/create-a-cross-account-pipeline-in-aws-cloudformation/)

  Learn how to automate the provisioning of cross\-account access to pipelines in AWS CodePipeline by using AWS Identity and Access Management\. Includes examples in an AWS CloudFormation template\.

  *Published March 2016*
+ [Exploring ASP\.NET Core Part 2: Continuous Delivery](https://blogs.aws.amazon.com/net/post/Tx2EHIJAM9LIW8G/Exploring-ASP-NET-Core-Part-2-Continuous-Delivery)

  Learn how to create a full continuous delivery system for an ASP\.NET Core application using CodeDeploy and AWS CodePipeline\.

  *Published March 2016*
+ [Create a Pipeline Using the AWS CodePipeline Console](http://www.stelligent.com/cloud/create-a-pipeline-using-the-aws-codepipeline-console/)

  Learn how to use the AWS CodePipeline console to create a two\-stage pipeline in a walkthrough based on the AWS CodePipeline [Tutorial: Create a four\-stage pipeline](tutorials-four-stage-pipeline.md)\.

  *Published March 2016*
+ [Mocking AWS CodePipeline Pipelines with AWS Lambda](http://www.stelligent.com/automation/mocking-aws-codepipeline-pipelines-with-lambda/)

  Learn how to invoke a Lambda function that lets you visualize the actions and stages in a CodePipeline software delivery process as you design it, before the pipeline is operational\. As you design your pipeline structure, you can use the Lambda function to test whether your pipeline will complete successfully\.

  *Published February 2016*
+ [Running AWS Lambda Functions in CodePipeline Using AWS CloudFormation](http://www.stelligent.com/automation/aws-lambda-functions-aws-codepipeline-cloudformation/)

  Learn how to create an AWS CloudFormation stack that provisions all the AWS resources used in the user guide task [Invoke an AWS Lambda function in a pipeline in CodePipeline](actions-invoke-lambda-function.md)\.

  *Published February 2016*
+ [Provisioning Custom CodePipeline Actions in AWS CloudFormation](http://www.stelligent.com/automation/provisioning-custom-codepipeline-actions-in-cloudformation/)

  Learn how to use AWS CloudFormation to provision custom actions in CodePipeline\.

  *Published January 2016*
+ [Provisioning CodePipeline with AWS CloudFormation](http://www.stelligent.com/automation/provisioning-aws-codepipeline-with-cloudformation/)

  Learn how to provision a basic continuous delivery pipeline in CodePipeline using AWS CloudFormation\.

  *Published December 2015*
+ [Building Continuous Deployment on AWS with CodePipeline, Jenkins, and Elastic Beanstalk](https://blogs.aws.amazon.com/application-management/post/Tx34AXRMYLXG5OT/Building-Continuous-Deployment-on-AWS-with-AWS-CodePipeline-Jenkins-and-AWS-Elas)

  Learn how to use GitHub, CodePipeline, Jenkins, and Elastic Beanstalk to create a deployment pipeline for a web application that is updated automatically every time you change your code\.

  *Published December 2015*
+ [Performance Testing in Continuous Delivery Using CodePipeline and BlazeMeter](https://aws.amazon.com/blogs/apn/performance-testing-in-continuous-delivery-using-aws-codepipeline-and-blazemeter/)

  Learn how to inject automated load tests at the right places in the CodePipeline delivery workflow with BlazeMeter’s native CodePipeline integration\.

  *Published September 2015*
+ [Deploying from CodePipeline to AWS OpsWorks Using a Custom Action and AWS Lambda](http://hipsterdevblog.com/blog/2015/07/28/deploying-from-codepipeline-to-opsworks-using-a-custom-action-and-lambda/)

  Learn how to configure your pipeline and the AWS Lambda function to deploy to AWS OpsWorks using CodePipeline\.

  *Published July 2015*
+ [Automated Delivery Acceptance Test Nirvana: Powered by CodePipeline, CloudWatch, and BlazeMeter](https://blazemeter.com/blog/automated-delivery-acceptance-test-nirvana)

  Learn how to use CodePipeline, CloudWatch, and BlazeMeter to create a continuous delivery workflow that reduces time to release and increases test coverage for developers during the release\.

  *Published July 2015*