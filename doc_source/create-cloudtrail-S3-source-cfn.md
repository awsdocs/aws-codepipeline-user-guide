--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Create a CloudWatch Events Rule That Starts Your Amazon S3 Pipeline \(AWS CloudFormation Template\)<a name="create-cloudtrail-S3-source-cfn"></a>

To use AWS CloudFormation to create a rule, update your template as shown here\.<a name="proc-cfn-event-s3-createrule"></a>

**To create a CloudWatch Events rule with Amazon S3 as the event source and AWS CodePipeline as the target and apply the permissions policy**

1. In the template, under `Resources`, use the `AWS::IAM::Role` AWS CloudFormation resource to configure the IAM role that allows your event to start your pipeline\. This entry creates a role that uses two policies:
   + The first policy allows the role to be assumed\.
   + The second policy provides permissions to start the pipeline\.

   **Why am I making this change?** Adding `AWS::IAM::Role` resource enables AWS CloudFormation to create permissions for Amazon CloudWatch Events\. This resource is added to your AWS CloudFormation stack\.

------
#### [ YAML ]

   ```
     AmazonCloudWatchEventRole:
       Type: AWS::IAM::Role
       Properties:
         AssumeRolePolicyDocument:
           Version: 2012-10-17
           Statement:
             -
               Effect: Allow
               Principal:
                 Service:
                   - events.amazonaws.com
               Action: sts:AssumeRole
         Path: /
         Policies:
           -
             PolicyName: cwe-pipeline-execution
             PolicyDocument:
               Version: 2012-10-17
               Statement:
                 -
                   Effect: Allow
                   Action: codepipeline:StartPipelineExecution
                   Resource: !Join [ '', [ 'arn:aws:codepipeline:', !Ref 'AWS::Region', ':', !Ref 'AWS::AccountId', ':', !Ref AppPipeline ] ]
   
   
   ...
   ```

------
#### [ JSON ]

   ```
           "AmazonCloudWatchEventRole": {
               "Type": "AWS::IAM::Role",
               "Properties": {
                   "AssumeRolePolicyDocument": {
                       "Version": "2012-10-17",
                       "Statement": [
                           {
                               "Effect": "Allow",
                               "Principal": {
                                   "Service": [
                                       "events.amazonaws.com"
                                   ]
                               },
                               "Action": "sts:AssumeRole"
                           }
                       ]
                   },
                   "Path": "/",
                   "Policies": [
                       {
                           "PolicyName": "cwe-pipeline-execution",
                           "PolicyDocument": {
                               "Version": "2012-10-17",
                               "Statement": [
                                   {
                                       "Effect": "Allow",
                                       "Action": "codepipeline:StartPipelineExecution",
                                       "Resource": {
                                           "Fn::Join": [
                                               "",
                                               [
                                                   "arn:aws:codepipeline:",
                                                   {
                                                       "Ref": "AWS::Region"
                                                   },
                                                   ":",
                                                   {
                                                       "Ref": "AWS::AccountId"
                                                   },
                                                   ":",
                                                   {
                                                       "Ref": "AppPipeline"
                                                   }
                                               ]
                                           ]
   
   
   ...
   ```

------

1. Use the `AWS::Events::Rule` AWS CloudFormation resource to add a CloudWatch Events rule\. This event pattern creates an event that monitors `PutObject` and `CompleteMultipartUpload` on your Amazon S3 source bucket\. In addition, include a target of your pipeline\. When `PutObject` or `CompleteMultipartUpload` occurs, this rule invokes `StartPipelineExecution` on your target pipeline\.

   **Why am I making this change?** Adding the `AWS::Events::Rule` resource enables AWS CloudFormation to create the event\. This resource is added to your AWS CloudFormation stack\.

------
#### [ YAML ]

   ```
     AmazonCloudWatchEventRule:
       Type: AWS::Events::Rule
       Properties:
         EventPattern:
           source:
             - aws.s3
           detail-type:
             - 'AWS API Call via CloudTrail'
           detail:
             eventSource:
               - s3.amazonaws.com
             eventName:
               - PutObject
               - CompleteMultipartUpload
             resources:
               ARN:
                 - !Join [ '', [ !GetAtt SourceBucket.Arn, '/', !Ref SourceObjectKey ] ]
         Targets:
           -
             Arn:
               !Join [ '', [ 'arn:aws:codepipeline:', !Ref 'AWS::Region', ':', !Ref 'AWS::AccountId', ':', !Ref AppPipeline ] ]
             RoleArn: !GetAtt AmazonCloudWatchEventRole.Arn
             Id: codepipeline-AppPipeline
   
   
   ...
   ```

------
#### [ JSON ]

   ```
           "AmazonCloudWatchEventRule": {
               "Type": "AWS::Events::Rule",
               "Properties": {
                   "EventPattern": {
                       "source": [
                           "aws.s3"
                       ],
                       "detail-type": [
                           "AWS API Call via CloudTrail"
                       ],
                       "detail": {
                           "eventSource": [
                               "s3.amazonaws.com"
                           ],
                           "eventName": [
                               "PutObject",
                               "CompleteMultipartUpload"
                           ],
                           "resources": {
                               "ARN": [
                                   {
                                       "Fn::Join": [
                                           "",
                                           [
                                               {
                                                   "Fn::GetAtt": [
                                                       "SourceBucket",
                                                       "Arn"
                                                   ]
                                               },
                                               "/",
                                               {
                                                   "Ref": "SourceObjectKey"
                                               }
                                           ]
                                       ]
                                   }
                               ]
                           }
                       }
                   },
                   "Targets": [
                       {
                           "Arn": {
                               "Fn::Join": [
                                   "",
                                   [
                                       "arn:aws:codepipeline:",
                                       {
                                           "Ref": "AWS::Region"
                                       },
                                       ":",
                                       {
                                           "Ref": "AWS::AccountId"
                                       },
                                       ":",
                                       {
                                           "Ref": "AppPipeline"
                                       }
                                   ]
                               ]
                           },
                           "RoleArn": {
                               "Fn::GetAtt": [
                                   "AmazonCloudWatchEventRole",
                                   "Arn"
                               ]
                           },
                           "Id": "codepipeline-AppPipeline"
                       }
                   ]
               }
           }
       },
   
   
   ...
   ```

------

1. Add this snippet to your first template to allow cross\-stack functionality:

------
#### [ YAML ]

   ```
   Outputs:
     SourceBucketARN:
       Description: "S3 bucket ARN that Cloudtrail will use"
       Value: !GetAtt SourceBucket.Arn
       Export:
         Name: SourceBucketARN
   ```

------
#### [ JSON ]

   ```
       "Outputs" : {
           "SourceBucketARN" : {
               "Description" : "S3 bucket ARN that Cloudtrail will use",
               "Value" : { "Fn::GetAtt": ["SourceBucket", "Arn"] },
               "Export" : {
                   "Name" : "SourceBucketARN"
               }
           }
   
   
   ...
   ```

------

1. Save your updated template to your local computer, and open the AWS CloudFormation console\. 

1. Choose your stack, and then choose **Create Change Set for Current Stack**\. 

1. Upload your updated template, and then view the changes listed in AWS CloudFormation\. These are the changes that will be made to the stack\. You should see your new resources in the list\.

1. Choose **Execute**\.<a name="proc-cfn-flag-s3"></a>

**To edit your pipeline's PollForSourceChanges parameter**
+ In the template, change `PollForSourceChanges` to `false`\. If you did not include `PollForSourceChanges` in your pipeline definition, add it and set it to `false`\.

  **Why am I making this change?** Changing `PollForSourceChanges` to `false` turns off periodic checks so you can use event\-based change detection only\.

------
#### [ YAML ]

  ```
            Name: Source
            Actions: 
              - 
                Name: SourceAction
                ActionTypeId: 
                  Category: Source
                  Owner: AWS
                  Version: 1
                  Provider: S3
                OutputArtifacts: 
                  - Name: SourceOutput
                Configuration: 
                  S3Bucket: !Ref SourceBucket
                  S3ObjectKey: !Ref SourceObjectKey
                  PollForSourceChanges: false
                RunOrder: 1
  ```

------
#### [ JSON ]

  ```
   
                   {
                                  "Name": "SourceAction",
                                  "ActionTypeId": {
                                      "Category": "Source",
                                      "Owner": "AWS",
                                      "Version": 1,
                                      "Provider": "S3"
                                  },
                                  "OutputArtifacts": [
                                      {
                                          "Name": "SourceOutput"
                                      }
                                  ],
                                  "Configuration": {
                                      "S3Bucket": {
                                          "Ref": "SourceBucket"
                                      },
                                      "S3ObjectKey": {
                                          "Ref": "SourceObjectKey"
                                      },
                                      "PollForSourceChanges": false
                                  },
                                  "RunOrder": 1
                              }
  ```

------<a name="proc-cfn-event-s3-createtrail"></a>

**To create a second template for your Amazon S3 pipeline's CloudTrail resources**
+ In a separate template, under `Resources`, use the `AWS::S3::Bucket`, `AWS::S3::BucketPolicy`, and `AWS::CloudTrail::Trail` AWS CloudFormation resources to provide a simple bucket definition and trail for CloudTrail\.

  **Why am I making this change? ** Given the current limit of five trails per account, the CloudTrail trail must be created and managed separately\. \(See [Limits in AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html)\.\) However, you can include many Amazon S3 buckets on a single trail, so you can create the trail once and then add Amazon S3 buckets for other pipelines as necessary\. Paste the following into your second sample template file\.

------
#### [ YAML ]

  ```
  ###################################################################################
  # Prerequisites: 
  #   - S3 SoureBucket and SourceObjectKey must exist
  ###################################################################################
  
  Parameters:
    SourceObjectKey:
      Description: 'S3 source artifact'
      Type: String
      Default: SampleApp_Linux.zip
  
  Resources:
    AWSCloudTrailBucketPolicy:
      Type: AWS::S3::BucketPolicy
      Properties:
        Bucket: !Ref AWSCloudTrailBucket
        PolicyDocument:
          Version: 2012-10-17
          Statement:
            -
              Sid: AWSCloudTrailAclCheck
              Effect: Allow
              Principal:
                Service:
                  - cloudtrail.amazonaws.com
              Action: s3:GetBucketAcl
              Resource: !GetAtt AWSCloudTrailBucket.Arn
            -
              Sid: AWSCloudTrailWrite
              Effect: Allow
              Principal:
                Service:
                  - cloudtrail.amazonaws.com
              Action: s3:PutObject
              Resource: !Join [ '', [ !GetAtt AWSCloudTrailBucket.Arn, '/AWSLogs/', !Ref 'AWS::AccountId', '/*' ] ]
              Condition: 
                StringEquals:
                  s3:x-amz-acl: bucket-owner-full-control
    AWSCloudTrailBucket:
      Type: AWS::S3::Bucket
      DeletionPolicy: Retain
    AwsCloudTrail:
      DependsOn:
        - AWSCloudTrailBucketPolicy
      Type: AWS::CloudTrail::Trail
      Properties:
        S3BucketName: !Ref AWSCloudTrailBucket
        EventSelectors:
          -
            DataResources:
              -
                Type: AWS::S3::Object
                Values:
                  - !Join [ '', [ !ImportValue SourceBucketARN, '/', !Ref SourceObjectKey ] ]
            ReadWriteType: WriteOnly
        IncludeGlobalServiceEvents: true
        IsLogging: true
        IsMultiRegionTrail: true
  
  
  ...
  ```

------
#### [ JSON ]

  ```
  {
      "Parameters": {
          "SourceObjectKey": {
              "Description": "S3 source artifact",
              "Type": "String",
              "Default": "SampleApp_Linux.zip"
          }
      },
      "Resources": {
          "AWSCloudTrailBucket": {
              "Type": "AWS::S3::Bucket",
              "DeletionPolicy": "Retain"
          },
          "AWSCloudTrailBucketPolicy": {
              "Type": "AWS::S3::BucketPolicy",
              "Properties": {
                  "Bucket": {
                      "Ref": "AWSCloudTrailBucket"
                  },
                  "PolicyDocument": {
                      "Version": "2012-10-17",
                      "Statement": [
                          {
                              "Sid": "AWSCloudTrailAclCheck",
                              "Effect": "Allow",
                              "Principal": {
                                  "Service": [
                                      "cloudtrail.amazonaws.com"
                                  ]
                              },
                              "Action": "s3:GetBucketAcl",
                              "Resource": {
                                  "Fn::GetAtt": [
                                      "AWSCloudTrailBucket",
                                      "Arn"
                                  ]
                              }
                          },
                          {
                              "Sid": "AWSCloudTrailWrite",
                              "Effect": "Allow",
                              "Principal": {
                                  "Service": [
                                      "cloudtrail.amazonaws.com"
                                  ]
                              },
                              "Action": "s3:PutObject",
                              "Resource": {
                                  "Fn::Join": [
                                      "",
                                      [
                                          {
                                              "Fn::GetAtt": [
                                                  "AWSCloudTrailBucket",
                                                  "Arn"
                                              ]
                                          },
                                          "/AWSLogs/",
                                          {
                                              "Ref": "AWS::AccountId"
                                          },
                                          "/*"
                                      ]
                                  ]
                              },
                              "Condition": {
                                  "StringEquals": {
                                      "s3:x-amz-acl": "bucket-owner-full-control"
                                  }
                              }
                          }
                      ]
                  }
              }
          },
          "AwsCloudTrail": {
              "DependsOn": [
                  "AWSCloudTrailBucketPolicy"
              ],
              "Type": "AWS::CloudTrail::Trail",
              "Properties": {
                  "S3BucketName": {
                      "Ref": "AWSCloudTrailBucket"
                  },
                  "EventSelectors": [
                      {
                          "DataResources": [
                              {
                                  "Type": "AWS::S3::Object",
                                  "Values": [
                                      {
                                          "Fn::Join": [
                                              "",
                                              [
                                                  {
                                                      "Fn::ImportValue": "SourceBucketARN"
                                                  },
                                                  "/",
                                                  {
                                                      "Ref": "SourceObjectKey"
                                                  }
                                              ]
                                          ]
                                      }
                                  ]
                              }
                          ],
                          "ReadWriteType": "WriteOnly"
                      }
                  ],
                  "IncludeGlobalServiceEvents": true,
                  "IsLogging": true,
                  "IsMultiRegionTrail": true
              }
          }
      }
  }
  
  
  ...
  ```

------