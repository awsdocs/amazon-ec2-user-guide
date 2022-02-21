# Recycle Bin<a name="recycle-bin"></a>

Recycle Bin is a data recovery feature that enables you to restore accidentally deleted Amazon EBS snapshots and EBS\-backed AMIs\. When using Recycle Bin, if your resources are deleted, they are retained in the Recycle Bin for a time period that you specify before being permanently deleted\.

You can restore a resource from the Recycle Bin at any time before its retention period expires\. After you restore a resource from the Recycle Bin, the resource is removed from the Recycle Bin and you can use it in the same way that you use any other resource of that type in your account\. If the retention period expires and the resource is not restored, the resource is permanently deleted from the Recycle Bin and it is no longer available for recovery\.

Using Recycle Bin helps to ensure business continuity by protecting your business\-critical data against accidental deletion\.

**Topics**
+ [How does it work?](#recycle-bin-concepts)
+ [Supported resources](#supported-resources)
+ [Considerations](recycle-bin-factors.md)
+ [Quotas](#recycle-bin-quotas)
+ [Related services](#recycle-bin-integrations)
+ [Pricing](#recycle-bin-pricing)
+ [Required IAM permissions](recycle-bin-perms.md)
+ [Work with retention rules](recycle-bin-working-with-rules.md)
+ [Work with resources in the Recycle Bin](recycle-bin-work-with-resources.md)
+ [Monitoring Recycle Bin using AWS CloudTrail](recycle-bin-ct.md)

## How does it work?<a name="recycle-bin-concepts"></a>

To enable and use Recycle Bin, you must create *retention rules* in the AWS Regions in which you want to protect your resources\. Retention rules specify the following:
+ The resource type that you want to protect\.
+ The resources that you want to retain in the Recycle Bin when they are deleted\.
+ The retention period for which to retain resources in the Recycle Bin before they are permanently deleted\.

With Recycle Bin, you can create two types of retention rules:
+ **Tag\-level retention rules** — A tag\-level retention rule uses resource tags to identify the resources that are to be retained in the Recycle Bin\. For each retention rule, you specify one or more tag key and value pairs\. Resources of the specified type that are tagged with at least one of the tag key and value pairs that are specified in the retention rule are automatically retained in the Recycle Bin upon deletion\. Use this type of retention rule if you want to protect specific resources in your account based on their tags\.
+ **Region\-level retention rules** — A Region\-level retention rule does not have any resource tags specified\. It applies to all of the resources of the specified type in the Region in which the rule is created, even if the resources are not tagged\. Use this type of retention rule if you want to protect all resources of a specific type in a specific Region\.

While a resource is in the Recycle Bin, you have the ability to restore it for use at any time\.

The resource remains in the Recycle Bin until one of the following happens:
+ You manually restore it for use\. When you restore a resource from the Recycle Bin, the resource is removed from the Recycle Bin and it immediately becomes available for use\. You can use restored resources in the same way as any other resource of that type in your account\.
+ The retention period expires\. If the retention period expires, and the resource has not been restored from the Recycle Bin, the resource is permanently deleted from the Recycle Bin and it can no longer be viewed or restored\.

## Supported resources<a name="supported-resources"></a>

Recycle Bin supports the following resource types:
+ Amazon EBS snapshots
+ Amazon EBS\-backed Amazon Machine Images \(AMIs\)

## Quotas<a name="recycle-bin-quotas"></a>

The following quotas apply to Recycle Bin\.


| Quota | Default quota | 
| --- | --- | 
| Retention rules per Region | 250 | 
| Tag key and value pairs per retention rule | 50 | 

## Related services<a name="recycle-bin-integrations"></a>

Recycle Bin works with the following services:
+ **AWS CloudTrail** — Enables you to record events that occur in Recycle Bin\. For more information, see [Monitoring Recycle Bin using AWS CloudTrail](recycle-bin-ct.md)\.

## Pricing<a name="recycle-bin-pricing"></a>

Resources in the Recycle Bin are billed at their standard rates\. There are no additional charges for using Recycle Bin and retention rules\. For more information, see [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.

**Note**  
Some resources might still appear in the Recycle Bin console or in the AWS CLI and API output for a short period after their retention periods have expired and they have been permanently deleted\. You are not billed for these resources\. Billing stops as soon as the retention period expires\.

You can use the following AWS generated cost allocation tags for cost tracking and allocation purposes when using AWS Billing and Cost Management\.
+ Key: `aws:recycle-bin:resource-in-bin`
+ Value: `true`

For more information, see [AWS\-Generated Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/aws-tags.html) in the *AWS Billing and Cost Management User Guide*\.