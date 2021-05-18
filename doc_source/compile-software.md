# Prepare to compile software on an Amazon Linux instance<a name="compile-software"></a>

There is a wealth of open\-source software available on the internet that has not been pre\-compiled and made available for download from a package repository\. You might eventually discover a software package that you need to compile yourself, from its source code\. For your system to be able to compile software, you need to install several development tools, such as make, gcc, and autoconf\.

**Important**  
This information applies to Amazon Linux\. For information about other distributions, see their specific documentation\.

Because software compilation is not a task that every Amazon EC2 instance requires, these tools are not installed by default, but they are available in a package group called "Development Tools" that is easily added to an instance with the yum groupinstall command\.

```
[ec2-user ~]$ sudo yum groupinstall "Development Tools"
```

Software source code packages are often available for download \(from websites such as [https://github\.com/](https://github.com/) and [http://sourceforge\.net/](https://sourceforge.net/)\) as a compressed archive file, called a tarball\. These tarballs will usually have the `.tar.gz` file extension\. You can decompress these archives with the tar command\.

```
[ec2-user ~]$ tar -xzf software.tar.gz
```

After you have decompressed and unarchived the source code package, you should look for a `README` or `INSTALL` file in the source code directory that can provide you with further instructions for compiling and installing the source code\. 

**To retrieve source code for Amazon Linux packages**

Amazon Web Services provides the source code for maintained packages\. You can download the source code for any installed packages with the yumdownloader \-\-source command\.
+ Run the yumdownloader \-\-source *package* command to download the source code for *package*\. For example, to download the source code for the `htop` package, enter the following command\.

  ```
  [ec2-user ~]$ yumdownloader --source htop
  
  Loaded plugins: priorities, update-motd, upgrade-helper
  Enabling amzn-updates-source repository
  Enabling amzn-main-source repository
  amzn-main-source                                                                                              | 1.9 kB  00:00:00     
  amzn-updates-source                                                                                           | 1.9 kB  00:00:00     
  (1/2): amzn-updates-source/latest/primary_db                                                                  |  52 kB  00:00:00     
  (2/2): amzn-main-source/latest/primary_db                                                                     | 734 kB  00:00:00     
  htop-1.0.1-2.3.amzn1.src.rpm
  ```

  The location of the source RPM is in the directory from which you ran the command\.