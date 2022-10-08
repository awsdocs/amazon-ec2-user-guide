# Deprecate an AMI<a name="ami-deprecate"></a>

You can deprecate an AMI to indicate that it is out of date and should not be used\. You can also specify a future deprecation date for an AMI, indicating when the AMI will be out of date\. For example, you might deprecate an AMI that is no longer actively maintained, or you might deprecate an AMI that has been superseded by a newer version\. By default, deprecated AMIs do not appear in AMI listings, preventing new users from using out\-of\-date AMIs\. However, existing users and launch services, such as launch templates and Auto Scaling groups, can continue to use a deprecated AMI by specifying its ID\. To delete the AMI so that users and services cannot use it, you must [deregister](deregister-ami.md) it\.

After an AMI is deprecated:
+ For AMI users, the deprecated AMI does not appear in [DescribeImages](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeImages.html) API calls unless you specify its ID or specify that deprecated AMIs must appear\. AMI owners continue to see deprecated AMIs in [DescribeImages](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeImages.html) API calls\.
+ For AMI users, the deprecated AMI is not available to select via the EC2 console\. For example, a deprecated AMI does not appear in the AMI catalog in the launch instance wizard\. AMI owners continue to see deprecated AMIs in the EC2 console\.
+ For AMI users, if you know the ID of a deprecated AMI, you can continue to launch instances using the deprecated AMI by using the API, CLI, or the SDKs\.
+ Launch services, such as launch templates and Auto Scaling groups, can continue to reference deprecated AMIs\.
+ EC2 instances that were launched using an AMI that is subsequently deprecated are not affected, and can be stopped, started, and rebooted\.

You can deprecate both private and public AMIs\.

You can also create Amazon Data Lifecycle Manager EBS\-backed AMI policies to automate the deprecation of EBS\-backed AMIs\. For more information, see [Automate AMI lifecycles](ami-policy.md)\.

**Note**  
By default, the deprecation date of all public AMIs is set to two years from the AMI creation date\. You can set the deprecation date to earlier than two years\. To cancel the deprecation date, or to move the deprecation to a later date, you must make the AMI private by only [sharing it with specific AWS accounts](sharingamis-explicit.md)\.

**Topics**
+ [Costs](#ami-deprecate-costs)
+ [Limitations](#limitations)
+ [Deprecate an AMI](#deprecate-ami)
+ [Describe deprecated AMIs](#describe-deprecate-ami)
+ [Cancel the deprecation of an AMI](#cancel-deprecate-ami)

## Costs<a name="ami-deprecate-costs"></a>

When you deprecate an AMI, the AMI is not deleted\. The AMI owner continues to pay for the AMI's snapshots\. To stop paying for the snapshots, the AMI owner must delete the AMI by [deregistering](deregister-ami.md) it\.

## Limitations<a name="limitations"></a>
+ To deprecate an AMI, you must be the owner of the AMI\.

## Deprecate an AMI<a name="deprecate-ami"></a>

You can deprecate an AMI on a specific date and time\. You must be the AMI owner to perform this procedure\.

------
#### [ Console ]

**To deprecate an AMI on a specific date**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigator, choose **AMIs**\.

1. From the filter bar, choose **Owned by me**\.

1. Select the AMI, and then choose **Actions**, **Manage AMI Deprecation**\. You can select multiple AMIs to set the same deprecation date of several AMIs at once\.

1. Select the **Enable** check box, and then enter the deprecation date and time\. 

   The upper limit for the deprecation date is 10 years from now, except for public AMIs, where the upper limit is 2 years from the creation date\. You can’t specify a date in the past\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

**To deprecate an AMI on a specific date**  
Use the [enable\-image\-deprecation](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-image-deprecation.html) command\. Specify the ID of the AMI and the date and time on which to deprecate the AMI\. If you specify a value for seconds, Amazon EC2 rounds the seconds to the nearest minute\.

The upper limit for `deprecate-at` is 10 years from now, except for public AMIs, where the upper limit is 2 years from the creation date\. You can’t specify a date in the past\. 

```
aws ec2 enable-image-deprecation \
    --image-id ami-1234567890abcdef0 \
    --deprecate-at "2021-10-15T13:17:12.000Z"
```

Expected output

```
{
  "Return": "true"
}
```

------

### Last launched time<a name="ami-last-launched-time"></a>

`LastLaunchedTime` is a timestamp that indicates when your AMI was last used to launch an instance\. AMIs that have not been used recently to launch an instance might be good candidates for deprecation or [deregistering](deregister-ami.md)\.

**Note**  
When an AMI is used to launch an instance, there is a 24\-hour delay before that usage is reported\. 
`lastLaunchedTime` data is available starting April 2017\. 

------
#### [ Console ]

**To view the last launched time of an AMI**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigator, choose **AMIs**\.

1. From the filter bar, choose **Owned by me**\.

1. Select the AMI, and then check the **Last launched time** field \(if you selected the check box next to the AMI, it's located on the **Details** tab\)\. The field shows the date and time when the AMI was last used to launch an instance\.

------
#### [ AWS CLI ]

**To view the last launched time of an AMI**  
Run the [describe\-image\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-image-attribute.html) command and specify `--attribute lastLaunchedTime`\. You must be the AMI owner to run this command\.

```
aws ec2 describe-image-attribute \
    --image-id ami-1234567890example \
    --attribute lastLaunchedTime
```

Example output

```
{
    "LastLaunchedTime": {
        "Value": "2022-02-10T02:03:18Z"
    },
    "ImageId": "ami-1234567890example",
}
```

------

## Describe deprecated AMIs<a name="describe-deprecate-ami"></a>

You can view the deprecation date and time of an AMI, and filter all the AMIs by deprecation date\. You can also use the AWS CLI to describe all the AMIs that have been deprecated, where the deprecation date is in the past\.

------
#### [ Console ]

**To view the deprecation date of an AMI**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigator, choose **AMIs**, and then select the AMI\.

1. Check the **Deprecation time** field \(if you selected the check box next to the AMI, it's located on the **Details** tab\)\. The field shows the deprecation date and time of the AMI\. If the field is empty, the AMI is not deprecated\.

**To filter AMIs by deprecation date**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigator, choose **AMIs**\.

1. From the filter bar, choose **Owned by me** or **Private images** \(private images include AMIs that are shared with you as well as owned by you\)\.

1. In the Search bar, enter **Deprecation time** \(as you enter the letters, the **Deprecation time** filter appears\), and then choose an operator and a date and time\.

------
#### [ AWS CLI ]

When you describe all AMIs using the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command, the results are different depending on whether you are an AMI user or the AMI owner\.
+ If you are an AMI user: 

  By default, when you describe all AMIs using the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command, deprecated AMIs that are not owned by you, but which are shared with you, do not appear in the results\. This is because the default is `--no-include-deprecated`\. To include deprecated AMIs in the results, you must specify the `--include-deprecated` parameter\.
+ If you are the AMI owner:

  When you describe all AMIs using the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command, all the AMIs that you own, including deprecated AMIs, appear in the results\. You do not need to specify the `--include-deprecated` parameter\. Furthermore, you cannot exclude deprecated AMIs that you own from the results by using `--no-include-deprecated`\.

If an AMI is deprecated, the `DeprecationTime` field appears in the results\.

**Note**  
A deprecated AMI is an AMI whose deprecation date is in the past\. If you have set the deprecation date to a date in the future, the AMI is not yet deprecated\.

**To include all deprecated AMIs when describing all AMIs**  
Use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command and specify the `--include-deprecated` parameter to include all deprecated AMIs that are not owned by you in the results\.

```
aws ec2 describe-images \
    --region us-east-1 \
    --owners 123456example    
    --include-deprecated
```

**To describe the deprecation date of an AMI**  
Use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command and specify the ID of the AMI\.

Note that if you specify `--no-include-deprecated` together with the AMI ID, the deprecated AMI will be returned in the results\.

```
aws ec2 describe-images \
    --region us-east-1 \    
    --image-ids ami-1234567890EXAMPLE
```

Expected output

The `DeprecationTime` field displays the date on which the AMI is set to be deprecated\. If the AMI is not set to be deprecated, the `DeprecationTime` field does not appear in the output\.

```
{
    "Images": [
        {
            "VirtualizationType": "hvm",
            "Description": "Provided by Red Hat, Inc.",
            "PlatformDetails": "Red Hat Enterprise Linux",
            "EnaSupport": true,
            "Hypervisor": "xen",
            "State": "available",
            "SriovNetSupport": "simple",
            "ImageId": "ami-1234567890EXAMPLE",
            "DeprecationTime": "2021-05-10T13:17:12.000Z"
            "UsageOperation": "RunInstances:0010",
            "BlockDeviceMappings": [
                {
                    "DeviceName": "/dev/sda1",
                    "Ebs": {
                        "SnapshotId": "snap-111222333444aaabb",
                        "DeleteOnTermination": true,
                        "VolumeType": "gp2",
                        "VolumeSize": 10,
                        "Encrypted": false
                    }
                }
            ],
            "Architecture": "x86_64",
            "ImageLocation": "123456789012/RHEL-8.0.0_HVM-20190618-x86_64-1-Hourly2-GP2",
            "RootDeviceType": "ebs",
            "OwnerId": "123456789012",
            "RootDeviceName": "/dev/sda1",
            "CreationDate": "2019-05-10T13:17:12.000Z",
            "Public": true,
            "ImageType": "machine",
            "Name": "RHEL-8.0.0_HVM-20190618-x86_64-1-Hourly2-GP2"
        }
    ]
}
```

------

## Cancel the deprecation of an AMI<a name="cancel-deprecate-ami"></a>

You can cancel the deprecation of an AMI, which removes the date and time from the **Deprecation time** field \(console\) or the `DeprecationTime` field from the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) output \(AWS CLI\)\. You must be the AMI owner to perform this procedure\.

------
#### [ Console ]

**To cancel the deprecation of an AMI**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigator, choose **AMIs**\.

1. From the filter bar, choose **Owned by me**\.

1. Select the AMI, and then choose **Actions**, **Manage AMI Deprecation**\. You can select multiple AMIs to cancel the deprecation of several AMIs at once\.

1. Clear the **Enable** check box, and then choose **Save**\.

------
#### [ AWS CLI ]

**To cancel the deprecation of an AMI**  
Use the [disable\-image\-deprecation](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-image-deprecation.html) command and specify the ID of the AMI\.

```
aws ec2 disable-image-deprecation \
    --image-id ami-1234567890abcdef0
```

Expected output

```
{
  "Return": "true"
}
```

------