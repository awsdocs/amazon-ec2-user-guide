# Storage<a name="Storage"></a>

Amazon EC2 provides you with flexible, cost effective, and easy\-to\-use data storage options for your instances\. Each option has a unique combination of performance and durability\. These storage options can be used independently or in combination to suit your requirements\.

After reading this section, you should have a good understanding about how you can use the data storage options supported by Amazon EC2 to meet your specific requirements\. These storage options include the following:
+ [Amazon Elastic Block Store \(Amazon EBS\)](AmazonEBS.md)
+ [Amazon EC2 Instance Store](InstanceStorage.md)
+ [Amazon Elastic File System \(Amazon EFS\)](AmazonEFS.md)
+ [Amazon Simple Storage Service \(Amazon S3\)](AmazonS3.md)

The following figure shows the relationship between these storage options and your instance\.

![\[Storage options for Amazon EC2\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/architecture_storage.png)

**Amazon EBS**  
Amazon EBS provides durable, block\-level storage volumes that you can attach to a running instance\. You can use Amazon EBS as a primary storage device for data that requires frequent and granular updates\. For example, Amazon EBS is the recommended storage option when you run a database on an instance\.

An EBS volume behaves like a raw, unformatted, external block device that you can attach to a single instance\. The volume persists independently from the running life of an instance\. After an EBS volume is attached to an instance, you can use it like any other physical hard drive\. As illustrated in the previous figure, multiple volumes can be attached to an instance\. You can also detach an EBS volume from one instance and attach it to another instance\. You can dynamically change the configuration of a volume attached to an instance\. EBS volumes can also be created as encrypted volumes using the Amazon EBS encryption feature\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.

To keep a backup copy of your data, you can create a *snapshot* of an EBS volume, which is stored in Amazon S3\. You can create an EBS volume from a snapshot, and attach it to another instance\. For more information, see [Amazon Elastic Block Store \(Amazon EBS\)](AmazonEBS.md)\.

**Amazon EC2 instance store**  
Many instances can access storage from disks that are physically attached to the host computer\. This disk storage is referred to as *instance store*\. Instance store provides temporary block\-level storage for instances\. The data on an instance store volume persists only during the life of the associated instance; if you stop or terminate an instance, any data on instance store volumes is lost\. For more information, see [Amazon EC2 Instance Store](InstanceStorage.md)\.

**Amazon EFS file system**  
Amazon EFS provides scalable file storage for use with Amazon EC2\. You can create an EFS file system and configure your instances to mount the file system\. You can use an EFS file system as a common data source for workloads and applications running on multiple instances\. For more information, see [Amazon Elastic File System \(Amazon EFS\)](AmazonEFS.md)\.

**Amazon S3**  
Amazon S3 provides access to reliable and inexpensive data storage infrastructure\. It is designed to make web\-scale computing easier by enabling you to store and retrieve any amount of data, at any time, from within Amazon EC2 or anywhere on the web\. For example, you can use Amazon S3 to store backup copies of your data and applications\. Amazon EC2 uses Amazon S3 to store EBS snapshots and instance store\-backed AMIs\. For more information, see [Amazon Simple Storage Service \(Amazon S3\)](AmazonS3.md)\.

**Adding storage**  
Every time you launch an instance from an AMI, a root storage device is created for that instance\. The root storage device contains all the information necessary to boot the instance\. You can specify storage volumes in addition to the root device volume when you create an AMI or launch an instance using *block device mapping*\. For more information, see [Block Device Mapping](block-device-mapping-concepts.md)\.

You can also attach EBS volumes to a running instance\. For more information, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

**Storage pricing**

For information about storage pricing, open [AWS Pricing](https://aws.amazon.com/pricing/), scroll down to **Services Pricing**, choose **Storage**, and then choose the storage option to open that storage option's pricing page\. For information about estimating the cost of storage, see the [AWS Pricing Calculator](https://calculator.aws/#/)\.