# Listing and Filtering Your Resources<a name="Using_Filtering"></a>

You can get a list of some types of resource using the Amazon EC2 console\. You can get a list of each type of resource using its corresponding command or API action\. If you have many resources, you can filter the results to include only the resources that match certain criteria\.


+ [Advanced Search](#advancedsearch)
+ [Listing Resources Using the Console](#listing-resources)
+ [Filtering Resources Using the Console](#filtering-resources)
+ [Listing and Filtering Using the CLI and API](#Filtering_Resources_CLI)

## Advanced Search<a name="advancedsearch"></a>

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

## Listing Resources Using the Console<a name="listing-resources"></a>

You can view the most common Amazon EC2 resource types using the console\. To view additional resources, use the command line interface or the API actions\.

**To list EC2 resources using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose the option that corresponds to the resource, such as **AMIs** or **Instances**\.  
![\[Amazon EC2 console navigation pane\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EC2_navigation.png)

1. The page displays all the available resources\.

## Filtering Resources Using the Console<a name="filtering-resources"></a>

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

## Listing and Filtering Using the CLI and API<a name="Filtering_Resources_CLI"></a>

Each resource type has a corresponding CLI command or API request that you use to list resources of that type\. For example, you can list Amazon Machine Images \(AMIs\) using `ec2-describe-images` or `DescribeImages`\. The response contains information for all your resources\. 

The resulting lists of resources can be long, so you might want to filter the results to include only the resources that match certain criteria\. You can specify multiple filter values, and you can also specify multiple filters\. For example, you can list all the instances whose type is either `m1.small` or `m1.large`, and that have an attached EBS volume that is set to delete when the instance terminates\. The instance must match all your filters to be included in the results\. 

You can also use wildcards with the filter values\. An asterisk \(\*\) matches zero or more characters, and a question mark \(?\) matches exactly one character\. For example, you can use `*database*` as a filter value to get all EBS snapshots that include `database` in the description\. If you were to specify `database` as the filter value, then only snapshots whose description equals `database` would be returned\. Filter values are case sensitive\. We support only exact string matching, or substring matching \(with wildcards\)\. If a resulting list of resources is long, using an exact string filter may return the response faster\. 

Your search can include the literal values of the wildcard characters; you just need to escape them with a backslash before the character\. For example, a value of `\*amazon\?\\` searches for the literal string `*amazon?\`\.

For a list of supported filters per Amazon EC2 resource, see the relevant documentation:

+ For the AWS CLI, see the relevant `describe` command in the *[AWS Command Line Interface Reference](http://docs.aws.amazon.com/cli/latest/reference/)*\.

+ For Windows PowerShell, see the relevant `Get` command in the *[AWS Tools for Windows PowerShell Reference](http://docs.aws.amazon.com/powershell/latest/reference/items/Amazon_Elastic_Compute_Cloud_cmdlets.html)*\.

+ For the Query API, see the relevant `Describe` API action in the *[Amazon EC2 API Reference](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/)*\.