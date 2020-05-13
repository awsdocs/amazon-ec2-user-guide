# Using the MATE desktop environment in the Amazon Linux 2 AMI with \.NET Core, Mono, and MATE<a name="amazon-linux-ami-mate"></a>

The [MATE desktop environment](https://mate-desktop.org/) is pre\-installed and pre\-configured in the Amazon Linux 2 AMI with \.NET Core and Mono\. The environment provides an intuitive graphical user interface for administering Amazon Linux 2 instances without using the command line\. The interface uses graphical representations, such as icons, windows, toolbars, folders, wallpapers, and desktop widgets\. Built\-in, GUI\-based tools are available to perform common tasks\. For example, there are tools for adding and removing software, applying updates, organizing files, launching programs, and monitoring system health\.

To use the MATE desktop environment, you must perform the following configuration steps\.

**Allow Remote Desktop Protocol \(RDP\) connections and set up a password**

1. Start an Amazon EC2 instance with the Amazon Linux AMI with \.NET Core, Mono, and MATE, and configure the security group to allow for inbound TCP traffic to port 3389\. For more information about configuring security groups, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)\. This configuration allows for the use of RDP to connect to the Amazon Linux 2 EC2 instance\.

1. Connect to the Amazon Linux 2 instance with \.NET Core, Mono, and MATE using [SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html), and run the following command to set the password for `ec2-user`\.

   ```
   sudo passwd ec2-user
   ```

1. Open an RDP client on the computer from which you will connect to the Amazon EC2 instance \(for example, Remote Desktop Connection on a Microsoft Windows client\)\. Enter `ec2-user` as the user name and enter the password that you created in the previous step\.

**Disabling the MATE Desktop Environment on your EC2 instance**  
You can turn off the GUI environment at any time by running one of the following commands\.

```
sudo systemctl disable xrdp
```

```
sudo systemctl stop xrdp
```

**Enabling the MATE Desktop Environment on your EC2 instance**  
To turn the GUI back on, you can run one of the following commands\.

```
sudo systemctl enable xrdp
```

```
sudo systemctl start xrdp
```