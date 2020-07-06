# Add a cross\-Region action in CodePipeline<a name="actions-create-cross-region"></a>

AWS CodePipeline includes a number of actions that help you configure build, test, and deploy resources for your automated release process\. You can add actions to your pipeline that are in an AWS Region different from your pipeline\. When an AWS service is the provider for an action, and this action type/provider type are in a different AWS Region from your pipeline, this is a cross\-Region action\. 

**Note**  
Certain action types in CodePipeline may only be available in certain AWS Regions\. Also note that there may be AWS Regions where an action type is available, but a specific AWS provider for that action type is not available\.

You can use the console, AWS CLI, or AWS CloudFormation to add cross\-Region actions in pipelines\.

When you create or edit a pipeline, you must have an artifact bucket in the pipeline Region and then you must have one artifact bucket per Region where you plan to execute an action\. For more information about the `ArtifactStores` parameter, see [CodePipeline pipeline structure reference](reference-pipeline-structure.md)\.

**Note**  
CodePipeline handles the copying of artifacts from one AWS Region to the other Regions when performing cross\-region actions\.

If you use the console to create a pipeline or cross\-Region actions, default artifact buckets are configured by CodePipeline in the Regions where you have actions\. When you use the AWS CLI, AWS CloudFormation, or an SDK to create a pipeline or cross\-Region actions, you provide the artifact bucket for each Region where you have actions\. 

**Note**  
You must create the artifact bucket and encryption key in the same AWS Region as the cross\-Region action and in the same account as your pipeline\.

You cannot create cross\-Region actions for the following action types:
+ Source actions
+ Third\-party actions
+ Custom actions

When a pipeline includes a cross\-Region action as part of a stage, CodePipeline replicates only the input artifacts of the cross\-Region action from the pipeline Region to the action's Region\.

**Note**  
The pipeline Region and the Region where your CloudWatch Events change detection resources are maintained remain the same\. The Region where your pipeline is hosted does not change\.

## Manage cross\-Region actions in a pipeline \(console\)<a name="actions-cross-region-console"></a>

You can use the CodePipeline console to add a cross\-Region action to an existing pipeline\. To create a new pipeline with cross\-Region actions using the Create pipeline wizard, see [Create a pipeline \(console\)](pipelines-create.md#pipelines-create-console)\.

In the console, you create a cross\-Region action in a pipeline stage by choosing the action provider and the **Region** field, which lists the resources you have created in that region for that provider\. When you add a cross\-Region action, CodePipeline uses a separate artifact bucket in the action's region\. For more information about cross\-Region artifact buckets, see [CodePipeline pipeline structure reference](reference-pipeline-structure.md)\.

### Add a cross\-Region action to a pipeline stage \(console\)<a name="actions-cross-region-console-add"></a>

Use the console to add a cross\-Region action to a pipeline\.

**Note**  
If the pipeline is running when changes are saved, that execution does not complete\.

**To add a cross\-Region action**

1. Sign in to the console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. Select your pipeline, and then choose **Edit**\.

1. At the bottom of the diagram, choose **\+ Add stage** if you are adding a new stage, or choose **Edit stage** if you want to add the action to an existing stage\.

1. On **Edit: <Stage>**, choose **\+ Add action group** to add a serial action\. Or choose **\+ Add action** to add a parallel action\.

1. On the **Edit action** page:

   1. In **Action name**, enter a name for the cross\-Region action\.

   1. In **Action provider**, choose the action provider\.

   1. In **Region**, choose the AWS Region where you have created or plan to create the resource for the action\. When the Region is selected, the available resources for that Region are listed for selection\. The **Region** field designates where the AWS resources are created for this action type and provider type\. This field only displays for actions where the action provider is an AWS service\. The **Region** field defaults to the same AWS Region as your pipeline\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/xregion-edit-action.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   1. In **Input artifacts** choose the appropriate input from the previous stage\. For example, if the previous stage is a source stage, choose **SourceArtifact**\.

   1. Complete all the required fields for the action provider you are configuring\.

   1. In **Output artifacts** choose the appropriate output to the next stage\. For example, if the next stage is a deployment stage, choose **BuildArtifact**\.

   1. Choose **Save**\.

1. On **Edit: <Stage>**, choose **Done**\.

1. Choose **Save**\.

### Edit a cross\-Region action in a pipeline stage \(console\)<a name="actions-cross-region-console-edit"></a>

Use the console to edit an existing cross\-Region action in a pipeline\.

**Note**  
If the pipeline is running when changes are saved, that execution does not complete\.

**To edit a cross\-Region action**

1. Sign in to the console at [http://console.aws.amazon.com/codesuite/codepipeline/home.](http://console.aws.amazon.com/codesuite/codepipeline/home.)

1. Select your pipeline, and then choose **Edit**\.

1. Choose **Edit stage**\.

1. On **Edit: <Stage>**, choose the icon to edit an existing action\.

1. On the **Edit action** page, make changes to the fields, as appropriate\.

1. On **Edit: <Stage>**, choose **Done**\.

1. Choose **Save**\.

### Delete a cross\-Region action from a pipeline stage \(console\)<a name="actions-cross-region-console-delete"></a>

Use the console to delete an existing cross\-Region action from a pipeline\.

**Note**  
If the pipeline is running when changes are saved, that execution does not complete\.

**To delete a cross\-Region action**

1. Sign in to the console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. Select your pipeline, and then choose **Edit**\.

1. Choose **Edit stage**\.

1. On **Edit: <Stage>**, choose the icon to delete an existing action\.

1. On **Edit: <Stage>**, choose **Done**\.

1. Choose **Save**\.

## Add a cross\-Region action to a pipeline \(CLI\)<a name="actions-cross-region-cli"></a>

You can use the AWS CLI to add a cross\-Region action to an existing pipeline\.

To create a cross\-Region action in a pipeline stage with the AWS CLI, you add the configuration action along with an optional `region` field\. You must also have already created an artifact bucket in the action's region\. Instead of providing the `artifactStore` parameter of the single\-region pipeline, you use the `artifactStores` parameter to include a listing of each Region's artifact bucket\.

**Note**  
In this walkthrough and its examples, *RegionA* is the Region where the pipeline is created\. It has access to the *RegionA* Amazon S3 bucket used to store pipeline artifacts and the service role used by CodePipeline\. *RegionB* is the region where the CodeDeploy application, deployment group, and service role used by CodeDeploy are created\. 

### Prerequisites<a name="actions-create-cross-region-prereq"></a>

You must have created the following:
+ A pipeline in *RegionA*\. 
+ An Amazon S3 artifact bucket in *RegionB*\. 
+ The resources for your action, such as your CodeDeploy application and deployment group for a cross\-Region deploy action, in *RegionB*\.

### Add a cross\-Region action to a pipeline \(CLI\)<a name="actions-create-cross-region-cli"></a>

Use the AWS CLI to add a cross\-Region action to a pipeline\.

**To add a cross\-Region action**

1. For a pipeline in *RegionA*, run the get\-pipeline command to copy the pipeline structure into a JSON file\. For example, for a pipeline named `MyFirstPipeline`, run the following command: 

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   This command returns nothing, but the file you created should appear in the directory where you ran the command\.

1. Add the `region` field to add a new stage with your cross\-Region action that includes the Region and resources for your action\. The following JSON sample adds a Deploy stage with a cross\-Region deploy action where the provider is CodeDeploy, in a new region `us-east-1`\.

   ```
    {
                   "name": "Deploy",
                   "actions": [
                       {
                           "inputArtifacts": [
                               {
                                   "name": "SourceArtifact"
                               }
                           ],
                           "name": "Deploy",
                           "region": "RegionB",
                           "actionTypeId": {
                               "category": "Deploy",
                               "owner": "AWS",
                               "version": "1",
                               "provider": "CodeDeploy"
                           },
                           "outputArtifacts": [],
                           "configuration": {
                               "ApplicationName": "name",
                               "DeploymentGroupName": "name"
                           },
                           "runOrder": 1
                       }
   ```

1. In the pipeline structure, remove the `artifactStore` field and add the `artifactStores` map for your new cross\-Region action\. The mapping must include an entry for each AWS Region in which you have actions\. For each entry in the mapping, the resources must be in the respective AWS Region\. In the example below, `ID-A` is the encryption key ID for *RegionA*, and `ID-B` is the encryption key ID for *RegionB*\.

   ```
   "artifactStores":{  
      "RegionA":{  
         "encryptionKey":{  
            "id":"ID-A",
            "type":"KMS"
         },
         "location":"Location1",
         "type":"S3"
      },
      "RegionB":{  
         "encryptionKey":{  
            "id":"ID-B",
            "type":"KMS"
         },
         "location":"Location2",
         "type":"S3"
      }
   }
   ```

   The following JSON example shows the us\-west\-2 bucket as `my-storage-bucket` and adds the new us\-east\-1 bucket named `my-storage-bucket-us-east-1`\.

   ```
           "artifactStores": {
               "us-west-2": {
                   "type": "S3",
                   "location": "my-storage-bucket"
               },
               "us-east-1": {
                   "type": "S3",
                   "location": "my-storage-bucket-us-east-1"
               }
           },
   ```

1. If you are working with the pipeline structure retrieved using the get\-pipeline command, remove the `metadata` lines from the JSON file\. Otherwise, the update\-pipeline command cannot use it\. Remove the `"metadata": { }` lines and the `"created"`, `"pipelineARN"`, and `"updated"` fields\.

   For example, remove the following lines from the structure: 

   ```
   "metadata": {  
     "pipelineArn": "arn:aws:codepipeline:region:account-ID:pipeline-name",
     "created": "date",
     "updated": "date"
     }
   ```

   Save the file\.

1. To apply your changes, run the update\-pipeline command, specifying the pipeline JSON file:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   ```
   aws codepipeline update-pipeline --cli-input-json file://pipeline.json
   ```

   This command returns the entire structure of the edited pipeline\. The output is similar to the following\.

   ```
   {
       "pipeline": {
           "version": 4,
           "roleArn": "ARN",
           "stages": [
               {
                   "name": "Source",
                   "actions": [
                       {
                           "inputArtifacts": [],
                           "name": "Source",
                           "actionTypeId": {
                               "category": "Source",
                               "owner": "AWS",
                               "version": "1",
                               "provider": "CodeCommit"
                           },
                           "outputArtifacts": [
                               {
                                   "name": "SourceArtifact"
                               }
                           ],
                           "configuration": {
                               "PollForSourceChanges": "false",
                               "BranchName": "master",
                               "RepositoryName": "MyTestRepo"
                           },
                           "runOrder": 1
                       }
                   ]
               },
               {
                   "name": "Deploy",
                   "actions": [
                       {
                           "inputArtifacts": [
                               {
                                   "name": "SourceArtifact"
                               }
                           ],
                           "name": "Deploy",
                           "region": "us-east-1",
                           "actionTypeId": {
                               "category": "Deploy",
                               "owner": "AWS",
                               "version": "1",
                               "provider": "CodeDeploy"
                           },
                           "outputArtifacts": [],
                           "configuration": {
                               "ApplicationName": "name",
                               "DeploymentGroupName": "name"
                           },
                           "runOrder": 1
                       }
                   ]
               }
           ],
           "name": "AnyCompanyPipeline",
           "artifactStores": {
               "us-west-2": {
                   "type": "S3",
                   "location": "my-storage-bucket"
               },
               "us-east-1": {
                   "type": "S3",
                   "location": "my-storage-bucket-us-east-1"
               }
           }
       }
   }
   ```
**Note**  
The update\-pipeline command stops the pipeline\. If a revision is being run through the pipeline when you run the update\-pipeline command, that run is stopped\. You must manually start the pipeline to run that revision through the updated pipeline\. Use the `start-pipeline-execution` command to manually start your pipeline\.

1. After you update your pipeline, the cross\-Region actions is displayed in the console, as shown here\.  
![\[A high-level view of a pipeline that includes a cross-Region action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/cross-region-icon.png)![\[A high-level view of a pipeline that includes a cross-Region action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A high-level view of a pipeline that includes a cross-Region action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## Add a cross\-Region action to a pipeline \(AWS CloudFormation\)<a name="actions-create-cross-region-cfn"></a>

You can use AWS CloudFormation to add a cross\-Region action to an existing pipeline\.

**To add a cross\-Region action with AWS CloudFormation**

1. Add the `Region` parameter to the `ActionDeclaration` resource in your template, as shown in this example:

   ```
   ActionDeclaration:
                 Type: Object
                 Properties:
                   ActionTypeId:
                     Type: ActionTypeId
                     Required: true
                   Configuration:
                     Type: Map
                   InputArtifacts:
                     Type: Array
                     ItemType:
                       Type: InputArtifact
                   Name:
                     Type: String
                     Required: true
                   OutputArtifacts:
                     Type: Array
                     ItemType:
                       Type: OutputArtifact
                   RoleArn:
                     Type: String
                   RunOrder:
                     Type: Integer
                   Region:
                     Type: String
   ```

1. Under `Mappings`, add the region map as shown in this example for a mapping named `SecondRegionMap` that maps values for the keys `RegionA` and `RegionB`\. Under the `Pipeline` resource, under the `artifactStore` field, add the `artifactStores` map for your new cross\-Region action as follows:

   ```
   Mappings:
     SecondRegionMap:
       RegionA:
         SecondRegion: "RegionB"
       RegionB:
         SecondRegion: "RegionA"
   
   ...
   
             Properties:
               ArtifactStores:
                 -
                   Region: RegionB
                   ArtifactStore:
                     Type: "S3"
                     Location: test-cross-region-artifact-store-bucket-RegionB
                 -
                   Region: RegionA
                   ArtifactStore:
                     Type: "S3"
                     Location: test-cross-region-artifact-store-bucket-RegionA
   ```

   The following YAML example shows the *RegionA* bucket as `us-west-2` and adds the new *RegionB* bucket, `eu-central-1`:

   ```
   Mappings:
     SecondRegionMap:
       us-west-2:
         SecondRegion: "eu-central-1"
       eu-central-1:
         SecondRegion: "us-west-2"
   
   ...
   
             Properties:
               ArtifactStores:
                 -
                   Region: eu-central-1
                   ArtifactStore:
                     Type: "S3"
                     Location: test-cross-region-artifact-store-bucket-eu-central-1
                 -
                   Region: us-west-2
                   ArtifactStore:
                     Type: "S3"
                     Location: test-cross-region-artifact-store-bucket-us-west-2
   ```

1. Save the updated template to your local computer, and then open the AWS CloudFormation console\.

1. Choose your stack, and then choose **Create Change Set for Current Stack**\. 

1. Upload the template, and then view the changes listed in AWS CloudFormation\. These are the changes to be made to the stack\. You should see your new resources in the list\.

1. Choose **Execute**\.