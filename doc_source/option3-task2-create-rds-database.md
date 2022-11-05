# Task 2: Create an RDS database – *optional*<a name="option3-task2-create-rds-database"></a>

**Note**  
Creating an RDS database is not the focus of this part of the tutorial\. If you already have an RDS database and would like to use it for this tutorial, you can skip this task\.

## Task objective<a name="option3-task2-create-rds-database-task-objective"></a>

The objective of this task is to create an RDS database\. You'll use this instance in Task 3 when you connect it to your EC2 instance\.

## Steps to create an RDS database<a name="option3-task2-create-rds-database-steps"></a>

Use the following steps to create an RDS database for Option 3 of this tutorial\.

To view an animation of these steps, see [View an animation: Create a DB instance](#option3-task2-create-rds-database-animation)\.

**RDS database configuration**

The steps in this task configure the RDS database as follows:
+ Engine type: MySQL
+ Template: Free tier
+ DB instance identifier: **tutorial\-database\-manual**
+ DB instance class: `db.t3.micro`

**Important**  
In a production environment, you should configure your instance to meet your specific needs\.

**To create a MySQL DB instance**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. From the Region selector \(at top right\), choose the AWS Region in which you created the EC2 instance\. The EC2 instance and the DB instance must be in the same Region\.

1. On the dashboard, choose **Create database**\.

1. Under **Choose a database creation method**, choose **Easy create**\. When you choose this option, the automatic connection feature to automatically configure the connection is not available\.

1. Under **Engine options**, for **Engine type**, choose **MySQL**\.

1. For **DB instance size**, choose **Free tier**\.

1. For **DB instance identifier** enter a name for the RDS database\. For this tutorial, enter **tutorial\-database\-manual**\.

1. For **Master username**, leave the default name, which is **admin**\.

1. For **Master password**, enter a password that you can remember for this tutorial, and then, for **Confirm password**, enter the password again\.

1. Choose **Create database**\.

   On the **Databases** screen, the **Status** of the new DB instance is **Creating** until the DB instance is ready to use\. When the status changes to **Available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.

### View an animation: Create a DB instance<a name="option3-task2-create-rds-database-animation"></a>

![\[This animation shows how to create a DB instance. For the text version of this animation, see the steps in the preceding procedure.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/tutorial-create-db-step2.gif)

You are now ready for [Task 3: Manually connect your EC2 instance to your RDS database by creating security groups and assigning them to the instances](option3-task3-connect-rds-database-to-ec2-instance.md)\.