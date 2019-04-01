# Placement Groups<a name="placement-groups"></a>

You can launch or start instances in a *placement group*, which determines how instances are placed on underlying hardware\. When you create a placement group, you specify one of the following strategies for the group:
+ *Cluster* – clusters instances into a low\-latency group in a single Availability Zone
+ *Partition* – spreads instances across logical partitions, ensuring that instances in one partition do not share underlying hardware with instances in other partitions
+ *Spread* – spreads instances across underlying hardware

There is no charge for creating a placement group\.

**Topics**
+ [Cluster Placement Groups](#placement-groups-cluster)
+ [Partition Placement Groups](#placement-groups-partition)
+ [Spread Placement Groups](#placement-groups-spread)
+ [Placement Group Rules and Limitations](#concepts-placement-groups)
+ [Creating a Placement Group](#create-placement-group)
+ [Launching Instances in a Placement Group](#launch-instance-placement-group)
+ [Describing Instances in a Placement Group](#describe-instance-placement)
+ [Changing the Placement Group for an Instance](#change-instance-placement-group)
+ [Deleting a Placement Group](#delete-placement-group)

## Cluster Placement Groups<a name="placement-groups-cluster"></a>

A cluster placement group is a logical grouping of instances within a single Availability Zone\. A placement group can span peered VPCs in the same Region\. The chief benefit of a cluster placement group, in addition to a 10 Gbps flow limit, is the non\-blocking, non\-oversubscribed, fully bi\-sectional nature of the connectivity\. In other words, all nodes within the placement group can talk to all other nodes within the placement group at the full line rate of 10 Gbps flows and 25 aggregate without any slowing due to over\-subscription\.

The following image shows instances that are placed into a cluster placement group\.

![\[A cluster placement group\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/placement-group-cluster.png)

Cluster placement groups are recommended for applications that benefit from low network latency, high network throughput, or both, and if the majority of the network traffic is between the instances in the group\. To provide the lowest latency and the highest packet\-per\-second network performance for your placement group, choose an instance type that supports enhanced networking\. For more information, see [Enhanced Networking](enhanced-networking.md)\.

We recommend that you launch the number of instances that you need in the placement group in a single launch request and that you use the same instance type for all instances in the placement group\. If you try to add more instances to the placement group later, or if you try to launch more than one instance type in the placement group, you increase your chances of getting an insufficient capacity error\.

If you stop an instance in a placement group and then start it again, it still runs in the placement group\. However, the start fails if there isn't enough capacity for the instance\.

If you receive a capacity error when launching an instance in a placement group that already has running instances, stop and start all of the instances in the placement group, and try the launch again\. Restarting the instances may migrate them to hardware that has capacity for all the requested instances\.

## Partition Placement Groups<a name="placement-groups-partition"></a>

A partition placement group is a group of instances spread across partitions\. Partitions are logical groupings of instances, where contained instances do not share the same underlying hardware across different partitions\.

The following image shows instances in a single Availability Zone that are placed into a partition placement group with three partitions, **Partition 1**, **Partition 2**, and **Partition 3**\. Each partition comprises multiple instances\. The instances in each partition do not share underlying hardware with the instances in the other partitions, limiting the impact of hardware failure to only one partition\.

![\[A partition placement group with seven partitions\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/placement-group-partition.png)

Partition placement groups can be used to spread deployment of large distributed and replicated workloads, such as HDFS, HBase, and Cassandra, across distinct hardware to reduce the likelihood of correlated failures\. When you launch instances into a partition placement group, Amazon EC2 tries to distribute the instances evenly across the number of partitions that you specify\. You can also launch instances into a specific partition to have more control over where the instances are placed\.

In addition, partition placement groups offer visibility into the partitions—you can see which instances are in which partitions\. You can share this information with topology\-aware applications, such as HDFS, HBase, and Cassandra, which use this information to make intelligent data replication decisions for increasing data availability and durability\.

A partition placement group can have a maximum of seven partitions per Availability Zone\. The number of instances that can be launched into a partition placement group is limited only by the limits of your account\. Partition placement groups can also span multiple Availability Zones in the same Region\.

If you start or launch an instance in a partition placement group and there is insufficient unique hardware to fulfill the request, the request fails\. Amazon EC2 makes more distinct hardware available over time, so you can try your request again later\.

Partition placement groups are currently only available through the API or AWS CLI\.

## Spread Placement Groups<a name="placement-groups-spread"></a>

A spread placement group is a group of instances that are each placed on distinct underlying hardware\.

The following image shows seven instances in a single Availability Zone that are placed into a spread placement group\. The instances do not share underlying hardware with each other\.

![\[A spread placement group\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/placement-group-spread.png)

Spread placement groups are recommended for applications that have a small number of critical instances that should be kept separate from each other\. Launching instances in a spread placement group reduces the risk of simultaneous failures that might occur when instances share the same underlying hardware\. Spread placement groups provide access to distinct hardware, and are therefore suitable for mixing instance types or launching instances over time\.

A spread placement group can span multiple Availability Zones, and you can have a maximum of seven running instances per Availability Zone per group\.

If you start or launch an instance in a spread placement group and there is insufficient unique hardware to fulfill the request, the request fails\. Amazon EC2 makes more distinct hardware available over time, so you can try your request again later\.

## Placement Group Rules and Limitations<a name="concepts-placement-groups"></a>

### General Rules and Limitations<a name="placement-groups-limitations-general"></a>

Before you use placement groups, be aware of the following rules:
+ The name you specify for a placement group must be unique within your AWS account for the Region\.
+ You can't merge placement groups\.
+ An instance can be launched in one placement group at a time; it cannot span multiple placement groups\.
+ Reserved Instances provide a capacity reservation for EC2 instances in a specific Availability Zone\. The capacity reservation can be used by instances in a placement group\. However, it is not possible to explicitly reserve capacity for a placement group\.
+ Instances with a tenancy of `host` cannot be launched in placement groups\.
+ For instances that are enabled for enhanced networking, traffic between instances within the same Region that is addressed using IPv4 or IPv6 addresses can use up to 5 Gbps for single\-flow traffic and up to 25 Gbps for multi\-flow traffic\. A flow represents a single, point\-to\-point network connection\.

### Cluster Placement Group Rules and Limitations<a name="placement-groups-limitations-cluster"></a>

The following rules apply to cluster placement groups:
+ The following are the only instance types that you can use when you launch an instance into a cluster placement group:
  + General purpose: A1, M4, M5, M5a, and M5d
  + Compute optimized: C3, C4, C5, C5d, C5n, and `cc2.8xlarge`
  + Memory optimized: `cr1.8xlarge`, R3, R4, R5, R5a, R5d, X1, X1e, and z1d
  + Storage optimized: D2, H1, `hs1.8xlarge`, I2, and I3
  + Accelerated computing: F1, G2, G3, P2, and P3
+ A cluster placement group can't span multiple Availability Zones\.
+ The maximum network throughput speed of traffic between two instances in a cluster placement group is limited by the slower of the two instances\. For applications with high\-throughput requirements, choose an instance type with network connectivity that meets your requirements\.
+ For instances that are enabled for enhanced networking, the following rules apply:
  + Instances within a cluster placement group can use up to 10 Gbps for single\-flow traffic\.
  + Traffic to and from Amazon S3 buckets within the same Region over the public IP address space or through a VPC endpoint can use all available instance aggregate bandwidth\.
+ You can launch multiple instance types into a cluster placement group\. However, this reduces the likelihood that the required capacity will be available for your launch to succeed\. We recommend using the same instance type for all instances in a cluster placement group\.
+ Network traffic to the internet and over an AWS Direct Connect connection to on\-premises resources is limited to 5 Gbps\.

### Partition Placement Group Rules and Limitations<a name="placement-groups-limitations-partition"></a>

The following rules apply to partition placement groups:
+ A partition placement group supports a maximum of seven partitions per Availability Zone\. The number of instances that you can launch in a partition placement group is limited only by your account limits\.
+ When instances are launched into a partition placement group, Amazon EC2 tries to evenly distribute the instances across all partitions\. Amazon EC2 doesn’t guarantee an even distribution of instances across all partitions\.
+ A partition placement group with Dedicated Instances can have a maximum of two partitions\.
+ Partition placement groups are not supported for Dedicated Hosts\.
+ Partition placement groups are currently only available through the API or AWS CLI\.

### Spread Placement Group Rules and Limitations<a name="placement-groups-limitations-spread"></a>

The following rules apply to spread placement groups:
+ A spread placement group supports a maximum of seven running instances per Availability Zone\. For example, in a Region with three Availability Zones, you can run a total of 21 instances in the group \(seven per zone\)\. If you try to start an eighth instance in the same Availability Zone and in the same spread placement group, the instance will not launch\. If you need to have more than seven instances in an Availability Zone, then the recommendation is to use multiple spread placement groups\. This does not provide guarantees about the spread of instances between groups, but does ensure the spread for each group to limit impact from certain classes of failures\. 
+ Spread placement groups are not supported for Dedicated Instances or Dedicated Hosts\.

## Creating a Placement Group<a name="create-placement-group"></a>

You can create a placement group using the Amazon EC2 console or the command line\.

**To create a placement group \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Placement Groups**, **Create Placement Group**\.

1. Specify a name for the group and choose the strategy\.
**Note**  
To specify a partition placement group, use the AWS CLI\.

1. Choose **Create**\.

**To create a placement group \(command line\)**
+ [create\-placement\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-placement-group.html) \(AWS CLI\)
+ [New\-EC2PlacementGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2PlacementGroup.html) \(AWS Tools for Windows PowerShell\)

**To create a partition placement group \(AWS CLI\)**
+ Use the [create\-placement\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-placement-group.html) command and specify the `--strategy` parameter with the value `partition` and the `--partition-count` parameter\. In this example, the partition placement group is named `HDFS-Group-A` and is created with five partitions\.

  ```
  aws ec2 create-placement-group --group-name HDFS-Group-A --strategy partition --partition-count 5
  ```

## Launching Instances in a Placement Group<a name="launch-instance-placement-group"></a>

You can create an AMI specifically for the instances to be launched in a placement group\. To do this, launch an instance and install the required software and applications on the instance\. Then, create an AMI from the instance\. For more information, see [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md)\.<a name="launch_cluster_instance"></a>

**To launch instances into a placement group \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose **Launch Instance**\. Complete the wizard as directed, taking care to do the following:
   + On the **Choose an Amazon Machine Image \(AMI\)** page, select an AMI\. To select an AMI you created, choose **My AMIs**\.
   + On the **Choose an Instance Type** page, select an instance type that can be launched into a placement group\.
   + On the **Configure Instance Details** page, enter the total number of instances that you need in this placement group, as you might not be able to add instances to the placement group later\.
   + On the **Configure Instance Details** page, select the placement group that you created from **Placement group**\. If you do not see the **Placement group** list on this page, verify that you have selected an instance type that can be launched into a placement group, as this option is not available otherwise\.

**To launch instances into a placement group \(command line\)**

1. Create an AMI for your instances using one of the following commands:
   + [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html) \(AWS CLI\)
   + [New\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Image.html) \(AWS Tools for Windows PowerShell\)

1. Launch instances into your placement group using one of the following options:
   + `--placement` with [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\)
   + `-PlacementGroup` with [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

**To launch instances into a specific partition of a partition placement group \(AWS CLI\)**
+ Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and specify the placement group name and partition using the `--placement "GroupName = HDFS-Group-A, PartitionNumber = 3"` parameter\. In this example, the placement group is named `HDFS-Group-A` and the partition number is `3`\.

  ```
  aws ec2 run-instances --placement "GroupName = HDFS-Group-A, PartitionNumber = 3"
  ```

## Describing Instances in a Placement Group<a name="describe-instance-placement"></a>

You can view the placement information of your instances using the Amazon EC2 console or the command line\. The placement group is viewable using the console\. The partition number for instances in a partition placement group is currently only viewable using the API or AWS CLI\.

**To view the placement group of an instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\. 

1. Select the instance and, in the details pane, inspect **Placement group**\. If the instance is not in a placement group, the field is empty\. Otherwise, the placement group name is displayed\.

**To view the partition number for an instance in a partition placement group \(AWS CLI\)**
+ Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and specify the `--instance-id` parameter\.

  ```
  aws ec2 describe-instances --instance-id i-0123a456700123456
  ```

  The response contains the placement information, which includes the placement group name and the partition number for the instance\.

  ```
  "Placement": {
       "AvailabilityZone": "us-east-1c",
       "GroupName": "HDFS-Group-A",
       "PartitionNumber": 3,
       "Tenancy": "default"
  }
  ```

**To filter instances for a specific partition placement group and partition number \(AWS CLI\)**
+ Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and specify the `--filters` parameter with the `placement-group-name` and `placement-partition-number` filters\. In this example, the placement group is named `HDFS-Group-A` and the partition number is `7`\.

  ```
  aws ec2 describe-instances --filters "Name = placement-group-name, Values = HDFS-Group-A" "Name = placement-partition-number, Values = 7"
  ```

  The response lists all the instances that are in the specified partition within the specified placement group\. The following is example output showing only the instance ID, instance type, and placement information for the returned instances\.

  ```
  "Instances": [
                  {   
                      "InstanceId": "i-0a1bc23d4567e8f90",
                      "InstanceType": "r4.large",
                      },
                    
                      "Placement": {
                          "AvailabilityZone": "us-east-1c",
                          "GroupName": "HDFS-Group-A",
                          "PartitionNumber": 7,
                          "Tenancy": "default"
                      }
  
                  {   
                      "InstanceId": "i-0a9b876cd5d4ef321",
                      "InstanceType": "r4.large",
                      },
  
                      "Placement": {
                          "AvailabilityZone": "us-east-1c",
                          "GroupName": "HDFS-Group-A",
                          "PartitionNumber": 7,
                          "Tenancy": "default"
                      }
                ],
  ```

## Changing the Placement Group for an Instance<a name="change-instance-placement-group"></a>

You can move an existing instance to a placement group, move an instance from one placement group to another, or remove an instance from a placement group\. Before you begin, the instance must be in the `stopped` state\.

You can change the placement group for an instance using the command line or an AWS SDK\.

**To move an instance to a placement group \(command line\)**

1. Stop the instance using one of the following commands:
   + [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\)
   + [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

1. Use the [modify\-instance\-placement](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-placement.html) command \(AWS CLI\) and specify the name of the placement group to which to move the instance\.

   ```
   aws ec2 modify-instance-placement --instance-id i-0123a456700123456 --group-name MySpreadGroup
   ```

   Alternatively, use the [Edit\-EC2InstancePlacement](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstancePlacement.html) command \(AWS Tools for Windows PowerShell\)\.

1. Restart the instance using one of the following commands:
   + [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\)
   + [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

**To remove an instance from a placement group \(command line\)**

1. Stop the instance using one of the following commands:
   + [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\)
   + [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

1. Use the [modify\-instance\-placement](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-placement.html) command \(AWS CLI\) and specify an empty string for the group name\.

   ```
   aws ec2 modify-instance-placement --instance-id i-0123a456700123456 --group-name ""
   ```

   Alternatively, use the [Edit\-EC2InstancePlacement](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstancePlacement.html) command \(AWS Tools for Windows PowerShell\)\.

1. Restart the instance using one of the following commands:
   + [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\)
   + [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

## Deleting a Placement Group<a name="delete-placement-group"></a>

If you need to replace a placement group or no longer need one, you can delete it\. Before you can delete your placement group, you must terminate all instances that you launched into the placement group, or move them to another placement group\.

**To terminate or move instances and delete a placement group \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select and terminate all instances in the placement group\. You can verify that the instance is in a placement group before you terminate it by checking the value of **Placement Group** in the details pane\.

   Alternatively, follow the steps in [Changing the Placement Group for an Instance](#change-instance-placement-group) to move the instances to a different placement group\.

1. In the navigation pane, choose **Placement Groups**\.

1. Select the placement group and choose **Delete Placement Group**\.

1. When prompted for confirmation, choose **Delete**\.

**To terminate instances and delete a placement group \(command line\)**

You can use one of the following sets of commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [terminate\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html) and [delete\-placement\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-placement-group.html) \(AWS CLI\)
+ [Remove\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Instance.html) and [Remove\-EC2PlacementGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2PlacementGroup.html) \(AWS Tools for Windows PowerShell\)
