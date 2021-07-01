# Fleet quotas<a name="fleet-quotas"></a>

The usual Amazon EC2 quotas apply to instances launched by an EC2 Fleet or a Spot Fleet, such as [Spot Instance limits](using-spot-limits.md) and [volume limits](volume_limits.md)\. In addition, the following limits apply:
+ The number of active EC2 Fleets and Spot Fleets per Region: 1,000\* †
+ The number of Spot capacity pools \(unique combination of instance type and subnet\): 300\* ‡
+ The size of the user data in a launch specification: 16 KB †
+ The target capacity per EC2 Fleet or Spot Fleet: 10,000
+ The target capacity across all EC2 Fleets and Spot Fleets in a Region: 100,000\*
+ An EC2 Fleet request or a Spot Fleet request can't span Regions\.
+ An EC2 Fleet request or a Spot Fleet request can't span different subnets from the same Availability Zone\.

If you need more than the default limits for target capacity, complete the AWS Support Center [Create case](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-fleet) form to request a limit increase\. For **Limit type**, choose **EC2 Fleet**, choose a Region, and then choose **Target Fleet Capacity per Fleet \(in units\)** or **Target Fleet Capacity per Region \(in units\)**, or both\.

\* These limits apply to both your EC2 Fleets and your Spot Fleets\.

† These are hard limits\. You cannot request a limit increase for these limits\.

‡ This limit only applies to fleets of type `request` or `maintain`\. This limit does not apply to `instant` fleets\.