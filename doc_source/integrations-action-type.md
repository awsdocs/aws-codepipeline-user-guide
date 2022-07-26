# Integrations with CodePipeline action types<a name="integrations-action-type"></a>

The integrations information in this topic is organized by CodePipeline action type\.

**Topics**
+ [Source action integrations](#integrations-source)
+ [Build action integrations](#integrations-build)
+ [Test action integrations](#integrations-test)
+ [Deploy action integrations](#integrations-deploy)
+ [Approval action integrations](#integrations-approval)
+ [Invoke action integrations](#integrations-invoke)

## Source action integrations<a name="integrations-source"></a>

The following information is organized by CodePipeline action type and can help you configure CodePipeline to integrate with the following source action providers\.

**Topics**
+ [Amazon ECR source actions](#integrations-source-ecr)
+ [Amazon S3 source actions](#integrations-source-s3)
+ [Connections to Bitbucket Cloud, GitHub \(version 2\), and GitHub Enterprise Server](#integrations-source-connections)
+ [CodeCommit source actions](#integrations-source-codecommit)
+ [GitHub \(version 1\) source actions](#integrations-source-gh-oauth)

### Amazon ECR source actions<a name="integrations-source-ecr"></a>


|  |  | 
| --- |--- |
| Amazon ECR | [Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/) is an AWS Docker image repository service\. You use Docker push and pull commands to upload Docker images to your repository\. An Amazon ECR repository URI and image are used in Amazon ECS task definitions to reference source image information\.Learn more:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html) | 

### Amazon S3 source actions<a name="integrations-source-s3"></a>


|  |  | 
| --- |--- |
| Amazon Simple Storage Service \(Amazon S3\) |  [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/) is storage for the internet\. You can use Amazon S3 to store and retrieve any amount of data at any time, from anywhere on the web\. You can configure CodePipeline to use a versioned Amazon S3 bucket as the source action for your code\.  Amazon S3 can also be included in a pipeline as a deploy action\.  Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### Connections to Bitbucket Cloud, GitHub \(version 2\), and GitHub Enterprise Server<a name="integrations-source-connections"></a>

Connections \(`CodestarSourceConnection` actions\) are used to access your third\-party Bitbucket Cloud, GitHub, or GitHub Enterprise Server repository\.

**Note**  
This feature is not available in the Asia Pacific \(Hong Kong\) and Europe \(Milan\) Regions\. To reference other available actions, see [Product and service integrations with CodePipeline](integrations.md)\.


|  |  | 
| --- |--- |
| Bitbucket |  You can configure CodePipeline to use a Bitbucket repository as the source for your code\. You must have previously created a Bitbucket account and at least one Bitbucket repository\. You can add a source action for your Bitbucket repository by either creating a pipeline or editing an existing one\.  You can create connections to a Bitbucket Cloud repository\. Installed Bitbucket provider types, such as Bitbucket Server, are not supported\.  You can set up resources called *connections* to allow your pipelines to access third\-party code repositories\. When you create a connection, you install the AWS CodeStar app with your third\-party code repository, and then associate it with your connection\. For Bitbucket, use the **Bitbucket** option in the console or the `CodestarSourceConnection` action in the CLI\. See [Bitbucket connections](connections-bitbucket.md)\. You can use the **Full clone** option for this action to reference the repository Git metadata so that downstream actions can perform Git commands directly\. This option can only be used by CodeBuild downstream actions\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 


|  |  | 
| --- |--- |
| GitHub or GitHub Enterprise Cloud |  You can configure CodePipeline to use a GitHub repository as the source for your code\. You must have previously created a GitHub account and at least one GitHub repository\. You can add a source action for your GitHub repository by either creating a pipeline or editing an existing one\. You can set up resources called *connections* to allow your pipelines to access third\-party code repositories\. When you create a connection, you install the AWS CodeStar app with your third\-party code repository, and then associate it with your connection\. Use the **GitHub \(Version 2\)** provider option in the console or the `CodestarSourceConnection` action in the CLI\. See [GitHub connections](connections-github.md)\. You can use the **Full clone** option for this action to reference the repository Git metadata so that downstream actions can perform Git commands directly\. This option can only be used by CodeBuild downstream actions\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 


|  |  | 
| --- |--- |
| GitHub Enterprise Server |  You can configure CodePipeline to use a GitHub Enterprise Server repository as the source for your code\. You must have previously created a GitHub account and at least one GitHub repository\. You can add a source action for your GitHub Enterprise Server repository by either creating a pipeline or editing an existing one\. You can set up resources called *connections* to allow your pipelines to access third\-party code repositories\. When you create a connection, you install the AWS CodeStar app with your third\-party code repository, and then associate it with your connection\. Use the **GitHub Enterprise Server** provider option in the console or the `CodestarSourceConnection` action in the CLI\. See [GitHub Enterprise Server connections](connections-ghes.md)\.  AWS CodeStar Connections does not support GitHub Enterprise Server version 2\.22\.0 due to a known issue in the release\. To connect, upgrade to version 2\.22\.1 or the latest available version\.  You can use the **Full clone** option for this action to reference the repository Git metadata so that downstream actions can perform Git commands directly\. This option can only be used by CodeBuild downstream actions\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### CodeCommit source actions<a name="integrations-source-codecommit"></a>


|  |  | 
| --- |--- |
| CodeCommit |  [CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/) is a version control service that you can use to privately store and manage assets \(such as documents, source code, and binary files\) in the cloud\. You can configure CodePipeline to use a branch in a CodeCommit repository as the source for your code\. Create the repository and associate it with a working directory on your local machine\. Then you can create a pipeline that uses the branch as part of a source action in a stage\. You can connect to the CodeCommit repository by either creating a pipeline or editing an existing one\.  You can use the **Full clone** option for this action to reference the repository Git metadata so that downstream actions can perform Git commands directly\. This option can only be used by CodeBuild downstream actions\. Learn more:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### GitHub \(version 1\) source actions<a name="integrations-source-gh-oauth"></a>

**Note**  


|  |  | 
| --- |--- |
| GitHub \(version 1\) |  The GitHub version 1 action is managed with OAuth Apps\. In available Regions, you can also use a [CodeStarSourceConnection for Bitbucket, GitHub, and GitHub Enterprise Server actions](action-reference-CodestarConnectionSource.md) source action in your pipeline that manages your GitHub source action with GitHub Apps\. If you have a pipeline that uses the GitHub version 1 action, see the steps to update it to use a GitHub version 2 action in [Update a GitHub version 1 source action to a GitHub version 2 source action](update-github-action-connections.md)\.  While we don’t recommend using the GitHub version 1 action, existing pipelines with the GitHub version 1 action will continue to work without any impact\.  Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

## Build action integrations<a name="integrations-build"></a>

The following information is organized by CodePipeline action type and can help you configure CodePipeline to integrate with the following build action providers\.

**Topics**
+ [CodeBuild build actions](#integrations-build-codebuild)
+ [CloudBees build actions](#integrations-build-cloudbees)
+ [Jenkins build actions](#integrations-build-jenkins)
+ [TeamCity build actions](#integrations-build-teamcity)

### CodeBuild build actions<a name="integrations-build-codebuild"></a>


|  |  | 
| --- |--- |
| CodeBuild |  [CodeBuild](http://aws.amazon.com/codebuild/) is a fully managed build service that compiles your source code, runs unit tests, and produces artifacts that are ready to deploy\. You can add CodeBuild as a build action to the build stage of a pipeline\. For more information, see the CodePipeline Action Configuration Reference for [AWS CodeBuild](action-reference-CodeBuild.md)\.  CodeBuild can also be included in a pipeline as a test action, with or without a build output\.  Learn more:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### CloudBees build actions<a name="integrations-build-cloudbees"></a>


|  |  | 
| --- |--- |
| CloudBees |  You can configure CodePipeline to use [CloudBees](http://www.cloudbees.com) to build or test your code in one or more actions in a pipeline\.Learn more:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html) | 

### Jenkins build actions<a name="integrations-build-jenkins"></a>


|  |  | 
| --- |--- |
| Jenkins  |  You can configure CodePipeline to use [Jenkins CI](https://jenkins-ci.org/) to build or test your code in one or more actions in a pipeline\. You must have previously created a Jenkins project and installed and configured the CodePipeline Plugin for Jenkins for that project\. You can connect to the Jenkins project by either creating a new pipeline or editing an existing one\. Access for Jenkins is configured on a per\-project basis\. You must install the CodePipeline Plugin for Jenkins on every Jenkins instance you want to use with CodePipeline\. You must also configure CodePipeline access to the Jenkins project\. Secure your Jenkins project by configuring it to accept HTTPS/SSL connections only\. If your Jenkins project is installed on an Amazon EC2 instance, consider providing your AWS credentials by installing the AWS CLI on each instance\. Then configure an AWS profile on those instances with the IAM user profile and AWS credentials you want to use for connections\. This is an alternative to adding and storing them through the Jenkins web interface\.  Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### TeamCity build actions<a name="integrations-build-teamcity"></a>


|  |  | 
| --- |--- |
| TeamCity |  You can configure CodePipeline to use [TeamCity](https://www.jetbrains.com/teamcity/) to build and test your code in one or more actions in a pipeline\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

## Test action integrations<a name="integrations-test"></a>

The following information is organized by CodePipeline action type and can help you configure CodePipeline to integrate with the following test action providers\.

**Topics**
+ [CodeBuild test actions](#integrations-test-codebuild)
+ [AWS Device Farm test actions](#integrations-test-devicefarm)
+ [BlazeMeter test actions](#integrations-test-blazemeter)
+ [Ghost Inspector test actions](#integrations-test-ghostinspector)
+ [Micro Focus StormRunner Load test actions](#integrations-test-stormrunner)
+ [Nouvola test actions](#integrations-test-nouvola)
+ [Runscope test actions](#integrations-test-runscope)

### CodeBuild test actions<a name="integrations-test-codebuild"></a>


|  |  | 
| --- |--- |
| CodeBuild |  [CodeBuild](http://aws.amazon.com/codebuild/) is a fully managed build service in the cloud\. CodeBuild compiles your source code, runs unit tests, and produces artifacts that are ready to deploy\.  You can add CodeBuild to a pipeline as a test action\. For more information, see the CodePipeline Action Configuration Reference for [AWS CodeBuild](action-reference-CodeBuild.md)\.  CodeBuild can also be included in a pipeline as a build action, with a mandatory build output artifact\.  Learn more:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### AWS Device Farm test actions<a name="integrations-test-devicefarm"></a>

**Note**  


|  |  | 
| --- |--- |
| AWS Device Farm |  [AWS Device Farm](http://aws.amazon.com/devicefarm/) is an app testing service that you can use to test and interact with your Android, iOS, and web applications on real, physical phones and tablets\. You can configure CodePipeline to use AWS Device Farm to test your code in one or more actions in a pipeline\. AWS Device Farm allows you to upload your own tests or use built\-in, script\-free compatibility tests\. Because testing is performed in parallel, tests on multiple devices begin in minutes\. A test report that contains high\-level results, low\-level logs, pixel\-to\-pixel screenshots, and performance data is updated as tests are completed\. AWS Device Farm supports testing of native and hybrid Android, iOS, and Fire OS apps, including those created with PhoneGap, Titanium, Xamarin, Unity, and other frameworks\. It supports remote access of Android apps, which allows you to interact directly with test devices\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### BlazeMeter test actions<a name="integrations-test-blazemeter"></a>


|  |  | 
| --- |--- |
| BlazeMeter |  You can configure CodePipeline to use [BlazeMeter](https://blazemeter.com/) to test your code in one or more actions in a pipeline\. Learn more:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html) | 

### Ghost Inspector test actions<a name="integrations-test-ghostinspector"></a>


|  |  | 
| --- |--- |
| Ghost Inspector |  You can configure CodePipeline to use [Ghost Inspector](https://ghostinspector.com/) to test your code in one or more actions in a pipeline\.  Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### Micro Focus StormRunner Load test actions<a name="integrations-test-stormrunner"></a>


|  |  | 
| --- |--- |
| Micro Focus StormRunner Load | You can configure CodePipeline to use [Micro Focus StormRunner Load](https://software.microfocus.com/en-us/products/stormrunner-load-agile-cloud-testing/overview) in one or more actions in a pipeline\.Learn more:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html) | 

### Nouvola test actions<a name="integrations-test-nouvola"></a>


|  |  | 
| --- |--- |
| Nouvola | You can configure CodePipeline to use [Nouvola](http://www.nouvola.com/aws-codepipeline-plugin/) to test your code in one or more actions in a pipeline\.Learn more:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html) | 

### Runscope test actions<a name="integrations-test-runscope"></a>


|  |  | 
| --- |--- |
| Runscope | You can configure CodePipeline to use [Runscope](https://www.runscope.com/) to test your code in one or more actions in a pipeline\.Learn more:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html) | 

## Deploy action integrations<a name="integrations-deploy"></a>

The following information is organized by CodePipeline action type and can help you configure CodePipeline to integrate with the following deploy action providers\.

**Topics**
+ [Amazon S3 deploy actions](#integrations-deploy-S3)
+ [AWS AppConfig deploy actions](#integrations-deploy-AppConfig)
+ [AWS CloudFormation deploy actions](#integrations-deploy-CloudFormation)
+ [AWS CloudFormation StackSets deploy actions](#integrations-deploy-StackSets)
+ [Amazon ECS deploy actions](#integrations-deploy-ECS)
+ [Elastic Beanstalk deploy actions](#integrations-deploy-beanstalk)
+ [AWS OpsWorks deploy actions](#integrations-deploy-opsworks)
+ [AWS Service Catalog deploy actions](#integrations-deploy-servicecatalog)
+ [Amazon Alexa deploy actions](#integrations-deploy-alexa)
+ [CodeDeploy deploy actions](#integrations-deploy-CodeDeploy)
+ [XebiaLabs deploy actions](#integrations-deploy-xebialabs)

### Amazon S3 deploy actions<a name="integrations-deploy-S3"></a>


|  |  | 
| --- |--- |
| Amazon S3 |  [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/) is storage for the internet\. You can use Amazon S3 to store and retrieve any amount of data at any time, from anywhere on the web\. You can add an action to a pipeline that uses Amazon S3 as a deployment provider\.  Amazon S3 can also be included in a pipeline as a source action\.  Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### AWS AppConfig deploy actions<a name="integrations-deploy-AppConfig"></a>


|  |  | 
| --- |--- |
| AWS AppConfig |  AWS AppConfig is a capability of AWS Systems Manager to create, manage, and quickly deploy application configurations\. You can use AppConfig with applications hosted on EC2 instances, AWS Lambda, containers, mobile applications, or IoT devices\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### AWS CloudFormation deploy actions<a name="integrations-deploy-CloudFormation"></a>

**Note**  


|  |  | 
| --- |--- |
| AWS CloudFormation |  [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/) gives developers and systems administrators an easy way to create and manage a collection of related AWS resources, using templates to provision and update those resources\. You can use the service’s sample templates or create your own\. Templates describe the AWS resources and any dependencies or runtime parameters required to run your application\. The AWS Serverless Application Model \(AWS SAM\) extends AWS CloudFormation to provide a simplified way to define and deploy serverless applications\. AWS SAM supports Amazon API Gateway APIs, AWS Lambda functions, and Amazon DynamoDB tables\. You can use CodePipeline with AWS CloudFormation and the AWS SAM to continuously deliver your serverless applications\. You can add an action to a pipeline that uses AWS CloudFormation as a deployment provider\. When you use AWS CloudFormation as a deployment provider, you can take action on AWS CloudFormation stacks and change sets as part of a pipeline execution\. AWS CloudFormation can create, update, replace, and delete stacks and change sets when a pipeline runs\. As a result, AWS and custom resources can be created, provisioned, updated, or terminated during a pipeline execution according to the specifications you provide in AWS CloudFormation templates and parameter definitions\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### AWS CloudFormation StackSets deploy actions<a name="integrations-deploy-StackSets"></a>

**Note**  
This feature is not available in the Asia Pacific \(Hong Kong\) and Europe \(Milan\) Regions\. To reference other available actions, see [Product and service integrations with CodePipeline](integrations.md)\.


|  |  | 
| --- |--- |
| AWS CloudFormation StackSets |  [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/) gives you a way to deploy resources across multiple accounts and AWS Regions\. You can use CodePipeline with AWS CloudFormation to update your stack set definition and deploy updates to your instances\. You can add the following actions to a pipeline to use AWS CloudFormation StackSets as a deployment provider\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html) Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### Amazon ECS deploy actions<a name="integrations-deploy-ECS"></a>


|  |  | 
| --- |--- |
| Amazon ECS |  Amazon ECS is a highly scalable, high performance container management service that allows you to run container\-based applications in the AWS Cloud\. When you create a pipeline, you can select Amazon ECS as a deployment provider\. A change to code in your source control repository triggers your pipeline to build a new Docker image, push it to your container registry, and then deploy the updated image to Amazon ECS\. You can also use the **ECS \(Blue/Green\)** provider action in CodePipeline to route and deploy traffic to Amazon ECS with CodeDeploy\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### Elastic Beanstalk deploy actions<a name="integrations-deploy-beanstalk"></a>


|  |  | 
| --- |--- |
| Elastic Beanstalk |  [Elastic Beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/) is a service for deploying and scaling web applications and services developed with Java, \.NET, PHP, Node\.js, Python, Ruby, Go, and Docker on familiar servers such as Apache, Nginx, Passenger, and IIS\. You can configure CodePipeline to use Elastic Beanstalk to deploy your code\. You can create the Elastic Beanstalk application and environment to use in a deploy action in a stage either before you create the pipeline or when you use the **Create Pipeline** wizard\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### AWS OpsWorks deploy actions<a name="integrations-deploy-opsworks"></a>


|  |  | 
| --- |--- |
| AWS OpsWorks |  AWS OpsWorks is a configuration management service that helps you configure and operate applications of all shapes and sizes using Chef\. Using AWS OpsWorks Stacks, you can define the application’s architecture and the specification of each component including package installation, software configuration and resources such as storage\. You can configure CodePipeline to use AWS OpsWorks Stacks to deploy your code in conjunction with custom Chef cookbooks and applications in AWS OpsWorks\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html) Before you create the pipeline, you create the AWS OpsWorks stack and layer\. You can create the AWS OpsWorks application to use in a deploy action in a stage either before you create the pipeline or when you use the **Create Pipeline** wizard\. CodePipeline support for AWS OpsWorks is currently available in the US East \(N\. Virginia\) Region \(us\-east\-1\) only\. Learn more:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### AWS Service Catalog deploy actions<a name="integrations-deploy-servicecatalog"></a>


|  |  | 
| --- |--- |
| AWS Service Catalog |  [AWS Service Catalog](https://docs.aws.amazon.com/servicecatalog/latest/dg/) enables organizations to create and manage catalogs of products that are approved for use on AWS\. You can configure CodePipeline to deploy updates and versions of your product templates to AWS Service Catalog\. You can create the AWS Service Catalog product to use in a deployment action and then use the **Create Pipeline** wizard to create the pipeline\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### Amazon Alexa deploy actions<a name="integrations-deploy-alexa"></a>

**Note**  
This feature is not available in the Asia Pacific \(Hong Kong\) or Europe \(Milan\) Region\. To use other deploy actions available in that Region, see [Deploy action integrations](#integrations-deploy)\.


|  |  | 
| --- |--- |
| Alexa Skills Kit |  [Amazon Alexa Skills Kit](https://developer.amazon.com/docs/custom-skills/use-the-alexa-skills-kit-samples.html) lets you build and distribute cloud\-based skills to users of Alexa\-enabled devices\. You can add an action to a pipeline that uses Alexa Skills Kit as a deployment provider\. Source changes are detected by your pipeline, and then your pipeline deploys updates to your Alexa skill in the Alexa service\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### CodeDeploy deploy actions<a name="integrations-deploy-CodeDeploy"></a>


|  |  | 
| --- |--- |
| CodeDeploy |  [CodeDeploy](https://docs.aws.amazon.com/codedeploy/latest/userguide/) coordinates application deployments to Amazon EC2 instances, on\-premises instances, or both\. You can configure CodePipeline to use CodeDeploy to deploy your code\. You can create the CodeDeploy application, deployment, and deployment group to use in a deploy action in a stage either before you create the pipeline or when you use the **Create Pipeline** wizard\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### XebiaLabs deploy actions<a name="integrations-deploy-xebialabs"></a>


|  |  | 
| --- |--- |
| XebiaLabs |  You can configure CodePipeline to use [XebiaLabs](https://xebialabs.com/) to deploy your code in one or more actions in a pipeline\. Learn more:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html) | 

## Approval action integrations<a name="integrations-approval"></a>


|  |  | 
| --- |--- |
| Amazon Simple Notification Service |  [Amazon SNS](https://docs.aws.amazon.com/sns/latest/gsg/) is a fast, flexible, fully managed push notification service that lets you send individual messages or to fan out messages to large numbers of recipients\. Amazon SNS makes it simple and cost effective to send push notifications to mobile device users, email recipients or even send messages to other distributed services\. When you create a manual approval request in CodePipeline, you can optionally publish to a topic in Amazon SNS so that all IAM users subscribed to it are notified that the approval action is ready to be reviewed\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

## Invoke action integrations<a name="integrations-invoke"></a>

The following information is organized by CodePipeline action type and can help you configure CodePipeline to integrate with the following invoke action providers\.

**Topics**
+ [Lambda invoke actions](#w23aac11b9c17b7)
+ [Snyk invoke actions](#w23aac11b9c17b9)
+ [Step Functions invoke actions](#w23aac11b9c17c11)

### Lambda invoke actions<a name="w23aac11b9c17b7"></a>


|  |  | 
| --- |--- |
| Lambda |  [Lambda](https://docs.aws.amazon.com/lambda/latest/dg/) lets you run code without provisioning or managing servers\. You can configure CodePipeline to use Lambda functions to add flexibility and functionality to your pipelines\. You can create the Lambda function to add as an action in a stage either before you create the pipeline or when you use the **Create Pipeline** wizard\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### Snyk invoke actions<a name="w23aac11b9c17b9"></a>

**Note**  


|  |  | 
| --- |--- |
| Snyk |  You can configure CodePipeline to use Snyk to keep your open source environments secure by detecting and fixing security vulnerabilities and updating dependencies in your application code and container images\. You can also use the **Snyk** action in CodePipeline to automate security testing controls in your pipeline\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 

### Step Functions invoke actions<a name="w23aac11b9c17c11"></a>

**Note**  
This feature is not available in the Asia Pacific \(Hong Kong\) and Europe \(Milan\) Regions\. To reference other available actions, see [Product and service integrations with CodePipeline](integrations.md)\.


|  |  | 
| --- |--- |
| Step Functions |  [Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/) lets you create and configure state machines\. You can configure CodePipeline to use Step Functions invoke actions to trigger state machine executions\. Learn more: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-action-type.html)  | 