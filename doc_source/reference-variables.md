# Variables<a name="reference-variables"></a>

This section is a reference only\. For information about creating variables, see [Working with Variables](actions-variables.md)\.

Variables allow you to configure your pipeline actions with values that are determined at the time of the action execution\. Variables can be produced by an action execution or be implicitly available at the start of each pipeline execution\.

Some action providers produce a defined set of variables\. You choose from default variable keys for that action provider, such as commit ID\.

**Variable Limits**

For limit information, see [Limits in AWS CodePipeline](limits.md)\.

**Topics**
+ [Concepts](#reference-variables-concepts)
+ [Configuring Variables](#reference-variables-workflow)
+ [Variable Resolution](#reference-variables-resolution)
+ [Rules for Variables](#reference-variables-rules)
+ [Variables Available for Pipeline Actions](#reference-variables-list)

## Concepts<a name="reference-variables-concepts"></a>

This section lists key terms and concepts related to variables and namespaces\.

### Variables<a name="reference-variables-concepts-variablekeys"></a>

Variables are key\-value pairs that can be used to dynamically configure actions in your pipeline\. There are currently two ways these variables are made available:
+ There is a set of variables that are implicitly available at the start of each pipeline execution\. This set currently includes `PipelineExecutionId`, the ID of the current pipeline execution\.
+ There are action types that produce sets of variables when they are executed\. You can see the variables produced by an action by inspecting the `outputVariables` field that is part of the [ListActionExecutions](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_ListActionExecutions.html) API\. To see which variables each action type produces, see the CodePipeline [Action Structure Reference](action-reference.md)\.

To reference these variables in your action configuration, you must use the variable reference syntax with the correct namespace\. 

For a list of available key names by action provider, see [Variables Available for Pipeline Actions](#reference-variables-list)\. For an example variable workflow, see [Configuring Variables ](#reference-variables-workflow)\.

### Namespaces<a name="reference-variables-concepts-namespaces"></a>

To ensure that variables can be uniquely referenced, they must be assigned to a namespace\. After you have a set of variables assigned to a namespace, they can be referenced in an action configuration by using the namespace and variable key with the following syntax:

```
#{namespace.variable_key}
```

There are two types of namespaces under which variables can be assigned:
+ **The codepipeline reserved namespace**

  This is the namespace assigned to the set of implicit variables available at the start of each pipeline execution\. This namespace is `codepipeline`\. Example variable reference:

  ```
  #{codepipeline.PipelineExecutionId}
  ```
+ **Action assigned namespace**

  This is a namespace that you assign to an action\. All variables produced by the action fall under this namespace\. To make the variables produced by an action available for use in a downstream action configuration, you must configure the producing action with a namespace\. Namespaces must be unique across the pipeline definition and cannot conflict with any artifact names\. Here is an example variable reference for an action configured with a namespace of `SourceVariables`\.

  ```
  #{SourceVariables.VersionId}
  ```

## Configuring Variables<a name="reference-variables-workflow"></a>

You configure an action to produce variables by declaring a namespace for the action\. The action must already be one of the action providers that generates variables\. Otherwise, the variables available are pipeline\-level variables\.

You declare the namespace either by: 
+ On the **Edit action ** page of the console, entering a namespace in **Variable namespace**\.
+ Entering a namespace in the `namespace` parameter field in the JSON pipeline structure\. 

In this example, you add the `namespace` parameter to the CodeCommit source action with the name `SourceVariables`\. This configures the action to produce the variables available for that action provider, such as `CommitId`\. 

```
{
    "name": "Source",
    "actions": [
        {
            "outputArtifacts": [
                {
                    "name": "SourceArtifact"
                }
            ],
            "name": "Source",
            "namespace": "SourceVariables",
            "configuration": {
                "RepositoryName": "MyRepo",
                "BranchName": "mainline",
                "PollForSourceChanges": "false"
            },
            "inputArtifacts": [],
            "region": "us-west-2",
            "actionTypeId": {
                "provider": "CodeCommit",
                "category": "Source",
                "version": "1",
                "owner": "AWS"
            },
            "runOrder": 1
        }
    ]
},
```

Next, you configure the downstream action to use the variables produced by the previous action\. You do this by:
+ On the **Edit action ** page of the console, entering the variable syntax \(for the downstream action\) in the action configuration fields\.
+ Entering the variable syntax \(for the downstream action\) in the action configuration fields in the JSON pipeline structure

In this example, the build action's configuration field shows environment variables that are updated upon the action execution\. The example specifies the namespace and variable for execution ID with `#{codepipeline.PipelineExecutionId}` and the namespace and variable for commit ID with **\#\{SourceVariables\.CommitId\}**\. 

```
{
    "name": "Build",
    "actions": [
        {
            "outputArtifacts": [
                {
                    "name": "BuildArtifact"
                }
            ],
            "name": "Build",
            "configuration": {
                "EnvironmentVariables": "[{\"name\":\"Release_ID\",\"value\":\"#{codepipeline.PipelineExecutionId}\",\"type\":\"PLAINTEXT\"},{\"name\":\"Commit_ID\",\"value\":\"#{SourceVariables.CommitId}\",\"type\":\"PLAINTEXT\"}]",
                "ProjectName": "env-var-test"
            },
            "inputArtifacts": [
                {
                    "name": "SourceArtifact"
                }
            ],
            "region": "us-west-2",
            "actionTypeId": {
                "provider": "CodeBuild",
                "category": "Build",
                "version": "1",
                "owner": "AWS"
            },
            "runOrder": 1
        }
    ]
},
```

## Variable Resolution<a name="reference-variables-resolution"></a>

Each time an action is executed as part of a pipeline execution, the variables it produces are available for use in any action that is guaranteed to occur after the producing action\. To use these variables in a consuming action, you can add them to the consuming action's configuration using the syntax shown in the previous example\. Before it performs a consuming action, CodePipeline resolves all of the variable references present in the configuration prior to initiating the action execution\.

![\[Example: Variables for multiple actions\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/variables-workflow-example.png)![\[Example: Variables for multiple actions\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Example: Variables for multiple actions\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## Rules for Variables<a name="reference-variables-rules"></a>

The following rules help you with the configuration of variables:
+ You specify the namespace and variable for an action through a new action property or by editing an action\. 
+ When you use the pipeline creation wizard, the console generates a namespace for each action created with the wizard\.
+ If the namespace isn't specified, the variables produced by that action cannot be referenced in any action configuration\.
+ To reference variables produced by an action, the referencing action must occur after the action that produces the variables\. This means it is either in a later stage than the action producing the variables, or in the same stage but at a higher run order\.

## Variables Available for Pipeline Actions<a name="reference-variables-list"></a>

The action provider determines which variables can be generated by the action\. 

Unlike a namespace which you can choose, most variable keys cannot be edited\. For example, for the Amazon S3 action provider, only the `ETag` and `VersionId` variable keys are available\.

For CodeBuild and Lambda actions, the variable keys are configured by the user\.

Each execution also has a set of CodePipeline\-generated pipeline variables that contain data about the execution, such as the pipeline release ID\. These variables can be consumed by any action in the pipeline\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/reference-variables.html)

For step\-by\-step procedures for managing variables, see [Working with Variables](actions-variables.md)\.