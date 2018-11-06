# Spot Instance Data Feed<a name="spot-data-feeds"></a>

To help you understand the charges for your Spot Instances, Amazon EC2 provides a data feed that describes your Spot Instance usage and pricing\. This data feed is sent to an Amazon S3 bucket that you specify when you subscribe to the data feed\.

Data feed files arrive in your bucket typically once an hour, and each hour of usage is typically covered in a single data file\. These files are compressed \(gzip\) before they are delivered to your bucket\. Amazon EC2 can write multiple files for a given hour of usage where files are large \(for example, when file contents for the hour exceed 50 MB before compression\)\.

**Note**  
If you don't have a Spot Instance running during a certain hour, you don't receive a data feed file for that hour\.

**Topics**
+ [Data Feed File Name and Format](#using-spot-instances-format)
+ [Amazon S3 Bucket Requirements](#using-spot-instances-dfs3)
+ [Subscribing to Your Spot Instance Data Feed](#using-spot-instances-datafeed-all)
+ [Deleting Your Spot Instance Data Feed](#using-spot-instances-datafeed-delete)

## Data Feed File Name and Format<a name="using-spot-instances-format"></a>

The Spot Instance data feed file name uses the following format \(with the date and hour in UTC\): 

```
bucket-name.s3.amazonaws.com/{optional prefix}/aws-account-id.YYYY-MM-DD-HH.n.unique-id.gz
```

For example, if your bucket name is `myawsbucket` and your prefix is `myprefix`, your file names are similar to the following:

```
myawsbucket.s3.amazonaws.com/myprefix/111122223333.2014-03-17-20.001.pwBdGTJG.gz
```

The Spot Instance data feed files are tab\-delimited\. Each line in the data file corresponds to one instance hour and contains the fields listed in the following table\.


|  Field  |  Description  | 
| --- | --- | 
|   `Timestamp`   |  The timestamp used to determine the price charged for this instance usage\.  | 
|   `UsageType`   |  The type of usage and instance type being charged for\. For `m1.small` Spot Instances, this field is set to `SpotUsage`\. For all other instance types, this field is set to `SpotUsage:`\{*instance\-type*\}\. For example, `SpotUsage:c1.medium`\.  | 
|   `Operation`   |  The product being charged for\. For Linux Spot Instances, this field is set to `RunInstances`\. For Windows Spot Instances, this field is set to `RunInstances:0002`\. Spot usage is grouped according to Availability Zone\.  | 
|   `InstanceID`   |  The ID of the Spot Instance that generated this instance usage\.  | 
|   `MyBidID`   |  The ID for the Spot Instance request that generated this instance usage\.  | 
|   `MyMaxPrice`   |  The maximum price specified for this Spot Instance request\.  | 
|   `MarketPrice`   |  The Spot price at the time specified in the `Timestamp` field\.  | 
|   `Charge`   |  The price charged for this instance usage\.  | 
|   `Version`   |  The version included in the data feed file name for this record\.  | 

## Amazon S3 Bucket Requirements<a name="using-spot-instances-dfs3"></a>

When you subscribe to the data feed, you must specify an Amazon S3 bucket to store the data feed files\. Before you choose an Amazon S3 bucket for the data feed, consider the following:
+ You must have `FULL_CONTROL` permission to the bucket, which includes permission for the `s3:GetBucketAcl` and `s3:PutBucketAcl` actions\.

  If you're the bucket owner, you have this permission by default\. Otherwise, the bucket owner must grant your AWS account this permission\.
+ When you subscribe to a data feed, these permissions are used to update the bucket ACL to give the AWS data feed account `FULL_CONTROL` permission\. The AWS data feed account writes data feed files to the bucket\. If your account doesn't have the required permissions, the data feed files cannot be written to the bucket\.
**Note**  
If you update the ACL and remove the permissions for the AWS data feed account, the data feed files cannot be written to the bucket\. You must resubscribe to the data feed to receive the data feed files\.
+ Each data feed file has its own ACL \(separate from the ACL for the bucket\)\. The bucket owner has `FULL_CONTROL` permission to the data files\. The AWS data feed account has read and write permissions\.
+ If you delete your data feed subscription, Amazon EC2 doesn't remove the read and write permissions for the AWS data feed account on either the bucket or the data files\. You must remove these permissions yourself\.

## Subscribing to Your Spot Instance Data Feed<a name="using-spot-instances-datafeed-all"></a>

To subscribe to your data feed, use the following [create\-spot\-datafeed\-subscription](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-spot-datafeed-subscription.html) command:

```
aws ec2 create-spot-datafeed-subscription --bucket myawsbucket [--prefix myprefix]
```

The following is example output:

```
{
    "SpotDatafeedSubscription": {
        "OwnerId": "111122223333",
        "Prefix": "myprefix",
        "Bucket": "myawsbucket",
        "State": "Active"
    }
}
```

## Deleting Your Spot Instance Data Feed<a name="using-spot-instances-datafeed-delete"></a>

To delete your data feed, use the following [delete\-spot\-datafeed\-subscription](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-spot-datafeed-subscription.html) command:

```
aws ec2 delete-spot-datafeed-subscription
```