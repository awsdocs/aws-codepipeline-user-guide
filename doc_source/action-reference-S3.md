# Amazon S3<a name="action-reference-S3"></a>

Triggers the pipeline when a new object is uploaded to the configured bucket and object key\.

**Note**  
When you create your source bucket, make sure you enable versioning on the bucket\. If you want to use an existing Amazon S3 bucket, see [Using versioning](http://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html) to enable versioning on an existing bucket\.

If you use the console to create or edit your pipeline, CodePipeline creates a CloudWatch Events rule that starts your pipeline when a change occurs in the S3 source bucket\.

You must have already created an Amazon S3 source bucket and uploaded the source files as a single ZIP file before you connect the pipeline through an Amazon S3 action\.

**Note**  
When Amazon S3 is the source provider for your pipeline, you may zip your source file or files into a single \.zip and upload the \.zip to your source bucket\. You may also upload a single unzipped file; however, downstream actions that expect a \.zip file will fail\.

**Topics**
+ [Action type](#action-reference-S3-type)
+ [Configuration parameters](#action-reference-S3-config)
+ [Input artifacts](#action-reference-S3-input)
+ [Output artifacts](#action-reference-S3-output)
+ [Output variables](#action-reference-S3-variables)
+ [Action declaration \(S3 example\)](#action-reference-S3-example)
+ [See also](#action-reference-S3-links)

## Action type<a name="action-reference-S3-type"></a>
+ Category: `Source`
+ Owner: `AWS`
+ Provider: `S3`
+ Version: `1`

## Configuration parameters<a name="action-reference-S3-config"></a>

**S3Bucket**  
Required: Yes  
The name of the Amazon S3 bucket where source changes are to be detected\.

**S3ObjectKey**  
Required: Yes  
The name of the Amazon S3 object key where source changes are to be detected\.

**PollForSourceChanges**  
Required: No  
`PollForSourceChanges` controls whether CodePipeline polls the Amazon S3 source bucket for source changes\. We recommend that you use CloudWatch Events and CloudTrail to detect source changes instead\. For more information about configuring CloudWatch Events, see [Update pipelines for push events \(Amazon S3 source\) \(CLI\)](update-change-detection.md#update-change-detection-cli-S3) or [Update pipelines for push events \(Amazon S3 source\) \(AWS CloudFormation template\)](update-change-detection.md#update-change-detection-cfn-s3)\.  
If you intend to configure CloudWatch Events, you must set `PollForSourceChanges` to `false` to avoid duplicate pipeline executions\.
Valid values for this parameter:  
+ `True`: If set, CodePipeline polls your source location for source changes\.
**Note**  
If you omit `PollForSourceChanges`, CodePipeline defaults to polling your source location for source changes\. This behavior is the same as if `PollForSourceChanges` is included and set to `true`\.
+ `False`: If set, CodePipeline does not poll your source location for source changes\. Use this setting if you intend to configure a CloudWatch Events rule to detect source changes\.

## Input artifacts<a name="action-reference-S3-input"></a>
+ **Number of Artifacts:** `0`
+ **Description:** Input artifacts do not apply for this action type\.

## Output artifacts<a name="action-reference-S3-output"></a>
+ **Number of Artifacts:** `1` 
+ **Description:** Provides the artifacts that are available in the source bucket configured to connect to the pipeline\. The artifacts generated from the bucket are the output artifacts for the Amazon S3 action\. The Amazon S3 object metadata \(ETag and version ID\) is displayed in CodePipeline as the source revision for the triggered pipeline execution\.

## Output variables<a name="action-reference-S3-variables"></a>

When configured, this action produces variables that can be referenced by the action configuration of a downstream action in the pipeline\. This action produces variables which can be viewed as output variables, even if the action doesn't have a namespace\. You configure an action with a namespace to make those variables available to the configuration of downstream actions\.

For more information about variables in CodePipeline, see [Variables](reference-variables.md)\.

**ETag**  
The entity tag for the object related to the source change that triggered the pipeline\. The ETag is an MD5 hash of the object\. ETag reflects only changes to the contents of an object, not its metadata\.

**VersionId**  
The version ID for the version of the object related to the source change that triggered the pipeline\.

## Action declaration \(S3 example\)<a name="action-reference-S3-example"></a>

------
#### [ YAML ]

```
Name: Source
Actions:
  - RunOrder: 1
    OutputArtifacts:
      - Name: SourceArtifact
    ActionTypeId:
      Provider: S3
      Owner: AWS
      Version: '1'
      Category: Source
    Region: us-west-2
    Name: Source
    Configuration:
      S3Bucket: my-bucket-oregon
      S3ObjectKey: my-application.zip
      PollForSourceChanges: 'false'
    InputArtifacts: []
```

------
#### [ JSON ]

```
{
    "Name": "Source",
    "Actions": [
        {
            "RunOrder": 1,
            "OutputArtifacts": [
                {
                    "Name": "SourceArtifact"
                }
            ],
            "ActionTypeId": {
                "Provider": "S3",
                "Owner": "AWS",
                "Version": "1",
                "Category": "Source"
            },
            "Region": "us-west-2",
            "Name": "Source",
            "Configuration": {
                "S3Bucket": "my-bucket-oregon",
                "S3ObjectKey": "my-application.zip",
                "PollForSourceChanges": "false"
            },
            "InputArtifacts": []
        }
    ]
},
```

------

## See also<a name="action-reference-S3-links"></a>

The following related resources can help you as you work with this action\.
+ [Tutorial: Create a simple pipeline \(S3 bucket\)](tutorials-simple-s3.md) – This tutorial provides a sample app spec file and sample CodeDeploy application and deployment group\. Use this tutorial to create a pipeline with an Amazon S3 source that deploys to Amazon EC2 instances\.