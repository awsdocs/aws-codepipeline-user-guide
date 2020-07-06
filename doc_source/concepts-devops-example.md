# DevOps pipeline example<a name="concepts-devops-example"></a>

As an example of a DevOps pipeline, a two\-stage pipeline might have a source stage called **Source** and a second stage called **Prod**\. In this example, the pipeline is updating the application with the latest changes and continuously deploying the latest result\. Before it deploys the latest application, the pipeline builds and tests the web application\. In this example, a group of developers have set up an infrastructure template and the source code for a web application in a GitHub repository called MyRepository\.

![\[A pipeline with sample stages and actions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/pipeline-elements-workflow-application.png)![\[A pipeline with sample stages and actions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A pipeline with sample stages and actions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

For example, a developer pushes a fix to the web application's index page, and the following occurs:

1. The application source code is maintained in a repository configured as a GitHub source action in the pipeline\. When developers push commits to the repository, CodePipeline detects the pushed change, and a pipeline execution starts from the **Source Stage**\.

1. The GitHub source action completes successfully \(that is, the latest changes have been downloaded and stored to the artifact bucket unique to that execution\)\. The *output artifacts* produced by the GitHub source action, which are the application files from the repository, are then used as the *input artifacts* to be worked on by the actions in the next stage\.

1. The pipeline execution transitions from the **Source Stage** to the **Prod Stage**\. The first action in the **Prod Stage** runs a build project created in CodeBuild and configured as a build action in the pipeline\. The build task pulls a build environment image and builds the web application in a virtual container\.

1. The next action in the **Prod Stage** is a unit test project created in CodeBuild and configured as a test action in the pipeline\.

1. The unit tested code is next worked on by a deploy action in the **Prod Stage** that deploys the application to a production environment\. After the deploy action completes successfully, the final action in the stage is an integration testing project created in CodeBuild and configured as a test action in the pipeline\. The test action calls to shell scripts that install and run a test tool, such as a link checker, on the web application\. After successful completion, the output is a built web application and a set of test results\.

Developers can add actions to the pipeline that deploy or further test the application after it is built and tested for each change\.

For more information, see [How pipeline executions work](concepts-how-it-works.md)\.