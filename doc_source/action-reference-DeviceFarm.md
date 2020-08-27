# AWS Device Farm<a name="action-reference-DeviceFarm"></a>

In your pipeline, you can configure a test action that uses AWS Device Farm to run and test your application on devices\. Device Farm uses test pools of devices and testing frameworks to test applications on specific devices\. For information about the types of testing frameworks supported by the Device Farm action, see [Working with Test Types in AWS Device Farm](https://docs.aws.amazon.com/devicefarm/latest/developerguide/test-types.html)\.

**Topics**
+ [Action Type](#action-reference-DeviceFarm-type)
+ [Configuration Parameters](#action-reference-DeviceFarm-config)
+ [Input Artifacts](#action-reference-DeviceFarm-input)
+ [Output Artifacts](#action-reference-DeviceFarm-output)
+ [Action Declaration](#action-reference-DeviceFarm-example)
+ [See Also](#action-reference-DeviceFarm-links)

## Action Type<a name="action-reference-DeviceFarm-type"></a>
+ Category: `Test`
+ Owner: `AWS`
+ Provider: `DeviceFarm`
+ Version: `1`

## Configuration Parameters<a name="action-reference-DeviceFarm-config"></a>

**AppType**  
Required: Yes  
The OS and type of application you are testing\. The following is a list of valid values:  
+ `iOS`
+ `Android`
+ `Web`

**ProjectId**  
Required: Yes  
The Device Farm project ID\.   
To find your project ID, in the Device Farm console, choose your project\. In the browser, copy the URL of your new project\. The URL contains the project ID\. The project ID is the value in the URL after `projects/`\. In the following example, the project ID is `eec4905f-98f8-40aa-9afc-4c1cfexample`\.  

```
https://<region-URL>/devicefarm/home?region=us-west-2#/projects/eec4905f-98f8-40aa-9afc-4c1cfexample/runs
```

**App**  
Required: Yes  
The name and location of the application file in your input artifact\. For example: `s3-ios-test-1.ipa`

**AppiumVersion**  
Required: Yes  
The version of Appium test framework to use for this test\.  
The AppiumVersion field value is not processed\. The default value is processed instead\.

**DevicePoolArn**  
Required: Yes  
The Device Farm device pool ARN\.   
To get the available device pool ARNs for the project, including the ARN for Top Devices, use the AWS CLI to enter the following command:   

```
aws devicefarm list-device-pools --arn arn:aws:devicefarm:us-west-2:account_ID:project:project_ID
```

**TestType**  
Required: Yes  
Specifies the supported testing framework for your test\. The following is a list of valid values for `TestType`:  
+ **APPIUM\_JAVA\_JUNIT**
+ **APPIUM\_JAVA\_TESTNG**
+ **APPIUM\_PYTHON**
+ **APPIUM\_WEB\_JAVA\_JUNIT**
+ **APPIUM\_WEB\_JAVA\_TESTNG**
+ **APPIUM\_WEB\_PYTHON**
+ **BUILTIN\_EXPLORER**
+ **BUILTIN\_FUZZ**
+ **CALABASH**
+ **INSTRUMENTATION**
+ **UIAUTOMATION**
+ **UIAUTOMATOR**
+ **WEB\_PERFORMANCE\_PROFILE**
+ **XCTEST**
+ **XCTEST\_UI**
The following test types are not supported by the action in CodePipeline: `WEB_PERFORMANCE_PROFILE`, `APPIUM_NODE`, `APPIUM_RUBY`, `APPIUM_WEB_NODE`, `APPIUM_WEB_RUBY`, `REMOTE_ACCESS_RECORD`, and `REMOTE_ACCESS_REPLAY`\.
For information about Device Farm test types, see [Working with Test Types in AWS Device Farm](https://docs.aws.amazon.com/devicefarm/latest/developerguide/test-types.html)\.

**RadioBluetoothEnabled**  
Required: No  
A Boolean value that indicates whether to enable Bluetooth at the beginning of the test\.

**RecordAppPerformanceData**  
Required: No  
A Boolean value that indicates whether to record device performance data such as CPU, FPS, and memory performance during the test\.

**RecordVideo**  
Required: No  
A Boolean value that indicates whether to record video during the test\.

**RadioWifiEnabled**  
Required: No  
A Boolean value that indicates whether to enable Wi\-Fi at the beginning of the test\.

**RadioNfcEnabled**  
Required: No  
A Boolean value that indicates whether to enable NFC at the beginning of the test\.

**RadioGpsEnabled**  
Required: No  
A Boolean value that indicates whether to enable GPS at the beginning of the test\.

**Test**  
Required: No  
The name and path of the test definition file in your source location\. The path is relative to the root of the input artifact for your test\.

**FuzzEventCount**  
Required: No  
The number of user interface events for the fuzz test to perform, between 1 and 10,000\.

**FuzzEventThrottle**  
Required: No  
The number of milliseconds for the fuzz test to wait before performing the next user interface event, between 1 and 1,000\.

**FuzzRandomizerSeed**  
Required: No  
A seed for the fuzz test to use for randomizing user interface events\. Using the same number for subsequent fuzz tests results in identical event sequences\.

**CustomHostMachineArtifacts**  
Required: No  
The location on the host machine where custom artifacts will be stored\.

**CustomDeviceArtifacts**  
Required: No  
The location on the device where custom artifacts will be stored\.

**UnmeteredDevicesOnly**  
Required: No  
A Boolean value that indicates whether to only use your unmetered devices when running tests in this step\.

**JobTimeoutMinutes**  
Required: No  
The number of minutes a test run will execute per device before it times out\.

**Latitude**  
Required: No  
The latitude of the device expressed in geographic coordinate system degrees\.

**Longitude**  
Required: No  
The longitude of the device expressed in geographic coordinate system degrees\.

## Input Artifacts<a name="action-reference-DeviceFarm-input"></a>
+ **Number of Artifacts:** `1`
+ **Description:** The set of artifacts to be made available to the test action\. Device Farm looks for the built application and test definitions to use\.

## Output Artifacts<a name="action-reference-DeviceFarm-output"></a>
+ **Number of Artifacts:** `0` 
+ **Description:** Output artifacts do not apply for this action type\.

## Action Declaration<a name="action-reference-DeviceFarm-example"></a>

------
#### [ YAML ]

```
Name: Test
Actions:
  - Name: TestDeviceFarm
    ActionTypeId:
      category: Test
      owner: AWS
      provider: DeviceFarm
      version: '1'
    RunOrder: 1
    Configuration:
      App: s3-ios-test-1.ipa
      AppType: iOS
      DevicePoolArn: >-
        arn:aws:devicefarm:us-west-2::devicepool:0EXAMPLE-d7d7-48a5-ba5c-b33d66efa1f5
      FuzzEventCount: '6000'
      FuzzEventThrottle: '50'
      ProjectId: eec4905f-98f8-40aa-9afc-4c1cfEXAMPLE
      TestType: BUILTIN_FUZZ
    OutputArtifacts: []
    InputArtifacts:
      - Name: SourceArtifact
    Region: us-west-2
```

------
#### [ JSON ]

```
{
    "Name": "Test",
    "Actions": [
        {
            "Name": "TestDeviceFarm",
            "ActionTypeId": {
                "category": "Test",
                "owner": "AWS",
                "provider": "DeviceFarm",
                "version": "1"
            },
            "RunOrder": 1,
            "Configuration": {
                "App": "s3-ios-test-1.ipa",
                "AppType": "iOS",
                "DevicePoolArn": "arn:aws:devicefarm:us-west-2::devicepool:0EXAMPLE-d7d7-48a5-ba5c-b33d66efa1f5",
                "FuzzEventCount": "6000",
                "FuzzEventThrottle": "50",
                "ProjectId": "eec4905f-98f8-40aa-9afc-4c1cfEXAMPLE",
                "TestType": "BUILTIN_FUZZ"
            },
            "OutputArtifacts": [],
            "InputArtifacts": [
                {
                    "Name": "SourceArtifact"
                }
            ],
            "Region": "us-west-2"
        }
    ]
},
```

------

## See Also<a name="action-reference-DeviceFarm-links"></a>

The following related resources can help you as you work with this action\.
+ [Working with Test Types in Device Farm](https://docs.aws.amazon.com/devicefarm/latest/developerguide/test-types.html) – This reference chapter in the *Device Farm Developer Guide* provides more description about the Android, iOS, and Web Application testing frameworks supported by Device Farm\.
+ [Actions in Device Farm](https://docs.aws.amazon.com/devicefarm/latest/APIReference/Welcome.html) – The API calls and parameters in the *Device Farm API Reference* can help you work with Device Farm projects\.
+ [Tutorial: Create a pipeline that builds and tests your Android app when a commit is pushed to your GitHub repository](tutorials-codebuild-devicefarm.md) – This tutorial provides a sample build spec file and sample application to create a pipeline with a GitHub source that builds and tests an Android app with CodeBuild and Device Farm\.
+ [Tutorial: Create a pipeline that tests your iOS app after a change in your S3 bucket](tutorials-codebuild-devicefarm-S3.md) – This tutorial provides a sample application to create a pipeline with an Amazon S3 source that tests a built iOS app with Device Farm\.