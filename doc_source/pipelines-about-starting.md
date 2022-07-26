# Start a pipeline in CodePipeline<a name="pipelines-about-starting"></a>

When a pipeline execution starts, it runs a revision through every stage and action in the pipeline\.

There are three ways to start a pipeline execution in AWS CodePipeline:
+ **When a source changes**: Your pipeline starts when someone makes a change to your source repository or source location\. 
**Note**  
Your pipeline runs automatically only when something changes in the source repository and branch that you have defined\.
+ **Manually**: You can use the console or the AWS CLI to start a pipeline manually\. For information, see [Start a pipeline manually](pipelines-rerun-manually.md)\.
+ **On a schedule**: You can set up a schedule that starts your pipeline\. See [Start a pipeline on a schedule](pipelines-trigger-source-schedule.md)\.

**Topics**
+ [Source actions and change detection methods](#change-detection-methods)
+ [Amazon ECR source actions and CloudWatch Events](create-cwe-ecr-source.md)
+ [Amazon S3 source actions and CloudWatch Events](create-cloudtrail-S3-source.md)
+ [Bitbucket connections](connections-bitbucket.md)
+ [CodeCommit source actions and CloudWatch Events](triggering.md)
+ [GitHub connections](connections-github.md)
+ [GitHub Enterprise Server connections](connections-ghes.md)
+ [Start a pipeline manually](pipelines-rerun-manually.md)
+ [Start a pipeline on a schedule](pipelines-trigger-source-schedule.md)

## Source actions and change detection methods<a name="change-detection-methods"></a>

When you add a source action to your pipeline, the actions work with additional resources described in the table\.

**Note**  
The CodeCommit and S3 source actions require either a configured change detection resource \(a CloudWatch Events rule\) or use the option to poll the repository for source changes\. For pipelines with a Bitbucket, GitHub, or GitHub Enterprise Server source action, you do not have to set up a webhook or default to polling\. The connections action manages change detection for you\. 


****  

| Source | Uses additional resources? | Steps | 
| --- | --- | --- | 
| Amazon S3 | This source action uses additional resources\. When you use the CLI or CloudFormation to create this action, you also create and manage these resources\. | See [Create a pipeline in CodePipeline](pipelines-create.md) and [Amazon S3 source actions and CloudWatch Events](create-cloudtrail-S3-source.md)  | 
| Bitbucket | This source action uses a connection resource\. | See [Bitbucket connections](connections-bitbucket.md) | 
| AWS CodeCommit | Amazon CloudWatch Events \(recommended\)\. This is the default for pipelines with an CodeCommit source created or edited in the console\. | See [Create a pipeline in CodePipeline](pipelines-create.md) and [ CodeCommit source actions and CloudWatch Events](triggering.md) | 
| Amazon ECR | Amazon CloudWatch Events\. This is created by the wizard for pipelines with an Amazon ECR source created or edited in the console\. | See [Create a pipeline in CodePipeline](pipelines-create.md) and [ Amazon ECR source actions and CloudWatch Events](create-cwe-ecr-source.md)\. | 
| GitHub or GitHub Enterprise Cloud | This source action uses a connection resource\. | See [GitHub connections](connections-github.md) | 
| GitHub Enterprise Server | This source action uses a connection resource and a host resource\. | See [GitHub Enterprise Server connections](connections-ghes.md) | 

If you have a pipeline that uses polling, you can update it to use the recommended detection method\. For more information, see [Update polling pipelines to the recommended change detection method](trigger-S3-migration-cwe.md)\.

If you want to turn off change detection for a source action that uses connections, see [CodeStarSourceConnection for Bitbucket, GitHub, and GitHub Enterprise Server actions](action-reference-CodestarConnectionSource.md)\.