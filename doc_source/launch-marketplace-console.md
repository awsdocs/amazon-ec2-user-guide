# Launching an AWS Marketplace Instance<a name="launch-marketplace-console"></a>

You can subscribe to an AWS Marketplace product and launch an instance from the product's AMI using the Amazon EC2 launch wizard\. For more information about paid AMIs, see [Paid AMIs](paid-amis.md)\. To cancel your subscription after launch, you first have to terminate all instances running from it\. For more information, see [Managing Your AWS Marketplace Subscriptions](paid-amis.md#marketplace-manage-subscriptions)\. 

**To launch an instance from the AWS Marketplace using the launch wizard**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the Amazon EC2 dashboard, choose **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, choose the **AWS Marketplace** category on the left\. Find a suitable AMI by browsing the categories, or using the search functionality\. Choose **Select** to choose your product\.

1. A dialog displays an overview of the product you've selected\. You can view the pricing information, as well as any other information that the vendor has provided\. When you're ready, choose **Continue**\.
**Note**  
You are not charged for using the product until you have launched an instance with the AMI\. Take note of the pricing for each supported instance type, as you will be prompted to select an instance type on the next page of the wizard\. Additional taxes may also apply to the product\.

1. On the **Choose an Instance Type** page, select the hardware configuration and size of the instance to launch\. When you're done, choose **Next: Configure Instance Details**\.

1. On the next pages of the wizard, you can configure your instance, add storage, and add tags\. For more information about the different options you can configure, see [Launching an instance using the Launch Instance Wizard](launching-instance.md)\. Choose **Next** until you reach the **Configure Security Group** page\. 

   The wizard creates a new security group according to the vendor's specifications for the product\. The security group may include rules that allow all IPv4 addresses \(`0.0.0.0/0`\) access on SSH \(port 22\) on Linux or RDP \(port 3389\) on Windows\. We recommend that you adjust these rules to allow only a specific address or range of addresses to access your instance over those ports\.

   When you are ready, choose **Review and Launch**\.

1. On the **Review Instance Launch** page, check the details of the AMI from which you're about to launch the instance, as well as the other configuration details you set up in the wizard\. When you're ready, choose **Launch** to select or create a key pair, and launch your instance\.

1. Depending on the product you've subscribed to, the instance may take a few minutes or more to launch\. You are first subscribed to the product before your instance can launch\. If there are any problems with your credit card details, you will be asked to update your account details\. When the launch confirmation page displays, choose **View Instances** to go to the Instances page\. 
**Note**  
You are charged the subscription price as long as your instance is running, even if it is idle\. If your instance is stopped, you may still be charged for storage\.

1. When your instance is in the **running** state, you can connect to it\. To do this, select your instance in the list and choose **Connect**\. Follow the instructions in the dialog\. For more information about connecting to your instance, see [Connect to Your Linux Instance](AccessingInstances.md)\.
**Important**  
Check the vendor's usage instructions carefully, as you may need to use a specific user name to log in to the instance\. For more information about accessing your subscription details, see [Managing Your AWS Marketplace Subscriptions](paid-amis.md#marketplace-manage-subscriptions)\.

1. If the instance fails to launch or the state immediately goes to `terminated` instead of `running`, see [Troubleshooting Instance Launch Issues](troubleshooting-launch.md)\.

## Launching an AWS Marketplace AMI Instance Using the API and CLI<a name="launch-marketplace-cli-api"></a>

To launch instances from AWS Marketplace products using the API or command line tools, first ensure that you are subscribed to the product\. You can then launch an instance with the product's AMI ID using the following methods:


| Method | Documentation | 
| --- | --- | 
|  AWS CLI  |  Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command, or see the following topic for more information: [Launching an Instance](https://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-launch.html#launching-instances)\.  | 
|  AWS Tools for Windows PowerShell   |  Use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command, or see the following topic for more information: [Launch an Amazon EC2 Instance Using Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-ec2-launch.html)  | 
| Query API | Use the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-RunInstances.html) request\. | 