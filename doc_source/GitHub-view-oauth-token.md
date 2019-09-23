# View Your Authorized OAuth Apps<a name="GitHub-view-oauth-token"></a>

CodePipeline uses OAuth tokens to integrate with GitHub\. GitHub tracks the permissions of the OAuth token for CodePipeline\.

When you use the console to create or edit a pipeline for a GitHub action, you choose **Connect to GitHub** in the console\. When you use the console to connect to GitHub, AWS creates a default AWS\-managed OAuth token for you\. For the steps to connect to GitHub, see [Pipeline Error: I receive a pipeline error that says: "Could not access the GitHub repository" or "Unable to connect to the GitHub repository"](troubleshooting.md#troubleshooting-gs2)\.

When your pipeline connects to the repository, it uses GitHub credentials to connect to GitHub\. You do not manage the token in any way, but you can view your connection information to verify your authorized OAuth applications in GitHub\.

Use these steps to review your authorized OAuth applications\.

1. In GitHub, from the drop\-down option on your profile photo, choose **Settings**\.

1. Choose **Applications** and then choose **Authorized OAuth Apps**\.

1. Review your authorized apps\.  
![\[Review authorized apps in GitHub.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/oauth-apps.png)![\[Review authorized apps in GitHub.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Review authorized apps in GitHub.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)