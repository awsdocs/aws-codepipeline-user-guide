# Tag a custom action in CodePipeline<a name="customactions-tag"></a>

Tags are key\-value pairs associated with AWS resources\. You can use the console or the CLI to apply tags to your custom actions in CodePipeline\. For information about CodePipeline resource tagging, use cases, tag key and value constraints, and supported resource types, see [Tagging resources](tag-resources.md)\.

You can add, remove, and update the values of tags in a custom action\. You can add up to 50 tags to each custom action\. 

**Topics**
+ [Add tags to a custom action](#customactions-tag-add)
+ [View tags for a custom action](#customactions-tag-list)
+ [Edit tags for a custom action](#customactions-tag-update)
+ [Remove tags from a custom action](#customactions-tag-delete)

## Add tags to a custom action<a name="customactions-tag-add"></a>

Follow these steps to use the AWS CLI to add a tag to a custom action\. To add a tag to a custom action when you create it, see [Create and add a custom action in CodePipeline](actions-create-custom-action.md)\.

In these steps, we assume that you have already installed a recent version of the AWS CLI or updated to the current version\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

At the terminal or command line, run the tag\-resource command, specifying the Amazon Resource Name \(ARN\) of the custom action where you want to add tags and the key and value of the tag you want to add\. You can add more than one tag to a custom action\. For example, to tag a custom action with two tags, a tag key named *TestActionType* with the tag value of *UnitTest*, and a tag key named *ApplicationName* with the tag value of *MyApplication*:

```
aws codepipeline tag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:actiontype:Owner/Category/Provider/Version --tags key=TestActionType,value=UnitTest key=ApplicationName,value=MyApplication
```

If successful, this command returns nothing\.

## View tags for a custom action<a name="customactions-tag-list"></a>

Follow these steps to use the AWS CLI to view the AWS tags for a custom action\. If no tags have been added, the returned list is empty\.

At the terminal or command line, run the list\-tags\-for\-resource command\. For example, to view a list of tag keys and tag values for a custom action with the ARN `arn:aws:codepipeline:us-west-2:account-id:actiontype:Owner/Category/Provider/Version`:

```
aws codepipeline list-tags-for-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:actiontype:Owner/Category/Provider/Version
```

If successful, this command returns information similar to the following:

```
{
    "tags": {
        "TestActionType": "UnitTest",
        "ApplicationName": "MyApplication"
    }
}
```

## Edit tags for a custom action<a name="customactions-tag-update"></a>

Follow these steps to use the AWS CLI to edit a tag for a custom action\. You can change the value for an existing key or add another key\. You can also remove tags from a custom action, as shown in the next section\.

At the terminal or command line, run the tag\-resource command, specifying the Amazon Resource Name \(ARN\) of the custom action where you want to update a tag and specify the tag key and tag value:

```
aws codepipeline tag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:actiontype:Owner/Category/Provider/Version --tags key=TestActionType,value=IntegrationTest
```

## Remove tags from a custom action<a name="customactions-tag-delete"></a>

Follow these steps to use the AWS CLI to remove a tag from a custom action\. When you remove tags from the associated resource, the tags are deleted\.

**Note**  
If you delete a custom action, all tag associations are removed from the deleted custom action\. You do not have to remove tags before deleting a custom action\.

At the terminal or command line, run the untag\-resource command, specifying the ARN of the custom action where you want to remove tags and the tag key of the tag you want to remove\. For example, to remove a tag on a custom action with the tag key *TestActionType*:

```
aws codepipeline untag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:actiontype:Owner/Category/Provider/Version --tag-keys TestActionType
```

If successful, this command returns nothing\. To verify the tags associated with the custom action, run the list\-tags\-for\-resource command\.