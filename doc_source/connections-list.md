# List Connections in CodePipeline<a name="connections-list"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

You can use the Developer Tools console or the list\-connections command in the AWS Command Line Interface \(AWS CLI\) to view a list of connections in your account\.

## List Connections \(Console\)<a name="connections-list-console"></a>

**To list connections**

1. Open the AWS Developer Tools console at [https://console.aws.amazon.com/codesuite/settings/connections](https://console.aws.amazon.com/codesuite/settings/connections)\.

1. View the name, status, and ARN for your connections\.

## List Connections \(CLI\)<a name="connections-list-cli"></a>

You can use the AWS CLI to list your connections to third\-party code repositories\.

To do this, use the list\-connections command\. 

**To list connections**
+ Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\), and use the AWS CLI to run the list\-connections command\.

  ```
  aws codestar-connections list-connections --provider-type Bitbucket
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
               "ARN": "arn:aws:codestar-connections:us-west-2:account_id:connection/aEXAMPLE-8aad-4d5d-8878-dfcab0bc441f",
               "OwnerAccountId": "account_id"
           },
           {
               "ConnectionName": "my-other-connection",
               "ProviderType": "Bitbucket",
               "Status": "AVAILABLE",
               "ARN": "arn:aws:codestar-connections:us-west-2:account_id:connection/aEXAMPLE-8aad-4d5d-8878-dfcab0bc441f",
               "OwnerAccountId": "account_id"
            },
        ],
       "NextToken": "next-token"
  }
  ```