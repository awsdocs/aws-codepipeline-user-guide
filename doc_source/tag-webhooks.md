# Tag a webhook in CodePipeline<a name="tag-webhooks"></a>

You can apply tags to your webhooks in CodePipeline\. Tags are key\-value pairs associated with AWS resources\. For information about CodePipeline resource tagging, use cases, tag key and value constraints, and supported resource types, see [Tagging resources](tag-resources.md)\.

You can specify tags when you create a webhook\. You can add, remove, and update the values of tags in a webhook\. You can add up to 50 tags to each webhook\.

**Topics**
+ [Add tags to an existing webhook](#tag-webhooks-add)
+ [View tags for a webhook](#tag-webhooks-list)
+ [Edit tags for a webhook](#tag-webhooks-update)
+ [Remove tags for a webhook](#tag-webhooks-delete)

## Add tags to an existing webhook<a name="tag-webhooks-add"></a>

Follow these steps to use the AWS CLI to add a tag to a webhook\. To add a tag to a webhook when you create it, see [Create a webhook for a GitHub source](pipelines-webhooks-create.md)\.

In these steps, we assume that you have already installed a recent version of the AWS CLI or updated to the current version\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

At the terminal or command line, run the tag\-resource command, specifying the Amazon Resource Name \(ARN\) of the webhook where you want to add tags and the key and value of the tag you want to add\. You can add more than one tag to a webhook\. For example, to tag a webhook named *MyWebhook* with two tags, a tag key named *Project* with the tag value of *NewProject*, and a tag key named *ApplicationName* with the tag value of *MyApplication*:

```
aws codepipeline tag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:webhook:MyWebhook --tags key=Project,value=NewProject key=ApplicationName,value=MyApplication
```

If successful, this command returns nothing\.

## View tags for a webhook<a name="tag-webhooks-list"></a>

Follow these steps to use the AWS CLI to view the AWS tags for a webhook\. If no tags have been added, the returned list is empty\.

At the terminal or command line, run the list\-tags\-for\-resource command\. For example, to view a list of tag keys and tag values for a webhook named *MyWebhook* with the ARN `arn:aws:codepipeline:us-west-2:account-id:webhook:MyWebhook`:

```
aws codepipeline list-tags-for-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:webhook:MyWebhook
```

If successful, this command returns information similar to the following:

```
{
    "tags": {
        "Project": "NewProject",
        "ApplicationName": "MyApplication"
    }
}
```

## Edit tags for a webhook<a name="tag-webhooks-update"></a>

Follow these steps to use the AWS CLI to update a tag for a webhook\. You can change the value for an existing key or add another key\. You can also remove tags from a webhook, as shown in the next section\.

At the terminal or command line, run the tag\-resource command, specifying the ARN of the webhook where you want to update a tag and specify the tag key and tag value:

```
aws codepipeline tag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:webhook:MyWebhook --tags key=Project,value=UpdatedProject
```

## Remove tags for a webhook<a name="tag-webhooks-delete"></a>

Follow these steps to use the AWS CLI to remove a tag from a webhook\. When you remove tags from the associated resource, the tags are deleted\.

**Note**  
If you delete a webhook, all tag associations are removed from the webhook\. You do not have to remove tags before you delete a webhook\.

At the terminal or command line, run the untag\-resource command, specifying the ARN of the webhook where you want to remove tags and the tag key of the tag you want to remove\. For example, to remove a tag on a webhook named *MyWebhook* with the tag key *Project*:

```
aws codepipeline untag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:webhook:MyWebhook --tag-keys Project
```

If successful, this command returns nothing\. To verify the tags associated with the webhook, run the list\-tags\-for\-resource command\.