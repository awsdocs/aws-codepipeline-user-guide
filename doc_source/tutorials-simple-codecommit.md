# Tutorial: Create a Simple Pipeline \(AWS CodeCommit Repository\)<a name="tutorials-simple-codecommit"></a>

The easiest way to get started with AWS CodePipeline is to use the **Create Pipeline** wizard in the AWS CodePipeline console to create a simple pipeline\. 

In this tutorial, you will use AWS CodePipeline to deploy code that is maintained in an AWS CodeCommit repository to a single Amazon EC2 instance\. You will use AWS CodeDeploy as the deployment service\.

Not what you're looking for? To create a simple pipeline using a versioned Amazon S3 bucket as a code repository, see [Tutorial: Create a Simple Pipeline \(Amazon S3 Bucket\)](tutorials-simple-s3.md)\.

After you complete this tutorial, you should have enough practice with AWS CodeCommit concepts to use it as a repository in your pipelines\.

AWS CodePipeline uses Amazon CloudWatch Events to detect changes in your AWS CodeCommit source repository and branch\. Using Amazon CloudWatch Events to automatically start your pipeline when changes occur is the default for this source type\. When you create a pipeline in this wizard using the console, the rule is created\.

Before you begin, make sure you have completed the following tasks:

+ [Configure an IAM user](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)

+ [Install and configure the AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/installing.html)

+ [Create your key pair using Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)

In addition, make sure to complete these service\-specific tasks:

+ [AWS CodeCommit](http://docs.aws.amazon.com/codecommit/latest/userguide/setting-up.html): Install Git and configure credentials 

+  [AWS CodeDeploy](http://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-setup.html): Create an IAM instance profile and an AWS CodeDeploy service role

+  [AWS CodePipeline](http://docs.aws.amazon.com/codepipeline/latest/userguide/setting-up.html#setting-up-assign-permissions): Assign AWS CodePipeline permissions to the IAM user role

**Note**  
If you have already completed the [Tutorial: Create a Simple Pipeline \(Amazon S3 Bucket\)](tutorials-simple-s3.md) tutorial, but not yet cleaned up its resources, you will need to create different names for many of the resources you used in that tutorial\. For example, instead of **MyFirstPipeline**, you might name your pipeline **MySecondPipeline**\.


+ [Step 1: Create an AWS CodeCommit Repository and Local Repo](#codecommit-create-repository)
+ [Step 2: Add Sample Code to Your AWS CodeCommit Repository](#codecommit-add-code)
+ [Step 3: Create an Amazon EC2 Instance and Install the AWS CodeDeploy Agent](#codecommit-create-deployment)
+ [Step 4: Create an Application in AWS CodeDeploy](#codecommit-create-codedeploy-app)
+ [Step 5: Create Your First Pipeline in AWS CodePipeline](#codecommit-create-pipeline)
+ [Step 6: Modify Code in Your AWS CodeCommit Repository](#codecommit-push-code)
+ [Step 7: Optional Stage Management Tasks](#codecommit-optional-tasks)
+ [Step 8: Clean Up Resources](#codecommit-clean-up)

## Step 1: Create an AWS CodeCommit Repository and Local Repo<a name="codecommit-create-repository"></a>

To start this tutorial, you will create a repository in AWS CodeCommit\. Your pipeline will get source code from this repository when it runs\. You will also create a local repository where you maintain and update code before pushing it to the AWS CodeCommit repository\.

**Important**  
AWS CodeCommit is currently supported for pipelines in the following regions only:   
US East \(Ohio\) Region \(us\-east\-2\)
US East \(N\. Virginia\) Region \(us\-east\-1\)
US West \(Oregon\) Region \(us\-west\-2\)
EU \(Ireland\) Region \(eu\-west\-1\)
South America \(São Paulo\) Region \(sa\-east\-1\)
Be sure to complete all of the steps in this tutorial with one of these AWS regions selected\.

Follow the first two procedures in the [Git with AWS CodeCommit Tutorial](http://docs.aws.amazon.com/codecommit/latest/userguide/getting-started.html) in the *AWS CodeCommit User Guide*:

+ [Step 1: Create an AWS CodeCommit Repository](http://docs.aws.amazon.com/codecommit/latest/userguide/getting-started.html#getting-started-create-repo)

+ [Step 2: Create a Local Repo](http://docs.aws.amazon.com/codecommit/latest/userguide/getting-started.html#getting-started-set-up-folders)
**Note**  
For information about connecting to a local repo you create, see [Connect to an AWS CodeCommit Repository](http://docs.aws.amazon.com/codecommit/latest/userguide/how-to-connect.html)\.

After you complete these two procedures, return to this page and continue to the next step\. Do not continue to the third step in the AWS CodeCommit tutorial\. You will complete different steps in this tutorial instead\.

## Step 2: Add Sample Code to Your AWS CodeCommit Repository<a name="codecommit-add-code"></a>

In this step, you will download code for a sample application that was created for an AWS CodeDeploy sample walkthrough, and add it to your AWS CodeCommit repository\.

1. Download the following file: 

   +  [SampleApp\_Linux\.zip](https://s3.amazonaws.com/aws-codedeploy-us-east-1/samples/latest/SampleApp_Linux.zip)\. 

1. Unzip the files from [SampleApp\_Linux\.zip](https://s3.amazonaws.com/aws-codedeploy-us-east-1/samples/latest/SampleApp_Linux.zip) into the local directory you created in the previous procedure \(for example, `/tmp/my-demo-repo` or `c:\temp\my-demo-repo`\)\. 

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

1. Run the following command to push the files from your local repo to your AWS CodeCommit repository:

   ```
   git push
   ```

1. The files you downloaded and added to your local repo have now been added to the `master` branch in your AWS CodeCommit `MyDemoRepo` repository and are ready to be included in a pipeline\.

## Step 3: Create an Amazon EC2 Instance and Install the AWS CodeDeploy Agent<a name="codecommit-create-deployment"></a>

In this step, you will create the Amazon EC2 instance to which you will deploy a sample application\. As part of this process, you will install the AWS CodeDeploy agent on the instance\. The AWS CodeDeploy agent is a software package that enables an instance to be used in AWS CodeDeploy deployments\.

**To launch an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the console dashboard, choose **Launch Instance**\.

1. On the **Step 1: Choose an Amazon Machine Image \(AMI\)** page, locate the row for the HVM edition of the Amazon Linux AMI, and then choose **Select**\. \(This AMI is labeled "Free tier eligible" and can be found at the top of the list\.\)
**Note**  
These basic configurations, called *Amazon Machine Images \(AMIs\)*, serve as templates for your instance\. This tutorial can be completed with any of the free tier eligible AMIs\. For simplicity, we will use the HVM edition of the Amazon Linux AMI\.

1. On the **Step 2: Choose an Instance Type** page, choose the free tier eligible `t2.micro` type as the hardware configuration for your instance, and then choose **Next: Configure Instance Details**\.

1. On the **Step 3: Configure Instance Details** page, do the following:

   + In **Number of instances**, enter `1`\.

   + In **Auto\-assign Public IP**, choose **Enable**\.

   + In **IAM role**, choose an IAM role that has been configured for use as an IAM instance profile for use with AWS CodeDeploy\. If you do not have an IAM instance profile, choose **Create new IAM role** and follow the instructions in [Create an IAM Instance Profile for Your Amazon EC2 Instances](http://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-iam-instance-profile.html)\.
**Note**  
For the purposes of this tutorial, you can use the following unrestricted policy in your IAM instance profile for AWS CodeDeploy\. For pipelines you use in your development workflows, you might create a more restrictive bucket policy\.  

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

1. On the **Step 3: Configure Instance Details** page, expand **Advanced Details**, and in the **User data** field, type the following:

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

   This code will install the AWS CodeDeploy agent on your instance as it is created\. If you prefer, you can [connect to your Linux instance using SSH](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) and [install the AWS CodeDeploy agent manually](http://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-run-agent.html#how-to-run-agent-install-linux) after the instance is created\.

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

   + If you have not created a key pair yet, select **Create a new key pair**, enter a name for the key pair, and then choose **Download Key Pair**\. This is your only chance to save the private key file\. Be sure to download it\. Save the private key file in a safe place\. You'll need to provide the name of your key pair when you launch an instance; you'll need to provide the corresponding private key each time you connect to the instance\. For more information, see [Amazon EC2 Key Pairs](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.
**Warning**  
Don't select the **Proceed without a key pair** option\. If you launch your instance without a key pair, you can't connect to it if you need to troubleshoot issues with the AWS CodeDeploy agent\.

   When you are ready, select the acknowledgement check box, and then choose **Launch Instances**\. 

1. Choose **View Instances** to close the confirmation page and return to the console\.

1. You can view the status of the launch on the **Instances** page\. When you launch an instance, its initial state is `pending`\. After the instance starts, its state changes to `running`, and it receives a public DNS name\. \(If the **Public DNS** column is not displayed, choose the **Show/Hide** icon, and then select **Public DNS**\.\)

1. It can take a few minutes for the instance to be ready for you to connect to it\. Check that your instance has passed its status checks\. You can view this information in the **Status Checks** column\.

If you want to confirm that the AWS CodeDeploy agent is configured correctly, you can [connect to your Linux instance using SSH](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) and then [verify the AWS CodeDeploy agent is running](http://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-run-agent.html#how-to-run-agent-verify-linux)\.

## Step 4: Create an Application in AWS CodeDeploy<a name="codecommit-create-codedeploy-app"></a>

In AWS CodeDeploy, an *application* is an identifier, in the form of a name, for the code you want to deploy\. AWS CodeDeploy uses this name to ensure the correct combination of revision, deployment configuration, and deployment group are referenced during a deployment\. You will select the name of the AWS CodeDeploy application you create in this step when you create your pipeline later in this tutorial\.

**To create an application in AWS CodeDeploy**

1. Open the AWS CodeDeploy console at [https://console\.aws\.amazon\.com/codedeploy](https://console.aws.amazon.com/codedeploy)\.

1. If the **Applications** page does not appear, on the AWS CodeDeploy menu, choose **Applications**\.

1. Choose **Create application**\.

1. In the **Application name** box, type `MyDemoApplication`\. 

1. In the **Deployment group name** box, type `MyDemoDeploymentGroup`\.

1. In the **Search by tags** list, select the Amazon EC2 tag type, choose **Name** in the **Key** box, and in the **Value** box, type `MyCodePipelineDemo`\. 
**Important**  
You must choose the same value for the **Name** key here that you assigned to your Amazon EC2 instance when you created it\. If you tagged your instance with something other than `MyCodePipelineDemo`, be sure to use it here\.

1. In the **Deployment configuration** list, choose `CodeDeployDefault.OneAtaTime`\.

1. In the **Service role ARN** box, choose an Amazon Resource Name \(ARN\) for a service role that trusts AWS CodeDeploy with, at minimum, the trust and permissions described in [Create a Service Role for AWS CodeDeploy](http://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-service-role.html)\. To get the service role ARN, see [Get the Service Role ARN \(Console\)](http://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-service-role.html#how-to-create-service-role-get-role-console)\.

1. Choose **Create application**\. 

## Step 5: Create Your First Pipeline in AWS CodePipeline<a name="codecommit-create-pipeline"></a>

You're now ready to create and run your first pipeline\.

**To create an AWS CodePipeline automated release process**

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codepipeline](http://console.aws.amazon.com/codepipeline)\.

1. On the introductory page, choose **Get started**\.

   If you see the **All pipelines** page, choose **Create pipeline**\.

1. In **Step 1: Name**, in **Pipeline name**, type **MyFirstPipeline**, and then choose **Next step**\.
**Note**  
If you choose another name for your pipeline, be sure to use it instead of *MyFirstPipeline* in the remaining steps of this tutorial\. After you create a pipeline, you cannot change its name\. Pipeline names are subject to some limitations\. For more information, see [Limits in AWS CodePipeline](limits.md)\. 

1. In **Step 2: Source**, in **Source provider**, choose AWS CodeCommit\. In **Repository name**, choose the name of the AWS CodeCommit repository you created in [Step 1: Create an AWS CodeCommit Repository and Local Repo](#codecommit-create-repository)\. In **Branch name**, choose the name of the branch that contains your latest code update\. Unless you created a different branch on your own, only `master` will be available\. 

   Choose **Next step**\.  
![\[The Step 2: Source page in the AWS CodePipeline pipeline
              wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-wizard-source-codecommit.png)![\[The Step 2: Source page in the AWS CodePipeline pipeline
              wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 2: Source page in the AWS CodePipeline pipeline
              wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   After you select the repository name and branch, you'll see a message showing the Amazon CloudWatch Events rule that will be created for this pipeline\. 

   Under **Change detection options**, leave the defaults\. This allows AWS CodePipeline to use Amazon CloudWatch Events to detect changes in your source repository\.

1. In **Step 3: Build**, choose **No Build**, and then choose **Next step**\.
**Note**  
In this tutorial, you are deploying code that requires no build service\.

1. In **Step 4: Deploy**, in **Deployment provider**, choose **AWS CodeDeploy**\. In **Application name**, type **MyDemoApplication**, or choose the **Refresh** button, and then choose the application name from the list\. In **Deployment group**, type **MyDemoDeploymentGroup**, or choose it from the list, and then choose **Next step**\.   
![\[The Step 4: Deploy page in the AWS CodePipeline pipeline
              wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-wizard-deploy.png)![\[The Step 4: Deploy page in the AWS CodePipeline pipeline
              wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 4: Deploy page in the AWS CodePipeline pipeline
              wizard\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
**Note**  
The name "Staging" is the name given by default to the stage created in the **Step 4: Deploy** step, just as "Source" is the name given to the first stage of the pipeline\.

1. In **Step 5: Service Role**, you will choose the IAM role to give AWS CodePipeline permission to use resources in your account\. Service role creation is only required the first time you create a pipeline in AWS CodePipeline\. 

   If you have not already created a service role for AWS CodePipeline:

   1. Choose **Create role**\. 

   1. On the IAM console page that describes the AWS\-CodePipeline\-Service role that will be created for you, choose **Allow**\. After you create the role, AWS\-CodePipeline\-Service will appear in **Role name** on the **Step 5: Service Role** page\. 

   1. Choose **Next step**\.

   If you already have a service role for AWS CodePipeline, you must ensure that it includes the permissions needed to work with AWS CodeCommit\. If your service role was created after April 18, 2016, it includes the necessary permissions\. If it was created on or before April 18, 2016, you may need to follow these steps:

   1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

   1. In the IAM console, in the navigation pane, choose **Roles**, and then choose your `AWS-CodePipeline-Service` role from the list of roles\.

   1. On the **Permissions** tab, in **Inline Policies**, in the row for your service role policy, choose **Edit Policy**\.
**Note**  
Your service role has a name in a format similar to `oneClick_AWS-CodePipeline-1111222233334`\.

   1. In the **Policy Document** box, add the following to your policy statement:

      ```
      {
        "Action": [  
            "codecommit:GetBranch",
            "codecommit:GetCommit",
            "codecommit:UploadArchive",
            "codecommit:GetUploadArchiveStatus",      
            "codecommit:CancelUploadArchive"
                  ],
        "Resource": "*",
        "Effect": "Allow"
      },
      ```

      The permissions might appear in your policy document like this after you add them:  
![\[Permissions for AWS CodeCommit in the Policy Editor\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/getting-started-cc-codecommit-Policy-Action.png)![\[Permissions for AWS CodeCommit in the Policy Editor\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Permissions for AWS CodeCommit in the Policy Editor\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   1. Choose **Validate Policy** to ensure the policy contains no errors\. When the policy is error\-free, choose **Apply Policy**\.

   1. In the **Step 5: Service Role** page, in **Role name**, choose the name of your service role for AWS CodePipeline\.

      Because the drop\-down list will display all IAM service roles associated with your account, if you choose a name different from the default, be sure that the name is recognizable as the service role for AWS CodePipeline\. 

   1. Choose **Next step**\.

1. In **Step 6: Review**, review the information, and then choose **Create pipeline**\.

1. The pipeline automatically starts to run\. You can view progress and success and failure messages as the AWS CodePipeline sample deploys the web page to the Amazon EC2 instance in the AWS CodeDeploy deployment\.  
![\[A view of a pipeline beginning to run in the AWS CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-firstpipeline-codecommit.png)![\[A view of a pipeline beginning to run in the AWS CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A view of a pipeline beginning to run in the AWS CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

Congratulations\! You just created a simple pipeline in AWS CodePipeline\. The pipeline has two stages: a source stage named **Source**, which detects changes in the sample application stored in the AWS CodeCommit repository and pulls those changes into the pipeline, and a **Staging** stage that deploys those changes to the Amazon EC2 instance using AWS CodeDeploy\. Next, you will verify the results\.

**To verify that your pipeline ran successfully**

1. View the initial progress of the pipeline\. The status of each stage will change from **No executions yet** to **In Progress**, and then to either **Succeeded** or **Failed**\. The pipeline should complete the first run within a few minutes\.

1. After the pipeline status displays **Succeeded**, in the status area for the **Staging** stage, choose **Details**\. 

1. In the AWS CodeDeploy console, in **Deployment Details**, in **Instance ID**, choose the instance ID of the successfully deployed instance\. 

1. On the **Description** tab, in **Public DNS**, copy the address, and then paste it into the address bar of your web browser\.

   This is the sample application you downloaded and pushed to your AWS CodeCommit repository\.  
![\[The sample web page application that was pushed to your AWS CodeCommit
              repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-demo-success-message-codedeploy.png)![\[The sample web page application that was pushed to your AWS CodeCommit
              repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The sample web page application that was pushed to your AWS CodeCommit
              repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

For more information about stages, actions, and how pipelines work, see [AWS CodePipeline Concepts](concepts.md)\.

## Step 6: Modify Code in Your AWS CodeCommit Repository<a name="codecommit-push-code"></a>

In this step, you will make changes to the HTML file that is part of the sample AWS CodeDeploy application you deployed to your Amazon EC2 instance\. When your pipeline runs again later in this tutorial, the changes you make will be visible at the `http://PublicDNS` URLs\.

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

1. Revise the contents of the `index.html` file to change the background color and some of the text on the web page, and then save the file\. 

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
     <div align="center"><h2>This application was updated using AWS CodePipeline, AWS CodeCommit, and AWS CodeDeploy.</h2></div>
     <div align="center">
       <p>Learn more:</p> 
       <p><a href="http://docs.aws.amazon.com/codepipeline/latest/userguide/">AWS CodePipeline User Guide</a></p>
       <p><a href="http://docs.aws.amazon.com/codecommit/latest/userguide/">AWS CodeCommit User Guide</a></p>
       <p><a href="http://docs.aws.amazon.com/codedeploy/latest/userguide/">AWS CodeDeploy User Guide</a></p>
     </div>
   </body>
   </html>
   ```

1. Commit and push your changes to your AWS CodeCommit repository by running the following commands, one at a time:

   ```
   git commit -am "Updated sample application files"
   ```

   ```
   git push
   ```

Your pipeline is configured to run whenever code changes are made to your AWS CodeCommit repository\. 

**To verify your pipeline ran successfully**

1. View the initial progress of the pipeline\. The status of each stage will change from **No executions yet** to **In Progress**, and then to either **Succeeded** or **Failed**\. The pipeline should complete within a few minutes\.

1. After the action status displays **Succeeded**, in the status area for the **Staging** stage, choose **Details**\. 

1. In the **Deployment Details** section, in **Instance ID**, choose the instance ID of the instance\. 

1. On the **Description** tab, in **Public DNS**, copy the address, and then paste it into the address bar of your web browser\.

   The updated web page will be displayed:  
![\[The updated sample web page application that was pushed to your AWS CodeCommit
              repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-demo-success-message-codecommit.png)![\[The updated sample web page application that was pushed to your AWS CodeCommit
              repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The updated sample web page application that was pushed to your AWS CodeCommit
              repository.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

For more information about stages, actions, and how pipelines work, see [AWS CodePipeline Concepts](concepts.md)\.

## Step 7: Optional Stage Management Tasks<a name="codecommit-optional-tasks"></a>

If you want to gain more experience working with stages before you end the tutorial, you can follow two additional procedures in the [Tutorial: Create a Simple Pipeline \(Amazon S3 Bucket\)](tutorials-simple-s3.md)\.

+ [Step 4: Add Another Stage to Your Pipeline](tutorials-simple-s3.md#s3-add-stage)

+ Disable and Enable Transitions Between Stages in AWS CodePipeline
**Note**  
In step 4 of the second procedure, instead of uploading your sample to an Amazon S3 bucket again, as described, make a change to the sample app in your local repo and push it to your AWS CodeCommit repository\.

## Step 8: Clean Up Resources<a name="codecommit-clean-up"></a>

You can use some of the resources you created in this tutorial for the next tutorial, \. For example, you can reuse the AWS CodeDeploy application and deployment\. However, after you complete this and any other tutorials, you should delete the pipeline and the resources it uses, so that you will not be charged for the continued use of those resources\. First, delete the pipeline, then the AWS CodeDeploy application and its associated Amazon EC2 instance, and finally, the AWS CodeCommit repository\.

**To clean up the resources used in this tutorial**

1. To clean up your AWS CodePipeline resources, follow the instructions in Delete a Pipeline in AWS CodePipeline\.

1. To clean up your AWS CodeDeploy resources, follow the instructions in [Clean Up Deployment Walkthrough Resources](http://docs.aws.amazon.com/codedeploy/latest/userguide/tutorials-simple-s3alkthrough.html#tutorials-simple-s3alkthrough-clean-up)\.

1. To delete the AWS CodeCommit repository, follow the instructions in [Delete an AWS CodeCommit Repository](http://docs.aws.amazon.com/codecommit/latest/userguide/how-to-delete-repository.html)\.