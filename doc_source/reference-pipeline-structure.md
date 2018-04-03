# AWS CodePipeline Pipeline Structure Reference<a name="reference-pipeline-structure"></a>

By default, any pipeline you successfully create in AWS CodePipeline will have a valid structure\. However, if you manually create or edit a JSON file to create a pipeline or update a pipeline from the AWS CLI, you might inadvertently create a structure that is not valid\. The following reference can help you better understand the requirements for your pipeline structure and how to troubleshoot issues\. Refer also to the constraints documented in [Limits in AWS CodePipeline](limits.md), which apply to all pipelines\. 

**Topics**
+ [Pipeline and Stage Structure Requirements in AWS CodePipeline](#pipeline-requirements)
+ [Action Structure Requirements in AWS CodePipeline](#action-requirements)

## Pipeline and Stage Structure Requirements in AWS CodePipeline<a name="pipeline-requirements"></a>

A two\-stage pipeline has the following basic structure:

```
{
    "roleArn": "An IAM ARN for a service role, such as arn:aws:iam::80398EXAMPLE:role/AWS-CodePipeline-Service",
    "stages": [
        {
            "name": "SourceStageName",
            "actions": [
            ... See [Action Structure Requirements in AWS CodePipeline](#action-requirements) ...
            ]
        },
        {
            "name": "NextStageName",
            "actions": [
            ... See [Action Structure Requirements in AWS CodePipeline](#action-requirements) ...
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
+ The first stage of a pipeline must contain at least one source action, and can only contain source actions\.
+ Only the first stage of a pipeline may contain source actions\.
+ At least one stage in each pipeline must contain an action that is not a source action\.
+ All stage names within a pipeline must be unique\.
+ Stage names cannot be edited within the AWS CodePipeline console\. If you edit a stage name by using the AWS CLI, and the stage contains an action with one or more secret parameters \(such as an OAuth token\), the value of those secret parameters will not be preserved\. You must manually type the value of the parameters \(which are masked by four asterisks in the JSON returned by the AWS CLI\) and include them in the JSON structure\.
+ The pipeline metadata fields are distinct from the pipeline structure and cannot be edited\. When you update a pipeline, the date in the `updated` metadata field changes automatically\. 
+ When you edit or update a pipeline, the pipeline name cannot be changed\.
**Note**  
If you want to rename an existing pipeline, you can use the CLI `get-pipeline` command to build a JSON file containing your pipeline's structure\. Then you can use the CLI `create-pipeline` command to create a new pipeline with that structure and give it a new name\.

The version number of a pipeline is automatically generated and updated every time you update the pipeline\.

## Action Structure Requirements in AWS CodePipeline<a name="action-requirements"></a>

An action has the following high\-level structure:

```
[
                {
                   "inputArtifacts": [
                        An input artifact structure, if supported for the action category
                    ],
                   "name": "ActionName",  
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

  This is true for all actions, whether they are in the same stage or in following stages, but the input artifact does not have to be the next action in strict sequence from the action that provided the output artifact\. Actions in parallel can declare different output artifact bundles, which are in turn consumed by different following actions\.

  The following illustration provides an example of input and output artifacts in actions in a pipeline:  
![\[An example of input and output artifacts in actions in a pipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-artifactsexplained.png)![\[An example of input and output artifacts in actions in a pipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[An example of input and output artifacts in actions in a pipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
+ Output artifact names must be unique within a pipeline\. For example, a pipeline can include one action that has an output artifact named `"MyApp"` and another action that has an output artifact named `"MyBuiltApp"`\. However, a pipeline cannot include two actions that both have an output artifact named `"MyApp"`\.
+ If an action contains a parameter whose value is secret, such as the OAuth token for a GitHub source action, the value of that parameter is masked in the JSON by a series of four asterisks \(\*\*\*\*\)\. The actual value is stored, and as long as you do not edit that value, or change the name of the action or the name of the stage where that action runs, you do not have to supply that value when editing the JSON using the AWS CLI or AWS CodePipeline API\. However, if you do change the name of the action, or the name of the stage in which the action runs, the value of any secret parameters will be lost\. You must manually type the values for any secret parameters in the JSON, or the action will fail the next time the pipeline runs\. 
+ For all currently supported action types, the only valid version string is "1"\.
+ For all currently supported action types, the only valid owner string is "AWS", "ThirdParty", or "Custom"\. For more information, see the [AWS CodePipeline API Reference](http://docs.aws.amazon.com/codepipeline/latest/APIReference)\.
+ The default `runOrder` value for an action is 1\. The value must be a positive integer \(natural number\)\. You cannot use fractions, decimals, negative numbers, or zero\. To specify a serial sequence of actions, use the smallest number for the first action and larger numbers for each of the rest of the actions in sequence\. To specify parallel actions, use the same integer for each action you want to run in parallel\. 

  For example, if you want three actions to run in sequence in a stage, you would give the first action the `runOrder` value of 1, the second action the `runOrder` value of 2, and the third the `runOrder` value of 3\. However, if you want the second and third actions to run in parallel, you would give the first action the `runOrder` value of 1 and both the second and third actions the `runOrder` value of 2\.
**Note**  
The numbering of serial actions do not have to be in strict sequence\. For example, if you have three actions in a sequence and decide to remove the second action, you do not need to renumber the `runOrder` value of the third action\. Because the `runOrder` value of that action \(3\) is higher than the `runOrder` value of the first action \(1\), it will run serially after the first action in the stage\.
+ Depending on the action type, you can have the following number of input and output artifacts:  
**Action Type Constraints for Artifacts**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)
+ These are the valid `actionTypeId` categories for AWS CodePipeline:
  + `Source`
  + `Build`
  + `Approval`
  + `Deploy`
  + `Test`
  + `Invoke`


+ Valid provider types for an action category depend on the category\. For example, for a source action type, a valid provider type is `S3`, `GitHub`, or `CodeCommit`\. This example shows the structure for the `S3` source action provider:

  ```
  "actionTypeId": {
                              "category": "Source",
                              "owner": "AWS",
                              "version": "1",
                              "provider": "S3"
                          },
  ```

+ Every action must have a valid action configuration, which depends on the provider type for that action\. The following table lists the required action configuration elements for each valid provider type:  
**Action Configuration Properties for Provider Types**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)

  The following example shows a valid action configuration for a source action that uses Amazon S3:

  ```
  "configuration": {
    "S3Bucket": "awscodepipeline-demobucket-example-date",
    "S3ObjectKey": "CodePipelineDemoApplication.zip",
  }
  ```

  The following example shows a valid configuration for a deploy action that uses Amazon ECS:

  ```
  "configuration": {
    "ClusterName": "my-ecs-cluster",
    "ServiceName": "sample-app-service",
    "FileName": "imagedefinitions.json",
  }
  ```
