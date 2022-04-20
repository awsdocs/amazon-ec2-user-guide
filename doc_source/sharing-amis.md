# Shared AMIs<a name="sharing-amis"></a>

*A shared AMI* is an AMI that a developer created and made available for others to use\. One of the easiest ways to get started with Amazon EC2 is to use a shared AMI that has the components you need and then add custom content\. You can also create your own AMIs and share them with others\. 

You use a shared AMI at your own risk\. Amazon can't vouch for the integrity or security of AMIs shared by other Amazon EC2 users\. Therefore, you should treat shared AMIs as you would any foreign code that you might consider deploying in your own data center and perform the appropriate due diligence\. We recommend that you get an AMI from a trusted source\.

Amazon's public images have an aliased owner, which appears as `amazon` in the account field\. This enables you to find AMIs from Amazon easily\. Other users can't alias their AMIs\.

For information about creating an AMI, see [Create an instance store\-backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-instance-store.html) or [Create an Amazon EBS\-backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html)\. For information about building, delivering, and maintaining your applications on the AWS Marketplace, see the [AWS Marketplace Documentation](https://docs.aws.amazon.com/marketplace/)\.

**Topics**
+ [Find shared AMIs](usingsharedamis-finding.md)
+ [Make an AMI public](sharingamis-intro.md)
+ [Share an AMI with specific organizations or organizational units](share-amis-with-organizations-and-OUs.md)
+ [Share an AMI with specific AWS accounts](sharingamis-explicit.md)
+ [Use bookmarks](using-bookmarks.md)
+ [Guidelines for shared Linux AMIs](building-shared-amis.md)