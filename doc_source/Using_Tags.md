# Tagging Your Amazon EC2 Resources<a name="Using_Tags"></a>

To help you manage your instances, images, and other Amazon EC2 resources, you can optionally assign your own metadata to each resource in the form of *tags*\. This topic describes tags and shows you how to create them\.

**Topics**
+ [Tag Basics](#tag-basics)
+ [Tagging Your Resources](#tag-resources)
+ [Tag Restrictions](#tag-restrictions)
+ [Tagging Your Resources for Billing](#tag-resources-for-billing)
+ [Working with Tags Using the Console](#Using_Tags_Console)
+ [Working with Tags Using the CLI or API](#Using_Tags_CLI)

## Tag Basics<a name="tag-basics"></a>

A tag is a label that you assign to an AWS resource\. Each tag consists of a *key* and an optional *value*, both of which you define\.

Tags enable you to categorize your AWS resources in different ways, for example, by purpose, owner, or environment\. This is useful when you have many resources of the same type—you can quickly identify a specific resource based on the tags you've assigned to it\. For example, you could define a set of tags for your account's Amazon EC2 instances that helps you track each instance's owner and stack level\.

The following diagram illustrates how tagging works\. In this example, you've assigned two tags to each of your instances—one tag with the key `Owner` and another with the key `Stack`\. Each tag also has an associated value\.

![\[Tag example\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Tag_Example.png)

We recommend that you devise a set of tag keys that meets your needs for each resource type\. Using a consistent set of tag keys makes it easier for you to manage your resources\. You can search and filter the resources based on the tags you add\.

Tags don't have any semantic meaning to Amazon EC2 and are interpreted strictly as a string of characters\. Also, tags are not automatically assigned to your resources\. You can edit tag keys and values, and you can remove tags from a resource at any time\. You can set the value of a tag to an empty string, but you can't set the value of a tag to null\. If you add a tag that has the same key as an existing tag on that resource, the new value overwrites the old value\. If you delete a resource, any tags for the resource are also deleted\.

You can work with tags using the AWS Management Console, the AWS CLI, and the Amazon EC2 API\.

If you're using AWS Identity and Access Management \(IAM\), you can control which users in your AWS account have permission to create, edit, or delete tags\. For more information, see [Controlling Access to Amazon EC2 Resources](UsingIAM.md)\.

## Tagging Your Resources<a name="tag-resources"></a>

You can tag most Amazon EC2 resources that already exist in your account\. The [table](#tag-ec2-resources-table) below lists the resources that support tagging\.

If you're using the Amazon EC2 console, you can apply tags to resources by using the **Tags** tab on the relevant resource screen, or you can use the **Tags** screen\. Some resource screens enable you to specify tags for a resource when you create the resource; for example, a tag with a key of `Name` and a value that you specify\. In most cases, the console applies the tags immediately after the resource is created \(rather than during resource creation\)\. The console may organize resources according to the `Name` tag, but this tag doesn't have any semantic meaning to the Amazon EC2 service\.

If you're using the Amazon EC2 API, the AWS CLI, or an AWS SDK, you can use the `CreateTags` EC2 API action to apply tags to existing resources\. Additionally, some resource\-creating actions enable you to specify tags for a resource when the resource is created\. If tags cannot be applied during resource creation, we roll back the resource creation process\. This ensures that resources are either created with tags or not created at all, and that no resources are left untagged at any time\. By tagging resources at the time of creation, you can eliminate the need to run custom tagging scripts after resource creation\.

The following table describes the Amazon EC2 resources that can be tagged, and the resources that can be tagged on creation\.


**Tagging Support for Amazon EC2 Resources**  

| Resource | Supports tags | Supports tagging on creation \(Amazon EC2 API, AWS CLI, AWS SDK\) | 
| --- | --- | --- | 
|  AFI  |  Yes  |  No  | 
|  AMI  |  Yes  | No | 
|  Bundle task  |  No  | No | 
|  Customer gateway  |  Yes  | No | 
|  Dedicated Host  |  Yes  | No | 
|  DHCP option  |  Yes  | No | 
|  EBS snapshot  |  Yes  | Yes | 
|  EBS volume  |  Yes  | Yes | 
|  EC2 Fleet  |  Yes  |  Yes  | 
|  Egress\-only internet gateway  |  No  | No | 
|  Elastic IP address  |  Yes  | No | 
|  Instance  |  Yes  | Yes | 
|  Instance store volume  |  N/A  | N/A | 
|  Internet gateway  |  Yes  | No | 
|  Key pair  |  No  | No | 
|  Launch template  |  Yes  |  No  | 
|  Launch template version  |  No  |  No  | 
|  NAT gateway  |  Yes  | No | 
|  Network ACL  |  Yes  | No | 
|  Network interface  |  Yes  | No | 
|  Placement group  |  No  | No | 
|  Reserved Instance  |  Yes  | No | 
|  Reserved Instance listing  |  No  | No | 
| Route table |  Yes  | No | 
|  Spot Instance request  |  Yes  | No | 
|  Security group–EC2\-Classic  |  Yes  | No | 
|  Security group–VPC  |  Yes  | No | 
|  Subnet  |  Yes  | No | 
|  Virtual private gateway  |  Yes  | No | 
|  VPC  |  Yes  | No | 
| VPC endpoint | No | No | 
|  VPC endpoint service  |  No  |  No  | 
|  VPC flow log  |  No  | No | 
| VPC peering connection | Yes | No | 
|  VPN connection  |  Yes  | No | 

You can tag instances and volumes on creation using the Amazon EC2 Launch Instances wizard in the Amazon EC2 console\. You can tag your EBS volumes on creation using the Volumes screen, or EBS snapshots using the Snapshots screen\. Alternatively, use the resource\-creating Amazon EC2 APIs \(for example, [RunInstances](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html)\) to apply tags when creating your resource\.

You can apply tag\-based resource\-level permissions in your IAM policies to the Amazon EC2 API actions that support tagging on creation to implement granular control over the users and groups that can tag resources on creation\. Your resources are properly secured from creation—tags are applied immediately to your resources, therefore any tag\-based resource\-level permissions controlling the use of resources are immediately effective\. Your resources can be tracked and reported on more accurately\. You can enforce the use of tagging on new resources, and control which tag keys and values are set on your resources\. 

You can also apply resource\-level permissions to the `CreateTags` and `DeleteTags` Amazon EC2 API actions in your IAM policies to control which tag keys and values are set on your existing resources\. For more information, see [Supported Resource\-Level Permissions for Amazon EC2 API Actions](ec2-supported-iam-actions-resources.md) and [Example Policies for Working with the AWS CLI or an AWS SDK](ExamplePolicies_EC2.md)\. 

For more information about tagging your resources for billing, see [Using Cost Allocation Tags](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management User Guide*\.

## Tag Restrictions<a name="tag-restrictions"></a>

The following basic restrictions apply to tags:
+ Maximum number of tags per resource – 50
+ For each resource, each tag key must be unique, and each tag key can have only one value\.
+ Maximum key length – 128 Unicode characters in UTF\-8
+ Maximum value length – 256 Unicode characters in UTF\-8
+ If your tagging schema is used across multiple services and resources, remember that other services may have restrictions on allowed characters\. Generally allowed characters are: letters, numbers, and spaces representable in UTF\-8, and the following characters: \+ \- = \. \_ : / @\.
+ Tag keys and values are case\-sensitive\.
+ Don't use the `aws:` prefix for either keys or values; it's reserved for AWS use\. You can't edit or delete tag keys or values with this prefix\. Tags with this prefix do not count against your tags per resource limit\.

You can't terminate, stop, or delete a resource based solely on its tags; you must specify the resource identifier\. For example, to delete snapshots that you tagged with a tag key called `DeleteMe`, you must use the `DeleteSnapshots` action with the resource identifiers of the snapshots, such as `snap-1234567890abcdef0`\. 

You can tag public or shared resources, but the tags you assign are available only to your AWS account and not to the other accounts sharing the resource\.

You can't tag all resources\. For more information, see [Tagging Support for Amazon EC2 Resources](#tag-ec2-resources-table)\.

## Tagging Your Resources for Billing<a name="tag-resources-for-billing"></a>

You can use tags to organize your AWS bill to reflect your own cost structure\. To do this, sign up to get your AWS account bill with tag key values included\. For more information about setting up a cost allocation report with tags, see [The Monthly Cost Allocation Report](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/configurecostallocreport.html) in *AWS Billing and Cost Management User Guide*\. To see the cost of your combined resources, you can organize your billing information based on resources that have the same tag key values\. For example, you can tag several resources with a specific application name, and then organize your billing information to see the total cost of that application across several services\. For more information, see [Using Cost Allocation Tags](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management User Guide*\.

Cost allocation tags can indicate which resources are contributing to costs, but deleting or deactivating resources doesn't always reduce costs\. For example, snapshot data that is referenced by another snapshot is preserved, even if the snapshot that contains the original data is deleted\. For more information, see [Amazon Elastic Block Store Volumes and Snapshots](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/checklistforunwantedcharges.html#checkebsvolumes) in the *AWS Billing and Cost Management User Guide*\.

**Note**  
If you've just enabled reporting, data for the current month is available for viewing after 24 hours\.

## Working with Tags Using the Console<a name="Using_Tags_Console"></a>

Using the Amazon EC2 console, you can see which tags are in use across all of your Amazon EC2 resources in the same region\. You can view tags by resource and by resource type, and you can also view how many items of each resource type are associated with a specified tag\. You can also use the Amazon EC2 console to apply or remove tags from one or more resources at a time\.

For more information about using filters when listing your resources, see [Listing and Filtering Your Resources](Using_Filtering.md)\.

For ease of use and best results, use Tag Editor in the AWS Management Console, which provides a central, unified way to create and manage your tags\. For more information, see [Working with Tag Editor](http://docs.aws.amazon.com/awsconsolehelpdocs/latest/gsg/tag-editor.html) in *Getting Started with the AWS Management Console*\.

**Topics**
+ [Displaying Tags](#displaying-tags)
+ [Adding and Deleting Tags on an Individual Resource](#adding-or-deleting-tags)
+ [Adding and Deleting Tags to a Group of Resources](#adding-or-deleting-tags-group)
+ [Adding a Tag When You Launch an Instance](#instance-details-tags)
+ [Filtering a List of Resources by Tag](#filtering-the-list-by-tag)

### Displaying Tags<a name="displaying-tags"></a>

You can display tags in two different ways in the Amazon EC2 console\. You can display the tags for an individual resource or for all resources\.

**Displaying Tags for Individual Resources**  
When you select a resource\-specific page in the Amazon EC2 console, it displays a list of those resources\. For example, if you select **Instances** from the navigation pane, the console displays a list of Amazon EC2 instances\. When you select a resource from one of these lists \(for example, an instance\), if the resource supports tags, you can view and manage its tags\. On most resource pages, you can view the tags in the **Tags** tab on the details pane\.

You can add a column to the resource list that displays all values for tags with the same key\. This column enables you to sort and filter the resource list by the tag\. There are two ways to add a new column to the resource list to display your tags\.
+ On the **Tags** tab, select **Show Column**\. A new column is added to the console\.
+ Choose the **Show/Hide Columns** gear\-shaped icon, and in the **Show/Hide Columns** dialog box, select the tag key under **Your Tag Keys**\.

**Displaying Tags for All Resources**  
You can display tags across all resources by selecting **Tags** from the navigation pane in the Amazon EC2 console\. The following image shows the **Tags** pane, which lists all tags in use by resource type\.

![\[The Tags pane in the Amazon EC2 console\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Tags_Pane.png)

### Adding and Deleting Tags on an Individual Resource<a name="adding-or-deleting-tags"></a>

You can manage tags for an individual resource directly from the resource's page\. 

**To add a tag to an individual resource**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region that meets your needs\. This choice is important because some Amazon EC2 resources can be shared between regions, while others can't\. For more information, see [Resource Locations](resources.md)\.

1. In the navigation pane, select a resource type \(for example, **Instances**\)\.

1. Select the resource from the resource list and choose **Tags**, **Add/Edit Tags**\.

1. In the **Add/Edit Tags** dialog box, specify the key and value for each tag, and then choose **Save**\.

**To delete a tag from an individual resource**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region that meets your needs\. This choice is important because some Amazon EC2 resources can be shared between regions, while others can't\. For more information, see [Resource Locations](resources.md)\.

1. In the navigation pane, choose a resource type \(for example, **Instances**\)\.

1. Select the resource from the resource list and choose **Tags**\.

1. Choose **Add/Edit Tags**, select the **Delete** icon for the tag, and choose **Save**\.

### Adding and Deleting Tags to a Group of Resources<a name="adding-or-deleting-tags-group"></a>

**To add a tag to a group of resources**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region that meets your needs\. This choice is important because some Amazon EC2 resources can be shared between regions, while others can't\. For more information, see [Resource Locations](resources.md)\.

1. In the navigation pane, choose **Tags**\.

1. At the top of the content pane, choose **Manage Tags**\.

1. For **Filter**, select the type of resource \(for example, instances\) to which to add tags\.

1. In the resources list, select the check box next to each resource to which to add tags\.

1. Under **Add Tag**, for **Key** and **Value**, type the tag key and values, and then choose **Add Tag**\.
**Note**  
If you add a new tag with the same tag key as an existing tag, the new tag overwrites the existing tag\.

**To remove a tag from a group of resources**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region that meets your needs\. This choice is important because some Amazon EC2 resources can be shared between regions, while others can't\. For more information, see [Resource Locations](resources.md)\.

1. In the navigation pane, choose **Tags**, **Manage Tags**\.

1. To view the tags in use, select the **Show/Hide Columns** gear\-shaped icon, and in the **Show/Hide Columns** dialog box, select the tag keys to view and choose **Close**\.

1. For **Filter**, select the type of resource \(for example, instances\) from which to remove tags\.

1. In the resource list, select the check box next to each resource from which to remove tags\.

1. Under **Remove Tag**, for **Key**, type the tag's name and choose **Remove Tag**\.

### Adding a Tag When You Launch an Instance<a name="instance-details-tags"></a>

**To add a tag using the Launch Wizard**

1. From the navigation bar, select the region for the instance\. This choice is important because some Amazon EC2 resources can be shared between regions, while others can't\. Select the region that meets your needs\. For more information, see [Resource Locations](resources.md)\.

1. Choose **Launch Instance**\.

1. The **Choose an Amazon Machine Image \(AMI\)** page displays a list of basic configurations called Amazon Machine Images \(AMIs\)\. Select the AMI to use and choose **Select**\. For more information about selecting an AMI, see [Finding a Linux AMI](finding-an-ami.md)\.

1. On the **Configure Instance Details** page, configure the instance settings as necessary, and then choose **Next: Add Storage**\.

1. On the **Add Storage** page, you can specify additional storage volumes for your instance\. Choose **Next: Add Tags** when done\.

1. On the **Add Tags** page, specify tags for the instance, the volumes, or both\. Choose **Add another tag** to add more than one tag to your instance\. Choose **Next: Configure Security Group** when you are done\. 

1. On the **Configure Security Group** page, you can choose from an existing security group that you own, or let the wizard create a new security group for you\. Choose **Review and Launch** when you are done\.

1. Review your settings\. When you're satisfied with your selections, choose **Launch**\. Select an existing key pair or create a new one, select the acknowledgment check box, and then choose **Launch Instances**\.

### Filtering a List of Resources by Tag<a name="filtering-the-list-by-tag"></a>

You can filter your list of resources based on one or more tag keys and tag values\.

**To filter a list of resources by tag**

1. Display a column for the tag as follows:

   1. Select a resource\.

   1. In the details pane, choose **Tags**\.

   1. Locate the tag in the list and choose **Show Column**\.

1. Choose the filter icon in the top right corner of the column for the tag to display the filter list\. 

1. Select the tag values, and then choose **Apply Filter** to filter the results list\.
**Note**  
For more information about filters, see [Listing and Filtering Your Resources](Using_Filtering.md)\.

## Working with Tags Using the CLI or API<a name="Using_Tags_CLI"></a>

Use the following to add, update, list, and delete the tags for your resources\. The corresponding documentation provides examples\.


| Task | AWS CLI | AWS Tools for Windows PowerShell | API Action | 
| --- | --- | --- | --- | 
|  Add or overwrite one or more tags\.  |  [create\-tags](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html)  |  [New\-EC2Tag](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html)  |  [CreateTags](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateTags.html)  | 
|  Delete one or more tags\.  |  [delete\-tags](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html)  |  [Remove\-EC2Tag](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Tag.html)  |  [DeleteTags](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DeleteTags.html)  | 
|  Describe one or more tags\.  |  [describe\-tags](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-tags.html)  |  [Get\-EC2Tag](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Tag.html)  |  [DescribeTags](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeTags.html)  | 

You can also filter a list of resources according to their tags\. The following examples demonstrate how to filter your instances using tags with the [describe\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\.

**Note**  
The way you enter JSON\-formatted parameters on the command line differs depending on your operating system\. Linux, macOS, or Unix and Windows PowerShell use the single quote \('\) to enclose the JSON data structure\. Omit the single quotes when using the commands with the Windows command line\. For more information, see [Specifying Parameter Values for the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/cli-using-param.html)\.

**Example 1: Describe instances with the specified tag key**  
The following command describes the instances with a Stack tag, regardless of the value of the tag\.

```
aws ec2 describe-instances --filters Name=tag-key,Values=Stack
```

**Example 2: Describe instances with the specified tag**  
The following command describes the instances with the tag Stack=production\.

```
aws ec2 describe-instances --filters Name=tag:Stack,Values=production
```

**Example 3: Describe instances with the specified tag value**  
The following command describes the instances with a tag with the value production, regardless of the tag key\.

```
aws ec2 describe-instances --filters Name=tag-value,Values=production
```

Some resource\-creating actions enable you to specify tags when you create the resource\. The following actions support tagging on creation\.


| Task | AWS CLI | AWS Tools for Windows PowerShell | API Action | 
| --- | --- | --- | --- | 
|  Launch one or more instances\.   |  [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html)  |  [New\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html)  |  [RunInstances](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html)  | 
|  Create an EBS volume\.  |  [create\-volume](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html)  |  [New\-EC2Volume](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Volume.html)  |  [CreateVolume](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateVolume.html)  | 

The following examples demonstrate how to apply tags when you create resources\.

**Example 4: Launch an instance and apply tags to the instance and volume**  
The following command launches an instance and applies a tag with a key of `webserver` and value of `production` to the instance\. The command also applies a tag with a key of `cost-center` and a value of `cc123` to any EBS volume that's created \(in this case, the root volume\)\.

```
aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t2.micro --key-name MyKeyPair --subnet-id subnet-6e7f829e --tag-specifications 'ResourceType=instance,Tags=[{Key=webserver,Value=production}]' 'ResourceType=volume,Tags=[{Key=cost-center,Value=cc123}]' 
```

You can apply the same tag keys and values to both instances and volumes during launch\. The following command launches an instance and applies a tag with a key of `cost-center` and a value of `cc123` to both the instance and any EBS volume that's created\.

```
aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t2.micro --key-name MyKeyPair --subnet-id subnet-6e7f829e --tag-specifications 'ResourceType=instance,Tags=[{Key=cost-center,Value=cc123}]' 'ResourceType=volume,Tags=[{Key=cost-center,Value=cc123}]' 
```

**Example 5: Create a volume and apply a tag**  
The following command creates a volume and applies two tags: `purpose` = `production`, and `cost-center` = `cc123`\.

```
aws ec2 create-volume --availability-zone us-east-1a --volume-type gp2 --size 80 --tag-specifications 'ResourceType=volume,Tags=[{Key=purpose,Value=production},{Key=cost-center,Value=cc123}]'
```