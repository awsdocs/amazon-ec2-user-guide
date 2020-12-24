# Setting up with Amazon EC2<a name="get-set-up-for-amazon-ec2"></a>

Complete the tasks in this section to get set up for launching an Amazon EC2 instance for the first time: 

1. [Sign up for AWS](#sign-up-for-aws)

1. [Create a key pair](#create-a-key-pair)

1. [Create a security group](#create-a-base-security-group)

When you are finished, you will be ready for the [Amazon EC2 Getting started](EC2_GetStarted.md) tutorial\.

## Sign up for AWS<a name="sign-up-for-aws"></a>

When you sign up for Amazon Web Services \(AWS\), your AWS account is automatically signed up for all services in AWS, including Amazon EC2\. You are charged only for the services that you use\.

With Amazon EC2, you pay only for what you use\. If you are a new AWS customer, you can get started with Amazon EC2 for free\. For more information, see [AWS Free Tier](https://aws.amazon.com/free/)\.

If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

**To create an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

## Create a key pair<a name="create-a-key-pair"></a>

AWS uses public\-key cryptography to secure the login information for your instance\. A Linux instance has no password; you use a key pair to log in to your instance securely\. You specify the name of the key pair when you launch your instance, then provide the private key when you log in using SSH\. 

If you haven't created a key pair already, you can create one using the Amazon EC2 console\. Note that if you plan to launch instances in multiple Regions, you'll need to create a key pair in each Region\. For more information about Regions, see [Regions and Zones](using-regions-availability-zones.md)\.

You can create a key pair using one of the following methods\. 

------
#### [ New console ]

**To create your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Choose **Create key pair**\.

1. For **Name**, enter a descriptive name for the key pair\. Amazon EC2 associates the public key with the name that you specify as the key name\. A key name can include up to 255 ASCII characters\. It can’t include leading or trailing spaces\.

1. For **File format**, choose the format in which to save the private key\. To save the private key in a format that can be used with OpenSSH, choose **pem**\. To save the private key in a format that can be used with PuTTY, choose **ppk**\.

1. Choose **Create key pair**\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name you specified as the name of your key pair, and the file name extension is determined by the file format you chose\. Save the private key file in a safe place\.
**Important**  
This is the only chance for you to save the private key file\.

1. If you will use an SSH client on a macOS or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   chmod 400 my-key-pair.pem
   ```

   If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected private key file](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

------
#### [ Old console ]

**To create your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.
**Note**  
The navigation pane is on the left side of the Amazon EC2 console\. If you do not see the pane, it might be minimized; choose the arrow to expand the pane\. 

1. Choose **Create Key Pair**\.

1. For **Key pair name**, enter a name for the new key pair, and then choose **Create**\. The name can include up to 255 ASCII characters\. It can’t include leading or trailing spaces\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name you specified as the name of your key pair, and the file name extension is `.pem`\. Save the private key file in a safe place\.
**Important**  
This is the only chance for you to save the private key file\.

1. If you will use an SSH client on a macOS or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   chmod 400 my-key-pair.pem
   ```

   If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected private key file](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

------

For more information, see [Amazon EC2 key pairs and Linux instances](ec2-key-pairs.md)\.

## Create a security group<a name="create-a-base-security-group"></a>

Security groups act as a firewall for associated instances, controlling both inbound and outbound traffic at the instance level\. You must add rules to a security group that enable you to connect to your instance from your IP address using SSH\. You can also add rules that allow inbound and outbound HTTP and HTTPS access from anywhere\.

Note that if you plan to launch instances in multiple Regions, you'll need to create a security group in each Region\. For more information about Regions, see [Regions and Zones](using-regions-availability-zones.md)\.

**Prerequisites**  
You'll need the public IPv4 address of your local computer\. The security group editor in the Amazon EC2 console can automatically detect the public IPv4 address for you\. Alternatively, you can use the search phrase "what is my IP address" in an Internet browser, or use the following service: [Check IP](http://checkip.amazonaws.com/)\. If you are connecting through an Internet service provider \(ISP\) or from behind a firewall without a static IP address, you need to find out the range of IP addresses used by client computers\.

You can create a custom security group using one of the following methods\.

------
#### [ New console ]

**To create a security group with least privilege**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select a Region for the security group\. Security groups are specific to a Region, so you should select the same Region in which you created your key pair\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create security group**\.

1. In the **Basic details** section, do the following:

   1. Enter a name for the new security group and a description\. Use a name that is easy for you to remember, such as your user name, followed by \_SG\_, plus the Region name\. For example, *me*\_SG\_*uswest2*\.

   1. In the **VPC** list, select your default VPC for the Region\.

1. In the **Inbound rules** section, create the following rules \(choose **Add rule** for each new rule\):
   + Choose **HTTP** from the **Type** list, and make sure that **Source** is set to **Anywhere** \(`0.0.0.0/0`\)\.
   + Choose **HTTPS** from the **Type** list, and make sure that **Source** is set to **Anywhere** \(`0.0.0.0/0`\)\.
   + Choose **SSH** from the **Type** list\. In the **Source** box, choose **My IP** to automatically populate the field with the public IPv4 address of your local computer\. Alternatively, choose **Custom** and specify the public IPv4 address of your computer or network in CIDR notation\. To specify an individual IP address in CIDR notation, add the routing suffix `/32`, for example, `203.0.113.25/32`\. If your company allocates addresses from a range, specify the entire range, such as `203.0.113.0/24`\.
**Warning**  
For security reasons, do not allow SSH access from all IPv4 addresses \(`0.0.0.0/0`\) to your instance, except for testing purposes and only for a short time\.

1. Choose **Create security group**\.

------
#### [ Old console ]

**To create a security group with least privilege**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create Security Group**\.

1. Enter a name for the new security group and a description\. Use a name that is easy for you to remember, such as your user name, followed by \_SG\_, plus the Region name\. For example, *me*\_SG\_*uswest2*\.

1. In the **VPC** list, select your default VPC for the Region\.

1. On the **Inbound** tab, create the following rules \(choose **Add rule** for each new rule\):
   + Choose **HTTP** from the **Type** list, and make sure that **Source** is set to **Anywhere** \(`0.0.0.0/0`\)\.
   + Choose **HTTPS** from the **Type** list, and make sure that **Source** is set to **Anywhere** \(`0.0.0.0/0`\)\.
   + Choose **SSH** from the **Type** list\. In the **Source** box, choose **My IP** to automatically populate the field with the public IPv4 address of your local computer\. Alternatively, choose **Custom** and specify the public IPv4 address of your computer or network in CIDR notation\. To specify an individual IP address in CIDR notation, add the routing suffix `/32`, for example, `203.0.113.25/32`\. If your company allocates addresses from a range, specify the entire range, such as `203.0.113.0/24`\.
**Warning**  
For security reasons, we don't recommend that you allow SSH access from all IPv4 addresses \(`0.0.0.0/0`\) to your instance, except for testing purposes and only for a short time\.

1. Choose **Create**\.

------
#### [ Command line ]

**To create a security group with least privilege**

Use one of the following commands:
+ [create\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html) \(AWS CLI\)
+ [New\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

------

For more information, see [Amazon EC2 security groups for Linux instances](ec2-security-groups.md)\.