# Create a CloudWatch Events Rule for an Amazon ECR Source \(AWS CloudFormation Template\)<a name="create-cwe-ecr-source-cfn"></a>

To use AWS CloudFormation to create a rule, use the template snippet as shown here\.

**To update your pipeline AWS CloudFormation template and create CloudWatch Events rule**

1. In the template, under `Resources`, use the `AWS::IAM::Role` AWS CloudFormation resource to configure the IAM role that allows your event to start your pipeline\. This entry creates a role that uses two policies:
   + The first policy allows the role to be assumed\.
   + The second policy provides permissions to start the pipeline\.

   **Why am I making this change?** Adding the `AWS::IAM::Role` resource enables AWS CloudFormation to create permissions for CloudWatch Events\. This resource is added to your AWS CloudFormation stack\.

------
#### [ YAML ]

   ```
     AmazonCloudWatchEventRole:
       Type: AWS::IAM::Role
       Properties:
         AssumeRolePolicyDocument:
           Version: 2012-10-17
           Statement:
             -
               Effect: Allow
               Principal:
                 Service:
                   - events.amazonaws.com
               Action: sts:AssumeRole
         Path: /
         Policies:
           -
             PolicyName: cwe-pipeline-execution
             PolicyDocument:
               Version: 2012-10-17
               Statement:
                 -
                   Effect: Allow
                   Action: codepipeline:StartPipelineExecution
                   Resource: !Join [ '', [ 'arn:aws:codepipeline:', !Ref 'AWS::Region', ':', !Ref 'AWS::AccountId', ':', !Ref AppPipeline ] ]
   ```

------
#### [ JSON ]

   ```
           "AmazonCloudWatchEventRole": {
               "Type": "AWS::IAM::Role",
               "Properties": {
                   "AssumeRolePolicyDocument": {
                       "Version": "2012-10-17",
                       "Statement": [
                           {
                               "Effect": "Allow",
                               "Principal": {
                                   "Service": [
                                       "events.amazonaws.com"
                                   ]
                               },
                               "Action": "sts:AssumeRole"
                           }
                       ]
                   },
                   "Path": "/",
                   "Policies": [
                       {
                           "PolicyName": "cwe-pipeline-execution",
                           "PolicyDocument": {
                               "Version": "2012-10-17",
                               "Statement": [
                                   {
                                       "Effect": "Allow",
                                       "Action": "codepipeline:StartPipelineExecution",
                                       "Resource": {
                                           "Fn::Join": [
                                               "",
                                               [
                                                   "arn:aws:codepipeline:",
                                                   {
                                                       "Ref": "AWS::Region"
                                                   },
                                                   ":",
                                                   {
                                                       "Ref": "AWS::AccountId"
                                                   },
                                                   ":",
                                                   {
                                                       "Ref": "AppPipeline"
                                                   }
                                               ]
   
   ...
   ```

------

1. In the template, under `Resources`, use the `AWS::Events::Rule` AWS CloudFormation resource to add a CloudWatch Events rule for the Amazon ECR source\. This event pattern creates an event that monitors push changes to your repository When CloudWatch Events detects a repository state change, the rule invokes `StartPipelineExecution` on your target pipeline\.

   **Why am I making this change? ** Adding the `AWS::Events::Rule` resource enables AWS CloudFormation to create the event\. This resource is added to your AWS CloudFormation stack\. This snippet uses an image named `my-image-repo` with a tag of `latest`\.

------
#### [ YAML ]

   ```
     AmazonCloudWatchEventRule:
       Type: AWS::Events::Rule
       Properties:
         EventPattern:
           source:
             - aws.ecr
           detail:
             eventName:
               - PutImage
             requestParameters:
               repositoryName:
                 - my-image-repo
               imageTag:
                 - latest
         Targets:
           -
             Arn: 
               !Join [ '', [ 'arn:aws:codepipeline:', !Ref 'AWS::Region', ':', !Ref 'AWS::AccountId', ':', !Ref AppPipeline ] ]
             RoleArn: !GetAtt AmazonCloudWatchEventRole.Arn
             Id: codepipeline-AppPipeline
   ```

------
#### [ JSON ]

   ```
   {
       "AmazonCloudWatchEventRule": {
           "Type": "AWS::Events::Rule",
           "Properties": {
               "EventPattern": {
                   "source": [
                       "aws.ecr"
                   ],
                   "detail": {
                       "eventName": [
                           "PutImage"
                       ],
                       "requestParameters": {
                           "repositoryName": [ "my-image-repo" ],
                           "imageTag": [ "latest" ]
                       }
                   }
               },
               "Targets": [
                   {
                       "Arn": {
                           "Fn::Join": [
                               "",
                               [
                                   "arn:aws:codepipeline:",
                                   {
                                       "Ref": "AWS::Region"
                                   },
                                   ":",
                                   {
                                       "Ref": "AWS::AccountId"
                                   },
                                   ":",
                                   {
                                       "Ref": "AppPipeline"
                                   }
                               ]
                           ]
                       },
                       "RoleArn": {
                           "Fn::GetAtt": [
                               "AmazonCloudWatchEventRole",
                               "Arn"
                           ]
                       },
                       "Id": "codepipeline-AppPipeline"
                   }
               ]
           }
       }
   },
   ```

------

1. Save the updated template to your local computer, and then open the AWS CloudFormation console\.

1. Choose your stack, and then choose **Create Change Set for Current Stack**\. 

1. Upload the template, and then view the changes listed in AWS CloudFormation\. These are the changes to be made to the stack\. You should see your new resources in the list\. 

1. Choose **Execute**\.