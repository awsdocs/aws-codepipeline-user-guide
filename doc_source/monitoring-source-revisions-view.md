# View Current Source Revision Details in a Pipeline in AWS CodePipeline<a name="monitoring-source-revisions-view"></a>

You can use the AWS CodePipeline console or the AWS CLI to view details about source artifacts \(output artifact that originated in the first stage of a pipeline\) that are used in an execution of a pipeline\. The details include identifiers, such as commit IDs, check\-in comments, time since the artifact was created or updated and, when you use the CLI, version numbers of build actions\. For some revision types, you can view and open the URL of the commit for the artifact version\.


+ [View Current Source Revision Details in a Pipeline \(Console\)](#monitoring-source-revisions-view-console)
+ [View Current Source Revision Details in a Pipeline \(CLI\)](#monitoring-source-revisions-view-cli)

## View Current Source Revision Details in a Pipeline \(Console\)<a name="monitoring-source-revisions-view-console"></a>

You can use the AWS CodePipeline console to view information about the most recent source revisions that were included in a pipeline execution\. 

**To view source revisions in a pipeline**

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codepipeline](http://console.aws.amazon.com/codepipeline)\.

   The names of all pipelines associated with your AWS account will be displayed\. 

1. Choose the name of the pipeline for which you want to view source revision details\. 

1. Locate an action for which you want to view source revision details, and then find the revision information at the bottom of its stage:   
![\[Information about a revision can be viewed at the bottom of a stage
                            in the AWS CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/view-changes-console-3.png)![\[Information about a revision can be viewed at the bottom of a stage
                            in the AWS CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Information about a revision can be viewed at the bottom of a stage
                            in the AWS CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Click in the details area to view more information about the artifact, including the length of time since the artifact was committed\. With the exception of artifacts stored in Amazon S3 buckets, identifiers such as commit IDs in this information detail view are linked to source information pages for the artifacts\.   
![\[Click in the details area to view more information about the
                            artifact.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/view-changes-console-4.png)![\[Click in the details area to view more information about the
                            artifact.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Click in the details area to view more information about the
                            artifact.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## View Current Source Revision Details in a Pipeline \(CLI\)<a name="monitoring-source-revisions-view-cli"></a>

You can run the get\-pipeline\-execution command to view information about the most recent source revisions that were included in a pipeline execution\. After you first run the get\-pipeline\-state command to get details about all stages in a pipeline, you identify the execution ID that applies to a particular stage for which you want source revision details, and then use that execution ID in the get\-pipeline\-execution command\. \(Stages in a pipeline might have been last successfully completed during different pipeline runs and can therefore have different execution IDs\.\)

In other words, if you want to view details about artifacts currently in the Staging stage, run the get\-pipeline\-state command, identify the current execution ID of the Staging stage, and then run the get\-pipeline\-execution command using that execution ID\. 

**To view source revisions in a pipeline**

1. Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\) and use the AWS CLI to run the [get\-pipeline\-state](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-state.html) command\. For a pipeline named *MyFirstPipeline*, you would type:

   ```
   aws codepipeline get-pipeline-state --name MyFirstPipeline
   ```

   This command returns the most recent state of a pipeline, including the latest pipeline execution ID for each stage\.

1. To view details about a pipeline execution, run the get\-pipeline\-execution command, specifying the unique name of the pipeline and the pipeline execution ID of the particular execution for which you want to view artifact details\. For example, to view details about the execution of a pipeline named *MyFirstPipeline*, with the execution ID 3137f7cb\-7cf7\-039j\-s83l\-d7eu3EXAMPLE, you would type the following:

   ```
   aws codepipeline get-pipeline-execution --pipeline-name MyFirstPipeline --pipeline-execution-id 3137f7cb-7cf7-039j-s83l-d7eu3EXAMPLE
   ```

   This command returns information about each source revision that is part of the pipeline execution and identifying information about the pipeline\. Only information about pipeline stages that were included in that execution are included\. There might be other stages in the pipeline that were not part of that pipeline execution\.

   The following example shows the returned data for a portion of pipeline named *MyFirstPipeline*, where an artifact named "MyApp" is stored in a GitHub repository:

1. 

   ```
   {
       "pipelineExecution": {
           "artifactRevisions": [
               {
                   "created": 1427298837.7689769,
                   "name": "MyApp",
                   "revisionChangeIdentifier": "1427298921.3976923",
                   "revisionId": "7636d59f3c461cEXAMPLE8417dbc6371",
                   "revisionSummary": "Updating the application for feature 12-4820",
                   "revisionUrl": "https://api.github.com/repos/anycompany/MyApp/git/commits/7636d59f3c461cEXAMPLE8417dbc6371"
               }
               //More revisions might be listed here
           ],
           "pipelineExecutionId": "3137f7cb-7cf7-039j-s83l-d7eu3EXAMPLE",
           "pipelineName": "MyFirstPipeline",
           "pipelineVersion": 2,
           "status": "Succeeded"
       }
   }
   ```