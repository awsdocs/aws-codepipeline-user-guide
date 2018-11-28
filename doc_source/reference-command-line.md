--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# AWS CodePipeline Command Line Reference<a name="reference-command-line"></a>

Use this reference when working with the AWS CodePipeline commands and as a supplement to information documented in the [AWS CLI User Guide](https://docs.aws.amazon.com/cli/latest/userguide/) and the [AWS CLI Reference](https://docs.aws.amazon.com/cli/latest/reference/)\.

Before you use the AWS CLI, make sure you complete the prerequisites in [Getting Started with AWS CodePipeline](getting-started-codepipeline.md)\.

To view a list of all available AWS CodePipeline commands, run the following command:

```
aws codepipeline help
```

To view information about a specific AWS CodePipeline command, run the following command, where *command\-name* is the name of one of the commands listed below \(for example, create\-pipeline\):

```
aws codepipeline command-name help
```

To begin learning how to use the commands in the AWS CodePipeline extension to the AWS CLI, go to one or more of the following sections:
+ [Create a Custom Action \(CLI\)](actions-create-custom-action.md#actions-create-custom-action-cli)
+ [Create a Pipeline \(CLI\)](pipelines-create.md#pipelines-create-cli)
+ [Delete a Pipeline \(CLI\)](pipelines-delete.md#pipelines-delete-cli)
+ [Disable or Enable Transitions \(CLI\)](transitions.md#transitions-disable-enable-cli)
+ [View Pipeline Details and History \(CLI\)](pipelines-view.md#pipelines-view-cli)
+ [Retry Failed Actions \(CLI\)](actions-retry.md#actions-retry-cli)
+ [Start a Pipeline Manually \(CLI\)](pipelines-rerun-manually.md#pipelines-rerun-manually-cli)>
+ [Edit a Pipeline \(AWS CLI\)](pipelines-edit.md#pipelines-edit-cli)

You can also view examples of how to use most of these commands in [AWS CodePipeline Tutorials](tutorials.md)\.