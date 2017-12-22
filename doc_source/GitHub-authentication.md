# Configure GitHub Authentication<a name="GitHub-authentication"></a>

AWS CodePipeline uses GitHub OAuth tokens and personal access tokens to access your GitHub repositories and retrieve the latest changes\. There are two ways to configure authentication in GitHub:

+ AWS creates a default AWS\-managed OAuth token when you use the console to create or update pipelines\. 

+ You can create and manage your own customer\-generated personal access tokens\. You need personal access tokens when you use the CLI, SDK, or AWS CloudFormation to create or update your pipeline\. 


+ [View Your Authorized OAuth Apps](GitHub-view-oauth-token.md)
+ [Use GitHub and the AWS CodePipeline CLI to Create and Rotate Your GitHub Personal Access Token on a Regular Basis](GitHub-rotate-personal-token-CLI.md)