# Tutorial: Create a pipeline with an Amazon ECR source and ECS\-to\-CodeDeploy deployment<a name="tutorials-ecs-ecr-codedeploy"></a>

In this tutorial, you configure a pipeline in AWS CodePipeline that deploys container applications using a blue/green deployment that supports Docker images\. In a blue/green deployment, you can launch the new version of your application alongside the old version and test the new version before you reroute traffic\. You can also monitor the deployment process and rapidly roll back if there is an issue\.

The completed pipeline detects changes to your image, which is stored in an image repository such as Amazon ECR, and uses CodeDeploy to route and deploy traffic to an Amazon ECS cluster and load balancer\. CodeDeploy uses a listener to reroute traffic to the port of the updated container specified in the AppSpec file\. For information about how the load balancer, production listener, target groups, and your Amazon ECS application are used in a blue/green deployment, see [Tutorial: Deploy an Amazon ECS Service](https://docs.aws.amazon.com/codedeploy/latest/userguide/tutorial-ecs-deployment.html)\.

The pipeline is also configured to use a source location, such as CodeCommit, where your Amazon ECS task definition is stored\. In this tutorial, you configure each of these AWS resources and then create your pipeline with stages that contain actions for each resource\.

Your continuous delivery pipeline will automatically build and deploy container images whenever source code is changed or a new base image is uploaded to Amazon ECR\.

This flow uses the following artifacts:
+ A Docker image file that specifies the container name and repository URI of your Amazon ECR image repository\.
+ An Amazon ECS task definition that lists your Docker image name, container name, Amazon ECS service name, and load balancer configuration\.
+ A CodeDeploy AppSpec file that specifies the name of the Amazon ECS task definition file, the name of the updated application's container, and the container port where CodeDeploy reroutes production traffic\. It can also specify optional network configuration and Lambda functions you can run during deployment lifecycle event hooks\.

**Note**  
When you commit a change to your Amazon ECR image repository, the pipeline source action creates an `imageDetail.json` file for that commit\. For information about the `imageDetail.json` file, see [imageDetail\.json file for Amazon ECS blue/green deployment actions](file-reference.md#file-reference-ecs-bluegreen)\.

When you create or edit your pipeline and update or specify source artifacts for your deployment stage, make sure to point to the source artifacts with the latest name and version you want to use\. After you set up your pipeline, as you make changes to your image or task definition, you might need to update your source artifact files in your repositories and then edit the deployment stage in your pipeline\.

**Topics**
+ [Prerequisites](#tutorials-ecs-ecr-codedeploy-prereq)
+ [Step 1: Create image and push to an Amazon ECR repository](#tutorials-ecs-ecr-codedeploy-imagerepository)
+ [Step 2: Create task definition and AppSpec source files and push to a CodeCommit repository](#tutorials-ecs-ecr-codedeploy-taskdefinition)
+ [Step 3: Create your Application Load Balancer and target groups](#tutorials-ecs-ecr-codedeploy-loadbal)
+ [Step 4: Create your Amazon ECS cluster and service](#tutorials-ecs-ecr-codedeploy-cluster)
+ [Step 5: Create your CodeDeploy application and deployment group \(ECS compute platform\)](#tutorials-ecs-ecr-codedeploy-deployment)
+ [Step 6: Create your pipeline](#tutorials-ecs-ecr-codedeploy-pipeline)
+ [Step 7: Make a change to your pipeline and verify deployment](#tutorials-ecs-ecr-codedeploy-update)

## Prerequisites<a name="tutorials-ecs-ecr-codedeploy-prereq"></a>

You must have already created the following resources:
+ A CodeCommit repository\. You can use the AWS CodeCommit repository you created in [Tutorial: Create a simple pipeline \(CodeCommit repository\)](tutorials-simple-codecommit.md)\.
+ Launch an Amazon EC2 Linux instance and install Docker to create an image as shown in this tutorial\. If you already have an image you want to use, you can skip this prerequisite\.

## Step 1: Create image and push to an Amazon ECR repository<a name="tutorials-ecs-ecr-codedeploy-imagerepository"></a>

In this section, you use Docker to create an image and then use the AWS CLI to create an Amazon ECR repository and push the image to the repository\.

**Note**  
If you already have an image you want to use, you can skip this step\.

**To create an image**

1. Sign in to your Linux instance where you have Docker installed\.

   Pull down an image for `nginx`\. This command provides the `nginx:latest` image from Docker Hub:

   ```
   docker pull nginx
   ```

1. Run docker images\. You should see the image in the list\.

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
           "repositoryArn": "arn:aws:ecr:us-east-1:aws_account_id:repository/nginx",
           "createdAt": 1505337806.0,
           "repositoryUri": "aws_account_id.dkr.ecr.us-east-1.amazonaws.com/nginx"
       }
   }
   ```

1. Tag the image with the `repositoryUri` value from the previous step\.

   ```
   docker tag nginx:latest aws_account_id.dkr.ecr.us-east-1.amazonaws.com/nginx:latest
   ```

1. Run the aws ecr get\-login\-password command, as shown in this example for the `us-west-2` Region\.

   ```
   aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin ACCOUNT_ID.dkr.ecr.us-west-2.amazonaws.com/nginx
   ```

1. Push the image to Amazon ECR using the `repositoryUri` from the earlier step\.

   ```
   docker push aws_account_id.dkr.ecr.us-east-1.amazonaws.com/nginx:latest
   ```

## Step 2: Create task definition and AppSpec source files and push to a CodeCommit repository<a name="tutorials-ecs-ecr-codedeploy-taskdefinition"></a>

In this section, you create a task definition JSON file and register it with Amazon ECS\. You then create an AppSpec file for CodeDeploy and use your Git client to push the files to your CodeCommit repository\.

**To create a task definition for your image**

1. Create a file named `taskdef.json` with the following contents\. For `image`, enter your image name, such as nginx\. This value is updated when your pipeline runs\.
**Note**  
Make sure that the execution role specified in the task definition contains the `AmazonECSTaskExecutionRolePolicy`\. For more information, see [Amazon ECS Task Execution IAM Role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html) in the *Amazon ECS Developer Guide*\.

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
+ The AppSpec file is used for CodeDeploy deployments\. The file, which includes optional fields, uses this format:

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
              AwsvpcConfiguration:
                Subnets: ["subnet-name-1", "subnet-name-2"]
                SecurityGroups: ["security-group"]
                AssignPublicIp: "ENABLED"
  Hooks:
  - BeforeInstall: "BeforeInstallHookFunctionName"
  - AfterInstall: "AfterInstallHookFunctionName"
  - AfterAllowTestTraffic: "AfterAllowTestTrafficHookFunctionName"
  - BeforeAllowTraffic: "BeforeAllowTrafficHookFunctionName"
  - AfterAllowTraffic: "AfterAllowTrafficHookFunctionName"
  ```

  For more information about the AppSpec file, including examples, see [CodeDeploy AppSpec File Reference](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html)\.

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

**To push files to your CodeCommit repository**

1. Push or upload the files to your CodeCommit repository\. These files are the source artifact created by the **Create pipeline** wizard for your deployment action in CodePipeline\. Your files should look like this in your local directory:

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

      1. Run the following command to push the files from your local repo to your CodeCommit repository:

         ```
         git push
         ```

   1. To use the CodeCommit console to upload your files:

      1. Open the CodeCommit console, and choose your repository from the **Repositories** list\.

      1. Choose **Add file**, and then choose **Upload file**\.

      1. Choose **Choose file**, and then browse for your file\. Commit the change by entering your user name and email address\. Choose **Commit changes**\.

      1. Repeat this step for each file you want to upload\.

## Step 3: Create your Application Load Balancer and target groups<a name="tutorials-ecs-ecr-codedeploy-loadbal"></a>

In this section, you create an Amazon EC2 Application Load Balancer\. You use the subnet names and target group values you create with your load balancer later, when you create your Amazon ECS service\. You can create an Application Load Balancer or a Network Load Balancer\. The load balancer must use a VPC with two public subnets in different Availability Zones\. In these steps, you confirm your default VPC, create a load balancer, and then create two target groups for your load balancer\. For more information, see [Target Groups for Your Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-target-groups.html)\.

**To verify your default VPC and public subnets**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Verify the default VPC to use\. In the navigation pane, choose **Your VPCs**\. Note which VPC shows **Yes** in the **Default VPC** column\. This is the default VPC\. It contains default subnets for you to select\.

1. Choose **Subnets**\. Choose two subnets that show **Yes** in the **Default subnet** column\.
**Note**  
Make a note of your subnet IDs\. You need them later in this tutorial\.

1. Choose the subnets, and then choose the **Description** tab\. Verify that the subnets you want to use are in different Availability Zones\.

1. Choose the subnets, and then choose the **Route Table** tab\. To verify that each subnet you want to use is a public subnet, confirm that a gateway row is included in the route table\.

**To create an Amazon EC2 Application Load Balancer**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Load Balancers**\.

1. Choose **Create Load Balancer**\.

1. Choose **Application Load Balancer**, and then choose **Create**\.

1. In **Name**, enter the name of your load balancer\.

1. In **Scheme**, choose **internet\-facing**\.

1. In **IP address type**, choose **ipv4**\.

1. Configure two listener ports for your load balancer:

   1. Under **Load Balancer Protocol**, choose **HTTP**\. Under **Load Balancer Port**, enter **80**\.

   1. Choose **Add listener**\.

   1. Under **Load Balancer Protocol** for the second listener, choose **HTTP**\. Under **Load Balancer Port**, enter **8080**\.

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

## Step 4: Create your Amazon ECS cluster and service<a name="tutorials-ecs-ecr-codedeploy-cluster"></a>

In this section, you create an Amazon ECS cluster and service where CodeDeploy routes traffic during deployment \(to an Amazon ECS cluster rather than EC2 instances\)\. To create your Amazon ECS service, you must use the subnet names, security group, and target group value you created with your load balancer to create your service\.

**Note**  
When you use these steps to create your Amazon ECS cluster, you use the **Networking only** cluster template, which provisions AWS Fargate containers\. AWS Fargate is a technology that manages your container instance infrastructure for you\. You do not need to choose or manually create Amazon EC2 instances for your Amazon ECS cluster\.

**To create an Amazon ECS cluster**

1. Open the Amazon ECS console at [https://console\.aws\.amazon\.com/ecs/](https://console.aws.amazon.com/ecs/)\.

1. In the navigation pane, choose **Clusters**\.

1. Choose **Create cluster**\.

1. Choose the **Networking only** cluster template that uses AWS Fargate, and then choose **Next step**\.

1. Enter a cluster name on the **Configure cluster** page\. You can add an optional tag for your resource\. Choose **Create**\.

**To create an Amazon ECS service**

Use the AWS CLI to create your service in Amazon ECS\.

1. Create a JSON file and name it `create-service.json`\. Paste the following into the JSON file\.

   For the `taskDefinition` field, when you register a task definition in Amazon ECS, you give it a family\. This is similar to a name for multiple versions of the task definition, specified with a revision number\. In this example, use "`ecs-demo:1`" for the family and revision number in your file\. Use the subnet names, security group, and target group value you created with your load balancer in [Step 3: Create your Application Load Balancer and target groups ](#tutorials-ecs-ecr-codedeploy-loadbal)\.
**Note**  
You need to include your target group ARN in this file\. Open the Amazon EC2 console and from the navigation pane, under **LOAD BALANCING**, choose **Target Groups**\. Choose your first target group\. Copy your ARN from the **Description** tab\.

   ```
   {
       "taskDefinition": "family:revision-number",
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
**Note**  
This example command creates a service named my\-service\. If you already have a service with this name, the command returns an error\.

   ```
   aws ecs create-service --service-name my-service --cli-input-json file://create-service.json
   ```

   The output returns the description fields for your service\.

1. Run the describe\-services command to verify that your service was created\.

   ```
   aws ecs describe-services --cluster cluster-name --services service-name
   ```

## Step 5: Create your CodeDeploy application and deployment group \(ECS compute platform\)<a name="tutorials-ecs-ecr-codedeploy-deployment"></a>

When you create a CodeDeploy application and deployment group for the Amazon ECS compute platform, the application is used during a deployment to reference the correct deployment group, target groups, listeners, and traffic rerouting behavior\.

**To create a CodeDeploy application**

1. Open the CodeDeploy console and choose **Create application**\.

1. In **Application name**, enter the name you want to use\.

1. In **Compute platform**, choose **Amazon ECS**\.

1. Choose **Create application**\.

**To create a CodeDeploy deployment group**

1. On your application page's **Deployment groups** tab, choose **Create deployment group**\.

1. In **Deployment group name**, enter a name that describes the deployment group\.

1. In **Service role**, choose a service role that grants CodeDeploy access to Amazon ECS\. To create a new service role, follow these steps:

   1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\)\.

   1. From the console dashboard, choose **Roles**\.

   1. Choose **Create role**\.

   1. Under **Select type of trusted entity**, select **AWS service**\. Under **Choose a use case**, select **CodeDeploy**\. Under **Select your use case**, select **CodeDeploy \- ECS**\. Choose **Next: Permissions**\. The `AWSCodeDeployRoleForECS` managed policy is already attached to the role\.

   1. Choose **Next: Tags**, and **Next: Review**\.

   1. Enter a name for the role \(for example, **CodeDeployECSRole**\), and then choose **Create role**\.

1. In **Environment configuration**, choose your Amazon ECS cluster name and service name\.

1. From **Load balancers**, choose the name of the load balancer that serves traffic to your Amazon ECS service\.

1. From **Production listener port**, choose the port and protocol for the listener that serves production traﬃc to your Amazon ECS service\. From **Test listener port**, choose the port and protocol for the test listener\.

1. From **Target group 1 name** and **Target group 2 name**, choose the target groups used to route traffic during your deployment\. Make sure that these are the target groups you created for your load balancer\.

1. Choose **Reroute traffic immediately** to determine how long after a successful deployment to reroute traffic to your updated Amazon ECS task\.

1. Choose **Create deployment group**\.

## Step 6: Create your pipeline<a name="tutorials-ecs-ecr-codedeploy-pipeline"></a>

In this section, you create a pipeline with the following actions:
+ A CodeCommit action where the source artifacts are the task definition and the AppSpec file\.
+ A source stage with an Amazon ECR source action where the source artifact is the image file\.
+ A deployment stage with an Amazon ECS deploy action where the deployment runs with a CodeDeploy application and deployment group\.

**To create a two\-stage pipeline with the wizard**

1. Sign in to the AWS Management Console and open the CodePipeline console at [http://console\.aws\.amazon\.com/codesuite/codepipeline/home](http://console.aws.amazon.com/codesuite/codepipeline/home)\.

1. On the **Welcome** page, **Getting started** page, or the **Pipelines** page, choose **Create pipeline**\.

1. In **Step 1: Choose pipeline settings**, in **Pipeline name**, enter **MyImagePipeline**\.

1. In **Service role**, choose **New service role** to allow CodePipeline to create a service role in IAM\.

1. Leave the settings under **Advanced settings** at their defaults, and then choose **Next**\.

1. In **Step 2: Add source stage**, in **Source provider**, choose **AWS CodeCommit**\. In **Repository name**, choose the name of the CodeCommit repository you created in [Step 1: Create a CodeCommit repository](tutorials-simple-codecommit.md#codecommit-create-repository)\. In **Branch name**, choose the name of the branch that contains your latest code update\. Unless you created a different branch on your own, only `master` is available\.

   Choose **Next**\.

1. In **Step 3: Add build stage**, choose **Skip build stage**, and then accept the warning message by choosing **Skip** again\. Choose **Next**\.

1. In **Step 4: Add deploy stage**:

   1. In **Deploy provider**, choose **Amazon ECS \(Blue/Green\)**\. In **Application name**, enter or choose the application name from the list, such as `codedeployapp`\. In **Deployment group**, enter or choose the deployment group name from the list, such as `codedeploydeplgroup`\.  
![\[The Step 4: Deploy page for an Amazon ECS action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/ECS-deploy-action.png)![\[The Step 4: Deploy page for an Amazon ECS action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 4: Deploy page for an Amazon ECS action\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)
**Note**  
The name "Deploy" is the name given by default to the stage created in the **Step 4: Deploy** step, just as "Source" is the name given to the first stage of the pipeline\.

   1. Under **Amazon ECS task definition**, choose **SourceArtifact**\. In the field, enter **taskdef\.json**\.

   1. Under **AWS CodeDeploy AppSpec file**, choose **SourceArtifact**\. In the field, enter **appspec\.yaml**\.
**Note**  
At this point, do not fill in any information under **Dynamically update task definition image**\.

   1. Choose **Next**\.

1. In **Step 5: Review**, review the information, and then choose **Create pipeline**\.

**To add an Amazon ECR source action to your pipeline**

View your pipeline and add an Amazon ECR source action to your pipeline\.

1. Choose your pipeline\. In the upper left, choose **Edit**\.

1. In the source stage, choose **Edit stage**\.

1. Add a parallel action by choosing **\+ Add action** next to your CodeCommit source action\.

1. In **Action name**, enter a name \(for example, **Image**\)\.

1. In **Action provider**, choose **Amazon ECR**\.  
![\[\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/ECR-source-action.png)

1. In **Repository name**, choose the name of your Amazon ECR repository\.

1. In **Image tag**, specify the image name and version, if different from latest\.

1. In **Output artifacts**, choose the output artifact default \(for example, `MyImage`\) that contains the image name and repository URI information you want the next stage to use\.

1. Choose **Save** on the action screen\. Choose **Done** on the stage screen\. Choose **Save** on the pipeline\. A message shows the Amazon CloudWatch Events rule to be created for the Amazon ECR source action\.

**To wire your source artifacts to the deploy action**

1. Choose **Edit** on your Deploy stage and choose the icon to edit the **Amazon ECS \(Blue/Green\)** action\.

1. Scroll to the bottom of the pane\. In **Input artifacts**, choose **Add**\. Add the source artifact from your new Amazon ECR repository \(for example, `MyImage`\)\.

1. In **Task Definition**, choose **SourceArtifact**, and then verify **taskdef\.json** is entered\.

1. In **AWS CodeDeploy AppSpec File**, choose **SourceArtifact**, and then verify **appspec\.yaml** is entered\.

1. In **Dynamically update task definition image**, in **Input Artifact with Image URI**, choose **MyImage**, and then enter the placeholder text that is used in the `taskdef.json` file:** IMAGE1\_NAME**\. Choose **Save**\.  
![\[The Step 4: Deploy page for an edited Amazon ECS stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/tutorial-ECS-pipeline-edit-deploystage.png)![\[The Step 4: Deploy page for an edited Amazon ECS stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[The Step 4: Deploy page for an edited Amazon ECS stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. In the AWS CodePipeline pane, choose **Save pipeline change**, and then choose **Save change**\. View your updated pipeline\.

   After this example pipeline is created, the action configuration for the console entries appears in the pipeline structure as follows:

   ```
   "configuration": {
     "AppSpecTemplateArtifact": "SourceArtifact",
     "AppSpecTemplatePath": "appspec.yaml",
     "TaskDefinitionTemplateArtifact": "SourceArtifact",
     "TaskDefinitionTemplatePath": "taskdef.json",
     "ApplicationName": "codedeployapp",
     "DeploymentGroupName": "codedeploydeplgroup",
     "Image1ArtifactName": "MyImage",
     "Image1ContainerName": "IMAGE1_NAME"
   },
   ```

1. To submit your changes and start a pipeline build, choose **Release change**, and then choose **Release**\.  
![\[Completed pipeline with an Amazon ECS Blue/Green stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/images/tutorial-ECS-bluegreen-pipeline.png)![\[Completed pipeline with an Amazon ECS Blue/Green stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)![\[Completed pipeline with an Amazon ECS Blue/Green stage\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/)

1. Choose the deployment action to view it in CodeDeploy and see the progress of the traffic shifting\.
**Note**  
You might see a deployment step that shows an optional wait time\. By default, CodeDeploy waits one hour after a successful deployment before it terminates the original task set\. You can use this time to roll back or terminate the task, but your deployment otherwise completes when the task set is terminated\.

## Step 7: Make a change to your pipeline and verify deployment<a name="tutorials-ecs-ecr-codedeploy-update"></a>

Make a change to your image and then push the change to your Amazon ECR repository\. This triggers your pipeline to run\. Verify that your image source change is deployed\.