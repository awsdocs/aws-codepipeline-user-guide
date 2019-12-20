# List Connections in CodePipeline<a name="connections-list"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

You can use the AWS CLI to list your connections to third\-party code repositories\.

Use the `list-connections` command\. 

**To list connections**
+ Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\) and use the AWS CLI to run the list\-connections command:

  ```
  aws codestar-connections list-connections --provider-type Bitbucket \
  --max-results 5 --next-token: next-token
  ```

  This command returns the following output\.

  ```
  {
       "Connections": [
           {
               "ConnectionName": "my-connection",
               "ProviderType": "Bitbucket",
               "Status": "PENDING",
               "ARN": "arn:aws:codestar-connections:us-west-2:account_id:connection/connection_unique_ID_string",
               "OwnerAccountId": "account_id"
           },
           {
               "ConnectionName": "my-other-connection",
               "ProviderType": "Bitbucket",
               "Status": "AVAILABLE",
               "ARN": "arn:aws:codestar-connections:us-west-2:account_id:connection/connection_unique_ID_string",
               "OwnerAccountId": "account_id"
            },
        ],
       "NextToken": "next-token"
  }
  ```