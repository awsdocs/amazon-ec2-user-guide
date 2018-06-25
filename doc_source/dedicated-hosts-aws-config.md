# Tracking Configuration Changes<a name="dedicated-hosts-aws-config"></a>

You can use AWS Config to record configuration changes for Dedicated Hosts, and instances that are launched, stopped, or terminated on them\. You can then use the information captured by AWS Config as a data source for license reporting\.

AWS Config records configuration information for Dedicated Hosts and instances individually and pairs this information through relationships\. There are three reporting conditions\.
+ **AWS Config recording status**—When **On**, AWS Config is recording one or more AWS resource types, which can include Dedicated Hosts and Dedicated Instances\. To capture the information required for license reporting, verify that hosts and instances are being recorded with the following fields\.
+ **Host recording status**—When **Enabled**, the configuration information for Dedicated Hosts is recorded\.
+ **Instance recording status**—When **Enabled**, the configuration information for Dedicated Instances is recorded\.

If any of these three conditions are disabled, the icon in the **Edit Config Recording** button is red\. To derive the full benefit of this tool, ensure that all three recording methods are enabled\. When all three are enabled, the icon is green\. To edit the settings, choose **Edit Config Recording**\. You are directed to the **Set up AWS Config** page in the AWS Config console, where you can set up AWS Config and start recording for your hosts, instances, and other supported resource types\. For more information, see [Setting up AWS Config using the Console](http://docs.aws.amazon.com/config/latest/developerguide/resource-config-reference.html) in the *AWS Config Developer Guide*\.

**Note**  
AWS Config records your resources after it discovers them, which might take several minutes\. 

After AWS Config starts recording configuration changes to your hosts and instances, you can get the configuration history of any host that you have allocated or released and any instance that you have launched, stopped, or terminated\. For example, at any point in the configuration history of a Dedicated Host, you can look up how many instances are launched on that host, along with the number of sockets and cores on the host\. For any of those instances, you can also look up the ID of its Amazon Machine Image \(AMI\)\. You can use this information to report on licensing for your own server\-bound software that is licensed per\-socket or per\-core\.

You can view configuration histories in any of the following ways\.
+ By using the AWS Config console\. For each recorded resource, you can view a timeline page, which provides a history of configuration details\. To view this page, choose the gray icon in the **Config Timeline** column of the **Dedicated Hosts** page\. For more information, see [Viewing Configuration Details in the AWS Config Console](http://docs.aws.amazon.com/config/latest/developerguide/view-manage-resource-console.html) in the *AWS Config Developer Guide*\.
+ By running AWS CLI commands\. First, you can use the [list\-discovered\-resources](http://docs.aws.amazon.com/cli/latest/reference/configservice/list-discovered-resources.html) command to get a list of all hosts and instances\. Then, you can use the [get\-resource\-config\-history](http://docs.aws.amazon.com/cli/latest/reference/configservice/get-resource-config-history.html#get-resource-config-history) command to get the configuration details of a host or instance for a specific time interval\. For more information, see [View Configuration Details Using the CLI](http://docs.aws.amazon.com/config/latest/developerguide/view-manage-resource-cli.html) in the *AWS Config Developer Guide*\.
+ By using the AWS Config API in your applications\. First, you can use the [ListDiscoveredResources](http://docs.aws.amazon.com/config/latest/APIReference/API_ListDiscoveredResources.html) action to get a list of all hosts and instances\. Then, you can use the [GetResourceConfigHistory](http://docs.aws.amazon.com/config/latest/APIReference/API_GetResourceConfigHistory.html) action to get the configuration details of a host or instance for a specific time interval\.

For example, to get a list of all of your Dedicated Hosts from AWS Config, run a CLI command such as the following:

```
aws configservice list-discovered-resources --resource-type AWS::EC2::Host
```

To obtain the configuration history of a Dedicated Host from AWS Config, run a CLI command such as the following:

```
aws configservice get-resource-config-history --resource type AWS::EC2::Instance --resource-id i-1234567890abcdef0
```

**To manage AWS Config settings using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Dedicated Hosts** page, choose **Edit Config Recording**\.

1. In the AWS Config console, follow the steps provided to turn on recording\. For more information, see [Setting up AWS Config using the Console](http://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)\.

 **To activate AWS Config using the command line or API**
+ Using the AWS CLI, see [Viewing Configuration Details in the AWS Config Console](http://docs.aws.amazon.com/config/latest/developerguide/view-manage-resource-console.html) in the *AWS Config Developer Guide*\.
+ Using the Amazon EC2 API, see [GetResourceConfigHistory](http://docs.aws.amazon.com/config/latest/APIReference/API_GetResourceConfigHistory.html)\.

For more information, see [Viewing Configuration Details in the AWS Config Console](http://docs.aws.amazon.com/config/latest/developerguide/view-manage-resource-console.html)\.