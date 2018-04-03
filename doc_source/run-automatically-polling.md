# Start a Pipeline Automatically Using Periodic Checks<a name="run-automatically-polling"></a>

Pipelines start automatically when repository changes are detected\. You can use rules set up in Amazon CloudWatch Events or periodic checks in AWS CodePipeline to detect repository changes\. 
+ You should use Amazon CloudWatch Events to detect changes in an AWS CodeCommit repository or an Amazon S3 source and then start the pipeline automatically\. 
+ Periodic checks can be enabled or disabled using the `PollForSourceChanges` flag\. For more information, see [AWS CodePipeline Pipeline Structure Reference](reference-pipeline-structure.md)\. This option is not recommended\.