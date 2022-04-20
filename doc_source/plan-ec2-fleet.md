# Plan an EC2 Fleet<a name="plan-ec2-fleet"></a>

When planning your EC2 Fleet, we recommend that you do the following:
+ Determine whether you want to create an EC2 Fleet that submits a synchronous or asynchronous one\-time request for the desired target capacity, or one that maintains a target capacity over time\. For more information, see [EC2 Fleet request types](ec2-fleet-request-type.md)\.
+ Determine the instance types that meet your application requirements\.
+ If you plan to include Spot Instances in your EC2 Fleet, review [Spot Best Practices](https://aws.amazon.com/ec2/spot/getting-started/#bestpractices) before you create the fleet\. Use these best practices when you plan your fleet so that you can provision the instances at the lowest possible price\.
+ Determine the target capacity for your EC2 Fleet\. You can set target capacity in instances or in custom units\. For more information, see [EC2 Fleet instance weighting](ec2-fleet-instance-weighting.md)\.
+ Determine what portion of the EC2 Fleet target capacity must be On\-Demand capacity and Spot capacity\. You can specify 0 for On\-Demand capacity or Spot capacity, or both\.
+ Determine your price per unit, if you are using instance weighting\. To calculate the price per unit, divide the price per instance hour by the number of units \(or weight\) that this instance represents\. If you are not using instance weighting, the default price per unit is the price per instance hour\.
+ Determine the maximum amount per hour that you’re willing to pay for your fleet\. For more information, see [Control spending](ec2-fleet-control-spending.md)\.
+ Review the possible options for your EC2 Fleet\. For information about the fleet parameters, see [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) in the *AWS CLI Command Reference*\. For EC2 Fleet configuration examples, see [EC2 Fleet example configurations](ec2-fleet-examples.md)\.