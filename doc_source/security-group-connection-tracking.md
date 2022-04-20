# Security group connection tracking<a name="security-group-connection-tracking"></a>

Your security groups use connection tracking to track information about traffic to and from the instance\. Rules are applied based on the connection state of the traffic to determine if the traffic is allowed or denied\. With this approach, security groups are stateful\. This means that responses to inbound traffic are allowed to flow out of the instance regardless of outbound security group rules, and vice versa\.

As an example, suppose that you initiate a command such as netcat or similar to your instances from your home computer, and your inbound security group rules allow ICMP traffic\. Information about the connection \(including the port information\) is tracked\. Response traffic from the instance for the command is not tracked as a new request, but rather as an established connection, and is allowed to flow out of the instance, even if your outbound security group rules restrict outbound ICMP traffic\.

For protocols other than TCP, UDP, or ICMP, only the IP address and protocol number is tracked\. If your instance sends traffic to another host \(host B\), and host B initiates the same type of traffic to your instance in a separate request within 600 seconds of the original request or response, your instance accepts it regardless of inbound security group rules\. Your instance accepts it because it’s regarded as response traffic\.

To ensure that traffic is immediately interrupted when you remove a security group rule, or to ensure that all inbound traffic is subject to firewall rules, you can use a network ACL for your subnet\. Network ACLs are stateless and therefore do not automatically allow response traffic\. For more information, see [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html) in the *Amazon VPC User Guide*\.

## Untracked connections<a name="untracked-connections"></a>

Not all flows of traffic are tracked\. If a security group rule permits TCP or UDP flows for all traffic \(0\.0\.0\.0/0 or ::/0\) and there is a corresponding rule in the other direction that permits all response traffic \(0\.0\.0\.0/0 or ::/0\) for all ports \(0\-65535\), then that flow of traffic is not tracked\. The response traffic is therefore allowed to flow based on the inbound or outbound rule that permits the response traffic, and not on tracking information\.

An untracked flow of traffic is immediately interrupted if the rule that enables the flow is removed or modified\. For example, if you have an open \(0\.0\.0\.0/0\) outbound rule, and you remove a rule that allows all \(0\.0\.0\.0/0\) inbound SSH \(TCP port 22\) traffic to the instance \(or modify it such that the connection would no longer be permitted\), your existing SSH connections to the instance are immediately dropped\. The connection was not previously being tracked, so the change will break the connection\. On the other hand, if you have a narrower inbound rule that initially allows the SSH connection \(meaning that the connection was tracked\), but change that rule to no longer allow new connections from the address of the current SSH client, the existing connection will not be broken by changing the rule\.

## Example<a name="connection-tracking-example"></a>

In the following example, the security group has specific inbound rules for TCP and ICMP traffic, and outbound rules that allow all outbound IPv4 and IPv6 traffic\.


| 
| 
| **Inbound rules** | 
| --- |
| Protocol type | Port number | Source IP | 
| TCP  | 22 \(SSH\) | 203\.0\.113\.1/32 | 
| TCP  | 80 \(HTTP\) | 0\.0\.0\.0/0 | 
| TCP  | 80 \(HTTP\) | ::/0 | 
| ICMP | All | 0\.0\.0\.0/0 | 
| **Outbound rules** | 
| --- |
| Protocol type | Port number | Destination IP | 
| All | All | 0\.0\.0\.0/0 | 
| All | All | ::/0 | 
+ TCP traffic on port 22 \(SSH\) to and from the instance is tracked, because the inbound rule allows traffic from 203\.0\.113\.1/32 only, and not all IP addresses \(0\.0\.0\.0/0\)\.
+ TCP traffic on port 80 \(HTTP\) to and from the instance is not tracked, because both the inbound and outbound rules allow all traffic \(0\.0\.0\.0/0 or ::/0\)\.
+ ICMP traffic is always tracked, regardless of rules\.
+ If you remove the outbound rule from the security group, all traffic to and from the instance is tracked, including traffic on port 80 \(HTTP\)\.

## Throttling<a name="connection-tracking-throttling"></a>

Amazon EC2 defines the maximum number of connections that can be tracked per instance\. After the maximum is reached, any packets that are sent or received are dropped because a new connection cannot be established\. When this happens, applications that send and receive packets cannot communicate properly\.

To determine whether packets were dropped because the network traffic for your instance exceeded the maximum number of connections that can be tracked, use the `conntrack_allowance_exceeded` network performance metric\. For more information, see [Monitor network performance for your EC2 instance](monitoring-network-performance-ena.md)\.

Connections made through the following are automatically tracked, even if the security group configuration does not require tracking:
+ Gateway Load Balancers
+ Global Accelerator accelerators
+ NAT gateways
+ Network Firewall firewall endpoints
+ Network Load Balancers
+ Transit gateway attachments

With Elastic Load Balancing, if you exceed the maximum number of connections that can be tracked per instance, we recommend that you scale either the number of instances registered with the load balancer or the size of the instances registered with the load balancer\.