--------

The procedures in this guide support the new console design\. If you choose to use the older version of the console, you will find many of the concepts and basic procedures in this guide still apply\. To access help in the new console, choose the information icon\.

--------

# Tutorial: Create a Pipeline with an Amazon ECR Source and ECS\-to\-CodeDeploy Deployment<a name="tutorials-ecs-ecr-codedeploy"></a>

In this tutorial, you configure a pipeline that continuously delivers your container applications using AWS resources that support Docker images\. The completed pipeline detects changes to your image, which is stored in the Amazon ECR image repository, and uses AWS CodeDeploy to route and deploy traffic to an Amazon ECS cluster and load balancer\. AWS CodeDeploy uses a listener to reroute traffic to the port of the updated container specified in the AppSpec file\. The pipeline is also configured to use an AWS CodeCommit source location where your Amazon ECS task definition is stored\. In this tutorial, you configure each of these AWS resources and then create your pipeline with stages that contain actions for each resource\.

The artifacts you set up in your pipeline source repositories become the input and output artifacts used between stages\. The source stage creates output artifacts that you then select as input artifacts for your deployment stage\. This flow uses the following artifacts: 
+ A Docker image file that specifies the container name and repository URI of your Amazon ECR image repository\.
+ An Amazon ECS task definition that lists your Docker image name, container name, Amazon ECS service name, and load balancer configuration\.
+ An AWS CodeDeploy AppSpec file that specifies the name of the Amazon ECS task definition file, the name of the updated application's container, and the container port where AWS CodeDeploy reroutes production traffic\. It can also specify optional network configuration and Lambda functions you can run during deployment lifecycle event hooks\.

When you create or edit your pipeline and update or specify source artifacts for your deployment stage, make sure to point to the source artifacts with the latest name and version you want to use\. After you set up your pipeline, as you make changes to your image or task definition, you might need to update your source artifact files in your repositories and then edit the deployment stage in your pipeline\.

**Topics**
+ [Prerequisites](#tutorials-ecs-ecr-codedeploy-prereq)
+ [Step 1: Create Image and Push to Amazon ECR Repository](#tutorials-ecs-ecr-codedeploy-imagerepository)
+ [Step 2: Create Task Definition Source Files and Push to AWS CodeCommit Repository](#tutorials-ecs-ecr-codedeploy-taskdefinition)
+ [Step 3: Create Your Application Load Balancer and Target Groups](#tutorials-ecs-ecr-codedeploy-loadbal)
+ [Step 4: Create Your Amazon ECS Cluster and Service](#tutorials-ecs-ecr-codedeploy-cluster)
+ [Step 5: Create Your AWS CodeDeploy Application and Deployment Group \(ECS Compute Platform\)](#tutorials-ecs-ecr-codedeploy-deployment)
+ [Step 6: Create Your Pipeline](#tutorials-ecs-ecr-codedeploy-pipeline)
+ [Step 7: Make a Change to Your Pipeline and Verify Deployment](#tutorials-ecs-ecr-codedeploy-update)

## Prerequisites<a name="tutorials-ecs-ecr-codedeploy-prereq"></a>

You must have already created the following resources:
+ An AWS CodeCommit repository\. You can use the AWS CodeCommit repository you created in [Tutorial: Create a Simple Pipeline \(AWS CodeCommit Repository\)](tutorials-simple-codecommit.md)\.
+ Launch an Amazon EC2 Linux instance and install Docker to create an image as shown in this tutorial\. If you already have an image you want to use, you can skip this prerequisite\.
+ Use the Amazon EC2 console to create an Application Load Balancer or a Network Load Balancer\. The load balancer must use a VPC with two public subnets in different Availability Zones \(the default VPC can be used\)\. Configure one target group for your load balancer \(with IP configuration\)\. For instructions, see [Target Groups for Your Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-target-groups.html)\.

## Step 1: Create Image and Push to Amazon ECR Repository<a name="tutorials-ecs-ecr-codedeploy-imagerepository"></a>

In this section, you use Docker to create an image and then use the AWS CLI to create an Amazon ECR repository and push the image to the repository\.

**Note**  
If you already have an image you want to use, you can skip this step\.

**To create an image**

1. Sign in to your Linux instance where you have Docker installed\. 

   Pull down an image for `nginx`\. This command provides the `nginx:latest` image from Docker Hub:

   ```
   docker pull nginx
   ```

1. Run docker images\. You should see the image in the list\.\. 

   ```
   docker images
   ```

**To create an Amazon ECR repository and push your image**

1. Create an Amazon ECR repository to store your image\. Make a note of the `repositoryUri` in the output\.

   ```
   aws ecr create-repository --repository-name nginx
   ```

   Output:

   ```
   {
       "repository": {
           "registryId": "aws_account_id",
           "repositoryName": "nginx",
           "repositoryArn": "arn:aws:ecr:us-east-1:aws_account_id:repository/hello-world",
           "createdAt": 1505337806.0,
           "repositoryUri": "aws_account_id.dkr.ecr.us-east-1.amazonaws.com/hello-world"
       }
   }
   ```

1. Tag the image with the `repositoryUri` value from the previous step\.

   ```
   docker tag nginx:latest aws_account_id.dkr.ecr.us-east-1.amazonaws.com/nginx:latest
   ```

1. Run the aws ecr get\-login \-\-no\-include\-email command in parentheses to get the docker login authentication command string for your registry\.

   ```
   $(aws ecr get-login --no-include-email)
   ```

   The authorization token command is run in memory \(not exposed in the output\) and completes the login\.

1. Push the image to Amazon ECR using the `repositoryUri` from the earlier step\.

   ```
   docker push aws_account_id.dkr.ecr.us-east-1.amazonaws.com/nginx:latest
   ```

## Step 2: Create Task Definition Source Files and Push to AWS CodeCommit Repository<a name="tutorials-ecs-ecr-codedeploy-taskdefinition"></a>

In this section, you create a task definition JSON file and register it with Amazon ECS\. You then create an AppSpec file for AWS CodeDeploy and use your Git client to push the files to your AWS CodeCommit repository\.

**To create a task definition for your image**

1. Create a file named `taskdef.json` with the following contents\. For `image`, enter your image name, such as nginx\. This value is updated when your pipeline runs\.
**Note**  
Make sure that the execution role specified in the task definition contains the AmazonECSTaskExecutionRolePolicy\. For more information, see [Amazon ECS Task Execution IAM Role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html)\.

   ```
   {
       "executionRoleArn": "arn:aws:iam::account_ID:role/ecsTaskExecutionRole",
       "containerDefinitions": [
           {
               "name": "sample-website",
               "image": "nginx",
               "essential": true,
               "portMappings": [
                   {
                       "hostPort": 80,
                       "protocol": "tcp",
                       "containerPort": 80
                   }
               ]
           }
       ],
       "requiresCompatibilities": [
           "FARGATE"
       ],
       "networkMode": "awsvpc",
       "cpu": "256",
       "memory": "512",
       "family": "ecs-demo"
   }
   ```

1. Register your task definition with the `taskdef.json` file\.

   ```
   aws ecs register-task-definition --cli-input-json file://taskdef.json
   ```

1. After the task definition is registered, edit your file to remove the image name and include the `<IMAGE1_NAME>` placeholder text in the image field\. 

   ```
   {
       "executionRoleArn": "arn:aws:iam::account_ID:role/ecsTaskExecutionRole",
       "containerDefinitions": [
           {
               "name": "sample-website",
               "image": "<IMAGE1_NAME>",
               "essential": true,
               "portMappings": [
                   {
                       "hostPort": 80,
                       "protocol": "tcp",
                       "containerPort": 80
                   }
               ]
           }
       ],
       "requiresCompatibilities": [
           "FARGATE"
       ],
       "networkMode": "awsvpc",
       "cpu": "256",
       "memory": "512",
       "family": "ecs-demo"
   }
   ```

**To create an AppSpec file**
+ The AppSpec file is used for AWS CodeDeploy deployments\. The file, which includes optional fields, uses this format: 

  ```
  version: 0.0
  Resources:
    - TargetService:
        Type: AWS::ECS::Service
        Properties:
          TaskDefinition: "task-definition-ARN"
          LoadBalancerInfo:
            ContainerName: "container-name"
            ContainerPort: container-port-number
  # Optional properties
          PlatformVersion: "LATEST"
          NetworkConfiguration:
              awsvpcConfiguration:
                subnets: ["subnet-name-1", "subnet-name-2"]
                securityGroups: "security-group"
                assignPublicIp: "ENABLED"
  
  Hooks:
  - BeforeInstall: "BeforeInstallHookFunctionName"
  - AfterInstall: "AfterInstallHookFunctionName"
  - AfterAllowTestTraffic: "AfterAllowTestTrafficHookFunctionName"
  - BeforeAllowTraffic: "BeforeAllowTrafficHookFunctionName"
  - AfterAllowTraffic: "AfterAllowTrafficHookFunctionName"
  ```

  ```
  ```

  For more information about the AppSpec file, including examples, see [AWS CodeDeploy AppSpec File Reference](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html)\.

  Create a file named `appspec.yaml` with the following contents\. For `TaskDefinition`, do not change the `<TASK_DEFINITION>` placeholder text\. This value is updated when your pipeline runs\.

  ```
  version: 0.0
  Resources:
    - TargetService:
        Type: AWS::ECS::Service
        Properties:
          TaskDefinition: <TASK_DEFINITION>
          LoadBalancerInfo:
            ContainerName: "sample-website"
            ContainerPort: 80
  ```

**To push files to your AWS CodeCommit repository**

1. Push or upload the files to your AWS CodeCommit repository\. These files are the source artifact created by the pipeline creation wizard for your deployment action in AWS CodePipeline\. Your files should look like this in your local directory:

   ```
   /tmp
     |my-demo-repo
       |-- appspec.yaml
       |-- taskdef.json
   ```

1. Choose the method you want to use to upload your files:

   1. To use your git command line from a cloned repository on your local computer:

      1. Change directories to your local repository:

         ```
         (For Linux, macOS, or Unix) cd /tmp/my-demo-repo
         (For Windows) cd c:\temp\my-demo-repo
         ```

      1. Run the following command to stage all of your files at once:

         ```
         git add -A
         ```

      1. Run the following command to commit the files with a commit message:

         ```
         git commit -m "Added task definition files"
         ```

      1. Run the following command to push the files from your local repo to your AWS CodeCommit repository:

         ```
         git push
         ```

   1. To use the AWS CodeCommit console to upload your files: 

      1. Open the AWS CodeCommit console, and choose your repository from the **Repositories** list\.

      1. Choose **Add file**, and then choose **Upload file**\. 

      1. Choose **Choose file**, and then browse for your file\. Commit the change by entering your user name and email address\. Choose **Commit changes**\.

      1. Repeat this step for each file you want to upload\.

## Step 3: Create Your Application Load Balancer and Target Groups<a name="tutorials-ecs-ecr-codedeploy-loadbal"></a>

In this section, you create an Amazon EC2 application load balancer\. You use the subnet names and target group values you create with your load balancer later, when you create your Amazon ECS service\. You can create an Application Load Balancer or a Network Load Balancer\. The load balancer must use a VPC with two public subnets in different Availability Zones\. In these steps, you confirm your default VPC, create a load balancer, and then create two target groups for your load balancer\. For more information, see [Target Groups for Your Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-target-groups.html)\.

**To verify your default VPC and public subnets**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Verify the default VPC to use\. In the navigation pane, choose **Your VPCs**\. Note which VPC shows **Yes** in the **Default VPC** column\. This is the default VPC\. It contains default subnets for you to select\.

1. Choose **Subnets**\. Choose two subnets that show **Yes** in the **Default subnet** column\. Make a note of the subnet IDs\.

1. Choose the subnets, and then choose the **Description** tab\. Verify that the subnets you want to use are in different Availability Zones\.

1. Choose the subnets, and then choose the **Route Table** tab\. To verify that each subnet you want to use is a public subnet, confirm that a gateway row is included in the route table\.

**To create an Amazon EC2 application load balancer**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Load Balancers**\. 

1. Choose **Create Load Balancer**\.

1. Choose **Application Load Balancer**, and then choose **Create**\.

1. In **Name**, enter the name of your load balancer\.

1. In **Scheme**, choose **internet\-facing**\.

1. In **IP address type**, choose **ipv4**\.

1. Configure two listener ports for your load balancer:

   1. Under **Load Balancer Protocol**, choose **HTTP**\. **Under Load Balancer Port**, enter **80**\.

   1. Choose **Add listener**\.

   1. Under **Load Balancer Protocol** for the second listener, choose **HTTP**\. **Under Load Balancer Port**, enter **8080**\.

1. Under **Availability Zones**, in **VPC**, choose the default VPC\. Next, choose the two default subnets you want to use\.

1. Choose **Next: Configure Security Settings**\.

1. Choose **Next: Configure Security Groups**\.

1. Choose **Select an existing security group**, and make a note of the security group ID\.

1. Choose **Next: Configure Routing**\.

1. In **Target group**, choose **New target group** and configure your first target group: 

   1. In **Name**, enter a target group name \(for example, **target\-group\-1**\)\.

   1. In **Target type**, choose **IP**\.

   1. In **Protocol** choose **HTTP**\. In **Port**, enter **80**\.

   1. Choose **Next: Register Targets**\.

1. Choose **Next: Review**, and then choose **Create**\.

**To create a second target group for your load balancer**

1. After your load balancer is provisioned, open the Amazon EC2 console\. In the navigation pane, choose **Target Groups**\.

1. Choose **Create target group**\.

1. In **Name**, enter a target group name \(for example, **target\-group\-2**\)\.

1. In **Target type**, choose **IP**\.

1. In **Protocol** choose **HTTP**\. In **Port**, enter **8080**\.

1. In **VPC**, choose the default VPC\.

1. Choose **Create**\.
**Note**  
You must have two target groups created for your load balancer in order for your deployment to run\. You only need to make a note of the ARN of your first target group\. This ARN is used in the `create-service` JSON file in the next step\.

**To update your load balancer to include your second target group**

1. Open the Amazon EC2 console\. In the navigation pane, choose **Load Balancers**\.

1. Choose your load balancer, and then choose the **Listeners** tab\. Choose the listener with port 8080, and then choose **Edit**\.

1. Choose the pencil icon next to **Forward to**\. Choose your second target group, and then choose the check mark\. Choose **Update** to save the updates\.

## Step 4: Create Your Amazon ECS Cluster and Service<a name="tutorials-ecs-ecr-codedeploy-cluster"></a>

In this section, you create an Amazon ECS cluster and service where AWS CodeDeploy routes traffic during deployment \(to an Amazon ECS cluster rather than EC2 instances\)\. To create your Amazon ECS service, you must use the subnet names and target group value you created with your load balancer to create your service\. 

**To create an Amazon ECS cluster**

1. Open the Amazon ECS console at [https://console\.aws\.amazon\.com/ecs/](https://console.aws.amazon.com/ecs/)\.

1. In the navigation pane, choose **Clusters**\.

1. Choose **Create cluster**\.

1. Choose the **Networking only** cluster template that is powered by Fargate, and then choose **Next step**\.

1. Enter a cluster name and anything else on the **Configure cluster** page, and then choose **Create**\.

**To create an Amazon ECS service**

Use the AWS CLI to create your service in Amazon ECS\. To use the default cluster rather than the cluster you just created, you can indicate `"default"` in the `"cluster"` field in the JSON file\.

1. Create a JSON file and name it `create-service.json`\. Paste the following into the JSON file\.
**Note**  
You need to include your target group ARN in this file\. Open the Amazon EC2 console and from the navigation pane, under **LOAD BALANCING**, choose **Target Groups**\. Choose your first target group\. Copy your ARN from the **Description** tab\.

   ```
   {
       "taskDefinition": "ecs-demo:1",
       "cluster": "my-cluster",
       "loadBalancers": [
           {
               "targetGroupArn": "target-group-arn",
               "containerName": "sample-website",
               "containerPort": 80
           }
       ],
       "desiredCount": 1,
       "launchType": "FARGATE",
       "schedulingStrategy": "REPLICA",
       "deploymentController": {
           "type": "CODE_DEPLOY"
       },
       "networkConfiguration": {
           "awsvpcConfiguration": {
               "subnets": [
                   "subnet-1",
                   "subnet-2"
               ],
               "securityGroups": [
                   "security-group"
               ],
               "assignPublicIp": "ENABLED"
           }
       }
   }
   ```

1. Run the create\-service command, specifying the JSON file:
**Important**  
Be sure to include `file://` before the file name\. It is required in this command\.

   This example creates a service named `my-service`\.

   ```
   aws ecs create-service --service-name my-service --cli-input-json file://create-service.json
   ```

   The output returns the description fields for your service\.

1. Run the describe\-services command to verify that your service was created\.

   ```
   aws ecs describe-services --cluster cluster-name --services service-name
   ```

## Step 5: Create Your AWS CodeDeploy Application and Deployment Group \(ECS Compute Platform\)<a name="tutorials-ecs-ecr-codedeploy-deployment"></a>

Create an AWS CodeDeploy application and deployment group\. 

**To create an AWS CodeDeploy application**

1. Open the AWS CodeDeploy console and choose **Create application**\.

1. In **Application name**, enter the name you want to use\.

1. In **Compute platform**, choose **Amazon ECS**\.

1. Choose **Create application**\.

**To create an AWS CodeDeploy deployment group**

1. On your application page's **Deployment groups** tab, choose **Create deployment group**\.

1. In **Deployment group name**, enter a name that describes the deployment group\.

1. In **Service role**, choose a service role that grants AWS CodeDeploy access to Amazon ECS\.

1. In **Environment configuration**, choose your Amazon ECS cluster name and service name\.

1. From **Load balancers**, choose the name of the load balancer that serves traffic to your Amazon ECS service\.

1. From **Production listener port**, choose the port and protocol for the listener that serves production traﬃc to your Amazon ECS service\.

1. From **Target group 1 name** and **Target group 2 name**, choose the target groups used to route traffic during your deployment\. Make sure that these are the target groups you created for your load balancer\.

1. Choose **Reroute traffic immediately** to determine how long after a successful deployment to reroute traffic to your updated Amazon ECS task\.

1. Choose **Create deployment group**\.

## Step 6: Create Your Pipeline<a name="tutorials-ecs-ecr-codedeploy-pipeline"></a>

In this section, you create a pipeline with the following actions:
+ An AWS CodeCommit action where the source artifacts are the task definition and the AppSpec file\.
+ A source stage with an Amazon ECR source action where the source artifact is the image file\.
+ A deployment stage with an Amazon ECS deploy action where the deployment runs with an AWS CodeDeploy application and deployment group\.

**To create a two\-stage pipeline with the wizard**

1. Sign in to the AWS Management Console and open the AWS CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Welcome** page, **Getting started** page, or the **Pipelines** page, choose **Create pipeline**\.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyImagePipeline**\.

1. In **Role name**:
   + If you do not have a service role, choose **New service role**, and in **Role name**, enter the name for the role\. IAM creates the role for you\.
   + If you have previously created an AWS\-CodePipeline\-Service service role, choose **Existing service role**\.
**Note**  
Depending on when your service role was created, you might need to update its permissions to support additional AWS services\. For information, see [Add Permissions for Other AWS Services](how-to-custom-role.md#how-to-update-role-new-services)\. 

1. In **Artifact store**: 

   1. Choose **Default location** to use the default artifact store, such as the Amazon S3 artifact bucket designated as the default, for your pipeline in the region you have selected for your pipeline\.

   1. Choose **Custom location** if you already have an artifact store, such as an Amazon S3 artifact bucket, in the same region as your pipeline\.
**Note**  
This is not the source bucket for your pipeline's source code\. This is the artifact store for your pipeline\. A separate artifact store, such as an Amazon S3 bucket, is required for each pipeline, in the same region as the pipeline\.

   Choose **Next**\.

1. In **Step 2: Add source stage**, in **Source provider**, choose AWS CodeCommit\. In **Repository name**, choose the name of the AWS CodeCommit repository you created in [Step 1: Create an AWS CodeCommit Repository and Local Repo](tutorials-simple-codecommit.md#codecommit-create-repository)\. In **Branch name**, choose the name of the branch that contains your latest code update\. Unless you created a different branch on your own, only `master` is available\. 

   After you select the repository name and branch, a message shows the Amazon CloudWatch Events rule to be created for this pipeline\. 

   Choose **Next**\.

1. In **Step 3: Add build stage**, choose **Skip**, and then accept the warning message by choosing **Skip** again\. Choose **Next**\.

1. In **Step 4: Add deploy stage**:

   1. In **Deploy provider**, choose **Amazon ECS \(Blue/Green\)**\. In **Application name**, enter or choose the application name from the list\. In **Deployment group**, enter or choose the deployment group name from the list\.  
![\[The Step 4: Deploy page for an Amazon ECS action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/ECS-deploy-action.png)![\[The Step 4: Deploy page for an Amazon ECS action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 4: Deploy page for an Amazon ECS action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
**Note**  
The name "Deploy" is the name given by default to the stage created in the **Step 4: Deploy** step, just as "Source" is the name given to the first stage of the pipeline\.

   1. Under **Amazon ECS task definition**, choose **SourceArtifact**\.

   1. Under **AWS CodeDeploy AppSpec file**, choose **SourceArtifact**\.
**Note**  
At this point, do not fill in any information under **Dynamically update task definition image**\.

   1. Choose **Next**\.

1. In **Step 5: Review**, review the information, and then choose **Create pipeline**\.

**To add an Amazon ECR source action to your pipeline**

View your pipeline and add an Amazon ECR source action to your pipeline\.

1. Choose your pipeline\. In the upper left, choose **Edit**\.

1. In the source stage, choose **Edit stage**\.

1. Add a parallel action by choosing **\+ Add action** next to your AWS CodeCommit source action\.

1. In **Action name**, enter a name \(for example, **Image**\)\. 

1. In **Action provider**, choose **Amazon ECR**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/ECR-source-action.png)

1. In **Repository name**, choose the name of your Amazon ECR repository\.

1. In **Image tag**, specify the image name and version, if different from latest\.

1. In **Output artifacts**, choose the output artifact default \(for example, `MyImage`\) that contains the image name and repository URI information you want the next stage to use\.

1. Choose **Save**\.

**To wire your source artifacts to the Deploy action**

1. Choose **Edit** on your Deploy stage and choose the icon to edit the **Amazon ECS \(Blue/Green\)** action\.

1. Scroll to the bottom of the pane\. In **Input artifacts**, choose **Add**\. Add the source artifact from your new Amazon ECR repository \(for example, `MyImage`\)\.

1. In **Task Definition**, choose **SourceArtifact**, and then enter **taskdef\.json**\.

1. In **AWS CodeDeploy AppSpec File**, choose **SourceArtifact** and enter **appspec\.yaml**\.

1. In **Dynamically update task definition image**, in **Input Artifact with Image URI**, choose **MyImage**, and then enter the placeholder text that is used in the `taskdef.json` file: "IMAGE1\_NAME"\. Choose **Save**\. 

1.   
![\[The Step 4: Deploy page for an edited Amazon ECS stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/tutorial-ECS-pipeline-edit-deploystage.png)![\[The Step 4: Deploy page for an edited Amazon ECS stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 4: Deploy page for an edited Amazon ECS stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. In the AWS CodePipeline pane, choose **Save pipeline change**, and then choose **Save change**\. View your updated pipeline\.

1. To submit your changes and start a pipeline build, choose **Release change**, and then choose **Release**\.  
![\[Completed pipeline with an Amazon ECS Blue/Green stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/tutorial-ECS-bluegreen-pipeline.png)![\[Completed pipeline with an Amazon ECS Blue/Green stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Completed pipeline with an Amazon ECS Blue/Green stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

## Step 7: Make a Change to Your Pipeline and Verify Deployment<a name="tutorials-ecs-ecr-codedeploy-update"></a>

Make a change to your image and then push the change to your Amazon ECR repository\. This triggers your pipeline to run\. Verify that your image source change is deployed\.