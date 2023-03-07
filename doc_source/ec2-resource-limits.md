# Amazon EC2 service quotas<a name="ec2-resource-limits"></a>

Amazon EC2 provides different *resources* that you can use\. These resources include images, instances, volumes, and snapshots\. When you create your AWS account, we set default quotas \(also referred to as limits\) on these resources on a per\-Region basis\. For example, there is a maximum number of instances that you can launch in a Region\. So if you were to launch an instance in the US West \(Oregon\) Region, for example, the request must not cause your usage to exceed your maximum number of instances in that Region\.

The Amazon EC2 console provides limit information for the resources managed by the Amazon EC2 and Amazon VPC consoles\. You can request an increase for many of these limits\. Use the limit information that we provide to manage your AWS infrastructure\. Plan to request any limit increases in advance of the time that you'll need them\.

For more information, see [Amazon EC2 endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/ec2-service.html) in the *Amazon Web Services General Reference*\. For information about Amazon EBS quotas, see [Amazon EBS quotas](ebs-resource-quotas.md)\.

## View your current limits<a name="view-limits"></a>

You can view and manage your limits for each Region using the Amazon EC2 console or the Service Quotas console\.

**To view your current limits using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select a Region\.  
![\[Select a Region.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EC2_select_region.png)

1. From the navigation pane, choose **Limits**\.

1. Locate the resource in the list\. You can use the search fields to filter the list by resource name or resource group\. The **Current limit** column displays the current maximum for the resource for your account\.

**To view your current limits using the Service Quotas console**

1. Open the Service Quotas console at [https://console\.aws\.amazon\.com/servicequotas/home/services/ec2/quotas/](https://console.aws.amazon.com/servicequotas/home/services/ec2/quotas/)\.

1. From the navigation bar, select a Region\.

1. Use the search bar to filter the list by resource name\. For example, enter **On\-Demand** to locate the limits for On\-Demand Instances\.

1. To view more information, choose the quota name to open the details page for the quota\.

## Request an increase<a name="request-increase"></a>

You can view and manage your limits for each Region\.

**To request an increase using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select a Region\.

1. From the navigation pane, choose **Limits**\.

1. Select the resource from the list and choose **Request limit increase**\.

1. Complete the required fields on the limit increase form and choose **Submit**\. We'll respond to you using the contact method that you specified\.

**To request an increase using the Service Quotas console**

1. Open the Service Quotas console at [https://console\.aws\.amazon\.com/servicequotas/home/services/ec2/quotas/](https://console.aws.amazon.com/servicequotas/home/services/ec2/quotas/)\.

1. From the navigation bar, select a Region\.

1. Use the search bar to filter the list by resource name\.

1. If the quota is adjustable, select the quota and choose **Request quota increase**\.

1. Enter the new quota value and choose **Request**\.

   You can track your request case using the AWS Support console\. For more information, see [Requesting a quota increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-quota-increase.html) in the *Service Quotas User Guide*\.

## Restriction on email sent using port 25<a name="port-25-throttle"></a>

On all instances, Amazon EC2 restricts outbound traffic to public IP addresses over port 25 by default\. You can request that this restriction be removed\. For more information, see [How do I remove the restriction on port 25 from my Amazon EC2 instance or AWS Lambda function?](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-port-25-throttle/) in the AWS Knowledge Center\.

**Note**  
This restriction does not apply to outbound traffic sent over port 25 to:  
IP addresses in the primary CIDR block of the VPC in which the originating network interface exists\.
IP addresses in the CIDRs defined in [ RFC 1918](https://datatracker.ietf.org/doc/html/rfc1918), [RFC 6598](https://datatracker.ietf.org/doc/html/rfc6598), and [ RFC 4193](https://datatracker.ietf.org/doc/html/rfc4193)\.