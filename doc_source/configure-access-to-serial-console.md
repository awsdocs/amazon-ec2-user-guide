# Configure access to the EC2 Serial Console<a name="configure-access-to-serial-console"></a>

To configure access to the serial console, you must grant serial console access at the account level and then configure IAM policies to grant access to your users\. You must also configure a password\-based user on every instance so that your users can use the serial console for troubleshooting\.

**Topics**
+ [Levels of access to the EC2 Serial Console](#serial-console-access-levels)
+ [Manage account access to the EC2 Serial Console](#serial-console-account-access)
+ [Configure IAM policies for EC2 Serial Console access](#serial-console-iam)
+ [Set an OS user password](#set-user-password)

## Levels of access to the EC2 Serial Console<a name="serial-console-access-levels"></a>

By default, there is no access to the serial console at the account level\. You need to explicitly grant access to the serial console at the account level\. For more information, see [Manage account access to the EC2 Serial Console](#serial-console-account-access)\.

You can use a service control policy \(SCP\) to allow access to the serial console within your organization\. You can then have granular access control at the user level by using an IAM policy to control access\. By using a combination of SCP and IAM policies, you have different levels of access control to the serial console\.

**Organization level**  
You can use a service control policy \(SCP\) to allow access to the serial console for member accounts within your organization\. For more information about SCPs, see [Service control policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html) in the *AWS Organizations User Guide*\.

**Instance level**  
You can configure the serial console access policies by using IAM PrincipalTag and ResourceTag constructions and by specifying instances by their ID\. For more information, see [Configure IAM policies for EC2 Serial Console access](#serial-console-iam)\.

**User level**  
You can configure access at the user level by configuring an IAM policy to allow or deny a specified user the permission to push the SSH public key to the serial console service of a particular instance\. For more information, see [Configure IAM policies for EC2 Serial Console access](#serial-console-iam)\.

**OS level**  
You can set a user password at the guest OS level\. This provides access to the serial console for some use cases\. However, to monitor the logs, you don't need a password\-based user\. For more information, see [Set an OS user password](#set-user-password)\.

## Manage account access to the EC2 Serial Console<a name="serial-console-account-access"></a>

By default, there is no access to the serial console at the account level\. You need to explicitly grant access to the serial console at the account level\.

**Topics**
+ [Grant permission to users to manage account access](#sc-account-access-permissions)
+ [View account access status to the serial console](#sc-view-account-access)
+ [Grant account access to the serial console](#sc-grant-account-access)
+ [Deny account access to the serial console](#sc-deny-account-access)

### Grant permission to users to manage account access<a name="sc-account-access-permissions"></a>

To allow your users to manage account access to the EC2 serial console, you need to grant them the required IAM permissions\.

The following policy grants permissions to view the account status, and to allow and prevent account access to the EC2 serial console\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:GetSerialConsoleAccessStatus",
                "ec2:EnableSerialConsoleAccess",
                "ec2:DisableSerialConsoleAccess"
            ],
            "Resource": "*"
        }
    ]
}
```

For more information, see [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the *IAM User Guide*\.

### View account access status to the serial console<a name="sc-view-account-access"></a>

**To view account access status to the serial console \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the left navigation pane, choose **EC2 Dashboard**\.

1. From **Account attributes**, choose **EC2 Serial Console**\.

   The **EC2 Serial Console access** field indicates whether account access is **Allowed** or **Prevented**\.

   The following screenshot shows that the account is prevented from using the EC2 serial console\.  
![\[Access to the EC2 Serial Console is prevented.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/serial-console-no-acct-access.png)

**To view account access status to the serial console \(AWS CLI\)**  
Use the [get\-serial\-console\-access\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-serial-console-access-status.html) command to view account access status to the serial console\.

```
aws ec2 get-serial-console-access-status --region us-east-1
```

In the following output, `true` indicates that the account is allowed access to the serial console\.

```
{
    "SerialConsoleAccessEnabled": true
}
```

### Grant account access to the serial console<a name="sc-grant-account-access"></a>

**To grant account access to the serial console \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the left navigation pane, choose **EC2 Dashboard**\.

1. From **Account attributes**, choose **EC2 Serial Console**\.

1. Choose **Manage**\.

1. To allow access to the EC2 serial console of all instances in the account, select the **Allow** check box\.

1. Choose **Update**\.

**To grant account access to the serial console \(AWS CLI\)**  
Use the [enable\-serial\-console\-access](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-serial-console-access.html) command to allow account access to the serial console\.

```
aws ec2 enable-serial-console-access --region us-east-1
```

In the following output, `true` indicates that the account is allowed access to the serial console\.

```
{
    "SerialConsoleAccessEnabled": true
}
```

### Deny account access to the serial console<a name="sc-deny-account-access"></a>

**To deny account access to the serial console \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the left navigation pane, choose **EC2 Dashboard**\.

1. From **Account attributes**, choose **EC2 Serial Console**\.

1. Choose **Manage**\.

1. To prevent access to the EC2 serial console of all instances in the account, clear the **Allow** check box\.

1. Choose **Update**\.

**To deny account access to the serial console \(AWS CLI\)**  
Use the [disable\-serial\-console\-access](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-serial-console-access.html) command to prevent account access to the serial console\.

```
aws ec2 disable-serial-console-access --region us-east-1
```

In the following output, `false` indicates that the account is denied access to the serial console\.

```
{
    "SerialConsoleAccessEnabled": false
}
```

## Configure IAM policies for EC2 Serial Console access<a name="serial-console-iam"></a>

By default, your users do not have access to the serial console\. Your organization must configure IAM policies to grant your users the required access\. For more information, see [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the *IAM User Guide*\.

For serial console access, create a JSON policy document that includes the `ec2-instance-connect:SendSerialConsoleSSHPublicKey` action\. This action grants a user permission to push the public key to the serial console service, which starts a serial console session\. We recommend restricting access to specific EC2 instances\. Otherwise, all users with this permission can connect to the serial console of all EC2 instances\.

**Topics**
+ [Explicitly allow access to the serial console](#iam-explicitly-allow-access)
+ [Explicitly deny access to the serial console](#serial-console-IAM-policy)
+ [Use resource tags to control access to the serial console](#iam-resource-tags)

### Explicitly allow access to the serial console<a name="iam-explicitly-allow-access"></a>

By default, no one has access to the serial console\. To grant access to the serial console, you need to configure a policy to explicitly allow access\. We recommend configuring a policy that restricts access to specific instances\.

The following policy allows access to the serial console of a specific instance, identified by its instance ID\.

Note that the `DescribeInstances`, `DescribeInstanceTypes`, and `GetSerialConsoleAccessStatus` actions do not support resource\-level permissions, and therefore all resources, indicated by the `*` \(asterisk\), must be specified for these actions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowDescribeInstances",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:DescribeInstanceTypes",
                "ec2:GetSerialConsoleAccessStatus"
            ],
             "Resource": "*"
        },
        {
            "Sid": "AllowinstanceBasedSerialConsoleAccess",
            "Effect": "Allow",
            "Action": [
                "ec2-instance-connect:SendSerialConsoleSSHPublicKey"
            ],
            "Resource": "arn:aws:ec2:region:account-id:instance/i-0598c7d356eba48d7"
        }
    ]
}
```

### Explicitly deny access to the serial console<a name="serial-console-IAM-policy"></a>

The following IAM policy allows access to the serial console of all instances, denoted by the `*` \(asterisk\), and explicitly denies access to the serial console of a specific instance, identified by its ID\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowSerialConsoleAccess",
            "Effect": "Allow",
            "Action": [
                "ec2-instance-connect:SendSerialConsoleSSHPublicKey",
                "ec2:DescribeInstances",
                "ec2:DescribeInstanceTypes",
                "ec2:GetSerialConsoleAccessStatus"
            ],
            "Resource": "*"
        },
        {
            "Sid": "DenySerialConsoleAccess",
            "Effect": "Deny",
            "Action": [
                "ec2-instance-connect:SendSerialConsoleSSHPublicKey"
            ],
            "Resource": "arn:aws:ec2:region:account-id:instance/i-0598c7d356eba48d7"
        }
    ]
}
```

### Use resource tags to control access to the serial console<a name="iam-resource-tags"></a>

You can use resource tags to control access to the serial console of an instance\.

Attribute\-based access control is an authorization strategy that defines permissions based on tags that can be attached to users and AWS resources\. For example, the following policy allows a user to initiate a serial console connection for an instance only if that instance's resource tag and the principal's tag have the same `SerialConsole` value for the tag key\.

For more information about using tags to control access to your AWS resources, see [Controlling access to AWS resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html#access_tags_control-resources) in the *IAM User Guide*\.

Note that the `DescribeInstances`, `DescribeInstanceTypes`, and `GetSerialConsoleAccessStatus` actions do not support resource\-level permissions, and therefore all resources, indicated by the `*` \(asterisk\), must be specified for these actions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowDescribeInstances",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:DescribeInstanceTypes",
                "ec2:GetSerialConsoleAccessStatus"
            ],
            "Resource": "*"
        },
        {
            "Sid": "AllowTagBasedSerialConsoleAccess",
            "Effect": "Allow",
            "Action": [
                "ec2-instance-connect:SendSerialConsoleSSHPublicKey"
            ],
            "Resource": "arn:aws:ec2:region:account-id:instance/*",
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/SerialConsole": "${aws:PrincipalTag/SerialConsole}"
                }
            }
        }
    ]
}
```

## Set an OS user password<a name="set-user-password"></a>

You can connect to the serial console without a password\. However, to *use* the serial console for troubleshooting an instance, the instance must have a password\-based OS user\.

You can set the password for any OS user, including the root user\. Note that the root user can modify all files, while each OS user might have limited permissions\.

You must set a user password for every instance for which you will use the serial console\. This is a one\-time requirement for each instance\.

**Note**  
The following instructions are applicable only if you launched your instance using an AMI provided by AWS because, by default, AMIs provided by AWS are not configured with a password\-based user\. If you launched your instance using an AMI that already has the root user password configured, you can skip these instructions\.

**To set an OS user password**

1. [Connect](AccessingInstances.md) to your instance\. You can use any method for connecting to your instance, except the EC2 Serial Console connection method\.

1. To set the password for a user, use the passwd command\. In the following example, the user is `root`\.

   ```
   [ec2-user ~]$ sudo passwd root
   ```

   The following is example output\.

   ```
   Changing password for user root.
   New password:
   ```

1. At the `New password` prompt, enter the new password\.

1. At the prompt, re\-enter the password\.