# Option 3: Manually connect your EC2 instance to your RDS database by mimicking the automatic connection feature<a name="tutorial-ec2-rds-option3"></a>

## Objective<a name="option3-objective"></a>

The objective of Option 3 is to learn how to manually configure the connection between an EC2 instance and an RDS database by manually reproducing the configuration of the automatic connection feature\.

## Before you begin<a name="option3-before-you-begin"></a>

You'll need the following to complete this tutorial:
+ An EC2 instance that is in the same VPC as the RDS database\. You can either use an existing EC2 instance or follow the steps in Task 1 to create a new instance\.
+ An RDS database that is in the same VPC as the EC2 instance\. You can either use an existing RDS database or follow the steps in Task 2 to create a new database\.
+ Permissions to call the following operations\. If you have completed Option 1 of this tutorial, you already have these permissions\.
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

### Tasks to complete Option 3<a name="option3-tasks"></a>
+ [Task 1: Launch an EC2 instance – *optional*](option3-task1-launch-ec2-instance.md)
+ [Task 2: Create an RDS database – *optional*](option3-task2-create-rds-database.md)
+ [Task 3: Manually connect your EC2 instance to your RDS database by creating security groups and assigning them to the instances](option3-task3-connect-rds-database-to-ec2-instance.md)