# Amazon ECR<a name="action-reference-ECR"></a>

Triggers the pipeline when a new image is pushed to the Amazon ECR repository\. This action provides an image definitions file referencing the URI for the image that was pushed to Amazon ECR\. This source action is often used in conjunction with another source action, such as CodeCommit, to allow a source location for all other source artifacts\. For more information, see [Tutorial: Create a Pipeline with an Amazon ECR Source and ECS\-to\-CodeDeploy Deployment](tutorials-ecs-ecr-codedeploy.md)\.

When you use the console to create or edit your pipeline, CodePipeline creates a CloudWatch Events rule that starts your pipeline when a change occurs in the repository\.

You must have already created an Amazon ECR repository and pushed an image before you connect the pipeline through an Amazon ECR action\.

**Topics**
+ [Action Type](#action-reference-ECR-type)
+ [Configuration Parameters](#action-reference-ECR-config)
+ [Input Artifacts](#action-reference-ECR-input)
+ [Output Artifacts](#action-reference-ECR-output)
+ [Output Variables](#action-reference-ECR-variables)
+ [Action Declaration \(Amazon ECR Example\)](#action-reference-ECR-example)
+ [See Also](#action-reference-ECR-links)

## Action Type<a name="action-reference-ECR-type"></a>
+ Category: `Source`
+ Owner: `AWS`
+ Provider: `ECR`
+ Version: `1`

## Configuration Parameters<a name="action-reference-ECR-config"></a>

**RepositoryName**  
Required: Yes  
The name of the Amazon ECR repository where the image was pushed\.

**ImageTag**  
Required: No  
The tag used for the image\.  
If a value for `ImageTag` is not specified, the value defaults to `latest`\.

## Input Artifacts<a name="action-reference-ECR-input"></a>
+ **Number of Artifacts:** `0`
+ **Description:** Input artifacts do not apply for this action type\.

## Output Artifacts<a name="action-reference-ECR-output"></a>
+ **Number of Artifacts:** `1` 
+ **Description:** This action produces an artifact that contains an `imageDetail.json` file that contains the URI for the image that triggered the pipeline execution\. For information about the `imageDetail.json` file, see [imageDetail\.json File for Amazon ECS Blue/Green Deployment Actions](file-reference.md#file-reference-ecs-bluegreen)\.

## Output Variables<a name="action-reference-ECR-variables"></a>

When configured, this action produces variables that can be referenced by the action configuration of a downstream action in the pipeline\. This action produces variables which can be viewed as output variables, even if the action doesn't have a namespace\. You configure an action with a namespace to make those variables available to the configuration of downstream actions\.

For more information, see [Variables](reference-variables.md)\.

**RegistryId**  
The AWS account ID associated with the registry that contains the repository\.

**RepositoryName**  
The name of the Amazon ECR repository where the image was pushed\.

**ImageTag**  
The tag used for the image\.

**ImageDigest**  
The `sha256` digest of the image manifest\.

**ImageURI**  
The URI for the image\.

## Action Declaration \(Amazon ECR Example\)<a name="action-reference-ECR-example"></a>

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
      Provider: ECR
    OutputArtifacts:
      - Name: SourceArtifact
    RunOrder: 1
    Configuration:
      ImageTag: latest
      RepositoryName: my-image-repo

    Name: ImageSource
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
                "Provider": "ECR"
            },
            "OutputArtifacts": [
                {
                    "Name": "SourceArtifact"
                }
            ],
            "RunOrder": 1,
            "Configuration": {
                "ImageTag": "latest",
                "RepositoryName": "my-image-repo"
            },
            "Name": "ImageSource"
        }
    ]
},
```

------

## See Also<a name="action-reference-ECR-links"></a>

The following related resources can help you as you work with this action\.
+ [Tutorial: Create a Pipeline with an Amazon ECR Source and ECS\-to\-CodeDeploy Deployment](tutorials-ecs-ecr-codedeploy.md) – This tutorial provides a sample app spec file and sample CodeDeploy application and deployment group to create a pipeline with a CodeCommit and Amazon ECR source that deploys to Amazon ECS instances\.