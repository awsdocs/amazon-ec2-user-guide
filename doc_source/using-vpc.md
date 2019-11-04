# Virtual Private Clouds<a name="using-vpc"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to define a virtual network in your own logically isolated area within the AWS cloud, known as a *virtual private cloud \(VPC\)*\. You can launch your Amazon EC2 resources, such as instances, into the subnets of your VPC\. Your VPC closely resembles a traditional network that you might operate in your own data center, with the benefits of using scalable infrastructure from AWS\. You can configure your VPC; you can select its IP address range, create subnets, and configure route tables, network gateways, and security settings\. You can connect instances in your VPC to the internet or to your own data center\.

When you create your AWS account, we create a *default VPC* for you in each region\. A default VPC is a VPC that is already configured and ready for you to use\. You can launch instances into your default VPC immediately\. Alternatively, you can create your own *nondefault VPC* and configure it as you need\.

If you created your AWS account before 2013\-12\-04, you might have support for the EC2\-Classic platform in some regions\. If you created your AWS account after 2013\-12\-04, it does not support EC2\-Classic, so you must launch your resources in a VPC\. For more information, see [EC2\-Classic](ec2-classic-platform.md)\.

## Amazon VPC Documentation<a name="amazon-vpc-docs"></a>

For more information about Amazon VPC, see the following documentation\.


| Guide | Description | 
| --- | --- | 
|  [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)  |  Describes key concepts and provides instructions for using the features of Amazon VPC\.  | 
|  [Amazon VPC Peering Guide](https://docs.aws.amazon.com/vpc/latest/peering/)  |  Describes VPC peering connections and provides instructions for using them\.  | 
|  [AWS Site\-to\-Site VPN Network Administrator Guide](https://docs.aws.amazon.com/vpc/latest/adminguide/)  |  Helps network administrators configure customer gateways\.  | 