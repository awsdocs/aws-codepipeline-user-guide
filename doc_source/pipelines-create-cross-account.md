# Create a pipeline in CodePipeline that uses resources from another AWS account<a name="pipelines-create-cross-account"></a>

You might want to create a pipeline that uses resources created or managed by another AWS account\. For example, you might want to use one account for your pipeline and another for your CodeDeploy resources\. 

**Note**  
When you create a pipeline with actions from multiple accounts, you must configure your actions so that they can still access artifacts within the limitations of cross\-account pipelines\. The following limitations apply to cross\-account actions:  
In general, an action can only consume an artifact if:  
The action is in the same account as the pipeline account OR
The artifact was created in the pipeline account for an action in another account OR 
The artifact was produced by a previous action in the same account as the action
In other words, you cannot pass an artifact from one account to another if neither account is the pipeline account\.
Cross\-account actions are not supported for the following action types:  
Jenkins build actions

For this example, you must create an AWS Key Management Service \(AWS KMS\) key to use, add the key to the pipeline, and set up account policies and roles to enable cross\-account access\. For an AWS KMS key, you can use the key ID, the key ARN, or the alias ARN\. 

**Note**  
To specify a customer master key \(CMK\) in a different AWS account, you must use the key ARN or alias ARN\.

In this walkthrough and its examples, *AccountA* is the account originally used to create the pipeline\. It has access to the Amazon S3 bucket used to store pipeline artifacts and the service role used by AWS CodePipeline\. *AccountB* is the account originally used to create the CodeDeploy application, deployment group, and service role used by CodeDeploy\. 

For *AccountA* to edit a pipeline to use the CodeDeploy application created by *AccountB*, *AccountA* must: 
+ Request the ARN or account ID of *AccountB* \(in this walkthrough, the *AccountB* ID is *012ID\_ACCOUNT\_B*\)\.
+ Create or use an AWS KMS customer managed key in the Region for the pipeline, and grant permissions to use that key to the service role \(*AWS\-CodePipeline\-Service*\) and *AccountB*\. 
+ Create an Amazon S3 bucket policy that grants *AccountB* access to the Amazon S3 bucket \(for example, *codepipeline\-us\-east\-2\-1234567890*\)\. 
+ Create a policy that allows *AccountA* to assume a role configured by *AccountB*, and attach that policy to the service role \(*AWS\-CodePipeline\-Service*\)\.
+ Edit the pipeline to use the customer managed AWS KMS key instead of the default key\.

For *AccountB* to allow access to its resources to a pipeline created in *AccountA*, *AccountB* must:
+ Request the ARN or account ID of *AccountA* \(in this walkthrough, the *AccountA* ID is *012ID\_ACCOUNT\_A*\)\.
+ Create a policy applied to the [Amazon EC2 instance role](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-iam-instance-profile.html) configured for CodeDeploy that allows access to the Amazon S3 bucket \(*codepipeline\-us\-east\-2\-1234567890*\)\.
+ Create a policy applied to the [Amazon EC2 instance role](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-iam-instance-profile.html) configured for CodeDeploy that allows access to the AWS KMS customer managed key used to encrypt the pipeline artifacts in *AccountA*\.
+ Configure and attach an IAM role \(*CrossAccount\_Role*\) with a trust relationship policy that allows *AccountA* to assume the role\.
+ Create a policy that allows access to the deployment resources required by the pipeline and attach it to *CrossAccount\_Role*\.
+ Create a policy that allows access to the Amazon S3 bucket \(*codepipeline\-us\-east\-2\-1234567890*\) and attach it to *CrossAccount\_Role*\.

**Topics**
+ [Prerequisite: Create an AWS KMS encryption key](#pipelines-create-cross-account-create-key)
+ [Step 1: Set up account policies and roles](#pipelines-create-cross-account-setup)
+ [Step 2: Edit the pipeline](#pipelines-create-cross-account-create)

## Prerequisite: Create an AWS KMS encryption key<a name="pipelines-create-cross-account-create-key"></a>

Customer\-managed keys are specific to a Region, as are all AWS KMS keys\. You must create your customer managed AWS KMS key in the same Region where the pipeline was created \(for example, `us-east-2`\)\.

**To create a customer managed key in AWS KMS**

1. Sign in to the AWS Management Console with *AccountA* and open the AWS KMS console\.

1. On the left, choose **Customer managed keys**\.

1. Choose **Create key**\. In **Configure key**, leave the **Symmetric** default selected and choose **Next**\.

1. In **Alias**, enter an alias to use for this key \(for example, *PipelineName\-Key*\)\. Optionally, provide a description and tags for this key, and then choose **Next**\.

1. In **Define Key Administrative Permissions**, choose your IAM user and any other users or groups you want to act as administrators for this key, and then choose **Next**\.

1. In **Define Key Usage Permissions**, under **This Account**, select the name of the service role for the pipeline \(for example, AWS\-CodePipeline\-Service\)\. Under **Other AWS accounts**, choose **Add another AWS account**\. Enter the account ID for *AccountB* to complete the ARN, and then choose **Next**\.

1. In **Review and edit key policy**, review the policy, and then choose **Finish**\.

1. From the list of keys, choose the alias of your key and copy its ARN \(for example, ***arn:aws:kms:us\-east\-2:012ID\_ACCOUNT\_A:key/2222222\-3333333\-4444\-556677EXAMPLE***\)\. You will need this when you edit your pipeline and configure policies\.

## Step 1: Set up account policies and roles<a name="pipelines-create-cross-account-setup"></a>

After you create the AWS KMS key, you must create and attach policies that will enable the cross\-account access\. This requires actions from both *AccountA* and *AccountB*\.

**Topics**
+ [Configure policies and roles in the account that will create the pipeline \(*AccountA*\)](#pipelines-create-cross-account-setup-accounta)
+ [Configure policies and roles in the account that owns the AWS resource \(*AccountB*\)](#pipelines-create-cross-account-setup-accountb)

### Configure policies and roles in the account that will create the pipeline \(*AccountA*\)<a name="pipelines-create-cross-account-setup-accounta"></a>

To create a pipeline that uses CodeDeploy resources associated with another AWS account, *AccountA* must configure policies for both the Amazon S3 bucket used to store artifacts and the service role for CodePipeline\.

**To create a policy for the Amazon S3 bucket that grants access to AccountB \(console\)**

1. Sign in to the AWS Management Console with *AccountA* and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the list of Amazon S3 buckets, choose the Amazon S3 bucket where artifacts for your pipelines are stored\. This bucket is named `codepipeline-region-1234567EXAMPLE`, where *region* is the AWS Region in which you created the pipeline and *1234567EXAMPLE* is a ten\-digit random number that ensures the bucket name is unique \(for example, *codepipeline\-us\-east\-2\-1234567890*\)\.

1. On the detail page for the Amazon S3 bucket, choose **Properties**\.

1. In the properties pane, expand **Permissions**, and then choose **Add bucket policy**\.
**Note**  
If a policy is already attached to your Amazon S3 bucket, choose **Edit bucket policy**\. You can then add the statements in the following example to the existing policy\. To add a new policy, choose the link, and follow the instructions in the AWS Policy Generator\. For more information, see [Overview of IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/policies_overview.html)\. 

1. In the **Bucket Policy Editor** window, type the following policy\. This will allow *AccountB* access to the pipeline artifacts, and will give *AccountB* the ability to add output artifacts if an action, such as a custom source or build action, creates them\. 

   In the following example, the ARN is for *AccountB* is *012ID\_ACCOUNT\_B*\. The ARN for the Amazon S3 bucket is *codepipeline\-us\-east\-2\-1234567890*\. Replace these ARNs with the ARN for the account you want to allow access and the ARN for the Amazon S3 bucket:

   ```
    {
     "Version": "2012-10-17",
     "Id": "SSEAndSSLPolicy",
     "Statement": [
   	{
     	"Sid": "DenyUnEncryptedObjectUploads",
   	  "Effect": "Deny",
   	  "Principal": "*",
   	  "Action": "s3:PutObject",
   	  "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*",
   	  "Condition": {
   		"StringNotEquals": {
   		"s3:x-amz-server-side-encryption": "aws:kms"
   		}
   	   }
   	 },
   	{
   	  "Sid": "DenyInsecureConnections",
   	  "Effect": "Deny",
   	  "Principal": "*",
   	  "Action": "s3:*",
   	  "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*",
   	  "Condition": {
   		"Bool": {
   	  		"aws:SecureTransport": false
   			}
   		}
   	  },
   	{
   	  "Sid": "",
   	  "Effect": "Allow",
   	  "Principal": {
   		"AWS": "arn:aws:iam::012ID_ACCOUNT_B:root"
   		},
   	  "Action": [
               "s3:Get*",
               "s3:Put*"
           ],
   	  "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*"
   	},
   	{
   	  "Sid": "",
   	  "Effect": "Allow",
   	  "Principal": {
   	      "AWS": "arn:aws:iam::012ID_ACCOUNT_B:root"
   			},
   	  "Action": "s3:ListBucket",
     	"Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890"
   	}
      ]
   }
   ```

1. Choose **Save**, and then close the policy editor\.

1. Choose **Save** to save the permissions for the Amazon S3 bucket\.

**To create a policy for the service role for CodePipeline \(console\)**

1. Sign in to the AWS Management Console with *AccountA* and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In **Dashboard**, choose **Roles**\.

1. In the list of roles, under **Role Name**, choose the name of the service role for CodePipeline\. By default, this is AWS\-CodePipeline\-Service\. If you used a different name for your service role, be sure to choose it from the list\.

1. On the **Summary** page, on the **Permissions** tab, expand **Inline Policies**, and then choose **Create Role Policy**\.
**Note**  
If you have not previously created any role policies, **Create Role Policy** will not appear\. Choose the link to create a new policy instead\.

1. In **Set Permissions**, choose **Custom Policy**, and then choose **Select**\.

1. On the **Review Policy** page, type a name for the policy in **Policy Name**\. In **Policy Document**, type the following policy to allow *AccountB* to assume the role\. In the following example, *012ID\_ACCOUNT\_B* is the ARN for *AccountB*:

   ```
   {
      "Version": "2012-10-17",
      "Statement": {
          "Effect": "Allow",
          "Action": "sts:AssumeRole",
          "Resource": [
              "arn:aws:iam::012ID_ACCOUNT_B:role/*"
          ]
      }
    }
   ```

1. Choose **Validate Policy**\.

1. After the policy is validated, choose **Apply Policy**\.

### Configure policies and roles in the account that owns the AWS resource \(*AccountB*\)<a name="pipelines-create-cross-account-setup-accountb"></a>

When you create an application, deployment, and deployment group in CodeDeploy, you also create an [Amazon EC2 instance role](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-iam-instance-profile.html)\. \(This role is created for you if you use the Run Deployment Walkthrough wizard, but you can also create it manually\.\) For a pipeline created in *AccountA* to use CodeDeploy resources created in *AccountB*, you must: 
+ Configure a policy for the instance role that allows it to access the Amazon S3 bucket where pipeline artifacts are stored\.
+ Create a second role in *AccountB* configured for cross\-account access\.

  This second role must not only have access to the Amazon S3 bucket in *AccountA*, it must also contain a policy that allows access to the CodeDeploy resources and a trust relationship policy that allows *AccountA* to assume the role\.
**Note**  
These policies are specific to setting up CodeDeploy resources to be used in a pipeline created using a different AWS account\. Other AWS resources will require policies specific to their resource requirements\.

**To create a policy for the Amazon EC2 instance role configured for CodeDeploy \(console\)**

1. Sign in to the AWS Management Console with *AccountB* and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In **Dashboard**, choose **Roles**\.

1. In the list of roles, under **Role Name**, choose the name of the service role used as the Amazon EC2 instance role for the CodeDeploy application\. This role name can vary, and more than one instance role can be used by a deployment group\. For more information, see [Create an IAM Instance Profile for your Amazon EC2 Instances](https://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-create-iam-instance-profile.html)\. 

1. On the **Summary** page, on the **Permissions** tab, expand **Inline Policies**, and then choose **Create Role Policy**\.

1. In **Set Permissions**, choose **Custom Policy**, and then choose **Select**\.

1. On the **Review Policy** page, enter a name for the policy in **Policy Name**\. In **Policy Document**, type the following policy to grant access to the Amazon S3 bucket used by *AccountA* to store artifacts for pipelines \(in this example, *codepipeline\-us\-east\-2\-1234567890*\):

   ```
   {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "s3:Get*"
          ],
          "Resource": [
            "arn:aws:s3:::codepipeline-us-east-2-1234567890/*"
          ]
        },
        {
          "Effect": "Allow",
          "Action": [
            "s3:ListBucket"
          ],
          "Resource": [
            "arn:aws:s3:::codepipeline-us-east-2-1234567890"
          ]
        }
      ]
    }
   ```

1. Choose **Validate Policy**\.

1. After the policy is validated, choose **Apply Policy**\.

1. Create a second policy for AWS KMS where ***arn:aws:kms:us\-east\-1:012ID\_ACCOUNT\_A:key/2222222\-3333333\-4444\-556677EXAMPLE*** is the ARN of the customer managed key created in *AccountA* and configured to allow *AccountB* to use it:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Action": [
              "kms:DescribeKey",
              "kms:GenerateDataKey*",
              "kms:Encrypt",
              "kms:ReEncrypt*",
              "kms:Decrypt"
             ],
           "Resource": [
              "arn:aws:kms:us-east-1:012ID_ACCOUNT_A:key/2222222-3333333-4444-556677EXAMPLE"
             ]
         }
      ]
   }
   ```
**Important**  
You must use the account ID of *AccountA* in this policy as part of the resource ARN for the AWS KMS key, as shown here, or the policy will not work\.

1. Choose **Validate Policy**\.

1. After the policy is validated, choose **Apply Policy**\.

Now create an IAM role to use for cross\-account access, and configure it so that *AccountA* can assume the role\. This role must contain policies that allow access to the CodeDeploy resources and the Amazon S3 bucket used to store artifacts in *AccountA*\.

**To configure the cross\-account role in IAM**

1. Sign in to the AWS Management Console with *AccountB* and open the IAM console at [https://console\.aws\.amazon\.com/iam](https://console.aws.amazon.com/iam)\.

1. In **Dashboard**, choose **Roles**, and then choose **Create New Role**\.

1. On the **Set New Role** page, type a name for this role in **Role Name** \(for example, *CrossAccount\_Role*\)\. You can name this role anything you want as long as it follows the naming conventions in IAM\. Consider giving the role a name that clearly states its purpose\.

1.  On the **Select Role Type** page, choose **Role for Cross\-Account Access**\. Next to **Provide access between AWS accounts you own**, choose **Select**\.

1. Type the AWS account ID for the account that will create the pipeline in CodePipeline \(*AccountA*\), and then choose **Next Step**\.
**Note**  
This step creates the trust relationship policy between *AccountB* and *AccountA*\.

1. In **Attach Policy**, choose **AmazonS3ReadOnlyAccess**, and then choose **Next Step**\.
**Note**  
This is not the policy you will use\. You must choose a policy to complete the wizard\.

1. On the **Review** page, choose **Create Role**\.

1. From the list of roles, choose the policy you just created \(for example, *CrossAccount\_Role*\) to open the **Summary** page for that role\.

1. Expand **Permissions**, and then expand **Inline Policies**\. Choose the link to create an inline policy\.

1. In **Set Permissions**, choose **Custom Policy**, and then choose **Select**\.

1. On the **Review Policy** page, type a name for the policy in **Policy Name**\. In **Policy Document**, type the following policy to allow access to CodeDeploy resources:

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "codedeploy:CreateDeployment",
           "codedeploy:GetDeployment",
           "codedeploy:GetDeploymentConfig",
           "codedeploy:GetApplicationRevision",
           "codedeploy:RegisterApplicationRevision"
         ],
         "Resource": "*"
       }
     ]
   }
   ```

1. Choose **Validate Policy**\.

1. After the policy is validated, choose **Apply Policy**\.

1. In **Inline Policies**, choose **Create Role Policy**\.

1. In **Set Permissions**, choose **Custom Policy**, and then choose **Select**\.

1. On the **Review Policy** page, type a name for the policy in **Policy Name**\. In **Policy Document**, type the following policy to allow this role to retrieve input artifacts from, and put output artifacts into, the Amazon S3 bucket in *AccountA*:

   ```
   {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "s3:GetObject*",
            "s3:PutObject",
            "s3:PutObjectAcl",
            "codecommit:ListBranches",
            "codecommit:ListRepositories"                
          ],
          "Resource": [
            "arn:aws:s3:::codepipeline-us-east-2-1234567890/*"
          ]
        }
      ]
   }
   ```

1. Choose **Validate Policy**\.

1. When the policy is validated, choose **Apply Policy**\.

1. In **Managed Policies**, find **AmazonS3ReadOnlyAccess** in the list of policies under **Policy Name**, and choose **Detach Policy**\. When prompted, choose **Detach**\.

## Step 2: Edit the pipeline<a name="pipelines-create-cross-account-create"></a>

You cannot use the CodePipeline console to create or edit a pipeline that uses resources associated with another AWS account\. However, you can use the console to create the general structure of the pipeline, and then use the AWS CLI to edit the pipeline and add those resources\. Alternatively, you can use the structure of an existing pipeline and manually add the resources to it\. 

**To add the resources associated with another AWS account \(AWS CLI\)**

1. At a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), run the get\-pipeline command on the pipeline to which you want to add resources\. Copy the command output to a JSON file\. For example, for a pipeline named MyFirstPipeline, you would type something similar to the following:

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   The output is sent to the *pipeline\.json* file\.

1. Open the JSON file in any plain\-text editor\. After `"type": "S3"` in the artifact store, add the KMS encryptionKey, ID, and type information where *codepipeline\-us\-east\-2\-1234567890* is the name of the Amazon S3 bucket used to store artifacts for the pipeline and ***arn:aws:kms:us\-east\-1:012ID\_ACCOUNT\_A:key/2222222\-3333333\-4444\-556677EXAMPLE*** is the ARN of the customer\-managed key you just created:

   ```
   {
     "artifactStore”: {
       "location": "codepipeline-us-east-2-1234567890", 
       "type": "S3",
       "encryptionKey": {
         "id": "arn:aws:kms:us-east-1:012ID_ACCOUNT_A:key/2222222-3333333-4444-556677EXAMPLE",
         "type": "KMS"
       }
     },
   ```

1. Add a deploy action in a stage to use the CodeDeploy resources associated with *AccountB*, including the `roleArn` values for the cross\-account role you created \(*CrossAccount\_Role*\)\.

   The following example shows JSON that adds a deploy action named *ExternalDeploy*\. It uses the CodeDeploy resources created in *AccountB* in a stage named *Staging*\. In the following example, the ARN for *AccountB* is *012ID\_ACCOUNT\_B*:

   ```
   ,
               {
                   "name": "Staging",
                   "actions": [
                       {
                           "inputArtifacts": [
                               {
                                   "name": "MyAppBuild"
                               }
                           ],
                           "name": "ExternalDeploy",
                           "actionTypeId": {
                               "category": "Deploy",
                               "owner": "AWS",
                               "version": "1",
                               "provider": "CodeDeploy"
                           },
                           "outputArtifacts": [],
                           "configuration": {
                               "ApplicationName": "AccountBApplicationName",
                               "DeploymentGroupName": "AccountBApplicationGroupName"
                           },
                           "runOrder": 1,
                           "roleArn": "arn:aws:iam::012ID_ACCOUNT_B:role/CrossAccount_Role"
                       }
                   ]
               }
   ```
**Note**  
This is not the JSON for the entire pipeline, just the structure for the action in a stage\.

1. You must remove the `metadata` lines from the file so the update\-pipeline command can use it\. Remove the section from the pipeline structure in the JSON file \(the `"metadata": { }` lines and the `"created"`, `"pipelineARN"`, and `"updated"` fields\)\.

   For example, remove the following lines from the structure: 

   ```
   "metadata": {  
     "pipelineArn": "arn:aws:codepipeline:region:account-ID:pipeline-name",
     "created": "date",
     "updated": "date"
     }
   ```

   Save the file\.

1.  To apply your changes, run the update\-pipeline command, specifying the pipeline JSON file, similar to the following:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline update-pipeline --cli-input-json file://pipeline.json
   ```

   This command returns the entire structure of the edited pipeline\.

**To test the pipeline that uses resources associated with another AWS account**

1. At a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), run the start\-pipeline\-execution command, specifying the name of the pipeline, similar to the following:

   ```
   aws codepipeline start-pipeline-execution --name MyFirstPipeline
   ```

   For more information, see [Start a pipeline manually in AWS CodePipeline](pipelines-rerun-manually.md)\.

1. Sign in to the AWS Management Console with *AccountA* and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account are displayed\.

1. In **Name**, choose the name of the pipeline you just edited\. This opens a detailed view of the pipeline, including the state of each action in each stage of the pipeline\.

1. Watch the progress through the pipeline\. Wait for a success message on the action that uses the resource associated with another AWS account\.
**Note**  
You will receive an error if you try to view details for the action while signed in with *AccountA*\. Sign out, and then sign in with *AccountB* to view the deployment details in CodeDeploy\. 