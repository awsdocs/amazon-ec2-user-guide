# Work with EFA<a name="efa-working-with"></a>

You can create, use, and manage an EFA much like any other elastic network interface in Amazon EC2\. However, unlike elastic network interfaces, EFAs cannot be attached to or detached from an instance in a running state\.

## EFA requirements<a name="efa-reqs"></a>

To use an EFA, you must do the following:
+ Choose one of the [supported instance types](efa.md#efa-instance-types)\.
+ Use one of the [supported AMIs](efa.md#efa-amis)\.
+ Install the EFA software components\. For more information, see [Step 3: Install the EFA software](efa-start.md#efa-start-enable) and [Step 5: \(*Optional*\) Install Intel MPI](efa-start.md#efa-start-impi)\.
+ Use a security group that allows all inbound and outbound traffic to and from the security group itself\. For more information, see [Step 1: Prepare an EFA\-enabled security group](efa-start.md#efa-start-security)\.

**Topics**
+ [EFA requirements](#efa-reqs)
+ [Create an EFA](#efa-create)
+ [Attach an EFA to a stopped instance](#efa-attach)
+ [Attach an EFA when launching an instance](#efa-launch)
+ [Add an EFA to a launch template](#efa-launch-template)
+ [Manage IP addresses for an EFA](#efa-manage-ip-address)
+ [Change the security group for an EFA](#efa-security)
+ [Detach an EFA](#efa-detach)
+ [View EFAs](#efa-view)
+ [Delete an EFA](#efa-delete)

## Create an EFA<a name="efa-create"></a>

You can create an EFA in a subnet in a VPC\. You can't move the EFA to another subnet after it's created, and you can only attach it to stopped instances in the same Availability Zone\.

**To create a new EFA using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Choose **Create Network Interface**\.

1. For **Description**, enter a descriptive name for the EFA\.

1. For **Subnet**, select the subnet in which to create the EFA\.

1. For **Private IP**, enter the primary private IPv4 address\. If you don't specify an IPv4 address, we select an available private IPv4 address from the selected subnet\.

1. \(IPv6 only\) If you selected a subnet that has an associated IPv6 CIDR block, you can optionally specify an IPv6 address in the **IPv6 IP** field\.

1. For **Security groups**, select one or more security groups\.

1. For **EFA**, choose **Enabled**\.

1. Choose **Yes, Create**\.

**To create a new EFA using the AWS CLI**  
Use the [create\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-network-interface.html) command and for `interface-type`, specify `efa`, as shown in the following example\.

```
aws ec2 create-network-interface --subnet-id subnet-01234567890 --description example_efa --interface-type efa
```

## Attach an EFA to a stopped instance<a name="efa-attach"></a>

You can attach an EFA to any supported instance that is in the `stopped` state\. You cannot attach an EFA to an instance that is in the `running` state\. For more information about the supported instance types, see [Supported instance types](efa.md#efa-instance-types)\.

You attach an EFA to an instance in the same way that you attach a network interface to an instance\. For more information, see [Attach a network interface to an instance](using-eni.md#attach_eni)\.

## Attach an EFA when launching an instance<a name="efa-launch"></a>

**To attach an existing EFA when launching an instance \(AWS CLI\)**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and for **NetworkInterfaceId**, specify the ID of the EFA, as shown in the following example\.

```
aws ec2 run-instances --image-id ami_id --count 1 --instance-type c5n.18xlarge --key-name my_key_pair --network-interfaces DeviceIndex=0,NetworkInterfaceId=efa_id,Groups=sg_id,SubnetId=subnet_id
```

**To attach a new EFA when launching an instance \(AWS CLI\)**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and for **InterfaceType**, specify `efa`, as shown in the following example\.

```
aws ec2 run-instances --image-id ami_id --count 1 --instance-type c5n.18xlarge --key-name my_key_pair --network-interfaces DeviceIndex=0,InterfaceType=efa,Groups=sg_id,SubnetId=subnet_id
```

## Add an EFA to a launch template<a name="efa-launch-template"></a>

You can create a launch template that contains the configuration information needed to launch EFA\-enabled instances\. To create an EFA\-enabled launch template, create a new launch template and specify a supported instance type, your EFA\-enabled AMI, and an EFA\-enabled security group\. For more information, see [Get started with EFA and MPI](efa-start.md)\.

You can leverage launch templates to launch EFA\-enabled instances with other AWS services, such as [AWS Batch](https://docs.aws.amazon.com/batch/latest/userguide/what-is-batch.html) or [AWS ParallelCluster](https://docs.aws.amazon.com/parallelcluster/latest/ug/what-is-aws-parallelcluster.html)\.

For more information about creating launch templates, see [Create a launch template](ec2-launch-templates.md#create-launch-template)\.

## Manage IP addresses for an EFA<a name="efa-manage-ip-address"></a>

You can change the IP addresses associated with an EFA\. If you have an Elastic IP address, you can associate it with an EFA\. If your EFA is provisioned in a subnet that has an associated IPv6 CIDR block, you can assign one or more IPv6 addresses to the EFA\.

You assign an Elastic IP \(IPv4\) and IPv6 address to an EFA in the same way that you assign an IP address to an elastic network interface\. For more information, see [Managing IP addresses](using-eni.md#managing-network-interface-ip-addresses)\.

## Change the security group for an EFA<a name="efa-security"></a>

 You can change the security group that is associated with an EFA\. To enable OS\-bypass functionality, the EFA must be a member of a security group that allows all inbound and outbound traffic to and from the security group itself\.

You change the security group that is associated with an EFA in the same way that you change the security group that is associated with an elastic network interface\. For more information, see [Changing the security group](using-eni.md#modify-groups)\.

## Detach an EFA<a name="efa-detach"></a>

To detach an EFA from an instance, you must first stop the instance\. You cannot detach an EFA from an instance that is in the running state\.

You detach an EFA from an instance in the same way that you detach an elastic network interface from an instance\. For more information, see [Detach a network interface from an instance](using-eni.md#detach_eni)\.

## View EFAs<a name="efa-view"></a>

You can view all of the EFAs in your account\.

You view EFAs in the same way that you view elastic network interfaces\. For more information, see [View details about a network interface](using-eni.md#view_eni_details)\.

## Delete an EFA<a name="efa-delete"></a>

To delete an EFA, you must first detach it from the instance\. You cannot delete an EFA while it is attached to an instance\.

You delete EFAs in the same way that you delete elastic network interfaces\. For more information, see [Delete a network interface](using-eni.md#delete_eni)\.