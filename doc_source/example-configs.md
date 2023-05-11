# Example Capacity Reservation Fleet configurations<a name="example-configs"></a>

**Topics**
+ [Example 1: Reserve capacity based on vCPUs](#example1)

## Example 1: Reserve capacity based on vCPUs<a name="example1"></a>

The following example creates a Capacity Reservation Fleet that uses two instance types: `m5.4xlarge` and `m5.12xlarge`\.

It uses a weighting system based on the number of vCPUs provided by the specified instance types\. The total target capacity is `480` vCPUs\. The `m5.4xlarge` provides 16 vCPUs and gets a weight of `16`, while the `m5.12xlarge` provides 48 vCPUs and gets a weight of `48`\. This weighting system configures the Capacity Reservation Fleet to reserve capacity for either 30 `m5.4xlarge` instances \(480/16=30\), or 10 `m5.12xlarge` instances \(480/48=10\)\.

The Fleet is configured to prioritize the `m5.12xlarge` capacity and gets priority of `1`, while the `m5.4xlarge` gets a lower priority of `2`\. This means that the fleet will attempt to reserve the `m5.12xlarge` capacity first, and only attempt to reserve the `m5.4xlarge` capacity if Amazon EC2 has insufficient `m5.12xlarge` capacity\.

The Fleet reserves the capacity for `Windows` instances and the reservation automatically expires on `October 31, 2021` at `23:59:59` UTC\.

```
$ aws ec2 create-capacity-reservation-fleet \
--total-target-capacity 48 \
--allocation-strategy prioritized \
--instance-match-criteria open \
--tenancy default \
--end-date 2021-10-31T23:59:59.000Z \
--instance-type-specifications file://instanceTypeSpecification.json
```

The following is the contents of `instanceTypeSpecification.json`\.

```
[
    {             
        "InstanceType": "m5.4xlarge",                        
        "InstancePlatform":"Windows",            
        "Weight": 16,
        "AvailabilityZone":"us-east-1a",        
        "EbsOptimized": true,            
        "Priority" : 2
    },
    {             
        "InstanceType": "m5.12xlarge",                        
        "InstancePlatform":"Windows",            
        "Weight": 48,
        "AvailabilityZone":"us-east-1a",        
        "EbsOptimized": true,            
        "Priority" : 1
	}
]
```