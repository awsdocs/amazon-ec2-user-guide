# Launch your instance<a name="LaunchingAndUsingInstances"></a>

An instance is a virtual server in the AWS Cloud\. You launch an instance from an Amazon Machine Image \(AMI\)\. The AMI provides the operating system, application server, and applications for your instance\.

When you sign up for AWS, you can get started with Amazon EC2 for free using the [AWS Free Tier](https://aws.amazon.com/)\. You can use the free tier to launch and use a micro instance for free for 12 months\. If you launch an instance that is not within the free tier, you incur the standard Amazon EC2 usage fees for the instance\. For more information, see the [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing)\.

You can launch an instance using the following methods\.


| Method | Documentation | 
| --- | --- | 
|  \[Amazon EC2 console\] Use the launch instance wizard to specify the launch parameters\.  |  [Launching an instance using the Launch Instance Wizard](launching-instance.md)  | 
|  \[Amazon EC2 console\] Create a launch template and launch the instance from the launch template\.  |  [Launching an Instance from a Launch Template](ec2-launch-templates.md)  | 
| \[Amazon EC2 console\] Use an existing instance as the base\. |  [Launching an instance using parameters from an existing instance](launch-more-like-this.md)  | 
|  \[Amazon EC2 console\] Use an Amazon EBS snapshot that you created\.  |  [Launching a Linux instance from a backup](instance-launch-snapshot.md)  | 
|  \[Amazon EC2 console\] Use an AMI that you purchased from the AWS Marketplace\.  |  [Launching an AWS Marketplace Instance](launch-marketplace-console.md)  | 
|  \[AWS CLI\] Use an AMI that you select\.  |  [Using Amazon EC2 through the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-ec2-instances.html)  | 
|  \[AWS Tools for Windows PowerShell\] Use an AMI that you select\.  |  [Amazon EC2 from the AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-ec2.html)  | 
|  \[AWS CLI\] Use EC2 Fleet to provision capacity across different EC2 instance types and Availability Zones, and across On\-Demand Instance, Reserved Instance, and Spot Instance purchase models\.   |  [Launching an EC2 Fleet](ec2-fleet.md)  | 

When you launch your instance, you can launch your instance in a subnet that is associated with one of the following resources:
+ An Availability Zone \- This option is the default\.
+ A Local Zone \- To launch an instance in a Local Zone, you must opt in to the feature\. For more information, see [Opting in to a Local Zone](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#using-regions-availability-zones-opt-in-local)\.
+ An Outpost \- To launch an instance in an Outpost, you must create an Outpost\. For information about how to create an Outpost, see [Get Started with AWS Outposts](https://docs.aws.amazon.com/outposts/latest/userguide/get-started-outposts.html) in the *AWS Outposts User Guide*\.

After you launch your instance, you can connect to it and use it\. To begin, the instance state is `pending`\. When the instance state is `running`, the instance has started booting\. There might be a short time before you can connect to the instance\. The instance receives a public DNS name that you can use to contact the instance from the internet\. The instance also receives a private DNS name that other instances within the same VPC can use to contact the instance\. For more information about connecting to your instance, see [Connect to Your Linux Instance](AccessingInstances.md)\.

When you are finished with an instance, be sure to terminate it\. For more information, see [Terminate Your Instance](terminating-instances.md)\.