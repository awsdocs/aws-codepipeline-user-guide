# AWS Managed \(Predefined\) Policies for CodePipeline<a name="managed-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. Managed policies grant necessary permissions for common use cases so you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies, which you can attach to users in your account, are specific to CodePipeline:
+ **AWSCodePipelineFullAccess** – Grants full access to CodePipeline\.
+ **AWSCodePipelineCustomActionAccess** – Grants permission to an IAM user to create custom actions in CodePipeline or integrate Jenkins resources for build or test actions\.
+ **AWSCodePipelineReadOnlyAccess** – Grants read\-only access to CodePipeline\.
+ **AWSCodePipelineApproverAccess** – Grants permission to an IAM user to approve or reject a manual approval action\.

## CodePipeline Managed Policies and Notifications<a name="notifications-permissions"></a>

CodePipeline supports notifications, which can notify users of important changes to pipelines\.  Managed policies for CodePipeline include policy statements for notification functionality\. For more information, see [What are notifications?](https://docs.aws.amazon.com/codestar-notifications/latest/userguide/welcome.html)\.

### Permissions Related to Notifications in Full Access Managed Policies<a name="notifications-fullaccess"></a>

The `AWSCodePipelineFullAccess` managed policy includes the following statements to allow full access to notifications\. Users with this managed policy applied can also create and manage Amazon SNS topics for notifications, subscribe and unsubscribe users to topics, and list topics to choose as targets for notification rules\.

```
    {
        "Sid": "CodeStarNotificationsReadWriteAccess",
        "Effect": "Allow",
        "Action": [
            "codestar-notifications:CreateNotificationRule",
            "codestar-notifications:DescribeNotificationRule",
            "codestar-notifications:UpdateNotificationRule",
            "codestar-notifications:DeleteNotificationRule",
            "codestar-notifications:Subscribe",
            "codestar-notifications:Unsubscribe"
        ],
        "Resource": "*",
        "Condition" : {
            "StringLike" : {"codestar-notifications:NotificationsForResource" : "arn:aws:codepipeline:*"} 
        }
    },    
    {
        "Sid": "CodeStarNotificationsListAccess",
        "Effect": "Allow",
        "Action": [
            "codestar-notifications:ListNotificationRules",
            "codestar-notifications:ListTargets",
            "codestar-notifications:ListTagsforResource"
        ],
        "Resource": "*"
    },
    {
        "Sid": "CodeStarNotificationsSNSTopicCreateAccess",
        "Effect": "Allow",
        "Action": [
            "sns:CreateTopic",
            "sns:SetTopicAttributes"
        ],
        "Resource": "arn:aws:sns:*:*:codestar-notifications*"
    },
    {
        "Sid": "SNSTopicListAccess",
        "Effect": "Allow",
        "Action": [
            "sns:ListTopics"
        ],
        "Resource": "*"
    }
```

### Permissions Related to Notifications in Read\-Only Managed Policies<a name="notifications-readonly"></a>

The `AWSCodePipelineReadOnlyAccess` managed policy includes the following statements to allow read\-only access to notifications\. Users with this managed policy applied can view notifications for resources, but cannot create, manage, or subscribe to them\. 

```
   {
        "Sid": "CodeStarNotificationsPowerUserAccess",
        "Effect": "Allow",
        "Action": [
            "codestar-notifications:DescribeNotificationRule"
        ],
        "Resource": "*",
        "Condition" : {
            "StringLike" : {"codestar-notifications:NotificationsForResource" : "arn:aws:codepipeline:*"} 
        }
    },    
    {
        "Sid": "CodeStarNotificationsListAccess",
        "Effect": "Allow",
        "Action": [
            "codestar-notifications:ListNotificationRules"
        ],
        "Resource": "*"
    }
```

For more information about IAM and notifications, see [Identity and Access Management for AWS CodeStar Notifications](https://docs.aws.amazon.com/codestar-notifications/latest/userguide/security-iam.html)\.