# Task 3: Automatically connect your EC2 instance to your RDS database<a name="option1-task3-connect-ec2-instance-to-rds-database"></a>

## Task objective<a name="option1-task3-connect-ec2-instance-to-rds-database-objective"></a>

The objective of this task is to use the automatic connection feature in the EC2 console to automatically configure the connection between your EC2 instance and your RDS database\.

## Steps to connect your EC2 instance and RDS database<a name="option1-task3-connect-ec2-instance-to-rds-database-steps"></a>

Use the following steps to connect your EC2 instance and RDS database using the automatic feature in the EC2 console\.

To view an animation of these steps, see [View an animation: Automatically connect a newly\-launched EC2 instance to an RDS database](#option1-task3-connect-ec2-instance-to-rds-database-animation)\.

**To automatically connect an EC2 instance to an RDS database using the EC2 console**

1. On the instance launch confirmation page \(it should be open from the previous task\), choose **Connect an RDS database**\.

   If you closed the confirmation page, follow these steps:

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the navigation pane, choose **Instances**\.

   1. Select the EC2 instance that you just created, and then choose **Actions**, **Networking**, **Connect RDS database**\.

      If **Connect RDS database** is not available, check that the EC2 instance is in the **Running** state\.

1. For **Database role**, choose **Instance**\. *Instance* in this case refers to the database instance\.

1. For **RDS database**, choose the RDS database that you created in Task 1\.
**Note**  
The EC2 instance and the RDS database must be in the same VPC in order to connect to each other\.

1. Choose **Connect**\.

### View an animation: Automatically connect a newly\-launched EC2 instance to an RDS database<a name="option1-task3-connect-ec2-instance-to-rds-database-animation"></a>

![\[This animation shows how to select an existing EC2 instance in the EC2 console and use the automatic connection feature to connect the EC2 instance to an RDS database. For the text version of this animation, see the steps in the preceding procedure.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/tutorial-connect-new-ec2-rds.gif)

You're now ready for [Task 4: Verify the connection configuration](option1-task4-verify-connection-configuration.md)\.