# Using Bookmarks<a name="using-bookmarks"></a>

If you have created a public AMI, or shared an AMI with another AWS user, you can create a *bookmark* that allows a user to access your AMI and launch an instance in their own account immediately\. This is an easy way to share AMI references, so users don't have to spend time finding your AMI in order to use it\.

Note that your AMI must be public, or you must have shared it with the user to whom you want to send the bookmark\.

**To create a bookmark for your AMI**

1. Type a URL with the following information, where *region* is the region in which your AMI resides:

   ```
   https://console.aws.amazon.com/ec2/v2/home?region=region#LaunchInstanceWizard:ami=ami_id
   ```

   For example, this URL launches an instance from the ami\-0abcdef1234567890 AMI in the us\-east\-1 region:

   ```
   https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#LaunchInstanceWizard:ami=ami-0abcdef1234567890
   ```

1. Distribute the link to users who want to use your AMI\.

1. To use a bookmark, choose the link or copy and paste it into your browser\. The launch wizard opens, with the AMI already selected\.