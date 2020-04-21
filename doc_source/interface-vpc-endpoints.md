# Amazon EC2 and interface VPC endpoints<a name="interface-vpc-endpoints"></a>

You can improve the security posture of your VPC by configuring Amazon EC2 to use an interface VPC endpoint\. Interface endpoints are powered by AWS PrivateLink, a technology that enables you to privately access Amazon EC2 APIs by restricting all network traffic between your VPC and Amazon EC2 to the Amazon network\. With interface endpoints, you also don't need an internet gateway, a NAT device, or a virtual private gateway\.

You are not required to configure AWS PrivateLink, but it's recommended\. For more information about AWS PrivateLink and VPC endpoints, see [Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html)\.

**Topics**
+ [Create an interface VPC endpoint](#create-endpoint)
+ [Create an interface VPC endpoint policy](#endpoint-policy)

## Create an interface VPC endpoint<a name="create-endpoint"></a>

Create an endpoint for Amazon EC2 using the following service name:
+ **`com.amazonaws.region.ec2`** — Creates an endpoint for the Amazon EC2 API actions\.

For more information, see [Creating an Interface Endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint) in the *Amazon VPC User Guide*\.

## Create an interface VPC endpoint policy<a name="endpoint-policy"></a>

You can attach a policy to your VPC endpoint to control access to the Amazon EC2 API\. The policy specifies:
+ The principal that can perform actions\.
+ The actions that can be performed\.
+ The resource on which the actions can be performed\.

**Important**  
When a non\-default policy is applied to an interface VPC endpoint for Amazon EC2, certain failed API requests, such as those failing from `RequestLimitExceeded`, might not be logged to AWS CloudTrail or Amazon CloudWatch\.

For more information, see [ Controlling Access to Services with VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html) in the * Amazon VPC User Guide*\.

The following example shows a VPC endpoint policy that denies permission to create unencrypted volumes or to launch instances with unencrypted volumes\. The example policy also grants permission to perform all other Amazon EC2 actions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
    {
        "Action": "ec2:*",
        "Effect": "Allow",
        "Resource": "*"
    },
    {
        "Action": [
            "ec2:CreateVolume"
        ],
        "Effect": "Deny",
        "Resource": "*",
        "Condition": {
            "Bool": {
                "ec2:Encrypted": "false"
            }
        }
    },
    {
        "Action": [
            "ec2:RunInstances"
        ],
        "Effect": "Deny",
        "Resource": "*",
        "Condition": {
            "Bool": {
                "ec2:Encrypted": "false"
            }
        }
    }]
}
```