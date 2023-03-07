# Launch an instance from a launch template<a name="ec2-launch-templates"></a>

You can create a *launch template* that contains the configuration information to launch an instance\. You can use launch templates to store launch parameters so that you do not have to specify them every time you launch an instance\. For example, a launch template can contain the AMI ID, instance type, and network settings that you typically use to launch instances\. When you launch an instance using the Amazon EC2 console, an AWS SDK, or a command line tool, you can specify the launch template to use\.

For each launch template, you can create one or more numbered *launch template versions*\. Each version can have different launch parameters\. When you launch an instance from a launch template, you can use any version of the launch template\. If you do not specify a version, the default version is used\. You can set any version of the launch template as the default version—by default, it's the first version of the launch template\.

The following diagram shows a launch template with three versions\. The first version specifies the instance type, AMI ID, subnet, and key pair to use to launch the instance\. The second version is based on the first version and also specifies a security group for the instance\. The third version uses different values for some of the parameters\. Version 2 is set as the default version\. If you launched an instance from this launch template, the launch parameters from version 2 would be used if no other version were specified\.

![\[Launch template with three versions.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/launch-template-diagram.png)

**Topics**
+ [Launch template restrictions](launch-template-restrictions.md)
+ [Use launch templates to control launching instances](use-launch-templates-to-control-launching-instances.md)
+ [Create a launch template](create-launch-template.md)
+ [Modify a launch template \(manage launch template versions\)](manage-launch-template-versions.md)
+ [Delete a launch template](delete-launch-template.md)
+ [Launch instances from a launch template](launch-instances-from-launch-template.md)