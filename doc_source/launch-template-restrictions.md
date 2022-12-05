# Launch template restrictions<a name="launch-template-restrictions"></a>

The following rules apply to launch templates and launch template versions:
+ **Quotas** – You are limited to creating 5,000 launch templates per Region and 10,000 versions per launch template\.
+ **Parameters are optional** – Launch template parameters are optional\. However, you must ensure that your request to launch an instance includes all the required parameters\. For example, if your launch template does not include an AMI ID, you must specify both the launch template and an AMI ID when you launch an instance\.
+ **Parameters not validated** – Launch template parameters are not fully validated when you create the launch template\. If you specify incorrect values for parameters, or if you do not use supported parameter combinations, no instances can launch using this launch template\. Ensure that you specify the correct values for the parameters and that you use supported parameter combinations\. For example, to launch an instance in a placement group, you must specify a supported instance type\.
+ **Tags** – You can tag a launch template, but you cannot tag a launch template version\.
+ **Immutable** – Launch templates are immutable\. To modify a launch template, you must create a new version of the launch template\.
+ **Version numbers** – Launch template versions are numbered in the order in which they are created\. When you create a launch template version, you cannot specify the version number yourself\.