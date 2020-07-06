# View pipeline details and history \(CLI\)<a name="pipelines-view-cli"></a>

You can run the following commands to view details about your pipelines and pipeline executions:
+  list\-pipelines command to view a summary of all of the pipelines associated with your AWS account\.
+ get\-pipeline command to review details of a single pipeline\.
+ list\-pipeline\-executions to view summaries of the most recent executions for a pipeline\.
+ get\-pipeline\-execution to view information about an execution of a pipeline, including details about artifacts, the pipeline execution ID, and the name, version, and status of the pipeline\. 
+ get\-pipeline\-state command to view pipeline, stage, and action status\.
+ list\-action\-executions to view action execution details for a pipeline\. 

**Topics**
+ [View pipeline \(CLI\)](#pipelines-list-cli)
+ [View execution history \(CLI\)](#pipelines-executions-cli)
+ [View execution status \(CLI\)](#pipelines-executions-status-cli)
+ [View source revisions \(CLI\)](#pipelines-source-revisions-cli)
+ [View action executions \(CLI\)](#pipelines-action-executions-cli)

## View pipeline \(CLI\)<a name="pipelines-list-cli"></a>

You can view pipeline details\.

1. Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\) and use the AWS CLI to run the [list\-pipelines](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/list-pipelines.html) command:

   ```
   aws codepipeline list-pipelines
   ```

   This command returns a list of all of the pipelines associated with your AWS account\.

1. To view details about a pipeline, run the [get\-pipeline](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline.html) command, specifying the unique name of the pipeline\. For example, to view details about a pipeline named *MyFirstPipeline*, enter the following:

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline
   ```

   This command returns the structure of the pipeline\.

## View execution history \(CLI\)<a name="pipelines-executions-cli"></a>

You can view pipeline execution history\.
+ To view details about past executions of a pipeline, run the [list\-pipeline\-executions](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/list-pipeline-executions.html) command, specifying the unique name of the pipeline\. For example, to view details about the current state of a pipeline named *MyFirstPipeline*, enter the following:

  ```
  aws codepipeline list-pipeline-executions --pipeline-name MyFirstPipeline
  ```

  This command returns summary information about all pipeline executions for which history has been recorded\. The summary includes start and end times, duration, and status\.

  The following example shows the returned data for a pipeline named *MyFirstPipeline* that has had three executions:

  ```
  {
      "pipelineExecutionSummaries": [
          {
              "lastUpdateTime": 1496380678.648,
              "pipelineExecutionId": "7cf7f7cb-3137-539g-j458-d7eu3EXAMPLE",
              "startTime": 1496380258.243,
              "status": "Succeeded"
          },
          {
              "lastUpdateTime": 1496591045.634,
              "pipelineExecutionId": "3137f7cb-8d494hj4-039j-d84l-d7eu3EXAMPLE",
              "startTime": 1496590401.222,
              "status": "Succeeded"
          },
          {
              "lastUpdateTime": 1496946071.6456,
              "pipelineExecutionId": "4992f7jf-7cf7-913k-k334-d7eu3EXAMPLE",
              "startTime": 1496945471.5645,
              "status": "Succeeded"
          }
      ]
  }
  ```

  To view more details about a pipeline execution, run the [get\-pipeline\-execution](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-execution.html), specifying the unique ID of the pipeline execution\. For example, to view more details about the first execution in the previous example, enter the following: 

  ```
  aws codepipeline get-pipeline-execution --pipeline-name MyFirstPipeline --pipeline-execution-id 7cf7f7cb-3137-539g-j458-d7eu3EXAMPLE
  ```

  This command returns summary information about an execution of a pipeline, including details about artifacts, the pipeline execution ID, and the name, version, and status of the pipeline\. 

  The following example shows the returned data for a pipeline named *MyFirstPipeline*:

  ```
  {
      "pipelineExecution": {
          "pipelineExecutionId": "3137f7cb-7cf7-039j-s83l-d7eu3EXAMPLE",
          "pipelineVersion": 2,
          "pipelineName": "MyFirstPipeline",
          "status": "Succeeded",
          "artifactRevisions": [
              {
                  "created": 1496380678.648,
                  "revisionChangeIdentifier": "1496380258.243",
                  "revisionId": "7636d59f3c461cEXAMPLE8417dbc6371",
                  "name": "MyApp",
                  "revisionSummary": "Updating the application for feature 12-4820"
              }
          ]
      }
  }
  ```

## View execution status \(CLI\)<a name="pipelines-executions-status-cli"></a>

You can view pipeline, stage, and action status\.
+ To view details about the current state of a pipeline, run the [get\-pipeline\-state](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-state.html) command, specifying the unique name of the pipeline\. For example, to view details about the current state of a pipeline named *MyFirstPipeline*, enter the following:

  ```
  aws codepipeline get-pipeline-state --name MyFirstPipeline
  ```

  This command returns the current status of all stages of the pipeline and the status of the actions in those stages\.

  The following example shows the returned data for a three\-stage pipeline named *MyFirstPipeline*, where the first two stages and actions show success, the third shows failure, and the transition between the second and third stages is disabled:

  ```
  {
      "updated": 1427245911.525,
      "created": 1427245911.525,
      "pipelineVersion": 1,
      "pipelineName": "MyFirstPipeline",
      "stageStates": [
          {
              "actionStates": [
                  {
                      "actionName": "Source",
                      "entityUrl": "https://console.aws.amazon.com/s3/home?#",
                      "latestExecution": {
                          "status": "Succeeded",
                          "lastStatusChange": 1427298837.768
                      }
                  }
              ],
              "stageName": "Source"
          },
          {
              "actionStates": [
                  {
                      "actionName": "Deploy-CodeDeploy-Application",
                      "entityUrl": "https://console.aws.amazon.com/codedeploy/home?#",
                      "latestExecution": {
                          "status": "Succeeded",
                          "lastStatusChange": 1427298939.456,
                          "externalExecutionUrl": "https://console.aws.amazon.com/?#",
                          "externalExecutionId": ""c53dbd42-This-Is-An-Example"",
                          "summary": "Deployment Succeeded"
                      }
                  }
              ],
              "inboundTransitionState": {
                  "enabled": true
              },
              "stageName": "Staging"
          },
          {
              "actionStates": [
                  {
                      "actionName": "Deploy-Second-Deployment",
                      "entityUrl": "https://console.aws.amazon.com/codedeploy/home?#",
                      "latestExecution": {
                          "status": "Failed",
                          "errorDetails": {
                              "message": "Deployment Group is already deploying deployment ...",
                              "code": "JobFailed"
                          },
                          "lastStatusChange": 1427246155.648
                      }
                  }
              ],
              "inboundTransitionState": {
                  "disabledReason": "Disabled while I investigate the failure",
                  "enabled": false,
                  "lastChangedAt": 1427246517.847,
                  "lastChangedBy": "arn:aws:iam::80398EXAMPLE:user/CodePipelineUser"
              },
              "stageName": "Production"
          }
      ]
  }
  ```

## View source revisions \(CLI\)<a name="pipelines-source-revisions-cli"></a>

You can view details about source artifacts \(output artifacts that originated in the first stage of a pipeline\) that are used in an execution of a pipeline\. The details include identifiers, such as commit IDs, check\-in comments, time since the artifact was created or updated and, when you use the CLI, version numbers of build actions\. For some revision types, you can view and open the URL of the commit for the artifact version\. Source revisions are made up of the following:
+ **Summary**: Summary information about the most recent revision of the artifact\. For GitHub and AWS CodeCommit repositories, the commit message\. For Amazon S3 buckets or actions, the user\-provided content of a codepipeline\-artifact\-revision\-summary key specified in the object metadata\. 
+ **revisionUrl**: The commit ID for the artifact revision\. For artifacts stored in GitHub or AWS CodeCommit repositories, the commit ID is linked to a commit details page\. 

You can run the get\-pipeline\-execution command to view information about the most recent source revisions that were included in a pipeline execution\. After you first run the get\-pipeline\-state command to get details about all stages in a pipeline, you identify the execution ID that applies to a stage for which you want source revision details\. Then you use the execution ID in the get\-pipeline\-execution command\. \(Because stages in a pipeline might have been last successfully completed during different pipeline runs, they can have different execution IDs\.\)

In other words, if you want to view details about artifacts currently in the Staging stage, run the get\-pipeline\-state command, identify the current execution ID of the Staging stage, and then run the get\-pipeline\-execution command using that execution ID\. 

**To view source revisions in a pipeline**

1. Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\) and use the AWS CLI to run the [get\-pipeline\-state](http://docs.aws.amazon.com/cli/latest/reference/codepipeline/get-pipeline-state.html) command\. For a pipeline named *MyFirstPipeline*, you would enter:

   ```
   aws codepipeline get-pipeline-state --name MyFirstPipeline
   ```

   This command returns the most recent state of a pipeline, including the latest pipeline execution ID for each stage\.

1. To view details about a pipeline execution, run the get\-pipeline\-execution command, specifying the unique name of the pipeline and the pipeline execution ID of the execution for which you want to view artifact details\. For example, to view details about the execution of a pipeline named *MyFirstPipeline*, with the execution ID 3137f7cb\-7cf7\-039j\-s83l\-d7eu3EXAMPLE, you would enter the following:

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

## View action executions \(CLI\)<a name="pipelines-action-executions-cli"></a>

You can view action execution details for a pipeline, such as action execution ID, input artifacts, output artifacts, execution result, and status\. You provide the Execution ID filter to return a listing of actions in a pipeline execution:

**Note**  
Detailed execution history is available for executions run on or after February 21, 2019\. 
+ To view action executions for a pipeline, do one of the following:
  + To view details for all action executions in a pipeline, run the list\-action\-executions command, specifying the unique name of the pipeline\. For example, to view action executions in a pipeline named *MyFirstPipeline*, enter the following:

    ```
    aws codepipeline list-action-executions --pipeline-name MyFirstPipeline
    ```

    The following shows a portion of sample output for this command:

    ```
    {
        "actionExecutionDetails": [
            {
                "actionExecutionId": "ID",
                "lastUpdateTime": 1552958312.034,
                "startTime": 1552958246.542,
                "pipelineExecutionId": "Execution_ID",
                "actionName": "Build",
                "status": "Failed",
                "output": {
                    "executionResult": {
                        "externalExecutionUrl": "Project_ID",
                        "externalExecutionSummary": "Build terminated with state: FAILED",
                        "externalExecutionId": "ID"
                    },
                    "outputArtifacts": []
                },
                "stageName": "Beta",
                "pipelineVersion": 8,
                "input": {
                    "configuration": {
                        "ProjectName": "java-project"
                    },
                    "region": "us-east-1",
                    "inputArtifacts": [
                        {
                            "s3location": {
                                "bucket": "codepipeline-us-east-1-ID",
                                "key": "MyFirstPipeline/MyApp/Object.zip"
                            },
                            "name": "MyApp"
                        }
                    ],
                    "actionTypeId": {
                        "version": "1",
                        "category": "Build",
                        "owner": "AWS",
                        "provider": "CodeBuild"
                    }
                }
            },
    
    . . .
    ```
  + To view all action executions in a pipeline execution, run the list\-action\-executions command, specifying the unique name of the pipeline and the execution ID\. For example, to view action executions for an *Execution\_ID*, enter the following:

    ```
    aws codepipeline list-action-executions --pipeline-name MyFirstPipeline --filter pipelineExecutionId=Execution_ID
    ```
  + The following shows a portion of sample output for this command:

    ```
    {
        "actionExecutionDetails": [
            {
                "stageName": "Beta",
                "pipelineVersion": 8,
                "actionName": "Build",
                "status": "Failed",
                "lastUpdateTime": 1552958312.034,
                "input": {
                    "configuration": {
                        "ProjectName": "java-project"
                    },
                    "region": "us-east-1",
                    "actionTypeId": {
                        "owner": "AWS",
                        "category": "Build",
                        "provider": "CodeBuild",
                        "version": "1"
                    },
                    "inputArtifacts": [
                        {
                            "s3location": {
                                "bucket": "codepipeline-us-east-1-ID",
                                "key": "MyFirstPipeline/MyApp/Object.zip"
                            },
                            "name": "MyApp"
                        }
                    ]
                },
    
    
    . . .
    ```