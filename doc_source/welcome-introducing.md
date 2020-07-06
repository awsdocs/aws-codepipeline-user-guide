# A quick look at CodePipeline<a name="welcome-introducing"></a>

The following diagram shows an example release process using CodePipeline\.

![\[An example release process using CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/PipelineFlow.png)![\[An example release process using CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[An example release process using CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

In this example, when developers commit changes to a source repository, CodePipeline automatically detects the changes\. Those changes are built, and if any tests are configured, those tests are run\. After the tests are complete, the built code is deployed to staging servers for testing\. From the staging server, CodePipeline runs more tests, such as integration or load tests\. Upon the successful completion of those tests, and after a manual approval action that was added to the pipeline is approved, CodePipeline deploys the tested and approved code to production instances\.

 CodePipeline can deploy applications to EC2 instances by using CodeDeploy, AWS Elastic Beanstalk, or AWS OpsWorks Stacks\. CodePipeline can also deploy container\-based applications to services by using Amazon ECS\. Developers can also use the integration points provided with CodePipeline to plug in other tools or services, including build services, test providers, or other deployment targets or systems\.

A pipeline can be as simple or as complex as your release process requires\.