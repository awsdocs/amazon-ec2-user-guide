# Configure your Amazon Linux instance<a name="Configure_Instance"></a>

After you have successfully launched and logged into your Amazon Linux instance, you can make changes to it\. There are many different ways you can configure an instance to meet the needs of a specific application\. The following are some common tasks to help get you started\.

**Topics**
+ [Common configuration scenarios](#configuration-scenarios)
+ [Manage software on your Amazon Linux instance](managing-software.md)
+ [Manage user accounts on your Linux instance](managing-users.md)
+ [Processor state control for your EC2 instance](processor_state_control.md)
+ [I/O scheduler](io-scheduler.md)
+ [Set the time for your Linux instance](set-time.md)
+ [Optimize CPU options](instance-optimize-cpu.md)
+ [Change the hostname of your Amazon Linux instance](set-hostname.md)
+ [Set up dynamic DNS on Your Amazon Linux instance](dynamic-dns.md)
+ [Run commands on your Linux instance at launch](user-data.md)
+ [Instance metadata and user data](ec2-instance-metadata.md)

## Common configuration scenarios<a name="configuration-scenarios"></a>

The base distribution of Amazon Linux contains many software packages and utilities that are required for basic server operations\. However, many more software packages are available in various software repositories, and even more packages are available for you to build from source code\. For more information on installing and building software from these locations, see [Manage software on your Amazon Linux instance](managing-software.md)\.

Amazon Linux instances come pre\-configured with an `ec2-user` account, but you may want to add other user accounts that do not have super\-user privileges\. For more information on adding and removing user accounts, see [Manage user accounts on your Linux instance](managing-users.md)\.

The default time configuration for Amazon Linux instances uses Amazon Time Sync Service to set the system time on an instance\. The default time zone is UTC\. For more information on setting the time zone for an instance or using your own time server, see [Set the time for your Linux instance](set-time.md)\.

If you have your own network with a domain name registered to it, you can change the hostname of an instance to identify itself as part of that domain\. You can also change the system prompt to show a more meaningful name without changing the hostname settings\. For more information, see [Change the hostname of your Amazon Linux instance](set-hostname.md)\. You can configure an instance to use a dynamic DNS service provider\. For more information, see [Set up dynamic DNS on Your Amazon Linux instance](dynamic-dns.md)\.

When you launch an instance in Amazon EC2, you have the option of passing user data to the instance that can be used to perform common configuration tasks and even run scripts after the instance starts\. You can pass two types of user data to Amazon EC2: cloud\-init directives and shell scripts\. For more information, see [Run commands on your Linux instance at launch](user-data.md)\.