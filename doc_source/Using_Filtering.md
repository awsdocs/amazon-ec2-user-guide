# Listing and filtering your resources<a name="Using_Filtering"></a>

You can get a list of some types of resources using the Amazon EC2 console\. You can get a list of each type of resource using its corresponding command or API action\. If you have many resources, you can filter the results to include only the resources that match certain criteria\.

**Topics**
+ [Listing and filtering resources using the console](#advancedsearch)
+ [Listing and filtering using the CLI and API](#Filtering_Resources_CLI)

## Listing and filtering resources using the console<a name="advancedsearch"></a>

**Contents**
+ [Listing resources using the console](#listing-resources)
+ [Filtering resources using the console](#console-filter)

### Listing resources using the console<a name="listing-resources"></a>

You can view the most common Amazon EC2 resource types using the console\. To view additional resources, use the command line interface or the API actions\.

**To list EC2 resources using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose the option that corresponds to the resource type\. For example, to list your instances, choose **Instances**\.

1. The page displays all resources of the selected resource type\.

### Filtering resources using the console<a name="console-filter"></a>

Search functionality differs slightly between the *old* and *new* Amazon EC2 console\.

#### New console<a name="console-filters-new"></a>

The new console supports two types of filtering\.
+ *API filtering* happens on the server side\. The filtering is applied on the API call and it reduces the number of resources returned by the server\. It allows for quick filtering across large sets of resources, and it can reduce data transfer time and cost between the server and the browser\.
+ *Client filtering* happens on the client side\. It enables you to filter down on data that is already available in the browser \(in other words, data that has already been returned by the API\)\. Client filtering works well in conjunction with an API filter to filter down to smaller data sets in the browser\.

The new Amazon EC2 console supports the following types of searches:

**Search by keyword**  
Searching by keyword is a free text search that lets you search for a value across all of your resources' attributes, without specifying an attribute to search\.   
All keyword searches use *client filtering*\. 
To search by keyword, enter or paste what you’re looking for in the search field, and then choose **Enter**\. For example, searching for `123` matches all instances that have *123* in any of their attributes, such as an IP address, instance ID, VPC ID, or AMI ID\. If your free text search returns unexpected matches, apply additional filters\.

**Search by attributes**  
Searching by an attribute lets you search a specific attribute across all of your resources\.   
Attribute searches use either *API filtering* or *client filtering*, depending on the selected attribute\. When performing an attribute search, the attributes are grouped accordingly\.
For example, you can search the **Instance state** attribute for all of your instances to return only instances that are in the `stopped` state\. To do this:  

1. In the search field on the Instances screen, start entering `Instance state`\. As you enter characters, a list of matching attributes appears\.

1. Select **Instance state** from the list\. A list of possible values for the selected attribute appears\.

1. Select **Stopped** from the list\.

You can use the following techniques to enhance or refine your searches:

**Inverse search**  
Inverse searches let you search for resources that do **not** match a specified value\. Inverse searches are performed by prefixing the search keyword with the exclamation mark \(\!\) character\. For example, to list all instances that are **not** assigned the security group named `launch-wizard-1`, search by the **Security group name** attribute, and for the keyword, enter `!launch-wizard-1`\.  
Inverse search is supported with keyword searches and attribute searches on client filters only\. It is not supported with attribute searches on API filters\.

**Partial search**  
With partial searches, you can search for partial string values\. To perform a partial search, enter only a part of the keyword that you want to search for\. For example, to search for all `t2.micro`, `t2.small`, and `t2.medium` instances, search by the **Instance Type** attribute, and for the keyword, enter `t2`\.  
Partial search is supported with keyword searches and attribute searches on client filters only\. It is not supported with attribute searches on API filters\.

**Regular expression search**  
To use regular expression searches, you must enable **Use regular expression matching** in the Preferences\.  
Regular expressions are useful when you need to match the values in a field with a specific pattern\. For example, to search for a value that starts with `s`, search for `^s`\. To search for a value that ends with `xyz`, search for `xyz$`\. Or to search for a value that starts with a number that is followed by one or more characters, search for `[0-9]+.*`\. Regular expression searches are not case\-sensitive\.   
Regular expression search is supported with keyword searches and attribute searches on client filters only\. It is not supported with attribute searches on API filters\.

**Wildcard search**  
Use the `*` wildcard to match zero or more characters\. Use the `?` wildcard to match zero or one character\. For example, if you have a data set with the following values: prod, prods, and production; "`prod*`" matches all values, whereas "`prod?`" matches only `prod` and `prods`\. To use the literals values, escape them with a backslash \(\\\)\. For example, "`prod\*`" would match prod\*\.  
Wildcard search is supported with attribute searches on API filters only\. It is not supported with keyword searches and attribute searches on client filters only\. 

**Combining searches**  
In general, multiple filters with the same attribute are automatically joined with `OR`\. For example, searching for `Instance State : Running` and `Instance State : Stopped` returns all instances that are either running OR stopped\. To join search with `AND`, search across different attributes\. For example, searching for `Instance State : Running` and `Instance Type : c4.large` returns only instances that are of type `c4.large` AND that are in the stopped state\.

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

**To filter a list of resources**

1. In the navigation pane, select a resource type \(for example, **Instances**\)\.

1. Choose the search field\.

1. Choose the filter from in the list\.

1. Specify a filter value\.

1. When you are finished, remove the filter\.

## Listing and filtering using the CLI and API<a name="Filtering_Resources_CLI"></a>

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
For examples of how to filter a list of resources according to their tags, see [Working with tags using the command line](Using_Tags.md#Using_Tags_CLI)\.