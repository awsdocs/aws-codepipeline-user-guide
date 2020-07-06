# CodeCommit<a name="action-reference-CodeCommit"></a>

Triggers the pipeline when a new commit is made on the configured CodeCommit repository and branch\.

If you use the console to create or edit the pipeline, CodePipeline creates a CodeCommit CloudWatch Events rule that starts your pipeline when a change occurs in the repository\.

You must have already created a CodeCommit repository before you connect the pipeline through a CodeCommit action\.

**Topics**
+ [Action type](#action-reference-CodeCommit-type)
+ [Configuration parameters](#action-reference-CodeCommit-config)
+ [Input artifacts](#action-reference-CodeCommit-input)
+ [Output artifacts](#action-reference-CodeCommit-output)
+ [Output variables](#action-reference-CodeCommit-variables)
+ [Action declaration \(CodeCommit example\)](#action-reference-CodeCommit-example)
+ [See also](#action-reference-CodeCommit-links)

## Action type<a name="action-reference-CodeCommit-type"></a>
+ Category: `Source`
+ Owner: `AWS`
+ Provider: `CodeCommit`
+ Version: `1`

## Configuration parameters<a name="action-reference-CodeCommit-config"></a>

**RepositoryName**  
Required: Yes  
The name of the repository where source changes are to be detected\.

**BranchName**  
Required: Yes  
The name of the branch where source changes are to be detected\.

**PollForSourceChanges**  
Required: No  
`PollForSourceChanges` controls whether CodePipeline polls the CodeCommit repository for source changes\. We recommend that you use CloudWatch Events to detect source changes instead\. For more information about configuring CloudWatch Events, see [Update pipelines for push events \(CodeCommit source\) \(CLI\)](update-change-detection.md#update-change-detection-cli-codecommit) or [Update pipelines for push events \(CodeCommit source\) \(AWS CloudFormation template\)](update-change-detection.md#update-change-detection-cfn-codecommit)\.  
If you intend to configure a CloudWatch Events rule, you must set `PollForSourceChanges` to `false` to avoid duplicate pipeline executions\.
Valid values for this parameter:  
+ `True`: If set, CodePipeline polls your repository for source changes\.
**Note**  
If you omit `PollForSourceChanges`, CodePipeline defaults to polling your repository for source changes\. This behavior is the same as if `PollForSourceChanges` is included and set to `true`\.
+ `False`: If set, CodePipeline does not poll your repository for source changes\. Use this setting if you intend to configure a CloudWatch Events rule to detect source changes\.

## Input artifacts<a name="action-reference-CodeCommit-input"></a>
+ **Number of Artifacts:** `0`
+ **Description:** Input artifacts do not apply for this action type\.

## Output artifacts<a name="action-reference-CodeCommit-output"></a>
+ **Number of Artifacts:** `1` 
+ **Description:** The output artifact of this action is a ZIP file that contains the contents of the configured repository and branch at the commit specified as the source revision for the pipeline execution\. The artifacts generated from the repository are the output artifacts for the CodeCommit action\. The source code commit ID is displayed in CodePipeline as the source revision for the triggered pipeline execution\.

## Output variables<a name="action-reference-CodeCommit-variables"></a>

When configured, this action produces variables that can be referenced by the action configuration of a downstream action in the pipeline\. This action produces variables which can be viewed as output variables, even if the action doesn't have a namespace\. You configure an action with a namespace to make those variables available to the configuration of downstream actions\.

For more information, see [Variables](reference-variables.md)\.

**CommitId**  
The CodeCommit commit ID that triggered the pipeline execution\. Commit IDs are the full SHA of the commit\.

**CommitMessage**  
The description message, if any, associated with the commit that triggered the pipeline execution\.

**RepositoryName**  
The name of the CodeCommit repository where the commit that triggered the pipeline was made\.

**BranchName**  
The name of the branch for the CodeCommit repository where the source change was made\.

**AuthorDate**  
The date when the commit was authored, in timestamp format\.  
For more information about the difference between an author and a committer in Git, see [Viewing the Commit History](http://git-scm.com/book/ch2-3.html) in Pro Git by Scott Chacon and Ben Straub\.

**CommitterDate**  
The date when the commit was committed, in timestamp format\.  
For more information about the difference between an author and a committer in Git, see [Viewing the Commit History](http://git-scm.com/book/ch2-3.html) in Pro Git by Scott Chacon and Ben Straub\.

## Action declaration \(CodeCommit example\)<a name="action-reference-CodeCommit-example"></a>

------
#### [ YAML ]

```
Actions:
  - OutputArtifacts:
      - Name: Artifact_MyWebsiteStack
    InputArtifacts: []
    Name: source
    Configuration:
      RepositoryName: MyWebsite
      BranchName: mainline
      PollForSourceChanges: 'false'
    RunOrder: 1
    ActionTypeId:
      Version: '1'
      Provider: CodeCommit
      Category: Source
      Owner: AWS
  Name: Source
```

------
#### [ JSON ]

```
{
    "Actions": [
        {
            "OutputArtifacts": [
                {
                    "Name": "Artifact_MyWebsiteStack"
                }
            ],
            "InputArtifacts": [],
            "Name": "source",
            "Configuration": {
                "RepositoryName": "MyWebsite",
                "BranchName": "mainline",
                "PollForSourceChanges": "false"
            },
            "RunOrder": 1,
            "ActionTypeId": {
                "Version": "1",
                "Provider": "CodeCommit",
                "Category": "Source",
                "Owner": "AWS"
            }
        }
    ],
    "Name": "Source"
},
```

------

## See also<a name="action-reference-CodeCommit-links"></a>

The following related resources can help you as you work with this action\.
+ [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md) – This tutorial provides a sample app spec file and sample CodeDeploy application and deployment group\. Use this tutorial to create a pipeline with a CodeCommit source that deploys to Amazon EC2 instances\.