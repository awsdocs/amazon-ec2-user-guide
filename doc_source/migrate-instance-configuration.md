# Migrate to a new instance configuration<a name="migrate-instance-configuration"></a>

If the current configuration of your instance is incompatible with the new instance type that you want, then you can't resize the instance to that instance type\. Instead, you can migrate your application to a new instance with a configuration that is compatible with the new instance type that you want\.

If you want to move from an instance launched from a PV AMI to an instance type that is HVM only, the general process is as follows:

------
#### [ New console ]

**To migrate your application to a compatible instance**

1. Back up any data on your instance store volumes that you need to keep to persistent storage\. To migrate data on your EBS volumes that you need to keep, create a snapshot of the volumes \(see [Create Amazon EBS snapshots](ebs-creating-snapshot.md)\) or detach the volume from the instance so that you can attach it to the new instance later \(see [Detach an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\)\.

1. Launch a new instance, selecting the following:
   + An HVM AMI\.
   + The HVM only instance type\.
   + If you are using an Elastic IP address, select the VPC that the original instance is currently running in\.
   + Any EBS volumes that you detached from the original instance and want to attach to the new instance, or new EBS volumes based on the snapshots that you created\.
   + If you want to allow the same traffic to reach the new instance, select the security group that is associated with the original instance\.

1. Install your application and any required software on the instance\.

1. Restore any data that you backed up from the instance store volumes of the original instance\.

1. If you are using an Elastic IP address, assign it to the newly launched instance as follows:

   1. In the navigation pane, choose **Elastic IPs**\.

   1. Select the Elastic IP address that is associated with the original instance and choose **Actions**, **Disassociate Elastic IP address**\. When prompted for confirmation, choose **Disassociate**\.

   1. With the Elastic IP address still selected, choose **Actions**, **Associate Elastic IP address**\.

   1. For **Resource type**, choose **Instance**\. 

   1. For **Instance**, choose the instance with which to associate the Elastic IP address\. You can also enter text to search for a specific instance\.

   1. \(Optional\) For **Private IP address**, specify a private IP address with which to associate the Elastic IP address\.

   1. Choose **Associate**\.

1. \(Optional\) You can terminate the original instance if it's no longer needed\. Select the instance and verify that you are about to terminate the original instance, not the new instance \(for example, check the name or launch time\)\. Choose **Instance state**, **Terminate instance**\.

------
#### [ Old console ]

**To migrate your application to a compatible instance**

1. Back up any data on your instance store volumes that you need to keep to persistent storage\. To migrate data on your EBS volumes that you need to keep, create a snapshot of the volumes \(see [Create Amazon EBS snapshots](ebs-creating-snapshot.md)\) or detach the volume from the instance so that you can attach it to the new instance later \(see [Detach an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\)\.

1. Launch a new instance, selecting the following:
   + An HVM AMI\.
   + The HVM only instance type\.
   + If you are using an Elastic IP address, select the VPC that the original instance is currently running in\.
   + Any EBS volumes that you detached from the original instance and want to attach to the new instance, or new EBS volumes based on the snapshots that you created\.
   + If you want to allow the same traffic to reach the new instance, select the security group that is associated with the original instance\.

1. Install your application and any required software on the instance\.

1. Restore any data that you backed up from the instance store volumes of the original instance\.

1. If you are using an Elastic IP address, assign it to the newly launched instance as follows:

   1. In the navigation pane, choose **Elastic IPs**\.

   1. Select the Elastic IP address that is associated with the original instance and choose **Actions**, **Disassociate address**\. When prompted for confirmation, choose **Disassociate address**\.

   1. With the Elastic IP address still selected, choose **Actions**, **Associate address**\.

   1. From **Instance**, select the new instance, and then choose **Associate**\.

1. \(Optional\) You can terminate the original instance if it's no longer needed\. Select the instance and verify that you are about to terminate the original instance, not the new instance \(for example, check the name or launch time\)\. Choose **Actions**, **Instance State**, **Terminate**\.

------