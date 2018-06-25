# Installing Software Packages<a name="install-software"></a>

The yum package manager is a great tool for installing software, because it can search all of your enabled repositories for different software packages and also handle any dependencies in the software installation process\.

**Important**  
These procedures are intended for use with Amazon Linux\. For more information about other distributions, see their specific documentation\.

To install a package from a repository, use the yum install *package* command, replacing *package* with the name of the software to install\. For example, to install the links text\-based web browser, enter the following command\.

```
[ec2-user ~]$ sudo yum install links
```

You can also use yum install to install RPM package files that you have downloaded from the Internet\. To do this, simply append the path name of an RPM file to the installation command instead of a repository package name\.

```
[ec2-user ~]$ sudo yum install my-package.rpm
```