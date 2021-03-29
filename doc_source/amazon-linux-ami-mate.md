# Configure the Amazon Linux 2 MATE desktop connection<a name="amazon-linux-ami-mate"></a>

The [MATE desktop environment](https://mate-desktop.org/) is pre\-installed and pre\-configured in the AMI with the following description: Amazon Linux 2 with \.Net Core, Mono, and MATE Desktop Environment\. The environment provides an intuitive graphical user interface for administering Amazon Linux 2 instances with minimal use of the command line\. The interface uses graphical representations, such as icons, windows, toolbars, folders, wallpapers, and desktop widgets\. Built\-in, GUI\-based tools are available to perform common tasks\. For example, there are tools for adding and emoving software, applying updates, rorganizing files, launching programs, and monitoring system health\.

## Prerequisite<a name="al2-mate-configure-prerequisite"></a>

To run the commands shown in this topic, you must install the AWS Command Line Interface \(AWS CLI\) or AWS Tools for Windows PowerShell, and configure your AWS profile\.

**Options**

1. Install the AWS CLI — For more information, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) and [Configuration basics](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) in the *AWS Command Line Interface User Guide*\.

1. Install the Tools for Windows PowerShell — For more information, see [Installing the AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-getting-set-up.html) and [Shared credentials](https://docs.aws.amazon.com/powershell/latest/userguide/shared-credentials-in-aws-powershell.html) in the *AWS Tools for Windows PowerShell User Guide*\.

## Configure the RDP connection<a name="al2-mate-configure-connection"></a>

Follow these steps to set up a Remote Desktop Protocol \(RDP\) connection from your local machine to an Amazon Linux 2 instance running the MATE desktop environment\.

1. Use the [describe\-images](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-images.html) command from your local command line tool to get the ID of the AMI for Amazon Linux 2 that includes MATE in the AMI name\.

   ```
   aws ec2 describe-images --filters "Name=name,Values=amzn2*MATE*" --query "Images[*].[ImageId,Name,Description]"
   [
       [
           "ami-0123example0abc12",
           "amzn2-x86_64-MATEDE_DOTNET-2020.12.04",
           ".NET Core 5.0, Mono 6.12, PowerShell 7.1, and MATE DE pre-installed to run your .NET applications on Amazon Linux 2 with Long Term Support (LTS)."
       ],
       [
           "ami-0456example0def34",
           "amzn2-x86_64-MATEDE_DOTNET-2020.04.14",
           "Amazon Linux 2 with .Net Core, PowerShell, Mono, and MATE Desktop Environment"
       ]
   ]
   ```

   Choose the AMI that is appropriate for your use\.

1. Launch an EC2 instance with the AMI that you located in the previous step\. Configure the security group to allow for inbound TCP traffic to port 3389\. For more information about configuring security groups, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)\. This configuration enables you to use an RDP client to connect to the instance\.

1. Connect to the instance using [SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)\. Run the following command on your Linux instance to set the password for `ec2-user`\.

   ```
   [ec2-user ~]$ sudo passwd ec2-user
   ```

1. Open an RDP client on the computer from which you will connect to the instance \(for example, Remote Desktop Connection on a computer running Microsoft Windows\)\. Enter `ec2-user` as the user name and enter the password that you set in the previous step\.

**To disable the MATE Desktop Environment on your Amazon EC2 instance**  
You can turn off the GUI environment at any time by running one of the following commands on your Linux instance\.

```
[ec2-user ~]$ sudo systemctl disable xrdp
```

```
[ec2-user ~]$ sudo systemctl stop xrdp
```

**To enable the MATE Desktop Environment on your Amazon EC2 instance**  
To turn the GUI back on, you can run one of the following commands on your Linux instance\.

```
[ec2-user ~]$ sudo systemctl enable xrdp
```

```
[ec2-user ~]$ sudo systemctl start xrdp
```