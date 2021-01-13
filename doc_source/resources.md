# Resource locations<a name="resources"></a>

Some resources can be used in all AWS Regions \(global\), and some resources are specific to the AWS Region or Availability Zone in which they reside\. 


| Resource | Type | Description | 
| --- | --- | --- | 
|  AWS account  |  Global  |  You can use the same AWS account in all Regions\.  | 
|  Key pairs  |  Global or Regional  |  The key pairs that you create using Amazon EC2 are tied to the Region where you created them\. You can create your own RSA key pair and upload it to the Region in which you want to use it; therefore, you can make your key pair globally available by uploading it to each Region\. For more information, see [Amazon EC2 key pairs and Linux instances](ec2-key-pairs.md)\.  | 
|  Amazon EC2 resource identifiers  |  Regional  |  Each resource identifier, such as an AMI ID, instance ID, EBS volume ID, or EBS snapshot ID, is tied to its Region and can be used only in the Region where you created the resource\.  | 
|  User\-supplied resource names  |  Regional  |  Each resource name, such as a security group name or key pair name, is tied to its Region and can be used only in the Region where you created the resource\. Although you can create resources with the same name in multiple Regions, they aren't related to each other\.  | 
|  AMIs  |  Regional  |  An AMI is tied to the Region where its files are located within Amazon S3\. You can copy an AMI from one Region to another\. For more information, see [Copy an AMI](CopyingAMIs.md)\.  | 
|  Elastic IP addresses  |  Regional  |  An Elastic IP address is tied to a Region and can be associated only with an instance in the same Region\.  | 
|  Security groups  |  Regional  |  A security group is tied to a Region and can be assigned only to instances in the same Region\. You can't enable an instance to communicate with an instance outside its Region using security group rules\. Traffic from an instance in another Region is seen as WAN bandwidth\.  | 
|  EBS snapshots  |  Regional  |  An EBS snapshot is tied to its Region and can only be used to create volumes in the same Region\. You can copy a snapshot from one Region to another\. For more information, see [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md)\.  | 
|  EBS volumes  |  Availability Zone  |  An Amazon EBS volume is tied to its Availability Zone and can be attached only to instances in the same Availability Zone\.  | 
|  Instances  |  Availability Zone  |  An instance is tied to the Availability Zones in which you launched it\. However, its instance ID is tied to the Region\.  | 