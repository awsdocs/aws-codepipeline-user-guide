# Start a Pipeline Automatically Using Periodic Checks<a name="run-automatically-polling"></a>

Pipelines start automatically when repository changes are detected\. You can use rules set up in Amazon CloudWatch Events or periodic checks in AWS CodePipeline to detect repository changes\. 

Although periodic checks are the default way to start your pipeline automatically when something changes, Amazon CloudWatch Events is recommended\. It's faster and more configurable\.

+ Amazon CloudWatch Events can be set up to detect changes in an AWS CodeCommit repository and start the pipeline automatically\. 

+ Periodic checks are enabled by default, but can be disabled using the `PollForSourceChanges` flag\. For more information, see \.