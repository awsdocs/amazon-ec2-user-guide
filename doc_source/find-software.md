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