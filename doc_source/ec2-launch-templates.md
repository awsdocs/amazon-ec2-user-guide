# Launching an Instance from a Launch Template<a name="ec2-launch-templates"></a>

You can create a *launch template* that contains the configuration information to launch an instance\. Launch templates enable you to store launch parameters so that you do not have to specify them every time you launch an instance\. For example, a launch template can contain the AMI ID, instance type, and network settings that you typically use to launch instances\. When you launch an instance using the Amazon EC2 console, an AWS SDK, or a command line tool, you can specify the launch template to use\.

For each launch template, you can create one or more numbered *launch template versions*\. Each version can have different launch parameters\. When you launch an instance from a launch template, you can use any version of the launch template\. If you do not specify a version, the default version is used\. You can set any version of the launch template as the default version—by default, it's the first version of the launch template\.

The following diagram shows a launch template with three versions\. The first version specifies the instance type, AMI ID, subnet, and key pair to use to launch the instance\. The second version is based on the first version and also specifies a security group for the instance\. The third version uses different values for some of the parameters\. Version 2 is set as the default version\. If you launched an instance from this launch template, the launch parameters from version 2 would be used if no other version were specified\.

![\[Launch template\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/launch-template-diagram.png)

**Topics**
+ [Launch Template Restrictions](#launch-template-restrictions)
+ [Using Launch Templates to Control Launch Parameters](#launch-templates-authorization)
+ [Controlling the Use of Launch Templates](#launch-template-permissions)
+ [Creating a Launch Template](#create-launch-template)
+ [Managing Launch Template Versions](#manage-launch-template-versions)
+ [Launching an Instance from a Launch Template](#launch-instance-from-launch-template)
+ [Using Launch Templates with Amazon EC2 Auto Scaling](#launch-templates-as)
+ [Using Launch Templates with EC2 Fleet](#launch-templates-ec2-fleet)
+ [Using Launch Templates with Spot Fleet](#launch-templates-spot-fleet)
+ [Deleting a Launch Template](#delete-launch-template)

## Launch Template Restrictions<a name="launch-template-restrictions"></a>

The following rules apply to launch templates and launch template versions:
+ You are limited to creating 5,000 launch templates per Region and 10,000 versions per launch template\.
+ Launch parameters are optional\. However, you must ensure that your request to launch an instance includes all required parameters\. For example, if your launch template does not include an AMI ID, you must specify both the launch template and an AMI ID when you launch an instance\.
+ Launch template parameters are not validated when you create the launch template\. Ensure that you specify the correct values for the parameters and that you use supported parameter combinations\. For example, to launch an instance in a placement group, you must specify a supported instance type\.
+ You can tag a launch template, but you cannot tag a launch template version\.
+ Launch template versions are numbered in the order in which they are created\. When you create a launch template version, you cannot specify the version number yourself\.

## Using Launch Templates to Control Launch Parameters<a name="launch-templates-authorization"></a>

A launch template can contain all or some of the parameters to launch an instance\. When you launch an instance using a launch template, you can override parameters that are specified in the launch template\. Or, you can specify additional parameters that are not in the launch template\.

**Note**  
You cannot remove launch template parameters during launch \(for example, you cannot specify a null value for the parameter\)\. To remove a parameter, create a new version of the launch template without the parameter and use that version to launch the instance\.

To launch instances, IAM users must have permissions to use the `ec2:RunInstances` action\. They must also have permissions to create or use the resources that are created or associated with the instance\. You can use resource\-level permissions for the `ec2:RunInstances` action to control the launch parameters that users can specify\. Alternatively, you can grant users permissions to launch an instance using a launch template\. This enables you to manage launch parameters in a launch template rather than in an IAM policy, and to use a launch template as an authorization vehicle for launching instances\. For example, you can specify that users can only launch instances using a launch template, and that they can only use a specific launch template\. You can also control the launch parameters that users can override in the launch template\. For example policies, see [Launch Templates](ExamplePolicies_EC2.md#iam-example-runinstances-launch-templates)\.

## Controlling the Use of Launch Templates<a name="launch-template-permissions"></a>

By default, IAM users do not have permissions to work with launch templates\. You can create an IAM user policy that grants users permissions to create, modify, describe, and delete launch templates and launch template versions\. You can also apply resource\-level permissions to some launch template actions to control a user's ability to use specific resources for those actions\. For more information, see [Supported Resource\-Level Permissions for Amazon EC2 API Actions](ec2-supported-iam-actions-resources.md) and the following example policies: [Example: Working with Launch Templates](ExamplePolicies_EC2.md#iam-example-launch-templates)\.

Take care when granting users permissions to use the `ec2:CreateLaunchTemplate` and `ec2:CreateLaunchTemplateVersion` actions\. These actions do not support resource\-level permissions that enable you to control which resources users can specify in the launch template\. To restrict the resources that are used to launch an instance, ensure that you grant permissions to create launch templates and launch template versions only to appropriate administrators\.

## Creating a Launch Template<a name="create-launch-template"></a>

Create a new launch template using parameters that you define, or use an existing launch template or an instance as the basis for a new launch template\.

**To create a new launch template using defined parameters \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**\.

1. Choose **Create launch template** and provide a name and description\.

1. For **Launch template contents**, provide the following information:
   + **AMI ID**: An AMI from which to launch the instance\. To search through all available AMIs, choose **Search for AMI**\. To select a commonly used AMI, choose **Quick Start**\. Or, choose **AWS Marketplace** or **Community AMIs**\. You can use an AMI that you own or [find a suitable AMI](finding-an-ami.md)\.
   + **Instance type**: Ensure that the instance type is compatible with the AMI that you've specified\. For more information, see [Instance Types](instance-types.md)\. 
   + **Key pair name**: The key pair for the instance\. For more information, see [Amazon EC2 Key Pairs](ec2-key-pairs.md)\.
   + **Network type**: If applicable, whether to launch the instance into a VPC or EC2\-Classic\. If you choose **VPC**, specify the subnet in the **Network interfaces** section\. If you choose **Classic**, ensure that the specified instance type is supported in EC2\-Classic and specify the Availability Zone for the instance\.
   + **Security Groups**: One or more security groups to associate with the instance\. For more information, see [Amazon EC2 Security Groups for Linux Instances](using-network-security.md)\.

1. For **Network interfaces**, you can specify up to two [network interfaces](using-eni.md) for the instance\.
   + **Device**: The device number for the network interface, for example, `eth0` for the primary network interface\. If you leave the field blank, AWS creates the primary network interface\.
   + **Network interface**: The ID of the network interface, or leave blank to let AWS create a new network interface\.
   + **Description**: \(Optional\) A description for the new network interface\.
   + **Subnet**: The subnet in which to create a new network interface\. For the primary network interface \(`eth0`\), this is the subnet in which the instance is launched\. If you've entered an existing network interface for `eth0`, the instance is launched in the subnet in which the network interface is located\.
   + **Auto\-assign public IP**: Whether to automatically assign a public IP address to the network interface with the device index of `eth0`\. This setting can only be enabled for a single, new network interface\.
   + **Primary IP**: A private IPv4 address from the range of your subnet\. Leave blank to let AWS choose a private IPv4 address for you\.
   + **Secondary IP**: A secondary private IPv4 address from the range of your subnet\. Leave blank to let AWS choose one for you\.
   + \(IPv6\-only\) **IPv6 IPs**: An IPv6 address from the range of the subnet\.
   + **Security group ID**: The ID of a security group in your VPC with which to associate the network interface\.
   + **Delete on termination**: Whether the network interface is deleted when the instance is deleted\.

1. For **Storage \(Volumes\)**, specify volumes to attach to the instance besides the volumes specified by the AMI\.
   + **Volume type**: The instance store or Amazon EBS volumes with which to associate your instance\. The type of volume depends on the instance type that you've chosen\. For more information, see [Amazon EC2 Instance Store](InstanceStorage.md) and [Amazon EBS Volumes](EBSVolumes.md)\.
   + **Device name**: A device name for the volume\.
   + **Snapshot**: The ID of the snapshot from which to create the volume\.
   + **Size**: For Amazon EBS volumes, the storage size\.
   + **Volume type**: For Amazon EBS volumes, the volume type\. For more information, see [Amazon EBS Volume Types](EBSVolumeTypes.md)\.
   + **IOPS**: For the Provisioned IOPS SSD volume type, the number of I/O operations per second \(IOPS\) that the volume can support\.
   + **Delete on termination**: For Amazon EBS volumes, whether to delete the volume when the instance is terminated\. For more information, see [Preserving Amazon EBS Volumes on Instance Termination](terminating-instances.md#preserving-volumes-on-termination)\.
   + **Encrypted**: Whether to encrypt new Amazon EBS volumes\. Amazon EBS volumes that are restored from encrypted snapshots are automatically encrypted\. Encrypted volumes may only be attached to [supported instance types](EBSEncryption.md#EBSEncryption_supported_instances)\.
   + **Key**: For encrypting new Amazon EBS volumes, the master key to use when encrypting the volumes\. Enter the default master key for your account, or any customer master key \(CMK\) that you have previously created using the AWS Key Management Service\. You can paste the full ARN of any key to which you have access\. For more information, see the [AWS Key Management Service Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/)\.

1. For **Tags**, specify [tags](Using_Tags.md) by providing key and value combinations\. You can tag the instance, the volumes, or both\.

1. For **Advanced Details**, expand the section to view the fields and specify any additional parameters for the instance\.
   + **Purchasing option**: The purchasing model\. Choose **Request Spot instances** to request Spot Instances at the Spot price, capped at the On\-Demand price, and choose **Customize Spot parameters** to change the default Spot Instance settings\. If you do not request a Spot Instance, EC2 launches an On\-Demand Instance by default\. For more information, see [Spot Instances](using-spot-instances.md)\.
   + **IAM instance profile**: An AWS Identity and Access Management \(IAM\) instance profile to associate with the instance\. For more information, see [IAM Roles for Amazon EC2](iam-roles-for-amazon-ec2.md)\.
   + **Shutdown behavior**: Whether the instance should stop or terminate when shut down\. For more information, see [Changing the Instance Initiated Shutdown Behavior](terminating-instances.md#Using_ChangingInstanceInitiatedShutdownBehavior)\.
   + **Stop \- Hibernate behavior**: Whether the instance is enabled for hibernation\. This field is only valid for instances that meet the hibernation prerequisites\. For more information, see [Hibernate Your Instance](Hibernate.md)\.
   + **Termination protection**: Whether to prevent accidental termination\. For more information, see [Enabling Termination Protection for an Instance](terminating-instances.md#Using_ChangingDisableAPITermination)\.
   + **Monitoring**: Whether to enable detailed monitoring of the instance using Amazon CloudWatch\. Additional charges apply\. For more information, see [Monitoring Your Instances Using CloudWatch](using-cloudwatch.md)\.
   + **T2/T3 Unlimited**: Whether to enable applications to burst beyond the baseline for as long as needed\. This field is only valid for T2 and T3 instances\. Additional charges may apply\. For more information, see [Burstable Performance Instances](burstable-performance-instances.md)\.
   + **Placement group name**: Specify a placement group in which to launch the instance\. Not all instance types can be launched in a placement group\. For more information, see [Placement Groups](placement-groups.md)\.
   + **EBS\-optimized instance**: Provides additional, dedicated capacity for Amazon EBS I/O\. Not all instance types support this feature, and additional charges apply\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.
   + **Tenancy**: Choose whether to run your instance on shared hardware \(**Shared**\), isolated, dedicated hardware \(**Dedicated**\), or on a Dedicated Host \(**Dedicated host**\)\. Additional charges may apply\. For more information, see [Dedicated Instances](dedicated-instance.md) and [Dedicated Hosts](dedicated-hosts-overview.md)\. If you specify a Dedicated Host, you can choose a specific host and the affinity for the instance\.
   + **RAM disk ID**: A RAM disk for the instance\. If you have specified a kernel, you may need to specify a specific RAM disk with the drivers to support it\. Only valid for paravirtual \(PV\) AMIs\.
   + **Kernel ID**: A kernel for the instance\. Only valid for paravirtual \(PV\) AMIs\.
   + **User data**: You can specify user data to configure an instance during launch, or to run a configuration script\. For more information, see [Running Commands on Your Linux Instance at Launch](user-data.md)\.

1. Choose **Create launch template**\.

**To create a launch template from an existing launch template \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**\.

1. Choose **Create launch template**\. Provide a name and description for the launch template\.

1. For **Source template**, choose a launch template on which to base the new launch template\.

1. For **Source template version**, choose the launch template version on which to base the new launch template\.

1. Adjust any launch parameters as required, and choose **Create launch template**\.

**To create a launch template from an instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, and choose **Actions**, **Create Template From Instance**\.

1. Provide a name and description, and adjust the launch parameters as required\.
**Note**  
When you create a launch template from an instance, the instance's network interface IDs and IP addresses are not included in the template\.

1. Choose **Create Template From Instance**\.

**To create a launch template \(AWS CLI\)**
+ Use the [create\-launch\-template](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template.html) \(AWS CLI\) command\. The following example creates a launch template that specifies the following:
  + The instance type \(`r4.4xlarge`\) and AMI \(`ami-8c1be5f6`\) to launch
  + The number of cores \(`4`\) and threads per core \(`2`\) for a total of 8 vCPUs \(4 cores x 2 threads\)
  + The subnet in which to launch the instance \(`subnet-7b16de0c`\)

  The template assigns a public IP address and an IPv6 address to the instance and creates a tag for the instance \(`Name`=`webserver`\)\.

  ```
  aws ec2 create-launch-template --launch-template-name TemplateForWebServer --version-description WebVersion1 --launch-template-data file://template-data.json
  ```

  The following is an example `template-data.json` file:

  ```
  {
      "NetworkInterfaces": [{
          "AssociatePublicIpAddress": true,
          "DeviceIndex": 0,
          "Ipv6AddressCount": 1,
          "SubnetId": "subnet-7b16de0c"
      }],
      "ImageId": "ami-8c1be5f6",
      "InstanceType": "r4.4xlarge",
      "TagSpecifications": [{
          "ResourceType": "instance",
          "Tags": [{
              "Key":"Name",
              "Value":"webserver"
          }]
      }],
      "CpuOptions": {
          "CoreCount":4,
          "ThreadsPerCore":2
      }
  }
  ```

  The following is example output\.

  ```
  {
      "LaunchTemplate": {
          "LatestVersionNumber": 1, 
          "LaunchTemplateId": "lt-01238c059e3466abc", 
          "LaunchTemplateName": "TemplateForWebServer", 
          "DefaultVersionNumber": 1, 
          "CreatedBy": "arn:aws:iam::123456789012:root", 
          "CreateTime": "2017-11-27T09:13:24.000Z"
      }
  }
  ```

**To get instance data for a launch template \(AWS CLI\)**
+ Use the [get\-launch\-template\-data](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-launch-template-data.html) \(AWS CLI\) command and specify the instance ID\. You can use the output as a base to create a new launch template or launch template version\. By default, the output includes a top\-level `LaunchTemplateData` object, which cannot be specified in your launch template data\. Use the `--query` option to exclude this object\.

  ```
  aws ec2 get-launch-template-data --instance-id i-0123d646e8048babc --query "LaunchTemplateData"
  ```

  The following is example output\.

  ```
      {
          "Monitoring": {}, 
          "ImageId": "ami-8c1be5f6", 
          "BlockDeviceMappings": [
              {
                  "DeviceName": "/dev/xvda", 
                  "Ebs": {
                      "DeleteOnTermination": true
                  }
              }
          ], 
          "EbsOptimized": false, 
          "Placement": {
              "Tenancy": "default", 
              "GroupName": "", 
              "AvailabilityZone": "us-east-1a"
          }, 
          "InstanceType": "t2.micro", 
          "NetworkInterfaces": [
              {
                  "Description": "", 
                  "NetworkInterfaceId": "eni-35306abc", 
                  "PrivateIpAddresses": [
                      {
                          "Primary": true, 
                          "PrivateIpAddress": "10.0.0.72"
                      }
                  ], 
                  "SubnetId": "subnet-7b16de0c", 
                  "Groups": [
                      "sg-7c227019"
                  ], 
                  "Ipv6Addresses": [
                      {
                          "Ipv6Address": "2001:db8:1234:1a00::123"
                      }
                  ], 
                  "PrivateIpAddress": "10.0.0.72"
              }
          ]
      }
  ```

  You can write the output directly to a file, for example:

  ```
  aws ec2 get-launch-template-data --instance-id i-0123d646e8048babc --query "LaunchTemplateData" >> instance-data.json
  ```

## Managing Launch Template Versions<a name="manage-launch-template-versions"></a>

You can create launch template versions for a specific launch template, set the default version, and delete versions that you no longer require\.

**Topics**
+ [Creating a Launch Template Version](#create-launch-template-version)
+ [Setting the Default Launch Template Version](#set-default-launch-template-version)
+ [Deleting a Launch Template Version](#delete-launch-template-version)

### Creating a Launch Template Version<a name="create-launch-template-version"></a>

When you create a launch template version, you can specify new launch parameters or use an existing version as the base for the new version\. For more information about the launch parameters, see [Creating a Launch Template](#create-launch-template)\.

**To create a launch template version \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**\.

1. Choose **Create launch template**\.

1. For **What would you like to do**, choose **Create a new template version**

1. For **Launch template name**, select the name of the existing launch template from the list\.

1. For **Template version description**, type a description for the launch template version\.

1. \(Optional\) Select a version of the launch template, or a version of a different launch template, to use as a base for the new launch template version\. The new launch template version inherits the launch parameters from this launch template version\.

1. Modify the launch parameters as required, and choose **Create launch template**\.

**To create a launch template version \(AWS CLI\)**
+ Use the [create\-launch\-template\-version](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template-version.html) \(AWS CLI\) command\. You can specify a source version on which to base the new version\. The new version inherits the launch parameters from this version, and you can override parameters using `--launch-template-data`\. The following example creates a new version based on version 1 of the launch template and specifies a different AMI ID\.

  ```
  aws ec2 create-launch-template-version --launch-template-id lt-0abcd290751193123 --version-description WebVersion2 --source-version 1 --launch-template-data "ImageId=ami-c998b6b2"
  ```

### Setting the Default Launch Template Version<a name="set-default-launch-template-version"></a>

You can set the default version for the launch template\. When you launch an instance from a launch template and do not specify a version, the instance is launched using the parameters of the default version\.

**To set the default launch template version \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**\.

1. Select the launch template and choose **Actions**, **Set default version**\.

1. For **Default version**, select the version number and choose **Set as default version**\.

**To set the default launch template version \(AWS CLI\)**
+ Use the [modify\-launch\-template](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-launch-template.html) \(AWS CLI\) command and specify the version that you want to set as the default\.

  ```
  aws ec2 modify-launch-template --launch-template-id lt-0abcd290751193123 --default-version 2
  ```

### Deleting a Launch Template Version<a name="delete-launch-template-version"></a>

If you no longer require a launch template version, you can delete it\. You cannot replace the version number after you delete it\. You cannot delete the default version of the launch template; you must first assign a different version as the default\.

**To delete a launch template version \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**\.

1. Select the launch template and choose **Actions**, **Delete template version**\.

1. Select the version to delete and choose **Delete launch template version**\.

**To delete a launch template version \(AWS CLI\)**
+ Use the [delete\-launch\-template\-versions](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-launch-template-versions.html) \(AWS CLI\) command and specify the version numbers to delete\.

  ```
  aws ec2 delete-launch-template-versions --launch-template-id lt-0abcd290751193123 --versions 1
  ```

## Launching an Instance from a Launch Template<a name="launch-instance-from-launch-template"></a>

You can use the parameters contained in a launch template to launch an instance\. You have the option to override or add launch parameters before you launch the instance\.

Instances that are launched using a launch template are automatically assigned two tags with the keys `aws:ec2launchtemplate:id` and `aws:ec2launchtemplate:version`\. You cannot remove or edit these tags\.

**To launch an instance from a launch template \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**\.

1. Select the launch template and choose **Actions**, **Launch instance from template**\.

1. Select the launch template version to use\.

1. \(Optional\) You can override or add launch template parameters by changing and adding parameters in the **Instance details** section\.

1. Choose **Launch instance from template**\.

**To launch an instance from a launch template \(AWS CLI\)**
+ Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command and specify the `--launch-template` parameter\. Optionally specify the launch template version to use\. If you don't specify the version, the default version is used\.

  ```
  aws ec2 run-instances --launch-template LaunchTemplateId=lt-0abcd290751193123,Version=1
  ```
+ To override a launch template parameter, specify the parameter in the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command\. The following example overrides the instance type that's specified in the launch template \(if any\)\.

  ```
  aws ec2 run-instances --launch-template LaunchTemplateId=lt-0abcd290751193123 --instance-type t2.small
  ```
+ If you specify a nested parameter that's part of a complex structure, the instance is launched using the complex structure as specified in the launch template plus any additional nested parameters that you specify\.

  In the following example, the instance is launched with the tag `Owner=TeamA` as well as any other tags that are specified in the launch template\. If the launch template has an existing tag with a key of `Owner`, the value is replaced with `TeamA`\.

  ```
  aws ec2 run-instances --launch-template LaunchTemplateId=lt-0abcd290751193123 --tag-specifications "ResourceType=instance,Tags=[{Key=Owner,Value=TeamA}]"
  ```

  In the following example, the instance is launched with a volume with the device name `/dev/xvdb` as well as any other block device mappings that are specified in the launch template\. If the launch template has an existing volume defined for `/dev/xvdb`, its values are replaced with specified values\.

  ```
  aws ec2 run-instances --launch-template LaunchTemplateId=lt-0abcd290751193123 --block-device-mappings "DeviceName=/dev/xvdb,Ebs={VolumeSize=20,VolumeType=gp2}"
  ```

If the instance fails to launch or the state immediately goes to `terminated` instead of `running`, see [Troubleshooting Instance Launch Issues](troubleshooting-launch.md)\.

## Using Launch Templates with Amazon EC2 Auto Scaling<a name="launch-templates-as"></a>

You can create an Auto Scaling group and specify a launch template to use for the group\. When Amazon EC2 Auto Scaling launches instances in the Auto Scaling group, it uses the launch parameters defined in the associated launch template\.

For more information, see [Creating an Auto Scaling Group Using a Launch Template](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

**To create or update an Amazon EC2 Auto Scaling group with a launch template \(AWS CLI\)**
+ Use the [create\-auto\-scaling\-group](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/create-auto-scaling-group.html) or the [update\-auto\-scaling\-group](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/update-auto-scaling-group.html) AWS CLI command and specify the `--launch-template` parameter\.

## Using Launch Templates with EC2 Fleet<a name="launch-templates-ec2-fleet"></a>

You can create an EC2 Fleet request and specify a launch template in the instance configuration\. When Amazon EC2 fulfills the EC2 Fleet request, it uses the launch parameters defined in the associated launch template\. You can override some of the parameters that are specified in the launch template\.

For more information, see [Creating an EC2 Fleet](manage-ec2-fleet.md#create-ec2-fleet)\.

**To create an EC2 Fleet with a launch template \(AWS CLI\)**
+ Use the [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) AWS CLI command\. Use the `--launch-template-configs` parameter to specify the launch template and any overrides for the launch template\.

## Using Launch Templates with Spot Fleet<a name="launch-templates-spot-fleet"></a>

You can create a Spot Fleet request and specify a launch template in the instance configuration\. When Amazon EC2 fulfills the Spot Fleet request, it uses the launch parameters defined in the associated launch template\. You can override some of the parameters that are specified in the launch template\.

For more information, see [Spot Fleet Requests](spot-fleet-requests.md)\.

**To create a Spot Fleet request with a launch template \(AWS CLI\)**
+ Use the [request\-spot\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) AWS CLI command\. Use the `LaunchTemplateConfigs` parameter to specify the launch template and any overrides for the launch template\.

## Deleting a Launch Template<a name="delete-launch-template"></a>

If you no longer require a launch template, you can delete it\. Deleting a launch template deletes all of its versions\.

**To delete a launch template \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**\.

1. Select the launch template and choose **Actions**, **Delete template**\.

1. Choose **Delete launch template**\.

**To delete a launch template \(AWS CLI\)**
+ Use the [delete\-launch\-template](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-launch-template.html) \(AWS CLI\) command and specify the launch template\.

  ```
  aws ec2 delete-launch-template --launch-template-id lt-01238c059e3466abc
  ```