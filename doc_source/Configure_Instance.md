# Configuring Your Amazon Linux Instance<a name="Configure_Instance"></a>

After you have successfully launched and logged into your Amazon Linux instance, you can make changes to it\. There are many different ways you can configure an instance to meet the needs of a specific application\. The following are some common tasks to help get you started\.

**Topics**
+ [Common Configuration Scenarios](#configuration-scenarios)
+ [Managing Software on Your Linux Instance](managing-software.md)
+ [Managing User Accounts on Your Linux Instance](managing-users.md)
+ [Processor State Control for Your EC2 Instance](processor_state_control.md)
+ [Setting the Time for Your Linux Instance](set-time.md)
+ [Changing the Hostname of Your Linux Instance](set-hostname.md)
+ [Setting Up Dynamic DNS on Your Linux Instance](dynamic-dns.md)
+ [Running Commands on Your Linux Instance at Launch](user-data.md)
+ [Instance Metadata and User Data](ec2-instance-metadata.md)

## Common Configuration Scenarios<a name="configuration-scenarios"></a>

The base distribution of Amazon Linux contains many software packages and utilities that are required for basic server operations\. However, many more software packages are available in various software repositories, and even more packages are available for you to build from source code\. For more information on installing and building software from these locations, see [Managing Software on Your Linux Instance](managing-software.md)\.

Amazon Linux instances come pre\-configured with an `ec2-user` account, but you may want to add other user accounts that do not have super\-user privileges\. For more information on adding and removing user accounts, see [Managing User Accounts on Your Linux Instance](managing-users.md)\.

The default time configuration for Amazon Linux instances uses Network Time Protocol to set the system time on an instance\. The default time zone is UTC\. For more information on setting the time zone for an instance or using your own time server, see [Setting the Time for Your Linux Instance](set-time.md)\.

If you have your own network with a domain name registered to it, you can change the hostname of an instance to identify itself as part of that domain\. You can also change the system prompt to show a more meaningful name without changing the hostname settings\. For more information, see [Changing the Hostname of Your Linux Instance](set-hostname.md)\. You can configure an instance to use a dynamic DNS service provider\. For more information, see [Setting Up Dynamic DNS on Your Linux Instance](dynamic-dns.md)\.

When you launch an instance in Amazon EC2, you have the option of passing user data to the instance that can be used to perform common configuration tasks and even run scripts after the instance starts\. You can pass two types of user data to Amazon EC2: cloud\-init directives and shell scripts\. For more information, see [Running Commands on Your Linux Instance at Launch](user-data.md)\.