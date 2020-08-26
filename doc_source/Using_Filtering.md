# Listing and filtering your resources<a name="Using_Filtering"></a>

You can get a list of some types of resource using the Amazon EC2 console\. You can get a list of each type of resource using its corresponding command or API action\. If you have many resources, you can filter the results to include only the resources that match certain criteria\.

**Topics**
+ [Advanced search](#advancedsearch)
+ [Listing resources using the console](#listing-resources)
+ [Filtering resources using the console](#filtering-resources)
+ [Listing and filtering using the CLI and API](#Filtering_Resources_CLI)

## Advanced search<a name="advancedsearch"></a>

Advanced search allows you to search using a combination of filters to achieve precise results\. You can filter by keywords, user\-defined tag keys, and predefined resource attributes\.

The specific search types available are:
+ **Search by keyword**

  To search by keyword, type or paste what you’re looking for in the search box, and then choose Enter\. For example, to search for a specific instance, you can type the instance ID\.
+ **Search by fields**

  You can also search by fields, tags, and attributes associated with a resource\. For example, to find all instances in the stopped state:

  1. In the search box, start typing **Instance State**\. As you type, you'll see a list of suggested fields\.

  1. Select **Instance State **from the list\.

  1. Select **Stopped** from the list of suggested values\.

  1. To further refine your list, select the search box for more search options\.
+ **Advanced search**

  You can create advanced queries by adding multiple filters\. For example, you can search by tags and see instances for the Flying Mountain project running in the Production stack, and then search by attributes to see all t2\.micro instances, or all instances in us\-west\-2a, or both\. 
+ **Inverse search**

  You can search for resources that do not match a specified value\. For example, to list all instances that are not terminated, search by the **Instance State** field, and prefix the Terminated value with an exclamation mark \(\!\)\.
+ **Partial search**

  When searching by field, you can also enter a partial string to find all resources that contain the string in that field\. For example, search by **Instance Type**, and then type **t2** to find all t2\.micro, t2\.small or t2\.medium instances\.
+ **Regular expression**

  Regular expressions are useful when you need to match the values in a field with a specific pattern\. For example, search by the Name tag, and then type **^s\.\*** to see all instances with a Name tag that starts with an 's'\. Regular expression search is not case\-sensitive\.

After you have the precise results of your search, you can bookmark the URL for easy reference\. In situations where you have thousands of instances, filters and bookmarks can save you a great deal of time; you don’t have to run searches repeatedly\.

**Combining search filters**

In general, multiple filters with the same key field \(for example, tag:Name, search, Instance State\) are automatically joined with OR\. This is intentional, as the vast majority of filters would not be logical if they were joined with AND\. For example, you would get zero results for a search on Instance State=running AND Instance State=stopped\. In many cases, you can granulate the results by using complementary search terms on different key fields, where the AND rule is automatically applied instead\. If you search for tag: Name:=All values and tag:Instance State=running, you get search results that contain both those criteria\. To fine\-tune your results, simply remove one filter in the string until the results fit your requirements\.

## Listing resources using the console<a name="listing-resources"></a>

You can view the most common Amazon EC2 resource types using the console\. To view additional resources, use the command line interface or the API actions\.

**To list EC2 resources using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose the option that corresponds to the resource, such as **AMIs** or **Instances**\.  
![\[Amazon EC2 console navigation pane\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EC2_navigation.png)

1. The page displays all the available resources\.

## Filtering resources using the console<a name="filtering-resources"></a>

You can perform filtering and sorting of the most common resource types using the Amazon EC2 console\. For example, you can use the search bar on the instances page to sort instances by tags, attributes, or keywords\. 

You can also use the search field on each page to find resources with specific attributes or values\. You can use regular expressions to search on partial or multiple strings\. For example, to find all instances that are using the MySG security group, enter `MySG` in the search field\. The results will include any values that contain `MySG` as a part of the string, such as `MySG2` and` MySG3`\. To limit your results to MySG only, enter `\bMySG\b` in the search field\. To list all the instances whose type is either `m1.small` or `m1.large`, enter `m1.small|m1.large` in the search field\. 

**To list volumes in the `us-east-1b` Availability Zone with a status of `available`**

1. In the navigation pane, choose **Volumes**\.

1. Click on the search box, select **Attachment Status** from the menu, and then select **Detached**\. \(A detached volume is available to be attached to an instance in the same Availability Zone\.\)

1. Click on the search box again, select **State**, and then select **Available**\. 

1. Click on the search box again, select **Availability Zone**, and then select `us-east-1b`\.

1. Any volumes that meet this criteria are displayed\.

**To list public 64\-bit Linux AMIs backed by Amazon EBS**

1. In the navigation pane, choose **AMIs**\.

1. In the **Filter** pane, select **Public images**, **EBS images**, and then your Linux distribution from the **Filter** lists\.

1. Type `x86_64` in the search field\.

1. Any AMIs that meet this criteria are displayed\.

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

**Example Example: Specify a single filter**  
You can list your Amazon EC2 instances using [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html)\. Without filters, the response contains information for all your resources\. You can use the following command to include only the running instances in your output\.  

```
aws ec2 describe-instances --filters Name=instance-state-name,Values=running
```
To list only the instance IDs for your running instances, add the `--query` parameter as follows\.  

```
aws ec2 describe-instances --filters Name=instance-state-name,Values=running --query "Reservations[*].Instances[*].InstanceId" --output text
```
The following is example output:  

```
i-0ef1f57f78d4775a4
i-0626d4edd54f1286d
i-04a636d18e83cfacb
```

**Example Example: Specify multiple filters or filter values**  
If you specify multiple filters or multiple filter values, the resource must match all filters to be included in the results\.  
You can you the following command to list all instances whose type is either `m5.large` or `m5d.large`\.  

```
aws ec2 describe-instances --filters Name=instance-type,Values=m5.large,m5d.large
```
You can use the following command to list all stopped instances whose type is `t2.micro`\.  

```
aws ec2 describe-instances --filters Name=instance-state-name,Values=stopped Name=instance-type,Values=t2.micro
```

**Example Example: Use wildcards in a filter value**  
If you specify database as the filter value for the `description` filter when describing EBS snapshots using [describe\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshots.html), the command returns only the snapshots whose description is "database"\.  

```
aws ec2 describe-snapshots --filters Name=description,Values=database
```
The \* wildcard matches zero or more characters\. If you specify \*database\* as the filter value, the command returns only snapshots whose description includes the word database\.  

```
aws ec2 describe-snapshots --filters Name=description,Values=*database*
```
The ? wildcard matches exactly 1 character\. If you specify database? as the filter value, the command returns only snapshots whose description is "database" or "database" followed by one character\.  

```
aws ec2 describe-snapshots --filters Name=description,Values=database?
```
If you specify `database????`, the command returns only snapshots whose description is "database" followed by up to four characters\. It excludes descriptions with "database" followed by five or more characters\.  

```
aws ec2 describe-snapshots --filters Name=description,Values=database????
```

**Example Example: Filter based on date**  
With the AWS CLI, you can use JMESPath to filter results using expressions\. For example, the following [describe\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshots.html) command displays the IDs of all snapshots created by your AWS account \(represented by *123456789012*\) before the specified date \(represented by *2020\-03\-31*\)\. If you do not specify the owner, the results include all public snapshots\.  

```
aws ec2 describe-snapshots --filters Name=owner-id,Values=123456789012 --query "Snapshots[?(StartTime<=`2020-03-31`)].[SnapshotId]" --output text
```
The following command displays the IDs of all snapshots created in the specified date range\.  

```
aws ec2 describe-snapshots --filters Name=owner-id,Values=123456789012 --query "Snapshots[?(StartTime>=`2019-01-01`) && (StartTime<=`2019-12-31`)].[SnapshotId]" --output text
```

**Filter based on tags**  
For examples of how to filter a list of resources according to their tags, see [Working with tags using the command line](Using_Tags.md#Using_Tags_CLI)\.