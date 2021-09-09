# Start a hibernated instance<a name="hibernating-resuming"></a>

Start a hibernated instance by starting it in the same way that you would start a stopped instance\.

------
#### [ New console ]

**To start a hibernated instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select a hibernated instance, and choose **Instance state**, **Start instance**\. It can take a few minutes for the instance to enter the `running` state\. During this time, the instance [status checks](monitoring-system-instance-status-check.md#types-of-instance-status-checks) show the instance in a failed state until the instance has started\.

------
#### [ Old console ]

**To start a hibernated instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select a hibernated instance, and choose **Actions**, **Instance State**, **Start**\. It can take a few minutes for the instance to enter the `running` state\. During this time, the instance [status checks](monitoring-system-instance-status-check.md#types-of-instance-status-checks) show the instance in a failed state until the instance has started\.

------
#### [ AWS CLI ]

**To start a hibernated instance using the AWS CLI**  
Use the [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) command\.

```
aws ec2 start-instances \
    --instance-ids i-1234567890abcdef0
```

------
#### [ PowerShell ]

**To start a hibernated instance using the AWS Tools for Windows PowerShell**  
Use the [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) command\.

```
Start-EC2Instance `
    -InstanceId i-1234567890abcdef0
```

------