# Input and output artifacts<a name="welcome-introducing-artifacts"></a>

CodePipeline integrates with development tools to check for code changes and then build and deploy through all of the stages of the continuous delivery process\.

Stages use input and output artifacts that are stored in the Amazon S3 artifact bucket you chose when you created the pipeline\. CodePipeline zips and transfers the files for input or output artifacts as appropriate for the action type in the stage\. 

For example:

1. CodePipeline triggers your pipeline to run when there is a commit to the source repository, providing the output artifact \(any files to be built\) from the **Source** stage\.

1. The output artifact \(any files to be built\) from the previous step is ingested as an input artifact to the **Build** stage\. An output artifact \(the built application\) from the **Build** stage can be an updated application or an updated Docker image built to a container\.

1. The output artifact from the previous step \(the built application\) is ingested as an input artifact to the **Deploy** stage, such as staging or production environments in the AWS Cloud\. You can deploy applications to a deployment fleet, or you can deploy container\-based applications to tasks running in ECS clusters\.

When you create or edit an action, you designate the input and output artifact or artifacts for the action\. In this example for a two\-stage pipeline with a **Source** and **Deploy** stage, in **Edit Action**, you choose the artifact name of the source action for the input artifact for the deploy action\.

![\[A high-level artifacts workflow in CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/example-artifact-dropdown.png)![\[A high-level artifacts workflow in CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A high-level artifacts workflow in CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

The following diagram shows a high\-level artifact workflow between stages in CodePipeline\.

![\[A high-level artifacts workflow in CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/Hi-Level-PipelineFlow.png)![\[A high-level artifacts workflow in CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A high-level artifacts workflow in CodePipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
+ When you use the console to create your first pipeline, CodePipeline creates an Amazon S3 bucket in the same AWS Region to store items for all pipelines\. Every time you use the console to create another pipeline in that Region, CodePipeline creates a folder for that pipeline in the bucket\. It uses that folder to store artifacts for your pipeline as the automated release process runs\. This bucket is named codepipeline\-*region*\-*12345EXAMPLE*, where *region* is the AWS Region in which you created the pipeline, and *12345EXAMPLE* is a 12\-digit random number that ensures the bucket name is unique\. 
**Note**  
CodePipeline truncates artifact names, which can cause some bucket names to appear similar\. Even though the artifact name appears to be truncated, CodePipeline maps to the artifact bucket in a way that is not affected by artifacts with truncated names\. The pipeline can function normally\. This is not an issue with the folder or artifacts\. There is a 100\-character limit to pipeline names\. Although the artifact folder name might appear to be shortened, it is still unique for your pipeline\.

  When you create or edit a pipeline, you must have an artifact bucket in the pipeline Region and you must have one artifact bucket per Region where you plan to execute an action\. If you use the console to create a pipeline or cross\-Region actions, default artifact buckets are configured by CodePipeline in the Regions where you have actions\.

  If you use the AWS CLI to create a pipeline, you can store the artifacts for that pipeline in any Amazon S3 bucket as long as that bucket is in the same AWS Region as the pipeline\. You might do this if you are concerned about exceeding the limits of Amazon S3 buckets allowed for your account\. If you use the AWS CLI to create or edit a pipeline, and you add a cross\-Region action \(an action with an AWS provider in a Region different from your pipeline\), you must provide an artifact bucket for each additional Region where you plan to execute an action\.
+ Every action has a type\. Depending on the type, the action might have one or both of the following:
  + An input artifact, which is the artifact it consumes or works on over the course of the action run\.
  + An output artifact, which is the output of the action\.

  Every output artifact in the pipeline must have a unique name\. Every input artifact for an action must match the output artifact of an action earlier in the pipeline, whether that action is immediately before the action in a stage or runs in a stage several stages earlier\. 

  An artifact can be worked on by more than one action\.

  The following shows how input artifacts and output artifacts are produced and consumed in a pipeline:  
![\[Input artifacts and output artifacts are produced and consumed in a pipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/codepipeline-artifactsexplained.png)![\[Input artifacts and output artifacts are produced and consumed in a pipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Input artifacts and output artifacts are produced and consumed in a pipeline.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)