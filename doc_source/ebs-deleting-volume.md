# Delete an Amazon EBS volume<a name="ebs-deleting-volume"></a>

You can delete an Amazon EBS volume that you no longer need\. After deletion, its data is gone and the volume can't be attached to any instance\. So before deletion, you can store a snapshot of the volume, which you can use to re\-create the volume later\. 

**Note**  
You can't delete a volume if it's attached to an instance\. To delete a volume, you must first detach it\. For more information, see [Detach an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\.  
You can check if a volume is attached to an instance\. In the console, on the **Volumes** page, you can view the state of your volumes\.  
If a volume is attached to an instance, it’s in the `in-use` state\.
If a volume is detached from an instance, it’s in the `available` state\. You can delete this volume\.

You can delete an EBS volume using one of the following methods\.

------
#### [ Console ]

**To delete an EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. Select the volume to delete and choose **Actions**, **Delete volume**\.
**Note**  
If **Delete volume** is greyed out, the volume is attached to an instance\. You must detach the volume from the instance before it can be deleted\.

1. In the confirmation dialog box, choose **Delete**\.

------
#### [ AWS CLI ]

**To delete an EBS volume using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [delete\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-volume.html) \(AWS CLI\)
+ [Remove\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Volume.html) \(AWS Tools for Windows PowerShell\)

------