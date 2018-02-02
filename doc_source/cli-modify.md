# Modifying an EBS Volume from the Command Line<a name="cli-modify"></a>

The following example demonstrates how an EBS volume can be modified from the command line using the AWS CLI\. Depending on your default configuration, you may need to specify information such as Region and Availability Zone\. The ID of the source volume being modified is required, and you must have appropriate permissions to carry out the action\. When an `io1` volume is the modification target, you must specify its level of provisioned IOPS\. Multiple modification actions \(to change capacity, IOPS, or type\) may be performed in a single command\.

<a name="cli-modify-size"></a>For example, an EBS volume is configured as follows:

+ Volume ID: `vol-11111111111111111`

+ Volume size: 100 GiB

+ Volume type: `gp2`

You can change the volume configuration to the following:

+ Volume size: 200 GiB

+ Volume type: `io1`

+ Provisioning level: 10,000 IOPS

Apply the above modifications with the following command:

```
aws ec2 modify-volume --region us-east-1 --volume-id vol-11111111111111111 --size 200 --volume-type io1 --iops 10000
```

The command yields output similar to the following:

```
{
    "VolumeModification": {
        "TargetSize": 200,
        "TargetVolumeType": "io1",
        "ModificationState": "modifying",
        "VolumeId": "vol-11111111111111111",
        "TargetIops": 10000,
        "StartTime": "2017-01-19T22:21:02.959Z",
        "Progress": 0,
        "OriginalVolumeType": "gp2",
        "OriginalIops": 300,
        "OriginalSize": 100
    }
}
```

**Note**  
Modifying volume size has no practical effect until you also extend the volume's file system to make use of the new storage capacity\. For more information, see [Extending a Linux File System after Resizing the Volume](recognize-expanded-volume-linux.md)\.