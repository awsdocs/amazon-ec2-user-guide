# Shared AMIs<a name="sharing-amis"></a>

*A shared AMI* is an AMI that a developer created and made available for others to use\. One of the easiest ways to get started with Amazon EC2 is to use a shared AMI that has the components you need and then add custom content\. You can also create your own AMIs and share them with others\. 

You use a shared AMI at your own risk\. Amazon can't vouch for the integrity or security of AMIs shared by other Amazon EC2 users\. Therefore, you should treat shared AMIs as you would any foreign code that you might consider deploying in your own data center, and perform the appropriate due diligence\. We recommend that you get an AMI from a trusted source\.

## Verified provider<a name="verified-ami-provider"></a>

In the Amazon EC2 console, public AMIs that are owned by Amazon or a verified Amazon partner are marked **Verified provider**\.

You can also use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) AWS CLI command to identify the public AMIs that come from a verified provider\. Public images that are owned by Amazon or a verified partner have an aliased owner, which is either `amazon` or `aws-marketplace`\. In the CLI output, these values appear for `ImageOwnerAlias`\. Other users can't alias their AMIs\. This enables you to easily find AMIs from Amazon or verified partners\.

To become a verified provider, you must register as a seller on the AWS Marketplace\. Once registered, you can list your AMI on the AWS Marketplace\. For more information, see [Getting started as a seller](https://docs.aws.amazon.com/marketplace/latest/userguide/user-guide-for-sellers.html) and [AMI\-based products](https://docs.aws.amazon.com/marketplace/latest/userguide/ami-products.html) in the *AWS Marketplace Seller Guide*\.

**Topics**
+ [Verified provider](#verified-ami-provider)
+ [Find shared AMIs](usingsharedamis-finding.md)
+ [Make an AMI public](sharingamis-intro.md)
+ [Share an AMI with specific organizations or organizational units](share-amis-with-organizations-and-OUs.md)
+ [Share an AMI with specific AWS accounts](sharingamis-explicit.md)
+ [Cancel having an AMI shared with your AWS account](cancel-sharing-an-AMI.md)
+ [Use bookmarks](using-bookmarks.md)
+ [Guidelines for shared Linux AMIs](building-shared-amis.md)

**If you're looking for information about other topics**
+ For information about creating an AMI, see [Create an instance store\-backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-instance-store.html) or [Create an Amazon EBS\-backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html)\.
+ For information about building, delivering, and maintaining your applications on the AWS Marketplace, see the [AWS Marketplace Documentation](https://docs.aws.amazon.com/marketplace/)\.