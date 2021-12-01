# Share an AMI with specific AWS accounts<a name="sharingamis-explicit"></a>

You can share an AMI with specific AWS accounts without making the AMI public\. All you need is the AWS account IDs\.



## Considerations<a name="considerations-for-sharing-AMI-with-accounts"></a>
+ There is no limit to the number of AWS accounts with which an AMI can be shared\.
+ User\-defined tags that you attach to a shared AMI are available only to your AWS account and not to the other accounts that the AMI is shared with\.
+ You can share AMIs that are backed by unencrypted and encrypted snapshots\. However, the encrypted snapshots must be encrypted with a customer managed key\. You can’t share AMIs that are backed by snapshots that are encrypted with the default AWS managed key\. For more information, see [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)\.
+ If you share an AMI that is backed by encrypted snapshots, you must allow the AWS accounts to use the customer managed keys that were used to encrypt the snapshots\. For more information, see [Allow organizations and OUs to use a KMS key](share-amis-with-organizations-and-OUs.md#allow-org-ou-to-use-key)\.
+ AMIs are a regional resource\. When you share an AMI, its only available in that Region\. To make an AMI available in a different Region, copy the AMI to the Region and then share it\. For more information, see [Copy an AMI](CopyingAMIs.md)\.
+ When you share an AMI, users can only launch instances from the AMI\. They can’t delete, share, or modify it\. However, after they have launched an instance using your AMI, they can then create an AMI from their instance\.
+ You are not billed when your AMI is used by other AWS accounts to launch instances\. The accounts that launch instances using the AMI are billed for the instances that they launch\. 

## Share an AMI \(console\)<a name="sharingamis-console"></a>

------
#### [ New console ]

**To grant explicit launch permissions using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\.

1. Select your AMI in the list, and then choose **Actions**, **Edit AMI permissions**\.

1. Choose **Private**\.

1. Under **Shared accounts**, choose **Add account ID**\.

1. For **AWS account ID**, enter the AWS account ID with which you want to share the AMI, and then choose **Share AMI**\.

   To share this AMI with multiple accounts, repeat Steps 5 and 6 until you have added all the required account IDs\.
**Note**  
You do not need to share the Amazon EBS snapshots that an AMI references in order to share the AMI\. Only the AMI itself needs to be shared; the system automatically provides the instance access to the referenced Amazon EBS snapshots for the launch\. However, you do need to share any KMS keys used to encrypt snapshots that the AMI references\. For more information, see [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)\.

1. Choose **Save changes** when you are done\.

1. \(Optional\) To view the AWS account IDs with which you have shared the AMI, select the AMI in the list, and choose the **Permissions** tab\. To find AMIs that are shared with you, see [Find shared AMIs](usingsharedamis-finding.md)\.

------
#### [ Old console ]

**To grant explicit launch permissions using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\.

1. Select your AMI in the list, and then choose **Actions**, **Modify Image Permissions**\.

1. Specify the AWS account number of the user with whom you want to share the AMI in the **AWS Account Number** field, then choose **Add Permission**\.

   To share this AMI with multiple users, repeat this step until you have added all the required users\.
**Note**  
You do not need to share the Amazon EBS snapshots that an AMI references in order to share the AMI\. Only the AMI itself needs to be shared; the system automatically provides the instance access to the referenced Amazon EBS snapshots for the launch\. However, you do need to share any KMS keys used to encrypt snapshots that the AMI references\. For more information, see [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)\.

1. Choose **Save** when you are done\.

1. \(Optional\) To view the AWS account IDs with which you have shared the AMI, select the AMI in the list, and choose the **Permissions** tab\. To find AMIs that are shared with you, see [Find shared AMIs](usingsharedamis-finding.md)\.

------

## Share an AMI \(AWS CLI\)<a name="sharingamis-aws-cli"></a>

Use the [modify\-image\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-image-attribute.html) command \(AWS CLI\) to share an AMI as shown in the following examples\.

**To grant explicit launch permissions**  
The following command grants launch permissions for the specified AMI to the specified AWS account\.

```
aws ec2 modify-image-attribute \
    --image-id ami-0abcdef1234567890 \
    --launch-permission "Add=[{UserId=123456789012}]"
```

**Note**  
You do not need to share the Amazon EBS snapshots that an AMI references in order to share the AMI\. Only the AMI itself needs to be shared; the system automatically provides the instance access to the referenced Amazon EBS snapshots for the launch\. However, you do need to share any KMS keys used to encrypt snapshots that the AMI references\. For more information, see [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)\.

**To remove launch permissions for an account**  
The following command removes launch permissions for the specified AMI from the specified AWS account:

```
aws ec2 modify-image-attribute \
    --image-id ami-0abcdef1234567890 \
    --launch-permission "Remove=[{UserId=123456789012}]"
```

**To remove all launch permissions**  
The following command removes all public and explicit launch permissions from the specified AMI\. Note that the owner of the AMI always has launch permissions and is therefore unaffected by this command\.

```
aws ec2 reset-image-attribute \
    --image-id ami-0abcdef1234567890 \
    --attribute launchPermission
```