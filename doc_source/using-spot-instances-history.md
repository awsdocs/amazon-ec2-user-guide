# Spot Instance pricing history<a name="using-spot-instances-history"></a>

When you request Spot Instances, we recommend that you use the default maximum price \(the On\-Demand price\)\. If you want to specify a maximum price, we recommend that you review the Spot price history before you do so\. You can view the Spot price history for the last 90 days, filtering by instance type, operating system, and Availability Zone\.

Spot Instance prices are set by Amazon EC2 and adjust gradually based on long\-term trends in supply and demand for Spot Instance capacity\. For the *current* Spot Instance prices see [Amazon EC2 Spot Instances Pricing](http://aws.amazon.com/ec2/spot/pricing/)\.

**To view the Spot price history \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, choose **Spot Requests**\.

1. If you are new to Spot Instances, you see a welcome page\. Choose **Get started**, scroll to the bottom of the screen, and then choose **Cancel**\.

1. Choose **Pricing History**\. 

1. Choose the operating system \(**Product**\), **Instance type**, and **Date range** for which to view the price history\. Move your pointer over the graph to display the prices at specific times in the selected date range\.  
![\[The Spot Instance Pricing History tool in the Amazon EC2 console.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/SpotInstance_spotinstancepricinghistory-gwt.png)

1. \(Optional\) To review the Spot price history for a specific Availability Zone, select a zone from the list\. You can also select a different product, instance type, or date range\.

**To view the Spot price history using the command line**

You can use one of the following commands\. For more information, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-spot\-price\-history](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-price-history.html) \(AWS CLI\)
+ [Get\-EC2SpotPriceHistory](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SpotPriceHistory.html) \(AWS Tools for Windows PowerShell\)