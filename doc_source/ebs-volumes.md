# Amazon EBS Volumes<a name="ebs-volumes"></a>

An Amazon EBS volume is a durable, block\-level storage device that you can attach to one or more instances\. You can use EBS volumes as primary storage for data that requires frequent updates, such as the system drive for an instance or storage for a database application\. You can also use them for throughput\-intensive applications that perform continuous disk scans\. EBS volumes persist independently from the running life of an EC2 instance\. 

After a volume is attached to an instance, you can use it like any other physical hard drive\. EBS volumes are flexible\. For current\-generation volumes attached to current\-generation instance types, you can dynamically increase size, modify the provisioned IOPS capacity, and change volume type on live production volumes\. 

Amazon EBS provides the following volume types: General Purpose SSD \(`gp2`\), Provisioned IOPS SSD \(`io1`\), Throughput Optimized HDD \(`st1`\), Cold HDD \(`sc1`\), and Magnetic \(`standard`, a previous\-generation type\)\. They differ in performance characteristics and price, allowing you to tailor your storage performance and cost to the needs of your applications\. For more information, see [Amazon EBS Volume Types](ebs-volume-types.md)\.

**Topics**
+ [Benefits of Using EBS Volumes](#EBSFeatures)
+ [Amazon EBS Volume Types](ebs-volume-types.md)
+ [Constraints on the Size and Configuration of an EBS Volume](volume_constraints.md)
+ [Creating an Amazon EBS Volume](ebs-creating-volume.md)
+ [Restoring an Amazon EBS Volume from a Snapshot](ebs-restoring-volume.md)
+ [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)
+ [Attaching a Volume to Multiple Instances with Amazon EBS Multi\-Attach](ebs-volumes-multi.md)
+ [Making an Amazon EBS Volume Available for Use on Linux](ebs-using-volumes.md)
+ [Viewing Information about an Amazon EBS Volume](ebs-describing-volumes.md)
+ [Monitoring the Status of Your Volumes](monitoring-volume-status.md)
+ [Detaching an Amazon EBS Volume from an Instance](ebs-detaching-volume.md)
+ [Deleting an Amazon EBS Volume](ebs-deleting-volume.md)

## Benefits of Using EBS Volumes<a name="EBSFeatures"></a>

EBS volumes provide several benefits that are not supported by instance store volumes\.
+ **Data availability**

  When you create an EBS volume in an Availability Zone, it is automatically replicated within that zone to prevent data loss due to failure of any single hardware component\. After you create a volume, you can attach it to any EC2 instance in the same Availability Zone\. After you attach a volume, it appears as a native block device similar to a hard drive or other physical device\. At that point, the instance can interact with the volume just as it would with a local drive\. The instance can format the EBS volume with a file system, such as ext3, and then install applications\. 

  You can attach an EBS volume to one or more instances\. You can also attach multiple EBS volumes to a single instance\. If you attach multiple volumes to a device that you have named, you can stripe data across the volumes for increased I/O and throughput performance\.

  An EBS volume and the instance to which it attaches must be in the same Availability Zone\.

  You can get monitoring data for your EBS volumes, including root device volumes for EBS\-backed instances, at no additional charge\. For more information about monitoring metrics, see [Amazon CloudWatch Metrics for Amazon EBS](using_cloudwatch_ebs.md)\. For information about tracking the status of your volumes, see [Amazon CloudWatch Events for Amazon EBS](ebs-cloud-watch-events.md)\.
+ **Data persistence**

  An EBS volume is off\-instance storage that can persist independently from the life of an instance\. You continue to pay for the volume usage as long as the data persists\. 

  EBS volumes that are attached to a running instance can automatically detach from the instance with their data intact when the instance is terminated if you uncheck the **Delete on Termination** checkbox when you configure EBS volumes for your instance on the EC2 console\. The volume can then be reattached to a new instance, enabling quick recovery\. If the checkbox for **Delete on Termination** is checked, the volume\(s\) will delete upon termination of the EC2 instance\. If you are using an EBS\-backed instance, you can stop and restart that instance without affecting the data stored in the attached volume\. The volume remains attached throughout the stop\-start cycle\. This enables you to process and store the data on your volume indefinitely, only using the processing and storage resources when required\. The data persists on the volume until the volume is deleted explicitly\. The physical block storage used by deleted EBS volumes is overwritten with zeroes before it is allocated to another account\. If you are dealing with sensitive data, you should consider encrypting your data manually or storing the data on a volume protected by Amazon EBS encryption\. For more information, see [Amazon EBS Encryption](EBSEncryption.md)\.

  By default, the root EBS volume that is created and attached to an instance at launch is deleted when that instance is terminated\. You can modify this behavior by changing the value of the flag `DeleteOnTermination` to `false` when you launch the instance\. This modified value causes the volume to persist even after the instance is terminated, and enables you to attach the volume to another instance\. 

  By default, additional EBS volumes that are created and attached to an instance at launch are not deleted when that instance is terminated\. You can modify this behavior by changing the value of the flag `DeleteOnTermination` to `true` when you launch the instance\. This modified value causes the volumes to be deleted when the instance is terminated\. 
+ **Data encryption**

  For simplified data encryption, you can create encrypted EBS volumes with the Amazon EBS encryption feature\. All EBS volume types support encryption\. You can use encrypted EBS volumes to meet a wide range of data\-at\-rest encryption requirements for regulated/audited data and applications\. Amazon EBS encryption uses 256\-bit Advanced Encryption Standard algorithms \(AES\-256\) and an Amazon\-managed key infrastructure\. The encryption occurs on the server that hosts the EC2 instance, providing encryption of data\-in\-transit from the EC2 instance to Amazon EBS storage\. For more information, see [Amazon EBS Encryption](EBSEncryption.md)\. 

   Amazon EBS encryption uses AWS Key Management Service \(AWS KMS\) master keys when creating encrypted volumes and any snapshots created from your encrypted volumes\. The first time you create an encrypted EBS volume in a region, a default master key is created for you automatically\. This key is used for Amazon EBS encryption unless you select a customer master key \(CMK\) that you created separately using AWS KMS\. Creating your own CMK gives you more flexibility, including the ability to create, rotate, disable, define access controls, and audit the encryption keys used to protect your data\. For more information, see the [AWS Key Management Service Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/)\. 
+ **Snapshots**

  Amazon EBS provides the ability to create snapshots \(backups\) of any EBS volume and write a copy of the data in the volume to Amazon S3, where it is stored redundantly in multiple Availability Zones\. The volume does not need to be attached to a running instance in order to take a snapshot\. As you continue to write data to a volume, you can periodically create a snapshot of the volume to use as a baseline for new volumes\. These snapshots can be used to create multiple new EBS volumes or move volumes across Availability Zones\. Snapshots of encrypted EBS volumes are automatically encrypted\. 

  When you create a new volume from a snapshot, it's an exact copy of the original volume at the time the snapshot was taken\. EBS volumes that are restored from encrypted snapshots are automatically encrypted\. By optionally specifying a different Availability Zone, you can use this functionality to create a duplicate volume in that zone\. The snapshots can be shared with specific AWS accounts or made public\. When you create snapshots, you incur charges in Amazon S3 based on the volume's total size\. For a successive snapshot of the volume, you are only charged for any additional data beyond the volume's original size\. 

  Snapshots are incremental backups, meaning that only the blocks on the volume that have changed after your most recent snapshot are saved\. If you have a volume with 100 GiB of data, but only 5 GiB of data have changed since your last snapshot, only the 5 GiB of modified data is written to Amazon S3\. Even though snapshots are saved incrementally, the snapshot deletion process is designed so that you need to retain only the most recent snapshot in order to restore the volume\. 

  To help categorize and manage your volumes and snapshots, you can tag them with metadata of your choice\. For more information, see [Tagging Your Amazon EC2 Resources](Using_Tags.md)\. 
+ **Flexibility**

   EBS volumes support live configuration changes while in production\. You can modify volume type, volume size, and IOPS capacity without service interruptions\. 