# Add or remove a public key on your instance<a name="replacing-key-pair"></a>

When you launch an instance, you are [prompted for a key pair](launching-instance.md#step-7-review-instance-launch)\. If you specify a key pair at launch, when your instance boots for the first time, the public key material is placed on your Linux instance in an entry within `~/.ssh/authorized_keys`\.

You can change the key pair that is used to access the default system account of your instance by adding a new public key on the instance, or by replacing the public key \(deleting the existing public key and adding a new one\) on the instance\. You can also remove all public keys from an instance\. You might take these actions for the following reasons:
+ If a user in your organization requires access to the system user account using a separate key pair, you can add the public key to your instance\.
+ If someone has a copy of the private key \(`.pem` file\) and you want to prevent them from connecting to your instance \(for example, if they've left your organization\), you can delete the public key on the instance and replace it with a new one\.
+ If you create a Linux AMI from an instance, the public key material is copied from the instance to the AMI\. If you launch an instance from the AMI, the new instance includes the public key from the original instance\. To prevent someone who has the private key from connecting to the new instance, you can remove the public key from the original instance *before* creating the AMI\.

The public keys are located in the `.ssh/authorized_keys` file on the instance\.

To add or replace a key pair, you must be able to connect to your instance\. If you've lost your existing private key or you launched your instance without a key pair, you won't be able connect to your instance and therefore won't be able to add or replace a key pair\. If you've lost your private key, you might be able to retrieve it\. For more information, see [I've lost my private key\. How can I connect to my Linux instance?](TroubleshootingInstancesConnecting.md#replacing-lost-key-pair) If you launched your instance without a key pair, you won't be able to connect to the instance unless you chose an AMI that is configured to allow users another way to log in\.

**Note**  
These procedures are for modifying the key pair for the default user account, such as `ec2-user`\. For information about adding user accounts to your instance, see [Manage user accounts on your Linux instance](managing-users.md)\.

**To add or replace a key pair**

1. Create a new key pair using the [Amazon EC2 console](create-key-pairs.md#having-ec2-create-your-key-pair) or a [third\-party tool](create-key-pairs.md#how-to-generate-your-own-key-and-import-it-to-aws)\.

1. Retrieve the public key from your new key pair\. For more information, see [Retrieve the public key material](describe-keys.md#retrieving-the-public-key)\.

1. [Connect to your instance](AccessingInstances.md) using your existing private key\.

1. Using a text editor of your choice, open the `.ssh/authorized_keys` file on the instance\. Paste the public key information from your new key pair underneath the existing public key information\. Save the file\.

1. Disconnect from your instance, and test that you can connect to your instance using the new private key file\.

1. \(Optional\) If you're replacing an existing key pair, connect to your instance and delete the public key information for the original key pair from the `.ssh/authorized_keys` file\.

**Important**  
If you're using an Auto Scaling group, ensure that the key pair you're replacing is not specified in your launch template or launch configuration\. If Amazon EC2 Auto Scaling detects an unhealthy instance, it launches a replacement instance\. However, the instance launch fails if the key pair cannot be found\. For more information, see [Launch templates](https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchTemplates.html) in the *Amazon EC2 Auto Scaling User Guide*\.

**To remove a public key from an instance**

1. [Connect to your instance](AccessingInstances.md)\.

1. Using a text editor of your choice, open the `.ssh/authorized_keys` file on the instance\. Delete the public key information, and then save the file\.

**Warning**  
After you remove all the public keys from an instance and disconnect from the instance, you can't connect to it again unless the AMI provides another way of logging in\.