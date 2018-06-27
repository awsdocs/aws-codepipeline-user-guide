# Start a Pipeline Automatically Using Periodic Checks<a name="run-automatically-polling"></a>

Pipelines start automatically when repository changes are detected, and one method of change detection is periodic checks\. Periodic checks can be enabled or disabled using the `PollForSourceChanges` flag\. For pipelines created or updated using the CLI, this parameter defaults to true, but this is not the recommended configuration\. Instead, update your pipeline to use the recommended change\-detection method and then set this parameter to false\.

For more information about creating a pipeline with the recommended configuration, see [Create a Pipeline \(Console\)](pipelines-create.md#pipelines-create-console) and [Create a Pipeline \(CLI\)](pipelines-create.md#pipelines-create-cli)\. For more information about updating an action or pipeline with the recommended configuration, see [Edit a Pipeline \(Console\)](pipelines-edit.md#pipelines-edit-console) and [Edit a Pipeline \(CLI\)](pipelines-edit.md#pipelines-edit-cli)\.

For more information, see [Change\-Detection Methods Used to Start Pipelines Automatically](pipelines-about-starting.md#change-detection-methods)\.