# Log AWS CodePipeline API Calls with AWS CloudTrail<a name="monitoring-cloudtrail-logs"></a>

AWS CodePipeline is integrated with CloudTrail, a service that captures API calls made by or on behalf of AWS CodePipeline in your AWS account and delivers the log files to an Amazon S3 bucket you specify\. CloudTrail captures API calls from the AWS CodePipeline console, from AWS CodePipeline commands through the AWS CLI, or from the AWS CodePipeline APIs\. Using the information collected by CloudTrail, you can determine which request was made to AWS CodePipeline, the source IP address from which the request was made, who made the request, when it was made, and so on\. To learn more about CloudTrail, including how to configure and enable it, see the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

## AWS CodePipeline Information in CloudTrail<a name="monitoring-cloudtrail-logs-log-entries-about"></a>

When CloudTrail logging is enabled in your AWS account, API calls made to AWS CodePipeline are tracked in log files\. AWS CodePipeline records are written together with other AWS service records in a log file\. CloudTrail determines when to create and write to a new file based on a time period and file size\. 

All of the AWS CodePipeline actions are logged and documented in the [AWS CodePipeline API Reference](http://docs.aws.amazon.com/codepipeline/latest/APIReference) and the [AWS CodePipeline Command Line Reference](http://docs.aws.amazon.com/cli/latest/reference/codepipeline)\. For example, calls to create, delete, and edit pipelines and create custom actions generate entries in CloudTrail log files\.

Every log entry contains information about who generated the request\. The user identity information in the log helps you determine whether the request was made with root or IAM user credentials, with temporary security credentials for a role or federated user, or by another AWS service\. For more information, see the **userIdentity** field in the [CloudTrail Event Reference](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/event_reference_top_level.html)\.

You can store your log files in your bucket for as long as you want, but you can also define Amazon S3 lifecycle rules to archive or delete log files automatically\. By default, your log files are encrypted by using Amazon S3 server\-side encryption \(SSE\)\.

You can choose to have CloudTrail publish Amazon SNS notifications when new log files are delivered if you want to take quick action upon log file delivery\. For more information, see [Configuring Amazon SNS Notifications](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)\.

You can also aggregate AWS CodePipeline log files from multiple AWS regions and multiple AWS accounts into a single Amazon S3 bucket\. For more information, see [Aggregating CloudTrail Log Files to a Single Amazon S3 Bucket](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/aggregating_logs_top_level.html)\.

## Understanding AWS CodePipeline Log File Entries<a name="monitoring-cloudtrail-logs-log-entries-example"></a>

CloudTrail log files can contain one or more log entries where each entry is made up of multiple JSON\-formatted events\. A log entry represents a single request from any source and includes information about the requested action, any parameters, the date and time of the action, and so on\. The log entries are not guaranteed to be in any particular order \(that is, they are not an ordered stack trace of the public API calls\)\.

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