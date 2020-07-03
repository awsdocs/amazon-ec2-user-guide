# Savings from purchasing Spot Instances<a name="spot-savings"></a>

You can view the usage and savings information for Spot Instances at the per\-fleet level, or for all running Spot Instances\. At the per\-fleet level, the usage and savings information includes all instances launched and terminated by the fleet\. You can view this information from the last hour or the last three days\.

The following screenshot from the Spot Requests page shows the Spot usage and savings information for a Spot Fleet\.

![\[The Spot Fleet Savings tab in the Amazon EC2 console.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/spot-savings.png)

You can view the following usage and savings information:
+ **Spot Instances** – The number of Spot Instances launched and terminated by the Spot Fleet\. When viewing the savings summary, the number represents all your running Spot Instances\.
+ **vCPU\-hours** – The number of vCPU hours used across all the Spot Instances for the selected time frame\.
+ **Mem\(GiB\)\-hours** – The number of GiB hours used across all the Spot Instances for the selected time frame\.
+ **On\-Demand total** – The total amount you would've paid for the selected time frame had you launched these instances as On\-Demand Instances\.
+ **Spot total** – The total amount to pay for the selected time frame\.
+ **Savings** – The percentage that you are saving by not paying the On\-Demand price\.
+ **Average cost per vCPU\-hour** – The average hourly cost of using the vCPUs across all the Spot Instances for the selected time frame, calculated as follows: **Average cost per vCPU\-hour** = **Spot total** / **vCPU\-hours**\.
+ **Average cost per mem\(GiB\)\-hour** – The average hourly cost of using the GiBs across all the Spot Instances for the selected time frame, calculated as follows: **Average cost per mem\(GiB\)\-hour** = **Spot total** / **Mem\(GiB\)\-hours**\.
+ **Details** table – The different instance types \(the number of instances per instance type is in parentheses\) that comprise the Spot Fleet\. When viewing the savings summary, these comprise all your running Spot Instances\.

Savings information can only be viewed using the Amazon EC2 console\.

**To view the savings information for a Spot Fleet \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, choose **Spot Requests**\.

1. Select a Spot Fleet request and choose **Savings**\.

1. By default, the page displays usage and savings information for the last three days\. You can choose **last hour** or the **last three days**\. For Spot Fleets that were launched less than an hour ago, the page shows the estimated savings for the hour\.

**To view the savings information for all running Spot Instances \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, choose **Spot Requests**\.

1. Choose **Savings Summary**\.