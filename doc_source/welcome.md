# What Is AWS CodePipeline?<a name="welcome"></a>

AWS CodePipeline is a continuous delivery service you can use to model, visualize, and automate the steps required to release your software\. You can quickly model and configure the different stages of a software release process\. CodePipeline automates the steps required to release your software changes continuously\. For information about pricing for CodePipeline, see [Pricing](http://aws.amazon.com/codepipeline/pricing/)\. 

**Topics**
+ [Video Introduction to AWS CodePipeline](#intro-video-welcome)
+ [What Can I Do with CodePipeline?](#welcome-what-can-I-do)
+ [A Quick Look at CodePipeline](#welcome-introducing)
+ [How Do I Get Started with CodePipeline?](#welcome-get-started)
+ [We Want to Hear from You](#welcome-contact-us)
+ [CodePipeline Concepts](concepts.md)

## Video Introduction to AWS CodePipeline<a name="intro-video-welcome"></a>

This short video \(3:06\) describes how CodePipeline builds, tests, and deploys your code every time there is a code change, based on the release process models you define\.

[![AWS Videos](http://img.youtube.com/vi/YxcIj_SLflw/0.jpg)](http://www.youtube.com/watch?v=YxcIj_SLflw)

## What Can I Do with CodePipeline?<a name="welcome-what-can-I-do"></a>

You can use CodePipeline to help you automatically build, test, and deploy your applications in the cloud\. Specifically, you can: 
+ **Automate your release processes**: CodePipeline fully automates your release process from end to end, starting from your source repository through build, test, and deployment\. You can prevent changes from moving through a pipeline by including a manual approval action in any stage except a Source stage\. You can release when you want, in the way you want, on the systems of your choice, across one instance or multiple instances\.
+ **Establish a consistent release process**: Define a consistent set of steps for every code change\. CodePipeline runs each stage of your release according to your criteria\.
+ **Speed up delivery while improving quality**: You can automate your release process to allow your developers to test and release code incrementally and speed up the release of new features to your customers\. 
+ **Use your favorite tools**: You can incorporate your existing source, build, and deployment tools into your pipeline\. For a full list of AWS services and third\-party tools currently supported by CodePipeline, see [Product and Service Integrations with CodePipeline](integrations.md)\.
+ **View progress at a glance**: You can review real\-time status of your pipelines, check the details of any alerts, retry failed actions, view details about the source revisions used in the latest pipeline execution in each stage, and manually rerun any pipeline\.
+ **View pipeline history details**: You can view details about executions of a pipeline, including start and end times, run duration, and execution IDs\. 

## A Quick Look at CodePipeline<a name="welcome-introducing"></a>

The following diagram shows an example release process using CodePipeline\.

![\[An example release process using CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/PipelineFlow.png)![\[An example release process using CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[An example release process using CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

In this example, when developers commit changes to a source repository, CodePipeline automatically detects the changes\. Those changes are built, and if any tests are configured, those tests are run\. After the tests are complete, the built code is deployed to staging servers for testing\. From the staging server, CodePipeline runs additional tests, such as integration or load tests\. Upon the successful completion of those tests, and after a manual approval action that was added to the pipeline is approved, CodePipeline deploys the tested and approved code to production instances\.

 CodePipeline can deploy applications to Amazon EC2 instances by using CodeDeploy, AWS Elastic Beanstalk, or AWS OpsWorks Stacks\. CodePipeline can also deploy container\-based applications to services by using Amazon ECS\. Developers can also use the integration points provided with CodePipeline to plug in other tools or services, including build services, test providers, or other deployment targets or systems\.

A pipeline can be as simple or as complex as your release process requires\.

### A Quick Look at Input and Output Artifacts<a name="welcome-introducing-artifacts"></a>

CodePipeline integrates with development tools to check for code changes and then build and deploy through all stages of the continuous delivery process\.

Stages use input and output artifacts that are stored in the artifact store for your pipeline\. An artifact store is an Amazon S3 bucket\. Your artifact store is in the same AWS Region as the pipeline to store items for all pipelines in that Region associated with your account\. Every time you use the console to create another pipeline in that Region, CodePipeline creates a folder for that pipeline in the bucket\. It uses that folder to store artifacts for your pipeline as the automated release process runs\. When you create or edit a pipeline, you must have an artifact bucket in the pipeline Region and then you must have one artifact bucket per Region where you are executing an action\.

CodePipeline zips and transfers the files for input or output artifacts as appropriate for the action type in the stage\. For example, at the start of a build action, CodePipeline retrieves the input artifact \(any files to be built\) and provides the artifact to the build action\. After the action is complete, CodePipeline takes the output artifact \(the built application\) and saves it to the output artifact bucket for use in the next stage\.

When you use the **Create Pipeline** wizard to configure or choose stages:

1.  CodePipeline triggers your pipeline to run when there is a commit to the source repository, providing the output artifact from the Source stage\.

1. The output artifact from the previous step is ingested as an input artifact to the Build stage\. An output artifact from the Build stage can be an updated application or an updated Docker image built to a container\.

1. The output artifact from the previous step is ingested as an input artifact to the Deploy stage, such as staging or production environments in the AWS Cloud\. You can deploy applications to a deployment fleet, or you can deploy container\-based applications to tasks running in Amazon ECS clusters\.

The following diagram shows a high\-level artifact workflow between stages in CodePipeline\.

![\[A high-level artifacts workflow in CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/Hi-Level-PipelineFlow.png)![\[A high-level artifacts workflow in CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A high-level artifacts workflow in CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## How Do I Get Started with CodePipeline?<a name="welcome-get-started"></a>

To get started with CodePipeline:

1. **Learn** how CodePipeline works by reading the [CodePipeline Concepts](concepts.md) section\.

1. **Prepare** to use CodePipeline by following the steps in [Getting Started with CodePipeline](getting-started-codepipeline.md)\.

1. **Experiment** with CodePipeline by following the steps in the [CodePipeline Tutorials](tutorials.md) tutorials\.

1. **Use** CodePipeline for your new or existing projects by following the steps in [Create a Pipeline in CodePipeline](pipelines-create.md)\.

## We Want to Hear from You<a name="welcome-contact-us"></a>

We welcome your feedback\. To contact us, visit [the CodePipeline Forum](https://forums.aws.amazon.com//forum.jspa?forumID=197)\.