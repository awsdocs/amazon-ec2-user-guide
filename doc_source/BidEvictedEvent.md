# Determine whether Amazon EC2 interrupted a Spot Instance<a name="BidEvictedEvent"></a>

If a Spot Instance is stopped, hibernated, or terminated, you can use CloudTrail to see whether Amazon EC2 interrupted the Spot Instance\. In AWS CloudTrail, the event name `BidEvictedEvent` indicates that Amazon EC2 interrupted the Spot Instance\.



**To view BidEvictedEvent events in CloudTrail**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. In the navigation pane, choose **Event history**\.

1. In the filter drop\-down, choose **Event name**, and then in the filter field to the right, enter **BidEvictedEvent**\.

1. Choose **BidEvictedEvent** in the resulting list to view its details\. Under **Event record**, you can find the instance ID\.

For more information about using CloudTrail, see [Log Amazon EC2 and Amazon EBS API calls with AWS CloudTrail](monitor-with-cloudtrail.md)\.