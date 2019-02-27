# Scale Spot Fleet Using Step Scaling Policies<a name="spot-fleet-step-scaling"></a>

With step scaling policies, you specify CloudWatch alarms to trigger the scaling process\. For example, if you want to scale out when CPU utilization reaches a certain level, create an alarm using the `CPUUtilization` metric provided by Amazon EC2\.

When you create a step scaling policy, you must specify one of the following scaling adjustment types:
+ **Add** – Increase the target capacity of the fleet by a specified number of capacity units or a specified percentage of the current capacity\.
+ **Remove** – Decrease the target capacity of the fleet by a specified number of capacity units or a specified percentage of the current capacity\.
+ **Set to** – Set the target capacity of the fleet to the specified number of capacity units\.

When an alarm is triggered, the automatic scaling process calculates the new target capacity using the fulfilled capacity and the scaling policy, and then updates the target capacity accordingly\. For example, suppose that the target capacity and fulfilled capacity are 10 and the scaling policy adds 1\. When the alarm is triggered, the automatic scaling process adds 1 to 10 to get 11, so Spot Fleet launches 1 instance\.

When a Spot Fleet terminates an instance because the target capacity was decreased, the instance receives a Spot Instance interruption notice\.

**Limits**
+ The Spot Fleet request must have a request type of `maintain`\. Automatic scaling is not supported for one\-time requests or Spot blocks\.

**Prerequisites**
+ Consider which CloudWatch metrics are important to your application\. You can create CloudWatch alarms based on metrics provided by AWS or your own custom metrics\.
+ For the AWS metrics that you will use in your scaling policies, enable CloudWatch metrics collection if the service that provides the metrics does not enable it by default\.
+ If you use the AWS Management Console to enable automatic scaling for your Spot Fleet, it creates a role named `aws-ec2-spot-fleet-autoscale-role` that grants Amazon EC2 Auto Scaling permission to describe the alarms for your policies, monitor the current capacity of the fleet, and modify the capacity of the fleet\. If you configure automatic scaling using the AWS CLI or an API, you can use this role if it exists, or manually create your own role for this purpose\.

**To create a role manually**

  1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

  1. In the navigation pane, choose **Roles**, and then choose **Create role**\.

  1. For **Select type of trusted entity**, choose **AWS service**\.

  1. For **Choose the service that will use this role**, choose **EC2**\. 

  1. For **Select your use case**, choose **EC2 \- Spot Fleet Auto Scaling**, and then choose **Next: Permissions**\.

  1. For **Attached permissions policy**, the **AmazonEC2SpotFleetAutoscaleRole** policy automatically appears\. Choose **Next: Tags**, and then **Next: Review**\.

  1. For **Review**, type a name for the role and choose **Create role**\.

**To create a CloudWatch alarm**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**\.

1. Choose **Create Alarm**\.

1. For **CloudWatch Metrics by Category**, choose a category\. For example, choose **EC2 Spot Metrics**, **Fleet Request Metrics**\.

1. Select a metric and choose **Next**\.

1. For **Alarm Threshold**, type a name and description for the alarm, and set the threshold value and number of time periods for the alarm\.

1. \(Optional\) To receive notification of a scaling event, for **Actions**, choose **New list** and type your email address\. Otherwise, you can delete the notification now and add one later as needed\.

1. Choose **Create Alarm**\.

**To configure step scaling policies for your Spot Fleet \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**\.

1. Select your Spot Fleet request and choose **Auto Scaling**\.

1. If automatic scaling is not configured, choose **Configure**\.

1. Use **Scale capacity between** to set the minimum and maximum capacity for your fleet\. Automatic scaling does not scale your fleet below the minimum capacity or above the maximum capacity\.

1. Initially, **Scaling policies** contains policies named ScaleUp and ScaleDown\. You can complete these policies, or choose **Remove policy** to delete them\. You can also choose **Add policy**\.

1. To define a policy, do the following:

   1. For **Policy name**, type a name for the policy\.

   1. For **Policy trigger**, select an existing alarm or choose **Create new alarm** to open the Amazon CloudWatch console and create an alarm\.

   1. For **Modify capacity**, select a scaling adjustment type, select a number, and select a unit\.

   1. \(Optional\) To perform step scaling, choose **Define steps**\. By default, an add policy has a lower bound of \-infinity and an upper bound of the alarm threshold\. By default, a remove policy has a lower bound of the alarm threshold and an upper bound of \+infinity\. To add another step, choose **Add step**\.

   1. \(Optional\) To modify the default value for the cooldown period, select a number from **Cooldown period**\.

1. Choose **Save**\.

**To configure step scaling policies for your Spot Fleet using the AWS CLI**

1. Register the Spot Fleet request as a scalable target using the [register\-scalable\-target](https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/register-scalable-target.html) command\.

1. Create a scaling policy using the [put\-scaling\-policy](https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/put-scaling-policy.html) command\.

1. Create an alarm that triggers the scaling policy using the [put\-metric\-alarm](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html) command\.