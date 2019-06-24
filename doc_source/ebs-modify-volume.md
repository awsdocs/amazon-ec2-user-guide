# Modifying the Size, Performance, or Type of an EBS Volume<a name="ebs-modify-volume"></a>

You can increase the volume size, change the volume type, or adjust the performance of your EBS volumes\. If your instance supports Elastic Volumes, you can do so without detaching the volume or restarting the instance\. This allows you to continue using your application while changes take effect\.

There is no charge to modify the configuration of a volume\. You are charged for the new volume configuration after volume modification starts\. For more information, see the [Amazon EBS Pricing](http://aws.amazon.com/ebs/pricing) page\.

**Topics**
+ [Requirements When Modifying Volumes](modify-volume-requirements.md)
+ [Requesting Modifications to Your EBS Volumes](requesting-ebs-volume-modifications.md)
+ [Monitoring the Progress of Volume Modifications](monitoring-volume-modifications.md)
+ [Extending a Linux File System After Resizing a Volume](recognize-expanded-volume-linux.md)