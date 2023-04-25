# Virtual private clouds<a name="using-vpc"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to define a virtual network in your own logically isolated area within the AWS cloud, known as a *virtual private cloud* or *VPC*\. You can create AWS resources, such as Amazon EC2 instances, into the subnets of your VPC\. Your VPC closely resembles a traditional network that you might operate in your own data center, with the benefits of using scalable infrastructure from AWS\. You can configure your VPC; you can select its IP address range, create subnets, and configure route tables, network gateways, and security settings\. You can connect instances in your VPC to the internet or to your own data center\.

## Your default VPCs<a name="default-vpcs"></a>

When you create your AWS account, we create a *default VPC* in each Region\. A default VPC is a VPC that is already configured and ready for you to use\. For example, there is a default subnet for each Availability Zone in each default VPC, an internet gateway attached to the VPC, and there's a route in the main route table that sends all traffic \(0\.0\.0\.0/0\) to the internet gateway\. Alternatively, you can create your own VPC and configure it to meet your needs\.

![\[We create a default VPC in each Region, with a default subnet in each Availability Zone.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/default-vpc.png)

## Create additional VPCs<a name="create-nondefault-vpcs"></a>

Use the following procedure to create a VPC with the subnets, gateways, and routing configuration that you need\.

**To create a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Create VPC**\.

1. Under **Resources to create**, choose **VPC and more**\.

1. For **Name tag auto\-generation**, enter a name for the VPC\.

1. For **IPv4 CIDR block**, either keep the default suggestion, enter the CIDR block required by your application or network\.

1. For **Number of Availability Zones**, choose **2**, so that you can launch instances in multiple Availability Zones to ensure high availability\.

1. If your instances must be accessible from the internet, do one of the following:
   + If your instances can be in a public subnet, select a nonzero value for **Number of public subnets**\. Keep both options under **DNS options** selected\. You can optionally add private subnets now or later on\.
   + If your instances must be in a private subnet, select **0** for **Number of public subnets**\. For **Number of private subnets**, select a number depending on your needs \(the possible values correspond to 1 or 2 private subnets per Availability Zone\)\. For **NAT gateways**, if your instances in both Availability Zones send or receive a significant volume of traffic across Availability Zones, select **1 per AZ**\. Otherwise, select **In 1 AZ** and launch instances that send or receive cross\-zone traffic in the same Availability Zone as the NAT gateway\.

1. Expand **Customize subnet CIDR blocks**\. Either keep the default suggestions, or enter a CIDR block for each subnet\. For more information, see [Subnet sizing](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html#subnet-sizing) in the *Amazon VPC User Guide*\.

1. Review the **Preview** pane, which shows the VPC resources that will be created based on your selections\.

1. Choose **Create VPC**\.

## Access the internet from your instances<a name="access-internet-from-vpc"></a>

Instances launched into a default subnet have access to the internet, as the VPC is configured to assign public IP addresses and DNS hostnames, and the main route table is configured with a route to an internet gateway attached to the VPC\.

For the subnets that you create in your VPCs, do one of the following to ensure that instances that you launch in these subnets have access to the internet:
+ Configure an internet gateway\. For more information, see [Connect subnets to the internet using an internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.
+ Configure a public NAT gateway\. For more information, see [Access the internet from a private subnet](https://docs.aws.amazon.com/vpc/latest/userguide/nat-gateway-scenarios.html#public-nat-internet-access) in the *Amazon VPC User Guide*\.

## SSH access to your instances<a name="ssh-access-to-vpc"></a>

To connect to an instance, you must authorize SSH traffic to the instance from your network\. You must also specify a key pair when you launch the instance and specify the `.pem` file when you connect to the instance\. For more information, see [Set up to connect to your instance](connection-prereqs.md)\.