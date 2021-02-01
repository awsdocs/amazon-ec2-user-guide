# Amazon EC2 service quotas<a name="ec2-resource-limits"></a>

Amazon EC2 provides different *resources* that you can use\. These resources include images, instances, volumes, and snapshots\. When you create your AWS account, we set default quotas \(also referred to as limits\) on these resources on a per\-Region basis\. For example, there is a maximum number of instances that you can launch in a Region\. So if you were to launch an instance in the US West \(Oregon\) Region, for example, the request must not cause your usage to exceed your maximum number of instances in that Region\.

The Amazon EC2 console provides limit information for the resources managed by the Amazon EC2 and Amazon VPC consoles\. You can request an increase for many of these limits\. Use the limit information that we provide to manage your AWS infrastructure\. Plan to request any limit increases in advance of the time that you'll need them\.

For more information, see [Amazon EC2 endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/ec2-service.html) in the *Amazon Web Services General Reference*\. For information about Amazon EBS quotas, see [Amazon EBS quotas](ebs-resource-quotas.md)\.

## View your current limits<a name="view-limits"></a>

Use the **Limits** page in the Amazon EC2 console to view the current limits for resources provided by Amazon EC2 and Amazon VPC, on a per\-Region basis\.

**To view your current limits**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select a Region\.  
![\[Select a Region\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EC2_select_region.png)

1. From the navigation pane, choose **Limits**\.

1. Locate the resource in the list\. You can use the search fields to filter the list by resource name or resource group\. The **Current limit** column displays the current maximum for the resource for your account\.

## Request an increase<a name="request-increase"></a>

Use the **Limits** page in the Amazon EC2 console to request an increase in your Amazon EC2 or Amazon VPC resources, on a per\-Region basis\.

Alternatively, request an increase using Service Quotas\. For more information, see [Requesting a quota increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-quota-increase.html) in the *Service Quotas User Guide*\.

**To request an increase using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select a Region\.

1. From the navigation pane, choose **Limits**\.

1. Select the resource in the list, and choose **Request limit increase**\.

1. Complete the required fields on the limit increase form and choose **Submit**\. We'll respond to you using the contact method that you specified\.

## Restriction on email sent using port 25<a name="port-25-throttle"></a>

On all instances, Amazon EC2 restricts traffic on port 25 by default\. You can request that this restriction be removed\. For more information, see [How do I remove the restriction on port 25 from my EC2 instance?](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-port-25-throttle/) in the AWS Knowledge Center\.