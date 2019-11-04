# Resource IDs<a name="resource-ids"></a>

When resources are created, we assign each resource a unique resource ID\. You can use resource IDs to find your resources in the Amazon EC2 console\. If you are using a command line tool or the Amazon EC2 API to work with Amazon EC2, resource IDs are required for certain commands\. For example, if you are using the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) AWS CLI command to stop an instance, you must specify the instance ID in the command\.

**Resource ID Length**  
A resource ID takes the form of a resource identifier \(such as `snap` for a snapshot\) followed by a hyphen and a unique combination of letters and numbers\. Starting in January 2016, we're gradually introducing longer length IDs for Amazon EC2 and Amazon EBS resource types\. The length of the alphanumeric character combination was in an 8\-character format; the new IDs are in a 17\-character format, for example, `i-1234567890abcdef0` for an instance ID\.

Supported resource types have an opt\-in period, during which you can choose a resource ID format, and a deadline date, after which the resource defaults to the longer ID format\. After the deadline has passed for a specific resource type, you can no longer disable the longer ID format for that resource type\.

Different resource types have different opt\-in periods and deadline dates\. The following table lists the supported resource types, along with their opt\-in periods and deadline dates\.


| Resource type | Opt\-in period | Deadline date | 
| --- | --- | --- | 
|  `instance` \| `snapshot` \|`reservation` \| `volume`  |  No longer available  |  December 15, 2016  | 
|  `bundle` \| `conversion-task` \| `customer-gateway` \| `dhcp-options` \| `elastic-ip-allocation` \|  `elastic-ip-association` \| `export-task` \| `flow-log` \| `image` \| `import-task` \| `internet-gateway` \| `network-acl` \| `network-acl-association` \| `network-interface` \| `network-interface-attachment` \| `prefix-list` \|  `route-table` \| `route-table-association` \| `security-group` \| `subnet` \| `subnet-cidr-block-association` \| `vpc` \| `vpc-cidr-block-association` \| `vpc-endpoint` \|  `vpc-peering-connection` \| `vpn-connection` \| `vpn-gateway`  |  February 09, 2018 \- June 30, 2018  |  June 30, 2018  | 

**During the Opt\-in Period**  
You can enable or disable longer IDs for a resource at any time during the opt\-in period\. After you've enabled longer IDs for a resource type, any new resources that you create are created with a longer ID\.

**Note**  
A resource ID does not change after it's created\. Therefore, enabling or disabling longer IDs during the opt\-in period does not affect your existing resource IDs\.

Depending on when you created your AWS account, supported resource types may default to using longer IDs\. However, you can opt out of using longer IDs until the deadline date for that resource type\. For more information, see [Longer EC2 and EBS Resource IDs](https://aws.amazon.com/ec2/faqs/#longer-ids) in the *Amazon EC2 FAQs*\.

**After the Deadline Date**  
You can’t disable longer IDs for a resource type after its deadline date has passed\. Any new resources that you create are created with a longer ID\.

## Working with Longer IDs<a name="resource-ids-working-with"></a>

You can enable or disable longer IDs per IAM user and IAM role\. By default, an IAM user or role defaults to the same settings as the root user\. 

**Topics**
+ [Viewing Longer ID Settings](#viewing_longer_id_settings)
+ [Modifying Longer ID Settings](#modifying_longer_id_settings)

### Viewing Longer ID Settings<a name="viewing_longer_id_settings"></a>

You can use the console and command line tools to view the resource types that support longer IDs\.

**To view your longer ID settings using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation bar at the top of the screen, select the region for which to view your longer ID settings\. 

1. From the dashboard, under **Account Attributes**, choose **Resource ID length management**\. 

1. Expand **Advanced Resource ID Management** to view the resource types that support longer IDs and their deadline dates\.

**To view your longer ID settings using the command line**

Use one of the following commands:
+ [describe\-id\-format](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-id-format.html) \(AWS CLI\)

  ```
  aws ec2 describe-id-format  --region region
  ```
+ [Get\-EC2IdFormat](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2IdFormat.html) \(AWS Tools for Windows PowerShell\)

  ```
  Get-EC2IdFormat -Region region
  ```

**To view longer ID settings for a specific IAM user or IAM role using the command line**

Use one of the following commands and specify the ARN of an IAM user, IAM role, or root account user in the request\.
+ [describe\-identity\-id\-format](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-identity-id-format.html) \(AWS CLI\)

  ```
  aws ec2 describe-identity-id-format --principal-arn arn-of-iam-principal --region region
  ```
+ [Get\-EC2IdentityIdFormat](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2IdentityIdFormat.html) \(AWS Tools for Windows PowerShell\)

  ```
  Get-EC2IdentityIdFormat -PrincipalArn arn-of-iam-principal -Region region
  ```

**To view the aggregated longer ID settings for a specific region using the command line**  
Use the [describe\-aggregate\-id\-format](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-aggregate-id-format.html) AWS CLI command to view the aggregated longer ID setting for the entire region, as well as the aggregated longer ID setting of all ARNs for each resource type\. This command is useful for performing a quick audit to determine whether a specific region is fully opted in for longer IDs\.

```
aws ec2 describe-aggregate-id-format --region region
```

**To identify users who have explicitly defined custom longer ID settings**  
Use the [describe\-principal\-id\-format](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-principal-id-format.html) AWS CLI command to view the longer ID format settings for the root user and all IAM roles and IAM users that have explicitly specified a longer ID preference\. This command is useful for identifying IAM users and IAM roles that have overridden the default longer ID settings\.

```
aws ec2 describe-principal-id-format --region region
```

### Modifying Longer ID Settings<a name="modifying_longer_id_settings"></a>

You can use the console and command line tools to modify longer ID settings for resource types that are still within their opt\-in period\.

**Note**  
The AWS CLI and AWS Tools for Windows PowerShell commands in this section are per\-region only\. They apply to the default region unless otherwise specified\. To modify the settings for other regions, include the `region` parameter in the command\.

**To modify longer ID settings using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation bar at the top of the screen, select the region for which to modify the longer ID settings\. 

1. From the dashboard, under **Account Attributes**, choose **Resource ID length management**\. 

1. Do one of the following:
   + To enable longer IDs for all supported resource types for all IAM users across all regions, choose **Switch to longer IDs**, **Yes, switch to longer IDs**\.
**Important**  
IAM users and IAM roles need the `ec2:ModifyIdentityIdFormat` permission to perform this action\.
   + To modify longer ID settings for a specific resource type for your IAM user account, expand **Advanced Resource ID Management**, and then select the corresponding check box in the **My IAM Role/User** column to enable longer IDs, or clear the check box to disable longer IDs\.
   + To modify longer ID settings for a specific resource type for all IAM users, expand **Advanced Resource ID Management**, and then select the corresponding check box in the **All IAM Roles/Users** column to enable longer IDs, or clear the check box to disable longer IDs\.

**To modify longer ID settings for your IAM user account using the command line**

Use one of the following commands:
**Note**  
If you’re using these commands as the root user, then changes apply to the entire AWS account, unless an IAM user or role explicitly overrides these settings for themselves\.
+ [modify\-id\-format](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-id-format.html) \(AWS CLI\)

  ```
  aws ec2 modify-id-format --resource resource_type --use-long-ids
  ```

  You can also use the command to modify the longer ID settings for all supported resource types\. To do this, replace the `resource_type` parameter with `all-current`\.

  ```
  aws ec2 modify-id-format --resource all-current --use-long-ids
  ```
**Note**  
To disable longer IDs, replace the `use-long-ids` parameter with `no-use-long-ids`\.
+ [Edit\-EC2IdFormat](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2IdFormat.html) \(AWS Tools for Windows PowerShell\)

  ```
  Edit-EC2IdFormat -Resource resource_type -UseLongId boolean
  ```

  You can also use the command to modify the longer ID settings for all supported resource types\. To do this, replace the `resource_type` parameter with `all-current`\.

  ```
  Edit-EC2IdFormat -Resource all-current -UseLongId boolean
  ```

**To modify longer ID settings for a specific IAM user or IAM role using the command line**

Use one of the following commands and specify the ARN of an IAM user, IAM role, or root user in the request\.
+ [modify\-identity\-id\-format](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-identity-id-format.html) \(AWS CLI\)

  ```
  aws ec2 modify-identity-id-format --principal-arn arn-of-iam-principal --resource resource_type --use-long-ids
  ```

  You can also use the command to modify the longer ID settings for all supported resource types\. To do this, specify `all-current` for the `--resource` parameter\.

  ```
  aws ec2 modify-identity-id-format --principal-arn arn-of-iam-principal --resource all-current --use-long-ids
  ```
**Note**  
To disable longer IDs, replace the `use-long-ids` parameter with `no-use-long-ids`\.
+ [Edit\-EC2IdentityIdFormat](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2IdentityIdFormat.html) \(AWS Tools for Windows PowerShell\)

  ```
  Edit-EC2IdentityIdFormat -PrincipalArn arn-of-iam-principal -Resource resource_type -UseLongId boolean
  ```

  You can also use the command to modify the longer ID settings for all supported resource types\. To do this, specify `all-current` for the `-Resource` parameter\.

  ```
  Edit-EC2IdentityIdFormat -PrincipalArn arn-of-iam-principal -Resource all-current -UseLongId boolean
  ```

## Controlling Access to Longer ID Settings<a name="resource-ids-access"></a>

By default, IAM users and roles do not have permission to use the following actions unless they're explicitly granted permission through their associated IAM policies:
+ `ec2:DescribeIdFormat`
+ `ec2:DescribeIdentityIdFormat`
+ `ec2:DescribeAggregateIdFormat`
+ `ec2:DescribePrincipalIdFormat`
+ `ec2:ModifyIdFormat`
+ `ec2:ModifyIdentityIdFormat`

For example, an IAM role may have permission to use all Amazon EC2 actions through an `"Action": "ec2:*"` element in the policy statement\.

To prevent IAM users and roles from viewing or modifying the longer resource ID settings for themselves or other users and roles in your account, ensure that the IAM policy contains the following statement:

```
{
  "Version": "2012-10-17", 
  "Statement": [ 
    { 
      "Effect": "Deny",
      "Action": [
        "ec2:ModifyIdFormat", 
        "ec2:DescribeIdFormat", 
        "ec2:ModifyIdentityIdFormat", 
        "ec2:DescribeIdentityIdFormat",
        "ec2:DescribeAggregateIdFormat",
        "ec2:DescribePrincipalIdFormat"
      ],
      "Resource": "*"
    }
  ] 
}
```

We do not support resource\-level permissions for the following actions:
+ `ec2:DescribeIdFormat`
+ `ec2:DescribeIdentityIdFormat`
+ `ec2:DescribeAggregateIdFormat`
+ `ec2:DescribePrincipalIdFormat`
+ `ec2:ModifyIdFormat`
+ `ec2:ModifyIdentityIdFormat`