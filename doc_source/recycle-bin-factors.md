# Considerations<a name="recycle-bin-factors"></a>

The following considerations apply when working with Recycle Bin and retention rules\.

**General considerations**
+ 
**Important**  
When you create your first retention rule, it can take up to 30 minutes for the rule to become active and for it to start retaining resources\. After you create the first retention rule, subsequent retention rules become active and start retaining resources almost immediately\.
+ If a resource matches more than one retention rule upon deletion, then the retention rule with the longest retention period takes precedence\.
+ You can't manually delete a resource from the Recycle Bin\. The resource will be automatically deleted when its retention period expires\.
+ While a resource is in the Recycle Bin, you can only view it, restore it, or modify its tags\. To use the resource in any other way, you must first restore it\.
+ If any AWS service, such as AWS Backup or Amazon Data Lifecycle Manager, deletes a resource that matches a retention rule, that resource is automatically retained by Recycle Bin\.
+ When a resource is sent to the Recycle Bin, the following system\-generate tag is assigned to the resource:
  + Tag key — `aws:recycle-bin:resource-in-bin`
  + Tag value — `true`

  You can't manually edit or delete this tag\. When the resource is restored from the Recycle Bin, the tag is automatically removed\.

**Considerations for snapshots**
+ 
**Important**  
If you have retention rules for AMIs and for their associated snapshots, make the retention period for the snapshots the same or longer than the retention period for the AMIs\. This ensures that Recycle Bin does not delete the snapshots associated with an AMI before deleting the AMI itself, as this would make the AMI unrecoverable\.
+ If a snapshot is enabled for fast snapshot restore when it is deleted, fast snapshot restore is automatically disabled shortly after the snapshot is sent to the Recycle Bin\. 
  + If you restore the snapshot before fast snapshot restore is disabled for the snapshot, it remains enabled\.
  + If you restore the snapshot, after fast snapshot restore has been disabled, it remains disabled\. If needed, you must manually re\-enable fast snapshot restore\.
+ If a snapshot is shared when it is deleted, it is automatically unshared when it is sent to the Recycle Bin\. If you restore the snapshot, all of the previous sharing permissions are automatically restored\.
+ If a snapshot that was created by another AWS service, such as AWS Backup is sent to the Recycle Bin and you later restore that snapshot from the Recycle Bin, it is no longer managed by the AWS service that created it\. You must manually delete the snapshot if it is no longer needed\.

**Considerations for AMIs**
+ Only Amazon EBS\-backed AMIs are supported\.
+ 
**Important**  
If you have retention rules for AMIs and for their associated snapshots, make the retention period for the snapshots the same or longer than the retention period for the AMIs\. This ensures that Recycle Bin does not delete the snapshots associated with an AMI before deleting the AMI itself, as this would make the AMI unrecoverable\.
+ If an AMI is shared when it is deleted, it is automatically unshared when it is sent to the Recycle Bin\. If you restore the AMI, all of the previous sharing permissions are automatically restored\.
+ Before you can restore an AMI from the Recycle Bin, you must first restore all of its associated snapshots from the Recycle Bin and ensure that they are in the `available` state\.
+ If the snapshots that are associated with the AMI are deleted from the Recycle Bin, the AMI is no longer recoverable\. The AMI will be deleted when the retention period expires\.
+ If an AMI that was created by another AWS service, such as AWS Backup, is sent to the Recycle Bin and you later restore that AMI from the Recycle Bin, it is no longer managed by the AWS service that created it\. You must manually delete the AMI if it is no longer needed\.

**Considerations for Amazon Data Lifecycle Manager snapshot policies**
+ If Amazon Data Lifecycle Manager deletes a snapshot and sends it to the Recycle Bin when the policy's retention threshold is reached, and you manually restore the snapshot from the Recycle Bin, you must manually delete that snapshot when it is no longer needed\. Amazon Data Lifecycle Manager will no longer manage the snapshot\.
+ If you manually delete a snapshot that was created by a policy, and that snapshot is in the Recycle Bin when the policy’s retention threshold is reached, Amazon Data Lifecycle Manager will not delete the snapshot\. Amazon Data Lifecycle Manager does not manage the snapshots while they are stored in the Recycle Bin\.

  If the snapshot is restored from the Recycle Bin before the policy's retention threshold is reached, Amazon Data Lifecycle Manager will delete the snapshot when the policy's retention threshold is reached\.

  If the snapshot is restored from the Recycle Bin after the policy's retention threshold is reached, Amazon Data Lifecycle Manager will no longer delete the snapshot\. You must manually delete the snapshot when it is no longer needed\.

**Considerations for AWS Backup**
+ If AWS Backup deletes a snapshot that matches a retention rule, that snapshot is automatically retained by Recycle Bin\.