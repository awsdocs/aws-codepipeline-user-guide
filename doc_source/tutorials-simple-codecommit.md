# Tutorial: Create a Simple Pipeline \(CodeCommit Repository\)<a name="tutorials-simple-codecommit"></a>

The easiest way to get started with AWS CodePipeline is to use the **Create Pipeline** wizard in the CodePipeline console to create a simple pipeline\. 

In this tutorial, you use CodePipeline to deploy code that is maintained in a CodeCommit repository to a single Amazon EC2 instance\. You will use CodeDeploy as the deployment service\.

**Important**  
Many of the actions you add to your pipeline in this procedure involve AWS resources that you need to create before you create the pipeline\. AWS resources for your source actions must always be created in the same AWS Region where you create your pipeline\. For example, if you create your pipeline in the US East \(Ohio\) Region, your CodeCommit repository must be in the US East \(Ohio\) Region\.   
You can add cross\-region actions when you create your pipeline\. AWS resources for cross\-region actions must be in the same Region where you plan to execute the action\. For more information about cross\-region actions, see [Add a Cross\-Region Action in CodePipeline](actions-create-cross-region.md)\.

Not what you're looking for? To create a simple pipeline using a versioned Amazon S3 bucket as a code repository, see [Tutorial: Create a Simple Pipeline \(Amazon S3 Bucket\)](tutorials-simple-s3.md)\.

After you complete this tutorial, you should have enough practice with CodeCommit concepts to use it as a repository in your pipelines\.

CodePipeline uses Amazon CloudWatch Events to detect changes in your CodeCommit source repository and branch\. Using Amazon CloudWatch Events to start your pipeline when changes occur is the default for this source type\. When you use the wizard in the console to create a pipeline, the rule is created for you\.

Before you begin, make sure you have completed the following tasks:
+ [Configure an IAM user](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)
+ [Install and configure the AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/installing.html)
+ [Create your key pair using Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)

In addition, make sure to complete these service\-specific tasks:
+ [CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up.html): Install Git and configure credentials 
+  [CodeDeploy](https://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-setup.html): Create an IAM instance profile and a CodeDeploy service role
+  [CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/setting-up.html#setting-up-assign-permissions): Assign CodePipeline permissions to the IAM user role

**Note**  
If you have already completed the [Tutorial: Create a Simple Pipeline \(Amazon S3 Bucket\)](tutorials-simple-s3.md) tutorial, but have not yet cleaned up its resources, you must create different names for many of the resources you used in that tutorial\. For example, instead of **MyFirstPipeline**, you might name your pipeline **MySecondPipeline**\.

**Topics**
+ [Step 1: Create a CodeCommit Repository and Local Repo](#codecommit-create-repository)
+ [Step 2: Add Sample Code to Your CodeCommit Repository](#codecommit-add-code)
+ [Step 3: Create an Amazon EC2 Linux Instance and Install the CodeDeploy Agent](#codecommit-create-deployment)
+ [Step 4: Create an Application in CodeDeploy](#codecommit-create-codedeploy-app)
+ [Step 5: Create Your First Pipeline in CodePipeline](#codecommit-create-pipeline)
+ [Step 6: Modify Code in Your CodeCommit Repository](#codecommit-push-code)
+ [Step 7: Optional Stage Management Tasks](#codecommit-optional-tasks)
+ [Step 8: Clean Up Resources](#codecommit-clean-up)

## Step 1: Create a CodeCommit Repository and Local Repo<a name="codecommit-create-repository"></a>

To start this tutorial, you create a repository in CodeCommit\. Your pipeline gets source code from this repository when it runs\. You also create a local repository where you maintain and update code before you push it to the CodeCommit repository\.

Follow the first two procedures in the [Git with CodeCommit Tutorial](https://docs.aws.amazon.com/codecommit/latest/userguide/getting-started.html) in the *CodeCommit User Guide*:
+ [Step 1: Create a CodeCommit Repository](https://docs.aws.amazon.com/codecommit/latest/userguide/getting-started.html#getting-started-create-repo)
+ [Step 2: Create a Local Repo](https://docs.aws.amazon.com/codecommit/latest/userguide/getting-started.html#getting-started-set-up-folders)

  For information about connecting to a local repo you create, see [Connect to a CodeCommit Repository](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-connect.html)\.

After you complete these two procedures, return to this page and continue to the next step\. Do not continue to the third step in the CodeCommit tutorial\. You must complete different steps in this tutorial instead\.

## Step 2: Add Sample Code to Your CodeCommit Repository<a name="codecommit-add-code"></a>

In this step, you download code for a sample application that was created for a CodeDeploy sample walkthrough, and add it to your CodeCommit repository\.

1. Download the following file: 
   +  [SampleApp\_Linux\.zip](https://s3.amazonaws.com/aws-codedeploy-us-east-1/samples/latest/SampleApp_Linux.zip)\. 

1. Unzip the files and push the files to the root of your test repository\.

   For this tutorial, if you created the `/tmp/my-demo-repo` directory, unzip the files from [SampleApp\_Linux\.zip](https://s3.amazonaws.com/aws-codedeploy-us-east-1/samples/latest/SampleApp_Linux.zip) into the local directory you created in the previous procedure \(for example, `/tmp/my-demo-repo` or `c:\temp\my-demo-repo`\)\. 

   Be sure to place the files directly into your local repository\. Do not include a `SampleApp_Linux` folder\. On your local Linux, macOS, or Unix machine, for example, your directory and file hierarchy should look like this:

   ```
   /tmp
     |my-demo-repo
       |-- appspec.yml
       |-- index.html
       |-- LICENSE.txt
       `-- scripts
         |-- install_dependencies
         |-- start_server
         `-- stop_server
   ```

1. Change directories to your local repo:

   ```
   (For Linux, macOS, or Unix) cd /tmp/my-demo-repo
   (For Windows) cd c:\temp\my-demo-repo
   ```

1. Run the following command to stage all of your files at once:

   ```
   git add -A
   ```

1. Run the following command to commit the files with a commit message:

   ```
   git commit -m "Added sample application files"
   ```

1. Run the following command to push the files from your local repo to your CodeCommit repository:

   ```
   git push
   ```

1. The files you downloaded and added to your local repo have now been added to the `master` branch in your CodeCommit `MyDemoRepo` repository and are ready to be included in a pipeline\.

## Step 3: Create an Amazon EC2 Linux Instance and Install the CodeDeploy Agent<a name="codecommit-create-deployment"></a>

In this step, you create the Amazon EC2 instance where you deploy a sample application\. As part of this process, you install the CodeDeploy agent on the instance\. The CodeDeploy agent is a software package that enables an instance to be used in CodeDeploy deployments\.

**Note**  
This tutorial provides sample steps for creating an Amazon EC2 Linux instance\. For sample steps to create an Amazon EC2 Windows instance, see Step 2: Create Amazon EC2 Windows Instances and Install the CodeDeploy Agent in [Tutorial: Create a Simple Pipeline \(Amazon S3 Bucket\)](tutorials-simple-s3.md)\.

**To launch an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the console dashboard, choose **Launch Instance**\.

1. On the **Step 1: Choose an Amazon Machine Image \(AMI\)** page, locate the row for the HVM edition of the Amazon Linux AMI, and then choose **Select**\. \(This AMI is labeled "Free tier eligible" and can be found at the top of the list\.\)
**Note**  
These basic configurations, called *Amazon Machine Images \(AMIs\)*, serve as templates for your instance\. This tutorial can be completed with any of the free tier eligible AMIs\. For simplicity, we use the HVM edition of the Amazon Linux AMI\.

1. On the **Step 2: Choose an Instance Type** page, choose the free tier eligible `t2.micro` type as the hardware configuration for your instance, and then choose **Next: Configure Instance Details**\.

1. On the **Step 3: Configure Instance Details** page, do the following:
   + In **Number of instances**, enter `1`\.
   + In **Auto\-assign Public IP**, choose **Enable**\.
   + In **IAM role**, choose an IAM role that has been configured for use as an IAM instance profile for use with CodeDeploy\. If you do not have an IAM instance profile, choose **Create new IAM role** and follow the instructions in [Create an IAM Instance Profile for Your Amazon EC2 Instances](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-iam-instance-profile.html)\.
**Note**  
For the purposes of this tutorial, you can use the following unrestricted policy in your IAM instance profile for CodeDeploy\. For pipelines you use in your development workflows, you might create a more restrictive bucket policy\.  

     ```
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Action": [
             "s3:Get*",
             "s3:List*"
           ],
           "Effect": "Allow",
           "Resource": "*"
         }
       ]
     }
     ```

1. On the **Step 3: Configure Instance Details** page, expand **Advanced Details**, and in the **User data** field, enter the following:

   ```
   #!/bin/bash
   yum -y update
   yum install -y ruby
   yum install -y aws-cli
   cd /home/ec2-user
   aws s3 cp s3://aws-codedeploy-us-east-2/latest/install . --region us-east-2
   chmod +x ./install
   ./install auto
   ```

   This code installs the CodeDeploy agent on your instance as it is created\. If you prefer, you can [connect to your Linux instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) and [install the CodeDeploy agent manually](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-run-agent.html#how-to-run-agent-install-linux) after the instance is created\.

1. Leave the rest of the items on the **Step 3: Configure Instance Details** page unchanged\. Choose **Next: Add Storage**, leave the **Step 4: Add Storage** page unchanged, and then choose **Next: Add Tags**\.

1. On the **Add Tags** page, with **Name** displayed in the **Key** box, type `MyCodePipelineDemo` in the **Value** box, and then choose **Next: Configure Security Group**\.
**Important**  
The **Key** and **Value** boxes are case\-sensitive\.

1. On the **Step 6: Configure Security Group** page, do the following: 
   + Next to **Assign a security group**, choose **Create a new security group**\.
   + In the row for **SSH**, under **Source**, choose **My IP**\.
   + Choose **Add Rule**, choose **HTTP**, and then under **Source**, choose **My IP**\.

1. Choose **Review and Launch**\.

1. On the **Review Instance Launch** page, choose **Launch**, and then do one of the following when prompted for a key pair:
   + If you already have a key pair to use with Amazon EC2 instances, select **Choose an existing key pair**, and then select your key pair\.
   + If you have not created a key pair yet, select **Create a new key pair**, enter a name for the key pair, and then choose **Download Key Pair**\. This is your only chance to save the private key file\. Be sure to download it\. Save the private key file in a safe place\. You must provide the name of your key pair when you launch an instance\. You must provide the corresponding private key each time you connect to the instance\. For more information, see [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.
**Warning**  
Don't select the **Proceed without a key pair** option\. If you launch your instance without a key pair, you can't connect to it if you need to troubleshoot issues with the CodeDeploy agent\.

   When you are ready, select the acknowledgement check box, and then choose **Launch Instances**\. 

1. Choose **View Instances** to close the confirmation page and return to the console\.

1. You can view the status of the launch on the **Instances** page\. When you launch an instance, its initial state is `pending`\. After the instance starts, its state changes to `running`, and it receives a public DNS name\. \(If the **Public DNS** column is not displayed, choose the **Show/Hide** icon, and then select **Public DNS**\.\)

1. It can take a few minutes for the instance to be ready for you to connect to it\. Check that your instance has passed its status checks\. You can view this information in the **Status Checks** column\.

If you want to confirm that the CodeDeploy agent is configured correctly, you can [connect to your Linux instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) and then [verify the CodeDeploy agent is running](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-run-agent.html#how-to-run-agent-verify-linux)\.

## Step 4: Create an Application in CodeDeploy<a name="codecommit-create-codedeploy-app"></a>

In CodeDeploy, an *application* is an identifier, in the form of a name, for the code you want to deploy\. CodeDeploy uses this name to ensure the correct combination of revision, deployment configuration, and deployment group are referenced during a deployment\. You select the name of the CodeDeploy application you create in this step when you create your pipeline later in this tutorial\.

**To create an application in CodeDeploy**

1. Open the CodeDeploy console at [https://console\.aws\.amazon\.com/codedeploy](https://console.aws.amazon.com/codedeploy)\.

1. If the **Applications** page does not appear, on the AWS CodeDeploy menu, choose **Applications**\.

1. Choose **Create application**\.

1. Leave **Custom application** selected\. In **Application name**, enter `MyDemoApplication`\. 

1. In **Compute Platform**, choose **EC2/On\-premises**\.

1. Choose **Create application**\.

**To create a deployment group in CodeDeploy**

1. On the page showing your application, choose **Create deployment group**\.

1. In **Deployment group name**, enter `MyDemoDeploymentGroup`\.

1. In **Service Role**, choose a service role that trusts AWS CodeDeploy with, at minimum, the trust and permissions described in [Create a Service Role for CodeDeploy](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-service-role.html)\. To get the service role ARN, see [Get the Service Role ARN \(Console\)](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-service-role.html#how-to-create-service-role-get-role-console)\.

1. Under **Deployment type**, choose **In\-place**\.

1. Under **Environment configuration**, choose **Amazon EC2 Instances**\. Choose **Name** in the **Key** box, and in the **Value** box, enter `MyCodePipelineDemo`\. 
**Important**  
You must choose the same value for the **Name** key here that you assigned to your Amazon EC2 instance when you created it\. If you tagged your instance with something other than `MyCodePipelineDemo`, be sure to use it here\.

1. Under **Deployment configuration**, choose `CodeDeployDefault.OneAtaTime`\.

1. Under **Load Balancer**, clear **Enable load balancing**\.

1. Expand the **Advanced** section\. Under **Alarms**, choose **Ignore alarm configuration**\.

1. Choose **Create deployment group**\.

## Step 5: Create Your First Pipeline in CodePipeline<a name="codecommit-create-pipeline"></a>

You're now ready to create and run your first pipeline\.

**To create a CodePipeline automated release process**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Welcome** page, **Getting started** page, or the **Pipelines** page, choose **Create pipeline**\.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyFirstPipeline**\.
**Note**  
If you choose another name for your pipeline, be sure to use it instead of *MyFirstPipeline* in the remaining steps of this tutorial\. After you create a pipeline, you cannot change its name\. Pipeline names are subject to some limitations\. For more information, see [Limits in AWS CodePipeline](limits.md)\. 

1. In **Service role**, do one of the following:
   + Choose **New service role** to allow CodePipeline to create a new service role in IAM\. In **Role name**, the role and policy name both default to this format: AWSCodePipelineServiceRole\-*region*\-*pipeline\_name*\. For example, this is the service role created for this tutorial: AWSCodePipelineServiceRole\-eu\-west\-2\-MyFirstPipeline\.
   + Choose **Existing service role** to use a service role already created in IAM\. In **Role name**, choose your service role from the list\.
**Note**  
Depending on when your service role was created, you might need to update its permissions to support additional AWS services\. For information, see [Add Permissions for Other AWS Services](how-to-custom-role.md#how-to-update-role-new-services)\. 

1. In **Artifact store**, do one of the following: 

   1. Choose **Default location**\. This uses the default artifact store, such as the Amazon S3 artifact bucket designated as the default, for your pipeline in the region you have selected for your pipeline\.

   1. Choose **Custom location** if you already have an artifact store, such as an Amazon S3 artifact bucket, in the same region as your pipeline\.
**Note**  
This is not the source bucket for your pipeline's source code\. This is the artifact store for your pipeline\. A separate artifact store, such as an Amazon S3 bucket, is required for each pipeline, in the same region as the pipeline\.

   Choose **Next**\.

1. In **Step 2: Add source stage**, in **Source provider**, choose **AWS CodeCommit**\. In **Repository name**, choose the name of the CodeCommit repository you created in [Step 1: Create a CodeCommit Repository and Local Repo](#codecommit-create-repository)\. In **Branch name**, choose the name of the branch that contains your latest code update\. Unless you created a different branch on your own, only `master` is available\. 

   Choose **Next step**\.  
![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-wizard-source-codecommit-pol.png)![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   After you select the repository name and branch, a message is displayed showing the Amazon CloudWatch Events rule to be created for this pipeline\. 

   Under **Change detection options**, leave the defaults\. This allows CodePipeline to use Amazon CloudWatch Events to detect changes in your source repository\.

   Choose **Next**\.

1. In **Step 3: Add build stage**, choose **Skip build stage**, and then accept the warning message by choosing **Skip** again\. Choose **Next**\.
**Note**  
In this tutorial, you are deploying code that requires no build service\.

1. In **Step 4: Add deploy stage**, in **Deploy provider**, choose **AWS CodeDeploy**\. The **Region** field defaults to the same AWS Region as your pipeline\. In **Application name**, enter **MyDemoApplication**, or choose the **Refresh** button, and then choose the application name from the list\. In **Deployment group**, enter **MyDemoDeploymentGroup**, or choose it from the list, and then choose **Next step**\.   
![\[The Step 4: Deploy page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-wizard-deploy-pol.png)![\[The Step 4: Deploy page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 4: Deploy page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
**Note**  
The name "Deploy" is the name given by default to the stage created in the **Step 4: Deploy** step, just as "Source" is the name given to the first stage of the pipeline\.

1. In **Step 5: Review**, review the information, and then choose **Create pipeline**\.

1. The pipeline starts to run\. You can view progress and success and failure messages as the CodePipeline sample deploys the webpage to the Amazon EC2 instance in the CodeDeploy deployment\.  
![\[A view of a pipeline beginning to run in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-firstpipeline-codecommit-pol.png)![\[A view of a pipeline beginning to run in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A view of a pipeline beginning to run in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

Congratulations\! You just created a simple pipeline in CodePipeline\. The pipeline has two stages:
+ A source stage \(**Source**\) that detects changes in the sample application stored in the CodeCommit repository and pulls those changes into the pipeline\. 
+ A deployment stage \(**Deploy**\) that deploys those changes to the Amazon EC2 instance using CodeDeploy\.

Next, you verify the results\.

**To verify that your pipeline ran successfully**

1. View the initial progress of the pipeline\. The status of each stage changes from **No executions yet** to **In Progress**, and then to either **Succeeded** or **Failed**\. The pipeline should complete the first run within a few minutes\.

1. After **Succeeded** is displayed for the pipeline status, in the status area for the **Staging** stage, choose **Details**\. This opens the CodeDeploy console\.

1. Choose your application in the list\. On the **Deployment group** tab, under **Deployment lifecycle events**, choose the instance ID\. This opens the EC2 console\.

1. On the **Description** tab, in **Public DNS**, copy the address, and then paste it into the address bar of your web browser\.

   This is the sample application you downloaded and pushed to your CodeCommit repository\.  
![\[The sample web page application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-demo-success-message-codedeploy.png)![\[The sample web page application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The sample web page application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

For more information about stages, actions, and how pipelines work, see [CodePipeline Concepts](concepts.md)\.

## Step 6: Modify Code in Your CodeCommit Repository<a name="codecommit-push-code"></a>

In this step, you make changes to the HTML file that is part of the sample CodeDeploy application you deployed to your Amazon EC2 instance\. When your pipeline runs again later in this tutorial, the changes you make are visible at the `http://PublicDNS` URLs\.

1. Change directories to your local repo:

   ```
   (For Linux, macOS, or Unix) cd /tmp/my-demo-repo
   (For Windows) cd c:\temp\my-demo-repo
   ```

1. Use a text editor to modify the `index.html` file:

   ```
   (For Linux or Unix)gedit index.html
   (For OS X)open –e index.html
   (For Windows)notepad index.html
   ```

1. Revise the contents of the `index.html` file to change the background color and some of the text on the webpage, and then save the file\. 

   ```
   <!DOCTYPE html>
   <html>
   <head>
     <title>Updated Sample Deployment</title>
     <style>
       body {
         color: #000000;
         background-color: #CCFFCC;
         font-family: Arial, sans-serif;  
         font-size:14px;
       }
           
       h1 {
         font-size: 250%;
         font-weight: normal;
         margin-bottom: 0;
       }
       
       h2 {
         font-size: 175%;
         font-weight: normal;
         margin-bottom: 0;
       }
     </style>
   </head>
   <body>
     <div align="center"><h1>Updated Sample Deployment</h1></div>
     <div align="center"><h2>This application was updated using CodePipeline, CodeCommit, and CodeDeploy.</h2></div>
     <div align="center">
       <p>Learn more:</p> 
       <p><a href="https://docs.aws.amazon.com/codepipeline/latest/userguide/">CodePipeline User Guide</a></p>
       <p><a href="https://docs.aws.amazon.com/codecommit/latest/userguide/">CodeCommit User Guide</a></p>
       <p><a href="https://docs.aws.amazon.com/codedeploy/latest/userguide/">CodeDeploy User Guide</a></p>
     </div>
   </body>
   </html>
   ```

1. Commit and push your changes to your CodeCommit repository by running the following commands, one at a time:

   ```
   git commit -am "Updated sample application files"
   ```

   ```
   git push
   ```

Your pipeline is configured to run whenever code changes are made to your CodeCommit repository\. 

**To verify your pipeline ran successfully**

1. View the initial progress of the pipeline\. The status of each stage changes from **No executions yet** to **In Progress**, and then to either **Succeeded** or **Failed**\. The pipeline should complete within a few minutes\.

1. After **Succeeded** is displayed for the action status, in the status area for the **Staging** stage, choose **Details**\. This opens the CodeDeploy console\.

1. On the **Deployment group** tab, under **Deployment lifecycle events**, choose the instance ID\. This opens the EC2 console\.

1. On the **Description** tab, in **Public DNS**, copy the address, and then paste it into the address bar of your web browser\.

   The updated webpage is displayed:  
![\[The updated sample webpage application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-demo-success-message-codecommit.png)![\[The updated sample webpage application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The updated sample webpage application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

For more information about stages, actions, and how pipelines work, see [CodePipeline Concepts](concepts.md)\.

## Step 7: Optional Stage Management Tasks<a name="codecommit-optional-tasks"></a>

If you want to gain more experience working with stages before you end the tutorial, you can follow two additional procedures in the [Tutorial: Create a Simple Pipeline \(Amazon S3 Bucket\)](tutorials-simple-s3.md)\.
+ [Step 5: Add Another Stage to Your Pipeline](tutorials-simple-s3.md#s3-add-stage)
+ [Disable and Enable Transitions Between Stages in CodePipeline](tutorials-simple-s3.md#s3-configure-transitions)
**Note**  
In step 4 of the second procedure, instead of uploading your sample to an Amazon S3 bucket again, make a change to the sample app in your local repo and push it to your CodeCommit repository\.

## Step 8: Clean Up Resources<a name="codecommit-clean-up"></a>

You can use some of the resources you created in this tutorial for the next tutorial, [Tutorial: Create a Four\-Stage Pipeline](tutorials-four-stage-pipeline.md)\. For example, you can reuse the CodeDeploy application and deployment\. However, after you complete this and any other tutorials, you should delete the pipeline and the resources it uses, so that you are not charged for the continued use of those resources\. First, delete the pipeline, then the CodeDeploy application and its associated Amazon EC2 instance, and finally, the CodeCommit repository\.

**To clean up the resources used in this tutorial**

1. To clean up your CodePipeline resources, follow the instructions in [Delete a Pipeline in AWS CodePipeline](pipelines-delete.md)\.

1. To clean up your CodeDeploy resources, follow the instructions in [Clean Up Deployment Walkthrough Resources](https://docs.aws.amazon.com/codedeploy/latest/userguide/tutorials-simple-s3alkthrough.html#tutorials-simple-s3alkthrough-clean-up)\.

1. To delete the CodeCommit repository, follow the instructions in [Delete a CodeCommit Repository](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-delete-repository.html)\.