# Task 3: Manually connect your EC2 instance to your RDS database by creating security groups and assigning them to the instances<a name="option3-task3-connect-rds-database-to-ec2-instance"></a>

## Task objective<a name="option3-task3-connect-rds-database-to-ec2-instance-task-objective"></a>

The objective of this task is to reproduce the connection configuration of the automatic connection feature by performing the following manually: You create two new security groups, and then add a security group each to the EC2 instance and the RDS database\.

## Steps to create new security groups and add them to the instances<a name="option3-task3-connect-rds-database-to-ec2-instance-steps"></a>

Use the following steps to connect an EC2 instance to your RDS database by creating two new security groups\. You then add a security group each to the EC2 instance and the RDS database\.

**To create two new security groups and assign one each to the EC2 instance and RDS database**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. First create the security group to add to the EC2 instance, as follows:

   1. In the navigation pane, choose **Security Groups**\.

   1. Choose **Create security group**\.

   1. For **Security group name**, enter a descriptive name for the security group\. For this tutorial, enter **ec2\-rds\-manual\-configuration**\.

   1. For **Description**, enter a brief description\. For this tutorial, enter **EC2 instance security group to allow EC2 instance to securely connect to RDS database**\.

   1. Choose **Create security group**\. You'll come back to this security group to add an outbound rule after you've created the RDS database security group\.

1. Now, create the security group to add to the RDS database, as follows:

   1. In the navigation pane, choose **Security Groups**\.

   1. Choose **Create security group**\.

   1. For **Security group name**, enter a descriptive name for the security group\. For this tutorial, enter **rds\-ec2\-manual\-configuration**\.

   1. For **Description**, enter a brief description\. For this tutorial, enter **RDS database security group to allow EC2 instance to securely connect to RDS database**\.

   1. Under **Inbound rules**, choose **Add rule**, and do the following:

      1. For **Type**, choose **MYSQL/Aurora**\.

      1. For **Source**, choose the EC2 instance security group **ec2\-rds\-manual\-configuration** that you created in Step 2 of this procedure\.

   1. Choose **Create security group**\.

1. Edit the EC2 instance security group to add an outbound rule, as follows:

   1. In the navigation pane, choose **Security Groups**\.

   1. Select the EC2 instance security group \(you named it **ec2\-rds\-manual\-configuration**\), and choose the **Outbound rules** tab\.

   1. Choose **Edit outbound rules**\.

   1. Choose **Add rule**, and do the following:

      1. For **Type**, choose **MYSQL/Aurora**\.

      1. For **Source**, choose the RDS database security group **rds\-ec2\-manual\-configuration** that you created in Step 3 of this procedure\.

      1. Choose **Save rules**\.

1. Add the EC2 instance security group to the EC2 instance as follows:

   1. In the navigation pane, choose **Instances**\.

   1. Select your EC2 instance, and choose **Actions**, **Security**, **Change security groups**\.

   1. Under **Associated security groups**, choose the **Select security groups** field, choose **ec2\-rds\-manual\-configuration** that you created earlier, and then choose **Add security group**\.

   1. Choose **Save**\.

1. Add the RDS database security group to the RDS database as follows:

   1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   1. In the navigation pane, choose **Databases** and select your database\.

   1. Choose **Modify**\.

   1. Under **Connectivity**, for **Security group**, choose **rds\-ec2\-manual\-configuration** that you created earlier, and then choose **Continue**\.

   1. Under **Scheduling of modifications**, choose **Apply immediately**\.

   1. Choose **Modify DB instance**\.

   You have now completed the manual steps that mimic the automatic steps that occur when you use the automatic connection feature\.

You have completed Option 3 of this tutorial\. If you've completed Options 1, 2, and 3, and you no longer need the resources that were created in this tutorial, you should delete them to prevent incurring unnecessary costs\. For more information, see [Clean up](tutorial-ec2-rds-clean-up.md)\.