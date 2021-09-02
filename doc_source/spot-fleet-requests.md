# Spot Fleet request types<a name="spot-fleet-requests"></a>

There are two types of Spot Fleet requests:

`request`  
If you configure the request type as `request`, Spot Fleet places an asynchronous one\-time request for your desired capacity\. Thereafter, if capacity is diminished because of Spot interruptions, the fleet does not attempt to replenish Spot Instances, nor does it submit requests in alternative Spot capacity pools if capacity is unavailable\.

`maintain`  
If you configure the request type as `maintain`, Spot Fleet places an asynchronous request for your desired capacity, and maintains capacity by automatically replenishing any interrupted Spot Instances\.

To specify the type of request in the Amazon EC2 console, do the following when creating a Spot Fleet request:
+ To create a Spot Fleet of type `request`, clear the **Maintain target capacity** check box\.
+ To create a Spot Fleet of type `maintain`, select the **Maintain target capacity** check box\.

For more information, see [Create a Spot Fleet request using defined parameters \(console\)](work-with-spot-fleets.md#create-spot-fleet-advanced)\.

Both types of requests benefit from an allocation strategy\. For more information, see [Allocation strategy for Spot Instances](spot-fleet-allocation-strategy.md)\.