# Resource Locations<a name="resources"></a>

Some resources can be used in all regions \(global\), and some resources are specific to the region or Availability Zone in which they reside\. 


| Resource | Type | Description | 
| --- | --- | --- | 
|  AWS account  |  Global  |  You can use the same AWS account in all regions\.  | 
|  Key pairs  |  Global or Regional  |  The key pairs that you create using Amazon EC2 are tied to the region where you created them\. You can create your own RSA key pair and upload it to the region in which you want to use it; therefore, you can make your key pair globally available by uploading it to each region\. For more information, see [Amazon EC2 key pairs](ec2-key-pairs.md)\.  | 
|  Amazon EC2 resource identifiers  |  Regional  |  Each resource identifier, such as an AMI ID, instance ID, EBS volume ID, or EBS snapshot ID, is tied to its region and can be used only in the region where you created the resource\.  | 
|  User\-supplied resource names  |  Regional  |  Each resource name, such as a security group name or key pair name, is tied to its region and can be used only in the region where you created the resource\. Although you can create resources with the same name in multiple regions, they aren't related to each other\.  | 
|  AMIs  |  Regional  |  An AMI is tied to the region where its files are located within Amazon S3\. You can copy an AMI from one region to another\. For more information, see [Copying an AMI](CopyingAMIs.md)\.  | 
|  Elastic IP addresses  |  Regional  |  An Elastic IP address is tied to a region and can be associated only with an instance in the same region\.  | 
|  Security groups  |  Regional  |  A security group is tied to a region and can be assigned only to instances in the same region\. You can't enable an instance to communicate with an instance outside its region using security group rules\. Traffic from an instance in another region is seen as WAN bandwidth\.  | 
|  EBS snapshots  |  Regional  |  An EBS snapshot is tied to its region and can only be used to create volumes in the same region\. You can copy a snapshot from one region to another\. For more information, see [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)\.  | 
|  EBS volumes  |  Availability Zone  |  An Amazon EBS volume is tied to its Availability Zone and can be attached only to instances in the same Availability Zone\.  | 
|  Instances  |  Availability Zone  |  An instance is tied to the Availability Zones in which you launched it\. However, its instance ID is tied to the region\.  | 