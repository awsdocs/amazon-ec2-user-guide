# Work with retention rules<a name="recycle-bin-working-with-rules"></a>

To enable and use Recycle Bin, you must create *retention rules* in the AWS Regions in which you want to protect your resources\. Retention rules specify the following:
+ The resource type that you want to protect\.
+ The resources that you want to retain in the Recycle Bin when they are deleted\.
+ The retention period for which to retain resources in the Recycle Bin before they are permanently deleted\.

With Recycle Bin, you can create two types of retention rules:
+ **Tag\-level retention rules** — A tag\-level retention rule uses resource tags to identify the resources that are to be retained in the Recycle Bin\. For each retention rule, you specify one or more tag key and value pairs\. Resources of the specified type that are tagged with at least one of the tag key and value pairs that are specified in the retention rule are automatically retained in the Recycle Bin upon deletion\. Use this type of retention rule if you want to protect specific resources in your account based on their tags\.
+ **Region\-level retention rules** — A Region\-level retention rule does not have any resource tags specified\. It applies to all of the resources of the specified type in the Region in which the rule is created, even if the resources are not tagged\. Use this type of retention rule if you want to protect all resources of a specific type in a specific Region\.

After you create a retention rule, resources that match its criteria are automatically retained in the Recycle Bin for the specified retention period after they are deleted\.

**Topics**
+ [Create a retention rule](#recycle-bin-create-rule)
+ [View Recycle Bin retention rules](#recycle-bin-view-rule)
+ [Update retention rules](#recycle-bin-update-rule)
+ [Tag retention rules](#recycle-bin-tag-resource)
+ [View retention rule tags](#recycle-bin-view-resource-tag)
+ [Remove tags from retention rules](#recycle-bin-untag-resource)
+ [Delete Recycle Bin retention rules](#recycle-bin-delete-rule)

## Create a retention rule<a name="recycle-bin-create-rule"></a>

To create a retention rule, you must specify:
+ An optional name for the retention rule\. The name can be up to 255 characters long\.
+ An optional description for the rule\. The description can be up to 255 characters long\.
+ The resource type that is to be protected by the retention rule\.
+ Resource tags that identify the resources that are to be retained in the Recycle Bin\. You can specify up to 50 tags for each rule\. However, you can add the same tag key and value pair to up to 5 retention rules only\.
  + To create a tag\-level retention rule, specify at least one tag key and value pair\.
  + To create an Region\-level retention rule, do not specify any tag key and value pairs\.
+ The period for which the resources are to be retained in the Recycle Bin after deletion\. The period can be up to 1 year \(365 days\)\.
+ Optional retention rule tags to help identify and organize your retention rules\. You can assign up to 50 tags to each rule\.

Retention rules function only in the Regions in which they are created\. If you intend to use Recycle Bin in other Regions, you must create additional retention rules in those Regions\.

You can create a Recycle Bin retention rule using one of the following methods\.

------
#### [ Recycle Bin console ]

**To create a retention rule**

1. Open the Recycle Bin console at [https://console\.aws\.amazon\.com/rbin/home/](https://console.aws.amazon.com/rbin/home/)

1. In the navigation panel, choose **Retention rules**, and then choose **Create retention rule**\.

1. In the **Rule details** section, do the following:

   1. \(*Optional*\) For **Retention rule name**, enter a descriptive name for the retention rule\.

   1. \(*Optional*\) For **Retention rule description**, enter a brief description for the retention rule\.

1. In the **Rule settings** section, do the following:

   1. For **Resource type**, select choose the type of resource for the retention rule to protect\. The retention rule will retain only resources of this type in the Recycle Bin\.

   1. Do one of the following:
      + To create a Region\-level retention rule that matches all deleted resources of the specified type in the Region, select **Apply to all resources**\. The retention rule will retain all deleted resources of the specified in the Recycle Bin upon deletion, even if the resources do not have any tags\.
      + To create a tag\-level retention rule, for **Resource tags to match**, enter the tag key and value pairs to use to identify resource of the specified type that are to be retained in the Recycle Bin\. Only resources of the specified type that have at least one of the specified tag key and value pairs will be retained by the retention rule\.

   1. For **Retention period**, enter the number of days for which the retention rule is to retain resources in the Recycle Bin\.

1. \(*Optional*\) In the **Tags** section, do the following:

   1. To tag the rule with custom tags, choose **Add tag** and then enter the tag key and value pair\.

1. Choose **Create retention rule**\.

------
#### [ AWS CLI ]

**To create a retention rule**  
Use the [create\-rule](https://docs.aws.amazon.com/cli/latest/reference/rbin/create-rule.html) AWS CLI command\. For `--retention-period`, specify the number of days to retain deleted snapshots in the Recycle Bin\. For `--resource-type`, specify `EBS_SNAPSHOT` for snapshots or `EC2_IMAGE` for AMIs\. To create a tag\-level retention rule, for `--resource-tags`, specify the tags to use to identify the snapshots that are to be retained\. To create a Region\-level retention rule, omit `--resource-tags`\.

```
$ aws rbin create-rule \
--retention-period RetentionPeriodValue=number_of_days,RetentionPeriodUnit=DAYS \
--resource-type EBS_SNAPSHOT|EC2_IMAGE \
--description "rule_description" \
--resource-tags ResourceTagKey=tag_key,ResourceTagValue=tag_value
```

**Example 1**  
The following example command creates a Region\-level retention rule that retains all deleted snapshots for a period of `8` days\.

```
$ aws rbin create-rule \
--retention-period RetentionPeriodValue=8,RetentionPeriodUnit=DAYS \
--resource-type EBS_SNAPSHOT \
--description "Match all snapshots"
```

**Example 2**  
The following example command creates a tag\-level rule that retains deleted snapshots that are tagged with `purpose=production` for a period of `14` days\.

```
$ aws rbin create-rule \
--retention-period RetentionPeriodValue=14,RetentionPeriodUnit=DAYS \
--resource-type EBS_SNAPSHOT \
--description "Match snapshots with a specific tag" \
--resource-tags ResourceTagKey=purpose,ResourceTagValue=production
```

------

## View Recycle Bin retention rules<a name="recycle-bin-view-rule"></a>

You can view Recycle Bin retention rules using one of the following methods\.

------
#### [ Recycle Bin console ]

**To view retention rules**

1. Open the Recycle Bin console at [https://console\.aws\.amazon\.com/rbin/home/](https://console.aws.amazon.com/rbin/home/)

1. In the navigation panel, choose **Retention rules**\.

1. The grid lists all of the retention rules for the selected Region\. To view more information about a specific retention rule, select it in the grid\.

------
#### [ AWS CLI ]

**To view all of your retention rules**  
Use the [list\-rules](https://docs.aws.amazon.com/cli/latest/reference/rbin/list-rules.html) AWS CLI command, and for `--resource-type`, specify `EBS_SNAPSHOT` for snapshots or `EC2_IMAGE` for AMIs\.

```
$ aws rbin list-rules --resource-type EBS_SNAPSHOT|EC2_IMAGE
```

**Example**  
The following example command provides lists all retention rules that retain snapshots\.

```
$ aws rbin list-rules --resource-type EBS_SNAPSHOT
```

**To view information for a specific retention rule**  
Use the [get\-rule](https://docs.aws.amazon.com/cli/latest/reference/rbin/get-rule.html) AWS CLI command\.

```
$ aws rbin get-rule --identifier rule_ID
```

**Example**  
The following example command provides information about retention rule `pwxIkFcvge4`\.

```
$ aws rbin get-rule --identifier pwxIkFcvge4
```

------

## Update retention rules<a name="recycle-bin-update-rule"></a>

You can update a retention rule's description, resource tags, and retention period at any time after creation\. You can't update a rule's resource type after creation\.

After you update a retention rule, the changes only apply to new resources that it retains\. The changes do not affect resources that it previously sent to the Recycle Bin\. For example, if you update a retention rule's retention period, only snapshots that are deleted after the update are retained for the new retention period\. Snapshots that it sent to the Recycle Bin before the update are still retained for the previous \(old\) retention period\.

You can update a retention rule using one of the following methods\.

------
#### [ Recycle Bin console ]

**To update a retention rule**

1. Open the Recycle Bin console at [https://console\.aws\.amazon\.com/rbin/home/](https://console.aws.amazon.com/rbin/home/)

1. In the navigation panel, choose **Retention rules**\.

1. In the grid, select the retention rule to update, and choose **Actions**, **Edit retention rule**\.

1. In the **Rule details** section, update **Retention rule name** and **Retention rule description** as needed\.

1. In the **Rule settings** section, update the **Resource type**, **Resource tags to match**, and **Retention period** as needed\.

1. In the **Tags** section, add or remove retention rule tags as needed\.

1. Choose **Save retention rule**\.

------
#### [ AWS CLI ]

**To update a retention rule**  
Use the [update\-rule](https://docs.aws.amazon.com/cli/latest/reference/rbin/update-rule.html) AWS CLI command\. For `--identifier`, specify the ID of the retention rule to update For `--resource-types`, specify `EBS_SNAPSHOT` for snapshots or `EC2_IMAGE` for AMIs\.

```
$ aws rbin update-rule \
--identifier rule_ID \
--retention-period RetentionPeriodValue=number_of_days,RetentionPeriodUnit=DAYS \
--resource-type EBS_SNAPSHOT|EC2_IMAGE \
--description "rule_description"
```

**Example**  
The following example command updates retention rule `6lsJ2Fa9nh9` to retain all snapshots for `21` days and updates its description\.

```
$ aws rbin update-rule \
--identifier 6lsJ2Fa9nh9 \
--retention-period RetentionPeriodValue=21,RetentionPeriodUnit=DAYS \
--resource-type EBS_SNAPSHOT \
--description "Retain for three weeks"
```

------

## Tag retention rules<a name="recycle-bin-tag-resource"></a>

You can assign custom tags to your retention rules to categorize them in different ways, for example, by purpose, owner, or environment\. This helps you to efficiently find a specific retention rule based on the custom tags that you assigned\.

You can assign a tag to a retention rule using one of the following methods\.

------
#### [ Recycle Bin console ]

**To tag a retention rule**

1. Open the Recycle Bin console at [https://console\.aws\.amazon\.com/rbin/home/](https://console.aws.amazon.com/rbin/home/)

1. In the navigation panel, choose **Retention rules**\.

1. Select the retention rule to tag, choose the **Tags** tab, and then choose **Manage tags**\.

1. Choose **Add tag**\. For **Key**, enter the tag key\. For **Value**, enter the tag value\.

1. Chose **Save**\.

------
#### [ AWS CLI ]

**To tag a retention rule**  
Use the [tag\-resource](https://docs.aws.amazon.com/cli/latest/reference/rbin/tag-resource.html) AWS CLI command\. For `--resource-arn`, specify the Amazon Resource Name \(ARN\) of the retention rule to tag, and for `--tags`, specify the tag key and value pair\.

```
$ aws rbin tag-resource \
--resource-arn retention_rule_arn \
--tags key=tag_key,value=tag_value
```

**Example**  
The following example command tags retention rule `arn:aws:rbin:us-east-1:123456789012:rule/nOoSBBtItF3` with tag `purpose=production`\.

```
$ aws rbin tag-resource \
--resource-arn arn:aws:rbin:us-east-1:123456789012:rule/nOoSBBtItF3 \
--tags key=purpose,value=production
```

------

## View retention rule tags<a name="recycle-bin-view-resource-tag"></a>

You can view the tags assigned to a retention rule using one of the following methods\.

------
#### [ Recycle Bin console ]

**To view tags for a retention rule**

1. Open the Recycle Bin console at [https://console\.aws\.amazon\.com/rbin/home/](https://console.aws.amazon.com/rbin/home/)

1. In the navigation panel, choose **Retention rules**\.

1. Select the retention rule for which to view the tags, and choose the **Tags** tab\.

------
#### [ AWS CLI ]

**To view the tags assigned to a retention rule**  
Use the [list\-tags\-for\-resource](https://docs.aws.amazon.com/cli/latest/reference/rbin/list-tags-for-resource.html) AWS CLI command\. For `--resource-arn`, specify the ARN of the retention rule\.

```
$ aws rbin list-tags-for-resource \
--resource-arn retention_rule_arn
```

**Example**  
The following example command lists the tags for retention rule `arn:aws:rbin:us-east-1:123456789012:rule/nOoSBBtItF3`\.

```
$ aws rbin list-tags-for-resource \
--resource-arn arn:aws:rbin:us-east-1:123456789012:rule/nOoSBBtItF3
```

------

## Remove tags from retention rules<a name="recycle-bin-untag-resource"></a>

You can remove tags from a retention rule using one of the following methods\.

------
#### [ Recycle Bin console ]

**To remove a tag from a retention rule**

1. Open the Recycle Bin console at [https://console\.aws\.amazon\.com/rbin/home/](https://console.aws.amazon.com/rbin/home/)

1. In the navigation panel, choose **Retention rules**\.

1. Select the retention rule from which to remove the tag, choose the **Tags** tab, and then choose **Manage tags**\.

1. Choose **Remove** next to the tag to remove\.

1. Chose **Save**\.

------
#### [ AWS CLI ]

**To remove a tag from a retention rule**  
Use the [untag\-resource](https://docs.aws.amazon.com/cli/latest/reference/rbin/untag-resource.html) AWS CLI command\. For `--resource-arn`, specify the ARN of the retention rule\. For `--tagkeys`, specify the tags keys of the tags to remove\.

```
$ aws rbin untag-resource \
--resource-arn retention_rule_arn \
--tagkeys tag_key
```

**Example**  
The following example command removes tags that have a tag key of `purpose` from retention rule `arn:aws:rbin:us-east-1:123456789012:rule/nOoSBBtItF3`\.

```
$ aws rbin untag-resource \
--resource-arn arn:aws:rbin:us-east-1:123456789012:rule/nOoSBBtItF3 \
--tagkeys purpose
```

------

## Delete Recycle Bin retention rules<a name="recycle-bin-delete-rule"></a>

You can delete a retention rule at any time\. When you delete a retention rule, it no longer retains new resources after they have been deleted\. Resources that were sent to the Recycle Bin before the retention rule was deleted continue to be retained in the Recycle Bin according to the retention period defined in the retention rule\. When the period expires, the resource is permanently deleted from the Recycle Bin\.

You can delete a retention rule using one of the following methods\.

------
#### [ Recycle Bin console ]

**To delete a retention rule**

1. Open the Recycle Bin console at [https://console\.aws\.amazon\.com/rbin/home/](https://console.aws.amazon.com/rbin/home/)

1. In the navigation panel, choose **Retention rules**\.

1. In the grid, select the retention rule to delete, and choose **Actions**, **Delete retention rule**\.

1. When prompted, enter the confirmation message and choose **Delete retention rule**\.

------
#### [ AWS CLI ]

**To delete a retention rule**  
Use the [delete\-rule](https://docs.aws.amazon.com/cli/latest/reference/rbin/delete-rule.html) AWS CLI command\. For `--identifier`, specify the ID of the retention rule to delete\.

```
$ aws rbin delete-rule --identifier rule_ID
```

**Example**  
The following example command deletes retention rule `6lsJ2Fa9nh9`\.

```
$ aws rbin delete-rule --identifier 6lsJ2Fa9nh9
```

------