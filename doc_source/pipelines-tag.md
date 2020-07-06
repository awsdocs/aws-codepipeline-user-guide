# Tag a pipeline in CodePipeline<a name="pipelines-tag"></a>

Tags are key\-value pairs associated with AWS resources\. You can apply tags to your pipelines in CodePipeline\. For information about CodePipeline resource tagging, use cases, tag key and value constraints, and supported resource types, see [Tagging resources](tag-resources.md)\.

You can use the CLI to specify tags when you create a pipeline\. You can use the console or CLI to add or remove tags, and update the values of tags in a pipeline\. You can add up to 50 tags to each pipeline\.

**Topics**
+ [Tag pipelines \(console\)](#pipelines-tag-console)
+ [Tag pipelines \(CLI\)](#pipelines-tag-cli)

## Tag pipelines \(console\)<a name="pipelines-tag-console"></a>

You can use the console or the CLI to tag resources\. Pipelines are the only CodePipeline resource that can be managed with either the console or the CLI\.

**Topics**
+ [Add tags to a pipeline \(console\)](#pipelines-tag-add-console)
+ [View tags for a pipeline \(console\)](#pipelines-tag-list-console)
+ [Edit tags for a pipeline \(console\)](#pipelines-tag-update-console)
+ [Remove tags from a pipeline \(console\)](#pipelines-tag-delete-console)

### Add tags to a pipeline \(console\)<a name="pipelines-tag-add-console"></a>

You can use the console to add tags to an existing pipeline\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Pipelines** page, choose the pipeline where you want to add tags\.

1. From the navigation pane, choose **Settings**\.

1. Under **Pipeline tags**, choose **Edit**\.

1. In the **Key** and **Value** fields, enter a key pair for each set of tags you want to add\. \(The **Value** field is optional\.\) For example, in **Key**, enter **Project**\. In **Value**, enter **ProjectA**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/pipeline-tags-edit-console.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. \(Optional\) Choose **Add tag** to add more rows and enter more tags\.

1. Choose **Submit**\. The tags are listed under pipeline settings\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/pipeline-tags-console.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

### View tags for a pipeline \(console\)<a name="pipelines-tag-list-console"></a>

You can use the console to list tags for existing pipelines\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Pipelines** page, choose the pipeline where you want to view tags\.

1. From the navigation pane, choose **Settings**\.

1. Under **Pipeline tags**, view the tags for the pipeline under the **Key** and **Value** columns\.

### Edit tags for a pipeline \(console\)<a name="pipelines-tag-update-console"></a>

You can use the console to edit tags that have been added to pipelines\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Pipelines** page, choose the pipeline where you want to update tags\.

1. From the navigation pane, choose **Settings**\.

1. Under **Pipeline tags**, choose **Edit**\.

1. In the **Key** and **Value** fields, update the values in each field as needed\. For example, for the **Project** key, in **Value**, change **ProjectA** to **ProjectB**\.

1. Choose **Submit**\.

### Remove tags from a pipeline \(console\)<a name="pipelines-tag-delete-console"></a>

You can use the console to delete tags from pipelines\. When you remove tags from the associated resource, the tags are deleted\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Pipelines** page, choose the pipeline where you want to remove tags\.

1. From the navigation pane, choose **Settings**\.

1. Under **Pipeline tags**, choose **Edit**\.

1. Next to the key and value for each tag you want to delete, choose **Remove tag**\.

1. Choose **Submit**\.

## Tag pipelines \(CLI\)<a name="pipelines-tag-cli"></a>

You can use the CLI to tag resources\. You must use the console to manage tags in pipelines\.

**Topics**
+ [Add tags to a pipeline \(CLI\)](#pipelines-tag-add-cli)
+ [View tags for a pipeline \(CLI\)](#pipelines-tag-list-cli)
+ [Edit tags for a pipeline \(CLI\)](#pipelines-tag-update-cli)
+ [Remove tags from a pipeline \(CLI\)](#pipelines-tag-delete-cli)

### Add tags to a pipeline \(CLI\)<a name="pipelines-tag-add-cli"></a>

You can use the console or the AWS CLI to tag pipelines\.

To add a tag to a pipeline when you create it, see [Create a pipeline in CodePipeline](pipelines-create.md)\.

In these steps, we assume that you have already installed a recent version of the AWS CLI or updated to the current version\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

At the terminal or command line, run the tag\-resource command, specifying the Amazon Resource Name \(ARN\) of the pipeline where you want to add tags and the key and value of the tag you want to add\. You can add more than one tag to a pipeline\. For example, to tag a pipeline named *MyPipeline* with two tags, a tag key named *DeploymentEnvironment* with the tag value of *Test*, and a tag key named *IscontainerBased* with the tag value of *true*:

```
aws codepipeline tag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:MyPipeline --tags key=Project,value=ProjectA key=IscontainerBased,value=true
```

If successful, this command returns nothing\.

### View tags for a pipeline \(CLI\)<a name="pipelines-tag-list-cli"></a>

Follow these steps to use the AWS CLI to view the AWS tags for a pipeline\. If no tags have been added, the returned list is empty\.

At the terminal or command line, run the list\-tags\-for\-resource command\. For example, to view a list of tag keys and tag values for a pipeline named *MyPipeline* with the `arn:aws:codepipeline:us-west-2:account-id:MyPipeline` ARN value:

```
aws codepipeline list-tags-for-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:MyPipeline
```

If successful, this command returns information similar to the following:

```
{
    "tags": {
        "Project": "ProjectA",
        "IscontainerBased": "true"
    }
}
```

### Edit tags for a pipeline \(CLI\)<a name="pipelines-tag-update-cli"></a>

Follow these steps to use the AWS CLI to edit a tag for a pipeline\. You can change the value for an existing key or add another key\. You can also remove tags from a pipeline, as shown in the next section\.

At the terminal or command line, run the tag\-resource command, specifying the ARN of the pipeline where you want to update a tag and specify the tag key and tag value:

```
aws codepipeline tag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:MyPipeline --tags key=Project,value=ProjectA
```

If successful, this command returns nothing\.

### Remove tags from a pipeline \(CLI\)<a name="pipelines-tag-delete-cli"></a>

Follow these steps to use the AWS CLI to remove a tag from a pipeline\. When you remove tags from the associated resource, the tags are deleted\.

**Note**  
If you delete a pipeline, all tag associations are removed from the deleted pipeline\. You do not have to remove tags before you delete a pipeline\.

At the terminal or command line, run the untag\-resource command, specifying the ARN of the pipeline where you want to remove tags and the tag key of the tag you want to remove\. For example, to remove multiple tags on a pipeline named *MyPipeline* with the tag keys *Project* and *IscontainerBased*:

```
aws codepipeline untag-resource --resource-arn arn:aws:codepipeline:us-west-2:account-id:MyPipeline --tag-keys Project IscontainerBased
```

If successful, this command returns nothing\. To verify the tags associated with the pipeline, run the list\-tags\-for\-resource command\.