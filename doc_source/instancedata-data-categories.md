# Instance metadata categories<a name="instancedata-data-categories"></a>

Instance metadata is divided into categories\. To retrieve instance metadata, you specify the category in the request, and the metadata is returned in the response\.

When new categories are released, a new instance metadata build is created with a new version number\. In the following table, the **Version when category was released** column specifies the build version when an instance metadata category was released\. To avoid having to update your code every time Amazon EC2 releases a new instance metadata build, use `latest` instead of the version number in your metadata requests\. For more information, see [Get the available versions of the instance metadata](instancedata-data-retrieval.md#instance-metadata-ex-1)\.

When Amazon EC2 releases a new instance metadata category, the instance metadata for the new category might not be available for existing instances\. With instances built on the [Nitro system](instance-types.md#ec2-nitro-instances), you can retrieve instance metadata only for the categories that were available at launch\. For instances with the Xen hypervisor, you can [stop and then start](Stop_Start.md) the instance to update the categories that are available for the instance\.

The following table lists the categories of instance metadata\. Some of the category names include placeholders for data that is unique to your instance\. For example, *mac* represents the MAC address for the network interface\. You must replace the placeholders with actual values when you retrieve the instance metadata\.


| Category | Description | Version when category was released | 
| --- | --- | --- | 
| ami\-id  | The AMI ID used to launch the instance\. | 1\.0 | 
| ami\-launch\-index  | If you started more than one instance at the same time, this value indicates the order in which the instance was launched\. The value of the first instance launched is 0\. | 1\.0 | 
| ami\-manifest\-path  | The path to the AMI manifest file in Amazon S3\. If you used an Amazon EBS\-backed AMI to launch the instance, the returned result is unknown\. | 1\.0 | 
| ancestor\-ami\-ids  | The AMI IDs of any instances that were rebundled to create this AMI\. This value will only exist if the AMI manifest file contained an ancestor\-amis key\. | 2007\-10\-10 | 
| autoscaling/target\-lifecycle\-state |  Value showing the target Auto Scaling lifecycle state that an Auto Scaling instance is transitioning to\. Present when the instance transitions to one of the target lifecycle states after March 10, 2022\. Possible values: `Detached` \| `InService` \| `Standby` \| `Terminated` \| `Warmed:Hibernated` \| `Warmed:Running` \| `Warmed:Stopped` \| `Warmed:Terminated`\. See [Retrieve the target lifecycle state through instance metadata](https://docs.aws.amazon.com/autoscaling/ec2/userguide/retrieving-target-lifecycle-state-through-imds.html) in the *Amazon EC2 Auto Scaling User Guide*\.   | 2021\-07\-15 | 
| block\-device\-mapping/ami  | The virtual device that contains the root/boot file system\. | 2007\-12\-15 | 
| block\-device\-mapping/ebsN  | The virtual devices associated with any Amazon EBS volumes\. Amazon EBS volumes are only available in metadata if they were present at launch time or when the instance was last started\. The N indicates the index of the Amazon EBS volume \(such as ebs1 or ebs2\)\. | 2007\-12\-15 | 
| block\-device\-mapping/ephemeralN  | The virtual devices for any non\-NVMe instance store volumes\. The N indicates the index of each volume\. The number of instance store volumes in the block device mapping might not match the actual number of instance store volumes for the instance\. The instance type determines the number of instance store volumes that are available to an instance\. If the number of instance store volumes in a block device mapping exceeds the number available to an instance, the additional instance store volumes are ignored\. | 2007\-12\-15 | 
| block\-device\-mapping/root  | The virtual devices or partitions associated with the root devices or partitions on the virtual device, where the root \(/ or C:\) file system is associated with the given instance\. | 2007\-12\-15 | 
| block\-device\-mapping/swap  | The virtual devices associated with swap\. Not always present\. | 2007\-12\-15 | 
| elastic\-gpus/associations/elastic\-gpu\-id | If there is an Elastic GPU attached to the instance, contains a JSON string with information about the Elastic GPU, including its ID and connection information\. | 2016\-11\-30 | 
| elastic\-inference/associations/eia\-id | If there is an Elastic Inference accelerator attached to the instance, contains a JSON string with information about the Elastic Inference accelerator, including its ID and type\. | 2018\-11\-29 | 
| events/maintenance/history | If there are completed or canceled maintenance events for the instance, contains a JSON string with information about the events\. For more information, see [To view event history about completed or canceled events](monitoring-instances-status-check_sched.md#viewing-event-history)\. | 2018\-08\-17 | 
| events/maintenance/scheduled | If there are active maintenance events for the instance, contains a JSON string with information about the events\. For more information, see [View scheduled events](monitoring-instances-status-check_sched.md#viewing_scheduled_events)\. | 2018\-08\-17 | 
| events/recommendations/rebalance | The approximate time, in UTC, when the EC2 instance rebalance recommendation notification is emitted for the instance\. The following is an example of the metadata for this category: \{"noticeTime": "2020\-11\-05T08:22:00Z"\}\. This category is available only after the notification is emitted\. For more information, see [EC2 instance rebalance recommendations](rebalance-recommendations.md)\. | 2020\-11\-04 | 
| hostname | If the EC2 instance is using IP\-based naming \(IPBN\), this is the private IPv4 DNS hostname of the instance\. If the EC2 instance is using Resource\-based naming \(RBN\), this is the RBN\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. For more information about IPBN and RBN, see [Amazon EC2 instance hostname types](ec2-instance-naming.md)\. | 1\.0 | 
|  iam/info  | If there is an IAM role associated with the instance, contains information about the last time the instance profile was updated, including the instance's LastUpdated date, InstanceProfileArn, and InstanceProfileId\. Otherwise, not present\. | 2012\-01\-12 | 
|  iam/security\-credentials/role\-name  | If there is an IAM role associated with the instance, role\-name is the name of the role, and role\-name contains the temporary security credentials associated with the role \(for more information, see [Retrieve security credentials from instance metadata](iam-roles-for-amazon-ec2.md#instance-metadata-security-credentials)\)\. Otherwise, not present\. | 2012\-01\-12 | 
| identity\-credentials/ec2/info | \[Internal use only\] Information about the credentials in identity\-credentials/ec2/security\-credentials/ec2\-instance\. These credentials are used by AWS features such as EC2 Instance Connect, and do not have any additional AWS API permissions or privileges beyond identifying the instance\. | 2018\-05\-23 | 
| identity\-credentials/ec2/security\-credentials/ec2\-instance | \[Internal use only\] Credentials that allow on\-instance software to identify itself to AWS to support features such as EC2 Instance Connect\. These credentials do not have any additional AWS API permissions or privileges\. | 2018\-05\-23 | 
| instance\-action | Notifies the instance that it should reboot in preparation for bundling\. Valid values: none \| shutdown \| bundle\-pending\. | 2008\-09\-01 | 
| instance\-id | The ID of this instance\. | 1\.0 | 
| instance\-life\-cycle | The purchasing option of this instance\. For more information, see [Instance purchasing options](instance-purchasing-options.md)\. | 2019\-10\-01 | 
| instance\-type  | The type of instance\. For more information, see [Instance types](instance-types.md)\. | 2007\-08\-29 | 
| ipv6  | The IPv6 address of the instance\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\) network interface and the first IPv6 address assigned\. If no IPv6 address exists on network interface\[0\], this item is not set and results in an HTTP 404 response\. | 2021\-01\-03 | 
|  kernel\-id  | The ID of the kernel launched with this instance, if applicable\. | 2008\-02\-01 | 
|  local\-hostname  | In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. If the EC2 instance is using IP\-based naming \(IPBN\), this is the private IPv4 DNS hostname of the instance\. If the EC2 instance is using Resource\-based naming \(RBN\), this is the RBN\. For more information about IPBN, RBN, and EC2 instance naming, see [Amazon EC2 instance hostname types](ec2-instance-naming.md)\. | 2007\-01\-19 | 
|  local\-ipv4  | The private IPv4 address of the instance\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. If this is an IPv6\-only instance, this item is not set and results in an HTTP 404 response\. | 1\.0 | 
|  mac  | The instance's media access control \(MAC\) address\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. | 2011\-01\-01 | 
| metrics/vhostmd | No longer available\. | 2011\-05\-01 | 
|  network/interfaces/macs/mac/device\-number  | The unique device number associated with that interface\. The device number corresponds to the device name; for example, a device\-number of 2 is for the eth2 device\. This category corresponds to the DeviceIndex and device\-index fields that are used by the Amazon EC2 API and the EC2 commands for the AWS CLI\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/interface\-id  | The ID of the network interface\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/ipv4\-associations/public\-ip  | The private IPv4 addresses that are associated with each public IP address and assigned to that interface\. | 2011\-01\-01 | 
| network/interfaces/macs/mac/ipv6s | The IPv6 addresses associated with the interface\. Returned only for instances launched into a VPC\.  | 2016\-06\-30  | 
|  network/interfaces/macs/mac/local\-hostname  |  The private IPv4 DNS hostname of the instance\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. If this is a IPv6\-only instance, this is the resource\-based name\. For more information about IPBN and RBN, see [Amazon EC2 instance hostname types](ec2-instance-naming.md)\.  | 2007\-01\-19 | 
|  network/interfaces/macs/mac/local\-ipv4s  | The private IPv4 addresses associated with the interface\. If this is an IPv6\-only network interface, this item is not set and results in an HTTP 404 response\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/mac  | The instance's MAC address\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/network\-card\-index  | The index of the network card\. Some instance types support multiple network cards\. | 2020\-11\-01 | 
| network/interfaces/macs/mac/owner\-id  | The ID of the owner of the network interface\. In multiple\-interface environments, an interface can be attached by a third party, such as Elastic Load Balancing\. Traffic on an interface is always billed to the interface owner\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/public\-hostname  | The interface's public DNS \(IPv4\)\. This category is only returned if the enableDnsHostnames attribute is set to true\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the Amazon VPC User Guide\. If the instance only has a public\-IPv6 address and no public\-IPv4 address, this item is not set and results in an HTTP 404 response\. |  2011\-01\-01 | 
|  network/interfaces/macs/mac/public\-ipv4s  | The public IP address or Elastic IP addresses associated with the interface\. There may be multiple IPv4 addresses on an instance\.  | 2011\-01\-01 | 
| network/interfaces/macs/mac/security\-groups  | Security groups to which the network interface belongs\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/security\-group\-ids  | The IDs of the security groups to which the network interface belongs\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/subnet\-id  | The ID of the subnet in which the interface resides\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/subnet\-ipv4\-cidr\-block  | The IPv4 CIDR block of the subnet in which the interface resides\. | 2011\-01\-01 | 
| network/interfaces/macs/mac/subnet\-ipv6\-cidr\-blocks  | The IPv6 CIDR block of the subnet in which the interface resides\. | 2016\-06\-30  | 
|  network/interfaces/macs/mac/vpc\-id  | The ID of the VPC in which the interface resides\. | 2011\-01\-01 | 
| network/interfaces/macs/mac/vpc\-ipv4\-cidr\-block  | The primary IPv4 CIDR block of the VPC\. | 2011\-01\-01 | 
| network/interfaces/macs/mac/vpc\-ipv4\-cidr\-blocks  | The IPv4 CIDR blocks for the VPC\. | 2016\-06\-30  | 
| network/interfaces/macs/mac/vpc\-ipv6\-cidr\-blocks  | The IPv6 CIDR block of the VPC in which the interface resides\. | 2016\-06\-30  | 
|  placement/availability\-zone  | The Availability Zone in which the instance launched\. | 2008\-02\-01 | 
|  placement/availability\-zone\-id  | The static Availability Zone ID in which the instance is launched\. The Availability Zone ID is consistent across accounts\. However, it might be different from the Availability Zone, which can vary by account\. | 2020\-08\-24 | 
|  placement/group\-name  | The name of the placement group in which the instance is launched\. | 2020\-08\-24 | 
|  placement/host\-id  | The ID of the host on which the instance is launched\. Applicable only to Dedicated Hosts\. | 2020\-08\-24 | 
|  placement/partition\-number  | The number of the partition in which the instance is launched\. | 2020\-08\-24 | 
|  placement/region  | The AWS Region in which the instance is launched\. | 2020\-08\-24 | 
|  product\-codes  | AWS Marketplace product codes associated with the instance, if any\.  | 2007\-03\-01 | 
|  public\-hostname  | The instance's public DNS \(IPv4\)\. This category is only returned if the enableDnsHostnames attribute is set to true\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the Amazon VPC User Guide\. If the instance only has a public\-IPv6 address and no public\-IPv4 address, this item is not set and results in an HTTP 404 response\. | 2007\-01\-19 | 
|  public\-ipv4  | The public IPv4 address\. If an Elastic IP address is associated with the instance, the value returned is the Elastic IP address\. | 2007\-01\-19 | 
|  public\-keys/0/openssh\-key  | Public key\. Only available if supplied at instance launch time\. | 1\.0 | 
|  ramdisk\-id  | The ID of the RAM disk specified at launch time, if applicable\. | 2007\-10\-10 | 
|  reservation\-id  | The ID of the reservation\. | 1\.0 | 
|  security\-groups  |  The names of the security groups applied to the instance\. After launch, you can change the security groups of the instances\. Such changes are reflected here and in network/interfaces/macs/**mac**/security\-groups\.  | 1\.0 | 
|  services/domain  |  The domain for AWS resources for the Region\.  | 2014\-02\-25 | 
|  services/partition  |  The partition that the resource is in\. For standard AWS Regions, the partition is `aws`\. If you have resources in other partitions, the partition is `aws-partitionname`\. For example, the partition for resources in the China \(Beijing\) Region is `aws-cn`\.  | 2015\-10\-20 | 
|  spot/instance\-action  |  The action \(hibernate, stop, or terminate\) and the approximate time, in UTC, when the action will occur\. This item is present only if the Spot Instance has been marked for hibernate, stop, or terminate\. For more information, see [instance\-action](spot-instance-termination-notices.md#instance-action-metadata)\.  | 2016\-11\-15 | 
|  spot/termination\-time  |  The approximate time, in UTC, that the operating system for your Spot Instance will receive the shutdown signal\. This item is present and contains a time value \(for example, 2015\-01\-05T18:02:00Z\) only if the Spot Instance has been marked for termination by Amazon EC2\. The termination\-time item is not set to a time if you terminated the Spot Instance yourself\. For more information, see [termination\-time](spot-instance-termination-notices.md#termination-time-metadata)\.  | 2014\-11\-05 | 
| tags/instance | The instance tags associated with the instance\. Only available if you explicitly allow access to tags in instance metadata\. For more information, see [Allow access to tags in instance metadata](Using_Tags.md#allow-access-to-tags-in-IMDS)\. | 2021\-03\-23 | 

## Dynamic data categories<a name="dynamic-data-categories"></a>

The following table lists the categories of dynamic data\.


| Category | Description | Version when category was released | 
| --- | --- | --- | 
| fws/instance\-monitoring  | Value showing whether the customer has enabled detailed one\-minute monitoring in CloudWatch\. Valid values: enabled \| disabled | 2009\-04\-04 | 
| instance\-identity/document  | JSON containing instance attributes, such as instance\-id, private IP address, etc\. See [Instance identity documents](instance-identity-documents.md)\.  | 2009\-04\-04 | 
| instance\-identity/pkcs7  | Used to verify the document's authenticity and content against the signature\. See [Instance identity documents](instance-identity-documents.md)\.  | 2009\-04\-04 | 
| instance\-identity/signature  | Data that can be used by other parties to verify its origin and authenticity\. See [Instance identity documents](instance-identity-documents.md)\.  | 2009\-04\-04 | 