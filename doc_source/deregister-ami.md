# Deregister your Linux AMI<a name="deregister-ami"></a>

You can deregister an AMI when you have finished using it\. After you deregister an AMI, you can't use it to launch new instances\.

When you deregister an AMI, it doesn't affect any instances that you've already launched from the AMI\. You'll continue to incur usage costs for these instances\. Therefore, if you are finished with these instances, you should terminate them\.

The procedure that you'll use to clean up your AMI depends on whether it is backed by Amazon EBS or instance store\. For more information, see [Determin the root device type of your AMI](ComponentsAMIs.md#display-ami-root-device-type)\.

**Note**  
An AMI must be owned by your account in order to deregister it\.

**Topics**
+ [Clean up your Amazon EBS\-backed AMI](#clean-up-ebs-ami)
+ [Clean up your instance store\-backed AMI](#clean-up-s3-ami)

## Clean up your Amazon EBS\-backed AMI<a name="clean-up-ebs-ami"></a>

When you deregister an Amazon EBS\-backed AMI, it doesn't affect the snapshot\(s\) that were created for the volume\(s\) of the instance during the AMI creation process\. You'll continue to incur storage costs for the snapshots\. Therefore, if you are finished with the snapshots, you should delete them\. 

The following diagram illustrates the process for cleaning up your Amazon EBS\-backed AMI\.

![\[Process to clean up your Amazon EBS-backed AMI\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami_delete_ebs.png)

**To clean up your Amazon EBS\-backed AMI**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\. Select the AMI, and take note of its ID — this can help you find the correct snapshot in the next step\. Choose **Actions**, and then **Deregister**\. When prompted for confirmation, choose **Continue**\.

   It might take a few minutes before the console removes the AMI from the list\. Choose **Refresh** to refresh the status\.

1. In the navigation pane, choose **Snapshots**, and select the snapshot \(look for the AMI ID in the **Description** column\)\. Choose **Actions**, and then choose **Delete Snapshot**\. When prompted for confirmation, choose **Yes, Delete**\.

1. \(Optional\) If you are finished with an instance that you launched from the AMI, terminate it\. In the navigation pane, choose **Instances**\. Select the instance, choose **Instance state**, **Terminate instance**\. When prompted for confirmation, choose **Terminate**\.

## Clean up your instance store\-backed AMI<a name="clean-up-s3-ami"></a>

When you deregister an instance store\-backed AMI, it doesn't affect the files that you uploaded to Amazon S3 when you created the AMI\. You'll continue to incur usage costs for these files in Amazon S3\. Therefore, if you are finished with these files, you should delete them\.

The following diagram illustrates the process for cleaning up your instance store\-backed AMI\.

![\[Process to clean up your instance store-backed AMI\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami_delete_instance_store.png)

**To clean up your instance store\-backed AMI**

1. Deregister the AMI using the [deregister\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/deregister-image.html) command as follows\.

   ```
   aws ec2 deregister-image --image-id ami_id
   ```

1. Delete the bundle in Amazon S3 using the [ec2\-delete\-bundle](ami-tools-commands.md#ami-delete-bundle) \(AMI tools\) command as follows\.

   ```
   ec2-delete-bundle -b myawsbucket/myami -a your_access_key_id -s your_secret_access_key -p image
   ```

1. \(Optional\) If you are finished with an instance that you launched from the AMI, you can terminate it using the [terminate\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html) command as follows\.

   ```
   aws ec2 terminate-instances --instance-ids instance_id
   ```

1. \(Optional\) If you are finished with the Amazon S3 bucket that you uploaded the bundle to, you can delete the bucket\. To delete an Amazon S3 bucket, open the Amazon S3 console, select the bucket, choose **Actions**, and then choose **Delete**\.