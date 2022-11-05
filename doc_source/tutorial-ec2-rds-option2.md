# Option 2: Automatically connect your EC2 instance to your RDS database using the RDS console<a name="tutorial-ec2-rds-option2"></a>

## Objective<a name="option2-objective"></a>

The objective of Option 2 is to explore the automatic connect feature in the RDS console that automatically configures the connection between your EC2 instance and RDS database to allow traffic from the EC2 instance to the RDS database\. In Option 3, you'll learn how to manually configure the connection\.

## Before you begin<a name="option2-before-you-begin"></a>

You'll need the following to complete this tutorial:
+ An EC2 instance that is in the same VPC as the RDS database\. You can either use an existing EC2 instance or follow the steps in Task 1 to create a new instance\.
+ Permissions to call the following operations:
  + `ec2:AssociateRouteTable`
  + `ec2:AuthorizeSecurityGroupEgress`
  + `ec2:CreateRouteTable`
  + `ec2:CreateSecurityGroup`
  + `ec2:CreateSubnet`
  + `ec2:DescribeInstances`
  + `ec2:DescribeNetworkInterfaces`
  + `ec2:DescribeRouteTables`
  + `ec2:DescribeSecurityGroups`
  + `ec2:DescribeSubnets`
  + `ec2:ModifyNetworkInterfaceAttribute`
  + `ec2:RevokeSecurityGroupEgress`

## Tasks to complete Option 2<a name="option2-tasks"></a>
+ [Task 1: Launch an EC2 instance – *optional*](option2-task1-launch-ec2-instance.md)
+ [Task 2: Create an RDS database and automatically connect it to your EC2 instance](option2-task2-create-rds-database.md)
+ [Task 3: Verify the connection configuration](option2-task3-verify-connection-configuration.md)