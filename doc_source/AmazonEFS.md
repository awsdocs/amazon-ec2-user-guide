# Using Amazon EFS with Amazon EC2<a name="AmazonEFS"></a>

Amazon EFS provides scalable file storage for use with Amazon EC2\. You can create an EFS file system and configure your instances to mount the file system\. You can use an EFS file system as a common data source for workloads and applications running on multiple instances\. For more information, see the [Amazon Elastic File System product page](https://aws.amazon.com/efs)\.

In this tutorial, you create an EFS file system and two Linux instances that can share data using the file system\.

**Important**  
Amazon EFS is not supported on Windows instances\.

**Topics**
+ [Prerequisites](#efs-prerequisites)
+ [Step 1: Create an EFS file system](#efs-create-file-system)
+ [Step 2: Mount the file system](#efs-mount-file-system)
+ [Step 3: Test the file system](#efs-test-file-system)
+ [Step 4: Clean up](#efs-clean-up)

## Prerequisites<a name="efs-prerequisites"></a>
+ Create a security group \(for example, efs\-sg\) to associate with the EC2 instances and EFS mount target, and add the following rules:
  + Allow inbound SSH connections to the EC2 instances from your computer \(the source is the CIDR block for your network\)\.
  + Allow inbound NFS connections to the file system via the EFS mount target from the EC2 instances that are associated with this security group \(the source is the security group itself\)\. For more information, see [Amazon EFS rules](security-group-rules-reference.md#sg-rules-efs), and [Creating security Groups](https://docs.aws.amazon.com/efs/latest/ug/accessing-fs-create-security-groups.html) in the *Amazon Elastic File System User Guide*\.
+ Create a key pair\. You must specify a key pair when you configure your instances or you can't connect to them\. For more information, see [Create a key pair](get-set-up-for-amazon-ec2.md#create-a-key-pair)\.

## Step 1: Create an EFS file system<a name="efs-create-file-system"></a>

Amazon EFS enables you to create a file system that multiple instances can mount and access at the same time\. For more information, see [Creating Resources for Amazon EFS](https://docs.aws.amazon.com/efs/latest/ug/creating-using.html) in the *Amazon Elastic File System User Guide*\.

**To create a file system**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create file system**\.

1. \(Optional\) For **Name**, enter a name for the file system\. This creates a tag with **Name** as the key and the name of the file system as the value\.

1. For **Virtual Private Cloud \(VPC\)**, select the VPC to use for your instances\.

1. Choose **Create**\.

1. After the file system is created, note the file system ID\. It is used later in this tutorial\.

1. Choose the file system ID\.

1. On the file systems page, choose **Network**, **Manage**\. View the mount targets that Amazon EFS creates in each Availability Zone in the Region in which your VPC resides\. For each Availability Zone for your instances, ensure that the value for **Security groups** is the security group that you created in [Prerequisites](#efs-prerequisites)\.

1. Choose **Save**\.

## Step 2: Mount the file system<a name="efs-mount-file-system"></a>

Use the following procedure to launch two `t2.micro` instances\. Note that T2 instances must be launched in a subnet\. You can use a default VPC or a nondefault VPC\.

**Note**  
There are other ways that you can mount the volume \(for example, on an already running instance\)\. For more information, see [Mounting File Systems](https://docs.aws.amazon.com/efs/latest/ug/mounting-fs.html) in the *Amazon Elastic File System User Guide*\.

**To launch two instances and mount an EFS file system**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. For **Step 1: Choose an Amazon Machine Image \(AMI\)**, select an Amazon Linux AMI\.

1. For **Step 2: Choose an Instance Type**, keep the default instance type, `t2.micro`, and choose **Next: Configure Instance Details**\.

1. For **Step 3: Configure Instance Details**, do the following:

   1. For **Number of instances**, enter 2\.

   1. \[Default VPC\] If you have a default VPC, it is the default value for **Network**\. Keep the default VPC and the default value for **Subnet** to use the default subnet in the Availability Zone that Amazon EC2 chooses for your instances\.

      \[Nondefault VPC\] Select your VPC for **Network**, and a public subnet from **Subnet**\.

   1. \[Nondefault VPC\] For **Auto\-assign Public IP**, choose **Enable**\. Otherwise, your instances do not get public IP addresses or public DNS names\.

   1. For **File systems**, choose **Add file system**\. Ensure that the value matches the file system ID that you created in [Step 1: Create an EFS file system](#efs-create-file-system)\. The path shown next to the file system ID is the mount point that the instance will use, which you can change\. Under **Advanced Details**, the **User data** is automatically generated, and includes the commands needed to mount the file system\.

   1. Advance to Step 6 of the wizard\.

1. On the **Configure Security Group** page, choose **Select an existing security group** and select the security group that you created in [Prerequisites](#efs-prerequisites)\. Then choose **Review and Launch**\.

1. On the **Review Instance Launch** page, choose **Launch**\.

1. In the **Select an existing key pair or create a new key pair** dialog box, select **Choose an existing key pair** and choose your key pair\. Select the acknowledgment check box, and choose **Launch Instances**\.

1. In the navigation pane, choose **Instances** to see the status of your instances\. Initially, their status is `pending`\. After the status changes to `running`, your instances are ready for use\.

Your instance is now configured to mount the Amazon EFS file system at launch and whenever it's rebooted\. 

## Step 3: Test the file system<a name="efs-test-file-system"></a>

You can connect to your instances and verify that the file system is mounted to the directory that you specified \(for example, /mnt/efs\)\.

**To verify that the file system is mounted**

1. Connect to your instances\. For more information, see [Connect to your Linux instance](AccessingInstances.md)\.

1. From the terminal window for each instance, run the df \-T command to verify that the EFS file system is mounted\.

   ```
   $ df -T
   Filesystem     Type              1K-blocks    Used          Available Use% Mounted on
   /dev/xvda1     ext4                8123812 1949800            6073764  25% /
   devtmpfs       devtmpfs            4078468      56            4078412   1% /dev
   tmpfs          tmpfs               4089312       0            4089312   0% /dev/shm
   efs-dns        nfs4       9007199254740992       0   9007199254740992   0% /mnt/efs
   ```

   Note that the name of the file system, shown in the example output as *efs\-dns*, has the following form\.

   ```
   file-system-id.efs.aws-region.amazonaws.com:/
   ```

1. \(Optional\) Create a file in the file system from one instance, and then verify that you can view the file from the other instance\.

   1. From the first instance, run the following command to create the file\.

      ```
      $ sudo touch /mnt/efs/test-file.txt
      ```

   1. From the second instance, run the following command to view the file\.

      ```
      $ ls /mnt/efs
      test-file.txt
      ```

## Step 4: Clean up<a name="efs-clean-up"></a>

When you are finished with this tutorial, you can terminate the instances and delete the file system\.

**To terminate the instances**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instances to terminate\.

1. Choose **Actions**, **Instance State**, **Terminate**\.

1. Choose **Yes, Terminate** when prompted for confirmation\.

**To delete the file system**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Select the file system to delete\.

1. Choose **Actions**, **Delete file system**\.

1. When prompted for confirmation, enter the file system ID and choose **Delete file system**\.