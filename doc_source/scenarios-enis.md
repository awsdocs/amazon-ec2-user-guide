# Scenarios for network interfaces<a name="scenarios-enis"></a>

Attaching multiple network interfaces to an instance is useful when you want to:
+ Create a management network\.
+ Use network and security appliances in your Virtual Private Cloud \(VPC\)\.
+ Create dual\-homed instances with workloads/roles on distinct subnets\.
+ Create a low\-budget, high\-availability solution\.

## Create a management network<a name="creating-a-management-network"></a>

This scenario describes how you can create a management network with network interfaces, given the following criteria and settings \(image follows\)\.

**Criteria**
+ The primary network interface on the instance \(*eth0*\) handles public traffic\.
+ The secondary network interface on the instance \(*eth1*\) handles backend management traffic\. It's connected to a separate subnet that has more restrictive access controls, and is located within the same Availability Zone \(AZ\) as the primary network interface\.

**Settings**
+ The primary network interface, which may or may not be behind a load balancer, has an associated security group that allows access to the server from the internet\. For example, allow TCP port 80 and 443 from `0.0.0.0/0` or from the load balancer\.
+ The secondary network interface has an associated security group that allows SSH access only, initiated from one of the following locations:
  + An allowed range of IP addresses, either within the VPC, or from the internet\.
  + A private subnet within the same AZ as the primary network interface\.
  + A virtual private gateway\.

**Note**  
To ensure failover capabilities, consider using a secondary private IPv4 for incoming traffic on a network interface\. In the event of an instance failure, you can move the interface and/or secondary private IPv4 address to a standby instance\.

![\[Creating a management network\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EC2_ENI_management_network.png)

## Use network and security appliances in your VPC<a name="use-network-and-security-appliances-in-your-vpc"></a>

Some network and security appliances, such as load balancers, network address translation \(NAT\) servers, and proxy servers prefer to be configured with multiple network interfaces\. You can create and attach secondary network interfaces to instances that are running these types of applications and configure the additional interfaces with their own public and private IP addresses, security groups, and source/destination checking\.

## Creating dual\-homed instances with workloads/roles on distinct subnets<a name="creating-dual-homed-instances-with-workloads-roles-on-distinct-subnets"></a>

You can place a network interface on each of your web servers that connects to a mid\-tier network where an application server resides\. The application server can also be dual\-homed to a backend network \(subnet\) where the database server resides\. Instead of routing network packets through the dual\-homed instances, each dual\-homed instance receives and processes requests on the front end, initiates a connection to the backend, and then sends requests to the servers on the backend network\.

## Create a low budget high availability solution<a name="create-a-low-budget-high-availability-solution"></a>

If one of your instances serving a particular function fails, its network interface can be attached to a replacement or hot standby instance pre\-configured for the same role in order to rapidly recover the service\. For example, you can use a network interface as your primary or secondary network interface to a critical service such as a database instance or a NAT instance\. If the instance fails, you \(or more likely, the code running on your behalf\) can attach the network interface to a hot standby instance\. Because the interface maintains its private IP addresses, Elastic IP addresses, and MAC address, network traffic begins flowing to the standby instance as soon as you attach the network interface to the replacement instance\. Users experience a brief loss of connectivity between the time the instance fails and the time that the network interface is attached to the standby instance, but no changes to the route table or your DNS server are required\.