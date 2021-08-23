# Assigning prefixes to Amazon EC2 network interfaces<a name="ec2-prefix-eni"></a>

You can assign a private IPv4 or IPv6 CIDR range, either automatically or manually, to your network interfaces\. By assigning prefixes, you scale and simplify the management of applications, including container and networking applications that require multiple IP addresses on an instance\.

The following assignment options are available:
+ **Automatic assignment** — AWS chooses the prefix from your VPC subnet’s IPv4 or IPv6 CIDR and assigns to your network interface\.
+ **Manual Assignment** — You specify the prefix from your VPC subnet’s IPv4 and IPv6 CIDRs, and AWS verifies that the prefix is not already assigned to other resources before assigning it to your network interface\.

Assigning prefixes has the following benefits:
+ Increased IP addresses on a network interface — When you use a prefix, you assign a block of IP addresses as opposed to individual IP addresses\. This increases the number of IP addresses on a network interface\.
+ Simplified VPC management for containers — In container applications, each container requires a unique IP address\. Assigning prefixes to your instance simplifies the management of your VPCs, as you can launch and terminate containers without having to call Amazon EC2 APIs for individual IP assignments\.

 

**Topics**
+ [Basics for assigning prefixes](#ec2-prefix-basics)
+ [Considerations and limits for prefixes](#prefix-limit)
+ [Work with prefixes](work-with-prefixes.md)

## Basics for assigning prefixes<a name="ec2-prefix-basics"></a>
+ You can assign a prefix to new or existing network interfaces\.
+ To use prefixes, you first assign a prefix to your network interface, then attach the network interface to your instance, and then configure your operating system\.
+ When you choose the option to specify a prefix, the prefix must meet the following requirements:
  + The IPv4 prefix that you can specify is `/28`\.
  + The IPv6 prefix that you can specify is `/80`\.
  + The prefix is in the subnet CIDR of the network interface, and it does not overlap with other prefixes or IP addresses assigned to existing resources in the subnet\.
+ You can assign a prefix to the primary or secondary network interface\.
+ You can assign an Elastic IP address to a network interface that has a prefix assigned to it\.
+ We resolve the private DNS host name of an instance to the primary private IPv4 address\.
+ We assign each private IPv4 address on a network interface, including those from prefixes, with the following forms:
  + `us-east-1` Region

    ```
    ip-private-ipv4-address.ec2.internal
    ```
  + All other Regions

    ```
    ip-private-ipv4-address.region.compute.internal
    ```

## Considerations and limits for prefixes<a name="prefix-limit"></a>

Take the following into consideration when you use prefixes:
+ Network interfaces with prefixes are supported with nitro\-based instances\.
+ Prefixes for network interfaces are limited to private IPv4 and IPv6 addresses\.
+ See [IP addresses per network interface per instance type](using-eni.md#AvailableIpPerENI) for limitations\.
+ The number of prefixes and IP addresses on a network interface must be less than the limit on the instance that the network interface is associated with\. For example, if you have a `c5.large` instance, the limit is `10` IPv4 addresses and `10` IPv6 addresses on a network interface, and the total number of `/28` and `/80` prefixes must be less than `10`\.
+ Prefixes are included in source/destination checks\.