# Logging CodePipeline API calls with AWS CloudTrail<a name="monitoring-cloudtrail-logs"></a>

AWS CodePipeline is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in CodePipeline;\. CloudTrail captures all API calls for CodePipeline as events\. The calls captured include calls from the CodePipeline console and code calls to the CodePipeline API operations\. If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for CodePipeline\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the information collected by CloudTrail, you can determine the request that was made to CodePipeline, the IP address from which the request was made, who made the request, when it was made, and additional details\. 

To learn more about CloudTrail, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

## CodePipeline information in CloudTrail<a name="service-name-info-in-cloudtrail"></a>

CloudTrail is enabled on your AWS account when you create the account\. When activity occurs in CodePipeline, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For an ongoing record of events in your AWS account, including events for CodePipeline, create a trail\. A *trail* enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all AWS Regions\. The trail logs events from all Regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see the following: 
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

All CodePipeline actions are logged by CloudTrail and are documented in the [CodePipeline API Reference](http://docs.aws.amazon.com/codepipeline/latest/APIReference)\. For example, calls to the `CreatePipeline`, `GetPipelineExecution` and `UpdatePipeline` actions generate entries in the CloudTrail log files\. 

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ Whether the request was made with root or AWS Identity and Access Management \(IAM\) user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

## Understanding CodePipeline log file entries<a name="monitoring-cloudtrail-logs-log-entries-example"></a>

A trail is a configuration that enables delivery of events as log files to an Amazon S3 bucket that you specify\. CloudTrail log files contain one or more log entries\. An event represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. CloudTrail log files aren't an ordered stack trace of the public API calls, so they don't appear in any specific order\. 

The following example shows a CloudTrail log entry for an update pipeline event, where a pipeline named MyFirstPipeline has been edited by the user named JaneDoe\-CodePipeline with the account ID 80398EXAMPLE\. The user changed the name of the source stage of a pipeline from `Source` to `MySourceStage`\. Because both the `requestParameters` and the `responseElements` elements in the CloudTrail log contain the entire structure of the edited pipeline, those elements have been abbreviated in the following example\. **Emphasis** has been added to the `requestParameters` portion of the pipeline where the change occurred, the previous version number of the pipeline, and the `responseElements` portion, which shows the version number incremented by 1\. Edited portions are marked with ellipses \(\.\.\.\) to illustrate where more data appears in a real log entry\.

```
{
 "eventVersion":"1.03",
  "userIdentity": {
   "type":"IAMUser",
   "principalId":"AKIAI44QH8DHBEXAMPLE",
   "arn":"arn:aws:iam::80398EXAMPLE:user/JaneDoe-CodePipeline",
   "accountId":"80398EXAMPLE",
   "accessKeyId":"AKIAIOSFODNN7EXAMPLE",
   "userName":"JaneDoe-CodePipeline",
   "sessionContext": {
	  "attributes":{
	   "mfaAuthenticated":"false",
	   "creationDate":"2015-06-17T14:44:03Z"
	   }
	  },
	"invokedBy":"signin.amazonaws.com"},
	"eventTime":"2015-06-17T19:12:20Z",
	"eventSource":"codepipeline.amazonaws.com",
	"eventName":"UpdatePipeline",
	"awsRegion":"us-east-2",
	"sourceIPAddress":"192.0.2.64",
	"userAgent":"signin.amazonaws.com",
	"requestParameters":{
	  "pipeline":{
		 "version":1,
		 "roleArn":"arn:aws:iam::80398EXAMPLE:role/AWS-CodePipeline-Service",
		 "name":"MyFirstPipeline",
		 "stages":[
		   {
		   "actions":[
		    {
          "name":"MySourceStage",
		      "actionType":{
			     "owner":"AWS",
			     "version":"1",
			     "category":"Source",
			     "provider":"S3"
			  },
			 "inputArtifacts":[],
			 "outputArtifacts":[
			  {"name":"MyApp"}
			  ],
			 "runOrder":1,
			 "configuration":{
			  "S3Bucket":"awscodepipeline-demobucket-example-date",
			  "S3ObjectKey":"sampleapp_linux.zip"
			   }
		    }
		   ],
		     "name":"Source"
		   },
		   (...)
               },
	"responseElements":{
	  "pipeline":{
	    "version":2,
	    (...)
           },
	    "requestID":"2c4af5c9-7ce8-EXAMPLE",
	    "eventID":""c53dbd42-This-Is-An-Example"",
	    "eventType":"AwsApiCall",
	    "recipientAccountId":"80398EXAMPLE"
	    }
      ]
}
```