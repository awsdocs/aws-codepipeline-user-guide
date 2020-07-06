# Tutorial: Create a simple pipeline \(CodeCommit repository\)<a name="tutorials-simple-codecommit"></a>

In this tutorial, you use CodePipeline to deploy code maintained in a CodeCommit repository to a single Amazon EC2 instance\. Your pipeline is triggered when you push a change to the CodeCommit repository\. The pipeline deploys your changes to an Amazon EC2 instance using CodeDeploy as the deployment service\.

The pipeline has two stages:
+ A source stage \(**Source**\) for your CodeCommit source action\.
+ A deployment stage \(**Deploy**\) for your CodeDeploy deployment action\.

The easiest way to get started with AWS CodePipeline is to use the **Create Pipeline** wizard in the CodePipeline console\.

**Note**  
Before you begin, make sure you've set up your Git client to work with CodeCommit\. For instructions, see [Setting up for CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up.html)\.

## Step 1: Create a CodeCommit repository<a name="codecommit-create-repository"></a>

First, you create a repository in CodeCommit\. Your pipeline gets source code from this repository when it runs\. You also create a local repository where you maintain and update code before you push it to the CodeCommit repository\.

**To create a CodeCommit repository**

1. Open the CodeCommit console at [https://console\.aws\.amazon\.com/codecommit/](https://console.aws.amazon.com/codecommit/)\.

1. In the Region selector, choose the AWS Region where you want to create the repository and pipeline\. For more information, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.

1. On the **Repositories** page, choose **Create repository**\.

1. On the **Create repository** page, in **Repository name**, enter a name for your repository \(for example, **MyDemoRepo**\)\.

1. Choose **Create**\.

**Note**  
The remaining steps in this tutorial use **MyDemoRepo** for the name of your CodeCommit repository\. If you choose a different name, be sure to use it throughout this tutorial\.

**To set up a local repository**

In this step, you set up a local repository to connect to your remote CodeCommit repository\.

1. With your new repository open in the console, choose **Clone URL** on the top right of the page, and then choose **Clone SSH**\. The address to clone your Git repository is copied to your clipboard\.

1. In your terminal or command line, navigate to a local directory where you'd like your local repository to be stored\. In this tutorial, we use `/tmp`\.

1. Run the following command to clone the repository, replacing the SSH address with the one you copied in the previous step\. This command creates a directory called `MyDemoRepo`\. You copy a sample application to this directory\.

   ```
   git clone ssh://git-codecommit.us-west-2.amazonaws.com/v1/repos/MyDemoRepo
   ```

## Step 2: Add sample code to your CodeCommit repository<a name="codecommit-add-code"></a>

In this step, you download code for a sample application that was created for a CodeDeploy sample walkthrough, and add it to your CodeCommit repository\.

1. Download the following file: [SampleApp\_Linux\.zip](samples/SampleApp_Linux.zip)

1. Unzip the files from [SampleApp\_Linux\.zip](samples/SampleApp_Linux.zip) into the local directory you created earlier \(for example, `/tmp/MyDemoRepo` or `c:\temp\MyDemoRepo`\)\.

   Be sure to place the files directly into your local repository\. Do not include a `SampleApp_Linux` folder\. On your local Linux, macOS, or Unix machine, for example, your directory and file hierarchy should look like this:

   ```
   /tmp
      └-- MyDemoRepo
          │-- appspec.yml
          │-- index.html
          │-- LICENSE.txt
          └-- scripts
              │-- install_dependencies
              │-- start_server
              └-- stop_server
   ```

1. Change directories to your local repo:

   ```
   (For Linux, macOS, or Unix) cd /tmp/MyDemoRepo
   (For Windows) cd c:\temp\MyDemoRepo
   ```

1. Run the following command to stage all of your files at once:

   ```
   git add -A
   ```

1. Run the following command to commit the files with a commit message:

   ```
   git commit -m "Add sample application files"
   ```

1. Run the following command to push the files from your local repo to your CodeCommit repository:

   ```
   git push
   ```

1. The files you downloaded and added to your local repo have now been added to the `master` branch in your CodeCommit `MyDemoRepo` repository and are ready to be included in a pipeline\.

## Step 3: Create an EC2 Linux instance and install the CodeDeploy agent<a name="codecommit-create-deployment"></a>

In this step, you create the EC2 instance where you deploy a sample application\. As part of this process, you install the CodeDeploy agent on the EC2 instance\. The CodeDeploy agent is a software package that enables an instance to be used in CodeDeploy deployments\. You also attach an IAM role to the instance \(known as an *instance role*\) to allow it to fetch files that the CodeDeploy agent uses to deploy your application\.

**To create an instance role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\)\.

1. From the console dashboard, choose **Roles**\.

1. Choose **Create role**\.

1. Under **Select type of trusted entity**, select **AWS service**\. Under **Choose a use case**, select **EC2**, and then choose **Next: Permissions**\.

1. Search for and select the policy named **AmazonEC2RoleforAWSCodeDeploy**, and then choose **Next: Tags**\.

1. Choose **Next: Review**\. Enter a name for the role \(for example, **EC2InstanceRole**\)\.
**Note**  
Make a note of your role name for the next step\. You choose this role when you are creating your instance\.

   Choose **Create role**\.

**To launch an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the console dashboard, choose **Launch instance**, and select **Launch instance** from the options that pop up\.

1. On **Step 1: Choose an Amazon Machine Image \(AMI\)**, locate **Amazon Linux 2 AMI \(HVM\), SSD Volume Type**, and then choose **Select**\. \(This AMI is labeled "Free tier eligible" and can be found at the top of the list\.\)

1. On the **Step 2: Choose an Instance Type** page, choose the free tier eligible `t2.micro` type as the hardware configuration for your instance, and then choose **Next: Configure Instance Details**\.

1. On the **Step 3: Configure Instance Details** page, do the following:
   + In **Number of instances**, enter `1`\.
   + In **Auto\-assign Public IP**, choose **Enable**\.
   + In **IAM role**, choose the IAM role you created in the previous procedure \(for example, **EC2InstanceRole**\)\.
   + Expand **Advanced Details**, and in the **User data** field, enter the following:

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
**Note**  
For an example that runs these commands with elevated privileges \(sudo commands\), see the CodeDeploy agent reference in [Install or reinstall the CodeDeploy agent for Amazon Linux or RHEL](https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent-operations-install-linux.html) in the *AWS CodeDeploy User Guide*\.

     This code installs the CodeDeploy agent on your instance as it is created\.
   + Leave the rest of the items on the **Step 3: Configure Instance Details** page unchanged\. Choose **Next: Add Storage**\.

1. Leave the **Step 4: Add Storage** page unchanged, and then choose **Next: Add Tags**\.

1. Choose **Add Tag**\. In **Key**, enter **Name**, and in **Value**, enter **MyCodePipelineDemo**\. Choose **Next: Configure Security Group**\. Later, you create a CodeDeploy application that deploys the sample application to this instance\. CodeDeploy selects instances to deploy based on the tags that are attached to instances\.

1. On the **Step 6: Configure Security Group** page, do the following: 
   + Next to **Assign a security group**, choose **Create a new security group**\.
   + In the row for **SSH**, under **Source**, choose **My IP**\.
   + Choose **Add Rule**, choose **HTTP**, and then under **Source**, choose **My IP**\.

1. Choose **Review and Launch**\.

1. On the **Review Instance Launch** page, choose **Launch**\. When prompted for a key pair, choose **Proceed without a key pair**\.
**Note**  
For the purposes of this tutorial, you can proceed without a key pair\. To use SSH to connect to your instances, create or use a key pair\.

   When you are ready, select the acknowledgment check box, and then choose **Launch Instances**\. 

1. Choose **View Instances** to close the confirmation page and return to the console\.

1. You can view the status of the launch on the **Instances** page\. When you launch an instance, its initial state is `pending`\. After the instance starts, its state changes to `running`, and it receives a public DNS name\. \(If the **Public DNS** column is not displayed, choose the **Show/Hide** icon, and then select **Public DNS**\.\)

1. It can take a few minutes for the instance to be ready for you to connect to it\. View the information in the **Status Checks** column to see if your instance has passed its status checks\. 

## Step 4: Create an application in CodeDeploy<a name="codecommit-create-codedeploy-app"></a>

In CodeDeploy, an [https://docs.aws.amazon.com/codedeploy/latest/userguide/applications.html](https://docs.aws.amazon.com/codedeploy/latest/userguide/applications.html) is a resource that contains the software application you want to deploy\. Later, you use this application with CodePipeline to automate deployments of the sample application to your Amazon EC2 instance\.

First, you create a role that allows CodeDeploy to perform deployments\. Then, you create a CodeDeploy application\.

**To create a CodeDeploy service role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\)\.

1. From the console dashboard, choose **Roles**\.

1. Choose **Create role**\.

1. Under **Select type of trusted entity**, select **AWS service**\. Under **Choose a use case**, select **CodeDeploy**, and then choose **Next: Permissions**\. The `AWSCodeDeployRole` managed policy is already attached to the role\.

1. Choose **Next: Tags**, and **Next: Review**\.

1. Enter a name for the role \(for example, **CodeDeployRole**\), and then choose **Create role**\.

**To create an application in CodeDeploy**

1. Open the CodeDeploy console at [https://console\.aws\.amazon\.com/codedeploy](https://console.aws.amazon.com/codedeploy)\.

1. If the **Applications** page does not appear, on the AWS CodeDeploy menu, choose **Applications**\.

1. Choose **Create application**\.

1. In **Application name**, enter **MyDemoApplication**\. 

1. In **Compute Platform**, choose **EC2/On\-premises**\.

1. Choose **Create application**\.

**To create a deployment group in CodeDeploy**

A [https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-groups.html](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-groups.html) is a resource that defines deployment\-related settings like which instances to deploy to and how fast to deploy them\.

1. On the page that displays your application, choose **Create deployment group**\.

1. In **Deployment group name**, enter **MyDemoDeploymentGroup**\.

1. In **Service Role**, choose the service role you created earlier \(for example, **CodeDeployRole**\)\.

1. Under **Deployment type**, choose **In\-place**\.

1. Under **Environment configuration**, choose **Amazon EC2 Instances**\. In the **Key** field, enter the name you used to tag the instance \(for example, **MyCodePipelineDemo**\)\.

1. Under **Deployment configuration**, choose `CodeDeployDefault.OneAtaTime`\.

1. Under **Load Balancer**, clear **Enable load balancing**\. You do not need to set up a load balancer or choose a target group for this example\.

1. Expand the **Advanced** section\. Under **Alarms**, if any alarms are listed, choose **Ignore alarm configuration**\.

1. Choose **Create deployment group**\.

## Step 5: Create your first pipeline in CodePipeline<a name="codecommit-create-pipeline"></a>

You're now ready to create and run your first pipeline\. In this step, you create a pipeline that runs automatically when code is pushed to your CodeCommit repository\.

**To create a CodePipeline pipeline**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   Open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

1. Choose **Create pipeline**\.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyFirstPipeline**\.

1. In **Service role**, choose **New service role** to allow CodePipeline to create a service role in IAM\.

1. Leave the settings under **Advanced settings** at their defaults, and then choose **Next**\.

1. In **Step 2: Add source stage**, in **Source provider**, choose **AWS CodeCommit**\. In **Repository name**, choose the name of the CodeCommit repository you created in [Step 1: Create a CodeCommit repository](#codecommit-create-repository)\. In **Branch name**, choose `master`, and then choose **Next step**\.  
![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-wizard-source-codecommit-pol.png)![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 2: Source page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   After you select the repository name and branch, a message displays the Amazon CloudWatch Events rule to be created for this pipeline\. 

   Under **Change detection options**, leave the defaults\. This allows CodePipeline to use Amazon CloudWatch Events to detect changes in your source repository\.

   Choose **Next**\.

1. In **Step 3: Add build stage**, choose **Skip build stage**, and then accept the warning message by choosing **Skip** again\. Choose **Next**\.
**Note**  
In this tutorial, you are deploying code that requires no build service, so you can skip this step\. However, if your source code needs to be built before it is deployed to instances, you can configure [CodeBuild](http://aws.amazon.com/codebuild/) in this step\.

1. In **Step 4: Add deploy stage**, in **Deploy provider**, choose **AWS CodeDeploy**\. In **Application name**, choose **MyDemoApplication**\. In **Deployment group**, choose **MyDemoDeploymentGroup**, and then choose **Next step**\.  
![\[The Step 4: Deploy page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-wizard-deploy-pol.png)![\[The Step 4: Deploy page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 4: Deploy page in the CodePipeline pipeline wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. In **Step 5: Review**, review the information, and then choose **Create pipeline**\.

1. The pipeline starts running after it is created\. It downloads the code from your CodeCommit repository and creates a CodeDeploy deployment to your EC2 instance\. You can view progress and success and failure messages as the CodePipeline sample deploys the webpage to the Amazon EC2 instance in the CodeDeploy deployment\.  
![\[A view of a pipeline starting to run in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-firstpipeline-codecommit-pol.png)![\[A view of a pipeline starting to run in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A view of a pipeline starting to run in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

Congratulations\! You just created a simple pipeline in CodePipeline\. 

Next, you verify the results\.

**To verify that your pipeline ran successfully**

1. View the initial progress of the pipeline\. The status of each stage changes from **No executions yet** to **In Progress**, and then to either **Succeeded** or **Failed**\. The pipeline should complete the first run within a few minutes\.

1. After **Succeeded** is displayed for the pipeline status, in the status area for the **Deploy** stage, choose **AWS CodeDeploy**\. This opens the CodeDeploy console\. If **Succeeded** is not displayed see [Troubleshooting CodePipeline](troubleshooting.md)\.

1.  On the **Deployments** tab, choose the deployment ID\. On the page for the deployment, under **Deployment lifecycle events**, choose the instance ID\. This opens the EC2 console\.

1. On the **Description** tab, in **Public DNS**, copy the address \(for example, `ec2-192-0-2-1.us-west-2.compute.amazonaws.com`\), and then paste it into the address bar of your web browser\.

   This is the sample application you downloaded and pushed to your CodeCommit repository\.  
![\[The sample webpage application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-demo-success-message-codedeploy.png)![\[The sample webpage application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The sample webpage application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

For more information about stages, actions, and how pipelines work, see [CodePipeline concepts](concepts.md)\.

## Step 6: Modify code in your CodeCommit repository<a name="codecommit-push-code"></a>

Your pipeline is configured to run whenever code changes are made to your CodeCommit repository\. In this step, you make changes to the HTML file that is part of the sample CodeDeploy application in the CodeCommit repository\. When you push these changes, your pipeline runs again, and the changes you make are visible at the web address you accessed earlier\.

1. Change directories to your local repo:

   ```
   (For Linux, macOS, or Unix) cd /tmp/MyDemoRepo
   (For Windows) cd c:\temp\MyDemoRepo
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

**To verify your pipeline ran successfully**

1. View the initial progress of the pipeline\. The status of each stage changes from **No executions yet** to **In Progress**, and then to either **Succeeded** or **Failed**\. The running of the pipeline should be complete within a few minutes\.

1. After **Succeeded** is displayed for the action status, refresh the demo page you accessed earlier in your browser\.

   The updated webpage is displayed:  
![\[The updated sample webpage application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-demo-success-message-codecommit.png)![\[The updated sample webpage application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The updated sample webpage application that was pushed to your CodeCommit repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## Step 7: Clean up resources<a name="codecommit-clean-up"></a>

You can use some of the resources you created in this tutorial for other tutorials in this guide\. For example, you can reuse the CodeDeploy application and deployment\. However, after you complete this and any other tutorials, you should delete the pipeline and the resources it uses so that you are not charged for the continued use of those resources\. First, delete the pipeline, then the CodeDeploy application and its associated Amazon EC2 instance, and finally, the CodeCommit repository\.

**To clean up the resources used in this tutorial**

1. To clean up your CodePipeline resources, follow the instructions in [Delete a pipeline in AWS CodePipeline](pipelines-delete.md)\.

1. To clean up your CodeDeploy resources, follow the instructions in [Clean Up Deployment Walkthrough Resources](https://docs.aws.amazon.com/codedeploy/latest/userguide/tutorials-simple-s3alkthrough.html#tutorials-simple-s3alkthrough-clean-up)\.

1. To delete the CodeCommit repository, follow the instructions in [Delete a CodeCommit repository](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-delete-repository.html)\.

## Step 8: Further reading<a name="codecommit-optional-tasks"></a>

Learn more about how CodePipeline works:
+ For more information about stages, actions, and how pipelines work, see [CodePipeline concepts](concepts.md)\.
+ For information about the actions you can perform using CodePipeline, see [Integrations with CodePipeline action types](integrations-action-type.md)\.
+ Try this more advanced tutorial, [Tutorial: Create a four\-stage pipeline](tutorials-four-stage-pipeline.md)\. It creates a multi\-stage pipeline that includes a step that builds code before it's deployed\.