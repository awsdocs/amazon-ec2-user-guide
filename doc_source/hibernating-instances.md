# Hibernate an instance<a name="hibernating-instances"></a>

You can hibernate an instance if the instance is [enabled for hibernation](enabling-hibernation.md) and meets the [hibernation prerequisites](hibernating-prerequisites.md)\. If an instance cannot hibernate successfully, a normal shutdown occurs\.

------
#### [ Console ]

**To hibernate an Amazon EBS\-backed instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select an instance, and choose **Instance state**, **Hibernate instance**\. If **Hibernate instance** is disabled, the instance is already hibernated or stopped, or it can't be hibernated\. For more information, see [Hibernation prerequisites](hibernating-prerequisites.md)\.

1. When prompted for confirmation, choose **Hibernate**\. It can take a few minutes for the instance to hibernate\. The instance state first changes to **Stopping**, and then changes to **Stopped** when the instance has hibernated\.

------
#### [ AWS CLI ]

**To hibernate an Amazon EBS\-backed instance**  
Use the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) command and specify the `--hibernate` parameter\.

```
aws ec2 stop-instances \
    --instance-ids i-1234567890abcdef0 \
    --hibernate
```

------
#### [ PowerShell ]

**To hibernate an Amazon EBS\-backed instance using the AWS Tools for Windows PowerShell**  
Use the [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) command and specify the `-Hibernate $true` parameter\.

```
Stop-EC2Instance `
    -InstanceId i-1234567890abcdef0 `
    -Hibernate $true
```

------

 

------
#### [ Console ]

**To view if hibernation was initiated on an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and, on the **Details** tab, in the **Instance details** section, inspect **State transition message**\. The message **Client\.UserInitiatedHibernate: User initiated hibernate** indicates that hibernation was initiated on the instance\.

------
#### [ AWS CLI ]

**To view if hibernation was initiated on an instance**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and specify the `state-reason-code` filter to see the instances on which hibernation was initiated\.

```
aws ec2 describe-instances \
    --filters "Name=state-reason-code,Values=Client.UserInitiatedHibernate"
```

The following field in the output indicates that hibernation was initiated on the instance\.

```
"StateReason": {
    "Code": "Client.UserInitiatedHibernate"
}
```

------
#### [ PowerShell ]

**To view if hibernation was initiated on an instance using the AWS Tools for Windows PowerShell**  
Use the [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) command and specify the `state-reason-code` filter to see the instances on which hibernation was initiated\.

```
Get-EC2Instance `
    -Filter @{Name="state-reason-code";Value="Client.UserInitiatedHibernate"}
```

The output lists the EC2 instances on which hibernation was initiated\. 

------