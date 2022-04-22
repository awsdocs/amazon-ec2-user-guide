# Troubleshoot changing the instance type<a name="troubleshoot-change-instance-type"></a>

Use the following information to help diagnose and fix issues that you might encounter when changing the instance type\.

## Instance won't start after changing instance type<a name="troubleshoot-change-instance-type-no-start"></a>

**Possible cause: Requirements for new instance type not met**  
If your instance won't boot, it is possible that one of the requirements for the new instance type was not met\. For more information, see [Why is my Linux instance not booting after I changed its type?](https://aws.amazon.com/premiumsupport/knowledge-center/boot-error-linux-nitro-instance/)

**Possible cause: AMI does not support instance type**  
If you use the EC2 console to change the instance type, only the instance types that are supported by the selected AMI are available\. However, if you use the AWS CLI to launch an instance, you can specify an incompatible AMI and instance type\. If the AMI and instance type are incompatible, the instance can't start\. For more information, see [Compatibility for changing the instance type](resize-limitations.md)\.

**Possible cause: Instance is in cluster placement group**  
If your instance is in a [cluster placement group](placement-groups.md#placement-groups-cluster) and, after changing the instance type, the instance fails to start, try the following:  

1. Stop all the instances in the cluster placement group\.

1. Change the instance type of the affected instance\.

1. Start all the instances in the cluster placement group\.

## Application or website not reachable from the internet after changing instance type<a name="troubleshoot-change-instance-type-ipv4"></a>

**Possible cause: Public IPv4 address is released**  
When you change the instance type, you must first stop the instance\. When you stop an instance, we release the public IPv4 address and give your instance a new public IPv4 address\.  
To retain the public IPv4 address between instance stops and starts, we recommend that you use an Elastic IP address, at no extra cost provided your instance is running\. For more information, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\.