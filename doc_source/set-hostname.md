# Changing the Hostname of Your Linux Instance<a name="set-hostname"></a>

When you launch an instance, it is assigned a hostname that is a form of the private, internal IPv4 address\. A typical Amazon EC2 private DNS name looks something like this: `ip-12-34-56-78.us-west-2.compute.internal`, where the name consists of the internal domain, the service \(in this case, `compute`\), the region, and a form of the private IPv4 address\. Part of this hostname is displayed at the shell prompt when you log into your instance \(for example, `ip-12-34-56-78`\)\. Each time you stop and restart your Amazon EC2 instance \(unless you are using an Elastic IP address\), the public IPv4 address changes, and so does your public DNS name, system hostname, and shell prompt\.

**Important**  
These procedures are intended for use with Amazon Linux\. For more information about other distributions, see their specific documentation\.

## Changing the System Hostname<a name="set-hostname-system"></a>

If you have a public DNS name registered for the IP address of your instance \(such as `webserver.mydomain.com`\), you can set the system hostname so your instance identifies itself as a part of that domain\. This also changes the shell prompt so that it displays the first portion of this name instead of the hostname supplied by AWS \(for example, `ip-12-34-56-78`\)\. If you do not have a public DNS name registered, you can still change the hostname, but the process is a little different\.

**To change the system hostname to a public DNS name**

Follow this procedure if you already have a public DNS name registered\.

1. 
   + For Amazon Linux 2: Use the hostnamectl command to set your hostname to reflect the fully qualified domain name \(such as **webserver\.mydomain\.com**\)\.

     ```
     [ec2-user ~]$ sudo hostnamectl set-hostname webserver.mydomain.com
     ```
   + For Amazon Linux AMI: On your instance, open the `/etc/sysconfig/network` configuration file in your favorite text editor and change the `HOSTNAME` entry to reflect the fully qualified domain name \(such as **webserver\.mydomain\.com**\)\.

     ```
     HOSTNAME=webserver.mydomain.com
     ```

1. Reboot the instance to pick up the new hostname\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

   Alternatively, you can reboot using the Amazon EC2 console \(on the **Instances** page, choose **Actions**, **Instance State**, **Reboot**\)\.

1. Log into your instance and verify that the hostname has been updated\. Your prompt should show the new hostname \(up to the first "\."\) and the hostname command should show the fully\-qualified domain name\.

   ```
   [ec2-user@webserver ~]$ hostname
   webserver.mydomain.com
   ```

**To change the system hostname without a public DNS name**

1. 
   + For Amazon Linux 2: Use the hostnamectl command to set your hostname to reflect the desired system hostname \(such as **webserver**\)\.

     ```
     [ec2-user ~]$ sudo hostnamectl set-hostname webserver.localdomain
     ```
   + For Amazon Linux AMI: On your instance, open the `/etc/sysconfig/network` configuration file in your favorite text editor and change the `HOSTNAME` entry to reflect the desired system hostname \(such as **webserver**\)\.

     ```
     HOSTNAME=webserver.localdomain
     ```

   Open the `/etc/sysconfig/network` configuration file in your favorite text editor and change the `HOSTNAME` entry to reflect the desired system hostname \(such as **webserver**\)\.

   ```
   HOSTNAME=webserver.localdomain
   ```

1. Open the `/etc/hosts` file in your favorite text editor and change the entry beginning with **127\.0\.0\.1** to match the example below, substituting your own hostname\.

   ```
   127.0.0.1 webserver.localdomain webserver localhost4 localhost4.localdomain4
   ```

1. Reboot the instance to pick up the new hostname\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

   Alternatively, you can reboot using the Amazon EC2 console \(on the **Instances** page, choose **Actions**, **Instance State**, **Reboot**\)\.

1. Log into your instance and verify that the hostname has been updated\. Your prompt should show the new hostname \(up to the first "\."\) and the hostname command should show the fully\-qualified domain name\.

   ```
   [ec2-user@webserver ~]$ hostname
   webserver.localdomain
   ```

## Changing the Shell Prompt Without Affecting the Hostname<a name="set-hostname-shell"></a>

If you do not want to modify the hostname for your instance, but you would like to have a more useful system name \(such as **webserver**\) displayed than the private name supplied by AWS \(for example, `ip-12-34-56-78`\), you can edit the shell prompt configuration files to display your system nickname instead of the hostname\.

**To change the shell prompt to a host nickname**

1. Create a file in `/etc/profile.d` that sets the environment variable called `NICKNAME` to the value you want in the shell prompt\. For example, to set the system nickname to **webserver**, run the following command\.

   ```
   [ec2-user ~]$ sudo sh -c 'echo "export NICKNAME=webserver" > /etc/profile.d/prompt.sh'
   ```

1. Open the `/etc/bashrc` file in your favorite text editor \(such as vim or nano\)\. You need to use sudo with the editor command because `/etc/bashrc` is owned by `root`\.

1. Edit the file and change the shell prompt variable \(`PS1`\) to display your nickname instead of the hostname\. Find the following line that sets the shell prompt in `/etc/bashrc` \(several surrounding lines are shown below for context; look for the line that starts with `[ "$PS1"`\):

   ```
     # Turn on checkwinsize
     shopt -s checkwinsize
     [ "$PS1" = "\\s-\\v\\\$ " ] && PS1="[\u@\h \W]\\$ "
     # You might want to have e.g. tty in prompt (e.g. more virtual machines)
     # and console windows
   ```

   Change the `\h` \(the symbol for `hostname`\) in that line to the value of the `NICKNAME` variable\.

   ```
     # Turn on checkwinsize
     shopt -s checkwinsize
     [ "$PS1" = "\\s-\\v\\\$ " ] && PS1="[\u@$NICKNAME \W]\\$ "
     # You might want to have e.g. tty in prompt (e.g. more virtual machines)
     # and console windows
   ```

1. \(Optional\) To set the title on shell windows to the new nickname, complete the following steps\.

   1. Create a file named `/etc/sysconfig/bash-prompt-xterm`\.

      ```
      [ec2-user ~]$ sudo touch /etc/sysconfig/bash-prompt-xterm
      ```

   1. Make the file executable using the following command\.

      ```
      [ec2-user ~]$ sudo chmod +x /etc/sysconfig/bash-prompt-xterm
      ```

   1. Open the `/etc/sysconfig/bash-prompt-xterm` file in your favorite text editor \(such as vim or nano\)\. You need to use sudo with the editor command because `/etc/sysconfig/bash-prompt-xterm` is owned by `root`\.

   1. Add the following line to the file\.

      ```
      echo -ne "\033]0;${USER}@${NICKNAME}:${PWD/#$HOME/~}\007"
      ```

1. Log out and then log back in to pick up the new nickname value\.

## Changing the Hostname on Other Linux Distributions<a name="set-hostname-other-linux"></a>

The procedures on this page are intended for use with Amazon Linux only\. For more information about other Linux distributions, see their specific documentation and the following articles:
+ [How do I assign a static hostname to a private Amazon EC2 instance running RHEL 7 or Centos 7?](https://aws.amazon.com/premiumsupport/knowledge-center/linux-static-hostname-rhel7-centos7/)