# Create a CloudWatch Events Rule That Starts Your Amazon S3 Pipeline \(CLI\)<a name="create-cloudtrail-S3-source-cli"></a>

**To create an AWS CloudTrail trail and enable logging**

To use the AWS CLI to create a trail, call the create\-trail command, specifying:
+ The trail name\.
+ The bucket to which you have already applied the bucket policy for AWS CloudTrail\.

For more information about creating a trail in the CLI, see [Creating a Trail with the AWS Command Line Interface](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail-by-using-the-aws-cli.html)\.

1. Call the create\-trail command and include the \-\-name and \-\-s3\-bucket\-name parameters\.

   Use the following syntax:

   ```
   aws cloudtrail create-trail 
   --name trail_name 
   --s3-bucket-name bucket_name
   ```

   Example:

   The following sample command uses \-\-name and \-\-s3\-bucket\-name to create a trail called my\-trail and a bucket named myBucket\.

   ```
   aws cloudtrail create-trail --name my-trail --s3-bucket-name myBucket
   ```

1. Call the start\-logging command and include the \-\-name parameter\.

   Example:

   The following sample command uses \-\-name to start logging on a trail called my\-trail\.

   ```
   aws cloudtrail start-logging --name my-trail
   ```

1. Call the put\-event\-selectors command and include the \-\-trail\-name and \-\-event\-selectors parameters\. Use event selectors to specify that you want your trail to log data events for your source bucket and send the events to the Amazon CloudWatch Events rule\.

   Example:

   The following sample command uses \-\-trail\-name and \-\-event\-selectors to specify managing data events for a source bucket and prefix called myBucket/myFolder\.

   ```
   aws cloudtrail put-event-selectors --trail-name my-trail --event-selectors '[{ "ReadWriteType": "WriteOnly", "IncludeManagementEvents":false, "DataResources": [{ "Type": "AWS::S3::Object", "Values": ["ARN:aws:s3:::myBucket/myFolder/file.zip"] }] }]'
   ```

**To create a CloudWatch Events rule with Amazon S3 as the event source and AWS CodePipeline as the target and apply the permissions policy**

1. Grant permissions for Amazon CloudWatch Events to use AWS CodePipeline to invoke the rule\. For more information, see [Using Resource\-Based Policies for Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/resource-based-policies-cwe.html)\.

   1. Use the following sample to create the trust policy to allow CloudWatch Events to assume the service role\. Name it "trustpolicyforCWE\.json\."

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

   1. Use the following command to create the "Role\-for\-MyRule" role and attach the trust policy\.

      ```
      aws iam create-role --role-name Role-for-MyRule --assume-role-policy-document file://trustpolicyforCWE.json
      ```

   1. Create the permissions policy JSON as shown in this sample for the pipeline named “MyFirstPipeline\.” Name the permissions policy “permissionspolicyforCWE\.json\.”

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

   1. Use the following command to attach the new “CodePipeline\-Permissions\-Policy\-for\-CWE” permissions policy to the “Role\-for\-MyRule” role you created\.

      ```
      aws iam put-role-policy --role-name Role-for-MyRule --policy-name CodePipeline-Permissions-Policy-For-CWE --policy-document file://permissionspolicyforCWE.json
      ```

1. Call the put\-rule command and include the \-\-name and \-\-event\-pattern parameters\.

   Use the following syntax:

   ```
   aws events put-rule 
   --name "rule_name" 
   --event-pattern "{"source":["aws.service_name"], "detail-type":["event_type"], "resources":"repository_ARN"]}"
   ```

   The following sample command uses \-\-event\-pattern to create a rule called MyS3SourceRule\.

   ```
   aws events put-rule --name "MyS3SourceRule" --event-pattern "{\"source\":[\"aws.s3\"],\"detail-type\":[\"AWS API Call via CloudTrail\"],\"detail\":{\"eventSource\":[\"s3.amazonaws.com\"],\"eventName\":[\"PutObject\"],\"resources\":{\"ARN\":[\"ARN:aws:s3:::myBucket/myFolder/file.zip\"]}}}
   ```

1. To add AWS CodePipeline as a target, call the put\-targets command and include the \-\-rule and \-\-Id parameters\.

   Use the following syntax:

   ```
   aws events put-targets 
   --rule rule_name 
   --targets Id,ARN
   ```

   The following sample command specifies that for the rule called MyS3SourceRule, the target is the ID and ARN for the pipeline that starts when something changes in the repository\.

   ```
   aws events put-targets --rule MyS3SourceRule --targets Id=1,Arn=arn:aws:codepipeline:us-west-2:80398EXAMPLE:TestPipeline
   ```