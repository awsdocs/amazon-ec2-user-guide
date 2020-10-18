# Migrating from EC2\-Classic to a VPC<a name="vpc-migrate"></a>

If you created your AWS account before December 4, 2013, you might have support for EC2\-Classic in some AWS Regions\. Some Amazon EC2 resources and features, such as enhanced networking and newer instance types, require a virtual private cloud \(VPC\)\. Some resources can be shared between EC2\-Classic and a VPC, while some can't\. For more information, see [Sharing and accessing resources between EC2\-Classic and a VPC](ec2-classic-platform.md#vpc-classic-shared-resources)\. We recommend that you migrate to a VPC to take advantage of VPC\-only features\.

To migrate from EC2\-Classic to a VPC, you must migrate or recreate your EC2\-Classic resources in a VPC\. You can migrate and recreate your resources in full, or you can perform an incremental migration over time using ClassicLink\.

**Topics**
+ [Options for getting a default VPC](#get-default-vpc)
+ [Migrate your resources to a VPC](#full-migrate)
+ [Use ClassicLink for an incremental migration](#classiclink-migrate)
+ [Example: Migrate a simple web application](#vpc-migrate-example)

## Options for getting a default VPC<a name="get-default-vpc"></a>

A *default VPC* is a VPC that is configured and ready for you to use, and is only available in Regions that are VPC\-only\. For Regions that support EC2\-Classic, you can create a nondefault VPC to set up your resources\. However, you might want to use a default VPC if you prefer not to set up a VPC yourself, or if you do not have specific requirements for your VPC configuration\. For more information about default VPCs, see [Default VPC and Default Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) in the *Amazon VPC User Guide*\.

The following are options for using a default VPC when you have an AWS account that supports EC2\-Classic\.

**Topics**
+ [Switch to a VPC\-only Region](#get-default-vpc-region)
+ [Create a new AWS account](#get-default-vpc-account)
+ [Convert your existing AWS account to VPC\-only](#convert-ec2-classic-account)

### Switch to a VPC\-only Region<a name="get-default-vpc-region"></a>

Use this option if you want to use your existing account to set up your resources in a default VPC and you do not need to use a specific Region\. To find a Region that has a default VPC, see [Detecting supported platforms](ec2-classic-platform.md#ec2-supported-platforms)\.

### Create a new AWS account<a name="get-default-vpc-account"></a>

New AWS accounts support VPC only\. Use this option if you want an account that has a default VPC in every Region\. 

### Convert your existing AWS account to VPC\-only<a name="convert-ec2-classic-account"></a>

Use this option if you want a default VPC in every Region in your existing account\. Before you can convert your account, you must delete all of your EC2\-Classic resources\. You can also migrate some resources to a VPC\. For more information, see [Migrate your resources to a VPC](#full-migrate)\.

**To convert your EC2\-Classic account**

1. Delete or migrate \(if applicable\) the resources that you have created for use in EC2\-Classic\. These include the following:
   + Amazon EC2 instances
   + EC2\-Classic security groups \(excluding the default security group, which you cannot delete yourself\)
   + EC2\-Classic Elastic IP addresses
   + Classic Load Balancers
   + Amazon RDS resources
   + Amazon ElastiCache resources
   + Amazon Redshift resources
   + AWS Elastic Beanstalk resources
   + AWS Data Pipeline resources
   + Amazon EMR resources
   + AWS OpsWorks resources

1. Go to the AWS Support Center at [console\.aws\.amazon\.com/support](https://console.aws.amazon.com/support)\.

1. Choose **Create case**\.

1. Choose **Account and billing support**\.

1. For **Type**, choose **Account**\. For **Category**, choose **Convert EC2 Classic to VPC**\.

1. Fill in the other details as required, and choose **Submit**\. We will review your request and contact you to guide you through the next steps\.

## Migrate your resources to a VPC<a name="full-migrate"></a>

You can migrate or move some of your resources to a VPC\. Some resources can only be migrated from EC2\-Classic to a VPC that's in the same Region and in the same AWS account\. If the resource cannot be migrated, you must create a new resource for use in your VPC\.

**Prerequisites**  
Before you begin, you must have a VPC\. If you don't have a default VPC, you can create a nondefault VPC using one of these methods:
+ In the Amazon VPC console, use the VPC wizard to create a new VPC\. For more information, see [Amazon VPC Console Wizard Configurations](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_wizard.html)\. Use this option if you want to set up a VPC quickly, using one of the available configuration options\.
+ In the Amazon VPC console, set up the components of a VPC according to your requirements\. For more information, see [VPCs and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)\. Use this option if you have specific requirements for your VPC, such as a particular number of subnets\.

**Topics**
+ [Security groups](#vpc-migrate-security-group)
+ [Elastic IP addresses](#vpc-migrate-eip)
+ [AMIs and instances](#vpc-migrate-ami-instance)
+ [Amazon RDS DB instances](#vpc-migrate-rds)

### Security groups<a name="vpc-migrate-security-group"></a>

If you want your instances in your VPC to have the same security group rules as your EC2\-Classic instances, you can use the Amazon EC2 console to copy your existing EC2\-Classic security group rules to a new VPC security group\. 

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

You can migrate an Elastic IP address that is allocated for use in EC2\-Classic for use with a VPC\. You cannot migrate an Elastic IP address to another Region or AWS account\. For more information, see [Migrating an Elastic IP Address from EC2\-Classic](ec2-classic-platform.md#migrating-eip)\.

**To identify an Elastic IP address that is allocated for use in EC2\-Classic**  
In the Amazon EC2 console, choose **Elastic IPs** in the navigation pane\. In the **Scope** column, the value is **standard**\.

Alternatively, use the following `describe-addresses` command\.

```
aws ec2 describe-addresses --filters Name=domain,Values=standard
```

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

To use your AMI to launch an instance in a new AWS account, you must first share the AMI with your new account\. For more information, see [Sharing an AMI with specific AWS accounts](sharingamis-explicit.md)\.

To use your AMI to launch an instance in a VPC in a different Region, you must first copy the AMI to that Region\. For more information, see [Copying an AMI](CopyingAMIs.md)\.

#### \(Optional\) Store your data on Amazon EBS volumes<a name="vpc-migrate-create-volumes"></a>

You can create an Amazon EBS volume and use it to back up and store the data on your instance—like you would use a physical hard drive\. Amazon EBS volumes can be attached and detached from any instance in the same Availability Zone\. You can detach a volume from your instance in EC2\-Classic, and attach it to a new instance that you launch into your VPC in the same Availability Zone\. 

For more information about Amazon EBS volumes, see the following topics:
+ [Amazon EBS volumes](ebs-volumes.md)
+ [Creating an Amazon EBS volume](ebs-creating-volume.md)
+ [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)

To back up the data on your Amazon EBS volume, you can take periodic snapshots of your volume\. For more information, see [Creating Amazon EBS snapshots](ebs-creating-snapshot.md)\. If you need to, you can create an Amazon EBS volume from your snapshot\. For more information, see [Creating a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)\.

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

For more information about the parameters that you can configure in each step of the wizard, see [Launching an instance using the Launch Instance Wizard](launching-instance.md)\.

### Amazon RDS DB instances<a name="vpc-migrate-rds"></a>

You can move your EC2\-Classic DB instance to a VPC in the same Region, in the same account\. For more information, see [Updating the VPC for a DB Instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.html#USER_VPC.VPC2VPC) in the *Amazon RDS User Guide*\.

## Use ClassicLink for an incremental migration<a name="classiclink-migrate"></a>

The ClassicLink feature makes it easier to manage an incremental migration to a VPC\. ClassicLink enables you to link an EC2\-Classic instance to a VPC in your account in the same Region, allowing your new VPC resources to communicate with the EC2\-Classic instance using private IPv4 addresses\. You can then migrate functionality one component at a time until your application is running fully in your VPC\.

Use this option if you cannot afford downtime during the migration, for example, if you have a multi\-tier application with processes that cannot be interrupted\.

For more information about ClassicLink, see [ClassicLink](vpc-classiclink.md)\.

**Topics**
+ [Step 1: Prepare your migration sequence](#classiclink-migrate-preparation)
+ [Step 2: Enable your VPC for ClassicLink](#classiclink-migrate-enable-vpc)
+ [Step 3: Link your EC2\-Classic instances to your VPC](#classiclink-migrate-attach-instance)
+ [Step 4: Complete the VPC migration](#classiclink-migrate-terminate-instances)

### Step 1: Prepare your migration sequence<a name="classiclink-migrate-preparation"></a>

To use ClassicLink effectively, you must first identify the components of your application that must be migrated to the VPC, and then confirm the order in which to migrate that functionality\. 

For example, you have an application that relies on a presentation web server, a backend database server, and authentication logic for transactions\. You may decide to start the migration process with the authentication logic, then the database server, and finally, the web server\.

Then, you can start migrating or recreating your resources\. For more information, see [Migrate your resources to a VPC](#full-migrate)\.

### Step 2: Enable your VPC for ClassicLink<a name="classiclink-migrate-enable-vpc"></a>

After you've configured your new VPC instances and made the functionality of your application available in the VPC, you can use ClassicLink to enable private IP communication between your new VPC instances and your EC2\-Classic instances\. First, you must enable your VPC for ClassicLink\.

**To enable a VPC for ClassicLink**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select a VPC\.

1. Choose **Actions**, **Enable ClassicLink**\.

1. When prompted for confirmation, choose **Enable ClassicLink**\.

### Step 3: Link your EC2\-Classic instances to your VPC<a name="classiclink-migrate-attach-instance"></a>

After you've enabled ClassicLink in your VPC, you can link your EC2\-Classic instances to the VPC\. The instance must be in the `running` state\.

**To link an instance to a VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select one or more running EC2\-Classic instances\.

1. Choose **Actions**, **ClassicLink**, **Link to VPC**\.

1. Choose a VPC\. The console displays only VPCs that are enabled for ClassicLink\.

1. Select one or more security groups to associate with your instances\. The console displays security groups only for VPCs enabled for ClassicLink\.

1. Choose **Link**\.

### Step 4: Complete the VPC migration<a name="classiclink-migrate-terminate-instances"></a>

Depending on the size of your application and the functionality that must be migrated, repeat the preceding steps until you've moved all of the components of your application from EC2\-Classic into your VPC\. 

After you've enabled internal communication between the EC2\-Classic and VPC instances, you must update your application to point to your migrated service in your VPC, instead of your service in the EC2\-Classic platform\. The exact steps for this depend on your application’s design\. Generally, this includes updating your destination IP addresses to point to the IP addresses of your VPC instances instead of your EC2\-Classic instances\.

After you've completed this step and you've tested that the application is functioning from your VPC, you can terminate your EC2\-Classic instances, and disable ClassicLink for your VPC\. You can also clean up any EC2\-Classic resources that you no longer need to avoid incurring charges for them\. For example, you can release Elastic IP addresses and delete the volumes that were associated with your EC2\-Classic instances\.

## Example: Migrate a simple web application<a name="vpc-migrate-example"></a>

In this example, you use AWS to host your gardening website\. To manage your website, you have three running instances in EC2\-Classic\. Instances A and B host your public\-facing web application, and you use Elastic Load Balancing to load balance the traffic between these instances\. You've assigned Elastic IP addresses to instances A and B so that you have static IP addresses for configuration and administration tasks on those instances\. Instance C holds your MySQL database for your website\. You've registered the domain name `www.garden.example.com`, and you've used Route 53 to create a hosted zone with an alias record set that's associated with the DNS name of your load balancer\.

![\[A web application in EC2-Classic\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vpc-migrate-example-classic.png)

The first part of migrating to a VPC is deciding what kind of VPC architecture suits your needs\. In this case, you've decided on the following: one public subnet for your web servers, and one private subnet for your database server\. As your website grows, you can add more web servers and database servers to your subnets\. By default, instances in the private subnet cannot access the internet; however, you can enable internet access through a Network Address Translation \(NAT\) device in the public subnet\. You might want to set up a NAT device to support periodic updates and patches from the internet for your database server\. You'll migrate your Elastic IP addresses to a VPC, and create a load balancer in your public subnet to load balance the traffic between your web servers\.

![\[A web application in a VPC\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vpc-migrate-example-vpc.png)

To migrate your web application to a VPC, you can follow these steps:
+ **Create a VPC**: In this case, you can use the VPC wizard in the Amazon VPC console to create your VPC and subnets\. The second wizard configuration creates a VPC with one private and one public subnet, and launches and configures a NAT device in your public subnet for you\. For more information, see [VPC with public and private subnets \(NAT\)](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenario2.html) in the *Amazon VPC User Guide*\.
+ **Configure your security groups**: In your EC2\-Classic environment, you have one security group for your web servers, and another security group for your database server\. You can use the Amazon EC2 console to copy the rules from each security group into new security groups for your VPC\. For more information, see [Security groups](#vpc-migrate-security-group)\. 
**Tip**  
Create the security groups that are referenced by other security groups first\.
+ **Create AMIs and launch new instances**: Create an AMI from one of your web servers, and a second AMI from your database server\. Then, launch replacement web servers into your public subnet, and launch your replacement database server into your private subnet\. For more information, see [Create an AMI](#vpc-migrate-create-ami)\.
+ **Configure your NAT device**: If you are using a NAT instance, you must create a security group for it that allows HTTP and HTTPS traffic from your private subnet\. For more information, see [NAT instances](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html)\. If you are using a NAT gateway, traffic from your private subnet is automatically allowed\.
+ **Configure your database**: When you created an AMI from your database server in EC2\-Classic, all of the configuration information that was stored in that instance was copied to the AMI\. You might have to connect to your new database server and update the configuration details\. For example, if you configured your database to grant full read, write, and modification permissions to your web servers in EC2\-Classic, you need to update the configuration files to grant the same permissions to your new VPC web servers instead\. 
+ **Configure your web servers**: Your web servers will have the same configuration settings as your instances in EC2\-Classic\. For example, if you configured your web servers to use the database in EC2\-Classic, update your web servers' configuration settings to point to your new database instance\.
**Note**  
By default, instances launched into a nondefault subnet are not assigned a public IP address, unless you specify otherwise at launch\. Your new database server might not have a public IP address\. In this case, you can update your web servers' configuration file to use your new database server's private DNS name\. Instances in the same VPC can communicate with each other via private IP address\.
+ **Migrate your Elastic IP addresses**: Disassociate your Elastic IP addresses from your web servers in EC2\-Classic, and then migrate them to a VPC\. After you've migrated them, you can associate them with your new web servers in your VPC\. For more information, see [Migrating an Elastic IP Address from EC2\-Classic](ec2-classic-platform.md#migrating-eip)\.
+ **Create a new load balancer**: To continue using Elastic Load Balancing to load balance the traffic to your instances, make sure you understand the various ways to configure your load balancer in VPC\. For more information, see the [Elastic Load Balancing User Guide](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/)\.
+ **Update your DNS records**: After you've set up your load balancer in your public subnet, verify that your `www.garden.example.com` domain points to your new load balancer\. To do this, update your DNS records and your alias record set in Route 53\. For more information about using Route 53, see [Getting Started with Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/getting-started.html)\.
+ **Shut down your EC2\-Classic resources**: After you've verified that your web application is working from within the VPC architecture, you can shut down your EC2\-Classic resources to stop incurring charges for them\.