# Create a CloudWatch Events rule for an Amazon ECR source \(CLI\)<a name="create-cwe-ecr-source-cli"></a>

Call the put\-rule command, specifying:
+ A name that uniquely identifies the rule you are creating\. This name must be unique across all of the pipelines you create with CodePipeline associated with your AWS account\.
+ The event pattern for the source and detail fields used by the rule\. For more information, see [Amazon CloudWatch Events and Event Patterns](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/CloudWatchEventsandEventPatterns.html)\.

**To create a CloudWatch Events rule with Amazon ECR as the event source and CodePipeline as the target**

1. Add permissions for Amazon CloudWatch Events to use CodePipeline to invoke the rule\. For more information, see [Using Resource\-Based Policies for Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/resource-based-policies-cwe.html)\.

   1. Use the following sample to create the trust policy that allows CloudWatch Events to assume the service role\. Name the trust policy `trustpolicyforCWE.json`\.

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

      ```
      aws iam create-role --role-name Role-for-MyRule --assume-role-policy-document file://trustpolicyforCWE.json
      ```

   1. Create the permissions policy JSON, as shown in this sample, for the pipeline named `MyFirstPipeline`\. Name the permissions policy `permissionspolicyforCWE.json`\.

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

   1. Use the following command to attach the `CodePipeline-Permissions-Policy-for-CWE` permissions policy to the `Role-for-MyRule` role\.

      **Why am I making this change?** Adding this policy to the role creates permissions for CloudWatch Events\.

      ```
      aws iam put-role-policy --role-name Role-for-MyRule --policy-name CodePipeline-Permissions-Policy-For-CWE --policy-document file://permissionspolicyforCWE.json
      ```

1. Call the put\-rule command and include the `--name`, `--event-pattern`, and `--role-arn` parameters\.

   **Why am I making this change?** We must create an event with a rule that specifies how an image push must be made, and a target that names the pipeline to be triggered by the event\.

   The following sample command creates a rule called `MyECRRepoRule`\.

   ```
   aws events put-rule --name "MyECRRepoRule" --event-pattern "{\"detail-type\":[\"ECR Image Action\"],\"source\":[\"aws.ecr\"],\"detail\":{\"action-type\":[\"PUSH\"],\"image-tag\":[\"latest\"],\"repository-name\":[\"cwe-test\"],\"result\":[\"SUCCESS\"]}}}" --role-arn "arn:aws:iam::ACCOUNT_ID:role/Role-for-MyRule"
   ```
**Note**  
To view the full event pattern supported for Amazon ECR events, see [Amazon ECR Events and EventBridge](https://docs.aws.amazon.com/AmazonECR/latest/userguide/ecr-eventbridge.html) or [Amazon Elastic Container Registry Events](https://docs.aws.amazon.com/eventbridge/latest/userguide/event-types.html#ecr-event-types)\.

1. To add CodePipeline as a target, call the put\-targets command and include the following parameters:
   + The `--rule` parameter is used with the `rule_name` you created by using put\-rule\.
   + The `--targets` parameter is used with the list `Id` of the target in the list of targets and the `ARN` of the target pipeline\.

   The following sample command specifies that for the rule called `MyECRRepoRule`, the target `Id` is composed of the number one, indicating that in a list of targets for the rule, this is target 1\. The sample command also specifies an example `Arn` for the pipeline and the example `RoleArn` for the rule\. The pipeline starts when something changes in the repository\.

   ```
   aws events put-targets --rule MyECRRepoRule --targets Id=1,Arn=arn:aws:codepipeline:us-west-2:80398EXAMPLE:TestPipeline,RoleArn=arn:aws:iam::80398EXAMPLE:role/Role-for-MyRule
   ```