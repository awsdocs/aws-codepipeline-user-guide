# Create a CloudWatch Events Rule for an Amazon ECR Source \(CLI\)<a name="create-cwe-ecr-source-cli"></a>

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

1. Call the put\-rule command and include the `--name` and `--event-pattern` parameters\.

   **Why am I making this change?** This command enables AWS CloudFormation to create the event\.

   The following sample command uses `--event-pattern` to create a rule called `MyECRRepoRule`\.

   ```
   aws events put-rule --name "MyECRRepoRule" --event-pattern "{\"source\":[\"aws.ecr\"],\"detail\":{\"eventName\":[\"PutImage\"],\"requestParameters\":{\"repositoryName\":[\"my-image-repo\"],\"imageTag\":[\"latest\"]}}}"
   ```
**Note**  
The put\-rule command above can use either the `aws.ecr` or `ecr.amazonaws.com` service name in the `source` field\.

1. To add CodePipeline as a target, call the put\-targets command and include the following parameters:
   + The `--rule` parameter is used with the `rule_name` you created by using put\-rule\.
   + The `--targets` parameter is used with the list `Id` of the target in the list of targets and the `ARN` of the target pipeline\.

   The following sample command specifies that for the rule called `MyECRRepoRule`, the target `Id` is composed of the number one, indicating that in a list of targets for the rule, this is target 1\. The sample command also specifies an example `ARN` for the pipeline\. The pipeline starts when something changes in the repository\.

   ```
   aws events put-targets --rule MyECRRepoRule --targets Id=1,Arn=arn:aws:codepipeline:us-west-2:80398EXAMPLE:TestPipeline
   ```