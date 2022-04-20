# AWS managed policies for Amazon Elastic Compute Cloud<a name="security-iam-awsmanpol"></a>

To add permissions to users, groups, and roles, it is easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the **ReadOnlyAccess** AWS managed policy provides read\-only access to all AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.

## AWS managed policy: AmazonEC2FullAccess<a name="security-iam-awsmanpol-AmazonEC2FullAccess"></a>

You can attach the `AmazonEC2FullAccess` policy to your IAM identities\. This policy grants permissions that allow full access to Amazon EC2\.

To view the permissions for this policy, see [AmazonEC2FullAccess](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonEC2FullAccess) in the AWS Management Console\.

## AWS managed policy: AmazonEC2ReadOnlyAccess<a name="security-iam-awsmanpol-AmazonEC2ReadOnlyAccess"></a>

You can attach the `AmazonEC2ReadOnlyAccess` policy to your IAM identities\. This policy grants permissions that allow read\-only access to Amazon EC2\.

To view the permissions for this policy, see [AmazonEC2ReadOnlyAccess](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess) in the AWS Management Console\.

## AWS managed policy: AWSEC2CapacityReservationFleetRolePolicy<a name="security-iam-awsmanpol-AWSEC2CapacityReservationFleetRolePolicy"></a>

This policy is attached to the service\-linked role named **AWSServiceRoleForEC2CapacityReservationFleet** to allow Capacity Reservations to create, modify, and cancel Capacity Reservations on your behalf\.

To view the permissions for this policy, see [AWSServiceRoleForEC2CapacityReservationFleet](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AWSServiceRoleForEC2CapacityReservationFleet) in the AWS Management Console\.

## AWS managed policy: AWSEC2FleetServiceRolePolicy<a name="security-iam-awsmanpol-AWSEC2FleetServiceRolePolicy"></a>

This policy is attached to the service\-linked role named **AWSServiceRoleForEC2Fleet** to allow EC2 Fleet to request, launch, terminate, and tag instances on your behalf\. For more information, see [Service\-linked role for EC2 Fleet](manage-ec2-fleet.md#ec2-fleet-service-linked-role)\.

## AWS managed policy: AWSEC2SpotFleetServiceRolePolicy<a name="security-iam-awsmanpol-AWSEC2SpotFleetServiceRolePolicy"></a>

This policy is attached to the service\-linked role named **AWSServiceRoleForEC2SpotFleet** to allow Spot Fleet to launch and manage instances on your behalf\. For more information, see [Service\-linked role for Spot Fleet](work-with-spot-fleets.md#service-linked-roles-spot-fleet-requests)\.

## AWS managed policy: AWSEC2SpotServiceRolePolicy<a name="security-iam-awsmanpol-AWSEC2SpotServiceRolePolicy"></a>

This policy is attached to the service\-linked role named **AWSServiceRoleForEC2Spot** to allow Amazon EC2 to launch and manage Spot Instances on your behalf\. For more information, see [Service\-linked role for Spot Instance requests](spot-requests.md#service-linked-roles-spot-instance-requests)\.

## Amazon EC2 updates to AWS managed policies<a name="security-iam-awsmanpol-updates"></a>

View details about updates to AWS managed policies for Amazon EC2 since this service began tracking these changes\.


| Change | Description | Date | 
| --- | --- | --- | 
| Amazon EC2 started tracking changes | Amazon EC2 started tracking changes to its AWS managed policies | March 1, 2021 | 