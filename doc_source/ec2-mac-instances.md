# Amazon EC2 Mac instances<a name="ec2-mac-instances"></a>

Amazon EC2 Mac instances natively support the macOS operating system\.
+ EC2 Mac instances \(`mac1.metal`\) are built on Apple Mac Mini hardware (2018) powered by 3\.2 GHz Intel eighth\-generation \(Coffee Lake\) Core i7 processors\. 
+ EC2 M1 Mac instances \(`mac2.metal`\) are built on Apple Mac Mini hardware (2020) powered by Apple Silicon M1 processors\. 

EC2 Mac instances are ideal for developing, building, testing, and signing applications for Apple devices, such as iPhone, iPad, iPod, Mac, Apple Watch, and Apple TV\. You can connect to your Mac instance using SSH or VNC\.

**Note**  
The **unit of billing** is the **dedicated host**\. There is no additional charge for the actual EC2 Mac instance that runs on the host\.

For more information, see [Amazon EC2 Mac Instances](https://aws.amazon.com/mac) and [Pricing](https://aws.amazon.com/mac/#Pricing)\.

**Topics**
+ [Considerations](#mac-instance-considerations)
+ [Instance readiness](#mac-instance-readiness)
+ [Launch a EC2 Mac instance](#mac-instance-launch)
+ [Connect to your EC2 Mac instance](#connect-to-mac-instance)
+ [Modify the macOS screen sharing resolution on EC2 Mac instances](#mac-screen-resolution)
+ [EC2 macOS AMIs](#ec2-macos-images)
+ [Updating and upgrading the macOS system and software](#mac-instance-updates)
+ [EC2 macOS Init](#ec2-macos-init)
+ [EC2 System Monitoring for macOS](#mac-instance-system-monitor)
+ [Increase the size of an EBS volume attached to your EC2 Mac instance](#mac-instance-increase-volume)
+ [Stop and terminate your EC2 Mac instance](#mac-instance-stop)
+ [Subscribe to New macOS AMI Release Notifications](#subscribe-notifications)
+ [Release the Dedicated Host for your EC2 Mac instance](#mac-instance-release-dedicated-host)

## Considerations<a name="mac-instance-considerations"></a>

The following considerations apply to Mac instances:
+ Both EC2 Mac instances types are available only as bare metal instances on [Dedicated Hosts](dedicated-hosts-overview.md), with a minimum allocation period of 24 hours before you can release the Dedicated Host\. You can launch one Mac instance per Dedicated Host\. You can share the Dedicated Host with the AWS accounts or organizational units within your AWS organization, or the entire AWS organization\.
+ Both EC2 Mac instances types are available only as On\-Demand EC2 instances\. They are not available as Spot Instances or Reserved Instances\. You can save money on Mac instances by purchasing a [Savings Plan](https://docs.aws.amazon.com/savingsplans/latest/userguide/)\.
+ EC2 Mac instances can run one of the following macOS operating systems:
  + macOS Mojave \(version 10\.14\.x\) \(mac1\.metal only\)
  + macOS Catalina \(version 10\.15\.x\) \(mac1\.metal only\)
  + macOS Big Sur \(version 11\.x\)
  + macOS Monterey \(version 12\.x\)
  + macOS Ventura \(version 13\.x\)
+ EBS hotplug is supported\.
+ AWS does not manage or support use of the internal SSD on the Apple Mac Mini hardware\. We strongly recommend that you use Amazon EBS volumes instead\. EBS volumes provide the same elasticity, availability and durability benefits on EC2 Mac instances as they do on any other EC2 instance\.
+ For optimal EBS performance, we recommend using General Purpose SSD \(`gp2` and `gp3`\) or Provisioned IOPS SSD \(`io1` and `io2`\) EBS volumes with EC2 Mac instances\.
+ [EC2 Mac instances support AWS EC2 Auto Scaling by utilizing Amazon License Manager with Host Resource Groups\.](http://aws.amazon.com/blogs/compute/implementing-autoscaling-for-ec2-mac-instances/) 
+ For both mac1\.metal \(x86_64\) and mac2\.metal \(arm64\) instances, automatic macOS software updates are disabled by default\. We recommend that you apply macOS software updates and test them on your instance before you put the instance into production\. For more information, see [Update the operating system and software](#mac-instance-updates)\.
+ When you stop or terminate EC2 Mac instances, there is a scrubbing workflow that is performed on the Dedicated Host\. For more information, see [Stop and terminate your Mac instance](#mac-instance-stop)\.

**Warning**  
Do not enable FileVault as it will result in the EC2 Mac instance failing to boot due to the partitions being locked\. If data\-at\-rest and data\-in\-transit is a requirement, use [EBS Encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html) to avoid the FileVault related boot issues and performance impact\.

## Instance readiness<a name="mac-instance-readiness"></a>

After you launch a EC2 Mac instance, you'll need to wait till the instance is ready before you can connect to it\. The approximate times you might wait are as follows:
+ EC2 Mac instances (`mac1.metal`) – up to 15 minutes from the initial launch
+ EC2 M1 Mac instances (`mac2.metal`) – up to 40 minutes from the initial launch

You can use a small shell script, like the one below, to poll the describe\-instance\-status API to know when the instance is ready to be connected to\. In the following command, replace the example region and instance ID with your own\.

```
region='us-east-1';
instance_id='i-0123456789example';

for i in seq 1 200; do aws ec2 describe-instance-status --region $region --instance-ids=$instance_id \
    --query='InstanceStatuses[0].InstanceStatus.Status'; sleep 5; 
done;
```

## Launch a Mac instance<a name="mac-instance-launch"></a>

EC2 Mac instances require a [Dedicated Host](dedicated-hosts-overview.md)\. You first need to allocate a host to your account, and then launch the instance onto the host\.

You can launch a Mac instance using the AWS Management Console or the AWS CLI\. 

### Launch a Mac instance using the console<a name="mac-instance-launch-console"></a>

**To launch a Mac instance onto a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Allocate the Dedicated Host, as follows:

   1. In the navigation pane, choose **Dedicated Hosts**\.

   1. Choose **Allocate Dedicated Host** and then do the following:

      1. For **Instance family**, choose **mac1** or **mac2**\. If the instance family doesn’t appear in the list, it’s not supported in the currently selected Region\.

      1. For **Instance type**, choose **mac1\.metal** or **mac2\.metal** based on the instance family chosen\.

      1. For **Availability Zone**, choose the Availability Zone for the Dedicated Host\.

      1. For **Quantity**, keep **1**\.

      1. Choose **Allocate**\.

1. Launch the instance on the host, as follows:

   1. Select the Dedicated Host that you created and then do the following:

      1. Choose **Actions**, **Launch instance\(s\) onto host**\.

      1. Under **Application and OS Images \(Amazon Machine Image\)**, select a macOS AMI\.

      1. Under **Instance type**, select the appropriate instance type \(**mac1\.metal** or **mac2\.metal**\)\.

      1. Under **Advanced details**, verify that **Tenancy**, **Tenancy host by**, and **Tenancy host ID** are preconfigured based on the Dedicated Host you created\. Update **Tenancy affinity** as needed\.

      1. Complete the wizard, specifying EBS volumes, security groups, and key pairs as needed\.

      1. In the **Summary** panel, choose **Launch instance**\.

   1. A confirmation page lets you know that your instance is launching\. Choose **View all instances** to close the confirmation page and return to the console\. The initial state of an instance is `pending`\. The instance is ready when its state changes to `running` and it passes status checks\.
**Note**  
EC2 Mac instances can take up to 15\-40 minutes to be ready\. For more information, see [Instance readiness](#mac-instance-readiness)\.

### Launch a Mac instance using the AWS CLI<a name="mac-instance-launch-cli"></a>

**Allocate the Dedicated Host**

Use the following [allocate\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-hosts.html) command to allocate a Dedicated Host for your Mac instance, replacing the `instance-type` with either `mac1.metal` or `mac2.metal`, and the `region` and `availability-zone` with the appropriate ones for your environment\.

```
aws ec2 allocate-hosts --region <region> --instance-type mac1.metal --availability-zone <availability zone e.g. us-east-1b> --auto-placement "on" --quantity 1
```

**Launch the instance on the host**

Use the following [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch a Mac instance, again replacing the `instance-type` with either `mac1.metal` or `mac2.metal`, and the `region` and `availability-zone` with the ones used previously\.

```
aws ec2 run-instances --region <region> --instance-type mac1.metal --placement Tenancy=host --image-id <ami_id> --key-name <my_key_pair>
```

The initial state of an instance is `pending`\. The instance is ready when its state changes to `running` and it passes status checks\. Use the following [describe\-instance\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-status.html) command to display status information for your instance\.

```
aws ec2 describe-instance-status --instance-ids <instance_id>
```

The following is example output for an instance that is running and has passed status checks\.

```
{
    "InstanceStatuses": [
        {
            "AvailabilityZone": "us-east-1b",
            "InstanceId": "i-017f8354e2dc69c4f",
            "InstanceState": {
                "Code": 16,
                "Name": "running"
            },
            "InstanceStatus": {
                "Details": [
                    {
                        "Name": "reachability",
                        "Status": "passed"
                    }
                ],
                "Status": "ok"
            },
            "SystemStatus": {
                "Details": [
                    {
                        "Name": "reachability",
                        "Status": "passed"
                    }
                ],
                "Status": "ok"
            }
        }
    ]
}
```

**Note**  
Mac instances can take up to 15\-40 minutes to be ready\. For more information, see [Instance readiness](#mac-instance-readiness)\.

## Connect to your Mac instance<a name="connect-to-mac-instance"></a>

You can connect to your Mac instance using SSH or Apple Remote Desktop \(ARD\)\.

**Note**  
After you launch a Mac instance, it can take up to 15\-40 minutes to be ready before you can connect to it\. For more information, see [Instance readiness](#mac-instance-readiness)\.

### Connect to your instance using SSH<a name="mac-instance-ssh"></a>

**Important**  
Multiple users can access the OS simultaneously; however, please review the appropriate [macOS SLA](https://www.apple.com/legal/sla/) with your counsel to confirm workload compliance\. Typically there is a 1:1 user:GUI session due to the built\-in Screen Sharing service on port 5900\. Using SSH within macOS supports multiple sessions up until the "Max Sessions" limit in sshd\_config file\.

Amazon EC2 Mac instances do not allow remote root SSH by default\. Password authentication is disabled to prevent brute\-force password attacks\. The ec2\-user account is configured to log in remotely using SSH\. The ec2\-user account also has sudo privileges\. After you connect to your instance, you can add other users\.

To support connecting to your instance using SSH, launch the instance using a key pair and a security group that allows SSH access, and ensure that the instance has internet connectivity\. You provide the `.pem` file for the key pair when you connect to the instance\.

Use the following procedure to connect to your Mac instance using an SSH client\. If you receive an error while attempting to connect to your instance, see [Troubleshoot connecting to your instance](TroubleshootingInstancesConnecting.md)\.

**To connect to your instance using SSH**

1. Verify that your local computer has an SSH client installed by entering ssh at the command line\. If your computer doesn't recognize the command, search for an SSH client for your operating system and install it\.

1. Get the public DNS name of your instance\. Using the Amazon EC2 console, you can find the public DNS name on both the **Details** and the **Networking** tabs\. Using the AWS CLI, you can find the public DNS name using the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\.

1. Locate the `.pem` file for the key pair that you specified when you launched the instance\.

1. Connect to your instance using the following ssh command, specifying the public DNS name of the instance and the `.pem` file\.

   ```
   ssh -i /path/key-pair-name.pem ec2-user@instance-public-dns-name
   ```

### Enable screen sharing service and connect to your instance using VNC<a name="mac-instance-vnc"></a>

Use the following steps to enable the screen sharing serviec and to connect your instance using Virtual Network Computing \(VNC\)\.

**Note**  
macOS Mojave (10\.14\) and newer only allows control if Screen Sharing is enabled through [System Preferences](https://support.apple.com/guide/remote-desktop/enable-remote-management-apd8b1c65bd/mac)\.

**To enable the screen sharing service and connect to your instance using VNC**

1. Verify that your local computer has a VNC client installed\. For macOS clients, you can leverage the built\-in macOS Screen Sharing application\. Otherwise, search for a VNC client solution for your respective operating system and then install the client software\.

1. From your local computer, [connect to your instance using SSH](#mac-instance-ssh)\.

1. (Optional) Run the following commands to prevent the default ec2\-user from being granted a secure token when setting their password\.

   ```
   sudo /usr/bin/dscl . append /Users/ec2-user AuthenticationAuthority ";DisabledTags;SecureToken"
   ```

1. Set up a password for the ec2\-user account using the passwd command as follows\.

   ```
   sudo /usr/bin/dscl . -passwd /Users/ec2-user
   ```

1. Enable and start the macOS Screen Sharing service for the user\.

   ```
   sudo /usr/bin/defaults write /var/db/launchd.db/com.apple.launchd/overrides.plist com.apple.screensharing -dict Disabled -bool false
   sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.screensharing.plist
   ```
   
1. (Optional) Enable multi\-session support and fast user switching\.

   ```
   sudo /usr/bin/defaults write .GlobalPreferences MultipleSessionsEnabled -bool TRUE
   /usr/bin/defaults write "Apple Global Domain" MultipleSessionsEnabled -bool true
   ```

1. From your computer, connect to your instance using the following ssh command\. In addition to the options shown in the previous section, use the \-L option to enable port forwarding and forward all traffic on local port 5900 to the VNC server service that is running on the instance\.

   ```
   ssh -L 5900:localhost:5900 -i /path/key-pair-name.pem ec2-user@instance-public-dns-name
   ```

1. From your local computer, use a VNC client to connect to localhost on port 5900\. For example, use the macOS built\-in Screen Sharing application as follows:

   1. Open Finder and launch the Screen Sharing application\.

   1. For **Connect to**, enter **localhost**\.

   1. Log in as prompted, using **ec2\-user** as the user name and the password that you created for the ec2\-user account\.

## Modify the macOS screen resolution on EC2 Mac instances<a name="mac-screen-resolution"></a>

After you connect to your EC2 Mac instance using a VNC client, you can modify the screen resolution of your macOS environment using any of the publicly available macOS utilities, such as [displayplacer](https://github.com/jakehilborn/displayplacer) or [BetterDisplay](https://github.com/waydabber/BetterDisplay)\.

**Note**  
The current build of displayplacer is not supported on mac2\.metal. For mac2\.metal aka EC2 M1 Mac instances, you can use the BetterDisplay tool to increase the display resolution\.

**To modify the screen resolution using displayplacer**

1. Install displayplacer\.

   ```
   brew tap jakehilborn/jakehilborn && brew install displayplacer
   ```

1. Show the current screen information and possible screen resolutions\.

   ```
   displayplacer list
   ```

1. Apply the desired screen resolution\.

   ```
   displayplacer "id:<screenID> res:<width>x<height> origin:(0,0) degree:0"
   ```

   For example:

   ```
   [ec2-user ~]$ RES="2560x1600"
   [ec2-user ~]$ displayplacer "id:69784AF1-CD7D-B79B-E5D4-60D937407F68 res:${RES} scaling:off origin:(0,0) degree:0"
   ```

## EC2 macOS AMIs<a name="ec2-macos-images"></a>

EC2 macOS AMIs are designed to provide a stable, secure, and high\-performance environment for developer workloads running on Amazon EC2 Mac instances\. The EC2 macOS AMIs include the following software to enable seamless usage on AWS, such as launch configuration utilities and AWS SDK tools\.

EC2 macOS AMIs include the following software by default:
+ [ENA driver for macOS](https://github.com/aws/homebrew-aws/blob/master/Casks/amazon-ena-ethernet.rb)
+ [EC2 macOS Init](https://github.com/aws/ec2-macos-init)
+ [SSM Agent for macOS](https://github.com/aws/amazon-ssm-agent)
+ [EC2 System Monitoring for macOS](https://github.com/aws/ec2-macos-system-monitor) \(mac1\.metal only\)
+ [AWS Command Line Interface \(AWS CLI\) version 2](https://github.com/aws/aws-cli/tree/v2)
+ [Apple Xcode Command Line Tools \(CLT\)](https://developer.apple.com/xcode/resources/)
+ [Homebrew](https://brew.sh/) with the [homebrew\-aws tap](https://github.com/aws/homebrew-aws)

AWS provides updated EC2 macOS AMIs on a regular basis that include updates to packages owned by AWS and the latest fully\-tested macOS version\. Additionally, AWS provides updated AMIs with the latest minor version updates or major version updates as soon as they can be fully tested and vetted\. If you do not need to preserve data or customizations to your Mac instances, you can get the latest updates by launching a new instance using the current AMI and then terminating the previous instance\. Otherwise, you can choose which updates to apply to your Mac instances\.

## Update the operating system and software<a name="mac-instance-updates"></a>

**Warning**  
Do not install beta or pre\-release macOS versions on your EC2 Mac instances, as this configuration is currently not supported\. Installing beta or pre release macOS versions will lead to degradation of your EC2 Mac Dedicated Host when you stop or terminate your instance, and will prevent you from starting or launching a new instance on that host\. 

On x86 Mac instances, you can install operating system updates from Apple using the `softwareupdate` command\. In\-place operating system updates are not currently supported on M1 Mac instances\.

****To install operating system updates from Apple on x86 Mac instances****

1. List the packages with available updates using the following command\.

   ```
   [ec2-user ~]$ softwareupdate --list
   ```

1. Install all updates or only specific updates\. To install specific updates, use the following command\.

   ```
   [ec2-user ~]$ sudo softwareupdate --install label
   ```

   To install all updates instead, use the following command\.

   ```
   [ec2-user ~]$ sudo softwareupdate --install --all --restart
   ```

System administrators can use AWS Systems Manager to roll out pre\-approved operating system updates on x86 Mac instances\. For more information, see the [AWS Systems Manager User Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/)\.

You can use Homebrew to install updates to packages in the EC2 macOS AMIs, so that you have the latest version of these packages on your instances\. You can also use Homebrew to install and run common macOS applications on Amazon EC2 macOS\. For more information, see the [Homebrew Documentation](https://docs.brew.sh/)\.

**To install updates using Homebrew**

1. Update Homebrew using the following command\.

   ```
   [ec2-user ~]$ brew update
   ```
   
1. After you update Homebrew, run diagnostics\.

   ```
   [ec2-user ~]$ brew doctor
   ```

1. List the packages with available updates using the following command\.

   ```
   [ec2-user ~]$ brew outdated
   ```

1. Upgrade all of the installed packages or only upgrade specific packages\. To upgrade specific packages, use the following command\.

   ```
   [ec2-user ~]$ brew upgrade <package name>
   ```

   To upgrade all the installed packages instead, use the following command\.

   ```
   [ec2-user ~]$ brew upgrade
   ```

## EC2 macOS Init<a name="ec2-macos-init"></a>

EC2 macOS Init is used to initialize EC2 Mac instances at launch\. It uses priority groups to run logical groups of tasks at the same time\.

The files for EC2 macOS Init are located in `/usr/local/aws/ec2-macos-init`\. The launchd plist file is `/Library/LaunchDaemons/com.amazon.ec2.macos-init.plist`\.

For more information, see [https://github.com/aws/ec2-macos-init](https://github.com/aws/ec2-macos-init)\.

## EC2 System Monitoring for macOS<a name="mac-instance-system-monitor"></a>

EC2 System Monitoring for macOS provides CPU utilization metrics to Amazon CloudWatch\. It sends these metrics to CloudWatch over a custom serial device in 1\-minute periods\. You can enable or disable this agent as follows\. It is enabled by default\.

```
sudo setup-ec2monitoring <enable|disable>
```

**Note**  
EC2 System Monitoring for macOS is not currently supported on mac2\.metal\.

## Increase the size of an EBS volume on your Mac instance<a name="mac-instance-increase-volume"></a>

You can increase the size of your Amazon EBS volumes on your Mac instance\. For more information, see [Amazon EBS Elastic Volumes](ebs-modify-volume.md)\.

After you increase the size of the volume, you must increase the size of your APFS container as follows\.

**Make increased disk space available for use**

1. Determine if a restart is needed\. If you resized an existing EBS volume on a running Mac instance, you must [reboot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-reboot.html) the instance to make the new size available\. If disk space modification was done during launch time, then a reboot will not be needed\. 

   View current status of disk sizes: 

   ```
   [ec2-user ~]$ diskutil list external physical
   /dev/disk0 (external, physical):
      #:                       TYPE NAME                    SIZE       IDENTIFIER
      0:                 GUID_partition_scheme            *322.1 GB     disk0
      1:                 EFI EFI                           209.7 MB     disk0s1
      2:                 Apple_APFS Container disk2        321.9 GB     disk0s2
   ```

1. Copy and paste the following command\.

   ```
   PDISK=$(diskutil list physical external | head -n1 | cut -d" " -f1)
   APFSCONT=$(diskutil list physical external | grep "Apple_APFS" | tr -s " " | cut -d" " -f8)
   yes | sudo diskutil repairDisk $PDISK
   ```

1. Copy and paste the following command\.

   ```
   sudo diskutil apfs resizeContainer $APFSCONT 0
   ```

## Stop and terminate your Mac instance<a name="mac-instance-stop"></a>

When you stop a Mac instance, the instance remains in the `stopping` state for about 15 minutes before it enters the `stopped` state\.

When you stop or terminate a Mac instance, Amazon EC2 performs a scrubbing workflow on the underlying Dedicated Host to erase the internal SSD, to clear the persistent NVRAM variables, and to update to the latest device firmware\. This ensures that Mac instances provide the same security and data privacy as other EC2 Nitro instances\. It also enables you to run the latest macOS AMIs\. During the scrubbing workflow, the Dedicated Host temporarily enters the pending state\. On x86 Mac instances, the scrubbing workflow may take up to 50 minutes to complete\. On M1 Mac instances, the scrubbing workflow may take up to 110 minutes to complete\. Additionally, on x86 Mac instances, if the device firmware needs to be updated, the scrubbing workflow may take up to 3 hours to complete\.

You can't start the stopped Mac instance or launch a new Mac instance until after the scrubbing workflow completes, at which point the Dedicated Host enters the `available` state\.

Metering and billing is paused when the Dedicated Host enters the `pending` state\. You are not charged for the duration of the scrubbing workflow\.

## Subscribe to macOS AMI notifications<a name="subscribe-notifications"></a>

To be notified when new AMIs are released or when bridgeOS has been updated, subscribe for notifications using Amazon SNS\.

**To subscribe to macOS AMI notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation bar, change the Region to **US East \(N\. Virginia\)**, if necessary\. You must use this Region because the SNS notifications that you are subscribing to were created in this Region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Choose **Create subscription**\.

1. For the **Create subscription** dialog box, do the following:

   1. For **Topic ARN**, copy and paste one of the following Amazon Resource Names \(ARNs\):
      + **arn:aws:sns:us\-east\-1:898855652048:amazon\-ec2\-macos\-ami\-updates**
      + **arn:aws:sns:us\-east\-1:898855652048:amazon\-ec2\-bridgeos\-updates**

      For **Protocol**:

   1. **Email:**

      For **Endpoint**, type an email address that you can use to receive the notifications\. After you create your subscription you'll receive a confirmation message with the subject line `AWS Notification - Subscription Confirmation`\. Open the email and choose **Confirm subscription** to complete your subscription

   1. **SMS:**

      For **Endpoint**, type a phone number that you can use to receive the notifications\.

   1. **AWS Lambda, Amazon SQS, Amazon Kinesis Data Firehose** \(*Notifications come in JSON format*\):

      For **Endpoint**, enter the ARN for the Lambda function, SQS queue, or Firehose stream you can use to receive the notifications\.

   1. Choose **Create subscription**\.

Whenever macOS AMIs are released, we send notifications to the subscribers of the `amazon-ec2-macos-ami-updates` topic\. Whenever bridgeOS is updated, we send notifications to the subscribers of the `amazon-ec2-bridgeos-updates` topic\. If you no longer want to receive these notifications, use the following procedure to unsubscribe\.

**To unsubscribe from macOS AMI notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation bar, change the Region to **US East \(N\. Virginia\)**, if necessary\. You must use this Region because the SNS notifications were created in this Region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Select the subscriptions and then choose **Actions**, **Delete subscriptions** When prompted for confirmation, choose **Delete**\.

## Release the Dedicated Host for your Mac instance<a name="mac-instance-release-dedicated-host"></a>

When you are finished with your Mac instance, you can release the Dedicated Host\. Before you can release the Dedicated Host, you must stop or terminate the Mac instance\. You cannot release the host until the allocation period exceeds the 24\-hour minimum\.

**To release the Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Instance state**, then choose either **Stop instance** or **Terminate instance**\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host and choose **Actions**, **Release host**\.

1. When prompted for confirmation, choose **Release**\.
