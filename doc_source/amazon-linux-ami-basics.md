# Amazon Linux<a name="amazon-linux-ami-basics"></a>

Amazon Linux is provided by Amazon Web Services \(AWS\)\. It is designed to provide a stable, secure, and high\-performance execution environment for applications running on Amazon EC2\. It also includes packages that enable easy integration with AWS, including launch configuration tools and many popular AWS libraries and tools\. AWS provides ongoing security and maintenance updates to all instances running Amazon Linux\. Many applications developed on CentOS \(and similar distributions\) run on Amazon Linux\.

AWS provides two versions of Amazon Linux: Amazon Linux 2 and the Amazon Linux AMI\. For more information, including the complete list of AMIs, see [Amazon Linux 2](https://aws.amazon.com/amazon-linux-2/) and [Amazon Linux AMI](https://aws.amazon.com/amazon-linux-ami/)\. For Amazon Linux Docker container images, see [amazonlinux](https://hub.docker.com/_/amazonlinux/) on Docker Hub\.

If you are migrating from another Linux distribution to Amazon Linux, we recommend that you migrate to Amazon Linux 2\. If you are currently using the Amazon Linux AMI, we recommend that you migrate to Amazon Linux 2\. To migrate to Amazon Linux 2, launch an instance or create a virtual machine using the current image\. Install your application on Amazon Linux 2, plus any packages required by your application\. Test your application, and make any changes required for it to run on Amazon Linux 2\. For more information about running Amazon Linux outside AWS, see [Running Amazon Linux 2 as a Virtual Machine On\-Premises](amazon-linux-2-virtual-machine.md)\.


+ [Connecting to an Amazon Linux Instance](#connect-to-amazon-linux-limits)
+ [Identifying Amazon Linux Images](#amazon-linux-image-id)
+ [Included AWS Command Line Tools](#amazon-linux-aws-command-line-tools)
+ [Package Repository](#package-repository)
+ [Extras Library \(Amazon Linux 2\)](#extras-library)
+ [Accessing Source Packages for Reference](#amazon-linux-source-packages)
+ [cloud\-init](#amazon-linux-cloud-init)
+ [Subscribing to Amazon Linux Notifications](#linux-ami-notifications)
+ [Running Amazon Linux 2 as a Virtual Machine On\-Premises](amazon-linux-2-virtual-machine.md)

## Connecting to an Amazon Linux Instance<a name="connect-to-amazon-linux-limits"></a>

Amazon Linux does not allow remote root SSH by default\. Also, password authentication is disabled to prevent brute\-force password attacks\. To enable SSH logins to an Amazon Linux instance, you must provide your key pair to the instance at launch\. You must also set the security group used to launch your instance to allow SSH access\. By default, the only account that can log in remotely using SSH is ec2\-user; this account also has sudo privileges\. If you enable remote root log in, be aware that it is less secure than relying on key pairs and a secondary user\.

## Identifying Amazon Linux Images<a name="amazon-linux-image-id"></a>

Each image contains a unique `/etc/image-id` file that identifies it\. This file contains the following information about the image:

+ `image_name`, `image_version`, `image_arch` — From the build recipe that Amazon used to construct the image

+ `image_stamp` — A unique, random hex value generated during image creation

+ `image_date` — The UTC time of image creation, in *YYYYMMDDhhmmss* format

+ `recipe_name`, `recipe_id` — The name and ID of the build recipe Amazon used to construct the image

Amazon Linux contains an `/etc/system-release` file that specifies the current release that is installed\. This file is updated using yum and is part of the `system-release` RPM\.

Amazon Linux also contains a machine\-readable version of `/etc/system-release` that follows the CPE specification; see `/etc/system-release-cpe`\.

### Amazon Linux 2<a name="image-id-amazon-linux-2"></a>

The following is an example of `/etc/image-id` for the current version of Amazon Linux 2:

```
[ec2-user ~]$ cat /etc/image-id
image_name="amzn2-ami-hvm"
image_version="2017.12"
image_arch="x86_64"
image_file="amzn2-ami-hvm-2017.12.0.20171211-x86_64.xfs.gpt"
image_stamp="d61d-706a"
image_date="20171212050437"
recipe_name="amzn2 ami"
recipe_id="c00ff066-85f8-2b34-a948-7e51-50ec-4ec4-47e73d1f"
```

The following is an example of `/etc/system-release` for the current version of Amazon Linux 2:

```
[ec2-user ~]$ cat /etc/system-release
Amazon Linux release 2.0 (2017.12) LTS Release Candidate
```

The following is an example of `/etc/os-release` for Amazon Linux 2:

```
[ec2-user ~]$ cat /etc/os-release
NAME="Amazon Linux"
VERSION="2.0 (2017.12)"
ID="amzn"
ID_LIKE="centos rhel fedora"
VERSION_ID="2.0"
PRETTY_NAME="Amazon Linux 2.0 (2017.12) LTS Release Candidate"
ANSI_COLOR="0;33"
CPE_NAME="cpe:2.3:o:amazon:amazon_linux:2.0"
HOME_URL="https://amazonlinux.com/"
```

### Amazon Linux AMI<a name="image-id-amazon-linux-ami"></a>

The following is an example of `/etc/image-id` for the current Amazon Linux AMI:

```
[ec2-user ~]$ cat /etc/image-id
image_name="amzn-ami-hvm"
image_version="2017.09"
image_arch="x86_64"
image_file="amzn-ami-hvm-2017.09.0.20170930-x86_64.ext4.gpt"
image_stamp="2cb1-42fd"
image_date="20171001085438"
recipe_name="amzn ami"
recipe_id="2eae227c-4a8b-749a-6eb1-0ec0-2ca0-c60d-5ca6b2ab"
```

The following is an example of `/etc/system-release` for the current Amazon Linux AMI:

```
[ec2-user ~]$ cat /etc/system-release
Amazon Linux AMI release 2017.09
```

## Included AWS Command Line Tools<a name="amazon-linux-aws-command-line-tools"></a>

The following command line tools for AWS integration and usage are included in Amazon Linux or in the default repositories\. For the complete list of packages, see [Amazon Linux AMI 2017\.09 Packages](https://aws.amazon.com/amazon-linux-ami/2017.09-packages/)\.

+ aws\-amitools\-ec2

+ aws\-apitools\-as

+ aws\-apitools\-cfn

+ aws\-apitools\-ec2

+ aws\-apitools\-elb

+ aws\-apitools\-mon

+ aws\-cfn\-bootstrap

+ aws\-cli

The minimal versions of Amazon Linux \(`amzn-ami-minimal-*` and `amzn2-ami-minimal-*`\) do not contain these packages; however, you can install them from the default repositories using the following command:

```
[ec2-user ~]$ sudo yum install -y package_name
```

For instances launched using IAM roles, a simple script has been included to prepare `AWS_CREDENTIAL_FILE`, `JAVA_HOME`, `AWS_PATH`, `PATH`, and product\-specific environment variables after a credential file has been installed to simplify the configuration of these tools\.

Also, to allow the installation of multiple versions of the API and AMI tools, we have placed symbolic links to the desired versions of these tools in `/opt/aws`, as described here:

`/opt/aws/bin`  
Symbolic links to `/bin` directories in each of the installed tools directories\.

`/opt/aws/{apitools|amitools}`  
Products are installed in directories of the form *name*\-*version* and a symbolic link *name* that is attached to the most recently installed version\.

`/opt/aws/{apitools|amitools}/name/environment.sh`  
Used by `/etc/profile.d/aws-apitools-common.sh` to set product\-specific environment variables, such as `EC2_HOME`\.

## Package Repository<a name="package-repository"></a>

Amazon Linux is designed to be used with online package repositories hosted in each Amazon EC2 region\. These repositories provide ongoing updates to packages in Amazon Linux, as well as access to hundreds of additional common open source server applications\. The repositories are available in all regions and are accessed using yum update tools\. Hosting repositories in each region enables us to deploy updates quickly and without any data transfer charges\.

Amazon Linux is updated regularly with security and feature enhancements\. If you do not need to preserve data or customizations for your instances, you can simply launch new instances using the current AMI\. If you need to preserve data or customizations for your instances, you can maintain those instances through the Amazon Linux package repositories\. These repositories contain all the updated packages\. You can choose to apply these updates to your running instances\. Older versions of the AMI and update packages continue to be available for use, even as new versions are released\.

**Important**  
Your instance must have access to the internet in order to access the repository\.

To install packages, use the following command:

```
[ec2-user ~]$ sudo yum install package
```

Access to the Extra Packages for Enterprise Linux \(EPEL\) repository is configured, but it is not enabled by default\. EPEL provides third\-party packages in addition to those that are in the repositories\. The third\-party packages are not supported by AWS\.

If you find that Amazon Linux does not contain an application you need, you can simply install the application directly on your Amazon Linux instance\. Amazon Linux uses RPMs and yum for package management, and that is likely the simplest way to install new applications\. You should always check to see if an application is available in our central Amazon Linux repository first, because many applications are available there\. These applications can easily be added to your Amazon Linux instance\.

To upload your applications onto a running Amazon Linux instance, use scp or sftp and then configure the application by logging on to your instance\. Your applications can also be uploaded during the instance launch by using the PACKAGE\_SETUP action from the built\-in cloud\-init package\. For more information, see [cloud\-init](#amazon-linux-cloud-init)\. 

### Security Updates<a name="security-updates"></a>

Security updates are provided using the package repositories as well as updated AMIs Security alerts are published in the [Amazon Linux Security Center](https://alas.aws.amazon.com)\. For more information about AWS security policies or to report a security problem, go to the [AWS Security Center](https://aws.amazon.com/security/)\.

Amazon Linux is configured to download and install security updates at launch time\. This is controlled using the following cloud\-init setting: `repo_upgrade`\. The following snippet of cloud\-init configuration shows how you can change the settings in the user data text you pass to your instance initialization:

```
#cloud-config
repo_upgrade: security
```

The possible values for `repo_upgrade` are as follows:

`security`  
Apply outstanding updates that Amazon marks as security updates\.

`bugfix`  
Apply updates that Amazon marks as bug fixes\. Bug fixes are a larger set of updates, which include security updates and fixes for various other minor bugs\.

`all`  
Apply all applicable available updates, regardless of their classification\.

`none`  
Do not apply any updates to the instance on startup\.

The default setting for `repo_upgrade` is security\. That is, if you don't specify a different value in your user data, by default, Amazon Linux performs the security upgrades at launch for any packages installed at that time\. Amazon Linux also notifies you of any updates to the installed packages by listing the number of available updates upon login using the `/etc/motd` file\. To install these updates, you need to run sudo yum upgrade on the instance\. 

### Repository Configuration<a name="repository-config"></a>

With Amazon Linux, AMIs are treated as snapshots in time, with a repository and update structure that always gives you the latest packages when you run yum update \-y\.

The repository structure is configured to deliver a continuous flow of updates that enable you to roll from one version of Amazon Linux to the next\. For example, if you launch an instance from an older version of the Amazon Linux AMI \(such as 2017\.03 or earlier\) and run yum update \-y, you end up with the latest packages\.

You can disable rolling updates by enabling the *lock\-on\-launch* feature\. The lock\-on\-launch feature locks your instance to receive updates only from the specified release of the AMI\. For example, you can launch a 2017\.03 AMI and have it receive only the updates that were released prior to the 2017\.09 AMI, until you are ready to migrate to the 2017\.09 AMI\.

**Important**  
If you lock to a version of the repositories that is not the latest, you do not receive further updates\. To receive a continuous flow of updates, you must use the latest AMI, or consistently update your AMI with the repositories pointed to latest\.

To enable lock\-on\-launch in new instances, launch it with the following user data passed to cloud\-init:

```
#cloud-config
repo_releasever: 2017.03
```

**To lock existing instances to their current AMI version**

1. Edit `/etc/yum.conf`\.

1. Comment out `releasever=latest`\.

1. Run yum clean all to clear the cache\.

## Extras Library \(Amazon Linux 2\)<a name="extras-library"></a>

With Amazon Linux 2, you can use the Extras Library to install application and software updates on your instances\. These software updates are known as *topics*\. You can install a specific version of a topic or omit the version information to use the most recent version\.

To list the available topics, use the following command:

```
[ec2-user ~]$ amazon-linux-extras list
```

To enable a topic and install the latest version of its package to ensure freshness, use the following command:

```
[ec2-user ~]$ sudo amazon-linux-extras install topic
```

To enable topics and install specific versions of their packages to ensure stability, use the following command:

```
[ec2-user ~]$ sudo amazon-linux-extras install topic=version topic=version
```

## Accessing Source Packages for Reference<a name="amazon-linux-source-packages"></a>

You can view the source of packages you have installed on your instance for reference purposes by using tools provided in Amazon Linux\. Source packages are available for all of the packages included in Amazon Linux and the online package repository\. Simply determine the package name for the source package you want to install and use the yumdownloader \-\-source command to view source within your running instance\. For example:

```
[ec2-user ~]$ yumdownloader --source bash
```

The source RPM can be unpacked, and, for reference, you can view the source tree using standard RPM tools\. After you finish debugging, the package is available for use\.

## cloud\-init<a name="amazon-linux-cloud-init"></a>

The cloud\-init package is an open source application built by Canonical that is used to bootstrap Linux images in a cloud computing environment, such as Amazon EC2\. Amazon Linux contains a customized version of cloud\-init\. It enables you to specify actions that should happen to your instance at boot time\. You can pass desired actions to cloud\-init through the user data fields when launching an instance\. This means you can use common AMIs for many use cases and configure them dynamically at startup\. Amazon Linux also uses cloud\-init to perform initial configuration of the ec2\-user account\.

For more information, see [http://cloudinit.readthedocs.org/en/latest/](http://cloudinit.readthedocs.org/en/latest/)\.

Amazon Linux uses the cloud\-init actions found in `/etc/cloud/cloud.cfg.d` and `/etc/cloud/cloud.cfg`\. You can create your own cloud\-init action files in `/etc/cloud/cloud.cfg.d`\. All files in this directory are read by cloud\-init\. They are read in lexical order, and later files overwrite values in earlier files\.

The cloud\-init package performs these \(and other\) common configuration tasks for instances at boot:

+ Set the default locale

+ Set the hostname

+ Parse and handle user data

+ Generate host private SSH keys

+ Add a user's public SSH keys to `.ssh/authorized_keys` for easy login and administration

+ Prepare the repositories for package management

+ Handle package actions defined in user data

+ Execute user scripts found in user data

+ Mount instance store volumes if applicable

  + By default, the `ephemeral0` instance store volume is mounted at `/media/ephemeral0` if it is present and contains a valid file system; otherwise, it is not mounted\.

  + By default, any swap volumes associated with the instance are mounted \(only for `m1.small` and `c1.medium` instance types\)\.

  + You can override the default instance store volume mount with the following cloud\-init directive:

    ```
    #cloud-config
    mounts:
    - [ ephemeral0 ]
    ```

    For more control over mounts, see [Mounts](http://cloudinit.readthedocs.io/en/latest/topics/modules.html#mounts) in the cloud\-init documentation\.

  + Instance store volumes that support TRIM are not formatted when an instance launches, so you must partition and format them before you can mount them\. For more information, see [Instance Store Volume TRIM Support](ssd-instance-store.md#InstanceStoreTrimSupport)\. You can use the `disk_setup` module to partition and format your instance store volumes at boot\. For more information, see [Disk Setup](http://cloudinit.readthedocs.io/en/latest/topics/modules.html#disk-setup) in the cloud\-init documentation\.

### Supported User\-Data Formats<a name="supported-user-data-formats"></a>

The cloud\-init package supports user\-data handling of a variety of formats:

+ Gzip

  + If user\-data is gzip compressed, cloud\-init decompresses the data and handles it appropriately\.

+ MIME multipart

  + Using a MIME multipart file, you can specify more than one type of data\. For example, you could specify both a user\-data script and a cloud\-config type\. Each part of the multipart file can be handled by cloud\-init if it is one of the supported formats\.

+ Base64 decoding

  +  If user\-data is base64\-encoded, cloud\-init determines if it can understand the decoded data as one of the supported types\. If it understands the decoded data, it decodes the data and handles it appropriately\. If not, it returns the base64 data intact\.

+ User\-Data script

  + Begins with `#!` or `Content-Type: text/x-shellscript`\.

  + The script is executed by `/etc/init.d/cloud-init-user-scripts` during the first boot cycle\. This occurs late in the boot process \(after the initial configuration actions are performed\)\.

+ Include file

  + Begins with `#include` or `Content-Type: text/x-include-url`\.

  + This content is an include file\. The file contains a list of URLs, one per line\. Each of the URLs is read, and their content passed through this same set of rules\. The content read from the URL can be gzipped, MIME\-multi\-part, or plaintext\.

+ Cloud Config Data

  + Begins with `#cloud-config` or `Content-Type: text/cloud-config`\.

  + This content is cloud\-config data\. See the examples for a commented example of supported configuration formats\.

+ Upstart job

  + Begins with `#upstart-job` or `Content-Type: text/upstart-job`\.

  + This content is stored in a file in `/etc/init`, and upstart consumes the content as per other upstart jobs\.

+ Cloud Boothook

  + Begins with `#cloud-boothook` or `Content-Type: text/cloud-boothook`\.

  + This content is boothook data\. It is stored in a file under `/var/lib/cloud` and then executed immediately\.

  +  This is the earliest "hook" available\. There is no mechanism provided for running it only one time\. The boothook must take care of this itself\. It is provided with the instance ID in the environment variable `INSTANCE_ID`\. Use this variable to provide a once\-per\-instance set of boothook data\.

## Subscribing to Amazon Linux Notifications<a name="linux-ami-notifications"></a>

If you want to be notified when new AMIs are released, you can subscribe to these notifications using Amazon SNS\.

**To subscribe to Amazon Linux notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v2/home](https://console.aws.amazon.com/sns/v2/home)\.

1. In the navigation bar, change the region to **US East \(N\. Virginia\)**, if necessary\. You must select this region because the SNS notification that you are subscribing to was created in this region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Choose **Create subscription**\.

1. For the **Create subscription** dialog box, do the following:

   1. \[Amazon Linux 2\] For **Topic ARN**, copy and paste the following Amazon Resource Name \(ARN\): **arn:aws:sns:us\-east\-1:137112412989:amazon\-linux\-2\-ami\-updates**\.

   1. \[Amazon Linux\] For **Topic ARN**, copy and paste the following Amazon Resource Name \(ARN\): **arn:aws:sns:us\-east\-1:137112412989:amazon\-linux\-ami\-updates**\.

   1. For **Protocol**, choose **Email**\.

   1. For **Endpoint**, type an email address that you can use to receive the notifications\.

   1. Choose **Create subscription**\.

1. You'll receive a confirmation email with the subject line "AWS Notification \- Subscription Confirmation"\. Open the email and click **Confirm subscription** to complete your subscription\.

Whenever AMIs are released, we send notifications to the subscribers of the corresponding topic\. If you no longer want to receive these notifications, use the following procedure to unsubscribe\.

**To unsubscribe from Amazon Linux notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v2/home](https://console.aws.amazon.com/sns/v2/home)\.

1. In the navigation bar, change the region to **US East \(N\. Virginia\)**, if necessary\. You must use this region because the SNS notification was created in this region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Select the subscription and then choose **Actions**, **Delete subscriptions**\. When prompted for confirmation, choose **Delete**\.