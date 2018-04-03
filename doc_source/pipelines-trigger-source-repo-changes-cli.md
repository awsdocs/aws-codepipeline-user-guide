# Create a CloudWatch Events Rule That Starts Your AWS CodeCommit Pipeline \(CLI\)<a name="pipelines-trigger-source-repo-changes-cli"></a>

To use the AWS CLI to create a rule, call the put\-rule command, specifying:
+ A name that uniquely identifies the rule you are creating\. This name must be unique across all of the pipelines you create with AWS CodePipeline associated with your AWS account\.
+ The event pattern for the source and detail fields used by the rule\. For more information, see [Amazon CloudWatch Events and Event Patterns](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/CloudWatchEventsandEventPatterns.html)\.

**To create a CloudWatch Events rule with AWS CodeCommit as the event source and AWS CodePipeline as the target**

1. Add permissions for Amazon CloudWatch Events to use AWS CodePipeline to invoke the rule\. For more information, see [Using Resource\-Based Policies for Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/resource-based-policies-cwe.html)\.

   1. Use the following sample to create the trust policy that allows CloudWatch Events to assume the service role\. Name the trust policy "trustpolicyforCWE\.json\."

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

   1. Use the following command to attach the “CodePipeline\-Permissions\-Policy\-for\-CWE” permissions policy to the “Role\-for\-MyRule” role\.

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

   Examples:

   The following sample command uses \-\-event\-pattern to create a rule called MyCodeCommitRepoRule\.

   ```
   aws events put-rule --name "MyCodeCommitRepoRule" --event-pattern "{\"source\":[\"aws.codecommit\"],\"detail-type\":[\"CodeCommit Repository State Change\"],\"detail\":{\"referenceType\":[\"branch\"],\"referenceName \":[\"master\"]}}"
   ```

1. To add AWS CodePipeline as a target, call the put\-targets command and include the \-\-rule and \-\-Id parameters\.

   Use the following syntax:

   ```
   aws events put-targets 
   --rule rule_name 
   --targets Id,ARN
   ```

   Examples:

   The following sample command specifies that for the rule called MyCodeCommitRepoRule, the target is the ID and ARN for the pipeline that starts when something changes in the repository\.

   ```
   aws events put-targets --rule MyCodeCommitRepoRule --targets Id=1,Arn=arn:aws:codepipeline:us-west-2:80398EXAMPLE:TestPipeline
   ```