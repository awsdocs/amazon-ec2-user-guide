# Utilization notifications<a name="monitor-cr-utilization"></a>

AWS Health sends the following email and AWS Health Dashboard notifications when capacity utilization for Capacity Reservations in your account drops below 20 percent\.
+ Individual notifications for each newly created Capacity Reservation that has been below 20 percent utilization over the last 24\-hour period\.
+ A summary notification for all Capacity Reservations that have been below 20 percent utilization over the last 7\-day period\.

The email notifications and AWS Health Dashboard notifications are sent to the email address associated with the AWS account that owns the Capacity Reservations\. The notifications include the following information:
+ The ID of the Capacity Reservation\.
+ The Availability Zone of the Capacity Reservation\.
+ The average utilization rate for the Capacity Reservation\.
+ The instance type and platform \(operating system\) of the Capacity Reservation\.

Additionally, when capacity utilization for a Capacity Reservation in your account drops below 20 percent over a 24\-hour and 7\-day period, AWS Health sends events to EventBridge\. With EventBridge, you can create rules that activate automatic actions, such as sending email notifications or triggering AWS Lambda functions, in response to such events\. For more information, see [Monitor Capacity Reservations using EventBridge](cr-eventbridge.md)\.