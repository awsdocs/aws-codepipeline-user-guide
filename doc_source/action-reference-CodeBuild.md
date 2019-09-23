# CodeBuild<a name="action-reference-CodeBuild"></a>

Allows you to run builds and tests as part of your pipeline\. When you run a CodeBuild build or test action, commands specified in the build spec are run inside of a CodeBuild container\. All artifacts that are specified as input artifacts to a CodeBuild action are available inside of the container running the commands\. CodeBuild can provide either a build or test action\. 

When you use the the CodePipeline wizard in the console to create a build project, the CodeBuild build project shows the source provider is CodePipeline\. When you create a build project in the CodeBuild console, you cannot specify CodePipeline as the source provider, but adding the build action to your pipeline adjusts the source in the CodeBuild console\. For more information, see the [AWS CodeBuild User Guide](https://docs.aws.amazon.com/codebuild/latest/userguide/)\.

**Topics**
+ [Action Type](#action-reference-CodeBuild-type)
+ [Configuration Parameters](#action-reference-CodeBuild-config)
+ [Input Artifacts](#action-reference-CodeBuild-input)
+ [Output Artifacts](#action-reference-CodeBuild-output)
+ [Action Declaration \(CodeBuild Example\)](#action-reference-CodeBuild-example)
+ [See Also](#action-reference-CodeBuild-links)

## Action Type<a name="action-reference-CodeBuild-type"></a>
+ Category: `Build` or `Test`
+ Owner: `AWS`
+ Provider: `CodeBuild`
+ Version: `1`

## Configuration Parameters<a name="action-reference-CodeBuild-config"></a>

**ProjectName**  
Required: Yes  
`ProjectName` is the name of the build project in CodeBuild\. 

**PrimarySource**  
Required: Conditional  
The value of the `PrimarySource` parameter must be the name of one of the input artifacts to the action\. CodeBuild looks for the build spec file and runs the build spec commands in the directory that contains the unzipped version of this artifact\.  
This parameter is required if multiple input artifacts are specified for a CodeBuild action\. When there is only one source artifact for the action, the `PrimarySource` artifact defaults to that artifact\.

## Input Artifacts<a name="action-reference-CodeBuild-input"></a>
+ **Number of Artifacts:** `1 to 5`
+ **Description:** CodeBuild looks for the build spec file and runs the build spec commands from the directory of the primary source artifact\. When more than one input source is specified for the CodeBuild action, this artifact must be set using the `PrimarySource` action configuration parameter in CodePipeline\. 

  Each input artifact is extracted to its own directory, the locations of which are stored in environment variables\. The directory for the primary source artifact is made available with `$CODEBUILD_SRC_DIR`\. The directories for all other input artifacts are made available with `$CODEBUILD_SRC_DIR_yourInputArtifactName`\.
**Note**  
The artifact configured in your CodeBuild project becomes the CodePipeline input artifact in your pipeline action\.

## Output Artifacts<a name="action-reference-CodeBuild-output"></a>
+ **Number of Artifacts:** `0 to 5` 
+ **Description:** These can be used to make the artifacts that are defined in the CodeBuild build spec file available to subsequent actions in the pipeline\. When only one output artifact is defined, this artifact can be defined directly under the `artifacts` section of the build spec file\. When more than one output artifact is specified, all artifacts referenced must be defined as secondary artifacts in the build spec file, and the names of the output artifacts in CodePipeline must match the artifact identifiers in the build spec file\.
**Note**  
The artifact configured in your CodeBuild project becomes the CodePipeline input artifact in your pipeline action\.

## Action Declaration \(CodeBuild Example\)<a name="action-reference-CodeBuild-example"></a>

------
#### [ YAML ]

```
name: Build
actions:
  - name: PackageExport
    actionTypeId:
      category: Build
      owner: AWS
      provider: CodeBuild
      version: '1'
    runOrder: 1
    configuration:
      ProjectName: my-build-project
      PrimarySource: MyApplicationSource1
    outputArtifacts:
      - name: MyPipeline-BuildArtifact
    inputArtifacts:
      - name: MyPipeline-SourceArtifact
```

------
#### [ JSON ]

```
{
                "name": "Build",
                "actions": [
                    {
                        "name": "PackageExport",
                        "actionTypeId": {
                            "category": "Build",
                            "owner": "AWS",
                            "provider": "CodeBuild",
                            "version": "1"
                        },
                        "runOrder": 1,
                        "configuration": {
                            "ProjectName": "my-build-project",
                            "PrimarySource": "MyApplicationSource1"
                        },
                        "outputArtifacts": [
                            {
                                "name": "MyPipeline-BuildArtifact"
                            }
                        ],
                        "inputArtifacts": [
                            {
                                "name": "MyPipeline-SourceArtifact"
                            }
                        ]
                    }
                ]
            },
```

------

## See Also<a name="action-reference-CodeBuild-links"></a>

The following related resources can help you as you work with this action\.
+ [AWS CodeBuild User Guide](https://docs.aws.amazon.com/codebuild/latest/userguide/) – For an example pipeline with a CodeBuild action, see [Use CodePipeline with CodeBuild to Test Code and Run Builds](https://docs.aws.amazon.com/codebuild/latest/userguide/how-to-create-pipeline.html)\. For examples of projects with multiple input and output CodeBuild artifacts, see [CodePipeline Integration with CodeBuild and Multiple Input Sources and Output Artifacts Sample ](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-pipeline-multi-input-output.html) and [Multiple Input Sources and Output Artifacts Sample](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-multi-in-out.html) \.
+ [Tutorial: Create a Pipeline That Builds and Tests Your Android App When a Commit Is Pushed to Your GitHub Repository](tutorials-codebuild-devicefarm.md) – This tutorial provides a sample build spec file and sample application to create a pipeline with a GitHub source that builds and tests an Android app with CodeBuild and AWS Device Farm\.