# Amazon ECR source actions and CloudWatch Events<a name="create-cwe-ecr-source"></a>

To add an Amazon ECR source action in CodePipeline, you can choose either to: 
+ Use the CodePipeline console **Create pipeline** wizard \([Create a pipeline \(console\)](pipelines-create.md#pipelines-create-console)\) or **Edit action** page to choose the **Amazon ECR** provider option\. The console creates an Amazon CloudWatch Events rule that starts your pipeline when the source changes\.
+ Use the CLI to add the action configuration for the `ECR` action and create additional resources as follows:
  + Use the `ECR` example action configuration in [Amazon ECR](action-reference-ECR.md) to create your action as shown in [Create a pipeline \(CLI\)](pipelines-create.md#pipelines-create-cli)\.
  + The change detection method defaults to starting the pipeline by polling the source\. You should disable periodic checks and create the change detection rule manually\. Use one of the following methods: [Create a CloudWatch Events rule for an Amazon ECR source \(console\)](create-cwe-ecr-source-console.md), [Create a CloudWatch Events rule for an Amazon ECR source \(CLI\)](create-cwe-ecr-source-cli.md), or [Create a CloudWatch Events rule for an Amazon ECR source \(AWS CloudFormation template\) ](create-cwe-ecr-source-cfn.md)\.

**Topics**
+ [Create a CloudWatch Events rule for an Amazon ECR source \(console\)](create-cwe-ecr-source-console.md)
+ [Create a CloudWatch Events rule for an Amazon ECR source \(CLI\)](create-cwe-ecr-source-cli.md)
+ [Create a CloudWatch Events rule for an Amazon ECR source \(AWS CloudFormation template\)](create-cwe-ecr-source-cfn.md)