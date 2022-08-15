# Previous generation Magnetic volumes<a name="EBSVolumeTypes_standard"></a>

The following table describes previous\-generation EBS volume types\. If you need higher performance or performance consistency than previous\-generation volumes can provide, we recommend that you consider using General Purpose SSD \(`gp2` and `gp3`\) or other current volume types\. For more information, see [Previous Generation Volumes](https://aws.amazon.com/ebs/previous-generation/)\.


|  | Magnetic | 
| --- | --- | 
| Volume type | standard | 
| Use cases | Workloads where data is infrequently accessed | 
| Volume size | 1 GiB\-1 TiB | 
| Max IOPS per volume | 40–200 | 
| Max throughput per volume | 40–90 MiB/s | 
| Boot volume | Supported | 

Magnetic volumes are backed by magnetic drives and are suited for workloads where data is accessed infrequently, and scenarios where low\-cost storage for small volume sizes is important\. These volumes deliver approximately 100 IOPS on average, with burst capability of up to hundreds of IOPS, and they can range in size from 1 GiB to 1 TiB\.

**Note**  
Magnetic is a previous generation volume type\. For new applications, we recommend using one of the newer volume types\. For more information, see [Previous Generation Volumes](http://aws.amazon.com/ebs/previous-generation/)\.