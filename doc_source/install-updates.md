# Updating Instance Software<a name="install-updates"></a>

It is important to keep software up\-to\-date\. Many packages in a Linux distribution are updated frequently to fix bugs, add features, and protect against security exploits\. When you first launch and connect to an Amazon Linux instance, you may see a message asking you to update software packages for security purposes\. This section shows how to update an entire system, or just a single package\.

**Important**  
These procedures are intended for use with Amazon Linux\. For more information about other distributions, see their specific documentation\.

```
       __|  __|_  )
       _|  (     /   Amazon Linux AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-ami/2013.03-release-notes/
There are 12 security update(s) out of 25 total update(s) available
Run "sudo yum update" to apply all updates.
[ec2-user ~]$
```

**To update all packages on an Amazon Linux instance**

1. \(Optional\) Start a screen session in your shell window\. Sometimes you may experience a network interruption that can disconnect the SSH connection to your instance\. If this happens during a long software update, it can leave the instance in a recoverable, although confused state\. A screen session allows you to continue running the update even if your connection is interrupted, and you can reconnect to the session later without problems\.

   1. Execute the screen command to begin the session\.

      ```
      [ec2-user ~]$ screen
      ```

   1. If your session is disconnected, log back into your instance and list the available screens\.

      ```
      [ec2-user ~]$ screen -ls
      There is a screen on:
      	17793.pts-0.ip-12-34-56-78	(Detached)
      1 Socket in /var/run/screen/S-ec2-user.
      ```

   1. Reconnect to the screen using the screen \-r command and the process ID from the previous command\.

      ```
      [ec2-user ~]$ screen -r 17793
      ```

   1. When you are finished using screen, use the exit command to close the session\.

      ```
      [ec2-user ~]$ exit
      [screen is terminating]
      ```

1. Run the yum update command\. Optionally, you can add the `--security` flag to apply only security updates\.

   ```
   [ec2-user ~]$ sudo yum update
   Loaded plugins: priorities, security, update-motd, upgrade-helper
   amzn-main                                                | 2.1 kB     00:00
   amzn-updates                                             | 2.3 kB     00:00
   Setting up Update Process
   Resolving Dependencies
   --> Running transaction check
   ---> Package aws-apitools-ec2.noarch 0:1.6.8.1-1.0.amzn1 will be updated
   ---> Package aws-apitools-ec2.noarch 0:1.6.10.0-1.0.amzn1 will be an update
   ---> Package gnupg2.x86_64 0:2.0.18-1.16.amzn1 will be updated
   ---> Package gnupg2.x86_64 0:2.0.19-8.21.amzn1 will be an update
   ---> Package libgcrypt.i686 0:1.4.5-9.10.amzn1 will be updated
   ---> Package libgcrypt.x86_64 0:1.4.5-9.10.amzn1 will be updated
   ---> Package libgcrypt.i686 0:1.4.5-9.12.amzn1 will be an update
   ---> Package libgcrypt.x86_64 0:1.4.5-9.12.amzn1 will be an update
   ---> Package openssl.x86_64 1:1.0.1e-4.53.amzn1 will be updated
   ---> Package openssl.x86_64 1:1.0.1e-4.54.amzn1 will be an update
   ---> Package python-boto.noarch 0:2.9.9-1.0.amzn1 will be updated
   ---> Package python-boto.noarch 0:2.13.3-1.0.amzn1 will be an update
   --> Finished Dependency Resolution
   
   Dependencies Resolved
   
   ================================================================================
    Package              Arch       Version                 Repository        Size
   ================================================================================
   Updating:
    aws-apitools-ec2     noarch     1.6.10.0-1.0.amzn1      amzn-updates      14 M
    gnupg2               x86_64     2.0.19-8.21.amzn1       amzn-updates     2.4 M
    libgcrypt            i686       1.4.5-9.12.amzn1        amzn-updates     248 k
    libgcrypt            x86_64     1.4.5-9.12.amzn1        amzn-updates     262 k
    openssl              x86_64     1:1.0.1e-4.54.amzn1     amzn-updates     1.7 M
    python-boto          noarch     2.13.3-1.0.amzn1        amzn-updates     1.6 M
   
   Transaction Summary
   ================================================================================
   Upgrade       6 Package(s)
   
   Total download size: 20 M
   Is this ok [y/N]:
   ```

1. Review the packages listed, type **y**, and press Enter to accept the updates\. Updating all of the packages on a system can take several minutes\. The yum output shows the status of the update while it is running\.

   ```
   Downloading Packages:
   (1/6): aws-apitools-ec2-1.6.10.0-1.0.amzn1.noarch.rpm    |  14 MB     00:00
   (2/6): gnupg2-2.0.19-8.21.amzn1.x86_64.rpm               | 2.4 MB     00:00
   (3/6): libgcrypt-1.4.5-9.12.amzn1.i686.rpm               | 248 kB     00:00
   (4/6): libgcrypt-1.4.5-9.12.amzn1.x86_64.rpm             | 262 kB     00:00
   (5/6): openssl-1.0.1e-4.54.amzn1.x86_64.rpm              | 1.7 MB     00:00
   (6/6): python-boto-2.13.3-1.0.amzn1.noarch.rpm           | 1.6 MB     00:00
   --------------------------------------------------------------------------------
   Total                                            28 MB/s |  20 MB     00:00
   Running rpm_check_debug
   Running Transaction Test
   Transaction Test Succeeded
   Running Transaction
     Updating   : libgcrypt-1.4.5-9.12.amzn1.x86_64                           1/12
     Updating   : gnupg2-2.0.19-8.21.amzn1.x86_64                             2/12
     Updating   : aws-apitools-ec2-1.6.10.0-1.0.amzn1.noarch                  3/12
     Updating   : 1:openssl-1.0.1e-4.54.amzn1.x86_64                          4/12
     ...
   
   Complete!
   ```

1. \(Optional\) Reboot your instance to ensure that you are using the latest packages and libraries from your update; kernel updates are not loaded until a reboot occurs\. Updates to any `glibc` libraries should also be followed by a reboot\. For updates to packages that control services, it may be sufficient to restart the services to pick up the updates, but a system reboot ensures that all previous package and library updates are complete\.

**To update a single package on an Amazon Linux instance**

Use this procedure to update a single package \(and its dependencies\) and not the entire system\.

1. Run the yum update command with the name of the package you would like to update\.

   ```
   [ec2-user ~]$ sudo yum update openssl
   Loaded plugins: priorities, security, update-motd, upgrade-helper
   amzn-main                                                | 2.1 kB     00:00
   amzn-updates                                             | 2.3 kB     00:00
   Setting up Update Process
   Resolving Dependencies
   --> Running transaction check
   ---> Package openssl.x86_64 1:1.0.1e-4.53.amzn1 will be updated
   ---> Package openssl.x86_64 1:1.0.1e-4.54.amzn1 will be an update
   --> Finished Dependency Resolution
   
   Dependencies Resolved
   
   ================================================================================
    Package       Arch         Version                    Repository          Size
   ================================================================================
   Updating:
    openssl       x86_64       1:1.0.1e-4.54.amzn1        amzn-updates       1.7 M
   
   Transaction Summary
   ================================================================================
   Upgrade       1 Package(s)
   
   Total download size: 1.7 M
   Is this ok [y/N]:
   ```

1. Review the package information listed, type **y**, and press Enter to accept the update or updates\. Sometimes there will be more than one package listed if there are package dependencies that must be resolved\. The yum output shows the status of the update while it is running\.

   ```
   Downloading Packages:
   openssl-1.0.1e-4.54.amzn1.x86_64.rpm                     | 1.7 MB     00:00
   Running rpm_check_debug
   Running Transaction Test
   Transaction Test Succeeded
   Running Transaction
     Updating   : 1:openssl-1.0.1e-4.54.amzn1.x86_64                           1/2
     Cleanup    : 1:openssl-1.0.1e-4.53.amzn1.x86_64                           2/2
     Verifying  : 1:openssl-1.0.1e-4.54.amzn1.x86_64                           1/2
     Verifying  : 1:openssl-1.0.1e-4.53.amzn1.x86_64                           2/2
   
   Updated:
     openssl.x86_64 1:1.0.1e-4.54.amzn1
   
   Complete!
   ```

1. \(Optional\) Reboot your instance to ensure that you are using the latest packages and libraries from your update; kernel updates are not loaded until a reboot occurs\. Updates to any `glibc` libraries should also be followed by a reboot\. For updates to packages that control services, it may be sufficient to restart the services to pick up the updates, but a system reboot ensures that all previous package and library updates are complete\.