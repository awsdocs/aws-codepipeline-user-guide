# AWS CloudFormation<a name="action-reference-CloudFormation"></a>

Executes an operation on an AWS CloudFormation stack\. A stack is a collection of AWS resources that you can manage as a single unit\. The resources in a stack are defined by the stack's AWS CloudFormation template\. A change set creates a comparison that can be viewed without altering the original stack\. For information about the types of AWS CloudFormation actions that can be performed on stacks and change sets, see the `ActionMode` parameter\.

**Topics**
+ [Action type](#action-reference-CloudFormation-type)
+ [Configuration parameters](#action-reference-CloudFormation-config)
+ [Input artifacts](#action-reference-CloudFormation-input)
+ [Output artifacts](#action-reference-CloudFormation-output)
+ [Output variables](#action-reference-CloudFormation-variables)
+ [Action declaration](#action-reference-CloudFormation-example)
+ [See also](#action-reference-CloudFormation-links)

## Action type<a name="action-reference-CloudFormation-type"></a>
+ Category: `Deploy`
+ Owner: `AWS`
+ Provider: `CloudFormation`
+ Version: `1`

## Configuration parameters<a name="action-reference-CloudFormation-config"></a>

**ActionMode**  
Required: Yes  
`ActionMode` is the name of the action AWS CloudFormation performs on a stack or change set\. The following action modes are available:  
+ `CHANGE_SET_EXECUTE` executes a change set for the resource stack that is based on a set of specified resource updates\. With this action, AWS CloudFormation starts to alter the stack\.
+ `CHANGE_SET_REPLACE` creates the change set, if it doesn't exist, based on the stack name and template that you submit\. If the change set exists, AWS CloudFormation deletes it, and then creates a new one\. 
+ `CREATE_UPDATE` creates the stack if it doesn't exist\. If the stack exists, AWS CloudFormation updates the stack\. Use this action to update existing stacks\. Unlike `REPLACE_ON_FAILURE`, if the stack exists and is in a failed state, CodePipeline won't delete and replace the stack\.
+ `DELETE_ONLY` deletes a stack\. If you specify a stack that doesn't exist, the action is completed successfully without deleting a stack\.
+ `REPLACE_ON_FAILURE` creates a stack, if it doesn't exist\. If the stack exists and is in a failed state, AWS CloudFormation deletes the stack, and then creates a new stack\. If the stack isn't in a failed state, AWS CloudFormation updates it\. 

  The stack is in a failed state when any of the following status types are displayed in AWS CloudFormation: 
  + `ROLLBACK_FAILED`
  + `CREATE_FAILED`
  + `DELETE_FAILED`
  + `UPDATE_ROLLBACK_FAILED`

  Use this action to automatically replace failed stacks without recovering or troubleshooting them\.
**Important**  
We recommend that you use `REPLACE_ON_FAILURE` for testing purposes only because it might delete your stack\.

**StackName**  
Required: Yes  
`StackName` is the name of an existing stack or a stack that you want to create\.

**Capabilities**  
Required: Conditional  
Use of `Capabilities` acknowledges that the template might have the capabilities to create and update some resources on its own, and that these capabilities are determined based on the types of resources in the template\.  
This property is required if you have IAM resources in your stack template or you create a stack directly from a template containing macros\. In order for the AWS CloudFormation action to successfully operate in this way, you must explicitly acknowledge that you would like it to do so with one of the following capabilities:  
+ `CAPABILITY_IAM` 
+ `CAPABILITY_NAMED_IAM` 
+ `CAPABILITY_AUTO_EXPAND` 
 You can specify more than one capability by using a comma \(no space\) between capabilities\. The example in [Action declaration](#action-reference-CloudFormation-example) shows an entry with both the CAPABILITY\_IAM and CAPABILITY\_AUTO\_EXPAND properties\.  
For more information about `Capabilities`, see the properties under [UpdateStack](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/API_UpdateStack.html) in the *AWS CloudFormation API Reference*\.

**ChangeSetName**  
Required: Conditional  
`ChangeSetName` is the name of an existing change set or a new change set that you want to create for the specified stack\.  
 This property is required for the following action modes: CHANGE\_SET\_REPLACE and CHANGE\_SET\_EXECUTE\. For all other action modes, this property is ignored\.

**RoleArn**  
Required: Conditional  
The `RoleArn` is the ARN of the IAM service role that AWS CloudFormation assumes when it operates on resources in the specified stack\. `RoleArn` is not applied when executing a change set\. If you do not use CodePipeline to create the change set, make sure that the change set or stack has an associated role\.  
This property is required for the following action modes:  
+ CREATE\_UPDATE
+ REPLACE\_ON\_FAILURE
+ DELETE\_ONLY
+ CHANGE\_SET\_REPLACE

**TemplatePath**  
Required: Conditional  
`TemplatePath` represents the AWS CloudFormation template file\. You include the file in an input artifact to this action\. The file name follows this format:  
`Artifactname::TemplateFileName`  
`Artifactname` is the input artifact name as it appears in CodePipeline\. For example, a source stage with the artifact name of `SourceArtifact` and a `template-export.json` file name creates a `TemplatePath` name, as shown in this example:  

```
"TemplatePath": "SourceArtifact::template-export.json"
```
This property is required for the following action modes:   
+ CREATE\_UPDATE
+ REPLACE\_ON\_FAILURE
+ CHANGE\_SET\_REPLACE
For all other action modes, this property is ignored\.  
The AWS CloudFormation template file containing the template body has a minimum length of 1 byte and a maximum length of 51,200 bytes\. For AWS CloudFormation deployment actions in CodePipeline, the maximum input artifact size is always 256 MB\. For more information, see [Quotas in AWS CodePipeline](limits.md) and [AWS CloudFormation Limits](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html)\.

**OutputFileName**  
Required: No  
Use `OutputFileName` to specify an output file name, such as `CreateStackOutput.json`, that CodePipeline adds to the pipeline output artifact for this action\. The JSON file contains the contents of the `Outputs` section from the AWS CloudFormation stack\.  
If you don't specify a name, CodePipeline doesn't generate an output file or artifact\.

**ParameterOverrides**  
Required: No  
Parameters are defined in your stack template and allow you to provide values for them at the time of stack creation or update\. You can use a JSON object to set parameter values in your template\. \(These values override those set in the template configuration file\.\) For more information about using parameter overrides, see [Configuration Properties \(JSON Object\)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline-action-reference.html#w4363ab1c13c13b9)\.  
We recommend that you use the template configuration file for most of your parameter values\. Use parameter overrides only for values that aren't known until the pipeline is running\. For more information, see [Using Parameter Override Functions with CodePipeline Pipelines](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline-parameter-override-functions.html) in the *AWS CloudFormation User Guide*\.  
All parameter names must be present in the stack template\.

**TemplateConfiguration**  
Required: No  
`TemplateConfiguration` is the template configuration file\. You include the file in an input artifact to this action\. It can contain template parameter values and a stack policy\. For more information about the template configuration file format, see [AWS CloudFormation Artifacts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline-cfn-artifacts.html)\.   
The template configuration file name follows this format:   
`Artifactname::TemplateConfigurationFileName`  
`Artifactname` is the input artifact name as it appears in CodePipeline\. For example, a source stage with the artifact name of `SourceArtifact` and a `test-configuration.json` file name creates a `TemplateConfiguration` name as shown in this example:  

```
"TemplateConfiguration": "SourceArtifact::test-configuration.json"
```

## Input artifacts<a name="action-reference-CloudFormation-input"></a>
+ **Number of Artifacts:** `0 to 10`
+ **Description:** As input, the AWS CloudFormation action optionally accepts artifacts for these purposes:
  + To provide the stack template file to execute\. \(See the `TemplatePath` parameter\.\)
  + To provide the template configuration file to use\. \(See the `TemplateConfiguration` parameter\.\) For more information about the template configuration file format, see [AWS CloudFormation Artifacts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline-cfn-artifacts.html)\. 
  + To provide the artifact for a Lambda function to be deployed as part of the AWS CloudFormation stack\.

## Output artifacts<a name="action-reference-CloudFormation-output"></a>
+ **Number of Artifacts:** `0 to 1` 
+ **Description:** If the `OutputFileName` parameter is specified, there is an output artifact produced by this action that contains a JSON file with the specified name\. The JSON file contains the contents of the Outputs section from the AWS CloudFormation stack\.

  For more information about the outputs section you can create for your AWS CloudFormation action, see [Outputs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html)\.

## Output variables<a name="action-reference-CloudFormation-variables"></a>

When configured, this action produces variables that can be referenced by the action configuration of a downstream action in the pipeline\. You configure an action with a namespace to make those variables available to the configuration of downstream actions\.

For AWS CloudFormation actions, variables are produced from any values designated in the `Outputs` section of a stack template\. Note that the only CloudFormation action modes that generate outputs are those that result in creating or updating a stack, such as stack creation, stack updates, and change set execution\. The corresponding action modes that generate variables are:
+ CREATE\_UPDATE
+ REPLACE\_ON\_FAILURE
+ ALWAYS\_REPLACE
+ CHANGE\_SET\_EXECUTE

For more information, see [Variables](reference-variables.md)\. For a tutorial that shows you how to create a pipeline with a CloudFormation deployment action in a pipeline that uses CloudFormation output variables, see [Tutorial: Create a pipeline that uses variables from AWS CloudFormation deployment actions](tutorials-cloudformation-action.md)\.

## Action declaration<a name="action-reference-CloudFormation-example"></a>

------
#### [ YAML ]

```
Name: ExecuteChangeSet
ActionTypeId:
  Category: Deploy
  Owner: AWS
  Provider: CloudFormation
  Version: '1'
RunOrder: 2
Configuration:
  ActionMode: CHANGE_SET_EXECUTE
  Capabilities: CAPABILITY_NAMED_IAM,CAPABILITY_AUTO_EXPAND
  ChangeSetName: pipeline-changeset
  ParameterOverrides: '{"ProjectId": "my-project","CodeDeployRole": "CodeDeploy_Role_ARN"}'
  RoleArn: CloudFormation_Role_ARN
  StackName: my-project--lambda
  TemplateConfiguration: 'my-project--BuildArtifact::template-configuration.json'
  TemplatePath: 'my-project--BuildArtifact::template-export.yml'
OutputArtifacts: []
InputArtifacts:
  - Name: my-project-BuildArtifact
```

------
#### [ JSON ]

```
{
    "Name": "ExecuteChangeSet",
    "ActionTypeId": {
        "Category": "Deploy",
        "Owner": "AWS",
        "Provider": "CloudFormation",
        "Version": "1"
    },
    "RunOrder": 2,
    "Configuration": {
        "ActionMode": "CHANGE_SET_EXECUTE",
        "Capabilities": "CAPABILITY_NAMED_IAM,CAPABILITY_AUTO_EXPAND",
        "ChangeSetName": "pipeline-changeset",
        "ParameterOverrides": "{\"ProjectId\": \"my-project\",\"CodeDeployRole\": \"CodeDeploy_Role_ARN\"}",
        "RoleArn": "CloudFormation_Role_ARN",
        "StackName": "my-project--lambda",
        "TemplateConfiguration": "my-project--BuildArtifact::template-configuration.json",
        "TemplatePath": "my-project--BuildArtifact::template-export.yml"
    },
    "OutputArtifacts": [],
    "InputArtifacts": [
        {
             "Name": "my-project-BuildArtifact"
        }
    ]
},
```

------

## See also<a name="action-reference-CloudFormation-links"></a>

The following related resources can help you as you work with this action\.
+ [Configuration Properties Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline-action-reference.html) – This reference chapter in the *AWS CloudFormation User Guide* provides more descriptions and examples for these CodePipeline parameters\.
+ [AWS CloudFormation API Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/) – The [CreateStack](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/API_CreateStack.html) parameter in the *AWS CloudFormation API Reference* describes stack parameters for AWS CloudFormation templates\.