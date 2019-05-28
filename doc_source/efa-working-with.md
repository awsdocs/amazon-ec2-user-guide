# Working with EFA<a name="efa-working-with"></a>

Elastic Fabric Adapters \(EFAs\) can be created, used, and managed much like any other elastic network interface \(ENI\) in Amazon EC2\. However, unlike ENIs, EFAs cannot be attached to or detached from an instance in a running state\.

## EFA Requirements<a name="efa-reqs"></a>

To use EFA, you must do the following:
+ Use one of the following supported instance types: c5n\.18xlarge, i3en\.24xlarge, p3dn\.24xlarge
+ Use one of the following supported AMIs: Amazon Linux, Amazon Linux 2, Red Hat Enterprise Linux 7\.6, CentOS 7\.6
+ Install the EFA software components\.
+ Use a security group that allows all inbound and outbound traffic to and from the security group itself\.

**Topics**
+ [EFA Requirements](#efa-reqs)
+ [Creating an EFA](#efa-create)
+ [Attaching an EFA to a Stopped Instance](#efa-attach)
+ [Attaching an EFA When Launching an Instance](#efa-launch)
+ [Adding an EFA to a Launch Template](#efa-launch-template)
+ [Assigning an IP Address to an EFA](#efa-ip-assign)
+ [Unassigning an IP Address from an EFA](#efa-ip-unassign)
+ [Changing the Security Group](#efa-security)
+ [Detaching an EFA](#efa-detach)
+ [Viewing EFAs](#efa-view)
+ [Deleting an EFA](#efa-delete)

## Creating an EFA<a name="efa-create"></a>

You can create an EFA in a subnet in a VPC\. You can't move the EFA to another subnet after it's created, and you can only attach it to stopped instances in the same Availability Zone\.

**To create a new EFA \(Console\)**

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

**To create a new EFA \(AWS CLI\)**  
Use the [create\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-network-interface.html) command and for `interface-type`, specify `efa`\. For example:

```
$ aws ec2 create-network-interface --subnet-id subnet-01234567890 --description example_efa --interface-type efa
```

## Attaching an EFA to a Stopped Instance<a name="efa-attach"></a>

You can attach an EFA to any supported instance that is in the `stopped` state\. You cannot attach an EFA to an instance that is in the `running` state\. For more information about the supported instance types, see [Supported Instance Types](efa.md#efa-instance-types)\.

You attach an EFA to an instance in the same way that you attach an ENI to an instance\. For more information, see [Attaching a Network Interface to a Stopped or Running Instance](using-eni.md#attach_eni_running_stopped)\.

## Attaching an EFA When Launching an Instance<a name="efa-launch"></a>

**To attach an existing EFA when launching an instance \(AWS CLI\)**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and for **NetworkInterfaceId**, specify the EFA's ID\. For example:

```
$ aws ec2 run-instances --image-id ami-123456 --count 1 --instance-type c5n.18xlarge --key-name my_key_pair --security-group-ids sg-123456 --subnet-id subnet-123456 --network-interfaces DeviceIndex=0,NetworkInterfaceId=eni-123456
```

**To attach a new EFA when launching an instance \(AWS CLI\)**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and for **InterfaceType**, specify `efa`\. For example:

```
$ aws ec2 run-instances --image-id ami-123456 --count 1 --instance-type c5n.18xlarge --key-name my_key_pair --security-group-ids sg-123456 --subnet-id subnet-123456 --network-interfaces DeviceIndex=0,InterfaceType=efa
```

## Adding an EFA to a Launch Template<a name="efa-launch-template"></a>

You can create a launch template that contains the configuration information needed to launch EFA\-enabled instances\. To create an EFA\-enabled launch template, create a new launch template and specify a supported instance type, your EFA\-enabled AMI, and an EFAenabled\-security group\. For more information about creating an EFA\-enabled AMI and EFA\-enabled security group, see [Getting Started with EFA](efa-start.md)\.

You can leverage launch templates to launch EFA\-enabled instances with other AWS services, such as AWS Batch\.

For more information about creating launch templates, see [Creating a Launch Template](ec2-launch-templates.md#create-launch-template)\.

## Assigning an IP Address to an EFA<a name="efa-ip-assign"></a>

If you have an Elastic IP \(IPv4\) address, you can associate it with an EFA\. And if your EFA is provisioned in a subnet that has an associated IPv6 CIDR block, you can assign one or more IPv6 addresses to the EFA\.

You assign an Elastic IP \(IPv4\) and IPv6 address to an EFA in the same way that you assign an IP address to an ENI\. For more information, see:
+ [Associating an Elastic IP Address \(IPv4\)](using-eni.md#associate_eip)
+ [Assigning an IPv6 Address](using-eni.md#eni-assign-ipv6)

## Unassigning an IP Address from an EFA<a name="efa-ip-unassign"></a>

You unassign an Elastic IP \(IPv4\) and IPv6 address from an EFA in the same way that you unassign an IP address from an ENI\. For more information, see:
+ [Disassociating an Elastic IP Address \(IPv4\)](using-eni.md#disassociate_eip)
+ [Unassigning an IPv6 Address](using-eni.md#eni-unassign-ipv6)

## Changing the Security Group<a name="efa-security"></a>

 You can change the security group that is associated with an EFA\. To enable OS\-bypass functionality, the EFA must be a member of a security group that allows all inbound and outbound traffic to and from the security group itself\.

You change the security group that is associated with an EFA in the same way that you change the security group that is associated with an ENI\. For more information, see [Changing the Security Group](using-eni.md#eni_security_group)\.

## Detaching an EFA<a name="efa-detach"></a>

To detach an EFA from an instance, you must first stop the instance\. You cannot detach an EFA from an instance that is in the running state\.

You detach an EFA from an instance in the same way that you detach an ENI from an instance\. For more information, see [Detaching a Network Interface from an Instance](using-eni.md#detach_eni)\.

## Viewing EFAs<a name="efa-view"></a>

You can view all of the EFAs in your account\.

You view EFAs in the same way that you view ENIs\. For more information, see [Viewing Details about a Network Interface](using-eni.md#view_eni_details)\.

## Deleting an EFA<a name="efa-delete"></a>

To delete an EFA, you must first detach it from the instance\. You cannot delete an EFA while it is attached to an instance\.

You delete EFAs in the same way that you delete ENIs\. For more information, see [Deleting a Network Interface](using-eni.md#delete_eni)\.