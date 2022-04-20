# What is Amazon EC2?<a name="concepts"></a>

Amazon Elastic Compute Cloud \(Amazon EC2\) provides scalable computing capacity in the Amazon Web Services \(AWS\) Cloud\. Using Amazon EC2 eliminates your need to invest in hardware up front, so you can develop and deploy applications faster\. You can use Amazon EC2 to launch as many or as few virtual servers as you need, configure security and networking, and manage storage\. Amazon EC2 enables you to scale up or down to handle changes in requirements or spikes in popularity, reducing your need to forecast traffic\.

For more information about cloud computing, see [What is cloud computing?](https://aws.amazon.com/what-is-cloud-computing/)

## Features of Amazon EC2<a name="ec2-features"></a>

Amazon EC2 provides the following features:
+ Virtual computing environments, known as *instances*
+ Preconfigured templates for your instances, known as *Amazon Machine Images \(AMIs\)*, that package the bits you need for your server \(including the operating system and additional software\)
+ Various configurations of CPU, memory, storage, and networking capacity for your instances, known as *instance types*
+ Secure login information for your instances using *key pairs* \(AWS stores the public key, and you store the private key in a secure place\)
+ Storage volumes for temporary data that's deleted when you stop, hibernate, or terminate your instance, known as *instance store volumes*
+ Persistent storage volumes for your data using Amazon Elastic Block Store \(Amazon EBS\), known as *Amazon EBS volumes*
+ Multiple physical locations for your resources, such as instances and Amazon EBS volumes, known as *Regions* and *Availability Zones*
+ A firewall that enables you to specify the protocols, ports, and source IP ranges that can reach your instances using *security groups*
+ Static IPv4 addresses for dynamic cloud computing, known as *Elastic IP addresses*
+ Metadata, known as *tags*, that you can create and assign to your Amazon EC2 resources
+ Virtual networks you can create that are logically isolated from the rest of the AWS Cloud, and that you can optionally connect to your own network, known as *virtual private clouds* \(VPCs\)

For more information about the features of Amazon EC2, see the [Amazon EC2 product page](https://aws.amazon.com/ec2)\.

For more information about running your website on AWS, see [Web Hosting](https://aws.amazon.com/websites/)\.

## How to get started with Amazon EC2<a name="how-to-get-started"></a>

First, you need to get set up to use Amazon EC2\. After you are set up, you are ready to complete the Get Started tutorial for Amazon EC2\. Whenever you need more information about an Amazon EC2 feature, you can read the technical documentation\.

**Get up and running**
+ [Set up to use Amazon EC2](get-set-up-for-amazon-ec2.md)
+ [Tutorial: Get started with Amazon EC2 Linux instances](EC2_GetStarted.md)

**Basics**
+ [Instances and AMIs](ec2-instances-and-amis.md)
+ [Regions and Zones](using-regions-availability-zones.md)
+ [Instance types](instance-types.md)
+ [Tags](Using_Tags.md)

**Networking and security**
+ [Key pairs](ec2-key-pairs.md)
+ [Security groups](ec2-security-groups.md)
+ [Elastic IP addresses](elastic-ip-addresses-eip.md)
+ [Virtual private clouds](using-vpc.md)

**Storage**
+ [Amazon EBS](AmazonEBS.md)
+ [Instance store](InstanceStorage.md)

**Working with Linux instances**
+ [AWS Systems Manager Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html) in the *AWS Systems Manager User Guide*
+ [Tutorial: Install a LAMP web server on Amazon Linux 2](ec2-lamp-amazon-linux-2.md)
+ [Tutorial: Configure SSL/TLS on Amazon Linux 2](SSL-on-amazon-linux-2.md)

If you have questions about whether AWS is right for you, [contact AWS Sales](https://aws.amazon.com/contact-us/)\. If you have technical questions about Amazon EC2, use the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\. 

## Related services<a name="related-services"></a>

You can provision Amazon EC2 resources, such as instances and volumes, directly using Amazon EC2\. You can also provision Amazon EC2 resources using other services in AWS\. For more information, see the following documentation:
+ [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/ec2/userguide/)
+ [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/)
+ [AWS Elastic Beanstalk Developer Guide](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/)
+ [AWS OpsWorks User Guide](https://docs.aws.amazon.com/opsworks/latest/userguide/)

To automatically distribute incoming application traffic across multiple instances, use Elastic Load Balancing\. For more information, see the [Elastic Load Balancing User Guide](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/)\.

To get a managed relational database in the cloud, use Amazon Relational Database Service \(Amazon RDS\) to launch a database instance\. Although you can set up a database on an EC2 instance, Amazon RDS offers the advantage of handling your database management tasks, such as patching the software, backing up, and storing the backups\. For more information, see the [Amazon Relational Database Service Developer Guide](https://docs.aws.amazon.com/AmazonRDS/latest/DeveloperGuide/)\.

To make it easier to manage Docker containers on a cluster of EC2 instances, use Amazon Elastic Container Service \(Amazon ECS\)\. For more information, see the [Amazon Elastic Container Service Developer Guide](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/) or the [Amazon Elastic Container Service User Guide for AWS Fargate](https://docs.aws.amazon.com/AmazonECS/latest/userguide/)\.

To monitor basic statistics for your instances and Amazon EBS volumes, use Amazon CloudWatch\. For more information, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\. 

To detect potentially unauthorized or malicious use of your EC2 instances, use Amazon GuardDuty\. For more information see the [Amazon GuardDuty User Guide](https://docs.aws.amazon.com/guardduty/latest/ug/)\.

## Access Amazon EC2<a name="access-ec2"></a>

Amazon EC2 provides a web\-based user interface, the Amazon EC2 console\. If you've signed up for an AWS account, you can access the Amazon EC2 console by signing into the AWS Management Console and selecting **EC2** from the console home page\.

If you prefer to use a command line interface, you have the following options:

**AWS Command Line Interface \(CLI\)**  
Provides commands for a broad set of AWS products, and is supported on Windows, Mac, and Linux\. To get started, see [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\. For more information about the commands for Amazon EC2, see [ec2](https://docs.aws.amazon.com/cli/latest/reference/ec2/index.html) in the *AWS CLI Command Reference*\.

**AWS Tools for Windows PowerShell**  
Provides commands for a broad set of AWS products for those who script in the PowerShell environment\. To get started, see the [AWS Tools for Windows PowerShell User Guide](https://docs.aws.amazon.com/powershell/latest/userguide/)\. For more information about the cmdlets for Amazon EC2, see the [AWS Tools for PowerShell Cmdlet Reference](https://docs.aws.amazon.com/powershell/latest/reference/Index.html)\.

Amazon EC2 supports creating resources using AWS CloudFormation\. You create a template, in JSON or YAML, that describes your AWS resources, and AWS CloudFormation provisions and configures those resources for you\. You can reuse your CloudFormation templates to provision the same resources multiple times, whether in the same Region and account or in multiple Regions and accounts\. For more information about the resource types and properties for Amazon EC2, see [EC2 resource type reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_EC2.html) in the *AWS CloudFormation User Guide*\.

Amazon EC2 provides a Query API\. These requests are HTTP or HTTPS requests that use the HTTP verbs GET or POST and a Query parameter named `Action`\. For more information about the API actions for Amazon EC2, see [Actions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/query-apis.html) in the *Amazon EC2 API Reference*\.

If you prefer to build applications using language\-specific APIs instead of submitting a request over HTTP or HTTPS, AWS provides libraries, sample code, tutorials, and other resources for software developers\. These libraries provide basic functions that automate tasks such as cryptographically signing your requests, retrying requests, and handling error responses, making it is easier for you to get started\. For more information, see [Tools to Build on AWS](https://aws.amazon.com/tools/)\.

## Pricing for Amazon EC2<a name="ec2-pricing"></a>

When you sign up for AWS, you can get started with Amazon EC2 for free using the [AWS Free Tier](https://aws.amazon.com/free/)\.

Amazon EC2 provides the following purchasing options for instances:

**On\-Demand Instances**  
Pay for the instances that you use by the second, with a minimum of 60 seconds, with no long\-term commitments or upfront payments\.

**Savings Plans**  
You can reduce your Amazon EC2 costs by making a commitment to a consistent amount of usage, in USD per hour, for a term of 1 or 3 years\.

**Reserved Instances**  
You can reduce your Amazon EC2 costs by making a commitment to a specific instance configuration, including instance type and Region, for a term of 1 or 3 years\.

**Spot Instances**  
Request unused EC2 instances, which can reduce your Amazon EC2 costs significantly\.

For a complete list of charges and prices for Amazon EC2, see [Amazon EC2 pricing](https://aws.amazon.com/ec2/pricing)\.

When calculating the cost of a provisioned environment, remember to include incidental costs such as snapshot storage for EBS volumes\. To calculate the cost of a sample provisioned environment, see [Cloud Economics Center](https://aws.amazon.com/economics/)\.

To see your bill, go to the **Billing and Cost Management Dashboard** in the [AWS Billing and Cost Management console](https://console.aws.amazon.com/billing/)\. Your bill contains links to usage reports that provide details about your bill\. To learn more about AWS account billing, see [AWS Billing and Cost Management User Guide](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/)\.

If you have questions concerning AWS billing, accounts, and events, [contact AWS Support](https://aws.amazon.com/contact-us/)\.

For an overview of Trusted Advisor, a service that helps you optimize the costs, security, and performance of your AWS environment, see [AWS Trusted Advisor](https://aws.amazon.com/premiumsupport/trustedadvisor/)\.

## PCI DSS compliance<a name="pci-compliance"></a>

Amazon EC2 supports the processing, storage, and transmission of credit card data by a merchant or service provider, and has been validated as being compliant with Payment Card Industry \(PCI\) Data Security Standard \(DSS\)\. For more information about PCI DSS, including how to request a copy of the AWS PCI Compliance Package, see [PCI DSS Level 1](https://aws.amazon.com/compliance/pci-dss-level-1-faqs/)\. 