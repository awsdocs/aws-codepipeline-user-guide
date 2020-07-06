# Events placeholder bucket reference<a name="reference-ct-placeholder-buckets"></a>

This section is a reference only\. For information about creating a pipeline with event detection resources, see [ Change detection methods to start pipelines](pipelines-about-starting.md#change-detection-methods)\.

Source actions provided by Amazon S3 and CodeCommit use event\-based change detection resources to trigger your pipeline when a change is made in the source bucket or repository\. These resources are the CloudWatch Events rules that are configured to respond to events in the pipeline source, such as a code change to the CodeCommit repository\. When you use CloudWatch Events for an Amazon S3 source, you must turn on CloudTrail so the events are logged\. CloudTrail requires an S3 bucket where it can send its digests\. You can access the log files for your CloudWatch Events resources from the placeholder bucket or from a bucket you designated\.
+ If you used the CLI or AWS CloudFormation to set up the CloudWatch Events resources, you can find your CloudTrail files in the bucket that you specified when you set up your pipeline\.
+ If you used the console to set up your pipeline with an S3 source, the console uses a CloudTrail placeholder bucket when it creates your CloudWatch Events resources for you\. CloudTrail digests are stored in the placeholder bucket in the AWS Region where the pipeline is created\.

You can change the configuration if you want to use a bucket other than the placeholder bucket\.

For more information about finding and managing your CloudTrail log files, see [Getting and Viewing Your CloudTrail Log Files](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/get-and-view-cloudtrail-log-files.html)\.

**Topics**
+ [Events placeholder bucket names by Region](#reference-ct-placeholder-buckets-list)

## Events placeholder bucket names by Region<a name="reference-ct-placeholder-buckets-list"></a>

This table lists the names of the S3 placeholder buckets that contain log files that track change detection events for pipelines with Amazon S3 source actions\.


****  

| Region name | Placeholder bucket name | Region identifier | 
| --- | --- | --- | 
| US East \(Ohio\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-us\-east\-2  | us\-east\-2 | 
| US East \(N\. Virginia\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-us\-east\-1  | us\-east\-1 | 
| US West \(N\. California\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-us\-west\-1  | us\-west\-1 | 
| US West \(Oregon\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-us\-west\-2  | us\-west\-2 | 
| Canada \(Central\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-ca\-central\-1  | ca\-central\-1 | 
| Europe \(Frankfurt\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-eu\-central\-1  | eu\-central\-1 | 
| Europe \(Ireland\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-eu\-west\-1  | eu\-west\-1 | 
| Europe \(London\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-eu\-west\-2  | eu\-west\-2 | 
| Europe \(Paris\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-eu\-west\-3  | eu\-west\-3 | 
| Europe \(Stockholm\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-eu\-north\-1  | eu\-north\-1 | 
| Asia Pacific \(Tokyo\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-ap\-northeast\-1  | ap\-northeast\-1 | 
| Asia Pacific \(Seoul\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-ap\-northeast\-2  | ap\-northeast\-2 | 
| Asia Pacific \(Singapore\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-ap\-southeast\-1  | ap\-southeast\-1 | 
| Asia Pacific \(Sydney\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-ap\-southeast\-2  | ap\-southeast\-2 | 
| Asia Pacific \(Mumbai\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-ap\-south\-1  | ap\-south\-1 | 
| South America \(São Paulo\) |  codepipeline\-cloudtrail\-placeholder\-bucket\-sa\-east\-1  | sa\-east\-1 | 