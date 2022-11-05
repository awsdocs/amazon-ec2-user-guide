# Task 1: Launch an EC2 instance – *optional*<a name="option2-task1-launch-ec2-instance"></a>

**Note**  
Launching an instance is not the focus of this tutorial\. If you already have an Amazon EC2 instance and would like to use it in this tutorial, you can skip this task\.

## Task objective<a name="option2-task1-launch-ec2-instance-task-objective"></a>

The objective of this task is to launch an EC2 instance so that you can complete Task 2 where you configure the connection between your EC2 instance and your Amazon RDS database\. If you have an EC2 instance that you can use, you can skip this task\.

## Steps to launch an EC2 instance<a name="option2-task1-launch-ec2-instance-steps"></a>

Use the following steps to launch an EC2 instance for this tutorial\.

To view an animation of these steps, see [View an animation: Launch an EC2 instance](#option2-launch-ec2-instance-animation)\.

**EC2 instance configuration**

The steps in this task configure the EC2 instance as follows:
+ Instance name: **tutorial\-instance\-2**
+ AMI: Amazon Linux 2
+ Instance type: `t2.micro`
+ Auto\-assign public IP: Enabled 
+ Security group with the following three rules:
  + Allow SSH from your IP address
  + Allow HTTPS traffic from anywhere
  + Allow HTTP traffic from anywhere

**Important**  
In a production environment, you should configure your instance to meet your specific needs\.

**To launch an EC2 instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **EC2 Dashboard**, choose **Launch instance**\.

1. Under **Name and tags**, for **Name**, enter a name to identify your instance\. For this tutorial, name the instance **tutorial\-instance\-2**\. While the instance name is not mandatory, when you select your instance in the RDS console, the name will help you easily identify it\.

1. Under **Application and OS Images**, choose an AMI that meets your web server needs\. This tutorial uses **Amazon Linux**\.

1. Under **Instance type**, for **Instance type**, select an instance type that meets your web server needs\. This tutorial uses `t2.micro`\.
**Note**  
You can use Amazon EC2 under the [Free tier](http://aws.amazon.com/free) provided your AWS account is less than 12 months old and you choose a `t2.micro` instance type \(or `t3.micro` in Regions where `t2.micro` is not available\)\.

1. Under **Key pair \(login\)**, for **Key pair name**, choose your key pair\.

1. Under **Network settings**, do the following:

   1. For **Network** and **Subnet**, if you haven’t made changes to your default VPC or subnets, you can keep the default settings\. 

      If you have made changes to your default VPC or subnets, check the following:

      1. The instance must be in the same VPC as the RDS database to use the automatic connection configuration\. By default you have only one VPC\.

      1. The VPC that you’re launching your instance into must have an internet gateway attached to it so that you can access your web server from the internet\. Your default VPC is automatically set up with an internet gateway\.

      1. To ensure that your instance receives a public IP address, for **Auto\-assign public IP**, check that **Enable** is selected\. If **Disable **is selected, choose **Edit** \(to the right of **Network Settings**\), and then, for **Auto\-assign public IP**, choose **Enable**\.

   1. To connect to your instance by using SSH, you need a security group rule that authorizes SSH \(Linux\) or RDP \(Windows\) traffic from your computer’s public IPv4 address\. By default, when you launch an instance, a new security group is created with a rule that allows inbound SSH traffic from anywhere\.

      To make sure that only your IP address can connect to your instance, under **Firewall \(security groups\)**, from the drop\-down list next to the **Allow SSH traffic from** check box, choose **My IP**\.

   1. To allow traffic from the internet to your instance, select the following check boxes:
      + **Allow HTTPs traffic from the internet**
      + **Allow HTTP traffic from the internet**

1. In the **Summary** panel, review your instance configuration and then choose **Launch instance**\.

1. Choose **View all instances** to close the confirmation page and return to the console\. Your instance will first be in a `pending` state, and will then go into the `running` state\. 

   If the instance fails to launch or the state immediately goes to `terminated` instead of `running`, see [Troubleshoot instance launch issues](troubleshooting-launch.md)\.

For more information about launching an instance, see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md)\.

### View an animation: Launch an EC2 instance<a name="option2-launch-ec2-instance-animation"></a>

![\[This animation shows how to launch an EC2 instance. For the text version of this animation, see the steps in the preceding procedure.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/tutorial-launch-instance.gif)

You're now ready for [Task 2: Create an RDS database and automatically connect it to your EC2 instance](option2-task2-create-rds-database.md)\.