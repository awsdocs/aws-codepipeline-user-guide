# Amazon Elastic Container Service<a name="action-reference-ECS"></a>

You can use an Amazon ECS action to deploy an Amazon ECS service and task set\. An Amazon ECS service is a container application that is deployed to an Amazon ECS cluster\. An Amazon ECS cluster is a collection of instances that host your container application in the cloud\. The deployment requires a task definition that you create in Amazon ECS and an image definitions file that CodePipeline uses to deploy the image\.

Before you create your pipeline, you must have already created the Amazon ECS resources, tagged and stored the image in your image repository, and uploaded the BuildSpec file to your file repository\.

**Note**  
This reference topic describes the Amazon ECS standard deployment action for CodePipeline\. For reference information about Amazon ECS to CodeDeploy blue/green deployment actions in CodePipeline, see [Amazon Elastic Container Service and CodeDeploy Blue\-Green](action-reference-ECSbluegreen.md)\.

**Topics**
+ [Action Type](#action-reference-ECS-type)
+ [Configuration Parameters](#action-reference-ECS-config)
+ [Input Artifacts](#action-reference-ECS-input)
+ [Output Artifacts](#action-reference-ECS-output)
+ [Action Declaration](#action-reference-ECS-example)
+ [See Also](#action-reference-ECS-links)

## Action Type<a name="action-reference-ECS-type"></a>
+ Category: `Deploy`
+ Owner: `AWS`
+ Provider: `ECS`
+ Version: `1`

## Configuration Parameters<a name="action-reference-ECS-config"></a>

**ClusterName**  
Required: Yes  
The Amazon ECS cluster in Amazon ECS\.

**ServiceName**  
Required: Yes  
The Amazon ECS service that you created in Amazon ECS\.

**FileName**  
Required: No  
The name of your image definitions file, the JSON file that describes your service's container name and the image and tag\. You use this file for ECS standard deployments\. For more information, see [Input Artifacts](#action-reference-ECS-input) and [imagedefinitions\.json file for Amazon ECS standard deployment actions](file-reference.md#pipelines-create-image-definitions)\.

**DeploymentTimeout**  
Required: No  
The Amazon ECS deployment action timeout in minutes\. The timeout is configurable up to the maximum default timeout for this action\. For example:   

```
"DeploymentTimeout": "15"
```

## Input Artifacts<a name="action-reference-ECS-input"></a>
+ **Number of Artifacts:** `1`
+ **Description:** The action looks for an `imagedefinitions.json` file in the source file repository for the pipeline\. An image definitions document is a JSON file that describes your Amazon ECS container name and the image and tag\. CodePipeline uses the file to retrieve the image from your image repository such as Docker Hub or Amazon ECR\. You can manually add an `imagedefinitions.json` file for a pipeline where the action is not automated\. For information about the `imagedefinitions.json` file, see [imagedefinitions\.json file for Amazon ECS standard deployment actions](file-reference.md#pipelines-create-image-definitions)\.

  The action requires an existing image that has already been pushed to your image repository\. Because the image mapping is provided by the `imagedefinitions.json` file, the action does not require that the Amazon ECR source be included as a source action in the pipeline\.

## Output Artifacts<a name="action-reference-ECS-output"></a>
+ **Number of Artifacts:** `0` 
+ **Description:** Output artifacts do not apply for this action type\.

## Action Declaration<a name="action-reference-ECS-example"></a>

------
#### [ YAML ]

```
Name: DeployECS
ActionTypeId:
  Category: Deploy
  Owner: AWS
  Provider: ECS
  Version: '1'
RunOrder: 2
Configuration:
  ClusterName: my-ecs-cluster
  ServiceName: sample-app-service
  FileName: imagedefinitions.json
  DeploymentTimeout: '15'
OutputArtifacts: []
InputArtifacts:
  - Name: my-image
```

------
#### [ JSON ]

```
{
    "Name": "DeployECS",
    "ActionTypeId": {
        "Category": "Deploy",
        "Owner": "AWS",
        "Provider": "ECS",
        "Version": "1"
    },
    "RunOrder": 2,
    "Configuration": {
        "ClusterName": "my-ecs-cluster",
        "ServiceName": "sample-app-service",
        "FileName": "imagedefinitions.json",
        "DeploymentTimeout": "15"
    },
    "OutputArtifacts": [],
    "InputArtifacts": [
        {
            "Name": "my-image"
        }
    ]
},
```

------

## See Also<a name="action-reference-ECS-links"></a>

The following related resources can help you as you work with this action\.
+ [Tutorial: Continuous Deployment with CodePipeline](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cd-pipeline.html) – This tutorial shows you how to create a Dockerfile that you store in a source file repository such as CodeCommit\. Next, the tutorial shows you how to incorporate a CodeBuild BuildSpec file that builds and pushes your Docker image to Amazon ECR and creates your imagedefinitions\.json file\. Finally, you create an Amazon ECS service and task definition, and then you create your pipeline with an Amazon ECS deployment action\.
**Note**  
This topic and tutorial describe the Amazon ECS standard deployment action for CodePipeline\. For information about Amazon ECS to CodeDeploy blue/green deployment actions in CodePipeline, see [Tutorial: Create a pipeline with an Amazon ECR source and ECS\-to\-CodeDeploy deployment](tutorials-ecs-ecr-codedeploy.md)\.
+ *Amazon Elastic Container Service Developer Guide* – For information about working with Docker images and containers, Amazon ECS services and clusters, and Amazon ECS task sets, see [What Is Amazon ECS?](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/)