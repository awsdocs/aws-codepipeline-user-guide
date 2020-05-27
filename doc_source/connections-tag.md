# Tag Connections Resources<a name="connections-tag"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

A *tag* is a custom attribute label that you or AWS assigns to an AWS resource\. Each AWS tag has two parts:
+ A *tag key* \(for example, `CostCenter`, `Environment`, `Project`, or `Secret`\)\. Tag keys are case sensitive\.
+ An optional field known as a *tag value* \(for example, `111122223333`, `Production`, or a team name\)\. Omitting the tag value is the same as using an empty string\. Like tag keys, tag values are case sensitive\.

Together these are known as *key\-value pairs*\.

You can tag the following resource types in AWS CodeStar connections:
+ Connections

These steps assume that you have already installed a recent version of the AWS CLI or updated to the current version\. For more information, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide*\.

**Topics**
+ [Add Tags to a Connections Resource](#connections-tag-add)
+ [View Tags for a Connections Resource](#connections-tag-view)
+ [Edit Tags for a Connections Resource](#connections-tag-edit)
+ [Remove Tags from a Connections Resource](#connections-tag-delete)

## Add Tags to a Connections Resource<a name="connections-tag-add"></a>

You can use the AWS CLI to tag resources in connections\.

At the terminal or command line, run the tag\-resource command, specifying the Amazon Resource Name \(ARN\) of the resource where you want to add tags and the key and value of the tag you want to add\. You can add more than one tag\. 

For example, use the following command to tag a connection with two tags, a tag key named *Project* with the tag value of *ProjectA*, and a tag key named *ReadOnly* with the tag value of *true*\.

```
aws codestar-connections tag-resource --resource-arn arn:aws:codestar-connections:us-west-2:account_id:connection/aEXAMPLE-8aad-4d5d-8878-dfcab0bc441f --tags Key=Project,Value=ProjectA Key=IscontainerBased,Value=true
```

If successful, this command returns nothing\.

## View Tags for a Connections Resource<a name="connections-tag-view"></a>

Follow these steps to use the AWS CLI to view the AWS tags for a resource\. If no tags have been added, the returned list is empty\.

At the terminal or command line, run the list\-tags\-for\-resource command\. For example, use the following command to view a list of tag keys and tag values for a connection with the `arn:aws:codestar-connections:us-west-2:account_id:connection/aEXAMPLE-8aad-4d5d-8878-dfcab0bc441f` ARN value\.

```
aws codestar-connections list-tags-for-resource --resource-arn arn:aws:codestar-connections:us-west-2:account_id:connection/aEXAMPLE-8aad-4d5d-8878-dfcab0bc441f
```

If successful, this command returns information similar to the following\.

```
{
    "Tags": [
        {
            "Key": "Project",
            "Value": "ProjectA"
        },
        {
            "Key": "ReadOnly",
            "Value": "true"
        }
    ]
}
```

## Edit Tags for a Connections Resource<a name="connections-tag-edit"></a>

Follow these steps to use the AWS CLI to edit a tag for a resource\. You can change the value for an existing key or add another key\.

At the terminal or command line, run the tag\-resource command, specifying the ARN of the resource where you want to update a tag and specify the tag key and tag value to update\. In this example, the value for the key `Project` is changed to `ProjectB`\.

```
aws codestar-connections tag-resource --resource-arn arn:aws:codestar-connections:us-west-2:account_id:connection/aEXAMPLE-8aad-4d5d-8878-dfcab0bc441f --tags Key=Project,Value=ProjectB
```

If successful, this command returns nothing\. To verify the tags associated with the pipeline, run the list\-tags\-for\-resource command\.

## Remove Tags from a Connections Resource<a name="connections-tag-delete"></a>

Follow these steps to use the AWS CLI to remove a tag from a resource\. When you remove tags from the associated resource, the tags are deleted\.

**Note**  
If you delete a connection resource, all tag associations are removed from the deleted resource\. You do not have to remove tags before you delete a connection resource\.

At the terminal or command line, run the untag\-resource command, specifying the ARN of the resource where you want to remove tags and the tag key of the tag you want to remove\. For example, to remove multiple tags on a connection with the tag keys *Project* and *ReadOnly*, use the following command\.

```
aws codestar-connections untag-resource --resource-arn arn:aws:codestar-connections:us-west-2:account_id:connection/aEXAMPLE-8aad-4d5d-8878-dfcab0bc441f --tag-keys Project ReadOnly
```

If successful, this command returns nothing\. To verify the tags associated with the resource, run the list\-tags\-for\-resource command\. The output shows that all tags have been removed\.

```
{
    "Tags": []
}
```