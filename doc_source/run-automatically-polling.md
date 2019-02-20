# Use Periodic Checks to Start a Pipeline<a name="run-automatically-polling"></a>

Pipelines start automatically when repository changes are detected\. One change detection method is periodic checks\. Periodic checks can be enabled or disabled using the `PollForSourceChanges` flag\. If you use the CLI to create or edit a pipeline, this parameter defaults to `true`\. This is not the recommended configuration\. Instead, edit your pipeline to use the recommended change\-detection method and then set this parameter to `false`\.

For more information about creating a pipeline with the recommended configuration, see [Create a Pipeline \(Console\)](pipelines-create.md#pipelines-create-console) and [Create a Pipeline \(CLI\)](pipelines-create.md#pipelines-create-cli)\. For more information about updating an action or pipeline with the recommended configuration, see [Edit a Pipeline \(Console\)](pipelines-edit.md#pipelines-edit-console) and [Edit a Pipeline \(CLI\)](pipelines-edit.md#pipelines-edit-cli)\.

For more information, see [Change\-Detection Methods Used to Start Pipelines Automatically](pipelines-about-starting.md#change-detection-methods)\.