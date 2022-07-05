# Migrate from EC2\-Classic to a VPC<a name="vpc-migrate"></a>


|  | 
| --- |
| We are retiring EC2\-Classic on August 15, 2022\. To avoid interruptions to your workloads, we recommend that you migrate from EC2\-Classic to a VPC prior to August 15, 2022\. For more information, see the blog post [EC2\-Classic Networking is Retiring \- Here's How to Prepare](http://aws.amazon.com/blogs/aws/ec2-classic-is-retiring-heres-how-to-prepare/)\. | 

To migrate from EC2\-Classic to a VPC, you must migrate or recreate your EC2\-Classic resources in a VPC\.

**Topics**
+ [Migrate your resources to a VPC](#full-migrate)
+ [Use the AWSSupport\-MigrateEC2ClassicToVPC runbook](#migrate-using-runbook)
+ [Example: Migrate a simple web application](#vpc-migrate-example)

## Migrate your resources to a VPC<a name="full-migrate"></a>

You can migrate or move some of your resources to a VPC\. Some resources can only be migrated from EC2\-Classic to a VPC that's in the same Region and in the same AWS account\. If the resource cannot be migrated, you must create a new resource for use in your VPC\.

**Prerequisites**  
Before you begin, you must have a VPC\. If you don't have a default VPC, you can create a nondefault VPC\. For more information, see [Create a VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#Create-VPC)\.

**Topics**
+ [Security groups](#vpc-migrate-security-group)
+ [Elastic IP addresses](#vpc-migrate-eip)
+ [AMIs and instances](#vpc-migrate-ami-instance)
+ [Amazon RDS DB instances](#vpc-migrate-rds)
+ [Classic Load Balancers](#vpc-migrate-elb)

### Security groups<a name="vpc-migrate-security-group"></a>

If you want your instances in your VPC to have the same security group rules as your EC2\-Classic instances, you can use the Amazon EC2 console to copy your existing EC2\-Classic security group rules \(including default ones\) to a new VPC security group\. Default security groups cannot be deleted and will be removed on your behalf when EC2\-Classic is retired\.

You can only copy security group rules to a new security group in the same AWS account in the same Region\. If you are using a different Region or a different AWS account, you must create a new security group and manually add the rules yourself\. For more information, see [Amazon EC2 security groups for Linux instances](ec2-security-groups.md)\.

**To copy your security group rules to a new security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group that's associated with your EC2\-Classic instance, then choose **Actions**, and select **Copy to new**\.
**Note**  
To identify an EC2\-Classic security group, check the **VPC ID** column\. For each EC2\-Classic security group, the value in the column is blank or a `-` symbol\.

1. In the **Create Security Group** dialog box, specify a name and description for your new security group\. Select your VPC from the **VPC** list\. 

1. The **Inbound** tab is populated with the rules from your EC2\-Classic security group\. You can modify the rules as required\. In the **Outbound** tab, a rule that allows all outbound traffic has automatically been created for you\. For more information about modifying security group rules, see [Amazon EC2 security groups for Linux instances](ec2-security-groups.md)\.
**Note**  
If you've defined a rule in your EC2\-Classic security group that references another security group, you cannot use the same rule in your VPC security group\. Modify the rule to reference a security group in the same VPC\.

1. Choose **Create**\.

### Elastic IP addresses<a name="vpc-migrate-eip"></a>

You can migrate an Elastic IP address that is allocated for use in EC2\-Classic for use with a VPC\. You cannot migrate an Elastic IP address to another Region or AWS account\. You cannot migrate an Elastic IP address that has been allocated to your account for less than 24 hours\.

When you migrate an Elastic IP address, it counts against your Elastic IP address limit for VPCs\. You cannot migrate an Elastic IP address if it results in your exceeding your limit\. To migrate an Elastic IP address, it must not be associated with an instance\. For more information about disassociating an Elastic IP address from an instance, see [Disassociate an Elastic IP address](elastic-ip-addresses-eip.md#using-instance-addressing-eips-associating-different)\.

After you've performed the command to move or restore your Elastic IP address, the process of migrating the Elastic IP address can take a few minutes\. Use the [describe\-moving\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-moving-addresses.html) command to check whether your Elastic IP address is still moving, or has completed moving\. After the moved is complete, you can view the allocation ID for the Elastic IP address on the **Elastic IPs** page\. If the Elastic IP address is in a moving state for longer than 5 minutes, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

**To identify an Elastic IP address that is allocated for use in EC2\-Classic**  
Open the Amazon EC2 console\. Choose **Elastic IPs** in the navigation pane and the select the check box for the Elastic IP address\. On the **Summary** page, check whether **Scope** is **EC2\-Classic** or **VPC**\.

**To move an Elastic IP address using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, and choose **Actions**, **Move to VPC scope**\.

1. In the confirmation dialog box, choose **Move Elastic IP**\.

### AMIs and instances<a name="vpc-migrate-ami-instance"></a>

An AMI is a template for launching your Amazon EC2 instance\. You can create your own AMI based on an existing EC2\-Classic instance, then use that AMI to launch instances into your VPC\.

**Topics**
+ [Identify EC2\-Classic instances](#vpc-migrate-id-instances)
+ [Create an AMI](#vpc-migrate-create-ami)
+ [\(Optional\) Share or copy your AMI](#vpc-migrate-share-ami)
+ [\(Optional\) Store your data on Amazon EBS volumes](#vpc-migrate-create-volumes)
+ [Launch an instance into your VPC](#vpc-migrate-instance)

#### Identify EC2\-Classic instances<a name="vpc-migrate-id-instances"></a>

If you have instances running in both EC2\-Classic and a VPC, you can identify your EC2\-Classic instances\.

**Amazon EC2 console**  
Choose **Instances** in the navigation pane\. In the **VPC ID** column, the value for each EC2\-Classic instance is blank or a `-` symbol\. If the **VPC ID** column is not present, choose the gear icon and make the column visible\.

**AWS CLI**  
Use the following [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) AWS CLI command\. The `--query` parameter displays only instances where the value for `VpcId` is `null`\.

```
aws ec2 describe-instances --query 'Reservations[*].Instances[?VpcId==`null`]'
```

#### Create an AMI<a name="vpc-migrate-create-ami"></a>

After you've identified your EC2\-Classic instance, you can create an AMI from it\.

**To create a Windows AMI**  
For more information, see [Creating a custom Windows AMI](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Creating_EBSbacked_WinAMI.html)\.

**To create a Linux AMI**  
The method that you use to create your Linux AMI depends on the root device type of your instance, and the operating system platform on which your instance runs\. To find out the root device type of your instance, go to the **Instances** page, select your instance, and look at the information in the **Root device type** field in the **Description** tab\. If the value is `ebs`, then your instance is EBS\-backed\. If the value is `instance-store`, then your instance is instance store\-backed\. You can also use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) AWS CLI command to find out the root device type\.

The following table provides options for you to create your Linux AMI based on the root device type of your instance, and the software platform\.

**Important**  
Some instance types support both PV and HVM virtualization, while others support only one or the other\. If you plan to use your AMI to launch a different instance type than your current instance type, verify that the instance type supports the type of virtualization that your AMI offers\. If your AMI supports PV virtualization, and you want to use an instance type that supports HVM virtualization, you might have to reinstall your software on a base HVM AMI\. For more information about PV and HVM virtualization, see [Linux AMI virtualization types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/virtualization_types.html)\.


| Instance root device type | Action | 
| --- | --- | 
| EBS | Create an EBS\-backed AMI from your instance\. For more information, see [Creating an Amazon EBS\-backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html)\. | 
| Instance store | Create an instance store\-backed AMI from your instance using the AMI tools\. For more information, see [Creating an instance store\-backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-instance-store.html)\. | 
| Instance store | Convert your instance store\-backed instance to an EBS\-backed instance\. For more information, see [Converting your instance store\-backed AMI to an Amazon EBS\-backed AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_ConvertingS3toEBS.html)\.  | 

#### \(Optional\) Share or copy your AMI<a name="vpc-migrate-share-ami"></a>

To use your AMI to launch an instance in a new AWS account, you must first share the AMI with your new account\. For more information, see [Share an AMI with specific AWS accounts](sharingamis-explicit.md)\.

To use your AMI to launch an instance in a VPC in a different Region, you must first copy the AMI to that Region\. For more information, see [Copy an AMI](CopyingAMIs.md)\.

#### \(Optional\) Store your data on Amazon EBS volumes<a name="vpc-migrate-create-volumes"></a>

You can create an Amazon EBS volume and use it to back up and store the data on your instance—like you would use a physical hard drive\. Amazon EBS volumes can be attached and detached from any instance in the same Availability Zone\. You can detach a volume from your instance in EC2\-Classic, and attach it to a new instance that you launch into your VPC in the same Availability Zone\. 

For more information about Amazon EBS volumes, see the following topics:
+ [Amazon EBS volumes](ebs-volumes.md)
+ [Create an Amazon EBS volume](ebs-creating-volume.md)
+ [Attach an Amazon EBS volume to an instance](ebs-attaching-volume.md)

To back up the data on your Amazon EBS volume, you can take periodic snapshots of your volume\. For more information, see [Create Amazon EBS snapshots](ebs-creating-snapshot.md)\. If you need to, you can create an Amazon EBS volume from your snapshot\. For more information, see [Create a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)\.

#### Launch an instance into your VPC<a name="vpc-migrate-instance"></a>

After you've created an AMI, you can use the Amazon EC2 launch wizard to launch an instance into your VPC\. The instance will have the same data and configurations as your existing EC2\-Classic instance\. 

**Note**  
You can use this opportunity to [upgrade to a current generation instance type](https://aws.amazon.com/ec2/previous-generation/#Upgrade_Paths)\. However, verify that the instance type supports the type of virtualization that your AMI offers \(PV or HVM\)\. For more information about PV and HVM virtualization, see [Linux AMI virtualization types](virtualization_types.md)\.

**To launch an instance into your VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the dashboard, choose **Launch instance**\. 

1. On the **Choose an Amazon Machine Image** page, select the **My AMIs** category, and select the AMI you created\. Alternatively, if you shared an AMI from another account, in the **Ownership** filter list, choose **Shared with me**\. Select the AMI that you shared from your EC2\-Classic account\. 

1. On the **Choose an Instance Type** page, select the type of instance, and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, select your VPC from the **Network** list\. Select the required subnet from the **Subnet** list\. Configure any other details that you require, then go through the next pages of the wizard until you reach the **Configure Security Group** page\.

1. Select **Select an existing group**, and select the security group that you created for your VPC\. Choose **Review and Launch**\.

1. Review your instance details, then choose **Launch** to specify a key pair and launch your instance\.

For more information about the parameters that you can configure in each step of the wizard, see [Launch an instance using the old launch instance wizard](launching-instance.md)\.

### Amazon RDS DB instances<a name="vpc-migrate-rds"></a>

You can move your EC2\-Classic DB instance to a VPC in the same Region, in the same account\. For more information, see [Updating the VPC for a DB Instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.html#USER_VPC.VPC2VPC) in the *Amazon RDS User Guide*\.

### Classic Load Balancers<a name="vpc-migrate-elb"></a>

You can migrate your Classic Load Balancer in EC2\-Classic to a Classic Load Balancer in a VPC, or you can migrate your Classic Load Balancer to an Application Load Balancer or a Network Load Balancer\. For more information, see [Migrate your Classic Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/migrate-classic-load-balancer.html) in the *Elastic Load Balancing User Guide*\.

## Use the AWSSupport\-MigrateEC2ClassicToVPC runbook<a name="migrate-using-runbook"></a>

The [AWSSupport\-MigrateEC2ClassicToVPC](https://docs.aws.amazon.com/systems-manager-automation-runbooks/latest/userguide/automation-awssupport-migrate-ec2-classic-to-vpc.html) runbook migrates an EC2\-Classic instance to a VPC\. For more information, see [How do I migrate an EC2\-Classic instance to a VPC?](http://aws.amazon.com/premiumsupport/knowledge-center/ssm-migrate-ec2classic-vpc/)

## Example: Migrate a simple web application<a name="vpc-migrate-example"></a>

In this example, you use AWS to host your gardening website\. To manage your website, you have three running instances in EC2\-Classic\. Instances A and B host your public\-facing web application, and you use Elastic Load Balancing to load balance the traffic between these instances\. You've assigned Elastic IP addresses to instances A and B so that you have static IP addresses for configuration and administration tasks on those instances\. Instance C holds your MySQL database for your website\. You've registered the domain name `www.garden.example.com`, and you've used Route 53 to create a hosted zone with an alias record set that's associated with the DNS name of your load balancer\.

![\[A web application in EC2-Classic\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vpc-migrate-example-classic.png)

The first part of migrating to a VPC is deciding what kind of VPC architecture suits your needs\. In this case, you've decided on the following: one public subnet for your web servers, and one private subnet for your database server\. As your website grows, you can add more web servers and database servers to your subnets\. By default, instances in the private subnet cannot access the internet; however, you can enable internet access through a Network Address Translation \(NAT\) device in the public subnet\. You might want to set up a NAT device to support periodic updates and patches from the internet for your database server\. You'll migrate your Elastic IP addresses to a VPC, and create a load balancer in your public subnet to load balance the traffic between your web servers\.

![\[A web application in a VPC\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vpc-migrate-example-vpc.png)

To migrate your web application to a VPC, you can follow these steps:
+ **Create a VPC**: For more information, see [Create a VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#Create-VPC) in the *Amazon VPC User Guide*\. For information about VPC architecture scenarios, see [Scenarios](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-scenarios-intro.html) in the *Amazon VPC User Guide*\. 
+ **Configure your security groups**: In your EC2\-Classic environment, you have one security group for your web servers, and another security group for your database server\. You can use the Amazon EC2 console to copy the rules from each security group into new security groups for your VPC\. For more information, see [Security groups](#vpc-migrate-security-group)\. 
**Tip**  
Create the security groups that are referenced by other security groups first\.
+ **Create AMIs and launch new instances**: Create an AMI from one of your web servers, and a second AMI from your database server\. Then, launch replacement web servers into your public subnet, and launch your replacement database server into your private subnet\. For more information, see [Create an AMI](#vpc-migrate-create-ami)\.
+ **Configure your NAT device**: If you are using a NAT instance, you must create a security group for it that allows HTTP and HTTPS traffic from your private subnet\. For more information, see [NAT instances](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html)\. If you are using a NAT gateway, traffic from your private subnet is automatically allowed\.
+ **Configure your database**: When you created an AMI from your database server in EC2\-Classic, all of the configuration information that was stored in that instance was copied to the AMI\. You might have to connect to your new database server and update the configuration details\. For example, if you configured your database to grant full read, write, and modification permissions to your web servers in EC2\-Classic, you need to update the configuration files to grant the same permissions to your new VPC web servers instead\. 
+ **Configure your web servers**: Your web servers will have the same configuration settings as your instances in EC2\-Classic\. For example, if you configured your web servers to use the database in EC2\-Classic, update your web servers' configuration settings to point to your new database instance\.
**Note**  
By default, instances launched into a nondefault subnet are not assigned a public IP address, unless you specify otherwise at launch\. Your new database server might not have a public IP address\. In this case, you can update your web servers' configuration file to use your new database server's private DNS name\. Instances in the same VPC can communicate with each other via private IP address\.
+ **Migrate your Elastic IP addresses**: Disassociate your Elastic IP addresses from your web servers in EC2\-Classic, and then migrate them to a VPC\. After you've migrated them, you can associate them with your new web servers in your VPC\. For more information, see [Elastic IP addresses](#vpc-migrate-eip)\.
+ **Create a new load balancer**: To continue using Elastic Load Balancing to load balance the traffic to your instances, make sure you understand the various ways to configure your load balancer in VPC\. For more information, see the [Elastic Load Balancing User Guide](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/)\.
+ **Update your DNS records**: After you've set up your load balancer in your public subnet, verify that your `www.garden.example.com` domain points to your new load balancer\. To do this, update your DNS records and your alias record set in Route 53\. For more information about using Route 53, see [Getting Started with Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/getting-started.html)\.
+ **Shut down your EC2\-Classic resources**: After you've verified that your web application is working from within the VPC architecture, you can shut down your EC2\-Classic resources to stop incurring charges for them\.