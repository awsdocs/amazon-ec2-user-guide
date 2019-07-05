# Tutorial: Remotely Manage Your Amazon EC2 Instances Using Systems Manager<a name="tutorial_run_command"></a>

This tutorial shows you how to remotely manage an Amazon EC2 instance from your local machine using the AWS Systems Manager Run Command capability \. The tutorial includes procedures for running commands using the Amazon EC2 console, AWS Tools for Windows PowerShell, and the AWS Command Line Interface\.

**Note**  
With Run Command, you can also manage your on\-premises servers and virtual machines \(VMs\) in your hybrid environment, including VMs provided by other cloud providers\. For more information, see [Setting Up Systems Manager for Hybrid Environments](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-managedinstances.html)\.

**Before You Begin**  
You must configure an AWS Identity and Access Management \(IAM\) instance profile for Systems Manager\. In order for your instance to communicate with the Systems Manager API, the AWS managed policy **AmazonSSMManagedInstanceCore** must be attached to the instance profile\. The permissions in this policy enable the instance to communicate with the Systems Manager API and provides the minimum permissions necessary to use the Systems Manager service\. 

For information about creating an IAM instance profile for Systems Manager with these permissions, and for information about other permissions and policies you can assign to your instance profile, see [Create an IAM Instance Profile for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-profile.html) in the *AWS Systems Manager User Guide*\. 

You must also configure your IAM user account for Systems Manager, as described in the next section\.

## Grant Your User Account Access to Systems Manager<a name="tutorial-iam-rc"></a>

Your user account must be configured to communicate with the Systems Manager API\. Use the following procedure to attach a managed AWS Identity and Access Management \(IAM\) policy to your user account that grants you full access to Systems Manager API actions\.

**To create the IAM policy for your user account**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\. \(If this is your first time using IAM, choose **Get Started**, and then choose **Create policy**\.\)

1. In the **Filter** field, type **AmazonSSMFullAccess** and press Enter\.

1. Choose the button next to **AmazonSSMFullAccess**, and then choose **Policy actions**, **Attach**\.

1. On the **Attach Policy** page, select the box next to your user account name, and then choose **Attach policy**\.

## Install SSM Agent<a name="tutorial-agent-install"></a>

SSM Agent processes Run Command requests and configures the instances that are specified in the request\. The agent is installed by default on Windows AMIs starting in November 2016 and later, Amazon Linux AMIs starting with 2017\.09, and all Amazon Linux 2 AMIs\.

To install SSM Agent on Linux, see [Installing and Configuring SSM Agent on Amazon EC2 Linux Instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html) in the *AWS Systems Manager User Guide*\.

To install SSM Agent on Windows, see [Installing and Configuring SSM Agent on Windows Instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html) in the *AWS Systems Manager User Guide*\.

## Send a Command Using the Systems Manager Console<a name="rc-tutorial-ui"></a>

Use the following procedure to list all services running on the instance by using Run Command from the Systems Manager console\.

**To run a command using Run Command from the console**

1. Open the Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager](https://console.aws.amazon.com/systems-manager)\.

1. In the navigation pane, choose **Run Command**\.

1. Choose **Run a command**\.

1. For **Command document**, choose the button next to `AWS-RunPowerShellScript` for Windows instances, or the button next to `AWS-RunShellScript` for Linux instances\.

1. In the targets section, choose the instance you created\. 

   If you don't see the instance, verify that you are currently in the same AWS Region as the instance you created\. Also verify that you configured the IAM role and trust policies as described earlier\.

1. For **Execute on**, enter **1**\.

1. For **Stop after**, enter **1**\.

1. For **Commands**, type **Get\-Service** for Windows, or **ps aux** for Linux\.

1. \(Optional\) For **Working Directory**, specify a path to the folder on your EC2 instances where you want to run the command\.

1. \(Optional\) For **Execution Timeout**, specify the number of seconds the EC2Config service or SSM Agent will attempt to run the command before it times out and fails\.
**Note**  
Windows AMIs published before November 2016 use the EC2Config service to process requests and configure instances\. Unless you have a specific reason for using the EC2Config service or an earlier version of SSM Agent to process Systems Manager requests, we recommend that you download and install the latest version of the SSM Agent to each of your Amazon EC2 instances and managed instances in your hybrid environment\. For more information, see [Working with SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html) in the *AWS Systems Manager User Guide*

1. For **Comment**, we recommend providing information that will help you identify this command in your list of commands\.

1. For **Timeout \(seconds\)**, type the number of seconds that Run Command should attempt to reach an instance before the instance is considered unreachable and the command execution fails\.

1. Choose **Run** to execute the command\. Run Command displays a status screen\. Choose **View result**\.

1. To view the output, choose the command invocation for the command, choose the **Output** tab\.  
![\[List of commands run using Run Command\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/run-command-walkthrough-7.png)

For more examples of how to execute commands using Run Command, see [Running Commands Using Systems Manager Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/run-command.html) in the *AWS Systems Manager User Guide*\.

## Send a Command Using AWS Tools for Windows PowerShell<a name="tutorial-powershell"></a>

Use the following procedure to list all services running on the instance by using Run Command from AWS Tools for Windows PowerShell\.

**To run a command**

1. On your local computer, download the latest version of [AWS Tools for Windows PowerShell](https://aws.amazon.com/powershell/)\.

1. Open **AWS Tools for Windows PowerShell** on your local computer and run the following command to specify your credentials\. 

   ```
   Set-AWSCredentials –AccessKey key –SecretKey key
   ```

1. Run the following command to set the Region for your PowerShell session\. Specify the AWS Region where you created the instance in the previous procedure\. This example uses the us\-west\-2 region\.

   ```
   Set-DefaultAWSRegion -Region us-west-2
   ```

1. Run the following command to retrieve the services running on the instance\. 

   ```
   Send-SSMCommand -InstanceId 'Instance-ID' -DocumentName AWS-RunPowerShellScript -Comment 'listing services on the instance' -Parameter @{'commands'=@('Get-Service')}
   ```

   The command returns a command ID, which you will use to view the results\.

1. The following command returns the output of the original `Send-SSMCommand`\. The output is truncated after 2500 characters\. To view the full list of services, specify an Amazon S3 bucket in the command using the \-OutputS3BucketName *bucket\_name* parameter\.

   ```
   Get-SSMCommandInvocation -CommandId Command-ID -Details $true | select -ExpandProperty CommandPlugins
   ```

For more examples of how to execute commands using Run Command with Tools for Windows PowerShell, see [Systems Manager Run Command Walkthough Using the AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/systems-manager/latest/userguide/walkthrough-powershell.html)\.

## Send a Command Using the AWS CLI<a name="tutorial-cli"></a>

Use the following procedure to list all services running on the instance by using Run Command in the AWS CLI\.

**To execute a command**

Install and configure the AWS CLI, if you have not already\.

For information, see [Install or Upgrade and then Configure the AWS CLI](https://docs.aws.amazon.com/systems-manager/latest/userguide/getting-started-cli.html) in the *AWS Systems Manager User Guide*\.

1. Run the following command to retrieve the services running on the instance\. 

   ```
   aws ssm send-command --document-name "AWS-RunShellScript" --comment "listing services" --instance-ids "Instance-ID" --parameters commands="service --status-all" --region us-west-2 --output text
   ```

   The command returns a command ID, which you will use to view the results\.

1. The following command returns the output of the original `Send-SSMCommand`\. The output is truncated after 2500 characters\. To view the full list of services, you specify an Amazon S3 bucket in the command using the `--output-s3-bucket-name` *bucket\_name* parameter\.

   ```
   aws ssm list-command-invocations --command-id "command ID" --details
   ```

For more examples of how to run commands using Run Command using the AWS CLI, see [Walkthrough: Use the AWS CLI with Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/walkthrough-cli.html) in the *AWS Systems Manager User Guide*\.

## Related Content<a name="run-command-related-content"></a>

For more information about Run Command and Systems Manager, see the following references\.
+ [AWS Systems Manager User Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/)
+ [AWS Systems Manager API Reference](https://docs.aws.amazon.com/systems-manager/latest/APIReference/)
+ [Systems Manager AWS Tools for PowerShell Cmdlet Reference](https://docs.aws.amazon.com/powershell/latest/reference/items/Amazon_Simple_Systems_Management_cmdlets.html)
+ [Systems Manager AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/ssm/index.html)
+  [AWS SDKs](https://aws.amazon.com/tools/#SDKs)