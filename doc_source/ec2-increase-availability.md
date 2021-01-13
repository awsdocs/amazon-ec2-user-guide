# Tutorial: Increase the availability of your application on Amazon EC2<a name="ec2-increase-availability"></a>

Suppose that you start out running your app or website on a single EC2 instance, and over time, traffic increases to the point that you require more than one instance to meet the demand\. You can launch multiple EC2 instances from your AMI and then use Elastic Load Balancing to distribute incoming traffic for your application across these EC2 instances\. This increases the availability of your application\. Placing your instances in multiple Availability Zones also improves the fault tolerance in your application\. If one Availability Zone experiences an outage, traffic is routed to the other Availability Zone\.

You can use Amazon EC2 Auto Scaling to maintain a minimum number of running instances for your application at all times\. Amazon EC2 Auto Scaling can detect when your instance or application is unhealthy and replace it automatically to maintain the availability of your application\. You can also use Amazon EC2 Auto Scaling to scale your Amazon EC2 capacity up or down automatically based on demand, using criteria that you specify\.

In this tutorial, we use Amazon EC2 Auto Scaling with Elastic Load Balancing to ensure that you maintain a specified number of healthy EC2 instances behind your load balancer\. Note that these instances do not need public IP addresses, because traffic goes to the load balancer and is then routed to the instances\. For more information, see [Amazon EC2 Auto Scaling](https://aws.amazon.com/ec2/autoscaling/) and [Elastic Load Balancing](https://aws.amazon.com/elasticloadbalancing/)\.

![\[Architecture with Auto Scaling and Elastic Load Balancing\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/tutorial_as_elb_architecture.png)

**Topics**
+ [Prerequisites](#scale-and-load-balance-prerequisites)
+ [Scale and load balance your application](#scale-and-load-balance)
+ [Test your load balancer](#test-load-balancer)

## Prerequisites<a name="scale-and-load-balance-prerequisites"></a>

This tutorial assumes that you have already done the following:

1. Create a virtual private cloud \(VPC\) with one public subnet in two or more Availability Zones\.

1. Launch an instance in the VPC\.

1. Connect to the instance and customized it\. For example, installing software and applications, copying data, and attaching additional EBS volumes\. For information about setting up a web server on your instance, see [Tutorial: Install a LAMP web server with the Amazon Linux AMI](install-LAMP.md)\.

1. Test your application on your instance to ensure that your instance is configured correctly\.

1. Create a custom Amazon Machine Image \(AMI\) from your instance\. For more information, see [Create an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md) or [Create an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\.

1. \(Optional\) Terminate the instance if you no longer need it\.

1. Create an IAM role that grants your application the access to AWS it needs\. For more information, see [To create an IAM role using the IAM console](iam-roles-for-amazon-ec2.md#create-iam-role-console)\.

## Scale and load balance your application<a name="scale-and-load-balance"></a>

Use the following procedure to create a load balancer, create a launch configuration for your instances, create an Auto Scaling group with two or more instances, and associate the load balancer with the Auto Scaling group\.

**To scale and load\-balance your application**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, under **LOAD BALANCING**, choose **Load Balancers**\.

1. Choose **Create Load Balancer**\.

1. For **Application Load Balancer**, choose **Create**\.

1. On the **Configure Load Balancer** page, do the following:

   1. For **Name**, enter a name for your load balancer\. For example, **my\-lb**\.

   1. For **Scheme**, keep the default value, **internet\-facing**\.

   1. For **Listeners**, keep the default, which is a listener that accepts HTTP traffic on port 80\.

   1. For **Availability Zones**, select the VPC that you used for your instances\. Select an Availability Zone and then select the public subnet for that Availability Zone\. Repeat for a second Availability Zone\.

   1. Choose **Next: Configure Security Settings**\.

1. For this tutorial, you are not using a secure listener\. Choose **Next: Configure Security Groups**\.

1. On the **Configure Security Groups** page, do the following:

   1. Choose **Create a new security group**\.

   1. Type a name and description for the security group, or keep the default name and description\. This new security group contains a rule that allows traffic to the port configured for the listener\.

   1. Choose **Next: Configure Routing**\.

1. On the **Configure Routing** page, do the following:

   1. For **Target group**, keep the default, **New target group**\.

   1. For **Name**, enter a name for the target group\.

   1. Keep **Protocol** as HTTP, **Port** as 80, and **Target type** as instance\.

   1. For **Health checks**, keep the default protocol and path\.

   1. Choose **Next: Register Targets**\.

1. On the **Register Targets** page, choose **Next: Review** to continue to the next page, as we'll use Amazon EC2 Auto Scaling to add EC2 instances to the target group\.

1. On the **Review** page, choose **Create**\. After the load balancer is created, choose **Close**\.

1. On the navigation pane, under **AUTO SCALING**, choose **Launch Configurations**\.
   + If you are new to Amazon EC2 Auto Scaling, you see a welcome page\. Choose **Create Auto Scaling group** to start the Create Auto Scaling Group wizard, and then choose **Create launch configuration**\.
   + Otherwise, choose **Create launch configuration**\.

1. For **Launch configuration name**, enter a name for your launch configuration \(for example, **my\-launch\-config**\)\.

1. For **Amazon machine image \(AMI\)**, under **My AMIs**, choose the AMI that you created in [Prerequisites](#scale-and-load-balance-prerequisites)\.

1. For **Instance type**, use **Choose instance type** to select an instance type\.

1. \(Optional\) For **Additional configuration**, do the following as needed:

   1. Choose **Request Spot Instances**\. Otherwise, the instances are On\-Demand instances\.

   1. For **IAM instance profile**, select the IAM role that you created in [Prerequisites](#scale-and-load-balance-prerequisites)\.

   1. Choose **Enable EC2 instance detailed monitoring within CloudWatch**\. Otherwise, basic monitoring is enabled\.

   1. To configure instance metadata, expand **Advanced details**, enable or disable instance metadata, and configure the version and hop limit as needed\.

   1. To run a startup script, expand **Advanced details** and enter the script in **User data**\.

   1. To assign public IP addresses to your instances, expand **Advanced details** and set **IP address type** as needed\.

1. For **Storage \(volumes\)**, you add volumes as needed\. You can create empty EBS volumes or create EBS volumes from EBS snapshots\.

1. For **Security groups**, you can select an existing security group or create a new one\. This security group must allow HTTP traffic and health checks from the load balancer\. If you assigned public IP addresses to your instances, you can optionally allow SSH traffic so you can connect to them\.

1. For **Key pair \(login\)**, choose an existing key pair, create a new key pair, or proceed without a key pair\. Select the acknowledgment check box\.

1. Choose **Create launch configuration**\.

1. After the launch configuration is created, you must create an Auto Scaling group\.
   + If you are new to Amazon EC2 Auto Scaling and you are using the Create Auto Scaling group wizard, you are taken to the next step automatically\.
   + Otherwise, select the Auto Scaling group and choose **Actions**, **Create an Auto Scaling group**\.

1. On the **Choose launch template or configuration** page, enter a name for the Auto Scaling group\. For example, **my\-asg**\. Choose **Next**\.

1. On the **Configure settings** page, choose your VPC and your two public subnets\. Choose **Next**\.

1. On the **Configure advanced options** page, select **Enable load balancing** and choose your target group\. Select the **ELB** health check type and choose **Next**\.

1. For **Group size**, type the number of instances \(for example, **2**\)\. Note that we recommend that you maintain approximately the same number of instances in each Availability Zone\.

1. On the **Configure group size and scaling policies** page, you can configure the size of the group or configure the group to scale dynamically based on demand\. Choose **Next**\.

1. \(Optional\) Choose **Add notifications ** to configure SNS notifications for scaling activities\. Choose **Next**\.

1. \(Optional\) Choose **Add tag ** to add tags\. Choose **Next**\.

1. On the **Review** page, edit the details as needed, and then choose **Create Auto Scaling group**\.

## Test your load balancer<a name="test-load-balancer"></a>

When a client sends a request to your load balancer, the load balancer routes the request to one of its registered instances\.

**To test your load balancer**

1. Verify that your instances are ready\. From the **Auto Scaling Groups** page, select your Auto Scaling group, and then choose the **Instance management** tab\. Initially, your instances are in the `Pending` state\. When **Lifecycle** is `InService`, your instances are ready for use\.

1. Verify that your instances are registered with the load balancer\. From the **Target Groups** page, choose the name of the target group to open its details page, and then choose **Targets**\. If the state of your instances is `initial`, it's possible that they are still registering\. When the state of your instances is `healthy`, they are ready for use\. After your instances are ready, you can go to the next step\.

1. From the **Load Balancers** page, select your load balancer\.

1. On the **Description** tab, locate the DNS name\. This name has the following form:

   ```
   my-lb-xxxxxxxxxx.us-west-2.elb.amazonaws.com
   ```

1. In a web browser, paste the DNS name for the load balancer into the address bar and press Enter\. You'll see your website displayed\.