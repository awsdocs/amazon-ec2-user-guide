# Using an AWS Deep Learning AMI<a name="efa-start-nccl-dlami"></a>

The following steps help you to get started with one of the following AWS Deep Learning AMIs:
+ Deep Learning AMI \(Amazon Linux 2\) Version 25\.0 and later
+ Deep Learning AMI \(Amazon Linux\) Version 25\.0 and later
+ Deep Learning AMI \(Ubuntu 18\.04\) Version 25\.0 and later
+ Deep Learning AMI \(Ubuntu 16\.04\) Version 25\.0 and later

For more information, see the [ *AWS Deep Learning AMI User Guide*](https://docs.aws.amazon.com/dlami/latest/devguide/what-is-dlami.html)\.

**Topics**
+ [Step 1: Prepare an EFA\-Enabled Security Group](#nccl-start-dlami-sg)
+ [Step 2: Launch a Temporary Instance](#nccl-start-dlami-temp)
+ [Step 3: Test Your EFA and NCCL Configuration](#nccl-start-dlami-test)
+ [Step 4: Install Your Machine Learning Applications](#nccl-start-dlami-app)
+ [Step 5: Create an EFA and NCCL\-Enabled AMI](#nccl-start-dlami-ami)
+ [Step 6: Terminate the Temporary Instance](#nccl-start-dlami-terminate)
+ [Step 7: Launch EFA and NCCL\-Enabled Instances into a Cluster Placement Group](#nccl-start-dlami-cluster)
+ [Step 8: Enable Passwordless SSH](#nccl-start-dlami-passwordless)

## Step 1: Prepare an EFA\-Enabled Security Group<a name="nccl-start-dlami-sg"></a>

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

## Step 2: Launch a Temporary Instance<a name="nccl-start-dlami-temp"></a>

Launch a temporary instance that you can use to install and configure the EFA software components\. You use this instance to create an EFA\-enabled AMI from which you can launch your EFA\-enabled instances\.

**To launch a temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an AMI** page, choose a supported **AWS Deep Learning AMI Version 25\.0** or later\.

1. On the **Choose an Instance Type** page, select `p3dn.24xlarge` and then choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, do the following:

   1. For **Elastic Fabric Adapter**, choose **Enable**\.

   1. In the **Network Interfaces** section, for device **eth0**, choose **New network interface**\.

   1. Choose **Next: Add Storage**\.

1. On the **Add Storage** page, specify the volumes to attach to the instances, in addition to the volumes specified by the AMI \(such as the root device volume\)\. Then choose **Next: Add Tags**\.

1. On the **Add Tags** page, specify a tag that you can use to identify the temporary instance, and then choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, for **Assign a security group**, select **Select an existing security group**\. Then select the security group that you created in **Step 1**\.

1. On the **Review Instance Launch** page, review the settings, and then choose **Launch** to choose a key pair and to launch your instance\.

## Step 3: Test Your EFA and NCCL Configuration<a name="nccl-start-dlami-test"></a>

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
   + `FI_EFA_TX_MIN_CREDITS=64`—specifies the minimum number of send credits that the sender requests from the receiver\. `64` is the recommended value for NCCL jobs using EFA\. The value should only be increased for message transfers that are larger than 256 MB\.
   + `NCCL_DEBUG=INFO`—enables detailed debugging output\. You can also specify `VERSION` to print only the NCCL version at the start of the test, or `WARN` to receive only error messages\.
   + `NCCL_TREE_THRESHOLD=0`—disables tree algorithms for the test\.

   For more information about the NCCL test arguments, see the [NCCL Tests README](https://github.com/NVIDIA/nccl-tests/blob/master/README.md) in the official nccl\-tests repository\.

   ```
   /opt/amazon/openmpi/bin/mpirun \
   	-x FI_PROVIDER="efa" \
   	-x FI_EFA_TX_MIN_CREDITS=64 \
   	-x NCCL_DEBUG=INFO \
   	-x NCCL_TREE_THRESHOLD=0 \
   	--hostfile my-hosts -n 8 -N 8 \
   	--mca btl tcp,self --mca btl_tcp_if_exclude lo,docker0 --bind-to none \
   	$HOME/src/bin/efa-tests/efa-cuda-10.0/all_reduce_perf -b 8 -e 1G -f 2 -g 1 -c 1 -n 100
   ```

## Step 4: Install Your Machine Learning Applications<a name="nccl-start-dlami-app"></a>

Install the machine learning applications on the temporary instance\. The installation procedure varies depending on the specific machine learning application\. For more information about installing software on your Linux instance, see [Managing Software on Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-software.html)\.

**Note**  
You might need to refer to your machine learning application’s documentation for installation instructions\.

## Step 5: Create an EFA and NCCL\-Enabled AMI<a name="nccl-start-dlami-ami"></a>

After you have installed the required software components, you create an EFA and NCCL\-enabled AMI that you can reuse to launch your EFA and NCCL\-enabled instances\.

**To create an AMI from your temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance that you created in **Step 1** and choose **Actions**, **Image**, **Create Image**\.

1. In the **Create Image** window, do the following:

   1. For **Image name**, enter a descriptive name for the AMI\.

   1. \(Optional\) For **Image description**, enter a brief description of the AMI\.

   1. Choose **Create Image** and then choose **Close**\.

1. In the navigation pane, choose **AMIs**\.

1. Locate the AMI you created in the list\. Wait for the Status to transition from `pending` to `available` before continuing to the next step\.

## Step 6: Terminate the Temporary Instance<a name="nccl-start-dlami-terminate"></a>

At this point, you no longer need the temporary instance that you launched in **Step 1**\. You can terminate the instance to stop incurring charges for it\.

**To terminate the temporary instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the temporary instance that you created in **Step 1** and then choose **Actions**, **Instance State**, **Terminate**, **Yes, Terminate**\.

## Step 7: Launch EFA and NCCL\-Enabled Instances into a Cluster Placement Group<a name="nccl-start-dlami-cluster"></a>

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

## Step 8: Enable Passwordless SSH<a name="nccl-start-dlami-passwordless"></a>

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