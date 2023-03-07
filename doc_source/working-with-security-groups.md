# Work with security groups<a name="working-with-security-groups"></a>

You can assign a security group to an instance when you launch the instance\. When you add or remove rules, those changes are automatically applied to all instances to which you've assigned the security group\. For more information, see [Assign a security group to an instance](#assigning-security-group)\.

After you launch an instance, you can change its security groups\. For more information, see [Change an instance's security group](#changing-security-group)\.

You can create, view, update, and delete security groups and security group rules using the Amazon EC2 console and the command line tools\.

**Topics**
+ [Create a security group](#creating-security-group)
+ [Copy a security group](#copy-security-group)
+ [View your security groups](#describing-security-group)
+ [Add rules to a security group](#adding-security-group-rule)
+ [Update security group rules](#updating-security-group-rules)
+ [Delete rules from a security group](#deleting-security-group-rule)
+ [Delete a security group](#deleting-security-group)
+ [Assign a security group to an instance](#assigning-security-group)
+ [Change an instance's security group](#changing-security-group)

## Create a security group<a name="creating-security-group"></a>

Although you can use the default security group for your instances, you might want to create your own groups to reflect the different roles that instances play in your system\.

By default, new security groups start with only an outbound rule that allows all traffic to leave the instances\. You must add rules to enable any inbound traffic or to restrict the outbound traffic\.

A security group can be used only in the VPC for which it is created\.

------
#### [ Console ]

**To create a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create security group**\.

1. In the **Basic details** section, do the following\.

   1. Enter a descriptive name and brief description for the security group\. They can't be edited after the security group is created\. The name and description can be up to 255 characters long\. The valid characters are a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=&;\{\}\!$\*\.

   1. For **VPC**, choose the VPC\.

1. You can add security group rules now, or you can add them later\. For more information, see [Add rules to a security group](#adding-security-group-rule)\.

1. You can add tags now, or you can add them later\. To add a tag, choose **Add new tag** and enter the tag key and value\.

1. Choose **Create security group**\.

------
#### [ Command line ]

**To create a security group**

Use one of the following commands:
+ [create\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html) \(AWS CLI\)
+ [New\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

------

## Copy a security group<a name="copy-security-group"></a>

You can create a new security group by creating a copy of an existing one\. When you copy a security group, the copy is created with the same inbound and outbound rules as the original security group\. If the original security group is in a VPC, the copy is created in the same VPC unless you specify a different one\.

The copy receives a new unique security group ID and you must give it a name\. You can also add a description\.

You can't copy a security group from one Region to another Region\.

You can create a copy of a security group using the Amazon EC2 console\.

**To copy a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to copy and choose **Actions**, **Copy to new security group**\.

1. Specify a name and optional description, and change the VPC and security group rules if needed\.

1. Choose **Create**\.

## View your security groups<a name="describing-security-group"></a>

You can view information about your security groups using one of the following methods\.

------
#### [ Console ]

**To view your security groups**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Your security groups are listed\. To view the details for a specific security group, including its inbound and outbound rules, choose its ID in the **Security group ID** column\.

------
#### [ Command line ]

**To view your security groups**

Use one of the following commands\.
+ [describe\-security\-groups](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-groups.html) \(AWS CLI\)
+ [describe\-security\-group\-rules](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-group-rules.html) \(AWS CLI\)
+ [Get\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

------
#### [ Amazon EC2 Global View ]

You can use Amazon EC2 Global View to view your security groups across all Regions for which your AWS account is enabled\. For more information, see [List and filter resources across Regions using Amazon EC2 Global View](Using_Filtering.md#global-view)\.

------

## Add rules to a security group<a name="adding-security-group-rule"></a>

When you add a rule to a security group, the new rule is automatically applied to any instances that are associated with the security group\. There might be a short delay before the rule is applied\. For more information, see [Security group rules for different use cases](security-group-rules-reference.md) and [Security group rules](security-group-rules.md)\.

------
#### [ Console ]

**To add an inbound rule to a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group, and choose **Actions**, **Edit inbound rules**\.

1. For each rule, choose **Add rule** and do the following\.

   1. For **Type**, choose the type of protocol to allow\.
      + For custom TCP or UDP, you must enter the port range to allow\.
      + For custom ICMP, you must choose the ICMP type from **Protocol**, and, if applicable, the code from **Port range**\. For example, to allow ping commands, choose **Echo Request** from **Protocol**\.
      + For any other type, the protocol and port range are configured for you\.

   1. For **Source**, do one of the following to allow traffic\.
      + Choose **Custom** and then enter an IP address in CIDR notation, a CIDR block, another security group, or a prefix list\.
      + Choose **Anywhere** to allow all traffic for the specified protocol to reach your instance\. This option automatically adds the 0\.0\.0\.0/0 IPv4 CIDR block as the source\. If your security group is in a VPC that's enabled for IPv6, this option automatically adds a rule for the ::/0 IPv6 CIDR block\.
**Warning**  
If you choose **Anywhere**, you enable all IPv4 and IPv6 addresses to access your instance the specified protocol\. If you are adding rules for ports 22 \(SSH\) or 3389 \(RDP\), you should authorize only a specific IP address or range of addresses to access your instance\.
      + Choose **My IP** to allow inbound traffic from only your local computer's public IPv4 address\.

   1. For **Description**, optionally specify a brief description for the rule\.

1. Choose **Preview changes**, **Save rules**\.

**To add an outbound rule to a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group, and choose **Actions**, **Edit outbound rules**\.

1. For each rule, choose **Add rule** and do the following\.

   1. For **Type**, choose the type of protocol to allow\.
      + For custom TCP or UDP, you must enter the port range to allow\.
      + For custom ICMP, you must choose the ICMP type from **Protocol**, and, if applicable, the code from **Port range**\.
      + For any other type, the protocol and port range are configured automatically\.

   1. For **Destination**, do one of the following\.
      + Choose **Custom** and then enter an IP address in CIDR notation, a CIDR block, another security group, or a prefix list for which to allow outbound traffic\.
      + Choose **Anywhere** to allow outbound traffic to all IP addresses\. This option automatically adds the 0\.0\.0\.0/0 IPv4 CIDR block as the destination\.

        If your security group is in a VPC that's enabled for IPv6, this option automatically adds a rule for the ::/0 IPv6 CIDR block\.
      + Choose **My IP** to allow outbound traffic only to your local computer's public IPv4 address\.

   1. \(Optional\) For **Description**, specify a brief description for the rule\.

1. Choose **Preview changes**, **Confirm**\.

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

## Update security group rules<a name="updating-security-group-rules"></a>

You can update a security group rule using one of the following methods\. The updated rule is automatically applied to any instances that are associated with the security group\.

------
#### [ Console ]

When you modify the protocol, port range, or source or destination of an existing security group rule using the console, the console deletes the existing rule and adds a new one for you\. 

**To update a security group rule**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group\.

1. Choose **Actions**, **Edit inbound rules** to update a rule for inbound traffic or **Actions**, **Edit outbound rules** to update a rule for outbound traffic\.

1. Update the rule as required\.

1. Choose **Preview changes**, **Confirm**\.

**To tag a security group rule**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group\.

1. On the **Inbound rules** or **Outbound rules** tab, select the check box for the rule and then choose **Manage tags**\.

1. The **Manage tags** page displays any tags that are assigned to the rule\. To add a tag, choose **Add tag** and enter the tag key and value\. To delete a tag, choose **Remove** next to the tag that you want to delete\.

1. Choose **Save changes**\.

------
#### [ Command line ]

You cannot modify the protocol, port range, or source or destination of an existing rule using the Amazon EC2 API or a command line tools\. Instead, you must delete the existing rule and add a new rule\. You can, however, update the description of an existing rule\.

**To update a rule**

Use one the following command\.
+ [modify\-security\-group\-rules](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-security-group-rules.html) \(AWS CLI\)

**To update the description for an existing inbound rule**

Use one of the following commands\.
+ [update\-security\-group\-rule\-descriptions\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-ingress.html) \(AWS CLI\)
+ [Update\-EC2SecurityGroupRuleIngressDescription](https://docs.aws.amazon.com/powershell/latest/reference/items/Update-EC2SecurityGroupRuleIngressDescription.html) \(AWS Tools for Windows PowerShell\)

**To update the description for an existing outbound rule**

Use one of the following commands\.
+ [update\-security\-group\-rule\-descriptions\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-egress.html) \(AWS CLI\)
+ [Update\-EC2SecurityGroupRuleEgressDescription](https://docs.aws.amazon.com/powershell/latest/reference/items/Update-EC2SecurityGroupRuleEgressDescription.html) \(AWS Tools for Windows PowerShell\)

**To tag a security group rule**

Use one of the following commands\.
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)

------

## Delete rules from a security group<a name="deleting-security-group-rule"></a>

When you delete a rule from a security group, the change is automatically applied to any instances associated with the security group\.

You can delete rules from a security group using one of the following methods\.

------
#### [ Console ]

**To delete a security group rule**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to update, choose **Actions**, and then choose **Edit inbound rules** to remove an inbound rule or **Edit outbound rules** to remove an outbound rule\.

1. Choose the **Delete** button to the right of the rule to delete\.

1. Choose **Preview changes**, **Confirm**\.

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

## Delete a security group<a name="deleting-security-group"></a>

You can't delete a security group that is associated with an instance\. You can't delete the default security group\. You can't delete a security group that is referenced by a rule in another security group in the same VPC\. If your security group is referenced by one of its own rules, you must delete the rule before you can delete the security group\.

------
#### [ Console ]

**To delete a security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to delete and choose **Actions**, **Delete security group**, **Delete**\.

------
#### [ Command line ]

**To delete a security group**

Use one of the following commands\.
+ [delete\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-security-group.html) \(AWS CLI\)
+ [Remove\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

------

## Assign a security group to an instance<a name="assigning-security-group"></a>

You can assign one or more security groups to an instance when you launch the instance\. You can also specify one or more security groups in a launch template\. The security groups are assigned to all instances that are launched using the launch template\.
+ To assign a security group to an instance when you launch the instance, see [Network settings](ec2-launch-instance-wizard.md#liw-network-settings) of [Launch an instance using defined parameters](ec2-launch-instance-wizard.md#liw-launch-instance-with-defined-parameters) \(new console\) or [Step 6: Configure Security Group](launching-instance.md#step-6-configure-security-group) \(old console\)\.
+ To specify a security group in a launch template, see [Network settings](create-launch-template.md#lt-network-settings) of [Create a new launch template using parameters you define](create-launch-template.md#create-launch-template-define-parameters)\.

## Change an instance's security group<a name="changing-security-group"></a>

After you launch an instance, you can change its security groups by adding or removing security groups\.

**Requirements**
+ The instance must be in the `running` or `stopped` state\.
+ A security group is specific to a VPC\. You can assign a security group to one or more instances launched in the VPC for which you created the security group\.

------
#### [ Console ]

**To change the security groups for an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance, and then choose **Actions**, **Security**, **Change security groups**\.

1. For **Associated security groups**, select a security group from the list and choose **Add security group**\.

   To remove an already associated security group, choose **Remove** for that security group\.

1. Choose **Save**\.

------
#### [ Command line ]

**To change the security groups for an instance**

Use one of the following commands\.
+ [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

------