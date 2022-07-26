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
**Note**  
You are not required to set up a local repository\. You can also use the console to upload files as described in [Step 2: Add sample code to your CodeCommit repository](#codecommit-add-code)\.

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

1. To upload files to your repository, use one of the following methods\.

   1. To use the CodeCommit console to upload your files: 

      1. Open the CodeCommit console, and choose your repository from the **Repositories** list\.

      1. Choose **Add file**, and then choose **Upload file**\. 

      1. Select **Choose file**, and then browse for your file\. To add a file under a folder, choose **Create file** and then enter the folder name with the file name, such as `scripts/install_dependencies`\. Paste the file contents into the new file\.

         Commit the change by entering your user name and email address\. 

         Choose **Commit changes**\.

      1. Repeat this step for each file\.

         Your repository contents should look like this:

         ```
                │-- appspec.yml
                │-- index.html
                │-- LICENSE.txt
                └-- scripts
                    │-- install_dependencies
                    │-- start_server
                    └-- stop_server
         ```

   1. To use git commands to upload your files: 

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

1. The files you downloaded and added to your local repo have now been added to the `main` branch in your CodeCommit `MyDemoRepo` repository and are ready to be included in a pipeline\.

## Step 3: Create an Amazon EC2 Linux instance and install the CodeDeploy agent<a name="codecommit-create-deployment"></a>

In this step, you create the Amazon EC2 instance where you deploy a sample application\. As part of this process, create an instance role that allows install and management of the CodeDeploy agent on the instance\. The CodeDeploy agent is a software package that enables an instance to be used in CodeDeploy deployments\. You also attach  policies that allow the instance to fetch files that the CodeDeploy agent uses to deploy your application and to allow the instance to be managed by SSM\.

**To create an instance role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\)\.

1. From the console dashboard, choose **Roles**\.

1. Choose **Create role**\.

1. Under **Select type of trusted entity**, select **AWS service**\. Under **Choose a use case**, select **EC2**\. Under **Select your use case**, choose **EC2**\. Choose **Next: Permissions**\.

1. Search for and select the policy named **`AmazonEC2RoleforAWSCodeDeploy`**\. 

1. Search for and select the policy named **`AmazonSSMManagedInstanceCore`**\. Choose **Next: Tags**\.

1. Choose **Next: Review**\. Enter a name for the role \(for example, **EC2InstanceRole**\)\.
**Note**  
Make a note of your role name for the next step\. You choose this role when you are creating your instance\.

   Choose **Create role**\.

**To launch an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the side navigation, choose **Instances**, and select **Launch instances** from the top of the page\.

1. In **Name**, enter **MyCodePipelineDemo**\. This assigns the instance a tag **Key** of **Name** and a tag **Value** of **MyCodePipelineDemo**\. Later, you create a CodeDeploy application that deploys the sample application to this instance\. CodeDeploy selects instances to deploy based on the tags\.

1. Under **Application and OS Images \(Amazon Machine Image\)**, locate the  **Amazon Linux** AMI option with the AWS logo, and make sure it is selected\. \(This AMI is described as the Amazon Linux 2 AMI \(HVM\) and is labeled "Free tier eligible"\.\)

1. Under **Instance type**, choose the free tier eligible `t2.micro` type as the hardware configuration for your instance\.

1. Under **Key pair \(login\)**, choose a key pair or create one\. 

   You can also choose **Proceed without a key pair**\.
**Note**  
For the purposes of this tutorial, you can proceed without a key pair\. To use SSH to connect to your instances, create or use a key pair\.

1. Under **Network settings**, do the following\.

   In **Auto\-assign Public IP**, make sure the status is **Enable**\.
   + Next to **Assign a security group**, choose **Create a new security group**\.
   + In the row for **SSH**, under **Source type**, choose **My IP**\.
   + Choose **Add security group**, choose **HTTP**, and then under **Source type**, choose **My IP**\.

1. Expand **Advanced details**\. In **IAM instance profile**, choose the IAM role you created in the previous procedure \(for example, **EC2InstanceRole**\)\.

1. Under  **Summary**, under **Number of instances**, enter `1`\.\.

1. Choose **Launch instance**\. 

1. You can view the status of the launch on the **Instances** page\. When you launch an instance, its initial state is `pending`\. After the instance starts, its state changes to `running`, and it receives a public DNS name\. \(If the **Public DNS** column is not displayed, choose the **Show/Hide** icon, and then select **Public DNS**\.\)

## Step 4: Create an application in CodeDeploy<a name="codecommit-create-codedeploy-app"></a>

In CodeDeploy, an [https://docs.aws.amazon.com/codedeploy/latest/userguide/applications.html](https://docs.aws.amazon.com/codedeploy/latest/userguide/applications.html) is a resource that contains the software application you want to deploy\. Later, you use this application with CodePipeline to automate deployments of the sample application to your Amazon EC2 instance\.

First, you create a role that allows CodeDeploy to perform deployments\. Then, you create a CodeDeploy application\.

**To create a CodeDeploy service role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\)\.

1. From the console dashboard, choose **Roles**\.

1. Choose **Create role**\.

1. Under **Select trusted entity**, choose **AWS service**\. Under **Use case**, choose **CodeDeploy**\. Choose **CodeDeploy** from the options listed\. Choose **Next**\. The `AWSCodeDeployRole` managed policy is already attached to the role\.

1. Choose **Next**\.

1. Enter a name for the role \(for example, **CodeDeployRole**\), and then choose **Create role**\.

**To create an application in CodeDeploy**

1. Open the CodeDeploy console at [https://console\.aws\.amazon\.com/codedeploy](https://console.aws.amazon.com/codedeploy)\.

1. If the **Applications** page does not appear, on the menu, choose **Applications**\.

1. Choose **Create application**\.

1. In **Application name**, enter **MyDemoApplication**\. 

1. In **Compute Platform**, choose **EC2/On\-premises**\.

1. Choose **Create application**\.

**To create a deployment group in CodeDeploy**

A [https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-groups.html](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-groups.html) is a resource that defines deployment\-related settings like which instances to deploy to and how fast to deploy them\.

1. On the page that displays your application, choose **Create deployment group**\.

1. In **Deployment group name**, enter **MyDemoDeploymentGroup**\.

1. In **Service role**, choose the service role you created earlier \(for example, **CodeDeployRole**\)\.

1. Under **Deployment type**, choose **In\-place**\.

1. Under **Environment configuration**, choose **Amazon EC2 Instances**\. In the **Key** field, enter **Name**\. In the **Value** field, enter the name you used to tag the instance \(for example, **MyCodePipelineDemo**\)\.

1. Under **Agent configuration with AWS Systems Manager**, choose **Now and schedule updates**\. This installs the agent on the instance\. The Linux instance is already configured with the SSM agent and will now be updated with the CodeDeploy agent\.

1. Under **Deployment configuration**, choose `CodeDeployDefault.OneAtaTime`\.

1. Under **Load Balancer**, make sure **Enable load balancing** is not selected\. You do not need to set up a load balancer or choose a target group for this example\.

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

1. In **Step 2: Add source stage**, in **Source provider**, choose **CodeCommit**\. In **Repository name**, choose the name of the CodeCommit repository you created in [Step 1: Create a CodeCommit repository](#codecommit-create-repository)\. In **Branch name**, choose `main`, and then choose **Next step**\.

   After you select the repository name and branch, a message displays the Amazon CloudWatch Events rule to be created for this pipeline\. 

   Under **Change detection options**, leave the defaults\. This allows CodePipeline to use Amazon CloudWatch Events to detect changes in your source repository\.

   Choose **Next**\.

1. In **Step 3: Add build stage**, choose **Skip build stage**, and then accept the warning message by choosing **Skip** again\. Choose **Next**\.
**Note**  
In this tutorial, you are deploying code that requires no build service, so you can skip this step\. However, if your source code needs to be built before it is deployed to instances, you can configure [CodeBuild](http://aws.amazon.com/codebuild/) in this step\.

1. In **Step 4: Add deploy stage**, in **Deploy provider**, choose **CodeDeploy**\. In **Application name**, choose **MyDemoApplication**\. In **Deployment group**, choose **MyDemoDeploymentGroup**, and then choose **Next step**\.

1. In **Step 5: Review**, review the information, and then choose **Create pipeline**\.

1. The pipeline starts running after it is created\. It downloads the code from your CodeCommit repository and creates a CodeDeploy deployment to your EC2 instance\. You can view progress and success and failure messages as the CodePipeline sample deploys the webpage to the Amazon EC2 instance in the CodeDeploy deployment\.  
![\[A view of a pipeline starting to run in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-firstpipeline-codecommit-pol.png)![\[A view of a pipeline starting to run in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A view of a pipeline starting to run in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

Congratulations\! You just created a simple pipeline in CodePipeline\. 

Next, you verify the results\.

**To verify that your pipeline ran successfully**

1. View the initial progress of the pipeline\. The status of each stage changes from **No executions yet** to **In Progress**, and then to either **Succeeded** or **Failed**\. The pipeline should complete the first run within a few minutes\.

1. After **Succeeded** is displayed for the pipeline status, in the status area for the **Deploy** stage, choose **CodeDeploy**\. This opens the CodeDeploy console\. If **Succeeded** is not displayed see [Troubleshooting CodePipeline](troubleshooting.md)\.

1.  On the **Deployments** tab, choose the deployment ID\. On the page for the deployment, under **Deployment lifecycle events**, choose the instance ID\. This opens the EC2 console\.

1. On the **Description** tab, in **Public DNS**, copy the address \(for example, `ec2-192-0-2-1.us-west-2.compute.amazonaws.com`\), and then paste it into the address bar of your web browser\.

   The web page displays for the sample application you downloaded and pushed to your CodeCommit repository\.

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

   The updated webpage is displayed\.

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