# Troubleshooting CodePipeline<a name="troubleshooting"></a>

The following information might help you troubleshoot common issues in AWS CodePipeline\.

**Topics**
+ [Pipeline error: A pipeline configured with AWS Elastic Beanstalk returns an error message: "Deployment failed\. The provided role does not have sufficient permissions: Service:AmazonElasticLoadBalancing"](#troubleshooting-aeb1)
+ [Deployment error: A pipeline configured with an AWS Elastic Beanstalk deploy action hangs instead of failing if the "DescribeEvents" permission is missing](#troubleshooting-aeb2)
+ [Pipeline error: A source action returns the insufficient permissions message: "Could not access the CodeCommit repository `repository-name`\. Make sure that the pipeline IAM role has sufficient permissions to access the repository\."](#troubleshooting-service-role-permissions)
+ [Pipeline error: A Jenkins build or test action runs for a long time and then fails due to lack of credentials or permissions](#troubleshooting-jen1)
+ [Pipeline error: My GitHub source stage contains Git submodules, but CodePipeline doesn't initialize them](#troubleshooting-gs1)
+ [Pipeline error: I receive a pipeline error that says: "Could not access the GitHub repository" or "Unable to connect to the GitHub repository"](#troubleshooting-gs2)
+ [Pipeline error: A pipeline created in one AWS Region using a bucket created in another AWS Region returns an "InternalError" with the code "JobFailed"](#troubleshooting-reg-1)
+ [Deployment error: A ZIP file that contains a WAR file is deployed successfully to AWS Elastic Beanstalk, but the application URL reports a 404 not found error](#troubleshooting-aeb2)
+ [Pipeline artifact folder names appear to be truncated](#troubleshooting-truncated-artifacts)
+ [Pipeline error: A deployment with the CodeDeployToECS action returns an error message: "Exception while trying to read the task definition artifact file from: <source artifact name>"](#troubleshooting-ecstocodedeploy-size)
+ [Need help with a different issue?](#troubleshooting-other)

## Pipeline error: A pipeline configured with AWS Elastic Beanstalk returns an error message: "Deployment failed\. The provided role does not have sufficient permissions: Service:AmazonElasticLoadBalancing"<a name="troubleshooting-aeb1"></a>

**Problem:** The service role for CodePipeline does not have sufficient permissions for AWS Elastic Beanstalk, including, but not limited to, some operations in Elastic Load Balancing\. The service role for CodePipeline was updated on August 6, 2015 to address this issue\. Customers who created their service role before this date must modify the policy statement for their service role to add the required permissions\. 

**Possible fixes:** The easiest solution is to edit the policy statement for your service role as detailed in [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services)\.

After you apply the edited policy, follow the steps in [Start a pipeline manually in AWS CodePipeline](pipelines-rerun-manually.md) to manually rerun any pipelines that use Elastic Beanstalk\.

Depending on your security needs, you can modify the permissions in other ways, too\. 

## Deployment error: A pipeline configured with an AWS Elastic Beanstalk deploy action hangs instead of failing if the "DescribeEvents" permission is missing<a name="troubleshooting-aeb2"></a>

**Problem:** The service role for CodePipeline must include the `"elasticbeanstalk:DescribeEvents"` action for any pipelines that use AWS Elastic Beanstalk\. Without this permission, AWS Elastic Beanstalk deploy actions hang without failing or indicating an error\. If this action is missing from your service role, then CodePipeline does not have permissions to run the pipeline deployment stage in AWS Elastic Beanstalk on your behalf\.

**Possible fixes:** Review your CodePipeline service role\. If the `"elasticbeanstalk:DescribeEvents"` action is missing, use the steps in [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services) to add it using the **Edit Policy** feature in the IAM console\.

After you apply the edited policy, follow the steps in [Start a pipeline manually in AWS CodePipeline](pipelines-rerun-manually.md) to manually rerun any pipelines that use Elastic Beanstalk\.

## Pipeline error: A source action returns the insufficient permissions message: "Could not access the CodeCommit repository `repository-name`\. Make sure that the pipeline IAM role has sufficient permissions to access the repository\."<a name="troubleshooting-service-role-permissions"></a>

**Problem:** The service role for CodePipeline does not have sufficient permissions for CodeCommit and likely was created before support for using CodeCommit repositories was added on April 18, 2016\. Customers who created their service role before this date must modify the policy statement for their service role to add the required permissions\. 

**Possible fixes:** Add the required permissions for CodeCommit to your CodePipeline service role's policy\. For more information, see [Add permissions to the CodePipeline service role](security-iam.md#how-to-update-role-new-services)\.

## Pipeline error: A Jenkins build or test action runs for a long time and then fails due to lack of credentials or permissions<a name="troubleshooting-jen1"></a>

**Problem:** If the Jenkins server is installed on an Amazon EC2 instance, the instance might not have been created with an instance role that has the permissions required for CodePipeline\. If you are using an IAM user on a Jenkins server, an on\-premises instance, or an Amazon EC2 instance created without the required IAM role, the IAM user either does not have the required permissions, or the Jenkins server cannot access those credentials through the profile configured on the server\. 

**Possible fixes:** Make sure that Amazon EC2 instance role or IAM user is configured with the `AWSCodePipelineCustomActionAccess` managed policy or with the equivalent permissions\. For more information, see [AWS managed \(predefined\) policies for CodePipeline](managed-policies.md)\.

If you are using an IAM user, make sure the AWS profile configured on the instance uses the IAM user configured with the correct permissions\. You might have to provide the IAM user credentials you configured for integration between Jenkins and CodePipeline directly into the Jenkins UI\. This is not a recommended best practice\. If you must do so, be sure the Jenkins server is secured and uses HTTPS instead of HTTP\.

## Pipeline error: My GitHub source stage contains Git submodules, but CodePipeline doesn't initialize them<a name="troubleshooting-gs1"></a>

**Problem:** CodePipeline does not support git submodules\. CodePipeline relies on the archive link API from GitHub, which does not support submodules\.

**Possible fixes:** Consider cloning the GitHub repository directly as part of a separate script\. For example, you could include a clone action in a Jenkins script\.

## Pipeline error: I receive a pipeline error that says: "Could not access the GitHub repository" or "Unable to connect to the GitHub repository"<a name="troubleshooting-gs2"></a>

**Problem:** CodePipeline uses OAuth tokens to integrate with GitHub\. When you create a pipeline with a GitHub source provider, CodePipeline manages your GitHub credentials by creating a default OAuth token\. When your pipeline connects to the repository, it uses GitHub credentials to connect to GitHub\. The OAuth token credentials are managed by CodePipeline\. You do not view or manage the token in any way\. The other type of credentials you can use to connect to GitHub are personal access tokens, which are created by you instead of by OAuth apps\. Personal access tokens are managed by you and not by CodePipeline\.

If these permissions have been revoked or otherwise disabled, then the pipeline fails when it cannot use the GitHub token to connect to the repository\.

It is a security best practice to rotate your personal access token on a regular basis\. For more information, see [Use GitHub and the CodePipeline CLI to create and rotate your GitHub personal access token on a regular basis](GitHub-authentication.md#GitHub-rotate-personal-token-CLI)\.

**Possible fixes:** 

 If CodePipeline is unable to connect to the GitHub repository, there are two troubleshooting options: 
+ You might simply need to reconnect your pipeline to the repository manually\. You might have revoked the permissions of the OAuth token for CodePipeline and they just need to be restored\. To do this, see the steps below\.
+ You might need to change your default OAuth token to a personal access token\. The number of OAuth tokens is limited\. For more information, see [the GitHub documentation](https://developer.github.com/v3/oauth/)\. If CodePipeline reaches that limit, older tokens stop working, and actions in pipelines that rely on that token fail\.

1. Check to see if the permissions for CodePipeline have been revoked\. For the steps to check the **Authorized OAuth Apps** list in GitHub, see [View your authorized OAuth apps](GitHub-authentication.md#GitHub-view-oauth-token)\. If you do not see CodePipeline in the list, you must use the console to reconnect your pipeline to GitHub\.

   1. Open your pipeline in the console and choose **Edit**\. On the source stage that contains your GitHub source action, choose **Edit stage**\.

   1. On the GitHub source action, choose the edit icon\.

   1. On the **Edit action** page, choose **Connect to GitHub** to restore the authorization\.   
![\[A pipeline contains stages that contain actions, separated by transitions that can be disabled and enabled.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/button-connect-to-github.png)![\[A pipeline contains stages that contain actions, separated by transitions that can be disabled and enabled.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A pipeline contains stages that contain actions, separated by transitions that can be disabled and enabled.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

      If prompted, you might need to re\-enter the **Repository** and **Branch** for your action\. Choose **Done**\. Choose **Done** on the stage editing page, and then choose **Save** on the pipeline editing page\. Run the pipeline\.

1. If this does not correct the error but you can see CodePipeline in the **Authorized OAuth Apps** list in GitHub, you might have exceeded the allowed number of tokens\. To fix this issue, you can manually configure one OAuth token as a personal access token, and then configure all pipelines in your AWS account to use that token\. For more information, see [Configure your pipeline to use a personal access token \(GitHub and CLI\)](GitHub-create-personal-token-CLI.md)\.

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

## Pipeline error: A deployment with the CodeDeployToECS action returns an error message: "Exception while trying to read the task definition artifact file from: <source artifact name>"<a name="troubleshooting-ecstocodedeploy-size"></a>

**Problem:** 

The maximum artifact ZIP size in the CodePipeline deploy action to ECS through CodeDeploy \(the `CodeDeployToECS` action\) is 3 MB\. The following error message is returned when artifact sizes exceed 3 MB: 

Exception while trying to read the task definition artifact file from: <source artifact name>

**Possible fixes:** Create an artifact with a compressed size less than 3 MB\.

## Need help with a different issue?<a name="troubleshooting-other"></a>

Try these other resources:
+ Contact [AWS Support](https://aws.amazon.com/contact-us/)\.
+ Ask a question in the [CodePipeline forum](https://forums.aws.amazon.com/forum.jspa?forumID=197)\.
+ [Request a quota increase](https://console.aws.amazon.com/support/home#/case/create%3FissueType=service-limit-increase)\. For more information, see [Quotas in AWS CodePipeline](limits.md)\.
**Note**  
It can take up to two weeks to process requests for a quota increase\.