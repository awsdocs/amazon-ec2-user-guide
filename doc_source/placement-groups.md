# Placement Groups<a name="placement-groups"></a>

When you launch a new EC2 instance, the EC2 service attempts to place the instance in such a way that all of your instances are spread out across underlying hardware to minimize correlated failures\. You can use *placement groups* to influence the placement of a group of *interdependent* instances to meet the needs of your workload\. Depending on the type of workload, you can create a placement group using one of the following placement strategies:
+ *Cluster* – packs instances close together inside an Availability Zone\. This strategy enables workloads to achieve the low\-latency network performance necessary for tightly\-coupled node\-to\-node communication that is typical of HPC applications\.
+ *Partition* – spreads your instances across logical partitions such that groups of instances in one partition do not share the underlying hardware with groups of instances in different partitions\. This strategy is typically used by large distributed and replicated workloads, such as Hadoop, Cassandra, and Kafka\.
+ *Spread* – strictly places a small group of instances across distinct underlying hardware to reduce correlated failures\.

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

A cluster placement group is a logical grouping of instances within a single Availability Zone\. A cluster placement group can span peered VPCs in the same Region\. Instances in the same cluster placement group enjoy a higher per\-flow throughput limit of up to 10 Gbps for TCP/IP traffic and are placed in the same high\-bisection bandwidth segment of the network\.

The following image shows instances that are placed into a cluster placement group\.

![\[A cluster placement group\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/placement-group-cluster.png)

Cluster placement groups are recommended for applications that benefit from low network latency, high network throughput, or both\. They are also recommended when the majority of the network traffic is between the instances in the group\. To provide the lowest latency and the highest packet\-per\-second network performance for your placement group, choose an instance type that supports enhanced networking\. For more information, see [Enhanced Networking](enhanced-networking.md)\.

We recommend that you launch your instances in the following way:
+ Use a single launch request to launch the number of instances that you need in the placement group\.
+ Use the same instance type for all instances in the placement group\. 

If you try to add more instances to the placement group later, or if you try to launch more than one instance type in the placement group, you increase your chances of getting an insufficient capacity error\.

If you stop an instance in a placement group and then start it again, it still runs in the placement group\. However, the start fails if there isn't enough capacity for the instance\.

If you receive a capacity error when launching an instance in a placement group that already has running instances, stop and start all of the instances in the placement group, and try the launch again\. Starting the instances may migrate them to hardware that has capacity for all of the requested instances\.

## Partition Placement Groups<a name="placement-groups-partition"></a>

Partition placement groups help reduce the likelihood of correlated hardware failures for your application\. When using partition placement groups, Amazon EC2 divides each group into logical segments called partitions\. Amazon EC2 ensures that each partition within a placement group has its own set of racks\. Each rack has its own network and power source\. No two partitions within a placement group share the same racks, allowing you to isolate the impact of hardware failure within your application\.

The following image is a simple visual representation of a partition placement group in a single Availability Zone\. It shows instances that are placed into a partition placement group with three partitions—**Partition 1**, **Partition 2**, and **Partition 3**\. Each partition comprises multiple instances\. The instances in a partition do not share racks with the instances in the other partitions, allowing you to contain the impact of a single hardware failure to only the associated partition\.

![\[A partition placement group with three partitions\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/placement-group-partition.png)

Partition placement groups can be used to deploy large distributed and replicated workloads, such as HDFS, HBase, and Cassandra, across distinct racks\. When you launch instances into a partition placement group, Amazon EC2 tries to distribute the instances evenly across the number of partitions that you specify\. You can also launch instances into a specific partition to have more control over where the instances are placed\.

A partition placement group can have partitions in multiple Availability Zones in the same Region\. A partition placement group can have a maximum of seven partitions per Availability Zone\. The number of instances that can be launched into a partition placement group is limited only by the limits of your account\. 

In addition, partition placement groups offer visibility into the partitions — you can see which instances are in which partitions\. You can share this information with topology\-aware applications, such as HDFS, HBase, and Cassandra\. These applications use this information to make intelligent data replication decisions for increasing data availability and durability\.

If you start or launch an instance in a partition placement group and there is insufficient unique hardware to fulfill the request, the request fails\. Amazon EC2 makes more distinct hardware available over time, so you can try your request again later\.

## Spread Placement Groups<a name="placement-groups-spread"></a>

A spread placement group is a group of instances that are each placed on distinct racks, with each rack having its own network and power source\.

The following image shows seven instances in a single Availability Zone that are placed into a spread placement group\. The seven instances are placed on seven different racks\.

![\[A spread placement group\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/placement-group-spread.png)

Spread placement groups are recommended for applications that have a small number of critical instances that should be kept separate from each other\. Launching instances in a spread placement group reduces the risk of simultaneous failures that might occur when instances share the same racks\. Spread placement groups provide access to distinct racks, and are therefore suitable for mixing instance types or launching instances over time\.

A spread placement group can span multiple Availability Zones in the same Region\. You can have a maximum of seven running instances per Availability Zone per group\.

If you start or launch an instance in a spread placement group and there is insufficient unique hardware to fulfill the request, the request fails\. Amazon EC2 makes more distinct hardware available over time, so you can try your request again later\.

## Placement Group Rules and Limitations<a name="concepts-placement-groups"></a>

### General Rules and Limitations<a name="placement-groups-limitations-general"></a>

Before you use placement groups, be aware of the following rules:
+ The name that you specify for a placement group must be unique within your AWS account for the Region\.
+ You can't merge placement groups\.
+ An instance can be launched in one placement group at a time; it cannot span multiple placement groups\.
+ [On\-Demand Capacity Reservation](ec2-capacity-reservations.md#capacity-reservations-limits) and [zonal Reserved Instances](reserved-instances-scope.md) provide a capacity reservation for EC2 instances in a specific Availability Zone\. The capacity reservation can be used by instances in a placement group\. However, it is not possible to explicitly reserve capacity for a placement group\.
+ Instances with a tenancy of `host` cannot be launched in placement groups\.

### Cluster Placement Group Rules and Limitations<a name="placement-groups-limitations-cluster"></a>

The following rules apply to cluster placement groups:
+ When you launch an instance into a cluster placement group, you must use one of the following instance types:
  + General purpose: A1, M4, M5, M5a, M5ad, M5d, M5dn, and M5n
  + Compute optimized: C3, C4, C5, C5d, C5n, and `cc2.8xlarge`
  + Memory optimized: `cr1.8xlarge`, R3, R4, R5, R5a, R5ad, R5d, R5dn, R5n, X1, X1e, and z1d
  + Storage optimized: D2, H1, `hs1.8xlarge`, I2, I3, and I3en
  + Accelerated computing: F1, G2, G3, G4dn, Inf1, P2, P3, and P3dn
+ A cluster placement group can't span multiple Availability Zones\.
+ The maximum network throughput speed of traffic between two instances in a cluster placement group is limited by the slower of the two instances\. For applications with high\-throughput requirements, choose an instance type with network connectivity that meets your requirements\.
+ For instances that are enabled for enhanced networking, the following rules apply:
  + Instances within a cluster placement group can use up to 10 Gbps for single\-flow traffic\. Instances that are not within a cluster placement group can use up to 5 Gbps for single\-flow traffic\.
  + Traffic to and from Amazon S3 buckets within the same Region over the public IP address space or through a VPC endpoint can use all available instance aggregate bandwidth\.
+ You can launch multiple instance types into a cluster placement group\. However, this reduces the likelihood that the required capacity will be available for your launch to succeed\. We recommend using the same instance type for all instances in a cluster placement group\.
+ Network traffic to the internet and over an AWS Direct Connect connection to on\-premises resources is limited to 5 Gbps\.

### Partition Placement Group Rules and Limitations<a name="placement-groups-limitations-partition"></a>

The following rules apply to partition placement groups:
+ A partition placement group supports a maximum of seven partitions per Availability Zone\. The number of instances that you can launch in a partition placement group is limited only by your account limits\.
+ When instances are launched into a partition placement group, Amazon EC2 tries to evenly distribute the instances across all partitions\. Amazon EC2 doesn’t guarantee an even distribution of instances across all partitions\.
+ A partition placement group with Dedicated Instances can have a maximum of two partitions\.
+ Partition placement groups are not supported for Dedicated Hosts\.

### Spread Placement Group Rules and Limitations<a name="placement-groups-limitations-spread"></a>

The following rules apply to spread placement groups:
+ A spread placement group supports a maximum of seven running instances per Availability Zone\. For example, in a Region with three Availability Zones, you can run a total of 21 instances in the group \(seven per zone\)\. If you try to start an eighth instance in the same Availability Zone and in the same spread placement group, the instance will not launch\. If you need to have more than seven instances in an Availability Zone, then the recommendation is to use multiple spread placement groups\. Using multiple spread placement groups does not provide guarantees about the spread of instances between groups, but it does ensure the spread for each group, thus limiting impact from certain classes of failures\. 
+ Spread placement groups are not supported for Dedicated Instances or Dedicated Hosts\.

## Creating a Placement Group<a name="create-placement-group"></a>

You can create a placement group using one of the following methods\.

------
#### [ New console ]

**To create a placement group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Placement Groups**, **Create placement group**\.

1. Specify a name for the group\.

1. Choose the placement strategy for the group\. If you choose **Partition**, choose the number of partitions within the group\.

1. Choose **Create group**\.

------
#### [ Old console ]

**To create a placement group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Placement Groups**, **Create Placement Group**\.

1. Specify a name for the group\.

1. Choose the placement strategy for the group\. If you choose **Partition**, specify the number of partitions within the group\.

1. Choose **Create**\.

------
#### [ AWS CLI ]

**To create a placement group using the AWS CLI**  
Use the [create\-placement\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-placement-group.html) command\. In this example, the placement group name is `my-cluster` and the placement strategy is `cluster`\.

```
aws ec2 create-placement-group --group-name my-cluster --strategy cluster
```

**To create a partition placement group using the AWS CLI**  
Use the [create\-placement\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-placement-group.html) command\. Specify the `--strategy` parameter with the value `partition`, and specify the `--partition-count` parameter with the desired number of partitions\. In this example, the partition placement group is named `HDFS-Group-A` and is created with five partitions\.

```
aws ec2 create-placement-group --group-name HDFS-Group-A --strategy partition --partition-count 5
```

------
#### [ PowerShell  ]

**To create a placement group using the AWS Tools for Windows PowerShell**  
Use the [New\-EC2PlacementGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2PlacementGroup.html) command\.

------

## Launching Instances in a Placement Group<a name="launch-instance-placement-group"></a>

You can launch an instance into a placement group if the [placement group rules and limitations are met](#concepts-placement-groups) using one of the following methods\.

------
#### [ Console ]<a name="launch_cluster_instance"></a>

**To launch instances into a placement group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose **Launch Instance**\. Complete the wizard as directed, taking care to do the following:
   + On the **Choose an Instance Type** page, select an instance type that can be launched into a placement group\.
   + On the **Configure Instance Details** page, the following fields are applicable to placement groups:
     + For **Number of instances**, enter the total number of instances that you need in this placement group, because you might not be able to add instances to the placement group later\.
     + For **Placement group**, select the **Add instance to placement group** check box\. If you do not see **Placement group** on this page, verify that you have selected an instance type that can be launched into a placement group\. Otherwise, this option is not available\.
     + For **Placement group name**, you can choose to add the instances to an existing placement group or to a new placement group that you create\.
     + For **Placement group strategy**, choose the appropriate strategy\. If you choose **partition**, for **Target partition**, choose **Auto distribution** to have Amazon EC2 do a best effort to distribute the instances evenly across all the partitions in the group\. Alternatively, specify the partition in which to launch the instances\.

------
#### [ AWS CLI ]

**To launch instances into a placement group using the AWS CLI**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and specify the placement group name using the `--placement "GroupName = my-cluster"` parameter\. In this example, the placement group is named `my-cluster`\.

```
aws ec2 run-instances --placement "GroupName = my-cluster"
```

**To launch instances into a specific partition of a partition placement group using the AWS CLI**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and specify the placement group name and partition using the `--placement "GroupName = HDFS-Group-A, PartitionNumber = 3"` parameter\. In this example, the placement group is named `HDFS-Group-A` and the partition number is `3`\.

```
aws ec2 run-instances --placement "GroupName = HDFS-Group-A, PartitionNumber = 3"
```

------
#### [ PowerShell ]

**To launch instances into a placement group using AWS Tools for Windows PowerShell**  
Use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command and specify the placement group name using the `-Placement_GroupName` parameter\.

------

## Describing Instances in a Placement Group<a name="describe-instance-placement"></a>

You can view the placement information of your instances using one of the following methods\. You can also filter partition placement groups by the partition number using the AWS CLI\.

------
#### [ Console ]

**To view the placement group and partition number of an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\. 

1. Select the instance and, in the details pane, inspect **Placement group**\. If the instance is not in a placement group, the field is empty\. Otherwise, the placement group name is displayed\. If the placement group is a partition placement group, inspect **Partition number** for the partition number for the instance\.

------
#### [ AWS CLI ]

**To view the partition number for an instance in a partition placement group using the AWS CLI**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and specify the `--instance-id` parameter\.

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

**To filter instances for a specific partition placement group and partition number using the AWS CLI**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and specify the `--filters` parameter with the `placement-group-name` and `placement-partition-number` filters\. In this example, the placement group is named `HDFS-Group-A` and the partition number is `7`\.

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

------

## Changing the Placement Group for an Instance<a name="change-instance-placement-group"></a>

You can change the placement group for an instance in any of the following ways:
+ Move an existing instance to a placement group
+ Move an instance from one placement group to another
+ Remove an instance from a placement group 

Before you move or remove the instance, the instance must be in the `stopped` state\. You can move or remove an instance using the AWS CLI or an AWS SDK\.

------
#### [ AWS CLI ]

**To move an instance to a placement group using the AWS CLI**

1. Stop the instance using the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) command\.

1. Use the [modify\-instance\-placement](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-placement.html) command and specify the name of the placement group to which to move the instance\.

   ```
   aws ec2 modify-instance-placement --instance-id i-0123a456700123456 --group-name MySpreadGroup
   ```

1. Start the instance using the [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) command\.

------
#### [ PowerShell ]

**To move an instance to a placement group using the AWS Tools for Windows PowerShell**

1. Stop the instance using the [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) command\.

1. Use the [Edit\-EC2InstancePlacement](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstancePlacement.html) command and specify the name of the placement group to which to move the instance\.

1. Start the instance using the [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) command\.

------

 

------
#### [ AWS CLI ]

**To remove an instance from a placement group using the AWS CLI**

1. Stop the instance using the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) command\.

1. Use the [modify\-instance\-placement](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-placement.html) command and specify an empty string for the placement group name\.

   ```
   aws ec2 modify-instance-placement --instance-id i-0123a456700123456 --group-name ""
   ```

1. Start the instance using the [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) command\.

------
#### [ PowerShell ]

**To remove an instance from a placement group using the AWS Tools for Windows PowerShell**

1. Stop the instance using the [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) command\.

1. Use the [Edit\-EC2InstancePlacement](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstancePlacement.html) command and specify an empty string for the placement group name\.

1. Start the instance using the [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) command\.

------

## Deleting a Placement Group<a name="delete-placement-group"></a>

If you need to replace a placement group or no longer need one, you can delete it\. You can delete a placement group using one of the following methods\.

**Important**  
Before you can delete a placement group, it must contain no instances\. You can [terminate](terminating-instances.md#terminating-instances-console) all instances that you launched into the placement group, [move](#move-instance-to-placement-group) them to another placement group, or [remove](#remove-instance-from-placement-group) them from the placement group\. You can verify that an instance is in a placement group before you terminate or move it by selecting the instance in the **Instances** screen and checking the value of **Placement group** in the details pane\.

------
#### [ New console ]

**To delete a placement group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Placement Groups**\.

1. Select the placement group and choose **Delete**\.

1. When prompted for confirmation, enter **Delete** and then choose **Delete**\.

------
#### [ Old console ]

**To delete a placement group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Placement Groups**\.

1. Select the placement group and choose **Delete Placement Group**\.

1. When prompted for confirmation, choose **Delete**\.

------
#### [ AWS CLI ]

**To delete a placement group using the AWS CLI**  
 Use the [delete\-placement\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-placement-group.html) command and specify the placement group name to delete the placement group\. In this example, the placement group name is `my-cluster`\.

```
aws ec2 delete-placement-group --group-name my-cluster
```

------
#### [ PowerShell ]

**To delete a placement group using the AWS Tools for Windows PowerShell**  
Use the [Remove\-EC2PlacementGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2PlacementGroup.html) command to delete the placement group\.

------