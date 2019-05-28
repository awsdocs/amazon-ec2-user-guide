# Getting Started with EFA<a name="efa-start"></a>

The following tasks help you to get started with Elastic Fabric Adapter\. In this tutorial, you create an EFA\-enabled AMI and an EFA\-enabled security group, and then launch EFA\-enabled instances into a cluster placement group using that AMI and security group\. 

**Topics**
+ [Step 1: Prepare an EFA\-enabled Security Group](#efa-start-security)
+ [Step 2: Launch a Temporary Instance](#efa-start-tempinstance)
+ [Step 3: Install EFA Software Components](#efa-start-enable)
+ [Step 4: Install your HPC Application](#efa-start-hpc-app)
+ [Step 5: Create an EFA\-enabled AMI](#efa-start-ami)
+ [Step 6: Launch EFA\-enabled Instances into a Cluster Placement Group](#efa-start-instances)
+ [Step 7: Terminate the Temporary Instance](#efa-start-terminate)

## Step 1: Prepare an EFA\-enabled Security Group<a name="efa-start-security"></a>

EFA requires a security group that allows all inbound and outbound traffic to and from the security group itself\.

**To create an EFA\-enabled security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups** and then choose **Create Security Group**\.

1. In the **Create Security Group** window, do the following:

   1. For **Security group name**, enter a descriptive name for the security group, such as `EFA-enabled security group`\.

   1. \(Optional\) For **Description**, enter a brief description of the security group\.

   1. For **VPC**, select the VPC into which you intend to launch your EFA\-enabled instances\.

   1. Choose **Create**\.

1. Select the security group that you created, and on the **Description** tab, copy the **Group ID**\.

1. On the **Inbound** and **Outbound** tabs, do the following:

   1. Choose **Edit**\.

   1. For **Type**, choose **All traffic**\.

   1. For **Source**, choose **Custom**\.

   1. Paste the security group ID that you copied into the field\.

   1. Choose **Save**\.

## Step 2: Launch a Temporary Instance<a name="efa-start-tempinstance"></a>

Launch a temporary instance that you can use to install and configure the EFA software components\. You use this instance to create an EFA\-enabled AMI from which you can launch your EFA\-enabled instances\.

**To launch a temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an AMI** page, choose one of the following supported AMIs and then choose **Select**: Amazon Linux, Amazon Linux 2, Red Hat Enterprise Linux 7\.6, CentOS 7\.6\.

1. On the **Choose an Instance Type** page, select one of the following supported instance types and then choose **Next: Configure Instance Details**: c5n\.18xlarge, i3en\.24xlarge, p3dn\.24xlarge\.

1. On the **Configure Instance Details** page, do the following:

   1. For **EFA**, choose **Enable**\.

   1. In the **Network Interfaces** section, for device **eth0**, choose **New network interface**\.

   1. Choose **Next: Add Storage**\.

1. On the **Add Storage** page, specify the volumes to attach to the instances in addition to the volumes specified by the AMI \(such as the root device volume\), and then choose **Next: Add Tags**\.

1. On the **Add Tags** page, specify a tag that you can use to identify the temporary instance, and then choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, for **Assign a security group**, select **Select an existing security group**, and then select the security group that you created in **Step 1\.**

1. On the **Review Instance Launch** page, review the settings, and then choose **Launch** to choose a key pair and to launch your instance\.

## Step 3: Install EFA Software Components<a name="efa-start-enable"></a>

Install the EFA\-enabled kernel, EFA drivers, libfabric, and Open MPI stack that is required to support EFA on your temporary instance\.

**To install the EFA software components on your temporary instance**

1. Connect to the instance you launched in **Step 2**\. For more information, see [Connect to Your Linux Instance](AccessingInstances.md)\.

1. Update the instance software\.

   ```
   $ sudo yum update -y
   ```

1. Download the EFA software installation files\. The software installation files are packaged into a compressed `.tar.gz` file\.

   ```
   $ wget https://s3-us-west-2.amazonaws.com/aws-efa-installer/aws-efa-installer-latest.tar.gz
   ```

1. Extract the EFA software installation files from the compressed `.tar.gz` file\. The files are extracted into a directory named `aws-efa-installer`\.

   ```
   $ tar -xf aws-efa-installer-latest.tar.gz
   ```

1. Navigate into the extracted directory\.

   ```
   $ cd aws-efa-installer
   ```

1. Run the EFA software installation script\. You can specify the `-y` option to run an unattended installation\.

   ```
   $ sudo ./efa_installer.sh -y
   ```

   The EFA software packages are installed in the `/opt/amazon/efa` directory\.

1. Confirm that the EFA software components were successfully installed\.

   ```
   $ fi_info -p efa
   ```

   The command should return information about the libfabric EFA interfaces\. The following is example output of the command:

   ```
   provider: efa
       fabric: EFA-fe80::1a:feff:feef:82a6
       domain: efa_0-rdm
       version: 3.0
       type: FI_EP_RDM
       protocol: FI_PROTO_EFA
   provider: efa
       fabric: EFA-fe80::1a:feff:feef:82a6
       domain: efa_0-dgrm
       version: 3.0
       type: FI_EP_DGRAM
       protocol: FI_PROTO_EFA
   provider: efa;ofi_rxd
       fabric: EFA-fe80::1a:feff:feef:82a6
       domain: efa_0-dgrm
       version: 1.0
       type: FI_EP_RDM
       protocol: FI_PROTO_RXD
   provider: efa;ofi_rxr
       fabric: EFA-fe80::1a:feff:feef:82a6
       domain: efa_0-rdm
       version: 1.0
       type: FI_EP_RDM
       protocol: FI_PROTO_RXR
   ```

## Step 4: Install your HPC Application<a name="efa-start-hpc-app"></a>

Install the HPC application on the temporary instance\. The installation procedure varies depending on the specific HPC application\. For more information about installing software on your Linux instance, see [Managing Software on Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-software.html)\.

**Note**  
You might need to refer to your HPC application’s documentation for installation instructions\.

## Step 5: Create an EFA\-enabled AMI<a name="efa-start-ami"></a>

After you have installed the required software components, you create an EFA\-AMI that you can reuse to launch your EFA\-enabled instances\.

**To create an AMI from your temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance that you created in **Step 2** and choose **Actions**, **Image**, **Create Image**\.

1. In the **Create Image** window, do the following:

   1. For **Image name**, enter a descriptive name for the AMI, such as `EFA-enabled AMI`\.

   1. \(Optional\) For **Image description**, enter a brief description of the AMI\.

   1. Choose **Create Image** and then choose **Close**\.

1. In the navigation pane, choose **AMIs**\.

1. Locate the AMI you created in the list\. Wait for the Status to transition from `pending` to `available` before continuing to the next step\.

## Step 6: Launch EFA\-enabled Instances into a Cluster Placement Group<a name="efa-start-instances"></a>

Launch your EFA\-enabled instances into a cluster placement group using the EFA\-enabled AMI that you created in **Step 5**, and the EFA\-enabled security group that you created in **Step 1**\.

**Note**  
It is not an absolute requirement to launch your EFAinstances into a cluster placement group\. However, we do recommend running your EFA\-enabled instances in a cluster placement group as it launches the instances into a low\-latency group in a single Availability Zone\.

**To launch your EFA\-enabled instances into a cluster placement group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an AMI** page, choose **My AMIs**, find the AMI that you created in **Step 5**, and then choose **Select**\.

1. On the **Choose an Instance Type** page, select one of the following supported instance types and then choose **Next: Configure Instance Details**: c5n\.18xlarge, i3en\.24xlarge, p3dn\.24xlarge\.

1. On the **Configure Instance Details** page, do the following:

   1. For **Number of instances**, enter the number of EFA\-enabled instances that you want to launch\.

   1. For **Network** and **Subnet**, select the VPC and subnet into which to launch the instances\.

   1. For **Placement group**, select **Add instance to placement group**\.

   1. For **Placement group name**, select **Add to a new placement group**, enter a descriptive name for the placement group, and then for **Placement group strategy**, select **cluster**\.

   1. For **EFA**, choose **Enable**\.

   1. In the **Network Interfaces** section, for device **eth0**, choose **New network interface**\. You can optionally specify a primary IPv4 address and one or more secondary IPv4 addresses\. If you're launching the instance into a subnet that has an associated IPv6 CIDR block, you can optionally specify a primary IPv6 address and one or more secondary IPv6 addresses\.

   1. Choose **Next: Add Storage**\.

1. On the **Add Storage** page, specify the volumes to attach to the instances in addition to the volumes specified by the AMI \(such as the root device volume\), and then choose **Next: Add Tags**\.

1. On the **Add Tags** page, specify tags for the instances, such as a user\-friendly name, and then choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, for **Assign a security group**, select **Select an existing security group**, and then select the security group that you created in **Step 1\.**

1. Choose **Review and Launch**\.

1. On the **Review Instance Launch** page, review the settings, and then choose **Launch** to choose a key pair and to launch your instances\.

## Step 7: Terminate the Temporary Instance<a name="efa-start-terminate"></a>

At this point, you no longer need the temporary instance that you launched in **Step 2**\. You can terminate the instance to stop incurring charges for it\.

**To terminate the temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the temporary instance that you created in **Step 2** and then choose **Actions**, **Instance State**, **Terminate**, **Yes, Terminate**\.