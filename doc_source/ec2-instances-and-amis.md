# Instances and AMIs<a name="ec2-instances-and-amis"></a>

An *Amazon Machine Image \(AMI\)* is a template that contains a software configuration \(for example, an operating system, an application server, and applications\)\. From an AMI, you launch an *instance*, which is a copy of the AMI running as a virtual server in the cloud\. You can launch multiple instances of an AMI, as shown in the following figure\.

![\[Launch multiple instances from an AMI\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/architecture_ami_instance.png)

Your instances keep running until you stop or terminate them, or until they fail\. If an instance fails, you can launch a new one from the AMI\.

## Instances<a name="instances"></a>

An instance is a virtual server in the cloud\. Its configuration at launch is a copy of the AMI that you specified when you launched the instance\.

You can launch different types of instances from a single AMI\. An *instance type* essentially determines the hardware of the host computer used for your instance\. Each instance type offers different compute and memory capabilities\. Select an instance type based on the amount of memory and computing power that you need for the application or software that you plan to run on the instance\. For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

After you launch an instance, it looks like a traditional host, and you can interact with it as you would any computer\. You have complete control of your instances; you can use sudo to run commands that require root privileges\.

Your AWS account has a limit on the number of instances that you can have running\. For more information about this limit, and how to request an increase, see [How many instances can I run in Amazon EC2](http://aws.amazon.com/ec2/faqs/#how-many-instances-ec2) in the Amazon EC2 General FAQ\. 

### Storage for your instance<a name="storage-options"></a>

The root device for your instance contains the image used to boot the instance\. For more information, see [Amazon EC2 root device volume](RootDeviceStorage.md)\.

Your instance may include local storage volumes, known as instance store volumes, which you can configure at launch time with block device mapping\. For more information, see [Block device mapping](block-device-mapping-concepts.md)\. After these volumes have been added to and mapped on your instance, they are available for you to mount and use\. If your instance fails, or if your instance is stopped or terminated, the data on these volumes is lost; therefore, these volumes are best used for temporary data\. To keep important data safe, you should use a replication strategy across multiple instances, or store your persistent data in Amazon S3 or Amazon EBS volumes\. For more information, see [Storage](Storage.md)\.

### Security best practices<a name="security-best-practices"></a>
+ Use AWS Identity and Access Management \(IAM\) to control access to your AWS resources, including your instances\. You can create IAM users and groups under your AWS account, assign security credentials to each, and control the access that each has to resources and services in AWS\. For more information, see [Identity and access management for Amazon EC2](security-iam.md)\.
+ Restrict access by only allowing trusted hosts or networks to access ports on your instance\. For example, you can restrict SSH access by restricting incoming traffic on port 22\. For more information, see [Amazon EC2 security groups for Linux instances](ec2-security-groups.md)\.
+ Review the rules in your security groups regularly, and ensure that you apply the principle of *least privilege*—only open up permissions that you require\. You can also create different security groups to deal with instances that have different security requirements\. Consider creating a bastion security group that allows external logins, and keep the remainder of your instances in a group that does not allow external logins\.
+ Disable password\-based logins for instances launched from your AMI\. Passwords can be found or cracked, and are a security risk\. For more information, see [Disable password\-based remote logins for root](building-shared-amis.md#public-amis-disable-password-logins-for-root)\. For more information about sharing AMIs safely, see [Shared AMIs](sharing-amis.md)\.

### Stopping and terminating instances<a name="instance-stopping-starting-terminating"></a>

You can stop or terminate a running instance at any time\.

**Stopping an instance**  
When an instance is stopped, the instance performs a normal shutdown, and then transitions to a `stopped` state\. All of its Amazon EBS volumes remain attached, and you can start the instance again at a later time\. 

You are not charged for additional instance usage while the instance is in a stopped state\. A minimum of one minute is charged for every transition from a stopped state to a running state\. If the instance type was changed while the instance was stopped, you will be charged the rate for the new instance type after the instance is started\. All of the associated Amazon EBS usage of your instance, including root device usage, is billed using typical Amazon EBS prices\. 

When an instance is in a stopped state, you can attach or detach Amazon EBS volumes\. You can also create an AMI from the instance, and you can change the kernel, RAM disk, and instance type\.

**Terminating an instance**  
When an instance is terminated, the instance performs a normal shutdown\. The root device volume is deleted by default, but any attached Amazon EBS volumes are preserved by default, determined by each volume's `deleteOnTermination` attribute setting\. The instance itself is also deleted, and you can't start the instance again at a later time\.

To prevent accidental termination, you can disable instance termination\. If you do so, ensure that the `disableApiTermination` attribute is set to `true` for the instance\. To control the behavior of an instance shutdown, such as `shutdown -h` in Linux or `shutdown` in Windows, set the `instanceInitiatedShutdownBehavior` instance attribute to `stop` or `terminate` as desired\. Instances with Amazon EBS volumes for the root device default to `stop`, and instances with instance\-store root devices are always terminated as the result of an instance shutdown\.

For more information, see [Instance lifecycle](ec2-instance-lifecycle.md)\.

## AMIs<a name="amis"></a>

Amazon Web Services \(AWS\) publishes many [Amazon Machine Images \(AMIs\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html) that contain common software configurations for public use\. In addition, members of the AWS developer community have published their own custom AMIs\. You can also create your own custom AMI or AMIs; doing so enables you to quickly and easily start new instances that have everything you need\. For example, if your application is a website or a web service, your AMI could include a web server, the associated static content, and the code for the dynamic pages\. As a result, after you launch an instance from this AMI, your web server starts, and your application is ready to accept requests\.

All AMIs are categorized as either *backed by Amazon EBS*, which means that the root device for an instance launched from the AMI is an Amazon EBS volume, or *backed by instance store*, which means that the root device for an instance launched from the AMI is an instance store volume created from a template stored in Amazon S3\.

The description of an AMI indicates the type of root device \(either `ebs` or `instance store`\)\. This is important because there are significant differences in what you can do with each type of AMI\. For more information about these differences, see [Storage for the root device](ComponentsAMIs.md#storage-for-the-root-device)\. 

You can deregister an AMI when you have finished using it\. After you deregister an AMI, you can't use it to launch new instances\. Existing instances launched from the AMI are not affected\. Therefore, if you are also finished with the instances launched from these AMIs, you should terminate them\.