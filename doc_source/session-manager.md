# Connecting to your Linux instance using Session Manager<a name="session-manager"></a>

Session Manager is a fully managed AWS Systems Manager capability that lets you manage your Amazon EC2 instances through an interactive one\-click browser\-based shell or through the AWS CLI\. You can use Session Manager to start a session with an instance in your account\. After the session is started, you can run bash commands as you would through any other connection type\. For more information about Session Manager, see [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) in the *AWS Systems Manager User Guide*\.

Before attempting to connect to an instance using Session Manager, ensure that the necessary setup steps have been completed\. For more information and instructions, see [ Getting Started with Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started.html)\.

**To connect to a Linux instance using Session Manager using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Connect**\.

1. For **Connection method**, choose **Session Manager**\.

1. Choose **Connect**\.

**Troubleshooting**  
If you receive an error that you’re not authorized to perform one or more Systems Manager actions \(`ssm:command-name`\), then you must update your policies to allow you to start sessions from the Amazon EC2 console\. For more information, see [ Quickstart Default IAM Policies for Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/getting-started-restrict-access-quickstart.html) in the *AWS Systems Manager User Guide*\.