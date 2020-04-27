# Migrating from a Linux Instance in EC2\-Classic to a Linux Instance in a VPC<a name="vpc-migrate"></a>

If you created your AWS account before 2013\-12\-04, you might have support for EC2\-Classic in some regions\. Some Amazon EC2 resources and features, such as enhanced networking and newer instance types, require a virtual private cloud \(VPC\)\. Some resources can be shared between EC2\-Classic and a VPC, while some can't\. For more information, see [Sharing and Accessing Resources Between EC2\-Classic and a VPC](ec2-classic-platform.md#vpc-classic-shared-resources)\.

If your account supports EC2\-Classic, you might have set up resources for use in EC2\-Classic\. If you want to migrate from EC2\-Classic to a VPC, you must recreate those resources in your VPC\.

There are two ways of migrating to a VPC\. You can do a full migration, or you can do an incremental migration over time\. The method you choose depends on the size and complexity of your application in EC2\-Classic\. For example, if your application consists of one or two instances running a static website, and you can afford a short period of downtime, you can do a full migration\. If you have a multi\-tier application with processes that cannot be interrupted, you can do an incremental migration using ClassicLink\. This allows you to transfer functionality one component at a time until your application is running fully in your VPC\.

If you need to migrate a Windows instance, see [Migrating a Windows Instance from EC2\-Classic to a VPC](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/vpc-migrate.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Topics**
+ [Full Migration to a VPC](#full-migrate)
+ [Incremental Migration to a VPC Using ClassicLink](#classiclink-migrate)

## Full Migration to a VPC<a name="full-migrate"></a>

Complete the following tasks to fully migrate your application from EC2\-Classic to a VPC\.

**Topics**
+ [Step 1: Create a VPC](#vpc-migrate-create-vpc)
+ [Step 2: Configure Your Security Group](#vpc-migrate-security-group)
+ [Step 3: Create an AMI from Your EC2\-Classic Instance](#vpc-migrate-create-ami)
+ [Step 4: Launch an Instance Into Your VPC](#vpc-migrate-instance)
+ [Example: Migrating a Simple Web Application](#vpc-migrate-example)

### Step 1: Create a VPC<a name="vpc-migrate-create-vpc"></a>

To start using a VPC, ensure that you have one in your account\. You can create one using one of these methods: 
+ Your AWS account comes with a default VPC in each region, which is ready for you to use\. Instances that you launch are by default launched into this VPC, unless you specify otherwise\. For more information about your default VPC, see [Default VPC and Default Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html)\. Use this option if you'd prefer not to set up a VPC yourself, or if you do not need specific requirements for your VPC configuration\.
+ In your existing AWS account, open the Amazon VPC console and use the VPC wizard to create a new VPC\. For more information, see [Amazon VPC Console Wizard Configurations](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_wizard.html)\. Use this option if you want to set up a VPC quickly in your existing EC2\-Classic account, using one of the available configuration sets in the wizard\. You'll specify this VPC each time you launch an instance\. 
+ In your existing AWS account, open the Amazon VPC console and set up the components of a VPC according to your requirements\. For more information, see [VPCs and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)\. Use this option if you have specific requirements for your VPC, such as a particular number of subnets\. You'll specify this VPC each time you launch an instance\. 

### Step 2: Configure Your Security Group<a name="vpc-migrate-security-group"></a>

You cannot use the same security groups between EC2\-Classic and a VPC\. However, if you want your instances in your VPC to have the same security group rules as your EC2\-Classic instances, you can use the Amazon EC2 console to copy your existing EC2\-Classic security group rules to a new VPC security group\. 

**Important**  
You can only copy security group rules to a new security group in the same AWS account in the same Region\. If you've created a new AWS account, you cannot use this method to copy your existing security group rules to your new account\. You'll have to create a new security group, and add the rules yourself\. For more information about creating a new security group, see [Amazon EC2 Security Groups for Linux Instances](ec2-security-groups.md)\.

**To copy your security group rules to a new security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group that's associated with your EC2\-Classic instance, then choose **Actions** and select **Copy to new**\.

1. In the **Create Security Group** dialog box, specify a name and description for your new security group\. Select your VPC from the **VPC** list\. 

1. The **Inbound** tab is populated with the rules from your EC2\-Classic security group\. You can modify the rules as required\. In the **Outbound** tab, a rule that allows all outbound traffic has automatically been created for you\. For more information about modifying security group rules, see [Amazon EC2 Security Groups for Linux Instances](ec2-security-groups.md)\.
**Note**  
If you've defined a rule in your EC2\-Classic security group that references another security group, you will not be able to use the same rule in your VPC security group\. Modify the rule to reference a security group in the same VPC\.

1. Choose **Create**\.

### Step 3: Create an AMI from Your EC2\-Classic Instance<a name="vpc-migrate-create-ami"></a>

An AMI is a template for launching your instance\. You can create your own AMI based on an existing EC2\-Classic instance, then use that AMI to launch instances into your VPC\.

The method you use to create your AMI depends on the root device type of your instance, and the operating system platform on which your instance runs\. To find out the root device type of your instance, go to the **Instances** page, select your instance, and look at the information in the **Root device type** field in the **Description** tab\. If the value is `ebs`, then your instance is EBS\-backed\. If the value is `instance-store`, then your instance is instance store\-backed\. You can also use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) AWS CLI command to find out the root device type\.

The following table provides options for you to create your AMI based on the root device type of your instance, and the software platform\.

**Important**  
Some instance types support both PV and HVM virtualization, while others support only one or the other\. If you plan to use your AMI to launch a different instance type than your current instance type, check that the instance type supports the type of virtualization that your AMI offers\. If your AMI supports PV virtualization, and you want to use an instance type that supports HVM virtualization, you may have to reinstall your software on a base HVM AMI\. For more information about PV and HVM virtualization, see [Linux AMI virtualization types](virtualization_types.md)\.


| Instance Root Device Type | Action | 
| --- | --- | 
| EBS | Create an EBS\-backed AMI from your instance\. For more information, see [Creating an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)\. | 
| Instance store | Create an instance store\-backed AMI from your instance using the AMI tools\. For more information, see [Creating an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\. | 
| Instance store | Convert your instance store\-backed instance to an EBS\-backed instances\. For more information, see [Converting your instance store\-backed AMI to an Amazon EBS\-backed AMI](Using_ConvertingS3toEBS.md)\.  | 

#### \(Optional\) Store Your Data on Amazon EBS Volumes<a name="vpc-migrate-create-volumes"></a>

You can create an Amazon EBS volume and use it to back up and store the data on your instance—like you would use a physical hard drive\. Amazon EBS volumes can be attached and detached from any instance in the same Availability Zone\. You can detach a volume from your instance in EC2\-Classic, and attach it to a new instance that you launch into your VPC in the same Availability Zone\. 

For more information about Amazon EBS volumes, see the following topics:
+ [Amazon EBS volumes](ebs-volumes.md)
+ [Creating an Amazon EBS volume](ebs-creating-volume.md)
+ [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)

To back up the data on your Amazon EBS volume, you can take periodic snapshots of your volume\. If you need to, you can restore an Amazon EBS volume from your snapshot\. For more information about Amazon EBS snapshots, see the following topics:
+ [Amazon EBS Snapshots](EBSSnapshots.md)
+ [Creating Amazon EBS Snapshots](ebs-creating-snapshot.md)
+ [Restoring an Amazon EBS volume from a snapshot](ebs-restoring-volume.md)

### Step 4: Launch an Instance Into Your VPC<a name="vpc-migrate-instance"></a>

After you've created an AMI, you can launch an instance into your VPC\. The instance will have the same data and configurations as your existing EC2\-Classic instance\. 

You can either launch your instance into a VPC that you've created in your existing account, or into a new, VPC\-only AWS account\.

#### Using Your Existing EC2\-Classic Account<a name="use-classic-account"></a>

You can use the Amazon EC2 launch wizard to launch an instance into your VPC\.

**To launch an instance into your VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the dashboard, choose **Launch Instance**\. 

1. On the **Choose an Amazon Machine Image** page, select the **My AMIs** category, and select the AMI you created\. 

1. On the **Choose an Instance Type** page, select the type of instance, and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, select your VPC from the **Network** list\. Select the required subnet from the **Subnet** list\. Configure any other details you require, then go through the next pages of the wizard until you reach the **Configure Security Group** page\.

1. Select **Select an existing group**, and select the security group you created earlier\. Choose **Review and Launch**\.

1. Review your instance details, then choose **Launch** to specify a key pair and launch your instance\.

For more information about the parameters you can configure in each step of the wizard, see [Launching an instance using the Launch Instance Wizard](launching-instance.md)\.

#### Using Your New, VPC\-Only Account<a name="use-vpc-only-account"></a>

To launch an instance in your new AWS account, you'll first have to share the AMI you created with your new account\. You can then use the Amazon EC2 launch wizard to launch an instance into your default VPC\. 

**To share an AMI with your new AWS account**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Switch to the account in which you created your AMI\.

1. In the navigation pane, choose **AMIs**\. 

1. In the **Filter** list, ensure **Owned by me** is selected, then select your AMI\. 

1. In the **Permissions** tab, choose **Edit**\. Enter the account number of your new AWS account, choose **Add Permission**, and then choose **Save**\.

**To launch an instance into your default VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Switch to your new AWS account\.

1. In the navigation pane, choose **AMIs**\.

1. In the **Filter** list, select **Private images**\. Select the AMI that you shared from your EC2\-Classic account, then choose **Launch**\. 

1. On the **Choose an Instance Type** page, select the type of instance, and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, your default VPC should be selected in the **Network** list\. Configure any other details you require, then go through the next pages of the wizard until you reach the **Configure Security Group** page\.

1. Select **Select an existing group**, and select the security group you created earlier\. Choose **Review and Launch**\.

1. Review your instance details, then choose **Launch** to specify a key pair and launch your instance\.

For more information about the parameters you can configure in each step of the wizard, see [Launching an instance using the Launch Instance Wizard](launching-instance.md)\.

### Example: Migrating a Simple Web Application<a name="vpc-migrate-example"></a>

In this example, you use AWS to host your gardening website\. To manage your website, you have three running instances in EC2\-Classic\. Instances A and B host your public\-facing web application, and you use Elastic Load Balancing to load balance the traffic between these instances\. You've assigned Elastic IP addresses to instances A and B so that you have static IP addresses for configuration and administration tasks on those instances\. Instance C holds your MySQL database for your website\. You've registered the domain name `www.garden.example.com`, and you've used Route 53 to create a hosted zone with an alias record set that's associated with the DNS name of your load balancer\.

![\[A web application in EC2-Classic\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vpc-migrate-example-classic.png)

The first part of migrating to a VPC is deciding what kind of VPC architecture will suit your needs\. In this case, you've decided on the following: one public subnet for your web servers, and one private subnet for your database server\. As your website grows, you can add more web servers and database servers to your subnets\. By default, instances in the private subnet cannot access the Internet; however, you can enable Internet access through a Network Address Translation \(NAT\) device in the public subnet\. You may want to set up a NAT device to support periodic updates and patches from the Internet for your database server\. You'll migrate your Elastic IP addresses to a VPC, and create a load balancer in your public subnet to load balance the traffic between your web servers\.

![\[A web application in a VPC\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vpc-migrate-example-vpc.png)

To migrate your web application to a VPC, you can follow these steps:
+ **Create a VPC**: In this case, you can use the VPC wizard in the Amazon VPC console to create your VPC and subnets\. The second wizard configuration creates a VPC with one private and one public subnet, and launches and configures a NAT device in your public subnet for you\. For more information, see [VPC with Public and Private Subnets \(NAT\)](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenario2.html) in the *Amazon VPC User Guide*\.
+ **Create AMIs from your instances**: Create an AMI from one of your web servers, and a second AMI from your database server\. For more information, see [Step 3: Create an AMI from Your EC2\-Classic Instance](#vpc-migrate-create-ami)\.
+ **Configure your security groups**: In your EC2\-Classic environment, you have one security group for your web servers, and another security group for your database server\. You can use the Amazon EC2 console to copy the rules from each security group into new security groups for your VPC\. For more information, see [Step 2: Configure Your Security Group](#vpc-migrate-security-group)\. 
**Tip**  
Create the security groups that are referenced by other security groups first\.
+ **Launch an instance into your new VPC**: Launch replacement web servers into your public subnet, and launch your replacement database server into your private subnet\. For more information, see [Step 4: Launch an Instance Into Your VPC](#vpc-migrate-instance)\.
+ **Configure your NAT device**: If you are using a NAT instance, you must create security group for it that allows HTTP and HTTPS traffic from your private subnet\. For more information, see [NAT Instances](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html)\. If you are using a NAT gateway, traffic from your private subnet is automatically allowed\.
+ **Configure your database**: When you created an AMI from your database server in EC2\-Classic, all the configuration information that was stored in that instance was copied to the AMI\. You may have to connect to your new database server and update the configuration details; for example, if you configured your database to grant full read, write, and modification permissions to your web servers in EC2\-Classic, you'll have to update the configuration files to grant the same permissions to your new VPC web servers instead\. 
+ **Configure your web servers**: Your web servers will have the same configuration settings as your instances in EC2\-Classic\. For example, if you configured your web servers to use the database in EC2\-Classic, update your web servers' configuration settings to point to your new database instance\.
**Note**  
By default, instances launched into a nondefault subnet are not assigned a public IP address, unless you specify otherwise at launch\. Your new database server may not have a public IP address\. In this case, you can update your web servers' configuration file to use your new database server's private DNS name\. Instances in the same VPC can communicate with each other via private IP address\.
+ **Migrate your Elastic IP addresses**: Disassociate your Elastic IP addresses from your web servers in EC2\-Classic, and then migrate them to a VPC\. After you've migrated them, you can associate them with your new web servers in your VPC\. For more information, see [Migrating an Elastic IP Address from EC2\-Classic](ec2-classic-platform.md#migrating-eip)\.
+ **Create a new load balancer**: To continue using Elastic Load Balancing to load balance the traffic to your instances, make sure you understand the various ways you can configure your load balancer in VPC\. For more information, see [Elastic Load Balancing in Amazon VPC](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/UserScenariosForVPC.html)\.
+ **Update your DNS records**: After you've set up your load balancer in your public subnet, ensure that your `www.garden.example.com` domain points to your new load balancer\. To do this, you'll need to update your DNS records and update your alias record set in Route 53\. For more information about using Route 53, see [Getting Started with Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/getting-started.html)\.
+ **Shut down your EC2\-Classic resources**: After you've verified that your web application is working from within the VPC architecture, you can shut down your EC2\-Classic resources to stop incurring charges for them\. Terminate your EC2\-Classic instances, and release your EC2\-Classic Elastic IP addresses\.

## Incremental Migration to a VPC Using ClassicLink<a name="classiclink-migrate"></a>

The ClassicLink feature makes it easier to manage an incremental migration to a VPC\. ClassicLink allows you to link an EC2\-Classic instance to a VPC in your account in the same region, allowing your new VPC resources to communicate with the EC2\-Classic instance using private IPv4 addresses\. You can then migrate functionality to the VPC one step at a time\. This topic provides some basic steps for managing an incremental migration from EC2\-Classic to a VPC\. 

For more information about ClassicLink, see [ClassicLink](vpc-classiclink.md)\.

**Topics**
+ [Step 1: Prepare Your Migration Sequence](#classiclink-migrate-preparation)
+ [Step 2: Create a VPC](#classiclink-migrate-create-vpc)
+ [Step 3: Enable Your VPC for ClassicLink](#classiclink-migrate-enable-vpc)
+ [Step 4: Create an AMI from Your EC2\-Classic Instance](#classiclink-migrate-ami)
+ [Step 5: Launch an Instance Into Your VPC](#classiclink-migrate-launch-instance)
+ [Step 6: Link Your EC2\-Classic Instances to Your VPC](#classiclink-migrate-attach-instance)
+ [Step 7: Complete the VPC Migration](#classiclink-migrate-terminate-instances)

### Step 1: Prepare Your Migration Sequence<a name="classiclink-migrate-preparation"></a>

To use ClassicLink effectively, you must first identify the components of your application that must be migrated to the VPC, and then confirm the order in which to migrate that functionality\. 

For example, you have an application that relies on a presentation web server, a backend database server, and authentication logic for transactions\. You may decide to start the migration process with the authentication logic, then the database server, and finally, the web server\.

### Step 2: Create a VPC<a name="classiclink-migrate-create-vpc"></a>

To start using a VPC, ensure that you have one in your account\. You can create one using one of these methods: 
+ In your existing AWS account, open the Amazon VPC console and use the VPC wizard to create a new VPC\. For more information, see [Amazon VPC Console Wizard Configurations](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_wizard.html)\. Use this option if you want to set up a VPC quickly in your existing EC2\-Classic account, using one of the available configuration sets in the wizard\. You'll specify this VPC each time you launch an instance\. 
+ In your existing AWS account, open the Amazon VPC console and set up the components of a VPC according to your requirements\. For more information, see [VPCs and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)\. Use this option if you have specific requirements for your VPC, such as a particular number of subnets\. You'll specify this VPC each time you launch an instance\. 

### Step 3: Enable Your VPC for ClassicLink<a name="classiclink-migrate-enable-vpc"></a>

After you've created a VPC, you can enable it for ClassicLink\. For more information about ClassicLink, see [ClassicLink](vpc-classiclink.md)\. 

**To enable a VPC for ClassicLink**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select your VPC, and then select **Enable ClassicLink** from the **Actions** list\.

1. In the confirmation dialog box, choose **Yes, Enable**\.

### Step 4: Create an AMI from Your EC2\-Classic Instance<a name="classiclink-migrate-ami"></a>

An AMI is a template for launching your instance\. You can create your own AMI based on an existing EC2\-Classic instance, then use that AMI to launch instances into your VPC\.

The method you use to create your AMI depends on the root device type of your instance, and the operating system platform on which your instance runs\. To find out the root device type of your instance, go to the **Instances** page, select your instance, and look at the information in the **Root device type** field in the **Description** tab\. If the value is `ebs`, then your instance is EBS\-backed\. If the value is `instance-store`, then your instance is instance store\-backed\. You can also use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) AWS CLI command to find out the root device type\.

The following table provides options for you to create your AMI based on the root device type of your instance, and the software platform\.

**Important**  
Some instance types support both PV and HVM virtualization, while others support only one or the other\. If you plan to use your AMI to launch a different instance type than your current instance type, check that the instance type supports the type of virtualization that your AMI offers\. If your AMI supports PV virtualization, and you want to use an instance type that supports HVM virtualization, you may have to reinstall your software on a base HVM AMI\. For more information about PV and HVM virtualization, see [Linux AMI virtualization types](virtualization_types.md)\.


| Instance Root Device Type | Action | 
| --- | --- | 
| EBS | Create an EBS\-backed AMI from your instance\. For more information, see [Creating an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)\. | 
| Instance store | Create an instance store\-backed AMI from your instance using the AMI tools\. For more information, see [Creating an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\. | 
| Instance store | Convert your instance store\-backed instance to an EBS\-backed instance\. For more information, see [Converting your instance store\-backed AMI to an Amazon EBS\-backed AMI](Using_ConvertingS3toEBS.md)\. | 

#### \(Optional\) Store Your Data on Amazon EBS Volumes<a name="classiclink-migrate-create-volumes"></a>

You can create an Amazon EBS volume and use it to back up and store the data on your instance—like you would use a physical hard drive\. Amazon EBS volumes can be attached and detached from any instance in the same Availability Zone\. You can detach a volume from your instance in EC2\-Classic, and attach it to a new instance that you launch into your VPC in the same Availability Zone\. 

For more information about Amazon EBS volumes, see the following topics:
+ [Amazon EBS volumes](ebs-volumes.md)
+ [Creating an Amazon EBS volume](ebs-creating-volume.md)
+ [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)

To back up the data on your Amazon EBS volume, you can take periodic snapshots of your volume\. If you need to, you can restore an Amazon EBS volume from your snapshot\. For more information about Amazon EBS snapshots, see the following topics:
+ [Amazon EBS Snapshots](EBSSnapshots.md)
+ [Creating Amazon EBS Snapshots](ebs-creating-snapshot.md)
+ [Restoring an Amazon EBS volume from a snapshot](ebs-restoring-volume.md)

### Step 5: Launch an Instance Into Your VPC<a name="classiclink-migrate-launch-instance"></a>

The next step in the migration process is to launch instances into your VPC so that you can start transferring functionality to them\. You can use the AMIs that you created in the previous step to launch instances into your VPC\. The instances will have the same data and configurations as your existing EC2\-Classic instances\. 

**To launch an instance into your VPC using your custom AMI**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the dashboard, choose **Launch Instance**\. 

1. On the **Choose an Amazon Machine Image** page, select the **My AMIs** category, and select the AMI you created\. 

1. On the **Choose an Instance Type** page, select the type of instance, and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, select your VPC from the **Network** list\. Select the required subnet from the **Subnet** list\. Configure any other details you require, then go through the next pages of the wizard until you reach the **Configure Security Group** page\.

1. Select **Select an existing group**, and select the security group you created earlier\. Choose **Review and Launch**\.

1. Review your instance details, then choose **Launch** to specify a key pair and launch your instance\.

For more information about the parameters you can configure in each step of the wizard, see [Launching an instance using the Launch Instance Wizard](launching-instance.md)\.

After you've launched your instance and it's in the `running` state, you can connect to it and configure it as required\.

### Step 6: Link Your EC2\-Classic Instances to Your VPC<a name="classiclink-migrate-attach-instance"></a>

After you've configured your instances and made the functionality of your application available in the VPC, you can use ClassicLink to enable private IP communication between your new VPC instances and your EC2\-Classic instances\.

**To link an instance to a VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\. 

1. Select your EC2\-Classic instance, then choose **Actions**, **ClassicLink**, and **Link to VPC**\.
**Note**  
Ensure that your instance is in the `running` state\.

1. In the dialog box, select your ClassicLink\-enabled VPC \(only VPCs that are enabled for ClassicLink are displayed\)\.

1. Select one or more of the VPC security groups to associate with your instance\. When you are done, choose **Link to VPC**\.

### Step 7: Complete the VPC Migration<a name="classiclink-migrate-terminate-instances"></a>

Depending on the size of your application and the functionality that must be migrated, repeat steps 4 to 6 until you've moved all the components of your application from EC2\-Classic into your VPC\. 

After you've enabled internal communication between the EC2\-Classic and VPC instances, you must update your application to point to your migrated service in your VPC, instead of your service in the EC2\-Classic platform\. The exact steps for this depend on your application’s design\. Generally, this includes updating your destination IP addresses to point to the IP addresses of your VPC instances instead of your EC2\-Classic instances\. You can migrate your Elastic IP addresses that you are currently using in the EC2\-Classic platform to a VPC\. For more information, see [Migrating an Elastic IP Address from EC2\-Classic](ec2-classic-platform.md#migrating-eip)\.

After you've completed this step and you've tested that the application is functioning from your VPC, you can terminate your EC2\-Classic instances, and disable ClassicLink for your VPC\. You can also clean up any EC2\-Classic resources that you may no longer need to avoid incurring charges for them; for example, you can release Elastic IP addresses, and delete the volumes that were associated with your EC2\-Classic instances\.