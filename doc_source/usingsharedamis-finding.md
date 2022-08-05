# Find shared AMIs<a name="usingsharedamis-finding"></a>

You can use the Amazon EC2 console or the command line to find shared AMIs\. 

AMIs are a Regional resource\. When you search for a shared AMI \(public or private\), you must search for it from the same Region from which it is shared\. To make an AMI available in a different Region, copy the AMI to the Region, and then share it\. For more information, see [Copy an AMI](CopyingAMIs.md)\.

**Topics**
+ [Find a shared AMI \(console\)](#usingsharedamis-finding-console)
+ [Find a shared AMI \(AWS CLI\)](#usingsharedamis-finding-cli)
+ [Use shared AMIs](#usingsharedamis-confirm)

## Find a shared AMI \(console\)<a name="usingsharedamis-finding-console"></a>

**To find a shared private AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\. 

1. In the first filter, choose **Private images**\. All AMIs that have been shared with you are listed\. To granulate your search, choose the **Search** bar and use the filter options provided in the menu\.

**To find a shared public AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\.

1. In the first filter, choose **Public images**\. To granulate your search, choose the **Search** field and use the filter options provided in the menu\.

**To find Amazon's shared public AMIs using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\.

1. In the first filter, choose **Public images**\.

1. Choose the **Search** field and then, from the menu options that appear, choose **Owner alias**, then **=**, and then **amazon** to display only Amazon's public images\.

**To find a shared public AMI from a verified provider using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMI Catalog**\.

1. Choose **Community AMIs**\.

1. The **Verified provider** label indicates the AMIs that are from Amazon or a verified partner\.

## Find a shared AMI \(AWS CLI\)<a name="usingsharedamis-finding-cli"></a>

Use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command \(AWS CLI\) to list AMIs\. You can scope the list to the types of AMIs that interest you, as shown in the following examples\.

**Example: List all public AMIs**  
The following command lists all public AMIs, including any public AMIs that you own\.

```
aws ec2 describe-images --executable-users all
```

**Example: List AMIs with explicit launch permissions**  
The following command lists the AMIs for which you have explicit launch permissions\. This list does not include any AMIs that you own\.

```
aws ec2 describe-images --executable-users self
```

**Example: List AMIs owned by verified providers**  
The following command lists the AMIs owned by verified providers\. Public AMIs owned by verified providers \(either Amazon or verified partners\) have an aliased owner, which appears as `amazon` or `aws-marketplace` in the account field\. This helps you to easily find AMIs from verified providers\. Other users can't alias their AMIs\.

```
aws ec2 describe-images \
    --owners amazon aws-marketplace \
    --query 'Images[*].[ImageId]' \
    --output text
```

**Example: List AMIs owned by an account**  
The following command lists the AMIs owned by the specified AWS account\.

```
aws ec2 describe-images --owners 123456789012
```

**Example: Scope AMIs using a filter**  
To reduce the number of displayed AMIs, use a filter to list only the types of AMIs that interest you\. For example, use the following filter to display only EBS\-backed AMIs\.

```
--filters "Name=root-device-type,Values=ebs"
```

## Use shared AMIs<a name="usingsharedamis-confirm"></a>

Before you use a shared AMI, take the following steps to confirm that there are no pre\-installed credentials that would allow unwanted access to your instance by a third party and no pre\-configured remote logging that could transmit sensitive data to a third party\. Check the documentation for the Linux distribution used by the AMI for information about improving the security of the system\.

To ensure that you don't accidentally lose access to your instance, we recommend that you initiate two SSH sessions and keep the second session open until you've removed credentials that you don't recognize and confirmed that you can still log into your instance using SSH\.

1. Identify and disable any unauthorized public SSH keys\. The only key in the file should be the key you used to launch the AMI\. The following command locates `authorized_keys` files:

   ```
   [ec2-user ~]$ sudo find / -name "authorized_keys" -print -exec cat {} \;
   ```

1. Disable password\-based authentication for the root user\. Open the `sshd_config` file and edit the `PermitRootLogin` line as follows:

   ```
   PermitRootLogin without-password
   ```

   Alternatively, you can disable the ability to log into the instance as the root user:

   ```
   PermitRootLogin No
   ```

   Restart the sshd service\.

1. Check whether there are any other user accounts that are able to log in to your instance\. Accounts with superuser privileges are particularly dangerous\. Remove or lock the password of any unknown accounts\.

1. Check for open ports that you aren't using and running network services listening for incoming connections\.

1. To prevent preconfigured remote logging, you should delete the existing configuration file and restart the rsyslog service\. For example:

   ```
   [ec2-user ~]$ sudo rm /etc/rsyslog.conf
   [ec2-user ~]$ sudo service rsyslog restart
   ```

1. Verify that all cron jobs are legitimate\.

If you discover a public AMI that you feel presents a security risk, contact the AWS security team\. For more information, see the [AWS Security Center](https://aws.amazon.com/security/)\.