# Tutorial: Create a Simple Pipeline \(Amazon S3 Bucket\)<a name="tutorials-simple-s3"></a>

The easiest way to create a pipeline is to use the **Create pipeline** wizard in the AWS CodePipeline console\. 

In this walkthrough, you create a two\-stage pipeline that uses a versioned Amazon S3 bucket and AWS CodeDeploy to release a sample application\. 

**Note**  
For pipelines with an Amazon S3 source, an Amazon CloudWatch Events rule detects source changes and then starts your pipeline when changes occur\. When you use the console to create or change a pipeline, the rule and all associated resources are created for you\. If you create or change an Amazon S3 pipeline in the CLI or AWS CloudFormation, you must create the Amazon CloudWatch Events rule, IAM role, and AWS CloudTrail trail manually\.

After you create this simple pipeline, you add an additional stage and then disable and enable the transition between stages\.

**Important**  
In addition to completing the steps in [Getting Started with AWS CodePipeline](getting-started-codepipeline.md), you should create an Amazon EC2 instance key pair to connect to the Amazon EC2 instances after they are launched\. To create an Amazon EC2 instance key pair, follow the instructions in [Creating Your Key Pair Using Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)\. 

Not what you're looking for? To create a simple pipeline using an AWS CodeCommit branch as a code repository, see [Tutorial: Create a Simple Pipeline \(AWS CodeCommit Repository\)](tutorials-simple-codecommit.md)\.

Before you begin, you should complete the prerequisites in [Getting Started with AWS CodePipeline](getting-started-codepipeline.md)\.

**Topics**
+ [Step 1: Create an Amazon S3 Bucket for Your Application](#s3-create-s3-bucket)
+ [Step 2: Create AWS CodeDeploy Resources to Deploy the Sample Application](#s3-create-deployment)
+ [Step 3: Create Your First Pipeline in AWS CodePipeline](#s3-create-pipeline)
+ [Step 4: Add Another Stage to Your Pipeline](#s3-add-stage)
+ [Step 5: Disable and Enable Transitions Between Stages in AWS CodePipeline](#s3-configure-transitions)
+ [Step 6: Clean Up Resources](#s3-clean-up)

## Step 1: Create an Amazon S3 Bucket for Your Application<a name="s3-create-s3-bucket"></a>

You can store your source files or applications in any versioned location\. In this walkthrough, you create an Amazon S3 bucket for the sample applications and enable versioning on that bucket\. After you have enabled versioning, you copy the sample applications to that bucket\. 

If you want to use an existing Amazon S3 bucket, see [Enable Versioning for a Bucket](http://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html), copy the sample applications to that bucket, and skip ahead to [Step 2: Create AWS CodeDeploy Resources to Deploy the Sample Application](#s3-create-deployment)\. 

If you want to use a GitHub repository instead of an Amazon S3 bucket, copy the sample applications to that repository, and skip ahead to [Step 2: Create AWS CodeDeploy Resources to Deploy the Sample Application](#s3-create-deployment)\.

**To create an Amazon S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\.

1. In **Bucket name**, type a name for your bucket, such as **awscodepipeline\-demobucket\-example\-date**\.
**Note**  
Because all bucket names in Amazon S3 must be unique, use one of your own, not the name shown in the example\. You can change the example name just by adding the date to it\. Make a note of this name because you need it for the rest of this walkthrough\.

   In the **Region** drop\-down box, choose the region where you intend to create your pipeline, such as **US West \(Oregon\)**, and then choose **Next**\.

1. Choose **Versioning**, select **Enable versioning**, and then choose **Save**\.

   When versioning is enabled, Amazon S3 saves every version of every object in the bucket\.

1. Choose **Next**\. Accept the default permissions to allow your account read/write access on objects\. For more information about Amazon S3 bucket and object permissions, see [Specifying Permissions in a Policy](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)\.

1. Choose **Next**, and then choose **Create bucket**\.

1. Next, you download a sample from a GitHub repository and save it into a folder or directory on your local computer\.
**Important**  
Do not use the **Clone or download** or **Download ZIP** buttons in the GitHub repositories\. This creates a nested folder structure that does not work with AWS CodeDeploy\.

   1. Open the GitHub repository that hosts the sample\.
      + If you want to deploy to Amazon Linux instances using AWS CodeDeploy: [https://github.com/awslabs/aws-codepipeline-s3-aws-codedeploy_linux](https://github.com/awslabs/aws-codepipeline-s3-aws-codedeploy_linux)
      + If you want to deploy to Windows Server instances using AWS CodeDeploy: [https://github\.com/awslabs/AWSCodePipeline\-S3\-AWSCodeDeploy\_Windows](https://github.com/awslabs/AWSCodePipeline-S3-AWSCodeDeploy_Windows)

   1. Choose the **dist** folder\.

   1. Choose the file name\.
      + If you want to deploy to Amazon Linux instances: `aws-codepipeline-s3-aws-codedeploy_linux.zip`
      + If you want to deploy to Windows Server instances: `AWSCodePipeline-S3-AWSCodeDeploy_Windows.zip`

   1. Choose **View Raw**, and then save the sample file to your local computer\.

1. In the Amazon S3 console for your bucket, choose **Upload**, and follow the instructions to upload your \.zip files into the bucket\.

## Step 2: Create AWS CodeDeploy Resources to Deploy the Sample Application<a name="s3-create-deployment"></a>

You can use Elastic Beanstalk or AWS CodeDeploy to deploy your code for staging or into production\. In this walkthrough, you use the **Sample deployment** wizard in AWS CodeDeploy to create your deployment resources\.

**To create an AWS CodeDeploy automated deployment**

1. Open the AWS CodeDeploy console at [https://console\.aws\.amazon\.com/codedeploy](https://console.aws.amazon.com/codedeploy) in the region where you intend to create your pipeline\. 

   For more information about the regions and endpoints available for AWS CodePipeline, see [Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html#codepipeline_region)\.

   If you see the **Applications** page instead of the **Welcome** page, in the **More info** section, choose **Sample deployment wizard**\.

1. On the **Get started with AWS CodeDeploy** page, choose **Sample deployment**, and then choose **Next**\.

1. On the **Choose a deployment type** page, choose **In\-place deployment**, and then choose **Next**\. 

1. On the **Configure instances** page, do the following:

   1. Choose the operating system and Amazon EC2 instance key pair you want to use\. Your choice of operating system should match the sample application you downloaded from GitHub\.
**Important**  
If you downloaded `aws-codepipeline-s3-aws-codedeploy_linux.zip`, choose **Amazon Linux**\.   
If you downloaded `AWSCodePipeline-S3-AWSCodeDeploy_Windows.zip`, choose **Windows Server**\.

   1. From the **Key pair name** drop\-down list, choose the name of the Amazon EC2 instance key pair you intend to use to connect to the Amazon EC2 instances after they are launched\.

   1. In **Tag key and value**, leave the **Key** name unchanged\. In **Value**, type **CodePipelineDemo**\.

   1. Choose **Launch instances**\.  
![\[The Configure instances controls in the AWS CodeDeploy Sample deployment wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-codedeploy-config.png)![\[The Configure instances controls in the AWS CodeDeploy Sample deployment wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Configure instances controls in the AWS CodeDeploy Sample deployment wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   For more information about each of these choices, see [Step 3: Configure instances](http://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-wizard-in-place.html#getting-started-wizard-in-place-3-instances) in the *AWS CodeDeploy User Guide*\.

1. After your instances have been created, choose **Next**\.

1. On the **Name your application ** page, in **Application name**, type **CodePipelineDemoApplication**, and then choose **Next**\.

1. On the **Select a revision** page, choose **Next**\. 

1. On the **Create a deployment group** page, in **Deployment group name**, type **CodePipelineDemoFleet**, and then choose **Next**\.
**Note**  
The instances created for you in **Step 3: Configure instances** are listed in the **Add instances** area\.

1. On the **Select a service role** page, from the **Service role** drop\-down box, choose **Use an existing service role**\. In the **Role name** list, choose the service role you want to use, and then choose **Next**\.
**Note**  
If no service roles appear in **Role name**, or if you do not have a service role you want to use, choose **Create a service role** or follow the steps in [Create a Service Role](http://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-service-role.html)\.  
The service role you create or use for AWS CodeDeploy is different from the service role you create for AWS CodePipeline\.

1. On the **Choose a deployment configuration** page, choose **Next**\.

1. On the **Review deployment details** page, choose **Deploy**\. The sample application for AWS CodeDeploy is deployed to each of the Amazon EC2 instances\. 

1. After the sample application is successfully deployed, verify the deployment in your web browser by going to `http://PublicDNS` for each Amazon EC2 instance in the deployment\. To get the public DNS value for an Amazon EC2 instance, in the Amazon EC2 console, select the instance, and look for the **Public DNS** value on the **Description** tab\.

    The webpage displays links to the AWS CodeDeploy documentation\. 
**Note**  
This webpage is not the one you deploy and release with the pipeline you create in the next section\.

For more information about AWS CodeDeploy, see [Getting Started with AWS CodeDeploy](http://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started.html)\.

## Step 3: Create Your First Pipeline in AWS CodePipeline<a name="s3-create-pipeline"></a>

In this part of the walkthrough, you create the pipeline\. The sample runs automatically through the pipeline\.

**To create an AWS CodePipeline automated release process**

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codepipeline](http://console.aws.amazon.com/codepipeline)\.

1. On the introductory page, choose **Get started**\.

   If you see the **Welcome** page, choose **Create pipeline**\.

1. In **Step 1: Name**, in **Pipeline name**, type **MyFirstPipeline**, and then choose **Next step**\.
**Note**  
If you choose another name for your pipeline, be sure to use that name instead of *MyFirstPipeline* for the rest of this walkthrough\. After you create a pipeline, you cannot change its name\. Pipeline names are subject to some limitations\. For more information, see [Limits in AWS CodePipeline](limits.md)\. 

1. In **Step 2: Source**, in **Source provider**, choose **Amazon S3**\. In **Amazon S3 location**, type the name of the Amazon S3 bucket you created in [Step 1: Create an Amazon S3 Bucket for Your Application](#s3-create-s3-bucket) and the sample file you copied to that bucket, either `aws-codepipeline-s3-aws-codedeploy_linux.zip` or `AWSCodePipeline-S3-AWSCodeDeploy_Windows.zip`\. Choose **Next step**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-wizard-source.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   For example, if you named your bucket **awscodepipeline\-demobucket\-example\-date** and you chose Amazon Linux for your Amazon EC2 instances in AWS CodeDeploy, you would type:

   ```
   s3://awscodepipeline-demobucket-example-date/aws-codepipeline-s3-aws-codedeploy_linux.zip
   ```

   If you named your bucket **awscodepipeline\-demobucket\-example\-date** and you chose Windows for your Amazon EC2 instances in AWS CodeDeploy, you would type:

   ```
   s3://awscodepipeline-demobucket-example-date/AWSCodePipeline-S3-AWSCodeDeploy_Windows.zip
   ```
**Note**  
If you copied the sample application to a GitHub repository instead of an Amazon S3 bucket, choose **GitHub** from the list of source providers, and then follow the instructions\. For more information, see [Create a Pipeline \(Console\)](pipelines-create.md#pipelines-create-console)\.

   After you select the source provider, a message displays the Amazon CloudWatch Events rule to be created for this pipeline\. The message also displays the data event to be added to your AWS CloudTrail trail in the following format: `my-bucket/path/to/object.zip`\.

   Under **Change detection options**, leave the defaults\. This allows AWS CodePipeline to use Amazon CloudWatch Events to detect changes in your source bucket\.

1. In **Step 3: Build**, choose **No Build**, and then choose **Next step**\.
**Note**  
You can configure a build action with a provider such as AWS CodeBuild, which is a fully managed build service in the cloud\. You can also configure a build action that uses a provider with a build server or system, such as Jenkins\. You can walk through the steps for setting up build resources and creating a pipeline that uses those resources in the next tutorial, [Tutorial: Create a Four\-Stage Pipeline](tutorials-four-stage-pipeline.md)\.

1. In **Step 4: Deploy**, in **Deployment provider**, choose **AWS CodeDeploy**\. In **Application name**, type **CodePipelineDemoApplication**, or choose the **Refresh** button, and then choose the application name from the list\. In **Deployment group**, type **CodePipelineDemoFleet**, or choose it from the list, and then choose **Next step**\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-wizard-deploy.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
**Note**  
The name "Staging" is the name given by default to the stage created in the **Step 4: Deploy** step, just as "Source" is the name given to the first stage of the pipeline\. 

1. In **Step 5: Service Role**, choose **Create role**\. 

   On the IAM console page that describes the AWS\-CodePipeline\-Service role to be created for you, choose **Allow**\.

   On the **Step 5: Service Role** page where AWS\-CodePipeline\-Service appears in **Role name**, choose **Next step**\.
**Note**  
Service role creation is only required the first time you create a pipeline in AWS CodePipeline\. If a service role has already been created, you can choose it from the drop\-down list of roles\. The drop\-down list displays all IAM service roles associated with your account\. If you choose a name different from the default, be sure that the name is recognizable as the service role for AWS CodePipeline\. 

1. In **Step 6: Review**, review the information, and then choose **Create pipeline**\.

1. The pipeline automatically starts to run\. You can view progress and success and failure messages as the AWS CodePipeline sample deploys a webpage to each of the Amazon EC2 instances in the AWS CodeDeploy deployment\.

Congratulations\! You just created a simple pipeline in AWS CodePipeline\. The pipeline has two stages:
+ A source stage named **Source**, which automatically detects changes in the versioned sample application stored in the Amazon S3 bucket and pulls those changes into the pipeline\.
+ A **Staging** stage that deploys those changes to Amazon EC2 instances with AWS CodeDeploy\. 

Now, verify the results\.

**To verify your pipeline ran successfully**

1. View the initial progress of the pipeline\. The status of each stage changes from **No executions yet** to **In Progress**, and then to either **Succeeded** or **Failed**\. The pipeline should complete the first run within a few minutes\.

1. After **Succeeded** is displayed for the action status, in the status area for the **Staging** stage, choose **Details**\. 

1. In the **Deployment details** section, in **Instance ID**, choose the instance ID of one of the successfully deployed instances\. 

1. On the **Description** tab, in **Public DNS**, copy the address, and then paste it into the address bar of your web browser\.

   The following page is the sample application you uploaded to your Amazon S3 bucket\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-demo-success-message.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

For more information about stages, actions, and how pipelines work, see [AWS CodePipeline Concepts](concepts.md)\.

## Step 4: Add Another Stage to Your Pipeline<a name="s3-add-stage"></a>

Now add another stage in the pipeline to deploy from staging servers to production servers using AWS CodeDeploy\. First, you create another deployment group in the CodePipelineDemoApplication in AWS CodeDeploy\. Then you add a stage that includes an action that uses this deployment group\. To add another stage, you use the AWS CodePipeline console or the AWS CLI to retrieve and manually edit the structure of the pipeline in a JSON file, and then run the update\-pipeline command to update the pipeline with your changes\.

**Topics**
+ [Create a Second Deployment Group in AWS CodeDeploy](#s3-add-stage-part-1)
+ [Add the Deployment Group as Another Stage in Your Pipeline](#s3-add-stage-part-2)

### Create a Second Deployment Group in AWS CodeDeploy<a name="s3-add-stage-part-1"></a>

**Note**  
In this part of the walkthrough, you create a second deployment group, but deploy to the same Amazon EC2 instances as before\. This is for demonstration purposes only\. It is purposely designed to fail in order to show you how errors are displayed in AWS CodePipeline\.

**To create a second deployment group in AWS CodeDeploy**

1. Open the AWS CodeDeploy console at [https://console\.aws\.amazon\.com/codedeploy](https://console.aws.amazon.com/codedeploy)\.

1. Choose **Applications**, and in the list of applications, choose **CodePipelineDemoApplication**\.

1. In **Deployment groups**, choose **Create deployment group**\.

1. On the **Create deployment group** page, in **Deployment group name**, type a name for the second deployment group, such as **CodePipelineProductionFleet**\.

1. Choose **In\-place deployment**\.

1. In **Key**, enter **Name**, but in **Value**, choose **CodePipelineDemo** from the list\. Leave the default configuration for **Deployment configuration**\. In **Service role ARN**, choose the same AWS CodeDeploy service role you used for the initial deployment \(not the AWS CodePipeline service role\), and then choose **Create deployment group**\.

### Add the Deployment Group as Another Stage in Your Pipeline<a name="s3-add-stage-part-2"></a>

Now that you have another deployment group, you can add a stage that uses this deployment group to deploy to the same Amazon EC2 instances you used earlier\. You can use the AWS CodePipeline console or the AWS CLI to add this stage\. 

**Topics**
+ [Create a Third Stage \(Console\)](#s3-add-stage-part-2-console)
+ [Create a Third Stage \(CLI\)](#s3-add-stage-part-2-cli)

#### Create a Third Stage \(Console\)<a name="s3-add-stage-part-2-console"></a>

You can use the AWS CodePipeline console to add a new stage that uses the new deployment group\. Because this deployment group is deploying to the Amazon EC2 instances you've already used, the deploy action in this stage fails\. 

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codepipeline](http://console.aws.amazon.com/codepipeline)\.

1. In **Name**, choose the name of the pipeline you created, MyFirstPipeline\. 

1. On the pipeline details page, choose **Edit**\. 

1. On the **Edit** page, choose **\+ Stage** to add a stage immediately after the Staging stage\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/edit-pipeline-console.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. In the name field for the new stage, type **Production**, and then choose **\+ Action**:

   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/add_action.png)

1. In the **Action category** drop\-down list, choose **Deploy**\. 

   In **Action name**, type ****Deploy\-Second\-Deployment****\. 

   In **Deployment provider**, choose **AWS CodeDeploy** from the drop\-down list\. 

   In the AWS CodeDeploy section, in **Application name**, choose **CodePipelineDemoApplication**from the drop\-down list, as you did when you created the pipeline\. In **Deployment group**, choose the deployment group you just created, **CodePipelineProductionFleet**\. In the **Input artifacts** section, type **MyApp** in **Input artifacts \#1**, and then choose **Add action**\. 
**Note**  
The name of the input artifact, **MyApp**, was created automatically for you in the **Create pipeline** wizard as the output artifact of the source action\. Every action has an input artifact \(the artifact the action works on\), an output artifact \(the product or result of the action\), or both, depending on the action type\. In this example, the deploy action inputs the output of the source action in the source stage, `MyApp`, and deploys it\. Because the action configured for the previous stage \(Staging\) has already deployed the application to the same Amazon EC2 instances, this action fails\. For more information about input and output artifacts and the structure of pipelines, see [AWS CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\.

1. On the **Edit** page, choose **Save pipeline changes**\. In the **Save pipeline changes** dialog box, choose **Save and continue**\.

1. Although the new stage has been added to your pipeline, a status of **No executions yet** is displayed because no changes have triggered another run of the pipeline\. You have to manually rerun the last revision to see how the edited pipeline runs\. On the pipeline details page, choose **Release change**, and then choose **Release** when prompted\. This runs the most recent revision available in each source location specified in a source action through the pipeline\. 

   Alternatively, to use the AWS CLI to rerun the pipeline, from a terminal on your local Linux, macOS, or Unix machine, or a command prompt on your local Windows machine, run the start\-pipeline\-execution command, specifying the name of the pipeline\. This runs the application in your source bucket through the pipeline for a second time\.

   ```
   aws codepipeline start-pipeline-execution --name MyFirstPipeline
   ```

   This command returns a `pipelineExecutionId` object\.

1. Return to the AWS CodePipeline console and in the list of pipelines, choose **MyFirstPipeline** to open the view page\.

   The pipeline shows three stages and the state of the artifact running through those three stages\. It might take up to five minutes for the pipeline to run through all stages\. You'll see the deployment succeeds on the first two stages, just as before, but the **Production** stage shows the **Deploy\-Second\-Deployment** action failed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-failed-third-stage.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. In the **Deploy\-Second\-Deployment** action, choose **Details**\. In the **Action execution failed **dialog box, choose **Link to execution details**\. You are redirected to the details page for the AWS CodeDeploy deployment\. In this case, the failure is the result of the first instance group deploying to all of the Amazon EC2 instances, leaving no instances for the second deployment group\.
**Note**  
This failure is by design, to demonstrate what happens when there is a failure in a pipeline stage\.

#### Create a Third Stage \(CLI\)<a name="s3-add-stage-part-2-cli"></a>

Although using the AWS CLI to add a stage to your pipeline is more complex than using the console, it provides more visibility into the structure of the pipeline\.

**To create a third stage for your pipeline**

1. Open a terminal session on your local Linux, macOS, or Unix machine, or a command prompt on your local Windows machine, and run the get\-pipeline command to display the structure of the pipeline you just created\. For **MyFirstPipeline**, you would type the following command: 

   ```
   aws codepipeline get-pipeline --name "MyFirstPipeline"
   ```

   This command returns the structure of MyFirstPipeline\. The first part of the output should look similar to the following:

   ```
   {
       "pipeline": {
           "roleArn": "arn:aws:iam::80398EXAMPLE:role/AWS-CodePipeline-Service",
           "stages": [
       ...
   ```

   The final part of the output includes the pipeline metadata and should look similar to the following:

   ```
       ...
           ],
           "artifactStore": {
               "type": "S3"
               "location": "codepipeline-us-east-2-250656481468",
           },
           "name": "MyFirstPipeline",
           "version": 4
       },
       "metadata": {
           "pipelineArn": "arn:aws:codepipeline:us-east-2:80398EXAMPLE:MyFirstPipeline",
           "updated": 1501626591.112,
           "created": 1501626591.112
       }
   }
   ```

1. Copy and paste this structure into a plain\-text editor, and save the file as **pipeline\.json**\. For convenience, save this file in the same directory where you run the aws codepipeline commands\.
**Note**  
You can pipe the JSON directly into a file with the get\-pipeline command as follows:  

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

1. Copy the **Staging** stage section and paste it after the first two stages\. Because it is a deploy stage, just like the **Staging** stage, you use it as a template for the third stage\. 

1. Change the name of the stage and the deployment group details, and then save the file\. 

   The following example shows the JSON you add to the pipeline\.json file after the **Staging** stage\. Edit the emphasized elements with new values\. Remember to include a comma to separate the **Staging** and **Production** stage definitions\.

   ```
   ,
   {
       "name": "Production",
        "actions": [
           {
            "inputArtifacts": [
                {
                 "name": "MyApp"
                }
              ],
             "name": "Deploy-Second-Deployment",
             "actionTypeId": {
                 "category": "Deploy",
                 "owner": "AWS",
                 "version": "1",
                 "provider": "CodeDeploy"
                 },
            "outputArtifacts": [],
            "configuration": {
                 "ApplicationName": "CodePipelineDemoApplication",
                 "DeploymentGroupName": "CodePipelineProductionFleet"
                  },
            "runOrder": 1
           }
       ]
   }
   ```

1. Run the update\-pipeline command, specifying the pipeline JSON file, similar to the following:

   ```
   aws codepipeline update-pipeline --cli-input-json file://pipeline.json
   ```

   This command returns the entire structure of the updated pipeline\.
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

1.  Run the start\-pipeline\-execution command, specifying the name of the pipeline\. This runs the application in your source bucket through the pipeline for a second time\.

   ```
   aws codepipeline start-pipeline-execution --name MyFirstPipeline
   ```

   This command returns a `pipelineExecutionId` object\.

1. Open the AWS CodePipeline console and choose **MyFirstPipeline** from the list of pipelines\.

   The pipeline shows three stages and the state of the artifact running through those three stages\. It might take up to five minutes for the pipeline to run through all stages\. Although the deployment succeeds on the first two stages, just as before, the **Production** stage shows that the **Deploy\-Second\-Deployment** action failed\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-failed-third-stage.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. In the **Deploy\-Second\-Deployment** action, choose **Details** to see details of the failure\. You are redirected to the details page for the AWS CodeDeploy deployment\. In this case, the failure is the result of the first instance group deploying to all of the Amazon EC2 instances, leaving no instances for the second deployment group\. 
**Note**  
This failure is by design, to demonstrate what happens when there is a failure in a pipeline stage\.

## Step 5: Disable and Enable Transitions Between Stages in AWS CodePipeline<a name="s3-configure-transitions"></a>

You can enable or disable the transition between stages in a pipeline\. Disabling the transition between stages allows you to manually control transitions between one stage and another\. For example, you might want to run the first two stages of a pipeline, but disable transitions to the third stage until you are ready to deploy to production, or while you troubleshoot a problem or failure with that stage\.

**To disable and enable transitions between stages in an AWS CodePipeline pipeline**

1. Open the AWS CodePipeline console and choose **MyFirstPipeline** from the list of pipelines\.

1. On the details page for the pipeline, choose the arrow that indicates the transition between the second stage, **Staging**, and the third stage that you added in the previous section, **Production**\.

1. In the **Disable transition** dialog box, type a reason for disabling the transition between the stages, and then choose **Disable**\.

   The arrow between stages displays an icon and color change indicating that the transition has been disabled\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-disabled-transition.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Upload your sample again to the Amazon S3 bucket\. Because the bucket is versioned, this change starts the pipeline\. For information, see [](#getting-started-upload-s3)\.

1. Return to the details page for your pipeline and watch the status of the stages\. The pipeline view changes to show progress and success on the first two stages, but no changes occur on the third stage\. This process might take a few minutes\.

1. Enable the transition by choosing the arrow that indicates the transition has been disabled between the two stages\. In the **Enable transition** dialog box, choose **Enable**\. The stage starts running in a few minutes and attempts to process the artifact that has already been run through the first two stages of the pipeline\.
**Note**  
If you want this third stage to succeed, edit the CodePipelineProductionFleet deployment group before you enable the transition, and specify a different set of Amazon EC2 instances where the application is deployed\. For more information about how to do this, see [Change Deployment Group Settings](http://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-change-deployment-group-settings.html)\. If you create more Amazon EC2 instances, you may incur additional costs\. 

## Step 6: Clean Up Resources<a name="s3-clean-up"></a>

You can use some of the resources you created in this walkthrough for the tutorial, [Tutorial: Create a Four\-Stage Pipeline](tutorials-four-stage-pipeline.md)\. For example, you can reuse the AWS CodeDeploy application and deployment\. However, after you complete this and any other tutorials, you should delete the pipeline and the resources it uses, so that you are not be charged for the continued use of those resources\. First, delete the pipeline, then the AWS CodeDeploy application and its associated Amazon EC2 instances, and finally, the Amazon S3 bucket\.

**To clean up the resources used in this tutorial**

1. To clean up your AWS CodePipeline resources, follow the instructions in [Delete a Pipeline in AWS CodePipeline](pipelines-delete.md)\.

1. To clean up your AWS CodeDeploy resources, follow the instructions in [Clean Up Deployment Walkthrough Resources](http://docs.aws.amazon.com/codedeploy/latest/userguide/tutorials-simple-s3alkthrough.html#tutorials-simple-s3alkthrough-clean-up)\.

1. To delete the Amazon S3 bucket, follow the instructions in [Deleting or Emptying an Amazon S3 Bucket](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/DeletingaBucket.html)\. If you do not intend to create more pipelines, delete the Amazon S3 bucket created for storing your pipeline artifacts\. For more information about this bucket, see [AWS CodePipeline Concepts](concepts.md)\.