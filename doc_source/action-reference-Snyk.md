# Snyk action structure reference<a name="action-reference-Snyk"></a>

The **Snyk** action in CodePipeline automates detecting and fixing security vulnerabilities in your open source code\. You can use Snyk with application source code in your third\-party repository, such as GitHub or Bitbucket, or with images for container applications\. Your action will scan and report on vulnerability levels and alerts that you configure\. 

**Note**  

**Topics**
+ [Action type ID](#action-reference-Snyk-type)
+ [Input artifacts](#action-reference-Snyk-input)
+ [Output artifacts](#action-reference-Snyk-output)
+ [See also](#action-reference-Snyk-links)

## Action type ID<a name="action-reference-Snyk-type"></a>
+ Category: `Invoke`
+ Owner: `ThirdParty`
+ Provider: `Snyk`
+ Version: `1`

Example:

```
            {
                "Category": "Invoke",
                "Owner": "ThirdParty",
                "Provider": "Snyk",
                "Version": "1"
            },
```

## Input artifacts<a name="action-reference-Snyk-input"></a>
+ **Number of artifacts:** `1`
+ **Description:** The files that make up the input artifact for the invoke action\.

## Output artifacts<a name="action-reference-Snyk-output"></a>
+ **Number of artifacts:** `1`
+ **Description:** The files that make up the output artifact for the invoke action\.

## See also<a name="action-reference-Snyk-links"></a>

The following related resources can help you as you work with this action\.
+ For more information about using Snyk actions in CodePipeline, refer to [Automate vulnerability scanning in CodePipeline with Snyk](https://snyk.io/blog/automate-vulnerability-scanning-in-aws-codepipeline-with-snyk/)\.