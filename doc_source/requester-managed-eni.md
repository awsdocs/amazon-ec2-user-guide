# Requester\-managed network interfaces<a name="requester-managed-eni"></a>

A requester\-managed network interface is a network interface that an AWS service creates in your VPC on your behalf\. The network interface is associated with a resource for another service, such as a DB instance from Amazon RDS, a NAT gateway, or an interface VPC endpoint from AWS PrivateLink\.

**Considerations**
+ You can view the requester\-managed network interfaces in your account\. You can add or remove tags, but you can't change other properties of a requester\-managed network interface\.
+ You can't detach a requester\-managed network interface\.
+ When you delete the resource associated with a requester\-managed network interface, the AWS service detaches the network interface and deletes it\. If the service detached a network interface but didn't delete it, you can delete the detached network interface\.

**To view requester\-managed network interfaces using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network & Security**, **Network Interfaces**\.

1. Select the ID of the network interface to open its details page\.

1. The following are the key fields that you can use to determine the purpose of the network interface:
   + **Description**: A description provided by the AWS service that created the interface\. For example, "VPC Endpoint Interface vpce 089f2123488812123"\.
   + **Requester\-managed**: Indicates whether the network interface is managed by AWS\.
   + **Requester ID**: The alias or AWS account ID of the principal or service that created the network interface\. If you created the network interface, this is your AWS account ID\. Otherwise, another principal or service created it\.

**To view requester\-managed network interfaces using the AWS CLI**  
Use the [describe\-network\-interfaces](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interfaces.html) command as follows\.

```
aws ec2 describe-network-interfaces --filters Name=requester-managed,Values=true
```

The following is example output that shows the key fields that you can use to determine the purpose of the network interface: `Description` and `InterfaceType`\.

```
{
     ...
    "Description": "VPC Endpoint Interface vpce-089f2123488812123",
    ...
    "InterfaceType": "vpc_endpoint",
    ...
    "NetworkInterfaceId": "eni-0d11e3ccd2c0e6c57",
    ...
    "RequesterId": "727180483921",
    "RequesterManaged": true, 
    ...
}
```

**To view requester\-managed network interfaces using the Tools for Windows PowerShell**  
Use the [Get\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterface.html) cmdlet as follows\.

```
Get-EC2NetworkInterface -Filter @{ Name="requester-managed"; Values="true" }
```

The following is example output that shows the key fields that you can use to determine the purpose of the network interface: `Description` and `InterfaceType`\.

```
Description        : VPC Endpoint Interface vpce-089f2123488812123
...
InterfaceType      : vpc_endpoint
...
NetworkInterfaceId : eni-0d11e3ccd2c0e6c57
...
RequesterId        : 727180483921
RequesterManaged   : True 
...
```