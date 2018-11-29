# Appendix: Starting and Stopping an Instance to Modify an EBS Volume<a name="stop-start"></a>

If you are using a previous generation Amazon EC2 instance and you need to modify the root \(boot\) volume, you must stop the instance, apply the modifications, and then restart the instance\. The procedure described here can be used to modify any EBS volume on any instance type\.

When you stop and start an instance, be aware of the following:
+ If your instance is running in a VPC and has a public IPv4 address, we release the address and give it a new public IPv4 address\. The instance retains its private IPv4 addresses and any Elastic IP addresses\.
+ If your instance is in an Auto Scaling group, Amazon EC2 Auto Scaling marks the stopped instance as unhealthy, and may terminate it and launch a replacement instance\. To prevent this, you can temporarily suspend the Auto Scaling processes for the group\. For more information, see [Suspending and Resuming Scaling Processes](https://docs.aws.amazon.com/autoscaling/latest/userguide/as-suspend-resume-processes.html) in the *Amazon EC2 Auto Scaling User Guide*\.

**To modify the root volume of an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select the instance with the volume to expand\.

1. Verify that **Shutdown Behavior** is set to **Stop** and not **Terminate**\. 

   1. Select the instance\.

   1. From the context \(right\-click\) menu, choose **Instance Settings**, **Change Shutdown Behavior**\. 

   1. If **Shutdown behavior** is set to **Terminate**, choose **Stop**, **Apply**\.

      If **Shutdown behavior** is already set to **Stop**, choose **Cancel**\.

1. Stop the instance\. For more information, see [Stopping and Starting Your Instances](Stop_Start.md#starting-stopping-instances)\.
**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To keep data from instance store volumes, be sure to back it up to persistent storage\.

1. Modify your EBS volume as described in [Modifying an EBS Volume from the Console](console-modify.md) or [Modifying an EBS Volume from the Command Line](cli-modify.md)\.

1. Restart the instance\.

   1. In the navigation pane, choose **Instances** and then select the instance to restart\.

   1. From the context \(right\-click\) menu, choose **Instance State**, **Start**\.

   1. In the **Start Instances** dialog box, choose **Yes, Start**\. If the instance fails to start, and the volume being expanded is a root volume, verify that you attached the expanded volume using the same device name as the original volume, for example `/dev/sda1`\.

After the instance has started, you can check the file system size to see if your instance recognizes the larger volume space\. On Linux, use the df \-h command to check the file system size\.

```
[ec2-user ~]$ df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/xvda1            7.9G  943M  6.9G  12% /
tmpfs                 1.9G     0  1.9G   0% /dev/shm
```

If the size does not reflect your newly expanded volume, you must extend the file system of your device so that your instance can use the new space\. For more information, see [Extending a Linux File System after Resizing the Volume](recognize-expanded-volume-linux.md)\.