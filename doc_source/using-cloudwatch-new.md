# Enable or turn off detailed monitoring for your instances<a name="using-cloudwatch-new"></a>

By default, your instance is enabled for basic monitoring\. You can optionally enable detailed monitoring\. After you enable detailed monitoring, the Amazon EC2 console displays monitoring graphs with a 1\-minute period for the instance\.

The following describes the data interval and charge for basic and detailed monitoring for instances\.


| Monitoring type | Description | Charges | 
| --- | --- | --- | 
| Basic monitoring | Data is available automatically in 5\-minute periods\. | No charge\. | 
| Detailed monitoring | Data is available in 1\-minute periods\. To get this level of data, you must specifically enable it for the instance\. For the instances where you've enabled detailed monitoring, you can also get aggregated data across groups of similar instances\. | You are charged per metric that is sent to CloudWatch\. You are not charged for data storage\. For more information, see Paid tier and Example 1 \- EC2 Detailed Monitoring on the [Amazon CloudWatch pricing page](http://aws.amazon.com/cloudwatch/pricing/)\. | 

**Topics**
+ [Required IAM permissions](#iam-detailed-monitoring)
+ [Enable detailed monitoring](#enable-detailed-monitoring)
+ [Turn off detailed monitoring](#disable-detailed-monitoring)

## Required IAM permissions<a name="iam-detailed-monitoring"></a>

To enable detailed monitoring for an instance, your IAM user must have permission to use the [ MonitorInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_MonitorInstances.html) API action\. To turn off detailed monitoring for an instance, your IAM user must have permission to use the [UnmonitorInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_UnmonitorInstances.html) API action\. 

## Enable detailed monitoring<a name="enable-detailed-monitoring"></a>

You can enable detailed monitoring on an instance as you launch it or after the instance is running or stopped\. Enabling detailed monitoring on an instance does not affect the monitoring of the EBS volumes attached to the instance\. For more information, see [Amazon CloudWatch metrics for Amazon EBS](using_cloudwatch_ebs.md)\.

------
#### [ New console ]

**To enable detailed monitoring for an existing instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **Monitoring**, **Manage detailed monitoring**\.

1. On the **Detailed monitoring** detail page, for **Detailed monitoring**, select the **Enable** check box\.

1. Choose **Save**\.

**To enable detailed monitoring when launching an instance**  
When launching an instance using the AWS Management Console, select the **Monitoring** check box on the **Configure Instance Details** page\.

------
#### [ Old console ]

**To enable detailed monitoring for an existing instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **CloudWatch Monitoring**, **Enable Detailed Monitoring**\.

1. In the **Enable Detailed Monitoring** dialog box, choose **Yes, Enable**\.

1. Choose **Close**\.

**To enable detailed monitoring when launching an instance \(console\)**  
When launching an instance using the AWS Management Console, select the **Monitoring** check box on the **Configure Instance Details** page\.

------
#### [ AWS CLI ]

**To enable detailed monitoring for an existing instance**  
Use the following [monitor\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/monitor-instances.html) command to enable detailed monitoring for the specified instances\.

```
aws ec2 monitor-instances --instance-ids i-1234567890abcdef0
```

**To enable detailed monitoring when launching an instance**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command with the `--monitoring` flag to enable detailed monitoring\.

```
aws ec2 run-instances --image-id ami-09092360 --monitoring Enabled=true...
```

------

## Turn off detailed monitoring<a name="disable-detailed-monitoring"></a>

You can turn off detailed monitoring on an instance as you launch it or after the instance is running or stopped\.

------
#### [ New console ]

**To turn off detailed monitoring**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **Monitoring**, **Manage detailed monitoring**\.

1. On the **Detailed monitoring** detail page, for **Detailed monitoring**, clear the **Enable** check box\.

1. Choose **Save**\.

------
#### [ Old console ]

**To turn off detailed monitoring**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **CloudWatch Monitoring**, **Disable Detailed Monitoring**\.

1. In the **Disable Detailed Monitoring** dialog box, choose **Yes, Disable**\.

1. Choose **Close**\.

------
#### [ AWS CLI ]

**To turn off detailed monitoring**  
Use the following [unmonitor\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/unmonitor-instances.html) command to turn off detailed monitoring for the specified instances\.

```
aws ec2 unmonitor-instances --instance-ids i-1234567890abcdef0
```

------