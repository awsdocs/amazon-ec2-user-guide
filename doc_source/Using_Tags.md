# Tag your Amazon EC2 resources<a name="Using_Tags"></a>

To help you manage your instances, images, and other Amazon EC2 resources, you can assign your own metadata to each resource in the form of *tags*\. Tags enable you to categorize your AWS resources in different ways, for example, by purpose, owner, or environment\. This is useful when you have many resources of the same type—you can quickly identify a specific resource based on the tags that you've assigned to it\. This topic describes tags and shows you how to create them\.

**Warning**  
Tag keys and their values are returned by many different API calls\. Denying access to `DescribeTags` doesn’t automatically deny access to tags returned by other APIs\. As a best practice, we recommend that you do not include sensitive data in your tags\.

**Topics**
+ [Tag basics](#tag-basics)
+ [Tag your resources](#tag-resources)
+ [Tag restrictions](#tag-restrictions)
+ [Tags and access management](#tag-resources-access-management)
+ [Tag your resources for billing](#tag-resources-for-billing)
+ [Work with tags using the console](#Using_Tags_Console)
+ [Work with tags using the command line](#Using_Tags_CLI)
+ [Work with instance tags in instance metadata](#work-with-tags-in-IMDS)
+ [Add tags to a resource using CloudFormation](#cloudformation-add-tag-specifications)

## Tag basics<a name="tag-basics"></a>

A tag is a label that you assign to an AWS resource\. Each tag consists of a *key* and an optional *value*, both of which you define\.

Tags enable you to categorize your AWS resources in different ways, for example, by purpose, owner, or environment\. For example, you could define a set of tags for your account's Amazon EC2 instances that helps you track each instance's owner and stack level\.

The following diagram illustrates how tagging works\. In this example, you've assigned two tags to each of your instances—one tag with the key `Owner` and another with the key `Stack`\. Each tag also has an associated value\.

![\[Tag example\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Tag_Example.png)

We recommend that you devise a set of tag keys that meets your needs for each resource type\. Using a consistent set of tag keys makes it easier for you to manage your resources\. You can search and filter the resources based on the tags you add\. For more information about how to implement an effective resource tagging strategy, see the AWS whitepaper [Tagging Best Practices](https://d1.awsstatic.com/whitepapers/aws-tagging-best-practices.pdf)\.

Tags don't have any semantic meaning to Amazon EC2 and are interpreted strictly as a string of characters\. Also, tags are not automatically assigned to your resources\. You can edit tag keys and values, and you can remove tags from a resource at any time\. You can set the value of a tag to an empty string, but you can't set the value of a tag to null\. If you add a tag that has the same key as an existing tag on that resource, the new value overwrites the old value\. If you delete a resource, any tags for the resource are also deleted\.

**Note**  
After you delete a resource, its tags might remain visible in the console, API, and CLI output for a short period\. These tags will be gradually disassociated from the resource and be permanently deleted\.

## Tag your resources<a name="tag-resources"></a>

You can tag most Amazon EC2 resources that already exist in your account\. The following [table](#tag-ec2-resources-table) lists the resources that support tagging\.

If you're using the Amazon EC2 console, you can apply tags to resources by using the **Tags** tab on the relevant resource screen, or you can use the **Tags** screen\. Some resource screens enable you to specify tags for a resource when you create the resource; for example, a tag with a key of `Name` and a value that you specify\. In most cases, the console applies the tags immediately after the resource is created \(rather than during resource creation\)\. The console may organize resources according to the `Name` tag, but this tag doesn't have any semantic meaning to the Amazon EC2 service\.

If you're using the Amazon EC2 API, the AWS CLI, or an AWS SDK, you can use the `CreateTags` EC2 API action to apply tags to existing resources\. Additionally, some resource\-creating actions enable you to specify tags for a resource when the resource is created\. If tags cannot be applied during resource creation, we roll back the resource creation process\. This ensures that resources are either created with tags or not created at all, and that no resources are left untagged at any time\. By tagging resources at the time of creation, you can eliminate the need to run custom tagging scripts after resource creation\. For more information about enabling users to tag resources on creation, see [Grant permission to tag resources during creation](supported-iam-actions-tagging.md)\.

The following table describes the Amazon EC2 resources that can be tagged, and the resources that can be tagged on creation using the Amazon EC2 API, the AWS CLI, or an AWS SDK\.


**Tagging support for Amazon EC2 resources**  

| Resource | Supports tags | Supports tagging on creation | 
| --- | --- | --- | 
|  AFI  |  Yes  |  Yes  | 
|  AMI  |  Yes  | Yes | 
|  Bundle task  |  No  | No | 
|  Capacity Reservation  |  Yes  |  Yes  | 
| Carrier gateway | Yes | Yes | 
|  Client VPN endpoint  |  Yes  |  Yes  | 
|  Client VPN route  |  No  |  No  | 
|  Customer gateway  |  Yes  | Yes | 
|  Dedicated Host  |  Yes  |  Yes  | 
|  Dedicated Host Reservation  |  Yes  | Yes | 
|  DHCP options  |  Yes  | Yes | 
|  EBS snapshot  |  Yes  | Yes | 
|  EBS volume  |  Yes  | Yes | 
|  EC2 Fleet  |  Yes  |  Yes  | 
|  Egress\-only internet gateway  |  Yes  | Yes | 
|  Elastic IP address  |  Yes  | Yes | 
|  Elastic Graphics accelerator  |  Yes  | No | 
| Instance | Yes | Yes | 
| Instance event window | Yes | Yes | 
|  Instance store volume  |  N/A  | N/A | 
|  Internet gateway  |  Yes  | Yes | 
|  IP address pool \(BYOIP\)  |  Yes  |  Yes  | 
|  Key pair  |  Yes  | Yes | 
|  Launch template  |  Yes  |  Yes  | 
|  Launch template version  |  No  |  No  | 
|  Local gateway  |  Yes  |  No  | 
|  Local gateway route table  |  Yes  |  No  | 
|  Local gateway virtual interface  |  Yes  |  No  | 
|  Local gateway virtual interface group  |  Yes  |  No  | 
|  Local gateway route table VPC association  |  Yes  |  Yes  | 
|  Local gateway route table virtual interface group association  |  Yes  |  No  | 
|  NAT gateway  |  Yes  | Yes | 
|  Network ACL  |  Yes  | Yes | 
|  Network interface  |  Yes  | Yes | 
|  Placement group  |  Yes  | Yes | 
|  Prefix list  |  Yes  |  Yes  | 
|  Reserved Instance  |  Yes  | No | 
|  Reserved Instance listing  |  No  | No | 
| Route table |  Yes  | Yes | 
|  Spot Fleet request  |  Yes  | Yes | 
|  Spot Instance request  |  Yes  | Yes | 
| Security group | Yes | Yes | 
| Security group rule | Yes | No | 
| Subnet | Yes | Yes | 
| Traffic Mirror filter | Yes | Yes | 
| Traffic Mirror session | Yes | Yes | 
| Traffic Mirror target | Yes | Yes | 
| Transit gateway | Yes | Yes | 
| Transit gateway multicast domain | Yes | Yes | 
| Transit gateway route table | Yes | Yes | 
| Transit gateway VPC attachment | Yes | Yes | 
| Virtual private gateway | Yes | Yes | 
| VPC | Yes | Yes | 
| VPC endpoint | Yes | Yes | 
| VPC endpoint service | Yes | Yes | 
| VPC endpoint service configuration | Yes | Yes | 
| VPC flow log | Yes | Yes | 
| VPC peering connection | Yes | Yes | 
| VPN connection | Yes | Yes | 

You can tag instances, volumes, and network interfaces on creation using the Amazon EC2 Launch Instances wizard in the Amazon EC2 console\. You can tag your EBS volumes on creation using the Volumes screen, or EBS snapshots using the Snapshots screen\. Alternatively, use the resource\-creating Amazon EC2 APIs \(for example, [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html)\) to apply tags when creating your resource\.

You can apply tag\-based resource\-level permissions in your IAM policies to the Amazon EC2 API actions that support tagging on creation to implement granular control over the users and groups that can tag resources on creation\. Your resources are properly secured from creation—tags are applied immediately to your resources, therefore any tag\-based resource\-level permissions controlling the use of resources are immediately effective\. Your resources can be tracked and reported on more accurately\. You can enforce the use of tagging on new resources, and control which tag keys and values are set on your resources\. 

You can also apply resource\-level permissions to the `CreateTags` and `DeleteTags` Amazon EC2 API actions in your IAM policies to control which tag keys and values are set on your existing resources\. For more information, see [Example: Tag resources](ExamplePolicies_EC2.md#iam-example-taggingresources)\. 

For more information about tagging your resources for billing, see [Using cost allocation tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing User Guide*\.

## Tag restrictions<a name="tag-restrictions"></a>

The following basic restrictions apply to tags:
+ Maximum number of tags per resource – 50
+ For each resource, each tag key must be unique, and each tag key can have only one value\.
+ Maximum key length – 128 Unicode characters in UTF\-8
+ Maximum value length – 256 Unicode characters in UTF\-8
+ Allowed characters
  + Although EC2 allows for any character in its tags, other services are more restrictive\. The allowed characters across services are: letters \(`a-z`, `A-Z`\), numbers \(`0-9`\), and spaces representable in UTF\-8, and the following characters: `+ - = . _ : / @`\.
  + If you enable instance tags in instance metadata, instance tag *keys* can only use letters \(`a-z`, `A-Z`\), numbers \(`0-9`\), and the following characters: `+ - = . , _ : @`\. Instance tag *keys* can't contain spaces or `/`, and can't comprise only `.` \(one period\), `..` \(two periods\), or `_index`\. For more information, see [Work with instance tags in instance metadata](#work-with-tags-in-IMDS)\.
+ Tag keys and values are case\-sensitive\.
+ The `aws:` prefix is reserved for AWS use\. If a tag has a tag key with this prefix, then you can't edit or delete the tag's key or value\. Tags with the `aws:` prefix do not count against your tags per resource limit\.

You can't terminate, stop, or delete a resource based solely on its tags; you must specify the resource identifier\. For example, to delete snapshots that you tagged with a tag key called `DeleteMe`, you must use the `DeleteSnapshots` action with the resource identifiers of the snapshots, such as `snap-1234567890abcdef0`\. 

When you tag public or shared resources, the tags you assign are available only to your AWS account; no other AWS account will have access to those tags\. For tag\-based access control to shared resources, each AWS account must assign its own set of tags to control access to the resource\.

You can't tag all resources\. For more information, see [Tagging support for Amazon EC2 resources](#tag-ec2-resources-table)\.

## Tags and access management<a name="tag-resources-access-management"></a>

If you're using AWS Identity and Access Management \(IAM\), you can control which users in your AWS account have permission to create, edit, or delete tags\. For more information, see [Grant permission to tag resources during creation](supported-iam-actions-tagging.md)\.

You can also use resource tags to implement attribute\-based control \(ABAC\)\. You can create IAM policies that allow operations based on the tags for the resource\. For more information, see [Control access to EC2 resources using resource tags](control-access-with-tags.md)\.

## Tag your resources for billing<a name="tag-resources-for-billing"></a>

You can use tags to organize your AWS bill to reflect your own cost structure\. To do this, sign up to get your AWS account bill with tag key values included\. For more information about setting up a cost allocation report with tags, see [Monthly cost allocation report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/configurecostallocreport.html) in the *AWS Billing User Guide*\. To see the cost of your combined resources, you can organize your billing information based on resources that have the same tag key values\. For example, you can tag several resources with a specific application name, and then organize your billing information to see the total cost of that application across several services\. For more information, see [Using cost allocation tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing User Guide*\.

**Note**  
If you've just enabled reporting, data for the current month is available for viewing after 24 hours\.

Cost allocation tags can indicate which resources are contributing to costs, but deleting or deactivating resources doesn't always reduce costs\. For example, snapshot data that is referenced by another snapshot is preserved, even if the snapshot that contains the original data is deleted\. For more information, see [Amazon Elastic Block Store volumes and snapshots](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/checklistforunwantedcharges.html#checkebsvolumes) in the *AWS Billing User Guide*\.

**Note**  
Elastic IP addresses that are tagged do not appear on your cost allocation report\.

## Work with tags using the console<a name="Using_Tags_Console"></a>

Using the Amazon EC2 console, you can see which tags are in use across all of your Amazon EC2 resources in the same Region\. You can view tags by resource and by resource type, and you can also view how many items of each resource type are associated with a specified tag\. You can also use the Amazon EC2 console to apply or remove tags from one or more resources at a time\.

For more information about using filters when listing your resources, see [List and filter your resources](Using_Filtering.md)\.

For ease of use and best results, use Tag Editor in the AWS Management Console, which provides a central, unified way to create and manage your tags\. For more information, see [Tag Editor](https://docs.aws.amazon.com/awsconsolehelpdocs/latest/gsg/tag-editor.html) in *Getting Started with the AWS Management Console*\.

**Topics**
+ [Display tags](#displaying-tags)
+ [Add and delete tags on an individual resource](#adding-or-deleting-tags)
+ [Add and delete tags to a group of resources](#adding-or-deleting-tags-group)
+ [Add a tag when you launch an instance](#instance-details-tags)
+ [Filter a list of resources by tag](#filtering-the-list-by-tag)

### Display tags<a name="displaying-tags"></a>

You can display tags in two different ways in the Amazon EC2 console\. You can display the tags for an individual resource or for all resources\.

**Display tags for individual resources**  
When you select a resource\-specific page in the Amazon EC2 console, it displays a list of those resources\. For example, if you select **Instances** from the navigation pane, the console displays your Amazon EC2 instances\. When you select a resource from one of these lists \(for example, an instance\), if the resource supports tags, you can view and manage its tags\. On most resource pages, you can view the tags by choosing the **Tags** tab\.

You can add a column to the resource list that displays all values for tags with the same key\. You can use this column sort and filter the resource list by the tag\.

------
#### [ New console ]
+ Choose the **Preferences** gear\-shaped icon in the top right corner of the screen\. In the **Preferences** dialog box, under **Tag columns**, select one of more tag keys, and then choose **Confirm**\.

------
#### [ Old console ]

There are two ways to add a new column to the resource list to display your tags:
+ On the **Tags** tab, select **Show Column**\. A new column is added to the console\.
+ Choose the **Show/Hide Columns** gear\-shaped icon, and in the **Show/Hide Columns** dialog box, select the tag key under **Your Tag Keys**\.

------

**Display tags for all resources**  
You can display tags across all resources by selecting **Tags** from the navigation pane in the Amazon EC2 console\. The following image shows the **Tags** pane, which lists all tags in use by resource type\.

![\[The Tags pane in the Amazon EC2 console\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Tags_Pane.png)

### Add and delete tags on an individual resource<a name="adding-or-deleting-tags"></a>

You can manage tags for an individual resource directly from the resource's page\. 

**To add a tag to an individual resource**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region that meets your needs\. This choice is important because some Amazon EC2 resources can be shared between Regions, while others can't\. For more information, see [Resource locations](resources.md)\.

1. In the navigation pane, select a resource type \(for example, **Instances**\)\.

1. Select the resource from the resource list and choose the **Tags** tab\.

1. Choose **Manage tags**, **Add tag**\. Enter the key and value for the tag\. When you are finished adding tags, choose **Save**\.

**To delete a tag from an individual resource**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region that meets your needs\. This choice is important because some Amazon EC2 resources can be shared between Regions, while others can't\. For more information, see [Resource locations](resources.md)\.

1. In the navigation pane, choose a resource type \(for example, **Instances**\)\.

1. Select the resource from the resource list and choose the **Tags** tab\.

1. Choose **Manage tags**\. For each tag, choose **Remove**\. When you are finished removing tags, choose **Save**\.

### Add and delete tags to a group of resources<a name="adding-or-deleting-tags-group"></a>

**To add a tag to a group of resources**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region that meets your needs\. This choice is important because some Amazon EC2 resources can be shared between Regions, while others can't\. For more information, see [Resource locations](resources.md)\.

1. In the navigation pane, choose **Tags**\.

1. At the top of the content pane, choose **Manage Tags**\.

1. For **Filter**, select the type of resource \(for example, instances\)\.

1. In the resources list, select the check box next to each resource\.

1. Under **Add Tag**, enter the tag key and value and choose **Add Tag**\.
**Note**  
If you add a new tag with the same tag key as an existing tag, the new tag overwrites the existing tag\.

**To remove a tag from a group of resources**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region that meets your needs\. This choice is important because some Amazon EC2 resources can be shared between Regions, while others can't\. For more information, see [Resource locations](resources.md)\.

1. In the navigation pane, choose **Tags**, **Manage Tags**\.

1. To view the tags in use, select the **Show/Hide Columns** gear\-shaped icon, and in the **Show/Hide Columns** dialog box, select the tag keys to view and choose **Close**\.

1. For **Filter**, select the type of resource \(for example, instances\)\.

1. In the resource list, select the check box next to each resource\.

1. Under **Remove Tag**, enter the tag key and choose **Remove Tag**\.

### Add a tag when you launch an instance<a name="instance-details-tags"></a>

**To add a tag using the Launch Wizard**

1. From the navigation bar, select the Region for the instance\. This choice is important because some Amazon EC2 resources can be shared between Regions, while others can't\. Select the Region that meets your needs\. For more information, see [Resource locations](resources.md)\.

1. Choose **Launch Instance**\.

1. The **Choose an Amazon Machine Image \(AMI\)** page displays a list of basic configurations called Amazon Machine Images \(AMIs\)\. Select the AMI to use and choose **Select**\. For more information, see [Find a Linux AMI](finding-an-ami.md)\.

1. On the **Configure Instance Details** page, configure the instance settings as necessary, and then choose **Next: Add Storage**\.

1. On the **Add Storage** page, you can specify additional storage volumes for your instance\. Choose **Next: Add Tags** when done\.

1. On the **Add Tags** page, specify tags for the instance, the volumes, or both\. Choose **Add another tag** to add more than one tag to your instance\. Choose **Next: Configure Security Group** when you are done\. 

1. On the **Configure Security Group** page, you can choose from an existing security group that you own, or let the wizard create a new security group for you\. Choose **Review and Launch** when you are done\.

1. Review your settings\. When you're satisfied with your selections, choose **Launch**\. Select an existing key pair or create a new one, select the acknowledgment check box, and then choose **Launch Instances**\.

### Filter a list of resources by tag<a name="filtering-the-list-by-tag"></a>

You can filter your list of resources based on one or more tag keys and tag values\.

**To filter a list of resources by tag**

1. In the navigation pane, select a resource type \(for example, **Instances**\)\.

1. Choose the search field\.

1. Choose the tag key from in the list\.

1. Choose the corresponding tag value from the list\.

1. When you are finished, remove the filter\.

For more information about filters, see [List and filter your resources](Using_Filtering.md)\.

## Work with tags using the command line<a name="Using_Tags_CLI"></a>

You can add tags to many EC2 resource when you create them, using the tag specifications parameter for the create command\. You can view the tags for a resource using the describe command for the resource\. You can also add, update, or delete tags for your existing resources using the following commands\.


| Task | AWS CLI | AWS Tools for Windows PowerShell | 
| --- | --- | --- | 
|  Add or overwrite one or more tags  |  [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html)  |  [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html)  | 
|  Delete one or more tags  |  [delete\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html)  |  [Remove\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Tag.html)  | 
|  Describe one or more tags  |  [describe\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-tags.html)  |  [Get\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Tag.html)  | 

**Topics**
+ [Add tags on resource creation](#tag-on-create-examples)
+ [Add tags to an existing resource](#create-tag-examples)
+ [Describe tagged resources](#describe-tag-examples)

### Add tags on resource creation<a name="tag-on-create-examples"></a>

The following examples demonstrate how to apply tags when you create resources\.

**Note**  
The way you enter JSON\-formatted parameters on the command line differs depending on your operating system\.  
Linux, macOS, or Unix and Windows PowerShell – Use single quotes \('\) to enclose the JSON data structure\.
Windows – Omit the single quotes when using the commands with the Windows command line\.
For more information, see [Specifying parameter values for the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-using-param.html)\.

**Example: Launch an instance and apply tags to the instance and volume**  
The following [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command launches an instance and applies a tag with the key **webserver** and the value **production** to the instance\. The command also applies a tag with the key **cost\-center** and the value **cc123** to any EBS volume that's created \(in this case, the root volume\)\.  

```
aws ec2 run-instances \
    --image-id ami-abc12345 \
    --count 1 \
    --instance-type t2.micro \
    --key-name MyKeyPair \
    --subnet-id subnet-6e7f829e \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=webserver,Value=production}]' 'ResourceType=volume,Tags=[{Key=cost-center,Value=cc123}]'
```
You can apply the same tag keys and values to both instances and volumes during launch\. The following command launches an instance and applies a tag with a key of **cost\-center** and a value of **cc123** to both the instance and any EBS volume that's created\.  

```
aws ec2 run-instances \
    --image-id ami-abc12345 \
    --count 1 \
    --instance-type t2.micro \
    --key-name MyKeyPair \
    --subnet-id subnet-6e7f829e \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=cost-center,Value=cc123}]' 'ResourceType=volume,Tags=[{Key=cost-center,Value=cc123}]'
```

**Example: Create a volume and apply a tag**  
The following [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) command creates a volume and applies two tags: **purpose=production** and **cost\-center=cc123**\.  

```
aws ec2 create-volume \
    --availability-zone us-east-1a \
    --volume-type gp2 \
    --size 80 \
    --tag-specifications 'ResourceType=volume,Tags=[{Key=purpose,Value=production},{Key=cost-center,Value=cc123}]'
```

### Add tags to an existing resource<a name="create-tag-examples"></a>

The following examples demonstrate how to add tags to an existing resource using the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) command\.

**Example: Add a tag to a resource**  
The following command adds the tag **Stack=production** to the specified image, or overwrites an existing tag for the AMI where the tag key is **Stack**\. If the command succeeds, no output is returned\.  

```
aws ec2 create-tags \
    --resources ami-78a54011 \
    --tags Key=Stack,Value=production
```

**Example: Add tags to multiple resources**  
This example adds \(or overwrites\) two tags for an AMI and an instance\. One of the tags contains just a key \(**webserver**\), with no value \(we set the value to an empty string\)\. The other tag consists of a key \(**stack**\) and value \(**Production**\)\. If the command succeeds, no output is returned\.  

```
aws ec2 create-tags \
    --resources ami-1a2b3c4d i-1234567890abcdef0 \
    --tags Key=webserver,Value=  Key=stack,Value=Production
```

**Example: Add tags with special characters**  
This example adds the tag **\[Group\]=test** to an instance\. The square brackets \(**\[** and **\]**\) are special characters, which must be escaped\.  
If you are using Linux or OS X, to escape the special characters, enclose the element with the special character with double quotes \(**"**\), and then enclose the entire key and value structure with single quotes \(**'**\)\.  

```
aws ec2 create-tags \
    --resources i-1234567890abcdef0 \
    --tags 'Key="[Group]",Value=test'
```
If you are using Windows, to escape the special characters, enclose the element that has special characters with double quotes \("\), and then precede each double quote character with a backslash \(**\\**\) as follows:  

```
aws ec2 create-tags ^
    --resources i-1234567890abcdef0 ^
    --tags Key=\"[Group]\",Value=test
```
If you are using Windows PowerShell, to escape the special characters, enclose the value that has special characters with double quotes \(**"**\), precede each double quote character with a backslash \(**\\**\), and then enclose the entire key and value structure with single quotes \(**'**\) as follows:  

```
aws ec2 create-tags `
    --resources i-1234567890abcdef0 `
    --tags 'Key=\"[Group]\",Value=test'
```

### Describe tagged resources<a name="describe-tag-examples"></a>

The following examples show you how to use filters with the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) to view instances with specific tags\. All EC2 describe commands use this syntax to filter by tag across a single resource type\. Alternatively, you can use the [describe\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-tags.html) command to filter by tag across EC2 resource types\.

**Example: Describe instances with the specified tag key**  
The following command describes the instances with a **Stack** tag, regardless of the value of the tag\.  

```
aws ec2 describe-instances \
    --filters Name=tag-key,Values=Stack
```

**Example: Describe instances with the specified tag**  
The following command describes the instances with the tag **Stack=production**\.  

```
aws ec2 describe-instances \
    --filters Name=tag:Stack,Values=production
```

**Example: Describe instances with the specified tag value**  
The following command describes the instances with a tag with the value **production**, regardless of the tag key\.  

```
aws ec2 describe-instances \
    --filters Name=tag-value,Values=production
```

**Example: Describe all EC2 resources with the specified tag**  
The following command describes all EC2 resources with the tag **Stack=Test**\.  

```
aws ec2 describe-tags \
    --filters Name=key,Values=Stack Name=value,Values=Test
```

## Work with instance tags in instance metadata<a name="work-with-tags-in-IMDS"></a>

You can access an instance's tags from the instance metadata\. By accessing tags from the instance metadata, you no longer need to use the `DescribeInstances` or `DescribeTags` API calls to retrieve tag information, which reduces your API transactions per second, and lets your tag retrievals scale with the number of instances that you control\. Furthermore, local processes that are running on an instance can view the instance's tag information directly from the instance metadata\.

By default, tags are not available from the instance metadata; you must explicitly allow access\. You can allow access at instance launch, or after launch on a running or stopped instance\. You can also allow access to tags by specifying this in a launch template\. Instances that are launched by using the template allow access to tags in the instance metadata\.

If you add or remove an instance tag, the instance metadata is updated while the instance is running for [instances built on the Nitro System](instance-types.md#ec2-nitro-instances), without needing to stop and then start the instance\. For all other instances, to update the tags in the instance metadata, you must stop and then start the instance\.

**Topics**
+ [Allow access to tags in instance metadata](#allow-access-to-tags-in-IMDS)
+ [Turn off access to tags in instance metadata](#turn-off-access-to-tags-in-IMDS)
+ [Retrieve tags from instance metadata](#retrieve-tags-from-IMDS)

### Allow access to tags in instance metadata<a name="allow-access-to-tags-in-IMDS"></a>

By default, there is no access to instance tags in the instance metadata\. For each instance, you must explicitly allow access by using one of the following methods\.

**To allow access to tags in instance metadata using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**\.

1. Select an instance, and then choose** Actions**, **Instance settings**, **Allow tags in instance metadata**\.

1. To allow access to tags in instance metadata, select the **Allow** check box\.

1. Choose **Save**\.

**To allow access to tags in instance metadata at launch using the AWS CLI**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and set `InstanceMetadataTags` to `enabled`\. 

```
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --instance-type c3.large \
    ...
    --metadata-options "InstanceMetadataTags=enabled"
```

**To allow access to tags in instance metadata on a running or stopped instance using the AWS CLI**  
Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) command and set `--instance-metadata-tags` to `enabled`\. 

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-123456789example \
    --instance-metadata-tags enabled
```

### Turn off access to tags in instance metadata<a name="turn-off-access-to-tags-in-IMDS"></a>

To turn off access to instance tags in the instance metadata, use one of the following methods\. You don't need to turn off access to instance tags on instance metadata at launch because it's turned off by default\.

**To turn off access to tags in instance metadata using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**\.

1. Select an instance, and then choose** Actions**, **Instance settings**, **Allow tags in instance metadata**\.

1. To turn off access to tags in instance metadata, clear the **Allow** check box\.

1. Choose **Save**\.

**To turn off access to tags in instance metadata using the AWS CLI**  
Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) command and set `--instance-metadata-tags` to `disabled`\. 

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-123456789example \
    --instance-metadata-tags disabled
```

### Retrieve tags from instance metadata<a name="retrieve-tags-from-IMDS"></a>

If instance tags are allowed in the instance metadata, the `tags/instance` category is accessible from the instance metadata\. For examples on how to retrieve tags from the instance metadata, see [Get the instance tags for an instance](instancedata-data-retrieval.md#instance-metadata-ex-7)\.

## Add tags to a resource using CloudFormation<a name="cloudformation-add-tag-specifications"></a>

With Amazon EC2 resource types, you specify tags using either a `Tags` or `TagSpecifications` property\.

The following examples add the tag **Stack=Production** to [AWS::EC2::Instance](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-instance.html) using its `Tags` property\.

**Example: Tags in YAML**  

```
Tags:
  - Key: "Stack"
    Value: "Production"
```

**Example: Tags in JSON**  

```
"Tags": [
    {
        "Key": "Stack",
        "Value": "Production"
    }
]
```

The following examples add the tag **Stack=Production** to [AWS::EC2::LaunchTemplate LaunchTemplateData](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-launchtemplate-launchtemplatedata.html) using its `TagSpecifications` property\.

**Example: TagSpecifications in YAML**  

```
TagSpecifications:
  - ResourceType: "instance"
    Tags:
    - Key: "Stack"
      Value: "Production"
```

**Example: TagSpecifications in JSON**  

```
"TagSpecifications": [
    {
        "ResourceType": "instance",
        "Tags": [
            {
                "Key": "Stack",
                "Value": "Production"
            }
        ]
    }
]
```