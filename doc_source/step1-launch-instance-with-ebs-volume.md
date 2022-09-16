# Step 1: Launch an EC2 instance with an added EBS volume<a name="step1-launch-instance-with-ebs-volume"></a>

 To launch an instance you choose an AWS Region, an operating system \(OS\), an Amazon Machine Image \(AMI\), an instance type, use a key pair to securely connect to your instance, set up the network, and configure storage\. When you launch an instance, choose a descriptive name to make it easy to identify the instance\. In this step, you launch an instance called `tutorial-volumes`\.

**Note**  
This tutorial uses the Amazon Linux 2 AMI and may not work for other custom AMIs using a different Linux distribution\.

For more information about Amazon Linux 2 AMIs, see [Amazon Linux 2 and Amazon Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html#amazon-linux)\.

You can view an animation that shows you how to launch an EC2 instance with an EBS volume by expanding **View an animation: Launch an EC2 instance with an added EBS volume**\. For the written steps, see the procedure that follows\.

## View an animation: Launch an EC2 instance with an added EBS volume<a name="step1-launch-instance-with-ebs-volume-animation"></a>

![\[This animation shows you how to launch an EC2 instance with an added EBS volume. For the text version of this animation, see the steps in the following procedure.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ec2-instance-with-volume.gif)

**To launch an EC2 instance**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **EC2 Dashboard**, choose **Launch instance**\.

1. Under **Name and tags**, for **Name**, enter `tutorial-volumes`\.

1. Under **Application and OS Images**, select **Quick Start**, and verify the following defaults:

   1. **Amazon Linux** is selected as the OS for your instance\.

   1. Under **Amazon Machine Image \(AMI\)**, **Amazon Linux 2 AMI** is selected\.

1. Under **Instance type**, select ` t3.micro`\.

1. Under **Key pair \(login\)**, choose your key pair\.

1. Under **Network settings**, ensure that **Allow SSH traffic** check box is selected\. This allows you to connect to your instance using SSH\.
**Warning**  
For security reasons, do not choose **Anywhere** for **Source**\. This allows access to your instance from all IP addresses on the internet\. This is acceptable for the purposes of this tutorial, but it is unsafe for production environments\.

1. Under **Configure storage**, choose **Add new volume**\. Ensure that the added **EBS volume** size is **8 GB** and the type is `gp3`\.

   1. To ensure that the data volume is deleted upon termination of the instance, choose **Advanced**\. Under **Volume 2 \(Custom\)**, select **Delete on termination**, and select **Yes**\.

1. In the **Summary** panel, choose **Launch instance**\.

1. Choose **View all instances** to close the confirmation page and return to the Amazon EC2 console\.

For more information about launching an instance, see [ Launch an instance using the new launch instance wizard](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-instance-wizard.html)\.