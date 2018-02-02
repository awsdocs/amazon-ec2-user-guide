# Resource IDs<a name="resource-ids"></a>

When resources are created, we assign each resource a unique resource ID\. You can use resource IDs to find your resources in the Amazon EC2 console\. If you are using a command line tool or the Amazon EC2 API to work with Amazon EC2, resource IDs are required for certain commands\. For example, if you are using the [stop\-instances](http://docs.aws.amazon.com/cli/latest/reference//ec2/stop-instances.html) AWS CLI command to stop an instance, you must specify the instance ID in the command\.

**Resource ID Length**  
A resource ID takes the form of a resource identifier \(such as `snap` for a snapshot\) followed by a hyphen and a unique combination of letters and numbers\. Starting in January 2016, we're gradually introducing longer length IDs for some Amazon EC2 and Amazon EBS resource types\. The length of the alphanumeric character combination was in an 8\-character format; the new IDs are in a 17\-character format, for example, `i-1234567890abcdef0` for an instance ID\.

Supported resource types will have an opt\-in period, during which you can enable the longer ID format\. After you've enabled longer IDs for a resource type, any new resources that you create are created with a longer ID unless you explicitly disable the longer ID format\. A resource ID does not change after it's created; therefore, your existing resources with shorter IDs are not affected\. Similarly, if you disable longer IDs for a resource type, any resources that you created with the longer IDs are not affected\.

All supported resource types will have a deadline date, after which all new resources of this type default to the longer ID format, and you can no longer disable the longer ID format\. You can enable or disable longer IDs per IAM user and IAM role\. By default, an IAM user or role defaults to the same settings as the root user\. 

Depending on when you created your account, supported resource types may default to using longer IDs\. However, you can opt out of using longer IDs until the deadline date for that resource type\. For more information, see [Longer EC2 and EBS Resource IDs](https://aws.amazon.com/ec2/faqs/#longer-ids) in the *Amazon EC2 FAQs*\. 

Resources created with longer IDs are visible to all IAM users and IAM roles, regardless of individual settings and provided that they have permissions to view the relevant resource types\.


+ [Working with Longer IDs](#resource-ids-working-with)
+ [Controlling Access to Longer ID Settings](#resource-ids-access)

## Working with Longer IDs<a name="resource-ids-working-with"></a>

You can view and modify the longer ID settings for yourself, or for a different IAM user, IAM role, or the root user of the account\.


+ [Viewing and Modifying Your Longer ID Settings](#resource-ids-working-with-self)
+ [Viewing and Modifying Longer ID Settings for Users or Roles](#resource-ids-working-with-iam)

### Viewing and Modifying Your Longer ID Settings<a name="resource-ids-working-with-self"></a>

You can use the Amazon EC2 console or the AWS CLI to view the resource types that support long IDs, and enable or disable the longer ID format for yourself\. The procedures in this section apply to the IAM user or IAM role that's logged into the console or that makes the request; they do not apply to the entire AWS account\.

**To view and modify the longer ID settings using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation bar at the top of the screen, the current region is displayed\. Select the region for which you want to view or change the longer ID settings\. Settings are not shared between regions\.

1. From the dashboard, under **Account Attributes**, choose **Resource ID length management**\. The resource types that support longer IDs are listed\. The date at which you're automatically switched over to using longer IDs for each resource type is displayed in the **Deadline** column\.

1. To enable the longer ID format for a supported resource type, choose the check box for the **Use Longer IDs** column\. To disable the longer ID format, clear the check box\.
**Important**  
If you’re logged in as the root user, these settings apply to the entire account, unless an IAM user or role logs in and explicitly overrides these settings for themselves\. Resources created with longer IDs are visible to all IAM users, regardless of individual settings and provided that they have permissions to view the relevant resource types\.

**To view and modify longer ID settings using the AWS CLI**

To view the longer ID settings of all supported resources, use the [describe\-id\-format](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-id-format.html) AWS CLI command:

```
aws ec2 describe-id-format

{
    "Statuses": [
        {
            "Deadline": "2016-11-01T13:00:00.000Z",
            "UseLongIds": false,
            "Resource": "instance"
        },
        {
            "Deadline": "2016-11-01T13:00:00.000Z",
            "UseLongIds": true,
            "Resource": "reservation"
        },
        {
            "Deadline": "2016-11-01T13:00:00.000Z",
            "UseLongIds": false,
            "Resource": "volume"
        },
        {
            "Deadline": "2016-11-01T13:00:00.000Z",
            "UseLongIds": false,
            "Resource": "snapshot"
        }
    ]
}
```

The results apply to the IAM user, IAM role, or root user that makes the request; they do not apply to the entire AWS account\. The results above indicate that the `instance`, `reservation`, `volume`, and `snapshot` resource types can be enabled or disabled for longer IDs; the `reservation` resource is already enabled\. The `Deadline` field indicates the date \(in UTC\) at which you will be automatically switched over to using longer IDs for that resource\. If a deadline date is not yet available, this value is not returned\.

To enable longer IDs for a specified resource, use the [modify\-id\-format](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-id-format.html) AWS CLI command:

```
aws ec2 modify-id-format --resource resource-type --use-long-ids
```

To disable longer IDs for a specified resource, use the [modify\-id\-format](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-id-format.html) AWS CLI command:

```
aws ec2 modify-id-format --resource resource-type --no-use-long-ids
```

If you’re using these actions as the root user, then these settings apply to the entire account, unless an IAM user or role explicitly overrides these settings for themselves\. These commands are per\-region only\. To modify the settings for other regions, use the `--region` parameter in the command\.

**Note**  
In the 2015\-10\-01 version of the Amazon EC2 API, if you call `describe-id-format` or `modify-id-format` using IAM role credentials, the results apply to the entire AWS account, and not the specific IAM role\. In the current version of the Amazon EC2 API, the results apply to the IAM role only\.

Alternatively, you can use the following commands:

**To describe the ID format**

+ [DescribeIdFormat](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeIdFormat.html) \(Amazon EC2 API\)

+ [Get\-EC2IdFormat](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2IdFormat.html) \(AWS Tools for Windows PowerShell\)

**To modify the ID format**

+ [ModifyIdFormat](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyIdFormat.html) \(Amazon EC2 API\)

+ [Edit\-EC2IdFormat](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2IdFormat.html) \(AWS Tools for Windows PowerShell\)

### Viewing and Modifying Longer ID Settings for Users or Roles<a name="resource-ids-working-with-iam"></a>

You can view supported resource types and enable the longer ID settings for a specific IAM user, IAM role, or the root user of your account by using the [describe\-identity\-id\-format](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-identity-id-format.html) and [modify\-identity\-id\-format](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-identity-id-format.html) AWS CLI commands\. To use these commands, you must specify the ARN of an IAM user, IAM role, or root account user in the request\. For example, the ARN of the role 'EC2Role' in account 123456789012 is `arn:aws:iam::123456789012:role/EC2Role`\. For more information, see [Principal](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal) in the *IAM User Guide*\. 

To view the longer ID settings of all supported resources for a specific IAM user or IAM role, use the following AWS CLI command:

```
aws ec2 describe-identity-id-format --principal-arn arn-of-iam-principal
```

To enable the longer ID settings for a resource type for a specific IAM user or IAM role, use the following AWS CLI command:

```
aws ec2 modify-identity-id-format --principal-arn arn-of-iam-principal --resource resource-type --use-long-ids
```

These commands apply to the ARN specified in the request, they do not apply to the IAM user, IAM role, or root user that made the request\.

You can enable the longer ID settings for all IAM users, IAM roles, and the root user of your account by using the following AWS CLI command:

```
aws ec2 modify-identity-id-format --principal-arn all --resource resource-type --use-long-ids
```

Alternatively, you can use the following commands:

**To describe the ID format**

+ [DescribeIdentityIdFormat](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeIdentityIdFormat.html) \(Amazon EC2 API\)

+ [Get\-EC2IdentityIdFormat](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2IdentityIdFormat.html) \(AWS Tools for Windows PowerShell\)

**To modify the ID format**

+ [ModifyIdentityIdFormat](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyIdentityIdFormat.html) \(Amazon EC2 API\)

+ [Edit\-EC2IdentityIdFormat](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2IdentityIdFormat.html) \(AWS Tools for Windows PowerShell\)

## Controlling Access to Longer ID Settings<a name="resource-ids-access"></a>

By default, IAM users and roles do not have permission to use the `ec2:DescribeIdFormat`, `ec2:DescribeIdentityIdFormat`, `ec2:ModifyIdFormat`, and `ec2:ModifyIdentityIdFormat` actions unless they're explicitly granted permission through their associated IAM policies\. For example, an IAM role may have permission to use all Amazon EC2 actions through an `"Action": "ec2:*"` element in the policy statement\. 

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
        "ec2:DescribeIdentityIdFormat" 
      ],
      "Resource": "*"
    }
  ] 
}
```

We do not support resource\-level permissions for the `ec2:DescribeIdFormat`, `ec2:DescribeIdentityIdFormat`, `ec2:ModifyIdFormat`, and `ec2:ModifyIdentityIdFormat` actions\. 