# Tutorial: Using variables with Lambda invoke actions<a name="tutorials-lambda-variables"></a>

A Lambda invoke action can use variables from another action as part of its input and return new variables along with its output\. For information about variables for actions in CodePipeline, see [Variables](reference-variables.md)\.

At the end of this tutorial, you will have:
+ A Lambda invoke action that:
  + Consumes the `CommitId` variable from a CodeCommit source action
  + Outputs three new variables: `dateTime`, `testRunId`, and `region`
+ A manual approval action that consumes the new variables from your Lambda invoke action to provide a test URL and a test run ID
+ A pipeline updated with the new actions

**Topics**
+ [Prerequisites](#lambda-variables-prereqs)
+ [Step 1: Create a Lambda function](#lambda-variables-function)
+ [Step 2: Add a Lambda invoke action and manual approval action to your pipeline](#lambda-variables-pipeline)

## Prerequisites<a name="lambda-variables-prereqs"></a>

Before you begin, you must have the following: 
+ You can create or use the pipeline with the CodeCommit source in [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md)\.
+ Edit your existing pipeline so that the CodeCommit source action has a namespace\. Assign the namespace `SourceVariables` to the action\.

## Step 1: Create a Lambda function<a name="lambda-variables-function"></a>

Use the following steps to create a Lambda function and a Lambda execution role\. You add the Lambda action to your pipeline after you create the Lambda function\.

**To create a Lambda function and execution role**

1. Sign in to the AWS Management Console and open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create function**\. Leave **Author from scratch** selected\.

1. In **Function name**, enter the name of your function, such as **myInvokeFunction**\. In **Runtime**, leave the default option selected\.

1. Expand **Choose or create an execution role**\. Choose **Create a new role with basic Lambda permissions**\.

1. Choose **Create function**\.

1. To use a variable from another action, it will have to be passed to the `UserParameters` in the Lambda invoke action configuration\. You will be configuring the action in our pipeline later in the tutorial, but you will add the code assuming the variable will be passed\.

   ```
   const commitId =
   event["CodePipeline.job"].data.actionConfiguration.configuration.UserParameters;
   ```

   To produce new variables, set a property called `outputVariables` on the input to `putJobSuccessResult`\. Note that you cannot produce variables as part of a `putJobFailureResult`\.

   ```
   const successInput = {
     jobId: jobId,
     outputVariables: {
       testRunId: Math.floor(Math.random() * 1000).toString(),
       dateTime: Date(Date.now()).toString(),
       region: lambdaRegion
     }
   };
   ```

   In your new function, leave **Edit code inline** selected, and paste the following example code under `index.js`\.

   ```
   var AWS = require('aws-sdk');
   
   exports.handler = function(event, context) {
       var codepipeline = new AWS.CodePipeline();
       
       // Retrieve the Job ID from the Lambda action
       var jobId = event["CodePipeline.job"].id;
       
       // Retrieve the value of UserParameters from the Lambda action configuration in AWS CodePipeline,
       // in this case it is the Commit ID of the latest change of the pipeline.
       var commitId = event["CodePipeline.job"].data.actionConfiguration.configuration.UserParameters; 
       
       // The region from where the lambda function is being executed.
       var lambdaRegion = process.env.AWS_REGION;
       
       // Notify AWS CodePipeline of a successful job
       var putJobSuccess = function(message) {
           var successInput = {
               jobId: jobId,
               outputVariables: {
                   testRunId: Math.floor(Math.random() * 1000).toString(),
                   dateTime: Date(Date.now()).toString(),
                   region: lambdaRegion
               }
           };
           codepipeline.putJobSuccessResult(params, function(err, data) {
               if(err) {
                   context.fail(err);      
               } else {
                   context.succeed(message);      
               }
           });
       };
       
       // Notify AWS CodePipeline of a failed job
       var putJobFailure = function(message) {
           var failureInput = {
               jobId: jobId,
               failureDetails: {
                   message: JSON.stringify(message),
                   type: 'JobFailed',
                   externalExecutionId: context.invokeid
               }
           };
           codepipeline.putJobFailureResult(params, function(err, data) {
               context.fail(message);      
           });
       };
       
       var sendResult = function() {
           try {
               console.log("Testing commit - " + commitId);
               
               // Your tests here
               
               // Succeed the job
               putJobSuccess("Tests passed.");
           } catch (ex) {
               // If any of the assertions failed then fail the job
               putJobFailure(ex);    
           }
       };
       
       sendResult();
   };
   ```

1. Choose **Save**\.

1. Copy the Amazon Resource Name \(ARN\) at the top of the screen\.

1. As a last step, open the AWS Identity and Access Management \(IAM\) console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\. Modify the Lambda execution role to add the following policy: [AWSCodePipelineCustomActionAccess](https://console.aws.amazon.com/iam/home?region=us-west-2#/policies/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FAWSCodePipelineCustomActionAccess)\. For the steps to create a Lambda execution role or modify the role policy, see [Step 2: Create the Lambda function](actions-invoke-lambda-function.md#actions-invoke-lambda-function-create-function) \. 

## Step 2: Add a Lambda invoke action and manual approval action to your pipeline<a name="lambda-variables-pipeline"></a>

In this step, you add a Lambda invoke action to your pipeline\. You add the action as part of a stage named **Test**\. The action type is an invoke action\. You then add a manual approval action after the invoke action\.

**To add a Lambda action and a manual approval action to the pipeline**

1. Open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

   The names of all pipelines that are associated with your AWS account are displayed\. Choose the pipeline where you want to add the action\.

1. Add the Lambda test action to your pipeline\.

   1. To edit your pipeline, choose **Edit**\. Add a stage after the source action in the existing pipeline\. Enter a name for the stage, such as **Test**\.

   1. In the new stage, choose the icon to add an action\. In **Action name**, enter the name of the invoke action, such as **Test\_Commit**\.

   1. In **Action provider**, choose **AWS Lambda**\.

   1. In **Input artifacts**, choose the name of your source action's output artifact, such as `SourceArtifact`\.

   1. In **Function name**, choose the name of the Lambda function that you created\.

   1. In **User parameters**, enter the variable syntax for the CodeCommit commit ID\. This creates the output variable that resolves to the commit to be reviewed and approved each time the pipeline is run\.

      ```
      #{SourceVariables.CommitId}
      ```

   1. In **Variable namespace**, add the namespace name, such as **TestVariables**\.  
![\[Console screenshot showing the available options when adding a Lambda test action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/variables-lambda-example.png)![\[Console screenshot showing the available options when adding a Lambda test action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the available options when adding a Lambda test action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   1. Choose **Done**\.

1. Add the manual approval action to your pipeline\.

   1. With your pipeline still in editing mode, add a stage after the invoke action\. Enter a name for the stage, such as **Approval**\.

   1. In the new stage, choose the icon to add an action\. In **Action name**, enter the name of the approval action, such as **Change\_Approval**\.

   1. In **Action provider**, choose **Manual approval**\.

   1. In **URL for review**, construct the URL by adding the variable syntax for the `region` variable and the `CommitId` variable\. Make sure that you use the namespaces assigned to the actions that provide the output variables\. 

      For this example, the URL with the variable syntax for a CodeCommit action has the default namespace `SourceVariables`\. The Lambda region output variable has the `TestVariables` namespace\. The URL looks like the following\.

      ```
      https://#{TestVariables.region}.console.aws.amazon.com/codesuite/codecommit/repositories/MyDemoRepo/commit/#{SourceVariables.CommitId}
      ```

      In **Comments**, construct the approval message text by adding the variable syntax for the `testRunId` variable\. For this example, the URL with the variable syntax for the Lambda `testRunId` output variable has the `TestVariables` namespace\. Enter the following message\.

      ```
      Make sure to review the code before approving this action. Test Run ID: #{TestVariables.testRunId}
      ```  
![\[Console screenshot showing the boxes where you enter the URL and comments message.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/variables-lambda-example-approval.png)![\[Console screenshot showing the boxes where you enter the URL and comments message.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the boxes where you enter the URL and comments message.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Choose **Done** to close the edit screen for the action, and then choose **Done** to close the edit screen for the stage\. To save the pipeline, choose **Done**\. The completed pipeline now contains a structure with source, test, approval, and deploy stages\.  
![\[Console screenshot showing the source, test, approval, and deploy pipeline stages.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/variables-lambda-example-pipeline.png)![\[Console screenshot showing the source, test, approval, and deploy pipeline stages.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the source, test, approval, and deploy pipeline stages.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   Choose **Release change** to run the latest change through the pipeline structure\.

1. When the pipeline reaches the manual approval stage, choose **Review**\. The resolved variables appear as the URL for the commit ID\. Your approver can choose the URL to view the commit\.  
![\[Console screenshot showing the review dialog box with approve and reject buttons.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/variables-lambda-example-URL.png)![\[Console screenshot showing the review dialog box with approve and reject buttons.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot showing the review dialog box with approve and reject buttons.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. After the pipeline runs successfully, you can also view the variable values on the action execution history page\.  
![\[Console screenshot of the action configuration pane showing the resolved configuration.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/variables-lambda-example-details.png)![\[Console screenshot of the action configuration pane showing the resolved configuration.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Console screenshot of the action configuration pane showing the resolved configuration.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)