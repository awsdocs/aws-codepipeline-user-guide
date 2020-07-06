# Tutorial: Create a four\-stage pipeline<a name="tutorials-four-stage-pipeline"></a>

Now that you've created your first pipeline in [Tutorial: Create a simple pipeline \(S3 bucket\)](tutorials-simple-s3.md) or [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md), you can start creating more complex pipelines\. This tutorial will walk you through the creation of a four\-stage pipeline that uses a GitHub repository for your source, a Jenkins build server to build the project, and a CodeDeploy application to deploy the built code to a staging server\. After the pipeline is created, you will edit it to add a stage with a test action to test the code, also using Jenkins\. 

Before you can create this pipeline, you must configure the required resources\. For example, if you want to use a GitHub repository for your source code, you must create the repository before you can add it to a pipeline\. As part of setting up, this tutorial walks you through setting up Jenkins on an EC2 instance for demonstration purposes\. 

**Important**  
Many of the actions you add to your pipeline in this procedure involve AWS resources that you need to create before you create the pipeline\. AWS resources for your source actions must always be created in the same AWS Region where you create your pipeline\. For example, if you create your pipeline in the US East \(Ohio\) Region, your CodeCommit repository must be in the US East \(Ohio\) Region\.   
You can add cross\-region actions when you create your pipeline\. AWS resources for cross\-region actions must be in the same AWS Region where you plan to execute the action\. For more information, see [Add a cross\-Region action in CodePipeline](actions-create-cross-region.md)\.

Before you begin this tutorial, you should have already completed the general prerequisites in [Getting started with CodePipeline](getting-started-codepipeline.md)\.

**Topics**
+ [Step 1: Complete prerequisites](#tutorials-four-stage-pipeline-prerequisites)
+ [Step 2: Create a pipeline in CodePipeline](#tutorials-four-stage-pipeline-pipeline-create)
+ [Step 3: Add another stage to your pipeline](#tutorials-four-stage-pipeline-add-stage)
+ [Step 4: Clean up resources](#tutorials-four-stage-pipeline-clean-up)

## Step 1: Complete prerequisites<a name="tutorials-four-stage-pipeline-prerequisites"></a>

To integrate with Jenkins, AWS CodePipeline requires you to install the CodePipeline Plugin for Jenkins on any instance of Jenkins you want to use with CodePipeline\. You should also configure a dedicated IAM user to use for permissions between your Jenkins project and CodePipeline\. The easiest way to integrate Jenkins and CodePipeline is to install Jenkins on an EC2 instance that uses an IAM instance role that you create for Jenkins integration\. In order for links in the pipeline for Jenkins actions to successfully connect, you must configure proxy and firewall settings on the server or EC2 instance to allow inbound connections to the port used by your Jenkins project\. Make sure you have configured Jenkins to authenticate users and enforce access control before you allow connections on those ports \(for example, 443 and 8443 if you have secured Jenkins to only use HTTPS connections, or 80 and 8080 if you allow HTTP connections\)\. For more information, see [Securing Jenkins](https://wiki.jenkins.io/display/JENKINS/Securing+Jenkins)\.

**Note**  
This tutorial uses a code sample and configures build steps that convert the sample from Haml to HTML\. You can download the open\-source sample code from the GitHub repository by following the steps in [Copy or clone the sample into a GitHub repository](#tutorials-four-stage-pipeline-prerequisites-github)\. You will need the entire sample in your GitHub repository, not just the \.zip file\.   
This tutorial also assumes that:  
You are familiar with installing and administering Jenkins and creating Jenkins projects\.
You have installed Rake and the Haml gem for Ruby on the same computer or instance that hosts your Jenkins project\.
You have set the required system environment variables so that Rake commands can be run from the terminal or command line \(for example, on Windows systems, modifying the PATH variable to include the directory where you installed Rake\)\.

**Topics**
+ [Copy or clone the sample into a GitHub repository](#tutorials-four-stage-pipeline-prerequisites-github)
+ [Create an IAM role to use for Jenkins integration](#tutorials-four-stage-pipeline-prerequisites-jenkins-iam-role)
+ [Install and configure Jenkins and the CodePipeline Plugin for Jenkins](#tutorials-four-stage-pipeline-prerequisites-jenkins-configure)

### Copy or clone the sample into a GitHub repository<a name="tutorials-four-stage-pipeline-prerequisites-github"></a>

**To clone the sample and push to a GitHub repository**

1. Download the sample code from the GitHub repository, or clone the repositories to your local computer\. There are two sample packages: 
   + If you will be deploying your sample to Amazon Linux, RHEL, or Ubuntu Server instances, choose [aws\-codepipeline\-jenkins\-aws\-codedeploy\_linux\.zip](https://github.com/awslabs/aws-codepipeline-jenkins-aws-codedeploy_linux)\. 
   + If you will be deploying your sample to Windows Server instances, choose [AWSCodePipeline\-Jenkins\-AWSCodeDeploy\_Windows\.zip](https://github.com/awslabs/AWSCodePipeline-Jenkins-AWSCodeDeploy_windows)\.

1. From the repository, choose **Fork** to clone the sample repo into a repo in your Github account\. For more information, see the [GitHub documentation](https://help.github.com/articles/create-a-repo/)\.

### Create an IAM role to use for Jenkins integration<a name="tutorials-four-stage-pipeline-prerequisites-jenkins-iam-role"></a>

As a best practice, consider launching an EC2 instance to host your Jenkins server and using an IAM role to grant the instance the required permissions for interacting with CodePipeline\.

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the IAM console, in the navigation pane, choose **Roles**, and then choose **Create role**\.

1. Under **Select type of trusted entity**, choose **AWS service**\. Under **Choose the service that will use this role**, choose **EC2**\. Under **Select your use case**, choose **EC2**\. 

1. Choose **Next: Permissions**\. On the **Attach permissions policies** page, select the `AWSCodePipelineCustomActionAccess` managed policy, and then choose **Next: Tags**\. Choose **Next: Review**\.

1. On the **Review** page, in **Role name**, enter the name of the role to create specifically for Jenkins integration \(for example, *JenkinsAccess*\), and then choose **Create role**\.

When you create the EC2 instance where you will install Jenkins, in **Step 3: Configure Instance Details**, make sure you choose the instance role \(for example, *JenkinsAccess*\)\.

For more information about instance roles and Amazon EC2, see [IAM roles for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html), [Using IAM Roles to Grant Permissions to Applications Running on Amazon EC2 Instances](https://docs.aws.amazon.com/IAM/latest/UserGuide/roles-usingrole-ec2instance.html), and [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/roles-creatingrole-service.html)\.

### Install and configure Jenkins and the CodePipeline Plugin for Jenkins<a name="tutorials-four-stage-pipeline-prerequisites-jenkins-configure"></a>

**To install Jenkins and the CodePipeline Plugin for Jenkins**

1. Create an EC2 instance where you will install Jenkins, and in **Step 3: Configure Instance Details**, make sure you choose the instance role you created \(for example, *JenkinsAccess*\)\. For more information about creating EC2 instances, see [Launch an Amazon EC2 instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-instance_linux.html) in the *Amazon EC2 User Guide*\. 
**Note**  
If you already have Jenkins resources you want to use, you can do so, but you must create a special IAM user, apply the `AWSCodePipelineCustomActionAccess` managed policy to that user, and then configure and use the access credentials for that user on your Jenkins resource\. If you want to use the Jenkins UI to supply the credentials, configure Jenkins to only allow HTTPS\. For more information, see [Troubleshooting CodePipeline](troubleshooting.md)\.

1. Install Jenkins on the EC2 instance\. For more information, see the Jenkins documentation for [installing Jenkins](https://wiki.jenkins.io/display/JENKINS/Installing+Jenkins) and [starting and accessing Jenkins](https://wiki.jenkins.io/pages/viewpage.action?pageId=42469975), as well as [details of integration with Jenkins](integrations-action-type.md#JenkinsInt_2) in [Product and service integrations with CodePipeline](integrations.md)\.

1. Launch Jenkins, and on the home page, choose **Manage Jenkins**\.

1. On the **Manage Jenkins** page, choose **Manage Plugins**\.

1. Choose the **Available** tab, and in the **Filter** search box, enter **AWS CodePipeline**\. Choose **CodePipeline Plugin for Jenkins** from the list and choose **Download now and install after restart**\.

1. On the **Installing Plugins/Upgrades** page, select **Restart Jenkins when installation is complete and no jobs are running**\.

1. Choose **Back to Dashboard**\.

1. On the main page, choose **New Item**\.

1. In **Item Name**, enter a name for the Jenkins project \(for example, *MyDemoProject*\)\. Choose **Freestyle project**, and then choose **OK**\.
**Note**  
Make sure that the name for your project meets the requirements for CodePipeline\. For more information, see [Quotas in AWS CodePipeline](limits.md)\.

1. On the configuration page for the project, select the **Execute concurrent builds if necessary** check box\. In **Source Code Management**, choose **AWS CodePipeline**\. If you have installed Jenkins on an EC2 instance and configured the AWS CLI with the profile for the IAM user you created for integration between CodePipeline and Jenkins, leave all of the other fields empty\.

1. Choose **Advanced**, and in **Provider**, enter a name for the provider of the action as it will appear in CodePipeline \(for example, *MyJenkinsProviderName*\)\. Make sure that this name is unique and easy to remember\. You will use it when you add a build action to your pipeline later in this tutorial, and again when you add a test action\.
**Note**  
This action name must meet the naming requirements for actions in CodePipeline\. For more information, see [Quotas in AWS CodePipeline](limits.md)\.

1. In **Build Triggers**, clear any check boxes, and then select **Poll SCM**\. In **Schedule**, enter five asterisks separated by spaces, as follows:

   ```
   * * * * *
   ```

   This polls CodePipeline every minute\. 

1. In **Build**, choose **Add build step**\. Choose **Execute shell** \(Amazon Linux, RHEL, or Ubuntu Server\) **Execute batch command** \(Windows Server\), and then enter the following:

   ```
   rake
   ```
**Note**  
Make sure that your environment is configured with the variables and settings required to run rake; otherwise, the build will fail\.

1. Choose **Add post\-build action**, and then choose **AWS CodePipeline Publisher**\. Choose **Add**, and in **Build Output Locations**, leave the location blank\. This configuration is the default\. It will create a compressed file at the end of the build process\.

1. Choose **Save** to save your Jenkins project\.

## Step 2: Create a pipeline in CodePipeline<a name="tutorials-four-stage-pipeline-pipeline-create"></a>

In this part of the tutorial, you create the pipeline using the **Create Pipeline** wizard\. 

**To create a CodePipeline automated release process**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. If necessary, use the Region selector to change the Region to the one where your pipeline resources are located\. For example, if you created resources for the previous tutorial in `us-east-2`, make sure that the Region selector is set to US East \(Ohio\)\.

   For more information about the Regions and endpoints available for CodePipeline, see [AWS CodePipeline endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/codepipeline.html)\.

1. On the **Welcome** page, **Getting started** page, or the **Pipelines** page, choose **Create pipeline**\.

1. On the **Step 1: Choose pipeline settings** page, in **Pipeline name**, enter the name for your pipeline\.

1. In **Service role**, choose **New service role** to allow CodePipeline to create a service role in IAM\.

1. Leave the settings under **Advanced settings** at their defaults, and choose **Next**\.

1. In **Step 2: Add source stage**, in **Source provider**, choose **GitHub**, and then choose **Connect to GitHub**\. This will open a new browser window that will connect you to GitHub\. If prompted to sign in, provide your GitHub credentials\. 
**Important**  
Do not provide your AWS credentials on the GitHub website\.

   After you have selected GitHub, a message displays advising that CodePipeline will create a webhook in GitHub for your pipeline\.

   After you have connected to GitHub, choose the repository and branch where you pushed the sample you want to use for this tutorial \(aws\-codepipeline\-jenkins\-aws\-codedeploy\_linux\.zip or AWSCodePipeline\-Jenkins\-AWSCodeDeploy\_Windows\.zip\), and then choose **Next**\.
**Note**  
In GitHub, there is a limit to the number of OAuth tokens you can use for an application, such as CodePipeline\. If you exceed this limit, retry the connection to allow CodePipeline to reconnect by reusing existing tokens\. For more information, see [Pipeline error: I receive a pipeline error that says: "Could not access the GitHub repository" or "Unable to connect to the GitHub repository"](troubleshooting.md#troubleshooting-gs2)\.

1. In **Step 3: Add build stage**, choose **Add Jenkins**\. In **Provider name**, enter the name of the action you provided in the CodePipeline Plugin for Jenkins \(for example *MyJenkinsProviderName*\)\. This name must exactly match the name in the CodePipeline Plugin for Jenkins\. In **Server URL**, enter the URL of the EC2 instance where Jenkins is installed\. In **Project name**, enter the name of the project you created in Jenkins, such as *MyDemoProject*, and then choose **Next**\.

1. In **Step 4: Add deploy stage**, reuse the CodeDeploy application and deployment group you created in [Tutorial: Create a simple pipeline \(S3 bucket\)](tutorials-simple-s3.md)\. In **Deploy provider**, choose **CodeDeploy**\. In **Application name**, enter **CodePipelineDemoApplication**, or choose the refresh button, and then choose the application name from the list\. In **Deployment group**, enter **CodePipelineDemoFleet**, or choose it from the list, and then choose **Next**\.
**Note**  
You can use your own CodeDeploy resources or create new ones, but you might incur additional costs\.

1. In **Step 5: Review**, review the information, and then choose **Create pipeline**\.

1. The pipeline automatically starts and runs the sample through the pipeline\. You can view progress and success and failure messages as the pipeline builds the Haml sample to HTML and deploys it a webpage to each of the Amazon EC2 instances in the CodeDeploy deployment\.

## Step 3: Add another stage to your pipeline<a name="tutorials-four-stage-pipeline-add-stage"></a>

Now you will add a test stage and then a test action to that stage that uses the Jenkins test included in the sample to determine whether the webpage has any content\. This test is for demonstration purposes only\.

**Note**  
If you did not want to add another stage to your pipeline, you could add a test action to the Staging stage of the pipeline, before or after the deployment action\.

### Add a test stage to your pipeline<a name="tutorials-four-stage-pipeline-add-stage-console"></a>

**Topics**
+ [Look up the IP address of an instance](#tutorials-four-stage-pipeline-instance-ip-lookup)
+ [Create a Jenkins project for testing the deployment](#tutorials-four-stage-pipeline-create-jenkins-project)
+ [Create a fourth stage](#tutorials-four-stage-pipeline-create-fourth-stage)

#### Look up the IP address of an instance<a name="tutorials-four-stage-pipeline-instance-ip-lookup"></a>

**To verify the IP address of an instance where you deployed your code**

1. After **Succeeded** is displayed for the pipeline status, in the status area for the Staging stage, choose **Details**\. 

1. In the **Deployment Details** section, in **Instance ID**, choose the instance ID of one of the successfully deployed instances\. 

1. Copy the IP address of the instance \(for example, *192\.168\.0\.4*\)\. You will use this IP address in your Jenkins test\.

#### Create a Jenkins project for testing the deployment<a name="tutorials-four-stage-pipeline-create-jenkins-project"></a>

**To create the Jenkins project**

1. On the instance where you installed Jenkins, open Jenkins and from the main page, choose **New Item**\.

1.  In **Item Name**, enter a name for the Jenkins project \(for example, *MyTestProject*\)\. Choose **Freestyle project**, and then choose **OK**\.
**Note**  
Make sure that the name for your project meets the CodePipeline requirements\. For more information, see [Quotas in AWS CodePipeline](limits.md)\.

1. On the configuration page for the project, select the **Execute concurrent builds if necessary** check box\. In **Source Code Management**, choose **AWS CodePipeline**\. If you have installed Jenkins on an EC2 instance and configured the AWS CLI with the profile for the IAM user you created for integration between CodePipeline and Jenkins, leave all the other fields empty\. 
**Important**  
If you are configuring a Jenkins project and it is not installed on an Amazon EC2 instance, or it is installed on an EC2 instance that is running a Windows operating system, complete the fields as required by your proxy host and port settings, and provide the credentials of the IAM user you configured for integration between Jenkins and CodePipeline\.

1. Choose **Advanced**, and in **Category**, choose **Test**\. 

1. In **Provider**, enter the same name you used for the build project \(for example, *MyJenkinsProviderName*\)\. You will use this name when you add the test action to your pipeline later in this tutorial\.
**Note**  
This name must meet the CodePipeline naming requirements for actions\. For more information, see [Quotas in AWS CodePipeline](limits.md)\.

1. In **Build Triggers**, clear any check boxes, and then select **Poll SCM**\. In **Schedule**, enter five asterisks separated by spaces, as follows:

   ```
   * * * * *
   ```

   This polls CodePipeline every minute\. 

1. In **Build**, choose **Add build step**\. If you are deploying to Amazon Linux, RHEL, or Ubuntu Server instances, choose** Execute shell **\. Then enter the following, where the IP address is the address of the EC2 instance you copied earlier:

   ```
   TEST_IP_ADDRESS=192.168.0.4 rake test
   ```

   If you are deploying to Windows Server instances, choose **Execute batch command**, and then enter the following, where the IP address is the address of the EC2 instance you copied earlier:

   ```
   set TEST_IP_ADDRESS=192.168.0.4 rake test
   ```
**Note**  
The test assumes a default port of 80\. If you want to specify a different port, add a test port statement, as follows:   

   ```
   TEST_IP_ADDRESS=192.168.0.4 TEST_PORT=8000 rake test
   ```

1. Choose **Add post\-build action**, and then choose **AWS CodePipeline Publisher**\. Do not choose **Add**\.

1. Choose **Save** to save your Jenkins project\.

#### Create a fourth stage<a name="tutorials-four-stage-pipeline-create-fourth-stage"></a>

**To add a stage to your pipeline that includes the Jenkins test action**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. In **Name**, choose the name of the pipeline you created, MySecondPipeline\. 

1. On the pipeline details page, choose **Edit**\. 

1. On the **Edit** page, choose **\+ Stage** to add a stage immediately after the Build stage\. 

1. In the name field for the new stage, enter a name \(for example, **Testing**\), and then choose **\+ Add action group**\. 

1. In **Action name**, enter *MyJenkinsTest\-Action*\. In **Test provider**, choose the provider name you specified in Jenkins \(for example, *MyJenkinsProviderName*\)\. In **Project name**, enter the name of the project you created in Jenkins \(for example, *MyTestProject*\)\. In **Input artifacts**, choose the artifact from the Jenkins build whose default name is *BuildArtifact*, and then choose **Done**\.
**Note**  
Because the Jenkins test action operates on the application built in the Jenkins build step, use the build artifact for the input artifact to the test action\.

   For more information about input and output artifacts and the structure of pipelines, see [CodePipeline pipeline structure reference](reference-pipeline-structure.md)\.

1. On the **Edit** page, choose **Save pipeline changes**\. In the **Save pipeline changes** dialog box, choose **Save and continue**\.

1. Although the new stage has been added to your pipeline, a status of **No executions yet** is displayed for that stage because no changes have triggered another run of the pipeline\. To run the sample through the revised pipeline, on the pipeline details page, choose **Release change**\. 

   The pipeline view shows the stages and actions in your pipeline and the state of the revision running through those four stages\. The time it takes for the pipeline to run through all stages will depend on the size of the artifacts, the complexity of your build and test actions, and other factors\. 

## Step 4: Clean up resources<a name="tutorials-four-stage-pipeline-clean-up"></a>

After you complete this tutorial, you should delete the pipeline and the resources it uses so you will not be charged for continued use of those resources\. If you do not intend to keep using CodePipeline, delete the pipeline, then the CodeDeploy application and its associated Amazon EC2 instances, and finally, the Amazon S3 bucket used to store artifacts\. You should also consider whether to delete other resources, such as the GitHub repository, if you do not intend to keep using them\.

**To clean up the resources used in this tutorial**

1. Open a terminal session on your local Linux, macOS, or Unix machine, or a command prompt on your local Windows machine, and run the delete\-pipeline command to delete the pipeline you created\. For **MySecondPipeline**, you would enter the following command: 

   ```
   aws codepipeline delete-pipeline --name "MySecondPipeline"
   ```

   This command returns nothing\.

1. To clean up your CodeDeploy resources, follow the instructions in [Cleaning Up](http://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-walkthrough.html#getting-started-walkthrough-clean-up)\.

1. To clean up your instance resources, delete the EC2 instance where you installed Jenkins\. For more information, see [Clean up your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-clean-up-your-instance.html)\.

1. If you do not intend to create more pipelines or use CodePipeline again, delete the Amazon S3 bucket used to store artifacts for your pipeline\. To delete the bucket, follow the instructions in [Deleting a bucket](http://docs.aws.amazon.com/AmazonS3/latest/UG/DeletingaBucket.html)\.

1. If you do not intend to use the other resources for this pipeline again, consider deleting them by following the guidance for that particular resource\. For example, if you want to delete the GitHub repository, follow the instructions in [Deleting a repository](https://help.github.com/articles/deleting-a-repository/) on the GitHub website\.