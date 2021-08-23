# View Amazon EBS snapshot information<a name="ebs-describing-snapshots"></a>

You can view detailed information about your snapshots using one of the following methods\.

------
#### [ Console ]

**To view snapshot information using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Snapshots** in the navigation pane\. 

1. To reduce the list, choose an option from the **Filter** list\. For example, to view only your snapshots, choose **Owned By Me**\. You can also filter your snapshots using tags and snapshot attributes\. Choose the search bar to view the available tags and attributes\.

1. To view more information about a snapshot, select it\.

------
#### [ AWS CLI ]

**To view snapshot information using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshots.html) \(AWS CLI\)
+ [Get\-EC2Snapshot](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Snapshot.html) \(AWS Tools for Windows PowerShell\)

**Example 1: Filter based on tags**  
The following command describes the snapshots with the tag Stack=production\.  

```
aws ec2 describe-snapshots --filters Name=tag:Stack,Values=production
```

**Example 2: Filter based on volume**  
The following command describes the snapshots created from the specified volume\.  

```
aws ec2 describe-snapshots --filters Name=volume-id,Values=vol-049df61146c4d7901
```

**Example 3: Filter based on snapshot age**  
With the AWS CLI, you can use JMESPath to filter results using expressions\. For example, the following command displays the IDs of all snapshots created by your AWS account \(represented by *123456789012*\) before the specified date \(represented by *2020\-03\-31*\)\. If you do not specify the owner, the results include all public snapshots\.  

```
aws ec2 describe-snapshots --filters Name=owner-id,Values=123456789012 --query "Snapshots[?(StartTime<=`2020-03-31`)].[SnapshotId]" --output text
```
The following command displays the IDs of all snapshots created in the specified date range\.  

```
aws ec2 describe-snapshots --filters Name=owner-id,Values=123456789012 --query "Snapshots[?(StartTime>=`2019-01-01`) && (StartTime<=`2019-12-31`)].[SnapshotId]" --output text
```

------