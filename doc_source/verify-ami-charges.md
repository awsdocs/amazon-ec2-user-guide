# Verify AMI charges on your bill<a name="verify-ami-charges"></a>

To ensure that you're not incurring unplanned costs, you can verify that the billing information for an instance in your AWS Cost and Usage Report \(CUR\) matches the billing information that's associated with the AMI that you used to launch the instance\.

To verify the billing information, find the instance ID in your CUR and check the corresponding value in the `[lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation)` column\. That value should match the value for **Usage operation** that's associated with the AMI\.

For example, the AMI `ami-0123456789EXAMPLE` has the following billing information:
+ **Platform details** = `Red Hat Enterprise Linux`
+ **Usage operation** = `RunInstances:0010`

If you launched an instance using this AMI, you can find the instance ID in your CUR, and check the corresponding value in the `[lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation)` column\. In this example, the value should be `RunInstances:0010`\.