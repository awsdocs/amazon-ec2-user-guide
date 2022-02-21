# Launch an instance using the Launch Instance Wizard<a name="launching-instance"></a>

You can launch an instance using the launch instance wizard\. The launch instance wizard specifies all the launch parameters required for launching an instance\. Where the launch instance wizard provides a default value, you can accept the default or specify your own value\. At the very least, you need to select an AMI and a key pair to launch an instance\.

Before you launch your instance, be sure that you are set up\. For more information, see [Set up to use Amazon EC2](get-set-up-for-amazon-ec2.md)\.

**Important**  
When you launch an instance that's not within the [AWS Free Tier](https://aws.amazon.com/free/), you are charged for the time that the instance is running, even if it remains idle\.

**Topics**
+ [Initiate instance launch](#initiate-instance-launch)
+ [Step 1: Choose an Amazon Machine Image \(AMI\)](#step-1-AMI)
+ [Step 2: Choose an Instance Type](#choose-an-instance-type-page)
+ [Step 3: Configure Instance Details](#configure_instance_details_step)
+ [Step 4: Add Storage](#step-4-add-storage)
+ [Step 5: Add Tags](#step-5-add-tags)
+ [Step 6: Configure Security Group](#step-6-configure-security-group)
+ [Step 7: Review Instance Launch and Select Key Pair](#step-7-review-instance-launch)
+ [Launch an instance using the new launch instance wizard – beta](ec2-launch-instance-wizard.md)

## Initiate instance launch<a name="initiate-instance-launch"></a>

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation bar at the top of the screen, the current Region is displayed \(for example, US East \(Ohio\)\)\. Select a Region for the instance that meets your needs\. This choice is important because some Amazon EC2 resources can be shared between Regions, while others can't\. For more information, see [Resource locations](resources.md)\.

1. From the Amazon EC2 console dashboard, choose **Launch instance**\.

## Step 1: Choose an Amazon Machine Image \(AMI\)<a name="step-1-AMI"></a>

When you launch an instance, you must select a configuration, known as an Amazon Machine Image \(AMI\)\. An AMI contains the information required to create a new instance\. For example, an AMI might contain the software required to act as a web server, such as Linux, Apache, and your website\.

When you launch an instance, you can either select an AMI from the list, or you can select a Systems Manager parameter that points to an AMI ID\. For more information, see [Using a Systems Manager parameter to find an AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html#using-systems-manager-parameter-to-find-AMI)\.

On the **Choose an Amazon Machine Image \(AMI\)** page, use one of two options to choose an AMI\. Either [search the list of AMIs](#procedure-search-list-of-AMIs), or [search by Systems Manager parameter](#procedure-by-systems-manager-parameter)\.<a name="procedure-search-list-of-AMIs"></a>

**By searching the list of AMIs**

1. Select the type of AMI to use in the left pane:  
**Quick Start**  
A selection of popular AMIs to help you get started quickly\. To select an AMI that is eligible for the free tier, choose **Free tier only** in the left pane\. These AMIs are marked **Free tier eligible**\.  
**My AMIs**  
The private AMIs that you own, or private AMIs that have been shared with you\. To view AMIs that are shared with you, choose **Shared with me** in the left pane\.  
**AWS Marketplace**  
An online store where you can buy software that runs on AWS, including AMIs\. For more information about launching an instance from the AWS Marketplace, see [Launch an AWS Marketplace instance](launch-marketplace-console.md)\.  
**Community AMIs**  
The AMIs that AWS community members have made available for others to use\. To filter the list of AMIs by operating system, choose the appropriate check box under **Operating system**\. You can also filter by architecture and root device type\.

1. Check the **Root device type** listed for each AMI\. Notice which AMIs are the type that you need, either `ebs` \(backed by Amazon EBS\) or `instance-store` \(backed by instance store\)\. For more information, see [Storage for the root device](ComponentsAMIs.md#storage-for-the-root-device)\. 

1. Check the **Virtualization type** listed for each AMI\. Notice which AMIs are the type that you need, either `hvm` or `paravirtual`\. For example, some instance types require HVM\. For more information, see [Linux AMI virtualization types](virtualization_types.md)\.

1. Check the **Boot mode** listed for each AMI\. Notice which AMIs use the boot mode that you need, either `legacy-bios` or `uefi`\. For more information, see [Boot modes](ami-boot.md)\.

1. Choose an AMI that meets your needs, and then choose **Select**\.<a name="procedure-by-systems-manager-parameter"></a>

**By Systems Manager parameter**

1. Choose **Search by Systems Manager parameter** \(at top right\)\.

1. For **Systems Manager parameter**, select a parameter\. The corresponding AMI ID appears next to **Currently resolves to**\.

1. Choose **Search**\. The AMIs that match the AMI ID appear in the list\.

1. Select the AMI from the list, and choose **Select**\.

## Step 2: Choose an Instance Type<a name="choose-an-instance-type-page"></a>

On the **Choose an Instance Type** page, select the hardware configuration and size of the instance to launch\. Larger instance types have more CPU and memory\. For more information, see [Instance types](instance-types.md)\.

To remain eligible for the free tier, choose the **t2\.micro** instance type \(or the **t3\.micro** instance type in Regions where **t2\.micro** is unavailable\)\. For more information, see [Burstable performance instances](burstable-performance-instances.md)\.

By default, the wizard displays current generation instance types, and selects the first available instance type based on the AMI that you selected\. To view previous generation instance types, choose **All generations** from the filter list\.

**Note**  
To set up an instance quickly for testing purposes, choose **Review and Launch** to accept the default configuration settings, and launch your instance\. Otherwise, to configure your instance further, choose **Next: Configure Instance Details**\.

## Step 3: Configure Instance Details<a name="configure_instance_details_step"></a>

On the **Configure Instance Details** page, change the following settings as necessary \(expand **Advanced Details** to see all the settings\), and then choose **Next: Add Storage**:
+ **Number of instances**: Enter the number of instances to launch\.
**Tip**  
To ensure faster instance launches, break up large requests into smaller batches\. For example, create five separate launch requests for 100 instances each instead of one launch request for 500 instances\.
+ \(Optional\) To help ensure that you maintain the correct number of instances to handle demand on your application, you can choose **Launch into Auto Scaling Group** to create a launch configuration and an Auto Scaling group\. Auto Scaling scales the number of instances in the group according to your specifications\. For more information, see the [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/ec2/userguide/)\.
**Note**  
If Amazon EC2 Auto Scaling marks an instance that is in an Auto Scaling group as unhealthy, the instance is automatically scheduled for replacement where it is terminated and another is launched, and you lose your data on the original instance\. An instance is marked as unhealthy if you stop or reboot the instance, or if another event marks the instance as unhealthy\. For more information, see [Health Checks for Auto Scaling Instances](https://docs.aws.amazon.com/autoscaling/ec2/userguide/healthcheck.html) in the *Amazon EC2 Auto Scaling User Guide*\. 
+ **Purchasing option**: Choose **Request Spot instances** to launch a Spot Instance\. This adds and removes options from this page\. Set your maximum price, and optionally update the request type, interruption behavior, and request validity\. For more information, see [Create a Spot Instance request](spot-requests.md#using-spot-instances-request)\.
+ **Network**: Select the VPC, or to create a new VPC, choose **Create new VPC** to go the Amazon VPC console\. When you have finished, return to the wizard and choose **Refresh** to load your VPC in the list\.
+ **Subnet**: You can launch an instance in a subnet associated with an Availability Zone, Local Zone, Wavelength Zone or Outpost\.

  To launch the instance in an Availability Zone, select the subnet into which to launch your instance\. You can select **No preference** to let AWS choose a default subnet in any Availability Zone\. To create a new subnet, choose **Create new subnet** to go to the Amazon VPC console\. When you are done, return to the wizard and choose **Refresh** to load your subnet in the list\.

  To launch the instance in a Local Zone, select a subnet that you created in the Local Zone\. 

  To launch an instance in an Outpost, select a subnet in a VPC that you associated with an Outpost\.
+ **Auto\-assign Public IP**: Specify whether your instance receives a public IPv4 address\. By default, instances in a default subnet receive a public IPv4 address and instances in a nondefault subnet do not\. You can select **Enable** or **Disable** to override the subnet's default setting\. For more information, see [Public IPv4 addresses](using-instance-addressing.md#concepts-public-addresses)\.
+ **Auto\-assign IPv6 IP**: Specify whether your instance receives an IPv6 address from the range of the subnet\. Select **Enable** or **Disable** to override the subnet's default setting\. This option is only available if you've associated an IPv6 CIDR block with your VPC and subnet\. For more information, see [Your VPC and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html) in the *Amazon VPC User Guide*\.
+  **Hostname type**: Select if you want the guest OS hostname of the EC2 instance to be the Resource name or IP name\. For more information about hostname type and these options, see [Amazon EC2 instance hostname types](ec2-instance-naming.md)\. 
+  **DNS Hostname**: Determines if the DNS queries to the IP name and/or the Resource name will respond with the IPv4 address \(A record\), IPv6 address \(AAAA record\), or both\. For more information about these options, see [Amazon EC2 instance hostname types](ec2-instance-naming.md)\.
+ **Domain join directory**: Select the AWS Directory Service directory \(domain\) to which your Linux instance is joined after launch\. If you select a domain, you must select an IAM role with the required permissions\. For more information, see [Seamlessly Join a Linux EC2 Instance to Your AWS Managed Microsoft AD Directory](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/seamlessly_join_linux_instance.html)\.
+ **Placement group**: A placement group determines the placement strategy of your instances\. Select an existing placement group, or create a new one\. This option is only available if you've selected an instance type that supports placement groups\. For more information, see [Placement groups](placement-groups.md)\.
+ **Capacity Reservation**: Specify whether to launch the instance into shared capacity, any `open` Capacity Reservation, a specific Capacity Reservation, or a Capacity Reservation group\. For more information, see [Launch instances into an existing Capacity Reservation](capacity-reservations-using.md#capacity-reservations-launch)\.
+ **IAM role**: Select an AWS Identity and Access Management \(IAM\) role to associate with the instance\. For more information, see [IAM roles for Amazon EC2](iam-roles-for-amazon-ec2.md)\.
+ **CPU options**: Choose **Specify CPU options** to specify a custom number of vCPUs during launch\. Set the number of CPU cores and threads per core\. For more information, see [Optimize CPU options](instance-optimize-cpu.md)\.
+ **Shutdown behavior**: Select whether the instance should stop or terminate when shut down\. For more information, see [Change the instance initiated shutdown behavior](terminating-instances.md#Using_ChangingInstanceInitiatedShutdownBehavior)\.
+ **Stop \- Hibernate behavior**: To enable hibernation, select this check box\. This option is only available if your instance meets the hibernation prerequisites\. For more information, see [Hibernate your On\-Demand Linux instance](Hibernate.md)\.
+ **Enable termination protection**: To prevent accidental termination, select this check box\. For more information, see [Enable termination protection](terminating-instances.md#Using_ChangingDisableAPITermination)\.
+ **Monitoring**: Select this check box to enable detailed monitoring of your instance using Amazon CloudWatch\. Additional charges apply\. For more information, see [Monitor your instances using CloudWatch](using-cloudwatch.md)\.
+ **EBS\-optimized instance**: An Amazon EBS\-optimized instance uses an optimized configuration stack and provides additional, dedicated capacity for Amazon EBS I/O\. If the instance type supports this feature, select this check box to enable it\. Additional charges apply\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.
+ **Tenancy**: If you are launching your instance into a VPC, you can choose to run your instance on isolated, dedicated hardware \(**Dedicated**\) or on a Dedicated Host \(**Dedicated host**\)\. Additional charges may apply\. For more information, see [Dedicated Instances](dedicated-instance.md) and [Dedicated Hosts](dedicated-hosts-overview.md)\.
+ **T2/T3 Unlimited**: Select this check box to enable applications to burst beyond the baseline for as long as needed\. Additional charges may apply\. For more information, see [Burstable performance instances](burstable-performance-instances.md)\.
+ **File systems**: To create a new file system to mount to your instance, choose **Create new file system**, enter a name for the new file system, and then choose **Create**\. The file system is created using Amazon EFS Quick Create, which applies the service recommended settings\. The security groups required to enable access to the file system are automatically created and attached to the instance and the mount targets of the file system\. You can also choose to manually create and attach the required security groups\. For more information, see [Create an EFS file system using Amazon EFS Quick Create](AmazonEFS.md#quick-create)\.

  To mount one or more existing Amazon EFS file systems to your instance, choose **Add file system** and then choose the file systems to mount and the mount points to use\. For more information, see [Create an EFS file system and mount it to your instance](AmazonEFS.md#create-mount)\. 
+ **Network interfaces**: If you selected a specific subnet, you can specify up to two network interfaces for your instance:
  + For **Network Interface**, select **New network interface** to let AWS create a new interface, or select an existing, available network interface\.
  + For **Primary IP**, enter a private IPv4 address from the range of your subnet, or leave **Auto\-assign** to let AWS choose a private IPv4 address for you\.
  + For **Secondary IP addresses**, choose **Add IP** to assign more than one private IPv4 address to the selected network interface\.
  + \(IPv6\-only\) For **IPv6 IPs**, choose **Add IP**, and enter an IPv6 address from the range of the subnet, or leave **Auto\-assign** to let AWS choose one for you\.
  + **Network Card Index**: The index of the network card\. The primary network interface must be assigned to network card index 0\. Some instance types support multiple network cards\.
  + Choose **Add Device** to add a secondary network interface\. A secondary network interface can reside in a different subnet of the VPC, provided it's in the same Availability Zone as your instance\.

  For more information, see [Elastic network interfaces](using-eni.md)\. If you specify more than one network interface, your instance cannot receive a public IPv4 address\. Additionally, if you specify an existing network interface for eth0, you cannot override the subnet's public IPv4 setting using **Auto\-assign Public IP**\. For more information, see [Assign a public IPv4 address during instance launch](using-instance-addressing.md#public-ip-addresses)\.
+ **Kernel ID**: \(Only valid for paravirtual \(PV\) AMIs\) Select **Use default** unless you want to use a specific kernel\.
+ **RAM disk ID**: \(Only valid for paravirtual \(PV\) AMIs\) Select **Use default** unless you want to use a specific RAM disk\. If you have selected a kernel, you may need to select a specific RAM disk with the drivers to support it\.
+ **Enclave**: Select **Enable** to enable the instance for AWS Nitro Enclaves\. For more information, see [ What is AWS Nitro Enclaves?](https://docs.aws.amazon.com/enclaves/latest/user/nitro-enclave.html) in the *AWS Nitro Enclaves User Guide*\.
+ **Metadata accessible**: You can enable or disable access to the instance metadata\. For more information, see [Use IMDSv2](configuring-instance-metadata-service.md)\.
+ **Metadata transport**: You can enable or disable the access method to the instance metadata service that's available for this EC2 instance based on the IP address type \(IPv4, IPv6, or IPv4 and IPv6\) of the instance\. For more information, see [Retrieve instance metadata](instancedata-data-retrieval.md)\.
+ **Metadata version**: If you enable access to the instance metadata, you can choose to require the use of Instance Metadata Service Version 2 when requesting instance metadata\. For more information, see [Configure instance metadata options for new instances](configuring-instance-metadata-options.md#configuring-IMDS-new-instances)\.
+ **Metadata token response hop limit**: If you enable instance metadata, you can set the allowable number of network hops for the metadata token\. For more information, see [Use IMDSv2](configuring-instance-metadata-service.md)\.
+ **User data**: You can specify user data to configure an instance during launch, or to run a configuration script\. To attach a file, select the **As file** option and browse for the file to attach\.

## Step 4: Add Storage<a name="step-4-add-storage"></a>

The AMI you selected includes one or more volumes of storage, including the root device volume\. On the **Add Storage** page, you can specify additional volumes to attach to the instance by choosing **Add New Volume**\. Configure each volume as follows, and then choose **Next: Add Tags**\.
+ **Type**: Select instance store or Amazon EBS volumes to associate with your instance\. The types of volume available in the list depend on the instance type you've chosen\. For more information, see [Amazon EC2 instance store](InstanceStorage.md) and [Amazon EBS volumes](ebs-volumes.md)\.
+ **Device**: Select from the list of available device names for the volume\. 
+ **Snapshot**: Enter the name or ID of the snapshot from which to restore a volume\. You can also search for available shared and public snapshots by typing text into the **Snapshot** field\. Snapshot descriptions are case\-sensitive\.
+ **Size**: For EBS volumes, you can specify a storage size\. Even if you have selected an AMI and instance that are eligible for the free tier, to stay within the free tier, you must stay under 30 GiB of total storage\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.
+ **Volume Type**: For EBS volumes, select a volume type\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)\.
+ **IOPS**: If you have selected a Provisioned IOPS SSD volume type, then you can enter the number of I/O operations per second \(IOPS\) that the volume can support\.
+ **Delete on Termination**: For Amazon EBS volumes, select this check box to delete the volume when the instance is terminated\. For more information, see [Preserve Amazon EBS volumes on instance termination](terminating-instances.md#preserving-volumes-on-termination)\.
+ **Encrypted**: If the instance type supports EBS encryption, you can specify the encryption state of the volume\. If you have enabled encryption by default in this Region, the default customer managed key is selected for you\. You can select a different key or disable encryption\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.

## Step 5: Add Tags<a name="step-5-add-tags"></a>

On the **Add Tags** page, specify [tags](Using_Tags.md) by providing key and value combinations\. You can tag the instance, the volumes, or both\. For Spot Instances, you can tag the Spot Instance request only\. Choose **Add another tag** to add more than one tag to your resources\. Choose **Next: Configure Security Group** when you are done\.

## Step 6: Configure Security Group<a name="step-6-configure-security-group"></a>

On the **Configure Security Group** page, use a security group to define firewall rules for your instance\. These rules specify which incoming network traffic is delivered to your instance\. All other traffic is ignored\. \(For more information about security groups, see [Amazon EC2 security groups for Linux instances](ec2-security-groups.md)\.\) Select or create a security group as follows, and then choose **Review and Launch**\.
+ To select an existing security group, choose **Select an existing security group**, and select your security group\. You can't edit the rules of an existing security group, but you can copy them to a new group by choosing **Copy to new**\. Then you can add rules as described in the next step\.
+ To create a new security group, choose **Create a new security group**\. The wizard automatically defines the launch\-wizard\-*x* security group and creates an inbound rule to allow you to connect to your instance over SSH \(port 22\)\.
+ You can add rules to suit your needs\. For example, if your instance is a web server, open ports 80 \(HTTP\) and 443 \(HTTPS\) to allow internet traffic\.

  To add a rule, choose **Add Rule**, select the protocol to open to network traffic, and then specify the source\. Choose **My IP** from the **Source** list to let the wizard add your computer's public IP address\. However, if you are connecting through an ISP or from behind your firewall without a static IP address, you need to find out the range of IP addresses used by client computers\.
**Warning**  
Rules that enable all IP addresses \(`0.0.0.0/0`\) to access your instance over SSH or RDP are acceptable for this short exercise, but are unsafe for production environments\. You should authorize only a specific IP address or range of addresses to access your instance\.

## Step 7: Review Instance Launch and Select Key Pair<a name="step-7-review-instance-launch"></a>

On the **Review Instance Launch** page, check the details of your instance, and make any necessary changes by choosing the appropriate **Edit** link\.

When you are ready, choose **Launch**\.

In the **Select an existing key pair or create a new key pair** dialog box, you can choose an existing key pair, or create a new one\. For example, choose **Choose an existing key pair**, then select the key pair you created when getting set up\. For more information, see [Amazon EC2 key pairs and Linux instances](ec2-key-pairs.md)\.

**Important**  
If you choose the **Proceed without key pair** option, you won't be able to connect to the instance unless you choose an AMI that is configured to allow users another way to log in\.

To launch your instance, select the acknowledgment check box, then choose **Launch Instances**\.

\(Optional\) You can create a status check alarm for the instance \(additional fees may apply\)\. \(If you're not sure, you can always add one later\.\) On the confirmation screen, choose **Create status check alarms** and follow the directions\. For more information, see [Create and edit status check alarms](monitoring-system-instance-status-check.md#creating_status_check_alarms)\.

If the instance fails to launch or the state immediately goes to `terminated` instead of `running`, see [Troubleshoot instance launch issues](troubleshooting-launch.md)\.