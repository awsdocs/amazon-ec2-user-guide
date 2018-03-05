# Using Public Data Sets<a name="using-public-data-sets"></a>

Amazon Web Services provides a repository of public data sets that can be seamlessly integrated into AWS cloud\-based applications\. Amazon stores the data sets at no charge to the community and, as with all AWS services, you pay only for the compute and storage you use for your own applications\.


+ [Public Data Set Concepts](#concepts-public-data-sets)
+ [Finding Public Data Sets](#using-public-data-sets-finding)
+ [Creating a Public Data Set Volume from a Snapshot](#using-public-data-sets-launching-set)
+ [Attaching and Mounting the Public Data Set Volume](#using-public-data-sets-launching-mounting)

## Public Data Set Concepts<a name="concepts-public-data-sets"></a>

Previously, large data sets such as the mapping of the Human Genome and the US Census data required hours or days to locate, download, customize, and analyze\. Now, anyone can access these data sets from an EC2 instance and start computing on the data within minutes\. You can also leverage the entire AWS ecosystem and easily collaborate with other AWS users\. For example, you can produce or use prebuilt server images with tools and applications to analyze the data sets\. By hosting this important and useful data with cost\-efficient services such as Amazon EC2, AWS hopes to provide researchers across a variety of disciplines and industries with tools to enable more innovation, more quickly\. 

For more information, go to the [Public Data Sets on AWS Page\.](http://aws.amazon.com/publicdatasets/)

### Available Public Data Sets<a name="concepts-public-data-sets-available"></a>

Public data sets are currently available in the following categories:

+ **Biology—**Includes Human Genome Project, GenBank, and other content\.

+ **Chemistry—**Includes multiple versions of PubChem and other content\. 

+ **Economics—**Includes census data, labor statistics, transportation statistics, and other content\. 

+ **Encyclopedic—**Includes Wikipedia content from multiple sources and other content\. 

## Finding Public Data Sets<a name="using-public-data-sets-finding"></a>

Before you can use a public data set, you must locate the data set and determine which format the data set is hosted in\. The data sets are available in two possible formats: Amazon EBS snapshots or Amazon S3 buckets\.

**To find a public data set and determine its format**

1. Go to the [Public Data Sets Page](http://aws.amazon.com/datasets/) to see a listing of all available public data sets\. You can also enter a search phrase on this page to query the available public data set listings\.

1. Click the name of a data set to see its detail page\.

1. On the data set detail page, look for a snapshot ID listing to identify an Amazon EBS formatted data set or an Amazon S3 URL\. 

Data sets that are in snapshot format are used to create new EBS volumes that you attach to an EC2 instance\. For more information, see [Creating a Public Data Set Volume from a Snapshot](#using-public-data-sets-launching-set)\.

For data sets that are in Amazon S3 format, you can use the AWS SDKs or the HTTP query API to access the information, or you can use the AWS CLI to copy or synchronize the data to and from your instance\. For more information, see [Amazon S3 and Amazon EC2](AmazonS3.md#S3UsageScenarios)\.

You can also use Amazon EMR to analyze and work with public data sets\. For more information, see [ What is Amazon EMR?](http://docs.aws.amazon.com/emr/latest/DeveloperGuide/emr-what-is-emr.html)\.

## Creating a Public Data Set Volume from a Snapshot<a name="using-public-data-sets-launching-set"></a>

To use a public data set that is in snapshot format, you create a new volume, specifying the snapshot ID of the public data set\. You can create your new volume using the AWS Management Console as follows\. If you prefer, you can use the [create\-volume](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) AWS CLI command instead\.

**To create a public data set volume from a snapshot**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region that your data set snapshot is located in\.

   If you need to create this volume in a different region, you can copy the snapshot to that region and then use it to create a volume in that region\. For more information, see [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)\.

1. In the navigation pane, choose **ELASTIC BLOCK STORE**, **Volumes**\.

1. Choose **Create Volume**\.

1. For **Volume Type**, choose a volume type\. For more information, see [Amazon EBS Volume Types](EBSVolumeTypes.md)\.

1. For **Snapshot**, start typing the ID or description of the snapshot that has the data set, and choose it from the list\.

   If the snapshot that you are expecting to see does not appear, you might not have selected the region it is in\. If the data set you identified in [Finding Public Data Sets](#using-public-data-sets-finding) does not specify a region on its detail page, it is likely contained in the `us-east-1` US East \(N\. Virginia\) region\.

1. <a name="data_set_select_size_step_gwt"></a>For **Size \(GiB\)**, type the size of the volume, or verify the that the default size of the snapshot is adequate\.
**Note**  
If you specify both a volume size and a snapshot, the size must be equal to or greater than the snapshot size\. When you select a volume type and a snapshot, the minimum and maximum sizes for the volume are shown next to **Size**\.

1. With a Provisioned IOPS SSD volume, for **IOPS**, type the maximum number of input/output operations per second \(IOPS\) that the volume should support\.

1. For **Availability Zone**, choose the Availability Zone in which to create the volume\. EBS volumes can only be attached to instances in the same Availability Zone\.

1. \(Optional\) Choose **Create additional tags** to add tags to the volume\. For each tag, provide a tag key and a tag value\.

1. Choose **Create Volume**\.

## Attaching and Mounting the Public Data Set Volume<a name="using-public-data-sets-launching-mounting"></a>

After you have created your new data set volume, you need to attach it to an EC2 instance to access the data \(this instance must also be in the same Availability Zone as the new volume\)\. For more information, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

After you have attached the volume to an instance, you need to mount the volume on the instance\. For more information, see [Making an Amazon EBS Volume Available for Use](ebs-using-volumes.md)\.

If you restored a snapshot to a larger volume than the default for that snapshot, you must extend the file system on the volume to take advantage of the extra space\. For more information, see [Modifying the Size, IOPS, or Type of an EBS Volume on Linux](ebs-modify-volume.md)\.