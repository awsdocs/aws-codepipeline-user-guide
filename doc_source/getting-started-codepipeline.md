# Getting started with CodePipeline<a name="getting-started-codepipeline"></a>

If you are new to CodePipeline, you can follow the tutorials in this guide after following the steps in this chapter to get set up\.

The CodePipeline console includes helpful information in a collapsible panel that you can open from the information icon or any **Info** link on the page\. \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/console-info-icon.png)\)\. You can close this panel at any time\.

![\[Viewing additional guidance in the console\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-guidance-open.png)

The CodePipeline console also provides a way to quickly search for your resources, such as repositories, build projects, deployment applications, and pipelines\. Choose **Go to resource** or press the `/` key, and then type the name of the resource\. Any matches appear in the list\. Searches are case insensitive\. You only see resources that you have permissions to view\. For more information, see [Viewing resources in the console](security-iam-resources-console.md)\. 

Before you can use AWS CodePipeline for the first time, you must complete the following steps\.

**Topics**
+ [Step 1: Create an AWS account](#create-aws-account)
+ [Step 2: Create or use an IAM user](#create-iam-user)
+ [Step 3: Use an IAM managed policy to assign CodePipeline permissions to the IAM user](#assign-permissions)
+ [Step 4: Install the AWS CLI](#install-cli)
+ [Step 5: Open the console for CodePipeline](#open-codepipeline-console)
+ [Next steps](#next-steps)

## Step 1: Create an AWS account<a name="create-aws-account"></a>

Create an AWS account, if you haven't done so already, by going to [https://aws\.amazon\.com/](https://aws.amazon.com/) and choosing **Sign Up**\.

## Step 2: Create or use an IAM user<a name="create-iam-user"></a>

Create an IAM user or use an existing one in your AWS account\. Make sure that you have an AWS access key ID and an AWS secret access key associated with that IAM user\. For more information, see [Creating an IAM User in Your AWS Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/Using_SettingUpUser.html)\.

## Step 3: Use an IAM managed policy to assign CodePipeline permissions to the IAM user<a name="assign-permissions"></a>

You must give the IAM user permissions to interact with CodePipeline\. The quickest way to do this is to apply the `AWSCodePipelineFullAccess` managed policy to the IAM user\. 

**Note**  
The `AWSCodePipelineFullAccess` policy includes permissions that allow the console user to pass an IAM role to CodePipeline or other AWS services\. This allows the service to assume the role and perform actions on your behalf\. When you attach the policy to a user, role, or group, the `iam:PassRole` permissions are applied\. Make sure the policy is only applied to trusted users\. When users with these permissions use the console to create or edit a pipeline, the following choices are available:  
Create a CodePipeline service role or choose an existing one and pass the role to CodePipeline
Might choose to create a CloudWatch Events rule for change detection and pass the CloudWatch Events service role to CloudWatch Events 
For more information, see [Granting a User Permissions to Pass a Role to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html)\.

**To grant permissions to an IAM user using the AWS Management Console**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the IAM console, in the navigation pane, choose **Policies**, and then choose the `AWSCodePipelineFullAccess` managed policy from the list of policies\.

1. On the **Summary** page, choose the **Policy Usage** tab, and then choose **Attach**\.

1. On the **Attach Policy** page, select the check box next to the IAM users or groups, and then choose **Attach Policy**\.
**Note**  
The `AWSCodePipelineFullAccess` policy provides access to all CodePipeline actions and resources that the IAM user has access to, as well as all possible actions when creating stages in a pipeline, such as creating stages that include CodeDeploy, Elastic Beanstalk, or Amazon S3\. As a best practice, you should grant individuals only the permissions they need to perform their duties\. For more information about how to restrict IAM users to a limited set of CodePipeline actions and resources, see [Remove permissions from the CodePipeline service role](security-iam.md#remove-permissions-from-policy)\.

## Step 4: Install the AWS CLI<a name="install-cli"></a>

To call CodePipeline commands from the AWS CLI on a local development machine, you must install the AWS CLI\. This step is optional if you intend to get started using only the steps in this guide for the CodePipeline console\.

**To install and configure the AWS CLI**

1. On your local machine, download and install the AWS CLI\. This will enable you to interact with CodePipeline from the command line\. For more information, see [Getting Set Up with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html)\.
**Note**  
CodePipeline works only with AWS CLI versions 1\.7\.38 and later\. To determine which version of the AWS CLI that you may have installed, run the command aws \-\-version\. To upgrade an older version of the AWS CLI to the latest version, follow the instructions in [Uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-uninstall.html), and then follow the instructions in [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

1. Configure the AWS CLI with the configure command, as follows:

   ```
   aws configure
   ```

   When prompted, specify the AWS access key and AWS secret access key of the IAM user that you will use with CodePipeline\. When prompted for the default region name, specify the region where you will create the pipeline, such as `us-east-2`\. When prompted for the default output format, specify `json`\. For example:

   ```
   AWS Access Key ID [None]: Type your target AWS access key ID here, and then press Enter
   AWS Secret Access Key [None]: Type your target AWS secret access key here, and then press Enter
   Default region name [None]: Type us-east-2 here, and then press Enter
   Default output format [None]: Type json here, and then press Enter
   ```
**Note**  
For more information about IAM, access keys, and secret keys, see [Managing Access Keys for IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingCredentials.html) and [How Do I Get Credentials?](https://docs.aws.amazon.com/IAM/latest/UserGuide/IAM_Introduction.html#IAM_SecurityCredentials)\.   
For more information about the Regions and endpoints available for CodePipeline, see [AWS CodePipeline endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/codepipeline.html)\.

## Step 5: Open the console for CodePipeline<a name="open-codepipeline-console"></a>
+ Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

## Next steps<a name="next-steps"></a>

You have completed the prerequisites\. You can begin using CodePipeline\. To start working with CodePipeline, see the [CodePipeline tutorials](tutorials.md)\.