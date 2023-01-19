# Find interrupted Spot Instances<a name="finding-an-interrupted-Spot-Instance"></a>

In the console, the **Instances** pane displays all instances, including Spot Instances\. You can identify a Spot Instance from the `spot` value in the **Instance lifecycle** column\. The **Instance state** column indicates whether the instance is `pending`, `running`, `stopping`, `stopped`, `shutting-down`, or `terminated`\. For a hibernated Spot Instance, the instance state is `stopped`\.

**To find an interrupted Spot Instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\. In the top right corner, choose the settings icon \( ![\[Settings icon.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/settings-icon.png) \), and under **Attribute columns**, select **Instance lifecycle**\. For Spot Instances, **Instance lifecycle** is `spot`\.

   Alternatively, in the navigation pane, choose **Spot Requests**\. You can see both Spot Instance requests and Spot Fleet requests\. To view the IDs of the instances, select a Spot Instance request or a Spot Fleet request and choose the **Instances** tab\. Choose an instance ID to display the instance in the **Instances** pane\.

1. For each Spot Instance, you can view its state in the **Instance State** column\.

**To find interrupted Spot Instances \(AWS CLI\)**  
You can list your interrupted Spot Instances using the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command with the `--filters` parameter\. To list only the instance IDs in the output, include the `--query` parameter\.

```
aws ec2 describe-instances \
    --filters Name=instance-lifecycle,Values=spot Name=instance-state-name,Values=terminated,stopped \
    --query "Reservations[*].Instances[*].InstanceId"
```