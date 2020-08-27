# AWS CodeDeploy<a name="action-reference-CodeDeploy"></a>

You use an AWS CodeDeploy action to deploy application code to your deployment fleet\. Your deployment fleet can consist of Amazon EC2 instances, on\-premises instances, or both\.

**Note**  
This reference topic describes the CodeDeploy deployment action for CodePipeline where the deployment platform is Amazon EC2\. For reference information about Amazon Elastic Container Service to CodeDeploy blue/green deployment actions in CodePipeline, see [Amazon Elastic Container Service and CodeDeploy Blue\-Green](action-reference-ECSbluegreen.md)\.

**Topics**
+ [Action Type](#action-reference-CodeDeploy-type)
+ [Configuration Parameters](#action-reference-CodeDeploy-config)
+ [Input Artifacts](#action-reference-CodeDeploy-input)
+ [Output Artifacts](#action-reference-CodeDeploy-output)
+ [Action Declaration](#action-reference-CodeDeploy-example)
+ [See Also](#action-reference-CodeDeploy-links)

## Action Type<a name="action-reference-CodeDeploy-type"></a>
+ Category: `Deploy`
+ Owner: `AWS`
+ Provider: `CodeDeploy`
+ Version: `1`

## Configuration Parameters<a name="action-reference-CodeDeploy-config"></a>

**ApplicationName**  
Required: Yes  
The name of the application that you created in CodeDeploy\.

**DeploymentGroupName**  
Required: Yes  
The deployment group that you created in CodeDeploy\.

## Input Artifacts<a name="action-reference-CodeDeploy-input"></a>
+ **Number of Artifacts:** `1`
+ **Description:** The AppSpec file that CodeDeploy uses to determine:
  + What to install onto your instances from your application revision in Amazon S3 or GitHub\.
  + Which lifecycle event hooks to run in response to deployment lifecycle events\.

  For more information about the AppSpec file, see the [CodeDeploy AppSpec File Reference](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html)\.

## Output Artifacts<a name="action-reference-CodeDeploy-output"></a>
+ **Number of Artifacts:** `0` 
+ **Description:** Output artifacts do not apply for this action type\.

## Action Declaration<a name="action-reference-CodeDeploy-example"></a>

------
#### [ YAML ]

```
Name: Deploy
Actions:
  - Name: Deploy
    ActionTypeId:
      Category: Deploy
      Owner: AWS
      Provider: CodeDeploy
      Version: '1'
    RunOrder: 1
    Configuration:
      ApplicationName: my-application
      DeploymentGroupName: my-deployment-group
    OutputArtifacts: []
    InputArtifacts:
      - Name: SourceArtifact
    Region: us-west-2
    Namespace: DeployVariables
```

------
#### [ JSON ]

```
{
    "Name": "Deploy",
    "Actions": [
        {
            "Name": "Deploy",
            "ActionTypeId": {
                "Category": "Deploy",
                "Owner": "AWS",
                "Provider": "CodeDeploy",
                "Version": "1"
            },
            "RunOrder": 1,
            "Configuration": {
                "ApplicationName": "my-application",
                "DeploymentGroupName": "my-deployment-group"
            },
            "OutputArtifacts": [],
            "InputArtifacts": [
                {
                    "Name": "SourceArtifact"
                }
            ],
            "Region": "us-west-2",
            "Namespace": "DeployVariables"
        }
    ]
},
```

------

## See Also<a name="action-reference-CodeDeploy-links"></a>

The following related resources can help you as you work with this action\.
+ [Tutorial: Create a simple pipeline \(S3 bucket\)](tutorials-simple-s3.md) – This tutorial walks you through the creation of a source bucket, EC2 instances, and CodeDeploy resources to deploy a sample application\. You then build your pipeline with a CodeDeploy deployment action that deploys code maintained in your S3 bucket to your Amazon EC2 instance\.
+ [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md) – This tutorial walks you through the creation of your CodeCommit source repository, EC2 instances, and CodeDeploy resources to deploy a sample application\. You then build your pipeline with a CodeDeploy deployment action that deploys code from your CodeCommit repository to your Amazon EC2 instance\.
+ [CodeDeploy AppSpec File Reference](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html) – This reference chapter in the *AWS CodeDeploy User Guide* provides reference information and examples for CodeDeploy AppSpec files\.