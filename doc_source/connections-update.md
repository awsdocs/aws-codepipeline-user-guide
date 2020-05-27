# Update a Pending Connection<a name="connections-update"></a>


|  | 
| --- |
| The connections feature is in preview release for AWS CodePipeline and is subject to change\. | 

A connection created through the AWS Command Line Interface \(AWS CLI\) or AWS CloudFormation is in `PENDING` status by default\. After you create a connection with the AWS CLI or AWS CloudFormation, use the AWS CodePipeline console to edit the connection to make its status `AVAILABLE`\.

The first time you use the console to add a new connection to a third\-party provider, you must complete the OAuth handshake with the third\-party provider using the installation associated with your connection\.

You can use the Developer Tools console to complete a pending connection\.

**To complete a connection**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all connections associated with your AWS account are displayed\.

1. In **Name**, choose the name of the pending connection you want to update\.

   **Update connection** is enabled when you choose a connection with a **Pending** status\.  
![\[Console screenshot showing the Connections list.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/connections-view.png)![\[Console screenshot showing the Connections list.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the Connections list.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Choose **Update connection**\. 

1. On the **Connect to Bitbucket Cloud** page, in **Connection name**, verify the name of your connection\.

   Under **Bitbucket Cloud apps**, choose an app installation, or choose **Install a new app** to create one\.  
![\[Console screenshot showing the Connect to Bitbucket Cloud dialog box, with the install new app button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/newreview-source-wizard-bitbucket.png)![\[Console screenshot showing the Connect to Bitbucket Cloud dialog box, with the install new app button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the Connect to Bitbucket Cloud dialog box, with the install new app button.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. On the app installation page, a message shows that the AWS CodeStar app is trying to connect to your Bitbucket account\. Choose **Grant access**\.  
![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/bitbucket-access-popup.png)![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing AWS CodeStar requests access.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. The connection ID for your new installation is displayed\. Choose **Complete connection**\.