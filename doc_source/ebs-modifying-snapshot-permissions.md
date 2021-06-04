# Share an Amazon EBS snapshot<a name="ebs-modifying-snapshot-permissions"></a>

You can modify the permissions of a snapshot if you want to share it with other AWS accounts\. You can share snapshots publicly with all other AWS accounts, or you can share them privately with individual AWS accounts that you specify\. Users that you have authorized can use the snapshots that you share to create their own EBS volumes, while your original snapshot remains unaffected\.

**Important**  
When you share a snapshot, you are giving others access to all of the data on the snapshot\. Share snapshots only with people that you trust with *all* of your snapshot data\.

**Topics**
+ [Before you share a snapshot](#share-snapshot-considerations)
+ [Share a snapshot](#share-unencrypted-snapshot)
+ [Share a KMS key](#share-kms-key)
+ [View snapshots that are shared with you](#view-shared-snapshot)
+ [Use snapshots that are shared with you](#use-shared-snapss)
+ [Determine the use of snapshots that you share](#shared-snapshot-cloudtrail-logging)

## Before you share a snapshot<a name="share-snapshot-considerations"></a>

The following considerations apply to sharing snapshots:
+ Snapshots are constrained to the Region in which they were created\. To share a snapshot with another Region, copy the snapshot to that Region and then share the copy\. For more information, see [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md)\.
+ You can't share snapshots that are encrypted with the default AWS managed key\. You can only share snapshots that are encrypted with a customer managed key\. For more information, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
+ You can share only unencrypted snapshots publicly\.
+ When you share an encrypted snapshot, you must also share the customer managed key used to encrypt the snapshot\. For more information, see [Share a KMS key](#share-kms-key)\.

## Share a snapshot<a name="share-unencrypted-snapshot"></a>

You can share a snapshot using one of the methods described in the section\.

------
#### [ Console ]

**To share a snapshot**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Snapshots** in the navigation pane\.

1. Select the snapshot and then choose **Actions**, **Modify Permissions**\.

1. Make the snapshot public or share it with specific AWS accounts as follows:
   + To make the snapshot public, choose **Public**\.
   + To share the snapshot with one or more AWS accounts, choose **Private**, enter the AWS account ID \(without hyphens\) in **AWS Account Number**, and choose **Add Permission**\. Repeat for any additional AWS accounts\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

The permissions for a snapshot are specified using the `createVolumePermission` attribute of the snapshot\. To make a snapshot public, set the group to `all`\. To share a snapshot with a specific AWS account, set the user to the ID of the AWS account\.

**To share a snapshot publicly**

Use one of the following commands\.
+ [modify\-snapshot\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-snapshot-attribute.html) \(AWS CLI\)

  For `--attribute`, specify `createVolumePermission`\. For `--operation-type`, specify `add`\. For `--group-names`, specify `all`\.

  ```
  $  aws ec2 modify-snapshot --snapshot-id 1234567890abcdef0 --attribute createVolumePermission --operation-type add --group-names all
  ```
+ [Edit\-EC2SnapshotAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2SnapshotAttribute.html) \(AWS Tools for Windows PowerShell\)

  For `-Attribute`, specify `CreateVolumePermission`\. For `-OperationType`, specify `Add`\. For `-GroupName`, specify `all`\.

  ```
  PS C:\>  Edit-EC2SnapshotAttribute -SnapshotId 1234567890abcdef0 -Attribute CreateVolumePermission -OperationType Add -GroupName all
  ```

**To share a snapshot privately**

Use one of the following commands\.
+ [modify\-snapshot\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-snapshot-attribute.html) \(AWS CLI\)

  For `--attribute`, specify `createVolumePermission`\. For `--operation-type`, specify `add`\. For `--user-ids`, specify the 12\-digit IDs of the AWS accounts with which to share the snapshots\.

  ```
  $  aws ec2 modify-snapshot --snapshot-id 1234567890abcdef0 --attribute createVolumePermission --operation-type add --user-ids 123456789012
  ```
+ [Edit\-EC2SnapshotAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2SnapshotAttribute.html) \(AWS Tools for Windows PowerShell\)

  For `-Attribute`, specify `CreateVolumePermission`\. For `-OperationType`, specify `Add`\. For `UserId`, specify the 12\-digit IDs of the AWS accounts with which to share the snapshots\.

  ```
  PS C:\>  Edit-EC2SnapshotAttribute -SnapshotId 1234567890abcdef0 -Attribute CreateVolumePermission -OperationType Add -UserId 123456789012
  ```

------

## Share a KMS key<a name="share-kms-key"></a>

When you share an encrypted snapshot, you must also share the customer managed key used to encrypt the snapshot\. You can apply cross\-account permissions to a customer managed key either when it is created or at a later time\.

Users of your shared customer managed key who are accessing encrypted snapshots must be granted permissions to perform the following actions on the key:
+ `kms:DescribeKey`
+ `kms:CreateGrant`
+ `kms:GenerateDataKey`
+ `kms:ReEncrypt`
+ `kms:Decrypt`

For more information about controlling access to a customer managed key, see [ Using key policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide*\.

**To share customer managed key using the AWS KMS console**

1. Open the AWS KMS console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. Choose **Customer managed keys** in the navigation pane\.

1. In the **Alias** column, choose the alias \(text link\) of the customer managed key that you used to encrypt the snapshot\. The key details open in a new page\.

1. In the **Key policy** section, you see either the *policy view* or the *default view*\. The policy view displays the key policy document\. The default view displays sections for **Key administrators**, **Key deletion**, **Key Use**, and **Other AWS accounts**\. The default view displays if you created the policy in the console and have not customized it\. If the default view is not available, you'll need to manually edit the policy in the policy view\. For more information, see [Viewing a Key Policy \(Console\)](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-viewing.html#key-policy-viewing-console) in the *AWS Key Management Service Developer Guide*\.

   Use either the policy view or the default view, depending on which view you can access, to add one or more AWS account IDs to the policy, as follows:
   + \(Policy view\) Choose **Edit**\. Add one or more AWS account IDs to the following statements: `"Allow use of the key"` and `"Allow attachment of persistent resources"`\. Choose **Save changes**\. In the following example, the AWS account ID `444455556666` is added to the policy\.

     ```
     {
       "Sid": "Allow use of the key",
       "Effect": "Allow",
       "Principal": {"AWS": [
         "arn:aws:iam::111122223333:user/KeyUser",
         "arn:aws:iam::444455556666:root"
       ]},
       "Action": [
         "kms:Encrypt",
         "kms:Decrypt",
         "kms:ReEncrypt*",
         "kms:GenerateDataKey*",
         "kms:DescribeKey"
       ],
       "Resource": "*"
     },
     {
       "Sid": "Allow attachment of persistent resources",
       "Effect": "Allow",
       "Principal": {"AWS": [
         "arn:aws:iam::111122223333:user/KeyUser",
         "arn:aws:iam::444455556666:root"
       ]},
       "Action": [
         "kms:CreateGrant",
         "kms:ListGrants",
         "kms:RevokeGrant"
       ],
       "Resource": "*",
       "Condition": {"Bool": {"kms:GrantIsForAWSResource": true}}
     }
     ```
   + \(Default view\) Scroll down to **Other AWS accounts**\. Choose **Add other AWS accounts** and enter the AWS account ID as prompted\. To add another account, choose **Add another AWS account** and enter the AWS account ID\. When you have added all AWS accounts, choose **Save changes**\.

## View snapshots that are shared with you<a name="view-shared-snapshot"></a>

You can view snapshots that are shared with you using one of the following methods\.

------
#### [ Console ]

**To view shared snapshots using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Filter the listed snapshots\. In the top\-left corner of the screen, choose one of the following options:
   + **Private snapshots** — To view only snapshots that are shared with you privately\.
   + **Public snapshots** — To view only snapshots that are shared with you publicly\.

------
#### [ AWS CLI ]

**To view snapshot permissions using the command line**

Use one of the following commands:
+ [describe\-snapshot\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshot-attribute.html) \(AWS CLI\)
+ [Get\-EC2SnapshotAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SnapshotAttribute.html) \(AWS Tools for Windows PowerShell\)

------

## Use snapshots that are shared with you<a name="use-shared-snapss"></a>

**To use a shared unencrypted snapshot**  
Locate the shared snapshot by ID or description\. For more information, see [View snapshots that are shared with you](#view-shared-snapshot)\. You can use this snapshot as you would any other snapshot that you own in your account\. For example, you can create a volume from the snapshot or copy it to a different Region\.

**To use a shared encrypted snapshot**  
Locate the shared snapshot by ID or description\. For more information, see [View snapshots that are shared with you](#view-shared-snapshot)\. Create a copy of the shared snapshot in your account, and encrypt the copy with a KMS key that you own\. You can then use the copy to create volumes or you can copy it to different Regions\.

## Determine the use of snapshots that you share<a name="shared-snapshot-cloudtrail-logging"></a>

You can use AWS CloudTrail to monitor whether a snapshot that you have shared with others is copied or used to create a volume\. The following events are logged in CloudTrail:
+ **SharedSnapshotCopyInitiated** — A shared snapshot is being copied\.
+ **SharedSnapshotVolumeCreated** — A shared snapshot is being used to create a volume\.

For more information about using CloudTrail, see [Log Amazon EC2 and Amazon EBS API calls with AWS CloudTrail](monitor-with-cloudtrail.md)\.