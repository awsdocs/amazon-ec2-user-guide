# Delete your public key on Amazon EC2<a name="delete-key-pair"></a>

You can delete public keys that are stored in Amazon EC2\. Deleting a public key does not delete the matching private key\.

When you delete a public key using the following methods, you're only deleting the public key that you stored in Amazon EC2 when you [created](create-key-pairs.md#having-ec2-create-your-key-pair) or [imported](create-key-pairs.md#how-to-generate-your-own-key-and-import-it-to-aws) the key pair\. Deleting a public key doesn't remove the public key from any instances to which you've added it, either when you launched the instance or later\. It also doesn't delete the private key on your local computer\. You can continue to connect to instances that you launched using a public key that you've deleted from Amazon EC2 as long as you still have the private key \(`.pem`\) file\.

**Note**  
To remove a public key from an instance, see [Add or remove a public key on your instance](replacing-key-pair.md)\.

**Important**  
If you're using an Auto Scaling group \(for example, in an Elastic Beanstalk environment\), ensure that the public key you're deleting is not specified in an associated launch template or launch configuration\. If Amazon EC2 Auto Scaling detects an unhealthy instance, it launches a replacement instance\. However, the instance launch fails if the public key cannot be found\. For more information, see [Launch templates](https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchTemplates.html) in the *Amazon EC2 Auto Scaling User Guide*\.

You can delete a public key on Amazon EC2 using the following methods\.

------
#### [ Console ]

**To delete your public key on Amazon EC2**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Select the key pair to delete and choose **Actions**, **Delete**\.

1. In the confirmation field, enter `Delete` and then choose **Delete**\.

------
#### [ AWS CLI ]

**To delete your public key on Amazon EC2**  
Use the [https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-key-pair.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-key-pair.html) AWS CLI command\.

------
#### [ PowerShell ]

**To delete your public key on Amazon EC2**  
Use the [https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2KeyPair.html](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2KeyPair.html) AWS Tools for Windows PowerShell command\.

------