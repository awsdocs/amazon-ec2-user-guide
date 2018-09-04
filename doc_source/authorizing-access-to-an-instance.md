# Authorizing Inbound Traffic for Your Linux Instances<a name="authorizing-access-to-an-instance"></a>

Security groups enable you to control traffic to your instance, including the kind of traffic that can reach your instance\. For example, you can allow computers from only your home network to access your instance using SSH\. If your instance is a web server, you can allow all IP addresses to access your instance using HTTP or HTTPS, so that external users can browse the content on your web server\.

Your default security groups and newly created security groups include default rules that do not enable you to access your instance from the Internet\. For more information, see [Default Security Groups](using-network-security.md#default-security-group) and [Custom Security Groups](using-network-security.md#creating-your-own-security-groups)\. To enable network access to your instance, you must allow inbound traffic to your instance\. To open a port for inbound traffic, add a rule to a security group that you associated with your instance when you launched it\.

To connect to your instance, you must set up a rule to authorize SSH traffic from your computer's public IPv4 address\. To allow SSH traffic from additional IP address ranges, add another rule for each range you need to authorize\.

If you've enabled your VPC for IPv6 and launched your instance with an IPv6 address, you can connect to your instance using its IPv6 address instead of a public IPv4 address\. Your local computer must have an IPv6 address and must be configured to use IPv6\.

If you need to enable network access to a Windows instance, see [Authorizing Inbound Traffic for Your Windows Instances](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/authorizing-access-to-an-instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Before You Start<a name="authorizing-access-prereqs"></a>

Decide who requires access to your instance; for example, a single host or a specific network that you trust such as your local computer's public IPv4 address\. The security group editor in the Amazon EC2 console can automatically detect the public IPv4 address of your local computer for you\. Alternatively, you can use the search phrase "what is my IP address" in an internet browser, or use the following service: [Check IP](http://checkip.amazonaws.com/)\. If you are connecting through an ISP or from behind your firewall without a static IP address, you need to find out the range of IP addresses used by client computers\.

**Warning**  
If you use `0.0.0.0/0`, you enable all IPv4 addresses to access your instance using SSH\. If you use `::/0`, you enable all IPv6 address to access your instance\. This is acceptable for a short time in a test environment, but it's unsafe for production environments\. In production, you authorize only a specific IP address or range of addresses to access your instance\.

For more information about security groups, see [Amazon EC2 Security Groups for Linux Instances](using-network-security.md)\.

## Adding a Rule for Inbound SSH Traffic to a Linux Instance<a name="add-rule-authorize-access"></a>

Security groups act as a firewall for associated instances, controlling both inbound and outbound traffic at the instance level\. You must add rules to a security group that enable you to connect to your Linux instance from your IP address using SSH\.

**To add a rule to a security group for inbound SSH traffic over IPv4 using the console**

1. In the navigation pane of the Amazon EC2 console, choose **Instances**\. Select your instance and look at the **Description** tab; **Security groups** lists the security groups that are associated with the instance\. Choose **view rules** to display a list of the rules that are in effect for the instance\.

1. In the navigation pane, choose **Security Groups**\. Select one of the security groups associated with your instance\.

1. In the details pane, on the **Inbound** tab, choose **Edit**\. In the dialog, choose **Add Rule**, and then choose **SSH** from the **Type** list\.

1. In the **Source** field, choose **My IP** to automatically populate the field with the public IPv4 address of your local computer\. Alternatively, choose **Custom** and specify the public IPv4 address of your computer or network in CIDR notation\. For example, if your IPv4 address is `203.0.113.25`, specify `203.0.113.25/32` to list this single IPv4 address in CIDR notation\. If your company allocates addresses from a range, specify the entire range, such as `203.0.113.0/24`\.

   For information about finding your IP address, see [Before You Start](#authorizing-access-prereqs)\.

1. Choose **Save**\.

\(VPC only\) If you launched an instance with an IPv6 address and want to connect to your instance using its IPv6 address, you must add rules that allow inbound IPv6 traffic over SSH\.

**To add a rule to a security group for inbound SSH traffic over IPv6 using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\. Select the security group for your instance\.

1. Choose **Inbound**, **Edit**, **Add Rule**\.

1. For **Type**, choose **SSH**\.

1. In the **Source** field, specify the IPv6 address of your computer in CIDR notation\. For example, if your IPv6 address is `2001:db8:1234:1a00:9691:9503:25ad:1761`, specify `2001:db8:1234:1a00:9691:9503:25ad:1761/128` to list the single IP address in CIDR notation\. If your company allocates addresses from a range, specify the entire range, such as `2001:db8:1234:1a00::/64`\.

1. Choose **Save**\.

**Note**  
Be sure to run the following commands on your local system, not on the instance itself\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

**To add a rule to a security group using the command line**

1. Find the security group that is associated with your instance using one of the following commands:
   + [describe\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-attribute.html) \(AWS CLI\)

     ```
     aws ec2 describe-instance-attribute --instance-id instance_id --attribute groupSet
     ```
   + [Get\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

     ```
     PS C:\> (Get-EC2InstanceAttribute -InstanceId instance_id -Attribute groupSet).Groups
     ```

   Both commands return a security group ID, which you use in the next step\.

1. Add the rule to the security group using one of the following commands:
   + [authorize\-security\-group\-ingress](http://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-ingress.html) \(AWS CLI\)

     ```
     aws ec2 authorize-security-group-ingress --group-id security_group_id --protocol tcp --port 22 --cidr cidr_ip_range
     ```
   + [Grant\-EC2SecurityGroupIngress](http://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupIngress.html) \(AWS Tools for Windows PowerShell\)

     The `Grant-EC2SecurityGroupIngress` command needs an `IpPermission` parameter, which describes the protocol, port range, and IP address range to be used for the security group rule\. The following command creates the `IpPermission` parameter:

     ```
     PS C:\> $ip1 = @{ IpProtocol="tcp"; FromPort="22"; ToPort="22"; IpRanges="cidr_ip_range" }
     ```

     ```
     PS C:\> Grant-EC2SecurityGroupIngress -GroupId security_group_id -IpPermission @($ip1)
     ```

## Assigning a Security Group to an Instance<a name="assign-security-group-to-instance"></a>

You can assign a security group to an instance when you launch the instance\. When you add or remove rules, those changes are automatically applied to all instances to which you've assigned the security group\.

After you launch an instance in EC2\-Classic, you can't change its security groups\. After you launch an instance in a VPC, you can change its security groups\. For more information, see [Changing an Instance's Security Groups](http://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SG_Changing_Group_Membership) in the *Amazon VPC User Guide*\.