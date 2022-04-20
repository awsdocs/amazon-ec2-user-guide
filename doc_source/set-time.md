# Set the time for your Linux instance<a name="set-time"></a>

A consistent and accurate time reference is crucial for many server tasks and processes\. Most system logs include a time stamp that you can use to determine when problems occurred and in what order the events took place\. If you use the AWS CLI or an AWS SDK to make requests from your instance, these tools sign requests on your behalf\. If your instance's date and time are not set correctly, the date in the signature may not match the date of the request, and AWS rejects the request\. 

Amazon provides the Amazon Time Sync Service, which is accessible from all EC2 instances, and is also used by other AWS services\. This service uses a fleet of satellite\-connected and atomic reference clocks in each AWS Region to deliver accurate current time readings of the Coordinated Universal Time \(UTC\) global standard through Network Time Protocol \(NTP\)\. The Amazon Time Sync Service automatically smooths any leap seconds that are added to UTC\.

The Amazon Time Sync Service is available through NTP at the `169.254.169.123` IPv4 address or the `fd00:ec2::123` IPv6 address for any instance running in a VPC\. The IPv6 address is only accessible on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\. Your instance does not require access to the internet, and you do not have to configure your security group rules or your network ACL rules to allow access\. The latest versions of Amazon Linux 2 and Amazon Linux AMIs synchronize with the Amazon Time Sync Service by default\.

Use the following procedures to configure the Amazon Time Sync Service on your instance using the `chrony` client\. Alternatively, you can use external NTP sources\. For more information about NTP and public time sources, see [http://www\.ntp\.org/](http://www.ntp.org/)\. An instance needs access to the internet for the external NTP time sources to work\.

For Windows instances, see [Set the time for a Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-set-time.html)\.

**Topics**
+ [Configure the time for EC2 instances with IPv4 addresses](#configure-amazon-time-service-amazon-linux-IPv4)
+ [Configure the time for EC2 instances with IPv6 addresses](#configure-amazon-time-service-amazon-linux-IPv6)
+ [Change the time zone on Amazon Linux](#change_time_zone)
+ [Compare timestamps](#compare-timestamps-with-clockbound)

## Configure the time for EC2 instances with IPv4 addresses<a name="configure-amazon-time-service-amazon-linux-IPv4"></a>

This section describes how to set the time for EC2 instances with IPv4 addresses depending on the type of Linux distribution\.

**Topics**
+ [Configure the Amazon Time Sync Service on Amazon Linux AMI](#configure-amazon-time-service-amazon-linux)
+ [Configure the Amazon Time Sync Service on Ubuntu](#configure-amazon-time-service-ubuntu)
+ [Configure the Amazon Time Sync Service on SUSE Linux](#configure-amazon-time-service-suse)

### Configure the Amazon Time Sync Service on Amazon Linux AMI<a name="configure-amazon-time-service-amazon-linux"></a>

**Note**  
On Amazon Linux 2, `chrony` is already installed and configured to use the Amazon Time Sync Service IP address\.

 With the Amazon Linux AMI, you must edit the `chrony` configuration file to add a server entry for the Amazon Time Sync Service\.

**To configure your instance to use the Amazon Time Sync Service**

1. Connect to your instance and uninstall the NTP service\.

   ```
   [ec2-user ~]$ sudo yum erase 'ntp*'
   ```

1. Install the `chrony` package\.

   ```
   [ec2-user ~]$ sudo yum install chrony
   ```

1. Open the `/etc/chrony.conf` file using a text editor \(such as vim or nano\)\. Verify that the file includes the following line:

   ```
   server 169.254.169.123 prefer iburst minpoll 4 maxpoll 4
   ```

   If the line is present, then the Amazon Time Sync Service is already configured and you can go to the next step\. If not, add the line after any other `server` or `pool` statements that are already present in the file, and save your changes\.

1. Restart the `chrony` daemon \(`chronyd`\)\.

   ```
   [ec2-user ~]$ sudo service chronyd restart
   ```

   ```
   Starting chronyd:                                          [  OK  ]
   ```
**Note**  
On RHEL and CentOS \(up to version 6\), the service name is `chrony` instead of `chronyd`\.

1. Use the `chkconfig` command to configure `chronyd` to start at each system boot\.

   ```
   [ec2-user ~]$ sudo chkconfig chronyd on
   ```

1. Verify that `chrony` is using the `169.254.169.123` IP address to synchronize the time\.

   ```
   [ec2-user ~]$ chronyc sources -v
   ```

   ```
   210 Number of sources = 7
           
             .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
            / .- Source state '*' = current synced, '+' = combined , '-' = not combined,
           | /   '?' = unreachable, 'x' = time may be in error, '~' = time too variable.
           ||                                                 .- xxxx [ yyyy ] +/- zzzz
           ||      Reachability register (octal) -.           |  xxxx = adjusted offset,
           ||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
           ||                                \     |          |  zzzz = estimated error.
           ||                                 |    |           \
           MS Name/IP address         Stratum Poll Reach LastRx Last sample               
           ===============================================================================
           ^* 169.254.169.123               3   6    17    43    -30us[ -226us] +/-  287us
           ^- ec2-12-34-231-12.eu-west>     2   6    17    43   -388us[ -388us] +/-   11ms
           ^- tshirt.heanet.ie              1   6    17    44   +178us[  +25us] +/- 1959us
           ^? tbag.heanet.ie                0   6     0     -     +0ns[   +0ns] +/-    0ns
           ^? bray.walcz.net                0   6     0     -     +0ns[   +0ns] +/-    0ns
           ^? 2a05:d018:c43:e312:ce77:>     0   6     0     -     +0ns[   +0ns] +/-    0ns
           ^? 2a05:d018:dab:2701:b70:b>     0   6     0     -     +0ns[   +0ns] +/-    0ns
   ```

   In the output that's returned, `^*` indicates the preferred time source\.

1. Verify the time synchronization metrics that are reported by `chrony`\.

   ```
   [ec2-user ~]$ chronyc tracking
   ```

   ```
   Reference ID    : A9FEA97B (169.254.169.123)
           Stratum         : 4
           Ref time (UTC)  : Wed Nov 22 13:18:34 2017
           System time     : 0.000000626 seconds slow of NTP time
           Last offset     : +0.002852759 seconds
           RMS offset      : 0.002852759 seconds
           Frequency       : 1.187 ppm fast
           Residual freq   : +0.020 ppm
           Skew            : 24.388 ppm
           Root delay      : 0.000504752 seconds
           Root dispersion : 0.001112565 seconds
           Update interval : 64.4 seconds
           Leap status     : Normal
   ```

### Configure the Amazon Time Sync Service on Ubuntu<a name="configure-amazon-time-service-ubuntu"></a>

You must edit the `chrony` configuration file to add a server entry for the Amazon Time Sync Service\.

**To configure your instance to use the Amazon Time Sync Service**

1. Connect to your instance and use `apt` to install the `chrony` package\.

   ```
   ubuntu:~$ sudo apt install chrony
   ```
**Note**  
If necessary, update your instance first by running `sudo apt update`\.

1. Open the `/etc/chrony/chrony.conf` file using a text editor \(such as vim or nano\)\. Add the following line before any other `server` or `pool` statements that are already present in the file, and save your changes:

   ```
   server 169.254.169.123 prefer iburst minpoll 4 maxpoll 4
   ```

1. Restart the `chrony` service\.

   ```
   ubuntu:~$ sudo /etc/init.d/chrony restart
   ```

   ```
   Restarting chrony (via systemctl): chrony.service.
   ```

1. Verify that `chrony` is using the `169.254.169.123` IP address to synchronize the time\.

   ```
   ubuntu:~$ chronyc sources -v
   ```

   ```
   210 Number of sources = 7
               
                 .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
                / .- Source state '*' = current synced, '+' = combined , '-' = not combined,
               | /   '?' = unreachable, 'x' = time may be in error, '~' = time too variable.
               ||                                                 .- xxxx [ yyyy ] +/- zzzz
               ||      Reachability register (octal) -.           |  xxxx = adjusted offset,
               ||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
               ||                                \     |          |  zzzz = estimated error.
               ||                                 |    |           \
               MS Name/IP address         Stratum Poll Reach LastRx Last sample
               ===============================================================================
               ^* 169.254.169.123               3   6    17    12    +15us[  +57us] +/-  320us
               ^- tbag.heanet.ie                1   6    17    13  -3488us[-3446us] +/- 1779us
               ^- ec2-12-34-231-12.eu-west-     2   6    17    13   +893us[ +935us] +/- 7710us
               ^? 2a05:d018:c43:e312:ce77:6     0   6     0   10y     +0ns[   +0ns] +/-    0ns
               ^? 2a05:d018:d34:9000:d8c6:5     0   6     0   10y     +0ns[   +0ns] +/-    0ns
               ^? tshirt.heanet.ie              0   6     0   10y     +0ns[   +0ns] +/-    0ns
               ^? bray.walcz.net                0   6     0   10y     +0ns[   +0ns] +/-    0ns
   ```

   In the output that's returned, `^*` indicates the preferred time source\.

1. Verify the time synchronization metrics that are reported by `chrony`\.

   ```
   ubuntu:~$ chronyc tracking
   ```

   ```
   Reference ID    : 169.254.169.123 (169.254.169.123)
               Stratum         : 4
               Ref time (UTC)  : Wed Nov 29 07:41:57 2017
               System time     : 0.000000011 seconds slow of NTP time
               Last offset     : +0.000041659 seconds
               RMS offset      : 0.000041659 seconds
               Frequency       : 10.141 ppm slow
               Residual freq   : +7.557 ppm
               Skew            : 2.329 ppm
               Root delay      : 0.000544 seconds
               Root dispersion : 0.000631 seconds
               Update interval : 2.0 seconds
               Leap status     : Normal
   ```

### Configure the Amazon Time Sync Service on SUSE Linux<a name="configure-amazon-time-service-suse"></a>

Install chrony from [https://software\.opensuse\.org/package/chrony](https://software.opensuse.org/package/chrony)\.

Open the `/etc/chrony.conf` file using a text editor \(such as vim or nano\)\. Verify that the file contains the following line:

```
server 169.254.169.123 prefer iburst minpoll 4 maxpoll 4
```

If this line is not present, add it\. Comment out any other server or pool lines\. Open yaST and enable the chrony service\.

## Configure the time for EC2 instances with IPv6 addresses<a name="configure-amazon-time-service-amazon-linux-IPv6"></a>

This section explains how the process described in [Configure the time for EC2 instances with IPv4 addresses](#configure-amazon-time-service-amazon-linux-IPv4) differs if you are configuring Amazon Time Sync Service for EC2 instances that use an IPv6 address\. It doesn't explain the entire Amazon Time Sync Service configuration process\. The IPv6 address is only accessible on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

**Note**  
We don't recommend using both the IPv4 address and the IPv6 address entries together in your chrony\.conf file\. The IPv4 and IPv6 NTP packets come from the same local server for your instance\. You will likely get mixed results with some packets coming from the IPv4 endpoint and some from the IPv6 endpoint if you are using both at the same time\.

Depending on the Linux distribution you are using, when you reach the step to edit the chrony\.conf file, you'll be using the IPv6 endpoint of the Amazon Time Sync Service \(`fd00:ec2::123`\) rather than the IPv4 endpoint \(`169.254.169.123`\):

```
server fd00:ec2::123 prefer iburst minpoll 4 maxpoll 4
```

Save the file and verify that `chrony` is using the `fd00:ec2::123` IPv6 address to synchronize time: 

```
[ec2-user ~]$ chronyc sources -v
```

In the output, if you see the `fd00:ec2::123` IPv6 address, the configuration is complete\.

## Change the time zone on Amazon Linux<a name="change_time_zone"></a>

Amazon Linux instances are set to the UTC \(Coordinated Universal Time\) time zone by default\. You can change the time on an instance to the local time or to another time zone in your network\.

**Important**  
This information applies to Amazon Linux\. For information about other distributions, see their specific documentation\.

**To change the time zone on an Amazon Linux 2 instance**

1. View the system's current time zone setting\.

   ```
   [ec2-user ~]$ timedatectl
   ```

1. List the available time zones\.

   ```
   [ec2-user ~]$ timedatectl list-timezones
   ```

1. Set the chosen time zone\.

   ```
   [ec2-user ~]$ sudo timedatectl set-timezone America/Vancouver
   ```

**To change the time zone on an Amazon Linux instance**

1. Identify the time zone to use on the instance\. The `/usr/share/zoneinfo` directory contains a hierarchy of time zone data files\. Browse the directory structure at that location to find a file for your time zone\.

   ```
   [ec2-user ~]$ ls /usr/share/zoneinfo
   Africa      Chile    GB         Indian       Mideast   posixrules  US
   America     CST6CDT  GB-Eire    Iran         MST       PRC         UTC
   Antarctica  Cuba     GMT        iso3166.tab  MST7MDT   PST8PDT     WET
   Arctic      EET      GMT0       Israel       Navajo    right       W-SU
   ...
   ```

   Some of the entries at this location are directories \(such as `America`\), and these directories contain time zone files for specific cities\. Find your city \(or a city in your time zone\) to use for the instance\.

1. Update the `/etc/sysconfig/clock` file with the new time zone\. In this example, we use the time zone data file for Los Angeles, `/usr/share/zoneinfo/America/Los_Angeles`\.

   1. Open the `/etc/sysconfig/clock` file with your favorite text editor \(such as vim or nano\)\. You need to use sudo with your editor command because `/etc/sysconfig/clock` is owned by `root`\.

      ```
      [ec2-user ~]$ sudo nano /etc/sysconfig/clock
      ```

   1. Locate the `ZONE` entry, and change it to the time zone file \(omitting the `/usr/share/zoneinfo` section of the path\)\. For example, to change to the Los Angeles time zone, change the `ZONE` entry to the following:

      ```
      ZONE="America/Los_Angeles"
      ```
**Note**  
Do not change the `UTC=true` entry to another value\. This entry is for the hardware clock, and does not need to be adjusted when you're setting a different time zone on your instance\.

   1. Save the file and exit the text editor\.

1. Create a symbolic link between `/etc/localtime` and the time zone file so that the instance finds the time zone file when it references local time information\.

   ```
   [ec2-user ~]$ sudo ln -sf /usr/share/zoneinfo/America/Los_Angeles /etc/localtime
   ```

1. Reboot the system to pick up the new time zone information in all services and applications\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. \(Optional\) Confirm that the current time zone is updated to the new time zone by using the date command\. The current time zone appears in the output\. In the following example, the current time zone is PDT, which refers to the Los Angeles time zone\.

   ```
   [ec2-user ~]$ date
   Sun Aug 16 05:45:16 PDT 2020
   ```

## Compare timestamps<a name="compare-timestamps-with-clockbound"></a>

If you're using the Amazon Time Sync Service, you can compare the timestamps on your Amazon EC2 instances with ClockBound to determine the true time of an event\. ClockBound measures the clock accuracy of your EC2 instance, and allows you to check if a given timestamp is in the past or future with respect to your instance's current clock\. This information is valuable for determining the order and consistency of events and transactions across EC2 instances, independent of each instance's geographic location\.

ClockBound is an open source daemon and library\. To learn more about ClockBound, including installation instructions, see [ClockBound](https://github.com/aws/clock-bound) on *GitHub*\.