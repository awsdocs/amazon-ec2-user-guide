# Spot Instance request example launch specifications<a name="spot-request-examples"></a>

The following examples show launch configurations that you can use with the [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) command to create a Spot Instance request\. For more information, see [Create a Spot Instance request](spot-requests.md#using-spot-instances-request)\.

1. [Launch Spot Instances](#spot-launch-specification1)

1. [Launch Spot Instances in the specified Availability Zone](#spot-launch-specification2)

1. [Launch Spot Instances in the specified subnet](#spot-launch-specification3)

1. [Launch a Dedicated Spot Instance](#spot-launch-specification4)

## Example 1: Launch Spot Instances<a name="spot-launch-specification1"></a>

The following example does not include an Availability Zone or subnet\. Amazon EC2 selects an Availability Zone for you\. Amazon EC2 launches the instances in the default subnet of the selected Availability Zone\.

```
{
  "ImageId": "ami-1a2b3c4d",
  "KeyName": "my-key-pair",
  "SecurityGroupIds": [ "sg-1a2b3c4d" ],
  "InstanceType": "m3.medium",
  "IamInstanceProfile": {
      "Arn": "arn:aws:iam::123456789012:instance-profile/my-iam-role"
  }
}
```

## Example 2: Launch Spot Instances in the specified Availability Zone<a name="spot-launch-specification2"></a>

The following example includes an Availability Zone\. Amazon EC2 launches the instances in the default subnet of the specified Availability Zone\.

```
{
  "ImageId": "ami-1a2b3c4d",
  "KeyName": "my-key-pair",
  "SecurityGroupIds": [ "sg-1a2b3c4d" ],
  "InstanceType": "m3.medium",
  "Placement": {
    "AvailabilityZone": "us-west-2a"
  },
  "IamInstanceProfile": {
      "Arn": "arn:aws:iam::123456789012:instance-profile/my-iam-role"
  }
}
```

## Example 3: Launch Spot Instances in the specified subnet<a name="spot-launch-specification3"></a>

The following example includes a subnet\. Amazon EC2 launches the instances in the specified subnet\. If the VPC is a nondefault VPC, the instance does not receive a public IPv4 address by default\.

```
{
  "ImageId": "ami-1a2b3c4d",
  "SecurityGroupIds": [ "sg-1a2b3c4d" ],
  "InstanceType": "m3.medium",
  "SubnetId": "subnet-1a2b3c4d",
  "IamInstanceProfile": {
      "Arn": "arn:aws:iam::123456789012:instance-profile/my-iam-role"
  }
}
```

To assign a public IPv4 address to an instance in a nondefault VPC, specify the `AssociatePublicIpAddress` field as shown in the following example\. When you specify a network interface, you must include the subnet ID and security group ID using the network interface, rather than using the `SubnetId` and `SecurityGroupIds` fields shown in example 3\.

```
{
  "ImageId": "ami-1a2b3c4d",
  "KeyName": "my-key-pair",
  "InstanceType": "m3.medium",
  "NetworkInterfaces": [
    {
      "DeviceIndex": 0,
      "SubnetId": "subnet-1a2b3c4d",
      "Groups": [ "sg-1a2b3c4d" ],
      "AssociatePublicIpAddress": true
    }
  ],
  "IamInstanceProfile": {
      "Arn": "arn:aws:iam::123456789012:instance-profile/my-iam-role"
  }
}
```

## Example 4: Launch a Dedicated Spot Instance<a name="spot-launch-specification4"></a>

The following example requests Spot Instance with a tenancy of `dedicated`\. A Dedicated Spot Instance must be launched in a VPC\.

```
{
  "ImageId": "ami-1a2b3c4d",
  "KeyName": "my-key-pair",
  "SecurityGroupIds": [ "sg-1a2b3c4d" ],
  "InstanceType": "c3.8xlarge",
  "SubnetId": "subnet-1a2b3c4d",
  "Placement": {
    "Tenancy": "dedicated"
  }
}
```