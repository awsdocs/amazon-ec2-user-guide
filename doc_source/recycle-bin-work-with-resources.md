# Work with resources in the Recycle Bin<a name="recycle-bin-work-with-resources"></a>

**Topics**
+ [Tag snapshots](#recycle-bin-tag-resource)
+ [View snapshot tags](#recycle-bin-view-resource-tag)
+ [Remove tags from a snapshot](#recycle-bin-untag-resource)
+ [Work with snapshots in the Recycle Bin](#recycle-bin-snapshots)

## Tag snapshots<a name="recycle-bin-tag-resource"></a>

You can use the Recycle Bin AWS CLI to tag snapshots\.

**To tag a snapshot**  
Use the [tag\-resource](https://docs.aws.amazon.com/cli/latest/reference/rbin/tag-resource.html) AWS CLI command\. For `--resource-arn`, specify the Amazon Resource Name \(ARN\) of the snapshot to tag, and for `--tags`, specify the tag key and value pair\.

```
$ aws rbin tag-resource \
--resource-arn snapshot_arn \
--tags key=tag_key,value=tag_value
```

**Example**  
The following example command tags snapshot `arn:aws:ec2:us-east-1:123456789012:snapshot/snap-01234567890abcdef` with tag `purpose=production`\.

```
$ aws rbin tag-resource \
--resource-arn arn:aws:ec2:us-east-1:123456789012:snapshot/snap-01234567890abcdef \
--tags key=purpose,value=production
```

## View snapshot tags<a name="recycle-bin-view-resource-tag"></a>

You can use the Recycle Bin AWS CLI to view the tags that are currently assigned to a snapshot\.

**To view the tags assigned to a snapshot**  
Use the [list\-tags\-for\-resource](https://docs.aws.amazon.com/cli/latest/reference/rbin/list-tags-for-resource.html) AWS CLI command\. For `--resource-arn`, specify the ARN of the snapshot\.

```
$ aws rbin list-tags-for-resource \
--resource-arn snapshot_arn
```

**Example**  
The following example command lists the tags for snapshot `arn:aws:ec2:us-east-1:123456789012:snapshot/snap-01234567890abcdef`\.

```
$ aws rbin list-tags-for-resource \
--resource-arn arn:aws:ec2:us-east-1:123456789012:snapshot/snap-01234567890abcdef
```

## Remove tags from a snapshot<a name="recycle-bin-untag-resource"></a>

You can use the Recycle Bin AWS CLI to remove a tag from a snapshot\.

**To remove a tag from a snapshot**  
Use the [untag\-resource](https://docs.aws.amazon.com/cli/latest/reference/rbin/untag-resource.html) AWS CLI command\. For `--resource-arn`, specify the ARN of the snapshot\. For `--tagkeys`, specify the tags keys of the tags to remove\.

```
$ aws rbin untag-resource \
--resource-arn snapshot_arn \
--tagkeys tag_key
```

**Example**  
The following example command removes tags that have a tag key of `purpose` from snapshot `arn:aws:ec2:us-east-1:123456789012:snapshot/snap-01234567890abcdef`\.

```
$ aws rbin untag-resource \
--resource-arn arn:aws:ec2:us-east-1:123456789012:snapshot/snap-01234567890abcdef \
--tagkeys purpose
```

## Work with snapshots in the Recycle Bin<a name="recycle-bin-snapshots"></a>

For more information, about working with snapshots in the Recycle Bin, see [Recover snapshots from the Recycle Bin](recycle-bin-working-with-snaps.md)\.