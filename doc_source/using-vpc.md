# Virtual private clouds<a name="using-vpc"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to define a virtual network in your own logically isolated area within the AWS cloud, known as a *virtual private cloud* or *VPC*\. You can create AWS resources, such as Amazon EC2 instances, into the subnets of your VPC\. Your VPC closely resembles a traditional network that you might operate in your own data center, with the benefits of using scalable infrastructure from AWS\. You can configure your VPC; you can select its IP address range, create subnets, and configure route tables, network gateways, and security settings\. You can connect instances in your VPC to the internet or to your own data center\.

**Default VPCs**  
When you create your AWS account, we create a *default VPC* in each Region\. A default VPC is a VPC that is already configured and ready for you to use\. For example, there is a default subnet for each Availability Zone in each default VPC, an internet gateway attached to the VPC, and there's a route in the main route table that sends all traffic \(0\.0\.0\.0/0\) to the internet gateway\. Alternatively, you can create your own nondefault VPC and configure the VPC, subnets, and routing to meet your needs\.

![\[We create a default VPC in each Region, with a default subnet in each Availability Zone.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/default-vpc.png)

**Accessing the internet from instances in your VPCs**  
Instances launched into a default subnet have access to the internet, as the VPC is configured to assign public IP addresses and DNS hostnames, and the main route table is configured with a route to an internet gateway attached to the VPC\.

For the subnets that you create in your VPCs, do one of the following to ensure that instances that you launch in these subnets have access to the internet:
+ Configure an internet gateway\. For more information, see [Connect subnets to the internet using an internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.
+ Configure a public NAT gateway\. For more information, see [Access the internet from a private subnet](https://docs.aws.amazon.com/vpc/latest/userguide/nat-gateway-scenarios.html#public-nat-internet-access) in the *Amazon VPC User Guide*\.

To connect to an instance, you must also authorize the traffic to the instance and specify a key pair when you launch the instance\. For more information, see [General prerequisites for connecting to your instance](connection-prereqs.md)\.