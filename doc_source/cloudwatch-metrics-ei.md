# Using CloudWatch Metrics to Monitor Amazon EI<a name="cloudwatch-metrics-ei"></a>

You can monitor your Amazon EI accelerators using Amazon CloudWatch, which collects metrics about your usage and performance\. These statistics are recorded for a period of two weeks so that you can access historical information and gain a better perspective of how your service is performing\. 

By default, Amazon EI sends metric data to CloudWatch in 5\-minute periods\.

For more information, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.

**Topics**
+ [Amazon EI Metrics and Dimensions](#ei-metrics-dimensions)
+ [Creating CloudWatch Alarms to Monitor Amazon EI](#ei-cw-alarms)

## Amazon EI Metrics and Dimensions<a name="ei-metrics-dimensions"></a>

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\. You can use the following procedures to view the metrics for Amazon EI\.

**To view metrics using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, change the Region\. From the navigation bar, select the region where Amazon EI resides\. For more information, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.

1. In the navigation pane, choose **Metrics**\. 

1. Under **All metrics**, select a metrics category, and then scroll down to view the full list of metrics\. 

**To view metrics \(AWS CLI\)**
+ At a command prompt, enter the following command:

  ```
  aws cloudwatch list-metrics --namespace " AWS/ElasticInference "
  ```

CloudWatch displays the following metrics for Amazon EI\.


| Metric | Description | 
| --- | --- | 
|                     **AcceleratorHealthCheckFailed**                 |                     Reports whether the Amazon EI accelerator haspassed a status health check in the last minute\. A value ofzero \(0\) indicates that the status check passed\. A value of one \(1\) indicates a status check failure\.Units: Count                 | 
|                     **ConnectivityCheckFailed**                 |                     Reports whether connectivity to the Amazon EI accelerator is active or has failed in the last minute\. A value of zero \(0\) indicates that the connection is active\. A value of one \(1\) indicates a connectivity failure\.Units: Count                 | 
|                     **AcceleratorMemoryUsage**                 |                     The memory of the Amazon EI accelerator used in the last minute\.Units: Bytes                 | 

You can filter the Amazon EI data using the following dimensions\. 


| Dimension | Description | 
| --- | --- | 
|                     **ElasticInferenceAcceleratorId **                 |                     This dimension filters the data by the Amazon EI accelerator\.                 | 
|                     **InstanceId**                 |                     This dimension filters the data by instance to which the Amazon EI accelerator is attached\.                 | 

## Creating CloudWatch Alarms to Monitor Amazon EI<a name="ei-cw-alarms"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period that you specify\. It sends a notification to an SNS topic based on the value of the metric relative to a given threshold over a number of time periods\.

For example, you can create an alarm that monitors the health of an Amazon EI accelerator\. It sends a notification when the Amazon EI accelerator fails a status health check for three consecutive 5\-minute periods\. 

**To create an alarm for Amazon EI accelerator health status**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **Create Alarm**\.

1. Choose **Amazon EI Metrics**\. 

1. Select the **Amazon EI** and the **AcceleratorHealthCheckFailed** metric and choose **Next**\.

1. Configure the alarm as follows, and then choose **Create Alarm**:
   + Under **Alarm Threshold**, enter a name and description\. For **Whenever**, choose **=>** and enter **1**\. For the consecutive periods, enter **3**\.
   + Under **Actions**, select an existing notification list or choose **New list**\.
   + Under **Alarm Preview**, select a period of 5 minutes\.