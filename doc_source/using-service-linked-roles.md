# Using service\-linked roles for Capacity Reservation Fleet<a name="using-service-linked-roles"></a>

On\-Demand Capacity Reservation Fleet uses AWS Identity and Access Management \(IAM\)[ service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to Capacity Reservation Fleet\. Service\-linked roles are predefined by Capacity Reservation Fleet and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up Capacity Reservation Fleet easier because you don’t have to manually add the necessary permissions\. Capacity Reservation Fleet defines the permissions of its service\-linked roles, and unless defined otherwise, only Capacity Reservation Fleet can assume its roles\. The defined permissions include the trust policy and the permissions policy, and that permissions policy cannot be attached to any other IAM entity\.

You can delete a service\-linked role only after first deleting their related resources\. This protects your Capacity Reservation Fleet resources because you can't inadvertently remove permission to access the resources\.

## Service\-linked role permissions for Capacity Reservation Fleet<a name="slr-permissions"></a>

Capacity Reservation Fleet uses the service\-linked role named **AWSServiceRoleForEC2CapacityReservationFleet** to create, describe, modify, and cancel Capacity Reservations that were previously created by a Capacity Reservation Fleet, on your behalf\.

The AWSServiceRoleForEC2CapacityReservationFleet service\-linked role trusts the following entity to assume the role: `capacity-reservation-fleet.amazonaws.com`\.

The role uses the **AWSEC2CapacityReservationFleetRolePolicy** policy, which includes the following permissions:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeCapacityReservations",
                "ec2:DescribeInstances"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateCapacityReservation",
                "ec2:CancelCapacityReservation",
                "ec2:ModifyCapacityReservation"
            ],
            "Resource": [
                "arn:aws:ec2:*:*:capacity-reservation/*"
            ],
            "Condition": {
                "StringLike": {
                    "ec2:CapacityReservationFleet": "arn:aws:ec2:*:*:capacity-reservation-fleet/crf-*"
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateTags"
            ],
            "Resource": [
                "arn:aws:ec2:*:*:capacity-reservation/*"
            ],
            "Condition": {
                "StringEquals": {
                    "ec2:CreateAction": "CreateCapacityReservation"
                }
            }
        }
    ]
}
```

You must configure permissions to allow an IAM entity \(such as a user, group, or role\) to create, edit, or delete a service\-linked role\. For more information, see [ Service\-Linked Role Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

## Create a service\-linked role for Capacity Reservation Fleet<a name="create-slr"></a>

You don't need to manually create a service\-linked role\. When you create a Capacity Reservation Fleet using the `create-capacity-reservation-fleet` AWS CLI command or the `CreateCapacityReservationFleet` API, the service\-linked role is automatically created for you\. 

If you delete this service\-linked role, and then need to create it again, you can use the same process to recreate the role in your account\. When you create a Capacity Reservation Fleet, Capacity Reservation Fleet creates the service\-linked role for you again\. 

## Edit a service\-linked role for Capacity Reservation Fleet<a name="edit-slr"></a>

Capacity Reservation Fleet does not allow you to edit the AWSServiceRoleForEC2CapacityReservationFleet service\-linked role\. After you create a service\-linked role, you cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

## Delete a service\-linked role for Capacity Reservation Fleet<a name="delete-slr"></a>

If you no longer need to use a feature or service that requires a service\-linked role, we recommend that you delete that role\. That way you don’t have an unused entity that is not actively monitored or maintained\. However, you must delete the resources for your service\-linked role before you can manually delete it\.

**Note**  
If the Capacity Reservation Fleet service is using the role when you try to delete the resources, then the deletion might fail\. If that happens, wait for a few minutes and try the operation again\. 

**To delete the AWSServiceRoleForEC2CapacityReservationFleet service\-linked role**

1. Use the `delete-capacity-reservation-fleet` AWS CLI command or the `DeleteCapacityReservationFleet` API to delete the Capacity Reservation Fleets in your account\.

1. Use the IAM console, the AWS CLI, or the AWS API to delete the AWSServiceRoleForEC2CapacityReservationFleet service\-linked role\. For more information, see [ Deleting a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role) in the *IAM User Guide*\.

## Supported Regions for Capacity Reservation Fleet service\-linked roles<a name="slr-regions"></a>

Capacity Reservation Fleet supports using service\-linked roles in all of the Regions where the service is available\. For more information, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/ec2-service.html#ec2_region)\.