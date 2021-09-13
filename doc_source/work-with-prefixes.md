# Work with prefixes<a name="work-with-prefixes"></a>

**Topics**
+ [Assign prefixes during network interface creation](#create-net-interface-prefix)
+ [Assign prefixes to existing network interfaces](#assign-net-interface-prefix)
+ [Configure your operating system for network interfaces with prefixes](#configure-os)
+ [View the prefixes assigned to your network interfaces](#view-prefix)
+ [Remove prefixes from your network interfaces](#unassign-prefix)

## Assign prefixes during network interface creation<a name="create-net-interface-prefix"></a>

If you use the automatic assignment option, you can reserve a block of IP addresses in your subnet\. AWS chooses the prefixes from this block\. For more information, see [Subnet CIDR reservations](https://docs.aws.amazon.com/vpc/latest/userguide/subnet-cidr-reservation.html) in the *Amazon VPC User Guide*\.

After you have created the network interface, use the [attach\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/attach-network-interface.html) AWS CLI command to attach the network interface to your instance\. You must configure your operating system to work with network interfaces with prefixes\. For more information, see [Configure your operating system for network interfaces with prefixes](#configure-os)\.

**Topics**
+ [Assign automatic prefixes during network interface creation](#assign-auto-creation)
+ [Assign specific prefixes during network interface creation](#assign-specific-creation)

### Assign automatic prefixes during network interface creation<a name="assign-auto-creation"></a>

You can assign automatic prefixes during network interface creation using one of the following methods\.

------
#### [ Console ]

**To assign automatic prefixes during network interface creation**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces** and then choose **Create network interface**\.

1. Specify a description for the network interface, select the subnet in which to create the network interface, and configure the private IPv4 and IPv6 addresses\.

1. Expand **Advanced settings** and do the following:

   1. To automatically assign an IPv4 prefix, for **IPv4 prefix delegation**, choose **Auto\-assign**\. Then for **Number of IPv4 prefixes**, specify the number of prefixes to assign\.

   1. To automatically assign an IPv6 prefix, for **IPv6 prefix delegation**, choose **Auto\-assign**\. Then for **Number of IPv6 prefixes**, specify the number of prefixes to assign\.
**Note**  
**IPv6 prefix delegation** appears only if the selected subnet is enabled for IPv6\.

1. Select the security groups to associate with the network interface and assign resource tags if needed\.

1. Choose **Create network interface**\.

------
#### [ AWS CLI ]

**To assign automatic IPv4 prefixes during network interface creation**  
Use the [ create\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-network-interface.html) command and set `--ipv4-prefix-count` to the number of prefixes that you want AWS to assign\. In the following example, AWS assigns `1` prefix\.

```
$ aws ec2 create-network-interface \
--subnet-id subnet-047cfed18eEXAMPLE \
--description "IPv4 automatic example" \
--ipv4-prefix-count 1
```

Example output

```
{
    "NetworkInterface": {
        "AvailabilityZone": "us-west-2a",
        "Description": "IPv4 automatic example",
        "Groups": [
            {
                "GroupName": "default",
                "GroupId": "sg-044c2de2c4EXAMPLE"
            }
        ],
        "InterfaceType": "interface",
        "Ipv6Addresses": [],
        "MacAddress": "02:98:65:dd:18:47",
        "NetworkInterfaceId": "eni-02b80b4668EXAMPLE",
        "OwnerId": "123456789012",
        "PrivateIpAddress": "10.0.0.62",
        "PrivateIpAddresses": [
            {
                "Primary": true,
                "PrivateIpAddress": "10.0.0.62"
            }
        ],
        "Ipv4Prefixes": [
            {
                "Ipv4Prefix": "10.0.0.208/28"
            }
        ],
        "RequesterId": "AIDAIV5AJI5LXF5XXDPCO",
        "RequesterManaged": false,
        "SourceDestCheck": true,
        "Status": "pending",
        "SubnetId": "subnet-047cfed18eEXAMPLE",
        "TagSet": [],
        "VpcId": "vpc-0e12f52b21EXAMPLE"
    }
}
```

**To assign automatic IPv6 prefixes during network interface creation**  
Use the [ create\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-network-interface.html) command and set `--ipv6-prefix-count` to the number of prefixes that you want AWS to assign\. In the following example, AWS assigns `1` prefix\.

```
$ aws ec2 create-network-interface \
--subnet-id subnet-047cfed18eEXAMPLE \
--description "IPv6 automatic example" \
--ipv6-prefix-count 1
```

Example output

```
{
    "NetworkInterface": {
        "AvailabilityZone": "us-west-2a",
        "Description": "IPv6 automatic example",
        "Groups": [
            {
                "GroupName": "default",
                "GroupId": "sg-044c2de2c4EXAMPLE"
            }
        ],
        "InterfaceType": "interface",
        "Ipv6Addresses": [],
        "MacAddress": "02:bb:e4:31:fe:09",
        "NetworkInterfaceId": "eni-006edbcfa4EXAMPLE",
        "OwnerId": "123456789012",
        "PrivateIpAddress": "10.0.0.73",
        "PrivateIpAddresses": [
            {
                "Primary": true,
                "PrivateIpAddress": "10.0.0.73"
            }
        ],
        "Ipv6Prefixes": [
            {
                "Ipv6Prefix": "2600:1f13:fc2:a700:1768::/80"
            }
        ],
        "RequesterId": "AIDAIV5AJI5LXF5XXDPCO",
        "RequesterManaged": false,
        "SourceDestCheck": true,
        "Status": "pending",
        "SubnetId": "subnet-047cfed18eEXAMPLE",
        "TagSet": [],
        "VpcId": "vpc-0e12f52b21EXAMPLE"
    }
}
```

------

### Assign specific prefixes during network interface creation<a name="assign-specific-creation"></a>

You can assign specific prefixes during network interface creation using one of the following methods\.

------
#### [ Console ]

**To assign specific prefixes during network interface creation**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces** and then choose **Create network interface**\.

1. Specify a description for the network interface, select the subnet in which to create the network interface, and configure the private IPv4 and IPv6 addresses\.

1. Expand **Advanced settings** and do the following:

   1. To assign a specific IPv4 prefix, for **IPv4 prefix delegation**, choose **Custom**\. Then choose **Add new prefix** and enter the prefix to use\.

   1. To assign a specific IPv6 prefix, for **IPv6 prefix delegation**, choose **Custom**\. Then choose **Add new prefix** and enter the prefix to use\.
**Note**  
**IPv6 prefix delegation** appears only if the selected subnet is enabled for IPv6\.

1. Select the security groups to associate with the network interface and assign resource tags if needed\.

1. Choose **Create network interface**\.

------
#### [ AWS CLI ]

**To assign specific IPv4 prefixes during network interface creation**  
Use the [ create\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-network-interface.html) command and set `--ipv4-prefixes` to the prefixes\. AWS selects IP addresses from this range\. In the following example, the prefix CIDR is `10.0.0.208/28`\.

```
$ aws ec2 create-network-interface \
    --subnet-id subnet-047cfed18eEXAMPLE \
    --description "IPv4 manual example" \
    --ipv4-prefixes Ipv4Prefix=10.0.0.208/28
```

Example output

```
{
        "NetworkInterface": {
            "AvailabilityZone": "us-west-2a",
            "Description": "IPv4 manual example",
            "Groups": [
                {
                    "GroupName": "default",
                    "GroupId": "sg-044c2de2c4EXAMPLE"
                }
            ],
            "InterfaceType": "interface",
            "Ipv6Addresses": [],
            "MacAddress": "02:98:65:dd:18:47",
            "NetworkInterfaceId": "eni-02b80b4668EXAMPLE",
            "OwnerId": "123456789012",
            "PrivateIpAddress": "10.0.0.62",
            "PrivateIpAddresses": [
                {
                    "Primary": true,
                    "PrivateIpAddress": "10.0.0.62"
                }
            ],
            "Ipv4Prefixes": [
                {
                    "Ipv4Prefix": "10.0.0.208/28"
                }
            ],
            "RequesterId": "AIDAIV5AJI5LXF5XXDPCO",
            "RequesterManaged": false,
            "SourceDestCheck": true,
            "Status": "pending",
            "SubnetId": "subnet-047cfed18eEXAMPLE",
            "TagSet": [],
            "VpcId": "vpc-0e12f52b21EXAMPLE"
        }
    }
```

**To assign specific IPv6 prefixes during network interface creation**  
Use the [ create\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-network-interface.html) command and set `--ipv6-prefixes` to the prefixes\. AWS selects IP addresses from this range\. In the following example, the prefix CIDR is `2600:1f13:fc2:a700:1768::/80`\.

```
$ aws ec2 create-network-interface \
    --subnet-id subnet-047cfed18eEXAMPLE \
    --description "IPv6 manual example" \
    --ipv6-prefixes Ipv6Prefix=2600:1f13:fc2:a700:1768::/80
```

Example output

```
{
        "NetworkInterface": {
            "AvailabilityZone": "us-west-2a",
            "Description": "IPv6 automatic example",
            "Groups": [
                {
                    "GroupName": "default",
                    "GroupId": "sg-044c2de2c4EXAMPLE"
                }
            ],
            "InterfaceType": "interface",
            "Ipv6Addresses": [],
            "MacAddress": "02:bb:e4:31:fe:09",
            "NetworkInterfaceId": "eni-006edbcfa4EXAMPLE",
            "OwnerId": "123456789012",
            "PrivateIpAddress": "10.0.0.73",
            "PrivateIpAddresses": [
                {
                    "Primary": true,
                    "PrivateIpAddress": "10.0.0.73"
                }
            ],
            "Ipv6Prefixes": [
                {
                    "Ipv6Prefix": "2600:1f13:fc2:a700:1768::/80"
                }
            ],
            "RequesterId": "AIDAIV5AJI5LXF5XXDPCO",
            "RequesterManaged": false,
            "SourceDestCheck": true,
            "Status": "pending",
            "SubnetId": "subnet-047cfed18eEXAMPLE",
            "TagSet": [],
            "VpcId": "vpc-0e12f52b21EXAMPLE"
        }
    }
```

------

## Assign prefixes to existing network interfaces<a name="assign-net-interface-prefix"></a>

After you have assigned the prefixes, use the [ attach\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/attach-network-interface.html) AWS CLI command to attach the network interface to your instance\. You must configure your operating system to work with network interfaces with prefixes\. For more information, see [Configure your operating system for network interfaces with prefixes](#configure-os)\.

### Assign automatic prefixes to an existing network interface<a name="assign-auto-existing"></a>

You can assign automatic prefixes to an existing network interface using one of the following methods\.

------
#### [ Console ]

**To assign automatic prefixes to an existing network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface to which to assign the prefixes, and choose **Actions**, **Manage prefixes**\.

1. To automatically assign an IPv4 prefix, for **IPv4 prefix delegation**, choose **Auto\-assign**\. Then for **Number of IPv4 prefixes**, specify the number of prefixes to assign\.

1. To automatically assign an IPv6 prefix, for **IPv6 prefix delegation**, choose **Auto\-assign**\. Then for **Number of IPv6 prefixes**, specify the number of prefixes to assign\.
**Note**  
**IPv6 prefix delegation** appears only if the selected subnet is enabled for IPv6\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

You can use the [ assign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-ipv6-addresses.html) command to assign IPv6 prefixes and the [assign\-private\-ip\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-private-ip-addresses.html) command to assign IPv4 prefixes to existing network interfaces\.

**To assign automatic IPv4 prefixes to an existing network interface**  
Use the [ assign\-private\-ip\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-private-ip-addresses.html) command and set `--ipv4-prefix-count` to the number of prefixes that you want AWS to assign\. In the following example, AWS assigns `1` IPv4 prefix\.

```
$ aws ec2 assign-private-ip-addresses \
--network-interface-id eni-081fbb4095EXAMPLE \
--ipv4-prefix-count 1
```

Example output

```
{
    "NetworkInterfaceId": "eni-081fbb4095EXAMPLE",
    "AssignedIpv4Prefixes": [
        {
            "Ipv4Prefix": "10.0.0.176/28"
        }
    ]
}
```

**To assign automatic IPv6 prefixes to an existing network interface**  
Use the [ assign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-ipv6-addresses.html) command and set `--ipv6-prefix-count` to the number of prefixes that you want AWS to assign\. In the following example, AWS assigns `1` IPv6 prefix\.

```
$ aws ec2 assign-ipv6-addresses \
--network-interface-id eni-00d577338cEXAMPLE \
--ipv6-prefix-count 1
```

Example output

```
{
    "AssignedIpv6Prefixes": [
        "2600:1f13:fc2:a700:18bb::/80"
    ],
    "NetworkInterfaceId": "eni-00d577338cEXAMPLE"
}
```

------

### Assign specific prefixes to an existing network interface<a name="assign-specific-existing"></a>

You can assign specific prefixes to an existing network interface using one of the following methods\.

------
#### [ Console ]

**To assign specific prefixes to an existing network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface to which to assign the prefixes, and choose **Actions**, **Manage prefixes**\.

1. To assign a specific IPv4 prefix, for **IPv4 prefix delegation**, choose **Custom**\. Then choose **Add new prefix** and enter the prefix to use\.

1. To assign a specific IPv6 prefix, for **IPv6 prefix delegation**, choose **Custom**\. Then choose **Add new prefix** and enter the prefix to use\.
**Note**  
**IPv6 prefix delegation** appears only if the selected subnet is enabled for IPv6\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

**Assign specific IPv4 prefixes to an existing network interface**  
Use the [ assign\-private\-ip\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-private-ip-addresses.html) command and set `--ipv4-prefixes` to the prefix\. AWS selects IPv4 addresses from this range\. In the following example, the prefix CIDR is `10.0.0.208/28`\.

```
$ aws ec2 assign-private-ip-addresses \
--network-interface-id eni-081fbb4095EXAMPLE \
--ipv4-prefixes 10.0.0.208/28
```

Example output

```
{
    "NetworkInterfaceId": "eni-081fbb4095EXAMPLE",
    "AssignedIpv4Prefixes": [
        {
            "Ipv4Prefix": "10.0.0.208/28"
        }
    ]
}
```

**Assign specific IPv6 prefixes to an existing network interface**  
Use the [ assign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-ipv6-addresses.html) command and set `--ipv6-prefixes` to the prefix\. AWS selects IPv6 addresses from this range\. In the following example, the prefix CIDR is `2600:1f13:fc2:a700:18bb::/80`\.

```
$ aws ec2 assign-ipv6-addresses \
--network-interface-id eni-00d577338cEXAMPLE \
--ipv6-prefixes 2600:1f13:fc2:a700:18bb::/80
```

Example output

```
{
    "NetworkInterfaceId": "eni-00d577338cEXAMPLE",
    "AssignedIpv6Prefixes": [
        {
            "Ipv6Prefix": "2600:1f13:fc2:a700:18bb::/80"
        }
    ]
}
```

------

## Configure your operating system for network interfaces with prefixes<a name="configure-os"></a>

Amazon Linux AMIs might contain additional scripts installed by AWS, known as `ec2-net-utils`\. These scripts optionally automate the configuration of your network interfaces\. They are available for Amazon Linux only\.

If you are not using Amazon Linux, you can use a Container Network Interface \(CNI\) for Kubernetes plug\-in, or `dockerd` if you use Docker to manage your containers\.

## View the prefixes assigned to your network interfaces<a name="view-prefix"></a>

You can view the prefixes assigned to your network interfaces using one of the following methods\.

------
#### [ Console ]

**To view the automatic prefixes assigned to an existing network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface for which to view the prefixes and choose the **Details** tab\.

1. The **IPv4 Prefix Delegation** field lists the assigned IPv4 prefixes, and the **IPv6 Prefix Delegation** field lists the assigned IPv6 prefixes\.

------
#### [ AWS CLI ]

You can use the [ describe\-network\-interfaces](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interfaces.html) AWS CLI command to view the prefixes assigned to your network interfaces\.

```
$ aws ec2 describe-network-interfaces
```

Example output

```
{
    "NetworkInterfaces": [
        {
            "AvailabilityZone": "us-west-2a",
            "Description": "IPv4 automatic example",
            "Groups": [
                {
                    "GroupName": "default",
                    "GroupId": "sg-044c2de2c4EXAMPLE"
                }
            ],
            "InterfaceType": "interface",
            "Ipv6Addresses": [],
            "MacAddress": "02:98:65:dd:18:47",
            "NetworkInterfaceId": "eni-02b80b4668EXAMPLE",
            "OwnerId": "123456789012",
            "PrivateIpAddress": "10.0.0.62",
            "PrivateIpAddresses": [
                {
                    "Primary": true,
                    "PrivateIpAddress": "10.0.0.62"
                }
            ],
            "Ipv4Prefixes": [
                {
                    "Ipv4Prefix": "10.0.0.208/28"
                }
            ],
            "Ipv6Prefixes": [],
            "RequesterId": "AIDAIV5AJI5LXF5XXDPCO",
            "RequesterManaged": false,
            "SourceDestCheck": true,
            "Status": "available",
            "SubnetId": "subnet-05eef9fb78EXAMPLE",
            "TagSet": [],
            "VpcId": "vpc-0e12f52b2146bf252"
        },
        {
            "AvailabilityZone": "us-west-2a",
            "Description": "IPv6 automatic example",
            "Groups": [
                {
                    "GroupName": "default",
                    "GroupId": "sg-044c2de2c411c91b5"
                }
            ],
            "InterfaceType": "interface",
            "Ipv6Addresses": [],
            "MacAddress": "02:bb:e4:31:fe:09",
            "NetworkInterfaceId": "eni-006edbcfa4EXAMPLE",
            "OwnerId": "123456789012",
            "PrivateIpAddress": "10.0.0.73",
            "PrivateIpAddresses": [
                {
                    "Primary": true,
                    "PrivateIpAddress": "10.0.0.73"
                }
            ],
            "Ipv4Prefixes": [],
            "Ipv6Prefixes": [
                {
                    "Ipv6Prefix": "2600:1f13:fc2:a700:1768::/80"
                }
            ],
            "RequesterId": "AIDAIV5AJI5LXF5XXDPCO",
            "RequesterManaged": false,
            "SourceDestCheck": true,
            "Status": "available",
            "SubnetId": "subnet-05eef9fb78EXAMPLE",
            "TagSet": [],
            "VpcId": "vpc-0e12f52b21EXAMPLE"
        }
    ]
}
```

------

## Remove prefixes from your network interfaces<a name="unassign-prefix"></a>

You can remove prefixes from your network interfaces using one of the following methods\.

------
#### [ Console ]

**To remove the prefixes from a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface from which to remove the prefixes and choose **Actions**, **Manage prefixes**\.

1. Do one of the following:
   + To remove all assigned prefixes, for **IPv4 prefix delegation** and **IPv6 prefix delegation**, choose **Do not assign**\.
   + To remove specific assigned prefixes, for **IPv4 prefix delegation** or **IPv6 prefix delegation**, choose **Custom** and then choose **Unassign ** next to the prefixes to remove\.
**Note**  
**IPv6 prefix delegation** appears only if the selected subnet is enabled for IPv6\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

You can use the [ unassign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/unassign-ipv6-addresses.html) command to remove IPv6 prefixes and the [unassign\-private\-ip\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/unassign-private-ip-addresses.html) commands to remove IPv4 prefixes from your existing network interfaces\.

**To remove IPv4 prefixes from a network interface**  


Use the [unassign\-private\-ip\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/unassign-private-ip-addresses.html) command and set `--ipv4-prefix` to the address that you want to remove\.

```
$ aws ec2 unassign-private-ip-addresses \
--network-interface-id eni-081fbb4095EXAMPLE \
--ipv4-prefixes 10.0.0.176/28
```

**To remove IPv6 prefixes from a network interface**  
Use the [ unassign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/unassign-ipv6-addresses.html) command and set `--ipv6-prefix` to the address that you want to remove\.

```
$ aws ec2 unassign-ipv6-addresses \
--network-interface-id eni-00d577338cEXAMPLE \
--ipv6-prefix 2600:1f13:fc2:a700:18bb::/80
```

------