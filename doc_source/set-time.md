# Setting the Time for Your Linux Instance<a name="set-time"></a>

A consistent and accurate time reference is crucial for many server tasks and processes\. Most system logs include a time stamp that you can use to determine when problems occur and in what order the events take place\. If you use the AWS CLI or an AWS SDK to make requests from your instance, these tools sign requests on your behalf\. If your instance's date and time are not set correctly, the date in the signature may not match the date of the request, and AWS rejects the request\. 

Amazon provides the Amazon Time Sync Service, which you can access from your instance\. This service uses a fleet of satellite\-connected and atomic reference clocks in each AWS Region to deliver accurate current time readings of the Coordinated Universal Time \(UTC\) global standard through Network Time Protocol \(NTP\)\. The Amazon Time Sync Service automatically smooths any leap seconds that are added to UTC\.

Alternatively, you can use external NTP sources\. For more information about NTP and public time sources, see [http://www\.ntp\.org/](http://www.ntp.org/)\. An instance needs access to the internet for the external NTP time sources to work\. 


+ [Configuring the Amazon Time Sync Service](#configure-amazon-time-service)
+ [Changing the Time Zone](#change_time_zone)

## Configuring the Amazon Time Sync Service<a name="configure-amazon-time-service"></a>

The Amazon Time Sync Service is available through NTP at the `169.254.169.123` IP address for any instance running in a VPC\. Your instance does not require access to the internet, and you do not have to configure your security group rules or your network ACL rules to allow access\. Use the following procedures to configure the Amazon Time Sync Service on your instance using the `chrony` client\.

**Note**  
Support for the Amazon Time Sync Service on C5 and M5 instances is coming soon\. When available, the default `chrony` configuration on these instance types will automatically start using the `169.254.169.123` time source\. Until then, ensure that your instance's security group rules allow outbound UDP traffic on port 123 \(NTP\)\.

**Important**  
On Amazon Linux 2, the default `chrony` configuration is already set up to use the Amazon Time Sync Service IP address\. For Amazon Linux, Red Hat Enterprise Linux \(RHEL\), CentOS, Fedora, and Ubuntu distributions, you must edit the `chrony` configuration file to add a server entry for the Amazon Time Sync Service\.

**To configure your Amazon Linux instance to use the Amazon Time Sync Service**

1. Connect to your instance and uninstall the NTP service\.

   ```
   [ec2-user ~]$ sudo yum erase ntp*
   ```

1. Install the `chrony` package\.

   ```
   [ec2-user ~]$ sudo yum install chrony
   ```

1. Open the `/etc/chrony.conf` file using a text editor \(such as vim or nano\)\. Verify that the file includes the following line:

   ```
   server 169.254.169.123 prefer iburst
   ```

   If the line is present, then the Amazon Time Sync Service is already configured and you can go to the next step\. If not, add the line after any other `server` or `pool` statements that are already present in the file, and save your changes\.

1. Start the `chrony` daemon \(`chronyd`\)\.

   ```
   [ec2-user ~]$ sudo service chronyd start
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

**To configure your Ubuntu or Debian derivative instance to use the Amazon Time Sync Service**

1. Connect to your instance and use `apt` to install the `chrony` package\.

   ```
   ubuntu:~$ sudo apt install chrony
   ```
**Note**  
If necessary, update your instance first by running `sudo apt update`\.

1. Open the `/etc/chrony/chrony.conf` file using a text editor \(such as vim or nano\)\. Add the following line before any other `server` or `pool` statements that are already present in the file, and save your changes:

   ```
   server 169.254.169.123 prefer iburst
   ```

1. Restart the `chrony` service\.

   ```
   ubuntu:~$ sudo /etc/init.d/chrony restart
   ```

   ```
   [ ok ] Restarting chrony (via systemctl): chrony.service.
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

## Changing the Time Zone<a name="change_time_zone"></a>

Amazon Linux instances are set to the UTC \(Coordinated Universal Time\) time zone by default, but you may wish to change the time on an instance to the local time or to another time zone in your network\.

**Important**  
These procedures are intended for use with Amazon Linux\. For more information about other distributions, see their specific documentation\.

**To change the time zone on an instance**

1. Identify the time zone to use on the instance\. The `/usr/share/zoneinfo` directory contains a hierarchy of time zone data files\. Browse the directory structure at that location to find a file for your time zone\.

   ```
   [ec2-user ~]$ ls /usr/share/zoneinfo
   Africa      Chile    GB         Indian       Mideast   posixrules  US
   America     CST6CDT  GB-Eire    Iran         MST       PRC         UTC
   Antarctica  Cuba     GMT        iso3166.tab  MST7MDT   PST8PDT     WET
   Arctic      EET      GMT0       Israel       Navajo    right       W-SU
   ...
   ```

   Some of the entries at this location are directories \(such as `America`\), and these directories contain time zone files for specific cities\. Find your city \(or a city in your time zone\) to use for the instance\. In this example, you can use the time zone file for Los Angeles, `/usr/share/zoneinfo/America/Los_Angeles`\.

1. Update the `/etc/sysconfig/clock` file with the new time zone\.

   1. Open the `/etc/sysconfig/clock` file with your favorite text editor \(such as vim or nano\)\. You need to use sudo with your editor command because `/etc/sysconfig/clock` is owned by `root`\.

   1. Locate the `ZONE` entry, and change it to the time zone file \(omitting the `/usr/share/zoneinfo` section of the path\)\. For example, to change to the Los Angeles time zone, change the `ZONE` entry to the following:

      ```
      ZONE="America/Los_Angeles"
      ```
**Note**  
Do not change the `UTC=true` entry to another value\. This entry is for the hardware clock, and does not need to be adjusted when you're setting a different time zone on your instance\.

   1. Save the file and exit the text editor\.

1. Create a symbolic link between `/etc/localtime` and your time zone file so that the instance finds the time zone file when it references local time information\.

   ```
   [ec2-user ~]$ sudo ln -sf /usr/share/zoneinfo/America/Los_Angeles /etc/localtime
   ```

1. Reboot the system to pick up the new time zone information in all services and applications\.

   ```
   [ec2-user ~]$ sudo reboot
   ```