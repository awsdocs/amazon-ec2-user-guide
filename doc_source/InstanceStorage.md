# Amazon EC2 instance store<a name="InstanceStorage"></a>

An *instance store* provides temporary block\-level storage for your instance\. This storage is located on disks that are physically attached to the host computer\. Instance store is ideal for temporary storage of information that changes frequently, such as buffers, caches, scratch data, and other temporary content, or for data that is replicated across a fleet of instances, such as a load\-balanced pool of web servers\.

An instance store consists of one or more instance store volumes exposed as block devices\. The size of an instance store as well as the number of devices available varies by instance type\.

The virtual devices for instance store volumes are `ephemeral[0-23]`\. Instance types that support one instance store volume have `ephemeral0`\. Instance types that support two instance store volumes have `ephemeral0` and `ephemeral1`, and so on\.

![\[Amazon EC2 instance storage\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/instance_storage.png)

**Topics**
+ [Instance store lifetime](#instance-store-lifetime)
+ [Instance store volumes](#instance-store-volumes)
+ [Add instance store volumes to your EC2 instance](add-instance-store-volumes.md)
+ [SSD instance store volumes](ssd-instance-store.md)
+ [Instance store swap volumes](instance-store-swap-volumes.md)
+ [Optimize disk performance for instance store volumes](disk-performance.md)

## Instance store lifetime<a name="instance-store-lifetime"></a>

You can specify instance store volumes for an instance only when you launch it\. You can't detach an instance store volume from one instance and attach it to a different instance\.

The data in an instance store persists only during the lifetime of its associated instance\. If an instance reboots \(intentionally or unintentionally\), data in the instance store persists\. However, data in the instance store is lost under any of the following circumstances:
+ The underlying disk drive fails
+ The instance stops
+ The instance hibernates
+ The instance terminates

Therefore, do not rely on instance store for valuable, long\-term data\. Instead, use more durable data storage, such as Amazon S3, Amazon EBS, or Amazon EFS\.

When you stop, hibernate, or terminate an instance, every block of storage in the instance store is reset\. Therefore, your data cannot be accessed through the instance store of another instance\.

If you create an AMI from an instance, the data on its instance store volumes isn't preserved and isn't present on the instance store volumes of the instances that you launch from the AMI\.

If you change the instance type, an instance store will not be attached to the new instance type\. For more information, see [Change the instance type](ec2-instance-resize.md)\.

## Instance store volumes<a name="instance-store-volumes"></a>

The instance type determines the size of the instance store available and the type of hardware used for the instance store volumes\. Instance store volumes are included as part of the instance's usage cost\. You must specify the instance store volumes that you'd like to use when you launch the instance \(except for NVMe instance store volumes, which are available by default\)\. Then format and mount the instance store volumes before using them\. You can't make an instance store volume available after you launch the instance\. For more information, see [Add instance store volumes to your EC2 instance](add-instance-store-volumes.md)\.

Some instance types use NVMe or SATA\-based solid state drives \(SSD\) to deliver high random I/O performance\. This is a good option when you need storage with very low latency, but you don't need the data to persist when the instance terminates or you can take advantage of fault\-tolerant architectures\. For more information, see [SSD instance store volumes](ssd-instance-store.md)\.

The data on NVMe instance store volumes and some HDD instance store volumes is encrypted at rest\. For more information, see [Data protection in Amazon EC2](data-protection.md)\.

**Available instance store volumes**  
The following table provides the quantity, size, type, and performance optimizations of instance store volumes available on each supported instance type\.

**Topics**
+ [General purpose](#instance-store-vol-gp)
+ [Compute optimized](#instance-store-vol-co)
+ [Memory optimized](#instance-store-vol-mo)
+ [Storage optimized](#instance-store-vol-so)
+ [Accelerated computing](#instance-store-vol-ac)

### General purpose<a name="instance-store-vol-gp"></a>


| Instance type | Instance store volumes | Type | Needs initialization\* | TRIM support\*\* | 
| --- | --- | --- | --- | --- | 
| m1\.small | 1 x 160 GB | HDD | ✔ |   | 
| m1\.medium | 1 x 410 GB | HDD | ✔ |   | 
| m1\.large | 2 x 420 GB | HDD | ✔ |   | 
| m1\.xlarge | 4 x 420 GB | HDD | ✔ |   | 
| m2\.xlarge | 1 x 420 GB | HDD | ✔ |   | 
| m2\.2xlarge | 1 x 850 GB | HDD | ✔ |   | 
| m2\.4xlarge | 2 x 840 GB | HDD | ✔ |   | 
| m3\.medium | 1 x 4 GB | SSD | ✔ |   | 
| m3\.large | 1 x 32 GB | SSD | ✔ |   | 
| m3\.xlarge | 2 x 40 GB | SSD | ✔ |   | 
| m3\.2xlarge | 2 x 80 GB | SSD | ✔ |   | 
| m5ad\.large | 1 x 75 GB | NVMe SSD |   | ✔ | 
| m5ad\.xlarge | 1 x 150 GB | NVMe SSD |   | ✔ | 
| m5ad\.2xlarge | 1 x 300 GB | NVMe SSD |   | ✔ | 
| m5ad\.4xlarge | 2 x 300 GB | NVMe SSD |   | ✔ | 
| m5ad\.8xlarge | 2 x 600 GB | NVMe SSD |   | ✔ | 
| m5ad\.12xlarge | 2 x 900 GB | NVMe SSD |   | ✔ | 
| m5ad\.16xlarge | 4 x 600 GB | NVMe SSD |   | ✔ | 
| m5ad\.24xlarge | 4 x 900 GB | NVMe SSD |   | ✔ | 
| m5d\.large | 1 x 75 GB | NVMe SSD |   | ✔ | 
| m5d\.xlarge | 1 x 150 GB | NVMe SSD |   | ✔ | 
| m5d\.2xlarge | 1 x 300 GB | NVMe SSD |   | ✔ | 
| m5d\.4xlarge | 2 x 300 GB | NVMe SSD |   | ✔ | 
| m5d\.8xlarge | 2 x 600 GB | NVMe SSD |   | ✔ | 
| m5d\.12xlarge | 2 x 900 GB | NVMe SSD |   | ✔ | 
| m5d\.16xlarge | 4 x 600 GB | NVMe SSD |   | ✔ | 
| m5d\.24xlarge | 4 x 900 GB | NVMe SSD |   | ✔ | 
| m5d\.metal | 4 x 900 GB | NVMe SSD |   | ✔ | 
| m5dn\.large | 1 x 75 GB | NVMe SSD |   | ✔ | 
| m5dn\.xlarge | 1 x 150 GB | NVMe SSD |   | ✔ | 
| m5dn\.2xlarge | 1 x 300 GB | NVMe SSD |   | ✔ | 
| m5dn\.4xlarge | 2 x 300 GB | NVMe SSD |   | ✔ | 
| m5dn\.8xlarge | 2 x 600 GB | NVMe SSD |   | ✔ | 
| m5dn\.12xlarge | 2 x 900 GB | NVMe SSD |   | ✔ | 
| m5dn\.16xlarge | 4 x 600 GB | NVMe SSD |   | ✔ | 
| m5dn\.24xlarge | 4 x 900 GB | NVMe SSD |   | ✔ | 
| m5dn\.metal | 4 x 900 GB | NVMe SSD |   | ✔ | 
| m6gd\.medium | 1 x 59 GB | NVMe SSD |   | ✔ | 
| m6gd\.large | 1 x 118 GB | NVMe SSD |   | ✔ | 
| m6gd\.xlarge | 1 x 237 GB | NVMe SSD |   | ✔ | 
| m6gd\.2xlarge | 1 x 474 GB | NVMe SSD |   | ✔ | 
| m6gd\.4xlarge | 1 x 950 GB | NVMe SSD |   | ✔ | 
| m6gd\.8xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| m6gd\.12xlarge | 2 x 1425 GB | NVMe SSD |   | ✔ | 
| m6gd\.16xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| m6gd\.metal | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| m6id\.large | 1 x 118 GB | NVMe SSD |   | ✔ | 
| m6id\.xlarge | 1 x 237 GB | NVMe SSD |   | ✔ | 
| m6id\.2xlarge | 1 x 474 GB | NVMe SSD |   | ✔ | 
| m6id\.4xlarge | 1 x 950 GB | NVMe SSD |   | ✔ | 
| m6id\.8xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| m6id\.12xlarge | 2 x 1425 GB | NVMe SSD |   | ✔ | 
| m6id\.16xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| m6id\.24xlarge | 4 x 1425 GB | NVMe SSD |   | ✔ | 
| m6id\.32xlarge | 4 x 1900 GB | NVMe SSD |   | ✔ | 
| m6id\.metal | 4 x 1900 GB | NVMe SSD |   | ✔ | 
| m6idn\.large | 1 x 118 GB | NVMe SSD |   | ✔ | 
| m6idn\.xlarge | 1 x 237 GB | NVMe SSD |   | ✔ | 
| m6idn\.2xlarge | 1 x 474 GB | NVMe SSD |   | ✔ | 
| m6idn\.4xlarge | 1 x 950 GB | NVMe SSD |   | ✔ | 
| m6idn\.8xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| m6idn\.12xlarge | 2 x 1425 GB | NVMe SSD |   | ✔ | 
| m6idn\.16xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| m6idn\.24xlarge | 4 x 1425 GB | NVMe SSD |   | ✔ | 
| m6idn\.32xlarge | 4 x 1900 GB | NVMe SSD |   | ✔ | 

### Compute optimized<a name="instance-store-vol-co"></a>


| Instance type | Instance store volumes | Type | Needs initialization\* | TRIM support\*\* | 
| --- | --- | --- | --- | --- | 
| c1\.medium | 1 x 350 GB | HDD | ✔ |   | 
| c1\.xlarge | 4 x 420 GB | HDD | ✔ |   | 
| c3\.large | 2 x 16 GB | SSD | ✔ |   | 
| c3\.xlarge | 2 x 40 GB | SSD | ✔ |   | 
| c3\.2xlarge | 2 x 80 GB | SSD | ✔ |   | 
| c3\.4xlarge | 2 x 160 GB | SSD | ✔ |   | 
| c3\.8xlarge | 2 x 320 GB | SSD | ✔ |   | 
| c5ad\.large | 1 x 75 GB | NVMe SSD |   | ✔ | 
| c5ad\.xlarge | 1 x 150 GB | NVMe SSD |   | ✔ | 
| c5ad\.2xlarge | 1 x 300 GB | NVMe SSD |   | ✔ | 
| c5ad\.4xlarge | 2 x 300 GB | NVMe SSD |   | ✔ | 
| c5ad\.8xlarge | 2 x 600 GB | NVMe SSD |   | ✔ | 
| c5ad\.12xlarge | 2 x 900 GB | NVMe SSD |   | ✔ | 
| c5ad\.16xlarge | 2 x 1200 GB | NVMe SSD |   | ✔ | 
| c5ad\.24xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| c5d\.large | 1 x 50 GB | NVMe SSD |   | ✔ | 
| c5d\.xlarge | 1 x 100 GB | NVMe SSD |   | ✔ | 
| c5d\.2xlarge | 1 x 200 GB | NVMe SSD |   | ✔ | 
| c5d\.4xlarge | 1 x 400 GB | NVMe SSD |   | ✔ | 
| c5d\.9xlarge | 1 x 900 GB | NVMe SSD |   | ✔ | 
| c5d\.12xlarge | 2 x 900 GB | NVMe SSD |   | ✔ | 
| c5d\.18xlarge | 2 x 900 GB | NVMe SSD |   | ✔ | 
| c5d\.24xlarge | 4 x 900 GB | NVMe SSD |   | ✔ | 
| c5d\.metal | 4 x 900 GB | NVMe SSD |   | ✔ | 
| c6gd\.medium | 1 x 59 GB | NVMe SSD |   | ✔ | 
| c6gd\.large | 1 x 118 GB | NVMe SSD |   | ✔ | 
| c6gd\.xlarge | 1 x 237 GB | NVMe SSD |   | ✔ | 
| c6gd\.2xlarge | 1 x 474 GB | NVMe SSD |   | ✔ | 
| c6gd\.4xlarge | 1 x 950 GB | NVMe SSD |   | ✔ | 
| c6gd\.8xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| c6gd\.12xlarge | 2 x 1425 GB | NVMe SSD |   | ✔ | 
| c6gd\.16xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| c6gd\.metal | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| c6id\.large | 1 x 118 GB | NVMe SSD |   | ✔ | 
| c6id\.xlarge | 1 x 237 GB | NVMe SSD |   | ✔ | 
| c6id\.2xlarge | 1 x 474 GB | NVMe SSD |   | ✔ | 
| c6id\.4xlarge | 1 x 950 GB | NVMe SSD |   | ✔ | 
| c6id\.8xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| c6id\.12xlarge | 2 x 1425 GB | NVMe SSD |   | ✔ | 
| c6id\.16xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| c6id\.24xlarge | 4 x 1425 GB | NVMe SSD |   | ✔ | 
| c6id\.32xlarge | 4 x 1900 GB | NVMe SSD |   | ✔ | 
| c6id\.metal | 4 x 1900 GB | NVMe SSD |   | ✔ | 
| cc2\.8xlarge | 4 x 840 GB | HDD | ✔ |   | 

### Memory optimized<a name="instance-store-vol-mo"></a>


| Instance type | Instance store volumes | Type | Needs initialization\* | TRIM support\*\* | 
| --- | --- | --- | --- | --- | 
| cr1\.8xlarge | 2 x 120 GB | HDD | ✔ |   | 
| hpc6id\.32xlarge | 4 x 3800 GB | NVMe SSD |   | ✔ | 
| r3\.large | 1 x 32 GB | SSD | ✔ |   | 
| r3\.xlarge | 1 x 80 GB | SSD | ✔ |   | 
| r3\.2xlarge | 1 x 160 GB | SSD | ✔ |   | 
| r3\.4xlarge | 1 x 320 GB | SSD | ✔ |   | 
| r3\.8xlarge | 2 x 320 GB | SSD | ✔ |   | 
| r5ad\.large | 1 x 75 GB | NVMe SSD |   | ✔ | 
| r5ad\.xlarge | 1 x 150 GB | NVMe SSD |   | ✔ | 
| r5ad\.2xlarge | 1 x 300 GB | NVMe SSD |   | ✔ | 
| r5ad\.4xlarge | 2 x 300 GB | NVMe SSD |   | ✔ | 
| r5ad\.8xlarge | 2 x 600 GB | NVMe SSD |   | ✔ | 
| r5ad\.12xlarge | 2 x 900 GB | NVMe SSD |   | ✔ | 
| r5ad\.16xlarge | 4 x 600 GB | NVMe SSD |   | ✔ | 
| r5ad\.24xlarge | 4 x 900 GB | NVMe SSD |   | ✔ | 
| r5d\.large | 1 x 75 GB | NVMe SSD |   | ✔ | 
| r5d\.xlarge | 1 x 150 GB | NVMe SSD |   | ✔ | 
| r5d\.2xlarge | 1 x 300 GB | NVMe SSD |   | ✔ | 
| r5d\.4xlarge | 2 x 300 GB | NVMe SSD |   | ✔ | 
| r5d\.8xlarge | 2 x 600 GB | NVMe SSD |   | ✔ | 
| r5d\.12xlarge | 2 x 900 GB | NVMe SSD |   | ✔ | 
| r5d\.16xlarge | 4 x 600 GB | NVMe SSD |   | ✔ | 
| r5d\.24xlarge | 4 x 900 GB | NVMe SSD |   | ✔ | 
| r5d\.metal | 4 x 900 GB | NVMe SSD |   | ✔ | 
| r5dn\.large | 1 x 75 GB | NVMe SSD |   | ✔ | 
| r5dn\.xlarge | 1 x 150 GB | NVMe SSD |   | ✔ | 
| r5dn\.2xlarge | 1 x 300 GB | NVMe SSD |   | ✔ | 
| r5dn\.4xlarge | 2 x 300 GB | NVMe SSD |   | ✔ | 
| r5dn\.8xlarge | 2 x 600 GB | NVMe SSD |   | ✔ | 
| r5dn\.12xlarge | 2 x 900 GB | NVMe SSD |   | ✔ | 
| r5dn\.16xlarge | 4 x 600 GB | NVMe SSD |   | ✔ | 
| r5dn\.24xlarge | 4 x 900 GB | NVMe SSD |   | ✔ | 
| r5dn\.metal | 4 x 900 GB | NVMe SSD |   | ✔ | 
| r6gd\.medium | 1 x 59 GB | NVMe SSD |   | ✔ | 
| r6gd\.large | 1 x 118 GB | NVMe SSD |   | ✔ | 
| r6gd\.xlarge | 1 x 237 GB | NVMe SSD |   | ✔ | 
| r6gd\.2xlarge | 1 x 474 GB | NVMe SSD |   | ✔ | 
| r6gd\.4xlarge | 1 x 950 GB | NVMe SSD |   | ✔ | 
| r6gd\.8xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| r6gd\.12xlarge | 2 x 1425 GB | NVMe SSD |   | ✔ | 
| r6gd\.16xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| r6gd\.metal | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| r6idn\.large | 1 x 118 GB | NVMe SSD |   | ✔ | 
| r6idn\.xlarge | 1 x 237 GB | NVMe SSD |   | ✔ | 
| r6idn\.2xlarge | 1 x 474 GB | NVMe SSD |   | ✔ | 
| r6idn\.4xlarge | 1 x 950 GB | NVMe SSD |   | ✔ | 
| r6idn\.8xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| r6idn\.12xlarge | 2 x 1425 GB | NVMe SSD |   | ✔ | 
| r6idn\.16xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| r6idn\.24xlarge | 4 x 1425 GB | NVMe SSD |   | ✔ | 
| r6idn\.32xlarge | 4 x 1900 GB | NVMe SSD |   | ✔ | 
| r6id\.large | 1 x 118 GB | NVMe SSD |   | ✔ | 
| r6id\.xlarge | 1 x 237 GB | NVMe SSD |   | ✔ | 
| r6id\.2xlarge | 1 x 474 GB | NVMe SSD |   | ✔ | 
| r6id\.4xlarge | 1 x 950 GB | NVMe SSD |   | ✔ | 
| r6id\.8xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| r6id\.12xlarge | 2 x 1425 GB | NVMe SSD |   | ✔ | 
| r6id\.16xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| r6id\.24xlarge | 4 x 1425 GB | NVMe SSD |   | ✔ | 
| r6id\.32xlarge | 4 x 1900 GB | NVMe SSD |   | ✔ | 
| r6id\.metal | 4 x 1900 GB | NVMe SSD |   | ✔ | 
| x1\.16xlarge | 1 x 1920 GB | SSD | ✔ |   | 
| x1\.32xlarge | 2 x 1920 GB | SSD | ✔ |   | 
| x2gd\.medium | 1 x 59 GB | NVMe SSD |   | ✔ | 
| x2gd\.large | 1 x 118 GB | NVMe SSD |   | ✔ | 
| x2gd\.xlarge | 1 x 237 GB | NVMe SSD |   | ✔ | 
| x2gd\.2xlarge | 1 x 475 GB | NVMe SSD |   | ✔ | 
| x2gd\.4xlarge | 1 x 950 GB | NVMe SSD |   | ✔ | 
| x2gd\.8xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| x2gd\.12xlarge | 2 x 1425 GB | NVMe SSD |   | ✔ | 
| x2gd\.16xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| x2gd\.metal | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| x2idn\.16xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| x2idn\.24xlarge | 2 x 1425 GB | NVMe SSD |   | ✔ | 
| x2idn\.32xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| x2idn\.metal | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| x2iedn\.xlarge | 1 x 118 GB | NVMe SSD |   | ✔ | 
| x2iedn\.2xlarge | 1 x 237 GB | NVMe SSD |   | ✔ | 
| x2iedn\.4xlarge | 1 x 475 GB | NVMe SSD |   | ✔ | 
| x2iedn\.8xlarge | 1 x 950 GB | NVMe SSD |   | ✔ | 
| x2iedn\.16xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| x2iedn\.24xlarge | 2 x 1425 GB | NVMe SSD |   | ✔ | 
| x2iedn\.32xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| x2iedn\.metal | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| x1e\.xlarge | 1 x 120 GB | SSD | ✔ |   | 
| x1e\.2xlarge | 1 x 240 GB | SSD | ✔ |   | 
| x1e\.4xlarge | 1 x 480 GB | SSD | ✔ |   | 
| x1e\.8xlarge | 1 x 960 GB | SSD | ✔ |   | 
| x1e\.16xlarge | 1 x 1920 GB | SSD | ✔ |   | 
| x1e\.32xlarge | 2 x 1920 GB | SSD | ✔ |   | 
| z1d\.large | 1 x 75 GB | NVMe SSD |   | ✔ | 
| z1d\.xlarge | 1 x 150 GB | NVMe SSD |   | ✔ | 
| z1d\.2xlarge | 1 x 300 GB | NVMe SSD |   | ✔ | 
| z1d\.3xlarge | 1 x 450 GB | NVMe SSD |   | ✔ | 
| z1d\.6xlarge | 1 x 900 GB | NVMe SSD |   | ✔ | 
| z1d\.12xlarge | 2 x 900 GB | NVMe SSD |   | ✔ | 
| z1d\.metal | 2 x 900 GB | NVMe SSD |   | ✔ | 

### Storage optimized<a name="instance-store-vol-so"></a>


| Instance type | Instance store volumes | Type | Needs initialization\* | TRIM support\*\* | 
| --- | --- | --- | --- | --- | 
| d2\.xlarge | 3 x 2048 GB | HDD | ✔ |   | 
| d2\.2xlarge | 6 x 2048 GB | HDD | ✔ |   | 
| d2\.4xlarge | 12 x 2048 GB | HDD | ✔ |   | 
| d2\.8xlarge | 24 x 2048 GB | HDD | ✔ |   | 
| d3\.xlarge | 3 x 1980 GB | NVMe HDD |   | ✔ | 
| d3\.2xlarge | 6 x 1980 GB | NVMe HDD |   | ✔ | 
| d3\.4xlarge | 12 x 1980 GB | NVMe HDD |   | ✔ | 
| d3\.8xlarge | 24 x 1980 GB | NVMe HDD |   | ✔ | 
| d3en\.xlarge | 2 x 13980 GB | NVMe HDD |   | ✔ | 
| d3en\.2xlarge | 4 x 13980 GB | NVMe HDD |   | ✔ | 
| d3en\.4xlarge | 8 x 13980 GB | NVMe HDD |   | ✔ | 
| d3en\.6xlarge | 12 x 13980 GB | NVMe HDD |   | ✔ | 
| d3en\.8xlarge | 16 x 13980 GB | NVMe HDD |   | ✔ | 
| d3en\.12xlarge | 24 x 13980 GB | NVMe HDD |   | ✔ | 
| h1\.2xlarge | 1 x 2000 GB | HDD | ✔ |   | 
| h1\.4xlarge | 2 x 2000 GB | HDD | ✔ |   | 
| h1\.8xlarge | 4 x 2000 GB | HDD | ✔ |   | 
| h1\.16xlarge | 8 x 2000 GB | HDD | ✔ |   | 
| hs1\.8xlarge | 24 x 2048 GB | HDD | ✔ |   | 
| i2\.xlarge | 1 x 800 GB | SSD | ✔ |   | 
| i2\.2xlarge | 2 x 800 GB | SSD | ✔ |   | 
| i2\.4xlarge | 4 x 800 GB | SSD | ✔ |   | 
| i2\.8xlarge | 8 x 800 GB | SSD | ✔ |   | 
| i3\.large | 1 x 475 GB | NVMe SSD |   | ✔ | 
| i3\.xlarge | 1 x 950 GB | NVMe SSD |   | ✔ | 
| i3\.2xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| i3\.4xlarge | 2 x 1900 GB | NVMe SSD |   | ✔ | 
| i3\.8xlarge | 4 x 1900 GB | NVMe SSD |   | ✔ | 
| i3\.16xlarge | 8 x 1900 GB | NVMe SSD |   | ✔ | 
| i3\.metal | 8 x 1900 GB | NVMe SSD |   | ✔ | 
| i3en\.large | 1 x 1250 GB | NVMe SSD |   | ✔ | 
| i3en\.xlarge | 1 x 2500 GB | NVMe SSD |   | ✔ | 
| i3en\.2xlarge | 2 x 2500 GB | NVMe SSD |   | ✔ | 
| i3en\.3xlarge | 1 x 7500 GB | NVMe SSD |   | ✔ | 
| i3en\.6xlarge | 2 x 7500 GB | NVMe SSD |   | ✔ | 
| i3en\.12xlarge | 4 x 7500 GB | NVMe SSD |   | ✔ | 
| i3en\.24xlarge | 8 x 7500 GB | NVMe SSD |   | ✔ | 
| i3en\.metal | 8 x 7500 GB | NVMe SSD |   | ✔ | 
| i4i\.large | 1 x 468 GB | NVMe SSD |   | ✔ | 
| i4i\.xlarge | 1 x 937 GB | NVMe SSD |   | ✔ | 
| i4i\.2xlarge | 1 x 1875 GB | NVMe SSD |   | ✔ | 
| i4i\.4xlarge | 1 x 3750 GB | NVMe SSD |   | ✔ | 
| i4i\.8xlarge | 2 x 3750 GB | NVMe SSD |   | ✔ | 
| i4i\.16xlarge | 4 x 3750 GB | NVMe SSD |   | ✔ | 
| i4i\.32xlarge | 8 x 3750 GB | NVMe SSD |   | ✔ | 
| i4i\.metal | 8 x 3750 GB | NVMe SSD |   | ✔ | 
| im4gn\.large | 1 x 937 GB | NVMe SSD |   | ✔ | 
| im4gn\.xlarge | 1 x 1875 GB | NVMe SSD |   | ✔ | 
| im4gn\.2xlarge | 1 x 3750 GB | NVMe SSD |   | ✔ | 
| im4gn\.4xlarge | 1 x 7500 GB | NVMe SSD |   | ✔ | 
| im4gn\.8xlarge | 2 x 7500 GB | NVMe SSD |   | ✔ | 
| im4gn\.16xlarge | 4 x 7500 GB | NVMe SSD |   | ✔ | 
| is4gen\.medium | 1 x 937 GB | NVMe SSD |   | ✔ | 
| is4gen\.large | 1 x 1875 GB | NVMe SSD |   | ✔ | 
| is4gen\.xlarge | 1 x 3750 GB | NVMe SSD |   | ✔ | 
| is4gen\.2xlarge | 1 x 7500 GB | NVMe SSD |   | ✔ | 
| is4gen\.4xlarge | 2 x 7500 GB | NVMe SSD |   | ✔ | 
| is4gen\.8xlarge | 4 x 7500 GB | NVMe SSD |   | ✔ | 

### Accelerated computing<a name="instance-store-vol-ac"></a>


| Instance type | Instance store volumes | Type | Needs initialization\* | TRIM support\*\* | 
| --- | --- | --- | --- | --- | 
| dl1\.24xlarge | 4 x 1000 GB | NVMe SSD |   | ✔ | 
| f1\.2xlarge | 1 x 470 GB | NVMe SSD |   | ✔ | 
| f1\.4xlarge | 1 x 940 GB | NVMe SSD |   | ✔ | 
| f1\.16xlarge | 4 x 940 GB | NVMe SSD |   | ✔ | 
| g2\.2xlarge | 1 x 60 GB | SSD | ✔ |   | 
| g2\.8xlarge | 2 x 120 GB | SSD | ✔ |   | 
| g4ad\.xlarge | 1 x 150 GB | NVMe SSD |   | ✔ | 
| g4ad\.2xlarge | 1 x 300 GB | NVMe SSD |   | ✔ | 
| g4ad\.4xlarge | 1 x 600 GB | NVMe SSD |   | ✔ | 
| g4ad\.8xlarge | 1 x 1200 GB | NVMe SSD |   | ✔ | 
| g4ad\.16xlarge | 2 x 1200 GB | NVMe SSD |   | ✔ | 
| g4dn\.xlarge | 1 x 125 GB | NVMe SSD |   | ✔ | 
| g4dn\.2xlarge | 1 x 225 GB | NVMe SSD |   | ✔ | 
| g4dn\.4xlarge | 1 x 225 GB | NVMe SSD |   | ✔ | 
| g4dn\.8xlarge | 1 x 900 GB | NVMe SSD |   | ✔ | 
| g4dn\.12xlarge | 1 x 900 GB | NVMe SSD |   | ✔ | 
| g4dn\.16xlarge | 1 x 900 GB | NVMe SSD |   | ✔ | 
| g4dn\.metal | 2 x 900 GB | NVMe SSD |   | ✔ | 
| g5\.xlarge | 1 x 250 GB | NVMe SSD |   | ✔ | 
| g5\.2xlarge | 1 x 450 GB | NVMe SSD |   | ✔ | 
| g5\.4xlarge | 1 x 600 GB | NVMe SSD |   | ✔ | 
| g5\.8xlarge | 1 x 900 GB | NVMe SSD |   | ✔ | 
| g5\.12xlarge | 1 x 3800 GB | NVMe SSD |   | ✔ | 
| g5\.16xlarge | 1 x 1900 GB | NVMe SSD |   | ✔ | 
| g5\.24xlarge | 1 x 3800 GB | NVMe SSD |   | ✔ | 
| g5\.48xlarge | 2 x 3800 GB | NVMe SSD |   | ✔ | 
| p3dn\.24xlarge | 2 x 900 GB | NVMe SSD |   | ✔ | 
| p4d\.24xlarge | 8 x 1000 GB | NVMe SSD |   | ✔ | 
| p4de\.24xlarge | 8 x 1000 GB | NVMe SSD |   | ✔ | 
| trn1\.2xlarge | 1 x 474 GB | NVMe SSD |   | ✔ | 
| trn1\.32xlarge | 4 x 1900 GB | NVMe SSD |   | ✔ | 

\* Volumes attached to certain instances suffer a first\-write penalty unless initialized\. For more information, see [Optimize disk performance for instance store volumes](disk-performance.md)\.

\*\* For more information, see [Instance store volume TRIM support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

† The `c1.medium` and `m1.small` instance types also include a 900 MB instance store swap volume, which may not be automatically enabled at boot time\. For more information, see [Instance store swap volumes](instance-store-swap-volumes.md)\.

**Instance store volume performance**

The following documentation describes the I/O performance of the instance store volumes\.
+ [General purpose instances](general-purpose-instances.md#general-purpose-ssd-perf)
+ [Compute optimized instances](compute-optimized-instances.md#compute-ssd-perf)
+ [Memory optimized instances](memory-optimized-instances.md#instances-ssd-perf)
+ [Storage optimized instances](storage-optimized-instances.md#storage-instances-diskperf)
+ [Accelerated computing instances](accelerated-computing-instances.md#accelerated-computing-ssd-perf)

**To query instance store volume information using the AWS CLI**  
You can use the [describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) AWS CLI command to display information about an instance type, such as its instance store volumes\. The following example displays the total size of instance storage for all R5 instances with instance store volumes\.

```
aws ec2 describe-instance-types \
    --filters "Name=instance-type,Values=r5*" "Name=instance-storage-supported,Values=true" \
    --query "InstanceTypes[].[InstanceType, InstanceStorageInfo.TotalSizeInGB]" \
    --output table
```

Example output

```
---------------------------
|  DescribeInstanceTypes  |
+----------------+--------+
|  r5ad.24xlarge |  3600  |
|  r5ad.12xlarge |  1800  |
|  r5dn.8xlarge  |  1200  |
|  r5ad.8xlarge  |  1200  |
|  r5ad.large    |  75    |
|  r5d.4xlarge   |  600   |
   . . .
|  r5dn.2xlarge  |  300   |
|  r5d.12xlarge  |  1800  |
+----------------+--------+
```

The following example displays the complete instance storage details for the specified instance type\.

```
aws ec2 describe-instance-types \
    --filters "Name=instance-type,Values=r5d.4xlarge" \
    --query "InstanceTypes[].InstanceStorageInfo"
```

The example output shows that this instance type has two 300 GB NVMe SSD volumes, for a total of 600 GB of instance storage\.

```
[
    {
        "TotalSizeInGB": 600,
        "Disks": [
            {
                "SizeInGB": 300,
                "Count": 2,
                "Type": "ssd"
            }
        ],
        "NvmeSupport": "required"
    }
]
```