# CodeStarSourceConnection<a name="action-reference-CodestarConnectionSource"></a>

Triggers a pipeline when a new commit is made on a third\-party source code repository\. The source action retrieves code changes when a pipeline is manually run or when a webhook event is sent from the source provider\.

**Note**  
This feature is not available in the Asia Pacific \(Hong Kong\) Region\. To use other source actions available in that Region, see [Source action integrations](integrations-action-type.md#integrations-source)\.

Connections can associate your AWS resources with the following third\-party repositories:
+ Bitbucket \(through the **Bitbucket** provider option in the CodePipeline console\)
+ GitHub and GitHub Enterprise Cloud \(through the **GitHub \(Version 2\)** provider option in the CodePipeline console\)
+ GitHub Enterprise Server \(through the **GitHub Enterprise Server** provider option in the CodePipeline console\)

After a code change is detected, you have the following options for passing the code to subsequent actions:
+ Default: Like other existing CodePipeline source actions, `CodeStarSourceConnection` can output a ZIP file with a shallow copy of your commit\.
+ Full clone: `CodeStarSourceConnection` can also be configured to output a URL reference to the repo for subsequent actions\.

  Currently, the Git URL reference can only be used by downstream CodeBuild actions to clone the repo and associated Git metadata\. Attempting to pass a Git URL reference to non\-CodeBuild actions results in an error\.

CodePipeline prompts you to add the AWS Connector installation app to your third\-party account when you create a connection\. You must have already created your third\-party provider account and repository before you can connect through the `CodeStarSourceConnection` action\.

**Note**  
To create or attach a policy to your IAM user or role with the permissions required to use AWS CodeStar connections, see [Connections permissions reference](https://docs.aws.amazon.com/dtconsole/latest/userguide/security-iam.html#permissions-reference-connections)\. Depending on when your CodePipeline service role was created, you might need to update its permissions to support AWS CodeStar connections\. For instructions, see [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services)\.

**Topics**
+ [Action type](#action-reference-CodestarConnectionSource-type)
+ [Configuration parameters](#action-reference-CodestarConnectionSource-config)
+ [Input artifacts](#action-reference-CodestarConnectionSource-input)
+ [Output artifacts](#action-reference-CodestarConnectionSource-output)
+ [Output variables](#action-reference-CodestarConnectionSource-variables)
+ [Action declaration](#action-reference-CodestarConnectionSource-example)
+ [Installing the installation app and creating a connection](#action-reference-CodestarConnectionSource-auth)
+ [See also](#action-reference-CodestarConnectionSource-links)

## Action type<a name="action-reference-CodestarConnectionSource-type"></a>
+ Category: `Source`
+ Owner: `AWS`
+ Provider: `CodeStarSourceConnection`
+ Version: `1`

## Configuration parameters<a name="action-reference-CodestarConnectionSource-config"></a>

****ConnectionArn****  
Required: Yes  
The connection ARN that is configured and authenticated for the source provider\.

****FullRepositoryId****  
Required: Yes  
The owner and name of the repository where source changes are to be detected\.  
Example: `some-user/my-repo`

****BranchName****  
Required: Yes  
The name of the branch where source changes are to be detected\.

****OutputArtifactFormat****  
Required: No  
Specifies the output artifact format\. Can be either `CODEBUILD_CLONE_REF` or `CODE_ZIP`\. If unspecified, the default is `CODE_ZIP`\.  
The `CODEBUILD_CLONE_REF` option can only be used by CodeBuild downstream actions\.  
If you choose this option, you will need to update the permissions for your CodeBuild project service role as shown in [Add CodeBuild GitClone permissions for connections to Bitbucket, GitHub, or GitHub Enterprise Server](troubleshooting.md#codebuild-role-connections)\. For a tutorial that shows you how to use the **Full clone** option, see [Tutorial: Use full clone with a GitHub pipeline source](tutorials-github-gitclone.md)\.

**DetectChanges**  
 Required: No  
Controls automatically starting your pipeline when a new commit is made on the configured repository and branch\. If unspecified, the default value is `true`, and the field does not display by default\. Valid values for this parameter:  
+ `true`: CodePipeline automatically starts your pipeline on new commits\.
+ `false`: CodePipeline does not start your pipeline on new commits\.

## Input artifacts<a name="action-reference-CodestarConnectionSource-input"></a>
+ **Number of Artifacts:** `0`
+ **Description:** Input artifacts do not apply for this action type\.

## Output artifacts<a name="action-reference-CodestarConnectionSource-output"></a>
+ **Number of Artifacts:** `1` 
+ **Description:** The artifacts generated from the repository are the output artifacts for the `CodeStarSourceConnection` action\. The source code commit ID is displayed in CodePipeline as the source revision for the triggered pipeline execution\. You can configure the output artifact of this action in:
  + A ZIP file that contains the contents of the configured repository and branch at the commit specified as the source revision for the pipeline execution\.
  + A JSON file that contains a URL reference to the repository so that downstream actions can perform Git commands directly\.
**Important**  
This option can only be used by CodeBuild downstream actions\.  
If you choose this option, you will need to update the permissions for your CodeBuild project service role as shown in [Troubleshooting CodePipeline](troubleshooting.md)\. For a tutorial that shows you how to use the **Full clone** option, see [Tutorial: Use full clone with a GitHub pipeline source](tutorials-github-gitclone.md)\.

## Output variables<a name="action-reference-CodestarConnectionSource-variables"></a>

When configured, this action produces variables that can be referenced by the action configuration of a downstream action in the pipeline\. This action produces variables which can be viewed as output variables, even if the action doesn't have a namespace\. You configure an action with a namespace to make those variables available to the configuration of downstream actions\.

For more information, see [Variables](reference-variables.md)\.

AuthorDate  
The date when the commit was authored, in timestamp format\.

BranchName  
The name of the branch for the repository where the source change was made\.

CommitId  
The commit ID that triggered the pipeline execution\.

CommitMessage  
The description message, if any, associated with the commit that triggered the pipeline execution\.

ConnectionArn  
The connection ARN that is configured and authenticated for the source provider\.

FullRepositoryName  
The name of the repository where the commit that triggered the pipeline was made\.

## Action declaration<a name="action-reference-CodestarConnectionSource-example"></a>

In the following example, the output artifact is set to the default ZIP format of `CODE_ZIP` for the connection with ARN `arn:aws:codestar-connections:region:account-id:connection/connection-id`\.

------
#### [ YAML ]

```
Name: Source
Actions:
  - InputArtifacts: []
    ActionTypeId:
      Version: '1'
      Owner: AWS
      Category: Source
      Provider: CodeStarSourceConnection
    OutputArtifacts:
      - Name: SourceArtifact
    RunOrder: 1
    Configuration:
      ConnectionArn: "arn:aws:codestar-connections:region:account-id:connection/connection-id"
      FullRepositoryId: "some-user/my-repo"
      BranchName: "main"
      OutputArtifactFormat: "CODE_ZIP"
    Name: ApplicationSource
```

------
#### [ JSON ]

```
{
    "Name": "Source",
    "Actions": [
        {
            "InputArtifacts": [],
            "ActionTypeId": {
                "Version": "1",
                "Owner": "AWS",
                "Category": "Source",
                "Provider": "CodeStarSourceConnection"
            },
            "OutputArtifacts": [
                {
                    "Name": "SourceArtifact"
                }
            ],
            "RunOrder": 1,
            "Configuration": {
                "ConnectionArn": "arn:aws:codestar-connections:region:account-id:connection/connection-id",
                "FullRepositoryId": "some-user/my-repo",
                "BranchName": "main",
                "OutputArtifactFormat": "CODE_ZIP"
            },
            "Name": "ApplicationSource"
        }
    ]
},
```

------

## Installing the installation app and creating a connection<a name="action-reference-CodestarConnectionSource-auth"></a>

The first time you use the console to add a new connection to a third\-party repository, you must authorize CodePipeline access to your repositories\. You choose or create an installation app that helps you connect to the account where you have created your third\-party code repository\.

 When you use the AWS CLI or an AWS CloudFormation template, you must provide the connection ARN of a connection that has already gone through the installation handshake\. Otherwise, the pipeline is not triggered\. 

**Note**  
For a `CodeStarSourceConnection` source action, you do not have to set up a webhook or default to polling\. The connections action manages your source change detection for you\.

## See also<a name="action-reference-CodestarConnectionSource-links"></a>

The following related resources can help you as you work with this action\.
+ [AWS::CodeStarConnections::Connection](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-codestarconnections-connection.html) – The AWS CloudFormation template reference for the AWS CodeStar Connections resource provides parameters and examples for connections in AWS CloudFormation templates\.
+ [AWS CodeStar Connections API Reference](https://docs.aws.amazon.com/codestar-connections/latest/APIReference/Welcome.html) – The *AWS CodeStar Connections API Reference* provides reference information for the available connections actions\.
+ To view the steps for creating a pipeline with source actions supported by connections, see the following:
  + For Bitbucket, use the **Bitbucket** option in the console or the `CodestarSourceConnection` action in the CLI\. See [Bitbucket connections](connections-bitbucket.md)\.
  + For GitHub and GitHub Enterprise Cloud, use the **GitHub** provider option in the console or the `CodestarSourceConnection` action in the CLI\. See [GitHub connections](connections-github.md)\.
  + For GitHub Enterprise Server, use the **GitHub Enterprise Server** provider option in the console or the `CodestarSourceConnection` action in the CLI\. See [GitHub Enterprise Server connections](connections-ghes.md)\.
+ To view a Getting Started tutorial that creates a pipeline with a Bitbucket source and a CodeBuild action, see [Getting started with connections](https://docs.aws.amazon.com/dtconsole/latest/userguide/getting-started-connections.html)\.
+ For a tutorial that shows you how to connect to a GitHub repository and use the **Full clone** option with a downstream CodeBuild action, see [Tutorial: Use full clone with a GitHub pipeline source](tutorials-github-gitclone.md)\.