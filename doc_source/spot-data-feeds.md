# Spot Instance data feed<a name="spot-data-feeds"></a>

To help you understand the charges for your Spot Instances, Amazon EC2 provides a data feed that describes your Spot Instance usage and pricing\. This data feed is sent to an Amazon S3 bucket that you specify when you subscribe to the data feed\.

Data feed files arrive in your bucket typically once an hour, and each hour of usage is typically covered in a single data file\. These files are compressed \(gzip\) before they are delivered to your bucket\. Amazon EC2 can write multiple files for a given hour of usage where files are large \(for example, when file contents for the hour exceed 50 MB before compression\)\.

**Note**  
If you don't have a Spot Instance running during a certain hour, you don't receive a data feed file for that hour\.

Spot Instance data feed is supported in all AWS Regions except China \(Beijing\), China \(Ningxia\), AWS GovCloud \(US\), and the [Regions that are disabled by default](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html#rande-manage-enable)\.

**Topics**
+ [Data feed file name and format](#using-spot-instances-format)
+ [Amazon S3 bucket requirements](#using-spot-instances-dfs3)
+ [Subscribe to your Spot Instance data feed](#using-spot-instances-datafeed-all)
+ [Describe your Spot Instance data feed](#using-spot-instances-datafeed-describe)
+ [Delete your Spot Instance data feed](#using-spot-instances-datafeed-delete)

## Data feed file name and format<a name="using-spot-instances-format"></a>

The Spot Instance data feed file name uses the following format \(with the date and hour in UTC\): 

```
bucket-name.s3.amazonaws.com/optional-prefix/aws-account-id.YYYY-MM-DD-HH.n.unique-id.gz
```

For example, if your bucket name is **my\-bucket\-name** and your prefix is **my\-prefix**, your file names are similar to the following:

```
my-bucket-name.s3.amazonaws.com/my-prefix/111122223333.2019-03-17-20.001.pwBdGTJG.gz
```

For more information about bucket names, see [Rules for bucket naming](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) in the *Amazon Simple Storage Service User Guide*\.

The Spot Instance data feed files are tab\-delimited\. Each line in the data file corresponds to one instance hour and contains the fields listed in the following table\.


|  Field  |  Description  | 
| --- | --- | 
|   `Timestamp`   |  The timestamp used to determine the price charged for this instance usage\.  | 
|   `UsageType`   |  The type of usage and instance type being charged for\. For `m1.small` Spot Instances, this field is set to `SpotUsage`\. For all other instance types, this field is set to `SpotUsage:`\{*instance\-type*\}\. For example, `SpotUsage:c1.medium`\.  | 
|   `Operation`   |  The product being charged for\. For Linux Spot Instances, this field is set to `RunInstances`\. For Windows Spot Instances, this field is set to `RunInstances:0002`\. Spot usage is grouped according to Availability Zone\.  | 
|   `InstanceID`   |  The ID of the Spot Instance that generated this instance usage\.  | 
|   `MyBidID`   |  The ID for the Spot Instance request that generated this instance usage\.  | 
|   `MyMaxPrice`   |  The maximum price specified for this Spot request\.  | 
|   `MarketPrice`   |  The Spot price at the time specified in the `Timestamp` field\.  | 
|   `Charge`   |  The price charged for this instance usage\.  | 
|   `Version`   |  The version included in the data feed file name for this record\.  | 

## Amazon S3 bucket requirements<a name="using-spot-instances-dfs3"></a>

When you subscribe to the data feed, you must specify an Amazon S3 bucket to store the data feed files\. Before you choose an Amazon S3 bucket for the data feed, consider the following:
+ You must have `FULL_CONTROL` permission to the bucket, which includes permission for the `s3:GetBucketAcl` and `s3:PutBucketAcl` actions\.

  If you're the bucket owner, you have this permission by default\. Otherwise, the bucket owner must grant your AWS account this permission\.
+ When you subscribe to a data feed, these permissions are used to update the bucket ACL to give the AWS data feed account `FULL_CONTROL` permission\. The AWS data feed account writes data feed files to the bucket\. If your account doesn't have the required permissions, the data feed files cannot be written to the bucket\.
**Note**  
If you update the ACL and remove the permissions for the AWS data feed account, the data feed files cannot be written to the bucket\. You must resubscribe to the data feed to receive the data feed files\.
+ Each data feed file has its own ACL \(separate from the ACL for the bucket\)\. The bucket owner has `FULL_CONTROL` permission to the data files\. The AWS data feed account has read and write permissions\.
+ If you delete your data feed subscription, Amazon EC2 doesn't remove the read and write permissions for the AWS data feed account on either the bucket or the data files\. You must remove these permissions yourself\.

## Subscribe to your Spot Instance data feed<a name="using-spot-instances-datafeed-all"></a>

To subscribe to your data feed, use the [create\-spot\-datafeed\-subscription](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-spot-datafeed-subscription.html) command\.

```
aws ec2 create-spot-datafeed-subscription \
    --bucket my-bucket-name \
    [--prefix my-prefix]
```

The following is example output:

```
{
    "SpotDatafeedSubscription": {
        "OwnerId": "111122223333",
        "Bucket": "my-bucket-name",
        "Prefix": "my-prefix",
        "State": "Active"
    }
}
```

## Describe your Spot Instance data feed<a name="using-spot-instances-datafeed-describe"></a>

To describe your data feed subscription, use the [describe\-spot\-datafeed\-subscription](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-datafeed-subscription.html) command\.

```
aws ec2 describe-spot-datafeed-subscription
```

The following is example output:

```
{
    "SpotDatafeedSubscription": {
        "OwnerId": "123456789012",
        "Prefix": "spotdata",
        "Bucket": "my-s3-bucket",
        "State": "Active"
    }
}
```

## Delete your Spot Instance data feed<a name="using-spot-instances-datafeed-delete"></a>

To delete your data feed, use the [delete\-spot\-datafeed\-subscription](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-spot-datafeed-subscription.html) command\.

```
aws ec2 delete-spot-datafeed-subscription
```