# Set up to use Amazon EC2<a name="get-set-up-for-amazon-ec2"></a>

Complete the tasks in this section to get set up for launching an Amazon EC2 instance for the first time: 

1. [Sign up for AWS](#sign-up-for-aws)

1. [Create a key pair](#create-a-key-pair)

1. [Create a security group](#create-a-base-security-group)

When you are finished, you will be ready for the [Amazon EC2 Getting started](EC2_GetStarted.md) tutorial\.

## Sign up for AWS<a name="sign-up-for-aws"></a>

When you sign up for Amazon Web Services, your AWS account is automatically signed up for all services in AWS, including Amazon EC2\. You are charged only for the services that you use\.

With Amazon EC2, you pay only for what you use\. If you are a new AWS customer, you can get started with Amazon EC2 for free\. For more information, see [AWS Free Tier](https://aws.amazon.com/free/)\.

If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

**To create an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

## Create a key pair<a name="create-a-key-pair"></a>

AWS uses public\-key cryptography to secure the login information for your instance\. A Linux instance has no password; you use a key pair to log in to your instance securely\. You specify the name of the key pair when you launch your instance, then provide the private key when you log in using SSH\. 

If you haven't created a key pair already, you can create one by using the Amazon EC2 console\. Note that if you plan to launch instances in multiple Regions, you'll need to create a key pair in each Region\. For more information about Regions, see [Regions and Zones](using-regions-availability-zones.md)\.

**To create your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Choose **Create key pair**\.

1. For **Name**, enter a descriptive name for the key pair\. Amazon EC2 associates the public key with the name that you specify as the key name\. A key name can include up to 255 ASCII characters\. It can’t include leading or trailing spaces\.

1. For **Key pair type**, choose either **RSA** or **ED25519**\. Note that **ED25519** keys are not supported for Windows instances\.

1. For **Private key file format**, choose the format in which to save the private key\. To save the private key in a format that can be used with OpenSSH, choose **pem**\. To save the private key in a format that can be used with PuTTY, choose **ppk**\.

   If you chose **ED25519** in the previous step, the **Private key file format** options do not appear, and the private key format defaults to **pem**\.

1. Choose **Create key pair**\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name you specified as the name of your key pair, and the file name extension is determined by the file format you chose\. Save the private key file in a safe place\.
**Important**  
This is the only chance for you to save the private key file\.

1. If you will use an SSH client on a macOS or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   chmod 400 my-key-pair.pem
   ```

   If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected private key file](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

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

1. From the top navigation bar, select a Region for the security group\. Security groups are specific to a Region, so you should select the same Region in which you created your key pair\.

1. In the left navigation pane, choose **Security Groups**\.

1. Choose **Create security group**\.

1. For **Basic details**, do the following:

   1. Enter a name for the new security group and a description\. Use a name that is easy for you to remember, such as your user name, followed by \_SG\_, plus the Region name\. For example, *me*\_SG\_*uswest2*\.

   1. In the **VPC** list, select your default VPC for the Region\.

1. For **Inbound rules**, create rules that allow specific traffic to reach your instance\. For example, use the following rules for a web server that accepts HTTP and HTTPS traffic\. For more examples, see [Security group rules for different use cases](security-group-rules-reference.md)\.

   1. Choose **Add rule**\. For **Type**, choose **HTTP**\. For **Source**, choose **Anywhere**\.

   1. Choose **Add rule**\. For **Type**, choose **HTTPS**\. For **Source**, choose **Anywhere**\.

   1. Choose **Add rule**\. For **Type**, choose **SSH**\. For **Source**, do one of the following:
      + Choose **My IP** to automatically add the public IPv4 address of your local computer\.
      + Choose **Custom** and specify the public IPv4 address of your computer or network in CIDR notation\. To specify an individual IP address in CIDR notation, add the routing suffix `/32`, for example, `203.0.113.25/32`\. If your company or your router allocates addresses from a range, specify the entire range, such as `203.0.113.0/24`\.
**Warning**  
For security reasons, do not choose **Anywhere** for **Source** with a rule for SSH\. This would allow access to your instance from all IP addresses on the internet\. This is acceptable for a short time in a test environment, but it is unsafe for production environments\.

1. For **Outbound rules**, keep the default rule, which allows all outbound traffic\.

1. Choose **Create security group**\.

------
#### [ Old console ]

**To create a security group with least privilege**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Security Groups**\.

1. Choose **Create Security Group**\.

1. Enter a name for the new security group and a description\. Use a name that is easy for you to remember, such as your user name, followed by \_SG\_, plus the Region name\. For example, *me*\_SG\_*uswest2*\.

1. In the **VPC** list, select your default VPC for the Region\.

1. On the **Inbound rules** tab, create the following rules \(choose **Add rule** for each new rule\):
   + Choose **HTTP** from the **Type** list, and make sure that **Source** is set to **Anywhere** \(`0.0.0.0/0`\)\.
   + Choose **HTTPS** from the **Type** list, and make sure that **Source** is set to **Anywhere** \(`0.0.0.0/0`\)\.
   + Choose **SSH** from the **Type** list\. In the **Source** box, choose **My IP** to automatically populate the field with the public IPv4 address of your local computer\. Alternatively, choose **Custom** and specify the public IPv4 address of your computer or network in CIDR notation\. To specify an individual IP address in CIDR notation, add the routing suffix `/32`, for example, `203.0.113.25/32`\. If your company allocates addresses from a range, specify the entire range, such as `203.0.113.0/24`\.
**Warning**  
For security reasons, do not allow SSH access from all IP addresses to your instance\. This is acceptable for a short time in a test environment, but it is unsafe for production environments\.

1. On the **Outbound rules** tab, keep the default rule, which allows all outbound traffic\.

1. Choose **Create security group**\.

------
#### [ Command line ]

**To create a security group with least privilege**

Use one of the following commands:
+ [create\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html) \(AWS CLI\)
+ [New\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

------

For more information, see [Amazon EC2 security groups for Linux instances](ec2-security-groups.md)\.