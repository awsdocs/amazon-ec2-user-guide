# Make an AMI public<a name="sharingamis-intro"></a>

You can share your AMIs with other AWS accounts\. To allow all AWS accounts to use an AMI to launch instances, make the AMI public\. To allow only specific accounts to use the AMI to launch instances, see [Share an AMI with specific AWS accounts](sharingamis-explicit.md)\.

**Topics**
+ [Considerations](#considerations-for-sharing-public-AMIs)
+ [Share an AMI with all AWS accounts \(console\)](#sharingamis-intro-console)
+ [Share an AMI with all AWS accounts \(AWS CLI\)](#sharingamis-cli)

## Considerations<a name="considerations-for-sharing-public-AMIs"></a>

Consider the following before making an AMI public\.
+ **Some AMIs can't be made public** – If your AMI includes one of the following components, you can't make it public \(but you can [share the AMI with specific AWS accounts](sharingamis-explicit.md)\):
  + Encrypted volumes
  + Snapshots of encrypted volumes
  + Product codes
+ **Avoid exposing sensitive data** – To avoid exposing sensitive data when you share an AMI, read the security considerations in [Guidelines for shared Linux AMIs](building-shared-amis.md) and follow the recommended actions\.
+ **Region** – AMIs are a Regional resource\. When you share an AMI, it is available only in the Region from which you shared it\. To make an AMI available in a different Region, copy the AMI to the Region and then share it\. For more information, see [Copy an AMI](CopyingAMIs.md)\.
+ **Usage** – When you share an AMI, users can only launch instances from the AMI\. They can’t delete, share, or modify it\. However, after they have launched an instance using your AMI, they can then create an AMI from the instance they launched\.
+ **Automatic deprecation** – By default, the deprecation date of all public AMIs is set to two years from the AMI creation date\. You can set the deprecation date to earlier than two years\. To cancel the deprecation date, or to move the deprecation to a later date, you must make the AMI private by only [sharing it with specific AWS accounts](sharingamis-explicit.md)\.
+ **Billing** – You are not billed when your AMI is used by other AWS accounts to launch instances\. The accounts that launch instances using the AMI are billed for the instances that they launch\.

## Share an AMI with all AWS accounts \(console\)<a name="sharingamis-intro-console"></a>

After you make an AMI public, it is available in **Community AMIs** when you launch an instance in the same Region using the console\. Note that it can take a short while for an AMI to appear in **Community AMIs** after you make it public\. It can also take a short while for an AMI to be removed from **Community AMIs** after you make it private\.

------
#### [ New console ]

**To share a public AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\.

1. Select your AMI from the list, and then choose **Actions**, **Edit AMI permissions**\.

1. Choose **Public**, and then choose **Save changes**\.

------
#### [ Old console ]

**To share a public AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\.

1. Select your AMI from the list, and then choose **Actions**, **Modify Image Permissions**\.

1. Choose **Public**, and then choose **Save**\.

------

## Share an AMI with all AWS accounts \(AWS CLI\)<a name="sharingamis-cli"></a>

Each AMI has a `launchPermission` property that controls which AWS accounts, besides the owner's, are allowed to use that AMI to launch instances\. By modifying the `launchPermission` property of an AMI, you can make the AMI public \(which grants launch permissions to all AWS accounts\), or share it with only the AWS accounts that you specify\.

You can add or remove account IDs from the list of accounts that have launch permissions for an AMI\. To make the AMI public, specify the `all` group\. You can specify both public and explicit launch permissions\.

**To make an AMI public**

1. Use the [modify\-image\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-image-attribute.html) command as follows to add the `all` group to the `launchPermission` list for the specified AMI\.

   ```
   aws ec2 modify-image-attribute \
       --image-id ami-0abcdef1234567890 \
       --launch-permission "Add=[{Group=all}]"
   ```

1. To verify the launch permissions of the AMI, use the [describe\-image\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-image-attribute.html) command\.

   ```
   aws ec2 describe-image-attribute \
       --image-id ami-0abcdef1234567890 \
       --attribute launchPermission
   ```

1. \(Optional\) To make the AMI private again, remove the `all` group from its launch permissions\. Note that the owner of the AMI always has launch permissions and is therefore unaffected by this command\.

   ```
   aws ec2 modify-image-attribute \
       --image-id ami-0abcdef1234567890 \
       --launch-permission "Remove=[{Group=all}]"
   ```