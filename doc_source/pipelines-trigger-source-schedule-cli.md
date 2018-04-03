# Create a CloudWatch Events Rule That Schedules Your Pipeline to Start \(CLI\)<a name="pipelines-trigger-source-schedule-cli"></a>

To use the AWS CLI to create a rule, call the put\-rule command, specifying:
+ A name that uniquely identifies the rule you are creating\. This name must be unique across all of the pipelines you create with AWS CodePipeline associated with your AWS account\.
+ The schedule expression for the rule\.

**To create a CloudWatch Events rule with a schedule as the event source**

1. Call the put\-rule command and include the \-\-name and \-\-schedule\-expression parameters\.

   Examples:

   The following sample command uses \-\-schedule\-expression to create a rule called MyRule2 that filters CloudWatch Events on a schedule\.

   ```
   aws events put-rule --schedule-expression 'cron(15 10 ? * 6L 2002-2005)' --name MyRule2
   ```

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