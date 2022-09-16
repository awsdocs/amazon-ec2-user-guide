# Step 5: Clean up unneeded resources<a name="step5-clean-up"></a>

In the following steps, you clean up the resources you created in this tutorial\. It is a good practice to delete resources that you are no longer using\. By deleting a resource you'll also stop incurring charges for that resource\.

**Note**  
Deleted, terminated, or released resources can't be recovered\.

**To terminate an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the check box for `tutorial-volumes`\.

1. Choose **Instance state**, and then **Terminate instance**\.

1. Choose **Terminate** when prompted for confirmation\.

**To delete a snapshot**

1. In the navigation pane, under **Elastic Block Store**, choose **Snapshots**\.

1. Select the check box for `tutorial-volumes-backup`\.

1. Under **Actions**, choose **Delete snapshot**\.

1. Choose **Delete** when prompted for confirmation\.