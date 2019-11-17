# Tutorial: Create a Pipeline That Deploys an Amazon Alexa Skill<a name="tutorials-alexa-skills-kit"></a>

In this tutorial, you configure a pipeline that continuously delivers your Alexa skill using the Alexa Skills Kit as the deployment provider in your deployment stage\. The completed pipeline detects changes to your skill when you make a change to the source files in your source repository\. The pipeline then uses the Alexa Skills Kit to deploy to the Alexa skill development stage\.

**Note**  
To create your custom skill as a Lambda function, see [https://developer.amazon.com/docs/custom-skills/host-a-custom-skill-as-an-aws-lambda-function.html](https://developer.amazon.com/docs/custom-skills/host-a-custom-skill-as-an-aws-lambda-function.html)\. You can also create a pipeline that uses Lambda source files and a CodeBuild project to deploy changes to Lambda for your skill\. For example, to create a new skill and related Lambda function, you can create an AWS CodeStar project\. See [Create an Alexa Skill Project in AWS CodeStar](https://docs.aws.amazon.com/codestar/latest/userguide/alexa-tutorial.html)\. For that option, the pipeline includes a third build stage with an CodeBuild action and an action in the Deploy stage for AWS CloudFormation\.

## Prerequisites<a name="tutorials-alexa-skills-kit-prereq"></a>

You must already have the following:
+ A CodeCommit repository\. You can use the AWS CodeCommit repository you created in [Tutorial: Create a Simple Pipeline \(CodeCommit Repository\)](tutorials-simple-codecommit.md)\.
+ An Amazon developer account\. This is the account that owns your Alexa skills\. You can create an account for free at [Alexa Skills Kit](https://developer.amazon.com/alexa-skills-kit)\. 
+ An Alexa skill\. You can create a sample skill using the [Get Custom Skill Sample Code](https://developer.amazon.com/docs/custom-skills/use-the-alexa-skills-kit-samples.html) tutorial\.
+ Install the ASK CLI and configure it using `ask init` with your AWS credentials\. See [https://developer.amazon.com/docs/smapi/quick-start-alexa-skills-kit-command-line-interface.html#install-initialize](https://developer.amazon.com/docs/smapi/quick-start-alexa-skills-kit-command-line-interface.html#install-initialize)\.

## Step 1: Create an Alexa Developer Services LWA Security Profile<a name="tutorials-alexa-skills-kit-profile"></a>

In this section, you create a security profile to use with Login With Amazon \(LWA\)\. If you already have a profile, you can skip this step\.
+ Use the steps in [https://developer.amazon.com/docs/smapi/ask-cli-command-reference.html#generate-lwa-tokens](https://developer.amazon.com/docs/smapi/ask-cli-command-reference.html#generate-lwa-tokens) to create a Security Profile\.
+ After you create the profile, make a note of the **Client ID** and **Client Secret**\.
+ Make sure you enter the **Allowed Return URLs** as provided in the instructions\. The URLs allow the ASK CLI command to redirect refresh token requests\.

## Step 2: Create Alexa Skill Source Files and Push to Your CodeCommit Repository<a name="tutorials-alexa-skills-kit-push"></a>

In this section, you create and push your Alexa skill source files to the repository that the pipeline uses for your source stage\. For the skill you have created in the Amazon developer console, you produce and push the following: 
+ A `skill.json` file\.
+ An `interactionModel/custom` folder\.
**Note**  
This directory structure complies with Alexa Skills Kit skill package format requirements, as outlined in [https://developer.amazon.com/docs/smapi/skill-package-api-reference.html#skill-package-format](https://developer.amazon.com/docs/smapi/skill-package-api-reference.html#skill-package-format)\. If your directory structure does not use the correct skill package format, changes do not successfully deploy to the Alexa Skills Kit console\.

**To create source files for your skill**

1. Retrieve your skill ID from the Alexa Skills Kit developer console\. Use this command:

   ```
   ask api list-skills
   ```

   Locate your skill by name and then copy the associated ID in the `skillId` field\.

1. Generate a `skill.json` file that contains your skill details\. Use this command:

   ```
   ask api get-skill -s skill-ID > skill.json
   ```

1. \(Optional\) Create an `interactionModel/custom` folder\.

   Use this command to generate the interaction model file within the folder\. For locale, this tutorial uses en\-US as the locale in the filename\.

   ```
   ask api get-model --skill-id skill-ID --locale locale >
       ./interactionModel/custom/locale.json
   ```

**To push files to your CodeCommit repository**

1. Push or upload the files to your CodeCommit repository\. These files are the source artifact created by the **Create Pipeline** wizard for your deployment action in AWS CodePipeline\. Your files should look like this in your local directory:

   ```
   skill.json
   /interactionModel
     /custom
       |en-US.json
   ```

1. Choose the method you want to use to upload your files:

   1. To use the Git command line from a cloned repository on your local computer:

      1. Run the following command to stage all of your files at once:

         ```
         git add -A
         ```

      1. Run the following command to commit the files with a commit message:

         ```
         git commit -m "Added Alexa skill files"
         ```

      1. Run the following command to push the files from your local repo to your CodeCommit repository:

         ```
         git push
         ```

   1. To use the CodeCommit console to upload your files: 

      1. Open the CodeCommit console, and choose your repository from the **Repositories** list\.

      1. Choose **Add file**, and then choose **Upload file**\. 

      1. Choose **Choose file**, and then browse for your file\. Commit the change by entering your user name and email address\. Choose **Commit changes**\.

      1. Repeat this step for each file you want to upload\.

## Step 3: Use ASK CLI Commands to Create a Refresh Token<a name="tutorials-alexa-skills-kit-token"></a>

CodePipeline uses a refresh token based on the client ID and secret in your Amazon developer account to authorize actions it performs on your behalf\. In this section, you use the ASK CLI to create the token\. You use these credentials when you use the **Create Pipeline** wizard\.

**To create a refresh token with your Amazon developer account credentials**

1. Use the following command: 

   ```
   ask util generate-lwa-tokens
   ```

1. When prompted, enter your client ID and secret as shown in this example: 

   ```
   ? Please type in the client ID: 
   amzn1.application-client.example112233445566
   ? Please type in the client secret:
   example112233445566
   ```

1. The sign\-in browser page displays\. Sign in with your Amazon developer account credentials\.

1. Return to the command line screen\. The access token and referesh token are generated in the output\. Copy the refresh token returned in the output\.

## Step 4: Create Your Pipeline<a name="tutorials-alexa-skills-kit-pipeline"></a>

In this section, you create a pipeline with the following actions:
+ A source stage with a CodeCommit action where the source artifacts are the Alexa skill files that support your skill\.
+ A deployment stage with an Alexa Skills Kit deploy action\.

**To create a pipeline with the wizard**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. Choose the AWS Region where you want to create the project and its resources\. The Alexa skill runtime is available only in the following regions:
   + Asia Pacific \(Tokyo\)
   + EU \(Ireland\)
   + US East \(N\. Virginia\)
   + US West \(Oregon\)

1. On the **Welcome** page, **Getting started** page, or the **Pipelines** page, choose **Create pipeline**\.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyAlexaPipeline**\.

1. In **Service role**, do one of the following:
   + Choose **New service role** to allow CodePipeline to create a new service role in IAM\. In **Role name**, the role and policy name both default to this format: AWSCodePipelineServiceRole\-*region*\-*pipeline\_name*\. For example, this is the service role created for this tutorial: AWSCodePipelineServiceRole\-eu\-west\-2\-MyAlexaPipeline\.
   + Choose **Existing service role** to use a service role already created in IAM\. In **Role name**, choose your service role from the list\.
**Note**  
Depending on when your service role was created, you might need to update its permissions to support other AWS services\. For information, see [Add Permissions for Other AWS Services](how-to-custom-role.md#how-to-update-role-new-services)\. 

1. In **Artifact store**: 

   1. Choose **Default location** to use the default artifact store, such as the Amazon S3 artifact bucket designated as the default, for your pipeline in the region you have selected for your pipeline\.

   1. Choose **Custom location** if you already have an artifact store, such as an Amazon S3 artifact bucket, in the same region as your pipeline\.
**Note**  
This is not the source bucket for your source code\. This is the artifact store for your pipeline\. A separate artifact store, such as an Amazon S3 bucket, is required for each pipeline\. When you create or edit a pipeline, you must have an artifact bucket in the pipeline Region, and then you must have one artifact bucket per AWS Region where you are running an action\.  
For more information, see [Input and Output Artifacts](welcome-introducing-artifacts.md) and [CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\.

   Choose **Next**\.

1. In **Step 2: Add source stage**, in **Source provider**, choose **AWS CodeCommit**\. In **Repository name**, choose the name of the CodeCommit repository you created in [Step 1: Create a CodeCommit Repository](tutorials-simple-codecommit.md#codecommit-create-repository)\. In **Branch name**, choose the name of the branch that contains your latest code update\. Unless you created a different branch on your own, only `master` is available\. 

   After you select the repository name and branch, a message shows the Amazon CloudWatch Events rule to be created for this pipeline\. 

   Choose **Next**\.

1. In **Step 3: Add build stage**, choose **Skip build stage**, and then accept the warning message by choosing **Skip** again\.

   Choose **Next**\.

1. In **Step 4: Add deploy stage**:

   1. In **Deploy provider**, choose **Alexa Skills Kit**\. 

   1. In **Alexa skill ID**, enter the skill ID assigned to your skill in the Alexa Skills Kit developer console\.

   1. In **Client ID**, enter the ID of the application you registered\.

   1. In **Client secret**, enter the secret you chose when you registered\.

   1. In **Refresh token**, enter the token you generated in step 3\.  
![\[The Step 4: Deploy page for an Alexa Skills Kit action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/alexa-deploy.png)![\[The Step 4: Deploy page for an Alexa Skills Kit action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 4: Deploy page for an Alexa Skills Kit action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   1. Choose **Next**\.

1. In **Step 5: Review**, review the information, and then choose **Create pipeline**\.  
![\[The completed pipeline for an Alexa Skills Kit action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/alexa-deploy-pipeline.png)![\[The completed pipeline for an Alexa Skills Kit action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The completed pipeline for an Alexa Skills Kit action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## Step 5: Make a Change to Any Source File and Verify Deployment<a name="tutorials-alexa-skills-kit-update"></a>

Make a change to your skill and then push the change to your repository\. This triggers your pipeline to run\. Verify that your skill is updated in the [Alexa Skills Kit developer console](https://developer.amazon.com/alexa/console/ask)\.