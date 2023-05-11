# Launch an instance using the new launch instance wizard<a name="ec2-launch-instance-wizard"></a>

You can launch an instance using the new launch instance wizard\. The launch instance wizard specifies the launch parameters that are required for launching an instance\. Where the launch instance wizard provides a default value, you can accept the default or specify your own value\. If you accept the default values, then it's possible to launch an instance by selecting only a key pair\.

Before you launch your instance, be sure that you are set up\. For more information, see [Set up to use Amazon EC2](get-set-up-for-amazon-ec2.md)\.

**Important**  
When you launch an instance that's not within the [AWS Free Tier](https://aws.amazon.com/free/), you are charged for the time that the instance is running, even if it remains idle\.

**Topics**
+ [About the new launch instance wizard](#about-liw)
+ [Quickly launch an instance](#liw-quickly-launch-instance)
+ [Launch an instance using defined parameters](#liw-launch-instance-with-defined-parameters)
+ [Launch an instance using the old launch instance wizard](launching-instance.md)

## About the new launch instance wizard<a name="about-liw"></a>

Welcome to the new and improved launch experience—a quicker and easier way to launch an instance\.

We're in the process of rolling out the new launch instance wizard\. If it's not available in your currently selected Region, you can select a different Region to check if it's available there\.

**Current improvements**
+ **Single page layout with summary side panel**

  Quickly get up and running with our new one\-page design\. See all of your settings in one location\. No need to navigate back and forth between steps to ensure your configuration is correct\. Use the **Summary** panel for an overview and to easily navigate the page\.
+ **Improved AMI selector**

  New users – Use the **Quick Start** Amazon Machine Image \(AMI\) selector to select an operating system so that you can quickly launch an instance\.

  Experienced users – The AMI selector displays your recently used AMIs and the AMIs that you own, making it easier to select the AMIs that you care about\. You can still browse the full catalog to find new AMIs\.

**Work in progress**

We’re working continuously to improve the experience\. Here’s what we’re currently working on:
+ **Defaults and dependency assistance**
  + **Default values** will be provided for all fields\.
  + **Additional logic** will be added to help you set up your instance configuration correctly \(for example, we’ll disable parameters that are not available with your current settings\)\.
+ **Further simplified designs**
  + **Simplified views and summaries** and **a more responsive design** will be added to make the one\-page experience more scalable\.
  + **Simplified networking** features will be added to help you to configure your firewall rules quickly and easily \(for example, we’ll select common preset rules\)\.

There will be many more improvements to the launch experience in the months ahead\.

**Please send feedback**  
We’d appreciate your feedback on the new launch instance wizard\. We’ll use your feedback to continue improving the experience over the next few months\. You can send us feedback directly from the EC2 console, or use the **Provide feedback** link at the bottom of this page\.

## Quickly launch an instance<a name="liw-quickly-launch-instance"></a>

To set up an instance quickly for testing purposes, follow these steps\. You'll select the operating system and your key pair, and accept the default values\. For information about all of the parameters in the launch instance wizard, see [Launch an instance using defined parameters](#liw-launch-instance-with-defined-parameters)\.

**To quickly launch an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation bar at the top of the screen, the current AWS Region is displayed \(for example, US East \(Ohio\)\)\. Select a Region in which to launch the instance\. This choice is important because some Amazon EC2 resources can be shared between Regions, while others can't\. For more information, see [Resource locations](resources.md)\.

1. From the Amazon EC2 console dashboard, choose **Launch instance**\. 

1. \(Optional\) Under **Name and tags**, for **Name**, enter a descriptive name for your instance\.

1. Under **Application and OS Images \(Amazon Machine Image\)**, choose **Quick Start**, and then choose the operating system \(OS\) for your instance\.

1. Under **Key pair \(login\)**, for **Key pair name**, choose an existing key pair or create a new one\.

1. In the **Summary** panel, choose **Launch instance**\.

## Launch an instance using defined parameters<a name="liw-launch-instance-with-defined-parameters"></a>

Except for the key pair, the launch instance wizard provides default values for all of the parameters\. You can accept any or all of the defaults, or configure an instance by specifying your own values for each parameter\. The parameters are grouped in the launch instance wizard\. The following instructions take you through each parameter group\.

**Topics**
+ [Initiate instance launch](#liw-initiate-instance-launch)
+ [Name and tags](#liw-name-and-tags)
+ [Application and OS Images \(Amazon Machine Image\)](#liw-ami)
+ [Instance type](#liw-instance-type)
+ [Key pair \(login\)](#liw-key-pair)
+ [Network settings](#liw-network-settings)
+ [Configure storage](#liw-storage)
+ [Advanced details](#liw-advanced-details)
+ [Summary](#liw-summary)

### Initiate instance launch<a name="liw-initiate-instance-launch"></a>

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation bar at the top of the screen, the current AWS Region is displayed \(for example, US East \(Ohio\)\)\. Select a Region in which to launch the instance\. This choice is important because some Amazon EC2 resources can be shared between Regions, while others can't\. For more information, see [Resource locations](resources.md)\.

1. From the Amazon EC2 console dashboard, choose **Launch instance**\.

### Name and tags<a name="liw-name-and-tags"></a>

The instance name is a tag, where the key is **Name**, and the value is the name that you specify\. You can tag the instance, volumes, elastic graphics, and network interfaces\. For Spot Instances, you can tag the Spot Instance request only\. For information about tags, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

Specifying an instance name and additional tags is optional\.
+ For **Name**, enter a descriptive name for the instance\. If you don't specify a name, the instance can be identified by its ID, which is automatically generated when you launch the instance\.
+ To add additional tags, choose **Add additional tags**\. Choose **Add tag**, and then enter a key and value, and select the resource type to tag\. Choose **Add tag** again for each additional tag to add\.

### Application and OS Images \(Amazon Machine Image\)<a name="liw-ami"></a>

An Amazon Machine Image \(AMI\) contains the information required to create an instance\. For example, an AMI might contain the software that's required to act as a web server, such as Linux, Apache, and your website\.

You can find a suitable AMI as follows\. With each option for finding an AMI, you can choose **Cancel** \(at top right\) to return to the launch instance wizard without choosing an AMI\.

**Search bar**  
To search through all available AMIs, enter a keyword in the AMI search bar and then press **Enter**\. To select an AMI, choose **Select**\.

**Recents**  
The AMIs that you've recently used\.  
Choose **Recently launched** or **Currently in use**, and then, from **Amazon Machine Image \(AMI\)**, select an AMI\.

**My AMIs**  
The private AMIs that you own, or private AMIs that have been shared with you\.  
Choose **Owned by me** or **Shared with me**, and then, from **Amazon Machine Image \(AMI\)**, select an AMI\.

**Quick Start**  
AMIs are grouped by operating system \(OS\) to help you get started quickly\.  
First select the OS that you need, and then, from **Amazon Machine Image \(AMI\)**, select an AMI\. To select an AMI that is eligible for the free tier, make sure that the AMI is marked **Free tier eligible**\.

**Browse more AMIs**  
Choose **Browse more AMIs** to browse the full AMI catalog\.  
+ To search through all available AMIs, enter a keyword in the search bar and then press **Enter**\.
+ To find an AMI by using a Systems Manager parameter, choose the arrow button to the right of the search bar, and then choose **Search by Systems Manager parameter**\. For more information, see [Use a Systems Manager parameter to find an AMI](finding-an-ami.md#using-systems-manager-parameter-to-find-AMI)\.
+ To search by category, choose **Quickstart AMIs**, **My AMIs**, **AWS Marketplace AMIs**, or **Community AMIs**\.

  The AWS Marketplace is an online store where you can buy software that runs on AWS, including AMIs\. For more information about launching an instance from the AWS Marketplace, see [Launch an AWS Marketplace instance](launch-marketplace-console.md)\. In **Community AMIs**, you can find AMIs that AWS community members have made available for others to use\. AMIs from Amazon or a verified partner are marked **Verified provider**\.
+ To filter the list of AMIs, select one or more check boxes under **Refine results** on the left of the screen\. The filter options are different depending on the selected search category\.
+ Check the **Root device type** listed for each AMI\. Notice which AMIs are the type that you need: either **ebs** \(backed by Amazon EBS\) or **instance\-store** \(backed by instance store\)\. For more information, see [Storage for the root device](ComponentsAMIs.md#storage-for-the-root-device)\. 
+ Check the **Virtualization** type listed for each AMI\. Notice which AMIs are the type that you need: either **hvm** or **paravirtual**\. For example, some instance types require HVM\. For more information, see [Linux AMI virtualization types](virtualization_types.md)\.
+ Check the **Boot mode** listed for each AMI\. Notice which AMIs use the boot mode that you need: either **legacy\-bios**, **uefi**, or **uefi\-preferred**\. For more information, see [Boot modes](ami-boot.md)\.
+ Choose an AMI that meets your needs, and then choose **Select**\.

**Warning when changing the AMI**  
If you modify the configuration of any volumes or security groups associated with the selected AMI, and then you choose a different AMI, a window opens to warn you that some of your current settings will be changed or removed\. You can review the changes to the security groups and volumes\. Furthermore, you can either view which volumes will be added and deleted, or view only the volumes that will be added\.

### Instance type<a name="liw-instance-type"></a>

The instance type defines the hardware configuration and size of the instance\. Larger instance types have more CPU and memory\. For more information, see [Instance types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html)\.
+ For **Instance type**, select the instance type for the instance\.

  **Free Tier** – If your AWS account is less than 12 months old, you can use Amazon EC2 under the Free Tier by selecting the **t2\.micro** instance type \(or the **t3\.micro** instance type in Regions where **t2\.micro** is unavailable\)\. If an instance type is eligible under the Free Tier, it is labeled **Free tier eligible**\. For more information about t2\.micro and t3\.micro, see [Burstable performance instances](burstable-performance-instances.md)\.
+ **Compare instance types**: You can compare different instance types by the following attributes: number of vCPUs, architecture, amount of memory \(GiB\), amount of storage \(GB\), storage type, and network performance\.

### Key pair \(login\)<a name="liw-key-pair"></a>

For **Key pair name**, choose an existing key pair, or choose **Create new key pair** to create a new one\. For more information, see [Amazon EC2 key pairs and Linux instances](ec2-key-pairs.md)\.

**Important**  
If you choose the **Proceed without key pair \(Not recommended\)** option, you won't be able to connect to the instance unless you choose an AMI that is configured to allow users another way to log in\.

### Network settings<a name="liw-network-settings"></a>

Configure the network settings, as necessary\.
+ **VPC**: Choose an existing VPC for your instance\. You can choose the default VPC or a VPC that you created\. For more information, see [Virtual private clouds](using-vpc.md)\.
+ **Subnet**: You can launch an instance in a subnet associated with an Availability Zone, Local Zone, Wavelength Zone, or Outpost\.

  To launch the instance in an Availability Zone, select the subnet in which to launch your instance\. To create a new subnet, choose **Create new subnet** to go to the Amazon VPC console\. When you are done, return to the launch instance wizard and choose the Refresh icon to load your subnet in the list\.

  To launch the instance in a Local Zone, select a subnet that you created in the Local Zone\. 

  To launch an instance in an Outpost, select a subnet in a VPC that you associated with the Outpost\.
+ **Auto\-assign Public IP**: Specify whether your instance receives a public IPv4 address\. By default, instances in a default subnet receive a public IPv4 address, and instances in a nondefault subnet don't\. You can select **Enable** or **Disable** to override the subnet's default setting\. For more information, see [Public IPv4 addresses](using-instance-addressing.md#concepts-public-addresses)\.
+ **Firewall \(security groups\)**: Use a security group to define firewall rules for your instance\. These rules specify which incoming network traffic is delivered to your instance\. All other traffic is ignored\. For more information about security groups, see [Amazon EC2 security groups for Linux instances](ec2-security-groups.md)\.

  If you add a network interface, you must specify the same security group in the network interface\.

  Select or create a security group as follows:
  + To select an existing security group for your VPC, choose **Select existing security group**, and select your security group from **Common security groups**\.
  + To create a new security group for your VPC, choose **Create security group**\. The launch instance wizard automatically defines the **launch\-wizard\-*x*** security group and provides the following check boxes for quickly adding security group rules:

    **Allow SSH traffic from** – Creates an inbound rule to allow you to connect to your instance over SSH \(port 22\)\. Specify whether the traffic comes from **Anywhere**, **Custom**, or **My IP**\.

    **Allow HTTPs traffic from the internet** – Creates an inbound rule that opens port 443 \(HTTPS\) to allow internet traffic from anywhere\. If your instance will be a web server, you'll need this rule\.

    **Allow HTTP traffic from the internet** – Creates an inbound rule that opens port 80 \(HTTP\) to allow internet traffic from anywhere\. If your instance will be a web server, you'll need this rule\.

    You can edit these rules and add rules to suit your needs\.

    To edit or add a rule, choose **Edit** \(at top right\)\. To add a rule, choose **Add security group rule**\. For **Type**, select the network traffic type\. The **Protocol** field is automatically filled in with the protocol to open to network traffic\. For **Source type**, select the source type\. To let the launch instance wizard add your computer's public IP address, choose **My IP**\. However, if you are connecting through an ISP or from behind your firewall without a static IP address, you need to find out the range of IP addresses used by client computers\.
**Warning**  
Rules that enable all IP addresses \(`0.0.0.0/0`\) to access your instance over SSH or RDP are acceptable if you are briefly launching a test instance and will stop or terminate it soon, but are unsafe for production environments\. You should authorize only a specific IP address or range of addresses to access your instance\.
+ **Advanced network configuration** – Available only if you choose a subnet\.

  **Network interface**
  + **Device index**: The index of the network card\. The primary network interface must be assigned to network card index **0**\. Some instance types support multiple network cards\.
  + **Network interface**: Select **New interface** to let Amazon EC2 create a new interface, or select an existing, available network interface\.
  + **Description**: \(Optional\) A description for the new network interface\.
  + **Subnet**: The subnet in which to create the new network interface\. For the primary network interface \(`eth0`\), this is the subnet in which the instance is launched\. If you've entered an existing network interface for `eth0`, the instance is launched in the subnet in which the network interface is located\.
  + **Security groups**: One or more security groups in your VPC with which to associate the network interface\.
  + **Primary IP**: A private IPv4 address from the range of your subnet\. Leave blank to let Amazon EC2 choose a private IPv4 address for you\.
  + **Secondary IP**: One or more additional private IPv4 addresses from the range of your subnet\. Choose **Manually assign** and enter an IP address\. Choose **Add IP** to add another IP address\. Alternatively, choose **Automatically assign** to let Amazon EC2 choose one for you, and enter a value to indicate the number of IP addresses to add\.
  + \(IPv6\-only\) **IPv6 IPs**: An IPv6 address from the range of the subnet\. Choose **Manually assign** and enter an IP address\. Choose **Add IP** to add another IP address\. Alternatively, choose **Automatically assign** to let Amazon EC2 choose one for you, and enter a value to indicate the number of IP addresses to add\.
  + **IPv4 Prefixes**: The IPv4 prefixes for the network interface\.
  + **IPv6 Prefixes**: The IPv6 prefixes for the network interface\.
  + **Delete on termination**: Whether the network interface is deleted when the instance is deleted\.
  + **Elastic Fabric Adapter**: Indicates whether the network interface is an Elastic Fabric Adapter\. For more information, see [Elastic Fabric Adapter](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html)\.

  Choose **Add network interface** to add a secondary network interface\. A secondary network interface can reside in a different subnet of the VPC, provided it's in the same Availability Zone as your instance\.

  For more information, see [Elastic network interfaces](using-eni.md)\. If you specify more than one network interface, your instance cannot receive a public IPv4 address\. Additionally, if you specify an existing network interface for eth0, you cannot override the subnet's public IPv4 setting using **Auto\-assign Public IP**\. For more information, see [Assign a public IPv4 address during instance launch](using-instance-addressing.md#public-ip-addresses)\.

### Configure storage<a name="liw-storage"></a>

The AMI you selected includes one or more volumes of storage, including the root volume\. You can specify additional volumes to attach to the instance\.

You can use the **Simple** or **Advanced** view\. With the **Simple** view, you specify the size and type of the volume\. To specify all volume parameters, choose the **Advanced** view \(at top right of the card\)\.

By using the **Advanced** view, you can configure each volume as follows:
+ **Storage type**: Select Amazon EBS or instance store volumes to associate with your instance\. The volume types available in the list depend on the instance type that you've chosen\. For more information, see [Amazon EC2 instance store](InstanceStorage.md) and [Amazon EBS volumes](ebs-volumes.md)\.
+ **Device name**: Select from the list of available device names for the volume\. 
+ **Snapshot**: Select the snapshot from which to restore the volume\. You can search for available shared and public snapshots by entering text into the **Snapshot** field\.
+ **Size \(GiB\)**: For EBS volumes, you can specify a storage size\. If you have selected an AMI and instance that are eligible for the free tier, keep in mind that to stay within the free tier, you must stay under 30 GiB of total storage\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.
+ **Volume type**: For EBS volumes, select a volume type\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)\.
+ **IOPS**: If you have selected a Provisioned IOPS SSD volume type, then you can enter the number of I/O operations per second \(IOPS\) that the volume can support\.
+ **Delete on termination**: For Amazon EBS volumes, choose **Yes** to delete the volume when the instance is terminated, or choose **No** to keep the volume\. For more information, see [Preserve Amazon EBS volumes on instance termination](terminating-instances.md#preserving-volumes-on-termination)\.
+ **Encrypted**: If the instance type supports EBS encryption, you can choose **Yes** to enable encryption for the volume\. If you have enabled encryption by default in this Region, encryption is enabled for you\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.
+ **KMS key**: If you selected **Yes** for **Encrypted**, then you must select a customer managed key to use to encrypt the volume\. If you have enabled encryption by default in this Region, the default customer managed key is selected for you\. You can select a different key or specify the ARN of any customer managed key that you created\.
+ **File systems**: Mount an Amazon EFS or Amazon FSx file system to the instance\. For more information about mounting an Amazon EFS file system, see [Use Amazon EFS with Amazon EC2](AmazonEFS.md)\. For more information about mounting an Amazon FSx file system, see [Use Amazon FSx with Amazon EC2](storage_fsx.md)

### Advanced details<a name="liw-advanced-details"></a>

For **Advanced details**, expand the section to view the fields and specify any additional parameters for the instance\.
+ **Purchasing option**: Choose **Request Spot Instances** to request Spot Instances at the Spot price, capped at the On\-Demand price, and choose **Customize** to change the default Spot Instance settings\. You can set your maximum price \(not recommended\), and change the request type, request duration, and interruption behavior\. If you do not request a Spot Instance, Amazon EC2 launches an On\-Demand Instance by default\. For more information, see [Create a Spot Instance request](spot-requests.md#using-spot-instances-request)\.
+ **Domain join directory**: Select the AWS Directory Service directory \(domain\) to which your Linux instance is joined after launch\. If you select a domain, you must select an IAM role with the required permissions\. For more information, see [Seamlessly join a Linux EC2 instance to your AWS Managed Microsoft AD directory](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/seamlessly_join_linux_instance.html)\.
+ **IAM instance profile**: Select an AWS Identity and Access Management \(IAM\) instance profile to associate with the instance\. For more information, see [IAM roles for Amazon EC2](iam-roles-for-amazon-ec2.md)\.
+ **Hostname type**: Select whether the guest OS hostname of the instance will include the resource name or the IP name\. For more information, see [Amazon EC2 instance hostname types](ec2-instance-naming.md)\.
+ **DNS Hostname**: Determines if the DNS queries to the resource name or the IP name \(depending on what you selected for **Hostname type**\) will respond with the IPv4 address \(A record\), IPv6 address \(AAAA record\), or both\. For more information, see [Amazon EC2 instance hostname types](ec2-instance-naming.md)\.
+ **Shutdown behavior**: Select whether the instance should stop or terminate when shut down\. For more information, see [Change the instance initiated shutdown behavior](terminating-instances.md#Using_ChangingInstanceInitiatedShutdownBehavior)\.
+ **Stop \- Hibernate behavior**: To enable hibernation, choose **Enable**\. This field is available only if your instance meets the hibernation prerequisites\. For more information, see [Hibernate your On\-Demand Linux instance](Hibernate.md)\.
+ **Termination protection**: To prevent accidental termination, choose **Enable**\. For more information, see [Enable termination protection](terminating-instances.md#Using_ChangingDisableAPITermination)\.
+ **Stop protection**: To prevent accidental stopping, choose **Enable**\. For more information, see [Enable stop protection](Stop_Start.md#Using_StopProtection)\.
+ **Detailed CloudWatch monitoring**: Choose **Enable** to turn on detailed monitoring of your instance using Amazon CloudWatch\. Additional charges apply\. For more information, see [Monitor your instances using CloudWatch](using-cloudwatch.md)\.
+ **Elastic inference**: An elastic inference accelerator to attach to your EC2 CPU instance\. For more information, see [Working with Amazon Elastic Inference](https://docs.aws.amazon.com/elastic-inference/latest/developerguide/working-with-ei.html) in the *Amazon Elastic Inference Developer Guide*\.
**Note**  
Starting April 15, 2023, AWS will not onboard new customers to Amazon Elastic Inference \(EI\), and will help current customers migrate their workloads to options that offer better price and performance\. After April 15, 2023, new customers will not be able to launch instances with Amazon EI accelerators in Amazon SageMaker, Amazon ECS, or Amazon EC2\. However, customers who have used Amazon EI at least once during the past 30\-day period are considered current customers and will be able to continue using the service\.
+ **Credit specification**: Choose **Unlimited** to enable applications to burst beyond the baseline for as long as needed\. This field is only valid for **T** instances\. Additional charges may apply\. For more information, see [Burstable performance instances](burstable-performance-instances.md)\.
+ **Placement group name**: Specify a placement group in which to launch the instance\. You can select an existing placement group, or create a new one\. Not all instance types support launching an instance in a placement group\. For more information, see [Placement groups](placement-groups.md)\.
+ **EBS\-optimized instance**: An instance that's optimized for Amazon EBS uses an optimized configuration stack and provides additional, dedicated capacity for Amazon EBS I/O\. If the instance type supports this feature, choose **Enable** to enable it\. Additional charges apply\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.
+ **Capacity Reservation**: Specify whether to launch the instance into any open Capacity Reservation \(**Open**\), a specific Capacity Reservation \(**Target by ID**\), or a Capacity Reservation group \(**Target by group**\)\. To specify that a Capacity Reservation should not be used, choose **None**\. For more information, see [Launch instances into an existing Capacity Reservation](capacity-reservations-using.md#capacity-reservations-launch)\.
+ **Tenancy**: Choose whether to run your instance on shared hardware \(**Shared**\), isolated, dedicated hardware \(**Dedicated**\), or on a Dedicated Host \(**Dedicated host**\)\. If you choose to launch the instance onto a Dedicated Host, you can specify whether to launch the instance into a host resource group or you can target a specific Dedicated Host\. Additional charges may apply\. For more information, see [Dedicated Instances](dedicated-instance.md) and [Dedicated Hosts](dedicated-hosts-overview.md)\.
+ **RAM disk ID**: \(Only valid for paravirtual \(PV\) AMIs\) Select a RAM disk for the instance\. If you have selected a kernel, you might need to select a specific RAM disk with the drivers to support it\.
+ **Kernel ID**: \(Only valid for paravirtual \(PV\) AMIs\) Select a kernel for the instance\.
+ **Nitro Enclave**: Allows you to create isolated execution environments, called enclaves, from Amazon EC2 instances\. Select **Enable** to enable the instance for AWS Nitro Enclaves\. For more information, see [What is AWS Nitro Enclaves?](https://docs.aws.amazon.com/enclaves/latest/user/nitro-enclave.html) in the *AWS Nitro Enclaves User Guide*\.
+ **License configurations**: You can launch instances against the specified license configuration to track your license usage\. For more information, see [Create a license configuration](https://docs.aws.amazon.com/license-manager/latest/userguide/create-license-configuration.html) in the *AWS License Manager User Guide*\.
+ **Metadata accessible**: You can enable or disable access to the instance metadata\. For more information, see [Configure instance metadata options for new instances](configuring-IMDS-new-instances.md)\.
+ **Metadata transport**: Enable the instance to reach the link local IMDSv2 IPv6 address \(`fd00:ec2::254`\) to retrieve instance metadata\. This option is only available if you are launching [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances) into an [IPv6\-only subnet](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html#subnet-ip-address-range)\. For more information about retrieving instance metadata, see [Retrieve instance metadata](instancedata-data-retrieval.md)\.
+ **Metadata version**: If you enable access to the instance metadata, you can choose to require the use of Instance Metadata Service Version 2 when requesting instance metadata\. For more information, see [Configure instance metadata options for new instances](configuring-IMDS-new-instances.md)\.
+ **Metadata response hop limit**: If you enable instance metadata, you can set the allowable number of network hops for the metadata token\. For more information, see [Configure instance metadata options for new instances](configuring-IMDS-new-instances.md)\.
+ **Allow tags in metadata**: If you select **Enable**, the instance will allow access to all of its tags from its metadata\. If no value is specified, then by default, access to the tags in instance metadata is not allowed\. For more information, see [Allow access to tags in instance metadata](Using_Tags.md#allow-access-to-tags-in-IMDS)\.
+ **User data**: You can specify user data to configure an instance during launch, or to run a configuration script\. For more information, see [Run commands on your Linux instance at launch](user-data.md)\.

### Summary<a name="liw-summary"></a>

Use the **Summary** panel to specify the number of instances to launch, to review your instance configuration, and to launch your instances\.
+ **Number of instances**: Enter the number of instances to launch\. All of the instances will launch with the same configuration\.
**Tip**  
To ensure faster instance launches, break up large requests into smaller batches\. For example, create five separate launch requests for 100 instances each instead of one launch request for 500 instances\.
+ \(Optional\) If you specify more than one instance, to help ensure that you maintain the correct number of instances to handle demand on your application, you can choose **consider EC2 Auto Scaling** to create a launch template and an Auto Scaling group\. Auto Scaling scales the number of instances in the group according to your specifications\. For more information, see the [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/ec2/userguide/)\.
**Note**  
If Amazon EC2 Auto Scaling marks an instance that is in an Auto Scaling group as unhealthy, the instance is automatically scheduled for replacement where it is terminated and another is launched, and you lose your data on the original instance\. An instance is marked as unhealthy if you stop or reboot the instance, or if another event marks the instance as unhealthy\. For more information, see [Health checks for Auto Scaling instances](https://docs.aws.amazon.com/autoscaling/ec2/userguide/healthcheck.html) in the *Amazon EC2 Auto Scaling User Guide*\. 
+ Review the details of your instance, and make any necessary changes\. You can navigate directly to a section by choosing its link in the **Summary** panel\.
+ When you're ready to launch your instance, choose **Launch instance**\.

  If the instance fails to launch or the state immediately goes to `terminated` instead of `running`, see [Troubleshoot instance launch issues](troubleshooting-launch.md)\.

  \(Optional\) You can create a billing alert for the instance\. On the confirmation screen, under **Next Steps**, choose **Create billing alerts** and follow the directions\. Billing alerts can also be created after you launch the instance\. For more information, see [Creating a billing alarm to monitor your estimated AWS charges](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html) in the *Amazon CloudWatch User Guide*\.