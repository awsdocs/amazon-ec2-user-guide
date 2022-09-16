# Step 3: Increase the size of the data volume<a name="step3-increase-size-of-data-volume"></a>

Suppose after six months of using the above configuration you run out of storage space on your data volume\. You decide to double the size of your data volume\. To do this, first you create a snapshot, and then you increase the size of the data volume\. When you create a snapshot, use a description to identify the snapshot\. In this tutorial you create a snapshot called `tutorial-volumes-backup`\.

**Note**  
When you modify an EBS volume, it goes through a sequence of states—`modifying`, `optimizing`, and `completed`\. Keep in mind that the file system cannot be extended until the volume enters the `optimizing` state\.

You can view an animation that shows you how to create a snapshot and increase the size of the data volume by expanding **View an animation: Increase size of data volume**\. For the written steps, see the procedure that follows\.

## View an animation: Increase size of data volume<a name="step3-increase-size-of-data-volume-animation"></a>

![\[This animation shows how to increase the size of the data volume. For the text version of this animation, see the steps in the following procedure.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/increase-volume-size-and-snapshot.gif)

**To increase the size of the data volume**

1. Return to the Amazon EC2 console by closing the browser window of EC2 Instance Connect\.

1. Create a snapshot of the data volume, in case you need to roll back your changes\.

   1. In the Amazon EC2 console, in the navigation pane, choose **Instances**, and select `tutorial-volumes`\.

   1. On the **Storage** tab, under **Block devices** select the **Volume ID** of the data volume\.

   1. On the **Volumes** detail page, choose **Actions**, and **Create snapshot**\.

   1. Under **Description**, enter `tutorial-volumes-backup`\.

   1. Choose **Create snapshot**\.

1. To increase the data volume size, in the navigation pane, choose **Instances**, and select `tutorial-volumes`\.

1. Under the **Storage** tab, select the **Volume ID** of your data volume\.

1. Select the check box for your **Volume ID**, choose **Actions**, and then **Modify volume**\.

1. The **Modify volume** screen displays the volume ID and the volume’s current configuration, including type, size, input/output operations per second \(IOPS\), and throughput\. In this tutorial you double the size of the data volume\.

   1. For **Volume type**, do not change value\.

   1. For **Size**, change to 16 GB\.

   1. For **IOPS**, do not change value\.

   1. For **Throughput**, do not change value\.

1. Choose **Modify**, and when prompted for confirmation choose **Modify** again\. You are charged for the new volume configuration after volume modification starts\. For pricing information, see [Amazon EBS Pricing](http://aws.amazon.com/ebs/pricing/?nc1=h_ls)\.
**Note**  
You must wait at least six hours and ensure the volume is in the `in-use` or `available` state before you can modify the volume again\.