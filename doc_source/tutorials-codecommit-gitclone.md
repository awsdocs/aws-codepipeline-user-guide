# Tutorial: Use full clone with a CodeCommit pipeline source<a name="tutorials-codecommit-gitclone"></a>

You can choose the full clone option for your CodeCommit source action in CodePipeline\. Use this option to allow CodeBuild to access Git metadata in your pipeline build action\.

In this tutorial, you create a pipeline that accesses your CodeCommit repository, uses the full clone option for source data, and runs a CodeBuild build that clones your repository and performs Git commands for the repository\.

**Note**  
CodeBuild actions are the only downstream actions support use of Git metadata available with the Git clone option\.

**Topics**
+ [Prerequisites](#tutorials-codecommit-gitclone-prereq)
+ [Step 1: Create a README file](#tutorials-codecommit-gitclone-file)
+ [Step 2: Create your pipeline and build project](#tutorials-codecommit-gitclone-pipeline)
+ [Step 3: Update the CodeBuild service role policy to clone the repository](#tutorials-codecommit-gitclone-rolepolicy)
+ [Step 4: View repository commands in build output](#tutorials-codecommit-gitclone-view)

## Prerequisites<a name="tutorials-codecommit-gitclone-prereq"></a>

Before you begin, you must create a CodeCommit repository in the same AWS account and Region as your pipeline\.

## Step 1: Create a README file<a name="tutorials-codecommit-gitclone-file"></a>

Use these steps to add a README file to your source repository\. The README file provides an example source file for the CodeBuild downstream action to read\.

**To add a README file**

1. Log in to your repository and choose your repository\.

1. To create a new file, choose **Add file > Create file**\. Name the file `README.md`\. file and add the following text\.

   ```
   This is a CodeCommit repository!
   ```

1. Choose **Commit changes**\.

   Make sure the `README.md` file is at the root level of your repository\.

## Step 2: Create your pipeline and build project<a name="tutorials-codecommit-gitclone-pipeline"></a>

In this section, you create a pipeline with the following actions:
+ A source stage with a CodeCommit source action\.
+ A build stage with an AWS CodeBuild build action\.

**To create a pipeline with the wizard**

1. Sign in to the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

1. On the **Welcome** page, **Getting started** page, or **Pipelines** page, choose **Create pipeline**\.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyCodeCommitPipeline**\.

1. In **Service role**, do one of the following:
   + Choose **Create service role**\.
   + Choose your existing CodePipeline service role\. This role must have the `codecommit:GetRepository` IAM permission to your service role policy\. See [Add permissions to the the CodePipeline service role](https://docs.aws.amazon.com/codepipeline/latest/userguide/security-iam.html#how-to-update-role-new-services)\.

1. In **Advanced**, leave the defaults\. Choose **Next**\.

1. On the **Step 2: Add source stage** page, do the following:

   1. In **Source provider**, choose **CodeCommit**\.

   1. In **Repository name**, choose the name of your repository\.

   1. In **Branch name**, choose your branch name\.

   1. Choose **Full clone** to enable the Git clone option for the source repository\. 

   Choose **Next**\.

1. In **Add build stage**, do the following:

   1. In **Build provider**, choose **AWS CodeBuild**\. Allow **Region** to default to the pipeline Region\.

   1. Choose **Create project**\.

   1. In **Project name**, enter a name for this build project\.

   1. In **Environment image**, choose **Managed image**\. For **Operating system**, choose **Ubuntu**\.

   1. For **Runtime**, choose **Standard**\. For **Image**, choose **aws/codebuild/standard:4\.0**\.

   1. For **Service role**, choose **New service role**\.
**Note**  
Note the name of your CodeBuild service role\. You will need the role name for the final step in this tutorial\.

   1. Under **Buildspec**, for **Build specifications**, choose **Insert build commands**\. Choose **Switch to editor**, and then under **Build commands** paste the following code\.

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
            - git describe --all
        #post_build:
          #commands:
            # - command
            # - command
      #artifacts:
        #files:
          # - location
        #name: $(date +%Y-%m-%d)
        #discard-paths: yes
        #base-directory: location
      #cache:
        #paths:
          # - paths
      ```

   1. Choose **Continue to CodePipeline**\. This returns you to the CodePipeline console and creates a CodeBuild project that uses your build commands for configuration\. The build project uses a service role to manage AWS service permissions\. This step might take a couple of minutes\.

   1. Choose **Next**\.

1. On the **Step 4: Add deploy stage** page, choose **Skip deploy stage**, and then accept the warning message by choosing **Skip** again\. Choose **Next**\.

1. On **Step 5: Review**, choose **Create pipeline**\.

## Step 3: Update the CodeBuild service role policy to clone the repository<a name="tutorials-codecommit-gitclone-rolepolicy"></a>

The initial pipeline run will fail because you need to update the CodeBuild service role with permissions to pull from your repository\.

Add the `codecommit:GitPull` IAM permission to your service role policy\. For instructions to update the policy in the IAM console, see [Add CodeBuild GitClone permissions for CodeCommit source actions](troubleshooting.md#codebuild-role-codecommitclone)\.

## Step 4: View repository commands in build output<a name="tutorials-codecommit-gitclone-view"></a>

**To view the build output**

1. When your service role is successfully updated, choose **Retry** on the failed CodeBuild stage\.

1. After the pipeline runs successfully, on your successful build stage, choose **Details**\.

   Under **Execution details**, view the CodeBuild build output\. The commands output the `README.md` file contents, list the files in the directory, clone the repository, view the log, and run `git describe --all`\. 