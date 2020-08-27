# Amazon Elastic Container Service and CodeDeploy Blue\-Green<a name="action-reference-ECSbluegreen"></a>

You can configure a pipeline in AWS CodePipeline that deploys container applications using a blue/green deployment\. In a blue/green deployment, you can launch a new version of your application alongside the old version, and you can test the new version before you reroute traffic to it\. You can also monitor the deployment process and rapidly roll back if there is an issue\.

The completed pipeline detects changes to your images or task definition file and and uses CodeDeploy to route and deploy traffic to an Amazon ECS cluster and load balancer\. CodeDeploy creates a new listener on your load balancer which can target your new task through a special port\. You can also configure the pipeline to use a source location, such as a CodeCommit repository, where your Amazon ECS task definition is stored\.

Before you create your pipeline, you must have already created the Amazon ECS resources, the CodeDeploy resources, and the load balancer and target group\. You must have already tagged and stored the image in your image repository, and uploaded the task definition and AppSpec file to your file repository\.

**Note**  
This topic describes the Amazon ECS to CodeDeploy blue/green deployment action for CodePipeline\. For reference information about Amazon ECS standard deployment actions in CodePipeline, see [Amazon Elastic Container Service](action-reference-ECS.md)\.

**Topics**
+ [Action Type](#action-reference-ECSbluegreen-type)
+ [Configuration Parameters](#action-reference-ECSbluegreen-config)
+ [Input Artifacts](#action-reference-ECSbluegreen-input)
+ [Output Artifacts](#action-reference-ECSbluegreen-output)
+ [Action Declaration](#action-reference-ECSbluegreen-example)
+ [See Also](#action-reference-ECSbluegreen-links)

## Action Type<a name="action-reference-ECSbluegreen-type"></a>
+ Category: `Deploy`
+ Owner: `AWS`
+ Provider: `CodeDeployToECS`
+ Version: `1`

## Configuration Parameters<a name="action-reference-ECSbluegreen-config"></a>

**ApplicationName**  
Required: Yes  
The name of the application in CodeDeploy\. Before you create your pipeline, you must have already created the application in CodeDeploy\.

**DeploymentGroupName**  
Required: Yes  
The deployment group specified for Amazon ECS task sets that you created for your CodeDeploy application\. Before you create your pipeline, you must have already created the deployment group in CodeDeploy\.

**TaskDefinitionTemplateArtifact**  
Required: Yes  
The name of the input artifact that provides the task definition file to the deployment action\. This is generally the name of the output artifact from the source action\. When you use the console, the default name for the source action output artifact is `SourceArtifact`\.

**AppSpecTemplateArtifact**  
Required: Yes  
The name of the input artifact that provides the AppSpec file to the deployment action\. This is generally the name of the output artifact from the source action\. When you use the console, the default name for the source action output artifact is `SourceArtifact`\.

**AppSpecTemplatePath**  
Required: No  
The file name of the AppSpec file stored in the pipeline source file location, such as your pipeline's CodeCommit repository\. The default file name is `appspec.yaml`\. If your AppSpec file has the same name and is stored at the root level in your file repository, you do not need to provide the file name\. If the path is not the default, enter the path and file name\.

**TaskDefinitionTemplatePath**  
Required: No  
The file name of the task definition stored in the pipeline file source location, such as your pipeline's CodeCommit repository\. The default file name is `taskdef.json`\. If your task definition file has the same name and is stored at the root level in your file repository, you do not need to provide the file name\. If the path is not the default, enter the path and file name\.

**Image<Number>ArtifactName**  
Required: No  
The name of the input artifact that provides the image to the deployment action\. This is generally the image repository's output artifact, such as output from the Amazon ECR source action\.  
Available values for `<Number>` are 1 through 4\.

**Image<Number>ContainerName**  
Required: No  
The name of the image available from the image repository, such as the Amazon ECR source repository\.  
Available values for `<Number>` are 1 through 4\.

## Input Artifacts<a name="action-reference-ECSbluegreen-input"></a>
+ **Number of Artifacts:** `1 to 5`
+ **Description:** The `CodeDeployToECS` action first looks for the task definition file and the AppSpec file in the source file repository, next looks for the image in the image repository, and then runs the AppSpec commands to deploy the task set and container to the cluster\.

  The `CodeDeployToECS` action looks for an `imageDetail.json` file that maps the image URI to the image\. When you commit a change to your Amazon ECR image repository, the pipeline ECR source action creates an `imageDetail.json` file for that commit\. You can also manually add an `imageDetail.json` file for a pipeline where the action is not automated\. For information about the `imageDetail.json` file, see [imageDetail\.json file for Amazon ECS blue/green deployment actions](file-reference.md#file-reference-ecs-bluegreen)\.

## Output Artifacts<a name="action-reference-ECSbluegreen-output"></a>
+ **Number of Artifacts:** `0` 
+ **Description:** Output artifacts do not apply for this action type\.

## Action Declaration<a name="action-reference-ECSbluegreen-example"></a>

------
#### [ YAML ]

```
Name: Deploy
Actions:
  - Name: Deploy
    ActionTypeId:
      Category: Deploy
      Owner: AWS
      Provider: CodeDeployToECS
      Version: '1'
    RunOrder: 1
    Configuration:
      AppSpecTemplateArtifact: SourceArtifact
      ApplicationName: ecs-cd-application
      DeploymentGroupName: ecs-deployment-group
      Image1ArtifactName: MyImage
      Image1ContainerName: IMAGE1_NAME
      TaskDefinitionTemplatePath: taskdef.json
      AppSpecTemplatePath: appspec.yaml
      TaskDefinitionTemplateArtifact: SourceArtifact
    OutputArtifacts: []
    InputArtifacts:
      - Name: SourceArtifact
      - Name: MyImage
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
                "Provider": "CodeDeployToECS",
                "Version": "1"
            },
            "RunOrder": 1,
            "Configuration": {
                "AppSpecTemplateArtifact": "SourceArtifact",
                "ApplicationName": "ecs-cd-application",
                "DeploymentGroupName": "ecs-deployment-group",
                "Image1ArtifactName": "MyImage",
                "Image1ContainerName": "IMAGE1_NAME",
                "TaskDefinitionTemplatePath": "taskdef.json",
                "AppSpecTemplatePath": "appspec.yaml",
                "TaskDefinitionTemplateArtifact": "SourceArtifact"
            },
            "OutputArtifacts": [],
            "InputArtifacts": [
                {
                    "Name": "SourceArtifact"
                },
                {
                    "Name": "MyImage"
                }
            ],
            "Region": "us-west-2",
            "Namespace": "DeployVariables"
        }
    ]
}
```

------

## See Also<a name="action-reference-ECSbluegreen-links"></a>

The following related resources can help you as you work with this action\.
+ [Tutorial: Create a pipeline with an Amazon ECR source and ECS\-to\-CodeDeploy deployment](tutorials-ecs-ecr-codedeploy.md) – This tutorial walks you through creation of the CodeDeploy and Amazon ECS resources you need for a blue/green deployment\. The tutorial shows you how to push a Docker image to Amazon ECR and create an Amazon ECS task definition that lists your Docker image name, container name, Amazon ECS service name, and load balancer configuration\. The tutorial then walks you through creating the AppSpec file and pipeline for your deployment\.
**Note**  
This topic and tutorial describe the CodeDeploy/ECS blue/green action for CodePipeline\. For information about ECS standard actions in CodePipeline, see [Tutorial: Continuous Deployment with CodePipeline](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cd-pipeline.html)\.
+ *AWS CodeDeploy User Guide* – For information about how to use the load balancer, production listener, target groups, and your Amazon ECS application in a blue/green deployment, see [Tutorial: Deploy an Amazon ECS Service](https://docs.aws.amazon.com/codedeploy/latest/userguide/tutorial-ecs-deployment.html)\. This reference information in the *AWS CodeDeploy User Guide* provides an overview for blue/green deployments with Amazon ECS and AWS CodeDeploy\.
+ *Amazon Elastic Container Service Developer Guide* – For information about working with Docker images and containers, ECS services and clusters, and ECS task sets, see [What Is Amazon ECS?](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/)