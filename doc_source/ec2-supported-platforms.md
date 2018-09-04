# Supported Platforms<a name="ec2-supported-platforms"></a>

Amazon EC2 supports the following platforms\. Your AWS account is capable of launching instances either into both platforms or only into EC2\-VPC, on a region by region basis\. 


| Platform | Introduced In | Description | 
| --- | --- | --- | 
|  EC2\-Classic  |  The original release of Amazon EC2  |  Your instances run in a single, flat network that you share with other customers\.  | 
|  EC2\-VPC  |  The original release of Amazon VPC  |  Your instances run in a virtual private cloud \(VPC\) that's logically isolated to your AWS account\.  | 

For more information about the availability of either platform in your account, see [Availability](http://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html#default-vpc-availability) in the *Amazon VPC User Guide*\. For more information about the differences between EC2\-Classic and EC2\-VPC, see [Differences Between EC2\-Classic and EC2\-VPC](using-vpc.md#differences-ec2-classic-vpc)\.

## Supported Platforms in the Amazon EC2 Console<a name="console-updates"></a>

The Amazon EC2 console indicates which platforms you can launch instances into for the selected region, and whether you have a default VPC in that region\.

Verify that the region you'll use is selected in the navigation bar\. On the Amazon EC2 console dashboard, look for **Supported Platforms** under **Account Attributes**\. If there are two values, `EC2` and `VPC`, you can launch instances into either platform\. If there is one value, `VPC`, you can launch instances only into EC2\-VPC\.

If you can launch instances only into EC2\-VPC, we create a default VPC for you\. Then, when you launch an instance, we launch it into your default VPC, unless you create a nondefault VPC and specify it when you launch the instance\.

### EC2\-VPC<a name="vpc-only"></a>

The dashboard displays the following under **Account Attributes** to indicate that the account supports only the EC2\-VPC platform, and has a default VPC with the identifier `vpc-1a2b3c4d`\.

![\[The Supported Platforms indicator\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vpc_indicator.png)

If your account supports only EC2\-VPC, you can select a VPC from the **Network** list, and a subnet from the **Subnet** list when you launch an instance using the launch wizard\.

![\[Instance details: default VPC\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vpc_only_launch.png)

### EC2\-Classic, EC2\-VPC<a name="both-platforms"></a>

The dashboard displays the following under **Account Attributes** to indicate that the account supports both the EC2\-Classic and EC2\-VPC platforms\.

![\[The Supported Platforms indicator\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/classic_indicator.png)

If your account supports EC2\-Classic and EC2\-VPC, you can launch into EC2\-Classic using the launch wizard by selecting **Launch into EC2\-Classic** from the **Network** list\. To launch into a VPC, you can select a VPC from the **Network** list, and a subnet from the **Subnet** list\.

### Related Topic<a name="related-topic"></a>

For more information about how you can tell which platforms you can launch instances into, see [Detecting Your Supported Platforms](http://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html#detecting-platform) in the *Amazon VPC User Guide*\.