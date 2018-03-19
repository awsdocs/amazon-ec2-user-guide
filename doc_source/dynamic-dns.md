# Setting Up Dynamic DNS on Your Linux Instance<a name="dynamic-dns"></a>

When you launch an EC2 instance, it is assigned a public IP address and a public DNS \(Domain Name System\) name that you can use to reach it from the Internet\. Because there are so many hosts in the Amazon Web Services domain, these public names must be quite long for each name to remain unique\. A typical Amazon EC2 public DNS name looks something like this: `ec2-12-34-56-78.us-west-2.compute.amazonaws.com`, where the name consists of the Amazon Web Services domain, the service \(in this case, `compute`\), the region, and a form of the public IP address\.

Dynamic DNS services provide custom DNS host names within their domain area that can be easy to remember and that can also be more relevant to your host's use case; some of these services are also free of charge\. You can use a dynamic DNS provider with Amazon EC2 and configure the instance to update the IP address associated with a public DNS name each time the instance starts\. There are many different providers to choose from, and the specific details of choosing a provider and registering a name with them are outside the scope of this guide\.

**Important**  
These procedures are intended for use with Amazon Linux\. For more information about other distributions, see their specific documentation\.<a name="procedure-dynamic-dns"></a>

**To use dynamic DNS with Amazon EC2**

1. Sign up with a dynamic DNS service provider and register a public DNS name with their service\. This procedure uses the free service from [noip\.com/free](https://www.noip.com/free) as an example\.

1. Configure the dynamic DNS update client\. After you have a dynamic DNS service provider and a public DNS name registered with their service, point the DNS name to the IP address for your instance\. Many providers \(including [noip\.com](https://noip.com)\) allow you to do this manually from your account page on their website, but many also support software update clients\. If an update client is running on your EC2 instance, your dynamic DNS record is updated each time the IP address changes, as after a shutdown and restart\. In this example, you install the noip2 client, which works with the service provided by [noip\.com](https://noip.com)\.

   1. Enable the Extra Packages for Enterprise Linux \(EPEL\) repository to gain access to the noip2 client\.
**Note**  
Amazon Linux instances have the GPG keys and repository information for the EPEL repository installed by default; however, Red Hat and CentOS instances must first install the `epel-release` package before you can enable the EPEL repository\. For more information and to download the latest version of this package, see [https://fedoraproject\.org/wiki/EPEL](https://fedoraproject.org/wiki/EPEL)\.

      ```
      [ec2-user ~]$ sudo yum-config-manager --enable epel
      ```

   1. Install the `noip` package\.

      ```
      [ec2-user ~]$ sudo yum install -y noip
      ```

   1. Create the configuration file\. Enter the login and password information when prompted and answer the subsequent questions to configure the client\.

      ```
      [ec2-user ~]$ sudo noip2 -C
      ```

1. Enable the noip service with the chkconfig command\.

   ```
   [ec2-user ~]$ sudo chkconfig noip on
   ```

   You can verify that the service is enabled with the chkconfig \-\-list command\.

   ```
   [ec2-user ~]$ chkconfig --list noip
   noip           	0:off	1:off	2:on	3:on	4:on	5:on	6:off
   ```

   Here, noip is `on` in runlevels 2, 3, 4, and 5 \(which is correct\)\. Now the update client starts at every boot and updates the public DNS record to point to the IP address of the instance\.

1. Start the noip service\.

   ```
   [ec2-user ~]$ sudo service noip start
   Starting noip2:                                            [  OK  ]
   ```

   This command starts the client, which reads the configuration file \(`/etc/no-ip2.conf`\) that you created earlier and updates the IP address for the public DNS name that you chose\.

1. Verify that the update client has set the correct IP address for your dynamic DNS name\. Allow a few minutes for the DNS records to update, and then try to connect to your instance using SSH with the public DNS name that you configured in this procedure\.