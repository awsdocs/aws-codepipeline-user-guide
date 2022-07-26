# Troubleshooting CodePipeline<a name="troubleshooting"></a>

The following information might help you troubleshoot common issues in AWS CodePipeline\.

**Topics**
+ [Pipeline error: A pipeline configured with AWS Elastic Beanstalk returns an error message: "Deployment failed\. The provided role does not have sufficient permissions: Service:AmazonElasticLoadBalancing"](#troubleshooting-aeb1)
+ [Deployment error: A pipeline configured with an AWS Elastic Beanstalk deploy action hangs instead of failing if the "DescribeEvents" permission is missing](#troubleshooting-aeb2)
+ [Pipeline error: A source action returns the insufficient permissions message: "Could not access the CodeCommit repository `repository-name`\. Make sure that the pipeline IAM role has sufficient permissions to access the repository\."](#troubleshooting-service-role-permissions)
+ [Pipeline error: A Jenkins build or test action runs for a long time and then fails due to lack of credentials or permissions](#troubleshooting-jen1)
+ [Pipeline error: A pipeline created in one AWS Region using a bucket created in another AWS Region returns an "InternalError" with the code "JobFailed"](#troubleshooting-reg-1)
+ [Deployment error: A ZIP file that contains a WAR file is deployed successfully to AWS Elastic Beanstalk, but the application URL reports a 404 not found error](#troubleshooting-aeb2)
+ [Pipeline artifact folder names appear to be truncated](#troubleshooting-truncated-artifacts)
+ [Add CodeBuild GitClone permissions for connections to Bitbucket, GitHub, or GitHub Enterprise Server](#codebuild-role-connections)
+ [Add CodeBuild GitClone permissions for CodeCommit source actions](#codebuild-role-codecommitclone)
+ [Pipeline error: A deployment with the CodeDeployToECS action returns an error message: "Exception while trying to read the task definition artifact file from: <source artifact name>"](#troubleshooting-ecstocodedeploy-size)
+ [GitHub version 1 source action: Repository list shows different repositories](#troubleshooting-connections-GitHub-org)
+ [GitHub version 2 source action: Unable to complete the connection for a repository](#troubleshooting-connections-GitHub-admin)
+ [Amazon S3 error: CodePipeline service role <ARN> is getting S3 access denied for the S3 bucket <BucketName>](#troubleshooting-S3-access-denied-list)
+ [Pipelines with an Amazon S3, Amazon ECR, or CodeCommit source no longer start automatically](#troubleshooting-events-identifiers)
+ [Need help with a different issue?](#troubleshooting-other)

## Pipeline error: A pipeline configured with AWS Elastic Beanstalk returns an error message: "Deployment failed\. The provided role does not have sufficient permissions: Service:AmazonElasticLoadBalancing"<a name="troubleshooting-aeb1"></a>

**Problem:** The service role for CodePipeline does not have sufficient permissions for AWS Elastic Beanstalk, including, but not limited to, some operations in Elastic Load Balancing\. The service role for CodePipeline was updated on August 6, 2015 to address this issue\. Customers who created their service role before this date must modify the policy statement for their service role to add the required permissions\. 

**Possible fixes:** The easiest solution is to edit the policy statement for your service role as detailed in [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services)\.

After you apply the edited policy, follow the steps in [Start a pipeline manually](pipelines-rerun-manually.md) to manually rerun any pipelines that use Elastic Beanstalk\.

Depending on your security needs, you can modify the permissions in other ways, too\. 

## Deployment error: A pipeline configured with an AWS Elastic Beanstalk deploy action hangs instead of failing if the "DescribeEvents" permission is missing<a name="troubleshooting-aeb2"></a>

**Problem:** The service role for CodePipeline must include the `"elasticbeanstalk:DescribeEvents"` action for any pipelines that use AWS Elastic Beanstalk\. Without this permission, AWS Elastic Beanstalk deploy actions hang without failing or indicating an error\. If this action is missing from your service role, then CodePipeline does not have permissions to run the pipeline deployment stage in AWS Elastic Beanstalk on your behalf\.

**Possible fixes:** Review your CodePipeline service role\. If the `"elasticbeanstalk:DescribeEvents"` action is missing, use the steps in [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services) to add it using the **Edit Policy** feature in the IAM console\.

After you apply the edited policy, follow the steps in [Start a pipeline manually](pipelines-rerun-manually.md) to manually rerun any pipelines that use Elastic Beanstalk\.

## Pipeline error: A source action returns the insufficient permissions message: "Could not access the CodeCommit repository `repository-name`\. Make sure that the pipeline IAM role has sufficient permissions to access the repository\."<a name="troubleshooting-service-role-permissions"></a>

**Problem:** The service role for CodePipeline does not have sufficient permissions for CodeCommit and likely was created before support for using CodeCommit repositories was added on April 18, 2016\. Customers who created their service role before this date must modify the policy statement for their service role to add the required permissions\. 

**Possible fixes:** Add the required permissions for CodeCommit to your CodePipeline service role's policy\. For more information, see [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services)\.

## Pipeline error: A Jenkins build or test action runs for a long time and then fails due to lack of credentials or permissions<a name="troubleshooting-jen1"></a>

**Problem:** If the Jenkins server is installed on an Amazon EC2 instance, the instance might not have been created with an instance role that has the permissions required for CodePipeline\. If you are using an IAM user on a Jenkins server, an on\-premises instance, or an Amazon EC2 instance created without the required IAM role, the IAM user either does not have the required permissions, or the Jenkins server cannot access those credentials through the profile configured on the server\. 

**Possible fixes:** Make sure that Amazon EC2 instance role or IAM user is configured with the `AWSCodePipelineCustomActionAccess` managed policy or with the equivalent permissions\. For more information, see [AWS managed policies for CodePipeline](managed-policies.md)\.

If you are using an IAM user, make sure the AWS profile configured on the instance uses the IAM user configured with the correct permissions\. You might have to provide the IAM user credentials you configured for integration between Jenkins and CodePipeline directly into the Jenkins UI\. This is not a recommended best practice\. If you must do so, be sure the Jenkins server is secured and uses HTTPS instead of HTTP\.

## Pipeline error: A pipeline created in one AWS Region using a bucket created in another AWS Region returns an "InternalError" with the code "JobFailed"<a name="troubleshooting-reg-1"></a>

**Problem:** The download of an artifact stored in an Amazon S3 bucket will fail if the pipeline and bucket are created in different AWS Regions\.

**Possible fixes:** Make sure the Amazon S3 bucket where your artifact is stored is in the same AWS Region as the pipeline you have created\.

## Deployment error: A ZIP file that contains a WAR file is deployed successfully to AWS Elastic Beanstalk, but the application URL reports a 404 not found error<a name="troubleshooting-aeb2"></a>

**Problem:** A WAR file is deployed successfully to an AWS Elastic Beanstalk environment, but the application URL returns a 404 Not Found error\.

**Possible fixes:** AWS Elastic Beanstalk can unpack a ZIP file, but not a WAR file contained in a ZIP file\. Instead of specifying a WAR file in your `buildspec.yml` file, specify a folder that contains the content to be deployed\. For example:

```
version: 0.2

phases:
  post_build:
    commands:
      - mvn package
      - mv target/my-web-app ./
artifacts:
  files:
    - my-web-app/**/*
 discard-paths: yes
```

For an example, see [AWS Elastic Beanstalk Sample for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-elastic-beanstalk.html)\.

## Pipeline artifact folder names appear to be truncated<a name="troubleshooting-truncated-artifacts"></a>

**Problem:** When you view pipeline artifact names in CodePipeline, the names appear to be truncated\. This can make the names appear to be similar or seem to no longer contain the entire pipeline name\.

**Explanation:** CodePipeline truncates artifact names to ensure that the full Amazon S3 path does not exceed policy size limits when CodePipeline generates temporary credentials for job workers\.

Even though the artifact name appears to be truncated, CodePipeline maps to the artifact bucket in a way that is not affected by artifacts with truncated names\. The pipeline can function normally\. This is not an issue with the folder or artifacts\. There is a 100\-character limit to pipeline names\. Although the artifact folder name might appear to be shortened, it is still unique for your pipeline\.

## Add CodeBuild GitClone permissions for connections to Bitbucket, GitHub, or GitHub Enterprise Server<a name="codebuild-role-connections"></a>

When you use an AWS CodeStar connection in a source action and a CodeBuild action, there are two ways the input artifact can be passed to the build:
+ The default: The source action produces a zip file that contains the code that CodeBuild downloads\.
+ Git clone: The source code can be directly downloaded to the build environment\. 

  The Git clone mode allows you to interact with the source code as a working Git repository\. To use this mode, you must grant your CodeBuild environment permissions to use the connection\.

To add permissions to your CodeBuild service role policy, you create a customer\-managed policy that you attach to your CodeBuild service role\. The following steps create a policy where the `UseConnection` permission is specified in the `action` field, and the connection ARN is specified in the `Resource` field\. 

**To use the console to add the UseConnection permissions**

1. To find the connection ARN for your pipeline, open your pipeline and click the \(i\) icon on your source action\. You add the connection ARN to your CodeBuild service role policy\.

   An example connection ARN is:

   ```
   arn:aws:codestar-connections:eu-central-1:123456789123:connection/sample-1908-4932-9ecc-2ddacee15095
   ```

1. To find your CodeBuild service role, open the build project used in your pipeline and navigate to the **Build details** tab\. 

1. Choose the **Service role** link\. This opens the IAM console where you can add a new policy that grants access to your connection\.

1. In the IAM console, choose **Attach policies**, and then choose **Create policy**\.

   Use the following sample policy template\. Add your connection ARN in the `Resource` field, as shown in this example:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "codestar-connections:UseConnection",
               "Resource": "insert connection ARN here"
           }
       ]
   }
   ```

   On the **JSON** tab, paste your policy\.

1. Choose **Review policy**\. Enter a name for the policy \(for example, **connection\-permissions**\), and then choose **Create policy**\.

1. Return to the page where you were attaching permissions, refresh the policy list, and select the policy you just created\. Choose **Attach policies**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/gitclone-role-policy-attach.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## Add CodeBuild GitClone permissions for CodeCommit source actions<a name="codebuild-role-codecommitclone"></a>

When your pipeline has a CodeCommit source action, there are two ways you can pass the input artifact to the build:
+ **Default** – The source action produces a zip file that contains the code that CodeBuild downloads\.
+ **Full clone** – The source code can be directly downloaded to the build environment\. 

  The **Full clone** option allows you to interact with the source code as a working Git repository\. To use this mode, you must add permissions for your CodeBuild environment to pull from your repository\.

To add permissions to your CodeBuild service role policy, you create a customer\-managed policy that you attach to your CodeBuild service role\. The following steps create a policy that specifies the `codecommit:GitPull` permission in the `action` field\.

**To use the console to add the GitPull permissions**

1. To find your CodeBuild service role, open the build project used in your pipeline and navigate to the **Build details** tab\.

1. Choose the **Service role** link\. This opens the IAM console where you can add a new policy that grants access to your repository\.

1. In the IAM console, choose **Attach policies**, and then choose **Create policy**\. 

1. On the **JSON** tab, paste the following sample policy\.

   ```
   {
       "Action": [
           "codecommit:GitPull"
       ],
       "Resource": "*",
       "Effect": "Allow"
   },
   ```

1. Choose **Review policy**\. Enter a name for the policy \(for example, **codecommit\-gitpull**\), and then choose **Create policy**\.

1. Return to the page where you were attaching permissions, refresh the policy list, and select the policy you just created\. Choose **Attach policies**\.

## Pipeline error: A deployment with the CodeDeployToECS action returns an error message: "Exception while trying to read the task definition artifact file from: <source artifact name>"<a name="troubleshooting-ecstocodedeploy-size"></a>

**Problem:** 

The task definition file is a required artifact for the CodePipeline deploy action to Amazon ECS through CodeDeploy \(the `CodeDeployToECS` action\)\. The maximum artifact ZIP size in the `CodeDeployToECS` deploy action is 3 MB\. The following error message is returned when the file is not found or the artifact size exceeds 3 MB:

Exception while trying to read the task definition artifact file from: <source artifact name>

**Possible fixes:** Make sure the task definition file is included as an artifact\. If the file already exists, makes sure the compressed size is less than 3 MB\.

## GitHub version 1 source action: Repository list shows different repositories<a name="troubleshooting-connections-GitHub-org"></a>

**Problem:** 

After a successful authorization for a GitHub version 1 action in the CodePipeline console, you can choose from a list of your GitHub repositories\. If the list does not include the repositories you expected to see, then you can troubleshoot the account used for authorization\.

**Possible fixes:** The list of repositories provided in the CodePipeline console are based on the GitHub organization the authorized account belongs to\. Verify that the account you are using to authorize with GitHub is the account associated with the GitHub organization where your repository is created\.

## GitHub version 2 source action: Unable to complete the connection for a repository<a name="troubleshooting-connections-GitHub-admin"></a>

**Problem:** 

Because a connection to a GitHub repository uses the AWS Connector for GitHub, you need organization owner permissions or admin permissions to the repository to create the connection\.

**Possible fixes:** For information about permission levels for a GitHub repository, see [https://docs\.github\.com/en/free\-pro\-team@latest/github/setting\-up\-and\-managing\-organizations\-and\-teams/permission\-levels\-for\-an\-organization](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/permission-levels-for-an-organization)\.

## Amazon S3 error: CodePipeline service role <ARN> is getting S3 access denied for the S3 bucket <BucketName><a name="troubleshooting-S3-access-denied-list"></a>

**Problem:** 

While in progress, the CodeCommit action in CodePipeline checks that the pipeline artifact bucket exists\. If the action does not have permission to check, an `AccessDenied` error occurs in Amazon S3 and the following error message displays in CodePipeline:

CodePipeline service role "arn:aws:iam::*AccountID*:role/service\-role/*RoleID*" is getting S3 access denied for the S3 bucket "*BucketName*"

The CloudTrail logs for the action also log the `AccessDenied` error\.

**Possible fixes:** Do the following:
+ For the policy attached to your CodePipeline service role, add `s3:ListBucket` to the list of actions in your policy\. For instructions on to view your service role policy, see [View the pipeline ARN and service role ARN \(console\)](pipelines-view-console.md#pipelines-settings-console)\. Edit the policy statement for your service role as detailed in [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services)\.
+ For the resource\-based policy attached to the Amazon S3 artifact bucket for your pipeline, also called the *artifact bucket policy*, add a statement to allow the `s3:ListBucket` permission to be used by your CodePipeline service role\. 

**To add your policy to the artifact bucket**

  1. Follow the steps in [View the pipeline ARN and service role ARN \(console\)](pipelines-view-console.md#pipelines-settings-console) to choose your artifact bucket on the pipeline **Settings** page and then view it in the Amazon S3 console\.

  1. Choose **Permissions**\.

  1. Under **Bucket policy**, choose **Edit**\.

  1. In the **Policy** text field, enter a new bucket policy, or edit the existing policy as shown in the following example\. The bucket policy is a JSON file, so you must enter valid JSON\.

     The following example shows a bucket policy statement for an artifact bucket where the example role ID for the service role is *AROAEXAMPLEID*\.

     ```
     {
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:ListBucket",
         "Resource": "arn:aws:s3:::BucketName",
         "Condition": {
             "StringLike": {
                 "aws:userid": "AROAEXAMPLEID:*"
             }
         }
     }
     ```

     The following example shows the same bucket policy statement after the permission is added\.

     ```
     {
         "Version": "2012-10-17",
         "Id": "SSEAndSSLPolicy",
         "Statement": [
             {
                 "Effect": "Allow",
                 "Principal": "*",
                 "Action": "s3:ListBucket",
                 "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890",
                 "Condition": {
                     "StringLike": {
                         "aws:userid": "AROAEXAMPLEID:*"
                     }
                 }
             },
             {
                 "Sid": "DenyUnEncryptedObjectUploads",
                 "Effect": "Deny",
                 "Principal": "*",
                 "Action": "s3:PutObject",
                 "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*",
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
                 "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*",
                 "Condition": {
                     "Bool": {
                         "aws:SecureTransport": false
                     }
                 }
             }
         ]
     }
     ```

     For more information, see the steps in [https://aws.amazon.com/blogs/security/writing-iam-policies-how-to-grant-access-to-an-amazon-s3-bucket/](https://aws.amazon.com/blogs/security/writing-iam-policies-how-to-grant-access-to-an-amazon-s3-bucket/)\.

  1. Choose **Save**\.

After you apply the edited policy, follow the steps in [Start a pipeline manually](pipelines-rerun-manually.md) to manually rerun your pipeline\.

## Pipelines with an Amazon S3, Amazon ECR, or CodeCommit source no longer start automatically<a name="troubleshooting-events-identifiers"></a>

**Problem:** 

After making a change to configuration settings for an action that uses event rules \(EventBridge or CloudWatch Events\) for change detection, the console might not detect a change where source trigger identifiers are similar and have identical initial characters\. Because the new event rule is not created by the console, the pipeline no longer starts automatically\.

An example of a minor change at the end of the parameter name for CodeCommit would be changing your CodeCommit branch name `MyTestBranch-1` to `MyTestBranch-2`\. Because the change is at the end of the branch name, the event rule for the source action might not update or create a rule for the new source settings\.

This applies to source actions that use CWE events for change detection as follows:


****  

| Source action | Parameters / trigger identifiers \(console\) | 
| --- | --- | 
| Amazon ECR |  **Repository name** **Image tag**  | 
| Amazon S3 |  **Bucket** **S3 object key**  | 
| CodeCommit |  **Repository name** **Branch name**  | 

**Possible fixes:** 

Do one of the following:
+ Change the CodeCommit/S3/ECR configuration settings so that changes are made to the starting portion of the parameter value\.

  **Example:** Change your branch name `release-branch` to `2nd-release-branch`\. Avoid a change at the end of the name, such as `release-branch-2`\.
+ Change the CodeCommit/S3/ECR configuration settings for each pipeline\.

  **Example:** Change your branch name `myRepo/myBranch` to `myDeployRepo/myDeployBranch`\. Avoid a change at the end of the name, such as `myRepo/myBranch2`\.
+ Instead of the console, use the CLI or AWS CloudFormation to create and update your change\-detection event rules\. For instructions on creating event rules for an S3 source action, see [Amazon S3 source actions and CloudWatch Events](create-cloudtrail-S3-source.md)\. For instructions on creating event rules for an Amazon ECR action, see [ Amazon ECR source actions and CloudWatch Events](create-cwe-ecr-source.md)\. For instructions on creating event rules for a CodeCommit action, see [ CodeCommit source actions and CloudWatch Events](triggering.md)\.

After you edit your action configuration in the console, accept the updated change\-detection resources created by the console\.

## Need help with a different issue?<a name="troubleshooting-other"></a>

Try these other resources:
+ Contact [AWS Support](https://aws.amazon.com/contact-us/)\.
+ Ask a question in the [CodePipeline forum](https://forums.aws.amazon.com/forum.jspa?forumID=197)\.
+ [Request a quota increase](https://console.aws.amazon.com/support/home#/case/create%3FissueType=service-limit-increase)\. For more information, see [Quotas in AWS CodePipeline](limits.md)\.
**Note**  
It can take up to two weeks to process requests for a quota increase\.