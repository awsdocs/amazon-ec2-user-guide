# Setting Up with Amazon EC2<a name="get-set-up-for-amazon-ec2"></a>

If you've already signed up for Amazon Web Services \(AWS\), you can start using Amazon EC2 immediately\. You can open the Amazon EC2 console, choose **Launch Instance**, and follow the steps in the launch wizard to launch your first instance\. 

If you haven't signed up for AWS yet, or if you need assistance launching your first instance, complete the following tasks to get set up to use Amazon EC2: 

1. [Sign Up for AWS](#sign-up-for-aws)

1. [Create an IAM User](#create-an-iam-user)

1. [Create a Key Pair](#create-a-key-pair)

1. [Create a Virtual Private Cloud \(VPC\)](#create-a-vpc)

1. [Create a Security Group](#create-a-base-security-group)

## Sign Up for AWS<a name="sign-up-for-aws"></a>

When you sign up for Amazon Web Services \(AWS\), your AWS account is automatically signed up for all services in AWS, including Amazon EC2\. You are charged only for the services that you use\.

With Amazon EC2, you pay only for what you use\. If you are a new AWS customer, you can get started with Amazon EC2 for free\. For more information, see [AWS Free Tier](https://aws.amazon.com/free/)\.

If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

**To create an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

Note your AWS account number, because you'll need it for the next task\.

## Create an IAM User<a name="create-an-iam-user"></a>

Services in AWS, such as Amazon EC2, require that you provide credentials when you access them, so that the service can determine whether you have permission to access its resources\. The console requires your password\. You can create access keys for your AWS account to access the command line interface or API\. However, we don't recommend that you access AWS using the credentials for your AWS account; we recommend that you use AWS Identity and Access Management \(IAM\) instead\. Create an IAM user, and then add the user to an IAM group with administrative permissions or grant this user administrative permissions\. You can then access AWS using a special URL and the credentials for the IAM user\.

If you signed up for AWS but have not created an IAM user for yourself, you can create one using the IAM console\. If you aren't familiar with using the console, see [Working with the AWS Management Console](http://docs.aws.amazon.com/awsconsolehelpdocs/latest/gsg/getting-started.html) for an overview\.

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Use your AWS account email address and password to sign in as the *[AWS account root user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html)* to the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user below and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add user**\.

1. For **User name**, enter **Administrator**\.

1. Select the check box next to **AWS Management Console access**\. Then select **Custom password**, and then enter your new password in the text box\.

1. \(Optional\) By default, AWS requires the new user to create a new password when first signing in\. You can clear the check box next to **User must create a new password at next sign\-in** to allow the new user to reset their password after they sign in\.

1. Choose **Next: Permissions**\.

1. Under **Set permissions**, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** enter **Administrators**\.

1. Choose **Filter policies**, and then select **AWS managed \-job function** to filter the table contents\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.
**Note**  
You must activate IAM user and role access to Billing before you can use the `AdministratorAccess` permissions to access the AWS Billing and Cost Management console\. To do this, follow the instructions in [step 1 of the tutorial about delegating access to the billing console](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_billing.html)\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM Entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users and to give your users access to your AWS account resources\. To learn about using policies that restrict user permissions to specific AWS resources, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

To sign in as this new IAM user, sign out of the AWS console, then use the following URL, where *your\_aws\_account\_id* is your AWS account number without the hyphens \(for example, if your AWS account number is `1234-5678-9012`, your AWS account ID is `123456789012`\):

```
https://your_aws_account_id.signin.aws.amazon.com/console/
```

Enter the IAM user name \(not your email address\) and password that you just created\. When you're signed in, the navigation bar displays "*your\_user\_name* @ *your\_aws\_account\_id*"\.

If you don't want the URL for your sign\-in page to contain your AWS account ID, you can create an account alias\. From the IAM console, choose **Dashboard** in the navigation pane\. From the dashboard, choose **Customize** and enter an alias such as your company name\. To sign in after you create an account alias, use the following URL:

```
https://your_account_alias.signin.aws.amazon.com/console/
```

To verify the sign\-in link for IAM users for your account, open the IAM console and check under **IAM users sign\-in link** on the dashboard\.

For more information about IAM, see [IAM and Amazon EC2](security-iam.md#intro-to-iam)\.

## Create a Key Pair<a name="create-a-key-pair"></a>

AWS uses public\-key cryptography to secure the login information for your instance\. A Linux instance has no password; you use a key pair to log in to your instance securely\. You specify the name of the key pair when you launch your instance, then provide the private key when you log in using SSH\. 

If you haven't created a key pair already, you can create one using the Amazon EC2 console\. Note that if you plan to launch instances in multiple regions, you'll need to create a key pair in each region\. For more information about regions, see [Regions, Availability Zones, and Local Zones](using-regions-availability-zones.md)\.

**To create a key pair**

1. Sign in to AWS using the URL that you created in the previous section\.

1. From the AWS dashboard, choose **EC2** to open the Amazon EC2 console\.

1. From the navigation bar, select a region for the key pair\. You can select any region that's available to you, regardless of your location\. However, key pairs are specific to a region; for example, if you plan to launch an instance in the US East \(Ohio\) Region, you must create a key pair for the instance in the US East \(Ohio\) Region\.  
![\[Select a region\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EC2_select_region.png)

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.
**Tip**  
The navigation pane is on the left side of the console\. If you do not see the pane, it might be minimized; choose the arrow to expand the pane\. You may have to scroll down to see the **Key Pairs** link\.  
![\[Open the key pairs page\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/key-pairs.png)

1. Choose **Create Key Pair**\.

1. Enter a name for the new key pair in the **Key pair name** field of the **Create Key Pair** dialog box, and then choose **Create**\. Use a name that is easy for you to remember, such as your IAM user name, followed by `-key-pair`, plus the region name\. For example, *me*\-key\-pair\-*useast2*\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name you specified as the name of your key pair, and the file name extension is `.pem`\. Save the private key file in a safe place\.
**Important**  
This is the only chance for you to save the private key file\. You'll need to provide the name of your key pair when you launch an instance and the corresponding private key each time you connect to the instance\.

1. If you will use an SSH client on a Mac or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   chmod 400 your_user_name-key-pair-region_name.pem
   ```

   If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected Private Key File](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

For more information, see [Amazon EC2 Key Pairs](ec2-key-pairs.md)\.

**To connect to your instance using your key pair**  
To connect to your Linux instance from a computer running Mac or Linux, you'll specify the `.pem` file to your SSH client with the `-i` option and the path to your private key\. To connect to your Linux instance from a computer running Windows, you can use PuTTY, the Windows Subsystem for Linux, or AWS Systems Manager Session Manager\. If you plan to use PuTTY, you'll need to use the following procedure to convert the `.pem` file to a `.ppk` file\.<a name="prepare-for-putty"></a>

**\(Optional\) To prepare to connect to a Linux instance from Windows using PuTTY**

1. Download and install PuTTY from [ http://www\.chiark\.greenend\.org\.uk/\~sgtatham/putty/](http://www.chiark.greenend.org.uk/~sgtatham/putty/)\. Be sure to install the entire suite\.

1. Start PuTTYgen \(for example, from the **Start** menu, choose **All Programs > PuTTY > PuTTYgen**\)\.

1. Under **Type of key to generate**, choose **RSA**\.  
![\[SSH-2 RSA key in PuTTYgen\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/puttygen-key-type.png)

1. Choose **Load**\. By default, PuTTYgen displays only files with the extension `.ppk`\. To locate your `.pem` file, select the option to display files of all types\.  
![\[Select all file types\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/puttygen-load-key.png)

1. Select the private key file that you created in the previous procedure and choose **Open**\. Choose **OK** to dismiss the confirmation dialog box\.

1. Choose **Save private key**\. PuTTYgen displays a warning about saving the key without a passphrase\. Choose **Yes**\.

1. Specify the same name for the key that you used for the key pair\. PuTTY automatically adds the `.ppk` file extension\.

## Create a Virtual Private Cloud \(VPC\)<a name="create-a-vpc"></a>

Amazon VPC enables you to launch AWS resources into a virtual network that you've defined, known as a *virtual private cloud* \(VPC\)\. The newer EC2 instance types require that you launch your instances in a VPC\. If you have a default VPC, you can skip this section and move to the next task, [Create a Security Group](#create-a-base-security-group)\. To determine whether you have a default VPC, open the Amazon EC2 console and look for **Default VPC** under **Account Attributes** on the dashboard\. If you do not have a default VPC listed on the dashboard, you can create a nondefault VPC using the steps below\.

**To create a nondefault VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. From the navigation bar, select a region for the VPC\. VPCs are specific to a region, so you should select the same region in which you created your key pair\.

1. On the VPC dashboard, choose **Launch VPC Wizard**\.

1. On the **Step 1: Select a VPC Configuration** page, ensure that **VPC with a Single Public Subnet** is selected, and choose **Select**\.

1. On the **Step 2: VPC with a Single Public Subnet** page, enter a friendly name for your VPC in the **VPC name** field\. Leave the other default configuration settings, and choose **Create VPC**\. On the confirmation page, choose **OK**\.

For more information about VPCs, see the [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\.

## Create a Security Group<a name="create-a-base-security-group"></a>

Security groups act as a firewall for associated instances, controlling both inbound and outbound traffic at the instance level\. You must add rules to a security group that enable you to connect to your instance from your IP address using SSH\. You can also add rules that allow inbound and outbound HTTP and HTTPS access from anywhere\.

Note that if you plan to launch instances in multiple regions, you'll need to create a security group in each region\. For more information about regions, see [Regions, Availability Zones, and Local Zones](using-regions-availability-zones.md)\.

**Prerequisites**  
You'll need the public IPv4 address of your local computer\. The security group editor in the Amazon EC2 console can automatically detect the public IPv4 address for you\. Alternatively, you can use the search phrase "what is my IP address" in an Internet browser, or use the following service: [Check IP](http://checkip.amazonaws.com/)\. If you are connecting through an Internet service provider \(ISP\) or from behind a firewall without a static IP address, you need to find out the range of IP addresses used by client computers\.

**To create a security group with least privilege**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.
**Tip**  
Alternatively, you can use the Amazon VPC console to create a security group\. However, the instructions in this procedure don't match the Amazon VPC console\. Therefore, if you switched to the Amazon VPC console in the previous section, either switch back to the Amazon EC2 console and use these instructions, or use the instructions in [Set Up a Security Group for Your VPC](https://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/SecurityGroup.html) in the *Amazon VPC Getting Started Guide*\.

1. From the navigation bar, select a region for the security group\. Security groups are specific to a region, so you should select the same region in which you created your key pair\.  
![\[Select a region\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EC2_select_region.png)

1. Choose **Security Groups** in the navigation pane\.

1. Choose **Create Security Group**\.

1. Enter a name for the new security group and a description\. Use a name that is easy for you to remember, such as your IAM user name, followed by \_SG\_, plus the region name\. For example, *me*\_SG\_*uswest2*\.

1. In the **VPC** list, select your VPC\. If you have a default VPC, it's the one that is marked with an asterisk \(\*\)\.

1. On the **Inbound** tab, create the following rules \(choose **Add Rule** for each new rule\), and then choose **Create**:
   + Choose **HTTP** from the **Type** list, and make sure that **Source** is set to **Anywhere** \(`0.0.0.0/0`\)\.
   + Choose **HTTPS** from the **Type** list, and make sure that **Source** is set to **Anywhere** \(`0.0.0.0/0`\)\.
   + Choose **SSH** from the **Type** list\. In the **Source** box, choose **My IP** to automatically populate the field with the public IPv4 address of your local computer\. Alternatively, choose **Custom** and specify the public IPv4 address of your computer or network in CIDR notation\. To specify an individual IP address in CIDR notation, add the routing suffix `/32`, for example, `203.0.113.25/32`\. If your company allocates addresses from a range, specify the entire range, such as `203.0.113.0/24`\.
**Warning**  
For security reasons, we don't recommend that you allow SSH access from all IPv4 addresses \(`0.0.0.0/0`\) to your instance, except for testing purposes and only for a short time\.

For more information, see [Amazon EC2 Security Groups for Linux Instances](ec2-security-groups.md)\.