# AWS CodePipeline identity\-based policy examples<a name="security_iam_id-based-policy-examples"></a>

By default, IAM users and roles don't have permission to create or modify CodePipeline resources\. They also can't perform tasks using the AWS Management Console, AWS CLI, or AWS API\. An IAM administrator must create IAM policies that grant users and roles permission to perform specific API operations on the specified resources they need\. The administrator must then attach those policies to the IAM users or groups that require those permissions\.

To learn how to create an IAM identity\-based policy using these example JSON policy documents, see [Creating Policies on the JSON Tab](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-json-editor) in the *IAM User Guide*\.

**Topics**
+ [Policy best practices](security_iam_service-with-iam-policy-best-practices.md)
+ [Viewing resources in the console](security-iam-resources-console.md)
+ [Allow users to view their own permissions](security_iam_id-based-policy-examples-view-own-permissions.md)
+ [Identity\-based policies \(IAM\) examples](security-iam-id-policies-examples.md)
+ [Using tags to control access to CodePipeline resources](tag-based-access-control.md)
+ [Permissions required to use the CodePipeline console](security-iam-permissions-console.md)
+ [AWS managed \(predefined\) policies for CodePipeline](managed-policies.md)
+ [CodePipeline managed policies and notifications](#notifications-permissions)
+ [Customer managed policy examples](customer-managed-policies.md)

## CodePipeline managed policies and notifications<a name="notifications-permissions"></a>

CodePipeline supports notifications, which can notify users of important changes to pipelines\. Managed policies for CodePipeline include policy statements for notification functionality\. For more information, see [What are notifications?](https://docs.aws.amazon.com/codestar-notifications/latest/userguide/welcome.html)\.

### Permissions related to notifications in full access managed policies<a name="notifications-fullaccess"></a>

The `AWSCodePipelineFullAccess` managed policy includes the following statements to allow full access to notifications\. Users with this managed policy applied can also create and manage Amazon SNS topics for notifications, subscribe and unsubscribe users to topics, list topics to choose as targets for notification rules, and list AWS Chatbot clients configured for Slack\.

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
            "codestar-notifications:ListTagsforResource",
            "codestar-notifications:ListEventTypes"
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
    },
    {
        "Sid": "CodeStarNotificationsChatbotAccess",
        "Effect": "Allow",
        "Action": [
            "chatbot:DescribeSlackChannelConfigurations"
          ],
       "Resource": "*"
    }
```

### Permissions related to notifications in read\-only managed policies<a name="notifications-readonly"></a>

The `AWSCodePipelineReadOnlyAccess` managed policy includes the following statements to allow read\-only access to notifications\. Users with this policy applied can view notifications for resources, but cannot create, manage, or subscribe to them\. 

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
            "codestar-notifications:ListNotificationRules",
            "codestar-notifications:ListEventTypes",
            "codestar-notifications:ListTargets"
        ],
        "Resource": "*"
    }
```

For more information about IAM and notifications, see [Identity and Access Management for AWS CodeStar Notifications](https://docs.aws.amazon.com/codestar-notifications/latest/userguide/security-iam.html)\.