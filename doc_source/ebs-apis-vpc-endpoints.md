# EBS direct APIs and interface VPC endpoints<a name="ebs-apis-vpc-endpoints"></a>

You can establish a private connection between your VPC and EBS direct APIs by creating an *interface VPC endpoint*\. Interface endpoints are powered by [AWS PrivateLink](http://aws.amazon.com/privatelink), a technology that enables you to privately access EBS direct APIs without an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC don't need public IP addresses to communicate with EBS direct APIs\. Traffic between your VPC and EBS direct APIs does not leave the Amazon network\. 

Each interface endpoint is represented by one or more [Elastic Network Interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) in your subnets\. 

For more information, see [Interface VPC endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon VPC User Guide*\. 

## Considerations for EBS direct APIs VPC endpoints<a name="vpc-endpoint-considerations"></a>

Before you set up an interface VPC endpoint for EBS direct APIs, ensure that you review [Interface endpoint properties and limitations](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#vpce-interface-limitations) in the *Amazon VPC User Guide*\. 

VPC endpoint policies are not supported for EBS direct APIs\. By default, full access to EBS direct APIs is allowed through the endpoint\. However, you can control access to the interface endpoint using security groups\. For more information, see [ Controlling access to services with VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html#vpc-endpoints-security-groups) in the *Amazon VPC User Guide*\.

## Creating an interface VPC endpoint for EBS direct APIs<a name="vpc-endpoint-create"></a>

You can create a VPC endpoint for the EBS direct APIs service using either the Amazon VPC console or the AWS Command Line Interface \(AWS CLI\)\. For more information, see [Creating an interface endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint) in the *Amazon VPC User Guide*\.

Create a VPC endpoint for EBS direct APIs using the following service name: 
+ `com.amazonaws.region.ebs`

If you enable private DNS for the endpoint, you can make API requests to EBS direct APIs using its default DNS name for the Region, for example, `ebs.us-east-1.amazonaws.com`\. For more information, see [Accessing a service through an interface endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#access-service-though-endpoint) in the *Amazon VPC User Guide*\.