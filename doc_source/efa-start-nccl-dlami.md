# Use an AWS Deep Learning AMI<a name="efa-start-nccl-dlami"></a>

The following steps help you to get started with one of the following AWS Deep Learning AMIs:
+ Deep Learning AMI \(Amazon Linux 2\) Version 25\.0 and later
+ Deep Learning AMI \(Amazon Linux\) Version 25\.0 and later
+ Deep Learning AMI \(Ubuntu 18\.04\) Version 25\.0 and later
+ Deep Learning AMI \(Ubuntu 16\.04\) Version 25\.0 and later

For more information, see the [https://docs.aws.amazon.com/dlami/latest/devguide/what-is-dlami.html](https://docs.aws.amazon.com/dlami/latest/devguide/what-is-dlami.html)\.

**Note**  
Only the `p3dn.24xlarge` and `p4d.24xlarge` instance types are supported\.

**Topics**
+ [Step 1: Prepare an EFA\-enabled security group](#nccl-start-dlami-sg)
+ [Step 2: Launch a temporary instance](#nccl-start-dlami-temp)
+ [Step 3: Test your EFA and NCCL configuration](#nccl-start-dlami-test)
+ [Step 4: Install your machine learning applications](#nccl-start-dlami-app)
+ [Step 5: Create an EFA and NCCL\-enabled AMI](#nccl-start-dlami-ami)
+ [Step 6: Terminate the temporary instance](#nccl-start-dlami-terminate)
+ [Step 7: Launch EFA and NCCL\-enabled instances into a cluster placement group](#nccl-start-dlami-cluster)
+ [Step 8: Enable passwordless SSH](#nccl-start-dlami-passwordless)

## Step 1: Prepare an EFA\-enabled security group<a name="nccl-start-dlami-sg"></a>

An EFA requires a security group that allows all inbound and outbound traffic to and from the security group itself\. The following procedure allows all inbound and outbound traffic for testing purposes only\. For other scenarios, see [Security group rules for different use cases](security-group-rules-reference.md)\.

**To create an EFA\-enabled security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups** and then choose **Create security group**\.

1. In the **Create security group** window, do the following:

   1. For **Security group name**, enter a descriptive name for the security group, such as `EFA-enabled security group`\.

   1. \(Optional\) For **Description**, enter a brief description of the security group\.

   1. For **VPC**, select the VPC into which you intend to launch your EFA\-enabled instances\.

   1. Choose **Create security group**\.

1. Select the security group that you created, and on the **Details** tab, copy the **Security group ID**\.

1. With the security group still selected, choose **Actions**, **Edit inbound rules**, and then do the following:

   1. Choose **Add rule**\.

   1. For **Type**, choose **All traffic**\.

   1. For **Source type**, choose **Custom** and paste the security group ID that you copied into the field\.

   1. Choose **Save rules**\.

1. With the security group still selected, choose **Actions**, **Edit outbound rules**, and then do the following:

   1. Choose **Add rule**\.

   1. For **Type**, choose **All traffic**\.

   1. For **Destination type**, choose **Custom** and paste the security group ID that you copied into the field\.

   1. Choose **Save rules**\.

## Step 2: Launch a temporary instance<a name="nccl-start-dlami-temp"></a>

Launch a temporary instance that you can use to install and configure the EFA software components\. You use this instance to create an EFA\-enabled AMI from which you can launch your EFA\-enabled instances\.

**To launch a temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an AMI** page, choose a supported **AWS Deep Learning AMI Version 25\.0** or later\.

1. On the **Choose an Instance Type** page, select `p3dn.24xlarge` or `p4d.24xlarge` and then choose **Next: Configure Instance Details**\. 

1. On the **Configure Instance Details** page, do the following:

   1. For **Subnet**, choose the subnet in which to launch the instance\. If you do not select a subnet, you can't enable the instance for EFA\.

   1. For **Elastic Fabric Adapter**, choose **Enable**\.

   1. In the **Network Interfaces** section, for device **eth0**, choose **New network interface**\.

   1. Choose **Next: Add Storage**\.

1. On the **Add Storage** page, specify the volumes to attach to the instances, in addition to the volumes specified by the AMI \(such as the root device volume\)\. Then choose **Next: Add Tags**\.

1. On the **Add Tags** page, specify a tag that you can use to identify the temporary instance, and then choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, for **Assign a security group**, select **Select an existing security group**\. Then select the security group that you created in **Step 1**\.

1. On the **Review Instance Launch** page, review the settings, and then choose **Launch** to choose a key pair and to launch your instance\.

## Step 3: Test your EFA and NCCL configuration<a name="nccl-start-dlami-test"></a>

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
   + `NCCL_PROTO=simple`—instructs NCCL to use a simple protocol for communication\. Currently, the EFA provider does not support LL protocols\. Enabling them could lead to data corruption\.

   For more information about the NCCL test arguments, see the [NCCL Tests README](https://github.com/NVIDIA/nccl-tests/blob/master/README.md) in the official nccl\-tests repository\.

   ```
   $ /opt/amazon/openmpi/bin/mpirun \
       -x FI_PROVIDER="efa" \
       -x FI_EFA_USE_DEVICE_RDMA=1 \
       -x LD_LIBRARY_PATH=/opt/nccl/build/lib:/usr/local/cuda/lib64:/opt/amazon/efa/lib64:/opt/amazon/openmpi/lib64:/usr/local/cuda/efa/lib:$LD_LIBRARY_PATH \
       -x NCCL_DEBUG=INFO \
       -x NCCL_ALGO=ring \
       -x NCCL_PROTO=simple \
       --hostfile my-hosts -n 8 -N 8 \
       --mca pml ^cm --mca btl tcp,self --mca btl_tcp_if_exclude lo,docker0 --bind-to none \
       $HOME/src/bin/efa-tests/efa-cuda-10.0/all_reduce_perf -b 8 -e 1G -f 2 -g 1 -c 1 -n 100
   ```

1. You can confirm that EFA is active as the underlying provider for NCCL when the `NCCL_DEBUG` log is printed\.

   ```
   ip-192-168-2-54:14:14 [0] NCCL INFO NET/OFI Selected Provider is efa*
   ```

   The following additional information is displayed when using a `p4d.24xlarge` instance\.

   ```
   ip-192-168-2-54:14:14 [0] NCCL INFO NET/OFI Running on P4d platform, Setting NCCL_TOPO_FILE environment variable to /home/ec2-user/install/plugin/share/aws-ofi-nccl/xml/p4d-24xl-topo.xml
   ```

## Step 4: Install your machine learning applications<a name="nccl-start-dlami-app"></a>

Install the machine learning applications on the temporary instance\. The installation procedure varies depending on the specific machine learning application\. For more information about installing software on your Linux instance, see [Managing Software on Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-software.html)\.

**Note**  
You might need to refer to your machine learning application’s documentation for installation instructions\.

## Step 5: Create an EFA and NCCL\-enabled AMI<a name="nccl-start-dlami-ami"></a>

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

## Step 6: Terminate the temporary instance<a name="nccl-start-dlami-terminate"></a>

At this point, you no longer need the temporary instance that you launched\. You can terminate the instance to stop incurring charges for it\.

**To terminate the temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the temporary instance that you created and then choose **Actions**, **Instance state**, **Terminate instance**\.

1. When prompted for confirmation, choose **Terminate**\.

## Step 7: Launch EFA and NCCL\-enabled instances into a cluster placement group<a name="nccl-start-dlami-cluster"></a>

Launch your EFA and NCCL\-enabled instances into a cluster placement group using the EFA\-enabled AMI and the EFA\-enabled security group that you created earlier\.

**Note**  
It is not an absolute requirement to launch your EFA\-enabled instances into a cluster placementgroup\. However, we do recommend running your EFA\-enabled instances in a cluster placement group as it launches the instances into a low\-latency group in a single Availability Zone\.
To ensure that capacity is available as you scale your cluster’s instances, you can create a Capacity Reservation for your cluster placement group\. For more information, see [Capacity Reservations in cluster placement groups](cr-cpg.md)\.

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

## Step 8: Enable passwordless SSH<a name="nccl-start-dlami-passwordless"></a>

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