# Connecting to Your Linux Instance Using MindTerm<a name="mindterm"></a>

The following instructions explain how to connect to your instance using MindTerm through the Amazon EC2 console\. If you receive an error while attempting to connect to your instance, see [Troubleshooting Connecting to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html)\.

After you launch your instance, you can connect to it and use it the way that you'd use a computer sitting in front of you\.

**Note**  
After you launch an instance, it can take a few minutes for the instance to be ready so that you can connect to it\. Check that your instance has passed its status checks\. You can view this information in the **Status Checks** column on the **Instances** page\.

## Prerequisites<a name="mindterm-prereqs"></a>
+ **Verify that your browser supports the NPAPI plugin**

  If your browser does not support the NPAPI plugin, it can't run the MindTerm client\.
**Important**  
The Chrome browser does not support the NPAPI plugin\. For more information, see the Chromium [NPAPI deprecation article](https://www.chromium.org/developers/npapi-deprecation)\. The FireFox browser does not support the NPAPI plugin\. For more information, see the Java [NPAPI deprecation article](https://www.java.com/en/download/faq/firefox_java.xml)\.
+ **Install Java**

  Your Linux computer most likely includes Java\. If not, see [How do I enable Java in my web browser?](https://java.com/en/download/help/index_installing.xml)\. On a Windows or macOS client, you must run your browser using administrator credentials\. For Linux, additional steps may be required if you are not logged in as `root`\.
+ **Enable Java in your browser**

  For instructions, see [https://java\.com/en/download/help/enable\_browser\.xml](https://java.com/en/download/help/enable_browser.xml)\.
+ **Locate the private key**** and verify permissions**

  Get the fully\-qualified path to the location on your computer of the `.pem` file for the key pair that you specified when you launched the instance\. Verify that the `.pem` file has permissions of 0400, not 0777\. For more information, see [Error: Unprotected Private Key File](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.
+ **Get the default user name for the AMI that you used to launch your instance**
  + For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
  + For a Centos AMI, the user name is `centos`\.
  + For a Debian AMI, the user name is `admin` or `root`\.
  + For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
  + For a RHEL AMI, the user name is `ec2-user` or `root`\.
  + For a SUSE AMI, the user name is `ec2-user` or `root`\.
  + For an Ubuntu AMI, the user name is `ubuntu`\.
  + Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.
+ **Enable inbound SSH traffic from your IP address to your instance**

  Ensure that the security group associated with your instance allows incoming SSH traffic from your IP address\. The default security group for the VPC does not allow incoming SSH traffic by default\. The security group created by the launch wizard enables SSH traffic by default\. For more information, see [Authorizing Inbound Traffic for Your Linux Instances](authorizing-access-to-an-instance.md)\.

## Starting MindTerm<a name="mindterm-connect"></a>

**To connect to your instance using a web browser with MindTerm**

1. In the Amazon EC2 console, choose **Instances** in the navigation pane\.

1. Select the instance, and then choose **Connect**\.

1. Choose **A Java SSH client directly from my browser \(Java required\)**\.

1. Amazon EC2 automatically detects the public DNS name of your instance and then populates **Public DNS** for you\. It also detects the name of the key pair that you specified when you launched the instance\. Complete the following, and then choose **Launch SSH Client**\.

   1. In **User name**, enter the user name to log in to your instance\.
      + For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
      + For a Centos AMI, the user name is `centos`\.
      + For a Debian AMI, the user name is `admin` or `root`\.
      + For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
      + For a RHEL AMI, the user name is `ec2-user` or `root`\.
      + For a SUSE AMI, the user name is `ec2-user` or `root`\.
      + For an Ubuntu AMI, the user name is `ubuntu`\.
      + Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.

   1. In **Private key path**, enter the fully qualified path to your private key \(`.pem`\) file, including the key pair name; for example:

      `C:\KeyPairs\my-key-pair.pem`

   1. \(Optional\) Choose **Store in browser cache** to store the location of the private key in your browser cache\. This enables Amazon EC2 to detect the location of the private key in subsequent browser sessions, until you clear your browser's cache\.

1. If necessary, choose **Yes** to trust the certificate, and choose **Run** to run the MindTerm client\.

1. If this is your first time running MindTerm, a series of dialog boxes asks you to accept the license agreement, to confirm setup for your home directory, and to confirm setup of the known hosts directory\. Confirm these settings\.

1. A dialog prompts you to add the host to your set of known hosts\. If you do not want to store the host key information on your local computer, choose **No**\.

1. A window opens and you are connected to your instance\. 

   If you chose **No** in the previous step, you see the following message, which is expected:

   ```
   Verification of server key disabled in this session.
   ```