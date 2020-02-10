# Working with Connections in CodePipeline<a name="connections"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

*Connections* are configurations that you use to connect AWS resources to external code repositories\. Each connection is a resource that can be given to services such as CodePipeline to connect to a third\-party repository such as Bitbucket\. For example, you can add the connection in CodePipeline so that it triggers your pipeline when a code change is made to your third\-party code repository\. Each connection is named and associated with a unique ARN that is used to reference the connection\.

When you create a connection, the console provides a wizard you can use to install the CodeStar app with your third\-party provider and associate it with a new connection\. If you have already installed the CodeStar app, you can use it\.

Currently, Bitbucket is the only supported connection provider\.

For more information about connections, see the [AWS CodeStar API Reference](https://docs.aws.amazon.com/codestar-connections/latest/APIReference/Welcome.html)\. For more information about the Bitbucket source action, see [CodeStarSourceConnection](action-reference-CodestarConnectionSource.md)\.

**Topics**
+ [Connect to Bitbucket](connections-create.md)
+ [List Connections](connections-list.md)
+ [Delete a Connection](connections-delete.md)