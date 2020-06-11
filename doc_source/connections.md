# Working with Connections in CodePipeline<a name="connections"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

*Connections* are configurations that you use to connect AWS resources to external code repositories\. Each connection is a resource that can be given to services such as CodePipeline to connect to a third\-party repository such as Bitbucket\. For example, you can add the connection in CodePipeline so that it triggers your pipeline when a code change is made to your third\-party code repository\. Each connection is named and associated with a unique ARN that is used to reference the connection\.

When you create a connection, the console provides a wizard you can use to install the AWS CodeStar app with your third\-party provider and associate it with a new connection\. If you have already installed the AWS CodeStar app, you can use it\.

Currently, Bitbucket is the only supported connection provider\.

**Note**  
Bitbucket connections only provide access to repositories owned by the Bitbucket account that was used to create the connection\.

For more information about connections, see the [AWS CodeStar Connections API Reference](https://docs.aws.amazon.com/codestar-connections/latest/APIReference/Welcome.html)\. For more information about the Bitbucket source action, see [CodeStarSourceConnection](action-reference-CodestarConnectionSource.md)\. 

To create or attach a policy to your IAM user or role with the permissions required to use AWS CodeStar connections, see [IAM Permissions Reference for Connections](connections-permissions.md)\. Depending on when your CodePipeline service role was created, you might need to update its permissions to support AWS CodeStar connections\. For instructions, see [Add Permissions to the CodePipeline Service Role](security-iam.md#how-to-update-role-new-services)\.

**Topics**
+ [Connect to Bitbucket](connections-create.md)
+ [Update a Pending Connection](connections-update.md)
+ [List Connections](connections-list.md)
+ [Delete a Connection](connections-delete.md)
+ [Tag Connections Resources](connections-tag.md)