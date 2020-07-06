# AWS Lambda<a name="action-reference-Lambda"></a>

Allows you to execute a Lambda function as an action in your pipeline\. Using the event object that is an input to this function, the function has access to the action configuration, input artifact locations, output artifact locations, and other information required to access the artifacts\. For an example event passed to a Lambda invoke function, see [Example JSON event](#action-reference-Lambda-event)\. As part of the implementation of the Lambda function, there must be a call to either the `[PutJobSuccessResult API](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PutJobSuccessResult.html)` or `[PutJobFailureResult API](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PutJobFailureResult.html)`\. Otherwise, the execution of this action hangs until the action times out\. If you specify output artifacts for the action, they must be uploaded to the S3 bucket as part of the function implementation\.

## Action type<a name="action-reference-Lambda-type"></a>
+ Category: `Invoke`
+ Owner: `AWS`
+ Provider: `Lambda`
+ Version: `1`

## Configuration parameters<a name="action-reference-Lambda-config"></a>

**FunctionName**  
Required: Yes  
`FunctionName` is the name of the function created in Lambda\.

**UserParameters**  
Required: No  
A string that can be processed as input by the Lambda function\.

## Input artifacts<a name="action-reference-Lambda-input"></a>
+ **Number of Artifacts:** `0 to 5`
+ **Description:** The set of artifacts to be made available to the Lambda function\.

## Output artifacts<a name="action-reference-Lambda-output"></a>
+ **Number of Artifacts:** `0 to 5` 
+ **Description:** The set of artifacts produced as output by the Lambda function\.

## Output variables<a name="action-reference-Lambda-variables"></a>

This action will produce as variables all key\-value pairs that are included in the `outputVariables` section of the [PutJobSuccessResult API](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_PutJobSuccessResult.html) request\.

For more information about variables in CodePipeline, see [Variables](reference-variables.md)\.

## Example action configuration<a name="action-reference-Lambda-example"></a>

------
#### [ YAML ]

```
Name: Lambda
Actions:
  - Name: Lambda
    ActionTypeId:
      Category: Invoke
      Owner: AWS
      Provider: Lambda
      Version: '1'
    RunOrder: 1
    Configuration:
      FunctionName: myLambdaFunction
      UserParameters: 'http://192.0.2.4'
    OutputArtifacts: []
    InputArtifacts: []
    Region: us-west-2
```

------
#### [ JSON ]

```
{
    "Name": "Lambda",
    "Actions": [
        {
            "Name": "Lambda",
            "ActionTypeId": {
                "Category": "Invoke",
                "Owner": "AWS",
                "Provider": "Lambda",
                "Version": "1"
            },
            "RunOrder": 1,
            "Configuration": {
                "FunctionName": "myLambdaFunction",
                "UserParameters": "http://192.0.2.4"
            },
            "OutputArtifacts": [],
            "InputArtifacts": [],
            "Region": "us-west-2"
        }
    ]
},
```

------

## Example JSON event<a name="action-reference-Lambda-event"></a>

The Lambda action sends a JSON event that contains the job ID, the pipeline action configuration, input and output artifact locations, and any encryption information for the artifacts\. The job worker accesses these details to complete the Lambda action\. For more information, see [job details](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_JobDetails.html)\. The following is an example event\.

```
{
    "CodePipeline.job": {
        "id": "11111111-abcd-1111-abcd-111111abcdef",
        "accountId": "111111111111",
        "data": {
            "actionConfiguration": {
                "configuration": {
                    "FunctionName": "MyLambdaFunction",
                    "UserParameters": "input_parameter"
                }
            },
            "inputArtifacts": [
                {
                    "location": {
                        "s3Location": {
                            "bucketName": "bucket_name",
                            "objectKey": "filename"
                        },
                        "type": "S3"
                    },
                    "revision": null,
                    "name": "ArtifactName"
                }
            ],
            "outputArtifacts": [],
            "artifactCredentials": {
                "secretAccessKey": "secret_key",
                "sessionToken": "session_token",
                "accessKeyId": "access_key_ID"
            },
            "continuationToken": "token_ID",
            "encryptionKey": { 
              "id": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab",
              "type": "KMS"
            }
        }
    }
}
```

The JSON event provides the following job details for the Lambda action in CodePipeline:
+ `id`: The unique system\-generated ID of the job\.
+ `accountId`: The AWS account ID associated with the job\.
+ `data`: Other information required for a job worker to complete the job\. 
  + `actionConfiguration`: The action parameters for the Lambda action\. For definitions, see [Configuration parameters ](#action-reference-Lambda-config)\.
  + `inputArtifacts`: The artifact supplied to the action\.
    + `location`: The artifact store location\.
      + `s3Location`: The input artifact location information for the action\.
        + `bucketName`: The name of the pipeline artifact store for the action \(for example, an Amazon S3 bucket named codepipeline\-us\-east\-2\-1234567890\)\.
        + `objectKey`: The name of the application \(for example, `CodePipelineDemoApplication.zip`\)\.
      + `type`: The type of artifact in the location\. Currently, `S3` is the only valid artifact type\.
    + `revision`: The artifact's revision ID\. Depending on the type of object, this can be a commit ID \(GitHub\) or a revision ID \(Amazon Simple Storage Service\)\. For more information, see [ArtifactRevision](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_ArtifactRevision.html)\.
    + `name`: The name of the artifact to be worked on, such as `MyApp`\.
  + `outputArtifacts`: The output of the action\.
    + `location`: The artifact store location\.
      + `s3Location`: The output artifact location information for the action\.
        + `bucketName`: The name of the pipeline artifact store for the action \(for example, an Amazon S3 bucket named codepipeline\-us\-east\-2\-1234567890\)\.
        + `objectKey`: The name of the application \(for example, `CodePipelineDemoApplication.zip`\)\.
      + `type`: The type of artifact in the location\. Currently, `S3` is the only valid artifact type\.
    + `revision`: The artifact's revision ID\. Depending on the type of object, this can be a commit ID \(GitHub\) or a revision ID \(Amazon Simple Storage Service\)\. For more information, see [ArtifactRevision](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_ArtifactRevision.html)\.
    + `name`: The name of the output of an artifact, such as `MyApp`\.
  + `artifactCredentials`: The AWS session credentials used to access input and output artifacts in the Amazon S3 bucket\. These credentials are temporary credentials that are issued by AWS Security Token Service \(AWS STS\)\.
    + `secretAccessKey`: The secret access key for the session\.
    + `sessionToken`: The token for the session\.
    + `accessKeyId`: The secret access key for the session\.
  + `continuationToken`: A token generated by the action\. Future actions use this token to identify the running instance of the action\. When the action is complete, no continuation token should be supplied\.
  + `encryptionKey`: The encryption key used to encrypt the data in the artifact store, such as an AWS KMS key\. If this is undefined, the default key for Amazon Simple Storage Service is used\. 
    + `id`: The ID used to identify the key\. For an AWS KMS key, you can use the key ID, the key ARN, or the alias ARN\. 
    + `type`: The type of encryption key, such as an AWS KMS key\.

## See also<a name="action-reference-Lambda-links"></a>

The following related resources can help you as you work with this action\.
+ [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/) – For an example pipeline with a Lambda invoke action, see [Building a Continuous Delivery Pipeline for a Lambda Application with AWS CodePipeline](https://docs.aws.amazon.com/lambda/latest/dg/build-pipeline.html) in the *AWS Lambda Developer Guide*\.
+ [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/) – For more information about Lambda actions and AWS CloudFormation artifacts for pipelines, see [Using Parameter Override Functions with CodePipeline Pipelines](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline-parameter-override-functions.html), [Automating Deployment of Lambda\-based Applications](https://docs.aws.amazon.com/lambda/latest/dg/automating-deployment.html), and [AWS CloudFormation Artifacts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline-cfn-artifacts.html)\.
+ [Invoke an AWS Lambda function in a pipeline in CodePipeline](actions-invoke-lambda-function.md) – This procedure provides a sample Lambda function and shows you how to use the console to create a pipeline with a Lambda invoke action\.