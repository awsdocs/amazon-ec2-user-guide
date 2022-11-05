# Option 1: Automatically connect your EC2 instance to your RDS database using the EC2 console<a name="tutorial-ec2-rds-option1"></a>

## Objective<a name="option1-objective"></a>

The objective of Option 1 is to explore the automatic connection feature in the EC2 console that automatically configures the connection between your EC2 instance and RDS database to allow traffic from the EC2 instance to the RDS database\. In Option 3, you'll learn how to manually configure the connection\.

## Before you begin<a name="option1-before-you-begin"></a>

You'll need the following to complete this tutorial:
+ An RDS database that is in the same VPC as the EC2 instance\. You can either use an existing RDS database or follow the steps in Task 1 to create a new RDS database\.
+ An EC2 instance that is in the same VPC as the RDS database\. You can either use an existing EC2 instance or follow the steps in Task 2 to create a new EC2 instance\.
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

## Tasks to complete Option 1<a name="option1-tasks"></a>
+ [Task 1: Create an RDS database – *optional*](option1-task1-create-rds-database.md)
+ [Task 2: Launch an EC2 instance – *optional*](option1-task2-launch-ec2-instance.md)
+ [Task 3: Automatically connect your EC2 instance to your RDS database](option1-task3-connect-ec2-instance-to-rds-database.md)
+ [Task 4: Verify the connection configuration](option1-task4-verify-connection-configuration.md)