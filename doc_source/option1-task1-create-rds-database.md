# Task 1: Create an RDS database – *optional*<a name="option1-task1-create-rds-database"></a>

**Note**  
Creating a Amazon RDS database is not the focus of this tutorial\. If you already have an RDS database and would like to use it in this tutorial, you can skip this task\.

## Task objective<a name="option1-task1-create-rds-database-task-objective"></a>

The objective of this task is to create an RDS database so that you can complete Task 3 where you configure the connection between your EC2 instance and your RDS database\. If you have an RDS database that you can use, you can skip this task\.

**Important**  
If you use an existing RDS database, make sure that it is in the same VPC as your EC2 instance so that you can use the automatic connection feature\.

## Steps to create an RDS database<a name="option1-task1-create-rds-database-steps"></a>

Use the following steps to create an RDS database\.

To view an animation of these steps, see [View an animation: Create an RDS database](#task1-create-rds-database-animation)\.

**RDS database configuration**

The steps in this task configure the RDS database as follows:
+ Engine type: MySQL
+ Template: Free tier
+ DB instance identifier: **tutorial\-database\-1**
+ DB instance class: `db.t3.micro`

**Important**  
In a production environment, you should configure your database to meet your specific needs\.

**To create a MySQL RDS database**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. From the Region selector \(at top right\), choose an AWS Region\. The database and the EC2 instance must be in the same Region in order to use the automatic connection feature in the EC2 console\.

1. On the dashboard, choose **Create database**\.

1. Under **Choose a database creation method**, check that **Standard create** is selected\. If you choose **Easy create**, the VPC selector is not available\. You must ensure that your database is in the same VPC as your EC2 instance in order to use the automatic connection feature in the EC2 console\.

1. Under **Engine options**, for **Engine type**, choose **MySQL**\.

1. Under **Templates**, choose a sample template to meet your needs\. For this tutorial, choose **Free tier** to create a database at no cost\. However, note that the free tier is only available if your account is less than 12 months old\. Other restrictions apply\. You can read more by choosing the **Info** link in the **Free tier** box\.

1. Under **Settings**, do the following:

   1. For **DB instance identifier**, enter a name for the database\. For this tutorial, enter **tutorial\-database\-1**\.

   1. For **Master username**, leave the default name, which is **admin**\.

   1. For **Master password**, enter a password that you can remember for this tutorial, and then, for **Confirm password**, enter the password again\.

1. Under **Instance configuration**, for **DB instance class**, leave the default, which is **db\.t3\.micro**\. If your account is less than 12 months, you can use this database class for free\. Other restrictions apply\. For more information, see [AWS Free Tier](http://aws.amazon.com/free/)\.

1. Under **Connectivity**, for **Compute resource**, choose **Don't connect to an EC2 compute resource** because you'll connect the EC2 instance and the RDS database later in Task 3\.

   \(Later, in Option 2 of this tutorial, you'll try out the automatic connection feature in the RDS console by choosing **Connect to an EC2 compute resource**\.\)

1. For **Virtual private cloud \(VPC\)**, choose a VPC\. The VPC must have a DB subnet group\. To use the automatic connection feature, your EC2 instance and RDS database must be in the same VPC\.

1. Keep all the default values for the other fields on this page\.

1. Choose **Create database**\.

   On the **Databases** screen, the **Status** of the new database is **Creating** until the database is ready to use\. When the status changes to **Available**, you can connect to the database\. Depending on the database class and the amount of storage, it can take up to 20 minutes before the new database is available\.

### View an animation: Create an RDS database<a name="task1-create-rds-database-animation"></a>

![\[This animation shows how to create an RDS database. For the text version of this animation, see the steps in the preceding procedure.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/tutorial-create-rds-database.gif)

You're now ready for [Task 2: Launch an EC2 instance – *optional*](option1-task2-launch-ec2-instance.md)\.