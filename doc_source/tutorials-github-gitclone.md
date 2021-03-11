# Tutorial: Use full clone with a GitHub pipeline source<a name="tutorials-github-gitclone"></a>

You can choose the full clone option for your GitHub source action in CodePipeline\. Use this option to run CodeBuild commands for Git metadata in your pipeline build action\.

In this tutorial, you will create a pipeline that connects to your GitHub repository, uses the full clone option for source data, and run a CodeBuild build that clones your repository and performs Git commands for the repository\.

**Note**  
This feature is not available in the Asia Pacific \(Hong Kong\) or Europe \(Milan\) Region\. To use other source actions available in that Region, see [Source action integrations](integrations-action-type.md#integrations-source)\.

**Topics**
+ [Prerequisites](#tutorials-github-gitclone-prereq)
+ [Step 1: Create a README file](#tutorials-github-gitclone-file)
+ [Step 2: Create your pipeline and build project](#tutorials-github-gitclone-pipeline)
+ [Step 3: Update the CodeBuild service role policy to use connections](#tutorials-github-gitclone-rolepolicy)
+ [Step 4: View repository commands in build output](#tutorials-github-gitclone-view)

## Prerequisites<a name="tutorials-github-gitclone-prereq"></a>

Before you begin, you must do the following:
+ Create a GitHub repository with your GitHub account\.
+ Have your GitHub credentials ready\. When you use the AWS Management Console to set up a connection, you are asked to sign in with your GitHub credentials\. 

## Step 1: Create a README file<a name="tutorials-github-gitclone-file"></a>

After you create your GitHub repository, use these steps to add a README file\.

1. Log in to your GitHub repository and choose your repository\.

1. To create a new file, choose **Add file > Create new file**\. Name the file `README.md`\. file and add the following text\.

   ```
   This is a GitHub repository!
   ```

1. Choose **Commit changes**\.

   Make sure the `README.md` file is at the root level of your repository\.

## Step 2: Create your pipeline and build project<a name="tutorials-github-gitclone-pipeline"></a>

In this section, you create a pipeline with the following actions:
+ A source stage with a connection to your GitHub repository and action\.
+ A build stage with an AWS CodeBuild build action\.

**To create a pipeline with the wizard**

1. Sign in to the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

1. On the **Welcome** page, **Getting started** page, or **Pipelines** page, choose **Create pipeline**\.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyGitHubPipeline**\.

1. In **Service role**, choose **Create service role**\.
**Note**  
If you choose instead to use your existing CodePipeline service role, make sure that you have added the `codestar-connections:UseConnection` IAM permission to your service role policy\. For instructions for the CodePipeline service role, see [Add permissions to the the CodePipeline service role](https://docs.aws.amazon.com/codepipeline/latest/userguide/security-iam.html#how-to-update-role-new-services)\.

1. In **Artifact store**, choose **Default location** to use the default artifact store
**Note**  
This is not the source bucket for your source code\. This is the artifact store for your pipeline\. A separate artifact store, such as an S3 bucket, is required for each pipeline\.

   Choose **Next**\.

1. On the **Step 2: Add source stage** page, add a source stage:

   1. In **Source provider**, choose **GitHub**\.

   1. Under **Connection**, choose an existing connection or create a new one\. To create or manage a connection for your GitHub source action, see [GitHub connections](connections-github.md)\.

   1. In **Repository name**, choose the name of your GitHub repository\.

   1. In **Branch name**, choose the repository branch you want to use\.

   1. Choose **Full clone** to enable the git clone option for the source repository\. Only actions provided by CodeBuild can use the git clone option\. You will use [Step 3: Update the CodeBuild service role policy to use connections](#tutorials-github-gitclone-rolepolicy) in this tutorial to update the permissions for your CodeBuild project service role to use this option\.

   Choose **Next**\.

1. In **Add build stage**, add a build stage:

   1. In **Build provider**, choose **AWS CodeBuild**\. Allow **Region** to default to the pipeline Region\.

   1. Choose **Create project**\.

   1. In **Project name**, enter a name for this build project\.

   1. In **Environment image**, choose **Managed image**\. For **Operating system**, choose **Ubuntu**\.

   1. For **Runtime**, choose **Standard**\. For **Image**, choose **aws/codebuild/standard:4\.0**\.

   1. For **Service role**, choose **New service role**\.
**Note**  
Note the name of your CodeBuild service role\. You will need the role name for the final step in this tutorial\.

   1. Under **Buildspec**, for **Build specifications**, choose **Insert build commands**\. Choose **Switch to editor**, and paste the following under **Build commands**\.
**Note**  
In the `env` section of the build spec, make sure the credential helper for git commands is enabled as shown in this example\.

      ```
      version: 0.2
      
      env:
        git-credential-helper: yes
      phases:
        install:
          #If you use the Ubuntu standard image 2.0 or later, you must specify runtime-versions.
          #If you specify runtime-versions and use an image other than Ubuntu standard image 2.0, the build fails.
          runtime-versions:
            nodejs: 10
            # name: version
          #commands:
            # - command
            # - command
        pre_build:
          commands:
            - ls -lt
            - cat README.md
        build:
          commands:
            - git log | head -100
            - git status
            - ls
            - git archive --format=zip HEAD > application.zip
        #post_build:
          #commands:
            # - command
            # - command
      artifacts:
        files:
          - application.zip
          # - location
        #name: $(date +%Y-%m-%d)
        #discard-paths: yes
        #base-directory: location
      #cache:
        #paths:
          # - paths
      ```

   1. Choose **Continue to CodePipeline**\. This returns to the CodePipeline console and creates a CodeBuild project that uses your build commands for configuration\. The build project uses a service role to manage AWS service permissions\. This step might take a couple of minutes\.

   1. Choose **Next**\.

1. On the **Step 4: Add deploy stage** page, choose **Skip deploy stage**, and then accept the warning message by choosing **Skip** again\. Choose **Next**\.

1. On **Step 5: Review**, choose **Create pipeline**\.

## Step 3: Update the CodeBuild service role policy to use connections<a name="tutorials-github-gitclone-rolepolicy"></a>

The initial pipeline run will fail because the CodeBuild service role must be updated with permissions to use connections\. Add the `codestar-connections:UseConnection` IAM permission to your service role policy\. For instructions to update the policy in the IAM console, see [Add CodeBuild GitClone permissions for connections to Bitbucket, GitHub, or GitHub Enterprise Server](troubleshooting.md#codebuild-role-connections)\.

## Step 4: View repository commands in build output<a name="tutorials-github-gitclone-view"></a>

1. When your service role is successfully updated, choose **Retry** on the failed CodeBuild stage\.

1. After the pipeline runs successfully, on your successful build stage, choose **Details**\.

   Under **Execution details**, view the CodeBuild build output\. The commands output the `README.md` file contents, list the files in the directory, clone the repository, view the log, and archive the repository as a ZIP file\.