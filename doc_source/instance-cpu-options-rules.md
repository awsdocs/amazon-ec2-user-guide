# Rules for specifying CPU options<a name="instance-cpu-options-rules"></a>

To specify the CPU options for your instance, be aware of the following rules:
+ CPU options can only be specified during instance launch and cannot be modified after launch\.
+ When you launch an instance, you must specify both the number of CPU cores and threads per core in the request\. For example requests, see [Specify CPU options for your instance](instance-specify-cpu-options.md)\.
+ The number of vCPUs for the instance is the number of CPU cores multiplied by the threads per core\. To specify a custom number of vCPUs, you must specify a valid number of CPU cores and threads per core for the instance type\. You cannot exceed the default number of vCPUs for the instance\. For more information, see [CPU cores and threads per CPU core per instance type](cpu-options-supported-instances-values.md)\.
+ To disable multithreading, specify one thread per core\.
+ When you [change the instance type](ec2-instance-resize.md) of an existing instance, the CPU options automatically change to the default CPU options for the new instance type\.
+ The specified CPU options persist after you stop, start, or reboot an instance\.