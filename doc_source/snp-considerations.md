# Considerations<a name="snp-considerations"></a>

Keep the following in mind when working with AMD SEV\-SNP\.
+ AMD SEV\-SNP can only be turned on when you launch an instance\. When AMD SEV\-SNP is turned on during launch, it will remain turned on throughout the instance's lifecycle\. 
+ You can [change the instance type](ec2-instance-resize.md) for an instance that has AMD SEV\-SNP turned on only to another instance type that supports AMD SEV\-SNP\.
+ Hibernation and Nitro Enclaves are not supported when AMD SEV\-SNP is turned on\.
+ Dedicated Hosts are not supported\.
+ If the underlying host for your instance is scheduled for maintenance, you will receive a scheduled event notification 14 days before the event\. You must manually stop or restart your instance to move it to a new host\.