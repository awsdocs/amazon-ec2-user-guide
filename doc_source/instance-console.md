# Getting Console Output<a name="instance-console"></a>

Console output is a valuable tool for problem diagnosis\. It is especially useful for troubleshooting kernel problems and service configuration issues that could cause an instance to terminate or become unreachable before its SSH daemon can be started\. 

Similarly, the ability to reboot instances that are otherwise unreachable is valuable for both troubleshooting and general instance management\. 

EC2 instances do not have a physical monitor through which you can view their console output\. They also lack physical controls that allow you to power up, reboot, or shut them down\. Instead, you perform these tasks through the Amazon EC2 API and the command line interface \(CLI\)\. 

## Instance Reboot<a name="instance-console-rebooting"></a>

 Just as you can reset a computer by pressing the reset button, you can reset EC2 instances using the Amazon EC2 console, CLI, or API\. For more information, see [Reboot Your Instance](ec2-instance-reboot.md)\. 

**Warning**  
For Windows instances, this operation performs a hard reboot that might result in data corruption\. 

## Instance Console Output<a name="instance-console-console-output"></a>

For Linux/Unix, the instance console output displays the exact console output that would normally be displayed on a physical monitor attached to a computer\. The console output returns buffered information that was posted shortly after an instance transition state \(start, stop, reboot, and terminate\)\. The posted output is not continuously updated; only when it is likely to be of the most value\.

For Windows instances, the instance console output includes the last three system event log errors\.

You can optionally retrieve the latest serial console output at any time during the instance lifecycle\. This option is only supported on [Nitro\-based Instances](instance-types.md#ec2-nitro-instances)\. It is not supported through the Amazon EC2 console\.

**Note**  
Only the most recent 64 KB of posted output is stored, which is available for at least 1 hour after the last posting\.

Only the instance owner can access the console output\. You can retrieve the console output for your instances using the console or the command line\.

**To get console output using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**, and select the instance\.

1. Choose **Actions**, **Instance Settings**, **Get System Log**\.

**To get console output using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [get\-console\-output](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-output.html) \(AWS CLI\)
+ [Get\-EC2ConsoleOutput](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ConsoleOutput.html) \(AWS Tools for Windows PowerShell\)

For more information about common system log errors, see [Troubleshooting System Log Errors for Linux\-Based Instances](TroubleshootingInstances.md#system-log-errors-linux)\.

## Capture a Screenshot of an Unreachable Instance<a name="instance-console-screenshot"></a>

If you are unable to reach your instance via SSH or RDP, you can capture a screenshot of your instance and view it as an image\. This provides visibility as to the status of the instance, and allows for quicker troubleshooting\.

There is no data transfer cost for this screenshot\. The image is generated in JPG format, no larger than 100 KB\.

**To access the instance console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**\.

1. Select the instance to capture\.

1. Choose **Actions**, **Instance Settings**\.

1. Choose **Get Instance Screenshot**\.

Right\-click on the image to download and save it\.

**To capture a screenshot using the command line**

You can use one of the following commands\. The returned content is base64\-encoded\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [get\-console\-screenshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-screenshot.html) \(AWS CLI\)
+ [GetConsoleScreenshot](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-GetConsoleScreenshot.html) \(Amazon EC2 Query API\)

## Instance Recovery When a Host Computer Fails<a name="instance-machine-failure"></a>

If there is an unrecoverable issue with the hardware of an underlying host computer, AWS may schedule an instance stop event\. You are notified of such an event ahead of time by email\.

**To recover an Amazon EBS\-backed instance running on a host computer that failed**

1. Back up any important data on your instance store volumes to Amazon EBS or Amazon S3\.

1. Stop the instance\.

1. Start the instance\.

1. Restore any important data\.

For more information, see [Stop and Start Your Instance](Stop_Start.md)\.

**To recover an instance store\-backed instance running on a host computer that failed**

1. Create an AMI from the instance\.

1. Upload the image to Amazon S3\.

1. Back up important data to Amazon EBS or Amazon S3\.

1. Terminate the instance\.

1. Launch a new instance from the AMI\.

1. Restore any important data to the new instance\.

For more information, see [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md)\.