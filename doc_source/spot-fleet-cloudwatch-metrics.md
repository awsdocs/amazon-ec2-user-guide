# CloudWatch Metrics for Spot Fleet<a name="spot-fleet-cloudwatch-metrics"></a>

Amazon EC2 provides Amazon CloudWatch metrics that you can use to monitor your Spot Fleet\.

**Important**  
To ensure accuracy, we recommend that you enable detailed monitoring when using these metrics\. For more information, see [Enable or Disable Detailed Monitoring for Your Instances](using-cloudwatch-new.md)\.

For more information about CloudWatch metrics provided by Amazon EC2, see [Monitoring Your Instances Using CloudWatch](using-cloudwatch.md)\.

## Spot Fleet Metrics<a name="spot-fleet-metrics"></a>

The `AWS/EC2Spot` namespace includes the following metrics, plus the CloudWatch metrics for the Spot Instances in your fleet\. For more information, see [Instance Metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)\.

The `AWS/EC2Spot` namespace includes the following metrics\.


| Metric | Description | 
| --- | --- | 
| `AvailableInstancePoolsCount` |  The Spot Instance pools specified in the Spot Fleet request\. Units: Count  | 
| `BidsSubmittedForCapacity` |  The capacity for which Amazon EC2 has submitted Spot Fleet requests\. Units: Count  | 
| `EligibleInstancePoolCount` |  The Spot Instance pools specified in the Spot Fleet request where Amazon EC2 can fulfill requests\. Amazon EC2 does not fulfill requests in pools where the maximum price you're willing to pay for Spot Instances is less than the Spot price or the Spot price is greater than the price for On\-Demand Instances\. Units: Count  | 
| `FulfilledCapacity` |  The capacity that Amazon EC2 has fulfilled\. Units: Count  | 
| `MaxPercentCapacityAllocation` |  The maximum value of `PercentCapacityAllocation` across all Spot Fleet pools specified in the Spot Fleet request\. Units: Percent  | 
| `PendingCapacity` |  The difference between `TargetCapacity` and `FulfilledCapacity`\. Units: Count  | 
| `PercentCapacityAllocation` |  The capacity allocated for the Spot Instance pool for the specified dimensions\. To get the maximum value recorded across all Spot Instance pools, use `MaxPercentCapacityAllocation`\. Units: Percent  | 
| `TargetCapacity` |  The target capacity of the Spot Fleet request\. Units: Count  | 
| `TerminatingCapacity` |  The capacity that is being terminated because the provisioned capacity is greater than the target capacity\. Units: Count  | 

If the unit of measure for a metric is `Count`, the most useful statistic is `Average`\.

## Spot Fleet Dimensions<a name="spot-fleet-dimensions"></a>

To filter the data for your Spot Fleet, use the following dimensions\.


| Dimensions | Description | 
| --- | --- | 
| `AvailabilityZone` |  Filter the data by Availability Zone\.  | 
| `FleetRequestId` |  Filter the data by Spot Fleet request\.  | 
| `InstanceType` |  Filter the data by instance type\.  | 

## View the CloudWatch Metrics for Your Spot Fleet<a name="view-spot-metrics"></a>

You can view the CloudWatch metrics for your Spot Fleet using the Amazon CloudWatch console\. These metrics are displayed as monitoring graphs\. These graphs show data points if the Spot Fleet is active\.

Metrics are grouped first by namespace, and then by the various combinations of dimensions within each namespace\. For example, you can view all Spot Fleet metrics or Spot Fleet metrics groups by Spot Fleet request ID, instance type, or Availability Zone\.

**To view Spot Fleet metrics**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, under **Metrics**, choose the **EC2 Spot** namespace\.

1. \(Optional\) To filter the metrics by dimension, select one of the following:
   + **Fleet Request Metrics** — Group by Spot Fleet request
   + **By Availability Zone** — Group by Spot Fleet request and Availability Zone
   + **By Instance Type** — Group by Spot Fleet request and instance type
   + **By Availability Zone/Instance Type** — Group by Spot Fleet request, Availability Zone, and instance type

1. To view the data for a metric, select the check box next to the metric\.  
![\[CloudWatch metrics for Spot Fleet\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_spot_fleet.png)