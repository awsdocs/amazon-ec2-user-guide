# Running Commands on Your Linux Instance at Launch<a name="user-data"></a>

When you launch an instance in Amazon EC2, you have the option of passing user data to the instance that can be used to perform common automated configuration tasks and even run scripts after the instance starts\. You can pass two types of user data to Amazon EC2: shell scripts and cloud\-init directives\. You can also pass this data into the launch wizard as plain text, as a file \(this is useful for launching instances using the command line tools\), or as base64\-encoded text \(for API calls\)\.

If you are interested in more complex automation scenarios, consider using AWS CloudFormation and AWS OpsWorks\. For more information, see the [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/) and the [AWS OpsWorks User Guide](https://docs.aws.amazon.com/opsworks/latest/userguide/)\.

For information about running commands on your Windows instance at launch, see [Running Commands on Your Windows Instance at Launch](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-user-data.html) and [Managing Windows Instance Configuration](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-configuration-manage.html) in the *Amazon EC2 User Guide for Windows Instances*\.

In the following examples, the commands from the [Install a LAMP Web Server on Amazon Linux 2](ec2-lamp-amazon-linux-2.md) are converted to a shell script and a set of cloud\-init directives that executes when the instance launches\. In each example, the following tasks are executed by the user data:
+ The distribution software packages are updated\.
+ The necessary web server, `php`, and `mariadb` packages are installed\.
+ The `httpd` service is started and turned on via systemctl\.
+ The `ec2-user` is added to the apache group\.
+ The appropriate ownership and file permissions are set for the web directory and the files contained within it\.
+ A simple web page is created to test the web server and PHP engine\.

By default, user data scripts and cloud\-init directives run only during the first boot cycle when an instance is launched\. However, you can configure your user data scripts and cloud\-init directives to run every time the instance is restarted from a stopped state\. For more information, see [How can I execute user data after the initial launch of my EC2 instance?](https://aws.amazon.com/premiumsupport/knowledge-center/execute-user-data-ec2/) in the AWS Knowledge Center\.

**Topics**
+ [Prerequisites](#user-data-requirements)
+ [User Data and Shell Scripts](#user-data-shell-scripts)
+ [User Data and the Console](#user-data-console)
+ [User Data and cloud\-init Directives](#user-data-cloud-init)
+ [User Data and the AWS CLI](#user-data-api-cli)

## Prerequisites<a name="user-data-requirements"></a>

The following examples assume that your instance has a public DNS name that is reachable from the Internet\. For more information, see [Step 1: Launch an Instance](EC2_GetStarted.md#ec2-launch-instance)\. You must also configure your security group to allow SSH \(port 22\), HTTP \(port 80\), and HTTPS \(port 443\) connections\. For more information about these prerequisites, see [Setting Up with Amazon EC2](get-set-up-for-amazon-ec2.md)\.

Also, these instructions are intended for use with Amazon Linux 2, and the commands and directives may not work for other Linux distributions\. For more information about other distributions, such as their support for cloud\-init, see their specific documentation\.

## User Data and Shell Scripts<a name="user-data-shell-scripts"></a>

If you are familiar with shell scripting, this is the easiest and most complete way to send instructions to an instance at launch, and the cloud\-init output log file \(`/var/log/cloud-init-output.log`\) captures console output so it is easy to debug your scripts following a launch if the instance does not behave the way you intended\.

**Important**  
By default, user data scripts and cloud\-init directives run only during the first boot cycle when an instance is launched\. However, you can configure your user data scripts and cloud\-init directives to run every time the instance is restarted from a stopped state\. For more information, see [How can I execute user data after the initial launch of my EC2 instance?](https://aws.amazon.com/premiumsupport/knowledge-center/execute-user-data-ec2/) in the AWS Knowledge Center\.

User data shell scripts must start with the `#!` characters and the path to the interpreter you want to read the script \(commonly /bin/bash\)\. For a great introduction on shell scripting, see [the BASH Programming HOW\-TO](http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO.html) at the Linux Documentation Project \([tldp\.org](http://tldp.org)\)\.

Scripts entered as user data are executed as the `root` user, so do not use the sudo command in the script\. Remember that any files you create will be owned by `root`; if you need non\-root users to have file access, you should modify the permissions accordingly in the script\. Also, because the script is not run interactively, you cannot include commands that require user feedback \(such as yum update without the `-y` flag\)\.

Adding these tasks at boot time adds to the amount of time it takes to boot the instance\. You should allow a few minutes of extra time for the tasks to complete before you test that the user script has finished successfully\.

## User Data and the Console<a name="user-data-console"></a>

You can specify instance user data when you launch the instance\. If the root volume of the instance is an EBS volume, you can also stop the instance and update its user data\.

### Specify Instance User Data at Launch<a name="user-data-launch-instance-wizard"></a>

Follow the procedure for launching an instance at [Launching Your Instance from an AMI](launching-instance.md#launch-instance-console), but when you get to [Step 6](launching-instance.md#configure_instance_details_step) in that procedure, copy your shell script in the **User data** field, and then complete the launch procedure\.

In the example script below, the script creates and configures our web server\.

```
#!/bin/bash
yum update -y
amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
yum install -y httpd mariadb-server
systemctl start httpd
systemctl enable httpd
usermod -a -G apache ec2-user
chown -R ec2-user:apache /var/www
chmod 2775 /var/www
find /var/www -type d -exec chmod 2775 {} \;
find /var/www -type f -exec chmod 0664 {} \;
echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php
```

Allow enough time for the instance to launch and execute the commands in your script, and then check to see that your script has completed the tasks that you intended\.

For our example, in a web browser, enter the URL of the PHP test file the script created\. This URL is the public DNS address of your instance followed by a forward slash and the file name\.

```
http://my.public.dns.amazonaws.com/phpinfo.php
```

You should see the PHP information page\. If you are unable to see the PHP information page, check that the security group you are using contains a rule to allow HTTP \(port 80\) traffic\. For more information, see [Adding Rules to a Security Group](using-network-security.md#adding-security-group-rule)\.

\(Optional\) If your script did not accomplish the tasks you were expecting it to, or if you just want to verify that your script completed without errors, examine the cloud\-init output log file at `/var/log/cloud-init-output.log` and look for error messages in the output\. 

For additional debugging information, you can create a Mime multipart archive that includes a cloud\-init data section with the following directive:

```
output : { all : '| tee -a /var/log/cloud-init-output.log' }
```

This directive sends command output from your script to `/var/log/cloud-init-output.log`\. For more information about cloud\-init data formats and creating Mime multi part archive, see [cloud\-init Formats](http://cloudinit.readthedocs.org/en/latest/topics/format.html)\.

### View and Update the Instance User Data<a name="user-data-view-change"></a>

**To modify instance user data**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **Instance State**, **Stop**\.
**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. Therefore, if you have any data on instance store volumes that you want to keep, be sure to back it up to persistent storage\.

1. When prompted for confirmation, choose **Yes, Stop**\. It can take a few minutes for the instance to stop\.

1. With the instance still selected, choose **Actions**, **Instance Settings**, **View/Change User Data**\. You can't change the user data if the instance is running, but you can view it\.

1. In the **View/Change User Data** dialog box, update the user data, and then choose **Save**\.

1. Restart the instance\. The new user data is visible on your instance after you restart it; however, user data scripts are not executed\.

## User Data and cloud\-init Directives<a name="user-data-cloud-init"></a>

The cloud\-init package configures specific aspects of a new Amazon Linux instance when it is launched; most notably, it configures the `.ssh/authorized_keys` file for the ec2\-user so you can log in with your own private key\. For more information, see [cloud\-init](amazon-linux-ami-basics.md#amazon-linux-cloud-init)\.

The cloud\-init user directives can be passed to an instance at launch the same way that a script is passed, although the syntax is different\. For more information about cloud\-init, go to [http://cloudinit\.readthedocs\.org/en/latest/index\.html](http://cloudinit.readthedocs.org/en/latest/index.html)\.

**Important**  
By default, user data scripts and cloud\-init directives run only during the first boot cycle when an instance is launched\. However, you can configure your user data scripts and cloud\-init directives to run every time the instance is restarted from a stopped state\. For more information, see [How can I execute user data after the initial launch of my EC2 instance?](https://aws.amazon.com/premiumsupport/knowledge-center/execute-user-data-ec2/) in the AWS Knowledge Center\.

The Amazon Linux version of cloud\-init does not support all of the directives that are available in the base package, and some of the directives have been renamed \(such as `repo_update` instead of `apt-upgrade`\)\.

Adding these tasks at boot time adds to the amount of time it takes to boot an instance\. You should allow a few minutes of extra time for the tasks to complete before you test that your user data directives have completed\.

**To pass cloud\-init directives to an instance with user data**

1. Follow the procedure for launching an instance at [Launching Your Instance from an AMI](launching-instance.md#launch-instance-console), but when you get to [Step 6](launching-instance.md#configure_instance_details_step) in that procedure, enter your cloud\-init directive text in the **User data** field, and then complete the launch procedure\.

   In the example below, the directives create and configure a web server on Amazon Linux 2\. The `#cloud-config` line at the top is required in order to identify the commands as cloud\-init directives\.

   ```
   #cloud-config
   repo_update: true
   repo_upgrade: all
   
   packages:
    - httpd
    - mariadb-server
   
   runcmd:
    - [ sh, -c, "amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2" ]
    - systemctl start httpd
    - sudo systemctl enable httpd
    - [ sh, -c, "usermod -a -G apache ec2-user" ]
    - [ sh, -c, "chown -R ec2-user:apache /var/www" ]
    - chmod 2775 /var/www
    - [ find, /var/www, -type, d, -exec, chmod, 2775, {}, \; ]
    - [ find, /var/www, -type, f, -exec, chmod, 0664, {}, \; ]
    - [ sh, -c, 'echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php' ]
   ```

1. Allow enough time for the instance to launch and execute the directives in your user data, and then check to see that your directives have completed the tasks you intended\.

   For our example, in a web browser, enter the URL of the PHP test file the directives created\. This URL is the public DNS address of your instance followed by a forward slash and the file name\.

   ```
   http://my.public.dns.amazonaws.com/phpinfo.php
   ```

   You should see the PHP information page\. If you are unable to see the PHP information page, check that the security group you are using contains a rule to allow HTTP \(port 80\) traffic\. For more information, see [Adding Rules to a Security Group](using-network-security.md#adding-security-group-rule)\.

1. \(Optional\) If your directives did not accomplish the tasks you were expecting them to, or if you just want to verify that your directives completed without errors, examine the output log file at `/var/log/cloud-init-output.log` and look for error messages in the output\. For additional debugging information, you can add the following line to your directives:

   ```
   output : { all : '| tee -a /var/log/cloud-init-output.log' }
   ```

   This directive sends runcmd output to `/var/log/cloud-init-output.log`\.

## User Data and the AWS CLI<a name="user-data-api-cli"></a>

You can use the AWS CLI to specify, modify, and view the user data for your instance\. For information about viewing user data from your instance using instance metadata, see [Retrieve Instance User Data](ec2-instance-metadata.md#instancedata-user-data-retrieval)\.

On Windows, you can use the AWS Tools for Windows PowerShell instead of using the AWS CLI\. For more information, see [User Data and the Tools for Windows PowerShell](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-user-data.html#user-data-powershell) in the *Amazon EC2 User Guide for Windows Instances*\.

**Example: Specify User Data at Launch**  
To specify user data when you launch your instance, use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command with the `--user-data` parameter\. With run\-instances, the AWS CLI performs base64 encoding of the user data for you\.

The following example shows how to specify a script as a string on the command line:

```
aws ec2 run-instances --image-id ami-abcd1234 --count 1 --instance-type m3.medium \
--key-name my-key-pair --subnet-id subnet-abcd1234 --security-group-ids sg-abcd1234 \
--user-data echo user data
```

The following example shows how to specify a script using a text file\. Be sure to use the `file://` prefix to specify the file\.

```
aws ec2 run-instances --image-id ami-abcd1234 --count 1 --instance-type m3.medium \
--key-name my-key-pair --subnet-id subnet-abcd1234 --security-group-ids sg-abcd1234 \
--user-data file://my_script.txt
```

The following is an example text file with a shell script\.

```
#!/bin/bash
yum update -y
service httpd start
chkconfig httpd on
```

**Example: Modify the User Data of a Stopped Instance**  
You can modify the user data of a stopped instance using the [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) command\. With modify\-instance\-attribute, the AWS CLI does not perform base64 encoding of the user data for you\.

On Linux, use the base64 command to encode the user data\.

```
base64 my_script.txt >my_script_base64.txt
```

On Windows, use the certutil command to encode the user data\. Before you can use this file with the AWS CLI, you must remove the first \(BEGIN CERTIFICATE\) and last \(END CERTIFICATE\) lines\.

```
certutil -encode my_script.txt my_script_base64.txt
notepad my_script_base64.txt
```

Use the `--user-data` and `--value` parameters to use the encoded text file to specify the user data\. Be sure to use the `file://` prefix to specify the file\.

```
aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 --attribute userData --value file://my_script_base64.txt
```

**Example: View User Data**  
To retrieve the user data for an instance, use the [describe\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-attribute.html) command\. With describe\-instance\-attribute, the AWS CLI does not perform base64 decoding of the user data for you\.

```
aws ec2 describe-instance-attribute --instance-id i-1234567890abcdef0 --attribute userData
```

The following is example output with the user data base64 encoded\.

```
{
    "UserData": {
        "Value": "IyEvYmluL2Jhc2gKeXVtIHVwZGF0ZSAteQpzZXJ2aWNlIGh0dHBkIHN0YXJ0CmNoa2NvbmZpZyBodHRwZCBvbg=="
    },
    "InstanceId": "i-1234567890abcdef0"
}
```

On Linux, use the `--query` option to get the encoded user data and the base64 command to decode it\.

```
aws ec2 describe-instance-attribute --instance-id i-1234567890abcdef0 --attribute userData --output text --query "UserData.Value" | base64 --decode
```

On Windows, use the `--query` option to get the coded user data and the certutil command to decode it\. Note that the encoded output is stored in a file and the decoded output is stored in another file\.

```
aws ec2 describe-instance-attribute --instance-id i-1234567890abcdef0 --attribute userData --output text --query "UserData.Value" >my_output.txt
certutil -decode my_output.txt my_output_decoded.txt
type my_output_decoded.txt
```

The following is example output\.

```
#!/bin/bash
yum update -y
service httpd start
chkconfig httpd on
```