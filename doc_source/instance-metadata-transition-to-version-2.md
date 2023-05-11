# Transition to using Instance Metadata Service Version 2<a name="instance-metadata-transition-to-version-2"></a>

When migrating to IMDSv2, we recommend that you use the following tools and transition path\.

**Topics**
+ [Tools for helping with the transition to IMDSv2](#tools-for-transitioning-to-imdsv2)
+ [Recommended path to requiring IMDSv2](#recommended-path-for-requiring-imdsv2)

## Tools for helping with the transition to IMDSv2<a name="tools-for-transitioning-to-imdsv2"></a>

If your software uses IMDSv1, use the following tools to help reconfigure your software to use IMDSv2\.

**AWS software**  
The latest versions of the AWS CLI and AWS SDKs support IMDSv2\. To use IMDSv2, make sure that your EC2 instances have the latest versions of the CLI and SDKs\. For information about updating the CLI, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.  
All Amazon Linux 2 software packages support IMDSv2\.  
For the minimum AWS SDK versions that support IMDSv2, see [Use a supported AWS SDK](use-a-supported-sdk-version-for-imdsv2.md)\.

**CloudWatch**  
IMDSv2 uses token\-backed sessions, while IMDSv1 does not\. The `MetadataNoToken` CloudWatch metric tracks the number of calls to the Instance Metadata Service \(IMDS\) that are using IMDSv1\. By tracking this metric to zero, you can determine if and when all of your software has been upgraded to use IMDSv2\. For more information, see [Instance metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)\.

**Updates to EC2 APIs and CLIs**  
For new instances, you can use the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) API to launch new instances that require the use of IMDSv2\. For more information, see [Configure instance metadata options for new instances](configuring-IMDS-new-instances.md)\.  
For existing instances, you can use the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API to require the use of IMDSv2\. For more information, see [Modify instance metadata options for existing instances](configuring-IMDS-existing-instances.md)\.  
To require the use of IMDSv2 on all new instances launched by Auto Scaling groups, your Auto Scaling groups can use either a launch template or a launch configuration\. When you [create a launch template](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template.html) or [create a launch configuration](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/create-launch-configuration.html), you must configure the `MetadataOptions` parameters to require the use of IMDSv2\. The Auto Scaling group launches new instances using the new launch template or launch configuration, but existing instances are not affected\. For existing instances in an Auto Scaling group, you can use the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API to require the use of IMDSv2 on the existing instances, or terminate the instances and the Auto Scaling group will launch new replacement instances with the instance metadata options settings that are defined in the new launch template or launch configuration\.

**Use an AMI that configures IMDSv2 by default**  
When you launch an instance, you can automatically configure it to use IMDSv2 by default \(the `HttpTokens` parameter is set to `required`\) by launching it with an AMI that is configured with the `ImdsSupport` parameter set to `v2.0`\. You can set the `ImdsSupport` parameter to `v2.0` when you register the AMI using the [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) CLI command, or you can modify an existing AMI by using the [modify\-image\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-image-attribute.html) CLI command\. For more information, see [Configure the AMI](configuring-IMDS-new-instances.md#configure-IMDS-new-instances-ami-configuration)\.

**IAM policies and SCPs**  
You can use an IAM policy or AWS Organizations service control policy \(SCP\) to control users as follows:  
+ Can't launch an instance using the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) API unless the instance is configured to use IMDSv2\.
+ Can't modify a running instance using the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API to re\-enable IMDSv1\.
The IAM policy or SCP must contain the following IAM condition keys:  
+ `ec2:MetadataHttpEndpoint`
+ `ec2:MetadataHttpPutResponseHopLimit`
+ `ec2:MetadataHttpTokens`
If a parameter in the API or CLI call does not match the state specified in the policy that contains the condition key, the API or CLI call fails with an `UnauthorizedOperation` response\.  
Furthermore, you can choose an additional layer of protection to enforce the change from IMDSv1 to IMDSv2\. At the access management layer with respect to the APIs called via EC2 Role credentials, you can use a new condition key in either IAM policies or AWS Organizations service control policies \(SCPs\)\. Specifically, by using the condition key `ec2:RoleDelivery` with a value of `2.0` in your IAM policies, API calls made with EC2 Role credentials obtained from IMDSv1 will receive an `UnauthorizedOperation` response\. The same thing can be achieved more broadly with that condition required by an SCP\. This ensures that credentials delivered via IMDSv1 cannot actually be used to call APIs because any API calls not matching the specified condition will receive an `UnauthorizedOperation` error\.  
For example IAM policies, see [Work with instance metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\. For more information on SCPs, see [Service Control Policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html) in the *AWS Organizations User Guide*\.

## Recommended path to requiring IMDSv2<a name="recommended-path-for-requiring-imdsv2"></a>

Using the above tools, we recommend that you follow this path for transitioning to IMDSv2\.

### Step 1: At the start<a name="path-step-1"></a>

Update the SDKs, CLIs, and your software that use Role credentials on their EC2 instances to versions compatible with IMDSv2\. For information about updating the CLI, see [Upgrading to the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html#install-linux-awscli-upgrade) in the *AWS Command Line Interface User Guide*\.

Then, change your software that directly accesses instance metadata \(in other words, that does not use an SDK\) using the IMDSv2 requests\. 

### Step 2: Track your transition progress<a name="path-step-2"></a>

Track your transition progress by using the CloudWatch metric `MetadataNoToken`\. This metric shows the number of IMDSv1 calls to the IMDS on your instances\. For more information, see [Instance metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)\. 

### Step 3: When there is zero IMDSv1 usage<a name="path-step-3"></a>

When the CloudWatch metric `MetadataNoToken` records zero IMDSv1 usage, your instances are ready to be fully transitioned to using IMDSv2\. At this stage, you can do the following:
+ **New instances**

  When launching a new instance, you can do the following:
  + Amazon EC2 console: In the launch instance wizard, set **Metadata accessible** to **Enabled** and **Metadata version** to **V2 only \(token required\)**\. For more information, see [Configure the instance at launch](configuring-IMDS-new-instances.md#configure-IMDS-new-instances-instance-settings)\.
  + AWS CLI: Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) CLI command to specify that only IMDSv2 is to be used\.
+ **Existing instances**

  For existing instances, you can do the following:
  + Amazon EC2 console: On the **Instances** page, select your instance, choose **Actions**, **Instance settings**, **Modify instance metadata options**, and for **IMDSv2**, choose **Required**\. For more information, see [Require the use of IMDSv2](configuring-IMDS-existing-instances.md#modify-require-IMDSv2)\.
  + AWS CLI: Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command to specify that only IMDSv2 is to be used\.

  You can modify the instance metadata options on running instances, and you don't need to restart the instances after modifying the instance metadata options\.

### Step 4: Check if your instances are transitioned to IMDSv2<a name="path-step-4"></a>

You can check if any instances are not yet configured to require the use of IMDSv2, in other words, IMDSv2 is still configured as `optional`\. If any instances are still configured as `optional`, you can modify the instance metadata options to make IMDSv2 `required` by repeating the preceding [Step 3](#path-step-3)\.

To filter your instances:
+ Amazon EC2 console: On the **Instances** page, filter your instances by using the **IMDSv2 = optional** filter\. For more information about filtering, see [Filter resources using the console](Using_Filtering.md#console-filter)\. You can also view whether IMDSv2 is required or optional for each instance: In the **Preferences** window, toggle on **IMDSv2** to add the **IMDSv2** column to the **Instances** table\.
+ AWS CLI: Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and filter by `metadata-options.http-tokens = optional`, as follows:

  ```
  aws ec2 describe-instances --filters "Name=metadata-options.http-tokens,Values=optional" --query "Reservations[*].Instances[*].[InstanceId]" --output text
  ```

### Step 5: When all of your instances are transitioned to IMDSv2<a name="path-step-5"></a>

The `ec2:MetadataHttpTokens`, `ec2:MetadataHttpPutResponseHopLimit`, and `ec2:MetadataHttpEndpoint` IAM condition keys can be used to control the use of the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) and the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) APIs and corresponding CLIs\. If a policy is created, and a parameter in the API call does not match the state specified in the policy using the condition key, the API or CLI call fails with an `UnauthorizedOperation` response\. For example IAM policies, see [Work with instance metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\.