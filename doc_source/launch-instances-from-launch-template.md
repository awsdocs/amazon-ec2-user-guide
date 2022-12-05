# Launch instances from a launch template<a name="launch-instances-from-launch-template"></a>

Launch templates are supported by several instance launch services\. This topic describes how to use a launch template when launching an instance using the EC2 launch instance wizard, Amazon EC2 Auto Scaling, EC2 Fleet, and Spot Fleet\.

**Topics**
+ [Launch an instance from a launch template](#launch-instance-from-launch-template)
+ [Use launch templates with Amazon EC2 Auto Scaling](#launch-templates-as)
+ [Use launch templates with EC2 Fleet](#launch-templates-ec2-fleet)
+ [Use launch templates with Spot Fleet](#launch-templates-spot-fleet)

## Launch an instance from a launch template<a name="launch-instance-from-launch-template"></a>

You can use the parameters contained in a launch template to launch an instance\. You have the option to override or add launch parameters before you launch the instance\.

Instances that are launched using a launch template are automatically assigned two tags with the keys `aws:ec2launchtemplate:id` and `aws:ec2launchtemplate:version`\. You cannot remove or edit these tags\.

------
#### [ Console ]

**To launch an instance from a launch template using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**\.

1. Select the launch template and choose **Actions**, **Launch instance from template**\.

1. For **Source template version**, select the launch template version to use\.

1. For **Number of instances**, specify the number of instances to launch\.

1. \(Optional\) You can override or add launch template parameters by changing and adding parameters in the **Instance details** section\.

1. Choose **Launch instance from template**\.

------
#### [ AWS CLI ]

**To launch an instance from a launch template using the AWS CLI**
+ Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and specify the `--launch-template` parameter\. Optionally specify the launch template version to use\. If you don't specify the version, the default version is used\.

  ```
  aws ec2 run-instances \
      --launch-template LaunchTemplateId=lt-0abcd290751193123,Version=1
  ```
+ To override a launch template parameter, specify the parameter in the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command\. The following example overrides the instance type that's specified in the launch template \(if any\)\.

  ```
  aws ec2 run-instances \
      --launch-template LaunchTemplateId=lt-0abcd290751193123 \
      --instance-type t2.small
  ```
+ If you specify a nested parameter that's part of a complex structure, the instance is launched using the complex structure as specified in the launch template plus any additional nested parameters that you specify\.

  In the following example, the instance is launched with the tag `Owner=TeamA` as well as any other tags that are specified in the launch template\. If the launch template has an existing tag with a key of `Owner`, the value is replaced with `TeamA`\.

  ```
  aws ec2 run-instances \
      --launch-template LaunchTemplateId=lt-0abcd290751193123 \
      --tag-specifications "ResourceType=instance,Tags=[{Key=Owner,Value=TeamA}]"
  ```

  In the following example, the instance is launched with a volume with the device name *`/dev/xvdb`* as well as any other block device mappings that are specified in the launch template\. If the launch template has an existing volume defined for *`/dev/xvdb`*, its values are replaced with the specified values\.

  ```
  aws ec2 run-instances \
      --launch-template LaunchTemplateId=lt-0abcd290751193123 \
      --block-device-mappings "DeviceName=/dev/xvdb,Ebs={VolumeSize=20,VolumeType=gp2}"
  ```

If the instance fails to launch or the state immediately goes to `terminated` instead of `running`, see [Troubleshoot instance launch issues](troubleshooting-launch.md)\.

------

## Use launch templates with Amazon EC2 Auto Scaling<a name="launch-templates-as"></a>

You can create an Auto Scaling group and specify a launch template to use for the group\. When Amazon EC2 Auto Scaling launches instances in the Auto Scaling group, it uses the launch parameters defined in the associated launch template\. For more information, see [Creating an Auto Scaling Group Using a Launch Template](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

Before you can create an Auto Scaling group using a launch template, you must create a launch template that includes the parameters required to launch an instance in an Auto Scaling group, such as the ID of the AMI\. The console provides guidance to help you create a template that you can use with Auto Scaling\.

**To create a launch template to use with Auto Scaling using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**, and then choose **Create launch template**\.

1. For **Launch template name**, enter a descriptive name for the launch template\.

1. For **Template version description**, provide a brief description of this version of the launch template\.

1. Under **Auto Scaling guidance**, select the check box to have Amazon EC2 provide guidance to help create a template to use with Auto Scaling\.

1. Modify the launch parameters as required\. Because you selected Auto Scaling guidance, some fields are required and some fields are not available\. For considerations to keep in mind when creating a launch template, and for information about how to configure the launch parameters for Auto Scaling, see [Creating a launch template for an Auto Scaling group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

1. Choose **Create launch template**\.

1. \(Optional\) To create an Auto Scaling group using this launch template, on the **Next steps** page, choose **Create Auto Scaling group**\.

**To create or update an Amazon EC2 Auto Scaling group with a launch template using the AWS CLI**
+ Use the [create\-auto\-scaling\-group](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/create-auto-scaling-group.html) or the [update\-auto\-scaling\-group](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/update-auto-scaling-group.html) command and specify the `--launch-template` parameter\.

## Use launch templates with EC2 Fleet<a name="launch-templates-ec2-fleet"></a>

You can create an EC2 Fleet request and specify a launch template in the instance configuration\. When Amazon EC2 fulfills the EC2 Fleet request, it uses the launch parameters defined in the associated launch template\. You can override some of the parameters that are specified in the launch template\.

For more information, see [Create an EC2 Fleet](manage-ec2-fleet.md#create-ec2-fleet)\.

**To create an EC2 Fleet with a launch template using the AWS CLI**
+ Use the [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) command\. Use the `--launch-template-configs` parameter to specify the launch template and any overrides for the launch template\.

## Use launch templates with Spot Fleet<a name="launch-templates-spot-fleet"></a>

You can create a Spot Fleet request and specify a launch template in the instance configuration\. When Amazon EC2 fulfills the Spot Fleet request, it uses the launch parameters defined in the associated launch template\. You can override some of the parameters that are specified in the launch template\.

For more information, see [Create a Spot Fleet request](work-with-spot-fleets.md#create-spot-fleet)\.

**To create a Spot Fleet request with a launch template using the console**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot](https://console.aws.amazon.com/ec2spot)\.

1. In the navigation pane, choose **Spot Requests**, and then choose **Request Spot Instances**\.

1. Under **Launch parameters**, choose **Use a launch template**\.

1. For **Launch template**, choose a launch template, and then, from the field to the right, choose the launch template version\.

1. Configure your Spot Fleet by selecting different options on this screen\. For more information about the options, see [Create a Spot Fleet request using defined parameters \(console\)](work-with-spot-fleets.md#create-spot-fleet-advanced)\.

1. When you're ready to create your Spot Fleet, choose **Launch**\.

**To create a Spot Fleet request with a launch template using the AWS CLI**
+ Use the [request\-spot\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command\. Use the `LaunchTemplateConfigs` parameter to specify the launch template and any overrides for the launch template\.