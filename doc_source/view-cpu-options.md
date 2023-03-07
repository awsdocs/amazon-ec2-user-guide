# View the CPU options for your instance<a name="view-cpu-options"></a>

You can view the CPU options for an existing instance in the Amazon EC2 console or by describing the instance using the AWS CLI\.

------
#### [ Console ]

**To view the CPU options for an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances** and select the instance\.

1. On the **Details** tab, under **Host and placement group**, find **Number of vCPUs**\.

------
#### [ AWS CLI ]

**To view the CPU options for an instance \(AWS CLI\)**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\.

```
aws ec2 describe-instances --instance-ids i-123456789abcde123
```

```
...
    "Instances": [
        {
            "Monitoring": {
                "State": "disabled"
            }, 
            "PublicDnsName": "ec2-198-51-100-5.eu-central-1.compute.amazonaws.com", 
            "State": {
                "Code": 16, 
                "Name": "running"
            }, 
            "EbsOptimized": false, 
            "LaunchTime": "2018-05-08T13:40:33.000Z", 
            "PublicIpAddress": "198.51.100.5", 
            "PrivateIpAddress": "172.31.2.206", 
            "ProductCodes": [], 
            "VpcId": "vpc-1a2b3c4d", 
            "CpuOptions": {
                "CoreCount": 34, 
                "ThreadsPerCore": 1
            }, 
            "StateTransitionReason": "", 
            ...
        }
    ]
...
```

In the output that's returned, the `CoreCount` field indicates the number of cores for the instance\. The `ThreadsPerCore` field indicates the number of threads per core\.

------

Alternatively, connect to your instance and use a tool such as lscpu to view the CPU information for your instance\.

You can use AWS Config to record, assess, audit, and evaluate configuration changes for instances, including terminated instances\. For more information, see [Getting Started with AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/getting-started.html) in the *AWS Config Developer Guide*\.