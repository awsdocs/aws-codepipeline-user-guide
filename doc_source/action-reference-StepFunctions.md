# AWS Step Functions<a name="action-reference-StepFunctions"></a>

An AWS CodePipeline action that does the following:
+ Starts an AWS Step Functions state machine execution from your pipeline\.
+ Provides an initial state to the state machine through either a property in the action configuration or a file located in a pipeline artifact to be passed as input\.
+ Optionally sets an execution ID prefix for identifying executions originating from the action\.
+ Supports [Standard and Express](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-standard-vs-express.html) state machines\.

## Action type<a name="action-reference-StepFunctions-type"></a>
+ Category: `Invoke`
+ Owner: `AWS`
+ Provider: `StepFunctions`
+ Version: `1`

## Configuration parameters<a name="action-reference-StepFunctions-config"></a>

**StateMachineArn**  
Required: Yes  
The Amazon Resource Name \(ARN\) for the state machine to be invoked\.

**ExecutionNamePrefix**  
Required: No  
By default, the action execution ID is used as the state machine execution name\. If a prefix is provided, it is prepended to the action execution ID with a hyphen and together used as the state machine execution name\.  

```
myPrefix-1624a1d1-3699-43f0-8e1e-6bafd7fde791
```
For an express state machine, the name should only contain 0\-9, A\-Z, a\-z, \- and \_\.

**InputType**  
Required: No  
+ **Literal** \(default\): When specified, the value in the **Input** field is passed directly to the state machine input\.

  Example entry for the **Input** field when **Literal** is selected:

  ```
  {"action": "test"}
  ```
+ **FilePath**: The contents of a file in the input artifact specified by the **Input** field is used as the input for the state machine execution\. An input artifact is required when **InputType** is set to **FilePath**\.

  Example entry for the **Input** field when **FilePath** is selected:

  ```
  assets/input.json
  ```

**Input**  
Required: Conditional  
+ **Literal**: When **InputType** is set to **Literal** \(default\), this field is optional\. 

  If provided, the **Input** field is used directly as the input for the state machine execution\. Otherwise, the state machine is invoked with an empty JSON object `{}`\.
+ **FilePath**: When **InputType** is set to **FilePath**, this field is required\.

  An input artifact is also required when **InputType** is set to **FilePath**\.

  The contents of the file in the input artifact specified are used as the input for the state machine execution\.

## Input artifacts<a name="action-reference-StepFunctions-input"></a>
+ **Number of Artifacts:** `0 to 1`
+ **Description:** If **InputType** is set to **FilePath**, this artifact is required and is used to source the input for the state machine execution\.

## Output artifacts<a name="action-reference-StepFunctions-output"></a>
+ **Number of Artifacts:** `0 to 1` 
+ **Description:**
  + **Standard State Machines**: If provided, the output artifact is populated with the output of the state machine\. This is obtained from the `output` property of the [Step Functions DescribeExecution API](https://docs.aws.amazon.com/step-functions/latest/apireference/API_DescribeExecution.html) response after the state machine execution completes successfully\.
  + **Express State Machines**: Not supported\.

## Output variables<a name="action-reference-StepFunctions-variables"></a>

This action produces output variables that can be referenced by the action configuration of a downstream action in the pipeline\.

For more information, see [Variables](reference-variables.md)\.

**StateMachineArn**  
The ARN of the state machine\.

**ExecutionArn**  
The ARN of the execution of the state machine\. Standard state machines only\.

## Example action configuration<a name="action-reference-StepFunctions-example"></a>

### Example for default input<a name="action-reference-StepFunctions-example-default"></a>

------
#### [ YAML ]

```
Name: ActionName
ActionTypeId:
  Category: Invoke
  Owner: AWS
  Version: 1
  Provider: StepFunctions
OutputArtifacts:
  - Name: myOutputArtifact
Configuration:
  StateMachineArn: arn:aws:states:us-east-1:111122223333:stateMachine:HelloWorld-StateMachine
  ExecutionNamePrefix: my-prefix
```

------
#### [ JSON ]

```
{
    "Name": "ActionName",
    "ActionTypeId": {
        "Category": "Invoke",
        "Owner": "AWS",
        "Version": 1,
        "Provider": "StepFunctions"
    },
    "OutputArtifacts": [
        {
            "Name": "myOutputArtifact"
        }
    ],
    "Configuration": {
        "StateMachineArn": "arn:aws:states:us-east-1:111122223333:stateMachine:HelloWorld-StateMachine",
        "ExecutionNamePrefix": "my-prefix"
    }
}
```

------

### Example for literal input<a name="action-reference-StepFunctions-example-literal"></a>

------
#### [ YAML ]

```
Name: ActionName
ActionTypeId:
  Category: Invoke
  Owner: AWS
  Version: 1
  Provider: StepFunctions
OutputArtifacts:
  - Name: myOutputArtifact
Configuration:
  StateMachineArn: arn:aws:states:us-east-1:111122223333:stateMachine:HelloWorld-StateMachine
  ExecutionNamePrefix: my-prefix
  Input: '{"action": "test"}'
```

------
#### [ JSON ]

```
{
    "Name": "ActionName",
    "ActionTypeId": {
        "Category": "Invoke",
        "Owner": "AWS",
        "Version": 1,
        "Provider": "StepFunctions"
    },
    "OutputArtifacts": [
        {
            "Name": "myOutputArtifact"
        }
    ],
    "Configuration": {
        "StateMachineArn": "arn:aws:states:us-east-1:111122223333:stateMachine:HelloWorld-StateMachine",
        "ExecutionNamePrefix": "my-prefix",
        "Input": "{\"action\": \"test\"}"
    }
}
```

------

### Example for input file<a name="action-reference-StepFunctions-example-filepath"></a>

------
#### [ YAML ]

```
Name: ActionName
InputArtifacts:
  - Name: myInputArtifact
ActionTypeId:
  Category: Invoke
  Owner: AWS
  Version: 1
  Provider: StepFunctions
OutputArtifacts:
  - Name: myOutputArtifact
Configuration:
  StateMachineArn: 'arn:aws:states:us-east-1:111122223333:stateMachine:HelloWorld-StateMachine'
  ExecutionNamePrefix: my-prefix
  InputType: FilePath
  Input: assets/input.json
```

------
#### [ JSON ]

```
{
    "Name": "ActionName",
    "InputArtifacts": [
        {
            "Name": "myInputArtifact"
        }
    ],
    "ActionTypeId": {
        "Category": "Invoke",
        "Owner": "AWS",
        "Version": 1,
        "Provider": "StepFunctions"
    },
    "OutputArtifacts": [
        {
            "Name": "myOutputArtifact"
        }
    ],
    "Configuration": {
        "StateMachineArn": "arn:aws:states:us-east-1:111122223333:stateMachine:HelloWorld-StateMachine",
        "ExecutionNamePrefix": "my-prefix",
        "InputType": "FilePath",
        "Input": "assets/input.json"
    }
}
```

------

## Behavior<a name="action-reference-StepFunctions-types"></a>

During a release, CodePipeline executes the configured state machine using the input as specified in the action configuration\.

When **InputType** is set to **Literal**, the content of the **Input** action configuration field is used as the input for the state machine\. When literal input is not provided, the state machine execution uses an empty JSON object `{}`\. For more information about running a state machine execution without input, see the [Step Functions StartExecution API](https://docs.aws.amazon.com/step-functions/latest/apireference/API_StartExecution.html)\.

When **InputType** is set to **FilePath**, the action unzips the input artifact and uses the content of the file specified in the **Input** action configuration field as the input for the state machine\. When **FilePath** is specified, the **Input** field is required and an input artifact must exist; otherwise, the action fails\.

After a successful start execution, behavior will diverge for the two state machine types, *standard* and *express*\.

### Standard state machines<a name="action-reference-StepFunctions-types-standard"></a>

If the standard state machine execution was successfully started, CodePipeline polls the `DescribeExecution` API until the execution reaches a terminal status\. If the execution completes successfully, the action succeeds; otherwise, it fails\.

If an output artifact is configured, the artifact will contain the return value of the state machine\. This is obtained from the `output` property of the [Step Functions DescribeExecution API](https://docs.aws.amazon.com/step-functions/latest/apireference/API_DescribeExecution.html) response after the state machine execution completes successfully\. Note that there are output length constraints enforced on this API\.

#### Error handling<a name="action-reference-StepFunctions-types-standard-handling"></a>
+ If the action fails to start a state machine execution, the action execution fails\.
+ If the state machine execution fails to reach a terminal status before the CodePipeline Step Functions action reaches its timeout \(default of 7 days\), the action execution fails\. The state machine might continue despite this failure\. For more information about state machine execution timeouts in Step Functions, see [Standard vs\. Express Workflows](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-standard-vs-express.html)\.
**Note**  
You can request a quota increase for the invoke action timeout for the account with the action\. However, the quota increase applies to all actions of this type in all Regions for that account\.
+ If the state machine execution reaches a terminal status of FAILED, TIMED\_OUT, or ABORTED, the action execution fails\.

### Express state machines<a name="action-reference-StepFunctions-types-express"></a>

If the express state machine execution was successfully started, the invoke action execution completes successfully\.

Considerations for actions configured for express state machines:
+ You cannot designate an output artifact\.
+ The action does not wait for the state machine execution to complete\.
+ After the action execution is started in CodePipeline, the action execution succeeds even if the state machine execution fails\.

#### Error handling<a name="action-reference-StepFunctions-types-express-handling"></a>
+ If CodePipeline fails to start a state machine execution, the action execution fails\. Otherwise, the action succeeds immediately\. The action succeeds in CodePipeline regardless of how long the state machine execution takes to complete or its outcome\.

## See also<a name="action-reference-StepFunctions-links"></a>

The following related resources can help you as you work with this action\.
+ [AWS Step Functions Developer Guide](https://docs.aws.amazon.com/step-functions/latest/dg/) – For information about state machines, executions, and inputs for state machines, see the *AWS Step Functions Developer Guide*\.
+ [Tutorial: Use an AWS Step Functions invoke action in a pipeline](tutorials-step-functions.md) – This tutorial gets you started with a sample standard state machine and shows you how to use the console to update a pipeline by adding a Step Functions invoke action\.