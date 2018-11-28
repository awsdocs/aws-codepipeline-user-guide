--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Start a Pipeline Manually in AWS CodePipeline<a name="pipelines-rerun-manually"></a>

By default, a pipeline starts automatically when it is created and any time a change is made in a source repository\. However, you might want to rerun the most recent revision through the pipeline a second time\. You can use the AWS CodePipeline console or the AWS CLI and start\-pipeline\-execution command to manually rerun the most recent revision through your pipeline\.

**Topics**
+ [Start a Pipeline Manually \(Console\)](#pipelines-rerun-manually-console)
+ [Start a Pipeline Manually \(CLI\)](#pipelines-rerun-manually-cli)

## Start a Pipeline Manually \(Console\)<a name="pipelines-rerun-manually-console"></a>

**To manually start a pipeline and run the most recent revision through a pipeline**

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. In **Name**, choose the name of the pipeline you want to start\. 

1. On the pipeline details page, choose **Release change**\. This starts the most recent revision available in each source location specified in a source action through the pipeline\.

## Start a Pipeline Manually \(CLI\)<a name="pipelines-rerun-manually-cli"></a>

**To manually start a pipeline and run the most recent version of an artifact through a pipeline**

1. Open a terminal \(Linux, macOS, or Unix\) or command prompt \(Windows\) and use the AWS CLI to run the start\-pipeline\-execution command, specifying the name of the pipeline you want to start\. For example, to start running the last change through a pipeline named *MyFirstPipeline*: 

   ```
   aws codepipeline start-pipeline-execution --name MyFirstPipeline
   ```

1. To verify success, view the returned object\. This command returns an execution ID, similar to the following:

   ```
   {
       "pipelineExecutionId": "c53dbd42-This-Is-An-Example"
   }
   ```
**Note**  
After you have started the pipeline, you can monitor its progress in the AWS CodePipeline console or by running the get\-pipeline\-state command\. For more information, see [View Pipeline Details and History \(Console\)](pipelines-view.md#pipelines-view-console) and [View Pipeline Details and History \(CLI\)](pipelines-view.md#pipelines-view-cli)\.