# Get recommendations for an instance type<a name="ec2-instance-recommendations"></a>

AWS Compute Optimizer provides Amazon EC2 instance recommendations to help you improve performance, save money, or both\. You can use these recommendations to decide whether to move to a new instance type\.

To make recommendations, Compute Optimizer analyzes your existing instance specifications and utilization metrics\. The compiled data is then used to recommend which Amazon EC2 instance types are best able to handle the existing workload\. Recommendations are returned along with per\-hour instance pricing\.

This topic outlines how to view recommendations through the Amazon EC2 console\. For more information, see the [AWS Compute Optimizer User Guide](https://docs.aws.amazon.com/compute-optimizer/latest/ug/viewing-dashboard.html)\.

**Note**  
To get recommendations from Compute Optimizer, you must first opt in to Compute Optimizer\. For more information, see [Getting Started with AWS Compute Optimizer](https://docs.aws.amazon.com/compute-optimizer/latest/ug/getting-started.html) in the *AWS Compute Optimizer User Guide*\.

**Topics**
+ [Limitations](#compute-optimizer-limitations)
+ [Findings](#findings-classifications)
+ [View recommendations](#viewing-recommendations)
+ [Considerations for evaluating recommendations](#considerations)
+ [Additional resources](#co-additional-resources)

## Limitations<a name="compute-optimizer-limitations"></a>

Compute Optimizer currently generates recommendations for M, C, R, T, and X instance types\. Other instance types are not considered by Compute Optimizer\. If you're using other instance types, they will not be listed in the Compute Optimizer recommendations view\. For information about these and other instance types, see [Instance types](instance-types.md)\.

## Findings<a name="findings-classifications"></a>

Compute Optimizer classifies its findings for EC2 instances as follows:
+ **Under\-provisioned** – An EC2 instance is considered under\-provisioned when at least one specification of your instance, such as CPU, memory, or network, does not meet the performance requirements of your workload\. Under\-provisioned EC2 instances might lead to poor application performance\. 
+ **Over\-provisioned** – An EC2 instance is considered over\-provisioned when at least one specification of your instance, such as CPU, memory, or network, can be sized down while still meeting the performance requirements of your workload, and when no specification is under\-provisioned\. Over\-provisioned EC2 instances might lead to unnecessary infrastructure cost\. 
+ **Optimized** – An EC2 instance is considered optimized when all specifications of your instance, such as CPU, memory, and network, meet the performance requirements of your workload, and the instance is not over\-provisioned\. An optimized EC2 instance runs your workloads with optimal performance and infrastructure cost\. For optimized instances, Compute Optimizer might sometimes recommend a new generation instance type\. 
+ **None** – There are no recommendations for this instance\. This might occur if you've been opted in to Compute Optimizer for less than 12 hours, or when the instance has been running for less than 30 hours, or when the instance type is not supported by Compute Optimizer\. For more information, see [Limitations](#compute-optimizer-limitations) in the previous section\.

## View recommendations<a name="viewing-recommendations"></a>

After you opt in to Compute Optimizer, you can view the findings that Compute Optimizer generates for your EC2 instances in the EC2 console\. You can then access the Compute Optimizer console to view the recommendations\. If you recently opted in, findings might not be reflected in the EC2 console for up to 12 hours\. 

------
#### [ New console ]

**To view a recommendation for an EC2 instance through the EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then choose the instance ID\.

1. On the instance summary page, in the **AWS Compute Optimizer** banner near the bottom of the page, choose **View detail**\.

   The instance opens in Compute Optimizer, where it is labeled as the **Current** instance\. Up to three different instance type recommendations, labeled **Option 1**, **Option 2**, and **Option 3**, are provided\. The bottom half of the window shows recent CloudWatch metric data for the current instance: **CPU utilization**, **Memory utilization**, **Network in**, and **Network out**\. 

1. \(Optional\) In the Compute Optimizer console, choose the settings \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/settings-icon.png)\) icon to change the visible columns in the table, or to view the public pricing information for a different purchasing option for the current and recommended instance types\.
**Note**  
If you’ve purchased a Reserved Instance, your On\-Demand Instance might be billed as a Reserved Instance\. Before you change your current instance type, first evaluate the impact on Reserved Instance utilization and coverage\.

------
#### [ Old console ]

**To view a recommendation for an EC2 instance through the EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select an instance, and on the **Description** tab, inspect the **Finding** field\. Choose **View detail**\.

   The instance opens in Compute Optimizer, where it is labeled as the **Current** instance\. Up to three different instance type recommendations, labeled **Option 1**, **Option 2**, and **Option 3**, are provided\. The bottom half of the window shows recent CloudWatch metric data for the current instance: **CPU utilization**, **Memory utilization**, **Network in**, and **Network out**\. 

1. \(Optional\) In the Compute Optimizer console, choose the settings \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/settings-icon.png)\) icon to change the visible columns in the table, or to view the public pricing information for a different purchasing option for the current and recommended instance types\.
**Note**  
If you’ve purchased a Reserved Instance, your On\-Demand Instance might be billed as a Reserved Instance\. Before you change your current instance type, first evaluate the impact on Reserved Instance utilization and coverage\.

------

Determine whether you want to use one of the recommendations\. Decide whether to optimize for performance improvement, for cost reduction, or for a combination of the two\. For more information, see [Viewing Resource Recommendations](https://docs.aws.amazon.com/compute-optimizer/latest/ug/viewing-recommendations.html) in the *AWS Compute Optimizer User Guide*\.

**To view recommendations for all EC2 instances across all Regions through the Compute Optimizer console**

1. Open the Compute Optimizer console at [https://console\.aws\.amazon\.com/compute\-optimizer/](https://console.aws.amazon.com/compute-optimizer/)\.

1. Choose **View recommendations for all EC2 instances**\.

1. You can perform the following actions on the recommendations page:

   1. To filter recommendations to one or more AWS Regions, enter the name of the Region in the **Filter by one or more Regions** text box, or choose one or more Regions in the drop\-down list that appears\. 

   1. To view recommendations for resources in another account, choose **Account**, and then select a different account ID\. 

      This option is available only if you are signed in to a management account of an organization, and you opted in all member accounts within the organization\. 

   1. To clear the selected filters, choose **Clear filters**\. 

   1. To change the purchasing option that is displayed for the current and recommended instance types, choose the settings \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/settings-icon.png)\) icon , and then choose **On\-Demand Instances**, **Reserved Instances, standard 1\-year no upfront**, or **Reserved Instances, standard 3\-year no upfront**\. 

   1. To view details, such as additional recommendations and a comparison of utilization metrics, choose the finding \(**Under\-provisioned**, **Over\-provisioned**, or **Optimized**\) listed next to the desired instance\. For more information, see [Viewing Resource Details](https://docs.aws.amazon.com/compute-optimizer/latest/ug/viewing-resource-details.html) in the *AWS Compute Optimizer User Guide*\.

## Considerations for evaluating recommendations<a name="considerations"></a>

Before changing an instance type, consider the following:
+ The recommendations don’t forecast your usage\. Recommendations are based on your historical usage over the most recent 14\-day time period\. Be sure to choose an instance type that is expected to meet your future resource needs\.
+ Focus on the graphed metrics to determine whether actual usage is lower than instance capacity\. You can also view metric data \(average, peak, percentile\) in CloudWatch to further evaluate your EC2 instance recommendations\. For example, notice how CPU percentage metrics change during the day and whether there are peaks that need to be accommodated\. For more information, see [Viewing Available Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/viewing_metrics_with_cloudwatch.html) in the *Amazon CloudWatch User Guide*\. 
+ Compute Optimizer might supply recommendations for burstable performance instances, which are T3, T3a, and T2 instances\. If you periodically burst above the baseline, make sure that you can continue to do so based on the vCPUs of the new instance type\. For more information, see [Key concepts and definitions for burstable performance instances](burstable-credits-baseline-concepts.md)\.
+ If you’ve purchased a Reserved Instance, your On\-Demand Instance might be billed as a Reserved Instance\. Before you change your current instance type, first evaluate the impact on Reserved Instance utilization and coverage\.
+ Consider conversions to newer generation instances, where possible\.
+ When migrating to a different instance family, make sure the current instance type and the new instance type are compatible, for example, in terms of virtualization, architecture, or network type\. For more information, see [Compatibility for changing the instance type](resize-limitations.md)\.
+ Finally, consider the performance risk rating that's provided for each recommendation\. Performance risk indicates the amount of effort you might need to spend in order to validate whether the recommended instance type meets the performance requirements of your workload\. We also recommend rigorous load and performance testing before and after making any changes\.

There are other considerations when resizing an EC2 instance\. For more information, see [Change the instance type](ec2-instance-resize.md)\.

## Additional resources<a name="co-additional-resources"></a>

For more information:
+ [Instance types](instance-types.md)
+ [AWS Compute Optimizer User Guide](https://docs.aws.amazon.com/compute-optimizer/latest/ug/)