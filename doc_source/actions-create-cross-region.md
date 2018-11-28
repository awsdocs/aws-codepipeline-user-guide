--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Add a Cross\-Region Action in AWS CodePipeline<a name="actions-create-cross-region"></a>

AWS CodePipeline includes a number of actions that help you configure build, test, and deploy resources for your automated release process\. You can add build, test, or deploy actions to your pipeline that are in an AWS Region different from your pipeline\. You can use the AWS CLI or AWS CloudFormation to add cross\-region actions in pipelines\.

You cannot create cross\-region actions for the following action types:
+ Source actions
+ Third\-party actions
+ Custom actions

To create a cross\-region action in a pipeline stage, you add the configuration action along with an optional `region` field\. You must also have already created an artifact bucket in the action's region\. Instead of providing the single\-region pipeline's `artifactStore` parameter, you include a listing of each region's artifact bucket with the `artifactStores` parameter\.

When you create a cross\-region action, AWS CodePipeline performs the following action:
+ When a pipeline includes a cross\-region action as part of a stage, AWS CodePipeline replicates the source artifacts from the pipeline region to the action's region\.

**Note**  
The pipeline region and the region where your CloudWatch Events change detection resources are maintained remain the same\. The region where your pipeline is hosted does not change\.  
In this walkthrough and its examples, *RegionA* is the region where the pipeline is created\. It has access to the *RegionA* Amazon S3 bucket used to store pipeline artifacts and the service role used by AWS CodePipeline\. *RegionB* is the region where the AWS CodeDeploy application, deployment group, and service role used by AWS CodeDeploy are created\. 

## Prerequisites<a name="actions-create-cross-region-prereq"></a>

You must have created the following:
+ A pipeline created in *RegionA*\. 
+ An Amazon S3 artifact bucket created in *RegionB*\. 
+ The resources for your action created in *RegionB*, such as your AWS CodeDeploy application and deployment group for a cross\-region deploy action\.

## Add a Cross\-Region Action to a Pipeline \(CLI\)<a name="actions-create-cross-region-cli"></a>

Use the AWS CLI to add a cross\-region action to a pipeline\.

**To add a cross\-region action**

1. For a pipeline in *RegionA*, run the get\-pipeline command to copy the pipeline structure into a JSON file\. For example, for a pipeline named `MyFirstPipeline`, run the following command: 

   ```
   aws codepipeline get-pipeline --name MyFirstPipeline >pipeline.json
   ```

   This command returns nothing, but the file you created should appear in the directory where you ran the command\.

1. Add the `region` field to add a new stage with your cross\-region action that includes the region and resources for your action\. The following JSON sample adds a Deploy stage with a cross\-region deploy action where the provider is AWS CodeDeploy, in a new region `us-east-1`\.

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

1. In the pipeline structure, remove the `artifactStore` field and add the `artifactStores` map for your new cross\-region action\. The mapping must include an artifact bucket for the current pipeline region and buckets for any additional regions as follows:

   ```
   "artifactStores":{  
      "RegionA":{  
         "encryptionKey":{  
            "id":"ID",
            "type":"KMS"
         },
         "location":"Location1",
         "type":"S3"
      },
      "RegionB":{  
         "encryptionKey":{  
            "id":"ID",
            "type":"KMS"
         },
         "location":"Location2",
         "type":"S3"
      }
   }
   ```

   The following JSON example shows the *RegionA* bucket as `us-west-2` and adds the new *RegionB* bucket, `us-east-1`:

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

1. After you update your pipeline, the cross\-region actions is displayed in the console, as shown here\.  
![\[A high-level view of a pipeline that includes a cross-region action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/cross-region-icon.png)![\[A high-level view of a pipeline that includes a cross-region action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[A high-level view of a pipeline that includes a cross-region action.\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## Add a Cross\-Region Action to a Pipeline \(AWS CloudFormation\)<a name="actions-create-cross-region-cfn"></a>

You can use AWS CloudFormation to add a cross\-region action to an existing pipeline\.

**To add a cross\-region action with AWS CloudFormation**

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

1. Under `Mappings`, add the region map\. Under the `Pipeline` resource, under the `artifactStore` field, add the `artifactStores` map for your new cross\-region action as follows:

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