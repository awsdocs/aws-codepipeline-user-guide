# Create a CloudWatch Events rule for an Amazon S3 source \(CLI\)<a name="create-cloudtrail-S3-source-cli"></a><a name="proc-cli-event-s3-createtrail"></a>

**To create an AWS CloudTrail trail and enable logging**

To use the AWS CLI to create a trail, call the create\-trail command, specifying:
+ The trail name\.
+ The bucket to which you have already applied the bucket policy for AWS CloudTrail\.

For more information, see [Creating a Trail with the AWS Command Line Interface](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail-by-using-the-aws-cli.html)\.

1. Call the create\-trail command and include the `--name` and `--s3-bucket-name` parameters\.

   **Why am I making this change?** This creates the CloudTrail trail required for your S3 source bucket\.

   The following command uses `--name` and `--s3-bucket-name` to create a trail named `my-trail` and a bucket named `myBucket`\.

   ```
   aws cloudtrail create-trail --name my-trail --s3-bucket-name myBucket
   ```

1. Call the start\-logging command and include the `--name` parameter\.

   **Why am I making this change? ** This command starts the CloudTrail logging for your source bucket and sends events to CloudWatch Events\.

   Example:

   The following command uses `--name` to start logging on a trail named `my-trail`\.

   ```
   aws cloudtrail start-logging --name my-trail
   ```

1. Call the put\-event\-selectors command and include the `--trail-name` and `--event-selectors` parameters\. Use event selectors to specify that you want your trail to log data events for your source bucket and send the events to the Amazon CloudWatch Events rule\.

   **Why am I making this change? ** This command filters events\.

   Example:

   The following command uses `--trail-name` and `--event-selectors` to specify data events for a source bucket and prefix named `myBucket/myFolder`\.

   ```
   aws cloudtrail put-event-selectors --trail-name my-trail --event-selectors '[{ "ReadWriteType": "WriteOnly", "IncludeManagementEvents":false, "DataResources": [{ "Type": "AWS::S3::Object", "Values": ["arn:aws:s3:::myBucket/myFolder/file.zip"] }] }]'
   ```<a name="proc-cli-event-s3-createrule"></a>

**To create a CloudWatch Events rule with Amazon S3 as the event source and CodePipeline as the target and apply the permissions policy**

1. Grant permissions for Amazon CloudWatch Events to use CodePipeline to invoke the rule\. For more information, see [Using Resource\-Based Policies for Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/resource-based-policies-cwe.html)\.

   1. Use the following sample to create the trust policy to allow CloudWatch Events to assume the service role\. Name it `trustpolicyforCWE.json`\.

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Principal": {
                      "Service": "events.amazonaws.com"
                  },
                  "Action": "sts:AssumeRole"
              }
          ]
      }
      ```

   1. Use the following command to create the `Role-for-MyRule` role and attach the trust policy\.

      **Why am I making this change?** Adding this trust policy to the role creates permissions for CloudWatch Events\.

      ```
      aws iam create-role --role-name Role-for-MyRule --assume-role-policy-document file://trustpolicyforCWE.json
      ```

   1. Create the permissions policy JSON, as shown here for the pipeline named `MyFirstPipeline`\. Name the permissions policy `permissionspolicyforCWE.json`\.

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "codepipeline:StartPipelineExecution"
                  ],
                  "Resource": [
                      "arn:aws:codepipeline:us-west-2:80398EXAMPLE:MyFirstPipeline"
                  ]
              }
          ]
      }
      ```

   1. Use the following command to attach the new `CodePipeline-Permissions-Policy-for-CWE` permissions policy to the `Role-for-MyRule` role you created\.

      ```
      aws iam put-role-policy --role-name Role-for-MyRule --policy-name CodePipeline-Permissions-Policy-For-CWE --policy-document file://permissionspolicyforCWE.json
      ```

1. Call the put\-rule command and include the `--name`, `--event-pattern`, and `--role-arn` parameters\.

   The following sample command creates a rule named `MyS3SourceRule`\.

   ```
   aws events put-rule --name "MyS3SourceRule" --event-pattern "{\"source\":[\"aws.s3\"],\"detail-type\":[\"AWS API Call via CloudTrail\"],\"detail\":{\"eventSource\":[\"s3.amazonaws.com\"],\"eventName\":[\"CopyObject\",\"PutObject\",\"CompleteMultipartUpload\"],\"requestParameters\":{\"bucketName\":[\"my-bucket\"],\"key\":[\"my-key\"]}}}
    --role-arn "arn:aws:iam::ACCOUNT_ID:role/Role-for-MyRule"
   ```

1. To add CodePipeline as a target, call the put\-targets command and include the `--rule` and `--targets` parameters\.

   The following command specifies that for the rule named `MyS3SourceRule`, the target `Id` is composed of the number one, indicating that in a list of targets for the rule, this is target 1\. The command also specifies an example `ARN` for the pipeline\. The pipeline starts when something changes in the repository\.

   ```
   aws events put-targets --rule MyS3SourceRule --targets Id=1,Arn=arn:aws:codepipeline:us-west-2:80398EXAMPLE:TestPipeline
   ```<a name="proc-cli-flag-s3"></a>

**To edit your pipeline's PollForSourceChanges parameter**
**Important**  
When you create a pipeline with this method, the `PollForSourceChanges` parameter defaults to true if it is not explicitly set to false\. When you add event\-based change detection, you must add the parameter to your output and set it to false to disable polling\. Otherwise, your pipeline starts twice for a single source change\. For details, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.

1. Run the get\-pipeline command to copy the pipeline structure into a JSON file\. For example, for a pipeline named `MyFirstPipeline`, run the following command: 

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   This command returns nothing, but the file you created should appear in the directory where you ran the command\.

1. Open the JSON file in any plain\-text editor and edit the source stage by changing the `PollForSourceChanges` parameter for a bucket named `storage-bucket` to `false`, as shown in this example\.

   **Why am I making this change?** Setting this parameter to `false` turns off periodic checks so you can use event\-based change detection only\.

   ```
   "configuration": {
     "S3Bucket": "storage-bucket",
     "PollForSourceChanges": "false",
     "S3ObjectKey": "index.zip"
     },
   ```

1. If you are working with the pipeline structure retrieved using the get\-pipeline command, you must remove the `metadata` lines from the JSON file\. Otherwise, the update\-pipeline command cannot use it\. Remove the `"metadata": { }` lines and the `"created"`, `"pipelineARN"`, and `"updated"` fields\.

   For example, remove the following lines from the structure:

   ```
   "metadata": {  
     "pipelineArn": "arn:aws:codepipeline:region:account-ID:pipeline-name",
     "created": "date",
     "updated": "date"
     }
   ```

   Save the file\.

1. To apply your changes, run the update\-pipeline command, specifying the pipeline JSON file:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline update-pipeline --cli-input-json file://pipeline.json
   ```

   This command returns the entire structure of the edited pipeline\.
**Note**  
The update\-pipeline command stops the pipeline\. If a revision is being run through the pipeline when you run the update\-pipeline command, that run is stopped\. You must manually start the pipeline to run that revision through the updated pipeline\. Use the start\-pipeline\-execution command to manually start your pipeline\.