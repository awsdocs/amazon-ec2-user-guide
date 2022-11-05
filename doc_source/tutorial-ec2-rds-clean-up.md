# Clean up<a name="tutorial-ec2-rds-clean-up"></a>

Now that you have completed the tutorial, it is good practice to clean up \(delete\) any resources you no longer want to use\. Cleaning up AWS resources prevents your account from incurring any further charges\.

**Topics**
+ [Terminate your EC2 instance](#tutorial-ec2-rds-clean-up-terminate-instance)
+ [Delete your RDS database](#tutorial-ec2-rds-clean-up-delete-rds-database)

## Terminate your EC2 instance<a name="tutorial-ec2-rds-clean-up-terminate-instance"></a>

If you launched an EC2 instance specifically for this tutorial, you can terminate it to stop incurring any charges associated with it\.

**To terminate an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance that you created for this tutorial, and choose **Instance state**, **Terminate instance**\.

1. Choose **Terminate** when prompted for confirmation\.

## Delete your RDS database<a name="tutorial-ec2-rds-clean-up-delete-rds-database"></a>

If you created an RDS database specifically for this tutorial, you can delete it to stop incurring any charges associated with it\.

**To delete an RDS database using the console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Select the RDS database that you created for this tutorial, and choose **Actions**, **Delete**\.

1. Enter **delete me** in the box, and then choose **Delete**\.