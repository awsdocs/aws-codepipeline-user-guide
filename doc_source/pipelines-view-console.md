# View pipeline details and history \(console\)<a name="pipelines-view-console"></a>

You can use the CodePipeline console to view a list of all of the pipelines in your account\. You can also view details for each pipeline, including when actions last ran in the pipeline, whether a transition between stages is enabled or disabled, whether any actions have failed, and other information\. You can also view a history page that shows details for all pipeline executions for which history has been recorded\. Execution history is retained for up to 12 months\.

**Note**  
Detailed execution history is available for executions run on or after February 21, 2019\. 

**Topics**
+ [View pipeline \(console\)](#pipelines-list-console)
+ [View pipeline execution history \(console\)](#pipelines-executions-console)
+ [View execution status \(console\)](#pipelines-executions-status-console)
+ [View pipeline execution source revisions \(console\)](#pipelines-source-revisions-console)
+ [View action executions \(console\)](#pipelines-action-executions-console)
+ [View action artifacts and artifact store information \(console\)](#pipelines-action-artifacts-console)
+ [View the pipeline ARN and service role ARN \(console\)](#pipelines-settings-console)

## View pipeline \(console\)<a name="pipelines-list-console"></a>

You can view status, transitions, and artifact updates for a pipeline\.

**Note**  
After an hour, the detailed view of a pipeline stops refreshing automatically in your browser\. To view current information, refresh the page\.

**To view a pipeline**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names and creation date of all pipelines associated with your AWS account are displayed, along with links to view execution history\.

1. To see details for a single pipeline, in **Name**, choose the pipeline\. You can also select the pipeline, and then choose **View pipeline**\. A detailed view of the pipeline, including the state of each action in each stage and the state of the transitions, is displayed\.  
![\[The console view of the pipeline indicates the state of each action in each stage and the state of the transitions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-view-pipeline-pol.png)![\[The console view of the pipeline indicates the state of each action in each stage and the state of the transitions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The console view of the pipeline indicates the state of each action in each stage and the state of the transitions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   The graphical view displays the following information for each stage:
   + The stage name\.
   + Every action configured for the stage\.
   + The state of transitions between stages \(enabled or disabled\), as indicated by the state of the arrow between stages\. An enabled transition is indicated by an arrow with a **Disable transition** button next to it\. A disabled transition is indicated by an arrow with a strikeout under it and an **Enable transition** button next to it\.
   + A color bar to indicate the status of the stage:
     + Gray: No executions yet
     + Blue: In progress
     + Green: Succeeded
     + Red: Failed

   The graphical view also displays the following information about actions in each stage:
   + The name of the action\.
   + The provider of the action, such as CodeDeploy\.
   + When the action was last run\.
   + Whether the action succeeded or failed\.
   + Links to other details about the last run of the action, where available\.
   + Details about the source revisions that are running through the latest pipeline execution in the stage or, for CodeDeploy deployments, the latest source revisions that were deployed to target instances\.

1. To see the configuration details for an action in a stage of a pipeline, choose the information icon next to the action\.

1. To view the details of the provider of the action, choose the provider\. For example, in the preceding example pipeline, if you choose CodeDeploy in either the Staging or Production stages the CodeDeploy console page for the deployment group configured for that stage is displayed\.

1. To see the progress details for an action in a stage, choose **Details** when it is displayed next to an action in progress \(indicated by an **In Progress** message\)\. If the action is in progress, you see the incremental progress and the steps or actions as they occur\. 
**Note**  
Details are available for source actions that retrieve content from GitHub repositories, but not those that retrieve content from Amazon S3 buckets or CodeCommit repositories\.

1. To approve or reject actions that have been configured for manual approval, choose **Review**\.

1. To retry actions in a stage that were not completed successfully, choose **Retry**\.

1. To get more information about errors or failures for a completed action in a stage, choose **Details**\. Details from the last time the action ran, including the results of that action \(**Succeeded** or **Failed**\) are displayed\.

1. To view details about source artifacts \(output artifact that originated in the first stage of a pipeline\) that are used the latest pipeline execution for a stage, click in the details information area at the bottom of the stage\. You can view details about identifiers, such as commit IDs, check\-in comments, and the time since the artifact was created or updated\. 

1. To view details about the most recent executions for the pipeline, choose **View history**\. For past executions, you can view revision details associated with source artifacts, such as execution IDs, status, start and end times, duration, and commit IDs and messages\.

## View pipeline execution history \(console\)<a name="pipelines-executions-console"></a>

You can use the console to view the history of executions in a pipeline, including status, source revisions, and timing details for each execution\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account are displayed, along with their status\.

1. In **Name**, choose the name of the pipeline\.

1. Choose **View history**\.

1. View the status, source revisions, change details, and triggers related to each execution for your pipeline\.  
![\[View execution history.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/view-execution-history.png)![\[View execution history.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[View execution history.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Choose an execution\. The detail view shows execution details, the **Timeline** tab, and the **Visualization** tab\.  
![\[View execution details.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/view-execution-detail.png)![\[View execution details.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[View execution details.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## View execution status \(console\)<a name="pipelines-executions-status-console"></a>

You can view the pipeline status in **Status** on the execution history page\. Choose an execution ID link, and then view the action status\.

The following are valid states for pipelines, stages, and actions:


**Pipeline\-level states**  

| Pipeline state | Description | 
| --- | --- | 
| InProgress | The pipeline execution is currently running\. | 
| Stopping | The pipeline execution is stopping due to a request to either stop and wait or stop and abandon the pipeline execution\. | 
| Stopped | The stopping process is complete, and the pipeline execution is stopped\. | 
| Succeeded | The pipeline execution was completed successfully\. | 
| Superseded | While this pipeline execution was waiting for the next stage to be completed, a newer pipeline execution advanced and continued through the pipeline instead\. | 
| Failed | The pipeline execution was not completed successfully\. | 


**Stage\-level states**  

| Stage state | Description | 
| --- | --- | 
| InProgress | The stage is currently running\. | 
| Stopping | The stage execution is stopping due to a request to either stop and wait or stop and abandon the pipeline execution\. | 
| Stopped | The stopping process is complete, and the stage execution is stopped\. | 
| Succeeded | The stage was completed successfully\. | 
| Failed | The stage was not completed successfully\. | 


**Action\-level states**  

| Action state | Description | 
| --- | --- | 
| InProgress | The action is currently running\. | 
| Abandoned | The action is abandoned due to a request to stop and abandon the pipeline execution\. | 
| Succeeded | The action was completed successfully\. | 
| Failed | For approval actions, the FAILED state means the action was either rejected by the reviewer or failed due to an incorrect action configuration\. | 

## View pipeline execution source revisions \(console\)<a name="pipelines-source-revisions-console"></a>

You can view details about source artifacts \(output artifact that originated in the first stage of a pipeline\) that are used in an execution of a pipeline\. The details include identifiers, such as commit IDs, check\-in comments, and, when you use the CLI, version numbers of pipeline build actions\. For some revision types, you can view and open the URL of the commit\. Source revisions are made up of the following:
+ **Summary**: Summary information about the most recent revision of the artifact\. For GitHub and AWS CodeCommit repositories, the commit message\. For Amazon S3 buckets or actions, the user\-provided content of a codepipeline\-artifact\-revision\-summary key specified in the object metadata\. 
+ **revisionUrl**: The revision URL for the artifact revision \(for example, the external repository URL\)\. 
+ **revisionId**: The revision ID for the artifact revision\. For example, for a source change in a CodeCommit or GitHub repository, this is the commit ID\. For artifacts stored in GitHub or CodeCommit repositories, the commit ID is linked to a commit details page\. 

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account will be displayed\. 

1. Choose the name of the pipeline for which you want to view source revision details, Do one of the following: 
   + Choose **View history**\. In **Source revisions**, the source change for each execution is listed\.  
![\[Information about a revision can be viewed on the execution history page\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/view-execution-history.png)![\[Information about a revision can be viewed on the execution history page\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Information about a revision can be viewed on the execution history page\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
   + Locate an action for which you want to view source revision details, and then find the revision information at the bottom of its stage:   
![\[Information about a revision can be viewed at the bottom of a stage in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/view-changes-console-3.png)![\[Information about a revision can be viewed at the bottom of a stage in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Information about a revision can be viewed at the bottom of a stage in the CodePipeline console.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

     Choose **View current revisions** to view source information\. With the exception of artifacts stored in Amazon S3 buckets, identifiers such as commit IDs in this information detail view are linked to source information pages for the artifacts\.   
![\[View source revisions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/view-changes-console-4.png)![\[View source revisions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[View source revisions.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## View action executions \(console\)<a name="pipelines-action-executions-console"></a>

You can view action details for a pipeline, such as action execution ID, input artifacts, output artifacts, and status\. You can view action details by choosing a pipeline in the console and then choosing an execution ID\.

**Note**  
Detailed execution history is available for executions run on or after February 21, 2019\. 

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account are displayed\. 

1. Choose the name of the pipeline for which you want to view action details, and then choose **View history**\. 

1. In **Execution ID**, choose the execution ID for which you want to view action execution details\.

1. You can view the following information on the **Timeline** tab:

   1. In **Action name**, choose the link to open a details page for the action where you can view status, stage name, action name, configuration data, and artifact information\.

   1. In **Provider**, choose the link to view the action provider details\. For example, in the preceding example pipeline, if you choose CodeDeploy in either the Staging or Production stages, the CodeDeploy console page for the CodeDeploy application configured for that stage is displayed\.

## View action artifacts and artifact store information \(console\)<a name="pipelines-action-artifacts-console"></a>

You can view input and output artifact details for an action\. You can also choose a link that takes you to the artifact information for that action\. Because the artifact store uses versioning, each action execution has a unique input and output artifact location\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account are displayed\. 

1. Choose the name of the pipeline for which you want to view action details, and then choose **View history**\. 

1. In **Execution ID**, choose the execution ID for which you want to view action details\.

1. On the **Timeline** tab, in **Action name**, choose the link to open a details page for the action\.

1. On the details page, in **Execution summary**, view the status and timing of the action execution\.

1. In **Action details**, view the action provider and AWS Region where the execution runs\. In **Action configuration**, view the resource configuration for the action \(for example, the CodeBuild build project name\)\.

1. In **Artifacts**, view the artifact details in **Artifact type** and **Artifact provider**\. Choose the link under **Artifact name** to view the artifacts in the artifact store\.   
![\[View action details.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/view-action-details.png)![\[View action details.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[View action details.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## View the pipeline ARN and service role ARN \(console\)<a name="pipelines-settings-console"></a>

You can use the console to view pipeline settings, such as the pipeline ARN, the service role ARN, and the pipeline artifact store\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

   The names of all pipelines associated with your AWS account will be displayed\. 

1. Choose the name of your pipeline, and then choose **Settings** in the left\-hand navigation pane\. The page shows the following:
   + The pipeline name
   + The pipeline Amazon Resource Name \(ARN\)
   + The CodePipeline service role ARN for your pipeline
   + The pipeline version
   + The name and location of the artifact store for the pipeline