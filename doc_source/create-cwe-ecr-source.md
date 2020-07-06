# Use CloudWatch Events to start a pipeline \(Amazon ECR source\)<a name="create-cwe-ecr-source"></a>

You can use Amazon CloudWatch Events to trigger pipelines to start when rule or schedule criteria are met\. For pipelines with an Amazon ECR source, an Amazon CloudWatch Events rule detects source changes and then starts your pipeline\. When you use the console to create or change a pipeline, the rule and all associated resources are created for you\. If you create or change a pipeline with an Amazon ECR source in the AWS CLI or AWS CloudFormation, you must use these steps to create the Amazon CloudWatch Events rule and all associated resources manually\.

In Amazon CloudWatch Events, you create a rule to detect and react to changes in the state of the pipeline's defined source\.

**Topics**
+ [Create a CloudWatch Events rule for an Amazon ECR source \(console\)](create-cwe-ecr-source-console.md)
+ [Create a CloudWatch Events rule for an Amazon ECR source \(CLI\)](create-cwe-ecr-source-cli.md)
+ [Create a CloudWatch Events rule for an Amazon ECR source \(AWS CloudFormation template\)](create-cwe-ecr-source-cfn.md)