# Fleet quotas<a name="fleet-quotas"></a>

The usual Amazon EC2 quotas \(formerly referred to as limits\) apply to instances launched by an EC2 Fleet or a Spot Fleet, such as [Spot Instance limits](using-spot-limits.md) and [volume limits](volume_limits.md)\.

In addition, the following quotas apply:


****  

| Quota description | Quota | 
| --- | --- | 
| The number of EC2 Fleets and Spot Fleets per Region in the active, deleted\_running, and cancelled\_running states | 1,000 ¹ ² ³ | 
| The number of Spot capacity pools \(unique combination of instance type and subnet\) | 300 ¹ ⁴ | 
| The size of the user data in a launch specification | 16 KB ² | 
| The target capacity per EC2 Fleet or Spot Fleet | 10,000 | 
| The target capacity across all EC2 Fleets and Spot Fleets in a Region | 100,000 ¹ | 
|  An EC2 Fleet request or a Spot Fleet request can't span Regions\.  |  | 
| An EC2 Fleet request or a Spot Fleet request can't span different subnets from the same Availability Zone\. |  | 

¹ These quotas apply to both your EC2 Fleets and your Spot Fleets\.

² These are hard quotas\. You cannot request an increase for these quotas\.

³ After you delete an EC2 Fleet or cancel a Spot Fleet request, and if you specified that the fleet should *not* terminate its Spot Instances when you deleted or canceled the request, the fleet request enters the `deleted_running` \(EC2 Fleet\) or `cancelled_running` \(Spot Fleet\) state and the instances continue to run until they are interrupted or you terminate them manually\. If you terminate the instances, the fleet request enters the `deleted_terminating` \(EC2 Fleet\) or `cancelled_terminating` \( Spot Fleet\) state and does not count towards this quota\. For more information, see [Delete an EC2 Fleet](manage-ec2-fleet.md#delete-fleet) and [Cancel a Spot Fleet request](work-with-spot-fleets.md#cancel-spot-fleet)\.

⁴ This quota only applies to fleets of type `request` or `maintain`\. This quota does not apply to `instant` fleets\.

## Request a quota increase for target capacity<a name="fleet-quota-increase-request"></a>

If you need more than the default quota for target capacity, you can request a quota increase\.

**To request a quota increase for target capacity**

1. Open the AWS Support Center [Create case](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-fleet) form\.

1. Choose **Service limit increase**\.

1. For **Limit type**, choose **EC2 Fleet**\.

1. For **Region**, choose the AWS Region in which to request the quota increase\.

1. For **Limit**, choose **Target Fleet Capacity per Fleet \(in units\)** or **Target Fleet Capacity per Region \(in units\)**, depending on which quota you want to increase\.

1. For **New limit value**, enter the new quota value\.

1. To request an increase for another quota, choose **Add another request**, and repeat Steps 4–6\.

1. For **Use case description**, enter your reason for requesting a quota increase\.

1. Under **Contact options**, specify your preferred contact language and contact method\.

1. Choose **Submit**\.