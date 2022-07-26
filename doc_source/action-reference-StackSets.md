# AWS CloudFormation StackSets<a name="action-reference-StackSets"></a>

CodePipeline offers the ability to perform AWS CloudFormation StackSets operations as part of your CI/CD process\. You use a stack set to create stacks in AWS accounts across AWS Regions by using a single AWS CloudFormation template\. All the resources included in each stack are defined by the stack set’s AWS CloudFormation template\. When you create the stack set, you specify the template to use, as well as any parameters and capabilities that the template requires\.

**Note**  
You must use the AWS Organizations management account to deploy with AWS CloudFormation StackSets\. You cannot use a delegated administration account for this action\.

For more information about concepts for AWS CloudFormation StackSets, see [StackSets concepts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html) in the *AWS CloudFormation User Guide*\.

You integrate your pipeline with AWS CloudFormation StackSets through two distinct action types that you use together:
+ The `CloudFormationStackSet` action creates or updates a stack set or stack instances from the template stored in the pipeline source location\. Each time a stack set is created or updated, it initiates a deployment of those changes to specified instances\. In the console, you can choose the **CloudFormation Stack Set** action provider when you create or edit your pipeline\.
+ The `CloudFormationStackInstances` action deploys changes from the `CloudFormationStackSet` action to specified instances, creates new stack instances, and defines parameter overrides to specified instances\. In the console, you can choose the **CloudFormation Stack Instances** action provider when you edit an existing pipeline\.

**Note**  
This feature is not available in the Asia Pacific \(Hong Kong\) and Europe \(Milan\) Regions\. To reference other available actions, see [Product and service integrations with CodePipeline](integrations.md)\.

You can use these actions to deploy to target AWS accounts or target AWS Organizations organizational unit IDs\.

**Note**  
To deploy to target AWS Organizations accounts or organizational unit IDs and use the service\-managed permissions model, you must enable trusted access between AWS CloudFormation StackSets and AWS Organizations\. For more information, see [Enabling trusted access with AWS CloudFormation Stacksets](https://docs.aws.amazon.com/organizations/latest/userguide/services-that-can-integrate-cloudformation.html#integrate-enable-ta-cloudformation)\.

**Topics**
+ [How AWS CloudFormation StackSets actions work](#action-reference-StackSets-concepts)
+ [How to structure StackSets actions in a pipeline](#action-reference-StackSets-bestpractices)
+ [The `CloudFormationStackSet` action](#action-reference-StackSet)
+ [The CloudFormationStackInstances action](#action-reference-StackInstances)
+ [Permissions models for stack set operations](#action-reference-StackSets-permissions)
+ [Template parameter data types](#action-reference-StackSets-datatypes)
+ [See also](#action-reference-CloudFormation-links)

## How AWS CloudFormation StackSets actions work<a name="action-reference-StackSets-concepts"></a>

A `CloudFormationStackSet` action creates or updates resources depending on whether the action is running for the first time\.

The `CloudFormationStackSet` action *creates* or *updates* the stack set and deploys those changes to specified instances\.

**Note**  
If you use this action to make an update that includes adding stack instances, the new instances are deployed first and the update is completed last\. The new instances first receive the old version, and then the update is applied to all instances\.
+ *Create*: When no instances are specified and the stack set does not exist, the **CloudFormationStackSet** action creates the stack set without creating any instances\.
+ *Update*: When the **CloudFormationStackSet** action is run for a stack set that is already created, the action updates the stack set\. If no instances are specified and the stack set already exists, all instances are updated\. If this action is used to update specific instances, all remaining instances move to an OUTDATED status\.

  You can use the **CloudFormationStackSet** action to update the stack set in the following ways\. 
  + Update the template on some or all instances\.
  + Update parameters on some or all instances\.
  + Update the execution role for the stack set \(this must match the execution role specified in the Administrator role\)\.
  + Change the permissions model \(only if no instances have been created\)\.
  + Enable/Disable `AutoDeployment` if the stack set permissions model is `Service Managed`\.
  + Update the Administrator role\.
  + Update the description on the stack set\.
  + Add deployment targets to the stack set update to create new stack instances\.

The `CloudFormationStackInstances` action creates new stack instances or updates outdated stack instances\. An instance becomes outdated when a stack set is updated, but not all instances within it are updated\.
+ *Create*: If the stack already exists, the `CloudFormationStackInstances` action only updates instances and does not create stack instances\.
+ *Update*: After the `CloudFormationStackSet` action is performed, if the template or parameters have been updated in only some instances, the rest will be marked `OUTDATED`\. In later pipeline stages, `CloudFormationStackInstances` updates the rest of the instances in the stack set in waves so that all instances are marked `CURRENT`\. This action can also be used to add additional instances or override parameters on new or existing instances\.

As part of an update, the `CloudFormationStackSet` and `CloudFormationStackInstances` actions can specify new deployment targets, which creates new stack instances\.

As part of an update, the `CloudFormationStackSet` and `CloudFormationStackInstances` actions do not delete stack sets, instances, or resources\. When the action updates a stack but does not specify all instances to be updated, the instances that were not specified for update are removed from the update and set to a status of `OUTDATED`\.

During a deployment, stack instances can also show a status of `OUTDATED` if the deployment to instances failed\.

## How to structure StackSets actions in a pipeline<a name="action-reference-StackSets-bestpractices"></a>

As a best practice, you should construct your pipeline so that the stack set is created and initially deploys to a subset or a single instance\. After you test your deployment and view the generated stack set, then add the `CloudFormationStackInstances` action so that the remaining instances are created and updated\.

Use the console or the CLI to create the recommended pipeline structure as follows:

1. Create a pipeline with a source action \(required\) and the `CloudFormationStackSet` action as the deploy action\. Run your pipeline\.

1. When your pipeline first runs, the `CloudFormationStackSet` action *creates* your stack set and at least one initial instance\. Verify the stack set creation and review the deployment to your initial instance\. For example, for initial stack set creation for account Account\-A where `us-east-1` is the specified Region, the stack instance is created with the stack set:  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-StackSets.html)

1. Edit your pipeline to add `CloudFormationStackInstances` as the second deployment action to create/update stack instances for the targets you designate\. For example, for stack instance creation for account `Account-A` where the `us-east-2` and `eu-central-1` Regions are specified, the remaining stack instances are created and the initial instance remains updated as follows:  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-StackSets.html)

1. Run your pipeline as needed to update your stack set and update or create stack instances\.

When you initiate a stack update where you have removed deployment targets from the action configuration, then the stack instances that were not designated for update are removed from the deployment and move into an OUTDATED status\. For example, for stack instance update for account `Account-A` where the `us-east-2` Region is removed from the action configuration, the remaining stack instances are created and the removed instance is set to OUTDATED as follows:


****  

| Stack instance | Region | Status | 
| --- | --- | --- | 
| StackInstanceID\-1 | us\-east\-1 | CURRENT | 
| StackInstanceID\-2 | us\-east\-2 | OUTDATED | 
| StackInstanceID\-3 | eu\-central\-1 | CURRENT | 

For more information about best practices for deploying stack sets, see [Best practices](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-bestpractices.html) for StackSets in the *AWS CloudFormation User Guide*\.

## The `CloudFormationStackSet` action<a name="action-reference-StackSet"></a>

This action creates or updates a stack set from the template stored in the pipeline source location\. 

After you define a stack set, you can create, update, or delete stacks in the target accounts and Regions specified in the configuration parameters\. When creating, updating and deleting stacks, you can specify other preferences, such as the order of Regions for operations to be performed, the failure tolerance percentage beyond which stack operations stop, and the number of accounts in which operations are performed on stacks concurrently\.

A stack set is a regional resource\. If you create a stack set in one AWS Region, you cannot access it from other Regions\.

When this action is used as an update action to the stack set, updates to the stack are not allowed without a deployment to at least one stack instance\.

**Topics**
+ [Action type](#action-reference-StackSet-type)
+ [Configuration parameters](#action-reference-StackSet-config)
+ [Input artifacts](#action-reference-StackSet-input)
+ [Output artifacts](#action-reference-StackSet-output)
+ [Output variables](#action-reference-StackSet-variables)
+ [Example **CloudFormationStackSet** action configuration](#action-reference-StackSet-example)

### Action type<a name="action-reference-StackSet-type"></a>
+ Category: `Deploy`
+ Owner: `AWS`
+ Provider: `CloudFormationStackSet`
+ Version: `1`

### Configuration parameters<a name="action-reference-StackSet-config"></a>

**StackSetName**  
Required: Yes  
The name to associate with the stack set\. This name must be unique in the Region where it is created\.  
The name may only contain alphanumeric and hyphen characters\. It must begin with an alphabetic character and be 128 characters or fewer\.

**Description**  
Required: No  
A description of the stack set\. You can use this to describe the stack set’s purpose or other relevant information\.

**TemplatePath**  
Required: Yes  
The location of the template that defines the resources in the stack set\. This must point to a template with a maximum size of 460,800 bytes\.  
Enter the path to the source artifact name and template file in the format `"InputArtifactName::TemplateFileName"`, as shown in the following example\.  

```
SourceArtifact::template.txt
```

**Parameters**  
Required: No  
A list of template parameters for your stack set that update during a deployment\.  
You can provide parameters as a literal list or a file path:  
+ You can enter parameters in the following shorthand syntax format: `ParameterKey=string,ParameterValue=string,UsePreviousValue=boolean,ResolvedValue=string ParameterKey=string,ParameterValue=string,UsePreviousValue=boolean,ResolvedValue=string`\. For more information about these data types, see [Template parameter data types](#action-reference-StackSets-datatypes)\.

  The following example shows a parameter named `BucketName` with the value my\-`bucket`\.

  ```
  ParameterKey=BucketName,ParameterValue=my-bucket
  ```

  The following example shows an entry with multiple parameters:

  ```
                                                                                                        
    ParameterKey=BucketName,ParameterValue=my-bucket
    ParameterKey=Asset1,ParameterValue=true              
    ParameterKey=Asset2,ParameterValue=true",
  ```
+ You can enter the location of the file containing a list of template parameter overrides entered in the format `"InputArtifactName::ParametersFileName"`, as shown in the following example\.

  ```
  SourceArtifact::parameters.txt
  ```

  The following example shows the file contents for `parameters.txt`\.

  ```
  [
      {
          "ParameterKey": "KeyName",
          "ParameterValue": "true"
      },
      {
          "ParameterKey": "KeyName",
          "ParameterValue": "true"
      }
  ]
  ```

**Capabilities**  
Required: No  
Indicates that the template can create and update resources, depending on the types of resources in the template\.  
You must use this property if you have IAM resources in your stack template or you create a stack directly from a template containing macros\. For the AWS CloudFormation action to successfully operate in this way, you must use one of the following capabilities:  
+ `CAPABILITY_IAM` 
+ `CAPABILITY_NAMED_IAM` 
 You can specify more than one capability by using a comma and no spaces between capabilities\. The example in [Example **CloudFormationStackSet** action configuration](#action-reference-StackSet-example) shows an entry with multiple capabilities\.

**PermissionModel**  
Required: No  
Determines how IAM roles are created and managed\. If the field is not specified, the default is used\. For information, see [Permissions models for stack set operations](#action-reference-StackSets-permissions)\.  
Valid values are:   
+ `SELF_MANAGED` \(default\): You must create administrator and execution roles to deploy to target accounts\.
+ `SERVICE_MANAGED`: AWS CloudFormation StackSets automatically creates the IAM roles required to deploy to accounts managed by AWS Organizations\. This requires an account to be a member of an Organization\.
This parameter can only be changed when no stack instances exist in the stack set\.

****AdministrationRoleArn****  
Because AWS CloudFormation StackSets performs operations across multiple accounts, you must define the necessary permissions in those accounts before you can create the stack set\.
Required: No  
This parameter is optional for the SELF\_MANAGED permissions model and is not used for the SERVICE\_MANAGED permissions model\.
The ARN of the IAM role in the administrator account used to perform stack set operations\.  
The name may contain alphanumeric characters, any of the following characters: \_\+=,\.@\-, and no spaces\. The name is not case sensitive\. This role name must be a minimum length of 20 characters and maximum length of 2048 characters\. Role names must be unique within the account\. The role name specified here must be an existing role name\. If you do not specify the role name, it is set to AWSCloudFormationStackSetAdministrationRole\. If you specify ServiceManaged, you must not define a role name\.

****ExecutionRoleName****  
Because AWS CloudFormation StackSets performs operations across multiple accounts, you must define the necessary permissions in those accounts before you can create the stack set\.
Required: No  
This parameter is optional for the SELF\_MANAGED permissions model and is not used for the SERVICE\_MANAGED permissions model\.
The name of the IAM role in the target accounts used to perform stack set operations\. The name may contain alphanumeric characters, any of the following characters: \_\+=,\.@\-, and no spaces\. The name is not case sensitive\. This role name must be a minimum length of 1 character and maximum length of 64 characters\. Role names must be unique within the account\. The role name specified here must be an existing role name\. Do not specify this role if you are using customized execution roles\. If you do not specify the role name, it is set to `AWSCloudFormationStackSetExecutionRole`\. If you set Service\_Managed to true, you must not define a role name\.

****OrganizationsAutoDeployment****  
Required: No  
This parameter is optional for the SERVICE\_MANAGED permissions model and is not used for the SELF\_MANAGED permissions model\.
Describes whether AWS CloudFormation StackSets automatically deploys to AWS Organizations accounts that are added to a target organization or organizational unit \(OU\)\. If `OrganizationsAutoDeployment` is specified, do not specify `DeploymentTargets` and `Regions`\.   
If no input is provided for `OrganizationsAutoDeployment`, then the default value is `Disabled`\.
Valid values are:  
+ `Enabled`\. Required: No\. 

  StackSets automatically deploys additional stack instances to AWS Organizations accounts that are added to a target organization or organizational unit \(OU\) in the specified Regions\. If an account is removed from a target organization or OU, AWS CloudFormation StackSets deletes stack instances from the account in the specified Regions\.
+ `Disabled`\. Required: No\. 

  StackSets does not automatically deploy additional stack instances to AWS Organizations accounts that are added to a target organization or organizational unit \(OU\) in the specified Regions\.
+ `EnabledWithStackRetention`\. Required: No\.

  Stack resources are retained when an account is removed from a target organization or OU\.

****DeploymentTargets****  
Required: No  
For the SERVICE\_MANAGED permissions model, you can provide either the organization root ID or organizational Unit IDs for deployment targets\. For the SELF\_MANAGED permissions model, you can only provide accounts\.
When this parameter is selected, you must also select **Regions**\.
A list of AWS accounts or organizational unit IDs where stack set instances should be created/updated\.  
+ **Accounts**:

  You can provide accounts as a literal list or a file path:
  + *Literal*: Enter parameters in the shorthand syntax format `account_ID,account_ID`, as shown in the following example\.

    ```
    111111222222,333333444444
    ```
  + *File path: * The location of the file containing a list of AWS accounts where stack set instances should be created/updated, entered in the format `InputArtifactName::AccountsFileName`, as shown in the following example\.

    ```
    SourceArtifact::accounts.txt
    ```

    The following example shows the file contents for `accounts.txt`\.

    ```
    [
        "111111222222"
    ]
    ```
+ **OrganizationalUnitIds**: 
**Note**  
This parameter is optional for the SERVICE\_MANAGED permissions model and is not used for the SELF\_MANAGED permissions model\. Do not use this if you select **OrganizationsAutoDeployment**\.

  The AWS organizational units in which to update associated stack instances\.

  You can provide organizational unit IDs as a literal list or a file path:
  + *Literal*: Enter an array of strings separated by commas, as shown in the following example\.

    ```
    ou-examplerootid111-exampleouid111,ou-examplerootid222-exampleouid222
    ```
  + *File path: * The location of the file containing a list of OrganizationalUnitIds in which to create or update stack set instances\.

    Enter a path to the file in the format `InputArtifactName::OrganizationalUnitIdsFileName`\.

    ```
    SourceArtifact::OU-IDs.txt
    ```

****Regions****  
Required: No  
When this parameter is selected, you must also select **DeploymentTargets**\.
A list of AWS Regions where stack set instances are created or updated\. Regions are updated in the order in which they are entered\.  
Enter a list of valid AWS Regions in the format `Region1,Region2`, as shown in the following example\.  

```
us-west-2,us-east-1
```

****FailureTolerancePercentage****  
Required: No  
The percentage of accounts per Region for which this stack operation can fail before AWS CloudFormation stops the operation in that Region\. If the operation is stopped in a Region, AWS CloudFormation doesn't attempt the operation in subsequent Regions\. When calculating the number of accounts based on the specified percentage, AWS CloudFormation rounds *down* to the next whole number\.

****MaxConcurrentPercentage****  
Required: No  
The maximum percentage of accounts in which to perform this operation at one time\. When calculating the number of accounts based on the specified percentage, AWS CloudFormation rounds *down* to the next whole number\. If rounding down would result in zero, AWS CloudFormation sets the number as one instead\. Although you use this setting to specify the *maximum*, for large deployments the actual number of accounts acted upon concurrently may be lower due to service throttling\.

### Input artifacts<a name="action-reference-StackSet-input"></a>

You must include at least one input artifact that contains the template for the stack set in a `CloudFormationStackSet` action\. You can include more input artifacts for lists of deployment targets, accounts, and parameters\.
+ **Number of artifacts:** `1 to 3`
+ **Description:** You can include artifacts to provide:
  + The stack template file\. \(See the `TemplatePath` parameter\.\)
  + The parameters file\. \(See the `Parameters` parameter\.\)
  + The accounts file\. \(See the `DeploymentTargets` parameter\.\)

### Output artifacts<a name="action-reference-StackSet-output"></a>
+ **Number of artifacts:** `0` 
+ **Description:** Output artifacts do not apply for this action type\.

### Output variables<a name="action-reference-StackSet-variables"></a>

If you configure this action, it produces variables that can be referenced by the action configuration of a downstream action in the pipeline\. You configure an action with a namespace to make those variables available to the configuration of downstream actions\.
+ **StackSetId**: The ID of the stack set\.
+ **OperationId**: The ID of the stack set operation\.

For more information, see [Variables](reference-variables.md)\.

### Example **CloudFormationStackSet** action configuration<a name="action-reference-StackSet-example"></a>

The following examples show the action configuration for the **CloudFormationStackSet** action\.

#### Example for the self\-managed permissions model<a name="action-reference-StackSet-example-selfmanaged"></a>

The following example shows a **CloudFormationStackSet** action where the deployment target entered is an AWS account ID\.

------
#### [ YAML ]

```
Name: CreateStackSet
ActionTypeId:
  Category: Deploy
  Owner: AWS
  Provider: CloudFormationStackSet
  Version: '1'
RunOrder: 1
Configuration:
  DeploymentTargets: '111111222222'
  FailureTolerancePercentage: '20'
  MaxConcurrentPercentage: '25'
  PermissionModel: SELF_MANAGED
  Regions: us-east-1
  StackSetName: my-stackset
  TemplatePath: 'SourceArtifact::template.json'
OutputArtifacts: []
InputArtifacts:
  - Name: SourceArtifact
Region: us-west-2
Namespace: DeployVariables
```

------
#### [ JSON ]

```
{
    "Name": "CreateStackSet",
    "ActionTypeId": {
        "Category": "Deploy",
        "Owner": "AWS",
        "Provider": "CloudFormationStackSet",
        "Version": "1"
    },
    "RunOrder": 1,
    "Configuration": {
        "DeploymentTargets": "111111222222",
        "FailureTolerancePercentage": "20",
        "MaxConcurrentPercentage": "25",
        "PermissionModel": "SELF_MANAGED",
        "Regions": "us-east-1",
        "StackSetName": "my-stackset",
        "TemplatePath": "SourceArtifact::template.json"
    },
    "OutputArtifacts": [],
    "InputArtifacts": [
        {
            "Name": "SourceArtifact"
        }
    ],
    "Region": "us-west-2",
    "Namespace": "DeployVariables"
}
```

------

#### Example for the service\-managed permissions model<a name="action-reference-StackSet-example-servicemanaged"></a>

The following example shows a **CloudFormationStackSet** action for the service\-managed permissions model where the option for auto deployment to AWS Organizations is enabled with stack retention\.

------
#### [ YAML ]

```
Name: Deploy
ActionTypeId:
  Category: Deploy
  Owner: AWS
  Provider: CloudFormationStackSet
  Version: '1'
RunOrder: 1
Configuration:
  Capabilities: 'CAPABILITY_IAM,CAPABILITY_NAMED_IAM'
  OrganizationsAutoDeployment: EnabledWithStackRetention
  PermissionModel: SERVICE_MANAGED
  StackSetName: stacks-orgs
  TemplatePath: 'SourceArtifact::template.json'
OutputArtifacts: []
InputArtifacts:
  - Name: SourceArtifact
Region: eu-central-1
Namespace: DeployVariables
```

------
#### [ JSON ]

```
{
    "Name": "Deploy",
    "ActionTypeId": {
        "Category": "Deploy",
        "Owner": "AWS",
        "Provider": "CloudFormationStackSet",
        "Version": "1"
    },
    "RunOrder": 1,
    "Configuration": {
        "Capabilities": "CAPABILITY_IAM,CAPABILITY_NAMED_IAM",
        "OrganizationsAutoDeployment": "EnabledWithStackRetention",
        "PermissionModel": "SERVICE_MANAGED",
        "StackSetName": "stacks-orgs",
        "TemplatePath": "SourceArtifact::template.json"
    },
    "OutputArtifacts": [],
    "InputArtifacts": [
        {
            "Name": "SourceArtifact"
        }
    ],
    "Region": "eu-central-1",
    "Namespace": "DeployVariables"
}
```

------

## The CloudFormationStackInstances action<a name="action-reference-StackInstances"></a>

This action creates new instances and deploys stack sets to specified instances\. A stack instance is a reference to a stack in a target account within a Region\. A stack instance can exist without a stack; for example, if the stack creation is not successful, the stack instance shows the reason for stack creation failure\. A stack instance is associated with only one stack set\.

After the initial creation of a stack set, you can add new stack instances by using `CloudFormationStackInstances`\. Template parameter values can be overridden at the stack instance level during create or update stack set instance operations\.

Each stack set has one template and set of template parameters\. When you update the template or template parameters, you update them for the entire set\. Then all instance statuses are set to `OUTDATED` until the changes are deployed to that instance\.

To override parameter values on specific instances, for example, if the template contains a parameter for `stage` with a value of `prod`, you can override the value of that parameter to be `beta` or `gamma`\.

**Topics**
+ [Action type](#action-reference-StackInstances-type)
+ [Configuration parameters](#action-reference-StackInstances-config)
+ [Input artifacts](#action-reference-StackInstances-input)
+ [Output artifacts](#action-reference-StackInstances-output)
+ [Output variables](#action-reference-StackInstances-variables)
+ [Example action configuration](#action-reference-StackInstances-example)

### Action type<a name="action-reference-StackInstances-type"></a>
+ Category: `Deploy`
+ Owner: `AWS`
+ Provider: `CloudFormationStackInstances`
+ Version: `1`

### Configuration parameters<a name="action-reference-StackInstances-config"></a>

**StackSetName**  
Required: Yes  
The name to associate with the stack set\. This name must be unique in the Region where it is created\.  
The name may only contain alphanumeric and hyphen characters\. It must begin with an alphabetic character and be 128 characters or fewer\.

****DeploymentTargets****  
Required: No  
For the SERVICE\_MANAGED permissions model, you can provide either the organization root ID or organizational Unit IDs for deployment targets\. For the SELF\_MANAGED permissions model, you can only provide accounts\.
When this parameter is selected, you must also select **Regions**\.
A list of AWS accounts or organizational unit IDs where stack set instances should be created/updated\.  
+ **Accounts**:

  You can provide accounts as a literal list or a file path:
  + *Literal*: Enter parameters in the shorthand syntax format `account_ID,account_ID`, as shown in the following example\.

    ```
    111111222222,333333444444
    ```
  + *File path: * The location of the file containing a list of AWS accounts where stack set instances should be created/updated, entered in the format `InputArtifactName::AccountsFileName`, as shown in the following example\.

    ```
    SourceArtifact::accounts.txt
    ```

    The following example shows the file contents for `accounts.txt`:

    ```
    [
        "111111222222"
    ]
    ```
+ **OrganizationalUnitIds**: 
**Note**  
This parameter is optional for the SERVICE\_MANAGED permissions model and is not used for the SELF\_MANAGED permissions model\. Do not use this if you select **OrganizationsAutoDeployment**\.

  The AWS organizational units in which to update associated stack instances\.

  You can provide organizational unit IDs as a literal list or a file path:
  + *Literal*: Enter an array of strings separated by commas, as shown in the following example\.

    ```
    ou-examplerootid111-exampleouid111,ou-examplerootid222-exampleouid222
    ```
  + *File path: * The location of the file containing a list of OrganizationalUnitIds in which to create or update stack set instances\.

    Enter a path to the file in the format `InputArtifactName::OrganizationalUnitIdsFileName`\.

    ```
    SourceArtifact::OU-IDs.txt
    ```

****Regions****  
Required: Yes  
When this parameter is selected, you must also select **DeploymentTargets**\.
A list of AWS Regions where stack set instances are created or updated\. Regions are updated in the order in which they are entered\.  
Enter a list of valid AWS Regions in the format: `Region1,Region2`, as shown in the following example\.  

```
us-west-2,us-east-1
```

**ParameterOverrides**  
Required: No  
A list of stack set parameters that you want to override in the selected stack instances\. Overridden parameter values are applied to all stack instances in the specified accounts and Regions\.  
You can provide parameters as a literal list or a file path:  
+ You can enter parameters in the following shorthand syntax format: `ParameterKey=string,ParameterValue=string,UsePreviousValue=boolean,ResolvedValue=string ParameterKey=string,ParameterValue=string,UsePreviousValue=boolean,ResolvedValue=string`\. For more information about these data types, see [Template parameter data types](#action-reference-StackSets-datatypes)\.

  The following example shows a parameter named `BucketName` with the value my\-`bucket`\.

  ```
  ParameterKey=BucketName,ParameterValue=my-bucket
  ```

  The following example shows an entry with multiple parameters\.

  ```
                                                                                                        
    ParameterKey=BucketName,ParameterValue=my-bucket
    ParameterKey=Asset1,ParameterValue=true              
    ParameterKey=Asset2,ParameterValue=true
  ```
+ You can enter the location of the file containing a list of template parameter overrides entered in the format `InputArtifactName::ParameterOverridessFileName`, as shown in the following example\.

  ```
  SourceArtifact::parameter-overrides.txt
  ```

  The following example shows the file contents for `parameter-overrides.txt`\.

  ```
  [
      {
          "ParameterKey": "KeyName",
          "ParameterValue": "true"
      },
      {
          "ParameterKey": "KeyName",
          "ParameterValue": "true"
      }
  ]
  ```

****FailureTolerancePercentage****  
Required: No  
The percentage of accounts per Region for which this stack operation can fail before AWS CloudFormation stops the operation in that Region\. If the operation is stopped in a Region, AWS CloudFormation doesn't attempt the operation in subsequent Regions\. When calculating the number of accounts based on the specified percentage, AWS CloudFormation rounds *down* to the next whole number\.

****MaxConcurrentPercentage****  
Required: No  
The maximum percentage of accounts on which to perform this operation at one time\. When calculating the number of accounts based on the specified percentage, AWS CloudFormation rounds *down* to the next whole number\. If rounding down would result in zero, AWS CloudFormation sets the number as one instead\. Although you specify the *maximum*, for large deployments the actual number of accounts acted upon concurrently may be lower due to service throttling\.

### Input artifacts<a name="action-reference-StackInstances-input"></a>

`CloudFormationStackInstances` can contain artifacts that list deployment targets and parameters\.
+ **Number of artifacts:** `0 to 2`
+ **Description:** As input, the stack set action optionally accepts artifacts for these purposes:
  + To provide the parameters file to use\. \(See the `ParameterOverrides` parameter\.\)
  + To provide the target accounts file to use\. \(See the `DeploymentTargets` parameter\.\)

### Output artifacts<a name="action-reference-StackInstances-output"></a>
+ **Number of artifacts:** `0` 
+ **Description:** Output artifacts do not apply for this action type\.

### Output variables<a name="action-reference-StackInstances-variables"></a>

When configured, this action produces variables that can be referenced by the action configuration of a downstream action in the pipeline\. You configure an action with a namespace to make those variables available to the configuration of downstream actions\.
+ **StackSetId**: The ID of the stack set\.
+ **OperationId**: The ID of the stack set operation\.

For more information, see [Variables](reference-variables.md)\.

### Example action configuration<a name="action-reference-StackInstances-example"></a>

The following examples show the action configuration for the **CloudFormationStackInstances** action\.

#### Example for the self\-managed permissions model<a name="action-reference-StackInstances-example-selfmanaged"></a>

The following example shows a **CloudFormationStackInstances** action where the deployment target entered is an AWS account ID `111111222222`\.

------
#### [ YAML ]

```
Name: my-instances
ActionTypeId:
  Category: Deploy
  Owner: AWS
  Provider: CloudFormationStackInstances
  Version: '1'
RunOrder: 2
Configuration:
  DeploymentTargets: '111111222222'
  Regions: 'us-east-1,us-east-2,us-west-1,us-west-2'
  StackSetName: my-stackset
OutputArtifacts: []
InputArtifacts:
  - Name: SourceArtifact
Region: us-west-2
```

------
#### [ JSON ]

```
{
    "Name": "my-instances",
    "ActionTypeId": {
        "Category": "Deploy",
        "Owner": "AWS",
        "Provider": "CloudFormationStackInstances",
        "Version": "1"
    },
    "RunOrder": 2,
    "Configuration": {
        "DeploymentTargets": "111111222222",
        "Regions": "us-east-1,us-east-2,us-west-1,us-west-2",
        "StackSetName": "my-stackset"
    },
    "OutputArtifacts": [],
    "InputArtifacts": [
        {
            "Name": "SourceArtifact"
        }
    ],
    "Region": "us-west-2"
}
```

------

#### Example for the service\-managed permissions model<a name="action-reference-StackInstances-example-servicemanaged"></a>

The following example shows a **CloudFormationStackInstances** action for the service\-managed permissions model where the deployment target is an AWS Organizations organizational unit ID `ou-1111-1example`\.

------
#### [ YAML ]

```
Name: Instances
ActionTypeId:
  Category: Deploy
  Owner: AWS
  Provider: CloudFormationStackInstances
  Version: '1'
RunOrder: 2
Configuration:
  DeploymentTargets: ou-1111-1example
  Regions: us-east-1
  StackSetName: my-stackset
OutputArtifacts: []
InputArtifacts:
  - Name: SourceArtifact
Region: eu-central-1
```

------
#### [ JSON ]

```
{
    "Name": "Instances",
    "ActionTypeId": {
        "Category": "Deploy",
        "Owner": "AWS",
        "Provider": "CloudFormationStackInstances",
        "Version": "1"
    },
    "RunOrder": 2,
    "Configuration": {
        "DeploymentTargets": "ou-1111-1example",
        "Regions": "us-east-1",
        "StackSetName": "my-stackset"
    },
    "OutputArtifacts": [],
    "InputArtifacts": [
        {
            "Name": "SourceArtifact"
        }
    ],
    "Region": "eu-central-1"
}
```

------

## Permissions models for stack set operations<a name="action-reference-StackSets-permissions"></a>

Because AWS CloudFormation StackSets performs operations across multiple accounts, you must define the necessary permissions in those accounts before you can create the stack set\. You can define permissions through self\-managed permissions or service\-managed permissions\.

With self\-managed permissions, you create the two IAM roles required by StackSets \- an administrator role such as the AWSCloudFormationStackSetAdministrationRole in the account where you define the stack set and an execution role such as the AWSCloudFormationStackSetExecutionRole in each of the accounts where you deploy stack set instances\. Using this permissions model, StackSets can deploy to any AWS account in which the user has permissions to create an IAM role\. For more information, see [Grant self\-managed permissions](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-prereqs-self-managed.html) in the *AWS CloudFormation User Guide*\.

**Note**  
Because AWS CloudFormation StackSets performs operations across multiple accounts, you must define the necessary permissions in those accounts before you can create the stack set\.

With service\-managed permissions, you can deploy stack instances to accounts managed by AWS Organizations\. Using this permissions model, you don't have to create the necessary IAM roles because StackSets creates the IAM roles on your behalf\. With this model, you can also enable automatic deployments to accounts that are added to the organization in the future\. See [Enable trusted access with AWS Organizations](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-orgs-enable-trusted-access.html) in the *AWS CloudFormation User Guide*\.

## Template parameter data types<a name="action-reference-StackSets-datatypes"></a>

The template parameters used in stack set operations include the following data types\. For more information, see [DescribeStackSet](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/API_Parameter.html)\.

ParameterKey  
+ Description: The key associated with the parameter\. If you don't specify a key and value for a particular parameter, AWS CloudFormation uses the default value that is specified in the template\.
+ Example:

  ```
  "ParameterKey=BucketName,ParameterValue=my-bucket"
  ```

ParameterValue  
+ Description: The input value associated with the parameter\.
+ Example:

  ```
  "ParameterKey=BucketName,ParameterValue=my-bucket"
  ```

UsePreviousValue  
+ During a stack update, use the existing parameter value that the stack is using for a given parameter key\. If you specify `true`, do not specify a parameter value\.
+ Example:

  ```
  "ParameterKey=Asset1,UsePreviousValue=true"
  ```

Each stack set has one template and set of template parameters\. When you update the template or template parameters, you update them for the entire set\. Then all instance statuses are set to OUTDATED until the changes are deployed to that instance\.

To override parameter values on specific instances, for example, if the template contains a parameter for `stage` with a value of `prod`, you can override the value of that parameter to be `beta` or `gamma`\.

## See also<a name="action-reference-CloudFormation-links"></a>

The following related resources can help you as you work with this action\.
+ [Parameter types](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html#parameters-section-structure-properties-type) – This reference chapter in the *AWS CloudFormation User Guide* provides more descriptions and examples for CloudFormation template parameters\.
+ Best practices – For more information about best practices for deploying stack sets, see [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-bestpractices.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-bestpractices.html) in the *AWS CloudFormation User Guide*\.
+ [AWS CloudFormation API Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/) – You can reference the following CloudFormation actions in the *AWS CloudFormation API Reference* for more information about the parameters used in stack set operations:

  
  + The [CreateStackSet](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/API_CreateStackSet.html) action creates a stack set\.
  + The [UpdateStackSet](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/API_UpdateStackSet.html) action updates the stack set and associated stack instances in the specified accounts and Regions\. Even if the stack set operation created by updating the stack set fails \(completely or partially, below or above a specified failure tolerance\), the stack set is updated with these changes\. Subsequent CreateStackInstances calls on the specified stack set use the updated stack set\.
  + The [CreateStackInstances](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/API_CreateStackInstances.html) action creates a stack instance for all specified regions within all specified accounts on a self\-managed permission model, or within all specified deployment targets on a service\-managed permission model\. You can override parameters for the instances created by this action\. If the instances already exist, CreateStackInstances calls UpdateStackInstances with the same input parameters\. When you use this action to create instances, it does not change the status of other stack instances\.
  + The [UpdateStackInstances](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/API_UpdateStackInstances.html) action brings stack instances up to date with the stack set for all specified regions within all specified accounts on a self\-managed permission model, or within all specified deployment targets on a service\-managed permission model\. You can override parameters for the instances updated by this action\. When you use this action to update a subset of instances, it does not change the status of other stack instances\.
  + The [DescribeStackSetOperation](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/API_DescribeStackSetOperation.html) action returns the description of the specified stack set operation\.
  + The [DescribeStackSet](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/API_DescribeStackSet.html) action returns the description of the specified stack set\.