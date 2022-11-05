# Task 2: Create an RDS database and automatically connect it to your EC2 instance<a name="option2-task2-create-rds-database"></a>

## Task objective<a name="option2-task2-create-rds-database-task-objective"></a>

The objective of this task is to create an RDS database and use the automatic connection feature in the RDS console to automatically configure the connection between your EC2 instance and your RDS database\.

## Steps to create an RDS database<a name="option2-task2-create-rds-database-steps"></a>

Use the following steps to create an RDS database and connect it to your EC2 instance using the automatic feature in the RDS console\.

To view an animation of these steps, see [View an animation: Create an RDS database and automatically connect it to an EC2 instance](#task2-create-rds-database-animation)\.

**DB instance configuration**

The steps in this task configure the DB instance as follows:
+ Engine type: MySQL
+ Template: Free tier
+ DB instance identifier: **tutorial\-database**
+ DB instance class: `db.t3.micro`

**Important**  
In a production environment, you should configure your instance to meet your specific needs\.

**To create an RDS database and automatically connect it to an EC2 instance**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. From the Region selector \(at top right\), choose the AWS Region in which you created the EC2 instance\. The EC2 instance and the RDS database must be in the same Region\.

1. On the dashboard, choose **Create database**\.

1. Under **Choose a database creation method**, check that **Standard create** is selected\. If you choose **Easy create**, the automatic connection feature is not available\.

1. Under **Engine options**, for **Engine type**, choose **MySQL**\.

1. Under **Templates**, choose a sample template to meet your needs\. For this tutorial, choose **Free tier** to create an RDS database at no cost\. However, note that the free tier is only available if your account is less than 12 months old\. Other restrictions apply\. You can read more by choosing the **Info** link in the **Free tier** box\.

1. Under **Settings**, do the following:

   1. For **DB instance identifier**, enter a name for the database\. For this tutorial, enter **tutorial\-database**\.

   1. For **Master username**, leave the default name, which is **admin**\.

   1. For **Master password**, enter a password that you can remember for this tutorial, and then, for **Confirm password**, enter the password again\.

1. Under **Instance configuration**, for **DB instance class**, leave the default, which is **db\.t3\.micro**\. If your account is less than 12 months, you can use this instance for free\. Other restrictions apply\. For more information, see [AWS Free Tier](http://aws.amazon.com/free/)\.

1. Under **Connectivity**, for **Compute resource**, choose **Connect to an EC2 compute resource**\. This is the automatic connection feature in the RDS console \.

1. For **EC2 instance**, choose the EC2 instance that you want to connect to\. For the purposes of this tutorial, you can either choose the instance that you created in the previous task, which you named **tutorial\-instance**, or choose another existing instance\. If you don't see your instance in the list, choose the refresh icon to the right of **Connectivity**\.

   When you use the automatic connection feature, a security group is added to this EC2 instance, and another security group is added to the RDS database\. The security groups are automatically configure to allow traffic between the EC2 instance and the RDS database\. In the next task, you'll verify that the security groups were created and assigned to the EC2 instance and RDS database\.

1. Choose **Create database**\.

   On the **Databases** screen, the **Status** of the new database is **Creating** until the database is ready to use\. When the status changes to **Available**, you can connect to the database\. Depending on the database class and the amount of storage, it can take up to 20 minutes before the new database is available\.

To learn more, see [Configure automatic network connectivity with an EC2 instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html#USER_CreateDBInstance.Prerequisites.VPC.Automatic) in the *Amazon RDS User Guide*\.

### View an animation: Create an RDS database and automatically connect it to an EC2 instance<a name="task2-create-rds-database-animation"></a>

![\[This animation shows how to create an RDS database, and then use the automatic connection functionality to connect it to an EC2 instance. For the text version of this animation, see the steps in the preceding procedure.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/tutorial-create-rds-connect-ec2.gif)

You're now ready for [Task 3: Verify the connection configuration](option2-task3-verify-connection-configuration.md)\.