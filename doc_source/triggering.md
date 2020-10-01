# CodeCommit source actions and CloudWatch Events<a name="triggering"></a>

To add a CodeCommit source action in CodePipeline, you can choose either to: 
+ Use the CodePipeline console **Create pipeline** wizard \([Create a pipeline \(console\)](pipelines-create.md#pipelines-create-console)\) or **Edit action** page to choose the **CodeCommit** provider option\. The console creates an Amazon CloudWatch Events rule that starts your pipeline when the source changes\.
+ Use the CLI to add the action configuration for the `CodeCommit` action and create additional resources as follows:
  + Use the `CodeCommit` example action configuration in [CodeCommit](action-reference-CodeCommit.md) to create your action as shown in [Create a pipeline \(CLI\)](pipelines-create.md#pipelines-create-cli)\.
  + The change detection method defaults to starting the pipeline by polling the source\. You should disable periodic checks and create the change detection rule manually\. Use one of the following methods: [Create a CloudWatch Events rule for a CodeCommit source \(console\)](pipelines-trigger-source-repo-changes-console.md), [Create a CloudWatch Events rule for a CodeCommit source \(CLI\)](pipelines-trigger-source-repo-changes-cli.md), or [Create a CloudWatch Events rule for a CodeCommit source \(AWS CloudFormation template\) ](pipelines-trigger-source-repo-changes-cfn.md)\.

**Topics**
+ [Create a CloudWatch Events rule for a CodeCommit source \(console\)](pipelines-trigger-source-repo-changes-console.md)
+ [Create a CloudWatch Events rule for a CodeCommit source \(CLI\)](pipelines-trigger-source-repo-changes-cli.md)
+ [Create a CloudWatch Events rule for a CodeCommit source \(AWS CloudFormation template\)](pipelines-trigger-source-repo-changes-cfn.md)