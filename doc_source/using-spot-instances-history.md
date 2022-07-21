# Spot Instance pricing history<a name="using-spot-instances-history"></a>

Spot Instance prices are set by Amazon EC2 and adjust gradually based on long\-term trends in supply and demand for Spot Instance capacity\.

When your Spot request is fulfilled, your Spot Instances launch at the current Spot price, not exceeding the On\-Demand price\. You can view the Spot price history for the last 90 days, filtering by instance type, operating system, and Availability Zone\.

**To view the current Spot prices**  
For the *current* Spot Instance prices, see [Amazon EC2 Spot Instances Pricing](http://aws.amazon.com/ec2/spot/pricing/)\.

**To view the Spot price history \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**\.

1. Choose **Pricing history**\. 

1. For **Graph**, choose to compare the price history by **Availability Zones** or by **Instance Types**\.
   + If you choose **Availability Zones**, then choose the **Instance type**, operating system \(**Platform**\), and **Date range** for which to view the price history\.
   + If you choose **Instance Types**, then choose up to five **Instance type\(s\)**, the **Availability Zone**, operating system \(**Platform**\), and **Date range** for which to view the price history\.

   The following screenshot shows a price comparison for different instance types\.  
![\[The Spot Instance pricing history tool in the Amazon EC2 console.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/spot-instance-pricing-history.png)

1. Hover \(move your pointer\) over the graph to display the prices at specific times in the selected date range\. The prices are displayed in the information blocks above the graph\. The price displayed in the top row shows the price on a specific date\. The price displayed in the second row shows the average price over the selected date range\.

1. To display the price per vCPU, toggle on **Display normalized prices**\. To display the price for the instance type, toggle off **Display normalized prices**\.

**To view the Spot price history using the command line**

You can use one of the following commands\. For more information, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-spot\-price\-history](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-price-history.html) \(AWS CLI\)
+ [Get\-EC2SpotPriceHistory](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SpotPriceHistory.html) \(AWS Tools for Windows PowerShell\)