# Delete an Amazon EBS volume<a name="ebs-deleting-volume"></a>

After you no longer need an Amazon EBS volume, you can delete it\. After deletion, its data is gone and the volume can't be attached to any instance\. However, before deletion, you can store a snapshot of the volume, which you can use to re\-create the volume later\. 

**Note**  
You can't delete a volume if it's attached to an instance\. To delete a volume, you must first detach it\. For more information, see [Detach an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\.  
You can check if a volume is attached to an instance\. In the console, on the **Volumes** page, you can view the state of your volumes\.  
If a volume is attached to an instance, it’s in the `in-use` state\.
If a volume is detached from an instance, it’s in the `available` state\. You can delete this volume\.

**To delete an EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. Select a volume and choose **Actions**, **Delete Volume**\. If **Delete Volume** is greyed out, the volume is attached to an instance\.

1. In the confirmation dialog box, choose **Yes, Delete**\. 

**To delete an EBS volume using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [delete\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-volume.html) \(AWS CLI\)
+ [Remove\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Volume.html) \(AWS Tools for Windows PowerShell\)