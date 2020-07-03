# Using the MATE desktop environment provided with Amazon Linux 2<a name="amazon-linux-ami-mate"></a>

The [MATE desktop environment](https://mate-desktop.org/) is pre\-installed and pre\-configured in the AMI with the following description: Amazon Linux 2 with \.Net Core, PowerShell, Mono, and MATE Desktop Environment\. The environment provides an intuitive graphical user interface for administering Amazon Linux 2 instances without using the command line\. The interface uses graphical representations, such as icons, windows, toolbars, folders, wallpapers, and desktop widgets\. Built\-in, GUI\-based tools are available to perform common tasks\. For example, there are tools for adding and removing software, applying updates, organizing files, launching programs, and monitoring system health\.

Complete the following procedure to use the MATE desktop environment\.

**To configure Remote Desktop Protocol \(RDP\) connections and set up a password**

1. Use the following [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command to get the ID of the AMI for Amazon Linux 2 that includes MATE in the AMI name\.

   ```
   aws ec2 describe-images --filters Name=name,Values=amzn2*MATE* --query Images[*].ImageId --output text
   ```

   The following is example output:

   ```
   ami-0abcdef1234567890
   ```

1. Launch an EC2 instance with the AMI that you located in the previous step\. Configure the security group to allow for inbound TCP traffic to port 3389\. For more information about configuring security groups, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)\. This configuration enables you to use an RDP client to connect to the instance\.

1. Connect to the instance using [SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)\. Run the following command to set the password for `ec2-user`\.

   ```
   [ec2-user ~]$ sudo passwd ec2-user
   ```

1. Open an RDP client on the computer from which you will connect to the instance \(for example, Remote Desktop Connection on a computer running Microsoft Windows\)\. Enter `ec2-user` as the user name and enter the password that you set in the previous step\.

**To disable the MATE Desktop Environment on your EC2 instance**  
You can turn off the GUI environment at any time by running one of the following commands\.

```
[ec2-user ~]$ sudo systemctl disable xrdp
```

```
[ec2-user ~]$ sudo systemctl stop xrdp
```

**To enable the MATE Desktop Environment on your EC2 instance**  
To turn the GUI back on, you can run one of the following commands\.

```
[ec2-user ~]$ sudo systemctl enable xrdp
```

```
[ec2-user ~]$ sudo systemctl start xrdp
```