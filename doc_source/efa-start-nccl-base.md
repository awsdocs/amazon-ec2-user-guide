# Use a base AMI<a name="efa-start-nccl-base"></a>

The following steps help you to get started using one of the [supported base AMIs](efa.md#efa-amis)\.

**Note**  
Only the `p3dn.24xlarge` and `p4d.24xlarge` instance types are supported\.

**Topics**
+ [Step 1: Prepare an EFA\-enabled security group](#nccl-start-base-setup)
+ [Step 2: Launch a temporary instance](#nccl-start-base-temp)
+ [Step 3: Install Nvidia GPU drivers, Nvidia CUDA toolkit, and cuDNN](#nccl-start-base-drivers)
+ [Step 4: Install the EFA software](#nccl-start-base-enable)
+ [Step 5: Install NCCL](#nccl-start-base-nccl)
+ [Step 6: Install the aws\-ofi\-nccl plugin](#nccl-start-base-plugin)
+ [Step 7: Install the NCCL tests](#nccl-start-base-tests)
+ [Step 8: Test your EFA and NCCL configuration](#nccl-start-base-test)
+ [Step 9: Install your machine learning applications](#nccl-start-base-app)
+ [Step 10: Create an EFA and NCCL\-enabled AMI](#nccl-start-base-ami)
+ [Step 11: Terminate the temporary instance](#nccl-start-base-terminate)
+ [Step 12: Launch EFA and NCCL\-enabled instances into a cluster placement group](#nccl-start-base-cluster)
+ [Step 13: Enable passwordless SSH](#nccl-start-base-passwordless)

## Step 1: Prepare an EFA\-enabled security group<a name="nccl-start-base-setup"></a>

An EFA requires a security group that allows all inbound and outbound traffic to and from the security group itself\. The following procedure allows all inbound and outbound traffic for testing purposes only\. For other scenarios, see [Security group rules for different use cases](security-group-rules-reference.md)\.

**To create an EFA\-enabled security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups** and then choose **Create Security Group**\.

1. In the **Create Security Group** window, do the following:

   1. For **Security group name**, enter a descriptive name for the security group, such as `EFA-enabled security group`\.

   1. \(Optional\) For **Description**, enter a brief description of the security group\.

   1. For **VPC**, select the VPC into which you intend to launch your EFA\-enabled instances\.

   1. Choose **Create**\.

1. Select the security group that you created, and on the **Description** tab, copy the **Group ID**\.

1. On the **Inbound** tab, do the following:

   1. Choose **Edit**\.

   1. For **Type**, choose **All traffic**\.

   1. For **Source**, choose **Custom** and paste the security group ID that you copied into the field\.

   1. Choose **Save**\.

1. On the **Outbound** tab, do the following:

   1. Choose **Edit**\.

   1. For **Type**, choose **All traffic**\.

   1. For **Destination**, choose **Custom** and paste the security group ID that you copied into the field\.

   1. Choose **Save**\.

## Step 2: Launch a temporary instance<a name="nccl-start-base-temp"></a>

Launch a temporary instance that you can use to install and configure the EFA software components\. You use this instance to create an EFA\-enabled AMI from which you can launch your EFA\-enabled instances\.

**To launch a temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an AMI** page, choose one of the supported AMIs\.

1. On the **Choose an Instance Type** page, select `p3dn.24xlarge` or `p4d.24xlarge` and then choose **Next: Configure Instance Details**\. 

1. On the **Configure Instance Details** page, do the following:

   1. For **Subnet**, choose the subnet in which to launch the instance\.

   1. For **Elastic Fabric Adapter**, choose **Enable**\.

   1. In the **Network Interfaces** section, for device **eth0**, choose **New network interface**\.

   1. Choose **Next: Add Storage**\.

1. On the **Add Storage** page, specify the volumes to attach to the instances, in addition to the volumes specified by the AMI \(such as the root device volume\)\. Ensure that you provision enough storage for the Nvidia CUDA Toolkit\. Then choose **Next: Add Tags**\.

1. On the **Add Tags** page, specify a tag that you can use to identify the temporary instance, and then choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, for **Assign a security group**, select **Select an existing security group**\. Then select the security group that you created in **Step 1**\.

1. On the **Review Instance Launch** page, review the settings, and then choose **Launch** to choose a key pair and to launch your instance\.

## Step 3: Install Nvidia GPU drivers, Nvidia CUDA toolkit, and cuDNN<a name="nccl-start-base-drivers"></a>

**To install the Nvidia GPU drivers, Nvidia CUDA toolkit, and cuDNN**

1. Install the utilities that are needed to install the Nvidia GPU drivers and the Nvidia CUDA toolkit\.
   + Amazon Linux, Amazon Linux 2, RHEL, and CentOS

     ```
     $ sudo yum groupinstall 'Development Tools' -y
     ```
   + Ubuntu

     ```
     $ sudo apt-get update
     $ sudo apt-get install build-essential -y
     ```

1. To use the Nvidia GPU driver, you must first disable the `nouveau` open source drivers\.

   1. Install the **gcc** compiler and the kernel headers package for the version of the kernel that you are currently running\.
      + Amazon Linux, Amazon Linux 2, RHEL, and CentOS

        ```
        $ sudo yum install -y gcc kernel-devel-$(uname -r)
        ```
      + Ubuntu

        ```
        $ sudo apt-get install -y gcc make linux-headers-$(uname -r)
        ```

   1. Add `nouveau` to the `/etc/modprobe.d/blacklist.conf `deny list file\.

      ```
      $ cat << EOF | sudo tee --append /etc/modprobe.d/blacklist.conf
      blacklist vga16fb
      blacklist nouveau
      blacklist rivafb
      blacklist nvidiafb
      blacklist rivatv
      EOF
      ```

   1. Open `/etc/default/grub` using your preferred text editor and add the following\. 
**Note**  
Use `sudo` to open the file with root privileges\.

      ```
      GRUB_CMDLINE_LINUX="rdblacklist=nouveau"
      ```

   1. Rebuild the Grub configuration\.
      + Amazon Linux, Amazon Linux 2, RHEL, and CentOS

        ```
        $ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
        ```
      + Ubuntu

        ```
        $ sudo update-grub
        ```

1. Reboot the instance and reconnect to it\.

1. Download and add the Nvidia Machine Learning repositories\.
   + Amazon Linux, Amazon Linux 2, RHEL, and CentOS 

     ```
     $ wget -O /tmp/ml-repo.rpm https://developer.download.nvidia.com/compute/machine-learning/repos/rhel7/x86_64/nvidia-machine-learning-repo-rhel7-1.0.0-1.x86_64.rpm
     $ sudo rpm -Uhv /tmp/ml-repo.rpm
     $ sudo yum-config-manager --add-repo https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/cuda-rhel7.repo
     $ sudo yum clean all
     $ sudo yum upgrade -y
     ```
   + Ubuntu 16\.04

     ```
     $ wget -O /tmp/deeplearning.deb http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb
     $ sudo dpkg -i /tmp/deeplearning.deb
     $ wget -O /tmp/cuda.pin https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-ubuntu1604.pin
     $ sudo mv /tmp/cuda.pin /etc/apt/preferences.d/cuda-repository-pin-600
     $ sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
     $ sudo add-apt-repository 'deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/ /'
     $ sudo apt update
     ```
   + Ubuntu 18\.04

     ```
     $ sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/7fa2af80.pub
     $ wget -O /tmp/deeplearning.deb http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
     $ sudo dpkg -i /tmp/deeplearning.deb
     $ wget -O /tmp/cuda.pin https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
     $ sudo mv /tmp/cuda.pin /etc/apt/preferences.d/cuda-repository-pin-600
     $ sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
     $ sudo add-apt-repository 'deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /'
     $ sudo apt update
     ```

1. Install the Nvidia GPU drivers, NVIDIA CUDA toolkit, and cuDNN
   + Amazon Linux, Amazon Linux 2, RHEL, and CentOS 

     ```
     $ sudo yum -y install cuda-toolkit-11-0 libcudnn8 libcudnn8-devel nvidia-driver-branch-450
     ```
   + Ubuntu

     ```
     $ sudo apt install -o Dpkg::Options::='--force-overwrite' cuda-drivers-450 cuda-toolkit-11-0 libcudnn8 libcudnn8-dev -y
     ```

1. Reboot the instance and reconnect to it\.

1. \(`p4d.24xlarge` instances only\) Install the Nvidia Fabric Manager, start the service, and ensure that it starts automatically when the instance starts\. Nvidia Fabric Manager is required for NV Switch Management\.
   + Amazon Linux, Amazon Linux 2, RHEL, and CentOS 

     ```
     $ sudo yum -y install nvidia-fabricmanager-450
     $ sudo systemctl start nvidia-fabricmanager
     $ sudo systemctl enable nvidia-fabricmanager
     ```
   + Ubuntu

     ```
     $ sudo apt install -o Dpkg::Options::='--force-overwrite' nvidia-fabricmanager-450
     $ sudo systemctl start nvidia-fabricmanager
     $ sudo systemctl enable nvidia-fabricmanager
     ```

1. Ensure that the CUDA paths are set each time that the instance starts\.
   + For *bash* shells, add the following statements to `/home/username/.bashrc` and `/home/username/.bash_profile`\. 

     ```
     export PATH=/usr/local/cuda/bin:/usr/local/cuda/NsightCompute-2019.1:$PATH
     export LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64:$LD_LIBRARY_PATH
     ```
   + For *tcsh* shells, add the following statements to `/home/username/.cshrc`\.

     ```
     setenv PATH=/usr/local/cuda/bin:/usr/local/cuda/NsightCompute-2019.1:$PATH
     setenv LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64:$LD_LIBRARY_PATH
     ```

1. To confirm that the Nvidia GPU drivers are functional, run the following command\.

   ```
   $ nvidia-smi -q | head
   ```

   The command should return information about the Nvidia GPUs, Nvidia GPU drivers, and Nvidia CUDA toolkit\.

## Step 4: Install the EFA software<a name="nccl-start-base-enable"></a>

Install the EFA\-enabled kernel, EFA drivers, Libfabric, and Open MPI stack that is required to support EFA on your temporary instance\.

**To install the EFA software**

1. Connect to the instance you launched\. For more information, see [Connect to your Linux instance](AccessingInstances.md)\.

1. To ensure that all of your software packages are up to date, perform a quick software update on your instance\. This process may take a few minutes\.
   + Amazon Linux, Amazon Linux 2, RHEL, and CentOS

     ```
     $ sudo yum update -y --skip-broken
     ```
   + Ubuntu

     ```
     $ sudo apt-get update
     ```

     ```
     $ sudo apt-get upgrade -y
     ```
   + SUSE Linux Enterprise

     ```
     $ sudo zypper update -y
     ```

1. Download the EFA software installation files\. The software installation files are packaged into a compressed tarball \(`.tar.gz`\) file\. To download the latest *stable* version, use the following command\.

   ```
   $ curl -O https://efa-installer.amazonaws.com/aws-efa-installer-1.11.2.tar.gz
   ```

   You can also get the latest version by replacing the version number with `latest` in the preceding command\.

1. \(Optional\) Verify the authenticity and integrity of the EFA tarball \(`.tar.gz`\) file\. We recommend that you do this to verify the identity of the software publisher and to check that the file has not been altered or corrupted since it was published\. If you do not want to verify the tarball file, skip this step\.
**Note**  
Alternatively, if you prefer to verify the tarball file by using an MD5 or SHA256 checksum instead, see [Verify the EFA installer using a checksum](efa-verify.md)\.

   1. Download the public GPG key and import it into your keyring\.

      ```
      $ wget https://efa-installer.amazonaws.com/aws-efa-installer.key
      ```

      ```
      $ gpg --import aws-efa-installer.key
      ```

      The command should return a key value\. Make a note of the key value, because you need it in the next step\.

   1. Verify the GPG key's fingerprint\. Run the following command and specify the key value from the previous step\.

      ```
      $ gpg --fingerprint key_value
      ```

      The command should return a fingerprint that is identical to `4E90 91BC BB97 A96B 26B1 5E59 A054 80B1 DD2D 3CCC`\. If the fingerprint does not match, don't run the EFA installation script, and contact AWS Support\.

   1. Download the signature file and verify the signature of the EFA tarball file\.

      ```
      $ wget https://efa-installer.amazonaws.com/aws-efa-installer-1.11.2.tar.gz.sig
      ```

      ```
      $ gpg --verify ./aws-efa-installer-1.11.2.tar.gz.sig
      ```

      The following shows example output\.

      ```
      gpg: Signature made Wed 29 Jul 2020 12:50:13 AM UTC using RSA key ID DD2D3CCC
      gpg: Good signature from "Amazon EC2 EFA <ec2-efa-maintainers@amazon.com>"
      gpg: WARNING: This key is not certified with a trusted signature!
      gpg:          There is no indication that the signature belongs to the owner.
      Primary key fingerprint: 4E90 91BC BB97 A96B 26B1  5E59 A054 80B1 DD2D 3CCC
      ```

      If the result includes `Good signature`, and the fingerprint matches the fingerprint returned in the previous step, proceed to the next step\. If not, don't run the EFA installation script, and contact AWS Support\.

1. Extract the files from the compressed `.tar.gz` file and navigate into the extracted directory\.

   ```
   $ tar -xf aws-efa-installer-1.11.2.tar.gz
   ```

   ```
   $ cd aws-efa-installer
   ```

1. Run the EFA software installation script\.

   ```
   $ sudo ./efa_installer.sh -y -g
   ```

   Libfabric is installed in the `/opt/amazon/efa` directory, while Open MPI is installed in the `/opt/amazon/openmpi` directory\.

1. Reboot the instance and then log back in\.

1. Confirm that the EFA software components were successfully installed\.

   ```
   $ fi_info -p efa -t FI_EP_RDM
   ```

   The command should return information about the Libfabric EFA interfaces\. The following example shows the command output\.
   + `p3dn.24xlarge` with single network interface

     ```
     provider: efa
     fabric: EFA-fe80::94:3dff:fe89:1b70
     domain: efa_0-rdm
     version: 2.0
     type: FI_EP_RDM
     protocol: FI_PROTO_EFA
     ```
   + `p4d.24xlarge` with multiple network interfaces

     ```
     provider: efa
     fabric: EFA-fe80::c6e:8fff:fef6:e7ff
     domain: efa_0-rdm
     version: 111.0
     type: FI_EP_RDM
     protocol: FI_PROTO_EFA
     provider: efa
     fabric: EFA-fe80::c34:3eff:feb2:3c35
     domain: efa_1-rdm
     version: 111.0
     type: FI_EP_RDM
     protocol: FI_PROTO_EFA
     provider: efa
     fabric: EFA-fe80::c0f:7bff:fe68:a775
     domain: efa_2-rdm
     version: 111.0
     type: FI_EP_RDM
     protocol: FI_PROTO_EFA
     provider: efa
     fabric: EFA-fe80::ca7:b0ff:fea6:5e99
     domain: efa_3-rdm
     version: 111.0
     type: FI_EP_RDM
     protocol: FI_PROTO_EFA
     ```

## Step 5: Install NCCL<a name="nccl-start-base-nccl"></a>

Install NCCL\. For more information about NCCL, see the [NCCL repository](https://github.com/NVIDIA/nccl)\.

**To install NCCL**

1. Navigate to the `/opt` directory\.

   ```
   $ cd /opt
   ```

1. Clone the official NCCL repository to the instance and navigate into the local cloned repository\.

   ```
   $ sudo git clone https://github.com/NVIDIA/nccl.git && cd nccl
   ```

1. Build and install NCCL and specify the CUDA installation directory\.

   ```
   $ sudo make -j src.build CUDA_HOME=/usr/local/cuda
   ```

## Step 6: Install the aws\-ofi\-nccl plugin<a name="nccl-start-base-plugin"></a>

The aws\-ofi\-nccl plugin maps NCCL's connection\-oriented transport APIs to Libfabric's connection\-less reliable interface\. This enables you to use Libfabric as a network provider while running NCCL\-based applications\. For more information about the aws\-ofi\-nccl plugin, see the [aws\-ofi\-nccl repository](https://github.com/aws/aws-ofi-nccl)\.

**To install the aws\-ofi\-nccl plugin**

1. Navigate to your home directory\.

   ```
   $ cd $HOME
   ```

1. \(Ubuntu only\) Install the utilities that are required to install the **aws\-ofi\-nccl** plugin\. To install the required utilities, run the following command\.

   ```
   $ sudo apt-get install libtool autoconf -y
   ```

1. Clone the `aws` branch of the official AWS aws\-ofi\-nccl repository to the instance and navigate into the local cloned repository\.

   ```
   $ git clone https://github.com/aws/aws-ofi-nccl.git -b aws && cd aws-ofi-nccl
   ```

1. To generate the `configure` script, run the `autogen.sh` script\.

   ```
   $ ./autogen.sh
   ```

1. To generate the *make* files, run the `configure` script and specify the MPI, Libfabric, NCCL, and CUDA installation directories\. 

   ```
   $ ./configure --prefix=/opt/aws-ofi-nccl --with-mpi=/opt/amazon/openmpi \
   --with-libfabric=/opt/amazon/efa --with-nccl=/opt/nccl/build \
   --with-cuda=/usr/local/cuda
   ```

1. Add the Open MPI directory to the `PATH` variable\.

   ```
   $ export PATH=/opt/amazon/openmpi/bin/:$PATH
   ```

1. Install the aws\-ofi\-nccl plugin\.

   ```
   $ make 
   ```

   ```
   $ sudo make install
   ```

## Step 7: Install the NCCL tests<a name="nccl-start-base-tests"></a>

Install the NCCL tests\. The NCCL tests enable you to confirm that NCCL is properly installed and that it is operating as expected\. For more information about the NCCL tests, see the [nccl\-tests repository](https://github.com/NVIDIA/nccl-tests)\.

**To install the NCCL tests**

1. Navigate to your home directory\.

   ```
   $ cd $HOME
   ```

1. Clone the official nccl\-tests repository to the instance and navigate into the local cloned repository\.

   ```
   $ git clone https://github.com/NVIDIA/nccl-tests.git
   ```

   ```
   $ cd nccl-tests
   ```

1. Add the Libfabric directory to the `LD_LIBRARY_PATH` variable\. 
   + Amazon Linux, Amazon Linux 2, RHEL , and CentOS

     ```
     $ export LD_LIBRARY_PATH=/opt/amazon/efa/lib64:$LD_LIBRARY_PATH
     ```
   + Ubuntu 

     ```
     $ export LD_LIBRARY_PATH=/opt/amazon/efa/lib:$LD_LIBRARY_PATH
     ```

1. Install the NCCL tests and specify the MPI, NCCL, and CUDA installation directories\.

   ```
   $ make MPI=1 MPI_HOME=/opt/amazon/openmpi NCCL_HOME=/opt/nccl/build CUDA_HOME=/usr/local/cuda
   ```

## Step 8: Test your EFA and NCCL configuration<a name="nccl-start-base-test"></a>

Run a test to ensure that your temporary instance is properly configured for EFA and NCCL\. 

**To test your EFA and NCCL configuration**

1. Create a host file that specifies the hosts on which to run the tests\. The following command creates a host file named `my-hosts` that includes a reference to the instance itself\.

------
#### [ IMDSv2 ]

   ```
   [ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
   && curl -H "X-aws-ec2-metadata-token: $TOKEN" –v http://169.254.169.254/latest/meta-data/local-ipv4 >> my-hosts
   ```

------
#### [ IMDSv1 ]

   ```
   [ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/local-ipv4 >> my-hosts
   ```

------

1. Run the test and specify the host file \(`--hostfile`\) and the number of GPUs to use \(`-n`\)\. The following command runs the `all_reduce_perf` test on 8 GPUs on the instance itself, and specifies the following environment variables\.
   + `FI_PROVIDER="efa"`—specifies the fabric interface provider\. This must be set to `"efa"`\.
   + `FI_EFA_USE_DEVICE_RDMA=1`—uses the device's RDMA functionality for one\-sided and two\-sided transfer\.
   + `NCCL_DEBUG=INFO`—enables detailed debugging output\. You can also specify `VERSION` to print only the NCCL version at the start of the test, or `WARN` to receive only error messages\.
   + `NCCL_ALGO=ring`—enables ring algorithm for collective operations\.

   For more information about the NCCL test arguments, see the [NCCL Tests README](https://github.com/NVIDIA/nccl-tests/blob/master/README.md) in the official nccl\-tests repository\.

   ```
   $ /opt/amazon/openmpi/bin/mpirun \
       -x FI_PROVIDER="efa" \
       -x FI_EFA_USE_DEVICE_RDMA=1 \
       -x RDMAV_FORK_SAFE=1 \
       -x LD_LIBRARY_PATH=/opt/nccl/build/lib:/usr/local/cuda/lib64:/opt/amazon/efa/lib64:/opt/amazon/openmpi/lib64:/opt/aws-ofi-nccl/lib:$LD_LIBRARY_PATH \
       -x NCCL_DEBUG=INFO \
       -x NCCL_ALGO=ring \
       --hostfile my-hosts -n 8 -N 8 \
       --mca pml ^cm --mca btl tcp,self --mca btl_tcp_if_exclude lo,docker0 --bind-to none \
       $HOME/nccl-tests/build/all_reduce_perf -b 8 -e 1G -f 2 -g 1 -c 1 -n 100
   ```

1. You can confirm that EFA is active as the underlying provider for NCCL when the `NCCL_DEBUG` log is printed\.

   ```
   ip-192-168-2-54:14:14 [0] NCCL INFO NET/OFI Selected Provider is efa*
   ```

   The following additional information is displayed when using a `p4d.24xlarge` instance\.

   ```
   ip-192-168-2-54:14:14 [0] NCCL INFO NET/OFI Running on P4d platform, Setting NCCL_TOPO_FILE environment variable to /home/ec2-user/install/plugin/share/aws-ofi-nccl/xml/p4d-24xl-topo.xml
   ```

## Step 9: Install your machine learning applications<a name="nccl-start-base-app"></a>

Install the machine learning applications on the temporary instance\. The installation procedure varies depending on the specific machine learning application\. For more information about installing software on your Linux instance, see [Managing Software on Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-software.html)\.

**Note**  
You might need to refer to your machine learning application’s documentation for installation instructions\.

## Step 10: Create an EFA and NCCL\-enabled AMI<a name="nccl-start-base-ami"></a>

After you have installed the required software components, you create an AMI that you can reuse to launch your EFA\-enabled instances\.

**To create an AMI from your temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the temporary instance that you created and choose **Actions**, **Image**, **Create image**\.

1. For **Create image**, do the following:

   1. For **Image name**, enter a descriptive name for the AMI\.

   1. \(Optional\) For **Image description**, enter a brief description of the purpose of the AMI\.

   1. Choose **Create image**\.

1. In the navigation pane, choose **AMIs**\.

1. Locate the AMI tht you created in the list\. Wait for the status to change from `pending` to `available` before continuing to the next step\.

## Step 11: Terminate the temporary instance<a name="nccl-start-base-terminate"></a>

At this point, you no longer need the temporary instance that you launched\. You can terminate the instance to stop incurring charges for it\.

**To terminate the temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the temporary instance that you created and then choose **Actions**, **Instance state**, **Terminate instance**\.

1. When prompted for confirmation, choose **Terminate**\.

## Step 12: Launch EFA and NCCL\-enabled instances into a cluster placement group<a name="nccl-start-base-cluster"></a>

Launch your EFA and NCCL\-enabled instances into a cluster placement group using the EFA\-enabled AMI and the EFA\-enabled security group that you created earlier\.

**To launch your EFA and NCCL\-enabled instances into a cluster placement group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an AMI** page, choose **My AMIs**, find the AMI that you created earlier, and then choose **Select**\.

1. On the **Choose an Instance Type** page, select **p3dn\.24xlarge** and then choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, do the following:

   1. For **Number of instances**, enter the number of EFA and NCCL\-enabled instances that you want to launch\.

   1. For **Network** and **Subnet**, select the VPC and subnet into which to launch the instances\.

   1. For **Placement group**, select **Add instance to placement group**\.

   1. For **Placement group name**, select **Add to a new placement group**, and then enter a descriptive name for the placement group\. Then for **Placement group strategy**, select **cluster**\.

   1. For **EFA**, choose **Enable**\.

   1. In the **Network Interfaces** section, for device **eth0**, choose **New network interface**\. You can optionally specify a primary IPv4 address and one or more secondary IPv4 addresses\. If you are launching the instance into a subnet that has an associated IPv6 CIDR block, you can optionally specify a primary IPv6 address and one or more secondary IPv6 addresses\.

   1. Choose **Next: Add Storage**\.

1. On the **Add Storage** page, specify the volumes to attach to the instances in addition to the volumes specified by the AMI \(such as the root device volume\)\. Then choose **Next: Add Tags**\.

1. On the **Add Tags** page, specify tags for the instances, such as a user\-friendly name, and then choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, for **Assign a security group**, select **Select an existing security group**, and then select the security group that you created earlier\.

1. Choose **Review and Launch**\.

1. On the **Review Instance Launch** page, review the settings, and then choose **Launch** to choose a key pair and to launch your instances\.

## Step 13: Enable passwordless SSH<a name="nccl-start-base-passwordless"></a>

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
   $ ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa
   ```

   The key pair is created in the `$HOME/.ssh/` directory\.

1. Change the permissions of the private key on the leader node\.

   ```
   $ chmod 600 ~/.ssh/id_rsa
   chmod 600 ~/.ssh/config
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