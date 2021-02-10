# Amazon EC2 Mac instances<a name="ec2-mac-instances"></a>

Mac1 instances natively support the macOS operating system\. They are built on Mac mini hardware and powered by 3\.2 GHz Intel eighth\-generation \(Coffee Lake\) Core i7 processors\. These instances are ideal for developing, building, testing, and signing applications for Apple devices, such as iPhone, iPad, iPod, Mac, Apple Watch, and Apple TV\. You can connect to your Mac instance using SSH or Virtual Network Computing \(VNC\)\.

For more information, see [Amazon EC2 Mac Instances](https://aws.amazon.com/mac)\.

**Topics**
+ [Considerations](#mac-instance-considerations)
+ [Launch a Mac instance using the console](#mac-instance-launch)
+ [Launch a Mac instance using the AWS CLI](#mac-instance-launch-cli)
+ [Connect to your instance using SSH](#mac-instance-ssh)
+ [Connect to your instance using VNC](#mac-instance-vnc)
+ [EC2 macOS AMIs](#ec2-macos-images)
+ [Update the operating system and software](#mac-instance-updates)
+ [EC2 macOS Init](#ec2-macos-init)
+ [EC2 System Monitoring for macOS](#mac-instance-system-monitor)
+ [Clean up your instance](#mac-instance-release)

## Considerations<a name="mac-instance-considerations"></a>

The following considerations apply to Mac instances:
+ Mac instances are available only as bare metal instances on Dedicated Hosts, with a minimum allocation period of 24 hours before you can release the Dedicated Host\. You can launch one Mac instance per Dedicated Host\. You can share the Dedicated Host with the AWS accounts or organizational units within your AWS organization, or the entire AWS organization\.
+ Mac instances are available only as On\-Demand Instances\. They are not available as Spot Instances or Reserved Instances\. You can save money on Mac instances by purchasing a [Savings Plan](https://docs.aws.amazon.com/savingsplans/latest/userguide/)\.
+ Mac instances run either macOS Mojave \(version 10\.14\) or macOS Catalina \(version 10\.15\)\.
+ If you attach an EBS volume to a running Mac instance, you must reboot the instance to make the volume available\.
+ If you attach a network interface to a running Mac instance, you must reboot the instance to make the network interface available\.
+ AWS does not manage or support the internal SSD on the Apple hardware\. We strongly recommend that you use Amazon EBS volumes instead\. EBS volumes provide the same elasticity, availability, and durability benefits on Mac instances as they do on any other EC2 instance\.
+ We recommend using General Purpose SSD \(`gp2` and `gp3`\) and Provisioned IOPS SSD \(`io1` and `io2`\) with Mac instances for optimal EBS performance\.
+ You cannot use Mac instances with Amazon EC2 Auto Scaling\.
+ Automatic software updates are disabled\. We recommend that you apply updates and test them on your instance before you put the instance into production\. For more information, see [Update the operating system and software](#mac-instance-updates)\.

## Launch a Mac instance using the console<a name="mac-instance-launch"></a>

You can launch a Mac instance using the AWS Management Console as described in the following procedure\. Mac instances require a Dedicated Host\.

**To launch a Mac instance onto a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Choose **Allocate Dedicated Host** and then do the following:

   1. For **Instance family**, choose **mac1**\. If **mac1** doesn’t appear in the list, it’s not supported in the currently selected Region\.

   1. For **Instance type**, select **mac1\.metal**\.

   1. For **Availability Zone**, choose the Availability Zone for the Dedicated Host\.

   1. For **Quantity**, keep **1**\.

   1. Choose **Allocate**\.

1. Select the Dedicated Host that you created and then do the following:

   1. Choose **Actions**, **Launch instances onto host**\.

   1. Select a macOS AMI\.

   1. Select the `mac1.metal` instance type\.

   1. On the **Configure Instance Details page**, verify that **Tenancy** and **Host** are preconfigured based on the Dedicated Host you created\. Update **Affinity** as needed\.

   1. Complete the wizard, specifying EBS volumes, security groups, and key pairs as needed\.

1. A confirmation page lets you know that your instance is launching\. Choose **View Instances** to close the confirmation page and return to the console\. The initial state of an instance is `pending`\. The instance is ready when its state changes to `running` and it passes status checks\.

## Launch a Mac instance using the AWS CLI<a name="mac-instance-launch-cli"></a>

Use the following [allocate\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-hosts.html) command to allocate a Dedicated Host for your Mac instance\.

```
aws ec2 allocate-hosts --instance-type mac1.metal --availability-zone us-east-1b --quantity 1
```

Use the following [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch a Mac instance\.

```
aws ec2 run-instances --instance-type mac1.metal --placement Tenancy=host --availability-zone us-east-1b --image-id ami_id --key-name my-key-pair
```

The initial state of an instance is `pending`\. The instance is ready when its state changes to `running` and it passes status checks\. Use the following [describe\-instance\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-status.html) command to display status information for your instance:

```
aws ec2 describe-instance-status --instance-ids i-017f8354e2dc69c4f
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

## Connect to your instance using SSH<a name="mac-instance-ssh"></a>

Amazon EC2 Mac instances do not allow remote root SSH by default\. Password authentication is disabled to prevent brute\-force password attacks\. The ec2\-user account is configured to log in remotely using SSH\. The ec2\-user account also has sudo privileges\. After you connect to your instance, you can add other users\.

To support connecting to your instance using SSH, launch the instance using a key pair and a security group that allows SSH access, and ensure that the instance has internet connectivity\. You provide the `.pem` file for the key pair when you connect to the instance\.

Use the following procedure to connect to your Mac instance using an SSH client\. If you receive an error while attempting to connect to your instance, see [Troubleshoot connecting to your instance](TroubleshootingInstancesConnecting.md)\.

**To connect to your instance using SSH**

1. Verify that your local computer has an SSH client installed by entering ssh at the command line\. If your computer doesn't recognize the command, search for an SSH client for your operating system and install it\.

1. Get the public DNS name of your instance\. Using the Amazon EC2 console, you can find the public DNS name on both the **Details** and the **Networking** tabs\. Using the AWS CLI, you can find the public DNS name using the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\.

1. Locate the `.pem` file for the key pair that you specified when you launched the instance\.

1. Connect to your instance using the following ssh command, specifying the public DNS name of the instance and the `.pem` file\.

   ```
   ssh -i /path/my-key-pair.pem ec2-user@my-instance-public-dns-name
   ```

## Connect to your instance using VNC<a name="mac-instance-vnc"></a>

Use the following procedure to connect to your instance using Virtual Network Computing \(VNC\)\.

**To connect to your instance using VNC**

1. Verify that your local computer has a VNC client installed\. On macOS, you can leverage the built\-in Screen Sharing application\. Otherwise, search for a VNC client for your operating system and install it\.

1. From your local computer, [connect to your instance using SSH](#mac-instance-ssh)\.

1. Set up a password for the ec2\-user account using the passwd command as follows\.

   ```
   [ec2-user ~]$ sudo passwd ec2-user
   ```

1. Start the VNC server and enable remote desktop access as follows\.

   ```
   [ec2-user ~]$ sudo /System/Library/CoreServices/RemoteManagement/ARDAgent.app/Contents/Resources/kickstart \
   -activate -configure -access -on \
   -restart -agent -privs -all
   ```

1. From your computer, connect to your instance using the following ssh command\. In addition to the options shown in the previous section, use the \-L option to enable port forwarding and forward all traffic on local port 5900 to the VNC server on the instance\.

   ```
   ssh -L 5900:localhost:5900 -i /path/my-key-pair.pem ec2-user@my-instance-public-dns-name
   ```

1. From your local computer, use the VNC client to connect to localhost on port 5900\. For example, use the Screen Sharing application on macOS as follows:

   1. Open Finder and launch the Screen Sharing application\.

   1. For **Connect to**, enter **localhost**\.

   1. Log in as prompted, using **ec2\-user** as the user name and the password that you created for the ec2\-user account\.

## EC2 macOS AMIs<a name="ec2-macos-images"></a>

Amazon EC2 macOS is designed to provide a stable, secure, and high\-performance environment for developer workloads running on Amazon EC2 Mac instances\. EC2 macOS AMIs includes packages that enable easy integration with AWS, such as launch configuration tools and popular AWS libraries and tools\. EC2 macOS AMIs include the following by default:
+ ENA drivers
+ EC2 macOS Init
+ EC2 System Monitoring for macOS
+ SSM Agent for macOS
+ AWS Command Line Interface \(AWS CLI\) version 2
+ Command Line Tools for Xcode
+ Homebrew

AWS provides updated EC2 macOS AMIs on a regular basis that include updates to AWS\-owned packages and the latest fully\-tested macOS version\. Additionally, AWS provides updated AMIs with the latest minor version updates or major version updates as soon as they can be fully tested and vetted\. If you do not need to preserve data or customizations to your Mac instances, you can get the latest updates by launching a new instance using the current AMI and then terminating the previous instance\. Otherwise, you can choose which updates to apply to your Mac instances\.

## Update the operating system and software<a name="mac-instance-updates"></a>

You can install operating system updates from Apple using the softwareupdate command\.

**To install operating system updates from Apple**

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
   [ec2-user ~]$ sudo softwareupdate --install
   ```

System administrators can use AWS Systems Manager to roll out pre\-approved operating system updates\. For more information, see the [AWS Systems Manager User Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/)\.

You can use Homebrew to install updates to packages in the EC2 macOS AMIs, so that you have the latest version of these packages on your instances\. You can also use Homebrew to install and run common macOS applications on Amazon EC2 macOS\. For more information, see the [Homebrew Documentation](https://docs.brew.sh/)\.

**To install updates using Homebrew**

1. Update Homebrew using the following command\.

   ```
   [ec2-user ~]$ brew update
   ```

1. List the packages with available updates using the following command\.

   ```
   [ec2-user ~]$ brew outdated
   ```

1. Install all updates or only specific updates\. To install specific updates, use the following command\.

   ```
   [ec2-user ~]$ brew upgrade formula
   ```

   To install all updates instead, use the following command\.

   ```
   [ec2-user ~]$ brew upgrade
   ```

## EC2 macOS Init<a name="ec2-macos-init"></a>

EC2 macOS Init is used to initialize EC2 Mac instances at launch\. It uses priority groups to run logical groups of tasks at the same time\.

The launchd plist file is `/Library/LaunchDaemons/com.amazon.ec2.macos-init.plist`\. The files for EC2 macOS Init are located in `/usr/local/aws/ec2-macos-init`\.

For more information, see [https://github\.com/aws/ec2\-macos\-init](https://github.com/aws/ec2-macos-init)\.

## EC2 System Monitoring for macOS<a name="mac-instance-system-monitor"></a>

EC2 System Monitoring for macOS provides CPU utilization metrics to Amazon CloudWatch\. It sends these metrics to CloudWatch over a custom serial device in 1\-minute periods\. You can enable or disable this agent as follows\. It is enabled by default\.

```
sudo setup-ec2monitoring [enable | disable]
```

## Clean up your instance<a name="mac-instance-release"></a>

When you are finished with your Mac instance, you can clean up by releasing the Dedicated Host\. Before you can release the Dedicated Host, you must stop or terminate the Mac instance\. You cannot release the host until the allocation period exceeds the 24\-hour minimum\.

**To release a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Instance state**, then choose either **Stop instance** or **Terminate instance**\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host and choose **Actions**, **Release host**\.

1. When prompted for confirmation, choose **Release**\.
