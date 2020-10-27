# Detaching an Amazon EBS volume from a Linux instance<a name="ebs-detaching-volume"></a>

Detaching an Amazon EBS volume from an instance makes the volume available to attach to a different instance or to delete\. Detaching a volume does not affect the data on the volume\.

**Considerations**
+ You can detach an Amazon EBS volume from an instance explicitly or by terminating the instance\. However, if the instance is running, you must first unmount the volume from the instance\.
+ If an EBS volume is the root device of an instance, you must stop the instance before you can detach the volume\.
+ You can reattach a volume that you detached \(without unmounting it\), but it might not get the same mount point\. If there were writes to the volume in progress when it was detached, the data on the volume might be out of sync\.
+ After you detach a volume, you are still charged for volume storage as long as the storage amount exceeds the limit of the AWS Free Tier\. You must delete a volume to avoid incurring further charges\. For more information, see [Deleting an Amazon EBS volume](ebs-deleting-volume.md)\.

You can get directions for volumes on a Windows instance from [Detaching a volume from a Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-detaching-volume.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Unmount and detach a volume<a name="umount-detach-volume"></a>

Use the following procedure to unmount and detach a volume from an instance\. This can be useful when you need to attach the volume to a different instance\.

**To detach an EBS volume using the console**

1. From your Linux instance, use the following command to unmount the `/dev/sdh` device\.

   ```
   [ec2-user ~]$ umount -d /dev/sdh
   ```

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. Select a volume and choose **Actions**, **Detach Volume**\. 

1. When prompted for confirmation, choose **Yes, Detach**\. 

**To detach an EBS volume from an instance using the command line**

After unmounting the volume, you can use one of the following commands to detach it\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [detach\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/detach-volume.html) \(AWS CLI\)
+ [Dismount\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/Dismount-EC2Volume.html) \(AWS Tools for Windows PowerShell\)

## Troubleshooting<a name="detach-troubleshoot"></a>

The following are common problems encountered when detaching volumes, and how to resolve them\.

**Note**  
To guard against the possibility of data loss, take a snapshot of your volume before attempting to unmount it\. Forced detachment of a stuck volume can cause damage to the file system or the data it contains or an inability to attach a new volume using the same device name, unless you reboot the instance\.
+ If you encounter problems while detaching a volume through the Amazon EC2 console, it can be helpful to use the describe\-volumes CLI command to diagnose the issue\. For more information, see [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html)\.
+ If your volume stays in the `detaching` state, you can force the detachment by choosing **Force Detach**\. Use this option only as a last resort to detach a volume from a failed instance, or if you are detaching a volume with the intention of deleting it\. The instance doesn't get an opportunity to flush file system caches or file system metadata\. If you use this option, you must perform the file system check and repair procedures\. 
+ If you've tried to force the volume to detach multiple times over several minutes and it stays in the `detaching` state, you can post a request for help to the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\. To help expedite a resolution, include the volume ID and describe the steps that you've already taken\.
+ When you attempt to detach a volume that is still mounted, the volume can become stuck in the `busy` state while it is trying to detach\. The following output from describe\-volumes shows an example of this condition:

  ```
  "Volumes": [
      {
          "AvailabilityZone": "us-west-2b",
          "Attachments": [
              {
                  "AttachTime": "2016-07-21T23:44:52.000Z",
                  "InstanceId": "i-fedc9876",
                  "VolumeId": "vol-1234abcd",
                  "State": "busy",
                  "DeleteOnTermination": false,
                  "Device": "/dev/sdf"
              }
          ...
      }
  ]
  ```

  When you encounter this state, detachment can be delayed indefinitely until you unmount the volume, force detachment, reboot the instance, or all three\.