# Monitor Capacity Reservations using CloudWatch metrics<a name="capacity-reservation-cw-metrics"></a>

With CloudWatch metrics, you can efficiently monitor your Capacity Reservations and identify unused capacity by setting CloudWatch alarms to notify you when usage thresholds are met\. This can help you maintain a constant Capacity Reservation volume and achieve a higher level of utilization\.

On\-Demand Capacity Reservations send metric data to CloudWatch every five minutes\. Metrics are not supported for Capacity Reservations that are active for less than five minutes\.

For more information about viewing metrics in the CloudWatch console, see [Using Amazon CloudWatch Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)\. For more information about creating alarms, see [Creating Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)\. 

**Topics**
+ [Capacity Reservation usage metrics](#capacity-reservation-usage-metrics)
+ [Capacity Reservation metric dimensions](#capacity-reservation-dimensions)
+ [View CloudWatch metrics for Capacity Reservations](#viewing-capacity-reservation-metrics)

## Capacity Reservation usage metrics<a name="capacity-reservation-usage-metrics"></a>

The `AWS/EC2CapacityReservations` namespace includes the following usage metrics you can use to monitor and maintain on\-demand capacity within thresholds you specify for your reservation\.


| Metric | Description | 
| --- | --- | 
|  UsedInstanceCount |  The number of instances that are currently in use\. Unit: Count  | 
|  AvailableInstanceCount  |  The number of instances that are available\. Unit: Count  | 
|  TotalInstanceCount  |  The total number of instances you have reserved\. Unit: Count  | 
|  InstanceUtilization  |  The percentage of reserved capacity instances that are currently in use\. Unit: Percent  | 

## Capacity Reservation metric dimensions<a name="capacity-reservation-dimensions"></a>

You can use the following dimensions to refine the metrics listed in the previous table\.


|  Dimension  |  Description  | 
| --- | --- | 
|  CapacityReservationId  |  This globally unique dimension filters the data you request for the identified capacity reservation only\.  | 

## View CloudWatch metrics for Capacity Reservations<a name="viewing-capacity-reservation-metrics"></a>

Metrics are grouped first by the service namespace, and then by the supported dimensions\. You can use the following procedures to view the metrics for your Capacity Reservations\. 

**To view Capacity Reservation metrics using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, change the Region\. From the navigation bar, select the Region where your Capacity Reservation resides\. For more information, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.

1. In the navigation pane, choose **Metrics**\.

1. For **All metrics**, choose **EC2 Capacity Reservations**\.

1. Choose the metric dimension **By Capacity Reservation**\. Metrics will be grouped by `CapacityReservationId`\.

1. To sort the metrics, use the column heading\. To graph a metric, select the check box next to the metric\.

**To view Capacity Reservation metrics \(AWS CLI\)**  
Use the following [list\-metrics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command:

```
aws cloudwatch list-metrics --namespace "AWS/EC2CapacityReservations"
```