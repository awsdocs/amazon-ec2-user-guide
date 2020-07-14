# Amazon EC2 instances<a name="Instances"></a>

If you're new to Amazon EC2, see the following topics to get started:
+ [What is Amazon EC2?](concepts.md)
+ [Setting up with Amazon EC2](get-set-up-for-amazon-ec2.md)
+ [Tutorial: Getting started with Amazon EC2 Linux instances](EC2_GetStarted.md)
+ [Instance lifecycle](ec2-instance-lifecycle.md)

Before you launch a production environment, you need to answer the following questions\.

**Q\. What instance type best meets my needs?**  
Amazon EC2 provides different instance types to enable you to choose the CPU, memory, storage, and networking capacity that you need to run your applications\. For more information, see [Instance types](instance-types.md)\.

**Q\. What purchasing option best meets my needs?**  
Amazon EC2 supports On\-Demand Instances \(the default\), Spot Instances, and Reserved Instances\. For more information, see [Instance purchasing options](instance-purchasing-options.md)\.

**Q\. Which type of root volume meets my needs?**  
Each instance is backed by Amazon EBS or backed by instance store\. Select an AMI based on which type of root volume you need\. For more information, see [Storage for the root device](ComponentsAMIs.md#storage-for-the-root-device)\.

**Q\. Can I remotely manage a fleet of EC2 instances *and* machines in my hybrid environment?**  
AWS Systems Manager enables you to remotely and securely manage the configuration of your Amazon EC2 instances, and your on\-premises instances and virtual machines \(VMs\) in hybrid environments, including VMs from other cloud providers\. For more information, see the *[AWS Systems Manager User Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/)*\.