# Network maximum transmission unit \(MTU\) for your EC2 instance<a name="network_mtu"></a>

The maximum transmission unit \(MTU\) of a network connection is the size, in bytes, of the largest permissible packet that can be passed over the connection\. The larger the MTU of a connection, the more data that can be passed in a single packet\. Ethernet packets consist of the frame, or the actual data you are sending, and the network overhead information that surrounds it\.

Ethernet frames can come in different formats, and the most common format is the standard Ethernet v2 frame format\. It supports 1500 MTU, which is the largest Ethernet packet size supported over most of the internet\. The maximum supported MTU for an instance depends on its instance type\. All Amazon EC2 instance types support 1500 MTU, and many current instance sizes support 9001 MTU, or jumbo frames\.

If your instance runs in a Wavelength Zone, the maximum MTU value is 1300\.

To see Network MTU information for Windows instances, switch to this page in the *Amazon EC2 User Guide for Windows Instances* guide: [Network maximum transmission unit \(MTU\) for your EC2 instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/network_mtu.html)\.

**Topics**
+ [Jumbo frames \(9001 MTU\)](#jumbo_frame_instances)
+ [Path MTU Discovery](#path_mtu_discovery)
+ [Check the path MTU between two hosts](#check_path_mtu)
+ [Check and set the MTU on your Linux instance](#set_mtu)
+ [Troubleshoot](#mtu-troubleshooting)

## Jumbo frames \(9001 MTU\)<a name="jumbo_frame_instances"></a>

Jumbo frames allow more than 1500 bytes of data by increasing the payload size per packet, and thus increasing the percentage of the packet that is not packet overhead\. Fewer packets are needed to send the same amount of usable data\. However, outside of a given AWS Region \(EC2\-Classic\), a single VPC, or a VPC peering connection, you will experience a maximum path of 1500 MTU\. VPN connections and traffic sent over an internet gateway are limited to 1500 MTU\. If packets are over 1500 bytes, they are fragmented, or they are dropped if the `Don't Fragment` flag is set in the IP header\.

Jumbo frames should be used with caution for internet\-bound traffic or any traffic that leaves a VPC\. Packets are fragmented by intermediate systems, which slows down this traffic\. To use jumbo frames inside a VPC and not slow traffic that's bound for outside the VPC, you can configure the MTU size by route, or use multiple elastic network interfaces with different MTU sizes and different routes\.

For instances that are collocated inside a cluster placement group, jumbo frames help to achieve the maximum network throughput possible, and they are recommended in this case\. For more information, see [Placement groups](placement-groups.md)\.

You can use jumbo frames for traffic between your VPCs and your on\-premises networks over AWS Direct Connect\. For more information, and for how to verify Jumbo Frame capability, see [Setting Network MTU](https://docs.aws.amazon.com/directconnect/latest/UserGuide/set-jumbo-frames-vif.html) in the *AWS Direct Connect User Guide*\.

All [current generation instances](instance-types.md#instance-type-summary-table) support jumbo frames\. The following previous generation instances support jumbo frames: A1, C3, G2, I2, M3, and R3\.

For more information about supported MTU sizes for transit gateways, see [MTU](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-quotas.html#mtu-quota) in *Amazon VPC Transit Gateways*\.

## Path MTU Discovery<a name="path_mtu_discovery"></a>

Path MTU Discovery \(PMTUD\) is used to determine the maximum transmission unit \(MTU\) of a network path\. Path MTU is the maximum packet size between the originating host and the receiving host\. If a host sends a packet that's larger than the MTU of the receiving host or that's larger than the MTU of a device along the path, the receiving host or device returns the following ICMP message: `Destination Unreachable: Fragmentation Needed and Don't Fragment was Set` \(Type 3, Code 4\)\. This instructs the original host to adjust the MTU until the packet can be transmitted\.

By default, security groups do not allow any inbound ICMP traffic\. However, security groups are stateful, therefore ICMP responses to outbound requests are allowed to flow in, regardless of security group rules\. Therefore, you do not need to explicitly add an inbound ICMP rule to ensure that your instance can receive the ICMP message response\. For more information about configuring ICMP rules in a network ACL, see [Path MTU Discovery](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#path_mtu_discovery) in the *Amazon VPC User Guide*\.

**Important**  
Path MTU Discovery does not guarantee that jumbo frames will not be dropped by some routers\. An internet gateway in your VPC will forward packets up to 1500 bytes only\. 1500 MTU packets are recommended for internet traffic\.

## Check the path MTU between two hosts<a name="check_path_mtu"></a>

You can check the path MTU between two hosts using the tracepath command, which is part of the `iputils` package that is available by default on many Linux distributions, including Amazon Linux\. 

**To check path MTU using tracepath**  
Use the following command to check the path MTU between your EC2 instance and another host\. You can use a DNS name or an IP address as the destination\. If the destination is another EC2 instance, verify that the security group allows inbound UDP traffic\. This example checks the path MTU between an EC2 instance and `amazon.com`\.

```
[ec2-user ~]$ tracepath amazon.com
 1?: [LOCALHOST]     pmtu 9001
 1:  ip-172-31-16-1.us-west-1.compute.internal (172.31.16.1)   0.187ms pmtu 1500
 1:  no reply
 2:  no reply
 3:  no reply
 4:  100.64.16.241 (100.64.16.241)                          0.574ms
 5:  72.21.222.221 (72.21.222.221)                         84.447ms asymm 21
 6:  205.251.229.97 (205.251.229.97)                       79.970ms asymm 19
 7:  72.21.222.194 (72.21.222.194)                         96.546ms asymm 16
 8:  72.21.222.239 (72.21.222.239)                         79.244ms asymm 15
 9:  205.251.225.73 (205.251.225.73)                       91.867ms asymm 16
...
31:  no reply
     Too many hops: pmtu 1500
     Resume: pmtu 1500
```

In this example, the path MTU is 1500\.

## Check and set the MTU on your Linux instance<a name="set_mtu"></a>

Some instances are configured to use jumbo frames, and others are configured to use standard frame sizes\. You may want to use jumbo frames for network traffic within your VPC or you may want to use standard frames for internet traffic\. Whatever your use case, we recommend verifying that your instance will behave the way you expect it to\. You can use the procedures in this section to check your network interface's MTU setting and modify it if needed\.

**To check the MTU setting on a Linux instance**  
You can check the current MTU value using the following ip command\. Note that in the example output, *mtu 9001* indicates that this instance uses jumbo frames\.

```
[ec2-user ~]$ ip link show eth0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 02:90:c0:b7:9e:d1 brd ff:ff:ff:ff:ff:ff
```

**To set the MTU value on a Linux instance**

1. You can set the MTU value using the ip command\. The following command sets the desired MTU value to 1500, but you could use 9001 instead\.

   ```
   [ec2-user ~]$ sudo ip link set dev eth0 mtu 1500
   ```

1. \(Optional\) To persist your network MTU setting after a reboot, modify the following configuration files, based on your operating system type\.
   + For Amazon Linux 2, add the following line to the `/etc/sysconfig/network-scripts/ifcfg-eth0` file:

     ```
     MTU=1500
     ```

     Add the following line to the `/etc/dhcp/dhclient.conf` file:

     ```
     request subnet-mask, broadcast-address, time-offset, routers, domain-name, domain-search, domain-name-servers, host-name, nis-domain, nis-servers, ntp-servers;
     ```
   + For Amazon Linux, add the following lines to your `/etc/dhcp/dhclient-eth0.conf` file\.

     ```
     interface "eth0" {
     supersede interface-mtu 1500;
     }
     ```
   + For other Linux distributions, consult their specific documentation\.

1. \(Optional\) Reboot your instance and verify that the MTU setting is correct\.

## Troubleshoot<a name="mtu-troubleshooting"></a>

If you experience connectivity issues between your EC2 instance and an Amazon Redshift cluster when using jumbo frames, see [Queries Appear to Hang](https://docs.aws.amazon.com/redshift/latest/mgmt/connecting-drop-issues.html) in the *Amazon Redshift Cluster Management Guide*