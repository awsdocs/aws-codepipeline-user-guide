# AWS AppConfig<a name="action-reference-AppConfig"></a>

AWS AppConfig is a capability of AWS Systems Manager\. AppConfig supports controlled deployments to applications of any size and includes built\-in validation checks and monitoring\. You can use AppConfig with applications hosted on Amazon EC2 instances, AWS Lambda, containers, mobile applications, or IoT devices\.

The `AppConfig` deploy action is an AWS CodePipeline action that deploys configurations stored in your pipeline source location to a specified AppConfig *application*, *environment*, and *configuration* profile\. It uses the preferences defined in an AppConfig *deployment strategy*\.

## Action type<a name="action-reference-AppConfig-type"></a>
+ Category: `Deploy`
+ Owner: `AWS`
+ Provider: `AppConfig`
+ Version: `1`

## Configuration parameters<a name="action-reference-AppConfig-config"></a>

**Application**  
Required: Yes  
The ID of the AWS AppConfig application with the details for your configuration and deployment\.

**Environment**  
Required: Yes  
The ID of the AWS AppConfig environment where the configuration is deployed\.

**ConfigurationProfile**  
Required: Yes  
The ID of the AWS AppConfig configuration profile to deploy\.

**InputArtifactConfigurationPath**  
Required: Yes  
The file path of the configuration data within the input artifact to deploy\.

**DeploymentStrategy**  
Required: No  
The AWS AppConfig deployment strategy to use for deployment\.

## Input artifacts<a name="action-reference-AppConfig-input"></a>
+ **Number of Artifacts:** `1`
+ **Description:** The input artifact for the deploy action\.

## Output artifacts<a name="action-reference-AppConfig-output"></a>

Not applicable\.

## Example action configuration<a name="action-reference-AppConfig-example"></a>

------
#### [ YAML ]

```
name: Deploy
actions:
  - name: Deploy
    actionTypeId:
      category: Deploy
      owner: AWS
      provider: AppConfig
      version: '1'
    runOrder: 1
    configuration:
      Application: 2s2qv57
      ConfigurationProfile: PvjrpU
      DeploymentStrategy: frqt7ir
      Environment: 9tm27yd
      InputArtifactConfigurationPath: /
    outputArtifacts: []
    inputArtifacts:
      - name: SourceArtifact
    region: us-west-2
    namespace: DeployVariables
```

------
#### [ JSON ]

```
{
    "name": "Deploy",
    "actions": [
        {
            "name": "Deploy",
            "actionTypeId": {
                "category": "Deploy",
                "owner": "AWS",
                "provider": "AppConfig",
                "version": "1"
            },
            "runOrder": 1,
            "configuration": {
                "Application": "2s2qv57",
                "ConfigurationProfile": "PvjrpU",
                "DeploymentStrategy": "frqt7ir",
                "Environment": "9tm27yd",
                "InputArtifactConfigurationPath": "/"
            },
            "outputArtifacts": [],
            "inputArtifacts": [
                {
                    "name": "SourceArtifact"
                }
            ],
            "region": "us-west-2",
            "namespace": "DeployVariables"
        }
    ]
}
```

------

## See also<a name="action-reference-StepFunctions-links"></a>

The following related resources can help you as you work with this action\.
+ [AWS AppConfig](https://docs.aws.amazon.com/systems-manager/latest/userguide/appconfig.html) – For information about AWS AppConfig deployments, see the *AWS Systems Manager User Guide*\.
+ [Tutorial: Create a pipeline that uses AWS AppConfig as a deployment provider](tutorials-AppConfig.md) – This tutorial gets you started setting up simple deployment configuration files and AppConfig resources, and shows you how to use the console to create a pipeline with an AWS AppConfig deployment action\.