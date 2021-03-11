# Working with action types<a name="action-types"></a>

Action types are preconfigured actions that you as a provider create for customers by using one of the supported integration models in AWS CodePipeline\. 

You can request, view, and update action types\. If the action type is created for your account as the owner, you can use the AWS CLI to view or update your action type properties and structure\. If you are the provider or owner of the action type, your customers can choose the action and add it to their pipelines after it is available in CodePipeline\.

**Note**  
You create actions with `custom` in the `owner` field to run with a job worker\. You do not create them with an integration model\. For information about custom actions, see [Create and add a custom action in CodePipeline](actions-create-custom-action.md)\.

**Action type components**

The following components make up an action type\.
+ **Action type ID** – The *ID* consists of the category, owner, provider, and version\. The following example shows an action type ID with an owner of `ThirdParty`, a category of `Test`, a provider named `TestProvider`, and a version of `1`\.

  ```
              {
                  "Category": "Test",
                  "Owner": "ThirdParty",
                  "Provider": "TestProvider",
                  "Version": "1"
              },
  ```
+ **Executor configuration** – The integration model, or action engine, specified when the action is created\. When you specify the executor for an action type, you choose one of two types:
  + *Lambda:* The action type owner writes the integration as a Lambda function, which is invoked by CodePipeline whenever there is a job available for the action\.
  + *JobWorker:* The action type owner writes the integration as a job worker that polls for available jobs on customer pipelines\. The job worker then runs the job and submits the job result back to CodePipeline by using CodePipeline APIs\.
**Note**  
The job worker integration model is not the preferred integration model\.
+ **Input and output artifacts:** Limits for the artifacts that the action type owner designates for customers of the action\.
+ **Permissions:** The permissions strategy that designates customers who can access the third\-party action type\. The permissions strategies available depend on the chosen integration model for the action type\.
+ **URLs:** Deep links to resources that the customer can interact with, such as the action type owner's configuration page\.

**Topics**
+ [Request an action type](#action-types-request)
+ [Add an available action type to a pipeline \(console\)](#action-types-in-pipelines)
+ [View an action type](#action-types-view-cli)
+ [Update an action type](#action-types-update-cli)

## Request an action type<a name="action-types-request"></a>

When a new CodePipeline action type is requested by a third\-party provider, the the action type is created for the action type owner in CodePipeline, and the owner can manage and view the action type\.

An action type can be either a private or public action\. When your action type is created, it is private\. To request an action type be changed to a public action, contact the CodePipeline service team\.

Before you create your action definition file, executor resources, and action type request for the CodePipeline team, you must choose an integration model\.



### Step 1: Choose your integration model<a name="action-types-choose-model"></a>

Choose your integration model and then create the configuration for that model\. After you choose the  integration model, you must configure your integration resources\.
+ For the Lambda integration model, you create a Lambda function and add permissions\. Add permissions to your integrator Lambda function to provide the CodePipeline service with permissions to invoke it using the CodePipeline service principal: `codepipeline.amazonaws.com`\. The permissions can be added using CloudFormation or the command line\.
  + [Sample code for adding permission via CloudFormation](https://code.amazon.com/packages/DuckHawkJobInvokerServiceInfrastructure/blobs/b9f9fa508c0c901e87e0909d05ad61ea49757b9b/--/configuration/cloudFormation/lambdaBasedTestRoles.template.yml#L72-L77)
  + [Documentation for command line](https://docs.aws.amazon.com/cli/latest/reference/lambda/add-permission.html)
+ For the job worker integration model, you create an integration with a list of allowed accounts where the job worker polls for jobs with the CodePipeline APIs\.

### Step 2: Create an action type definition file<a name="action-type-definition-file"></a>

You define an action type in an action type definition file using JSON\. In the file, you include the action category, the integration model used to manage the action type, and configuration properties\. 

**Note**  
After you create a public action, you can't change the action type property under `properties` from `optional` to `required`\. You also can't change the `owner`\.

For more information about the action type definition file parameters, see [ActionTypeDeclaration](http://docs.aws.amazon.com/cli/latest/reference/codepipelineAPI_ActionTypeDeclaration.html) and [UpdateActionType](http://docs.aws.amazon.com/cli/latest/reference/codepipelineAPI_UpdateActionType.html) in the [CodePipeline API Reference](http://docs.aws.amazon.com/cli/latest/reference/codepipeline)\. 

There are eight sections in the action type definition file:
+ `description`: The description for the action type to be updated\.
+ `executor`: Information about the executor for an action type that was created with a supported integration model, either `Lambda` or `job worker`\. You can only provide either `jobWorkerExecutorConfiguration` or `lambdaExecutorConfiguration`, based on your executor type\.
  + `configuration`: Resources for the configuration of the action type, based on the chosen integration model\. For the Lambda integration model, use the Lambda function ARN\. For the job worker integration model, use the account or list of accounts from where the job worker runs\.
  + `jobTimeout`: The timeout in seconds for the job\. An action execution can consist of multiple jobs\. This is the timeout for a single job, and not for the entire action execution\.
**Note**  
For the Lambda integration model, the maximum timeout is 15 minutes\.
  + `policyStatementsTemplate`: The policy statement that specifies the permissions in the CodePipeline customer’s account that are needed to successfully run an action execution\.
  + `type`: The integration model used to create and update the action type, either `Lambda` or `JobWorker`\.
+ `id`: The category, owner, provider, and version ID for the action type: 
  + `category`: The kind of action can be taken in the stage: Source, Build, Deploy, Test, Invoke, or Approval\. 
  + `provider`: The provider of the action type being called, such as the provider company or product name\. The provider name is supplied when the action type is created\.
  + `owner`: The creator of the action type being called: `AWS` or `ThirdParty`\.
  + `version`: A string used to version the action type\. For the first version, set the version number to 1\.
+ `inputArtifactDetails`: The number of artifacts to expect from the previous stage in the pipeline\.
+ `outputArtifactDetails`: The number of artifacts to expect from the result from the action type stage\. 
+ `permissions`: Details identifying the accounts with permissions to use the action type\.
+ `properties`: The parameters required for your project tasks to complete\.
  + `description`: The description of the property that is displayed to users\.
  + `optional`: Whether the configuration property is optional\.
  + `noEcho`: Whether the field value entered by the customer is omitted from the log\. If `true`, then the value is redacted when returned with a GetPipeline API request\.
  + `key`: Whether the configuration property is a key\.
  + `queryable`: Whether the property is used with polling\. An action type can have up to one queryable property\. If it has one, that property must be both required and not secret\.
  + `name`: The property name that is displayed to users\.
+ `urls`: A list of the URLs CodePipeline displays to your users\.
  + `entityUrlTemplate`: URL to the external resources for the action type, such as a configuration page\.
  + `executionUrlTemplate`: URL to the details for the latest run of the action\.
  + `revisionUrlTemplate`: URL displayed in the CodePipeline console to the page where customers can update or change the configuration of the external action\.
  + `thirdPartyConfigurationUrl`: URL of a page where users can sign up for an external service and perform initial configuration of the action provided by that service\.

The following code shows an example action type definition file\.

```
{
   "actionType": { 
      "description": "string",
      "executor": { 
         "configuration": { 
            "jobWorkerExecutorConfiguration": { 
               "pollingAccounts": [ "string" ],
               "pollingServicePrincipals": [ "string" ]
            },
            "lambdaExecutorConfiguration": { 
               "lambdaFunctionArn": "string"
            }
         },
         "jobTimeout": number,
         "policyStatementsTemplate": "string",
         "type": "string"
      },
      "id": { 
         "category": "string",
         "owner": "string",
         "provider": "string",
         "version": "string"
      },
      "inputArtifactDetails": { 
         "maximumCount": number,
         "minimumCount": number
      },
      "outputArtifactDetails": { 
         "maximumCount": number,
         "minimumCount": number
      },
      "permissions": { 
         "allowedAccounts": [ "string" ]
      },
      "properties": [ 
         { 
            "description": "string",
            "key": boolean,
            "name": "string",
            "noEcho": boolean,
            "optional": boolean,
            "queryable": boolean
         }
      ],
      "urls": { 
         "configurationUrl": "string",
         "entityUrlTemplate": "string",
         "executionUrlTemplate": "string",
         "revisionUrlTemplate": "string"
      }
   }
}
```



### Step 3: Register Your Integration with CodePipeline<a name="action-types-register"></a>

To register your action type with CodePipeline, you contact the CodePipeline service team with your request\.

The CodePipeline service team registers the new action type integration by making changes in the service codebase\. CodePipeline registers two new actions: a *public action* and a *private action*\. You use the private action for testing, and then when ready, you activate the public action to serve customer traffic\.

**To register a request for a Lambda integration**
+ Send a request to the CodePipeline service team using the following form\.

  ```
  This issue will track the onboarding of [Name] in CodePipeline.
  
  
  [Contact engineer] will be the primary point of contact for this integration.
  
  Name of the action type as you want it to appear to customers: Example.com Testing
  
  Initial onboard checklist:
  
  1. Attach an action type definition file in JSON format. This includes the schema for the action type
  
  2. A list of test accounts for the allowlist which can access the new action type [{account, account_name}]
  
  3. The Lambda function ARN
  
  4. List of AWS Regions where your action will be available
  
  5. Will this be available as a public action?
  ```

**To register a request for a job worker integration**
+ Send a request to the CodePipeline service team using the following form\.

  ```
  This issue will track the onboarding of [Name] in CodePipeline.
  
  [Contact engineer] will be the primary point of contact for this integration.
  
  
  Name of the action type as you want it to appear to customers: Example.com Testing
  
  Initial onboard checklist:
  
  1. Attach an action type definition file in JSON format. This includes the schema for the action type.
  
  2. A list of test accounts for the allowlist which can access the new action type [{account, account_name}]
  
  3. URL information:
  Website URL: https://www.example.com/%TestThirdPartyName%/%TestVersionNumber%
  
  Example URL pattern where customers will be able to review their configuration information for the action: https://www.example.com/%TestThirdPartyName%/%customer-ID%/%CustomerActionConfiguration%
  
  Example runtime URL pattern: https://www.example.com/%TestThirdPartyName%/%customer-ID%/%TestRunId%
  
  4. List of AWS Regions where your action will be available
  
  5. Will this be available as a public action?
  ```

### Step 4: Activate Your New Integration<a name="action-types-activate"></a>

Contact the CodePipeline service team when you are ready to use the new integration publicly\.

## Add an available action type to a pipeline \(console\)<a name="action-types-in-pipelines"></a>

You add your action type to a pipeline so that you can test it\. You can do this by creating a new pipeline or editing an existing one\. 

**Note**  
If your action type is a source, build, or deploy category action, you can add it by creating a pipeline\. If your action type is in the test category, you must add it by editing an existing pipeline\.

**To add your action type to an existing pipeline from the CodePipeline console**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. In the list of pipelines, choose the pipeline where you want to add the action type\.

1. On the summary view page of the pipeline, choose **Edit**\.

1. Choose to edit the stage\. In the stage where you want to add your action type, choose **Add action group**\. The **Edit action ** page displays\.

1. On the **Edit action** page, in **Action name**, enter a name for the action\. This is the name that displays for the stage in your pipeline\.

1. In **Action provider**, choose your action type from the list\. 

   Note that the value in the list is based on the `provider` specified in the action type definition file\.

1. In **Input artifacts**, enter the artifact name in this format:

   `Artifactname::FileName`

   Note that the minimum and maximum quantities allowed are defined based on the `inputArtifactDetails` specified in the action type definition file\.

1. Choose **Connect to <Action\_Name>**\.

   A browser window opens and connects to the website you have created for your action type\.

1. Log in to your website as a customer and complete the steps a customer takes to use your action type\. Your steps will vary depending on your action category, website, and configuration, but usually includes a completion action that returns the customer to the **Edit action** page\.

1. In the CodePipeline **Edit action** page, the additional configuration fields for the action display\. The fields that display are the configuration properties that you specified in the action definition file\. Enter theinformation in the fields that are customized for your action type\.

   For example, if the action definition file specified a property named `Host`, then a field with the label **Host** is shown on the **Edit action** page for your action\.

1. In **Output artifacts**, enter the artifact name in this format:

   `Artifactname::FileName`

   Note that the minimum and maximum quantities allowed are defined based on the `outputArtifactDetails` specified in the action type definition file\.

1. Choose **Done** to return to the pipeline details page\.
**Note**  
Your customers can optionally use the CLI to add the action type to their pipeline\. 

1. To test your action, commit a change to the source specified in the source stage of the pipeline or follow the steps in [Manually Start a Pipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/how-to-manually-start.html)\.

To create a pipeline with your action type, follow the steps in [Create a Pipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/how-to-create-pipelines) and choose your action type from as many stages as you will test\.

## View an action type<a name="action-types-view-cli"></a>

You can use the CLI to view your action type\. Use the get\-action\-type command to view action types that have been created using an integration model\.

**To view an action type**

1. Create an input JSON file and name the file `file.json`\. Add your action type ID in JSON format as shown in the following example\.

   ```
   {
       "category": "Test",
       "owner": "ThirdParty",
       "provider": "TestProvider",
       "version": "1"
   }
   ```

1. In a terminal window or at the command line, run the get\-action\-type command\.

   ```
   aws codepipeline get-action-type --cli-input-json file://file.json
   ```

   This command returns the action definition output for an action type\. This example shows an action type that was created with the Lambda integration model\.

   ```
   {
       "actionType": {
           "executor": {
               "configuration": {
                   "lambdaExecutorConfiguration": {
                       "lambdaFunctionArn": "arn:aws:lambda:us-west-2:<account-id>:function:my-function"
                   }
               },
               "type": "Lambda"
           },
           "id": {
               "category": "Test",
               "owner": "ThirdParty",
               "provider": "TestProvider",
               "version": "1"
           },
           "inputArtifactDetails": {
               "minimumCount": 0,
               "maximumCount": 1
           },
           "outputArtifactDetails": {
               "minimumCount": 0,
               "maximumCount": 1
           },
           "permissions": {
               "allowedAccounts": [
                   "<account-id>"
               ]
           },
           "properties": []
       }
   }
   ```

## Update an action type<a name="action-types-update-cli"></a>

You can use the CLI to edit action types that are created with an integration model\.

For a public action type, you can't update the owner, you can't change optional properties to required, and you can only add new optional properties\.

1. Use the `get-action-type` command to get the structure for your action type\. Copy the structure\.

1. Create an input JSON file and name it `action.json`\. Paste the action type structure you copied in the previous step into it\. Update any parameters you want to change\. You can also add optional parameters\. 

   For more information about the parameters for the input file, see the action definition file description in [Step 2: Create an action type definition file](#action-type-definition-file)\.

   The following example shows how to update an example action type created with the Lambda integration model\. This example makes the following changes:
   + Changes the `provider` name to `TestProvider1`\.
   + Add a job timeout limit of 900 seconds\.
   + Adds an action configuration property named `Host` that is displayed to the customer using the action\.

     ```
     {
         "actionType": {
             "executor": {
                 "configuration": {
                     "lambdaExecutorConfiguration": {
                         "lambdaFunctionArn": "arn:aws:lambda:us-west-2:<account-id>:function:my-function"
                     }
                 },
                 "type": "Lambda",
                 "jobTimeout": 900 
             },
             "id": {
                 "category": "Test",
                 "owner": "ThirdParty",
                 "provider": "TestProvider1",
                 "version": "1"
             },
             "inputArtifactDetails": {
                 "minimumCount": 0,
                 "maximumCount": 1
             },
             "outputArtifactDetails": {
                 "minimumCount": 0,
                 "maximumCount": 1
             },
             "permissions": {
                 "allowedAccounts": [
                     "account-id"
                 ]
             },
             "properties": {
              "description": "Owned build action parameter description",
              "optional": true,
              "noEcho": false,
              "key": true,
              "queryable": false,
              "name": "Host"
              }
         }
     }
     ```

1. At the terminal or command line, run the update\-action\-type command

   ```
   aws codepipeline update-action-type --cli-input-json file://action.json
   ```

   This command returns the action type output to match your updated parameters\.