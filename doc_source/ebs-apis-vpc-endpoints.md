# Using interface VPC endpoints with EBS direct APIs<a name="ebs-apis-vpc-endpoints"></a>

You can establish a private connection between your VPC and EBS direct APIs by creating an *interface VPC endpoint*, powered by [AWS PrivateLink](privatelink)\. You can access EBS direct APIs as if it were in your VPC, without using an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC don't need public IP addresses to communicate with EBS direct APIs\.

We create an endpoint network interface in each subnet that you enable for the interface endpoint\.

For more information, see [Access AWS services through AWS PrivateLink](https://docs.aws.amazon.com/vpc/latest/privatelink/privatelink-access-aws-services.html) in the *AWS PrivateLink Guide*\.

## Considerations for EBS direct APIs VPC endpoints<a name="vpc-endpoint-considerations"></a>

Before you set up an interface VPC endpoint for EBS direct APIs, review [Considerations](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html#considerations-interface-endpoints) in the *AWS PrivateLink Guide*\.

VPC endpoint policies are not supported for EBS direct APIs\. By default, full access to EBS direct APIs is allowed through the endpoint\. However, you can control access to the interface endpoint using security groups\.

## Create an interface VPC endpoint for EBS direct APIs<a name="vpc-endpoint-create"></a>

You can create a VPC endpoint for EBS direct APIs using either the Amazon VPC console or the AWS Command Line Interface \(AWS CLI\)\. For more information, see [ Create a VPC endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html#create-interface-endpoint-aws) in the *AWS PrivateLink Guide*\.

Create a VPC endpoint for EBS direct APIs using the following service name: 
+ `com.amazonaws.region.ebs`

If you enable private DNS for the endpoint, you can make API requests to EBS direct APIs using its default DNS name for the Region, for example, `ebs.us-east-1.amazonaws.com`\.