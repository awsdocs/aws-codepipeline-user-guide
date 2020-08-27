# CodePipeline pipeline structure reference<a name="reference-pipeline-structure"></a>

By default, any pipeline you successfully create in AWS CodePipeline has a valid structure\. However, if you manually create or edit a JSON file to create a pipeline or update a pipeline from the AWS CLI, you might inadvertently create a structure that is not valid\. The following reference can help you better understand the requirements for your pipeline structure and how to troubleshoot issues\. See the constraints in [Quotas in AWS CodePipeline](limits.md), which apply to all pipelines\. 

**Topics**
+ [Valid action types and providers in CodePipeline](#actions-valid-providers)
+ [Pipeline and stage structure requirements in CodePipeline](#pipeline-requirements)
+ [Action structure requirements in CodePipeline](#action-requirements)

## Valid action types and providers in CodePipeline<a name="actions-valid-providers"></a>

The pipeline structure format is used to build actions and stages in a pipeline\. An action type consists of an action category and provider type\. 

The following are the valid action categories in CodePipeline:
+ Source
+ Build
+ Test
+ Deploy
+ Approval
+ Invoke

Each action category has a designated set of providers\. Each action provider, such as Amazon S3, has a provider name, such as `S3`, that must be used in the `Provider` field in the action category in your pipeline structure\. 

There are three valid values for the `Owner` field in the action category section in your pipeline structure: `AWS`, `ThirdParty`, and `Custom`\.

To find the provider name and owner information for your action provider, see [Action structure reference](action-reference.md) or [Number of input and output artifacts for each action type](#reference-action-artifacts)\.

This table lists valid providers by action type\.


**Valid action providers by action type**  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)

Some action types in CodePipeline are available in select AWS Regions only\. It is possible that an action type is available in an AWS Region, but an AWS provider for that action type is not available\.

For more information about each action provider, see [Integrations with CodePipeline action types](integrations-action-type.md)\. 

The following sections provide examples for provider information and configuration properties for each action type\.

## Pipeline and stage structure requirements in CodePipeline<a name="pipeline-requirements"></a>

A two\-stage pipeline has the following basic structure:

```
{
    "roleArn": "An IAM ARN for a service role, such as arn:aws:iam::80398EXAMPLE:role/AWS-CodePipeline-Service",
    "stages": [
        {
            "name": "SourceStageName",
            "actions": [
            ... See  ...
            ]
        },
        {
            "name": "NextStageName",
            "actions": [
            ... See  ...
            ]
        }
    ],
    "artifactStore": {
        "type": "S3",
        "location": "The name of the Amazon S3 bucket automatically generated for you the first time you create a pipeline
            using the console, such as codepipeline-us-east-2-1234567890, or any Amazon S3 bucket you provision for this purpose"
    },
    "name": "YourPipelineName",
    "version": 1
}
```

The pipeline structure has the following requirements:
+ A pipeline must contain at least two stages\.
+ The first stage of a pipeline must contain at least one source action\. It can contain source actions only\.
+ Only the first stage of a pipeline can contain source actions\.
+ At least one stage in each pipeline must contain an action that is not a source action\.
+ All stage names in a pipeline must be unique\.
+ Stage names cannot be edited in the CodePipeline console\. If you edit a stage name by using the AWS CLI, and the stage contains an action with one or more secret parameters \(such as an OAuth token\), the value of those secret parameters is not preserved\. You must manually enter the value of the parameters \(which are masked by four asterisks in the JSON returned by the AWS CLI\) and include them in the JSON structure\.
+ The `artifactStore` field contains the artifact bucket type and location for a pipeline with all actions in the same AWS Region\. If you add actions in a Region different from your pipeline, the `artifactStores` mapping is used to list the artifact bucket for each AWS Region where actions are executed\. When you create or edit a pipeline, you must have an artifact bucket in the pipeline Region and then you must have one artifact bucket per Region where you plan to execute an action\. 

  The following example shows the basic structure for a pipeline with cross\-Region actions that uses the `artifactStores` parameter: 

  ```
      "pipeline": {
          "name": "YourPipelineName",
          "roleArn": "ServiceRoleARN",
          "artifactStores": {
              "us-east-1": {
                  "type": "S3",
                  "location": "S3 artifact bucket name, such as codepipeline-us-east-1-1234567890"
              },
              "us-west-2": {
                  "type": "S3",
                  "location": "S3 artifact bucket name, such as codepipeline-us-west-2-1234567890"
              }
          },
          "stages": [
              {
  
  ...
  ```
+ The pipeline metadata fields are distinct from the pipeline structure and cannot be edited\. When you update a pipeline, the date in the `updated` metadata field changes automatically\. 
+ When you edit or update a pipeline, the pipeline name cannot be changed\.
**Note**  
If you want to rename an existing pipeline, you can use the CLI `get-pipeline` command to build a JSON file that contains your pipeline's structure\. You can then use the CLI `create-pipeline` command to create a pipeline with that structure and give it a new name\.

The version number of a pipeline is automatically generated and updated every time you update the pipeline\.

## Action structure requirements in CodePipeline<a name="action-requirements"></a>

An action has the following high\-level structure:

```
[
                {
                   "inputArtifacts": [
                        An input artifact structure, if supported for the action category
                    ],
                   "name": "ActionName",
                   "region": "Region", 
                   "namespace": "source_namespace", 
                   "actionTypeId": {
                        "category": "An action category",
                        "owner": "AWS",
                        "version": "1"
                        "provider": "A provider type for the action category",
                   },
                   "outputArtifacts": [
                        An output artifact structure, if supported for the action category
                   ],
                   "configuration": {
                        Configuration details appropriate to the provider type
                   },
                   "runOrder": A positive integer that indicates the run order within the stage,
                }
            ]
```

For a list of example `configuration` details appropriate to the provider type, see [Configuration details by provider type](#structure-configuration-examples)\.

The action structure has the following requirements:
+ All action names within a stage must be unique\.
+ The input artifact of an action must exactly match the output artifact declared in a preceding action\. For example, if a preceding action includes the following declaration: 

  ```
  "outputArtifacts": [
      {
      "MyApp"
      }
  ],
  ```

   and there are no other output artifacts, then the input artifact of a following action must be: 

  ```
  "inputArtifacts": [
      {
      "MyApp"
      }
  ],
  ```

  This is true for all actions, whether they are in the same stage or in following stages, but the input artifact does not have to be the next action in strict sequence from the action that provided the output artifact\. Actions in parallel can declare different output artifact bundles, which are, in turn, consumed by different following actions\.

  The following illustration provides an example of input and output artifacts in actions in a pipeline:  
![\[An example of input and output artifacts in actions in a pipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-artifactsexplained.png)![\[An example of input and output artifacts in actions in a pipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[An example of input and output artifacts in actions in a pipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
+ Output artifact names must be unique in a pipeline\. For example, a pipeline can include one action that has an output artifact named `"MyApp"` and another action that has an output artifact named `"MyBuiltApp"`\. However, a pipeline cannot include two actions that both have an output artifact named `"MyApp"`\.
+ Cross\-Region actions use the `Region` field to designate the AWS Region where the actions are to be created\. The AWS resources created for this action must be created in the same Region provided in the `region` field\. You cannot create cross\-Region actions for the following action types:
  + Source actions
  + Actions by third\-party providers
  + Actions by custom providers
+ Actions can be configured with variables\. You use the `namespace` field to set the namespace and variable information for execution variables\. For reference information about execution variables and action output variables, see [Variables](reference-variables.md)\.
+ For all currently supported action types, the only valid version string is "1"\.
+ For all currently supported action types, the only valid owner string is "AWS", "ThirdParty", or "Custom"\. For more information, see the [CodePipeline API Reference](http://docs.aws.amazon.com/codepipeline/latest/APIReference)\.
+ The default `runOrder` value for an action is 1\. The value must be a positive integer \(natural number\)\. You cannot use fractions, decimals, negative numbers, or zero\. To specify a serial sequence of actions, use the smallest number for the first action and larger numbers for each of the rest of the actions in sequence\. To specify parallel actions, use the same integer for each action you want to run in parallel\. In the console, you can specify a serial sequence for an action by choosing **Add action group** at the level in the stage where you want it to run, or you can specify a parallel sequence by choosing **Add action**\. *Action group* refers to a run order of one or more actions at the same level\.

  For example, if you want three actions to run in sequence in a stage, you would give the first action the `runOrder` value of 1, the second action the `runOrder` value of 2, and the third the `runOrder` value of 3\. However, if you want the second and third actions to run in parallel, you would give the first action the `runOrder` value of 1 and both the second and third actions the `runOrder` value of 2\.
**Note**  
The numbering of serial actions do not have to be in strict sequence\. For example, if you have three actions in a sequence and decide to remove the second action, you do not need to renumber the `runOrder` value of the third action\. Because the `runOrder` value of that action \(3\) is higher than the `runOrder` value of the first action \(1\), it runs serially after the first action in the stage\.
+ When you use an Amazon S3 bucket as a deployment location, you also specify an object key\. An object key can be a file name \(object\) or a combination of a prefix \(folder path\) and file name\. You can use variables to specify the location name you want the pipeline to use\. Amazon S3 deployment actions support the use of the following variables in Amazon S3 object keys\.  
**Using variables in Amazon S3**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)
+ These are the valid `actionTypeId` categories for CodePipeline:
  + `Source`
  + `Build`
  + `Approval`
  + `Deploy`
  + `Test`
  + `Invoke`

  Some provider types and configuration options are provided here\.
+ Valid provider types for an action category depend on the category\. For example, for a source action type, a valid provider type is `S3`, `GitHub`, `CodeCommit`, or `Amazon ECR`\. This example shows the structure for a source action with an `S3` provider:

  ```
  "actionTypeId": {
    "category": "Source",
    "owner": "AWS",
    "version": "1",
    "provider": "S3"},
  ```
+ Every action must have a valid action configuration, which depends on the provider type for that action\. The following table lists the required action configuration elements for each valid provider type:  
**Action configuration properties for provider types**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)

**Topics**
+ [Number of input and output artifacts for each action type](#reference-action-artifacts)
+ [Default settings for the PollForSourceChanges parameter](#PollForSourceChanges-defaults)
+ [Configuration details by provider type](#structure-configuration-examples)

### Number of input and output artifacts for each action type<a name="reference-action-artifacts"></a>

Depending on the action type, you can have the following number of input and output artifacts:


**Action type constraints for artifacts**  

| Owner | Type of action | Provider | Valid number of input artifacts | Valid number of output artifacts | 
| --- | --- | --- | --- | --- | 
| AWS | Source | Amazon S3 | 0 | 1 | 
| AWS | Source | CodeCommit | 0 | 1 | 
| AWS | Source | Amazon ECR | 0 | 1 | 
| ThirdParty | Source | GitHub | 0 | 1 | 
| AWS | Build | CodeBuild | 1 to 5 | 0 to 5 | 
| AWS | Test | CodeBuild | 1 to 5 | 0 to 5 | 
| AWS | Test | AWS Device Farm | 1 | 0 | 
| AWS | Approval | Manual | 0 | 0 | 
| AWS | Deploy | Amazon S3 | 1 | 0 | 
| AWS | Deploy | AWS CloudFormation | 0 to 10 | 0 to 1 | 
| AWS | Deploy | CodeDeploy | 1 | 0 | 
| AWS | Deploy | AWS Elastic Beanstalk | 1 | 0 | 
| AWS | Deploy | AWS OpsWorks Stacks | 1 | 0 | 
| AWS | Deploy | Amazon ECS | 1 | 0 | 
| AWS | Deploy | AWS Service Catalog | 1 | 0 | 
| AWS | Invoke | AWS Lambda | 0 to 5 | 0 to 5 | 
| ThirdParty | Deploy | Alexa Skills Kit | 1 to 2 | 0 | 
| Custom | Build | Jenkins | 0 to 5 | 0 to 5 | 
| Custom | Test | Jenkins | 0 to 5 | 0 to 5 | 
| Custom | Any supported category | As specified in the custom action | 0 to 5 | 0 to 5 | 

### Default settings for the PollForSourceChanges parameter<a name="PollForSourceChanges-defaults"></a>

The `PollForSourceChanges` parameter default is determined by the method used to create the pipeline, as described in the following table\. In many cases, the `PollForSourceChanges` parameter defaults to true and must be disabled\. When the `PollForSourceChanges` parameter defaults to true, you should do the following:
+ Add the `PollForSourceChanges` parameter to the JSON file or AWS CloudFormation template\.
+ Create change detection resources \(CloudWatch Events rule or webhook, as applicable\)\.
+ Set the `PollForSourceChanges` parameter to false\.
**Note**  
If you create a CloudWatch Events rule or webhook, you must set the parameter to false to avoid triggering the pipeline more than once\.

  The `PollForSourceChanges` parameter is not used for Amazon ECR source actions\.
+   
**PollForSourceChanges parameter defaults**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)

### Configuration details by provider type<a name="structure-configuration-examples"></a>

This section lists valid `configuration` parameters for each action provider\.

The following example shows a valid configuration for a deploy action that uses AWS Service Catalog, for a pipeline that was created in the console without a separate configuration file:

```
"configuration": {
  "TemplateFilePath": "S3_template.json",
  "ProductVersionName": "devops S3 v2",
  "ProductType": "CLOUD_FORMATION_TEMPLATE",
  "ProductVersionDescription": "Product version description",
  "ProductId": "prod-example123456"
}
```

The following example shows a valid configuration for a deploy action that uses AWS Service Catalog, for a pipeline that was created in the console with a separate `sample_config.json` configuration file:

```
"configuration": {
  "ConfigurationFilePath": "sample_config.json",
  "ProductId": "prod-example123456"
}
```

The following example shows a valid configuration for a deploy action that uses Alexa Skills Kit:

```
"configuration": {
  "ClientId": "amzn1.application-oa2-client.aadEXAMPLE",
  "ClientSecret": "****",
  "RefreshToken": "****",
  "SkillId": "amzn1.ask.skill.22649d8f-0451-4b4b-9ed9-bfb6cEXAMPLE"
}
```

The following example shows a valid configuration for a deploy action that uses Amazon S3:

```
"configuration": {
  "BucketName": "website-bucket",
  "Extract": "true",
  "ObjectKey": "MyWebsite"
}
```

The following example shows a valid configuration for a manual approval:

```
"configuration": {
  "CustomData": "Comments on the manual approval",
  "ExternalEntityLink": "http://my-url.com",
  "NotificationArn": "arn:aws:sns:us-west-2:12345EXAMPLE:Notification"
}
```