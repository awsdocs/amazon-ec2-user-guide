# Scale Spot Fleet Using a Target Tracking Policy<a name="spot-fleet-target-tracking"></a>

With target tracking scaling policies, you select a metric and set a target value\. Spot Fleet creates and manages the CloudWatch alarms that trigger the scaling policy and calculates the scaling adjustment based on the metric and the target value\. The scaling policy adds or removes capacity as required to keep the metric at, or close to, the specified target value\. In addition to keeping the metric close to the target value, a target tracking scaling policy also adjusts to the fluctuations in the metric due to a fluctuating load pattern and minimizes rapid fluctuations in the capacity of the fleet\.

You can create multiple target tracking scaling policies for a Spot Fleet, provided that each of them uses a different metric\. The fleet scales based on the policy that provides the largest fleet capacity\. This enables you to cover multiple scenarios and ensure that there is always enough capacity to process your application workloads\.

To ensure application availability, the fleet scales out proportionally to the metric as fast as it can, but scales in more gradually\.

Note that when a Spot Fleet terminates an instance because the target capacity was decreased, the instance receives a Spot Instance interruption notice\.

Do not edit or delete the CloudWatch alarms that Spot Fleet manages for a target tracking scaling policy\. Spot Fleet deletes the alarms automatically when you delete the target tracking scaling policy\.

**Limits**
+ The Spot Fleet request must have a request type of `maintain`\. Automatic scaling is not supported for one\-time requests or Spot blocks\.

**To configure a target tracking policy using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**\.

1. Select your Spot Fleet request, and then choose the **Auto Scaling** tab\.

1. If automatic scaling is not configured, choose **Configure**\.

1. Use **Scale capacity between** to set the minimum and maximum capacity for your fleet\. Automatic scaling does not scale your fleet below the minimum capacity or above the maximum capacity\.

1. For **Policy name**, type a name for the policy\.

1. Choose a **Target metric**\.

1. Type a **Target value** for the metric\.

1. \(Optional\) Set **Cooldown period** to modify the default cooldown period\.

1. \(Optional\) Select **Disable scale\-in** to omit creating a scale\-in policy based on the current configuration\. You can create a scale\-in policy using a different configuration\.

1. Choose **Save**\.

**To configure a target tracking policy using the AWS CLI**

1. Register the Spot Fleet request as a scalable target using the [register\-scalable\-target](http://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/register-scalable-target.html) command\.

1. Create a scaling policy using the [put\-scaling\-policy](http://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/put-scaling-policy.html) command\.