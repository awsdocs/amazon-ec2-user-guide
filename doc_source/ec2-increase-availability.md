# Tutorial: Increase the Availability of Your Application on Amazon EC2<a name="ec2-increase-availability"></a>

Suppose that you start out running your app or website on a single EC2 instance, and over time, traffic increases to the point that you require more than one instance to meet the demand\. You can launch multiple EC2 instances from your AMI and then use Elastic Load Balancing to distribute incoming traffic for your application across these EC2 instances\. This increases the availability of your application\. Placing your instances in multiple Availability Zones also improves the fault tolerance in your application\. If one Availability Zone experiences an outage, traffic is routed to the other Availability Zone\.

You can use Amazon EC2 Auto Scaling to maintain a minimum number of running instances for your application at all times\. Amazon EC2 Auto Scaling can detect when your instance or application is unhealthy and replace it automatically to maintain the availability of your application\. You can also use Amazon EC2 Auto Scaling to scale your Amazon EC2 capacity up or down automatically based on demand, using criteria that you specify\.

In this tutorial, we use Amazon EC2 Auto Scaling with Elastic Load Balancing to ensure that you maintain a specified number of healthy EC2 instances behind your load balancer\. Note that these instances do not need public IP addresses, because traffic goes to the load balancer and is then routed to the instances\. For more information, see [Amazon EC2 Auto Scaling](https://aws.amazon.com/ec2/autoscaling/) and [Elastic Load Balancing](https://aws.amazon.com/elasticloadbalancing/)\.

![\[Architecture with Auto Scaling and Elastic Load Balancing\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/tutorial_as_elb_architecture.png)

**Topics**
+ [Prerequisites](#scale-and-load-balance-prerequisites)
+ [Scale and Load Balance Your Application](#scale-and-load-balance)
+ [Test Your Load Balancer](#test-load-balancer)

## Prerequisites<a name="scale-and-load-balance-prerequisites"></a>

This tutorial assumes that you have already done the following:

1. Created a virtual private cloud \(VPC\) with one public subnet in two or more Availability Zones\.

1. Launched an instance in the VPC\.

1. Connected to the instance and customized it\. For example, installing software and applications, copying data, and attaching additional EBS volumes\. For information about setting up a web server on your instance, see [Tutorial: Install a LAMP Web Server with the Amazon Linux AMI](install-LAMP.md)\.

1. Tested your application on your instance to ensure that your instance is configured correctly\.

1. Created a custom Amazon Machine Image \(AMI\) from your instance\. For more information, see [Creating an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md) or [Creating an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\.

1. \(Optional\) Terminated the instance if you no longer need it\.

1. Created an IAM role that grants your application the access to AWS it needs\. For more information, see [To create an IAM role using the IAM console](iam-roles-for-amazon-ec2.md#create-iam-role-console)\.

## Scale and Load Balance Your Application<a name="scale-and-load-balance"></a>

Use the following procedure to create a load balancer, create a launch configuration for your instances, create an Auto Scaling group with two or more instances, and associate the load balancer with the Auto Scaling group\.

**To scale and load\-balance your application**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, under **LOAD BALANCING**, choose **Load Balancers**\.

1. Choose **Create Load Balancer**\.

1. For **Application Load Balancer**, choose **Create**\.

1. On the **Configure Load Balancer** page, do the following:

   1. For **Name**, type a name for your load balancer\. For example, **my\-lb**\.

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

   1. For **Name**, type a name for the target group\.

   1. Keep **Protocol** as HTTP, **Port** as 80, and **Target type** as instance\.

   1. For **Health checks**, keep the default protocol and path\.

   1. Choose **Next: Register Targets**\.

1. On the **Register Targets** page, choose **Next: Review** to continue to the next page, as we'll use Amazon EC2 Auto Scaling to add EC2 instances to the target group\.

1. On the **Review** page, choose **Create**\. After the load balancer is created, choose **Close**\.

1. On the navigation pane, under **AUTO SCALING**, choose **Launch Configurations**\.
   + If you are new to Amazon EC2 Auto Scaling, you see a welcome page\. Choose **Create Auto Scaling group** to start the Create Auto Scaling Group wizard, and then choose **Create launch configuration**\.
   + Otherwise, choose **Create launch configuration**\.

1. On the **Choose AMI** page, select the **My AMIs** tab, and then select the AMI that you created in [Prerequisites](#scale-and-load-balance-prerequisites)\.

1. On the **Choose Instance Type** page, select an instance type, and then choose **Next: Configure details**\.

1. On the **Configure details** page, do the following:

   1. For **Name**, type a name for your launch configuration \(for example, **my\-launch\-config**\)\.

   1. For **IAM role**, select the IAM role that you created in [Prerequisites](#scale-and-load-balance-prerequisites)\.

   1. \(Optional\) If you need to run a startup script, expand **Advanced Details** and type the script in **User data**\.

   1. Choose **Skip to review**\.

1. On the **Review** page, choose **Edit security groups**\. You can select an existing security group or create a new one\. This security group must allow HTTP traffic and health checks from the load balancer\. If your instances will have public IP addresses, you can optionally allow SSH traffic if you need to connect to the instances\. When you are finished, choose **Review**\.

1. On the **Review** page, choose **Create launch configuration**\.

1. When prompted, select an existing key pair, create a new key pair, or proceed without a key pair\. Select the acknowledgment check box, and then choose **Create launch configuration**\.

1. After the launch configuration is created, you must create an Auto Scaling group\.
   + If you are new to Amazon EC2 Auto Scaling and you are using the Create Auto Scaling group wizard, you are taken to the next step automatically\.
   + Otherwise, choose **Create an Auto Scaling group using this launch configuration**\.

1. On the **Configure Auto Scaling group details** page, do the following:

   1. For **Group name**, type a name for the Auto Scaling group\. For example, **my\-asg**\.

   1. For **Group size**, type the number of instances \(for example, **2**\)\. Note that we recommend that you maintain approximately the same number of instances in each Availability Zone\.

   1. Select your VPC from **Network** and your two public subnets from **Subnet**\.

   1. Under **Advanced Details**, select **Receive traffic from one or more load balancers**\. Select your target group from **Target Groups**\.

   1. Choose **Next: Configure scaling policies**\.

1. On the **Configure scaling policies** page, choose **Review**, as we will let Amazon EC2 Auto Scaling maintain the group at the specified size\. Note that later on, you can manually scale this Auto Scaling group, configure the group to scale on a schedule, or configure the group to scale based on demand\.

1. On the **Review** page, choose **Create Auto Scaling group**\.

1. After the group is created, choose **Close**\.

## Test Your Load Balancer<a name="test-load-balancer"></a>

When a client sends a request to your load balancer, the load balancer routes the request to one of its registered instances\.

**To test your load balancer**

1. Verify that your instances are ready\. From the **Auto Scaling Groups** page, select your Auto Scaling group, and then choose the **Instances** tab\. Initially, your instances are in the `Pending` state\. When their states are `InService`, they are ready for use\.

1. Verify that your instances are registered with the load balancer\. From the **Target Groups** page, select your target group, and then choose the **Targets** tab\. If the state of your instances is `initial`, it's possible that they are still registering\. When the state of your instances is `healthy`, they are ready for use\. After your instances are ready, you can test your load balancer as follows\.

1. From the **Load Balancers** page, select your load balancer\.

1. On the **Description** tab, locate the DNS name\. This name has the following form:

   ```
   my-lb-xxxxxxxxxx.us-west-2.elb.amazonaws.com
   ```

1. In a web browser, paste the DNS name for the load balancer into the address bar and press Enter\. You'll see your website displayed\.