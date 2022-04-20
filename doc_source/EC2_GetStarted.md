# Tutorial: Get started with Amazon EC2 Linux instances<a name="EC2_GetStarted"></a>

Use this tutorial to get started with Amazon Elastic Compute Cloud \(Amazon EC2\)\. You'll learn how to launch, connect to, and use a Linux instance\. An *instance* is a virtual server in the AWS Cloud\. With Amazon EC2, you can set up and configure the operating system and applications that run on your instance\.

When you sign up for AWS, you can get started with Amazon EC2 using the [AWS Free Tier](https://aws.amazon.com/free/)\. If you created your AWS account less than 12 months ago, and have not already exceeded the free tier benefits for Amazon EC2, it will not cost you anything to complete this tutorial, because we help you select options that are within the free tier benefits\. Otherwise, you'll incur the standard Amazon EC2 usage fees from the time that you launch the instance until you terminate the instance \(which is the final task of this tutorial\), even if it remains idle\.

**Topics**
+ [Overview](#ec2-get-started-overview)
+ [Prerequisites](#ec2-getstarted-prereqs)
+ [Step 1: Launch an instance](#ec2-launch-instance)
+ [Step 2: Connect to your instance](#ec2-connect-to-instance-linux)
+ [Step 3: Clean up your instance](#ec2-clean-up-your-instance)
+ [Next steps](#ec2-next-steps)

**Related tutorials**
+ If you'd prefer to launch a Windows instance, see this tutorial in the *Amazon EC2 User Guide for Windows Instances*: [Get started with Amazon EC2 Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2_GetStarted.html)\.
+ If you'd prefer to use the command line, see this tutorial in the *AWS Command Line Interface User Guide*: [Using Amazon EC2 through the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-using-ec2.html)\.

## Overview<a name="ec2-get-started-overview"></a>

The instance is an Amazon EBS\-backed instance \(meaning that the root volume is an EBS volume\)\. You can either specify the Availability Zone in which your instance runs, or let Amazon EC2 select an Availability Zone for you\. You can think of an Availability Zone as an isolated data center\.

When you launch your instance, you secure it by specifying a key pair \(to prove your identity\) and a security group \(which acts as a virtual firewall to control ingoing and outgoing traffic\)\. When you connect to your instance, you must specify the private key of the key pair that you specified when launching your instance\.

![\[An Amazon EBS-backed instance with an additional Amazon EBS volume\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/overview_getting_started.png)

## Prerequisites<a name="ec2-getstarted-prereqs"></a>

Before you begin, be sure that you've completed the steps in [Set up to use Amazon EC2](get-set-up-for-amazon-ec2.md)\.

## Step 1: Launch an instance<a name="ec2-launch-instance"></a>

You can launch a Linux instance using the AWS Management Console as described in the following procedure\. This tutorial is intended to help you launch your first instance quickly, so it doesn't cover all possible options\. For more information about the advanced options, see [Launch an instance using the old launch instance wizard](launching-instance.md)\. For information about other ways to launch your instance, see [Launch your instance](LaunchingAndUsingInstances.md)\.

**To launch an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the console dashboard, choose **Launch Instance**\.

1. The **Choose an Amazon Machine Image \(AMI\)** page displays a list of basic configurations, called *Amazon Machine Images \(AMIs\)*, that serve as templates for your instance\. Select an HVM version of Amazon Linux 2\. Notice that these AMIs are marked "Free tier eligible\."

1. On the **Choose an Instance Type** page, you can select the hardware configuration of your instance\. Select the `t2.micro` instance type, which is selected by default\. The `t2.micro` instance type is eligible for the free tier\. In Regions where `t2.micro` is unavailable, you can use a `t3.micro` instance under the free tier\. For more information, see [AWS Free Tier](https://aws.amazon.com/free/)\.

1. On the **Choose an Instance Type** page, choose **Review and Launch** to let the wizard complete the other configuration settings for you\.

1. On the **Review Instance Launch** page, under **Security Groups**, you'll see that the wizard created and selected a security group for you\. You can use this security group, or alternatively you can select the security group that you created when getting set up using the following steps:

   1. Choose **Edit security groups**\.

   1. On the **Configure Security Group** page, ensure that **Select an existing security group** is selected\.

   1. Select your security group from the list of existing security groups, and then choose **Review and Launch**\.

1. On the **Review Instance Launch** page, choose **Launch**\.

1. When prompted for a key pair, select **Choose an existing key pair**, then select the key pair that you created when getting set up\.
**Warning**  
Don't select **Proceed without a key pair**\. If you launch your instance without a key pair, then you can't connect to it\.

   When you are ready, select the acknowledgement check box, and then choose **Launch Instances**\.

1. A confirmation page lets you know that your instance is launching\. Choose **View Instances** to close the confirmation page and return to the console\.

1. On the **Instances** screen, you can view the status of the launch\. It takes a short time for an instance to launch\. When you launch an instance, its initial state is `pending`\. After the instance starts, its state changes to `running` and it receives a public DNS name\. \(If the **Public IPv4 DNS** column is hidden, choose the settings icon \( ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/settings-icon.png) \) in the top\-right corner, toggle on **Public IPv4 DNS**, and choose **Confirm**\.

1. It can take a few minutes for the instance to be ready so that you can connect to it\. Check that your instance has passed its status checks; you can view this information in the **Status check** column\.

## Step 2: Connect to your instance<a name="ec2-connect-to-instance-linux"></a>

There are several ways to connect to your Linux instance\. For more information, see [Connect to your Linux instance](AccessingInstances.md)\.

**Important**  
You can't connect to your instance unless you launched it with a key pair for which you have the `.pem` file and you launched it with a security group that allows SSH access from your computer\. If you can't connect to your instance, see [Troubleshoot connecting to your instance](TroubleshootingInstancesConnecting.md) for assistance\.

## Step 3: Clean up your instance<a name="ec2-clean-up-your-instance"></a>

After you've finished with the instance that you created for this tutorial, you should clean up by terminating the instance\. If you want to do more with this instance before you clean up, see [Next steps](#ec2-next-steps)\.

**Important**  
Terminating an instance effectively deletes it; you can't reconnect to an instance after you've terminated it\.

If you launched an instance that is not within the [AWS Free Tier](https://aws.amazon.com/free/), you'll stop incurring charges for that instance as soon as the instance status changes to `shutting down` or `terminated`\. To keep your instance for later, but not incur charges, you can stop the instance now and then start it again later\. For more information, see [Stop and start your instance](Stop_Start.md)\.

**To terminate your instance**

1. In the navigation pane, choose **Instances**\. In the list of instances, select the instance\.

1. Choose **Instance state**, **Terminate instance**\.

1. Choose **Terminate** when prompted for confirmation\.

   Amazon EC2 shuts down and terminates your instance\. After your instance is terminated, it remains visible on the console for a short while, and then the entry is automatically deleted\. You cannot remove the terminated instance from the console display yourself\.

## Next steps<a name="ec2-next-steps"></a>

After you start your instance, you might want to try some of the following exercises:
+ Learn how to remotely manage your EC2 instance using Run Command\. For more information, see [AWS Systems Manager Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html) in the *AWS Systems Manager User Guide*\.
+ Configure a CloudWatch alarm to notify you if your usage exceeds the Free Tier\. For more information, see [Tracking your AWS Free Tier usage](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/tracking-free-tier-usage.html) in the *AWS Billing User Guide*\.
+ Add an EBS volume\. For more information, see [Create an Amazon EBS volume](ebs-creating-volume.md) and [Attach an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.
+ Install the LAMP stack\. For more information, see [Tutorial: Install a LAMP web server on Amazon Linux 2](ec2-lamp-amazon-linux-2.md)\.