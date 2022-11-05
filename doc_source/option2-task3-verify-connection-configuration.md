# Task 3: Verify the connection configuration<a name="option2-task3-verify-connection-configuration"></a>

## Task objective<a name="option2-task3-verify-connection-configuration-task-objective"></a>

The objective of this task is to verify that the two security groups were created and assigned to the instance and the database\.

When you use the automatic connection feature in the RDS console to configure the connectivity, the security groups are automatically created and assigned to the instance and database, as follows:
+ Security group **rds\-ec2\-*x*** is created and added to the RDS database\. It has one inbound rule that references the **ec2\-rds\-*x*** security group as its source\. This allows traffic from the EC2 instance with the **ec2\-rds\-*x*** security group to reach the RDS database\.
+ Security group **ec2\-rds\-*x*** is created and added to the EC2 instance\. It has one outbound rule that references the **rds\-ec2\-*x*** security group as its destination\. This allows traffic from the EC2 instance to reach the RDS database with the **rds\-ec2\-*x*** security group\.

## Steps to verify the connection configuration<a name="option2-task3-verify-connection-configuration-steps"></a>

Use the following steps to verify the connection configuration\.

To view an animation of these steps, see [View an animation: Verify the connection configuration](#option2-task3-verify-automatic-connection-animation)\.

**To verify the connection configuration using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the EC2 instance that you selected to connect to the RDS database in the previous task, and choose the **Security** tab\.

1. Under **Security details**, **Security groups**, verify that a security group called **ec2\-rds\-*x*** is in the list\. *x* is a number\.

1. Choose the **ec2\-rds\-*x*** security group to open it\.

1. Choose the **Outbound rules** tab\.

1. Verify that the following security group rule exists, as follows:
   + Type: **MYSQL/Aurora**
   + Port range: **3306**
   + Destination: ***sg\-1234567890example* / rds\-ec2\-*x***
   + Description: **Rule to allow connections to **database\-tutorial** from any instances this security group is attached to**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation page, choose **Databases**\.

1. Choose the RDS database that you created for this tutorial\.

1. On the **Connectivity & security** tab, under **Security**, **VPC security groups**, verify that a security group called **rds\-ec2\-*x*** is displayed\.

1. Choose the **rds\-ec2\-*x*** security group\. The **Security Groups** screen in EC2 console opens\.

1. Choose the **rds\-ec2\-*x*** security group open it\.

1. Choose the **Inbound rules** tab\.

1. Verify that the following security group rule exists, as follows:
   + Type: **MYSQL/Aurora**
   + Port range: **3306**
   + Source: ***sg\-0987654321example* / ec2\-rds\-*x*** – This is the security group that is assigned to the EC2 instance that you verified in the preceding steps\.
   + Description: **Rule to allow connections from EC2 instances with *sg\-1234567890example* attached**

   By verifying that these security groups and security group rules exist and that they are assigned to the EC2 instance and RDS database as described in this procedure, you can verify that the connection was automatically configured by using the automatic connection feature\.

### View an animation: Verify the connection configuration<a name="option2-task3-verify-automatic-connection-animation"></a>

![\[This animation shows how to verify the connection configuration. For the text version of this animation, see the steps in the preceding procedure.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/tutorial-verify-automatic-connection.gif)

You have completed Option 2 of this tutorial\. You can now either complete Option 3, which teaches you how to manually configure the security groups that were automatically created in Option 2\.