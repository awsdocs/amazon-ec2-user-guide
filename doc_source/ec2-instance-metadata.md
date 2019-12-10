# Instance Metadata and User Data<a name="ec2-instance-metadata"></a>

*Instance metadata* is data about your instance that you can use to configure or manage the running instance\. Instance metadata is divided into categories, for example, host name, events, and security groups\.

You can also use instance metadata to access *user data* that you specified when launching your instance\. For example, you can specify parameters for configuring your instance, or include a simple script\. You can build generic AMIs and use user data to modify the configuration files supplied at launch time\. For example, if you run web servers for various small businesses, they can all use the same generic AMI and retrieve their content from the Amazon S3 bucket that you specify in the user data at launch\. To add a new customer at any time, create a bucket for the customer, add their content, and launch your AMI with the unique bucket name provided to your code in the user data\. If you launch more than one instance at the same time, the user data is available to all instances in that reservation\. Each instance that is part of the same reservation has a unique `ami-launch-index` number, allowing you to write code that controls what to do\. For example, the first host might elect itself as an initial master node in a cluster\. For a detailed AMI launch example, see [Example: AMI Launch Index Value](AMI-launch-index-examples.md)\.

EC2 instances can also include *dynamic data*, such as an instance identity document that is generated when the instance is launched\. For more information, see [Dynamic Data Categories](instancedata-data-categories.md#dynamic-data-categories)\.

**Important**  
Although you can only access instance metadata and user data from within the instance itself, the data is not protected by authentication or cryptographic methods\. Anyone who has direct access to the instance, and potentially any software running on the instance, can view its metadata\. Therefore, you should not store sensitive data, such as passwords or long\-lived encryption keys, as user data\.

**Topics**
+ [Configuring the Instance Metadata Service](configuring-instance-metadata-service.md)
+ [Retrieving Instance Metadata](instancedata-data-retrieval.md)
+ [Working with Instance User Data](instancedata-add-user-data.md)
+ [Retrieving Dynamic Data](instancedata-dynamic-data-retrieval.md)
+ [Example: AMI Launch Index Value](AMI-launch-index-examples.md)
+ [Instance Metadata Categories](instancedata-data-categories.md)
+ [Instance Identity Documents](instance-identity-documents.md)