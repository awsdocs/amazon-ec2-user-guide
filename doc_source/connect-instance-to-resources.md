# Connect your EC2 instance to an AWS resource<a name="connect-instance-to-resources"></a>

After you launch an instance, you can connect it to one or more AWS resources\.

Currently this page only describes how to automatically connect an Amazon EC2 instance to an Amazon RDS database\. In the future, this page will provide instructions on how to connect other resources to your instance\.

## Automatically connect an EC2 instance to an RDS database<a name="connect-ec2-instance-to-rds-database"></a>

You can use the automatic connection functionality in the Amazon EC2 console to quickly connect one or more EC2 instances to an RDS database to allow traffic between them\.

For more information, see [How the connection is automatically configured](#how-traffic-is-enabled-between-instance-and-database)\. For a detailed walkthrough, which includes other ways to connect an EC2 instance and an RDS database, see [Tutorial: Connect an Amazon EC2 instance to an Amazon RDS database](tutorial-connect-ec2-instance-to-rds-database.md)\.

**Topics**
+ [Costs](#costs)
+ [Prerequisites](#prerequisites)
+ [Automatically connect an instance and a database](#automatically-connect-instance-and-database)
+ [How the connection is automatically configured](#how-traffic-is-enabled-between-instance-and-database)

### Costs<a name="costs"></a>

While there is no charge to automatically connect your EC2 instance to an RDS database, you are charged for the underlying services\. Data transfer fees will apply if your EC2 instance and RDS database are in different Availability Zones\. For information about data transfer fees, see [Data Transfer](http://aws.amazon.com/ec2/pricing/on-demand/#Data_Transfer) on the Amazon EC2 On\-Demand Pricing page\.

### Prerequisites<a name="prerequisites"></a>

Before you can automatically connect an EC2 instance to an RDS database, check the following:
+ The EC2 instances must be in the **Running** state\. You can't connect an EC2 instance if it's in another state\.
+ The EC2 instances and the RDS database must be in the same virtual private cloud \(VPC\)\. The automatic connection feature is not supported if an EC2 instance and RDS database are in different VPCs\.

### Automatically connect an instance and a database<a name="automatically-connect-instance-and-database"></a>

You can automatically connect an EC2 instance to an RDS database immediately after you've launched your instance, or later\.

#### Automatically connect immediately after launch<a name="automatically-connect-instance-and-database-after-launch"></a>

Use the following steps to automatically connect an EC2 instance to an RDS database immediately after you've launched the EC2 instance\.

To view an animation of these steps, see **[View an animation: Automatically connect a newly\-launched EC2 instance to an RDS database](#automatically-connect-ec2-rds-after-launch)**\.

**To automatically connect a newly\-launched EC2 instance to an RDS database using the EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the console dashboard, choose **Launch instances**, and then follow the steps to [launch an instance](ec2-launch-instance-wizard.md#liw-quickly-launch-instance)\.

1. On the instance launch confirmation page, choose **Connect an RDS database**\.

1. In the **Connect RDS Database** dialog box, do the following:

   1. For **Database role**, choose either **Cluster** or **Instance**\.

   1. For **RDS database**, select a database to connect to\.
**Note**  
The EC2 instances and the RDS database must be in the same VPC in order to connect to each other\.

   1. Choose **Connect**\.

##### View an animation: Automatically connect a newly\-launched EC2 instance to an RDS database<a name="automatically-connect-ec2-rds-after-launch"></a>

![\[This animation shows how to launch an EC2 instance using the launch instance wizard in the EC2 console, and then, immediately after launching the instance, using the automatic connection feature to connect the instance to an RDS database. For the text version of this animation, see the steps in the preceding procedure.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/automatically-connect-ec2-rds-after-launch.gif)

#### Automatically connect an existing instance<a name="automatically-connect-instance-and-database-later"></a>

Use the following steps to automatically connect an existing EC2 instance to an RDS database\.

To view an animation of these steps, see **[View an animation: Automatically connect an existing EC2 instance to an RDS database](#automatically-connect-ec2-rds-later)**\.

**To automatically connect an existing EC2 instance to an RDS database using the EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select one or more EC2 instances to connect to an RDS database, and then choose **Actions**, **Networking**, **Connect RDS database**\.

   If **Connect RDS database** is not available, check that the EC2 instances are in the **Running** state and that they are in the same VPC\.

1. In the **Connect RDS Database** dialog box, do the following:

   1. For **Database role**, choose either **Cluster** or **Instance**\.

   1. For **RDS database**, select a database to connect to\.
**Note**  
The EC2 instances and the RDS database must be in the same VPC in order to connect to each other\.

   1. Choose **Connect**\.

##### View an animation: Automatically connect an existing EC2 instance to an RDS database<a name="automatically-connect-ec2-rds-later"></a>

![\[This animation shows how to select an existing EC2 instance in the EC2 console and use the automatic connection feature to connect the EC2 instance to an RDS database. For the text version of this animation, see the steps in the preceding procedure.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/automatically-connect-ec2-rds-later.gif)

For information about how to use the Amazon RDS console to automatically connect an EC2 instance to an RDS database, see [Configure automatic network connectivity with an EC2 instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html#USER_CreateDBInstance.Prerequisites.VPC.Automatic) in the *Amazon RDS User Guide*\.

### How the connection is automatically configured<a name="how-traffic-is-enabled-between-instance-and-database"></a>

When you use the EC2 console to automatically configure the connection between an EC2 instance and an RDS database to allow traffic between them, the connection is configured by [security groups](ec2-security-groups.md)\.

The security groups are automatically created and added to the EC2 instance and RDS database, as follows:
+ Amazon EC2 creates a security group called **ec2\-rds\-*x*** and adds it to the EC2 instance\. It has one outbound rule that allows traffic to the database by specifying **rds\-ec2\-*x*** \(the database security group\) as its destination\.
+ Amazon RDS creates a security group called **rds\-ec2\-*x*** and adds it to the database\. It has one inbound rule that allows traffic from the EC2 instance by specifying **ec2\-rds\-*x*** \(the EC2 instance security group\) as its source\.

The security groups reference each other as the destination and source, and only allow traffic on the database port\. You can reuse these security groups so that any database with the **rds\-ec2\-*x*** security group can talk to any EC2 instance with the **ec2\-rds\-*x*** security group\.

The security group names follow a pattern\. For the security groups created by Amazon EC2, the pattern is **ec2\-rds\-*x***, and for the security groups created by Amazon RDS, the pattern is **rds\-ec2\-*x***\. ***x*** is a number, which increases by 1 each time a new security group is automatically created\.