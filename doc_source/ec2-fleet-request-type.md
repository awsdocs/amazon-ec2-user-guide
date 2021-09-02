# EC2 Fleet request types<a name="ec2-fleet-request-type"></a>

There are three types of EC2 Fleet requests: 

`instant`  
If you configure the request type as `instant`, EC2 Fleet places a synchronous one\-time request for your desired capacity\. In the API response, it returns the instances that launched, along with errors for those instances that could not be launched\. For more information, see [Use an EC2 Fleet of type 'instant'](instant-fleet.md)\.

`request`  
If you configure the request type as `request`, EC2 Fleet places an asynchronous one\-time request for your desired capacity\. Thereafter, if capacity is diminished because of Spot interruptions, the fleet does not attempt to replenish Spot Instances, nor does it submit requests in alternative Spot capacity pools if capacity is unavailable\.

`maintain`  
\(Default\) If you configure the request type as `maintain`, EC2 Fleet places an asynchronous request for your desired capacity, and maintains capacity by automatically replenishing any interrupted Spot Instances\.

All three types of requests benefit from an allocation strategy\. For more information, see [Allocation strategies for Spot Instances](ec2-fleet-allocation-strategy.md)\.