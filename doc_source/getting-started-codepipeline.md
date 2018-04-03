# Getting Started with AWS CodePipeline<a name="getting-started-codepipeline"></a>

Before you can use AWS CodePipeline for the first time, you must complete the following steps\.

**Topics**
+ [Step 1: Create an AWS Account](#create-aws-account)
+ [Step 2: Create or Use an IAM User](#create-iam-user)
+ [Step 3: Use an IAM Managed Policy to Assign AWS CodePipeline Permissions to the IAM User](#assign-permissions)
+ [Step 4: Install the AWS CLI](#install-cli)
+ [Step 5: Open the Console for AWS CodePipeline](#open-codepipeline-console)
+ [Next Steps](#next-steps)

## Step 1: Create an AWS Account<a name="create-aws-account"></a>

Create an AWS account, if you haven't done so already, by going to [https://aws\.amazon\.com/](https://aws.amazon.com/) and choosing **Sign Up**\.

## Step 2: Create or Use an IAM User<a name="create-iam-user"></a>

Create an IAM user or use an existing one in your AWS account\. Make sure that you have an AWS access key ID and an AWS secret access key associated with that IAM user\. For more information, see [Creating an IAM User in Your AWS Account](http://docs.aws.amazon.com/IAM/latest/UserGuide/Using_SettingUpUser.html)\.

## Step 3: Use an IAM Managed Policy to Assign AWS CodePipeline Permissions to the IAM User<a name="assign-permissions"></a>

You must give the IAM user permissions to interact with AWS CodePipeline\. The quickest way to do this is to apply the **AWSCodePipelineFullAccess** managed policy to the IAM user\. 

**To grant permissions to an IAM user using the AWS Management Console**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the IAM console, in the navigation pane, choose **Policies**, and then choose the **AWSCodePipelineFullAccess** managed policy from the list of policies\.

1. On the **Policy Details** page, choose the **Attached Entities** tab, and then choose **Attach**\.

1. On the **Attach Policy** page, select the check box next to the IAM users or groups, and then choose **Attach Policy**\.
**Note**  
The **AWSCodePipelineFullAccess** policy provides access to all AWS CodePipeline actions and resources that the IAM user has access to, as well as all possible actions when creating stages in a pipeline, such as creating stages that include AWS CodeDeploy, Elastic Beanstalk, or Amazon S3\. As a best practice, you should grant individuals only the permissions they need to perform their duties\. For more information about how to restrict IAM users to a limited set of AWS CodePipeline actions and resources, see [Remove Permissions for Unused AWS Services](how-to-custom-role.md#remove-permissions-from-policy)\.

## Step 4: Install the AWS CLI<a name="install-cli"></a>

**To install and configure the AWS CLI**

1. On your local machine, download and install the AWS CLI\. This will enable you to interact with AWS CodePipeline from the command line\. For more information, see [Getting Set Up with the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html)\.
**Note**  
AWS CodePipeline works only with AWS CLI versions 1\.7\.38 and later\. To determine which version of the AWS CLI that you may have installed, run the command `aws --version`\. To upgrade an older version of the AWS CLI to the latest version, follow the instructions in [Uninstalling the AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/cli-uninstall.html), and then follow the instructions in [Installing the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

1. Configure the AWS CLI with the configure command, as follows:

   ```
   aws configure
   ```

   When prompted, specify the AWS access key and AWS secret access key of the IAM user that you will use with AWS CodePipeline\. When prompted for the default region name, specify the region where you will create the pipeline, such as `us-east-2`\. When prompted for the default output format, specify `json`\. For example:

   ```
   AWS Access Key ID [None]: Type your target AWS access key ID here, and then press Enter
   AWS Secret Access Key [None]: Type your target AWS secret access key here, and then press Enter
   Default region name [None]: Type us-east-2 here, and then press Enter
   Default output format [None]: Type json here, and then press Enter
   ```
**Note**  
For more information about IAM, access keys, and secret keys, see [Managing Access Keys for IAM Users](http://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingCredentials.html) and [How Do I Get Credentials?](http://docs.aws.amazon.com/IAM/latest/UserGuide/IAM_Introduction.html#IAM_SecurityCredentials)\.   
For more information about the regions and endpoints available for AWS CodePipeline, see [Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html#codepipeline_region)\.

## Step 5: Open the Console for AWS CodePipeline<a name="open-codepipeline-console"></a>
+ Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codepipeline](http://console.aws.amazon.com/codepipeline)\.

## Next Steps<a name="next-steps"></a>

You have completed the prerequisites\. You can begin using AWS CodePipeline\. To start working with AWS CodePipeline, see the [AWS CodePipeline Tutorials](tutorials.md)\.