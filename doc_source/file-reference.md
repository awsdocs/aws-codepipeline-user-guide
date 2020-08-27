# Image definitions file reference<a name="file-reference"></a>

This section is a reference only\. For information about creating a pipeline with source or deploy actions for containers, see [Create a pipeline in CodePipeline](pipelines-create.md)\.

AWS CodePipeline job workers for container actions, such as an Amazon ECR source action or Amazon ECS deploy actions, use definitions files to map the image URI and container name to the task definition\. Each definitions file is a JSON\-formatted file used by the action provider as follows:
+ Amazon ECS standard deployments require an `imagedefinitions.json` file as an input to the deploy action\.
+ Amazon ECS blue/green deployments require an `imageDetail.json` file as an input to the deploy action\.
  + Amazon ECR source actions generate an `imageDetail.json` file that is provided as an output from the source action\.

**Topics**
+ [imagedefinitions\.json file for Amazon ECS standard deployment actions](#pipelines-create-image-definitions)
+ [imageDetail\.json file for Amazon ECS blue/green deployment actions](#file-reference-ecs-bluegreen)

## imagedefinitions\.json file for Amazon ECS standard deployment actions<a name="pipelines-create-image-definitions"></a>

An image definitions document is a JSON file that describes your Amazon ECS container name and the image and tag\. If you are deploying container\-based applications, you must generate an image definitions file to provide the CodePipeline job worker with the Amazon ECS container and image identification to retrieve from the repository, such as Docker Hub\.

**Note**  
The default file name for the file is `imagedefinitions.json`\. If you choose to use a different file name, you must provide it when you create the pipeline deployment stage\.

Create the `imagedefinitions.json` file with the following considerations:
+ The file must use UTF\-8 encoding\.
+ The maximum file size limit for the image definitions file is 100 KB\.
+ You must create the file as a source or build artifact so that it is an input artifact for the deploy action\. In other words, make sure that the file is either uploaded to your source location, such as your CodeCommit repository, or generated as a built output artifact\.

The `imagedefinitions.json` file provides the container name and image URI\. It must be constructed with the following set of key\-value pairs\.


| Key | Value | 
| --- | --- | 
| name | container\_name | 
| imageUri | imageUri | 

Here is the JSON structure, where the container name is `sample-app`, the image URI is `ecs-repo`, and the tag is `latest`:

```
[
  {
    "name": "sample-app",
    "imageUri": "11111EXAMPLE.dkr.ecr.us-west-2.amazonaws.com/ecs-repo:latest"
  }
]
```

You can also construct the file to list multiple container\-image pairs\. 

JSON structure:

```
[
  {
    "name": "simple-app",
    "imageUri": "httpd:2.4"
  },
  {
    "name": "simple-app-1",
    "imageUri": "mysql"
  },
  {
    "name": "simple-app-2",
    "imageUri": "java1.8"
  }
]
```

Before you create your pipeline, use the following steps to set up the `imagedefinitions.json` file\.

1. As part of planning the container\-based application deployment for your pipeline, plan the source stage and the build stage, if applicable\.

1. Choose one of the following:

   1.  If your pipeline has skipped the build stage, you must manually create the JSON file and upload it to your source repository so the source action can provide the artifact\. Create the file using a text editor, and name the file or use the default `imagedefinitions.json` file name\. Push the image definitions file to your source repository\.
**Note**  
If your source repository is an Amazon S3 bucket, remember to zip the JSON file\.

   1. If your pipeline has a build stage, add a command to your build spec file that outputs the image definitions file in your source repository during the build phase\. The following example uses the printf command to create an `imagedefinitions.json` file\. List this command in the `post_build` section of the `buildspec.yml` file:

      ```
      printf '[{"name":"container_name","imageUri":"image_URI"}]' >
      imagedefinitions.json
      ```

      You must include the image definitions file as an output artifact in the `buildspec.yml` file\.

1. When you create your pipeline in the console, on the **Deploy** page of the **Create Pipeline** wizard, in **Image Filename**, enter the image definitions file name\.

For a step\-by\-step tutorial for creating a pipeline that uses Amazon ECS as the deployment provider, see [Tutorial: Continuous Deployment with CodePipeline](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cd-pipeline.html)\.

## imageDetail\.json file for Amazon ECS blue/green deployment actions<a name="file-reference-ecs-bluegreen"></a>

An `imageDetail.json` document is a JSON file that describes your Amazon ECS image URI\. If you are deploying container\-based applications for a blue/green deployment, you must generate the `imageDetail.json` file to provide the Amazon ECS and CodeDeploy job worker with the image identification to retrieve from the repository, such as Docker Hub\.

**Note**  
The name of the file must be `imageDetail.json`\.

You must create the `imageDetail.json` file as a source or build artifact so that it is an input artifact for the deploy action\. You can use one of these methods to provide the `imageDetail.json` file in the pipeline: 
+ Include the `imageDetail.json` file in your source location so that it is provided in the pipeline as input to your Amazon ECS blue/green deployment action\.
**Note**  
If your source repository is an Amazon S3 bucket, remember to zip the JSON file\.
+ Amazon ECR source actions automatically generate an `imageDetail.json` file as an input artifact to the next action\.
**Note**  
Because the Amazon ECR source action creates this file, pipelines with an Amazon ECR source action do not need to manually provide an `imageDetail.json` file\.   
For a tutorial about creating a pipeline that includes an Amazon ECR source stage, see [Tutorial: Create a pipeline with an Amazon ECR source and ECS\-to\-CodeDeploy deployment](tutorials-ecs-ecr-codedeploy.md)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/imageDetail_file_diagram.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

The `imageDetail.json` file provides the image URI\. It must be constructed with the following key\-value pair\.


| Key | Value | 
| --- | --- | 
| ImageURI | image\_URI | 

------
#### [ imageDetail\.json ]

Here is the JSON structure, where the image URI is `ACCOUNTID.dkr.ecr.us-west-2.amazonaws.com/dk-image-repo@sha256:example3`:

```
{
"ImageURI": "ACCOUNTID.dkr.ecr.us-west-2.amazonaws.com/dk-image-repo@sha256:example3"
}
```

------
#### [ imageDetail\.json \(generated by ECR\) ]

An `imageDetail.json` file is generated automatically by the Amazon ECR source action each time a change is pushed to the image repository\. The `imageDetail.json` generated by Amazon ECR source actions is provided as an output artifact from the source action to the next action in the pipeline\.

Here is the JSON structure, where the repository name is `dk-image-repo`, the image URI is `ecs-repo`, and the image tag is `latest`:

```
{
    "ImageSizeInBytes": "44728918",
    "ImageDigest": "sha256:EXAMPLE11223344556677889900bfea42ea2d3b8a1ee8329ba7e68694950afd3",
    "Version": "1.0",
    "ImagePushedAt": "Mon Jan 21 20:04:00 UTC 2019",
    "RegistryId": "EXAMPLE12233",
    "RepositoryName": "dk-image-repo",
    "ImageURI": "ACCOUNTID.dkr.ecr.us-west-2.amazonaws.com/dk-image-repo@sha256:example3",
    "ImageTags": [
        "latest"
    ]
}
```

The `imageDetail.json` file maps the image URI and container name to the Amazon ECS task definition as follows:
+ `ImageSizeInBytes`: The size, in bytes, of the image in the repository\.
+ `ImageDigest`: The `sha256` digest of the image manifest\.
+ `Version`: The image version\.
+ `ImagePushedAt`: The date and time when the latest image was pushed to the repository\.
+ `RegistryId`: The AWS account ID associated with the registry that contains the repository\.
+ `RepositoryName`: The name of the Amazon ECR repository where the image was pushed\.
+ `ImageURI`: The URI for the image\.
+ `ImageTags`: The tag used for the image\.

------

Before you create your pipeline, use the following steps to set up the `imageDetail.json` file\.

1. As part of planning the container\-based application blue/green deployment for your pipeline, plan the source stage and the build stage, if applicable\.

1. Choose one of the following:

   1.  If your pipeline has skipped the build stage, you must manually create the JSON file and upload it to your source repository, such as CodeCommit, so the source action can provide the artifact\. Create the file using a text editor, and name the file or use the default `imageDetail.json` file name\. Push the `imageDetail.json` file to your source repository\.

   1. If your pipeline has a build stage, perform the following:

      1. Add a command to your build spec file that outputs the image definitions file in your source repository during the build phase\. The following example uses the printf command to create an `imageDetail.json` file\. List this command in the `post_build` section of the buildspec\.yml file:

         ```
         printf '{"ImageURI":"image_URI"}' > imageDetail.json
         ```

         You must include the `imageDetail.json` file as an output artifact in the `buildspec.yml` file\.

      1. Add the `imageDetail.json` as an artifact file in the `buildspec.yml` file\.

         ```
         artifacts:
           files:
             - imageDetail.json
         ```