# Add a Manual Approval Action to a Pipeline in AWS CodePipeline<a name="approvals-action-add"></a>

You can add an approval action to a stage in an AWS CodePipeline pipeline at the point where you want the pipeline to stop so someone can manually approve or reject the action\. 

**Note**  
Approval actions can't be added to Source stages\. Source stages can contain only source actions\. 

If you want to use Amazon SNS to send notifications when an approval action is ready for review, you must first complete the following prerequisites: 
+ Grant permission to your AWS CodePipeline service role to access Amazon SNS resources\. For information, see [Grant Amazon SNS Permissions to an AWS CodePipeline Service Role](approvals-service-role-permissions.md)\.
+ Grant permission to one or more IAM users in your organization to update the status of an approval action\. For information, see [Grant Approval Permissions to an IAM User in AWS CodePipeline](approvals-iam-permissions.md)\.

## Add a Manual Approval Action to an AWS CodePipeline Pipeline \(Console\)<a name="approvals-action-add-console"></a>

You can use the AWS CodePipeline console to add an approval action to an existing AWS CodePipeline pipeline\. You must use the AWS CLI if you want to add approval actions when you create a new pipeline\. 

1. Open the AWS CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

1. In **Name**, choose the pipeline\.

1. On the pipeline details page, choose **Edit**\.

1. If you want to add an approval action to a new stage, choose **\+ Stage** at the point in the pipeline where you want to add an approval request, and type a name for the stage\. 

   If you want to add an approval action to an existing stage, choose the edit icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/edit_icon.png)\) on that stage\.

1. Choose the action icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/add_action.png)\)\.

1. On the **Add action** page, do the following:

   1. In **Action category**, choose **Approval**\.

   1. In **Action name**, type a name to identify the action\.

   1. In **Approval type**, choose **Manual approval**\.

   1. \(Optional\) In **SNS topic ARN**, choose the name of the topic you will use to send notifications for the approval action\.

   1. \(Optional\) In **URL for review**, enter the URL of the page or application you want the approver to examine\. Approvers can access this URL through a link included in the console view of the pipeline\. 

   1. \(Optional\) In **Comments**, type any additional information you want to share with the reviewer\.

      Your completed page might look similar to the following:  
![\[The Add action page completed for an Approval action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/pipeline-add-action-manual-approval.png)![\[The Add action page completed for an Approval action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Add action page completed for an Approval action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   1. Choose **Add action**\.

## Add a Manual Approval Action to an AWS CodePipeline Pipeline \(CLI\)<a name="approvals-action-add-cli"></a>

You can use the CLI to add an approval action to an existing pipeline or when you create a pipeline\. You do this by including an approval action, with the Manual approval approval type, in a stage you are creating or editing\. 

For more information about creating and editing pipelines, see [Create a Pipeline in AWS CodePipeline](pipelines-create.md) and [Edit a Pipeline in AWS CodePipeline](pipelines-edit.md)\.

To add a stage to a pipeline that includes only an approval action, you would include something similar to the following example when you create or update the pipeline\. 

**Note**  
The `configuration` section is optional\. This is just a portion, not the entire structure, of the file\. For more information, see [AWS CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\.

```
{
    "name": "MyApprovalStage",
    "actions": [
        {
            "name": "MyApprovalAction",
            "actionTypeId": {
                "category": "Approval",
                "owner": "AWS",
                "version": "1",
                "provider": "Manual"
            },
            "inputArtifacts": [],
            "outputArtifacts": [],
            "configuration": {
                "NotificationArn": "arn:aws:sns:us-east-2:80398EXAMPLE:MyApprovalTopic",
                "ExternalEntityLink": "http://example.com",
                "CustomData": "The latest changes include feedback from Bob."},
            "runOrder": 1
        }
    ]
}
```

If the approval action is in a stage with other actions, the section of your JSON file that contains the stage might look similar instead to the following example\.

**Note**  
The `configuration` section is optional\. This is just a portion, not the entire structure, of the file\. For more information, see [AWS CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\.

```
,
{
    "name": "Production",
    "actions": [
        {
            "inputArtifacts": [],
            "name": "MyApprovalStage",
            "actionTypeId": {
                "category": "Approval",
                "owner": "AWS",
                "version": "1",
                "provider": "Manual"
            },
            "outputArtifacts": [],
            "configuration": {
                "NotificationArn": "arn:aws:sns:us-east-2:80398EXAMPLE:MyApprovalTopic",
                "ExternalEntityLink": "http://example.com",
                "CustomData": "The latest changes include feedback from Bob."
            },
            "runOrder": 1
        },
        {
            "inputArtifacts": [
                {
                    "name": "MyApp"
                }
            ],
            "name": "MyDeploymentStage",
            "actionTypeId": {
                "category": "Deploy",
                "owner": "AWS",
                "version": "1",
                "provider": "CodeDeploy"
            },
            "outputArtifacts": [],
            "configuration": {
                "ApplicationName": "MyDemoApplication",
                "DeploymentGroupName": "MyProductionFleet"
            },
            "runOrder": 2
        }
    ]
}
```