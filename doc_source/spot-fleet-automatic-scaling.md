# Automatic scaling for Spot Fleet<a name="spot-fleet-automatic-scaling"></a>

*Automatic scaling* is the ability to increase or decrease the target capacity of your Spot Fleet automatically based on demand\. A Spot Fleet can either launch instances \(scale out\) or terminate instances \(scale in\), within the range that you choose, in response to one or more scaling policies\.

Spot Fleet supports the following types of automatic scaling:
+ [Target tracking scaling](spot-fleet-target-tracking.md) – Increase or decrease  the current capacity of the fleet based on a target value for a specific metric\. This is similar to the way that your thermostat maintains the temperature of your home—you select temperature and the thermostat does the rest\.
+ [Step scaling](spot-fleet-step-scaling.md) – Increase or decrease the current capacity of the fleet based on a set of scaling adjustments, known as step adjustments, that vary based on the size of the alarm breach\.
+ [Scheduled scaling](spot-fleet-scheduled-scaling.md) – Increase or decrease the current capacity of the fleet based on the date and time\.

If you are using [instance weighting](how-spot-fleet-works.md#spot-instance-weighting), keep in mind that Spot Fleet can exceed the target capacity as needed\. Fulfilled capacity can be a floating\-point number but target capacity must be an integer, so Spot Fleet rounds up to the next integer\. You must take these behaviors into account when you look at the outcome of a scaling policy when an alarm is triggered\. For example, suppose that the target capacity is 30, the fulfilled capacity is 30\.1, and the scaling policy subtracts 1\. When the alarm is triggered, the automatic scaling process subtracts 1 from 30\.1 to get 29\.1 and then rounds it up to 30, so no scaling action is taken\. As another example, suppose that you selected instance weights of 2, 4, and 8, and a target capacity of 10, but no weight 2 instances were available so Spot Fleet provisioned instances of weights 4 and 8 for a fulfilled capacity of 12\. If the scaling policy decreases target capacity by 20% and an alarm is triggered, the automatic scaling process subtracts 12\*0\.2 from 12 to get 9\.6 and then rounds it up to 10, so no scaling action is taken\.

The scaling policies that you create for Spot Fleet support a cooldown period\. This is the number of seconds after a scaling activity completes where previous trigger\-related scaling activities can influence future scaling events\. For scale\-out policies, while the cooldown period is in effect, the capacity that has been added by the previous scale\-out event that initiated the cooldown is calculated as part of the desired capacity for the next scale out\. The intention is to continuously \(but not excessively\) scale out\. For scale in policies, the cooldown period is used to block subsequent scale in requests until it has expired\. The intention is to scale in conservatively to protect your application's availability\. However, if another alarm triggers a scale\-out policy during the cooldown period after a scale\-in, automatic scaling scales out your scalable target immediately\.

We recommend that you scale based on instance metrics with a 1\-minute frequency because that ensures a faster response to utilization changes\. Scaling on metrics with a 5\-minute frequency can result in slower response time and scaling on stale metric data\. To send metric data for your instances to CloudWatch in 1\-minute periods, you must specifically enable detailed monitoring\. For more information, see [Enable or turn off detailed monitoring for your instances](using-cloudwatch-new.md) and [Create a Spot Fleet request using defined parameters \(console\)](spot-fleet-requests.md#create-spot-fleet-advanced)\.

For more information about configuring scaling for Spot Fleet, see the following resources:
+ [application\-autoscaling](https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling) section of the *AWS CLI Command Reference*
+ [Application Auto Scaling API Reference](https://docs.aws.amazon.com/autoscaling/application/APIReference/)
+ [Application Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/application/userguide/)

## IAM permissions required for Spot Fleet automatic scaling<a name="spot-fleet-auto-scaling-IAM"></a>

Automatic scaling for Spot Fleet is made possible by a combination of the Amazon EC2, Amazon CloudWatch, and Application Auto Scaling APIs\. Spot Fleet requests are created with Amazon EC2, alarms are created with CloudWatch, and scaling policies are created with Application Auto Scaling\. 

In addition to the [IAM permissions for Spot Fleet](spot-fleet-requests.md#spot-fleet-iam-users) and Amazon EC2, the IAM user that accesses fleet scaling settings must have the appropriate permissions for the services that support dynamic scaling\. IAM users must have permissions to use the actions shown in the following example policy\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "application-autoscaling:*",
                "ec2:DescribeSpotFleetRequests",
                "ec2:ModifySpotFleetRequest",
                "cloudwatch:DeleteAlarms",
                "cloudwatch:DescribeAlarmHistory",
                "cloudwatch:DescribeAlarms",
                "cloudwatch:DescribeAlarmsForMetric",
                "cloudwatch:GetMetricStatistics",
                "cloudwatch:ListMetrics",
                "cloudwatch:PutMetricAlarm",
                "cloudwatch:DisableAlarmActions",
                "cloudwatch:EnableAlarmActions",
                "iam:CreateServiceLinkedRole",
                "sns:CreateTopic",
                "sns:Subscribe",
                "sns:Get*",
                "sns:List*"
            ],
            "Resource": "*"
        }
    ]
}
```

You can also create your own IAM policies that allow more fine\-grained permissions for calls to the Application Auto Scaling API\. For more information, see [Authentication and Access Control](https://docs.aws.amazon.com/autoscaling/application/userguide/auth-and-access-control.html) in the *Application Auto Scaling User Guide*\.

The Application Auto Scaling service also needs permission to describe your Spot Fleet and CloudWatch alarms, and permissions to modify your Spot Fleet target capacity on your behalf\. If you enable automatic scaling for your Spot Fleet, it creates a service\-linked role named `AWSServiceRoleForApplicationAutoScaling_EC2SpotFleetRequest`\. This service\-linked role grants Application Auto Scaling permission to describe the alarms for your policies, to monitor the current capacity of the fleet, and to modify the capacity of the fleet\. The original managed Spot Fleet role for Application Auto Scaling was `aws-ec2-spot-fleet-autoscale-role`, but it is no longer required\. The service\-linked role is the default role for Application Auto Scaling\. For more information, see [Service\-Linked Roles](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-service-linked-roles.html) in the *Application Auto Scaling User Guide*\.