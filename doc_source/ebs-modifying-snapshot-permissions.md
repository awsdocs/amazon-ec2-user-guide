# Sharing an Amazon EBS Snapshot<a name="ebs-modifying-snapshot-permissions"></a>

By modifying the permissions of the snapshot,you can share your unencrypted snapshots with your co\-workers or others in the AWS community Users that you have authorized can use your unencrypted shared snapshots as the basis for creating their own EBS volumes\. If you choose, you can also make your unencrypted snapshots available publicly to all AWS users\. 

You can share an encrypted snapshot with specific AWS accounts, though you cannot make it public\. For others to use the snapshot, you must also share the custom CMK key used to encrypt it\. Cross\-account permissions may be applied to a custom key either when it is created or at a later time\. Users with access can copy your snapshot and create their own EBS volumes based on your snapshot while your original snapshot remains unaffected\. 

**Important**  
When you share a snapshot \(whether by sharing it with another AWS account or making it public to all\), you are giving others access to all the data on the snapshot\. Share snapshots only with people with whom you want to share *all* your snapshot data\. 

Several technical and policy restrictions apply to sharing snapshots:

+ Snapshots are constrained to the region in which they were created\. To share a snapshot with another region, copy the snapshot to that region\. For more information about copying snapshots, see [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)\.

+ If your snapshot uses the longer resource ID format, you can only share it with another account that also supports longer IDs\. For more information, see [Resource IDs](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/resource-ids.html)\.

+ AWS prevents you from sharing snapshots that were encrypted with your default CMK\. Snapshots that you intend to share must instead be encrypted with a custom CMK\. For information about creating keys, see [Creating Keys](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html)\. 

+ Users of your shared CMK who are accessing encrypted snapshots must be granted `DescribeKey` and `ReEncrypt` permissions\. For information about managing and sharing CMK keys, see [Controlling Access to Customer Master Keys](http://docs.aws.amazon.com/kms/latest/developerguide/control-access.html)\.

+ If you have access to a shared encrypted snapshot and you want to restore a volume from it, you must create a personal copy of the snapshot and then use that copy to restore the volume\. We recommend that you re\-encrypt the snapshot during the copy process with a different key that you control\. This protects your access to the volume if the original key is compromised, or if the owner revokes the key for any reason\.

**To modify snapshot permissions using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Snapshots** in the navigation pane\. 

1. Select a snapshot and then choose **Modify Permissions** from the **Actions** list\.

1. Choose whether to make the snapshot public or to share it with specific AWS accounts:

   1. To make the snapshot public, choose **Public**\.

      This is not a valid option for encrypted snapshots or snapshots with AWS Marketplace product codes\.

   1. To expose the snapshot to only specific AWS accounts, choose **Private**, enter the ID of the AWS account \(without hyphens\) in the **AWS Account Number** field, and choose **Add Permission**\. Repeat until you've added all the required AWS accounts\. 
**Important**  
If your snapshot is encrypted, you must ensure that the following are true:  
The snapshot is encrypted with a custom CMK, not your default CMK\. If you attempt to change the permissions of a snapshot encrypted with your default CMK, the console displays an error message\.
You are sharing the custom CMK with the accounts that have access to your snapshot\.

1. Choose **Save**\. Now a user logged into the permitted account can locate the shared snapshot by choosing **Private Snapshots** in the filter menu\.

**To view and modify snapshot permissions using the command line**

To view the `createVolumePermission` attribute of a snapshot, you can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [describe\-snapshot\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshot-attribute.html) \(AWS CLI\)

+ [Get\-EC2SnapshotAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SnapshotAttribute.html) \(AWS Tools for Windows PowerShell\)

To modify the `createVolumePermission` attribute of a snapshot, you can use one of the following commands\.

+ [modify\-snapshot\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-snapshot-attribute.html) \(AWS CLI\)

+ [Edit\-EC2SnapshotAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2SnapshotAttribute.html) \(AWS Tools for Windows PowerShell\)