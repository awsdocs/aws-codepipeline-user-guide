# Delete a Connection in CodePipeline<a name="connections-delete"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

You can use the AWS CLI to delete a connection\.

Use the `delete-connection` command\. 

**Important**  
After you run the command, the connection is deleted\. No confirmation dialog box is displayed\. You can create a new connection, but the ARN is never reused\.

**To delete a connection**
+ Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\) and use the AWS CLI to run the delete\-connection command, specifying the ARN of the connection you want to delete:

  ```
  aws codestar-connections delete-connection \ 
  --connection-arn arn:aws:codestar-connections:eu-central-1:account_id:connection/connection_unique_ID_string
  ```

  This command returns nothing\.