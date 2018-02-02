# Finding Software Packages<a name="find-software"></a>

You can use the yum search command to search the descriptions of packages that are available in your configured repositories\. This is especially helpful if you don't know the exact name of the package you want to install\. Simply append the keyword search to the command; for multiple word searches, wrap the search query with quotation marks\.

**Important**  
These procedures are intended for use with Amazon Linux\. For more information about other distributions, see their specific documentation\.

Multiple word search queries in quotation marks only return results that match the exact query\. If you don't see the expected package, simplify your search to one keyword and then scan the results\. You can also try keyword synonyms to broaden your search\.

```
[ec2-user ~]$ sudo yum search "find"
Loaded plugins: priorities, security, update-motd, upgrade-helper
============================== N/S Matched: find ===============================
findutils.x86_64 : The GNU versions of find utilities (find and xargs)
perl-File-Find-Rule.noarch : Perl module implementing an alternative interface
                           : to File::Find
perl-Module-Find.noarch : Find and use installed modules in a (sub)category
libpuzzle.i686 : Library to quickly find visually similar images (gif, png, jpg)
libpuzzle.x86_64 : Library to quickly find visually similar images (gif, png,
                 : jpg)
mlocate.x86_64 : An utility for finding files by name
```

The yum package manager also combines several packages into groups that you can install with one command to perform a particular task, such as installing a web server or build tools for software compilation\. To list the groups that are already installed on your system and the available groups that you can install, use the yum grouplist command\.

```
[ec2-user ~]$ sudo yum grouplist
Loaded plugins: priorities, security, update-motd, upgrade-helper
Setting up Group Process
Installed Groups:
   Development Libraries
   Development tools
   Editors
   Legacy UNIX compatibility
   Mail Server
   MySQL Database
   Network Servers
   Networking Tools
   PHP Support
   Perl Support
   System Tools
   Web Server
Available Groups:
   Console internet tools
   DNS Name Server
   FTP Server
   Java Development
   MySQL Database client
   NFS file server
   Performance Tools
   PostgreSQL Database client (version 8)
   PostgreSQL Database server (version 8)
   Scientific support
   TeX support
   Technical Writing
   Web Servlet Engine
Done
```

You can see the different packages in a group by using the yum groupinfo "*Group Name*" command, replacing *Group Name* with the name of the group to get information about\. This command lists all of the mandatory, default, and optional packages that can be installed with that group\.

If you cannot find the software you need in the default `amzn-main` and `amzn-updates` repositories, you can add more repositories, such as the Extra Packages for Enterprise Linux \(EPEL\) repository\. For more information, see [Adding Repositories](add-repositories.md)\.