# Tutorial: Create a Simple Pipeline (CodeCommit Repository)<a name="tutorials-simple-codecommit"></a>

In this tutorial, you use CodePipeline to deploy code that is maintained in a CodeCommit repository to a single Amazon EC2 instance. When you push a change to the CodeCommit repository, your CodePipeline pipeline will trigger. The pipeline will deploy your changes to an Amazon EC2 instance using CodeDeploy as the deployment service.

The easiest way to get started with AWS CodePipeline is to use the **Create Pipeline** wizard in the CodePipeline console to create a simple pipeline. 

**Note**  
Before you begin, make sure you've set up your Git client to work with CodeCommit: [Setting up for AWS CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up.html)

## Step 1: Create a CodeCommit Repository<a name="codecommit-create-repository"></a>

To start this tutorial, you create a repository in CodeCommit. Your pipeline gets source code from this repository when it runs. You also create a local repository where you maintain and update code before you push it to the CodeCommit repository.

1. Open the [CodeCommit console](https://console.aws.amazon.com/codesuite/codecommit/home).

1. In the region selector, choose the AWS Region where you want to create the repository. For more information, see [Regions and Git Connection Endpoints](regions.md).

1. On the **Repositories** page, choose **Create repository**. 

1. On the **Create repository** page, in **Repository name**, enter a name for your repository (for example, **MyDemoRepo**).

1. Choose **Create**. 

**Note**  
The remaining steps in this tutorial use `MyDemoRepo` for the name of your CodeCommit repository. If you choose a different name, be sure to use it throughout this tutorial.

## Step 2: Create a Local Repository<a name="codecommit-set-up-folders"></a>

In this step, you set up a local repository on your local machine to connect to your repository. To do this, you select a directory on your local machine that represents the local repo. You use Git to clone and initialize a copy of your empty CodeCommit repository inside of that directory. Then you specify the user name and email address used to annotate your commits.

1. With your new repository opened in the console, choose **Clone URL** on the top right of the page, and then choose "Clone SSH". The address to clone your Git repository is copied to your clipboard.

1. Run the following command to clone the repository, replacing the SSH address with the one you copied in the previous step. This command will create a directory called MyDemoRepo, inside which we will copy a sample application.

   ```
   git clone ssh://git-codecommit.us-west-2.amazonaws.com/v1/repos/MyDemoRepo
   ```

## Step 2: Add Sample Code to Your CodeCommit Repository<a name="codecommit-add-code"></a>

In this step, you download code for a sample application that was created for a CodeDeploy sample walkthrough, and add it to your CodeCommit repository.

1. Download the following file: 
   +  [SampleApp\_Linux.zip](https://s3.amazonaws.com/aws-codedeploy-us-east-1/samples/latest/SampleApp_Linux.zip). 

1. Unzip the files and push the files to the root of your test repository (the directory created in the previous step).

   For this tutorial, unzip the files from [SampleApp\_Linux.zip](https://s3.amazonaws.com/aws-codedeploy-us-east-1/samples/latest/SampleApp_Linux.zip) into the local directory you created in the previous procedure (for example, `/tmp/MyDemoRepo` or `c:\temp\MyDemoRepo`). 

   Be sure to place the files directly into your local repository. Do not include a `SampleApp_Linux` folder. On your local Linux, macOS, or Unix machine, for example, your directory and file hierarchy should look like this:

   ```
   /tmp
     |MyDemoRepo
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
   (For Linux, macOS, or Unix) cd MyDemoRepo
   (For Windows) cd c:\temp\MyDemoRepo
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

1. The files you downloaded and added to your local repo have now been added to the `master` branch in your CodeCommit `MyDemoRepo` repository and are ready to be included in a pipeline.

## Step 3: Create an Amazon EC2 Linux Instance and Install the CodeDeploy Agent<a name="codecommit-create-deployment"></a>

In this step, you create the Amazon EC2 instance where you deploy a sample application. As part of this process, you install the CodeDeploy agent on the EC2 instance. The CodeDeploy agent is a software package that enables an instance to be used in CodeDeploy deployments. You will also attach an IAM role to the instance (known as an instance role) to allow it to fetch files that the CodeDeploy agent uses to deploy your application.

**To create an instance role**

1. Open the [IAM console](https://console.aws.amazon.com/iam/home).

1. From the console dashboard, choose **Roles**.

1. Choose **Create role**.

1. Under **Choose the service that will use this role**, select **EC2**, and then choose **Next: Permissions**.

1. Search for and select the policy named **AmazonEC2RoleforAWSCodeDeploy**, and then choose **Next: Tags**.

1. Choose **Next: Review**. Give the role a name (for example, **EC2InstanceRole**), and then choose **Create role**.

**To launch an instance**

1. Open the [Amazon EC2 console](https://console.aws.amazon.com/ec2/).

1. From the console dashboard, choose **Launch Instance**.

1. On the **Step 1: Choose an Amazon Machine Image (AMI)** page, locate the row for the HVM edition of the Amazon Linux AMI, and then choose **Select**. (This AMI is labeled "Free tier eligible" and can be found at the top of the list.)

1. On the **Step 2: Choose an Instance Type** page, choose the free tier eligible `t2.micro` type as the hardware configuration for your instance, and then choose **Next: Configure Instance Details**.

1. On the **Step 3: Configure Instance Details** page, do the following:
   + In **Number of instances**, enter `1`.
   + In **Auto-assign Public IP**, choose **Enable**.
   + In **IAM role**, choose the IAM role you created in the last step (eg., **EC2InstanceRole**).
   * Expand **Advanced Details**, and in the **User data** field, enter the following:

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

   This code installs the CodeDeploy agent on your instance as it is created.

1. Leave the rest of the items on the **Step 3: Configure Instance Details** page unchanged. Choose **Next: Add Storage**, leave the **Step 4: Add Storage** page unchanged, and then choose **Next: Add Tags**.

1. Click **Add Tag**. In the **Key** field, type `MyCodePipelineDemo`, and then choose **Next: Configure Security Group**. Later, we will create a CodeDeploy application that will deploy the sample application to this instance. CodeDeploy will select instances to deploy based on the tags that are attached to instances.

1. On the **Step 6: Configure Security Group** page, do the following: 
   + Next to **Assign a security group**, choose **Create a new security group**.
   + In the row for **SSH**, under **Source**, choose **My IP**.
   + Choose **Add Rule**, choose **HTTP**, and then under **Source**, choose **My IP**.

1. Choose **Review and Launch**.

1. On the **Review Instance Launch** page, choose **Launch**, and then do one of the following when prompted for a key pair:
   + If you already have a key pair to use with Amazon EC2 instances, select **Choose an existing key pair**, and then select your key pair.
   + If you have not created a key pair yet, select **Create a new key pair**, enter a name for the key pair, and then choose **Download Key Pair**. This is your only chance to save the private key file. Be sure to download it. Save the private key file in a safe place. You must provide the name of your key pair when you launch an instance. You must provide the corresponding private key each time you connect to the instance. For more information, see [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html).
**Warning**  
If you select **Proceed without a key pair**, you won't be able to SSH into the instance if you need to troubleshoot issues with the CodeDeploy agent.

   When you are ready, select the acknowledgement check box, and then choose **Launch Instances**. 

1. Choose **View Instances** to close the confirmation page and return to the console.

1. You can view the status of the launch on the **Instances** page. When you launch an instance, its initial state is `pending`. After the instance starts, its state changes to `running`, and it receives a public DNS name. (If the **Public DNS** column is not displayed, choose the **Show/Hide** icon, and then select **Public DNS**.)

1. It can take a few minutes for the instance to be ready for you to connect to it. Check that your instance has passed its status checks. You can view this information in the **Status Checks** column.

## Step 4: Create an Application in CodeDeploy<a name="codecommit-create-codedeploy-app"></a>

In CodeDeploy, an [*application*](https://docs.aws.amazon.com/codedeploy/latest/userguide/applications.html) is a resource that contains the software application you want to deploy. Later, you will use this application with CodePipeline to automate deployments of the sample application to your EC2 instance.

**To create a service role**
First, we need to create a role that the CodeDeploy service can use to manage your instances, to allow it to perform a deployment.

1. Open the [IAM console](https://console.aws.amazon.com/iam/home).

1. From the console dashboard, choose **Roles**.

1. Choose **Create role**.

1. Under **Choose the service that will use this role**, choose **CodeDeploy**. Under **Select your use case**, choose **CodeDeploy**.

1. Choose **Next: Permissions**, **Next: Tags** and **Next: Review**.

1. Give the role a name (for example, **CodeDeployRole**), and then choose **Create role**


**To create an application in CodeDeploy**

1. Open the [CodeDeploy console](https://console.aws.amazon.com/codedeploy).

1. If the **Applications** page does not appear, on the AWS CodeDeploy menu, choose **Applications**.

1. Choose **Create application**.

1. Leave **Custom application** selected. In **Application name**, enter `MyDemoApplication`. 

1. In **Compute Platform**, choose **EC2/On-premises**.

1. Choose **Create application**.

**To create a deployment group in CodeDeploy**
A [deployment group](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-groups.html) is a resource which defines deployment-related settings, like which instances to deploy to and how fast to deploy.

1. On the page showing your application, choose **Create deployment group**.

1. In **Deployment group name**, enter `MyDemoDeploymentGroup`.

1. In **Service Role**, choose the service role we created in the **To create a service role** step (eg., **EC2InstanceRole**).

1. Under **Deployment type**, choose **In-place**.

1. Under **Environment configuration**, choose **Amazon EC2 Instances**. Type in the tag key you tagged the instance with in the **Key** box (eg., MyCodePipelineDemo).

1. Under **Deployment configuration**, choose `CodeDeployDefault.OneAtaTime`.

1. Under **Load Balancer**, clear **Enable load balancing**.

1. Expand the **Advanced** section. Under **Alarms**, choose **Ignore alarm configuration**.

1. Choose **Create deployment group**.

## Step 5: Create Your First Pipeline in CodePipeline

You're now ready to create and run your first pipeline.

**To create a CodePipeline automated release process**

1. Open the [CodePipeline console](http://console.aws.amazon.com/codesuite/codepipeline/home).

1. Choose **Create pipeline**.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyFirstPipeline**.

1. In **Service role**, choose **New service role** to allow CodePipeline to create a new service role in IAM.

1. In **Artifact store**, choose **Default location**.

1. In **Step 2: Add source stage**, in **Source provider**, choose **AWS CodeCommit**. In **Repository name**, choose the name of the CodeCommit repository you created in [Step 1: Create a CodeCommit Repository and Local Repository](#codecommit-create-repository). In **Branch name**, choose **master**. Choose **Next step**.  
![The Step 2: Source page in the CodePipeline pipeline wizard](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-wizard-source-codecommit-pol.png)

   After you select the repository name and branch, a message is displayed showing the Amazon CloudWatch Events rule to be created for this pipeline. 

   Under **Change detection options**, leave the defaults. This allows CodePipeline to use Amazon CloudWatch Events to detect changes in your source repository.

   Choose **Next**.

1. In **Step 3: Add build stage**, choose **Skip build stage**, and then accept the warning message by choosing **Skip** again. Choose **Next**.
**Note**  
In this tutorial, you are deploying code that requires no build service, so we are skipping this step, but if your source code needs to be built before being deployed to instances, you could configure [CodeBuild](https://aws.amazon.com/codebuild/) in this step.

1. In **Step 4: Add deploy stage**, in **Deploy provider**, choose **AWS CodeDeploy**. In **Application name**, choose **MyDemoApplication**. In **Deployment group**, choose **MyDemoDeploymentGroup**, and then choose **Next step**.
![The Step 4: Deploy page in the CodePipeline pipeline wizard](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-wizard-deploy-pol.png)

1. In **Step 5: Review**, review the information, and then choose **Create pipeline**.

1. The pipeline will start running after it is created. It will download the code from your CodeCommit repository, and create a CodeDeploy deployment to your EC2 instance. You can view progress and success and failure messages as the CodePipeline sample deploys the webpage to the Amazon EC2 instance in the CodeDeploy deployment.
![A view of a pipeline beginning to run in the CodePipeline console.](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-firstpipeline-codecommit-pol.png)

Congratulations\! You just created a simple pipeline in CodePipeline. The pipeline has two stages:
+ A source stage (**Source**) that detects changes in the sample application stored in the CodeCommit repository and pulls those changes into the pipeline. 
+ A deployment stage (**Deploy**) that deploys those changes to the Amazon EC2 instance using CodeDeploy.

Next, you verify the results.

**To verify that your pipeline ran successfully**

1. View the initial progress of the pipeline. The status of each stage changes from **No executions yet** to **In Progress**, and then to either **Succeeded** or **Failed**. The pipeline should complete the first run within a few minutes.

1. After **Succeeded** is displayed for the pipeline status, in the status area for the **Staging** stage, choose **Details**. This opens the CodeDeploy console.

1. Choose your application in the list. On the **Deployment group** tab, under **Deployment lifecycle events**, choose the instance ID. This opens the EC2 console.

1. On the **Description** tab, in **Public DNS**, copy the address (eg., `ec2-192-0-2-1.us-west-2.compute.amazonaws.com`), and then paste it into the address bar of your web browser.

   This is the sample application you downloaded and pushed to your CodeCommit repository.  
![The sample web page application that was pushed to your CodeCommit repository.](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-demo-success-message-codedeploy.png)

## Step 6: Modify Code in Your CodeCommit Repository<a name="codecommit-push-code"></a>

Your pipeline is configured to run whenever code changes are made to your CodeCommit repository. In this step, you make changes to the HTML file that is part of the sample CodeDeploy application in the CodeCommit repository. When you push these changes, your pipeline will automatically run again, and the changes you make will be visible at the web address you accessed previously.

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

1. Revise the contents of the `index.html` file to change the background color and some of the text on the webpage, and then save the file. 

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

1. View the initial progress of the pipeline. The status of each stage changes from **Succeeded** to **In Progress**, and then to either **Succeeded** or **Failed**. The pipeline should complete within a few minutes.

1. After **Succeeded** is displayed for the action status, in the status area for the **Deploy** stage, choose **Details**. This opens the CodeDeploy console.

1. Refresh the demo page you accessed earlier in your browser.

   The updated webpage is displayed:  
![The updated sample webpage application that was pushed to your CodeCommit repository.](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-demo-success-message-codecommit.png)

## Step 7: Read more about how CodePipeline works<a name=""></a>

* For more information about stages, actions, and how pipelines work, see [CodePipeline Concepts](concepts.md).
* For information about what kinds of actions you can perform using CodePipeline, see [Integrations with CodePipeline Action Types](intergrations-action-type.md).
* Try this more advanced tutorial which creates a multi-stage pipeline that includes a step which builds code before it's deployed: [Tutorial: Create a Four-Stage Pipeline](tutorials-four-stage-pipeline.md)

## Step 8: Clean Up Resources<a name="codecommit-clean-up"></a>

You can use some of the resources you created in this tutorial for other tutorials on this site. For example, you can reuse the CodeDeploy application and deployment. However, after you complete this and any other tutorials, you should delete the pipeline and the resources it uses, so that you are not charged for the continued use of those resources. First, delete the pipeline, then the CodeDeploy application and its associated Amazon EC2 instance, and finally, the CodeCommit repository.

**To clean up the resources used in this tutorial**

1. To clean up your CodePipeline resources, follow the instructions in [Delete a Pipeline in AWS CodePipeline](pipelines-delete.md).

1. To clean up your CodeDeploy resources, follow the instructions in [Clean Up Deployment Walkthrough Resources](https://docs.aws.amazon.com/codedeploy/latest/userguide/tutorials-simple-s3alkthrough.html#tutorials-simple-s3alkthrough-clean-up).

1. To delete the CodeCommit repository, follow the instructions in [Delete a CodeCommit Repository](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-delete-repository.html).