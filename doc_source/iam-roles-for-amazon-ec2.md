# IAM roles for Amazon EC2<a name="iam-roles-for-amazon-ec2"></a>

Applications must sign their API requests with AWS credentials\. Therefore, if you are an application developer, you need a strategy for managing credentials for your applications that run on EC2 instances\. For example, you can securely distribute your AWS credentials to the instances, enabling the applications on those instances to use your credentials to sign requests, while protecting your credentials from other users\. However, it's challenging to securely distribute credentials to each instance, especially those that AWS creates on your behalf, such as Spot Instances or instances in Auto Scaling groups\. You must also be able to update the credentials on each instance when you rotate your AWS credentials\.

We designed IAM roles so that your applications can securely make API requests from your instances, without requiring you to manage the security credentials that the applications use\. Instead of creating and distributing your AWS credentials, you can delegate permission to make API requests using IAM roles as follows:

1. Create an IAM role\.

1. Define which accounts or AWS services can assume the role\.

1. Define which API actions and resources the application can use after assuming the role\.

1. Specify the role when you launch your instance, or attach the role to an existing instance\.

1. Have the application retrieve a set of temporary credentials and use them\.

For example, you can use IAM roles to grant permissions to applications running on your instances that need to use a bucket in Amazon S3\. You can specify permissions for IAM roles by creating a policy in JSON format\. These are similar to the policies that you create for IAM users\. If you change a role, the change is propagated to all instances\.

When creating IAM roles, associate least privilege IAM policies that restrict access to the specific API calls the application requires\.

You can only attach one IAM role to an instance, but you can attach the same role to many instances\. For more information about creating and using IAM roles, see [Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/WorkingWithRoles.html) in the *IAM User Guide*\.

You can apply resource\-level permissions to your IAM policies to control the users' ability to attach, replace, or detach IAM roles for an instance\. For more information, see [Supported resource\-level permissions for Amazon EC2 API actions](iam-policy-structure.md#ec2-supported-iam-actions-resources) and the following example: [Example: Work with IAM roles](ExamplePolicies_EC2.md#iam-example-iam-roles)\.

**Topics**
+ [Instance profiles](#ec2-instance-profile)
+ [Retrieve security credentials from instance metadata](#instance-metadata-security-credentials)
+ [Grant an IAM user permission to pass an IAM role to an instance](#permission-to-pass-iam-roles)
+ [Work with IAM roles](#working-with-iam-roles)

## Instance profiles<a name="ec2-instance-profile"></a>

Amazon EC2 uses an *instance profile* as a container for an IAM role\. When you create an IAM role using the IAM console, the console creates an instance profile automatically and gives it the same name as the role to which it corresponds\. If you use the Amazon EC2 console to launch an instance with an IAM role or to attach an IAM role to an instance, you choose the role based on a list of instance profile names\. 

If you use the AWS CLI, API, or an AWS SDK to create a role, you create the role and instance profile as separate actions, with potentially different names\. If you then use the AWS CLI, API, or an AWS SDK to launch an instance with an IAM role or to attach an IAM role to an instance, specify the instance profile name\. 

An instance profile can contain only one IAM role\. This limit cannot be increased\.

For more information, see [Instance Profiles](https://docs.aws.amazon.com/IAM/latest/UserGuide/instance-profiles.html) in the *IAM User Guide*\.

## Retrieve security credentials from instance metadata<a name="instance-metadata-security-credentials"></a>

An application on the instance retrieves the security credentials provided by the role from the instance metadata item `iam/security-credentials/`*role\-name*\. The application is granted the permissions for the actions and resources that you've defined for the role through the security credentials associated with the role\. These security credentials are temporary and we rotate them automatically\. We make new credentials available at least five minutes before the expiration of the old credentials\.

**Warning**  
If you use services that use instance metadata with IAM roles, ensure that you don't expose your credentials when the services make HTTP calls on your behalf\. The types of services that could expose your credentials include HTTP proxies, HTML/CSS validator services, and XML processors that support XML inclusion\.

The following command retrieves the security credentials for an IAM role named `s3access`\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/iam/security-credentials/s3access
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/iam/security-credentials/s3access
```

------

The following is example output\.

```
{
  "Code" : "Success",
  "LastUpdated" : "2012-04-26T16:39:16Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "ASIAIOSFODNN7EXAMPLE",
  "SecretAccessKey" : "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
  "Token" : "token",
  "Expiration" : "2017-05-17T15:09:54Z"
}
```

For applications, AWS CLI, and Tools for Windows PowerShell commands that run on the instance, you do not have to explicitly get the temporary security credentials—the AWS SDKs, AWS CLI, and Tools for Windows PowerShell automatically get the credentials from the EC2 instance metadata service and use them\. To make a call outside of the instance using temporary security credentials \(for example, to test IAM policies\), you must provide the access key, secret key, and the session token\. For more information, see [Using Temporary Security Credentials to Request Access to AWS Resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) in the *IAM User Guide*\.

For more information about instance metadata, see [Instance metadata and user data](ec2-instance-metadata.md)\. For information about the instance metadata IP address, see [Retrieve instance metadata](instancedata-data-retrieval.md)\.

## Grant an IAM user permission to pass an IAM role to an instance<a name="permission-to-pass-iam-roles"></a>

To enable an IAM user to launch an instance with an IAM role or to attach or replace an IAM role for an existing instance, you must grant the user permission to use the following API actions:
+ `iam:PassRole`
+ `ec2:AssociateIamInstanceProfile` 
+ `ec2:ReplaceIamInstanceProfileAssociation`

For example, the following IAM policy grants users permission to launch instances with an IAM role, or to attach or replace an IAM role for an existing instance using the AWS CLI\.

**Note**  
If you want the policy to grant IAM users access to all of your roles, specify the resource as `*` in the policy\. However, please consider the principle of [least privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege) as a best\-practice \.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
         "ec2:RunInstances",
         "ec2:AssociateIamInstanceProfile",
         "ec2:ReplaceIamInstanceProfileAssociation"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "iam:PassRole",
      "Resource": "arn:aws:iam::123456789012:role/DevTeam*"
    }
  ]
}
```

To grant users permission to launch instances with an IAM role, or to attach or replace an IAM role for an existing instance using the Amazon EC2 console, you must grant them permission to use `iam:ListInstanceProfiles`, `iam:PassRole`, `ec2:AssociateIamInstanceProfile`, and `ec2:ReplaceIamInstanceProfileAssociation` in addition to any other permissions they might need\. For example policies, see [Example policies for working in the Amazon EC2 console](iam-policies-ec2-console.md)\.

## Work with IAM roles<a name="working-with-iam-roles"></a>

You can create an IAM role and attach it to an instance during or after launch\. You can also replace or detach an IAM role for an instance\.

**Topics**
+ [Create an IAM role](#create-iam-role)
+ [Launch an instance with an IAM role](#launch-instance-with-role)
+ [Attach an IAM role to an instance](#attach-iam-role)
+ [Replace an IAM role](#replace-iam-role)
+ [Detach an IAM role](#detach-iam-role)
+ [Generate a policy for your IAM role based on access activity](#generate-policy-for-iam-role)

### Create an IAM role<a name="create-iam-role"></a>

You must create an IAM role before you can launch an instance with that role or attach it to an instance\.<a name="create-iam-role-console"></a>

**To create an IAM role using the IAM console**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**, **Create role**\.

1. On the **Select role type** page, choose **EC2** and the **EC2** use case\. Choose **Next: Permissions**\.

1. On the **Attach permissions policy** page, select an AWS managed policy that grants your instances access to the resources that they need\.

1. On the **Review** page, enter a name for the role and choose **Create role**\.

Alternatively, you can use the AWS CLI to create an IAM role\. The following example creates an IAM role with a policy that allows the role to use an Amazon S3 bucket\.<a name="create-iam-role-cli"></a>

**To create an IAM role and instance profile \(AWS CLI\)**

1. Create the following trust policy and save it in a text file named `ec2-role-trust-policy.json`\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": { "Service": "ec2.amazonaws.com"},
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

1. Create the `s3access` role and specify the trust policy that you created using the [create\-role](https://docs.aws.amazon.com/cli/latest/reference/iam/create-role.html) command\.

   ```
   aws iam create-role \
       --role-name s3access \
       --assume-role-policy-document file://ec2-role-trust-policy.json
   ```

   Example response

   ```
   {
       "Role": {
           "AssumeRolePolicyDocument": {
               "Version": "2012-10-17",
               "Statement": [
                   {
                       "Action": "sts:AssumeRole",
                       "Effect": "Allow",
                       "Principal": {
                           "Service": "ec2.amazonaws.com"
                       }
                   }
               ]
           },
           "RoleId": "AROAIIZKPBKS2LEXAMPLE",
           "CreateDate": "2013-12-12T23:46:37.247Z",
           "RoleName": "s3access",
           "Path": "/",
           "Arn": "arn:aws:iam::123456789012:role/s3access"
       }
   }
   ```

1. Create an access policy and save it in a text file named `ec2-role-access-policy.json`\. For example, this policy grants administrative permissions for Amazon S3 to applications running on the instance\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": ["s3:*"],
         "Resource": ["*"]
       }
     ]
   }
   ```

1. Attach the access policy to the role using the [put\-role\-policy](https://docs.aws.amazon.com/cli/latest/reference/iam/put-role-policy.html) command\.

   ```
   aws iam put-role-policy \
       --role-name s3access \
       --policy-name S3-Permissions \
       --policy-document file://ec2-role-access-policy.json
   ```

1. Create an instance profile named `s3access-profile` using the [create\-instance\-profile](https://docs.aws.amazon.com/cli/latest/reference/iam/create-instance-profile.html) command\.

   ```
   aws iam create-instance-profile --instance-profile-name s3access-profile
   ```

   Example response

   ```
   {
       "InstanceProfile": {
           "InstanceProfileId": "AIPAJTLBPJLEGREXAMPLE",
           "Roles": [],
           "CreateDate": "2013-12-12T23:53:34.093Z",
           "InstanceProfileName": "s3access-profile",
           "Path": "/",
           "Arn": "arn:aws:iam::123456789012:instance-profile/s3access-profile"
       }
   }
   ```

1. Add the `s3access` role to the `s3access-profile` instance profile\.

   ```
   aws iam add-role-to-instance-profile \
       --instance-profile-name s3access-profile \
       --role-name s3access
   ```

Alternatively, you can use the following AWS Tools for Windows PowerShell commands:
+ [New\-IAMRole](https://docs.aws.amazon.com/powershell/latest/reference/items/New-IAMRole.html)
+ [Register\-IAMRolePolicy](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-IAMRolePolicy.html)
+ [New\-IAMInstanceProfile](https://docs.aws.amazon.com/powershell/latest/reference/items/New-IAMInstanceProfile.html)

### Launch an instance with an IAM role<a name="launch-instance-with-role"></a>

After you've created an IAM role, you can launch an instance, and associate that role with the instance during launch\.

**Important**  
After you create an IAM role, it might take several seconds for the permissions to propagate\. If your first attempt to launch an instance with a role fails, wait a few seconds before trying again\. For more information, see [Troubleshooting Working with Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/RolesTroubleshooting.html) in the *IAM User Guide*\.<a name="launch-instance-with-role-console"></a>

**To launch an instance with an IAM role \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the dashboard, choose **Launch instance**\.

1. Select an AMI and instance type and then choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, for **IAM role**, select the IAM role that you created\. 
**Note**  
The **IAM role** list displays the name of the instance profile that you created when you created your IAM role\. If you created your IAM role using the console, the instance profile was created for you and given the same name as the role\. If you created your IAM role using the AWS CLI, API, or an AWS SDK, you may have named your instance profile differently\. 

1. Configure any other details, then follow the instructions through the rest of the wizard, or choose **Review and Launch** to accept default settings and go directly to the **Review Instance Launch** page\.

1. Review your settings, then choose **Launch** to choose a key pair and launch your instance\.

1. If you are using the Amazon EC2 API actions in your application, retrieve the AWS security credentials made available on the instance and use them to sign the requests\. The AWS SDK does this for you\.

------
#### [ IMDSv2 ]

   ```
   [ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
   && curl -H "X-aws-ec2-metadata-token: $TOKEN" –v http://169.254.169.254/latest/meta-data/iam/security-credentials/role_name
   ```

------
#### [ IMDSv1 ]

   ```
   [ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/iam/security-credentials/role_name
   ```

------

Alternatively, you can use the AWS CLI to associate a role with an instance during launch\. You must specify the instance profile in the command\.<a name="launch-instance-with-role-cli"></a>

**To launch an instance with an IAM role \(AWS CLI\)**

1. Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch an instance using the instance profile\. The following example shows how to launch an instance with the instance profile\. 

   ```
   aws ec2 run-instances \
       --image-id ami-11aa22bb \
       --iam-instance-profile Name="s3access-profile" \
       --key-name my-key-pair \
       --security-groups my-security-group \
       --subnet-id subnet-1a2b3c4d
   ```

   Alternatively, use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) Tools for Windows PowerShell command\.

1. If you are using the Amazon EC2 API actions in your application, retrieve the AWS security credentials made available on the instance and use them to sign the requests\. The AWS SDK does this for you\.

   ```
   curl http://169.254.169.254/latest/meta-data/iam/security-credentials/role_name
   ```

### Attach an IAM role to an instance<a name="attach-iam-role"></a>

To attach an IAM role to an instance that has no role, the instance can be in the `stopped` or `running` state\.

------
#### [ New console ]<a name="attach-iam-role-console"></a>

**To attach an IAM role to an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, choose **Actions**, **Security**, **Modify IAM role**\.

1. Select the IAM role to attach to your instance, and choose **Save**\.

------
#### [ Old console ]

**To attach an IAM role to an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, choose **Actions**, **Instance Settings**, **Attach/Replace IAM role**\.

1. Select the IAM role to attach to your instance, and choose **Apply**\.

------<a name="attach-iam-role-instance-cli"></a>

**To attach an IAM role to an instance \(AWS CLI\)**

1. If required, describe your instances to get the ID of the instance to which to attach the role\.

   ```
   aws ec2 describe-instances
   ```

1. Use the [associate\-iam\-instance\-profile](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-iam-instance-profile.html) command to attach the IAM role to the instance by specifying the instance profile\. You can use the Amazon Resource Name \(ARN\) of the instance profile, or you can use its name\.

   ```
   aws ec2 associate-iam-instance-profile \
       --instance-id i-1234567890abcdef0 \
       --iam-instance-profile Name="TestRole-1"
   ```

   Example response

   ```
   {
       "IamInstanceProfileAssociation": {
           "InstanceId": "i-1234567890abcdef0", 
           "State": "associating", 
           "AssociationId": "iip-assoc-0dbd8529a48294120", 
           "IamInstanceProfile": {
               "Id": "AIPAJLNLDX3AMYZNWYYAY", 
               "Arn": "arn:aws:iam::123456789012:instance-profile/TestRole-1"
           }
       }
   }
   ```

Alternatively, use the following Tools for Windows PowerShell commands:
+ [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html)
+ [Register\-EC2IamInstanceProfile](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2IamInstanceProfile.html)

### Replace an IAM role<a name="replace-iam-role"></a>

To replace the IAM role on an instance that already has an attached IAM role, the instance must be in the `running` state\. You can do this if you want to change the IAM role for an instance without detaching the existing one first\. For example, you can do this to ensure that API actions performed by applications running on the instance are not interrupted\.

------
#### [ New console ]<a name="replace-iam-role-console"></a>

**To replace an IAM role for an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, choose **Actions**, **Security**, **Modify IAM role**\.

1. Select the IAM role to attach to your instance, and choose **Save**\.

------
#### [ Old console ]

**To replace an IAM role for an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, choose **Actions**, **Instance Settings**, **Attach/Replace IAM role**\.

1. Select the IAM role to attach to your instance, and choose **Apply**\.

------<a name="replace-iam-role-cli"></a>

**To replace an IAM role for an instance \(AWS CLI\)**

1. If required, describe your IAM instance profile associations to get the association ID for the IAM instance profile to replace\.

   ```
   aws ec2 describe-iam-instance-profile-associations
   ```

1. Use the [replace\-iam\-instance\-profile\-association](https://docs.aws.amazon.com/cli/latest/reference/ec2/replace-iam-instance-profile-association.html) command to replace the IAM instance profile by specifying the association ID for the existing instance profile and the ARN or name of the instance profile that should replace it\.

   ```
   aws ec2 replace-iam-instance-profile-association \
       --association-id iip-assoc-0044d817db6c0a4ba \
       --iam-instance-profile Name="TestRole-2"
   ```

   Example response

   ```
   {
       "IamInstanceProfileAssociation": {
           "InstanceId": "i-087711ddaf98f9489", 
           "State": "associating", 
           "AssociationId": "iip-assoc-09654be48e33b91e0", 
           "IamInstanceProfile": {
               "Id": "AIPAJCJEDKX7QYHWYK7GS", 
               "Arn": "arn:aws:iam::123456789012:instance-profile/TestRole-2"
           }
       }
   }
   ```

Alternatively, use the following Tools for Windows PowerShell commands:
+ [Get\-EC2IamInstanceProfileAssociation](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2IamInstanceProfileAssociation.html)
+ [Set\-EC2IamInstanceProfileAssociation](https://docs.aws.amazon.com/powershell/latest/reference/items/Set-EC2IamInstanceProfileAssociation.html)

### Detach an IAM role<a name="detach-iam-role"></a>

You can detach an IAM role from a running or stopped instance\.

------
#### [ New console ]<a name="detach-iam-role-console"></a>

**To detach an IAM role from an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, choose **Actions**, **Security**, **Modify IAM role**\.

1. For **IAM role**, choose **No IAM Role**\. Choose **Save**\.

1. In the confirmation dialog box, enter ** Detach**, and then choose **Detach**\.

------
#### [ Old console ]

**To detach an IAM role from an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, choose **Actions**, **Instance Settings**, **Attach/Replace IAM role**\.

1. For **IAM role**, choose **No Role**\. Choose **Apply**\.

1. In the confirmation dialog box, choose **Yes, Detach**\.

------<a name="detach-iam-role-cli"></a>

**To detach an IAM role from an instance \(AWS CLI\)**

1. If required, use [describe\-iam\-instance\-profile\-associations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-iam-instance-profile-associations.html) to describe your IAM instance profile associations and get the association ID for the IAM instance profile to detach\.

   ```
   aws ec2 describe-iam-instance-profile-associations
   ```

   Example response

   ```
   {
       "IamInstanceProfileAssociations": [
           {
               "InstanceId": "i-088ce778fbfeb4361", 
               "State": "associated", 
               "AssociationId": "iip-assoc-0044d817db6c0a4ba", 
               "IamInstanceProfile": {
                   "Id": "AIPAJEDNCAA64SSD265D6", 
                   "Arn": "arn:aws:iam::123456789012:instance-profile/TestRole-2"
               }
           }
       ]
   }
   ```

1. Use the [disassociate\-iam\-instance\-profile](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-iam-instance-profile.html) command to detach the IAM instance profile using its association ID\.

   ```
   aws ec2 disassociate-iam-instance-profile --association-id iip-assoc-0044d817db6c0a4ba
   ```

   Example response

   ```
   {
       "IamInstanceProfileAssociation": {
           "InstanceId": "i-087711ddaf98f9489", 
           "State": "disassociating", 
           "AssociationId": "iip-assoc-0044d817db6c0a4ba", 
           "IamInstanceProfile": {
               "Id": "AIPAJEDNCAA64SSD265D6", 
               "Arn": "arn:aws:iam::123456789012:instance-profile/TestRole-2"
           }
       }
   }
   ```

Alternatively, use the following Tools for Windows PowerShell commands:
+ [Get\-EC2IamInstanceProfileAssociation](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2IamInstanceProfileAssociation.html)
+ [Unregister\-EC2IamInstanceProfile](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2IamInstanceProfile.html)

### Generate a policy for your IAM role based on access activity<a name="generate-policy-for-iam-role"></a>

When you first create an IAM role for your applications, you might sometimes grant permissions beyond what is required\. Before launching your application in your production environment, you can generate an IAM policy that is based on the access activity for an IAM role\. IAM Access Analyzer reviews your AWS CloudTrail logs and generates a policy template that contains the permissions that have been used by the role in your specified date range\. You can use the template to create a managed policy with fine\-grained permissions and then attach it to the IAM role\. That way, you grant only the permissions that the role needs to interact with AWS resources for your specific use case\. This helps you adhere to the best practice of [granting least privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege)\. To learn more, see [Generate policies based on access activity](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_generate-policy.html) in the *IAM User Guide*\.