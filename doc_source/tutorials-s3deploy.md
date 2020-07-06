# Tutorial: Create a pipeline that uses Amazon S3 as a deployment provider<a name="tutorials-s3deploy"></a>

In this tutorial, you configure a pipeline that continuously delivers files using Amazon S3 as the deployment action provider in your deployment stage\. The completed pipeline detects changes when you make a change to the source files in your source repository\. The pipeline then uses Amazon S3 to deploy the files to your bucket\. Each time you modify, add, or delete your website files in your source location, the deployment creates the website with your latest files\. This tutorial provides two options:
+ Create a pipeline that deploys a static website to your S3 public bucket\. This example creates a pipeline with an AWS CodeCommit source action and an Amazon S3 deployment action\. See [Option 1: Deploy static website files to Amazon S3](#tutorials-s3deploy-acc)\.
+ Create a pipeline that compiles sample TypeScript code into JavaScript and deploys the CodeBuild output artifact to your S3 bucket for archive\. This example creates a pipeline with an Amazon S3 source action, a CodeBuild build action, and an Amazon S3 deployment action\. See [Option 2: Deploy built archive files to Amazon S3 from an S3 source bucket](#tutorials-s3deploy-s3source)\.

**Important**  
Many of the actions you add to your pipeline in this procedure involve AWS resources that you need to create before you create the pipeline\. AWS resources for your source actions must always be created in the same AWS Region where you create your pipeline\. For example, if you create your pipeline in the US East \(Ohio\) Region, your CodeCommit repository must be in the US East \(Ohio\) Region\.   
You can add cross\-region actions when you create your pipeline\. AWS resources for cross\-region actions must be in the same AWS Region where you plan to execute the action\. For more information, see [Add a cross\-Region action in CodePipeline](actions-create-cross-region.md)\.

## Option 1: Deploy static website files to Amazon S3<a name="tutorials-s3deploy-acc"></a>

In this example, you download the sample static website template file, upload the files to your AWS CodeCommit repository, create your bucket, and configure it for hosting\. Next, you use the AWS CodePipeline console to create your pipeline and specify an Amazon S3 deployment configuration\.

### Prerequisites<a name="tutorials-s3deploy-acc-prereq"></a>

You must already have the following:
+ A CodeCommit repository\. You can use the AWS CodeCommit repository you created in [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md)\.
+ Source files for your static website\. Use this link to download a [sample static website](samples/sample-website.zip)\. The sample\-website\.zip download produces the following files: 
  + An `index.html` file
  + A `main.css` file
  + A `graphic.jpg` file
+ An S3 bucket configured for website hosting\. See [Hosting a static website on Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html)\. Make sure you create your bucket in the same Region as the pipeline\.
**Note**  
To host a website, your bucket must have public read access, which gives everyone read access\. With the exception of website hosting, you should keep the default access settings that block public access to S3 buckets\.

### Step 1: Push source files to your CodeCommit repository<a name="tutorials-s3deploy-acc-push"></a>

In this section, push your source files to the repository that the pipeline uses for your source stage\.

**To push files to your CodeCommit repository**

1. Extract the downloaded sample files\. Do not upload the ZIP file to your repository\.

1. Push or upload the files to your CodeCommit repository\. These files are the source artifact created by the **Create Pipeline** wizard for your deployment action in CodePipeline\. Your files should look like this in your local directory:

   ```
   index.html
   main.css
   graphic.jpg
   ```

1. You can use Git or the CodeCommit console to upload your files:

   1. To use the Git command line from a cloned repository on your local computer:

      1. Run the following command to stage all of your files at once:

         ```
         git add -A
         ```

      1. Run the following command to commit the files with a commit message:

         ```
         git commit -m "Added static website files"
         ```

      1. Run the following command to push the files from your local repo to your CodeCommit repository:

         ```
         git push
         ```

   1. To use the CodeCommit console to upload your files: 

      1. Open the CodeCommit console, and choose your repository from the **Repositories** list\.

      1. Choose **Add file**, and then choose **Upload file**\. 

      1. Select **Choose file**, and then browse for your file\. Commit the change by entering your user name and email address\. Choose **Commit changes**\.

      1. Repeat this step for each file you want to upload\.

### Step 2: Create your pipeline<a name="tutorials-s3deploy-acc-pipeline"></a>

In this section, you create a pipeline with the following actions:
+ A source stage with a CodeCommit action where the source artifacts are the files for your website\.
+ A deployment stage with an Amazon S3 deployment action\.

**To create a pipeline with the wizard**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Welcome** page, **Getting started** page, or **Pipelines** page, choose **Create pipeline**\.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyS3DeployPipeline**\.

1. In **Service role**, choose **New service role** to allow CodePipeline to create a service role in IAM\.

1. Leave the settings under **Advanced settings** at their defaults, and then choose **Next**\.

1. In **Step 2: Add source stage**, in **Source provider**, choose **AWS CodeCommit**\. In **Repository name**, choose the name of the CodeCommit repository you created in [Step 1: Create a CodeCommit repository](tutorials-simple-codecommit.md#codecommit-create-repository)\. In **Branch name**, choose the name of the branch that contains your latest code update\. Unless you created a different branch on your own, only `master` is available\. 

   After you select the repository name and branch, the Amazon CloudWatch Events rule to be created for this pipeline is displayed\. 

   Choose **Next**\.

1. In **Step 3: Add build stage**, choose **Skip build stage**, and then accept the warning message by choosing **Skip** again\. 

   Choose **Next**\.

1. In **Step 4: Add deploy stage**:

   1. In **Deploy provider**, choose **Amazon S3**\. 

   1. In **Bucket**, enter the name of your public bucket\.

   1. Select **Extract file before deploy**\.
**Note**  
The deployment fails if you do not select **Extract file before deploy**\. This is because the AWS CodeCommit action in your pipeline zips source artifacts and your file is a ZIP file\.

      When **Extract file before deploy** is selected, **Deployment path** is displayed\. Enter the name of the path you want to use\. This creates a folder structure in Amazon S3 to which the files are extracted\. For this tutorial, leave this field blank\.  
![\[The Step 4: Deploy page for an S3 deploy action with an AWS CodeCommit source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/tutorial-s3deploy-stage-codecommit.png)![\[The Step 4: Deploy page for an S3 deploy action with an AWS CodeCommit source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 4: Deploy page for an S3 deploy action with an AWS CodeCommit source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   1. \(Optional\) In **Canned ACL**, you can apply a set of predefined grants, known as a [canned ACL](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl), to the uploaded artifacts\. 

   1. \(Optional\) In **Cache control**, enter the caching parameters\. You can set this to control caching behavior for requests/responses\. For valid values, see the [http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) header field for HTTP operations\.

   1. Choose **Next**\.

1. In **Step 5: Review**, review the information, and then choose **Create pipeline**\.  
![\[The completed pipeline for an Amazon S3 deploy action with an AWS CodeCommit source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/tutorial-s3deploy-pipeline-codecommit.png)![\[The completed pipeline for an Amazon S3 deploy action with an AWS CodeCommit source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The completed pipeline for an Amazon S3 deploy action with an AWS CodeCommit source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. After your pipeline runs successfully, open the Amazon S3 console and verify that your files appear in your public bucket as shown:

   ```
   index.html
   main.css
   graphic.jpg
   ```

1. Access your endpoint to test the website\. Your endpoint follows this format: `http://bucket-name.s3-website-region.amazonaws.com/`\.

   Example endpoint: `http://my-bucket.s3-website-us-west-2.amazonaws.com/`\. 

   The sample appears as shown here\.  
![\[Sample website\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/tutorial-s3deploy-pipeline-website.png)![\[Sample website\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Sample website\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

### Step 3: Make a change to any source file and verify deployment<a name="tutorials-s3deploy-acc-update"></a>

Make a change to your source files and then push the change to your repository\. This triggers your pipeline to run\. Verify that your website is updated\.

## Option 2: Deploy built archive files to Amazon S3 from an S3 source bucket<a name="tutorials-s3deploy-s3source"></a>

In this option, the build commands in your build stage compile TypeScript code into JavaScript code and deploy the output to your S3 target bucket under a separate timestamped folder\. First, you create TypeScript code and a buildspec\.yml file\. After you combine the source files in a ZIP file, you upload the source ZIP file to your S3 source bucket, and use a CodeBuild stage to deploy a built application ZIP file to your S3 target bucket\. The compiled code is retained as an archive in your target bucket\.

### Prerequisites<a name="tutorials-s3deploy-s3source-prereq"></a>

You must already have the following:
+ An S3 source bucket\. You can use the bucket you created in [Tutorial: Create a simple pipeline \(S3 bucket\)](tutorials-simple-s3.md)\.
+ An S3 target bucket\. See [Hosting a static website on Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html)\. Make sure you create your bucket in the same AWS Region as the pipeline you want to create\.
**Note**  
This example demonstrates deploying files to a private bucket\. Do not enable your target bucket for website hosting or attach any policies that make the bucket public\.

### Step 1: Create and upload source files to your S3 source bucket<a name="tutorials-s3deploy-s3source-upload"></a>

In this section, you create and upload your source files to the bucket that the pipeline uses for your source stage\. This section provides instructions for creating the following source files:
+ A `buildspec.yml` file, which is used for CodeBuild build projects\.
+ An `index.ts` file\. 

**To create a buildspec\.yml file**
+ Create a file named `buildspec.yml` with the following contents\. These build commands install TypeScript and use the TypeScript compiler to rewrite the code in `index.ts` to JavaScript code\.

  ```
  version: 0.2
  
  phases:
    install:
      commands:
        - npm install -g typescript
    build:
      commands:
        - tsc index.ts
  artifacts:
    files:
      - index.js
  ```

**To create an index\.ts file**
+ Create a file named `index.ts` with the following contents\.

  ```
  interface Greeting {
      message: string;
  }
  
  class HelloGreeting implements Greeting {
      message = "Hello!";
  }
  
  function greet(greeting: Greeting) {
      console.log(greeting.message);
  }
  
  let greeting = new HelloGreeting();
  
  greet(greeting);
  ```

**To upload files to your S3 source bucket**

1. Your files should look like this in your local directory:

   ```
   buildspec.yml
   index.ts
   ```

   Zip the files and name the file `source.zip`\.

1. In the Amazon S3 console, for your source bucket, choose **Upload**\. Choose **Add files**, and then browse for the ZIP file you created\.

1.  Choose **Upload**\. These files are the source artifact created by the **Create Pipeline** wizard for your deployment action in CodePipeline\. Your file should look like this in your bucket:

   ```
   source.zip
   ```

### Step 2: Create your pipeline<a name="tutorials-s3deploy-s3source-pipeline"></a>

In this section, you create a pipeline with the following actions:
+ A source stage with an Amazon S3 action where the source artifacts are the files for your downloadable application\.
+ A deployment stage with an Amazon S3 deployment action\.

**To create a pipeline with the wizard**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Welcome** page, **Getting started** page, or **Pipelines** page, choose **Create pipeline**\.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyS3DeployPipeline**\.

1. In **Service role**, choose **New service role** to allow CodePipeline to create a service role in IAM\.

1. Leave the settings under **Advanced settings** at their defaults, and then choose **Next**\.

1. In **Step 2: Add source stage**, in **Source provider**, choose **Amazon S3**\. In **Bucket**, choose the name of your source bucket\. In **S3 object key**, enter the name of your source ZIP file\. Make sure you include the \.zip file extension\.

   Choose **Next**\.

1. In **Step 3: Add build stage**:

   1. In **Build provider**, choose **AWS CodeBuild**\.

   1. Choose **Create build project**\. On the **Create project** page:

   1. In **Project name**, enter a name for this build project\.

   1. In **Environment**, choose **Managed image**\. For **Operating system**, choose **Ubuntu**\.

   1. For **Runtime**, choose **Standard**\. For **Runtime version**, choose **aws/codebuild/standard:1\.0**\.

   1. In **Image version**, choose **Always use the latest image for this runtime version**\.

   1. For **Service role**, choose your CodeBuild service role, or create one\.

   1. For **Build specifications**, choose **Use a buildspec file**\.

   1. Choose **Continue to CodePipeline**\. A message is displayed if the project was created successfully\.

   1. Choose **Next**\.

1. In **Step 4: Add deploy stage**:

   1. In **Deploy provider**, choose **Amazon S3**\. 

   1. In **Bucket**, enter the name of your S3 target bucket\.

   1. Make sure that **Extract file before deploy** is cleared\.

      When **Extract file before deploy** is cleared, **S3 object key** is displayed\. Enter the name of the path you want to use: `js-application/{datetime}.zip`\.

      This creates a `js-application` folder in Amazon S3 to which the files are extracted\. In this folder, the `{datetime}` variable creates a timestamp on each output file when your pipeline runs\.  
![\[The Step 4: Deploy page for an Amazon S3 deploy action with an Amazon S3 source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/tutorial-s3deploy-stage-s3source.png)![\[The Step 4: Deploy page for an Amazon S3 deploy action with an Amazon S3 source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 4: Deploy page for an Amazon S3 deploy action with an Amazon S3 source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

   1. \(Optional\) In **Canned ACL**, you can apply a set of predefined grants, known as a [canned ACL](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl), to the uploaded artifacts\. 

   1. \(Optional\) In **Cache control**, enter the caching parameters\. You can set this to control caching behavior for requests/responses\. For valid values, see the [http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) header field for HTTP operations\.

   1. Choose **Next**\.

1. In **Step 5: Review**, review the information, and then choose **Create pipeline**\.  
![\[The completed pipeline for an Amazon S3 deploy action with an Amazon S3 source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/tutorial-s3deploy-pipeline-s3source.png)![\[The completed pipeline for an Amazon S3 deploy action with an Amazon S3 source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The completed pipeline for an Amazon S3 deploy action with an Amazon S3 source\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. After your pipeline runs successfully, view your bucket in the Amazon S3 console\. Verify that your deployed ZIP file is displayed in your target bucket under the `js-application` folder\. The JavaScript file contained in the ZIP file should be `index.js`\. The `index.js` file contains the following output:

   ```
   var HelloGreeting = /** @class */ (function () {
       function HelloGreeting() {
           this.message = "Hello!";
       }
       return HelloGreeting;
   }());
   function greet(greeting) {
       console.log(greeting.message);
   }
   var greeting = new HelloGreeting();
   greet(greeting);
   ```

### Step 3: Make a change to any source file and verify deployment<a name="tutorials-s3deploy-s3source-update"></a>

Make a change to your source files and then upload them to your source bucket\. This triggers your pipeline to run\. View your target bucket and verify that the deployed output files are available in the `js-application` folder as shown:

![\[Sample ZIP download\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/tutorial-s3deploy-pipeline-appzip.png)![\[Sample ZIP download\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Sample ZIP download\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)