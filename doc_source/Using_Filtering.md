# List and filter your resources<a name="Using_Filtering"></a>

You can get a list of some types of resources using the Amazon EC2 console\. You can get a list of each type of resource using its corresponding command or API action\. If you have many resources, you can filter the results to include, or exclude, only the resources that match certain criteria\.

**Topics**
+ [List and filter resources using the console](#advancedsearch)
+ [List and filter using the CLI and API](#Filtering_Resources_CLI)
+ [List and filter resources across Regions using Amazon EC2 Global View](#global-view)

## List and filter resources using the console<a name="advancedsearch"></a>

**Contents**
+ [List resources using the console](#listing-resources)
+ [Filter resources using the console](#console-filter)

### List resources using the console<a name="listing-resources"></a>

You can view the most common Amazon EC2 resource types using the console\. To view additional resources, use the command line interface or the API actions\.

**To list EC2 resources using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose the option that corresponds to the resource type\. For example, to list your instances, choose **Instances**\.

   The page displays all resources of the selected resource type\.

### Filter resources using the console<a name="console-filter"></a>

**To filter a list of resources**

1. In the navigation pane, select a resource type \(for example, **Instances**\)\.

1. Choose the search field\.

1. Select the filter from the list\.

1. Select an operator, for example, **= \(Equals\)**\. Some attributes have more available operators to select\. Note that not all screens support selecting an operator\.

1. Select a filter value\.

1. To edit a selected filter, choose the filter token \(blue box\), make the required edits, and then choose **Apply**\. Note that not all screens support editing the selected filter\.  
![\[Edit a filter.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/filter-edit.png)

1. When you are finished, remove the filter\.

The search and filter functionality differs slightly between the *old* and *new* Amazon EC2 console\.

#### New console<a name="console-filters-new"></a>

The new console supports two types of filtering\.
+ *API filtering* happens on the server side\. The filtering is applied on the API call, which reduces the number of resources returned by the server\. It allows for quick filtering across large sets of resources, and it can reduce data transfer time and cost between the server and the browser\. API filtering supports **=** \(equals\) and **:** \(contains\) operators, and is always case sensitive\.
+ *Client filtering* happens on the client side\. It enables you to filter down on data that is already available in the browser \(in other words, data that has already been returned by the API\)\. Client filtering works well in conjunction with an API filter to filter down to smaller data sets in the browser\. In addition to **=** \(equals\) and **:** \(contains\) operators, client filtering can also support range operators, such as **>=** \(greater than or equal\), and negation \(inverse\) operators, such as **\!=** \(does not equal\)\.

The new Amazon EC2 console supports the following types of searches:

**Search by keyword**  
Searching by keyword is a free text search that lets you search for a value across all of your resources' attributes or tags, without specifying an attribute or tag key to search\.   
All keyword searches use *client filtering*\. 
To search by keyword, enter or paste what you’re looking for in the search field, and then choose **Enter**\. For example, searching for `123` matches all instances that have *123* in any of their attributes, such as an IP address, instance ID, VPC ID, or AMI ID, or in any of their tags, such as the Name\. If your free text search returns unexpected matches, apply additional filters\.

**Search by attribute**  
Searching by an attribute lets you search a specific attribute across all of your resources\.   
Attribute searches use either *API filtering* or *client filtering*, depending on the selected attribute\. When performing an attribute search, the attributes are grouped accordingly\.
For example, you can search the **Instance state** attribute for all of your instances to return only instances that are in the `stopped` state\. To do this:  

1. In the search field on the **Instances** screen, start entering `Instance state`\. As you enter the characters, the two types of filters appear for **Instance state**: **API filters** and **Client filters**\.

1. To search on the server side, choose **Instance state** under **API filters**\. To search on the client side, choose **Instance state \(client\)** under **Client filters**\.

   A list of possible operators for the selected attribute appears\.

1. Choose the **=** \(Equals\) operator\.

   A list of possible values for the selected attribute and operator appears\.

1. Choose **stopped** from the list\.

**Search by tag**  
Searching by a tag lets you filter the resources in the currently displayed table by a tag key or a tag value\.  
Tag searches use either *API filtering* or *client filtering*, depending on the settings in the Preferences window\.  

**To ensure API filtering for tags**

1. Open the **Preferences** window\.

1. Clear the **Use regular expression matching** check box\. If this check box is selected, client filtering is performed\.

1. Select the **Use case sensitive matching** check box\. If this check box is cleared, client filtering is performed\.

1. Choose **Confirm**\.
When searching by tag, you can use the following values:  
+ **\(empty\)** – Find all resources with the specified tag key, but there must be no tag value\.
+ **All values** – Find all resources with the specified tag key and any tag value\.
+ **Not tagged** – Find all resources that do not have the specified tag key\.
+ The displayed value – Find all resources with the specified tag key and the specified tag value\.

You can use the following techniques to enhance or refine your searches:

**Inverse search**  
Inverse searches let you search for resources that do **not** match a specified value\. In the **Instances** and **AMIs** screens, inverse searches are performed by selecting the **\!=** \(Does not equal\) or **\!:** \(Does not contain\) operator and then selecting a value\. In other screens, inverse searches are peformed by prefixing the search keyword with the exclamation mark \(\!\) character\.  
Inverse search is supported with keyword searches and attribute searches on *client* filters only\. It is not supported with attribute searches on API filters\.
For example, you can search the **Instance state** attribute for all of your instances to exclude all instances that are in the `terminated` state\. To do this:  

1. In the search field on the **Instances** screen, start entering `Instance state`\. As you enter the characters, the two types of filters appear for **Instance state**: **API filters** and **Client filters**\.

1. Under **Client filters**, choose **Instance state \(client\)**\. Inverse search is only supported on *client* filters\.

   A list of possible operators for the selected attribute appears\.

1. Choose **\!=** \(Does not equal\), and then choose **terminated**\.
To filter instances based on an instance state attribute, you can also use the search icons \( ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/search.png) \) in the **Instance state** column\. The search icon with a plus sign \( **\+** \) displays all the instances that *match* that attribute\. The search icon with a minus sign \( **\-** \) *excludes* all instances that match that attribute\.  
Here is another example of using the inverse search: To list all instances that are **not** assigned the security group named `launch-wizard-1`, under **Client filters**, search by the **Security group name** attribute, choose **\!=**, and in the search bar, enter `launch-wizard-1`\.

**Partial search**  
With partial searches, you can search for partial string values\. To perform a partial search, enter only a part of the keyword that you want to search for\. On the **Instances** and **AMIs** screens, partial searches can only be performed with the **:** \(Contains\) operator\. On other screens, you can select the client filter attribute and immediately enter only a part of the keyword that you want to search for\. For example, on the **Instance type** screen, to search for all `t2.micro`, `t2.small`, and `t2.medium` instances, search by the **Instance Type** attribute, and for the keyword, enter `t2`\.

**Regular expression search**  
To use regular expression searches, you must select the **Use regular expression matching** check box in the Preferences window\.  
Regular expressions are useful when you need to match the values in a field with a specific pattern\. For example, to search for a value that starts with `s`, search for `^s`\. To search for a value that ends with `xyz`, search for `xyz$`\. Or to search for a value that starts with a number that is followed by one or more characters, search for `[0-9]+.*`\.  
Regular expression search is supported with keyword searches and attribute searches on client filters only\. It is not supported with attribute searches on API filters\.

**Case\-sensitive search**  
To use case\-sensitive searches, you must select the **Use case sensitive matching** check box in the **Preferences** window\. The case\-sensitive preference only applies to client and tag filters\.  
API filters are always case\-sensitive\.

**Wildcard search**  
Use the `*` wildcard to match zero or more characters\. Use the `?` wildcard to match zero or one character\. For example, if you have a data set with the values `prod`, `prods`, and `production`, a search of `prod*` matches all values, whereas `prod?` matches only `prod` and `prods`\. To use the literal values, escape them with a backslash \(\\\)\. For example, "`prod\*`" would match `prod*`\.  
Wildcard search is supported with attribute and tag searches on API filters only\. It is not supported with keyword searches, and with attribute and tag searches on client filters\. 

**Combining searches**  
In general, multiple filters with the same attribute are automatically joined with `OR`\. For example, searching for `Instance State : Running` and `Instance State : Stopped` returns all instances that are either running OR stopped\. To join search with `AND`, search across different attributes\. For example, searching for `Instance State : Running` and `Instance Type : c4.large` returns only instances that are of type `c4.large` AND that are in the running state\.

#### Old console<a name="console-filters-old"></a>

The old Amazon EC2 console supports the following types of searches:

**Search by keyword**  <a name="old-keyword"></a>
Searching by keyword is a free text search that lets you search for a value across all of your resources' attributes\. To search by keyword, enter or paste what you’re looking for in the search field, and then choose **Enter**\. For example, searching for `123` matches all instances that have *123* in any of their attributes, such as an IP address, instance ID, VPC ID, or AMI ID\. If your free text search returns unexpected matches, apply additional filters\.

**Search by attributes**  <a name="old-attribute"></a>
Searching by an attribute lets you search a specific attribute across all of your resources\. For example, you can search the **State** attribute for all of your instances to return only instances that are in the `stopped` state\. To do this:  

1. In the search field on the Instances screen, start entering `Instance State`\. As you enter characters, a list of matching attributes appears\.

1. Select **Instance State **from the list\. A list of possible values for the selected attribute appears\.

1. Select **Stopped** from the list\.

You can use the following techniques to enhance or refine your searches:

Inverse search  <a name="inverse"></a>
Inverse searches let you search for resources that do **not** match a specified value\. Inverse searches are performed by prefixing the search keyword with the exclamation mark \(\!\) character\. For example, to list all instances that are **not** terminated, search by the **Instance State** attribute, and for the keyword, enter `!Terminated`\.

Partial search  <a name="partial"></a>
With partial searches, you can search for partial string values\. To perform a partial search, enter only a part of the keyword you want to search for\. For example, to search for all `t2.micro`, `t2.small`, and `t2.medium` instances, search by the **Instance Type** attribute, and for the keyword, enter `t2`\.

Regular expression search  <a name="regular"></a>
Regular expressions are useful when you need to match the values in a field with a specific pattern\. For example, to search for all instances that have an attribute value that starts with `s`, search for `^s`\. Or to search for all instances that have an attribute value that ends with `xyz`, search for `xyz$`\. Regular expression searches are not case\-sensitive\.

Combining searches  <a name="combined"></a>
In general, multiple filters with the same attribute are automatically joined with `OR`\. For example, searching for `Instance State : Running` and `Instance State : Stopped` returns all instances that are either running OR stopped\. To join search with `AND`, search across different attributes\. For example, searching for `Instance State : Running` and `Instance Type : c4.large` returns only instances that are of type `c4.large` AND that are in the stopped state\.

## List and filter using the CLI and API<a name="Filtering_Resources_CLI"></a>

Each resource type has a corresponding CLI command and API action that you use to list resources of that type\. The resulting lists of resources can be long, so it can be faster and more useful to filter the results to include only the resources that match specific criteria\.

**Filtering considerations**
+ You can specify multiple filters and multiple filter values in a single request\.
+ You can use wildcards with the filter values\. An asterisk \(\*\) matches zero or more characters, and a question mark \(?\) matches zero or one character\.
+ Filter values are case sensitive\.
+ Your search can include the literal values of the wildcard characters; you just need to escape them with a backslash before the character\. For example, a value of `\*amazon\?\\` searches for the literal string `*amazon?\`\.

**Supported filters**

To see the supported filters for each Amazon EC2 resource, see the following documentation:
+ AWS CLI: The `describe` commands in the [AWS CLI Command Reference\-Amazon EC2](https://docs.aws.amazon.com/cli/latest/reference/ec2/index.html)\.
+ Tools for Windows PowerShell: The `Get` commands in the [AWS Tools for PowerShell Cmdlet Reference\-Amazon EC2](https://docs.aws.amazon.com/powershell/latest/reference/items/EC2_cmdlets.html)\.
+ Query API: The `Describe` API actions in the [Amazon EC2 API Reference](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/)\.

**Example: Specify a single filter**  
You can list your Amazon EC2 instances using [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html)\. Without filters, the response contains information for all of your resources\. You can use the following command to include only the running instances in your output\.  

```
aws ec2 describe-instances --filters Name=instance-state-name,Values=running
```
To list only the instance IDs for your running instances, add the `--query` parameter as follows\.  

```
aws ec2 describe-instances --filters Name=instance-state-name,Values=running --query "Reservations[*].Instances[*].InstanceId" --output text
```
The following is example output\.  

```
i-0ef1f57f78d4775a4
i-0626d4edd54f1286d
i-04a636d18e83cfacb
```

**Example: Specify multiple filters or filter values**  
If you specify multiple filters or multiple filter values, the resource must match all filters to be included in the results\.  
You can use the following command to list all instances whose type is either `m5.large` or `m5d.large`\.  

```
aws ec2 describe-instances --filters Name=instance-type,Values=m5.large,m5d.large
```
You can use the following command to list all stopped instances whose type is `t2.micro`\.  

```
aws ec2 describe-instances --filters Name=instance-state-name,Values=stopped Name=instance-type,Values=t2.micro
```

**Example: Use wildcards in a filter value**  
If you specify `database` as the filter value for the `description` filter when describing EBS snapshots using [describe\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshots.html), the command returns only the snapshots whose description is "database"\.  

```
aws ec2 describe-snapshots --filters Name=description,Values=database
```
The \* wildcard matches zero or more characters\. If you specify `*database*` as the filter value, the command returns only snapshots whose description includes the word database\.  

```
aws ec2 describe-snapshots --filters Name=description,Values=*database*
```
The ? wildcard matches exactly 1 character\. If you specify `database?` as the filter value, the command returns only snapshots whose description is "database" or "database" followed by one character\.  

```
aws ec2 describe-snapshots --filters Name=description,Values=database?
```
If you specify `database????`, the command returns only snapshots whose description is "database" followed by up to four characters\. It excludes descriptions with "database" followed by five or more characters\.  

```
aws ec2 describe-snapshots --filters Name=description,Values=database????
```

**Example: Filter based on date**  
With the AWS CLI, you can use JMESPath to filter results using expressions\. For example, the following [describe\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshots.html) command displays the IDs of all snapshots created by your AWS account \(represented by *123456789012*\) before the specified date \(represented by *2020\-03\-31*\)\. If you do not specify the owner, the results include all public snapshots\.  

```
aws ec2 describe-snapshots --filters Name=owner-id,Values=123456789012 --query "Snapshots[?(StartTime<='2020-03-31')].[SnapshotId]" --output text
```
The following command displays the IDs of all snapshots created in the specified date range\.  

```
aws ec2 describe-snapshots --filters Name=owner-id,Values=123456789012 --query "Snapshots[?(StartTime>='2019-01-01') && (StartTime<='2019-12-31')].[SnapshotId]" --output text
```

**Filter based on tags**  
For examples of how to filter a list of resources according to their tags, see [Work with tags using the command line](Using_Tags.md#Using_Tags_CLI)\.

## List and filter resources across Regions using Amazon EC2 Global View<a name="global-view"></a>

Amazon EC2 Global View enables you to view some of your Amazon EC2 and Amazon VPC resources across a single AWS Region, or across multiple Regions in a single console\. Using Amazon EC2 Global View, you can view a summary of all of your VPCs, subnets, instances, security groups, and volumes across all of the Regions for which your AWS account is enabled\. Amazon EC2 Global View also provides *global search* functionality that lets you search for specific resources or specific resource types across multiple Regions simultaneously\. 

Amazon EC2 Global View does not let you modify resources in any way\.

**Required permissions**  
An IAM user must have the following permissions to use Amazon EC2 Global View\.

```
{
  "Version": "2012-10-17",
  "Statement": [
  {
    "Effect": "Allow",
    "Action": [
    "ec2:DescribeInstances",
    "ec2:DescribeVpcs",
    "ec2:DescribeRegions",
    "ec2:DescribeVolumes",
    "ec2:DescribeSubnets",
    "ec2:DescribeSecurityGroups"
  ],
  "Resource": "*"
  }]
}
```

**To use Amazon EC2 Global View**  
Open the Amazon EC2 Global View console at [ https://console\.aws\.amazon\.com/ec2globalview/home](https://console.aws.amazon.com/ec2globalview/home)\.

The console consists of two tabs:
+ **Region explorer**—This tab includes the following sections:
  + **Resource summary**—Provides a high\-level overview of your resources across all Regions\.

    **Enabled Regions** indicates the number of Regions for which your AWS account is enabled\. The remaining fields indicate the number of resources that you currently have in those Regions\. Choose any of the links to view the resources of that type across all Regions\. For example, if the link below the **Instances** label is **29 in 10 Regions**, it indicates that you currently have `29` instances across `10` Regions\. Choose the link to view a list of all 29 instances\.
  + **Resource counts per Region**—Lists all of the AWS Regions \(including those for which your account is not enabled\) and provides totals for each resource type for each Region\.

    Choose a Region name to view all resources of all types for that specific Region\. For example, choose **Africa \(Cape Town\) af\-south\-1** to view all VPCs, subnets, instances, security groups, and volumes in that Region\. Alternatively, select a Region and choose **View resources for selected Region**\.

    Choose the value for a specific resource type in a specific Region to view only resources of that type in that Region\. For example, choose the value for Instances for **Africa \(Cape Town\) af\-south\-1** to view only the instances in that Region\.
+ **Global search**—This tab enables you to search for specific resources or specific resource types across a single Region or across multiple Regions\. It also enables you to view details for a specific resource\.

  To search for resources, enter the search criteria in the field preceding the grid\. You can search by Region, by resource type, and by the tags assigned to resources\.

  To view the details for a specific resource, select it in the grid\. You can also choose the resource ID of a resource to open it in its respective console\. For example, choose an instance ID to open the instance in the Amazon EC2 console, or choose a subnet ID to open the subnet in the Amazon VPC console\.