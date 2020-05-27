# Delete a Connection in CodePipeline<a name="connections-delete"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

You can use the AWS Developer Tools console or the delete\-connection command in the AWS Command Line Interface \(AWS CLI\) to delete a connection\.

**Topics**
+ [Delete a Connection \(Console\)](#connections-delete-console)
+ [Delete a Connection \(CLI\)](#connections-delete-cli)

## Delete a Connection \(Console\)<a name="connections-delete-console"></a>

**To delete a connection**

1. Open the AWS Developer Tools console at [https://console.aws.amazon.com/codesuite/settings/connections](https://console.aws.amazon.com/codesuite/settings/connections)\.

1. In **Connection name**, choose the name of the connection you want to delete\. 

1. Choose **Delete**\.

1. Enter **delete** in the field to confirm, and then choose **Delete**\.
**Important**  
This action cannot be undone\.

## Delete a Connection \(CLI\)<a name="connections-delete-cli"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to delete a connection\.

To do this, use the delete\-connection command\. 

**Important**  
After you run the command, the connection is deleted\. No confirmation dialog box is displayed\. You can create a new connection, but the Amazon Resource Name \(ARN\) is never reused\.

**To delete a connection**
+ Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\)\. Use the AWS CLI to run the delete\-connection command, specifying the ARN of the connection that you want to delete\.

  ```
  aws codestar-connections delete-connection --connection-arn arn:aws:codestar-connections:us-west-2:account_id:connection/aEXAMPLE-8aad-4d5d-8878-dfcab0bc441f
  ```

  This command returns nothing\.