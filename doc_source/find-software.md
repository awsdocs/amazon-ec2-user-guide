# Finding Software Packages on Amazon Linux<a name="find-software"></a>

You can use the yum search command to search the descriptions of packages that are available in your configured repositories\. This is especially helpful if you don't know the exact name of the package you want to install\. Simply append the keyword search to the command; for multiple word searches, wrap the search query with quotation marks\.

**Important**  
This information applies to Amazon Linux\. For information about other distributions, see their specific documentation\.

```
[ec2-user ~]$ sudo yum search "find"
```

The following is example output for Amazon Linux 2\.

```
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
============================== N/S matched: find ===============================
findutils.x86_64 : The GNU versions of find utilities (find and xargs)
gedit-plugin-findinfiles.x86_64 : gedit findinfiles plugin
ocaml-findlib-devel.x86_64 : Development files for ocaml-findlib
perl-File-Find-Rule.noarch : Perl module implementing an alternative interface to File::Find
robotfindskitten.x86_64 : A game/zen simulation. You are robot. Your job is to find kitten.
mlocate.x86_64 : An utility for finding files by name
ocaml-findlib.x86_64 : Objective CAML package manager and build helper
perl-Devel-Cycle.noarch : Find memory cycles in objects
perl-Devel-EnforceEncapsulation.noarch : Find access violations to blessed objects
perl-File-Find-Rule-Perl.noarch : Common rules for searching for Perl things
perl-File-HomeDir.noarch : Find your home and other directories on any platform
perl-IPC-Cmd.noarch : Finding and running system commands made easy
perl-Perl-MinimumVersion.noarch : Find a minimum required version of perl for Perl code
texlive-xesearch.noarch : A string finder for XeTeX
valgrind.x86_64 : Tool for finding memory management bugs in programs
valgrind.i686 : Tool for finding memory management bugs in programs
```

The following is example output for Amazon Linux\.

```
Loaded plugins: priorities, security, update-motd, upgrade-helper
============================== N/S Matched: find ===============================
findutils.x86_64 : The GNU versions of find utilities (find and xargs)
perl-File-Find-Rule.noarch : Perl module implementing an alternative interface to File::Find
perl-Module-Find.noarch : Find and use installed modules in a (sub)category
libpuzzle.i686 : Library to quickly find visually similar images (gif, png, jpg)
libpuzzle.x86_64 : Library to quickly find visually similar images (gif, png, jpg)
mlocate.x86_64 : An utility for finding files by name
```

Multiple word search queries in quotation marks only return results that match the exact query\. If you don't see the expected package, simplify your search to one keyword and then scan the results\. You can also try keyword synonyms to broaden your search\.

For more information about packages for Amazon Linux 2 and Amazon Linux, see the following:
+ [Package Repository](amazon-linux-ami-basics.md#package-repository)
+ [Extras Library \(Amazon Linux 2\)](amazon-linux-ami-basics.md#extras-library)