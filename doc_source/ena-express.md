# Improve network performance with ENA Express on Linux instances<a name="ena-express"></a>

ENA Express is powered by AWS Scalable Reliable Datagram \(SRD\) technology\. SRD is a high performance network transport protocol that uses dynamic routing to increase throughput and minimize tail latency\. With ENA Express, you can communicate between two EC2 instances in the same subnet\.

**Benefits of ENA Express**
+ Increases the maximum bandwidth a single flow can use from 5 Gbps to 25 Gbps within the subnet\.
+ Reduces tail latency of network traffic between EC2 instances, especially during periods of high network load\.
+ Detects and avoids congested network paths\.
+ Handles some tasks directly in the network layer, such as packet reordering on the receiving end, and most retransmits that are needed\. This frees up the application layer for other work\.

**Note**  
If your application sends or receives a high volume of packets per second, and needs to optimize for latency most of the time, especially during periods when there is no congestion on the network, [Enhanced networking](enhanced-networking.md) might be a better fit for your network\.

During periods of time when network traffic is light, you might notice a slight increase in packet latency \(tens of microseconds\) when the packet uses ENA Express\. During those times, applications that prioritize specific network performance characteristics can benefit from ENA Express as follows:
+ Processes can benefit from increased maximum single flow bandwidth from 5 Gbps to 25 Gbps within the same subnet\.
+ Longer running processes should experience reduced tail latency during periods of network congestion\.
+ Processes can benefit from a smoother and more standard distribution for network response times\.

## Prerequisites<a name="ena-express-prerequisites"></a>

To ensure that ENA Express can operate effectively, update the following settings for your instance\.
+ If your instance uses jumbo frames, run the following command to set your maximum transmission unit \(MTU\) to `8900`\.

  ```
  [ec2-user ~]$ sudo ip link set dev eth0 mtu 8900
  ```
+ Increase the receiver \(Rx\) ring size, as follows:

  ```
  [ec2-user ~]$ ethtool -G device rx 8192
  ```

## How ENA Express works<a name="ena-express-how-it-works"></a>

SRD technology uses a packet spraying mechanism to distribute load and avoid network congestion\. It distributes packets for each network flow across different AWS network paths, and dynamically adjusts distribution when it detects signs of congestion\. It also manages packet reordering on the receiving end\.

To ensure that ENA Express can manage network traffic as intended, sending and receiving instances and the communication between them must meet all of the following requirements:
+ Both sending and receiving instance types are supported\. See the [Supported instance types for ENA Express](#ena-express-supported-instance-types) table for more information\.
+ Both sending and receiving instances must have ENA Express configured\. If there are differences in the configuration, you can run into situations where traffic defaults to standard ENA transmission\. The following scenario shows what can happen\.

  **Scenario: Differences in configuration**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ena-express.html)

  In this case, TCP traffic between the two instances can use ENA Express, as both instances have enabled it\. However, since one of the instances does not use ENA Express for UDP traffic, communication between these two instances over UDP uses standard ENA transmission\.
+ The sending and receiving instances must run in the same subnet\.
+ The network path between the instances must not include middleware boxes\. ENA Express doesn't currently support middleware boxes\.
+ To utilize full bandwidth potential, use driver version 2\.2\.9 or higher\.
+ To produce metrics, use driver version 2\.8 or higher\.

If any requirement is unmet, the instances use the standard TCP/UDP protocol but without SRD to communicate\.

To ensure that your instance network driver is configured for optimum performance, review the recommended best practices for ENA drivers\. These best practices apply to ENA Express, as well\. For more information, see the [ENA Linux Driver Best Practices and Performance Optimization Guide](https://github.com/amzn/amzn-drivers/blob/master/kernel/linux/ena/ENA_Linux_Best_Practices.rst) on the GitHub website\.

**Note**  
Amazon EC2 refers to the relationship between an instance and a network interface that's attached to it as an *attachment*\. ENA Express settings apply to the attachment\. If the network interface is detached from the instance, the attachment no longer exists, and the ENA Express settings that applied to it are no longer in force\. The same is true when an instance is terminated, even if the network interface remains\.

## Supported instance types for ENA Express<a name="ena-express-supported-instance-types"></a>

The following table contains instance types that support ENA Express\.


| Instance family | Instance type | Instance size | Architecture | 
| --- | --- | --- | --- | 
| General purpose | m6i | m6i\.32xl, m6i\.metal | x86\_64 | 
| General purpose | m6id | m6id\.32xl, m6id\.metal | x86\_64 | 
| Compute optimized | c6gn | c6gn\.16xl | arm64 | 
| Compute optimized | c6i | c6i\.32xl, c6i\.metal | x86\_64 | 
| Compute optimized | c6id | c6id\.32xl, c6id\.metal | x86\_64 | 
| Memory optimized | r6i | r6i\.32xl, r6i\.metal | x86\_64 | 
| Memory optimized | r6id | r6id\.32xl, r6id\.metal | x86\_64 | 
| Storage optimized | i4i | i4i\.32xl, i4i\.metal | x86\_64 | 
| Storage optimized | im4gn | im4gn\.16xl | arm64 | 

## List and view ENA Express settings<a name="ena-express-list-view"></a>

This section covers how to list and view ENA Express information from the AWS Management Console or from the AWS CLI\. For more information, choose the tab that matches the method you'll use\.

------
#### [ Console ]

This tab covers how to find information about your current ENA Express settings and to view instance type support in the AWS Management Console\.

**View instance type support**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instance types**\.

1. Select an instance type to see the details for that instance\. You can choose the **Instance type** link to open the detail page, or you can select the checkbox on the left side of the list to view details in the detail pane at the bottom of the page\.

1. In the **Networking** tab or that section on the detail page, **ENA Express support** shows a true or false value to indicate if the instance type supports this feature\.

**View settings from the Network interface list**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Network interfaces**\.

1. Select a network interface to see the details for that instance\. You can choose the **Network interface ID** link to open the detail page, or you can select the checkbox on the left side of the list to view details in the detail pane at the bottom of the page\.

1. In the **Network interface attachment** section on the the **Details** tab or detail page, review settings for **ENA Express** and **ENA Express UDP**\.

**View settings from instances**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**\.

1. Select an instance to see the details for that instance\. You can choose the **Instance ID** link to open the detail page, or you can select the checkbox on the left side of the list to view details in the detail pane at the bottom of the page\.

1. In the **Network interfaces** section on the **Networking** tab, scroll right to review settings for **ENA Express** and **ENA Express UDP**\.

------
#### [ AWS CLI ]

This tab covers how to find information about your current ENA Express settings and to view instance type support in the AWS CLI\.

**Describe instance types**  
For information on instance type settings for a specific instance type, run the [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-instance-types.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-instance-types.html) command in the AWS CLI, and substitute the instance type as follows:

```
[ec2-user ~]$ aws ec2 describe-instance-types --instance-types m6i.metal
{
"InstanceTypes": [
    {
        "InstanceType": "m6i.metal",
        "CurrentGeneration": true,
        ...
        },
        "NetworkInfo": {
            ...
            "EnaSrdSupported": true
        },
        ...
    }
]
}
```

**Describe network interfaces**  
For information on instance type settings, run the [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-network-interfaces.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-network-interfaces.html) command in the AWS CLI as follows:

```
[ec2-user ~]$ aws ec2 describe-network-interfaces
{
"NetworkInterfaces": [
    {
        "Association": {
            ....IPs, DNS...
        },
        "Attachment": {
            "AttachTime": "2022-11-17T09:04:28+00:00",
            "AttachmentId": "eni-attach-0ab1c23456d78e9f0",
            "DeleteOnTermination": true,
            "DeviceIndex": 0,
            "NetworkCardIndex": 0,
            "InstanceId": "i-0abcd123e456fabcd",
            "InstanceOwnerId": "111122223333",
            "Status": "attached",
            "EnaSrdSpecification": {
                "EnaSrdEnabled": true,
                "EnaSrdUdpSpecification": {
                    "EnaSrdUdpEnabled": true
                }
            }
        },
        ...
        "NetworkInterfaceId": "eni-0d1234e5f6a78901b",
        "OwnerId": "111122223333",
        ...
    }
]
}
```

------

## Configure ENA Express settings<a name="ena-express-configure"></a>

You can configure ENA Express for supported EC2 instance types without needing to install any additional software\. This section covers how to configure ENA Express from the AWS Management Console or from the AWS CLI\. For more information, choose the tab that matches the method you'll use\.

------
#### [ Console ]

This tab covers how to manage ENA Express settings for network interfaces that are attached to an instance\.

**Manage ENA Express from the Network interface list**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Network interfaces**\.

1. Select a network interface that is attached to an instance\. You can choose the **Network interface ID** link to open the detail page, or you can select the checkbox on the left side of the list\.

1. Choose **Manage ENA Express** from the **Action** menu at the top right side of the page\. This opens the **Manage ENA Express** dialog, with the selected network interface ID and current settings displayed\.
**Note**  
If the network interface you selected is not attached to an instance, this action does not appear in the menu\.

1. To use **ENA Express**, select the **Enable** check box\.

1. When ENA Express is enabled, you can configure UDP settings\. To use **ENA Express UDP**, select the **Enable** check box\.

1. To save your settings, choose **Save**\.

**Manage ENA Express from the Instance list**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**\.

1. Select the instance that you want to manage\. You can choose the **Instance ID** to open the detail page, or you can select the checkbox on the left side of the list\.

1. Select the **Network interface** to configure for your instance\.

1. Choose **Manage ENA Express** from the **Action** menu at the top right side of the page\.

1. To configure ENA Express for a network interface that's attached to your instance, select it from the **Network interface** list\.

1. To use **ENA Express** for the selected network interface attachment, select the **Enable** check box\.

1. When ENA Express is enabled, you can configure UDP settings\. To use **ENA Express UDP**, select the **Enable** check box\.

1. To save your settings, choose **Save**\.

**Configure ENA Express when you attach a network interface to an EC2 instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Network interfaces**\.

1. Select a network interface that is not attached to an instance \(**Status** is *Available*\)\. You can choose the **Network interface ID** link to open the detail page, or you can select the checkbox on the left side of the list\.

1. Select the **Instance** that you'll attach to\.

1. To use **ENA Express** after you attach the network interface to the instance, select the **Enable** check box\.

1. When ENA Express is enabled, you can configure UDP settings\. To use **ENA Express UDP**, select the **Enable** check box\.

1. To attach the network interface to the instance and save your ENA Express settings, choose **Attach**\.

------
#### [ AWS CLI ]

This tab covers how to configure ENA Express settings in the AWS CLI\.

**Configure ENA Express when you attach a network interface**  
To configure ENA Express when you attach a network interface to an instance, run the [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/attach-network-interface.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/attach-network-interface.html) command in the AWS CLI, as shown in the following examples:

**Example 1: Use ENA Express for TCP traffic, but not for UDP traffic**

In this example, we configure `EnaSrdEnabled` as *true*, and we allow `EnaSrdUdpEnabled` to default to *false*\.

```
[ec2-user ~]$ aws ec2 attach-network-interface --network-interface-id eni-0123f4567890a1b23 --instance-id i-0f1a234b5cd67e890 --device-index 1 --ena-srd-specification 'EnaSrdEnabled=true'
{
"AttachmentId": "eni-attach-012c3d45e678f9012"
}
```

**Example 2: Use ENA Express for both TCP traffic and UDP traffic**

In this example, we configure both `EnaSrdEnabled` and `EnaSrdUdpEnabled` as *true*\.

```
[ec2-user ~]$ aws ec2 attach-network-interface --network-interface-id eni-0123f4567890a1b23 --instance-id i-0f1a234b5cd67e890 --device-index 1 --ena-srd-specification 'EnaSrdEnabled=true,EnaSrdUdpSpecification={EnaSrdUdpEnabled=true}'
{
"AttachmentId": "eni-attach-012c3d45e678f9012"
}
```

**Update ENA Express settings for your network interface attachment**  
To update ENA Express settings for a network interface that's attached to an instance, run the [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/modify-network-interface-attribute.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/modify-network-interface-attribute.html) command in the AWS CLI, as shown in the following examples:

**Example 1: Use ENA Express for TCP traffic, but not for UDP traffic**

In this example, we configure `EnaSrdEnabled` as *true*, and we allow `EnaSrdUdpEnabled` to default to *false* if it has never been set previously\.

```
[ec2-user ~]$ aws ec2 modify-network-interface-attribute --network-interface-id eni-0123f4567890a1b23 --ena-srd-specification 'EnaSrdEnabled=true'
```

**Example 2: Use ENA Express for both TCP traffic and UDP traffic**

In this example, we configure both `EnaSrdEnabled` and `EnaSrdUdpEnabled` as *true*\.

```
[ec2-user ~]$ aws ec2 modify-network-interface-attribute --network-interface-id eni-0123f4567890a1b23 --ena-srd-specification 'EnaSrdEnabled=true,EnaSrdUdpSpecification={EnaSrdUdpEnabled=true}'
```

**Example 3: Stop using ENA Express for UDP traffic**

In this example, we configure `EnaSrdUdpEnabled` as *false*\.

```
[ec2-user ~]$ aws ec2 modify-network-interface-attribute --network-interface-id eni-0123f4567890a1b23 --ena-srd-specification 'EnaSrdUdpSpecification={EnaSrdUdpEnabled=false}'
```

------

## Monitor ENA Express performance<a name="ena-express-monitor"></a>

After you've enabled ENA Express for the network interface attachments on both the sending instance and receiving instance, you can use ENA Express metrics to help ensure that your instances take full advantage of the performance improvements that SRD technology provides\.

To see a list of metrics that are filtered for ENA Express, run the following ethtool command for your network interface \(shown here as `eth0`\):

```
[ec2-user ~]$ ethtool -S eth0 | grep ena_srd
NIC statistics:
	ena_srd_mode: 0
	ena_srd_tx_pkts: 0
	pena_srd_eligible_tx_pkts: 0
	ena_srd_rx_pkts: 0
	ena_srd_resource_utilization: 0
```

**Verify ENA Express settings for an instance**  
To verify the current ENA Express settings for the network interface attachment on your instance, run the `ethtool` command to list ENA Express metrics, and take note of the value of the `ena_srd_mode` metric\. Values are as follows: 
+ `0` = ENA Express off, UDP off
+ `1` = ENA Express on, UDP off
+ `2` = ENA Express off, UDP on
**Note**  
This only happens when ENA Express was originally enabled, and UDP was configured to use it\. The prior value is retained for UDP traffic\.
+ `3` = ENA Express on, UDP on

After you've enabled ENA Express for the network interface attachment on an instance, the sending instance initiates communication with the receiving instance, and SRD detects if ENA Express is operating on both the sending instance and the reciving instance\. If ENA Express is operating, the communication can use SRD transmission\. If ENA Express is not operating, the communication falls back to standard ENA transmission\. To confirm if packet transmission is using SRD, you can compare the number of eligible packets \(`ena_srd_eligible_tx_pkts` metric\) to the number of SRD packets transmitted \(`ena_srd_tx_pkts` metric\) during a given time period\.

You can monitor your SRD resource utlization using the the `ena_srd_resource_utilization` metric\. If your instance is close to exhausting its SRD resources, you'll know it's time to scale out the instance\.

For more information about ENA Express metrics, see [Metrics for ENA Express](monitoring-network-performance-ena.md#network-performance-metrics-ena-express)\.