# Controlling Access Using EC2 Resource Tags<a name="control-access-with-tags"></a>

You can also provide tag information in the `Condition` element of a policy to control access based on tags\. This gives you better control over which EC2 resources a user can modify, use, or delete\.

For example, you can create a policy that allows users to terminate an instance but denies the action if the instance has the tag `environment=production`\. To do this, you use the `ec2:ResourceTag` condition key to allow or deny access to the resource based on the tags that are attached to the resource\. 

```
"StringEquals": { "ec2:ResourceTag/environment": "production" }
```

To learn whether an Amazon EC2 API action supports controlling access using the `ec2:ResourceTag` condition key, see [Actions, Resources, and Condition Keys for Amazon EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html) in the *IAM User Guide*\. Note that the `Describe` actions do not support resource\-level permissions, and therefore you must specify them in a separate statement without conditions\. 

For example IAM policies, see [Example Policies for Working with the AWS CLI or an AWS SDK](ExamplePolicies_EC2.md)\. 

**Note**  
If you allow or deny users access to resources based on tags, you must consider explicitly denying users the ability to add those tags to or remove them from the same resources\. Otherwise, it's possible for a user to circumvent your restrictions and gain access to a resource by modifying its tags\. 