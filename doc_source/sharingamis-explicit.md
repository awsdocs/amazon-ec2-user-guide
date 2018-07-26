# Sharing an AMI with Specific AWS Accounts<a name="sharingamis-explicit"></a>

You can share an AMI with specific AWS accounts without making the AMI public\. All you need are the AWS account IDs\.

AMIs are a regional resource\. Therefore, sharing an AMI makes it available in that region\. To make an AMI available in a different region, copy the AMI to the region and then share it\. For more information, see [Copying an AMI](CopyingAMIs.md)\.

**Note**  
If you are sharing an AMI containing a snapshot of an encrypted volume, see [Sharing an Amazon EBS Snapshot](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modifying-snapshot-permissions.html) for restrictions that apply\.

## Sharing an AMI \(Console\)<a name="sharingamis-console"></a>

**To grant explicit launch permissions using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\.

1. Select your AMI in the list, and then choose **Actions**, **Modify Image Permissions**\.

1. Specify the AWS account number of the user with whom you want to share the AMI in the **AWS Account Number** field, then choose **Add Permission**\.

   To share this AMI with multiple users, repeat this step until you have added all the required users\. 

1. To allow create volume permissions for snapshots, select ** Add "create volume" permissions to the following associated snapshots when creating permissions\.** 
**Note**  
You do not need to share the Amazon EBS snapshots that an AMI references in order to share the AMI\. Only the AMI itself needs to be shared; the system automatically provides the instance access to the referenced Amazon EBS snapshots for the launch\.

1. Choose **Save** when you are done\.

1. \(Optional\) To view the AWS account IDs with which you have shared the AMI, select the AMI in the list, and choose the **Permissions** tab\. To find AMIs that are shared with you, see [Finding Shared AMIs](usingsharedamis-finding.md)\.

## Sharing an AMI \(AWS CLI\)<a name="sharingamis-aws-cli"></a>

Use the [modify\-image\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-image-attribute.html) command \(AWS CLI\) to share an AMI as shown in the following examples\.

**To grant explicit launch permissions**  
The following command grants launch permissions for the specified AMI to the specified AWS account\.

```
aws ec2 modify-image-attribute --image-id ami-12345678 --launch-permission "Add=[{UserId=123456789012}]"
```

The following command grants create volume permission for a snapshot\.

```
aws ec2 modify-snapshot-attribute --snapshot-id snap-1234567890abcdef0 --attribute createVolumePermission --operation-type add --user-ids 123456789012
```

**To remove launch permissions for an account**  
The following command removes launch permissions for the specified AMI from the specified AWS account:

```
aws ec2 modify-image-attribute --image-id ami-12345678 --launch-permission "Remove=[{UserId=123456789012}]"
```

The following command removes create volume permission for a snapshot\.

```
aws ec2 modify-snapshot-attribute --snapshot-id snap-1234567890abcdef0 --attribute createVolumePermission --operation-type remove --user-ids 123456789012
```

**To remove all launch permissions**  
The following command removes all public and explicit launch permissions from the specified AMI\. Note that the owner of the AMI always has launch permissions and is therefore unaffected by this command\.

```
aws ec2 reset-image-attribute --image-id ami-12345678 --attribute launchPermission
```