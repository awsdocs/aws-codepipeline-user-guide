# AWS CodeBuild<a name="action-reference-CodeBuild"></a>

Allows you to run builds and tests as part of your pipeline\. When you run a CodeBuild build or test action, commands specified in the build spec are run inside of a CodeBuild container\. All artifacts that are specified as input artifacts to a CodeBuild action are available inside of the container running the commands\. CodeBuild can provide either a build or test action\. For more information, see the [AWS CodeBuild User Guide](https://docs.aws.amazon.com/codebuild/latest/userguide/)\.

When you use the CodePipeline wizard in the console to create a build project, the CodeBuild build project shows the source provider is CodePipeline\. When you create a build project in the CodeBuild console, you cannot specify CodePipeline as the source provider, but adding the build action to your pipeline adjusts the source in the CodeBuild console\. For more information, see [ProjectSource](https://docs.aws.amazon.com/codebuild/latest/APIReference/API_ProjectSource.html) in the *AWS CodeBuild API Reference*\.

**Topics**
+ [Action type](#action-reference-CodeBuild-type)
+ [Configuration parameters](#action-reference-CodeBuild-config)
+ [Input artifacts](#action-reference-CodeBuild-input)
+ [Output artifacts](#action-reference-CodeBuild-output)
+ [Output variables](#action-reference-CodeBuild-variables)
+ [Action declaration \(CodeBuild example\)](#action-reference-CodeBuild-example)
+ [See also](#action-reference-CodeBuild-links)

## Action type<a name="action-reference-CodeBuild-type"></a>
+ Category: `Build` or `Test`
+ Owner: `AWS`
+ Provider: `CodeBuild`
+ Version: `1`

## Configuration parameters<a name="action-reference-CodeBuild-config"></a>

**ProjectName**  
Required: Yes  
`ProjectName` is the name of the build project in CodeBuild\.

**PrimarySource**  
Required: Conditional  
The value of the `PrimarySource` parameter must be the name of one of the input artifacts to the action\. CodeBuild looks for the build spec file and runs the build spec commands in the directory that contains the unzipped version of this artifact\.  
This parameter is required if multiple input artifacts are specified for a CodeBuild action\. When there is only one source artifact for the action, the `PrimarySource` artifact defaults to that artifact\.

**BatchEnabled**  
Required: No  
The Boolean value of the `BatchEnabled` parameter allows the action to run multiple builds in the same build execution\.  
When this option is enabled, the `CombineArtifacts` option is available\.  
For pipeline examples with batch builds enabled, see[CodePipeline integration with CodeBuild and batch builds](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-pipeline-batch.html)\.

**CombineArtifacts**  
Required: No  
The Boolean value of the `CombineArtifacts` parameter combines all build artifacts from a batch build into a single artifact file for the build action\.  
To use this option, the `BatchEnabled` parameter must be enabled\.

**EnvironmentVariables**  
Required: No  
The value of this parameter is used to set environment variables for the CodeBuild action in your pipeline\. The value for the `EnvironmentVariables` parameter takes the form of a JSON array of environment variable objects\. See the example parameter in [Action declaration \(CodeBuild example\)](#action-reference-CodeBuild-example)\.  
Each object has three parts, all of which are strings:  
+ `name`: The name or key of the environment variable\. 
+ `value`: The value of the environment variable\. When using the `PARAMETER_STORE` or `SECRETS_MANAGER` type, this value must be the name of a parameter you have already stored in AWS Systems Manager Parameter Store or a secret you have already stored in AWS Secrets Manager, respectively\.
**Note**  
We strongly discourage the use of environment variables to store sensitive values, especially AWS secret key IDs and secret access keys\. When you use the CodeBuild console or AWS CLI, environment variables are displayed in plain text\. For sensitive values, we recommend that you use the `SECRETS_MANAGER` type instead\. 
+ `type`: \(Optional\) The type of environment variable\. Valid values are `PARAMETER_STORE`, `SECRETS_MANAGER`, or `PLAINTEXT`\. When not specified, this defaults to `PLAINTEXT`\.
When you enter the `name`, `value`, and `type` for your environment variables configuration, especially if the environment variable contains CodePipeline output variable syntax, do not exceed the 1000\-character limit for the configuration’s value field\. A validation error is returned when this limit is exceeded\.
For more information, see [ EnvironmentVariable](https://docs.aws.amazon.com/codebuild/latest/APIReference/API_EnvironmentVariable.html)\.

## Input artifacts<a name="action-reference-CodeBuild-input"></a>
+ **Number of Artifacts:** `1 to 5`
+ **Description:** CodeBuild looks for the build spec file and runs the build spec commands from the directory of the primary source artifact\. When more than one input source is specified for the CodeBuild action, this artifact must be set using the `PrimarySource` action configuration parameter in CodePipeline\. 

  Each input artifact is extracted to its own directory, the locations of which are stored in environment variables\. The directory for the primary source artifact is made available with `$CODEBUILD_SRC_DIR`\. The directories for all other input artifacts are made available with `$CODEBUILD_SRC_DIR_yourInputArtifactName`\.
**Note**  
The artifact configured in your CodeBuild project becomes the input artifact used by the CodeBuild action in your pipeline\.

## Output artifacts<a name="action-reference-CodeBuild-output"></a>
+ **Number of Artifacts:** `0 to 5` 
+ **Description:** These can be used to make the artifacts that are defined in the CodeBuild build spec file available to subsequent actions in the pipeline\. When only one output artifact is defined, this artifact can be defined directly under the `artifacts` section of the build spec file\. When more than one output artifact is specified, all artifacts referenced must be defined as secondary artifacts in the build spec file\. The names of the output artifacts in CodePipeline must match the artifact identifiers in the build spec file\.
**Note**  
The artifact configured in your CodeBuild project becomes the CodePipeline input artifact in your pipeline action\.

  If the `CombineArtifacts` parameter is selected for batch builds, the output artifact location contains the combined artifacts from multiple builds that were run in the same execution\.

## Output variables<a name="action-reference-CodeBuild-variables"></a>

This action will produce as variables all environment variables that were exported as part of the build\. See [https://docs\.aws\.amazon\.com/codebuild/latest/userguide/build\-spec\-ref\.html\#exported\-variables\-build\-spec](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html#exported-variables-build-spec) for more details on how to export environment variables\.

For more information about variables in CodePipeline, see [Variables](reference-variables.md)\.

## Action declaration \(CodeBuild example\)<a name="action-reference-CodeBuild-example"></a>

------
#### [ YAML ]

```
Name: Build
Actions:
  - Name: PackageExport
    ActionTypeId:
      Category: Build
      Owner: AWS
      Provider: CodeBuild
      Version: '1'
    RunOrder: 1
    Configuration:
      BatchEnabled: 'true'
      CombineArtifacts: 'true'
      ProjectName: my-build-project
      PrimarySource: MyApplicationSource1
      EnvironmentVariables: '[{"name":"TEST_VARIABLE","value":"TEST_VALUE","type":"PLAINTEXT"},{"name":"ParamStoreTest","value":"PARAMETER_NAME","type":"PARAMETER_STORE"}]'
    OutputArtifacts:
      - Name: MyPipeline-BuildArtifact
    InputArtifacts:
      - Name: MyApplicationSource1
      - Name: MyApplicationSource2
```

------
#### [ JSON ]

```
{
    "Name": "Build",
    "Actions": [
        {
            "Name": "PackageExport",
            "ActionTypeId": {
                "Category": "Build",
                "Owner": "AWS",
                "Provider": "CodeBuild",
                "Version": "1"
            },
            "RunOrder": 1,
            "Configuration": {
                "BatchEnabled": "true",
                "CombineArtifacts": "true",
                "ProjectName": "my-build-project",
                "PrimarySource": "MyApplicationSource1",
                "EnvironmentVariables": "[{\"name\":\"TEST_VARIABLE\",\"value\":\"TEST_VALUE\",\"type\":\"PLAINTEXT\"},{\"name\":\"ParamStoreTest\",\"value\":\"PARAMETER_NAME\",\"type\":\"PARAMETER_STORE\"}]"
            },
            "OutputArtifacts": [
                {
                    "Name": "MyPipeline-BuildArtifact"
                }
            ],
            "InputArtifacts": [
                {
                    "Name": "MyApplicationSource1"
                },
                {
                    "Name": "MyApplicationSource2"
                }
            ]
        }
    ]
}
```

------

## See also<a name="action-reference-CodeBuild-links"></a>

The following related resources can help you as you work with this action\.
+ [AWS CodeBuild User Guide](https://docs.aws.amazon.com/codebuild/latest/userguide/) – For an example pipeline with a CodeBuild action, see [Use CodePipeline with CodeBuild to Test Code and Run Builds](https://docs.aws.amazon.com/codebuild/latest/userguide/how-to-create-pipeline.html)\. For examples of projects with multiple input and output CodeBuild artifacts, see [CodePipeline Integration with CodeBuild and Multiple Input Sources and Output Artifacts Sample ](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-pipeline-multi-input-output.html) and [Multiple Input Sources and Output Artifacts Sample](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-multi-in-out.html) \.
+ [Tutorial: Create a pipeline that builds and tests your Android app when a commit is pushed to your GitHub repository](tutorials-codebuild-devicefarm.md) – This tutorial provides a sample build spec file and sample application to create a pipeline with a GitHub source that builds and tests an Android app with CodeBuild and AWS Device Farm\.
+ [Build Specification Reference for CodeBuild ](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html) – This reference topic provides definitions and examples for understanding CodeBuild build spec files\. 