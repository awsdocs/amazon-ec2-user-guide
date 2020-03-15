# Adding Repositories<a name="add-repositories"></a>

By default, Amazon Linux instances launch with two repositories enabled: `amzn-main` and `amzn-updates`\. While there are many packages available in these repositories that are updated by Amazon Web Services, there may be a package that you wish to install that is contained in another repository\.

**Important**  
This information applies to Amazon Linux\. For information about other distributions, see their specific documentation\.

To install a package from a different repository with yum, you need to add the repository information to the `/etc/yum.conf` file or to its own `repository.repo` file in the `/etc/yum.repos.d` directory\. You can do this manually, but most yum repositories provide their own `repository.repo` file at their repository URL\.

**To determine what yum repositories are already installed**
+ List the installed yum repositories with the following command:

  ```
  [ec2-user ~]$ yum repolist all
  ```

  The resulting output lists the installed repositories and reports the status of each\. Enabled repositories display the number of packages they contain\.

**To add a yum repository to `/etc/yum.repos.d`**

1. Find the location of the `.repo` file\. This will vary depending on the repository you are adding\. In this example, the `.repo` file is at `https://www.example.com/repository.repo`\.

1. Add the repository with the yum\-config\-manager command\.

   ```
   [ec2-user ~]$ sudo yum-config-manager --add-repo https://www.example.com/repository.repo
   Loaded plugins: priorities, update-motd, upgrade-helper
   adding repo from: https://www.example.com/repository.repo
   grabbing file https://www.example.com/repository.repo to /etc/yum.repos.d/repository.repo
   repository.repo                                      | 4.0 kB     00:00
   repo saved to /etc/yum.repos.d/repository.repo
   ```

After you install a repository, you must enable it as described in the next procedure\.

**To enable a yum repository in `/etc/yum.repos.d`**
+ Use the yum\-config\-manager command with the `--enable repository` flag\. The following command enables the Extra Packages for Enterprise Linux \(EPEL\) repository from the Fedora project\. By default, this repository is present in `/etc/yum.repos.d` on Amazon Linux AMI instances, but it is not enabled\.

  ```
  [ec2-user ~]$ sudo yum-config-manager --enable epel
  ```
**Note**  
To enable the EPEL repository on Amazon Linux 2, use the following command:  

  ```
  [ec2-user ~]$ sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
  ```
For information on enabling the EPEL repository on other distributions, such as Red Hat and CentOS, see the EPEL documentation at [https://fedoraproject\.org/wiki/EPEL](https://fedoraproject.org/wiki/EPEL)\.