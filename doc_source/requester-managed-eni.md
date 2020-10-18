# Requester\-managed network interfaces<a name="requester-managed-eni"></a>

A requester\-managed network interface is a network interface that an AWS service creates in your VPC\. This network interface can represent an instance for another service, such as an Amazon RDS instance, or it can enable you to access another service or resource, such as an AWS PrivateLink service, or an Amazon ECS task\.

You cannot modify or detach a requester\-managed network interface\. If you delete the resource that the network interface represents, the AWS service detaches and deletes the network interface for you\. To change the security groups for a requester\-managed network interface, you might have to use the console or command line tools for that service\. For more information, see the service\-specific documentation\.

You can tag a requester\-managed network interface\. For more information, see [Adding or editing tags](using-eni.md#eni_add_edit_tags)\.

You can view the requester\-managed network interfaces that are in your account\.

**To view requester\-managed network interfaces using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and view the following information on the details pane:
   + **Attachment owner**: If you created the network interface, this field displays your AWS account ID\. Otherwise, it displays an alias or ID for the principal or service that created the network interface\.
   + **Description**: Provides information about the purpose of the network interface; for example, "VPC Endpoint Interface"\.

**To view requester\-managed network interfaces using the command line**

1. Use the [describe\-network\-interfaces](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interfaces.html) AWS CLI command to describe the network interfaces in your account\. 

   ```
   aws ec2 describe-network-interfaces
   ```

1. In the output, the `RequesterManaged` field displays `true` if the network interface is managed by another AWS service\.

   ```
   {
       "Status": "in-use",
        ...
       "Description": "VPC Endpoint Interface vpce-089f2123488812123", 
       "NetworkInterfaceId": "eni-c8fbc27e", 
       "VpcId": "vpc-1a2b3c4d", 
       "PrivateIpAddresses": [
           {
               "PrivateDnsName": "ip-10-0-2-227.ec2.internal", 
               "Primary": true, 
               "PrivateIpAddress": "10.0.2.227"
           }
       ], 
       "RequesterManaged": true, 
        ...
   }
   ```

   Alternatively, use the [Get\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterface.html) Tools for Windows PowerShell command\.