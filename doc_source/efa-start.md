# Getting started with EFA and MPI<a name="efa-start"></a>

This tutorial helps you to launch an EFA and MPI\-enabled instance cluster for HPC workloads\. In this tutorial, you will perform the following steps:

**Topics**
+ [Step 1: Prepare an EFA\-enabled security group](#efa-start-security)
+ [Step 2: Launch a temporary instance](#efa-start-tempinstance)
+ [Step 3: Install the EFA software](#efa-start-enable)
+ [Step 4: Disable ptrace protection](#efa-start-ptrace)
+ [Step 5: \(Optional\) Install Intel MPI](#efa-start-impi)
+ [Step 6: Install your HPC application](#efa-start-hpc-app)
+ [Step 7: Create an EFA\-enabled AMI](#efa-start-ami)
+ [Step 8: Launch EFA\-enabled instances into a cluster placement group](#efa-start-instances)
+ [Step 9: Terminate the temporary instance](#efa-start-terminate)
+ [Step 10: Enable passwordless SSH](#efa-start-passwordless)

## Step 1: Prepare an EFA\-enabled security group<a name="efa-start-security"></a>

An EFA requires a security group that allows all inbound and outbound traffic to and from the security group itself\.

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

## Step 2: Launch a temporary instance<a name="efa-start-tempinstance"></a>

Launch a temporary instance that you can use to install and configure the EFA software components\. You use this instance to create an EFA\-enabled AMI from which you can launch your EFA\-enabled instances\.

**To launch a temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an AMI** page, choose **Select** for one of the following supported AMIs: Amazon Linux, Amazon Linux 2, RHEL 7\.6, RHEL 7\.7, RHEL 7\.8, CentOS 7, Ubuntu 16\.04, and Ubuntu 18\.04\.

1. On the **Choose an Instance Type** page, select one of the following supported instance types and then choose **Next: Configure Instance Details**: `c5n.18xlarge`, `c5n.metal`, `g4dn.metal`, `i3en.24xlarge`, `i3en.metal`, `inf1.24xlarge`, `m5dn.24xlarge`, `m5n.24xlarge`, `r5dn.24xlarge`, `r5n.24xlarge`, and `p3dn.24xlarge`\.

1. On the **Configure Instance Details** page, do the following:

   1. For **Elastic Fabric Adapter**, choose **Enable**\.

   1. In the **Network Interfaces** section, for device **eth0**, choose **New network interface**\.

   1. Choose **Next: Add Storage**\.

1. On the **Add Storage** page, specify the volumes to attach to the instances in addition to the volumes that are specified by the AMI \(such as the root device volume\)\. Then choose **Next: Add Tags**\.

1. On the **Add Tags** page, specify a tag that you can use to identify the temporary instance, and then choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, for **Assign a security group**, select **Select an existing security group**, and then select the security group that you created in **Step 1**\.

1. On the **Review Instance Launch** page, review the settings, and then choose **Launch** to choose a key pair and to launch your instance\.

## Step 3: Install the EFA software<a name="efa-start-enable"></a>

Install the EFA\-enabled kernel, EFA drivers, Libfabric, and Open MPI stack that is required to support EFA on your temporary instance\.

The steps differ depending on whether you intend to use EFA with Open MPI or with Intel MPI\.

**To install the EFA software**

1. Connect to the instance you launched\. For more information, see [Connect to your Linux instance](AccessingInstances.md)\.

1. To ensure that all of your software packages are up to date, perform a quick software update on your instance\. This process may take a few minutes\.
   + Amazon Linux, Amazon Linux 2, RHEL 7\.6/7\.7/7\.8, CentOS 7

     ```
     $ sudo yum update -y
     ```
   + Ubuntu 16\.04 and Ubuntu 18\.04

     ```
     $ sudo apt-get update -y
     ```

     ```
     $ sudo apt-get upgrade -y
     ```

1. Download the EFA software installation files\. To download the latest *stable* version, use the following command\.

   ```
   $ curl -O https://efa-installer.amazonaws.com/aws-efa-installer-1.9.4.tar.gz
   ```

   You can also get the latest version by replacing the version number with `latest` in the preceding command\.

1. The software installation files are packaged into a compressed `.tar.gz` file\. Extract the files from the compressed `.tar.gz` file and navigate into the extracted directory\.

   ```
   $ tar -xf aws-efa-installer-1.9.4.tar.gz
   ```

   ```
   $ cd aws-efa-installer
   ```

1. Install the EFA software\.
   + If you intend to use EFA with Open MPI, you must install the EFA software with Libfabric and Open MPI, and you must skip **Step 5: \(Optional\) Install Intel MPI**\.

     To install the EFA software with Libfabric and Open MPI, run the following command\.

     ```
     $ sudo ./efa_installer.sh -y
     ```

     Libfabric is installed in the `/opt/amazon/efa` directory, while Open MPI is installed in the `/opt/amazon/openmpi` directory\.
   + If you intend to use EFA with Intel MPI only, you can install the EFA software without Libfabric and Open MPI\. In this case, Intel MPI uses its embedded Libfabric\. If you choose to do this, you must complete **Step 5: \(Optional\) Install Intel MPI**\. 

     To install the EFA software without Libfabric and Open MPI, run the following command\.

     ```
     $ sudo ./efa_installer.sh -y --minimal
     ```

1. Log out of the instance and then log back in\.

1. Confirm that the EFA software components were successfully installed\.

   ```
   $ fi_info -p efa
   ```

   The command should return information about the Libfabric EFA interfaces\. The following example shows the command output\.

   ```
   provider: efa
       fabric: EFA-fe80::94:3dff:fe89:1b70
       domain: efa_0-rdm
       version: 2.0
       type: FI_EP_RDM
       protocol: FI_PROTO_EFA
   provider: efa
       fabric: EFA-fe80::94:3dff:fe89:1b70
       domain: efa_0-dgrm
       version: 2.0
       type: FI_EP_DGRAM
       protocol: FI_PROTO_EFA
   provider: efa;ofi_rxd
       fabric: EFA-fe80::94:3dff:fe89:1b70
       domain: efa_0-dgrm
       version: 1.0
       type: FI_EP_RDM
       protocol: FI_PROTO_RXD
   ```

## Step 4: Disable ptrace protection<a name="efa-start-ptrace"></a>

To improve your HPC application's performance, Libfabric uses the instance's local memory for interprocess communications when the processes are running on the same instance\. 

The shared memory feature uses Cross Memory Attach \(CMA\), which is not supported with *ptrace protection*\. If you are using a Linux distribution that has ptrace protection enabled by default, such as Ubuntu, you must disable it\. If your Linux distribution does not have ptrace protection enabled by default, skip this step\.

**To disable ptrace protection**  
Do one of the following:
+ To temporarily disable ptrace protection for testing purposes, run the following command\.

  ```
  $ sudo sysctl -w kernel.yama.ptrace_scope=0
  ```
+ To permanently disable ptrace protection, add `kernel.yama.ptrace_scope = 0` to `/etc/sysctl.d/10-ptrace.conf` and reboot the instance\.

## Step 5: \(Optional\) Install Intel MPI<a name="efa-start-impi"></a>

**Important**  
If you intend to use Open MPI, skip this step\. Perform this step only if you intend to use Intel MPI\.

Intel MPI requires an additional installation and environment variable configuration\.

### Prerequisites<a name="efa-start-impi-prereq"></a>

Ensure that the user performing the following steps has sudo permissions\.

**To install Intel MPI**

1. To download the Intel MPI installation files, see the [ Intel Developer Zone website](https://software.intel.com/en-us/mpi-library/choose-download/linux)\.

   You must register before you can download the installation files\. After you have registered, do the following:

   1. For **Product**, choose **Intel MPI Library for Linux**\.

   1. For **Version**, choose **2019 Update 7**, and then choose **Full Product**\.

1. The installation files are packaged into a compressed `.tar.gz` file\. Extract the files from the compressed `.tar.gz` file and navigate into the extracted directory\.

   ```
   $ tar -xf file_name.tgz
   ```

   ```
   $ cd directory_name
   ```

1. Open `silent.cfg` using your preferred text editor\. In line 10, change `ACCEPT_EULA=decline` to `ACCEPT_EULA=accept`\. Save the changes and close the file\.

1. Run the installation script\.

   ```
   $ sudo ./install.sh -s silent.cfg
   ```

   Intel MPI is installed in the `/opt/intel/impi/` directory by default\.

1. Add the Intel MPI environment variables to the corresponding shell startup scripts to ensure that they are set each time that the instance starts\. Do one of the following depending on your shell\.
   + For **bash**, add the following environment variable to `/home/username/.bashrc` and `/home/username/.bash_profile`\.

     ```
     source /opt/intel/compilers_and_libraries/linux/mpi/intel64/bin/mpivars.sh
     ```
   + For **csh and tcsh**, add the following environment variable to `/home/username/.cshrc`\.

     ```
     source /opt/intel/compilers_and_libraries/linux/mpi/intel64/bin/mpivars.csh
     ```

1. Log out of the instance and then log back in\.

1. Run the following command to confirm that Intel MPI was successfully installed\.

   ```
   $ which mpicc
   ```

   Ensure that the returned path includes the `/opt/intel/` subdirectory\.

**Note**  
If you no longer want to use Intel MPI, remove the environment variables from the shell startup scripts\.

## Step 6: Install your HPC application<a name="efa-start-hpc-app"></a>

Install the HPC application on the temporary instance\. The installation procedure varies depending on the specific HPC application\. For more information about installing software on your Linux instance, see [Managing Software on Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-software.html)\.

**Note**  
You might need to refer to your HPC application’s documentation for installation instructions\.

## Step 7: Create an EFA\-enabled AMI<a name="efa-start-ami"></a>

After you have installed the required software components, you create an AMI that you can reuse to launch your EFA\-enabled instances\.

**To create an AMI from your temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the temporary instance that you created and choose **Actions**, **Image**, **Create Image**\.

1. In the **Create Image** window, do the following:

   1. For **Image name**, enter a descriptive name for the AMI\.

   1. \(Optional\) For **Image description**, enter a brief description of the AMI\.

   1. Choose **Create Image** and then choose **Close**\.

1. In the navigation pane, choose **AMIs**\.

1. Locate the AMI you created in the list\. Wait for the Status to transition from `pending` to `available` before continuing to the next step\.

## Step 8: Launch EFA\-enabled instances into a cluster placement group<a name="efa-start-instances"></a>

Launch your EFA\-enabled instances into a cluster placement group using the EFA\-enabled AMI that you created in **Step 7**, and the EFA\-enabled security group that you created in **Step 1**\.

**Note**  
It is not an absolute requirement to launch your EFA\-enabled instances into a cluster placement group\. However, we do recommend running your EFA\-enabled instances in a cluster placement group as it launches the instances into a low\-latency group in a single Availability Zone\.

**To launch your EFA\-enabled instances into a cluster placement group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an AMI** page, choose **My AMIs**, find the AMI that you created in **Step 7**, and then choose **Select**\.

1. On the **Choose an Instance Type** page, select one of the following supported instance types and then choose **Next: Configure Instance Details**: `c5n.18xlarge`, `c5n.metal`, `g4dn.metal`, `i3en.24xlarge`, `i3en.metal`, `inf1.24xlarge`, `m5dn.24xlarge`, `m5n.24xlarge`, `r5dn.24xlarge`, `r5n.24xlarge`, and `p3dn.24xlarge`\.

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

1. On the **Configure Security Group** page, for **Assign a security group**, select **Select an existing security group**, and then select the security group that you created in **Step 1**\.

1. Choose **Review and Launch**\.

1. On the **Review Instance Launch** page, review the settings, and then choose **Launch** to choose a key pair and to launch your instances\.

## Step 9: Terminate the temporary instance<a name="efa-start-terminate"></a>

At this point, you no longer need the temporary instance that you launched\. You can terminate the instance to stop incurring charges for it\.

**To terminate the temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the temporary instance that you created and then choose **Actions**, **Instance State**, **Terminate**, **Yes, Terminate**\.

## Step 10: Enable passwordless SSH<a name="efa-start-passwordless"></a>

To enable your applications to run across all of the instances in your cluster, you must enable passwordless SSH access from the leader node to the member nodes\. The leader node is the instance from which you run your applications\. The remaining instances in the cluster are the member nodes\.

**To enable passwordless SSH between the instances in the cluster**

1. Select one instance in the cluster as the leader node, and connect to it\.

1. Disable `strictHostKeyChecking` and enable `ForwardAgent` on the leader node\. Open `~/.ssh/config` using your preferred text editor and add the following\.

   ```
   Host *
       ForwardAgent yes
   Host *
       StrictHostKeyChecking no
   ```

1. Generate an RSA key pair\.

   ```
   $ ssh-keygen -t rsa -N "" -f /home/ubuntu/.ssh/id_rsa
   ```

   The key pair is created in the `$HOME/.ssh/` directory\.

1. Change the permissions of the private key on the leader node\.

   ```
   $ chmod 600 ~/.ssh/id_rsa
   ```

1. Open `~/.ssh/id_rsa.pub` using your preferred text editor and copy the key\.

1. For each member node in the cluster, do the following:

   1. Connect to the instance\.

   1. Open `~/.ssh/authorized_keys` using your preferred text editor and add the public key that you copied earlier\.

1. To test that the passwordless SSH is functioning as expected, connect to your leader node and run the following command\.

   ```
   $ ssh member_node_private_ip
   ```

   You should connect to the member node without being prompted for a key or password\.