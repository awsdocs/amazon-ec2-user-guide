# Working with Security Groups<a name="working-with-security-groups"></a>

You can assign a security group to an instance when you launch the instance\. When you add or remove rules, those changes are automatically applied to all instances to which you've assigned the security group\.

After you launch an instance, you can change its security groups\. For more information, see [Changing an Instance's Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SG_Changing_Group_Membership) in the *Amazon VPC User Guide*\.

You can create, view, update, and delete security groups and security group rules using the Amazon EC2 console and the command line tools\.

**Topics**
+ [Creating a Security Group](#creating-security-group)
+ [Copying a Security Group](#copy-security-group)
+ [Viewing Your Security Groups](#describing-security-group)
+ [Adding Rules to a Security Group](#adding-security-group-rule)
+ [Updating Security Group Rules](#updating-security-group-rules)
+ [Deleting Rules from a Security Group](#deleting-security-group-rule)
+ [Deleting a Security Group](#deleting-security-group)

## Creating a Security Group<a name="creating-security-group"></a>

You can create a custom security group using one of the following methods\. You must specify the VPC for which you're creating the security group\.

------
#### [ New console ]

**To create a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create security group**\.

1. In the **Basic details** section, do the following\.

   1. Enter a descriptive name and brief description for the security group\. The name and description can be up to 255 characters long, and they can include `a-z, A-Z, 0-9, spaces, and ._-:/()#,@[]+=&;{}!$*`\.

   1. For **VPC**, choose the VPC in which to create the security group\. The security group can only be used in the VPC in which it is created\.

1. You can add security group rules now, or you can add them at any time after you have created the security group\. For more information about adding security group rules, see [Adding Rules to a Security Group](#adding-security-group-rule)\.

1. Choose **Create**\.

------
#### [ Old console ]

**To create a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create Security Group**\.

1. Specify a name and description for the security group\.

1. For **VPC**, choose the ID of the VPC\.

1. You can start adding rules, or you can choose **Create** to create the security group now \(you can always add rules later\)\. For more information about adding rules, see [Adding Rules to a Security Group](#adding-security-group-rule)\. 

------
#### [ Command line ]

**To create a security group**

Use one of the following commands:
+ [create\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html) \(AWS CLI\)
+ [New\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

------

## Copying a Security Group<a name="copy-security-group"></a>

You can create a new security group by creating a copy of an existing one\. When you copy a security group, the copy is created with the same inbound and outbound rules as the original security group\. If the original security group is in a VPC, the copy is created in the same VPC unless you specify a different one\.

The copy receives a new unique security group ID and you must give it a name\. You can also add a description\.

You can create a copy of a security group using one of the following methods\.

------
#### [ New console ]

**To copy a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to copy and choose **Actions**, **Copy to new security group**\.

1. Specify a name and optional description, and change the VPC and security group rules if needed\.

1. Choose **Create**\.

------
#### [ Old console ]

**To copy a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\. 

1. Select the security group you want to copy, choose **Actions**, **Copy to new**\.

1. The **Create Security Group** dialog opens, and is populated with the rules from the existing security group\. Specify a name and description for your new security group\. For **VPC**, choose the ID of the VPC\. When you are done, choose **Create**\.

------

## Viewing Your Security Groups<a name="describing-security-group"></a>

You can view information about your security groups using one of the following methods\.

------
#### [ New console ]

**To view your security groups**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Your security groups are listed\. To view the details for a specific security group, including its inbound and outbound rules, choose its ID in the **Security group ID** column\.

------
#### [ Old console ]

**To view your security groups**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. \(Optional\) Select **VPC ID** from the filter list, then choose the ID of the VPC\.

1. Select a security group\. We display general information in the **Description** tab, inbound rules on the **Inbound** tab, outbound rules on the **Outbound** tab, and tags on the **Tags** tab\.

------
#### [ Command line ]

**To view your security groups**

Use one of the following commands\.
+ [describe\-security\-groups](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-groups.html) \(AWS CLI\)
+ [Get\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

------

## Adding Rules to a Security Group<a name="adding-security-group-rule"></a>

When you add a rule to a security group, the new rule is automatically applied to any instances that are associated with the security group\. There might be a short delay before the rule is applied\. For more information about choosing security group rules for specific types of access, see [Security Group Rules Reference](security-group-rules-reference.md)\.

You can add rules to a security group using one of the following methods\.

------
#### [ New console ]

**To add an inbound rule to a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. In the list, select the security group and choose **Actions**, **Edit inbound rules**\.

1. Choose **Add rule** and do the following\.

   1. For **Type**, choose the type of protocol to allow\.
      + If you choose a custom TCP or UDP protocol, you must manually enter the port range to allow\.
      + If you choose a custom ICMP protocol, you must choose the ICMP type name from **Protocol**, and, if applicable, the code name from **Port range**\.
      + If you choose any other type, the protocol and port range are configured automatically\. 

   1. For **Source**, do one of the following\.
      + Choose **Custom** and then enter an IP address in CIDR notation, a CIDR block, or another security group from which to allow inbound traffic\.
      + Choose **Anywhere** to allow all inbound traffic of the specified protocol to reach your instance\. This option automatically adds the `0.0.0.0/0` IPv4 CIDR block as an allowed source\. This is acceptable for a short time in a test environment, but it's unsafe for production environments\. In production, authorize only a specific IP address or range of addresses to access your instance\.

        If your security group is in a VPC that's enabled for IPv6, this option automatically adds a second rule for IPv6 traffic \(`::/0`\)\.
      + Choose **My IP** to allow inbound traffic from only your local computer's public IPv4 address\.

   1. For **Description**, optionally specify a brief description for the rule\.

1. Choose **Preview changes**, **Save rules**\.

**To add an outbound rule to a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. In the list, select the security group and choose **Actions**, **Edit outbound rules**\.

1. Choose **Add rule** and do the following\.

   1. For **Type**, choose the type of protocol to allow\.
      + If you choose a custom TCP or UDP protocol, you must manually enter the port range to allow\.
      + If you choose a custom ICMP protocol, you must choose the ICMP type name from **Protocol**, and, if applicable, the code name from **Port range**\.
      + If you choose any other type, the protocol and port range are configured automatically\. 

   1. For **Destination**, do one of the following\.
      + Choose **Custom** and then enter an IP address in CIDR notation, a CIDR block, or another security group for which to allow outbound traffic\.
      + Choose **Anywhere** to allow outbound traffic to all IP addresses\. This option automatically adds the `0.0.0.0/0` IPv4 CIDR block as an allowed source\. 

        If your security group is in a VPC that's enabled for IPv6, this option automatically adds a second rule for IPv6 traffic \(`::/0`\)\.
      + Choose **My IP** to allow outbound traffic only to your local computer's public IPv4 address\.

   1. For **Description**, optionally specify a brief description for the rule\.

1. Choose **Preview changes**, **Confirm**\.

------
#### [ Old console ]

**To add rules to a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups** and select the security group\.

1. On the **Inbound** tab, choose **Edit**\. 

1. In the dialog, choose **Add Rule** and do the following:
   + For **Type**, select the protocol\.
   + If you select a custom TCP or UDP protocol, specify the port range in **Port Range**\. 
   + If you select a custom ICMP protocol, choose the ICMP type name from **Protocol**, and, if applicable, the code name from **Port Range**\.
   + For **Source**, choose one of the following:
     + **Custom**: in the provided field, you must specify an IP address in CIDR notation, a CIDR block, or another security group\.
     + **Anywhere**: automatically adds the `0.0.0.0/0` IPv4 CIDR block\. This option enables all traffic of the specified type to reach your instance\. This is acceptable for a short time in a test environment, but it's unsafe for production environments\. In production, authorize only a specific IP address or range of addresses to access your instance\.

       If your security group is in a VPC that's enabled for IPv6, the **Anywhere** option creates two rules—one for IPv4 traffic \(`0.0.0.0/0`\) and one for IPv6 traffic \(`::/0`\)\.
     + **My IP**: automatically adds the public IPv4 address of your local computer\.
   + For **Description**, you can optionally specify a description for the rule\.

   For more information about the types of rules that you can add, see [Security Group Rules Reference](security-group-rules-reference.md)\.

1. Choose **Save**\.

1. You can also specify outbound rules\. On the **Outbound tab**, choose **Edit**, **Add Rule**, and do the following:
   + For **Type**, select the protocol\.
   + If you select a custom TCP or UDP protocol, specify the port range in **Port Range**\. 
   + If you select a custom ICMP protocol, choose the ICMP type name from **Protocol**, and, if applicable, the code name from **Port Range**\.
   + For **Destination**, choose one of the following:
     + **Custom**: in the provided field, you must specify an IP address in CIDR notation, a CIDR block, or another security group\.
     + **Anywhere**: automatically adds the `0.0.0.0/0` IPv4 CIDR block\. This option enables outbound traffic to all IP addresses\.

       If your security group is in a VPC that's enabled for IPv6, the **Anywhere** option creates two rules—one for IPv4 traffic \(`0.0.0.0/0`\) and one for IPv6 traffic \(`::/0`\)\.
     + **My IP**: automatically adds the IP address of your local computer\.
   + For **Description**, you can optionally specify a description for the rule\.

1. Choose **Save**\.

------
#### [ Command line ]

**To add rules to a security group**

Use one of the following commands\.
+ [authorize\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-ingress.html) \(AWS CLI\)
+ [Grant\-EC2SecurityGroupIngress](https://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupIngress.html) \(AWS Tools for Windows PowerShell\)

**To add one or more egress rules to a security group**

Use one of the following commands\.
+ [authorize\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-egress.html) \(AWS CLI\)
+ [Grant\-EC2SecurityGroupEgress](https://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupEgress.html) \(AWS Tools for Windows PowerShell\)

------

## Updating Security Group Rules<a name="updating-security-group-rules"></a>

You can update a security group rule using one of the following methods\.

------
#### [ New console ]

When you modify the protocol, port range, or source or destination of an existing security group rule using the console, the console deletes the existing rule and adds a new one for you\. 

**To update a security group rule**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to update, choose **Actions**, and then choose **Edit inbound rules** to update a rule for inbound traffic or **Edit outbound rules** to update a rule for outbound traffic\.

1. Update the rule as required and then choose **Preview changes**, **Confirm**\.

------
#### [ Old console ]

When you modify the protocol, port range, or source or destination of an existing security group rule using the console, the console deletes the existing rule and adds a new one for you\. 

**To update a security group rule**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to update, and choose the **Inbound** tab to update a rule for inbound traffic or the **Outbound** tab to update a rule for outbound traffic\.

1. Choose **Edit**\.

1. Modify the rule entry as required and choose **Save**\.

------
#### [ Command line ]

You cannot modify the protocol, port range, or source or destination of an existing rule using the Amazon EC2 API or a command line tools\. Instead, you must delete the existing rule and add a new rule\. You can, however, update the description of an existing rule\.

**To update the description for an existing inbound rule**

Use one of the following commands\.
+ [update\-security\-group\-rule\-descriptions\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-ingress.html) \(AWS CLI\)
+ [Update\-EC2SecurityGroupRuleIngressDescription](https://docs.aws.amazon.com/powershell/latest/reference/items/Update-EC2SecurityGroupRuleIngressDescription.html) \(AWS Tools for Windows PowerShell\)

**To update the description for an existing outbound rule**

Use one of the following commands\.
+ [update\-security\-group\-rule\-descriptions\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-egress.html) \(AWS CLI\)
+ [Update\-EC2SecurityGroupRuleEgressDescription](https://docs.aws.amazon.com/powershell/latest/reference/items/Update-EC2SecurityGroupRuleEgressDescription.html) \(AWS Tools for Windows PowerShell\)

------

## Deleting Rules from a Security Group<a name="deleting-security-group-rule"></a>

When you delete a rule from a security group, the change is automatically applied to any instances associated with the security group\.

You can delete rules from a security group using one of the following methods\.

------
#### [ New console ]

**To delete a security group rule**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to update, choose **Actions**, and then choose **Edit inbound rules** to remove an inbound rule or **Edit outbound rules** to remove an outbound rule\.

1. Choose the remove button to the right of the rule to delete\.

1. Choose **Preview changes**, **Confirm**\.

------
#### [ Old console ]

**To delete a security group rule**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\. 

1. Select a security group\.

1. On the **Inbound** tab \(for inbound rules\) or **Outbound** tab \(for outbound rules\), choose **Edit**\. Choose **Delete** \(a cross icon\) next to each rule to delete\. 

1. Choose **Save**\.

------
#### [ Command line ]

**To remove one or more ingress rules from a security group**

Use one of the following commands\.
+ [revoke\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/revoke-security-group-ingress.html) \(AWS CLI\)
+ [Revoke\-EC2SecurityGroupIngress](https://docs.aws.amazon.com/powershell/latest/reference/items/Revoke-EC2SecurityGroupIngress.html) \(AWS Tools for Windows PowerShell\)

**To remove one or more egress rules from a security group**

Use one of the following commands\.
+ [revoke\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/revoke-security-group-egress.html) \(AWS CLI\)
+ [Revoke\-EC2SecurityGroupEgress](https://docs.aws.amazon.com/powershell/latest/reference/items/Revoke-EC2SecurityGroupEgress.html) \(AWS Tools for Windows PowerShell\)

------

## Deleting a Security Group<a name="deleting-security-group"></a>

You can't delete a security group that is associated with an instance\. You can't delete the default security group\. You can't delete a security group that is referenced by a rule in another security group in the same VPC\. If your security group is referenced by one of its own rules, you must delete the rule before you can delete the security group\.

------
#### [ New console ]

**To delete a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to delete and choose **Actions**, **Delete security group**, **Delete**\.

------
#### [ Old console ]

**To delete a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select a security group and choose **Actions**, **Delete Security Group**\.

1. Choose **Yes, Delete**\.

------
#### [ Command line ]

**To delete a security group**

Use one of the following commands\.
+ [delete\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-security-group.html) \(AWS CLI\)
+ [Remove\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

------