# Edit pipelines to use push events<a name="update-change-detection"></a>

AWS CodePipeline supports full, end\-to\-end continuous delivery, which includes starting your pipeline whenever there is a code change\. There are two supported ways to start your pipeline upon a code change:
+ Events \(either Amazon CloudWatch Events or webhooks\)
+ Polling \(checking periodically\)

Initially, only polling was supported\. Events are now the default and recommended way to start your pipeline when there’s a code change\.

**Important**  
You must explicitly set the `PollForSourceChanges` parameter to *false* within your Source action’s configuration to stop a pipeline from polling\. As a result, it is possible to erroneously configure a pipeline with both event\-based change detection *and* polling by, for example, configuring a CloudWatch Events rule and also omitting the `PollForSourceChanges` parameter\. This results in duplicate pipeline executions, and the pipeline is counted toward the limit on total number of polling pipelines, which by default is much lower than event\-based pipelines\.

There are some important advantages to using push events instead of polling:
+ On average, events are significantly faster\. Events should start your pipeline almost immediately, as opposed to polling, which requires waiting for the next periodic check\.
+ Higher limits\. Compared to pipelines that poll for changes, CodePipeline can support far more event\-based pipelines\.
+ Better experience with many pipelines\. Some customers might experience throttling or higher costs by having many pipelines continuously polling their repository for code changes\. You can avoid this by using events\.

When you use the CodePipeline console or AWS CodeStar to create a pipeline, events are enabled by default\. For backward compatibility, new pipelines created through the API, AWS CLI, or AWS CloudFormation use the original polling functionality\. We strongly recommend that you use events instead\. To opt in, use the AWS CLI or AWS CloudFormation to create the CloudWatch event or webhook and disable polling\. Use the instructions in the following table\.

You should also use events on pipelines that were created before the new console was launched\. To opt in, use the CodePipeline console to create the CloudWatch event or webhook and disable polling\. Use the instructions in the following table\.


**To update AWS CodeCommit source actions**  

| Type | Action Required | Instructions | 
| --- | --- | --- | 
| If you create and manage your pipeline with the AWS CLI | Use the AWS CLI to disable periodic checks and create your Amazon CloudWatch Events resources | [Update pipelines for push events \(CodeCommit source\) \(CLI\)](#update-change-detection-cli-codecommit) | 
| If you create and manage your pipeline with AWS CloudFormation | Use AWS CloudFormation to execute a change set that disables periodic checks and creates your Amazon CloudWatch Events resources | [Update pipelines for push events \(CodeCommit source\) \(AWS CloudFormation template\)](#update-change-detection-cfn-codecommit) | 
| If you created your pipeline in the console before October 11, 2017 | Use the CodePipeline console to let CodePipeline disable periodic checks and create your Amazon CloudWatch Events resources | [Update pipelines for push events \(CodeCommit or Amazon S3 source\) \(console\)](#update-change-detection-console-codecommit-S3) | 
| If you created your pipeline in the console after October 11, 2017 | No action required |  | 


**To update Amazon S3 source actions**  

| Type | Action Required | Instructions | 
| --- | --- | --- | 
| If you create and manage your pipeline with the AWS CLI | Use the AWS CLI to disable periodic checks and create your Amazon CloudWatch Events and CloudTrail resources | [Update pipelines for push events \(Amazon S3 source\) \(CLI\)](#update-change-detection-cli-S3) | 
| If you create and manage your pipeline with AWS CloudFormation | Use AWS CloudFormation to execute a change set that disables periodic checks and creates your Amazon CloudWatch Events and AWS CloudTrail resources | [Update pipelines for push events \(Amazon S3 source\) \(AWS CloudFormation template\)](#update-change-detection-cfn-s3) | 
| If you created your pipeline in the console before March 22, 2018 | Use the CodePipeline console to let CodePipeline disable periodic checks and create your Amazon CloudWatch Events and AWS CloudTrail resources | [Update pipelines for push events \(CodeCommit or Amazon S3 source\) \(console\)](#update-change-detection-console-codecommit-S3) | 
| If you create your pipeline in the console after March 22, 2018 | No action required |  | 


**To update GitHub source actions**  

| Type | Action Required | Instructions | 
| --- | --- | --- | 
| If you create and manage your pipeline with the AWS CLI | Use the AWS CLI to disable periodic checks and create and register your webhook | [Update pipelines for push events \(GitHub source\) \(CLI\)](#update-change-detection-cli-github) | 
| If you create and manage your pipeline AWS CloudFormation | Use AWS CloudFormation to execute a change set that disables periodic checks and creates and registers your webhook | [Update pipelines for push events \(GitHub source\) \(AWS CloudFormation template\)](#update-change-detection-cfn-github) | 
| If you created your pipeline in the console before May 1, 2018 | Use the CodePipeline console to let CodePipeline disable periodic checks and create and register your webhook | [Update pipelines for push events \(GitHub source\) \(console\)](#update-change-detection-console-github) | 
| If you create your pipeline in the console after May 1, 2018 | No action required |  | 

## Update pipelines for push events \(console\)<a name="update-change-detection-console"></a>

You can use the CodePipeline console to update your pipeline to use the recommended change detection method\.

### Update pipelines for push events \(CodeCommit or Amazon S3 source\) \(console\)<a name="update-change-detection-console-codecommit-S3"></a>

You can use the CodePipeline console to update your pipeline to use Amazon CloudWatch Events to detect changes in your CodeCommit source repository or your Amazon S3 source bucket\.

**Note**  
When you use the console to edit a pipeline that has a CodeCommit source repository or an Amazon S3 source bucket, the rule and IAM role are created for you\. If you use the AWS CLI to edit the pipeline, you must create the Amazon CloudWatch Events rule and IAM role yourself\. For more information, see [ Use CloudWatch Events to start a pipeline \(CodeCommit source\)](triggering.md)\.

Use these steps to edit a pipeline that is using periodic checks\. If you want to create a pipeline, see [Create a pipeline in CodePipeline](pipelines-create.md)\.

**To edit the pipeline source stage**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account are displayed\.

1. In **Name**, choose the name of the pipeline you want to edit\. This opens a detailed view of the pipeline, including the state of each of the actions in each stage of the pipeline\.

1. On the pipeline details page, choose **Edit**\. 

1. In **Edit** stage, choose the edit icon on the source action\.

1. Expand **Change Detection Options** and choose **Use CloudWatch Events to automatically start my pipeline when a change occurs \(recommended\)**\. 

   A message appears showing the Amazon CloudWatch Events rule to be created for this pipeline\. Choose **Update**\.  
![\[Use the console to update a CodeCommit pipeline for change detection\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/poll-migration-console-codecommit.png)![\[Use the console to update a CodeCommit pipeline for change detection\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Use the console to update a CodeCommit pipeline for change detection\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   If you are updating a pipeline that has an Amazon S3 source, you see the following message\. Choose **Update**\.  
![\[Use the console to update an Amazon S3 pipeline for change detection\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/poll-migration-console-s3.png)![\[Use the console to update an Amazon S3 pipeline for change detection\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Use the console to update an Amazon S3 pipeline for change detection\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. When you have finished editing your pipeline, choose **Save pipeline changes** to return to the summary page\.

   A message displays the name of the Amazon CloudWatch Events rule to be created for your pipeline\. Choose **Save and continue**\.

1. To test your action, release a change by using the AWS CLI to commit a change to the source specified in the source stage of the pipeline\.

### Update pipelines for push events \(GitHub source\) \(console\)<a name="update-change-detection-console-github"></a>

You can use the CodePipeline console to update your pipeline to use webhooks to detect changes in your CodeCommit source repository\.

Follow these steps to edit a pipeline that is using polling \(periodic checks\) to use Amazon CloudWatch Events instead\. If you want to create a pipeline, see [Create a pipeline in CodePipeline](pipelines-create.md)\.

When you use the console, the `PollForSourceChanges` parameter for your pipelined is changed for you\. The GitHub webhook is created and registered for you\.

**To edit the pipeline source stage**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account are displayed\.

1. In **Name**, choose the name of the pipeline you want to edit\. This opens a detailed view of the pipeline, including the state of each of the actions in each stage of the pipeline\.

1. On the pipeline details page, choose **Edit**\.

1. In **Edit stage**, choose the edit icon on the source action\.

1. Expand **Change detection options** and choose **Use Amazon CloudWatch Events to automatically start my pipeline when a change occurs \(recommended\)**\.

   A message is displayed to advise that CodePipeline creates a webhook in GitHub to detect source changes\. Choose **Update**\. In addition to the webhook, CodePipeline creates the following:
   + A secret, randomly generated and used to authorize the connection to GitHub\.
   + The webhook URL, generated using the public endpoint for the Region\.

   CodePipeline registers the webhook with GitHub\. This subscribes the URL to receive repository events\.  
![\[Use the console to update a GitHub pipeline for change detection\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/poll-migration-console-github.png)![\[Use the console to update a GitHub pipeline for change detection\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Use the console to update a GitHub pipeline for change detection\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. When you have finished editing your pipeline, choose **Save pipeline changes** to return to the summary page\.

   A message displays the name of the webhook to be created for your pipeline\. Choose **Save and continue**\.

1. To test your action, release a change by using the AWS CLI to commit a change to the source specified in the source stage of the pipeline\.

## Update pipelines for push events \(CLI\)<a name="update-change-detection-cli"></a>

You can use the CLI to update your pipeline to use the recommended change detection method\.

### Update pipelines for push events \(CodeCommit source\) \(CLI\)<a name="update-change-detection-cli-codecommit"></a>

Follow these steps to edit a pipeline that is using polling \(periodic checks\) to use a CloudWatch Events rule to start the pipeline\. If you want to create a pipeline, see [Create a pipeline in CodePipeline](pipelines-create.md)\.

To build an event\-driven pipeline with CodeCommit, you edit the `PollForSourceChanges` parameter of your pipeline and then create the following resources:
+ Amazon CloudWatch Events event
+ IAM role to allow this event to start your pipeline<a name="proc-cli-flag-codecommit"></a>

**To edit your pipeline's PollForSourceChanges parameter**
**Important**  
When you create a pipeline with this method, the `PollForSourceChanges` parameter defaults to true if it is not explicitly set to false\. When you add event\-based change detection, you must add the parameter to your output and set it to false to disable polling\. Otherwise, your pipeline starts twice for a single source change\. For details, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.

1. Run the get\-pipeline command to copy the pipeline structure into a JSON file\. For example, for a pipeline named `MyFirstPipeline`, run the following command: 

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   This command returns nothing, but the file you created should appear in the directory where you ran the command\.

1. Open the JSON file in any plain\-text editor and edit the source stage by changing the `PollForSourceChanges` parameter to `false`, as shown in this example\.

   **Why am I making this change?** Changing this parameter to `false` turns off periodic checks so you can use event\-based change detection only\.

   ```
   "configuration": {
     "PollForSourceChanges": "false",
     "BranchName": "master",
     "RepositoryName": "MyTestRepo"
     },
   ```

1. If you are working with the pipeline structure retrieved using the get\-pipeline command, remove the `metadata` lines from the JSON file\. Otherwise, the update\-pipeline command cannot use it\. Remove the `"metadata": { }` lines and the `"created"`, `"pipelineARN"`, and `"updated"` fields\.

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
The update\-pipeline command stops the pipeline\. If a revision is being run through the pipeline when you run the update\-pipeline command, that run is stopped\. You must manually start the pipeline to run that revision through the updated pipeline\. Use the `start-pipeline-execution` command to manually start your pipeline\.<a name="proc-cli-event-codecommit"></a>

**To create a CloudWatch Events rule with CodeCommit as the event source and CodePipeline as the target**

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

1. Call the put\-rule command and include the `--name`, `--event-pattern` , and`--role-arn` parameters\.

   **Why am I making this change?** This command enables AWS CloudFormation to create the event\.

   The following sample command creates a rule called `MyCodeCommitRepoRule`\.

   ```
   aws events put-rule --name "MyCodeCommitRepoRule" --event-pattern "{\"source\":[\"aws.codecommit\"],\"detail-type\":[\"CodeCommit Repository State Change\"],\"resources\":[\"repository-ARN\"],\"detail\":{\"referenceType\":[\"branch\"],\"referenceName\":[\"master\"]}}" --role-arn "arn:aws:iam::ACCOUNT_ID:role/Role-for-MyRule"
   ```

1. To add CodePipeline as a target, call the put\-targets command and include the following parameters:
   + The `--rule` parameter is used with the `rule_name` you created by using put\-rule\. 
   + The `--targets` parameter is used with the list `Id` of the target in the list of targets and the `ARN` of the target pipeline\.

   The following sample command specifies that for the rule called `MyCodeCommitRepoRule`, the target `Id` is composed of the number one, indicating that in a list of targets for the rule, this is target 1\. The sample command also specifies an example `ARN` for the pipeline\. The pipeline starts when something changes in the repository\.

   ```
   aws events put-targets --rule MyCodeCommitRepoRule --targets Id=1,Arn=arn:aws:codepipeline:us-west-2:80398EXAMPLE:TestPipeline
   ```

### Update pipelines for push events \(Amazon S3 source\) \(CLI\)<a name="update-change-detection-cli-S3"></a>

Follow these steps to edit a pipeline that is using polling \(periodic checks\) to use an event in CloudWatch Events instead\. If you want to create a pipeline, see [Create a pipeline in CodePipeline](pipelines-create.md)\.

To build an event\-driven pipeline with Amazon S3, you edit the `PollForSourceChanges` parameter of your pipeline and then create the following resources:
+ AWS CloudTrail trail, bucket, and bucket policy that Amazon S3 can use to log the events\.
+ Amazon CloudWatch Events event
+ IAM role to allow the CloudWatch event to start your pipeline<a name="proc-cli-event-s3-createtrail"></a>

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

### Update pipelines for push events \(GitHub source\) \(CLI\)<a name="update-change-detection-cli-github"></a>

Follow these steps to edit a pipeline that is using periodic checks to use a webhook instead\. If you want to create a pipeline, see [Create a pipeline in CodePipeline](pipelines-create.md)\.

To build an event\-driven pipeline, you edit the `PollForSourceChanges` parameter of your pipeline and then create the following resources manually:
+ GitHub webhook and authorization parameters<a name="proc-cli-gh-webhook"></a>

**To create and register your webhook**
**Note**  
When you use the CLI or AWS CloudFormation to create a pipeline and add a webhook, you must disable periodic checks\. To disable periodic checks, you must explicitly add the `PollForSourceChanges` parameter and set it to false, as detailed in the final procedure below\. Otherwise, the default for a CLI or AWS CloudFormation pipeline is that `PollForSourceChanges` defaults to true and does not display in the pipeline structure output\. For more information about PollForSourceChanges defaults, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.

1. In a text editor, create and save a JSON file for the webhook you want to create\. Use this sample file for a webhook named `my-webhook`:

   ```
   {"webhook": 
     {"name": "my-webhook",
      "targetPipeline": "pipeline_name",
      "targetAction": "source_action_name",
      "filters": [
       {
         "jsonPath": "$.ref", 
         "matchEquals": "refs/heads/{Branch}"
       }
      ],
      "authentication": "GITHUB_HMAC",
      "authenticationConfiguration": {"SecretToken":"secret"}
     }
   }
   ```

1. Call the put\-webhook command and include the `--cli-input` and `--region` parameters\.

   The following sample command creates a webhook with the `webhook_json` JSON file\.

   ```
   aws codepipeline put-webhook --cli-input-json file://webhook_json.json --region "eu-central-1"
   ```

1. In the output shown in this example, the URL and ARN are returned for a webhook named `my-webhook`\.

   ```
   {
       "webhook": {
           "url": "https://webhooks.domain.com/trigger111111111EXAMPLE11111111111111111",
           "definition": {
               "authenticationConfiguration": {
                   "SecretToken": "secret"
               },
               "name": "my-webhook",
               "authentication": "GITHUB_HMAC",
               "targetPipeline": "pipeline_name",
               "targetAction": "Source",
               "filters": [
                   {
                       "jsonPath": "$.ref",
                       "matchEquals": "refs/heads/{Branch}"
                   }
               ]
           },
           "arn": "arn:aws:codepipeline:eu-central-1:ACCOUNT_ID:webhook:my-webhook"
       },
       "tags": [{
         "key": "Project",
         "value": "ProjectA"
       }]
   }
   ```

   This example adds tagging to the webhook by including the `Project` tag key and `ProjectA` value on the webhook\. For more information about tagging resources in CodePipeline, see [Tagging resources](tag-resources.md)\.

1. Call the register\-webhook\-with\-third\-party command and include the `--webhook-name` parameter\.

   The following sample command registers a webhook named `my-webhook`\.

   ```
   aws codepipeline register-webhook-with-third-party --webhook-name my-webhook
   ```<a name="proc-cli-flag-github"></a>

**To edit your pipeline's PollForSourceChanges parameter**
**Important**  
When you create a pipeline with this method, the `PollForSourceChanges` parameter defaults to true if it is not explicitly set to false\. When you add event\-based change detection, you must add the parameter to your output and set it to false to disable polling\. Otherwise, your pipeline starts twice for a single source change\. For details, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.

1. Run the get\-pipeline command to copy the pipeline structure into a JSON file\. For example, for a pipeline named `MyFirstPipeline`, you would type the following command: 

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   This command returns nothing, but the file you created should appear in the directory where you ran the command\.

1. Open the JSON file in any plain\-text editor and edit the source stage by changing or adding the `PollForSourceChanges` parameter\. In this example, for a repository named `UserGitHubRepo`, the parameter is set to `false` \.

   **Why am I making this change? ** Changing this parameter turns off periodic checks so you can use event\-based change detection only\.

   ```
   "configuration": {
     "Owner": "darlaker",
     "Repo": "UserGitHubRepo",
     "PollForSourceChanges": "false",
     "Branch": "master",
     "OAuthToken": "****"
     },
   ```

1. If you are working with the pipeline structure retrieved using the get\-pipeline command, you must edit the structure in the JSON file by removing the `metadata` lines from the file\. Otherwise, the update\-pipeline command cannot use it\. Remove the `"metadata"` section from the pipeline structure in the JSON file, including the : `{ }` and the `"created"`, `"pipelineARN"`, and `"updated"` fields\.

   For example, remove the following lines from the structure: 

   ```
   "metadata": {  
     "pipelineArn": "arn:aws:codepipeline:region:account-ID:pipeline-name",
     "created": "date",
     "updated": "date"
     }
   ```

   Save the file\.

1. To apply your changes, run the update\-pipeline command, specifying the pipeline JSON file, similar to the following:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline update-pipeline --cli-input-json file://pipeline.json
   ```

   This command returns the entire structure of the edited pipeline\.
**Note**  
The update\-pipeline command stops the pipeline\. If a revision is being run through the pipeline when you run the update\-pipeline command, that run is stopped\. You must manually start the pipeline to run that revision through the updated pipeline\. Use the start\-pipeline\-execution command to manually start your pipeline\.

## Update pipelines for push events \(AWS CloudFormation template\)<a name="update-change-detection-cfn"></a>

You can use AWS CloudFormation to update your pipeline to use the recommended method to detect changes in your source\.

Follow these steps to edit a pipeline with AWS CloudFormation that is using periodic checks\. If you want to create a pipeline, see [Continuous Delivery with CodePipeline](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/continuous-delivery-codepipeline.html)\.

### Update pipelines for push events \(CodeCommit source\) \(AWS CloudFormation template\)<a name="update-change-detection-cfn-codecommit"></a>

To build an event\-driven pipeline with AWS CodeCommit, you edit the `PollForSourceChanges` parameter of your pipeline and then add the following resources to your template:
+ An Amazon CloudWatch Events rule
+ An IAM role for your CloudWatch Events rule

If you use AWS CloudFormation to create and manage your pipelines, your template includes content like the following\.

**Note**  
The `Configuration` property in the source stage called `PollForSourceChanges`\. If that property isn't included in your template, then `PollForSourceChanges` is set to `true` by default\.

------
#### [ YAML ]

```
Resources:
  AppPipeline: 
    Type: AWS::CodePipeline::Pipeline
    Properties: 
      Name: codecommit-polling-pipeline
      RoleArn: 
        !GetAtt CodePipelineServiceRole.Arn
      Stages: 
        - 
          Name: Source
          Actions: 
            - 
              Name: SourceAction
              ActionTypeId: 
                Category: Source
                Owner: AWS
                Version: 1
                Provider: CodeCommit
              OutputArtifacts: 
                - Name: SourceOutput
              Configuration: 
                BranchName: !Ref BranchName
                RepositoryName: !Ref RepositoryName
                PollForSourceChanges: true
              RunOrder: 1
```

------
#### [ JSON ]

```
               "Stages": [
                    {
                        "Name": "Source",
                        "Actions": [
                            {
                                "Name": "SourceAction",
                                "ActionTypeId": {
                                    "Category": "Source",
                                    "Owner": "AWS",
                                    "Version": 1,
                                    "Provider": "CodeCommit"
                                },
                                "OutputArtifacts": [
                                    {
                                        "Name": "SourceOutput"
                                    }
                                ],
                                "Configuration": {
                                    "BranchName": {
                                        "Ref": "BranchName"
                                    },
                                    "RepositoryName": {
                                        "Ref": "RepositoryName"
                                    },
                                    "PollForSourceChanges": true
                                },
                                "RunOrder": 1
                            }
                        ]
                    },
```

------<a name="proc-cfn-event-codecommit"></a>

**To update your pipeline AWS CloudFormation template and create CloudWatch Events rule**

1. In the template, under `Resources`, use the `AWS::IAM::Role` AWS CloudFormation resource to configure the IAM role that allows your event to start your pipeline\. This entry creates a role that uses two policies:
   + The first policy allows the role to be assumed\.
   + The second policy provides permissions to start the pipeline\.

   **Why am I making this change?** Adding the `AWS::IAM::Role` resource enables AWS CloudFormation to create permissions for CloudWatch Events\. This resource is added to your AWS CloudFormation stack\.

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
   
   ...
   ```

------

1. In the template, under `Resources`, use the `AWS::Events::Rule` AWS CloudFormation resource to add a CloudWatch Events rule\. This event pattern creates an event that monitors push changes to your repository When CloudWatch Events detects a repository state change, the rule invokes `StartPipelineExecution` on your target pipeline\.

   **Why am I making this change? ** Adding the `AWS::Events::Rule` resource enables AWS CloudFormation to create the event\. This resource is added to your AWS CloudFormation stack\.

------
#### [ YAML ]

   ```
     AmazonCloudWatchEventRule:
       Type: AWS::Events::Rule
       Properties:
         EventPattern:
           source:
             - aws.codecommit
           detail-type:
             - 'CodeCommit Repository State Change'
           resources:
             - !Join [ '', [ 'arn:aws:codecommit:', !Ref 'AWS::Region', ':', !Ref 'AWS::AccountId', ':', !Ref RepositoryName ] ]
           detail:
             event:
               - referenceCreated
               - referenceUpdated
             referenceType:
               - branch
             referenceName:
               - master
         Targets:
           -
             Arn: 
               !Join [ '', [ 'arn:aws:codepipeline:', !Ref 'AWS::Region', ':', !Ref 'AWS::AccountId', ':', !Ref AppPipeline ] ]
             RoleArn: !GetAtt AmazonCloudWatchEventRole.Arn
             Id: codepipeline-AppPipeline
   ```

------
#### [ JSON ]

   ```
   "AmazonCloudWatchEventRule": {
     "Type": "AWS::Events::Rule",
     "Properties": {
       "EventPattern": {
         "source": [
           "aws.codecommit"
         ],
         "detail-type": [
           "CodeCommit Repository State Change"
         ],
         "resources": [
           {
             "Fn::Join": [
               "",
               [
                 "arn:aws:codecommit:",
                 {
                   "Ref": "AWS::Region"
                 },
                 ":",
                 {
                   "Ref": "AWS::AccountId"
                 },
                 ":",
                 {
                   "Ref": "RepositoryName"
                 }
               ]
             ]
           }
         ],
         "detail": {
           "event": [
             "referenceCreated",
             "referenceUpdated"
           ],
           "referenceType": [
             "branch"
           ],
           "referenceName": [
             "master"
           ]
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
   },
   ```

------

1. Save the updated template to your local computer, and then open the AWS CloudFormation console\.

1. Choose your stack, and then choose **Create Change Set for Current Stack**\. 

1. Upload the template, and then view the changes listed in AWS CloudFormation\. These are the changes to be made to the stack\. You should see your new resources in the list\. 

1. Choose **Execute**\.<a name="proc-cfn-flag-codecommit"></a>

**To edit your pipeline's PollForSourceChanges parameter**
**Important**  
In many cases, the `PollForSourceChanges` parameter defaults to true when you create a pipeline\. When you add event\-based change detection, you must add the parameter to your output and set it to false to disable polling\. Otherwise, your pipeline starts twice for a single source change\. For details, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.
+ In the template, change `PollForSourceChanges` to `false`\. If you did not include `PollForSourceChanges` in your pipeline definition, add it and set it to `false`\.

  **Why am I making this change?** Changing this parameter to `false` turns off periodic checks so you can use event\-based change detection only\.

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
                  Provider: CodeCommit
                OutputArtifacts: 
                  - Name: SourceOutput
                Configuration: 
                  BranchName: !Ref BranchName
                  RepositoryName: !Ref RepositoryName
                  PollForSourceChanges: false
                RunOrder: 1
  ```

------
#### [ JSON ]

  ```
  {
    "Name": "Source", 
    "Actions": [
      {
        "Name": "SourceAction",
        "ActionTypeId": {
          "Category": "Source",
          "Owner": "AWS",
          "Version": 1,
          "Provider": "CodeCommit"
        },
        "OutputArtifacts": [
          {
            "Name": "SourceOutput"
          }
        ],
        "Configuration": {
          "BranchName": {
            "Ref": "BranchName"
          },
          "RepositoryName": {
            "Ref": "RepositoryName"
          },
          "PollForSourceChanges": false
        },
        "RunOrder": 1
      }
    ]
  },
  ```

------

**Example**  
When you create these resources with AWS CloudFormation, your pipeline is triggered when files in your repository are created or updated\. Here is the final template snippet:  

```
Resources:
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
  AmazonCloudWatchEventRule:
    Type: AWS::Events::Rule
    Properties:
      EventPattern:
        source:
          - aws.codecommit
        detail-type:
          - 'CodeCommit Repository State Change'
        resources:
          - !Join [ '', [ 'arn:aws:codecommit:', !Ref 'AWS::Region', ':', !Ref 'AWS::AccountId', ':', !Ref RepositoryName ] ]
        detail:
          event:
            - referenceCreated
            - referenceUpdated
          referenceType:
            - branch
          referenceName:
            - master
      Targets:
        -
          Arn: 
            !Join [ '', [ 'arn:aws:codepipeline:', !Ref 'AWS::Region', ':', !Ref 'AWS::AccountId', ':', !Ref AppPipeline ] ]
          RoleArn: !GetAtt AmazonCloudWatchEventRole.Arn
          Id: codepipeline-AppPipeline
  AppPipeline: 
    Type: AWS::CodePipeline::Pipeline
    Properties: 
      Name: codecommit-events-pipeline
      RoleArn: 
        !GetAtt CodePipelineServiceRole.Arn
      Stages: 
        - 
          Name: Source
          Actions: 
            - 
              Name: SourceAction
              ActionTypeId: 
                Category: Source
                Owner: AWS
                Version: 1
                Provider: CodeCommit
              OutputArtifacts: 
                - Name: SourceOutput
              Configuration: 
                BranchName: !Ref BranchName
                RepositoryName: !Ref RepositoryName
                PollForSourceChanges: false
              RunOrder: 1


...
```

```
    "Resources": {

...

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
                                    }
                                }
                            ]
                        }
                    }
                ]
            }
        },
        "AmazonCloudWatchEventRule": {
            "Type": "AWS::Events::Rule",
            "Properties": {
                "EventPattern": {
                    "source": [
                        "aws.codecommit"
                    ],
                    "detail-type": [
                        "CodeCommit Repository State Change"
                    ],
                    "resources": [
                        {
                            "Fn::Join": [
                                "",
                                [
                                    "arn:aws:codecommit:",
                                    {
                                        "Ref": "AWS::Region"
                                    },
                                    ":",
                                    {
                                        "Ref": "AWS::AccountId"
                                    },
                                    ":",
                                    {
                                        "Ref": "RepositoryName"
                                    }
                                ]
                            ]
                        }
                    ],
                    "detail": {
                        "event": [
                            "referenceCreated",
                            "referenceUpdated"
                        ],
                        "referenceType": [
                            "branch"
                        ],
                        "referenceName": [
                            "master"
                        ]
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
        },
        "AppPipeline": {
            "Type": "AWS::CodePipeline::Pipeline",
            "Properties": {
                "Name": "codecommit-events-pipeline",
                "RoleArn": {
                    "Fn::GetAtt": [
                        "CodePipelineServiceRole",
                        "Arn"
                    ]
                },
                "Stages": [
                    {
                        "Name": "Source",
                        "Actions": [
                            {
                                "Name": "SourceAction",
                                "ActionTypeId": {
                                    "Category": "Source",
                                    "Owner": "AWS",
                                    "Version": 1,
                                    "Provider": "CodeCommit"
                                },
                                "OutputArtifacts": [
                                    {
                                        "Name": "SourceOutput"
                                    }
                                ],
                                "Configuration": {
                                    "BranchName": {
                                        "Ref": "BranchName"
                                    },
                                    "RepositoryName": {
                                        "Ref": "RepositoryName"
                                    },
                                    "PollForSourceChanges": false
                                },
                                "RunOrder": 1
                            }
                        ]
                    },

...
```

### Update pipelines for push events \(Amazon S3 source\) \(AWS CloudFormation template\)<a name="update-change-detection-cfn-s3"></a>

Use these steps to edit your pipeline with an Amazon S3 source from polling to event\-based change detection\.

To build an event\-driven pipeline with Amazon S3, you edit the `PollForSourceChanges` parameter of your pipeline and then add the following resources to your template:
+ Amazon CloudWatch Events requires that all Amazon S3 events must be logged\. You must create an AWS CloudTrail trail, bucket, and bucket policy that Amazon S3 can use to log the events that occur\. For more information, see [Logging Management and Data Events with AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-management-and-data-events-with-cloudtrail.html)\.
+ Amazon CloudWatch Events rule and IAM role to allow this event to start our pipeline\.

If you use AWS CloudFormation to create and manage your pipelines, your template includes content like the following\.

**Note**  
The `Configuration` property in the source stage called `PollForSourceChanges`\. If your template doesn't include that property, then `PollForSourceChanges` is set to `true` by default\.

------
#### [ YAML ]

```
  AppPipeline: 
    Type: AWS::CodePipeline::Pipeline
    Properties: 
      RoleArn: !GetAtt CodePipelineServiceRole.Arn
      Stages: 
        - 
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
                - 
                  Name: SourceOutput
              Configuration: 
                S3Bucket: !Ref SourceBucket
                S3ObjectKey: !Ref S3SourceObjectKey
                PollForSourceChanges: true
              RunOrder: 1


...
```

------
#### [ JSON ]

```
        "AppPipeline": {
            "Type": "AWS::CodePipeline::Pipeline",
            "Properties": {
                "RoleArn": {
                    "Fn::GetAtt": ["CodePipelineServiceRole", "Arn"]
                },
                "Stages": [
                    {
                        "Name": "Source",
                        "Actions": [
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
                                    "PollForSourceChanges": true
                                },
                                "RunOrder": 1
                            }
                        ]
                    },


...
```

------<a name="proc-cfn-event-s3-createrule"></a>

**To create a CloudWatch Events rule with Amazon S3 as the event source and CodePipeline as the target and apply the permissions policy**

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

1. Use the `AWS::Events::Rule` AWS CloudFormation resource to add a CloudWatch Events rule\. This event pattern creates an event that monitors `CopyObject`, `PutObject` and `CompleteMultipartUpload` on your Amazon S3 source bucket\. In addition, include a target of your pipeline\. When `CopyObject`, `PutObject`, or `CompleteMultipartUpload` occurs, this rule invokes `StartPipelineExecution` on your target pipeline\.

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
               - CopyObject
               - PutObject
               - CompleteMultipartUpload
             requestParameters:
               bucketName:
                 - !Ref SourceBucket
               key:
                 - !Ref SourceObjectKey
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
               "CopyObject",
               "PutObject",
               "CompleteMultipartUpload"
             ],
             "requestParameters": {
               "bucketName": [
                 {
                   "Ref": "SourceBucket"
                 }
               ],
               "key": [
                 {
                   "Ref": "SourceObjectKey"
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
**Important**  
When you create a pipeline with this method, the `PollForSourceChanges` parameter defaults to true if it is not explicitly set to false\. When you add event\-based change detection, you must add the parameter to your output and set it to false to disable polling\. Otherwise, your pipeline starts twice for a single source change\. For details, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.
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
  #   - S3 SourceBucket and SourceObjectKey must exist
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

**Example**  
When you use AWS CloudFormation to create these resources, your pipeline is triggered when files in your repository are created or updated\.   
Do not stop here\. Although your pipeline is created, you must create a second AWS CloudFormation template for your Amazon S3 pipeline\. If you do not create the second template, your pipeline does not have any change detection functionality\.

```
Resources:
  SourceBucket:
    Type: AWS::S3::Bucket
    Properties:
      VersioningConfiguration: 
        Status: Enabled
  CodePipelineArtifactStoreBucket:
    Type: AWS::S3::Bucket
  CodePipelineArtifactStoreBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket: !Ref CodePipelineArtifactStoreBucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          -
            Sid: DenyUnEncryptedObjectUploads
            Effect: Deny
            Principal: '*'
            Action: s3:PutObject
            Resource: !Join [ '', [ !GetAtt CodePipelineArtifactStoreBucket.Arn, '/*' ] ]
            Condition:
              StringNotEquals: 
                s3:x-amz-server-side-encryption: aws:kms
          -
            Sid: DenyInsecureConnections
            Effect: Deny
            Principal: '*'
            Action: s3:*
            Resource: !Join [ '', [ !GetAtt CodePipelineArtifactStoreBucket.Arn, '/*' ] ]
            Condition:
              Bool:
                aws:SecureTransport: false
  CodePipelineServiceRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          -
            Effect: Allow
            Principal:
              Service:
                - codepipeline.amazonaws.com
            Action: sts:AssumeRole
      Path: /
      Policies:
        -
          PolicyName: AWS-CodePipeline-Service-3
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              -
                Effect: Allow
                Action:
                  - codecommit:CancelUploadArchive
                  - codecommit:GetBranch
                  - codecommit:GetCommit
                  - codecommit:GetUploadArchiveStatus
                  - codecommit:UploadArchive
                Resource: '*'
              -
                Effect: Allow
                Action:
                  - codedeploy:CreateDeployment
                  - codedeploy:GetApplicationRevision
                  - codedeploy:GetDeployment
                  - codedeploy:GetDeploymentConfig
                  - codedeploy:RegisterApplicationRevision
                Resource: '*'
              -
                Effect: Allow
                Action:
                  - codebuild:BatchGetBuilds
                  - codebuild:StartBuild
                Resource: '*'
              -
                Effect: Allow
                Action:
                  - devicefarm:ListProjects
                  - devicefarm:ListDevicePools
                  - devicefarm:GetRun
                  - devicefarm:GetUpload
                  - devicefarm:CreateUpload
                  - devicefarm:ScheduleRun
                Resource: '*'
              -
                Effect: Allow
                Action:
                  - lambda:InvokeFunction
                  - lambda:ListFunctions
                Resource: '*'
              -
                Effect: Allow
                Action:
                  - iam:PassRole
                Resource: '*'
              -
                Effect: Allow
                Action:
                  - elasticbeanstalk:*
                  - ec2:*
                  - elasticloadbalancing:*
                  - autoscaling:*
                  - cloudwatch:*
                  - s3:*
                  - sns:*
                  - cloudformation:*
                  - rds:*
                  - sqs:*
                  - ecs:*
                Resource: '*'
  AppPipeline: 
    Type: AWS::CodePipeline::Pipeline
    Properties: 
      Name: s3-events-pipeline
      RoleArn: 
        !GetAtt CodePipelineServiceRole.Arn
      Stages: 
        - 
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
        - 
          Name: Beta
          Actions: 
            - 
              Name: BetaAction
              InputArtifacts: 
                - Name: SourceOutput
              ActionTypeId: 
                Category: Deploy
                Owner: AWS
                Version: 1
                Provider: CodeDeploy
              Configuration: 
                ApplicationName: !Ref ApplicationName
                DeploymentGroupName: !Ref BetaFleet
              RunOrder: 1
      ArtifactStore: 
        Type: S3
        Location: !Ref CodePipelineArtifactStoreBucket
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
            
Outputs:
  SourceBucketARN:
    Description: "S3 bucket ARN that Cloudtrail will use"
    Value: !GetAtt SourceBucket.Arn
    Export:
      Name: SourceBucketARN
```

```
    "Resources": {
        "SourceBucket": {
            "Type": "AWS::S3::Bucket",
            "Properties": {
                "VersioningConfiguration": {
                    "Status": "Enabled"
                }
            }
        },
        "CodePipelineArtifactStoreBucket": {
            "Type": "AWS::S3::Bucket"
        },
        "CodePipelineArtifactStoreBucketPolicy": {
            "Type": "AWS::S3::BucketPolicy",
            "Properties": {
                "Bucket": {
                    "Ref": "CodePipelineArtifactStoreBucket"
                },
                "PolicyDocument": {
                    "Version": "2012-10-17",
                    "Statement": [
                        {
                            "Sid": "DenyUnEncryptedObjectUploads",
                            "Effect": "Deny",
                            "Principal": "*",
                            "Action": "s3:PutObject",
                            "Resource": {
                                "Fn::Join": [
                                    "",
                                    [
                                        {
                                            "Fn::GetAtt": [
                                                "CodePipelineArtifactStoreBucket",
                                                "Arn"
                                            ]
                                        },
                                        "/*"
                                    ]
                                ]
                            },
                            "Condition": {
                                "StringNotEquals": {
                                    "s3:x-amz-server-side-encryption": "aws:kms"
                                }
                            }
                        },
                        {
                            "Sid": "DenyInsecureConnections",
                            "Effect": "Deny",
                            "Principal": "*",
                            "Action": "s3:*",
                            "Resource": {
                                "Fn::Join": [
                                    "",
                                    [
                                        {
                                            "Fn::GetAtt": [
                                                "CodePipelineArtifactStoreBucket",
                                                "Arn"
                                            ]
                                        },
                                        "/*"
                                    ]
                                ]
                            },
                            "Condition": {
                                "Bool": {
                                    "aws:SecureTransport": false
                                }
                            }
                        }
                    ]
                }
            }
        },
        "CodePipelineServiceRole": {
            "Type": "AWS::IAM::Role",
            "Properties": {
                "AssumeRolePolicyDocument": {
                    "Version": "2012-10-17",
                    "Statement": [
                        {
                            "Effect": "Allow",
                            "Principal": {
                                "Service": [
                                    "codepipeline.amazonaws.com"
                                ]
                            },
                            "Action": "sts:AssumeRole"
                        }
                    ]
                },
                "Path": "/",
                "Policies": [
                    {
                        "PolicyName": "AWS-CodePipeline-Service-3",
                        "PolicyDocument": {
                            "Version": "2012-10-17",
                            "Statement": [
                                {
                                    "Effect": "Allow",
                                    "Action": [
                                        "codecommit:CancelUploadArchive",
                                        "codecommit:GetBranch",
                                        "codecommit:GetCommit",
                                        "codecommit:GetUploadArchiveStatus",
                                        "codecommit:UploadArchive"
                                    ],
                                    "Resource": "*"
                                },
                                {
                                    "Effect": "Allow",
                                    "Action": [
                                        "codedeploy:CreateDeployment",
                                        "codedeploy:GetApplicationRevision",
                                        "codedeploy:GetDeployment",
                                        "codedeploy:GetDeploymentConfig",
                                        "codedeploy:RegisterApplicationRevision"
                                    ],
                                    "Resource": "*"
                                },
                                {
                                    "Effect": "Allow",
                                    "Action": [
                                        "codebuild:BatchGetBuilds",
                                        "codebuild:StartBuild"
                                    ],
                                    "Resource": "*"
                                },
                                {
                                    "Effect": "Allow",
                                    "Action": [
                                        "devicefarm:ListProjects",
                                        "devicefarm:ListDevicePools",
                                        "devicefarm:GetRun",
                                        "devicefarm:GetUpload",
                                        "devicefarm:CreateUpload",
                                        "devicefarm:ScheduleRun"
                                    ],
                                    "Resource": "*"
                                },
                                {
                                    "Effect": "Allow",
                                    "Action": [
                                        "lambda:InvokeFunction",
                                        "lambda:ListFunctions"
                                    ],
                                    "Resource": "*"
                                },
                                {
                                    "Effect": "Allow",
                                    "Action": [
                                        "iam:PassRole"
                                    ],
                                    "Resource": "*"
                                },
                                {
                                    "Effect": "Allow",
                                    "Action": [
                                        "elasticbeanstalk:*",
                                        "ec2:*",
                                        "elasticloadbalancing:*",
                                        "autoscaling:*",
                                        "cloudwatch:*",
                                        "s3:*",
                                        "sns:*",
                                        "cloudformation:*",
                                        "rds:*",
                                        "sqs:*",
                                        "ecs:*"
                                    ],
                                    "Resource": "*"
                                }
                            ]
                        }
                    }
                ]
            }
        },
        "AppPipeline": {
            "Type": "AWS::CodePipeline::Pipeline",
            "Properties": {
                "Name": "s3-events-pipeline",
                "RoleArn": {
                    "Fn::GetAtt": [
                        "CodePipelineServiceRole",
                        "Arn"
                    ]
                },
                "Stages": [
                    {
                        "Name": "Source",
                        "Actions": [
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
                        ]
                    },
                    {
                        "Name": "Beta",
                        "Actions": [
                            {
                                "Name": "BetaAction",
                                "InputArtifacts": [
                                    {
                                        "Name": "SourceOutput"
                                    }
                                ],
                                "ActionTypeId": {
                                    "Category": "Deploy",
                                    "Owner": "AWS",
                                    "Version": 1,
                                    "Provider": "CodeDeploy"
                                },
                                "Configuration": {
                                    "ApplicationName": {
                                        "Ref": "ApplicationName"
                                    },
                                    "DeploymentGroupName": {
                                        "Ref": "BetaFleet"
                                    }
                                },
                                "RunOrder": 1
                            }
                        ]
                    }
                ],
                "ArtifactStore": {
                    "Type": "S3",
                    "Location": {
                        "Ref": "CodePipelineArtifactStoreBucket"
                    }
                }
            }
        },
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
                                    }
                                }
                            ]
                        }
                    }
                ]
            }
        },
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
    "Outputs" : {
        "SourceBucketARN" : {
            "Description" : "S3 bucket ARN that Cloudtrail will use",
            "Value" : { "Fn::GetAtt": ["SourceBucket", "Arn"] },
            "Export" : {
                "Name" : "SourceBucketARN"
            }
        }
    }
}


...
```

### Update pipelines for push events \(GitHub source\) \(AWS CloudFormation template\)<a name="update-change-detection-cfn-github"></a>

Follow these steps to update your pipeline \(with a GitHub source\) from periodic checks \(polling\) to event\-based change detection using webhooks\.

To build an event\-driven pipeline with AWS CodeCommit, you edit the `PollForSourceChanges` parameter of your pipeline and then add the following resources to your template:
+ A GitHub webhook

If you use AWS CloudFormation to create and manage your pipelines, your template has content like the following\.

**Note**  
Note the `PollForSourceChanges` configuration property in the source stage\. If your template doesn't include that property, then `PollForSourceChanges` is set to `true` by default\.

------
#### [ YAML ]

```
Resources:
  AppPipeline: 
    Type: AWS::CodePipeline::Pipeline
    Properties: 
      Name: github-polling-pipeline
      RoleArn: 
        !GetAtt CodePipelineServiceRole.Arn
      Stages: 
        - 
          Name: Source
          Actions: 
            - 
              Name: SourceAction
              ActionTypeId: 
                Category: Source
                Owner: ThirdParty
                Version: 1
                Provider: GitHub
              OutputArtifacts: 
                - Name: SourceOutput
              Configuration: 
                Owner: !Ref GitHubOwner
                Repo: !Ref RepositoryName
                Branch: !Ref BranchName
                OAuthToken: {{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}                PollForSourceChanges: true
              RunOrder: 1


...
```

------
#### [ JSON ]

```
        "AppPipeline": {
            "Type": "AWS::CodePipeline::Pipeline",
            "Properties": {
                "Name": "github-polling-pipeline",
                "RoleArn": {
                    "Fn::GetAtt": [
                        "CodePipelineServiceRole",
                        "Arn"
                    ]
                },
                "Stages": [
                    {
                        "Name": "Source",
                        "Actions": [
                            {
                                "Name": "SourceAction",
                                "ActionTypeId": {
                                    "Category": "Source",
                                    "Owner": "ThirdParty",
                                    "Version": 1,
                                    "Provider": "GitHub"
                                },
                                "OutputArtifacts": [
                                    {
                                        "Name": "SourceOutput"
                                    }
                                ],
                                "Configuration": {
                                    "Owner": {
                                        "Ref": "GitHubOwner"
                                    },
                                    "Repo": {
                                        "Ref": "RepositoryName"
                                    },
                                    "Branch": {
                                        "Ref": "BranchName"
                                    },
                                    "OAuthToken": "{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}",
                                    "PollForSourceChanges": true
                                },
                                "RunOrder": 1
                            }
                        ]
                    },


...
```

------<a name="proc-cfn-webhook-github"></a>

**To add parameters and create a webhook in your template**

We strongly recommend that you use AWS Secrets Manager to store your credentials\. If you use Secrets Manager, you must have already configured and stored your secret parameters in Secrets Manager\. This example uses dynamic references to AWS Secrets Manager for the GitHub credentials for your webhook\. For more information, see [ Using Dynamic References to Specify Template Values](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/dynamic-references.html#dynamic-references-secretsmanager)\. 
**Important**  
When passing secret parameters, do not enter the value directly into the template\. The value is rendered as plaintext and is therefore readable\. For security reasons, do not use plaintext in your AWS CloudFormation template to store your credentials\.

When you use the CLI or AWS CloudFormation to create a pipeline and add a webhook, you must disable periodic checks\.
**Note**  
To disable periodic checks, you must explicitly add the `PollForSourceChanges` parameter and set it to false, as detailed in the final procedure below\. Otherwise, the default for a CLI or AWS CloudFormation pipeline is that `PollForSourceChanges` defaults to true and does not display in the pipeline structure output\. For more information about PollForSourceChanges defaults, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.

1. In the template, under `Resources`, add your parameters:

------
#### [ YAML ]

   ```
   Parameters:
           GitHubOwner:
             Type: String
   
   ...
   ```

------
#### [ JSON ]

   ```
   {
     "Parameters": {
       "BranchName": {
         "Description": "GitHub branch name",
           "Type": "String",
             "Default": "master"
       },
       "GitHubOwner": {
         "Type": "String"
       },
   
   
   ...
   ```

------

1. Use the `AWS::CodePipeline::Webhook` AWS CloudFormation resource to add a webhook\.
**Note**  
The `TargetAction` you specify must match the `Name` property of the source action defined in the pipeline\.

   If `RegisterWithThirdParty` is set to `true`, make sure the user associated to the `OAuthToken` can set the required scopes in GitHub\. The token and webhook require the following GitHub scopes: 
   + `repo` \- used for full control to read and pull artifacts from public and private repositories into a pipeline\. 
   + `admin:repo_hook` \- used for full control of repository hooks\.

   Otherwise, GitHub returns a 404\. For more information about the 404 returned, see [https://help.github.com/articles/about-webhooks](https://help.github.com/articles/about-webhooks)\.

------
#### [ YAML ]

   ```
     AppPipelineWebhook:
       Type: AWS::CodePipeline::Webhook
       Properties:
         Authentication: GITHUB_HMAC
         AuthenticationConfiguration:
           SecretToken: {{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}
         Filters:
           - 
             JsonPath: "$.ref"
             MatchEquals: refs/heads/{Branch}
         TargetPipeline: !Ref AppPipeline
         TargetAction: SourceAction
         Name: AppPipelineWebhook
         TargetPipelineVersion: !GetAtt AppPipeline.Version
         RegisterWithThirdParty: true
   
   
   ...
   ```

------
#### [ JSON ]

   ```
     "AppPipelineWebhook": {
       "Type": "AWS::CodePipeline::Webhook",
       "Properties": {
         "Authentication": "GITHUB_HMAC",
         "AuthenticationConfiguration": {
           "SecretToken": "{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}"
         },
         "Filters": [
           {
             "JsonPath": "$.ref",
             "MatchEquals": "refs/heads/{Branch}"
           }
         ],
         "TargetPipeline": {
           "Ref": "AppPipeline"
         },
         "TargetAction": "SourceAction",
         "Name": "AppPipelineWebhook",
         "TargetPipelineVersion": {
           "Fn::GetAtt": [
             "AppPipeline",
             "Version"
           ]
         },
         "RegisterWithThirdParty": true
       }
     },
   
   ...
   ```

------

1. Save the updated template to your local computer, and then open the AWS CloudFormation console\. 

1. Choose your stack, and then choose **Create Change Set for Current Stack**\.

1. Upload the template, and then view the changes listed in AWS CloudFormation\. These are the changes to be made to the stack\. You should see your new resources in the list\. 

1. Choose **Execute**\.<a name="proc-cfn-flag-github"></a>

**To edit your pipeline's PollForSourceChanges parameter**
**Important**  
When you create a pipeline with this method, the `PollForSourceChanges` parameter defaults to true if it is not explicitly set to false\. When you add event\-based change detection, you must add the parameter to your output and set it to false to disable polling\. Otherwise, your pipeline starts twice for a single source change\. For details, see [Default settings for the PollForSourceChanges parameter](reference-pipeline-structure.md#PollForSourceChanges-defaults)\.
+ In the template, change `PollForSourceChanges` to `false`\. If you did not include `PollForSourceChanges` in your pipeline definition, add it and set it to false\.

  **Why am I making this change? ** Changing this parameter to `false` turns off periodic checks so you can use event\-based change detection only\.

------
#### [ YAML ]

  ```
            Name: Source
            Actions: 
              - 
                Name: SourceAction
                ActionTypeId: 
                  Category: Source
                  Owner: ThirdParty
                  Version: 1
                  Provider: GitHub
                OutputArtifacts: 
                  - Name: SourceOutput
                Configuration: 
                  Owner: !Ref GitHubOwner
                  Repo: !Ref RepositoryName
                  Branch: !Ref BranchName
                  OAuthToken: {{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}
                  PollForSourceChanges: false
                RunOrder: 1
  ```

------
#### [ JSON ]

  ```
   {
      "Name": "Source",
      "Actions": [
        {
          "Name": "SourceAction",
          "ActionTypeId": {
            "Category": "Source",
            "Owner": "ThirdParty",
            "Version": 1,
            "Provider": "GitHub"
          },
          "OutputArtifacts": [
            {
              "Name": "SourceOutput"
            }
          ],
          "Configuration": {
            "Owner": {
              "Ref": "GitHubOwner"
            },
            "Repo": {
              "Ref": "RepositoryName"
            },
            "Branch": {
              "Ref": "BranchName"
            },
            "OAuthToken": "{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}",
            "PollForSourceChanges": false
          },
          "RunOrder": 1
        }
  ```

------

**Example**  
When you create these resources with AWS CloudFormation, the webhook defined is created in the specified GitHub repository\. Your pipeline is triggered on commit\.   

```
Parameters:
  GitHubOwner:
    Type: String
    
Resources:
  AppPipelineWebhook:
    Type: AWS::CodePipeline::Webhook
    Properties:
      Authentication: GITHUB_HMAC
      AuthenticationConfiguration:
        SecretToken: {{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}
      Filters:
        - 
          JsonPath: "$.ref"
          MatchEquals: refs/heads/{Branch}
      TargetPipeline: !Ref AppPipeline
      TargetAction: SourceAction
      Name: AppPipelineWebhook
      TargetPipelineVersion: !GetAtt AppPipeline.Version
      RegisterWithThirdParty: true
  AppPipeline: 
    Type: AWS::CodePipeline::Pipeline
    Properties: 
      Name: github-events-pipeline
      RoleArn: 
        !GetAtt CodePipelineServiceRole.Arn
      Stages: 
        - 
          Name: Source
          Actions: 
            - 
              Name: SourceAction
              ActionTypeId: 
                Category: Source
                Owner: ThirdParty
                Version: 1
                Provider: GitHub
              OutputArtifacts: 
                - Name: SourceOutput
              Configuration: 
                Owner: !Ref GitHubOwner
                Repo: !Ref RepositoryName
                Branch: !Ref BranchName
                OAuthToken: {{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}
                PollForSourceChanges: false
              RunOrder: 1

...
```

```
{
    "Parameters": {
        "BranchName": {
            "Description": "GitHub branch name",
            "Type": "String",
            "Default": "master"
        },
        "RepositoryName": {
            "Description": "GitHub repository name",
            "Type": "String",
            "Default": "test"
        },
        "GitHubOwner": {
            "Type": "String"
        },
        "ApplicationName": {
            "Description": "CodeDeploy application name",
            "Type": "String",
            "Default": "DemoApplication"
        },
        "BetaFleet": {
            "Description": "Fleet configured in CodeDeploy",
            "Type": "String",
            "Default": "DemoFleet"
        }
    },
    "Resources": {

...

        },
        "AppPipelineWebhook": {
            "Type": "AWS::CodePipeline::Webhook",
            "Properties": {
                "Authentication": "GITHUB_HMAC",
                "AuthenticationConfiguration": {
                    "SecretToken": {
                        "{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}"
                    }
                },
                "Filters": [
                    {
                        "JsonPath": "$.ref",
                        "MatchEquals": "refs/heads/{Branch}"
                    }
                ],
                "TargetPipeline": {
                    "Ref": "AppPipeline"
                },
                "TargetAction": "SourceAction",
                "Name": "AppPipelineWebhook",
                "TargetPipelineVersion": {
                    "Fn::GetAtt": [
                        "AppPipeline",
                        "Version"
                    ]
                },
                "RegisterWithThirdParty": true
            }
        },
        "AppPipeline": {
            "Type": "AWS::CodePipeline::Pipeline",
            "Properties": {
                "Name": "github-events-pipeline",
                "RoleArn": {
                    "Fn::GetAtt": [
                        "CodePipelineServiceRole",
                        "Arn"
                    ]
                },
                "Stages": [
                    {
                        "Name": "Source",
                        "Actions": [
                            {
                                "Name": "SourceAction",
                                "ActionTypeId": {
                                    "Category": "Source",
                                    "Owner": "ThirdParty",
                                    "Version": 1,
                                    "Provider": "GitHub"
                                },
                                "OutputArtifacts": [
                                    {
                                        "Name": "SourceOutput"
                                    }
                                ],
                                "Configuration": {
                                    "Owner": {
                                        "Ref": "GitHubOwner"
                                    },
                                    "Repo": {
                                        "Ref": "RepositoryName"
                                    },
                                    "Branch": {
                                        "Ref": "BranchName"
                                    },
                                    "OAuthToken": "{{resolve:secretsmanager:MyGitHubSecret:SecretString:token}}",
                                    "PollForSourceChanges": false
                                },
                                "RunOrder": 1

...
```